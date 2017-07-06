## Pyramid Scene Parsing Network `(support CUDA 8.0 and cuDNN v.5)`

by Hengshuang Zhao, Jianping Shi, Xiaojuan Qi, Xiaogang Wang, Jiaya Jia, details are in [project page](https://hszhao.github.io/projects/pspnet/index.html).

### Introduction

This repository is for '[Pyramid Scene Parsing Network](https://arxiv.org/abs/1612.01105)', which ranked 1st place in [ImageNet Scene Parsing Challenge 2016](http://image-net.org/challenges/LSVRC/2016/results). The code is modified from Caffe version of [hszhao](https://github.com/hszhao/PSPNet) and [DeepLab v2](https://github.com/xmyqsh/deeplab-v2) which supports `cuDNN v.5`.

> un-updated: `docs/` `examples/` 

### Installation

For installation, please follow the instructions of [Caffe](https://github.com/BVLC/caffe) and [DeepLab v2](https://bitbucket.org/aquariusjay/deeplab-public-ver2). To enable cuDNN for GPU acceleration, cuDNN v.5 is needed. If you meet error related with 'matio', please download and install [matio](https://sourceforge.net/projects/matio/files/matio/1.5.2) as required in 'DeepLab v2'.

The code has been installed and runtested successfully on Ubuntu 16.04 with CUDA 8.0 / cudnn v.5.

Here is the installation step-by-step:

1. Clone the repository:

	```shell
	git clone https://github.com/BassyKuo/PSPNET-cudnn5.git
	```

2. Build Caffe:

   You can build the repository by `cmake` or `makefile`.

   * Using Cmake:

	```shell
	cd $PSPNET_DIR
	mkdir build; cd build
	cmake .. -DBLAS=Open
	make all install -j32
	```

	* Using Makefile:

	```shell
	cd $PSPNET_DIR
	cp Makefile.config.example Makefile.config
	vim Makefile.config 
	# uncomment configures which you need, then save it.

	make all -j32
	```

3. Check if the installation is successful or not:

	```shell
	# For python interface, install pycaffe and add the PSPNET path to `PYTHONPATH`
	make pycaffe
	export PYTHONPATH=${PSPNET_DIR}/python:${PYTHONPATH}

	# Test the package installed or not
	$ python -c "import caffe; print caffe.__version__"
	1.0.0-rc3
	```

4. Build Matlab-caffe / Octave-caffe:

	```shell
	vim Makefile.config
	# uncomment `MATLAB_DIR := /usr/local`
	
	make matcaffe
	```

	if you use `octave` instead of `matlab`, only need to build with CMake since the Makefile build only supports MATLAB:
	(see https://github.com/BVLC/caffe/issues/4401)
	```shell
	mkdir build; cd build
	cmake .. -DBLAS:STRING=Open -DBUILD_matlab=ON
	make all install -j32
	make octave
	```


### NOTICE

1. If you do not have GPU, you can build the repository with `CPU_ONLY`. For example by using `cmake`,
   ```shell
   cd $PSPNET_DIR
   mkdir build; cd build
   cmake .. -DCPU_ONLY=ON
   make all install -j32
   ```

   Or using `Makefile`: uncomment the line `CPU_ONLY` and comment `USE_CUDNN`, then make it.

   However, it causes an error: **cannot not find <cublas_v2.h>** because `interp.hpp` dependent on cublas_v2.h file.
   You should add the cuda library path in your makefile, or modify the file as below:
   ```cpp
   // in include/caffe/util/interp.hpp
   -- #include <cublas_v2.h> 
   ++ #include </usr/local/cuda/include/cublas_v2.h> 
   ```

2. Using `evaluation`, you should download dataset and caffemodel first.
   If you have [gdrive](https://github.com/prasmussen/gdrive), you can download caffemodels in the `model` folder as below:
   ```shell
   $ cd evaluation/model
   $ gdrive download 0BzaU285cX7TCT1M3TmNfNjlUeEU
   $ gdrive download 0BzaU285cX7TCNVhETE5vVUdMYk0
   $ gdrive download 0BzaU285cX7TCN1R3QnUwQ0hoMTA
   ```
   or download them by WEB console:
   * [pspnet50_ADE20K.caffemodel](https://drive.google.com/open?id=0BzaU285cX7TCN1R3QnUwQ0hoMTA)
   * [pspnet101_VOC2012.caffemodel](https://drive.google.com/open?id=0BzaU285cX7TCNVhETE5vVUdMYk0)
   * [pspnet101_cityscapes.caffemodel](https://drive.google.com/open?id=0BzaU285cX7TCT1M3TmNfNjlUeEU)


   And copy list files from `samplelist` to dataset directory, for example:
   ```shell
   $ cd evaluation
   $ cp samplelist/ADE20K_val.txt /data/ADEChallengeData2016/list/
   ```

3. If you use `octave` rather than `matlab`, like me, build the repository by `cmake` wih argument `-DBUILD_matlab=ON`, and use `make octave`.
   You will get `caffe_.mex` in your `matlab/+caffe/private/`. 
   (more information about mex-file you can see [here](https://www.gnu.org/software/octave/doc/interpreter/Getting-Started-with-Mex_002dFiles.html))

   Move to evaluation folder, and run `run_octave.sh`.
   It seems that there are some errors called from [empty](https://www.mathworks.com/help/matlab/ref/empty.html) in octave, like this:
   ```shell
   error: no such method or property `empty'
   error: called from
		Net at line 43 column 22
		get_net at line 28 column 5
		Net at line 31 column 14
		eval_sub at line 26 column 5
		eval_all at line 73 column 3
   ```
   I have no idea how to fix it. If you solve this problem, please contact me. Thanks.

### Errors

If you got troublue when building caffe, check [here](https://gist.github.com/wangruohui/679b05fcd1466bb0937f#fix-hdf5-naming-problem) to find solutions.



## Citation

If PSPNet is useful for your research, please consider citing:

    @inproceedings{zhao2017pspnet,
      author = {Hengshuang Zhao and
                Jianping Shi and
                Xiaojuan Qi and
                Xiaogang Wang and
                Jiaya Jia},
      title = {Pyramid Scene Parsing Network},
      booktitle = {Proceedings of IEEE Conference on Computer Vision and Pattern Recognition (CVPR)},
      year = {2017}
    }
