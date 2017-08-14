---
layout: single
title: TensorFlow Object Detection
description: ""
category:
tags: [tensorflow]
permalink: /2017/07/30/tensorflow-object-detection/
---

# Introduction



## Annotation

To draw bounding boxes using a GUI, I recommend sloth.

To install the GUI, you need

- Python 3.5
- PyQt4

Use Conda to get those dependencies work togeter

```
conda create -n slot_env python=3.5
source activate slot_env
pip install git+https://github.com/cvhciKIT/sloth.git
conda install -c anaconda pyqt=4.11.4
```

If sloth is installed, you can type the executable `sloth` to lanch the GUI.

## Configure Sloth for custom labels

We need to configure Sloth so that we can create our label set.

We can create a configuration file as a Python script. A configuration is a list of dictionaris.

For example, we want to draw rectagle bounding box for two classes `cat` and `dog`
```
LABELS = (
    {"attributes": {"type":  "rect",
                    "class": "cat",
                    },
     "item":     "sloth.items.RectItem",
     "inserter": "sloth.items.RectItemInserter",
     "text":     "cat"
    },
    {"attributes": {"type":  "rect",
                    "class": "dog",
                    },
     "item":     "sloth.items.RectItem",
     "inserter": "sloth.items.RectItemInserter",
     "text":     "dog"
    },
)
```

Save the config as `my_config.py`, then launch Sloth GUI with custom labels

```
sloth --config my_config.py
```

The annotation will be saved as json. Note that each json file consists of one or more images.

For example, if you annotate two images, the first images is annotated with a dog and a cat, and the second with just a cat, the output json can be like

```
[
    {
        "class": "image",
        "filename": "image1.jpg",
        "annotations": [
            {
                "class": "cat",
                "height": 60.0,
                "width": 46.0,
                "y": 105.0,
                "x": 346.0
            },
            {
                "class": "dog",
                "height": 58.0,
                "width": 56.0,
                "y": 119.0,
                "x": 636.0
            }
        ]
    },
    {
        "class": "image",
        "filename": "image2.jpg",
        "annotations": [
            {
                "class": "cat",
                "height": 60.0,
                "width": 46.0,
                "y": 105.0,
                "x": 346.0
            },
        ]
    },
]
```

# More to come
