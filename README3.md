# ANNADL - Visual Question Answering

## Preparation

We have used the Google Colab environment to run our models.

We have uploaded the images to Google Drive and imported the data from there.

## Data Preparation & Augmentation

We have generated statistics from training answers to observe that the dataset is heavily unbalanced towards yes/no types of answers.

We have encoded the questions using the word2vec model provided by the Gensim library. We have decided to use the window length as 5 and vector length 100 after some empirical investigation. We have used the similarities of specific words to determine the success of the built word2vec model. We have trained the encoder in a different vector and export the trained models.

## Training Decisions

We have constructed our network so that we use a CNN to encode images, then another encoder network to train on the word2vec encoding of the images considering the sequentiality of the sentences, and finally combine them in a classifier network for the labels provided as the answers.
We have set the number of dense layers, and dropout ratio as hyperparameters beforehand.

### Image encoding

We first decided to use VGG to encode the images by removing the last layer of the pre-trained network.
The resulting encoding didn't perform well considering that VGG is a network trained on detecting images, but it is not trained to directly provide relations between different objects.
We have considered re-training VGG however considering the number of parameters it has, we decided not to do it, since it would take too much time.

Instead, we have decided on training an autoencoder for the provided images. We have implemented a basic encode-decode network and after some trial and error with the parameters and order of the Convolution, MaxPooling, BatchNormalization, and UpSampling layers, we have obtained 75% accuracy for the autoencoder.

### Word encoding

At first, we have directly fed the output of the word2vec model to the Concatenate layer to see if the network can optimize without requiring the location input inside the sentences.
Observing that it is necessary, we have added an LSTM layer to train the model between the input and the Concatenate layer, with the amount and units also being defined as the model's hyperparameters.

We also experimented with the GloVe encoding but the performances weren't high enough to justify its usage, so we reverted to our Word2Vec encodings.

### Loss functions

At first, we have tried training our neural network with CategoricalCrossEntropy, which provided good results, however didn't compensate for the class imbalance.
So, we have created a custom loss function that multiplies the result by the disparity between the ratio of classes in the training set.

The loss function directly using the ratios from the training dataset didn't consider the types of questions, so it made the model respond with mixed responses, without allowing it to grasp the concepts in the questions.
To compensate for that we have categorized answers given in the dataset to various types and functions that they serve (such as "running" is an action while "pizza" is an edible object).
We have classified many words under more than one category, considering the types of questions that they appear in, for example, the word "watermelon" appears in questions that both target eating and being placed, so watermelon is both an object and an edible.
We have adjusted our loss function to increase the loss more if the category of the answer and the question didn't match, which helped the model to diversify its answers.
