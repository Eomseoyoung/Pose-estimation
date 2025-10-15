# Pose-estimation

## 프로젝트 개요
이 프로젝트는 **합성 데이터 기반 Pose Estimation**을 목표로 합니다.  
실제 촬영 데이터 수집의 한계를 극복하기 위해 **Unreal Engine → Blender → 좌표 추출 → 전처리 → 모델 학습 → 추론 시각화** 과정을 거치는 파이프라인을 구축합니다.  

이 저장소는 **연구 논문용**보다는, **개발 과정을 기록하고 공유하는 개발일지**에 초점을 맞추고 있습니다.  

---

## 전체 파이프라인


    A[Unreal Engine] --> B[포즈 애니메이션 제작]
    B --> C[Blender]
    C --> D[좌표 추출 CSV/JSON]
    D --> E[데이터 전처리]
    E --> F[Pose Estimation 모델 학습]
    F --> G[추론 및 시각화]


pose-estimation-project/
├── data/          # 원본 및 전처리 데이터
├── notebooks/     # EDA, 시각화
├── scripts/       # Unreal/Blender 스크립트
├── models/        # 학습된 모델
├── results/       # 추론 결과
├── docs/
│   └── dev-log/   # 개발일지 (Day1, Day2, ...)
└── README.md
<img width="2554" height="1387" alt="image" src="https://github.com/user-attachments/assets/4458fbd6-454a-496e-b1f3-1be066701c2c" />
<img width="2549" height="1381" alt="image" src="https://github.com/user-attachments/assets/f9f182b6-5505-414c-bc9d-2d9bebb0de94" />
<img width="1564" height="840" alt="image" src="https://github.com/user-attachments/assets/14325dcc-bcf5-43ce-a6cb-b06c0fc9c66a" />


## 1.state와 behavior의 구분 
    state의 경우 볼드체로, behavior의 경우 이탤릭체로 구분하기로 함
    상태 유지 동작과 상태 변화 동작은 한국어로 동일하니 상태 변화 동작을 컬러표기함


## 2.학습데이터 형태
    behavior(3초) → behavior(~3초) → behavior(3초) 약 10초 영상
    8개 분류 존재 (자기 자신 참조 제외) / 필요시 눕기와 넘어지기 구분 → 그 경우 10개 분류
    1. 선_상태 섬 → 선_상태 걷기 → 선_상태 섬
    2. 선_상태 섬 → 선_상태 달리기 → 선_상태 섬
    3. 선_상태 섬 → 선_상태 앉기(s) → 앉음_상태 앉아있기
    4. 선_상태 섬 → 선_상태 눕기(넘어지기) → 누움_상태 누워있기 
    5. 앉음_상태 앉아있기 → 앉음_상태  일어서기(s) → 선_상태 섬
    6. 앉음_상태 앉아있기 → 앉음_상태  눕기(넘어지기) → 누움_상태 누워있기
    7. 누움_상태 누워있기 → 누움_상태 일어서기(l) →  선_상태 섬
    8. 누움_상태 누워있기 → 누움_상태 앉기(l) →  앉은_상태 앉아있기

## 3. 학습데이터 추출
     각기 다른 포즈데이터(.FBX)를 가지고 블랜더 및 언리얼를 이용해 뼈 좌표 추출 및 라벨링 작업 진행
         -> 라벨링 작업시에 시계열 데이터로 가지고 있어야하기때문에 틱단위로 좌표추출 진행
         -> 언리얼엔진에서의 포즈애니메이션의 좌표추출이 불가함으로 언리얼에서는 걷기 달리기 등의 기본 좌표 추출 진행
         -> maximo에서 포즈애니메이션 수집 후 블랜더에서 좌표추출 진행

## 4. 학습데이터 전처리
    1. unreal에서와 posenet, blender에서의 같은 애니메이션의 좌표값이 기준값이 다르게 설정이 되어 중앙값을 허리중심(spine)로 맞춰줌
    2. 총 6개의 좌표에서 나올 수 있는 각도를 지정하여 파생변수로 넣어 클래스 증가
