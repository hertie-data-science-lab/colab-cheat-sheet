<p align="center">
<b><a href="#setting-up-colab-workspace">Setting Up Colab Workspace</a></b>
|
<b><a href="#fetching-and-unzipping-files">Fetching and Unzipping Files</a></b>
|
<b><a href="#formatting-in-text-cells">Formatting in Text Cells</a></b>
|
<b><a href="#connect-to-gdrive">Connect to GDrive</a></b>
|
<b><a href="#import-custom-modules">Import Custom Modules</a></b>
|
<b><a href="#display-image-inline">Display Image Inline</a></b>
|
<b><a href="#download-file">Download File</a></b>
|
<b><a href="#free-gpu-ram-upgrade">Free GPU RAM Upgrade</a></b>
|
<b><a href="#prevent-disconnection">Prevent Disconnection</a></b>
|
<b><a href="#save-model-checkpoints">Save Model Checkpoints</a></b>
|
<b><a href="#work-with-large-data">Work With Large Data</a></b>
|
<b><a href="#dump-data">Dump Data</a></b>
</p>


# Google Colaboratory Management Routine 

### Introduction

Google Colaboratory is an amazing tool for your data science and coding journey. It offers free GPU usage for heavy computational tasks, code cells for running code and text cells for note-taking. This guide will help you to set up and get the most out of Colab. 

## I. General Usage:

### Setting Up Colab Workspace

1. Set up Google Drive

Early organization prevents future chaos. Since Colab is working off of GDrive, and taking into account that you will create hundreds of Colab notebook in your hopefully long and prosperous coding career, it pays off to be tidy: 

- Create a new folder in your Google Drive for Colab notebooks; 
- From now on, only create and save Colab notebooks inside this folder. Organize it neatly into projects that you do. Trust me, it will save you countless hours of hunting down scattered notebooks down the line. 

2. Set up your free GPU

If you work on tasks that require heavy computation (deep learning, working on large and complex dataset, etc.), then you can go to Runtime tab -> Change runtime type -> GPU. Otherwise, for learning and writing simple Python programs, it's advisable to keep it as None and reserve the GPU usage for others that need it. 

3. Directories in Colab: 

On the left hand side of your Colab notebook, you will see the Folder symbol. Clicking on it will open up the directory structure within Colab. By default, the working directory of Colab is "content", which has a "sample data" folder, you can check this with the following command: 

```console
# pwd will print the working directory name. The ! or % is a magic prefix that lets you execute system command-line code within the notebook

!pwd
```

You can change the working directory to a new file path in Colab with the following code snippet:

```python
import os
os.chdir(FILEPATH)
```

or use command-line magic

```console
!cd FILEPATH
```

### Fetching and unzipping files

You can download files and datasets from different location on the web with the following command:


```console
# To download file from url

!wget FILE_URL
```

```console
# To unzip file 

!unzip FILE_LOCATION_AFTER_DOWNLOAD
```

### Formatting in Text Cells

Text cells in Colab work similarly to html and markdown. It is a great tool to keep track of your notes and documentation while experimenting with code. For a quick guide on how to become a pro at Colab text cells, you can visit the notebook below:  

