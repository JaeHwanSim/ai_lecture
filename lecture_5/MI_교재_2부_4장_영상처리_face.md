

# 4장. 얼굴 인식 기법

# [1] Haar Cascade Classifier

Haar Cascade Classifier는 **빠르고 실시간 얼굴 인식**이 가능한 고전적인 컴퓨터 비전 알고리즘임. Paul Viola와 Michael Jones가 2001년에 제안한 **"Rapid Object Detection using a Boosted Cascade of Simple Features"** 논문에서 소개됨. OpenCV에 구현되어 있어 많이 사용됨.

### 1. Haar 특징 (Haar-like Features)

* 사각형 영역 간의 **밝기 차이(intensity difference)**를 기반으로 특징을 추출함.
* 눈(어두움)과 뺨(밝음) 같은 영역 차이를 감지할 수 있음.

| 특징 종류                  | 설명                           |
| ---------------------- | ---------------------------- |
| Edge Feature           | 수평 또는 수직 경계 감지 (예: 이마와 눈 사이) |
| Line Feature           | 밝은 줄과 어두운 줄 (예: 코)           |
| Four-rectangle Feature | 입 모양과 같은 대칭 구조               |

---

### 2. Cascade 구조

* **단계적으로 검출**하는 구조로, 초기에 간단한 특징으로 후보를 빠르게 제거하고, 점점 정밀한 검사를 수행.
* 덕분에 **속도는 빠르고 정확도도 높음.**

| 단계 | 설명                     |
| -- | ---------------------- |
| 초기 | 얼굴이 아닌 대부분의 영역을 빠르게 제거 |
| 후속 | 남은 후보 영역만 정밀 검사        |

---

### 3. Haar 방법의 장단점

| 항목  | 설명                                                  |
| --- | --------------------------------------------------- |
| 장점  | 빠른 속도, 실시간 처리 가능, CPU에서도 실행 가능                      |
| 단점  | 조명 변화, 회전, 얼굴 크기 변화에 민감함                            |
| 개선법 | 딥러닝 기반의 DNN, CNN(MTCNN, SSD, RetinaFace) 등으로 대체되는 중 |

---

### 4. OpenCV 활용 실시간 웹캠 얼굴 인식

```python
cap = cv2.VideoCapture(0)
face_cascade = cv2.CascadeClassifier(cv2.data.haarcascades + 'haarcascade_frontalface_default.xml')

while True:
    ret, frame = cap.read()
    if not ret:
        break
        
    gray = cv2.cvtColor(frame, cv2.COLOR_BGR2GRAY)
    faces = face_cascade.detectMultiScale(gray, 1.3, 5)
    
    for (x, y, w, h) in faces:
        cv2.rectangle(frame, (x, y), (x+w, y+h), (0, 255, 0), 2)

    cv2.imshow('Face Detection', frame)
    if cv2.waitKey(1) & 0xFF == ord('q'):
        break

cap.release()
cv2.destroyAllWindows()
```

| 함수/속성                          | 설명                  |
| ------------------------------ | ------------------- |
| `cv2.VideoCapture(0)`          | 웹캠에서 실시간 영상 캡처      |
| `os.listdir(folder)`           | 폴더 내 파일 목록 읽기       |
| `cv2.imwrite(filename, image)` | 이미지를 파일로 저장         |

---

# [2] HOG + SVM

- https://jakevdp.github.io/PythonDataScienceHandbook/05.14-image-features.html

---
# [3] MediaPipe Face Mesh


- MediaPipe의 **Face Mesh**는 얼굴의 3D 형태를 정밀하게 추정하여 **468개의 얼굴 랜드마크(특정 지점)**를 예측해주는 강력한 얼굴 분석 솔루션임.
2D 이미지에서 3D 메쉬 형태의 얼굴 구조를 **실시간으로 추출**할 수 있어서,
얼굴 인식, 표정 분석, 얼굴 정렬, 필터, 아바타 생성 등 다양한 응용에 활용됨.

## 1. 특징

