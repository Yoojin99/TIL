# Display 단위 (px, pt, dp, sp) 와 픽셀 밀도

## 절대 단위 상대 단위

* 절대 단위 : 절대적인 크기가 정해짐. (pt)
* 상대 단위 : 기준에 따라 크기가 달라짐. (px)

**근데 px 의 크기를 찾아보면 1/92 인치로 절대적인 크기로 말하는 곳이 있고 상대적인 크기라고 말하는 등 말이 다름**

## 물리적 vs 논리적 픽셀

### 물리적 픽셀

화면은 수많은 빛을 내는 것들로 구성되어 있고 이 빛 단위 하나를 물리적인 픽셀이라 함.

<img width="442" alt="image" src="https://github.com/Yoojin99/100-Days-of-SwiftUI-Practice/assets/41438361/1edcc5a4-5c26-4caa-86c4-30b48bec2f49">

그리고 css 에서의 pixel 은 1/96 이라는 절대적인 크기를 정하고 있기 때문에 혼동 x

### 논리적 픽셀

주로 디자인에서 픽셀 해상도에 기반해서 이미지를 다룰 때 사용. 논리적 픽셀은 그리드 상의 작은 사각형으로 이미지의 색깔 정보를 나타냄.

<img width="433" alt="image" src="https://github.com/Yoojin99/100-Days-of-SwiftUI-Practice/assets/41438361/823991e7-6fb9-4691-babb-2d33b075d1fd">

**논리적 픽셀은 절대적 크기가 없음. 이미지가 보여질 환경을 정의하지 않는 이상 100 x 100 논리적 픽셀 크기의 이미지의 절대적 크기를 알 수 없음.**

### 논리적 픽셀과 해상도

<img width="724" alt="image" src="https://github.com/Yoojin99/100-Days-of-SwiftUI-Practice/assets/41438361/9d9ba62b-d142-4236-876a-1ee6672a3c4c">

* 좌측 : 10 x 12 화면. 
* 우측 : 5 x 6 화면.

**동일한 px의 로고지만 고해상도(같은 면적에 픽셀 여러 개)인 화면에서 더 작게 보여짐**

## px (pixel)

**화소. 화면의 이미지를 구성하는 최소 단위. 해상도를 표현, 해상도에 따라 상대적 크기를 가짐.**

**픽셀은 논리적인 크기로 1px 의 정확한 크기는 ppi 에 따라 달라짐.**

* 해상도 (같은 면적일 때)
	* 픽셀 수 ⬆️ : 고해상도
	* 픽셀 수 ⬇️ : 저해상도

## PPI (Pixel per inch)

**1인치당 몇 개의 픽셀로 이루어져 있는지 나타내는 해상도 밀도 단위.**

### px 크기가 상대적인 이유

<img width="719" alt="image" src="https://github.com/Yoojin99/100-Days-of-SwiftUI-Practice/assets/41438361/a5805034-0a78-4def-b610-a49231b87670">

* 1ppi : 한 변의 길이가 **1인치인 정사각형의 한 변에 1개의 픽셀 존재**. 즉 1x1 픽셀로 구성됨
* 10ppi : 한 변의 길이가 **1인치인 정사각형의 한 변에 10개의 픽셀 존재**. 즉 10x10 픽셀로 구성됨

## pt (point)

**iOS에서 사용하는 크기 단위. 1/72 인치.** 

iOS / macOS/ watchOS 등 ui 의 constraint 를 설정할 때 값들의 단위가 됨.

### pt 사용 이유

* px : ppi 에 따라 크기 달라짐
* pt : 절대적인 크기를 가 수치.

<img width="677" alt="image" src="https://github.com/Yoojin99/100-Days-of-SwiftUI-Practice/assets/41438361/479aaa3e-5995-4564-93dd-1fbdc968eb50">

