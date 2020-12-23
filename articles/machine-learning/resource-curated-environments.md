---
title: 特选环境
titleSuffix: Azure Machine Learning
description: 了解 Azure 机器学习特选环境，一组预配置的环境，有助于减少实验和部署准备时间。
services: machine-learning
author: luisquintanilla
ms.author: luquinta
ms.reviewer: luquinta
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
ms.date: 11/16/2020
ms.openlocfilehash: 0e6817e03c5e5363ad925367b0632e26fc2da4a2
ms.sourcegitcommit: e2dc549424fb2c10fcbb92b499b960677d67a8dd
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/17/2020
ms.locfileid: "94701392"
---
# <a name="azure-machine-learning-curated-environments"></a>Azure 机器学习的特选环境

本文列出了 Azure 机器学习中的特选环境，以及其中预装的包和通道。 特选环境由 Azure 机器学习提供，且默认可用于你的工作区。 它们由缓存的 Docker 映像提供支持，降低了运行准备成本，缩短了部署时间。 使用这些环境可以快速完成各种机器学习框架的入门。

> [!NOTE]
> 此列表于 2020 年 9 月进行了更新。 请使用 Python SDK 获取最新列表。 有关详细信息，请参阅[环境](./how-to-use-environments.md#use-a-curated-environment)一文。

## <a name="azureml-automl"></a>AzureML-AutoML

**包通道：**

* anaconda
* conda-forge
* pytorch

**Conda 包：**

* Python
* numpy
* scikit-learn
* pandas
* py-xgboost
* fbprophet
* holidays
* setuptools-git
* psutil

**Pip 包：**

* azureml-core
* azureml-pipeline-core
* azureml-telemetry
* azureml-defaults
* azureml-interpret
* azureml-explain-model
* azureml-automl-core
* azureml-automl-runtime
* azureml-train-automl-client
* azureml-train-automl-runtime
* inference-schema
* py-cpuinfo

## <a name="azureml-automl-dnn"></a>AzureML-AutoML-DNN

**包通道：**

* anaconda
* conda-forge
* pytorch

**Conda 包：**

* Python
* numpy
* scikit-learn
* pandas
* py-xgboost
* fbprophet
* holidays
* setuptools-git
* pytorch
* cudatoolkit
* psutil

**Pip 包：**

* azureml-core
* azureml-pipeline-core
* azureml-telemetry
* azureml-defaults
* azureml-interpret
* azureml-explain-model
* azureml-automl-core
* azureml-automl-runtime
* azureml-train-automl-client
* azureml-train-automl-runtime
* inference-schema
* pytorch-transformers
* spacy
* en_core_web_sm
* py-cpuinfo

## <a name="azureml-automl-dnn-gpu"></a>AzureML-AutoML-DNN-GPU

**包通道：**

* anaconda
* conda-forge
* pytorch

**Conda 包：**

* Python
* numpy
* scikit-learn
* pandas
* fbprophet
* holidays
* setuptools-git
* pytorch
* cudatoolkit
* psutil

**Pip 包：**

* azureml-core
* azureml-pipeline-core
* azureml-telemetry
* azureml-defaults
* azureml-interpret
* azureml-explain-model
* azureml-automl-core
* azureml-automl-runtime
* azureml-train-automl-client
* azureml-train-automl-runtime
* inference-schema
* horovod
* pytorch-transformers
* spacy
* en_core_web_sm
* py-cpuinfo

## <a name="azureml-automl-dnn-vision-gpu"></a>AzureML-AutoML-DNN-Vision-GPU

**Conda 包：**

* Python

**Pip 包：**

* azureml-core
* azureml-dataset-runtime
* azureml-contrib-dataset
* azureml-telemetry
* azureml-automl-core
* azureml-automl-runtime
* azureml-train-automl-client
* azureml-defaults
* azureml-interpret
* azureml-explain-model
* azureml-train-automl-runtime
* azureml-train-automl
* azureml-contrib-automl-dnn-vision

## <a name="azureml-automl-gpu"></a>AzureML-AutoML-GPU

**包通道：**

* anaconda
* conda-forge
* pytorch

**Conda 包：**

* Python
* numpy
* scikit-learn
* pandas
* fbprophet
* holidays
* setuptools-git
* psutil

**Pip 包：**

* azureml-core
* azureml-pipeline-core
* azureml-telemetry
* azureml-defaults
* azureml-interpret
* azureml-explain-model
* azureml-automl-core
* azureml-automl-runtime
* azureml-train-automl-client
* azureml-train-automl-runtime
* inference-schema
* py-cpuinfo

## <a name="azureml-chainer-510-cpu"></a>AzureML-Chainer-5.1.0-CPU

**包通道：**

* conda-forge

**Conda 包：**

* Python

**Pip 包：**

* azureml-core
* azureml-defaults
* azureml-telemetry
* azureml-train-restclients-hyperdrive
* azureml-train-core
* chainer
* mpi4py

## <a name="azureml-chainer-510-gpu"></a>AzureML-Chainer-5.1.0-GPU

**包通道：**

* conda-forge

**Conda 包：**

* Python

**Pip 包：**

* azureml-core
* azureml-defaults
* azureml-telemetry
* azureml-train-restclients-hyperdrive
* azureml-train-core
* chainer
* cupy-cuda90
* mpi4py

## <a name="azureml-dask-cpu"></a>AzureML-Dask-CPU

**包通道：**

* conda-forge
* pytorch
* 默认值

**Conda 包：**

* Python

**Pip 包：**

* adlfs
* azureml-core
* azureml-dataset-runtime
* dask[complete]
* dask-ml[complete]
* 分布式
* fastparquet
* fsspec
* joblib
* jupyterlab
* lz4
* mpi4py
* 笔记本
* pyarrow

## <a name="azureml-dask-gpu"></a>AzureML-Dask-GPU

**包通道：**

* conda-forge

**Conda 包：**

* Python
* matplotlib

**Pip 包：**

* azureml-defaults
* adlfs
* azureml-core
* dask[complete]
* dask-ml[complete]
* 分布式
* fastparquet
* fsspec
* joblib
* jupyterlab
* lz4
* mpi4py
* 笔记本
* pyarrow

## <a name="azureml-hyperdrive-forecastdnn"></a>AzureML-Hyperdrive-ForecastDNN

**Conda 包：**

* Python

**Pip 包：**

* azureml-core
* azureml-pipeline-core
* azureml-telemetry
* azureml-defaults
* azureml-automl-core
* azureml-automl-runtime
* azureml-train-automl-client
* azureml-train-automl-runtime
* azureml-contrib-automl-dnn-forecasting

## <a name="azureml-minimal"></a>AzureML-Minimal

**包通道：**

* conda-forge

**Conda 包：**

* Python

**Pip 包：**

* azureml-core
* azureml-defaults

## <a name="azureml-pyspark-mmlspark-015"></a>AzureML-PySpark-MmlSpark-0.15

**包通道：**

* conda-forge

**Conda 包：**

* Python

**Pip 包：**

* azureml-core
* azureml-defaults
* azureml-telemetry
* azureml-train-restclients-hyperdrive
* azureml-train-core

## <a name="azureml-pytorch-10-cpu"></a>AzureML-PyTorch-1.0-CPU

**包通道：**

* conda-forge

**Conda 包：**

* Python

**Pip 包：**

* azureml-core
* azureml-defaults
* azureml-telemetry
* azureml-train-restclients-hyperdrive
* azureml-train-core
* torch
* torchvision
* mkl
* horovod

## <a name="azureml-pytorch-10-gpu"></a>AzureML-PyTorch-1.0-GPU

**包通道：**

* conda-forge

**Conda 包：**

* Python

**Pip 包：**

* azureml-core
* azureml-defaults
* azureml-telemetry
* azureml-train-restclients-hyperdrive
* azureml-train-core
* torch
* torchvision
* mkl
* horovod

## <a name="azureml-pytorch-11-cpu"></a>AzureML-PyTorch-1.1-CPU

**包通道：**

* conda-forge

**Conda 包：**

* Python

**Pip 包：**

* azureml-core
* azureml-defaults
* azureml-telemetry
* azureml-train-restclients-hyperdrive
* azureml-train-core
* torch
* torchvision
* mkl
* horovod
* tensorboard
* future

## <a name="azureml-pytorch-11-gpu"></a>AzureML-PyTorch-1.1-GPU

**包通道：**

* conda-forge

**Conda 包：**

* Python

**Pip 包：**

* azureml-core
* azureml-defaults
* azureml-telemetry
* azureml-train-restclients-hyperdrive
* azureml-train-core
* torch
* torchvision
* mkl
* horovod
* tensorboard
* future

## <a name="azureml-pytorch-12-cpu"></a>AzureML-PyTorch-1.2-CPU

**包通道：**

* conda-forge

**Conda 包：**

* Python

**Pip 包：**

* azureml-core
* azureml-defaults
* azureml-telemetry
* azureml-train-restclients-hyperdrive
* azureml-train-core
* torch
* torchvision
* mkl
* horovod
* tensorboard
* future

## <a name="azureml-pytorch-12-gpu"></a>AzureML-PyTorch-1.2-GPU

**包通道：**

* conda-forge

**Conda 包：**

* Python

**Pip 包：**

* azureml-core
* azureml-defaults
* azureml-telemetry
* azureml-train-restclients-hyperdrive
* azureml-train-core
* torch
* torchvision
* mkl
* horovod
* tensorboard
* future

## <a name="azureml-pytorch-13-cpu"></a>AzureML-PyTorch-1.3-CPU

**包通道：**

* conda-forge

**Conda 包：**

* Python

**Pip 包：**

* azureml-core
* azureml-defaults
* azureml-telemetry
* azureml-train-restclients-hyperdrive
* azureml-train-core
* torch
* torchvision
* mkl
* horovod
* tensorboard
* future

## <a name="azureml-pytorch-13-gpu"></a>AzureML-PyTorch-1.3-GPU

**包通道：**

* conda-forge

**Conda 包：**

* Python

**Pip 包：**

* azureml-core
* azureml-defaults
* azureml-telemetry
* azureml-train-restclients-hyperdrive
* azureml-train-core
* torch
* torchvision
* mkl
* horovod
* tensorboard
* future

## <a name="azureml-pytorch-14-cpu"></a>AzureML-PyTorch-1.4-CPU

**包通道：**

* conda-forge

**Conda 包：**

* Python

**Pip 包：**

* azureml-core
* azureml-defaults
* azureml-telemetry
* azureml-train-restclients-hyperdrive
* azureml-train-core
* torch
* torchvision
* mkl
* horovod
* tensorboard
* future

## <a name="azureml-pytorch-14-gpu"></a>AzureML-PyTorch-1.4-GPU

**包通道：**

* conda-forge

**Conda 包：**

* Python

**Pip 包：**

* azureml-core
* azureml-defaults
* azureml-telemetry
* azureml-train-restclients-hyperdrive
* azureml-train-core
* torch
* torchvision
* mkl
* horovod
* tensorboard
* future

## <a name="azureml-pytorch-15-cpu"></a>AzureML-PyTorch-1.5-CPU

**包通道：**

* conda-forge

**Conda 包：**

* Python

**Pip 包：**

* azureml-core
* azureml-defaults
* azureml-telemetry
* azureml-train-restclients-hyperdrive
* azureml-train-core
* torch
* torchvision
* mkl
* horovod
* tensorboard
* future

## <a name="azureml-pytorch-15-gpu"></a>AzureML-PyTorch-1.5-GPU

**包通道：**

* conda-forge

**Conda 包：**

* Python

**Pip 包：**

* azureml-core
* azureml-defaults
* azureml-telemetry
* azureml-train-restclients-hyperdrive
* azureml-train-core
* torch
* torchvision
* mkl
* horovod
* tensorboard
* future

## <a name="azureml-pytorch-16-cpu"></a>AzureML-PyTorch-1.6-CPU

**包通道：**

* conda-forge

**Conda 包：**

* Python

**Pip 包：**

* azureml-core
* azureml-defaults
* azureml-telemetry
* azureml-train-restclients-hyperdrive
* azureml-train-core
* torch
* torchvision
* mkl
* horovod
* tensorboard
* future

## <a name="azureml-pytorch-16-gpu"></a>AzureML-PyTorch-1.6-GPU

**包通道：**

* conda-forge

**Conda 包：**

* Python

**Pip 包：**

* azureml-core
* azureml-defaults
* azureml-telemetry
* azureml-train-restclients-hyperdrive
* azureml-train-core
* torch
* torchvision
* mkl
* horovod
* tensorboard
* future

## <a name="azureml-scikit-learn-0203"></a>AzureML-Scikit-learn-0.20.3

**包通道：**

* conda-forge

**Conda 包：**

* Python

**Pip 包：**

* azureml-core
* azureml-defaults
* azureml-telemetry
* azureml-train-restclients-hyperdrive
* azureml-train-core
* scikit-learn
* scipy
* joblib

## <a name="azureml-tensorflow-110-cpu"></a>AzureML-TensorFlow-1.10-CPU

**包通道：**

* conda-forge

**Conda 包：**

* Python

**Pip 包：**

* azureml-core
* azureml-defaults
* azureml-telemetry
* azureml-train-restclients-hyperdrive
* azureml-train-core
* tensorflow
* horovod

## <a name="azureml-tensorflow-110-gpu"></a>AzureML-TensorFlow-1.10-GPU

**包通道：**

* conda-forge

**Conda 包：**

* Python

**Pip 包：**

* azureml-core
* azureml-defaults
* azureml-telemetry
* azureml-train-restclients-hyperdrive
* azureml-train-core
* tensorflow-gpu
* horovod

## <a name="azureml-tensorflow-112-cpu"></a>AzureML-TensorFlow-1.12-CPU

**包通道：**

* conda-forge

**Conda 包：**

* Python

**Pip 包：**

* azureml-core
* azureml-defaults
* azureml-telemetry
* azureml-train-restclients-hyperdrive
* azureml-train-core
* tensorflow
* horovod

## <a name="azureml-tensorflow-112-gpu"></a>AzureML-TensorFlow-1.12-GPU

**包通道：**

* conda-forge

**Conda 包：**

* Python

**Pip 包：**

* azureml-core
* azureml-defaults
* azureml-telemetry
* azureml-train-restclients-hyperdrive
* azureml-train-core
* tensorflow-gpu
* horovod

## <a name="azureml-tensorflow-113-cpu"></a>AzureML-TensorFlow-1.13-CPU

**包通道：**

* conda-forge

**Conda 包：**

* Python

**Pip 包：**

* azureml-core
* azureml-defaults
* azureml-telemetry
* azureml-train-restclients-hyperdrive
* azureml-train-core
* tensorflow
* horovod

## <a name="azureml-tensorflow-113-gpu"></a>AzureML-TensorFlow-1.13-GPU

**包通道：**

* conda-forge

**Conda 包：**

* Python

**Pip 包：**

* azureml-core
* azureml-defaults
* azureml-telemetry
* azureml-train-restclients-hyperdrive
* azureml-train-core
* tensorflow-gpu
* horovod

## <a name="azureml-tensorflow-20-cpu"></a>AzureML-TensorFlow-2.0-CPU

**包通道：**

* conda-forge

**Conda 包：**

* Python

**Pip 包：**

* azureml-core
* azureml-defaults
* azureml-telemetry
* azureml-train-restclients-hyperdrive
* azureml-train-core
* tensorflow
* horovod

## <a name="azureml-tensorflow-20-gpu"></a>AzureML-TensorFlow-2.0-GPU

**包通道：**

* conda-forge

**Conda 包：**

* Python

**Pip 包：**

* azureml-core
* azureml-defaults
* azureml-telemetry
* azureml-train-restclients-hyperdrive
* azureml-train-core
* tensorflow-gpu
* horovod

## <a name="azureml-tensorflow-21-cpu"></a>AzureML-TensorFlow-2.1-CPU

**包通道：**

* conda-forge

**Conda 包：**

* Python

**Pip 包：**

* azureml-core
* azureml-defaults
* azureml-telemetry
* azureml-train-restclients-hyperdrive
* azureml-train-core
* tensorflow
* horovod

## <a name="azureml-tensorflow-21-gpu"></a>AzureML-TensorFlow-2.1-GPU

**包通道：**

* conda-forge

**Conda 包：**

* Python

**Pip 包：**

* azureml-core
* azureml-defaults
* azureml-telemetry
* azureml-train-restclients-hyperdrive
* azureml-train-core
* tensorflow-gpu
* horovod

## <a name="azureml-tensorflow-22-cpu"></a>AzureML-TensorFlow-2.2-CPU

**包通道：**

* conda-forge

**Conda 包：**

* Python

**Pip 包：**

* azureml-core
* azureml-defaults
* azureml-telemetry
* azureml-train-restclients-hyperdrive
* azureml-train-core
* tensorflow
* horovod

## <a name="azureml-tensorflow-22-gpu"></a>AzureML-TensorFlow-2.2-GPU

**包通道：**

* conda-forge

**Conda 包：**

* Python

**Pip 包：**

* azureml-core
* azureml-defaults
* azureml-telemetry
* azureml-train-restclients-hyperdrive
* azureml-train-core
* tensorflow-gpu
* horovod

## <a name="azureml-tutorial"></a>AzureML-Tutorial

**包通道：**

* anaconda
* conda-forge

**Conda 包：**

* Python
* pandas
* numpy
* tqdm
* scikit-learn
* matplotlib

**Pip 包：**

* azureml-core
* azureml-defaults
* azureml-telemetry
* azureml-train-restclients-hyperdrive
* azureml-train-core
* azureml-widgets
* azureml-pipeline-core
* azureml-pipeline-steps
* azureml-opendatasets
* azureml-automl-core
* azureml-automl-runtime
* azureml-train-automl-client
* azureml-train-automl-runtime
* azureml-train-automl
* azureml-train
* azureml-sdk
* azureml-interpret
* azureml-tensorboard
* azureml-mlflow
* mlflow
* sklearn-pandas

## <a name="azureml-vowpalwabbit-880"></a>AzureML-VowpalWabbit-8.8.0

**包通道：**

* conda-forge

**Conda 包：**

* Python

**Pip 包：**

* azureml-core
* azureml-defaults
* azureml-dataset-runtime[fuse,pandas]
