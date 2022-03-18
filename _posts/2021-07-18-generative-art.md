---
layout: post
title: Generative Art using Python and AxiDraw Pen Plotter
tags: tutorial open-source hobby
permalink: generative
---

This tutorial shows how to make generative art using Python and AxiDraw pen plotter. I also published this tutorial on [mirror.xyz](https://mirror.xyz/0x33632E2F9Dd846f5Cd6038e4415cb875ED19D87F/jSgGFegMyB6Hip4a7F169PTkhBH88bvKrLoKhgqWnf0) for testing out web3 publishing experience. 

The code can be accessed at [Github Repo](https://github.com/harrywang/python-generative-art).

You should start with `axidraw-basics.ipynb` notebook, which shows how to setup AxiDraw V3 and use Python to control it to plot a simple SVG.

After that, you can look at `voronoi-art.ipynb` notebook for a more complicated tutorial on generating art based on Voronoi diagram like:

<img class="mx-auto" width="200" src="https://user-images.githubusercontent.com/595772/127227945-6e172864-345a-4e63-a977-64466acfba5a.png">


It's fun to watch the plotting process. I named my AxiDraw pen plotter "Bohu" after [Bohu Tang](https://en.wikipedia.org/wiki/Tang_Yin) (1470-1524) - a Chinese painter, calligrapher, and poet of the Ming dynasty period, who is 18 years younger than Leonardo da Vinci (1452-1519).

<img class="mx-auto" width="400" src="https://user-images.githubusercontent.com/595772/127374294-1c657a88-e1e8-4cc6-a0d8-f1965d778734.gif">

It looks awesome after framed:

<img class="mx-auto" width="400" src="https://user-images.githubusercontent.com/595772/127390033-dc394516-b2db-4ad1-9228-5ffe6fb845e0.jpg">


## Setup Inkscape and Axidraw Driver

Please follow the [instructions](https://wiki.evilmadscientist.com/Axidraw_Software_Installation) and do the followings:

1. Download and install the latest version of [Inkscape for Mac](https://inkscape.org/) (1.1 tested)
2. Download and run the latest [AxiDraw installer](https://wiki.evilmadscientist.com/Axidraw_Software_Installation) (AxiDraw_Install_Mac_274r1 tested)

## Axidraw via Python

You can setup the environment as follows (tested with Python 3.8+)

```
$ python3 -m venv venv
$ source venv/bin/activate
$ pip install -r requirements.txt
```
then, `jupyter lab` to run the notebook `axidraw-basics.ipynb` to learn how to setup Axidraw and plot a simple SVG image.

## Plotting via Inkscape

Once you have the SVG ready, you can follow the steps below to plot it via AxiDraw using Inkscape:

Open Inkscape and choose the paper size - here I choose Letter in landscape (this is related to how to position the paper under AxiDraw):

<img class="mx-auto" src="https://user-images.githubusercontent.com/595772/127328431-4aa61162-2ae9-4c1d-9e77-5bec390c7232.png">

Open the SVG:

<img class="mx-auto" src="https://user-images.githubusercontent.com/595772/127328517-497dca47-7831-4d98-84ff-2bf4bf3a79e6.png">


Choose AxiDraw Control:

<img class="mx-auto" src="https://user-images.githubusercontent.com/595772/127328745-b9ea73dd-51dd-48f5-abd8-383adc651f56.png">


Setup AxiDraw using "Raise pen, turn off motors" and move the plotting head to the Home position (see your AxiDraw machine - there is a Home):

<img class="mx-auto" src="https://user-images.githubusercontent.com/595772/127329145-6778fae5-f4b8-431e-953e-ead8a87fa311.png">


Setup the pen using "Pen up" and adjust the pen tip 3mm-6mm above the paper.

<img class="mx-auto" src="https://user-images.githubusercontent.com/595772/127329157-f6d2b574-9bd6-44f7-b3d6-55c04b1760d2.png">


Position the left upper corner of the paper directly underneath the pen tip and start plotting using the Apply button shown below. Note that the plotting will pause if the computer goes to sleep.

<img class="mx-auto" src="https://user-images.githubusercontent.com/595772/127330963-6fd1dd0b-971a-4435-8f17-08c59cfc15e4.png">

## References

This tutorial is based on the following tutorials:

- [evilmadscientist.com](https://cdn.evilmadscientist.com/dl/ad/public/AxiDraw_Guide_v40_r3.pdf)
- [generativehut.com](https://www.generativehut.com/post/robots-and-generative-art-and-python-oh-my)


