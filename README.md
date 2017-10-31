# Pyramid Scene Parsing Network 
> _support CUDA 8.0 and cuDNN v.5_

by Hengshuang Zhao, Jianping Shi, Xiaojuan Qi, Xiaogang Wang, Jiaya Jia, details are in [project page](https://hszhao.github.io/projects/pspnet/index.html).

## Introduction

This repository is for '[Pyramid Scene Parsing Network](https://arxiv.org/abs/1612.01105)', which ranked 1st place in [ImageNet Scene Parsing Challenge 2016](http://image-net.org/challenges/LSVRC/2016/results). The code is modified from [hszhao](https://github.com/hszhao/PSPNet) and [DeepLab v2](https://github.com/xmyqsh/deeplab-v2) which supports `cuDNN v.5`.

> un-updated: `docs/` `examples/`

## Installation

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
	
## Evaluation
The author provides **Matlab** code to evaluate this framework. 
Before using `evaluation`, you should download dataset and caffemodel first.

* **Download dataset**

  In this repository, you can use **ADE20K**, **VOC2012** and **cityscapes** dataset for evaluation. Please check your dataset path, and modify the `data_root` in [eval_all.m](https://github.com/BassyKuo/PSPNET-cudnn5/blob/master/evaluation/eval_all.m#L16)
  
  For example,
  ```shell
  $ vim evaluation/eval_all.m
  16 -- data_root = '/data2/hszhao/dataset/ADEChallengeData2016';
  16 ++ data_root = '/data/ADEChallengeData2016';
  ```
  
   And copy list files from `samplelist` to dataset directory, for example:
   ```shell
   $ cd evaluation
   $ cp samplelist/ADE20K_val.txt /data/ADEChallengeData2016/list/
   ```
   ---
   ### [NOTICE]
   
   Make sure the ground truth exist in your dataset path. 
   For example (fetch from [eval_acc.m](https://github.com/BassyKuo/PSPNET-cudnn5/blob/master/evaluation/eval_acc.m)):
   
   :o: **Correct**
   ```matlab
   >> data_root = '/data/ADEChallengeData2016';
   >> eval_list = 'list/ADE20K_val.txt';
   >> list = importdata(fullfile(data_root,eval_list))
   list =
      2000×1 cell array
       'images/validation/ADE_val_00000001.jpg annotations/validation/ADE_val_00000001.png'
      ...
  
   >> str = strsplit(list{1})
   str =
      1×2 cell array
       'images/validation/ADE_val_00000001.jpg'    'annotations/validation/ADE_val_00000001.png'
   
   >> fileAnno = fullfile(pathAnno, str{2})
   fileAnno =
       '/data/ADEChallengeData2016/annotations/validation/ADE_val_00000001.png'
       
   % '/data/ADEChallengeData2016/annotations/validation/ADE_val_00000001.png' is the 
   % segmentation ground truth image for '/data/ADEChallengeData2016/images/validation/ADE_val_00000001.jpg'
   ```

   :x: **Wrong**
   ```matlab
   >> data_root = '/data/VOC2012';                
   >> eval_list = 'list/VOC2012_test.txt';
   >> list = importdata(fullfile(data_root,eval_list))
   list =
      1456×1 cell array
       '/JPEGImages/2008_000006.jpg'
       '/JPEGImages/2008_000011.jpg'
      ...
   >> str = strsplit(list{1})
   str =
      cell
       '/JPEGImages/2008_000006.jpg'
   >> fileAnno = fullfile(pathAnno, str{2})
   Index exceeds matrix dimensions.

   % Fail to find `str{2}` because `str` only has 1 cell.
   % In this case, you should not use 'list/VOC2012_test.txt' but 'list/VOC2012_train.txt' or 'list/VOC2012_val.txt'
   % generated yourself.
   ```

* **Download caffemodels**

   If you have [gdrive](https://github.com/prasmussen/gdrive), you can download caffemodels in the `evaluation/model/` folder as below:
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


## Other options for installation

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

2. If you use `octave` rather than `matlab`, like me, build the repository by `cmake` wih argument `-DBUILD_matlab=ON`, and use `make octave`.
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
   I have not fixed the error yet. If you do, you can make a PR to help this part to be complete.

## Errors & Solutions

Here are some method to solve the problems occurred during building.

1. Errors raised during the `make all install` step, check [here](https://gist.github.com/wangruohui/679b05fcd1466bb0937f#hack-cuda-to-support-gcc-5) to find solutions. 

2. Errors raised when building **matcaffe** with Matlab, please check:
   * [GCC/G++ version problem](https://github.com/BassyKuo/PSPNET-cudnn5/issues/5) 
   
3. protobuf error:
   * If the gcc/g++ version later than 5, you should upgrade `libprotobuf`. Please check [here](https://github.com/google/protobuf/tree/master/src) see how to reinstall `protobuf` with the new complier.
   
4. 'MAT' error:
   * If the error message shown as `undefined reference to Mat_XXXX`, for example:
   ```sh
   make[1]: *** [examples/CMakeFiles/convert_mnist_siamese_data.dir/all] Error 2
   make[1]: *** Waiting for unfinished jobs....
   ../lib/libcaffe.so.1.0.0-rc3: undefined reference to `Mat_VarCreate'
   ../lib/libcaffe.so.1.0.0-rc3: undefined reference to `Mat_CreateVer'
   ../lib/libcaffe.so.1.0.0-rc3: undefined reference to `Mat_VarWrite'
   ../lib/libcaffe.so.1.0.0-rc3: undefined reference to `Mat_VarFree'
   ../lib/libcaffe.so.1.0.0-rc3: undefined reference to `Mat_VarReadInfo'
   ../lib/libcaffe.so.1.0.0-rc3: undefined reference to `Mat_Close'
   ../lib/libcaffe.so.1.0.0-rc3: undefined reference to `Mat_VarReadDataLinear'
   ../lib/libcaffe.so.1.0.0-rc3: undefined reference to `Mat_Open'

   ```
     you can check [here](https://github.com/TheLegendAli/DeepLab-Context2/issues/1#issuecomment-264710631) to solve the problem.

If you need any further of my help, you're always welcome to open an issue.

Thank you :)

---

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
