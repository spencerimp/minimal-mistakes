---
layout: single
title: Anaconda notes
description: ""
category: 
tags: [conda, opencv]
permalink: /2017/07/14/anaconda-notes/
---
Well, Anaconda is generally nice to reproduce the environment with Python. However, there are some pitfalls. In this I will write down the issue and its solution (I hope I do not have to update this note frequently).


# Cannot see environment in Jupyter
```
conda install jupyter
conda install nb_conda # you need this package to see the environments in jupyter
```
Note that you need to deactivate and activate the environment to let nb_conda take effect.

# Package in environment.yml cannot found

This issue usually arises when you restore the environment by 

```
conda-env create -f environment.yml
```
Which loads exactly the same conda packages from the machine you exported the environment. And some conda packages only exist in some platforms, hence the problem.

I would suggest: create a new environment and specify the python version only, then type in the packages manually (I know this sounds tedious, not better than check the availability of packages on various platforms)


# OpenCV issues

**Case 1**

Installed the OpenCV but still cannot import it. Error mesage may look like 


```
ImportError: libopencv_reg.so.3.1: cannot enable executable stack as shared object requires: Invalid argument
```

This is usally happened importing opencv in Python in Bash for Windows environment.

The idea is to use execstack with you installed opencv libraries. The tricky thing to the locate those libraries. 

Remember to change [YOUR_ENVIRONMENT] to the activated environment.
   
```
sudo apt-get install execstack
sudo /usr/sbin/execstack -c ~/anaconda3/env/[YOUR_ENVIRONMENT]/lib/libopencv_*
```

Reference

[http://answers.opencv.org/question/103825/py-opencv-import-error-in-bash-for-windows-10/](http://answers.opencv.org/question/103825/py-opencv-import-error-in-bash-for-windows-10/)