# PDlib - A PHP extension for [Dlib](https://github.com/davisking/dlib)

Dlib is a modern C++ toolkit containing machine learning algorithms and tools for creating complex software in C++ to solve real world problems. See http://dlib.net for the main project documentation and API reference.

## Note
This is a fork of the [original pdlib extension](https://github.com/goodspb/pdlib), which we will use temporarily for the Nextcloud [FaceRecognition](https://github.com/matiasdelellis/facerecognition) application while the original author is not responding.

## Installation

Read [Installation](https://github.com/matiasdelellis/pdlib/wiki/Installation) documentation on the wiki for details.

## Usage

### General Usage

Good starting point can be `tests/integration_face_recognition.phpt`. Check that first.

Basically, if you just quickly want to get from your image to 128D descriptor of faces in image,
here is really minimal example how:

```php
<?php
$img_path = "image.jpg";
$fd = new CnnFaceDetection("detection_cnn_model.dat");
$detected_faces = $fd->detect($img_path);
foreach($detected_faces as $detected_face) {
  $fld = new FaceLandmarkDetection("landmark_model.dat");
  $landmarks = $fld->detect($img_path, $detected_face);
  $fr = new FaceRecognition("recognition_model.dat");
  $descriptor = $fr->computeDescriptor($img_path, $landmarks);
  // Optionally use descriptor later in `dlib_chinese_whispers` function
}
```

Location from where to get these models can be found on DLib website, as well as in `tests/integration_face_recognition.phpt` test.

### Specific use cases

#### face detection

If you want to use HOG based approach:

```php
<?php

// face detection
detected_faces = dlib_face_detection("image.jpg");
// $detected_faces is indexed array, where values are assoc arrays with "top", "bottom", "left" and "right" values
```

If you want to use CNN approach (and CNN model):

```php
<?php
$fd = new CnnFaceDetection("detection_cnn_model.dat");
$detected_faces = $fd->detect("image.jpg");
// $detected_face is indexed array, where values are assoc arrays with "top", "bottom", "left" and "right" values
```

CNN model can get you slightly better results, but is much, much more demanding (CPU and memory, GPU is also preferred).

#### face landmark detection

```php
<?php

// face landmark detection
$landmarks = dlib_face_landmark_detection("~/a.jpg");
var_dump($landmarks);
```

Additionally, you can also use class-based approach:
```php
$rect = array("left"=>value, "top"=>value, "right"=>value, "bottom"=>value);
// You can download a trained facial shape predictor from:
// http://dlib.net/files/shape_predictor_5_face_landmarks.dat.bz2
$fld = new FaceLandmarkDetection("path/to/shape/predictor/model");
$parts = $fld->detect("path/to/image.jpg", $rect);
// $parts is integer array where keys are associative values with "x" and "y" for keys
```

Note that, if you use class-based approach, you need to feed bounding box rectangle with values obtained from `dlib_face_detection`. If you use `dlib_face_landmark_detection`, everything is already done for you (and you are using HOG face detection model).

#### face recognition (aka getting face descriptor)

```php
<?php

$fr = new FaceRecognition($model_path);
$landmarks = array(
    "rect" => $rect_of_faces_obtained_with_CnnFaceDetection,
    "parts" => $parts_obtained_with_FaceLandmarkDetection);
$descriptor = $fr->computeDescriptor($img_path, $landmarks);
// $descriptor is 128D array
```

#### chinese whispers

Provides raw access to dlib's `chinese_whispers` function.
Client need to build and provide edges. Edges are provided
as numeric array. Each element of this array should also be
numeric array with 2 elements of long type.

Returned value is also numeric array, containing obtained labels.

```php
<?php
// This example will cluster nodes 0 and 1, but would leave 2 out.
// $labels will look like [0,0,1].
$edges = [[0,0], [0,1], [1,1], [2,2]];
$labels = dlib_chinese_whispers($edges);
```

## Features
- [x] 1.Face Detection
- [x] 2.Face Landmark Detection
- [x] 3.Deep Face Recognition
- [x] 4.Deep Learning Face Detection
- [x] 5. Raw chinese_whispers