| 항목  | 설명                                   |
| --- | ------------------------------------ |
| 입력  | RGB 이미지 or 실시간 비디오 프레임               |
| 출력  | 468개의 얼굴 랜드마크 + 얼굴 박스 + 얼굴 정렬된 3D 정보 |
| 속도  | 모바일 및 웹에서도 실시간 처리 가능                 |
| 정확도 | IR depth sensor 없이도 정밀한 3D 형태 추정     |
| 활용  | 증강현실(AR), 얼굴 추적, 감정 분석, 가상 메이크업 등    |

---

## 2. 동작 구조

1. **얼굴 검출**: 이미지에서 얼굴 영역을 탐지함
2. **랜드마크 예측**: 468개의 포인트를 추정 (눈, 코, 입, 턱 등)
3. **3D 위치 추정**: z-depth까지 포함하여 얼굴 구조 추정


### ✅ 객체 생성

```python
import mediapipe as mp

mp_face_mesh = mp.solutions.face_mesh
face_mesh = mp_face_mesh.FaceMesh(
    static_image_mode=False,        # True면 단일 이미지 처리
    max_num_faces=1,                # 감지할 얼굴 수
    refine_landmarks=True,         # 눈동자/입술 개선
    min_detection_confidence=0.5,  # 얼굴 탐지 신뢰도
    min_tracking_confidence=0.5    # 추적 신뢰도
)
```

| 파라미터                       | 설명                             |
| -------------------------- | ------------------------------ |
| `static_image_mode`        | 단일 이미지(True) vs 실시간 스트림(False) |
| `max_num_faces`            | 감지할 얼굴의 최대 수                   |
| `refine_landmarks`         | 눈동자, 입술 등의 세부 지점 향상            |
| `min_detection_confidence` | 탐지 최소 신뢰도 (0\~1)               |
| `min_tracking_confidence`  | 추적 최소 신뢰도 (0\~1)               |


### ✅ 처리 (process)

```python
results = face_mesh.process(rgb_image)
```

| 함수               | 설명                           |
| ---------------- | ---------------------------- |
| `process(image)` | RGB 이미지를 입력 받아 얼굴 랜드마크 추출 수행 |

※ OpenCV는 BGR이므로 반드시 `cv2.cvtColor(image, cv2.COLOR_BGR2RGB)`로 변환해야 함

---

### ✅ 결과 구조 (`results`)

```python
results.multi_face_landmarks
```

| 속성                     | 설명                                       |
| ---------------------- | ---------------------------------------- |
| `multi_face_landmarks` | 감지된 얼굴들의 랜드마크 리스트 (각 얼굴마다 468개)          |
| `landmark[i].x/y/z`    | 각 랜드마크의 3D 좌표 (0\~1 범위, 이미지 크기 기준 상대 좌표) |

---

