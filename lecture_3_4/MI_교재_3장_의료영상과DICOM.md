---

# **3장. 의료 영상 데이터: DICOM**

## 1\. 의료 영상

의료 영상(Medical Imaging)이란 인체 내부 구조를 시각화하여 진단, 치료 계획 수립, 수술 안내 등에 활용되는 기술임. 생체 내부의 **형태(structure)** 또는 \*\*기능(function)\*\*을 시각화함으로써 비침습적으로 환자 상태를 확인할 수 있음.

### ● 의료 영상의 주요 목적

* 질병의 조기 진단  
* 치료 경과 모니터링  
* 수술 계획 및 시뮬레이션  
* 컴퓨터 기반 진단 지원(CAD: Computer-Aided Diagnosis)

---

### ●  주요 영상 Modalities(획득 방식)

의료 영상 modality란 영상을 생성하는 물리적 방식 또는 장비 종류를 의미함. 대표적인 modality들은 다음과 같음.

#### (1) 구조 기반 영상 (Structural Imaging)

| 영상 종류 | 원리 | 장점 | 단점 | 활용 예시 |
| :---- | :---- | :---- | :---- | :---- |
| X-ray | X선 투과율 | 빠름, 저렴함 | 중첩 문제 | 흉부, 골절 진단 |
| CT (Computed Tomography) | X-ray 회전 스캔 \+ 재구성 | 3D, 정확한 골 구조 | 방사선 피폭 큼 | 두부 외상, 폐질환 |
| MRI (Magnetic Resonance Imaging) | 자기장+RF 신호 | 연부 조직에 탁월 | 고가, 시간 소요 | 뇌, 척추, 관절 |
| 초음파 (Ultrasound) | 음파 반사 | 실시간, 안전 | 시야 제한, 사용자 의존성 | 심장, 복부, 산부인과 |

#### (2) 기능 기반 영상 (Functional Imaging)

| 영상 종류 | 원리 | 장점 | 단점 | 활용 예시 |
| :---- | :---- | :---- | :---- | :---- |
| PET (Positron Emission Tomography) | 방사성 동위원소 분포 | 대사 활동 분석 | 고가, 방사선 피폭 | 암, 뇌 기능 |
| fMRI (Functional MRI) | 산소 농도 변화 | 비침습적 뇌 기능 분석 | 시간 해상도 낮음 | 뇌 기능 연구 |
| SPECT | 감마선 탐지 | 장기 기능 시각화 | 낮은 해상도 | 심장, 뇌 혈류 |

#### (3) 각 영상 modality의 비교

| 항목 | X-ray | CT | MRI | 초음파 | PET |
| :---- | :---- | :---- | :---- | :---- | :---- |
| 차원 | 2D | 3D | 3D | 2D/3D | 3D |
| 시간 | 매우 빠름 | 중간 | 느림 | 실시간 | 느림 |
| 해상도 | 낮음 | 높음 | 매우 높음 | 낮음 | 낮음 |
| 비용 | 저렴 | 중간 | 고가 | 저렴 | 고가 |
| 방사선 | 있음 | 있음 | 없음 | 없음 | 있음 |
| 주요 사용 부위 | 뼈, 폐 | 내장, 뇌 | 뇌, 관절 | 복부, 심장 | 전신 대사 |

---

## **2\. 의료 영상 데이터 구조**

## (1) DICOM

### ● DICOM 개요

