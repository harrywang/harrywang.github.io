---
title: calligraphy.ai
layout: page
---
# Calligraphy + Art + AI

Creativity is often considered the ultimate moonshot for Artificial Intelligence. Generative Modeling and Natural Language Processing have been applied to create paintings, generate music, and write poems. There has been extensive research on linking text and image, such as image captioning, text-to-image generation, where the texts are largely the descriptions about the images and are either encoded as features or treated as labels. Inspired by Chinese calligraphy as a unique form of visual art where the character itself is an aesthetic painting, we develop a creative framework based on Conditional Generative Adversarial Networks and Contextual Neural Language Model to generate artworks that have intrinsic meaning and aesthetic value. We demonstrate the framework in a case study, where artworks are generated in real-time to improve the overall customer experience and engagement in a restaurant. (Patent Pending, 2020)

Our idea was inspired by paintings by Franz Kline, such as Painting Number 2 (1954) shown below, which I enjoy viewing a few times at MoMA.

[Image Source](https://www.moma.org/collection/works/79234)

<img src="https://user-images.githubusercontent.com/595772/81478786-830a7a00-91ed-11ea-9014-57a10be30aa7.png" style="display: block; margin-left: auto; margin-right: auto; width: 80%;">

## Paper

"A Framework and Dataset for Abstract Art Generation via CalligraphyGAN", with Jinggang Zhuo and Ling Fan, [Workshop on Machine Learning for Creativity and Design at NeurIPS 2020](https://neurips2020creativity.github.io/), Vancouver, Canada. [(arXiv link)](https://arxiv.org/abs/2012.00744)

## Dataset

We collected 138,499 images of Chinese calligraphy characters written by 19 calligraphers from the Internet, which cover 7328 different characters in total. You can access the dataset [here](https://github.com/zhuojg/chinese-calligraphy-dataset).

## Source Code 

You can access the source code for this project [here](https://github.com/harrywang/CalligraphyGAN). We built our system based on many other open source projects - many thanks to those authors. We have also included the docker scripts and Streamlit demo code to make this project more accessible. 

## Demo
[Calligraphy.AI](http://54.223.165.220:8501/): hosted on a small server located in China without much model tuning, so please expect slow loading and surprising results, which is part of the fun, right? :)

## Team

This is a collaborative project with the Design AI Lab directed by Dr. [Ling Fan](https://www.linkedin.com/in/fatflatfloat/) at the [College of Design and Innovation](https://tjdi.tongji.edu.cn/?lang=en), Tongji University.

Master Student: [Jinggang Zhuo](https://github.com/zhuojg)
