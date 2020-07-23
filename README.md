# Yolov3 Yolov4 TensorRT Implementation

![GitHub stars](https://img.shields.io/github/stars/enazoe/yolo-tensorrt) ![GitHub forks](https://img.shields.io/github/forks/enazoe/yolo-tensorrt)  ![GitHub watchers](https://img.shields.io/github/watchers/enazoe/yolo-tensorrt) 

## INTRODUCTION

The project is the encapsulation  of nvidia official yolo-tensorrt [implementation](https://github.com/NVIDIA-AI-IOT/deepstream_reference_apps). And you must have the trained yolo model(__.weights__) and __.cfg__ file from the darknet.

- [x] yolov3 , yolov3-tiny
- [x] yolov4 , yolov4-tiny
- [ ] yolov5

## PLATFORM & PERFORMENCE

|     model      |        gpu        | precision  | memory used | detect time |
| :------------: | :---------------: | :--------: | :---------: | :---------: |
| yolov3-416x416 | jetson nano (15w) | HALF(FP16) |             |    250ms    |

## WRAPPER

Prepare the pretrained __.weights__ and __.cfg__ model. 

```c++
Detector detector;
Config config;
cv::Mat mat_image = cv::imread("dog.jpg");

std::vector<Result> res;
detector.detect(mat_image, res)
```

## How to use yolo-trt as DLL or SO libraries


### windows10

- dependency : TensorRT 7.1.3.4  , cuda 11.0 , cudnn 8.0  , opencv3.3 , gflags , vs2015
- build:
  
    open MSVC _sln/sln.sln_ file 
    - dll project : the trt yolo detector dll
    - demo project : test of the dll

### ubuntu


```
sudo apt-get install libgflags-dev
cd yolo-tensorrt/
mkdir build
cd build/
cmake ..
make
```
The project generate the __libdetector.so__ lib, and the sample code.
_If you want to use the generated libdetector.so lib in your own project,the cmake file perhaps could help you in scripts dir._

- jetson nano 
	
	dependency : gflags , JetPack 4.2.2

	__note:__ when the platform is jetson nano the gencode arch must be set _compute_53,code=sm_53_ at cmake file.

- jetson xavier nx
	
	dependency : gflags , JetPack 4.4

	__note:__ when the platform is jetson-xavier-nx the gencode arch must be set _compute_72,code=sm_72_ at cmake file.

## API

```
struct Config
{
	std::string file_model_cfg = "configs/yolov3.cfg";

	std::string file_model_weights = "configs/yolov3.weights";

	float detect_thresh = 0.9;

	ModelType net_type = YOLOV3;

	Precision inference_precison = INT8;
	
	int gpu_id = 0;

	std::string calibration_image_list_file_txt = "configs/calibration_images.txt";
};

class API Detector
{
public:
	explicit Detector();
	~Detector();

	void init(const Config &config);

	void detect(const cv::Mat &mat_image, std::vector<Result> &vec_result);

private:
	Detector(const Detector &);
	const Detector &operator =(const Detector &);
	class Impl;
	Impl *_impl;
};
```

## REFERENCE

- https://github.com/wang-xinyu/tensorrtx/tree/master/yolov4
- https://github.com/mj8ac/trt-yolo-app_win64
- https://github.com/NVIDIA-AI-IOT/deepstream_reference_apps