# Tensorflow-cl

Run Tensorflow on OpenCL™ devices.  UNDER CONSTRUCTION!!!

## Summary

This repo was created from the original Tensorflow repository at:

- https://github.com/tensorflow/tensorflow

Please see the main repository for full Tensorflow documentation.  This readme will only focus on the OpenCL porting aspects of Tensorflow.

## What works

### What's working
- per-element binary operators: `add`, `sub`, `mul`, `div`, `pow`, `minimum`, `maximum`, `squared_difference`, as per [test_tf3.py](tensorflow/stream_executor/cl/test/test_tf3.py)
- per-element unary operators: `tanh`, `abs`, `acos`, `asin`, `atan`, `ceil`, `cos`, `exp`, `floor`, `inverse`, `isfinite`, `isinf`, `isnan`, `log`, `neg`, `sign`, `sin`, `sqrt`, square`, `tan` (test: [test_tf4.py](tensorflow/stream_executor/cl/test/test_tf4.py))
- Variables can be placed on GPU
- `matmul` (using [CLBlast](https://github.com/CNugteren/CLBlast))

### To do

- gradients
- convolutions
- reductions

## Installation 

- For now, Ubuntu 16.04 is supported.  In the future, I plan to support Mac OS X too
- You need:
  - the tensorflow non-gpu installation pre-requisites,
   - an OpenCL 1.2-enabled GPU, and  OpenCL 1.2-enabled drivers
   - python 3
- Simply download https://github.com/hughperkins/tensorflow-cl/releases/download/v0.6.0/tensorflow-0.11.0rc0-py3-none-any.whl , and
- Install using pip:
```
pip install --upgrade tensorflow-0.11.0rc0-py3-none-any.whl
```

If you want, you can [build from source](doc/build-form-source.md)

## Run

- test per-element binary operations [tensorflow/stream_executor/cl/test/test_tf3.py](tensorflow/stream_executor/cl/test/test_tf3.py):
```
cd
source ~/env3/bin/activate
python ~/git/tensorflow-cl/tensorflow/stream_executor/cl/test/test_tf3.py
```
- test per-element unary operations [tensorflow/stream_executor/cl/test/test_tf4.py](tensorflow/stream_executor/cl/test/test_tf4.py):
```
cd
source ~/env3/bin/activate
python ~/git/tensorflow-cl/tensorflow/stream_executor/cl/test/test_tf4.py
```
- test blas [test_blas.py](tensorflow/stream_executor/cl/test/test_blas.py) :
```
cd
source ~/env3/bin/activate
python ~/git/tensorflow-cl/tensorflow/stream_executor/cl/test/test_blas.py
```

## Design/architecture

- tensorflow code stays 100% [NVIDIA® CUDA™](https://www.nvidia.com/object/cuda_home_new.html)
- [cuda-on-cl](https://github.com/hughperkins/cuda-on-cl) compiles the CUDA code into OpenCL
- Cedric Nugteren's [CLBlast](https://github.com/CNugteren/CLBlast) provides BLAS (matrix multiplications)

## Related projects

### DNN Libraries
- [OpenCL Torch](https://github.com/hughperkins/distro-cl)
- [DeepCL](https://github.com/hughperkins/DeepCL)

### OpenCL middleware
- [CLBlast](https://github.com/CNugteren/CLBlast) BLAS for OpenCL
- [cuda-on-cl](https://github.com/hughperkins/cuda-on-cl)  Compile CUDA apps for OpenCL
- [EasyCL](https://github.com/hughperkins/EasyCL)   Handles running kernels, passing in arguments etc, on OpenCL

## News

- Oct 25:
  - fixed BLAS wrapper, working now, on GPU, test script: [test_blas.py](tensorflow/stream_executor/cl/test/test_blas.py)
- Oct 24:
  - hmmm, just discovered some new options, to ensure operations really are on the gpu, and ... many are not :-P, so back to the drawing board a bit
    - the good news is that component-wise add really is on the gpu
    - the bad news is that everything else is not :-P
  - (re-)added following per-element binary operators: `sub`, `mul`, `div`, `pow`, `minimum`, `maximum`, `squared_difference`.  This time, they actually are really running on the gpu :-)  (test: [test_tf3.py](tensorflow/stream_executor/cl/test/test_tf3.py))
  - (re-)added following per-element unary operators:, which really are running on gpu now :-), [test_tf4.py](tensorflow/stream_executor/cl/test/test_tf4.py): `tanh`, `abs`, `acos`, `asin`, `atan`, `ceil`, `cos`, `exp`, `floor`, `inverse`, `isfinite`, `isinf`, `isnan`, `log`, `neg`, `sign`, `sin`, `sqrt`, square`, `tan`
  - Variables can be placed on gpu now, [test_gradients.py](tensorflow/stream_executor/cl/test/test_gradients.py)
- Oct 23:
  - can use component wise addition from Python now :-)
  - fixed critical bug involving `float4`s, that meant that tensors larger than, say, 3 :-P, could not be added correctly
  - ~~added following per-element binary operators: `sub`, `mul`, `div`, `not_equal`, `minimum`, `maximum`, `pow`, `squared_difference` (test: [test_tf3.py](tensorflow/stream_executor/cl/test/test_tf3.py))~~
  - ~~added following per-element unary operator: `tanh`, `abs`, `acos`, `asin`, `atan`, `ceil`, `cos`, `exp`, `floor`, `inverse`, `isfinite`, `isinf`, `isnan`, `log`, `neg`, `sigmoid`, `sign`, `sin`, `sqrt`, square`, `tan` (test: [test_tf4.py](tensorflow/stream_executor/cl/test/test_tf4.py))~~
  - ~~added following comparison operators: `equal_to`, `greater`, `greater_equal`, `less`, `less_equal`~~
  - ~~added in BLAS (using Cedric Nugteren's [CLBlast](https://github.com/CNugteren/CLBlast) ).  Not very tested yet.  Test script [test_blas.py](tensorflow/stream_executor/cl/test/test_blas.py)~~
- Oct 22:
  - componentwise addition working, when called from c++
  - commit `0db9cc2e`: re-enabled `-fPIC`, `-pie`
    - this is a pre-requisite for being able to run from python at some point
    - but if you built prior to this, you need to deeeeep clean, and rebuild from scratch:
    ```
    rm -Rf third_party/cuda-on-cl/build
    bazel clean --expunge
    ```
  - python working (as of commit 5e67304c3c)
    - you'll need to do `bazel clean`, and rebuild from scratch, if you already did a build prior to this commit
- Oct 20:
  - removed requirement for CUDA Toolkit
  - updated build slightly: added https://github.com/hughperkins/cuda-on-cl as a submodule
- Oct 18:
  - stream executor up
  - crosstool working
