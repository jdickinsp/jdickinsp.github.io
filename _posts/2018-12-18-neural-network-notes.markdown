---
layout: post
title:  "Neural network notes"
date:   2018-12-18 00:00:00 -0800
categories: neural networks
---

#### Intro
- machine learning - applied statistics to estimate functions.
- hyperparameters - parameters set outside the learning algorithm.
- components of ML: optimization algorithm, cost function, model, dataset


#### Neural Networks
- can compute an NAND gate, since NAND gates are universal computation building blocks
- can compute any computation
- learning algorithms tune weights and biases
- logistic function = 1 / (1 + e^(-z)), z = dot_product(w, x) + b
- feedforward nets, no loops
- recurrent nets, feedback loops with neurons active for limited duration
- objective function, commonly is mean squared error
- gradient descent used to solve minimization function
- softmax and log likelihood => outputs probability distribution
- validation data helps find good hyperparameters
- regularization, so network prefers to learn smaller weights, helps prevent overfitting
- regularized networks generalize better
- dropout, disable portion of neurons during run, equivalent to running multiple networks
- hyperparameters - learning rate, L2 reg, mini batch size
- grid search, automatic hyperparameter optimization
- deep learning
  - convolution layers
  - dropout layers for regularization
  - rectified linear units instead of sigmoid
  - uses GPU
  - generate more training samples to improve generality
- deep belief network - generative model, runs the network backwards, generating inputs
