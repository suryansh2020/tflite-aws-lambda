# TF-Lite for AWS Lambda

The official wheels of TensorFlow-Lite don't work for AWS Lambda.
They only work for debian-based Linux distributions, like Ubuntu. 

To make it work for AWS Lambda, we need to recompile it for Amazon Linux,
which is a CentOS-based distribution.

In this repo, you'll find compiled binaries as well as the instructions 
for compiling it yourself.


## Cloning TensorFlow Lite

Latest available versions (as of 16 Nov 2021): 

* `v2.7.0`
* `v2.6.2`
* `v2.5.2`
* `v2.4.4`

Check for the up-to-date list here: https://github.com/tensorflow/tensorflow/releases

Clone the version you need:

```bash
TENSORFLOW_VERSION=v2.4.4
git clone --branch ${TENSORFLOW_VERSION} https://github.com/tensorflow/tensorflow.git
```

## Compiling TF-Lite

### Python 3.7

Installing Python: 

```bash
yum install -y python3.7 python3-devel

export PYTHON=python3.7
$PYTHON -m pip install -U pip
$PYTHON -m pip install numpy wheel pybind11
```

Compiling TF-Lite:

```bash
sh ./tensorflow/tensorflow/lite/tools/pip_package/build_pip_package.sh
```

The wheel will be located here:

```
./tensorflow/lite/tools/pip_package/gen/tflite_pip/python3.7/dist/tflite_runtime-2.4.4-cp37-cp37m-linux_x86_64.whl
```


### Python 3.8

Installing Python: 

```bash
amazon-linux-extras enable python3.8
yum install -y python38 python38-devel

export PYTHON=python3.8
$PYTHON -m pip install -U pip
$PYTHON -m pip install numpy wheel pybind11
```

Compiling TF-Lite:

```bash
sh ./tensorflow/tensorflow/lite/tools/pip_package/build_pip_package.sh
```

The wheel will be located here:

```
./tensorflow/lite/tools/pip_package/gen/tflite_pip/python3.8/dist/tflite_runtime-2.4.4-cp38-cp38-linux_x86_64.whl
```


### Python 3.9

Installing Python: 

```bash
yum install -y wget
wget https://repo.anaconda.com/miniconda/Miniconda3-py39_4.10.3-Linux-x86_64.sh
bash Miniconda3-py39_4.10.3-Linux-x86_64.sh -b

export PATH=/root/miniconda3/bin/:$PATH
export PYTHON=python3.9

$PYTHON -m pip install -U pip
$PYTHON -m pip install numpy wheel pybind11
```

Compiling TF-Lite:

```bash
sh ./tensorflow/tensorflow/lite/tools/pip_package/build_pip_package.sh
```

The wheel will be located here:

```
./tensorflow/lite/tools/pip_package/gen/tflite_pip/python3.9/dist/tflite_runtime-2.4.4-cp39-cp39-linux_x86_64.whl
```


## Doing it in Docker

Compiling it:

```bash
PYTHON_VERSION=3.7
TENSORFLOW_VERSION=v2.4.4

docker build \
    --build-arg PYTHON_VERSION=${PYTHON_VERSION} \
    --build-arg TENSORFLOW_VERSION=${TENSORFLOW_VERSION} \
    -t tf-lite-lambda:${PYTHON_VERSION}-${TENSORFLOW_VERSION} \
    .
```

Extracting the wheel:

```bash
mkdir tflite

docker run --rm \
    -v $(pwd)/tflite:/tflite/results \
    -u $(id -u ${USER}):$(id -g ${USER}) \
    tf-lite-lambda:${PYTHON_VERSION}-${TENSORFLOW_VERSION}
```

Running it with one script:

```bash
PYTHON_VERSION=3.7
TENSORFLOW_VERSION=v2.4.4
./build_wheel.sh ${PYTHON_VERSION} ${TENSORFLOW_VERSION}
```

## Sources

* https://github.com/alexeygrigorev/serverless-deep-learning
* https://github.com/tpaul1611/python_tflite_for_amazonlinux