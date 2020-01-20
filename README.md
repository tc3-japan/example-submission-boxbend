# sample-submission-boxbend
This is a sample submission for the challenge "Predict how different shapes bend under pressure".

## Sample submission overview
There are DockerFile and some scripts, train.sh, pred.sh and model.py in code/ directory.
You can use this sample on your own environment for testing.
train.sh starts training data then create model file, and pred.sh starts prediction with the mode file.

## setup
create `/data` directory and sub-directory below:
```
/data
├── gt
│   └── train
├── input
│   ├── pred
│   └── train
└── output
     └── pred
```
You put data on your local machine. Data for train into /data/input/train, data for prediction into /data/input/train and ground truth for training into /data/.
If you want to put the data other than above directories, update the path in train.sh and pred.sh.


Download this sample code then build with docker build command.
```
docker build {IMAGE_NAME}:{TAG} .
```

## Usage
### train.sh and pred.sh
First run train.sh with the following options:
```
docker run --rm -v /data:/data -v /tmp:/tmp {IMAGE_NAME}:{TAG} /code/train.sh
```
All train files and ground truth for train are read with this command. then model will be created in `/tmp` directory.

Next run pred.sh with the following options:
```
docker run --rm -v /data:/data -v /tmp:/tmp {IMAGE_NAME}:{TAG} /code/pred.sh
```
Prediction files will be created into /data/output/pred


### model.py
`model.py` comes as a single Python script. As you can see from the help, it comes with 3 commands (all of them have --help so I have omitted that for brevity):
```
$ ./model.py --help
Usage: model.py COMMAND [ARGS]...

Commands:
  predict-all
  predict-one
  train
```
The first step is to train a model:
```
$ ./model.py train --help
Usage: model.py train INPUT_DIR GROUND_TRUTH_DIR MODEL_FILE
```
This will read all of the files in the input and ground-truth directories, and then use them to build a model. That model will predict displacement in the z-axis only, and assume all other changes are 0. The trained model is written out to the model file. For example (this may take awhile):
```
$ ./model.py train /data/input/train /data/gt/train /data/model.bin
```
Now you can use that model to predict one input:
```
$ ./model.py predict-one --help
Usage: model.py predict-one MODEL_FILE INPUT_FILE OUTPUT_FILE
```
Or all of the inputs in a directory:
```
$ ./model.py predict-all --help
Usage: model.py predict-all MODEL_FILE INPUT_DIR OUTPUT_DIR
```
For example:
```
$ ./model.py predict-one /data/model.bin \
  /data/input/train/x40y10z10t1uh1d3p1s1sh1.json \
  /data/output/train/x40y10z10t1uh1d3p1s1sh1.csv
```
Or (this may also take awhile):
```
$ ./model.py predict-all /data/model.bin /data/input/pred /data/output/pred
```
