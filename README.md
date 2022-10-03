# Welcome to Transformer Reinforcement Learning X (`trlX`)
> A repo for distributed training of language models with Reinforcement Learning via Human Feedback (RLHF)


## Overview
The `trlX` repo allows you to fine-tune Huggingface supported language models up to 20B parameters via either reinforcement learning using a provided scoring function or reward-labeled dataset. We aim to support a range of both online and offline RL algorithms including Proximal Policy Optimization (PPO), Natural Language PPO (NLPPO), Actor Critic (A2C), and Implicit Q Learning (ILQL).

Currently the library supports `gpt2` and `gptj` with plans to include `GPT-NeoX`, `T5` and more. Disibtributed training has been implemented via HF Accelerate and tested up to two nodes, each with 8 gpus.

## Structure

The training pipeline is broken into four pieces:

- Prompt pipeline: Handles loading of prompts/text used for exploration in online methods
- Rollout pipeline: Handles loading and storage of reward labeled data used
- Orchestrator: Handles exploration/rollout collection of online methods. Pushes collected rollouts to the rollout pipeline.
- Model: Wraps the supplied base model (ex: `gpt2`) and implements the desired training method loss (ex: PPO).


## How to add a task
Fine-tuning a language model via PPO consists of roughly three steps:

1. **Rollout**: The language model generates a response or continuation based on query which could be the start of a sentence.
2. **Evaluation**: The query and response are evaluated with a function, model, human feedback or some combination of them. The important thing is that this process should yield a scalar value for each query/response pair.
3. **Optimization**: This is the most complex part. In the optimisation step the query/response pairs are used to calculate the log-probabilities of the tokens in the sequences. This is done with the model that is trained and and a reference model, which is usually the pre-trained model before fine-tuning. The KL-divergence between the two outputs is used as an additional reward signal to make sure the generated responses don't deviate to far from the reference language model. The active language model is then trained with PPO.

We use sentiment learning as an example.

## Installation

### Python package
Install the library with pip:
```bash
pip install trl
```

### From source
If you want to run the examples in the repository a few additional libraries are required. Clone the repository and install it with pip:
```bash
git clone https://github.com/lvwerra/trl.git
cd tlr/
pip install -r requirements.txt
```
### Jupyter notebooks

If you run Jupyter notebooks you might need to run the following:
```bash
jupyter nbextension enable --py --sys-prefix widgetsnbextension
```

For Jupyterlab additionally this command:

```bash
jupyter labextension install @jupyter-widgets/jupyterlab-manager
```


## References

### Proximal Policy Optimisation
The PPO implementation largely follows the structure introduced in the paper **"Fine-Tuning Language Models from Human Preferences"** by D. Ziegler et al. \[[paper](https://arxiv.org/pdf/1909.08593.pdf), [code](https://github.com/openai/lm-human-preferences)].

### Language models
The language models utilize the `transformers` library by 🤗 Hugging Face.
