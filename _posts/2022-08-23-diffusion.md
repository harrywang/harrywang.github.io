---
layout: post
title: Setup Stable Diffusion
tags: tutorial coding 
permalink: diffusion
---

I spent a few hours trying to set up [Stable Diffusion](https://github.com/CompVis/stable-diffusion) on Mac M1 and failed - I cannot install the packages properly, e.g., version not found, dependency issues, etc. I found some successful attempts [here](https://github.com/CompVis/stable-diffusion/issues/25) but have no time to try them yet.

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

Hardware Type: A100 PCIe 40GB
Hours used: 150000 (about 17.1 years)
Cloud Provider: AWS

p4d.24xlarge instance with 8 A100 with 40G VRAM: 32.77 USD (hourly), 19.22 USD (1-year reserved), 11.57 USD (3-year reserved)

The training would cost between 225,000 USD and 600,000 USD

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
- how to do systemic/serendipitous prompt engineering to improve art creation novelty, efficiency, and quality by leveraging ideas from areas such as AutoMl, recommender systems, and reinforcement learning.

Anyway, out of the 20 generated images from the prompt above, the following are my top 3:

<img class="mx-auto" width='500' src="https://user-images.githubusercontent.com/595772/186077379-069a39fb-56a1-42b9-91ff-fbf8d0bc9503.png">

<img class="mx-auto" width='500' src="https://user-images.githubusercontent.com/595772/186077382-894b90c3-fcf3-4ad5-a966-9d59121d3dbd.png">

<img class="mx-auto" width='500' src="https://user-images.githubusercontent.com/595772/186077544-756a378a-5cb4-4dba-89b1-3898aceb6427.png">
