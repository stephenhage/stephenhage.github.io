---
title: Skin Cancer Classifier from Images
layout: post
---
## Introduction
Skin cancer affected 3 million people in the United States in 2012, with skin cancers accounting for more cancer diagnoses than all other types combined (PR Newswire, 2018). Even though much has been made of skin cancer awareness, with campaigns to persuade people to take preventative measures, it is both an enormous health risk for Americans, and a large fiscal cost for individuals and insurance companies.

Add to that the fact that trained oncology doctors correctly diagnose skin cancer types approximately 77% of the time (Harvard Dataverse). With a correct early diagnosis, more than 90% of patients experience a full recovery. The goal of this model is to use images available at the Harvard Dataverse to diagnose skin cancer from skin lesions in a Human Against Machine challenge.

## Data Exploration
The primary data source for this model is the series of images included in the data. There are seven classes of skin cancer included, with nearly two-thirds representing melanocytic nevi. In addition to the images, the data also includes patient information such as age and gender, as well as the body location of the image with fifteen unique values.

As a warning, some of the images are gruesome since they represent skin lesions. A sample of images is included at the bottom as a reference ([jump to it](#skinimages) ). Each image is in color and scaled at 600x400. Thus, each image is represented as a 600x400x3 matrix. 

One concern that will need to be addressed in each model is overfitting. This dataset has just over 10,000 images, so after splitting into training and test sets, there is a strong chance of having a poorly generalized neural network.	

Let's jump into the code. This code was originally run on [Kaggle](https://www.kaggle.com/kmader/skin-cancer-mnist-ham10000) so the os package is handy for file path references. The image information is stored in a csv so Pandas is useful here. Sklearn is helpful for setting a benchmark model (and later ensembling), and Keras will be used (with a Tensorflow backend) for deep learning.

```
# import packages for file path reference, linear algebra, plotting, image reading and modeling
import os
import numpy as np 
import pandas as pd 
import matplotlib.pyplot as plt
from skimage.io import imread
from sklearn.model_selection import train_test_split
from sklearn.metrics import accuracy_score
from sklearn.linear_model import LogisticRegression
from keras.preprocessing.image import ImageDataGenerator
from keras.models import Sequential, Model, Input
from keras import layers, callbacks
from keras import backend as K
from keras.utils.np_utils import to_categorical # convert to one-hot-encoding
from keras.utils import plot_model
from keras.optimizers import Adam
from keras.callbacks import ReduceLROnPlateau
import itertools
from glob import glob
from PIL import Image
from xgboost import XGBClassifier

pd.set_option("display.max_columns",20)
basedir = "../input/"
```

The CSV file has information about the diagnoses as well as image names. The image storage structur is odd, though, as the images are split into two directories with no apparent pattern. So an important step to relating the images to the diagnosis codes is to create a dictionary for each image and find which directory it is in for ease of reading the images later.
```
imageid_path_dict = {os.path.splitext(os.path.basename(x))[0]: x
                     for x in glob(os.path.join(basedir, '*', '*.jpg'))}
hmnist = pd.read_csv("../input/HAM10000_metadata.csv")
hmnist['path'] = hmnist.image_id.map(imageid_path_dict.get)
hmnist['dx_code'] = pd.Categorical(hmnist.dx).codes

```

Kaggle has a useful beta function allowing for use of a GPU, which is important for running deep learning models. Even a 1GB GPU can't process the images at full size, so it's important to resize them to require less memory. Even though the primary models run will be focused on computer vision, it may be useful to get some dummy variables  for the gender and localization of the lesion.
```
hmnist['image'] = hmnist.path.map(lambda x: np.asarray(Image.open(x).resize((75,100))))
hmnist[['male', 'female', 'unknown']] = pd.get_dummies(hmnist.sex)
hmnist[['scalp', 'ear', 'face', 'back', 'trunk', 'chest',
       'upper extremity', 'abdomen', 'unknown', 'lower extremity',
       'genital', 'neck', 'hand', 'foot', 'acral']] = pd.get_dummies(hmnist.localization)
       
gencols = ['male', 'female', 'unknown']
loccols = ['scalp', 'ear', 'face', 'back', 'trunk', 'chest',
       'upper extremity', 'abdomen', 'unknown', 'lower extremity',
       'genital', 'neck', 'hand', 'foot', 'acral']
#create test holdout that won't be touched until the model is completed 
holdout = hmnist.sample(n = 100)
hmnist = hmnist.drop(holdout.index)
```

As mentioned earlier, there are only 10,000 images to use for this exercise, which presents a danger of overfitting to the training set. Deep learning has a propensity to overfit even to the validation set, so this is as much to track loss and accuracy for overfitting detection more than for estimating how much the model can generalize. It will also be useful to create a function for plotting, since it will be a repeated process for each model.
```
# split into train/validation sets

x_train_full, x_test_full, y_train, y_test = train_test_split(hmnist, hmnist['dx_code'], test_size=0.20)
x_train = np.asarray(x_train_full['image'].tolist())
x_test = np.asarray(x_test_full['image'].tolist())
y_train = to_categorical(y_train, num_classes = 7)
y_test = to_categorical(y_test, num_classes = 7)

# set up plotting function
def makeplots(h):
    fig, axs = plt.subplots(1,2,figsize=(15,5))
    # summarize history for accuracy
    axs[0].plot(range(1,len(h.history['acc'])+1),h.history['acc'])
    axs[0].plot(range(1,len(h.history['val_acc'])+1),h.history['val_acc'])
    axs[0].set_title('Model Accuracy')
    axs[0].set_ylabel('Accuracy')
    axs[0].set_xlabel('Epoch')
    axs[0].set_xticks(np.arange(1,len(h.history['acc'])+1),len(h.history['acc'])/10)
    axs[0].legend(['train', 'val'], loc='best')
    # summarize history for loss
    axs[1].plot(range(1,len(h.history['loss'])+1),h.history['loss'])
    axs[1].plot(range(1,len(h.history['val_loss'])+1),h.history['val_loss'])
    axs[1].set_title('Model Loss')
    axs[1].set_ylabel('Loss')
    axs[1].set_xlabel('Epoch')
    axs[1].set_xticks(np.arange(1,len(h.history['loss'])+1),len(h.history['loss'])/10)
    axs[1].legend(['train', 'val'], loc='best')
    plt.show()
```

### A simple CNN 
In order to prevent overfitting, the CNN will be used in combination with Keras’ image data generator function. This function will allow for repeated use of the same images, but with random rotations, random zoom, as well as horizontal- and vertical-flipping.
```
# Data generator and flow from directory
## Scale for the CNN and, since there aren't many training images, take advantage of the data generator and flip/zoom/rotate funcs
train_datagen = ImageDataGenerator(
    rescale = 1./255,
    rotation_range = 40,
    shear_range = 0.2,
    zoom_range = 0.2,
    horizontal_flip = False,
    vertical_flip = False)

epochs = 25
batch_size = 32
test_datagen = ImageDataGenerator(rescale = 1./255)
train_generator = train_datagen.flow(
    # train_dir,
    x_train,
    y_train,
    # target_size = (75, 100, 3),
    batch_size = batch_size,
   )
test_generator = test_datagen.flow(
    x_test,
    y_test,
    batch_size = batch_size)
	
```

This first CNN uses three convolutional layers with max pooling, before adding a fully-connected layer. Each convolutional layer is activated with relu, and is followed by 2x2 max pooling. Prior to the final predictor layer, the model is flattened. Since there are seven classes of cancer being predicted, the final layer uses a softmax activation. Rmsprop is the optimizer, with categorical cross-entropy as the loss function. 
Due to the data generator, even with thirty epochs the validation accuracy rises with the training set accuracy. This implies that the model is not overfitting to a few random images. On the validation set, this first CNN realizes a 68% accuracy rate. This falls short of the human baseline.

```
model = Sequential()
model.add(layers.Conv2D(32, (3, 3), activation = 'relu', input_shape = (100, 75, 3)))
model.add(layers.MaxPooling2D(2, 2))
model.add(layers.Conv2D(64, (3, 3), activation = 'relu'))
model.add(layers.MaxPooling2D(2, 2))
model.add(layers.Conv2D(128, (3, 3), activation = 'relu'))
model.add(layers.MaxPooling2D(2, 2))

model.add(layers.Dense(512, activation = 'relu'))
model.add(layers.Flatten())
model.add(layers.Dense(7, activation = 'softmax'))
model.compile(optimizer = 'rmsprop',
            loss = 'categorical_crossentropy',
            metrics = ['accuracy'])

history = model.fit_generator(
    train_generator,
    steps_per_epoch=250,
    epochs=50,
    validation_data=test_generator,
    validation_steps=50,
    )
    
loss, accuracy = model.evaluate(x_test, y_test, verbose = 1)

makeplots(history)
```
![Model 1](https://github.com/stephenhage/stephenhage.github.io/blob/master/images/hmnist/model1img.png "Model 1")

### Deeper Model
This next iteration adds two more convolutional layers, though max pooling is not used on each because of the scaled down images. In addition to the increase in convolutional layers, it also increases the size of the fully-connected layer, and adds another fully-connected layer before flattening. Because this deeper model is computationally more expensive and prone to overfitting, callbacks are utilized for early stopping if accuracy doesn’t improve after two epochs. It also lowers the learning rate after ten epochs if the validation loss is static. As it turns out, this model surpasses 68% accuracy within seven epochs, though that is about as accurate as this model gets.
```
model = Sequential()
model.add(layers.Conv2D(32, (3, 3), activation = 'relu', input_shape = (100, 75, 3)))
model.add(layers.MaxPooling2D(2, 2))
model.add(layers.Conv2D(64,(3, 3), activation = 'relu'))
# model.add(layers.MaxPooling2D(2, 2))
model.add(layers.Conv2D(128, (3, 3), activation = 'relu'))
# model.add(layers.MaxPooling2D(2, 2))
model.add(layers.Conv2D(256, (3, 3), activation = 'relu'))
model.add(layers.MaxPooling2D(2, 2))
model.add(layers.Conv2D(512, (3, 3), activation = 'relu'))
model.add(layers.MaxPooling2D(2, 2))
model.add(layers.Conv2D(1028, (3, 3), activation = 'relu'))
model.add(layers.MaxPooling2D(2, 2))
model.add(layers.Flatten())
# model.add(layers.Dense(2056, activation = 'relu'))
model.add(layers.Dense(1028, activation = 'relu'))
model.add(layers.Dropout(0.5))
model.add(layers.Dense(7, activation = 'softmax'))
model.compile(optimizer = 'rmsprop',
            loss = 'categorical_crossentropy',
            metrics = ['accuracy'])
early_stopping = callbacks.EarlyStopping(monitor = 'acc', patience = 5)
red_lr = callbacks.ReduceLROnPlateau(
        monitor = 'val_loss',
        factor = 0.1,
        patience = 2)

history = model.fit_generator(
    train_generator,
    steps_per_epoch=250,
    epochs=50,
    validation_data=test_generator,
    validation_steps=50,
    callbacks = [early_stopping, red_lr])
    
loss, accuracy = model.evaluate(x_test, y_test, verbose = 1)

makeplots(history)
```
<img src="https://github.com/stephenhage/stephenhage.github.io/blob/master/images/hmnist/model2img.png"/>

### Try predictions wtih xgboost
 It is at least plausible that the image in combination with the known gender or localization of the image, would yield more information than the image alone. Therefore, an xgboost and a logistic regression model were fit to the non-image data. The predictions made from those two models were given varying weights along with the deep CNN model to see if it could top the 68% accuracy mark. The best accuracy rate achieved through this process was still at 68%.
```
xgb = XGBClassifier()
relcols = gencols + loccols + ['age']
X = x_train_full[relcols]
y = x_train_full.dx_code
X_test = x_test_full[relcols].values
Y_test = x_test_full.dx_code.values
xgb.fit(X.values, y.values)
testpreds = xgb.predict(X_test)
accuracy = accuracy_score(testpreds, Y_test)
```

### Manual Inception multi-branch model
The famous Inception object detection model has an architecture which combines different convolutional layers trained to different depths in order to independently 'discover' patterns and combine to provide a more predictive model. This model borrows from that idea by combining many similar convolutional layers with varying depth. Several attempts were made to combine different numbers of convolutional layers, each to different depths, but none surpassed even 66% accuracy on the validation set.


```
img_in = Input(shape = (100, 75, 3),
            dtype = 'float32',
            name = 'imgs')
branch_a = layers.Conv2D(128, 1, activation = 'relu', strides = 2)(img_in)

branch_b = layers.Conv2D(128, 1, activation = 'relu')(img_in)
branch_b = layers.Conv2D(128, 3, activation = 'relu', strides = 2)(branch_b)

branch_c = layers.AveragePooling2D(3, strides = 2)(img_in)
branch_c = layers.Conv2D(128, 3, activation = 'relu')(branch_c)

branch_d = layers.Conv2D(128, 1, activation = 'relu')(img_in)
branch_d = layers.Conv2D(128, 3, activation = 'relu')(branch_d)
branch_d = layers.Conv2D(128, 3, activation = 'relu', strides = 2)(branch_d)

output = layers.concatenate(
    [branch_a, branch_b, branch_c, branch_d], axis = -1)

branch_a = layers.Conv2D(64, 1, activation='relu', strides=2, padding = 'same')(img_in)
branch_b = layers.Conv2D(64, 1, activation='relu')(img_in)
branch_b = layers.Conv2D(64, 3, activation='relu', strides=2, padding = 'same')(branch_b)
branch_c = layers.AveragePooling2D(3, strides=2, padding = 'same')(img_in)
branch_c = layers.Conv2D(64, 3, activation='relu', padding = 'same')(branch_c)
branch_d = layers.Conv2D(64, 1, activation='relu')(img_in)
branch_d = layers.Conv2D(64, 3, activation='relu', padding = 'same')(branch_d)
branch_d = layers.Conv2D(64, 3, activation='relu', strides=2, padding = 'same')(branch_d)
branch_e = layers.MaxPooling2D(3, strides=2, padding = 'same')(img_in)
branch_e = layers.Conv2D(64, 3, activation='relu', padding = 'same')(branch_e)
branch_f = layers.Conv2D(64, 1, activation='relu')(img_in)
branch_f = layers.Conv2D(64, 3, activation='relu', padding = 'same')(branch_f)
branch_f = layers.Conv2D(64, 3, activation='relu', padding = 'same')(branch_f)
branch_f = layers.Conv2D(64, 3, activation='relu', padding = 'same')(branch_f)
branch_f = layers.Conv2D(128, 3, activation='relu', strides=2, padding = 'same')(branch_f)
output = layers.concatenate([branch_a, branch_b, branch_c, branch_d], axis=1)
output = layers.Flatten()(output)
diagnosis = layers.Dense(7, activation = 'softmax')(output)
model = Model(inputs = img_in, outputs = diagnosis)
model.compile(optimizer = 'rmsprop',
            loss = 'categorical_crossentropy',
            metrics = ['acc'])
history = model.fit_generator(
    train_generator,
    steps_per_epoch=250,
    epochs=50,
    validation_data=test_generator,
    validation_steps=50,
    callbacks = [early_stopping, red_lr])
loss, accuracy = model.evaluate(x_test, y_test, verbose = 1)
```
<img src="https://github.com/stephenhage/stephenhage.github.io/blob/master/images/hmnist/model3img.png"/>

### Recommendations
Some popular science articles of late have claimed that CNN models can correctly classify cancer types with 95% or higher accuracy. It is very likely that those models have access to more images than this dataset has. If one were to simply classify every image as melanocytic nevi, he or she could claim about 66% accuracy. The most successful model in this set was less than 2.5% better. 

Another option to improve accuracy would be, as the research papers have done, to repurpose thoroughly pre-trained CNNs and finely tune them to these 10,000 images. Since CNNs are notoriously adaptable, that may be a viable approach.

One final recommendation would be to attempt these same models on distribute cloud servers such as Google Cloud or AWS. The images in this dataset were, of necessity, downscaled in order for the machine used to process all the parameters and keep the arrays in memory. It is hard to say how much information may be lost in scaling the images down, so perhaps distributed computing could use the same procedures to realize better accuracy.


### Appendix
```
### Sample images
n_samples = 5
fig, m_axs = plt.subplots(7, n_samples, figsize = (4*n_samples, 3*7))
for n_axs, (type_name, type_rows) in zip(m_axs, 
                                         hmnist.sort_values(['dx']).groupby('dx')):
    n_axs[0].set_title(type_name)
    for c_ax, (_, c_row) in zip(n_axs, type_rows.sample(n_samples).iterrows()):
        c_ax.imshow(c_row['image'])
        c_ax.axis('off')
```

### skinimages
~[skin images](https://github.com/stephenhage/stephenhage.github.io/blob/master/images/hmnist/hmnist_imgs.png?raw=true)
