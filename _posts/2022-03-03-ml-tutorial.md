---
layout: post-toc
title: A Minimalist End-to-End Machine Learning Tutorial
tags: tutorial open-source coding
permalink: ml
---

This tutorial is developed based on teaching materials from my courses at the University of Delaware and my professional training workshops for industry practitioners. Many of the tools have been/are being used by data scientists and machine learning engineers in my teams. My goal is to have a minimalistic coverage of key concepts and steps in a typical end-to-end machine learning project with open-source/free tools, simple datasets, and well-documented coding examples.

The key topics include:

- Full machine learning pipeline ([Numpy, Pandas, Matplotlib, Seaborn](https://harrywang.me/ds), [Scikit-learn](https://scikit-learn.org/stable/)
- Data app development and deployment ([Streamlit](https://streamlit.io/))
- AutoEDA ([Pandas-profiling](https://github.com/ydataai/pandas-profiling)) and AutoML ([AutoGluon](https://auto.gluon.ai))
- MLOps, experiment tracking and automation ([ClearML](https://clear.ml/))
- No-Code machine learning ([Rapidminer](https://rapidminer.com/))

All data files, notebooks, models are in the [code repo](https://github.com/harrywang/mini-ml).

## Local Setup

Tested on Python 3.9.7 (refer to my [tutorial on setting up Python development environment on Mac](https://bit.ly/2uX6wAX))

Clone the repo, switch to the repo folder, setup the virtual environment, and install the required packages as follows:

```shell
git clone https://github.com/harrywang/mini-ml.git
cd mini-ml
python3 -m venv venv
source venv/bin/activate
pip install -r requirements.txt
```

Then run `jupyter lab` or `code .` (if use [VSCode](https://code.visualstudio.com/)) to go over the notebooks.

[Back to Top](#title)

## Full ML Pipeline

The `mini-ml.ipynb` notebook demonstrates the following key steps of an end-to-end machine learning project with detailed inline comments and Markdown cells:

- Exploratory Data Analysis (EDA)
- Data pre-processing and pipeline
- Model building, evaluation, tuning, and selection
- Feature importance analysis and feature selection
- Model persistence

Note that the focus is to show the overall workflow not to build the best performing model.

You can run the notebook directly from [Kaggle](https://www.kaggle.com/harrywang/minimalist-machine-learning-tutorial).

[Back to Top](#title)

## Data App

After training a model using the pipeline shown above, you can deploy the model in the cloud to make predictions. 

Streamlit enables fast data app development and deployment using Python with no front‑end experience required. 

`streamlit-app.py` shows how to read the model generated from the `mini-ml.ipynb` notebook and create an interactive data app with a nice UI:

<img class="mx-auto" width='400' src="https://user-images.githubusercontent.com/595772/156841714-1bf12a2f-ac0c-4a81-991c-5f392bc06bc3.png">

See the demo app live at:

- [Streamlit Cloud](https://share.streamlit.io/harrywang/streamlit-basics/app.py)
- [Heroku](https://st-demo-harrywang.herokuapp.com/)

Follow the steps below to get the data app running locally for testing:

- Run `mini-ml.ipynb` notebook to generate the model file `clf-best.pickle`
- Make sure the virtual environment is activated: `source venv/bin/activate`
- Run the app from the root of the repo folder using terminal: `streamlit run streamlit-app.py`, then open a browser to visit the app at [http://localhost:8501](http://localhost:8501) 
- Stop the app by using Ctrl + C or closing the terminal

Follow my [tutorial](https://github.com/harrywang/streamlit-basics) to deploy the app to the cloud for public access via services such as Streamlit Share, Heroku, or AWS.

[Back to Top](#title)

## AutoEDA and AutoML

The `automl.ipynb` notebook shows how you can conduct automatic EDA with `pandas_profiling` and AutoML with `autogluon`.

With a few lines of code, `pandas_profiling` can generate a comprehensive EDA report:

<img class="mx-auto" src="https://user-images.githubusercontent.com/595772/156847132-ceb01927-0755-4335-97d3-006d1bcc5fde.png">

`autogluon` can automatically train and tune many models and generate a leaderboard to show the model performance (**NOTE**: I use `autogluon.tabular` to support Mac with M1 chip - some packages, such as CatBoost, do not support M1 chip yet. A full version of `autogluon` is used in the [Kaggle version](https://www.kaggle.com/harrywang/autoeda-and-automl)):


`autogluon.tabular` version tries 8 models:

<img class="mx-auto" width='400' src="https://user-images.githubusercontent.com/595772/156929364-f7c940a1-b3b4-4b60-8486-9a06a448ce53.png">

`autogluon` full version tries 14 models:

<img class="mx-auto" width='400' src="https://user-images.githubusercontent.com/595772/156928522-650fb4c9-43c3-4540-84c8-85d69ab8884a.png">


Although `autogluon` and other similar AutoML packages/systems can produce good performing models with minimal effort, the results are often generated in a "black box" fashion. It is still critical to learn EDA techniques and machine learning algorithms/pipelines as shown in the `mini-ml.ipynb` notebook to better understand the results and conduct additional model tuning and selection. 

A good practice is to use these packages to get a good performance benchmark before trying other models with potential more data collection and feature engineering.

[Back to Top](#title)

## MLOps

MLOps is very important for systematic tracking, management, and deployment of ML/AI projects. When training AI models, many experiments are conducted to apply different datasets, hyperparameters, epochs, etc. Without a MLOps system, it's very difficult, if not impossible, to keep track of the settings of the experiments, compare results, and automate executions. In `clearml-sklearn.ipynb` and `clearml-pytorch.py`, I demonstrate how to use ClearMl to do experiment tracking. 

I have used other similar products, such as [WandB](https://wandb.ai) and choose [ClearML](https://clear.ml/) for a few reasons:

- It's open-source
- It's super easy to set up and non-intrusive to your code
- We have deployed ClearML for our AI team in Shanghai to use since early 2021 (even before they changed the name from Allegro Trains to ClearML).

Follow the steps below to setup ClearML and run the programs:

- Sign up an account at [https://clear.ml/](https://clear.ml/) and create clearml credentials at [https://app.clear.ml/settings/workspace-configuration](https://app.clear.ml/settings/workspace-configuration)

<img class="mx-auto" src="https://user-images.githubusercontent.com/595772/156857265-3f81219d-19d0-4696-bcb8-38ad6e2be4e2.png">

<img class="mx-auto" src="https://user-images.githubusercontent.com/595772/156857337-f85a6650-8865-4111-a217-903b987ecc50.png">

- run `clearml-init` and follow the setup instructions, a new configuration file will be created at `~/clearml.conf`
- restart JupyterLab or VSCode if needed to load the new configuration file
- run `clearml-sklearn.ipynb` notebook to train a decision tree classifier and an SVM classifier and log the experiment parameters and performance results. 

<img class="mx-auto" src="https://user-images.githubusercontent.com/595772/156858992-8ebcaade-1bfd-48d0-8bf4-97a36271093d.png">

<img class="mx-auto" src="https://user-images.githubusercontent.com/595772/156858995-f6e6b15a-6e52-4ccc-aedc-6c31fd48b24e.png">

- run`python clearml-pytorch.py` to see another example on training a deep learning model (the well-known MNIST handwritten recognition algorithm) using PyTorch

[Back to Top](#title)

## No-Code ML

I use RapidMiner to teach basic machine learning to less technical audiences, such as product managers, executives, MBA students, etc. [RapidMiner Studio Free version](https://rapidminer.com/products/studio/) is enough, which has 10,000 data rows limit.

In the `rapidminer` folder, I created a No-Code version of the `mini-ml.ipynb` notebook using Rapidminer (v9.10).

- `mini-ml.rmp` is the training process, where two models, i.e., decision tree and random forest, are trained (typical CV with a Grid Search) for a simple classification problem. 

<img class="mx-auto" src="https://user-images.githubusercontent.com/595772/156889739-f44f7d02-d219-4136-8733-698bec46ec00.png">

Create a new process and import `mini-ml.rmp` as follows:

<img class="mx-auto" width="400" src="https://user-images.githubusercontent.com/595772/156865478-72d5281a-bb8c-49a6-80d4-d0b60bc5c48b.png">

Then, select the Read CSV operator and change csv file path to the `titanic.csv` in the repo folder:

<img class="mx-auto" src="https://user-images.githubusercontent.com/595772/156865384-b0a23b42-e6da-4fe7-a62e-4378de5233a2.png">

Click the run button to finish the training process and the resulting decision tree model is saved as `mini-ml-model.ioo` as found in the `rapidminer` folder.

<img class="mx-auto" width="400" src="https://user-images.githubusercontent.com/595772/156889924-cbe3e962-89b9-41c9-982a-dafc8eb3b5e7.png">


- `mini-ml-predict.rmp` is the prediction process, where the `mini-ml-model.ioo` decision tree model generated by the `mini-ml.rmp` process above is loaded to make predictions.

<img class="mx-auto" src="https://user-images.githubusercontent.com/595772/156890008-595ea792-c697-4e24-b0a6-21e627355ccc.png">

[Back to Top](#title)