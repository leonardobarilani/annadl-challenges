# ANNADL - Image Segmentation

## Preparation

We have used Google Colab environment to run our models.

We have uploaded the images to Google Drive and imported the data from there.

To compensate for all the randomness, we have fixed all the seeds that Python and all the libraries that we use to constants to make sure.

To easily load different types of datasets we have created a DataPath class that allows us to pass around a variable that represents the exact dataset we want to use.

## Data Preparation & Augmentation

At first, we tried to use the ImageDataGenerator to pull the training and test images directly with the `flow_from_directory` method, however it has failed to provide us with the tooling that we wanted to have, thus we have created a custom dataset class, inspired from the Multiclass_Segmentation example from the 4th Keras tutorial.

We have imported the image augmentation parameters from our previous tasks and decreased the values for the shift ranges, considering that it would yield a better result since shifting the masks too much would create collisions between different classes.

At first, we have shrunk the images and the masks to (256, 256), however we later decided to crop the images to (256,256) and discard the insignificant image parts (that are only composed of the background).

Since the given sample images are (2048,1536) we were able to extract 48 image parts per image (excluding image parts with only backgrounds, however, have decreased this number).

## Training Decisions

### Encode & Decode Architecture

At first, we have imported the implementation on Multiclass_Segmentation example, that uses VGG16 to encode the input, and upsampling and convolution layers to decode the input back to shape.

We have tried adding more layers, increase the patience on EarlyStopping callback, different networks (DenseNet101, VGG19, ResNet50, Xception, MobileNetV2), however all the experiments we have tried didn't learn anything.

### U-Net Architecture

None of the changes that we did helped us with the noise problem, thus we have switched to the U-net architecture. In particoular we used the network presented in [this article](https://keras.io/examples/vision/oxford_pets_image_segmentation/).

On the submission this network implementation has scored more than 0.002

### Loss functions

At first we have started with SparseCategoricalCrossentropy.

Observing that the dataset provided is not balanced, we have extracted class weights from a sample image, and observed that the validation IoU has increased.

Thus, we have implemented a custom loss function, using SparseCategoricalCrossentropy underneath, that also added the sample weights for the classes in the mask.

We have found different loss functions through [an article written about image segmentation](https://lars76.github.io/2018/09/27/loss-functions-for-segmentation.html).

We have taken the loss functions from this article and adapted if needed to work with our architecture, and we tried different loss functions.

The best performing one (based on validation loss and validation meanIoU) was Tversky Loss with its beta value 0.3.

Our submission with this loss function has increased the submission score to 0.02.

## Submission

After our first submissions, we have realized that our network is learning for each subset of data specifically, so to increase the submission score, we have created a function that would append the results that we have from re-training each network on different datasets, using the weights from the neural network we first trained.
