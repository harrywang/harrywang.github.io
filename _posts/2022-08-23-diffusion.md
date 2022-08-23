---
layout: post
title: Setup Stable Diffusion
tags: tutorial coding 
permalink: diffusion
---

I spent a few hours trying to set up [Stable Diffusion](https://github.com/CompVis/stable-diffusion) on Mac M1 and failed - I cannot install the packages properly, e.g., version not find, dependency issues, etc. I found some successful attempts [here](https://github.com/CompVis/stable-diffusion/issues/25) but have no time to try them yet.

I ended up setup Stable Diffusion on my old GPU server running Ubuntu and here are my notes.

- Check Ubuntu Version

```
lsb_release -a
No LSB modules are available.
Distributor ID:	Ubuntu
Description:	Ubuntu 18.04.6 LTS
Release:	18.04
Codename:	bionic
```

- Check CUDA version: my GPU machine only has two 2080Ti with 11G GPU each and CUDA 10.2

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

- Download/Upload the checkpoint file to the server. I use `links` to browse websites and download files via terminal:

```
sudo apt install links
links www.google.com
```

rename the checkpoint file to `model.ckpt` and put it in the following folder (create a new one):

```
mkdir -p models/ldm/stable-diffusion-v1/
```

- **IMPORTANT**: Switch to `stable-diffusion` folder and change the `cudatoolkit=10.2` to match my CUDA: `vim environment.yaml`

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

Now, Stable Diffusion is ready to go:

```
python optimizedSD/optimized_txt2img.py --prompt "a car in 2050 designed by Antoni Gaudi" --H 512 --W 512 --seed 27 --n_iter 2 --n_samples 10 --ddim_steps 50
```

I found that the quality of generated images without prompt engineering still varies a lot and how to use AI to pick top n results is an interesting issue to investigate, e.g., preferences on cleaner background, less color, simpler composition, aesthetics scoring, etc. 

Out of the 20 generated images, the following are my top 3:

<img class="mx-auto" width='500' src="https://user-images.githubusercontent.com/595772/186077379-069a39fb-56a1-42b9-91ff-fbf8d0bc9503.png">

<img class="mx-auto" width='500' src="https://user-images.githubusercontent.com/595772/186077382-894b90c3-fcf3-4ad5-a966-9d59121d3dbd.png">

<img class="mx-auto" width='500' src="https://user-images.githubusercontent.com/595772/186077544-756a378a-5cb4-4dba-89b1-3898aceb6427.png">