**pt 는 절대적인 크기를 갖고 있고 px 는 ppi 에 따라 변하는 가변적인 크기를 갖기 때문에 pt 크기 : px 크기 는 달라질 수 있음.**

* Retina display 가 나오기 전에는 iPhone 의 ppi 는 163 ppi 였음.
* Retina display 가 나오는 등 해상도 좋은 iPhone 이 계속 나옴.

<img width="517" alt="image" src="https://github.com/Yoojin99/100-Days-of-SwiftUI-Practice/assets/41438361/0a48c6d9-acfa-44ff-b75c-bb53f5b712fb">

⬆️ **px 단위를 사용하면 해상도 (ppi) 에 따라 실제 화면에 보이는 크기가 달라짐**

<img width="507" alt="image" src="https://github.com/Yoojin99/100-Days-of-SwiftUI-Practice/assets/41438361/23025003-5ff7-44fe-b0ac-03de152e1c13">

⬆️ **pt 는 화면상의 절대적인 크기를 갖는 수치 (iphone mini 화면에서의 1cm 랑 iphone 14 pro 화면에서의 1cm 는 같은거랑 비슷하게 생각)이기 때문에 pt 단위를 사용하면 ppi 와 관계 없이 동일한 크기의 이미지 보여줄 수 있음**

## 1x 2x 3x

Scaling / Pixel 의 관점에서 애플에는 3종류의 기기가 있음.

* Normal : 1 pt == 1 px @1x
* Retina : 1 pt == 4 px (2x2) @2x
* Retina iPhone6 등 : 1 pt == 9px (3x) @3x 

<img width="348" alt="image" src="https://github.com/Yoojin99/100-Days-of-SwiftUI-Practice/assets/41438361/7c37ace8-46a5-4575-a73e-1204b184156a">

[애플 공식 문서](https://developer.apple.com/library/archive/documentation/DeviceInformation/Reference/iOSDeviceCompatibility/Displays/Displays.html)에 의하면 1, 2, 3 과 같은 숫자를 UIKit Scale Factor 라 부르는 듯.

### 예시

<img width="419" alt="image" src="https://github.com/Yoojin99/100-Days-of-SwiftUI-Practice/assets/41438361/0cd635f9-ed56-4e64-aa4e-add6cecfe928">

* iPhone 11
	* 414 x 896 pt,  2x ➡️ 828 x 1792 px
* iPhone 11 Pro
	* 375 x 812 pt, 3x ➡️ 1125 x 2436 px

## dp

**디바이스 크기에 의존하지 않는 픽셀 (Device Independent Pixel). pt와 비슷한 개념.** 안드로이드에서 사용.

같은 dp 여도 해상도 높아짐 -> 더 선명하게 표현. dp 는 픽셀 단위로 변환될 범용적 단위.
360 x 640 dp 로 작업 후 아이콘, 이미지를 고 픽셀에서도 선명하게 보이게 큰 사이즈로 만듦.

## dpi

화면 밀도. iOS 의 UIKit Scale Factor (1x, 2x, 3x) 와 비슷한 개념.

<img width="703" alt="image" src="https://github.com/Yoojin99/100-Days-of-SwiftUI-Practice/assets/41438361/58682113-6d3b-452d-b218-7ae534917941">

* mdpi : 1dp == 1px
* xxhpdi: 1dp == 4px

## sp

텍스트 크기를 정의하는 경우 사용. Scale-independent pixels. 사용자의 폰트 크기 설정에 따라서 스케일링됨.

* 참고
* https://medium.com/hemisphereco/what-is-px-what-is-a-pt-what-is-dp-ecaefefa25a2
* https://developer.apple.com/library/archive/documentation/DeviceInformation/Reference/iOSDeviceCompatibility/Displays/Displays.html
* https://brunch.co.kr/@eeasily/17
* https://www.ios-resolution.com
* https://www.paintcodeapp.com/news/ultimate-guide-to-iphone-resolutions
* https://design3damso.tistory.com/entry/디자인-단위
