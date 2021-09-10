---
title: 'ML Studio(클래식): Azure Machine Learning으로 마이그레이션'
description: 현대화된 데이터 과학 플랫폼을 위해 Studio(클래식)에서 Azure Machine Learning으로 마이그레이션합니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio-classic
ms.topic: how-to
author: xiaoharper
ms.author: zhanxia
ms.date: 08/23/2021
ms.openlocfilehash: 87902df54eccdd7038c8351f4ca1794b013a0f13
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/23/2021
ms.locfileid: "122699252"
---
# <a name="migrate-to-azure-machine-learning"></a>Azure Machine Learning으로 마이그레이션 

> [!IMPORTANT]
> Machine Learning Studio(클래식)에 대한 지원은 2024년 8월 31일에 종료됩니다. 해당 날짜까지 [Azure Machine Learning](/azure/machine-learning/overview-what-is-azure-machine-learning)으로 전환하는 것이 좋습니다.
>
> 2021년 12월 1일부터 새로운 Machine Learning Studio(클래식) 리소스를 만들 수 없습니다. 2024년 8월 31일까지는 기존 Machine Learning Studio(클래식) 리소스를 계속 사용할 수 있습니다.  
>
> ML Studio(클래식) 설명서는 사용 중지되며 나중에 업데이트되지 않을 수 있습니다.

Studio(클래식)에서 Azure Machine Learning으로 마이그레이션하는 방법을 알아봅니다. Azure Machine Learning은 코드 없는 방식과 코드 우선 방식을 결합한 현대화된 데이터 과학 플랫폼을 제공합니다.

