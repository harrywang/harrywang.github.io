---
layout: post
title: When Data Science Meets NFT
tags: dataset open-source teaching
permalink: nft
---

<img class="mx-auto" src="https://user-images.githubusercontent.com/595772/164983138-f011ad3e-bec9-4245-9711-e6fda74df441.png">

## TL;DR 

I created an open dataset for the [Crypto Coven](https://www.cryptocoven.xyz/) NFT collection using [OpenSea API](https://docs.opensea.io/reference/api-overview) and my scripts, which can be used to teach/practice various topics, such as exploratory data analysis, time series analysis, data visualization, predictive analytics, text mining, and image analytics via deep learning.

The dataset can be accessed at [Kaggle](https://www.kaggle.com/datasets/harrywang/crypto-coven). I also added some simple code notebooks and look forward to more contributions from the community:
- [NFT Sales Price Prediction using AutoML](https://www.kaggle.com/code/harrywang/nft-sales-price-prediction-using-automl)
- [Witch Image Similarity using Tensorflow and Scikit-learn](https://www.kaggle.com/code/harrywang/witch-image-similarity)

<img class="mx-auto" width='550' src="https://user-images.githubusercontent.com/595772/164979075-b5e3d317-ea8c-462a-b651-34a41a132ce8.png">


## Analysis Examples

I have used this dataset for assignments in the undergraduate data analytics and machine learning courses I teach. 

I list some analysis examples here.

- **What's the percentage of witches that have not been sold at all?**

    50%

- **Find the top 10 addresses holding most witches. Based on the average sales price, how much those withes are worth?**

    This address `0xa1707c82aa2866955991c7f2c6f431d6619b8b4c` owns 162 witches, which are worth ~ $595,801 based on the average sales price in the past. Check out this address on OpenSea: [https://opensea.io/Divine_Femininity](https://opensea.io/Divine_Femininity)
- **Which is the most expensive witch ever sold (before 4/21/2022)? Who bought it?**

    It's [her](https://www.cryptocoven.xyz/witches/7496), sold for $39,772 by [https://opensea.io/kaiynne2](https://opensea.io/kaiynne2) on January 25, 2022.
    <img class="mx-auto" width='500' src="https://user-images.githubusercontent.com/595772/164984725-8551f01c-2d69-48e7-b376-37cce77c921d.png">
- **Plot the daily sales price and weekly moving average to understand the NFT investment cycles.**

    <img class="mx-auto" width='500' src="https://user-images.githubusercontent.com/595772/164984944-d7eaab2a-6701-45ef-b435-0c4e026c63c6.png">

- **Find witches who look alike**

    Witch image features are extracted using pre-trained [Xception model](https://keras.io/api/applications/xception/) and similar images are calculated using cosine similarity and the top N nearest neighbors are returned. 

    See some results (I did not know there are smoker witches ^_^ before this analysis):

    <img class="mx-auto" width='500' src="https://user-images.githubusercontent.com/595772/164985141-1cb6395e-f22f-48f2-8e74-072946d3b702.png">
    <img class="mx-auto" width='500' src="https://user-images.githubusercontent.com/595772/164985142-e8bc5f3c-b20f-4538-b1a2-dd8a4aabccf0.png">

I hope you find this dataset interesting and useful and use it in your teaching and learning. 
