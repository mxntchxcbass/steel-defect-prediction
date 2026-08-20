<br/>
<br/>

# 0. Getting Started (시작하기)
```bash
# 10조_제조_코드_최종 (1).ipynb 를 Jupyter/Colab에서 순서대로 실행
pip install pandas numpy scikit-learn xgboost optuna shap
```

<br/>
<br/>

# 1. Project Overview (프로젝트 개요)
- 프로젝트 이름: **철판 결함 예측 분석 모델 개발**
- 프로젝트 설명: 철판 표면 결함 7종(Bumps · K_Scratch · Z_Scratch · Stains · Dirtiness · Pastry · Other_Faults)을 자동 분류해 수작업 선별 부담과 오분류를 줄이는 멀티클래스 품질 판별 모델
- 기간: 2026.05

<br/>
<br/>

# 2. Team Members (팀원 및 팀 소개)
- **팀명**: 10조 (제조 트랙) · 5인 · 임** 튜터

| 최지호 |
|:---:|

> 팀 산출물(코드·보고서·튜터 피드백 문서)에 본인 외 팀원의 개별 이름·역할이 기재되어 있지 않아, 확인 가능한 본인 담당만 정리합니다.

<br/>
<br/>

# 3. Key Features (주요 기능)
- **데이터 불균형 대응**: 클래스 간 최대 12배 표본 차이를 파악하고, **교차검증 폴드 내부의 훈련 데이터에만 SMOTE 오버샘플링**을 적용해 데이터 누출 방지

- **다중공선성 정리**: `TypeOfSteel A300/A400`, `Y_Maximum` 등 중복 변수를 체계적으로 제거

- **신중한 이상치 처리**: IQR 일괄 제거 시 `K_Scratch` 클래스가 80% 이상 손실되는 것을 사전에 확인 → 통계적 기준 대신 **극단값 2건만 수동 정밀 제거**

- **파생변수 설계**: 기하학적·밝기·위치·인덱스 4개 범주로 원본 27개 → **37개 변수로 확장**

- **모델 재설계 (Model 1 → Model 2)**: Model 1에서 `Bumps`↔`Other_Faults` 오분류가 데이터 품질 문제(잡음 클래스)로 진단됨 → Confusion Matrix · PCA/t-SNE · SHAP 3중 분석으로 두 클래스 군집이 크게 겹치고 변수 영향력이 미미함을 규명 → `Other_Faults`를 제외한 **Model 2**로 문제를 재정의

- **모델 비교**: Random Forest · ExtraTrees · XGBoost 등 5개 단일 모델 + Stacking 앙상블을 모두 비교, **Optuna 150 trials**로 F1-Macro 기준 하이퍼파라미터 탐색

<br/>
<br/>

# 4. Tasks & Responsibilities (작업 및 역할 분담)
| 이름 | 담당 |
|:---:|---|
| 최지호 | <ul><li>베이스라인 모델 구축</li><li>앙상블(Stacking) 모델링</li></ul> |

<br/>
<br/>

# 5. Technology Stack (기술 스택)
## 5.1 Language
| | |
|---|---|
| Python | ![Python](https://img.shields.io/badge/Python-3776AB?style=flat-square&logo=python&logoColor=white) |

<br/>

## 5.2 분석 / 모델링
| | | |
|---|---|---|
| Pandas / NumPy | ![Pandas](https://img.shields.io/badge/Pandas-150458?style=flat-square&logo=pandas&logoColor=white) | 데이터 처리 |
| scikit-learn | ![scikit-learn](https://img.shields.io/badge/scikit--learn-F7931E?style=flat-square&logo=scikitlearn&logoColor=white) | RandomForest · ExtraTrees · Stacking |
| XGBoost | ![XGBoost](https://img.shields.io/badge/XGBoost-EB0028?style=flat-square) | GradientBoosting 채택 모델 |
| Optuna | ![Optuna](https://img.shields.io/badge/Optuna-2085FF?style=flat-square) | 하이퍼파라미터 튜닝 (150 trials) |
| SHAP | ![SHAP](https://img.shields.io/badge/SHAP-8A2BE2?style=flat-square) | 결함 유형별 변수 기여도 해석 |

<br/>

## 5.3 Cooperation
| | |
|---|---|
| Git | ![Git](https://img.shields.io/badge/Git-F05032?style=flat-square&logo=git&logoColor=white) |
| Jupyter | ![Jupyter](https://img.shields.io/badge/Jupyter-F37626?style=flat-square&logo=jupyter&logoColor=white) |

<br/>
<br/>

# 6. Project Structure (프로젝트 구조)
```plaintext
철판결함예측분석모델개발/
├── 10조_제조_코드_최종 (1).ipynb                          # EDA·전처리·베이스라인·앙상블 전체 코드
├── 철판 결함 예측 분석 모델 개발.docx                      # 보고서 문서
├── 결과_보고서_10팀(철판_결함_예측_분석_모델_개발) .pptx.pdf
└── 2026-05-08 10-17-19.mp4                                # 발표영상
```

<br/>
<br/>

# 7. Development Workflow (분석 워크플로우)
```
데이터 구조 파악 → 도메인 기반 이상치 처리(K_Scratch 보존) → 파생변수 설계(27→37)
  → 다중 모델 비교(5종 + Stacking) → 오분류 원인 시각적 분석(Confusion Matrix·PCA/t-SNE·SHAP)
    → Model 1 → Model 2 재설계(Other_Faults 제외) → 결함별 공정 개선안 도출
```

<br/>
<br/>

# 8. Coding Convention
## 데이터 누출 방지
```
SMOTE 등 오버샘플링은 교차검증 폴드 내부의 훈련 데이터에만 적용한다.
전체 데이터에 먼저 적용하지 않는다.
```

## 이상치 처리 원칙
```
통계적 기준(IQR 등)을 기계적으로 적용하기 전에,
소수 클래스가 얼마나 손실되는지 먼저 확인한다.
손실이 크면 수동으로 극단값만 정밀 제거한다.
```

## 문제 재정의 기준
```
모델 튜닝으로 해결되지 않는 오분류는 모델이 아닌 데이터 품질 문제로 의심하고
PCA/t-SNE/SHAP으로 클래스 분리도를 먼저 검증한다.
```

<br/>
<br/>

# 9. 커밋 컨벤션
## 기본 구조
```
type : subject

body
```

## type 종류
```
feat : 새로운 분석/기능 추가
fix : 로직 수정
docs : 문서 수정
refactor : 코드 리팩토링
chore : 환경/패키지 설정
```

## 커밋 이모지
```
📝 코드 작성   🔨 리팩토링   🐛 버그 리포트   🚑 버그 수정
📚 문서 작성   ✨ 새 기능    🚀 배포
```

<br/>
<br/>

# 10. 결과 요약

| 접근 | 정확도 | Macro-F1 | 비고 |
|---|:--:|:--:|---|
| **GradientBoosting(XGBoost)** ⭐ | **81.96%** | **0.849** | 채택 (Weighted-F1 0.821) |
| Stacking (OOF) | 78.92% | — | 다중 베이스 + 메타러너 |

- 표본이 충분한 K/Z_Scratch(F1 0.96) · Stains/Dirtiness(F1 0.95+)는 안정적으로 분류
- 특징이 중첩되는 **Pastry(F1 0.585) · Bumps(F1 0.74)** 는 추가 검사 배분이 필요한 클래스로 지목
- 핵심 인사이트: "데이터 품질이 모델 구조보다 중요" — `Other_Faults`는 잡음 클래스로 진단, 제외 후 나머지 6개 결함에서 예지보전 전환 가능성 확인
