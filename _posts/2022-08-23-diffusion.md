---
layout: post
title: Deploy Stable Diffusion for AI Image Generation
tags: tutorial coding 
permalink: diffusion
---

<img class="mx-auto" src="https://user-images.githubusercontent.com/595772/217112364-0ad39757-8468-4f40-9d89-90bd6578ea90.png">

- Updated 2/4/2023： added InvokeAI(https://github.com/invoke-ai/InvokeAI) instruction
- Updated on 1/30/2023: use [Stable Diffusion WebUI](https://github.com/AUTOMATIC1111/stable-diffusion-webui/wiki/Installation-on-Apple-Silicon) instead given that DiffusionBee has issues loading custom models - check out my setup notes below - it may save you lots of time and trouble!
- Updated on 1/16/2023: start to use the awesome offline stable diffusion app: [DiffusionBee](https://diffusionbee.com/)
- Updated on 12/6/2022: add M1 deployment notes

Stable Diffusion WebUI is my current go-to UI.

## Setup Stable Diffusion WebUI

I ran into so many issues trying to set it up on my MacBook Pro M1 and finally made it work.

The most important lesson learned: **the Python version matters!**

I figured this out from the inline comment [here](https://github.com/AUTOMATIC1111/stable-diffusion-webui/blob/2c1bb46c7ad5b4536f6587d327a03f0ff7811c5d/launch.py#L37) after having many issues with Python 3.8.0 and 3.9.7 - It would be helpful if the author can highlight this in the README file. 

I use `pyenv` to manage my Python versions and use the following commands to install Python 3.10.6 first.

```
pyenv versions
pyenv install 3.10.6
pyenv global 3.10.6
```

- clone the repo: `git clone https://github.com/AUTOMATIC1111/stable-diffusion-webui.git`
- download a checkpoint, such as stable diffusion 1.5 from [https://huggingface.co/runwayml/stable-diffusion-v1-5](https://huggingface.co/runwayml/stable-diffusion-v1-5) and put the downloaded model in `/stable-diffusion-webui/models/Stable-diffusion/` folder.
- switch to `stable-diffusion-webui` folder and run `./webui.sh`
- visit [http://127.0.0.1:7860](http://127.0.0.1:7860) to use the tool

It should be simple as the few steps above if the Python version is correct. 

> "Stable Diffusion 2.0 and 2.1 require both a model and a configuration file, and image width & height will need to be set to 768 or higher when generating images"

For using Stable Diffusion v2.0, follow the [instruction](https://github.com/AUTOMATIC1111/stable-diffusion-webui/wiki/Installation-on-Apple-Silicon) to download the checkpoints and yaml files. 

My `/stable-diffusion-webui/models/Stable-diffusion/` folder looks like the following:

<img class="mx-auto" src="https://user-images.githubusercontent.com/595772/215642669-92422d92-51f6-4389-afbe-3af37dd811b4.png">


Note: for v2.0, you may need to run `./webui.sh --no-half` or restart to make it work.

for `768-v-ema.ckpt` SD v2.0, you have to use at least 768x768 or higher, e.g., 768x1024 to generate images otherwise you get garbage images shown below:

- 512x512 garbage result:

<img class="mx-auto" src="https://user-images.githubusercontent.com/595772/215643435-f65ada42-6c43-415c-9948-55b405eabb6c.png">

- 768x768 result:

<img class="mx-auto" src="https://user-images.githubusercontent.com/595772/215643430-5d386c1c-4cb2-4f7a-98f0-3aefc6c3e272.png">


I want to record the issues I ran into below in case I need to refer to them later.

- 3.8.x has the following error (python 3.9.7 works but has other errors):

  ```
  File "/Users/harrywang/.pyenv/versions/3.8.11/lib/python3.8/site-packages/ldm.py", line 20
      print self.face_rec_model_path 
            ^
  SyntaxError: Missing parentheses in call to 'print'. Did you mean print(self.face_rec_model_path)?
  ```

- run into this [error](https://github.com/AUTOMATIC1111/stable-diffusion-webui/issues/4346), removed the `k_diffusion_commit_hash` in [launch.py](https://github.com/AUTOMATIC1111/stable-diffusion-webui/blob/master/launch.py#L302)

  ```
  git_clone(k_diffusion_repo, repo_dir('k-diffusion'), "K-diffusion", k_diffusion_commit_hash)
  ```
  
  to 

  ```
    git_clone(k_diffusion_repo, repo_dir('k-diffusion'), "K-diffusion")
  ```
- I have the following msg but I have not update to torch 1.13.1 yet - 1.12.1 seems to work fine so far.

  ```
  You are running torch 1.12.1.
  The program is tested to work with torch 1.13.1.
  To reinstall the desired version, run with commandline flag --reinstall-torch.
  Beware that this will cause a lot of large files to be downloaded, as well as
  there are reports of issues with training tab on the latest version.
  ```

- run into the following errors - all seems to related to `mps`. I used `python launch.py  --skip-torch-cuda-test --no-half --use-cpu all` to launch to fix them - essentially don't use `mps` and use `cpu` instead.

  ```
  NotImplementedError: The operator 'aten::frac.out' is not currently implemented for the MPS device. If you want this op to be added in priority during the prototype phase of this feature, please comment on https://github.com/pytorch/pytorch/issues/77764. As a temporary fix, you can set the environment variable `PYTORCH_ENABLE_MPS_FALLBACK=1` to use the CPU as a fallback for this op. WARNING: this will be slower than running natively on MPS.
  ```

  ```
  RuntimeError: "LayerNormKernelImpl" not implemented for 'Half'
  ```

## Setup InvokeAI

This is the my notes of installing [InvokeAI](https://github.com/invoke-ai/InvokeAI) instruction on MacBook Pro M1. Tested with Python 3.10.6. 

- `pip install .` installs packages using `pyproject.toml`.
- `invokeai-configure` asks you to download sd models

```
git clone https://github.com/invoke-ai/InvokeAI.git
cd InvokeAI
python -m venv venv
pip install --use-pep517 .
invokeai-configure
invokeai --web
```

Visit [http://localhost:9090](http://localhost:9090) to use the UI.

InvokeAI seems to take more resources than AUTOMATIC1111 Stable Diffusion WebUI below. 

## M1 Deployment

I just followed the instructions [here](https://github.com/apple/ml-stable-diffusion). 

Tested on my 2020 MacBook Pro M1 with 16G RAM and Torch 1.13.0.

Run the following to generate the models in `coreml-sd` folder:

```
git clone https://github.com/apple/ml-stable-diffusion.git
cd ml-stable-diffusion
conda create -n coreml_stable_diffusion python=3.8 -y
conda activate coreml_stable_diffusion
pip install -e .
huggingface-cli login
python -m python_coreml_stable_diffusion.torch2coreml --convert-unet --convert-text-encoder --convert-vae-decoder --convert-safety-checker -o coreml-sd
```

Generate image with Python and output to `image-outputs` folder:

```
python -m python_coreml_stable_diffusion.pipeline --prompt "a photo of an astronaut riding a horse on mars" -i coreml-sd -o image-outputs --compute-unit ALL --seed 93
```

The method above loads the model every time, which is quite slow (2-3 minutes). Use Swift to speed up model loading by setting up the Resources:

```
python -m python_coreml_stable_diffusion.torch2coreml --convert-unet --convert-text-encoder --convert-vae-decoder --convert-safety-checker --bundle-resources-for-swift-cli -o coreml-sd 
```

Then, generate image with Swift and output to `image-outputs` folder:

```
swift run StableDiffusionSample "a photo of an astronaut riding a horse on mars" --resource-path coreml-sd/Resources/ --seed 93 --output-path image-outputs
```


## Ubuntu Deployment
In the past few months, I tried almost all popular text-to-image AI generation models/products, such as Dall-E 2, MidJourney, Disco Diffusion, Stable Diffusion, etc. Stable Diffusion checkpoint was just released a few days ago. I deployed one on my old GPU server and record my notes here for people who may also want to try. Machine creativity is a quite interesting research area for IS scholars and I jotted down some potential research topics in the end of this post as well.

I first spent a few hours trying to set up [Stable Diffusion](https://github.com/CompVis/stable-diffusion) on Mac M1 and failed - I cannot install the packages properly, e.g., version not found, dependency issues, etc. I found some successful attempts [here](https://github.com/CompVis/stable-diffusion/issues/25) but have no time to try them yet.

I ended up setting up Stable Diffusion on my old GPU server running Ubuntu and here are my notes.

- Check Ubuntu Version

```
lsb_release -a
No LSB modules are available.
Distributor ID:	Ubuntu
Description:	Ubuntu 18.04.6 LTS
Release:	18.04
Codename:	bionic
```

- Check CUDA version: my GPU machine only has two 2080Ti with 11G VRAM each and CUDA 10.2

```
nvidia-smi

+-----------------------------------------------------------------------------+
| NVIDIA-SMI 440.100      Driver Version: 440.100      CUDA Version: 10.2     |
|-------------------------------+----------------------+----------------------+
| GPU  Name        Persistence-M| Bus-Id        Disp.A | Volatile Uncorr. ECC |
| Fan  Temp  Perf  Pwr:Usage/Cap|         Memory-Usage | GPU-Util  Compute M. |
|===============================+======================+======================|
|   0  GeForce RTX 208...  Off  | 00000000:1A:00.0 Off |                  N/A |
| 30%   27C    P8    20W / 250W |      1MiB / 11019MiB |      0%      Default |
+-------------------------------+----------------------+----------------------+
|   1  GeForce RTX 208...  Off  | 00000000:68:00.0 Off |                  N/A |
| 30%   26C    P8    19W / 250W |     73MiB / 11016MiB |      0%      Default |
+-------------------------------+----------------------+----------------------+
```

- Use [optimized fork](https://github.com/basujindal/stable-diffusion), which uses lesser VRAM than the original by sacrificing on inference speed `git clone https://github.com/basujindal/stable-diffusion` - I did this because I ran into [CUDA out of memory issue](https://github.com/CompVis/stable-diffusion/issues/39) using the original repo.

- Get the checkpoint file from [HuggingFace Repo](https://huggingface.co/CompVis/stable-diffusion-v-1-4-original). Download/Upload the checkpoint file to the server. I use `links` to browse websites and download files via terminal:

```
sudo apt install links
links www.google.com
```

rename the checkpoint file to `model.ckpt` and put it in the following folder (create a new one):

```
mkdir -p models/ldm/stable-diffusion-v1/
```

A side note on estimated training cost based on the reported GPU usage and the related AWS price I found:

- Hardware Type: A100 PCIe 40GB
- Hours used: 150000 (about 17.1 years)
- Cloud Provider: AWS

Price of p4d.24xlarge instance with 8 A100 with 40G VRAM:

- 32.77 USD (hourly)
- 19.22 USD (1-year reserved)
- 11.57 USD (3-year reserved)

The training would cost between 225,000 USD and 600,000 USD.

- **IMPORTANT**: Switch to `stable-diffusion` folder and change to `cudatoolkit=10.2` to match my CUDA: `vim environment.yaml`

```
name: ldm
channels:
  - pytorch
  - defaults
dependencies:
  - python=3.8.5
  - pip=20.3
  - cudatoolkit=10.2
  - pytorch=1.11.0
...
```

- Create a conda environment and activate it

```
conda env create -f environment.yaml
conda activate ldm
```

Now, Stable Diffusion is ready to go and let's see what AI will create based on the following text:

> A car in 2050 designed by Antoni Gaudi

```
python optimizedSD/optimized_txt2img.py --prompt "a car in 2050 designed by Antoni Gaudi" --H 512 --W 512 --seed 27 --n_iter 2 --n_samples 10 --ddim_steps 50
```
This whole area is relatively new and there are many potential interesting research topics, e.g.,

- how humans work with AI creativity tools like Stable Diffusion (workflow shift? efficiency improvement? creativity boosting?...)
- how to use AI to pick top n results generated from the same prompt, e.g., preference on cleaner background, less color, simpler composition, aesthetics scoring, etc.
- how to do systemic/serendipitous prompt engineering to improve art creation novelty, efficiency, and quality by leveraging ideas from areas such as AutoML, recommender systems, and reinforcement learning.

Anyway, out of the 20 generated images from the prompt above, the following are my top 3:

<img class="mx-auto" width='500' src="https://user-images.githubusercontent.com/595772/186077379-069a39fb-56a1-42b9-91ff-fbf8d0bc9503.png">

<img class="mx-auto" width='500' src="https://user-images.githubusercontent.com/595772/186077382-894b90c3-fcf3-4ad5-a966-9d59121d3dbd.png">

<img class="mx-auto" width='500' src="https://user-images.githubusercontent.com/595772/186077544-756a378a-5cb4-4dba-89b1-3898aceb6427.png">

PS. The first image for this post is generated via [Midjourney](https://www.midjourney.com/) using the prompt "A car in 2050 designed by Antoni Gaudi".
