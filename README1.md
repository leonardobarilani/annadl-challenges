# ANNADL - Image Classification

## Preparation

We have used the Google Colab environment to build the project; we linked the dataset from Google Drive.

We have loaded the data directly through appropriate functions in NumPy and matched the classes on the JSON file while we are iterating through all the files in the folder.

Through our experiments, we have used Tensorboard to visualize the loss and accuracy of the models.

We used accuracy as the target metric and categorical cross-entropy as the loss function since it is the recommended classification function.

We have split our dataset to %80 training %20 validation, validation being used for early stopping, and visualizations on Tensorboard to determine how well the model functions.

We have seen that the dataset is imbalanced by a close margin, so we have assigned weights accordingly.

## Non-Transfer Architecture

Our first models consisted of different sequential models.

Inspired by the code provided in the demo sessions, we have built a similar model to the one given. We have tried changing the increase in filter count, applying different filters, changing the kernel size for different layers, adding Dropouts in between, adding BatchNormalization, changing the network's depth, and using different layers in between.

We converged to a solution which consists of 5 layers with Convolution, MaxPooling, and Dropout at 0.2, followed by a Flatten layer, a Dense layer with 128 units, and finally a SoftMax for class prediction.

We scored around 0.65, with the final results a bit above 0.70.

## Transfer Architecture

Our first implementation of Transfer Learning scored 0.79 and used VGG16 with finetuning with freezing the first 15 layers.

After that we tried various networks such as:

* xception
* densenets (DenseNet121, DenseNet169, DenseNet201)
* vgg (VGG16, VGG19)
* resnets (ResNet50, ResNet101, ResNet152, ResNet50V2, ResNet101V2, ResNet152V2)

The best scoring network was VGG16, succeeded by DenseNet121 and DenseNet201. Therefore we picked VGG16 to improve our model further.

We have added different layers, changed hyperparameters like the number of Dense layers in the network. Adding Dropout before the final layer and decreasing the number of trainable layers helped us increase the score, while other experiments were not as successful.

The final score of the model was 0.88.

## Unsuccessful experiments

After observing the success of transfer learning, we have tried combining multiple architectures. Combining VGG and DenseNet architectures side-by-side did not produce better performing results. We have also tried changing the learning rate and the optimization algorithm. However, all of our experiments either resulted in a decrease in learning speed or exploding gradients. Therefore, we decided to submit the VGG based model as-is.

## Evaluation

We have used the validation loss and validation accuracy to monitor the well being of our models. We have compared each iteration's values on Tensorboard and tried to estimate whether different architectures would result in similar results. We have also evaluated the confusion matrix of our models for different categories.

On each successful iteration (both metrics being higher than the previous one), we have run our Kaggle save and upload functions. We saved our models in GitHub to version control with Git and have an easy to access backup. We did a commit for each model that has succeeded better in the Kaggle contest. We have included two models, one best with Transfer Learning, one best without. Many experiments are not a part of the file since they failed and did not become a part of the bundle.

## Replicability of Results

The results that we obtained were not always replicable due to an unknown randomness factor that we haven’t seen or accounted for before in the class or any type of documentation we have seen before, even though we set the Numpy and Tensorflow seeds as advised.
