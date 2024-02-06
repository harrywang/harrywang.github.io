---
layout: post
title: Data Labeling using GPT APIs
tags: tutorial teaching research
permalink: gpt-labeling
---

<img class="mx-auto" src="https://github.com/harrywang/harrywang.github.io/assets/595772/1566e1a6-050a-401d-89c5-27d65b0c36b7">

### TL;DR

Data labeling stands as a significant application for Large Language Models (LLMs). In this post, I will be sharing my insights and knowledge gained from using ChatGPT (API versions 3.5 and 4) for an Aspect-Based Sentiment Analysis (ABSA) task. I selected ABSA as the example because it is a challenging task, and I have prior experience handling similar tasks in both research and industry projects without relying on LLMs. For example, my team had trained and made available a BERT-based ABSA model, which can be accessed [here](https://huggingface.co/tezign/BERT-LSTM-based-ABSA).

Key takeaways: 

- Large Language Models (LLMs) can effectively perform data labeling tasks at a level similar to human performance without human data labeling and model training, leading to potential time and cost savings.
- Use GPT4 to label 2 million reviews with long few-shot prompt can cost about $30K
- Supervised Fine-tuning (SFT) is cheaper and faster but the performance is much worse than GPT4 (NOTE: this may due to the fine-tuning data issue - I did not spend much time tweaking the fine-tuning dataset.)

Key topics include:

- Different labeling processes: manual vs. llm-based
- Different methods (prompt engineering vs. mini-batching vs. fine-tuning) and time/cost implications
- Performance comparison for different methods

### ABSA Task and Dataset

Aspect-Based Sentiment Analysis (ABSA) is a NLP task that aims to identify and extract the sentiment of specific aspects of a product or service. There has been many papers, datasets, and competitions in this area (see [https://paperswithcode.com/task/aspect-based-sentiment-analysis](https://paperswithcode.com/task/aspect-based-sentiment-analysis)).

For example, the following is a restaurant review with sentiments for three aspects:

> This place is pretty cool with awesome decor. The drinks are decent but a bit on the pricey side.

- Ambience: Positive
- Food: Neutral
- Price: Negative

We created a hotel review ABSA dataset for a research project, which has about 2 millions' hotel reviews with three aspects: 

- Employee service
- Service robot service
- Human-robot interaction

We hired human data labellers to manually label about 25K reviews and trained a model to label the rest of the dataset.

### Traditional Process

An ABSA task typically includes the following steps:

- Manually label a subset of the data in two steps: first define the aspect, then label the corresponding sentiment. check the labeling consistency (inter-coder agreement) and acquire more labellers if needed
- Train a model using the labeled data and check the performance (label more data if needed)
- Predict the rest of the data using the trained model

This process is very labor intensive and costly, e.g, **we manually labeled about 25K reviews, which took several labelers a few weeks to complete**.

### LLM-based Process

The process of using LLM for ABSA is as follows:

- Manually label a small test dataset, such as a few hundred reviews.
- Write a few-shot labeling prompt (detailed below) and label, say, 100 reviews
- Review the initial round of labeling results and incorporate any incorrectly labeled examples, particularly the challenging cases, into the few-shot prompts as supplementary examples. Repeat this process as necessary.
- Use the final prompt to label the rest of the dataset

### Prompt Engineering

The prompt is constructed iteratively as previously outlined, eventually looks like the following:

```
You are an experienced data labeling engineer with extensive experience in labeling hotel reviews. Your task is to classify a review based on three dimensions, with four categories: positive, negative, neutral, and not mentioned.

The definitions and examples of the three dimensions are as follows:

Dimension 1: Quality of hotel staff service

Definition: customer perceptions directly related to staff behavior or attitude, such as timely service, skilled, knowledgeable, professional, polite, caring, understanding, sincere, helpful, etc.

Examples:

Review: The cleaning lady cleans in a timely manner.
Sentiment: Positive

Review: Staff were testing robots in the hallway, the noise was very loud and annoying, and the front desk did nothing about it!
Sentiment: Negative

[more examples]...


Dimension 2: Quality of robot service

Definition: customer perceptions of robot functionality or perceptions of the service result after using the robot

Examples:

Review: The robot is very convenient
Sentiment: Positive

Review: The robot delivers too slowly
Sentiment: Negative

[more examples]...

Dimension 3: Human-robot interaction perception

Definition: customer perceptions other than robot functionality, such as robot social intelligence (communication understanding ability), robot social existence (making one feel it has human characteristics or experiences a human can bring), robot design and novelty (voice, and posture freshness, curiosity, advanced, coolness.

Examples:

Review: The little robot speaks adorably, too cute
Sentiment: Positive

Review: The robot's voice is too loud and noisy;
Sentiment: Negative

[more examples]...


Now, classify the sentiment of the following review into three dimensions using a JSON object as the output method, with "employee_service", "robot_service", "human_robot_interaction" as the keys and the value is one of positive/negative/neutral/unknown.

Here is the hotel review:

```

### Performance Comparison

The latest API pricing is as follows (GPT4 is about 20 times more expensive than GPT3.5):

| Model                 | Input per 1K Tokens | Output per 1K Tokens |
|-----------------------|-----------------------------|------------------------------|
| GPT-4-0125            | $0.01                       | $0.03                        |
| GPT-3.5-turbo-0125    | $0.0005                     | $0.0015                      |


The prompt shown above is quite long (use [tiktoken](https://huggingface.co/spaces/JacobLinCool/tiktoken-calculator) to calculate the # of tokens), which is about 1400 tokens. Together with the actual review, the input to OpenAI API is about 1500 tokens. 

So, the cost for one review is about the following (we can ignore the output for cost estimation given it is quite short):

- GPT-4: $0.015
- GPT-3.5: $0.00075

For 2 million reviews, the total cost would be:

- GPT-4: $30K USD - quite expensive!!
- GPT-3.5: $1.5K USD

In order to reduce the cost, we tried the following methods:

- Mini-batching: attach 100 reviews (instead of 1) to the prompt to reduce the total number of API calls. **NOTE: if you put too many reviews in each batch, the output may exceed the maximal 4096 token.** 
- Supervised Fine-tuning (SFT): try to fine-tune GPT 3.5 with 100 reviews, remove the examples from the few-shot prompt above to reduce input token (reduced from ~1500 to ~500)

However, both mini-batching and SFT result in much worse labeling performance as shown below.

| Model          | Service          | Accuracy | Precision | Recall | F1    |
|----------------|------------------|----------|-----------|--------|-------|
| **GPT-4**      | Employee-Service | 0.99     | 0.98      | 1.00   | 0.99  |
|                | Robot-Service    | 0.91     | 0.62      | 0.61   | 0.61  |
|                | HCI              | 0.86     | 0.83      | 0.83   | 0.83  |
| **GPT-3.5**    | Employee-Service | 0.96     | 0.72      | 0.68   | 0.69  |
|                | Robot-Service    | 0.80     | 0.44      | 0.38   | 0.40  |
|                | HCI              | 0.56     | 0.39      | 0.33   | 0.32  |
| **GPT-4 One Pass** | Employee-Service | 0.81     | 0.44      | 0.40   | 0.39  |
|                | Robot-Service    | 0.62     | 0.62      | 0.54   | 0.48  |
|                | HCI              | 0.71     | 0.85      | 0.52   | 0.45  |
| **GPT SFT**    | Employee-Service | 0.49     | 0.74      | 0.60   | 0.50  |
|                | Robot-Service    | 0.57     | 0.48      | 0.43   | 0.37  |
|                | HCI              | 0.81     | 0.54      | 0.50   | 0.52  |

The time for labeling 100 reviews for different methods are listed below:

- ~ 5 mins 31 sec for 100 separate API calls
- ~ 6 mins 19 sec for 100 review in one batch
- ~ 1 min 28 sec for 100 separate API calls using the fine-tuned model with shorter prompt (much faster)

To summarize, GPT 4 works well for the ABSA task but can be expensive. SFT method seems to be much cheaper and faster but the performance needs to be improved, e.g., better data preparation and engineering.

PS. The featured image for this post is generated using DALLE 3.
