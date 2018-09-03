

[![license](https://img.shields.io/github/license/mashape/apistatus.svg?maxAge=2592000)](https://github.com/fadymedhat/MCLNN/blob/master/LICENSE)

MCLNN dataset transformation
========
The transformation involves the generation of a single file containing the intermediate representation of a signal,
e.g. spectrogram in case of sound. 

The transformer loads each file in order from the dataset, applied the intermediate representation and stores the resulting
transformation to a single hdf5 file (referred later as Dataset.hdf5) for the whole dataset.


## Configuration 

In this section, we will refer to possible scenarios and their corresponding configurations demonstrated over datasets used 
in the experiments as examples for clarification.


#### Without Augmentation  

Below is the folder structure of the ESC10 dataset. The folders for each sound category are arranged alphabetically
 and similarly for the files within each folder. The intermediate representation can include the 1st derivative 
 across the frames of a spectrogram, which is controlled by the INCLUDE_DELTA flag.
 
 
<p align='center'><img height="300"  src='imgs/esc10folderstructure.png'/></p>


```
class ESC10:
    
    # disable augmentation
    AUGMENTATION_VARIANTS_COUNT = 0
    
    # the original file count for a dataset
    DATASET_ORIGINAL_FILE_COUNT = 400
    
    # this variable adjusts the total count if augmentation is enabled
    TOTAL_EXPECTED_COUNT = DATASET_ORIGINAL_FILE_COUNT + DATASET_ORIGINAL_FILE_COUNT * AUGMENTATION_VARIANTS_COUNT
    
    # the source folder containing the raw files of the dataset
    SRC_PATH = 'I:/dataset-esc10/ESC-10'
    
    # destination path for the single hdf5 containing the whole dataset
    DST_PATH = 'I:/ESC10-for-MCLNN'

    DATASET_NAME = "esc10"
    
    # dataset standard file length = 5 seconds
    DEFAULT_DURATION = "5secs"
    
    # The first frame to extract from the generated spectrogram 
    # at a sampling rate of 22050 sample per second and nfft 1024 overlap 512 > 22050 * 5 sec / 512 = 215 frames
    FIRST_FRAME_IN_SLICE = 4  # to avoid disruptions at the beginning
    
    # the frame count to extract after the first frame to avoid disruptions at the end of the clip
    FRAME_NUM = 200  
   
    # mel-scale filter count
    MEL_FILTERS_COUNT = 60
    
    # width of the nfft window
    FFT_BINS = 1024
    
    # overlapping frames between windows 
    HOP_LENGTH_IN_SAMPLES = 512
    
    # include the 1st derivative between consecutive frames.
    INCLUDE_DELTA = True

    # Number of files to load before starting the transformation
    PROCESSING_BATCH = 10
    
    # Sleep time prevents possible deadlock situations between reading and writing 
    SLEEP_TIME = 0 # operates in combination with PROCESSING_BATCH
      
```


#### With Augmentation 

Augmentation is a method to apply certain controlled deformations to the dataset while keeping the properties of the 
original sample to a certain extent. This process enhances the generalization of a model during training. 

The below figure shows the folder structure expected while generating the .hdf5 for an augmented ESC10 dataset.
This dataset dataset was augmented using 12 variants. Accordingly, the figure shows each original file of the dataset 
and the accompanying 12 versions.

__NOTE:__
The order of the files should match the structure shown below in terms of the original clip followed by its augmented variants
, since the index generation is dependant on it.
 
 <p align='center'><img height="300"  src='imgs/esc10folderstructureaugmented.png'/></p>




```    
class ESC10AUGMENTED:

    # number of augmentations for each clip
    AUGMENTATION_VARIANTS_COUNT = 12
    
    # original dataset clip count without augmentation
    DATASET_ORIGINAL_FILE_COUNT = 400
    
    # total with augmentation 
    TOTAL_EXPECTED_COUNT = DATASET_ORIGINAL_FILE_COUNT + DATASET_ORIGINAL_FILE_COUNT * AUGMENTATION_VARIANTS_COUNT
      
                        .
                        .
                        .
   
```    
    
    
#### Loading index from CSV

If the dataset is accompanied with a CSV file, specifiying the samples filename and category will be needed. Below is a listing for the 
 required configuration.
 
 The below figure shows a chunk of the CSV file released with the Urbansound8k. The file is not exactly the original one,
 but rather a modified version in terms of the rows ordering in the csv file without changing the data and the sequence 
 folder added.
 
 The indices of the two highlighted columns are required for the configuration as shown in the below listing. 
 
<p align='center'><img height='200' src='imgs/urbansound8kcsv.png'/></p>
 
 
```

class URBANSOUND8K:
  
                        .
                        .
                        .
                        
    # the name of the CSV file located in the DST_PATH
    CSV_FILE_PATH = os.path.join(DST_PATH, 'UrbanSound8KwithFileSeq.csv')
    
    # csv column index for file name
    COL_FILE_NAME = 2 
    
    # csv column index for folder name (class category)
    COL_FOLDER_NAME = 9 
    
```