기본 “리프트 앤 시프트” 마이그레이션을 위한 가이드입니다. 기존 기계 학습 워크플로를 최적화하거나 ML 플랫폼을 현대화하려는 경우 디지털 설문 조사 도구, 워크시트, 계획 템플릿 등의 추가 리소스는 [Azure Machine Learning 채택 프레임워크](https://aka.ms/mlstudio-classic-migration-repo)를 참조하세요.

![Azure ML 채택 프레임워크](./media/migrate-overview/aml-adoption-framework.png)

## <a name="recommended-approach"></a>권장 접근 방식

Azure Machine Learning으로 마이그레이션하려면 다음 방식을 사용하는 것이 좋습니다.

> [!div class="checklist"]
> * 1단계: Azure Machine Learning 평가
> * 2단계: 전략 및 계획 정의
> * 3단계: 실험 및 웹 서비스 다시 빌드
> * 4단계: 클라이언트 앱 통합
> * 5단계: Studio(클래식) 자산 정리
> * 6단계: 시나리오 검토 및 확장


## <a name="step-1-assess-azure-machine-learning"></a>1단계: Azure Machine Learning 평가
1. [Azure Machine Learning](https://azure.microsoft.com/services/machine-learning/)의 이점, 비용, 아키텍처에 대해 알아봅니다.

1. Azure Machine Learning 및 Studio(클래식)의 기능을 비교합니다.

    >[!NOTE]
    > Azure Machine Learning의 **디자이너** 기능은 Studio(클래식)와 비슷한 끌어서 놓기 환경을 제공합니다. 하지만 Azure Machine Learning은 대안으로 강력한 [코드 우선 워크플로](concept-model-management-and-deployment.md)도 제공합니다. 이 마이그레이션 시리즈는 Studio(클래식) 환경과 가장 유사하므로 디자이너에 중점을 둡니다.

    [!INCLUDE [aml-compare-classic](../../includes/machine-learning-compare-classic-aml.md)]

3. Azure Machine Learning 디자이너에서 중요한 Studio(클래식) 모듈이 지원되는지 확인합니다. 자세한 내용은 아래의 [Studio(클래식) 및 디자이너 모듈 매핑](#studio-classic-and-designer-module-mapping) 표를 참조하세요.

4. [Azure Machine Learning 작업 영역을 만듭니다](how-to-manage-workspace.md?tabs=azure-portal).

## <a name="step-2-define-a-strategy-and-plan"></a>2단계: 전략 및 계획 정의

1. 비즈니스 타당성 및 예상 결과를 정의합니다.
1. 비즈니스 결과에 맞게 실행 가능한 Azure Machine Learning 채택 계획을 수립합니다.
1. 변경에 대한 사용자, 프로세스, 환경을 준비합니다.

계획 문서 템플릿 등의 계획 리소스를 보려면 [Azure Machine Learning 채택 프레임워크](https://aka.ms/mlstudio-classic-migration-repo)를 참조하세요. 

## <a name="step-3-rebuild-your-first-model"></a>3단계: 첫 번째 모델 다시 빌드

전략을 정의한 후에는 첫 번째 모델을 마이그레이션합니다.

1. [데이터 세트를 Azure Machine Learning으로 마이그레이션](migrate-register-dataset.md)합니다.
1. 디자이너를 사용하여 [실험을 다시 빌드](migrate-rebuild-experiment.md)합니다.
1. 디자이너를 사용하여 [웹 서비스를 다시 배포](migrate-rebuild-web-service.md)합니다.

    >[!NOTE]
    > Azure Machine Learning은 [데이터 세트](how-to-create-register-datasets.md), [학습](how-to-set-up-training-targets.md), [배포](how-to-deploy-and-where.md)에 대한 코드 우선 워크플로도 지원합니다.

## <a name="step-4-integrate-client-apps"></a>4단계: 클라이언트 앱 통합

1. 새 [Azure Machine Learning 엔드포인트](migrate-rebuild-integrate-with-client-app.md)를 사용하도록 Studio(클래식) 웹 서비스를 호출하는 클라이언트 애플리케이션을 수정합니다.

## <a name="step-5-cleanup-studio-classic-assets"></a>5단계: Studio(클래식) 자산 정리

1. 추가 요금을 방지하기 위해 [Studio(클래식) 자산을 정리](/classic/export-delete-personal-data-dsr.md)합니다. Azure Machine Learning 워크로드의 유효성을 검사할 때까지 대체 옵션으로 자산을 유지할 수 있습니다.

## <a name="step-6-review-and-expand-scenarios"></a>6단계: 시나리오 검토 및 확장

1. 모델 마이그레이션에서 모범 사례를 검토하고 워크로드의 유효성을 검사합니다.
1. 시나리오를 확장하고 추가 작업을 Azure Machine Learning으로 마이그레이션합니다.


## <a name="studio-classic-and-designer-module-mapping"></a>Studio(클래식) 및 디자이너 모듈 매핑

디자이너에서 Studio(클래식) 실험을 다시 빌드하는 동안 사용할 모듈을 확인하려면 다음 표를 참조하세요.


> [!IMPORTANT]
> 디자이너는 C# 패키지가 아닌 Studio(클래식)와 같은 오픈 소스 Python 패키지를 통해 모듈을 구현합니다. 이러한 차이로 인해 디자이너 모듈의 출력은 해당 Studio(클래식)와 약간 다를 수 있습니다.


|범주|Studio(클래식) 모듈|대체 디자이너 모듈|
|--------------|----------------|--------------------------------------|
|데이터 입력 및 출력|- 수동으로 데이터 입력 </br> - 데이터 내보내기 </br> - 데이터 가져오기 </br> - 학습된 모델 로드 </br> - 압축된 데이터 세트 압축 풀기|- 수동으로 데이터 입력 </br> - 데이터 내보내기 </br> - 데이터 가져오기|
|데이터 형식 변환|- CSV로 변환 </br> - 데이터 세트로 변환 </br> - ARFF로 변환 </br> - SVMLight로 변환 </br> - TSV로 변환|- CSV로 변환 </br> - 데이터 세트로 변환|
|데이터 변환 - 조작|- 열 추가</br> - 행 추가 </br> - SQL 변환 적용 </br> - 누락 데이터 정리 </br> - 표시기 값으로 변환 </br> - 메타데이터 편집 </br> - 데이터 조인 </br> - 중복 행 제거 </br> - 데이터 세트의 열 선택 </br> - 열 변환 선택 </br> - SMOTE </br> - 범주 값 그룹화|- 열 추가</br> - 행 추가 </br> - SQL 변환 적용 </br> - 누락 데이터 정리 </br> - 표시기 값으로 변환 </br> - 메타데이터 편집 </br> - 데이터 조인 </br> - 중복 행 제거 </br> - 데이터 세트의 열 선택 </br> - 열 변환 선택 </br> - SMOTE|
|데이터 변환 - 확장 및 축소 |- 값 잘라내기 </br> - Bin으로 데이터 그룹화 </br> - 데이터 정규화 </br>- 주 구성 요소 분석 |- 값 잘라내기 </br> - Bin으로 데이터 그룹화 </br> - 데이터 정규화|
|데이터 변환 - 샘플링 및 분할|- 파티션 및 샘플 </br> - 데이터 분할|- 파티션 및 샘플 </br> - 데이터 분할|
|데이터 변환 - 필터 |- 필터 적용 </br> - FIR 필터 </br> - IIR 필터 </br> - 중앙값 필터 </br> - 이동 평균 필터 </br> - 임계값 필터 </br> - 사용자 정의 필터||
|데이터 변환 - 개수로 학습 |- 개수 변환 작성 </br> - 개수 테이블 내보내기 </br> - 개수 테이블 가져오기 </br> - 개수 변환 병합</br>  - 개수 테이블 매개 변수 수정||
|기능 선택 |- 필터 기반 기능 선택 </br> - 피셔 선형 판별 분석  </br> - 순열 기능 중요도 |- 필터 기반 기능 선택 </br>  - 순열 기능 중요도|
| 모델 - 분류| - 다중 클래스 의사 결정 포리스트 </br> - 다중 클래스 의사 결정 정글  </br> - 다중 클래스 로지스틱 회귀  </br>- 다중 클래스 인공신경망  </br>- 일대다 다중 클래스 </br>- 2클래스 평균 퍼셉트론 </br>- 2클래스 Bayes 지점 머신 </br>- 2클래스 향상된 의사 결정 트리  </br> - 2클래스 의사 결정 포리스트  </br> - 2클래스 의사 결정 정글  </br> - 이중 클래스 로컬 심층 SVM </br> - 2클래스 로지스틱 회귀  </br> - 2클래스 인공신경망 </br> - 2클래스 Support Vector Machine  | - 다중 클래스 의사 결정 포리스트 </br>  - 다중 클래스 향상된 의사 결정 트리  </br> - 다중 클래스 로지스틱 회귀 </br> - 다중 클래스 인공신경망 </br> - 일대다 다중 클래스  </br> - 2클래스 평균 퍼셉트론  </br> - 2클래스 향상된 의사 결정 트리  </br> - 2클래스 의사 결정 포리스트 </br>- 2클래스 로지스틱 회귀 </br> - 2클래스 인공신경망 </br>- 2클래스 Support Vector Machine  |
| 모델 - 클러스터링| - K-평균 클러스터링| - K-평균 클러스터링|
| 모델 - 회귀| - Bayes 선형 회귀  </br> - 향상된 의사 결정 트리 회귀  </br>- 의사 결정 포리스트 회귀  </br> - 빠른 포리스트 분위수 회귀  </br> - 선형 회귀 </br> - 인공신경망 회귀 </br> - 서수 회귀  포아송 회귀| - 향상된 의사 결정 트리 회귀  </br>- 의사 결정 포리스트 회귀  </br> - 빠른 포리스트 분위수 회귀 </br> - 선형 회귀  </br> - 인공신경망 회귀 </br> - 포아송 회귀|
| 모델 - 변칙 검색| - 1클래스 SVM  </br> - PCA 기반 변칙 검색 | - PCA 기반 변칙 검색|
| Machine Learning - 평가  | - 모델 교차 유효성 검사  </br>- 모델 평가  </br>- 추천 평가 | - 모델 교차 유효성 검사  </br>- 모델 평가 </br> - 추천 평가|
| Machine Learning - 학습| - 스윕 클러스터링  </br> - 변칙 검색 모델 학습 </br>- 클러스터링 모델 학습  </br> - Matchbox 추천 학습  -</br> 모델 학습  </br>- 모델 하이퍼 매개 변수 튜닝| - 변칙 검색 모델 학습  </br> - 클러스터링 모델 학습 </br> -  모델 학습  -</br> - PyTorch 모델 학습  </br>- SVD 추천 학습  </br>- 와이드 및 딥 추천 학습 </br>- 모델 하이퍼 매개 변수 튜닝|
| Machine Learning - 점수| - 변환 적용  </br>- 클러스터에 데이터 할당  </br>- Matchbox 추천 점수 매기기 </br> - 모델 채점|- 변환 적용  </br> - 클러스터에 데이터 할당 </br> - 이미지 모델 채점  </br> - 모델 채점 </br>- SVD 추천 점수 매기기 </br> \- 와이드 및 딥 추천 점수 매기기|
| OpenCV 라이브러리 모듈| - 이미지 가져오기 </br>- 미리 학습된 연속 이미지 분류 | |
| Python 언어 모듈| - Python 스크립트 실행| - Python 스크립트 실행  </br> - Python 모델 만들기 |
| R 언어 모듈  | - R 스크립트 실행  </br> - R 모델 만들기| - R 스크립트 실행|
| 통계 함수 | - 수학 연산 적용 </br>- 기본 통계 컴퓨팅  </br>- 선형 상관 관계 컴퓨팅  </br>- 확률 함수 평가  </br>- 불연속 값 바꾸기  </br>- 데이터 요약  </br>- t-테스트를 사용하여 가설 테스트| - 수학 연산 적용  </br>- 데이터 요약|
| 텍스트 분석| - 언어 감지  </br>- 텍스트에서 핵심 구 추출  </br>- 텍스트에서 N Gram 기능 추출  </br>- 기능 해싱 </br>- 잠재적 Dirichlet 할당  </br>- 명명된 엔터티 인식 </br>- 텍스트 전처리  </br>- Vowpal Wabbit 버전 7-10 모델 점수 매기기  </br>- Vowpal Wabbit 버전 8 모델 점수 매기기 </br>- Vowpal Wabbit 버전 7-10 모델 학습  </br>- Vowpal Wabbit 버전 8 모델 학습 |- 단어를 벡터로 변환 </br> - 텍스트에서 N Gram 기능 추출 </br>- 기능 해싱  </br>- 잠재적 Dirichlet 할당 </br>- 텍스트 전처리  </br>- Vowpal Wabbit 모델 점수 매기기 </br> - Vowpal Wabbit 모델 학습|
| 시계열| - 시계열 변칙 검색 | |
| 웹 서비스 | - 입력 </br> - 출력 | - 입력 </br>  - 출력|
| Computer Vision| | - 이미지 변환 적용 </br> - 이미지 디렉터리로 변환 </br> - 이미지 변환 시작 </br> - 이미지 디렉터리 분할  </br> - DenseNet 이미지 분류   </br>- ResNet 이미지 분류 |

개별 디자이너 모듈을 사용하는 방법에 대한 자세한 내용은 [디자이너 모듈 참조](algorithm-module-reference/module-reference.md)를 참조하세요.

### <a name="what-if-a-designer-module-is-missing"></a>디자이너 모듈이 없는 경우 어떻게 되나요?

Azure Machine Learning 디자이너에는 Studio(클래식)에서 가장 인기 있는 모듈이 포함되어 있습니다. 또한 최신 기계 학습 기술을 활용하는 새로운 모듈도 포함되어 있습니다. 

디자이너에 누락된 모듈이 있어 마이그레이션이 차단된 경우 [지원 티켓을 만들어](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) 문의해 주세요.

## <a name="example-migration"></a>마이그레이션 예

다음 마이그레이션 예는 Studio(클래식)와 Azure Machine Learning 간의 차이점 중 일부를 강조 표시합니다.

### <a name="datasets"></a>데이터 세트

Studio(클래식)에서 **데이터 세트** 가 작업 영역에 저장되고 Studio(클래식)에서만 사용할 수 있었습니다.

![automobile-price-classic-dataset](./media/migrate-overview/studio-classic-dataset.png)

Azure Machine Learning에서는 **데이터 세트** 가 작업 영역에 등록되며 모든 Azure Machine Learning에서 사용할 수 있습니다. Azure Machine Learning 데이터 세트의 이점에 대한 자세한 내용은 [보안 데이터 액세스](concept-data.md#reference-data-in-storage-with-datasets)를 참조하세요.

![automobile-price-aml-dataset](./media/migrate-overview/aml-dataset.png)

### <a name="pipeline"></a>파이프라인

Studio(클래식)에서는 **실험** 에 작업에 대한 처리 논리가 포함되었습니다. 끌어서 놓기 모듈을 사용하여 실험을 만들었습니다.

![automobile-price-classic-experiment](./media/migrate-overview/studio-classic-experiment.png)

Azure Machine Learning에서는 **파이프라인** 에 작업에 대한 처리 논리가 포함됩니다. 끌어서 놓기 모듈이나 코드 작성으로 파이프라인을 만들 수 있습니다.

![automobile-price-aml-pipeline](./media/migrate-overview/aml-pipeline.png)

### <a name="web-service-endpoint"></a>웹 서비스 엔드포인트

Studio(클래식)는 실시간 예측에 **REQUEST/RESPOND API** 를 사용하고 일괄 처리 예측 또는 재학습에 **BATCH EXECUTION API** 를 사용했습니다.

![automobile-price-classic-webservice](./media/migrate-overview/studio-classic-web-service.png)

Azure Machine Learning은 실시간 예측에 실시간 **엔드포인트** 를 사용하고 일괄 처리 예측 또는 재학습에 **파이프라인 엔드포인트** 를 사용합니다.

![automobile-price-aml-endpoint](./media/migrate-overview/aml-endpoint.png)

## <a name="next-steps"></a>다음 단계

이 문서에서는 Azure Machine Learning으로 마이그레이션하기 위한 개략적인 요구 사항을 알아보았습니다. 자세한 단계는 다음과 같은 Studio(클래식) 마이그레이션 시리즈의 다른 문서를 참조하세요.

1. **마이그레이션 개요**
1. [마이그레이션 데이터세트](migrate-register-dataset.md)
1. [Studio(클래식) 학습 파이프라인을 다시 빌드](migrate-rebuild-experiment.md)합니다.
1. [Studio(클래식) 웹 서비스를 다시 빌드](migrate-rebuild-web-service.md)합니다.
1. [Azure Machine Learning 웹 서비스를 클라이언트 앱과 통합](migrate-rebuild-integrate-with-client-app.md)합니다.
1. [R 스크립트 실행을 마이그레이션](migrate-execute-r-script.md)합니다.

추가 마이그레이션 리소스에 대해서는 [Azure Machine Learning 채택 프레임워크](https://aka.ms/mlstudio-classic-migration-repo)를 참조하세요.