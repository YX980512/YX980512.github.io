# Fastai for Various Applications

Fastai simplifies machine learning applications by unifying the workflow across different domains such as computer vision, natural language processing, tabular data analysis, and recommendation systems. Here is a record of my learning.

## Common Workflow in Fastai

Regardless of the application, the following steps are generally followed:

1. **Create appropriate DataLoaders** - This prepares your data for training by defining how it's accessed and preprocessed.
2. **Create a Learner** - This combines your data with a model, specifying how training should be conducted.
3. **Call a fit method** - This starts the training process.
4. **Make predictions or view results** - Utilize the trained model to make predictions or evaluate the results.

## Applications of Fastai

### Computer Vision Classification

- **Dataset**: Oxford-IIIT Pet Dataset (7,349 images of cats and dogs).
- **Pretrained Model**: Uses a model pretrained on 1.3 million images.
- **Task**: Fine-tune a model for recognizing dogs and cats.

```python
from fastai.vision.all import *
path = untar_data(URLs.PETS)/'images'
dls = ImageDataLoaders.from_name_func(
    path, get_image_files(path), valid_pct=0.2, seed=42,
    label_func=is_cat, item_tfms=Resize(224))
learn = vision_learner(dls, resnet34, metrics=error_rate)
learn.fine_tune(1)
```

### Computer Vision Segmentation

- **Dataset**: Subset of the Camvid dataset.
- **Model**: Training a segmentation model using unet_learner.

```python
from fastai.vision.all import *
path = untar_data(URLs.CAMVID_TINY)
dls = SegmentationDataLoaders.from_label_func(
    path, bs=8, fnames=get_image_files(path/"images"),
    label_func=lambda o: path/'labels'/f'{o.stem}_P{o.suffix}',
    codes=np.loadtxt(path/'codes.txt', dtype=str))
learn = unet_learner(dls, resnet50)
learn.fine_tune(12)
```

![](/images/seg1.jpg "Results")

Or we can plot the k instances that contributed the most to the validation loss by using the SegmentationInterpretation class.

```python
interp = SegmentationInterpretation.from_learner(learn)
interp.plot_top_losses(k=4)
```

![](/images/seg2.jpg "Show")

