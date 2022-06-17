# Face Recognition 人臉識別

> 此報告為參考並學習[face_recognition](https://github.com/ageitgey/face_recognition) 後，做成的報告。
>
> [face_recognition](https://github.com/ageitgey/face_recognition) 是一個人臉識別的開源項目，並完整配備開發文檔及應用案例，也兼容樹莓派。



在Face Recognition(人臉辨識)的問題上，通常會再進一步分成兩個種類 :
- **Face Verification (人臉驗證) :** 
  - 給予輸入image, name/ID
  - 輸出是否為此人
  - 視為 1:1 matching problem
  
  e.g. 手機的人臉解鎖

- **Face Recognition (人臉辨識) :** 
  - 擁有K個人物的Database
  - 給予Input image
  - 輸出ID, if (image為K個人物中的其中一個) </br>
  無法辨識此人, if (image不為K個人物中任何一個)
  - 視為 1:K matching problem
  
  e.g. 使用的人臉辨識的員工通行閘門

此項目提供了`face_recognition` 命令行工具，我們可以透過他處理整個資料夾的圖片。

[![PyPI](https://img.shields.io/pypi/v/face_recognition.svg)](https://pypi.python.org/pypi/face_recognition)
[![Build Status](https://travis-ci.org/ageitgey/face_recognition.svg?branch=master)](https://travis-ci.org/ageitgey/face_recognition)
[![Documentation Status](https://readthedocs.org/projects/face-recognition/badge/?version=latest)](http://face-recognition.readthedocs.io/en/latest/?badge=latest)

## 特點

#### 從照片中尋找人臉

定位照片中所有人臉：

![](https://cloud.githubusercontent.com/assets/896692/23625227/42c65360-025d-11e7-94ea-b12f28cb34b4.png)

```python
import face_recognition
image = face_recognition.load_image_file("your_file.jpg")
face_locations = face_recognition.face_locations(image)
```

#### 識別人物的key point

識別人物的關鍵點，包括眼睛、鼻子、嘴巴和下巴。

![](https://cloud.githubusercontent.com/assets/896692/23625282/7f2d79dc-025d-11e7-8728-d8924596f8fa.png)

```python
import face_recognition
image = face_recognition.load_image_file("your_file.jpg")
face_landmarks_list = face_recognition.face_landmarks(image)
```



#### 識別照片中的人物是誰

![](https://cloud.githubusercontent.com/assets/896692/23625229/45e049b6-025d-11e7-89cc-8a71cf89e713.png)

```python
import face_recognition
known_image = face_recognition.load_image_file("biden.jpg")
unknown_image = face_recognition.load_image_file("unknown.jpg")

biden_encoding = face_recognition.face_encodings(known_image)[0]
unknown_encoding = face_recognition.face_encodings(unknown_image)[0]

results = face_recognition.compare_faces([biden_encoding], unknown_encoding)
```

我們也可以搭配其他的Python庫（如opencv）實現實時人臉檢測：

![](https://cloud.githubusercontent.com/assets/896692/24430398/36f0e3f0-13cb-11e7-8258-4d0c9ce1e419.gif)

 [實時人臉檢測](https://github.com/ageitgey/face_recognition/blob/master/examples/facerec_from_webcam_faster.py) 。

## 如何安裝

### 環境配置

- Python 3.3+ or Python 2.7
- macOS or Linux 
- 因為Windows 並不是他們官方支持的系統，所以我藉助了高中同學的MacBook Pro

#### 在 Mac 或者 Linux上安裝

第一步安裝dlib和相關Python依賴：

- [如何在macOS或者Ubuntu上安装dlib](https://gist.github.com/ageitgey/629d75c1baac34dfa5ca2a1928a7aeaf)

Then, install this module from pypi using `pip3` (or `pip2` for Python 2):

```bash
pip3 install face_recognition
```


## 使用方法

### 命令行介面

當我們安裝好後，可以使用兩種命令行工具：

- `face_recognition` - 在一張照片或一個照片資料夾中辨認出是誰的臉。
- `face_detection` - 在一張照片或一個照片資料夾中定位出是人臉位置。

#### `face_recognition` 命令行工具

`face_recognition`命令行工具可以在一張照片或一個照片資料夾中辨認出是誰的臉。

首先，我們需要有一個以知姓名的圖片資料夾，一人一張圖，圖片的文檔名即為人名：

![known](https://cloud.githubusercontent.com/assets/896692/23582466/8324810e-00df-11e7-82cf-41515eba704d.png)

之後，我們需要第二個圖片資料夾，裡面是我們希望辨別的圖片：

![unknown](https://cloud.githubusercontent.com/assets/896692/23582465/81f422f8-00df-11e7-8b0d-75364f641f58.png)

然後，在命令行中切換到這兩個資料夾的路徑，並使用`face_recognition`命令行，傳給這兩個資料夾，之後就會得出未知圖片中的人名：

```bash
$ face_recognition ./pictures_of_people_i_know/ ./unknown_pictures/

/unknown_pictures/unknown.jpg,Barack Obama
/face_recognition_test/unknown_pictures/unknown.jpg,unknown_person
```

輸出結果的每一行對應著圖片中的一張臉，圖片名字和對應的人臉識別結果用逗號隔開。

如果結果輸出了`unknown_person`，就代表圖片中沒有包含已知人物圖片資料夾中的任何一個人。

#### `face_detection` 命令行工具

`face_detection`命令行工具可以在一張照片或一個照片資料夾中定位人臉位置(即輸出像素座標點)。

在命令行中使用`face_detection`，傳入一個圖片資料夾或一張圖片進行定位：

```bash
$ face_detection  ./folder_with_pictures/

examples/image1.jpg,65,215,169,112
examples/image2.jpg,62,394,211,244
examples/image2.jpg,95,941,244,792
```

輸出結果的每一行都對應著圖片中的一張臉，座標分別為上、右、下、左。

##### 調整人臉識別的容錯率

如果一張臉職別出了不只一種結果，那就代表這兩個人長得很像。這時候我們就可以把容錯率調低，使結果更準確。

透過傳入參數 `--tolerance` 來實現降低容錯率，默認容錯為0.6，容錯越低越嚴格。

```bash
$ face_recognition --tolerance 0.54 ./pictures_of_people_i_know/ ./unknown_pictures/

/unknown_pictures/unknown.jpg,Barack Obama
/face_recognition_test/unknown_pictures/unknown.jpg,unknown_person
```

如果我們想看人臉辨識的詳細數值，可以輸入 `--show-distance true`：

```bash
$ face_recognition --show-distance true ./pictures_of_people_i_know/ ./unknown_pictures/

/unknown_pictures/unknown.jpg,Barack Obama,0.378542298956785
/face_recognition_test/unknown_pictures/unknown.jpg,unknown_person,None
```

