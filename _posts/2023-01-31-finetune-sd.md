---
layout: post
title: What I Learned About Fine-tuning Stable Diffusion
tags: tutorial coding teaching
permalink: sd
---

<img class="mx-auto" src="https://user-images.githubusercontent.com/595772/219880541-ce36d12a-bc7a-476c-b831-09a8d130f61a.png">

[LoRA (Low-Rank Adaptation) of Large Language Models](https://arxiv.org/abs/2106.09685) was included in the [Diffuser](https://huggingface.co/docs/diffusers/index) release last week, which enables fine-tuning Stable Diffusion (SD) model with much lower GPU requirements so that I can finally try it on my old RTX 2080 Ti (I now use Tesla V100 most of the time). In addition, LoRA fine-tuning is much faster and the trained weights are much smaller, e.g., ~3M vs. ~5G.

There are many tutorials on fine-tuning Stable Diffusion using Colab notebooks and UI tools. But I did not find a good "self-contained" repo with environment setup, simple sample datasets, training scripts, and instructions so that people can just clone, customize, and run. 

In this tutorial, I want to share my experience in fine-tuning Stable Diffusion using HuggingFace training scripts with a few sample datasets. I am still learning about the tips and tricks on this and will report more findings as I go along.

The data and code can be accessed at this [repo](https://github.com/harrywang/finetune-sd) and this tutorial is based on [these references](#references).

Some of my lessons learned are:

- SD fine-tuning evaluation is quite subjective, which has the following implications:
  1. Intermediate checkpoints may have better results - this is different from traditional fine-tuning where we have objective metrics such as precision, recall, f-score, etc. to evaluate the results and could use various rules to stop training to avoid overfitting.
  2. Given 1 above, using a system like [W&B](https://wandb.ai/site) to track the intermediate experiment results is quite convenient for model selection.
- Making good training datasets may be more important than training hyperparameter tuning.
- Given the same dataset and base model, hyperparameter tuning is the key to fine-tuning quality, which is still largely an art (tricks and tips) than science.
- Merging existing models and/or with your own custom ones may lead to the desired results.

The key topics are:

- how to prepare datasets
- how to run the training and generation scripts
- how to merge models and test results

## Prepare Custom Datasets

Stable Diffusion can be fine-tuned in different ways, such as:

- [Text2Image fine-tuning](https://huggingface.co/docs/diffusers/main/en/training/text2image) needs a custom dataset with image and caption pairs, which you can find an example with 15 images of my cat Miles in 
[this folder](https://github.com/harrywang/finetune-sd/tree/main/data/full-finetune/cat). I manually added the captions in `metadata.jsonl` file but you can use models like [BLIP](https://huggingface.co/spaces/Salesforce/BLIP) to generate captions using code or [Automatic1111 SD WebUI](https://github.com/AUTOMATIC1111/stable-diffusion-webui/wiki/Textual-Inversion#preprocess).
- [DreamBooth fine-tuning](https://huggingface.co/docs/diffusers/main/en/training/dreambooth) needs just a few (typically 5 to 15) images of a subject or style with no captions, which you can find 4 sample datasets in [this folder](https://github.com/harrywang/finetune-sd/tree/main/data/dreambooth):
  - `dog`: the five dog images used in the original DreamBooth paper
  - `glasses`: I found some sunglasses images on the Internet trying to reproduce the sunglasses examples from the DreamBooth paper
  - `cat`: my cat Miles
  - `missdong`: I use [MetaHuman](https://www.unrealengine.com/en-US/metahuman) to generate images for a virtual lady, which I named Miss Dong after a Chinese song I like with the same name 董小姐 ([listen on Spotify](https://open.spotify.com/track/2M6vMxGBuWv3IeRhNsEYIm))

One thing to know when preparing the dataset is how the images are preprocessed before being fed to the model for fine-tuning ([see my sample code and examples](https://harrywang.me/clip#image-feature-extraction-and-downstream-tasks)):

- **resized**: the shortest side is resized to 512 by default
- **cropped**: 512x512 is randomly cropped by default - we will use center-cropping instead
- **padded**: if the image is smaller than 512x512, paddings are added

The following shows the results of my default resizing and cropping (left column) and my custom resizing and cropping (right column) - note the custom cropped images focus more on the subject, which is better for fine-tuning:
<img class="mx-auto" src="https://user-images.githubusercontent.com/595772/215897720-a34098ec-ff45-46f6-9dd0-84a48eefa6bc.jpg">

In addition, the images should have variations about the subject, such as:

- facial expression
- camera angles
- pose
- background
- properties (clothing, haircut - not for Miles ^_^)

## Run Training and Use the Fine-tuned Models

To try the examples, you can simply clone the repo and set up the environment as follows (Note that the Python version matters - the code was tested with Python 3.9.x and 3.10.x and 3.8.x may have issues like [[1](https://github.com/d8ahazard/sd_dreambooth_extension/issues/40)])

```
git clone https://github.com/harrywang/finetune-sd.git
cd finetune-sd
python -m venv venv
source venv/bin/activate
pip install -r requirements.txt
accelerate config default
```

Then, you need to log in to HuggingFace with your token and WandB (optional) with your API key:

```
huggingface-cli login
wandb login
```
Then, you can run the training scripts using my cat dataset as follows:

- Conduct a Text2Image fine-tuning using LoRA:

  ```
  export MODEL_NAME="runwayml/stable-diffusion-v1-5"
  export DATA_DIR="./data/full-finetune/cat"
  export OUTPUT_DIR="./models/lora/miles"

  accelerate launch --mixed_precision="fp16"  train_text_to_image_lora.py \
    --pretrained_model_name_or_path=$MODEL_NAME \
    --train_data_dir=$DATA_DIR \
    --dataloader_num_workers=8 \
    --resolution=512 --center_crop --random_flip \
    --train_batch_size=1 \
    --gradient_accumulation_steps=4 \
    --max_train_steps=1500 \
    --learning_rate=1e-04 \
    --max_grad_norm=1 \
    --lr_scheduler="cosine" --lr_warmup_steps=0 \
    --output_dir=${OUTPUT_DIR} \
    --checkpointing_steps=500 \
    --validation_prompt="A photo of a cat in a bucket" \
    --validation_epochs=10 \
    --seed=42 \
    --report_to=wandb
  ```

- Conduct a DreamBooth fine-tuning using LoRA (`sks` below is the special token - see more discussion about this [here](https://www.reddit.com/r/DreamBooth/comments/zc5w3e/rare_tokens_for_dreambooth_training/)):

  ```
  export MODEL_NAME="runwayml/stable-diffusion-v1-5"
  export INSTANCE_DIR="./data/dreambooth/cat"
  export OUTPUT_DIR="./models/dreambooth-lora/miles"

  accelerate launch train_dreambooth_lora.py \
    --pretrained_model_name_or_path=$MODEL_NAME  \
    --instance_data_dir=$INSTANCE_DIR \
    --output_dir=$OUTPUT_DIR \
    --instance_prompt="a photo of sks cat" \
    --resolution=512 --center_crop \
    --train_batch_size=1 \
    --gradient_accumulation_steps=4 \
    --checkpointing_steps=100 \
    --learning_rate=1e-4 \
    --lr_scheduler="constant" \
    --lr_warmup_steps=0 \
    --max_train_steps=1500 \
    --validation_prompt="A photo of a sks cat in a bucket" \
    --validation_epochs=10 \
    --seed=42 \
    --report_to="wandb"
  ```
- Conduct a DreamBooth fine-tuning using LoRA with class prompt (class images generated by the model) and prior preservation (with weight 0.5):

  ```
  export MODEL_NAME="runwayml/stable-diffusion-v1-5"
  export INSTANCE_DIR="./data/dreambooth/cat"
  export CLASS_DIR="./data/dreambooth/cat-class"
  export OUTPUT_DIR="./models/dreambooth-lora/miles"

  accelerate launch train_dreambooth_lora.py \
    --pretrained_model_name_or_path=$MODEL_NAME  \
    --instance_data_dir=$INSTANCE_DIR \
    --class_data_dir=$CLASS_DIR \
    --output_dir=$OUTPUT_DIR \
    --instance_prompt="a photo of sks cat" \
    --class_prompt="a photo of a cat" \
    --with_prior_preservation --prior_loss_weight=0.5 \
    --resolution=512 \
    --train_batch_size=2 \
    --gradient_accumulation_steps=1 \
    --checkpointing_steps=100 \
    --learning_rate=1e-4 \
    --report_to="wandb" \
    --lr_scheduler="constant" \
    --lr_warmup_steps=0 \
    --max_train_steps=1500 \
    --validation_prompt="A photo of sks cat in a bucket" \
    --num_class_images=200 \
    --validation_epochs=10 \
    --seed=42
  ```

If you use W&B, the validation results (4 generated images from the prompt "A photo of a sks cat in a bucket") are automatically tracked:

- at training step 4, the cats are nothing like my cat Miles (underfitting):

  <img class="mx-auto" src="https://user-images.githubusercontent.com/595772/219907652-26dc9bb4-92c2-4b5e-ab0e-8e7b810b8259.png">

- at training step 684, some cats look really like Miles, especially the second and third ones:

  <img class="mx-auto" src="https://user-images.githubusercontent.com/595772/219907654-d041786f-bde9-4f9b-b13a-0ca4ded90413.png">

- at training step 1084, all cats have weird eyes:

  <img class="mx-auto" src="https://user-images.githubusercontent.com/595772/219907656-59bd527d-5556-4168-813d-f96c17728152.png">

In this case, choosing a checkpoint close to step 684 may generate better results. 

Once the training is finished, the fine-tuned LoRA weights are stored in the output folder, which is `./models/dreambooth-lora/miles` for my cat example above. The folder includes the final weights and intermediate checkpoint weights:

<img class="mx-auto" src="https://user-images.githubusercontent.com/595772/219909134-0e8aed54-0857-4e07-afdd-7395e212fe25.png">

Use `generate-lora.py` to generate images using the fine-tuned LoRA weights:

```
python generate-lora.py --prompt "a sks cat standing on the great wall" --model_path "./models/dreambooth-lora/miles" --output_folder "./outputs" --steps 50
```

TODO: Follow this [tutorial](https://www.kombitz.com/2023/02/09/how-to-use-lora-models-with-automatic1111s-stable-diffusion-web-ui/) to use the LoRA models with Automatic1111 SD WebUI.

You can find other examples to run LoRA fine-tuning using other datasets [here](https://github.com/harrywang/finetune-sd) and examples to run DreamBooth without LoRA as well.

However, I have not been able to train a good model on faces (Miss Dong dataset) yet. I also have not experimented with different settings on learning rates, prior preservation, schedulers, and text encoder found [here](https://huggingface.co/blog/dreambooth), which seem to be quite effective on face fine-tuning.

## Merge Models

Another way to get a custom model is via merging existing models, which can be easily done using Automatic1111 WebUI by following the tutorial [here](https://www.youtube.com/watch?v=xLQcWKI5OLk).

I use an example to show why and how to merge models. You can find tons of models and model merging receipts from [civitai.com](https://civitai.com/).

The following XY Plot shows the generated images using the prompt "cat" and seed values from 1 to 5 from three models (top to bottom):

<img class="mx-auto" src="https://user-images.githubusercontent.com/595772/219909592-4c5a15b4-34ae-4f22-b2c7-6de5c1f6de51.png">

- the [SD 1.5 base model](https://huggingface.co/runwayml/stable-diffusion-v1-5/tree/main): the cats are realistic, i.e., like real cats
- the [Dream Shaper model](https://civitai.com/models/4384/dreamshaper): the cats are very "dreamy", i.e., cartoon-looking with a more artistic touch.
- my merged model of the two models above with multiplier setting 0.3, which means I want to have 30% of the DreamShaper model effect merged into the SD 1.5 base model. The results clearly show the balanced effect - realistic with a more dreamy feeling (merging the two models only takes less than 2 minutes on Tesla V100).

<img class="mx-auto" src="https://user-images.githubusercontent.com/595772/219909605-15a05e93-03f7-4c0b-99c3-fa4e5cb85820.png">

By repeating the model merging steps, you can generate models with targeted effects, and many popular models are merged models, such as [DreamShaper](https://civitai.com/models/4384/dreamshaper) mentioned above, [Photogen](https://civitai.com/models/3666/protogen-x34-photorealism-official-release), [PastelMix](https://civitai.com/models/5414/pastel-mix-stylized-anime-model) and many NSFW models.


## References

- [https://huggingface.co/blog/lora](https://huggingface.co/blog/lora)
- [https://huggingface.co/blog/dreambooth](https://huggingface.co/blog/dreambooth)
- [https://tryolabs.com/blog/2022/10/25/the-guide-to-fine-tuning-stable-diffusion-with-your-own-images](https://tryolabs.com/blog/2022/10/25/the-guide-to-fine-tuning-stable-diffusion-with-your-own-images)
- Various posts in the [unofficial Stable Diffusion subreddit](https://www.reddit.com/r/StableDiffusion/)

PS. The first image for this post is generated via [Midjourney](https://www.midjourney.com/) using the prompt "experiment cooking with thousands of different receipts and flasks flying in the universe ".