| 구분                                 | 링크                                                                                                                                        |
| ---------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------- |
| 🔗 MediaPipe FaceMesh API (Python) | [https://developers.google.com/mediapipe/solutions/vision/face\_mesh](https://developers.google.com/mediapipe/solutions/vision/face_mesh) |
| 🔗 Drawing Utils                   | [https://google.github.io/mediapipe/solutions/drawing\_utils.html](https://google.github.io/mediapipe/solutions/drawing_utils.html)       |
| 🔗 Full Documentation (Python)     | [https://google.github.io/mediapipe/solutions/face\_mesh](https://google.github.io/mediapipe/solutions/face_mesh)                         |

---
## 3. 3D 랜드마크

MediaPipe Face Mesh는 얼굴의 주요 구조를 구성하는 **468개의 3D 랜드마크**를 예측합니다.
각 포인트는 고유한 **인덱스(index)**를 가지며, 얼굴의 특정 부위와 매칭됩니다.

전체 468개의 포인트는 다음과 같이 분류되며, 주요 부위별로 의미를 정리한 표는 아래와 같습니다.

---

###  (1) Face Mesh 468개의 주요 영역별 분포 요약

| 부위      | 인덱스 범위 또는 대표 인덱스        | 개수 (대략) | 설명                    |
| ------- | ----------------------- | ------- | --------------------- |
| 얼굴 윤곽   | 0–16, 234–454 (테두리 포함)  | 약 130개  | 턱, 광대, 관자놀이 등 윤곽선     |
| 눈썹      | 좌: 55–65, 우: 285–295    | 약 20개   | 눈썹 선과 윗부분             |
| 눈       | 좌: 33–133, 우: 362–263   | 약 50개   | 눈 주위, 눈꺼풀, 눈동자 포함     |
| 눈동자     | 좌: 468, 469, 470, 471   | 4개      | 동공 중심 및 경계            |
| 코       | 1, 2, 4, 5, 6, 197–195  | 약 20개   | 콧대, 콧볼, 코끝            |
| 입       | 61–88, 146–178, 308–324 | 약 80개   | 입술 테두리, 안쪽, 윗입술, 아랫입술 |
| 혀(내부 입) | 13, 14, 78–82           | 일부 포함   | 입안 중심, 혀              |
| 이마      | 10, 338, 297 등          | 약 30개   | 헤어라인 부근               |
| 귀 주변    | 127, 234, 454           | 약 30개   | 귀 기준점, 얼굴 좌우          |
| 얼굴 중심축  | 1, 9, 168, 200, 152     | –       | 코끝, 이마, 턱 등 기준축       |

---

### (2). 얼굴 주요 부위별 대표 인덱스 상세 표

#### ✅ 얼굴 윤곽 (Contour)

| 포인트     | 설명        |
| ------- | --------- |
| 10      | 이마 중앙     |
| 152     | 턱끝 중앙     |
| 234–454 | 좌우 턱선과 광대 |

---

#### ✅ 눈 (Eyes)

| 부위     | 왼쪽 인덱스        | 오른쪽 인덱스       | 설명        |
| ------ | ------------- | ------------- | --------- |
| 위 눈꺼풀  | 159, 160, 161 | 386, 387, 388 | 위쪽 경계     |
| 아래 눈꺼풀 | 145, 144, 153 | 374, 380, 385 | 아래쪽 경계    |
| 눈 안쪽   | 33, 133       | 362, 263      | 안쪽/바깥쪽    |
| 눈동자    | 468–471       | –             | 좌안 눈동자 4점 |

---

#### ✅ 눈썹 (Eyebrows)

| 부위  | 왼쪽 인덱스 | 오른쪽 인덱스  | 설명    |
| --- | ------ | -------- | ----- |
| 중앙  | 70–73  | 300–303  | 눈썹 중심 |
| 바깥쪽 | 55, 65 | 285, 295 | 눈썹 끝점 |

---

#### ✅ 코 (Nose)

| 포인트 | 설명          |
| --- | ----------- |
| 1   | 코 시작점       |
| 2   | 콧등 중심       |
| 4   | 콧망울 왼쪽      |
| 5   | 콧망울 오른쪽     |
| 6   | 코끝 (콧방울 아래) |
| 168 | 콧대 중간       |

---

#### ✅ 입 (Mouth)

| 부위      | 인덱스     |
| ------- | ------- |
| 입술 외곽   | 61–88   |
| 입술 내부   | 146–178 |
| 윗입술 중심  | 13      |
| 아랫입술 중심 | 14      |

---

#### ✅ 이마, 헤어라인

| 포인트 | 설명       |
| --- | -------- |
| 10  | 이마 중앙 상단 |
| 297 | 왼쪽 헤어라인  |
| 332 | 오른쪽 헤어라인 |

---

#### ✅ 귀 주변

| 포인트 | 설명       |
| --- | -------- |
| 127 | 왼쪽 귀 부근  |
| 356 | 오른쪽 귀 부근 |

---
---

## 4. Face Mesh의 삼각형 인덱스 구조

> MediaPipe Face Mesh의 468개 얼굴 랜드마크는 단순히 점들의 모음이 아니라, 이 점들을 \*\*삼각형(triangle)\*\*으로 연결하여 얼굴의 **3D 메쉬 구조**를 형성함.

> 이 삼각형 연결 방식은 컴퓨터 그래픽스나 3D 모델링에서 흔히 사용하는 기법으로, 얼굴 표면을 **작은 삼각형 면으로 분할**하여 정밀하게 묘사할 수 있게 해줌.


### ✅ 개념

* **Face Mesh = 점(landmarks) + 삼각형 연결 정보**
* 삼각형은 **3개의 랜드마크 인덱스로 정의됨**
  → 예: `[10, 338, 297]`는 10, 338, 297번 포인트를 연결한 삼각형


* 랜드마크만 표시: 점만 찍힘 → 얼굴 구조가 부정확해 보임
* 삼각형 연결(Tessellation) 표시: 면이 생겨서 얼굴 형태가 매끄럽게 보임

| 시각화 종류    | 느낌                |
| --------- | ----------------- |
| 468점만 표시  | 점묘화 느낌, 구조 파악 어려움 |
| 삼각형 연결 포함 | 얼굴 표면 재현 가능, 구조적  |


### ✅ 예시

```python
triangle = [10, 338, 297]  # 점 세 개를 연결하여 삼각형 하나 구성
```

### ✅ 활용


| 용도           | 설명                              |
| ------------ | ------------------------------- |
| 얼굴 표면 재구성 | 얼굴의 곡면을 표현하기 위해 점 사이를 삼각형으로 채움  |
| 3D 마스크/필터 | 각 삼각형에 텍스처, 색, 모양을 입혀 얼굴을 재현함   |
| 변형 추적     | 표정 변화, 얼굴 모양 변경 등을 삼각형 단위로 추적   |
| 메쉬 기반 모델  | 딥러닝에서 삼각형 구조 기반으로 메시 생성 및 비교 가능 |

---

### (1) 삼각형 인덱스를 만드는 원리

#### 목적:

**불규칙한 점들(landmarks)을 정렬된 얼굴 면(mesh surface)으로 표현**

#### 방법


① **랜드마크 정렬**

MediaPipe는 468개의 점을 **일정한 규칙**에 따라 얼굴의 구조별로 배치함


② **삼각형 분할 (Triangulation)**

* 얼굴 전체를 삼각형 단위로 나눔 (약 900여 개)
* 보통 **델로네 삼각분할(Delaunay Triangulation)** 또는 미리 정의된 패턴 사용


③ **삼각형 구성**

* 각 삼각형은 `[i, j, k]` 형식으로 구성됨
* `FACEMESH_TESSELATION`이라는 리스트로 제공됨 (MediaPipe 내부 정의)

---

---

### (2) MediaPipe에서 삼각형 인덱스를 사용하는 방법

```python
import mediapipe as mp

mp_face_mesh = mp.solutions.face_mesh
mp_drawing = mp.solutions.drawing_utils

# 얼굴 랜드마크와 삼각형 연결선 시각화
mp_drawing.draw_landmarks(
    image=frame,
    landmark_list=face_landmarks,
    connections=mp_face_mesh.FACEMESH_TESSELATION,
    landmark_drawing_spec=None,
    connection_drawing_spec=mp_drawing.DrawingSpec(color=(0, 255, 0), thickness=1)
)
```

#### ✅ `FACEMESH_TESSELATION`이란?

* MediaPipe에서 미리 정의한 삼각형 연결 리스트
* 약 **900개의 삼각형 구성** (`[(10, 338), (338, 297), (297, 332), ...]`)
* 사용자가 직접 연결을 지정하지 않아도 전체 메쉬 자동 시각화 가능

---


### (3) 델로네 삼각분할(Delaunay Triangulation)이란?

> **점들을 삼각형으로 연결하는 가장 자연스럽고 예쁜 방법**임.

#### 목적:

* 점들이 흩어져 있을 때, 그 점들을 연결해서 **삼각형들로 채워야** 하는 일이 있음.
* 이때 삼각형을 아무렇게나 만들지 않고,
  **가장 ‘균형 잡힌’ 삼각형**들을 만드는 방법이 바로 **델로네 삼각분할**임.

---

#### 델로네 삼각분할법

> **"삼각형의 외접원이 다른 어떤 점도 포함하지 않도록 만드는 삼각분할"**

* 외접원이란?

- 삼각형의 세 꼭짓점을 모두 지나는 **원**
- 이 외접원 안에 **다른 점이 들어가면 안 됨**

👉 이 조건을 만족하도록 삼각형을 만들면,
**좁고 긴 삼각형이 거의 없고**, 각도가 **둔각-예각**으로 **균형 잡히게** 만들어짐.


### 델로네 삼각분할의 장점

* 균형 있는 삼각형: 가늘고 긴 삼각형을 피해서 계산이 안정적
* 면적이 비슷함: 필터, 마스크 등에 자연스럽게 적용 가능    
* 응용 다양: 얼굴 메쉬, 지형지도, 게임, 3D 렌더링 등 

---
 