[![Open In Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://github.com/Tanu-N-Prabhu/Python/blob/master/Cheat_sheet_for_Google_Colab.ipynb)]


### Connect to GDrive 

Sometimes you need to save things or read files from your GDrive, you can do so with the following code snippets. 

```python
from google.colab import drive
drive.mount('/content/gdrive')
```

## Import Custom Modules 

To import custom libraries and modules stored in Drive or Dropbox:

```python
import sys
sys.path.insert(0, module_path)

import your_module
```

## Display Image Inline 

```python
from IPython.display import Image, display
Image(IMAGEPATH)
```

## Download File

Sample operation:

```python
# download model image file
from google.colab import files
files.download(FILEPATH)
```

## II. Advanced Usage 

### Free RAM Upgrade 

Users are automatically alloted 12GB of RAM on Colab. However, this memory space is often difficult to work with for large dataset. To upgrade RAM resources to 25GB, we can execute the following code block: 

```python
a = []
while(1):
    a.append('1')
```

This infinite loop will cause the current session to crash, prompting Colab to ask if we want to upgrade to higher RAM space. Click "yes" and we're in!

Please use this sparingly, only when you really need more memory. Otherwise, I doubt Google will allow for this cheat code. Thank you! 

## Prevent Disconnection

Colab automatically disconnects your session if you don't interact with it after 30 minutes, causing all your data and variables to be lost. This is annoying when you're just training a model and don't want to sit there and babysit your kernel. 

To prevent this, we can run the following in the page console. To access this, right click on the page and choose Inspect (Command + Shift + C for Mac users). Then click on Console and paste the following code snippet:

```javascript
function ClickConnect(){
console.log("Working"); 
document.querySelector("colab-toolbar-button").click() 
}setInterval(ClickConnect,60000)
```

Then click Enter. Wait for a minute and see if "Working" is printed on the console before exiting. This code will simulate user interaction with the kernel every 60 seconds to prevent disconnection. 

## Save Model Checkpoints

It is good practice to save model weights as it trains to prevent having to start from scratch when Colab runtime restarts during training. There are dedicated services like [Weights and Biases](https://www.wandb.com/) to help with this in a more structured way, but it might just be easier to save it natively onto GDrive.

The following code helps to create a collection of checkpoint files that updates at the end of each epoch, saving best weights based on validation accuracy. 

More details for [TensorFlow](https://colab.research.google.com/github/tensorflow/docs/blob/master/site/en/tutorials/keras/save_and_load.ipynb#scrollTo=S_FA-ZvxuXQV). 

For TensorFlow:

- Save model checkpoints

```python

import os 
checkpoint_path = "training_1/cp.ckpt" 

checkpoint_dir = os.path.dirname(checkpoint_path)

 # Create checkpoint  callback 
cp_callback =ModelCheckpoint(checkpoint_path, 
     monitor='val_acc',save_best_only=True,save_weights_only=True,verbose=1)

network_fit = myModel.fit(x, y, batch_size=25, epochs=20,
                                  ,callbacks = [cp_callback] )
```

- Reload model with saved checkpoint weights:

```python

myModel.load_weights(checkpoint_path)
```

For Pytorch: 

- Save model checkpoints:

```python

model_save_name = 'classifier.pt'
path = f"/content/gdrive/My Drive/{model_save_name}" 
torch.save(model.state_dict(), path)
```

- Reload model with saved checkpoint weights:

```python

model_save_name = 'classifier.pt'
path = f"/content/gdrive/My Drive/{model_save_name}"
model.load_state_dict(torch.load(path))
```

## Work With Large Data

When working with large dataset with hundreds of thousands of files stored on GDrive, "[Errno 5] Input/output error" will often occur as GDrive operations time out due to the large number of files that it has to supply to Colab. To resolve this issue, the best way is to upload a zipped file with all the data that you need onto GDrive, connect Colab to GDrive and unloading that zipped file into the local environment of Colab. 

In this way, Colab can work with the dataset directly in its native environment, without having to reach back to Drive to access the file, which will cause major read time issue and IO error. 

```console
import os
!cp GDrive_path_to_zip_file Colab_path
os.chdir(Colab_path)
!unzip -qq zip_file
```

The above code will copy the zipped file in the GDrive path to the desired Colab local directory, and then unzip the file quietly so you can work with it directly in the Colab environment. 

## Dump Data

For long tail operations that needs large variable storage, Pickle seems to be the easiest option as it can store list, dictionary or any data object as binary files to be accessed for later use. Example:

To dump the clusters_rural variable for later use: 

```python
#Dump file for later integration with original dataframe
import os
import pickle

os.chdir('/content/drive/My Drive/Thesis/OSM_data')

with open('clusters_rural_pois', 'wb') as fp:
    pickle.dump(clusters_rural, fp)
```

To load back variables: 

```python
with open('clusters_rural_pois', 'rb') as fp:
  clusters_rural = pickle.load(fp)
```

However, this method is inefficient for image files variables as the binary format requires a huge amount of write-memory. I haven't figured out a better data storage method for this type. Please let me know if you have any idea.