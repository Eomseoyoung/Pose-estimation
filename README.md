# Pose-estimation

## 프로젝트 개요
이 프로젝트는 **합성 데이터 기반 Pose Estimation**을 목표로 합니다.  
실제 촬영 데이터 수집의 한계를 극복하기 위해 **Unreal Engine → Blender → 좌표 추출 → 전처리 → 모델 학습 → 추론 시각화** 과정을 거치는 파이프라인을 구축합니다.  

이 저장소는 **연구 논문용**보다는, **개발 과정을 기록하고 공유하는 개발일지**에 초점을 맞추고 있습니다.  

---

## 전체 파이프라인
```mermaid
graph TD
    A[Unreal Engine] --> B[포즈 애니메이션 제작]
    B --> C[Blender]
    C --> D[좌표 추출 (CSV/JSON)]
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
