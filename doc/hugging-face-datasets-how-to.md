# How To: 🤗datasets

This is a short tutorial on how prepare a dataset for using it with the [Hugging Face](https://huggingface.co/) 🤗datatset library.
This will cover the mroe advanced use cases, for the basic ones you can refer to the [official documentation](https://huggingface.co/docs/datasets/), in particular we will cover the loading scripts which are not well explained in the documentation.

A loading script runs **locally** and it is used to download the data from a generic sources and prepare them for use in the library.
The simplest source is the Hugging Face Hub repos themselves, but you can also download the data from other sources, like a remote server.

# 1. Prepare the data

A good way to handle the data is to store it in tar archives: the python `tarfile` module is able to read and write them.
The 🤗datasets library is able to read the data from a tar archive, and it will be able to download it from the Hugging Face Hub.

This step can be done in a notebook, or in a script. Here is an example:
    
```python
import tarfile
import os
from sklearn.model_selection import train_test_split

video_folders = []
for x in os.listdir():
    if os.path.isdir(x):
        for y in os.listdir(x):
            if os.path.isdir(os.path.join(x, y)):
                video_folders.append(os.path.join(x, y))

videos = []

for path in video_folders:
    video = []
    for file in os.listdir(path):
        if file.endswith(".pgm"):
            video.append(os.path.join(path, file))

    videos.append(sorted(video, key=lambda x: int(x.split('_')[2])))

train_ratio = 0.75
validation_ratio = 0.15
test_ratio = 0.10

train, test = train_test_split(images, test_size=1-train_ratio, random_state=42)
validation, test = train_test_split(test, test_size=test_ratio/(test_ratio + validation_ratio), random_state=42)

with tarfile.open("train.tar.gz", "w:gz") as tar:
    for image in tqdm(train):
        tar.add(image)

with tarfile.open("test.tar.gz", "w:gz") as tar:
    for image in tqdm(test):
        tar.add(image)

with tarfile.open("validation.tar.gz", "w:gz") as tar:
    for image in tqdm(validation):
        tar.add(image)

```

In this example we have a folder containing a folder for each video, and each video folder contains the frames of the video as a sequence of images.
We want to create the dataset splits to use the functionality of the 🤗datasets library to load the splits independently, for this we use the `train_test_split` function from `sklearn`.

In the first two loops we collect the paths of the images, and then we sort them by the frame number.
Then we split the data in train, test and validation, and we create the tar archives.

# 2. Upload the data to the Hugging Face Hub

At this point it is useful to already upload the archives to the Hugging Face Hub, so that we obtain the links that we will use in the loading script.

1. Create a new dataset on the Hugging Face Hub, for users in  an organization this can be done from the organization page: 


2. Upload the archives to the dataset, you can do this from the dataset page, using the `Add file` button:



4. After uploading the tar archives, the link for donwlading them will be available by clicking the small download arrow on the right of the file size:



Right click the arrow and copy link.

# 3. Prepare the loading script

The last step is to prepare the loading script, which will be used to download the data from the Hugging Face Hub and prepare it for use in the library.
Here is an example of a loading script for the dataset we have prepared:

```python
import datasets
from datasets import GeneratorBasedBuilder

_DATA_URL = {
    "train": ["https://huggingface.co/datasets/ami-iit/url/to/train.tar.gz"],
    "validation": ["https://huggingface.co/datasets/ami-iit/url/to/validation.tar.gz"],
    "test": ["https://huggingface.co/datasets/ami-iit/url/to/test.tar.gz"],
}

class VideoDataset(GeneratorBasedBuilder):
    VERSION = datasets.Version("0.0.1")
    def _info(self) -> datasets.DatasetInfo:
        return datasets.DatasetInfo(
            description="Example dataset",
            features=datasets.Features(
                {
                    "video": datasets.Sequence(datasets.Image()),
                    "n_frames": datasets.Value("int32"),
                    "camera": datasets.Value("string"),
                }
            )
        )
    
    def _generate_examples(self, archives):
        """Yields examples."""
        idx = 0
        for archive in archives:
            old_path = None 
            video = []
            for path, file in archive:
                if old_path is not None and path.split('/')[0:2] != old_path: # A new video has started saving the previous one
                    ex = {"video": video,
                        "n_frames": len(video),
                        "camera": old_path[1]}
                    yield idx, ex
                    idx += 1    
                    video = []  

                if path.endswith(".pgm"):
                    video.append({"path": path, "bytes": file.read()})

                old_path = path.split('/')[0:2]

            

    def _split_generators(self, dl_manager: datasets.DownloadManager):
        """Returns SplitGenerators."""
        archives = dl_manager.download(_DATA_URL)
        
        return [
            datasets.SplitGenerator(
                name=datasets.Split.TRAIN,
                gen_kwargs={
                    "archives": [dl_manager.iter_archive(archive) for archive in archives["train"]]
                },
            ),
            datasets.SplitGenerator(
                name=datasets.Split.VALIDATION,
                gen_kwargs={
                    "archives": [dl_manager.iter_archive(archive) for archive in archives["validation"]]
                },
            ),
            datasets.SplitGenerator(
                name=datasets.Split.TEST,
                gen_kwargs={
                    "archives": [dl_manager.iter_archive(archive) for archive in archives["test"]]
                },
            )
        ]
```

The script contains a class derived from `GeneratorBasedBuilder`. We need to at least define the methods `_info`, `_generate_examples` and `_split_generators`.

The `_info` method defines the features of the dataset, in this case we have a sequence of images, the number of frames and the camera name, so each datasets example will be a dictionary with these three keys.

The `_split_generators` method defines the splits of the dataset, and instructs the download manager on how to download the data. The download manager will download the archives and return a dictionary with the paths to the downloaded archives. Then we return a list of `SplitGenerator` objects, one for each split. Each `SplitGenerator` object has a `name` and a `gen_kwargs` attribute. The `name` attribute is the name of the split, and the `gen_kwargs` attribute is a dictionary of keyword arguments that will be passed to the `_generate_examples` method. In this case we pass a list of generators, one for each archive, that will be used to iterate over the files in the archive.

The `_generate_examples` method is a generator that yields the examples of the dataset. It needs to be tailored to the requirements of the examples and the structure of the original data.

When the loading script is ready it can be uploaded to the hub, in the same way we uploaded the data archives.

# 4. Load the dataset and enjoy!

Now we can load the dataset from the hub and use it in the library:

```python
import datatsets

dataset = datasets.load_dataset("ami-iit/dataset_name", split="train", streaming=True, use_auth_token=True)
```

It is important to log in to the Hugging Face Hub before loading the dataset, use `huggingface-cli login` to log in.
The `use_auth_token=True` argument is necessary to download the data from private datasets.
The `streaming=True` argument used to stream large datasets to avoid saving them in memory and save space.