* DICOM(Digital Imaging and Communications in Medicine)은 의료 영상 저장 및 교환을 위한 국제 표준 포맷임.  
* 영상 데이터뿐 아니라 환자 정보, 촬영 장비 정보, 촬영 설정 등 **메타데이터**를 포함함.  
* 대부분의 병원에서 사용하는 [PACS](https://www.gunchinews.com/news/articleView.html?idxno=246) 시스템은 DICOM을 기반으로 구성됨.  
* 파일 확장자: `.dcm`

### ● DICOM 구조

┌────────────────────────────┐

│ File Meta Information      │ ← 전송 관련 정보 (DICOM Version, UID 등)

├────────────────────────────┤

│ Dataset (메타데이터 \+ 이미지) │

│  ├ Patient Module          │ ← 환자 정보

│  ├ Study Module            │ ← 검사(Study) 정보

│  ├ Series Module           │ ← 시리즈 정보

│  ├ Image Module            │ ← 영상 정보

│  └ Pixel Data              │ ← 이미지 원본 데이터

└────────────────────────────┘

---

### ● DICOM 표준 및 관련 링크

* [DICOM 공식 문서 (NEMA)](https://www.dicomstandard.org/)  
* [DICOM Tags](https://www.dicomlibrary.com/dicom/dicom-tags/)  
* [DICOM Standard Browser](https://dicom.innolitics.com/ciods/basic-structured-display)  
* [Pydicom 라이브러리 문서](https://pydicom.github.io/)  
* [RadiAntDICOM Viewer](https://www.radiantviewer.com/dicom-viewer-manual/dicom_tags.html)  
* [Free DICOM Viewer: MicroDicom](https://www.microdicom.com/#google_vignette)

---

### ● 주요 메타데이터

#### 📦 DICOM 헤더 주요 항목

| Tag (Group, Element) | 속성명 | 설명 | 예시 |
| :---- | :---- | :---- | :---- |
| (0008, 0016\) | SOPClassUID | 해당 DICOM 객체의 유형 | 1.2.840.10008.5.1.4.1.1.2 (CT Image) |
| (0008, 0018\) | SOPInstanceUID | 이 객체의 고유 ID | 1.2.840.xxxxxx |
| (0002, 0010\) | TransferSyntaxUID | 전송 구문 (압축, 인코딩 정보) | 1.2.840.10008.1.2.1 (Explicit VR Little Endian) |

#### 1\. SOP Class UID (Service-Object Pair Class UID)

● 정의

* \*\*DICOM 객체의 타입(클래스)\*\*을 정의하는 UID (고유 식별자)임.  
* 어떤 \*\*서비스-객체 쌍(SOP)\*\*을 나타내는지 알려줌.

● 역할

* 이 UID는 해당 DICOM 파일이 **CT 영상인지**, **MRI 영상인지**, **PET 영상인지** 등을 구분해줌.  
* 또한 영상뿐 아니라 **SR(Structured Report)**, **RT Dose**, **Segmentation** 등도 포함함.

● 예시

| SOPClassUID | 의미 |
| :---- | :---- |
| `1.2.840.10008.5.1.4.1.1.2` | CT 이미지 저장 |
| `1.2.840.10008.5.1.4.1.1.4` | MR 이미지 저장 |
| `1.2.840.10008.5.1.4.1.1.128` | PET 이미지 저장 |

#### 2\. SOP Instance UID

● 정의

* 해당 \*\*DICOM 객체(한 개의 이미지 파일)\*\*를 고유하게 식별하는 UID  
* **DICOM 인스턴스의 주민등록번호**라고 생각하면 됨.

한 병원에서 CT 검사 결과가 여러 장 저장되는데, \*\*"SOPInstanceUID는 그 중 딱 한 장"\*\*을 정확히 가리키는 ID임.

● 역할

* 동일한 환자에 여러 이미지가 있을 때, 개별 이미지를 유일하게 식별 가능  
* 데이터베이스나 PACS 서버에서 인스턴스를 정확하게 참조할 때 사용

● 예시

SOPInstanceUID: 1.2.826.0.1.3680043.2.1125.1.964234984.8123.1597420179.275

#### 3\. Transfer Syntax UID

● 정의

* DICOM 파일 내부의 **이미지 압축/인코딩 방식**을 지정하는 UID  
* 예: 압축 방식(JPEG, JPEG2000), 엔디안(little-endian/big-endian), VR 표현 등

● 역할

* **수신 장비나 소프트웨어가 DICOM 데이터를 어떻게 해석할지** 결정  
* 디코딩 과정에서 필요한 필수 정보

● 예시

| UID | Transfer Syntax 종류 | 설명 |
| :---- | :---- | :---- |
| `1.2.840.10008.1.2` | Implicit VR Little Endian | 기본 비압축, VR 명시 안함 |
| `1.2.840.10008.1.2.1` | Explicit VR Little Endian | VR 명시, Little Endian |
| `1.2.840.10008.1.2.4.50` | JPEG Baseline | 손실 압축 사용 |
| `1.2.840.10008.1.2.4.90` | JPEG 2000 (Lossless) | 무손실 JPEG 2000 |

#### 4\. VR (Value Representation)이란?

● 정의

\*\*VR(Value Representation)\*\*은 DICOM 데이터 요소에서 **값이 어떤 타입으로 표현되는지**를 나타내는 정보임. 즉, 각 DICOM 데이터 필드가 **문자열인지, 정수인지, 날짜인지 등**을 명시함.

| 항목 | 역할 |
| :---- | :---- |
| 데이터 **형식 구분** | 문자열, 정수, 부동소수, 날짜 등 |
| **파싱/디코딩**을 위한 정보 제공 | 수신 프로그램이 어떤 식으로 데이터를 해석할지 알려줌 |
| DICOM **표준 호환성 유지** | 모든 장비가 같은 방식으로 해석 가능하게 함 |

● 주요 VR 타입

| VR 코드 | 의미 | 예시 값 | 설명 |
| :---- | :---- | :---- | :---- |
| **PN** | Person Name | `Kim^Jin^Soo` | 환자 이름 등 |
| **DA** | Date | `20250714` | 날짜 (YYYYMMDD) |
| **TM** | Time | `135501.00` | 시간 (HHMMSS) |
| **UI** | Unique Identifier | `1.2.840.10008.1.2.1` | UID |
| **CS** | Code String | `M` | 코드 (예: 성별 M/F) |
| **US** | Unsigned Short | `512` | 2바이트 정수 |
| **IS** | Integer String | `42` | 문자열로 된 정수 |
| **FL** | Float | `3.14` | 4바이트 float |
| **DS** | Decimal String | `0.003` | 소수점 표현 |
| **SQ** | Sequence | \- | 내부에 또 다른 DICOM 요소들을 포함 |

●  Explicit VR vs Implicit VR

* Explicit VR (명시적 VR)

각 DICOM 태그마다 VR이 **명시적으로 기록되어 있음** 수신 장비가 데이터 형식을 쉽게 식별 가능 예: `TransferSyntaxUID = 1.2.840.10008.1.2.1` (Explicit VR Little Endian)

* Implicit VR (암시적 VR)

VR 정보가 **명시적으로 기록되어 있지 않음** 대신 수신 소프트웨어가 **DICOM Dictionary**를 참고해 해석 예: `TransferSyntaxUID = 1.2.840.10008.1.2` (Implicit VR Little Endian)

#### 5\. Little Endian 이란?

컴퓨터에서 정수나 부동소수점 숫자는 \*\*여러 바이트(byte)\*\*로 저장됨. 이 때, **어떤 바이트를 먼저 저장할 것인가**를 정하는 방식이 \*\*바이트 순서(Byte Order)\*\*임.

📌 대부분의 PC (x86 계열), 병원 장비 및 소프트웨어는 **Little Endian**을 기본으로 사용

● Little Endian vs Big Endian

| 항목 | Little Endian | Big Endian |
| :---- | :---- | :---- |
| 바이트 순서 | **하위 바이트부터** 저장 | **상위 바이트부터** 저장 |
| 예: 0x12345678 저장 | `78 56 34 12` | `12 34 56 78` |
| 주로 사용 OS | x86(Intel), ARM (일부) | 네트워크, 일부 RISC |

* 예를 들어, 0x12345678 을 저장하는 방식은,

| 주소 | Little Endian 값 | Big Endian 값 |
| :---- | :---- | :---- |
| 0x1000 | 78 | 12 |
| 0x1001 | 56 | 34 |
| 0x1002 | 34 | 56 |
| 0x1003 | 12 | 78 |

---

#### 🧑‍ 환자 정보 메타데이터

| Tag (Group, Element) | 속성명 | 설명 | 예시 |
| :---- | :---- | :---- | :---- |
| (0010, 0010\) | PatientName | 환자 이름 | 홍^길동 |
| (0010, 0020\) | PatientID | 병원 내 환자 식별 ID | 123456 |
| (0010, 0030\) | PatientBirthDate | 생년월일 | 19700101 |
| (0010, 0040\) | PatientSex | 성별 | M / F / O |

---

#### 🧪 연구 및 검사 정보 메타데이터

| Tag (Group, Element) | 속성명 | 설명 | 예시 |
| :---- | :---- | :---- | :---- |
| (0008, 0020\) | StudyDate | 검사 날짜 | 20230615 |
| (0008, 0030\) | StudyTime | 검사 시간 | 134512.000000 |
| (0008, 0050\) | AccessionNumber | 병원 정보 시스템의 검사 번호 | 2023-CT-0452 |
| (0008, 1030\) | StudyDescription | 연구 설명 | Chest CT Scan |
| (0020, 0011\) | SeriesNumber | Series 번호 | 1 |
| (0020, 000D) | StudyInstanceUID | 연구 단위 고유 ID | 1.2.840.113619... |
| (0020, 0010\) | StudyID | 연구 ID | 3589214 |
| (0008, 0060\) | Modality | 영상 종류 | CT / MR / CR / US 등 |
| (0008, 0070\) | Manufacturer | 제조사 | Siemens |

---

#### 🖼️ 이미지 정보 메타데이터

| Tag (Group, Element) | 속성명 | 설명 | 예시 |
| :---- | :---- | :---- | :---- |
| (0028, 0010\) | Rows | 이미지 높이 (픽셀 수) | 512 |
| (0028, 0011\) | Columns | 이미지 너비 (픽셀 수) | 512 |
| (0028, 0100\) | BitsAllocated | 픽셀당 비트 수 | 16 |
| (0028, 0101\) | BitsStored | 저장된 비트 수 | 12 |
| (0028, 0102\) | HighBit | 최상위 비트 위치 | 11 |
| (0028, 0030\) | PixelSpacing | 픽셀 간 물리적 간격 (mm 단위) | \['0.488281', '0.488281'\] |
| (0020, 0013\) | InstanceNumber | 슬라이스 번호 또는 순서 | 30 |
| (0028, 1050\) | WindowCenter | 영상 윈도우 중심값 | 40 |
| (0028, 1051\) | WindowWidth | 영상 윈도우 너비값 | 400 |

#### 🖼️ 이미지 데이터

| Tag (Group, Element) | 속성명 | 설명 | 예시 |
| :---- | :---- | :---- | :---- |
| (7FE0,0010) | PixelData | 바이너리 영상 데이터 | 128,...., |

#### 모달리티 특화 메타 데이터

| Modalities | Tag (Group,Element) | Keyword | 설명 |
| :---- | :---- | :---- | :---- |
| **공통** | (0010,0010) | PatientName | 환자 이름 |
|  | (0008,0060) | Modality | 영상 종류 (CT, MR, CR, US 등) |
|  | (0020,0013) | InstanceNumber | 슬라이스 또는 프레임 번호 |
|  | (0028,0010)/(0011) | Rows / Columns | 영상 높이 / 너비 |
|  | (0028,0030) | PixelSpacing | 픽셀 간 실제 거리 (mm) |
|  | (0028, 0100\) | BitsAllocated | 픽셀당 비트 수 |
|  | (0028, 0101\) | BitsStored | 저장된 비트 수 |
|  | (0028, 0102\) | HighBit | 최상위 비트 위치 |

#### 🩻 X-ray (CR, DX)

| Tag (Group,Element) | Keyword | 설명 |
| :---- | :---- | :---- |
| (0008,103e) | SeriesDescription | 예: Chest PA, Lateral 등 |
| (0018,0060) | KVP | 관전압 (kVp) |
| (0018,1110) | DistanceSourceToDetector | 촬영 거리 |
| (0018,1160) | FilterType | 필터 종류 |
| (0018,5101) | ViewPosition | 촬영 방향 (PA, AP, LAT 등) |
| (0018,1405) | ExposureTime | 노출 시간 (ms) |
| (0018,1151) | XRayTubeCurrent | 튜브 전류 (mA) |
| (0028,1050)/(1051) | WindowCenter / WindowWidth | 영상 윈도우 설정 |

## 🩻 CT (Computed Tomography)

| Tag (Group,Element) | Keyword | 설명 |
| :---- | :---- | :---- |
| (0018,0060) | KVP | X선 관전압 |
| (0018,1151) | XRayTubeCurrent | 관전류 (mA) |
| (0018,0050) | SliceThickness | 슬라이스 두께 (mm) |
| (0018,0088) | SpacingBetweenSlices | 슬라이스 간 간격 |
| (0020,0032) | ImagePositionPatient | 환자 기준 이미지 위치 (xyz 좌표) |
| (0020,0037) | ImageOrientationPatient | 환자 기준 이미지 방향 |
| (0028,1052)/(1053) | RescaleIntercept / RescaleSlope | 정량화 보정 계수 |
| (0028,1050)/(1051) | WindowCenter / WindowWidth | 영상의 시각화 범위 조절용 |

## 🩻 MRI (Magnetic Resonance Imaging)

| Tag (Group,Element) | Keyword | 설명 |
| :---- | :---- | :---- |
| (0018,0020) | ScanningSequence | SE, IR, GR 등 |
| (0018,0021) | SequenceVariant | 순열 정보 |
| (0018,0022) | ScanOptions | 옵션 (FS, FC 등) |
| (0018,0080) | RepetitionTime | 반복 시간 (TR) |
| (0018,0081) | EchoTime | 에코 시간 (TE) |
| (0018,1314) | FlipAngle | Flip 각도 |
| (0018,0050) | SliceThickness | 슬라이스 두께 |
| (0028,1050)/(1051) | WindowCenter / WindowWidth | 영상 대비 조절용 |

## 🩻 Ultrasound (US)

| Tag (Group,Element) | Keyword | 설명 |
| :---- | :---- | :---- |
| (0008,103e) | SeriesDescription | 복부, 심장, 산부인과 등 |
| (0018,1063) | FrameTime | 프레임 간 간격 (동영상일 경우) |
| (0018,6011) | SequenceOfUltrasoundRegions | 촬영 영역 정보 |
| (0018,6014) | RegionLocationMinX0 | 영역 시작 X 위치 |
| (0018,6024) | PhysicalUnitsXDirection | 측정 단위 (mm, %, dB 등) |
| (0028,0008) | NumberOfFrames | 프레임 수 (동영상일 경우) |

---

## 🩻 PET (Positron Emission Tomography)

| Tag (Group,Element) | Keyword | 설명 |
| :---- | :---- | :---- |
| (0018,1074) | RadionuclideTotalDose | 총 방사선 투여량 |
| (0018,1075) | RadionuclideHalfLife | 반감기 |
| (0018,1079) | RadiopharmaceuticalStartTime | 투여 시작 시간 |
| (0018,0031) | Radiopharmaceutical | 사용된 방사선 물질 (FDG 등) |
| (0018,9305) | AcquisitionTime | 획득 시간 |
| (0028,1052)/(1053) | RescaleIntercept / RescaleSlope | 정량화 보정 계수 |
| (0020,0013) | InstanceNumber | 프레임 순서 |

---

### ●  복습 문제: DICOM 포맷의 가장 큰 특징은?

1. JPEG로 압축되어 저장됨  
     
2. 의료 영상은 저장하지 않고 텍스트만 포함됨  
     
3. 영상과 메타데이터를 함께 저장함  
     
4. 영상 처리에 사용되지 않음

**정답: 3번** **해설:** DICOM은 영상과 환자/촬영 관련 메타데이터를 함께 저장하는 국제 표준 포맷임.

---

### DICOM 관련 라이브러리 및 파일 다운로드

\# (1) 라이브러리 설치

\!pip install pydicom \-q

\# (2) 파일 다운로드

import os

import zipfile

import urllib.request

 \#\# imgproc 파일 다운로드 및 압축해제

zip\_url \= "https://github.com/bykim9988/imgproc/archive/refs/heads/master.zip"

zip\_file \= "imgproc.zip"

IMG\_DIR \= "imgproc-master"

urllib.request.urlretrieve(zip\_url, zip\_file)

with zipfile.ZipFile(zip\_file, 'r') as zip\_ref:

    zip\_ref.extractall()

 \#\# 결과 확인

print(f"{IMG\_DIR} 디렉토리에 압축이 해제되었습니다.")

if os.path.exists(zip\_file):

    os.remove(zip\_file)

    print(f"{zip\_file} 삭제 완료")

### 📂 폴더 구조

/content/

├── imgproc-master/          ← 이미지 파일 폴더

├── \*\*\*\*.dcm   ← DICOM 이미지들

---

### 메타 정보 추출 코드

import numpy as np

import matplotlib.pyplot as plt

import pydicom as dicom

from pprint import pprint

def analyze\_dicom(file\_path):

    try:

        ds \= pydicom.dcmread(file\_path)

        modality \= ds.get("Modality", "Unknown")

        print(f"\\n📁 파일: {os.path.basename(file\_path)}")

        print(f"🧪 Modality: {modality}")

        

        print("📌 주요 메타데이터 추출:")

        print(f"  \- Patient Name: {ds.get('PatientName', 'N/A')}")

        print(f"  \- Study Date: {ds.get('StudyDate', 'N/A')}")

        print(f"  \- Rows x Columns: {ds.get('Rows', 'N/A')} x {ds.get('Columns', 'N/A')}")

        

        if modality \== "CT":

            print(f"  \- Slice Thickness: {ds.get('SliceThickness', 'N/A')} mm")

            print(f"  \- KVP: {ds.get('KVP', 'N/A')}")

            print(f"  \- Rescale Slope / Intercept: {ds.get('RescaleSlope', 'N/A')} / {ds.get('RescaleIntercept', 'N/A')}")

        

        elif modality \== "MR":

            print(f"  \- Repetition Time (TR): {ds.get('RepetitionTime', 'N/A')} ms")

            print(f"  \- Echo Time (TE): {ds.get('EchoTime', 'N/A')} ms")

            print(f"  \- Flip Angle: {ds.get('FlipAngle', 'N/A')}")

        

        elif modality in \["CR", "DX"\]:  \# X-ray 계열

            print(f"  \- View Position: {ds.get('ViewPosition', 'N/A')}")

            print(f"  \- Exposure Time: {ds.get('ExposureTime', 'N/A')} ms")

            print(f"  \- X-Ray Tube Current: {ds.get('XRayTubeCurrent', 'N/A')} mA")

        

        elif modality \== "US":

            print(f"  \- Number of Frames: {ds.get('NumberOfFrames', 'N/A')}")

            print(f"  \- Frame Time: {ds.get('FrameTime', 'N/A')} ms")

        

        elif modality \== "PT":  \# PET

            print(f"  \- Radiopharmaceutical: {ds.get('Radiopharmaceutical', 'N/A')}")

            print(f"  \- Total Dose: {ds.get('RadionuclideTotalDose', 'N/A')} Bq")

        else:

            print("  \- (해당 modality에 특화된 정보 없음)")

        print(f"  \- Bits Allocated....: {ds.BitsAllocated}")

        print(f"  \- Bits Stored.......: {ds.BitsStored}")

        print(f"  \- High Bit..........: {ds.HighBit}")

    

        print(f"  \- raw image max val.: {ds.pixel\_array.max()}" )

        print(f"  \- raw image min val.: {ds.pixel\_array.min()}")

        if hasattr(ds, 'WindowCenter'):

            print(f"  \- WindowCenter......: {ds.WindowCenter}" )

        if hasattr(ds, 'WindowWidth'):

            print(f"  \- WindowWidth.......: {ds.WindowWidth}" )

    except Exception as e:

        print(f"❌ 오류 발생 \- {file\_path}: {e}")

\# ▶ DICOM 폴더 내 모든 파일 분석

def analyze\_dicom\_folder(folder\_path):

    print(f"\\n📂 폴더 분석 시작: {folder\_path}")

    for fname in os.listdir(folder\_path):

        if fname.lower().endswith(".dcm"):

            full\_path \= os.path.join(folder\_path, fname)

            analyze\_dicom(full\_path)

---

## (2) DICOM 영상 시각화

---

def disp\_dicom\_image(file\_path):

    import pydicom as dicom

    ds= dicom.dcmread(file\_path)

    image \= ds.pixel\_array.copy()

    RescaleSlope \= ds.RescaleSlope if hasattr(ds,'RescaleSlope') else 1.0

    RescaleIntercept \= ds.RescaleIntercept  if hasattr(ds, 'RescaleIntercept') else 0.0

    rescaled\_image \= image \*  RescaleSlope \+ RescaleIntercept

    rescaled\_image \= rescaled\_image.astype(np.int16)

    plt.figure(figsize=(12,10))

    plt.subplot(231)

    plt.imshow(ds.pixel\_array, cmap=plt.cm.gray)

    plt.grid(False)

    plt.title(file\_path)

    plt.subplot(232)

    plt.imshow(image, cmap=plt.cm.gray)

    plt.grid(False)

    plt.title("Preprocessed")

    plt.subplot(233)

    plt.imshow(rescaled\_image, cmap=plt.cm.gray)

    plt.grid(False)

    plt.title("Rescaled")

    plt.subplot(234)

    plt.hist(ds.pixel\_array.flatten())

    plt.title("Original")

    plt.grid(True)

    plt.subplot(235)

    plt.hist(image.flatten())

    plt.title("Preprocessed")

    plt.grid(True)

    plt.subplot(236)

    plt.hist(rescaled\_image.flatten())

    plt.title("Rescaled")

    plt.grid(True)

    plt.show()

    if hasattr(ds, 'WindowCenter') and hasattr(ds, 'WindowWidth'):

        wc \= ds.WindowCenter

        windowed\_image \= rescaled\_image.copy()

        idx \=  

        plt.figure(figsize=(12, 6))

        plt.subplot(121)

        if type(ds.WindowCenter) is dicom.multival.MultiValue:

            wc \= float(ds.WindowCenter\[idx\])

        else:

            wc \= float(ds.WindowCenter)

        if type(ds.WindowWidth) is dicom.multival.MultiValue:

            ww \= float(ds.WindowWidth\[idx\])

        else:

            ww \= float(ds.WindowWidth)

        vmin= wc \- round(ww/2)

        vmax \= wc \+ round(ww/2)

        windowed\_image \= np.clip(windowed\_image, , )

        plt.imshow(windowed\_image, cmap=plt.cm.gray)

        plt.grid(False)

        plt.title("Windowed")

        plt.subplot(122)

        plt.hist(windowed\_image.flatten())

        plt.title("Windowed")

        plt.grid(True)

        plt.show() 

\# ▶ DICOM 폴더 내 모든 파일 분석

def display\_dicom\_images(folder\_path):

    print(f"\\n📂 영상 출력 시작: {folder\_path}")

    for fname in os.listdir(folder\_path):

        if fname.lower().endswith(".dcm"):

            full\_path \= os.path.join(folder\_path, fname)

            print(f"  \- {full\_path}")

            disp\_dicom\_image(full\_path)

---

### Kaggle: Structural MRI Datasets (T1, T2, FLAIR etc.)

- [https://www.kaggle.com/datasets/ilknuricke/neurohackinginrimages/data](https://www.kaggle.com/datasets/ilknuricke/neurohackinginrimages/data) \--\> Code  Tab \--\> Brain\_MRI\_dicom\_reading

\# 코드 수정부분

\# (1) Download files

\# Kaggle 공식 다운로드 코드

\# (2)

for dirname, \_, filenames in os.walk(path):   

\# (3)

with zipfile.ZipFile(path+"/"+Dataset+".zip","r") as z:

    z.extractall(".")

\# (4)

    axs\[0\].imshow(datasetT1, cmap='gray')

\# (5)

    show\_dcm\_info(datasetT1)

    show\_dcm\_info(datasetT2)

    show\_dcm\_info(datasetFLAIR)

    print( datasetT1.pixel\_array.min(), datasetT1.pixel\_array.max() )

    print( datasetT2.pixel\_array.min(), datasetT2.pixel\_array.max() )

    print( datasetFLAIR.pixel\_array.min(), datasetFLAIR.pixel\_array.max() )

---

