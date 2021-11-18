---
title: Studio UI를 사용하여 AutoML 설정
titleSuffix: Azure Machine Learning
description: Azure Machine Learning 스튜디오에서 Azure Machine Learning 자동화 ML을 사용하여 코드 줄 없이도 AutoML 학습 실행을 설정하는 방법을 알아봅니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: automl
ms.author: nibaccam
author: cartacioS
ms.reviewer: nibaccam
ms.date: 11/15/2021
ms.topic: how-to
ms.custom: automl, FY21Q4-aml-seo-hack, contperf-fy21q4
ms.openlocfilehash: 145a87800ee1f6f72e629f9e8e6ceace06889316
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/17/2021
ms.locfileid: "132717864"
---
# <a name="set-up-no-code-automl-training-with-the-studio-ui"></a>스튜디오 UI를 사용하여 코드 없는 AutoML 학습 설정 

이 문서에서는 [Azure Machine Learning 스튜디오](overview-what-is-machine-learning-studio.md)에서 Azure Machine Learning 자동화 ML을 사용하여 코드 줄 없이도 AutoML 학습 실행을 설정하는 방법을 알아봅니다.

AutoML(자동화된 Machine Learning)은 특정 데이터에 사용할 가장 적합한 기계 학습 알고리즘을 선택하는 프로세스입니다. 이 프로세스를 통해 기계 학습 모델을 빠르게 생성할 수 있습니다. [Azure Machine Learning이 자동화된 Machine Learning을 구현하는 방법을 자세히 알아보세요](concept-automated-ml.md).
 
종단 간 예제를 보려면 [자습서: AutoML-코드 없이 분류 모델 학습](tutorial-first-experiment-automated-ml.md)을 사용해보세요. 

Python 코드 기반 환경의 경우 Azure Machine Learning SDK를 사용하여 [자동화된 Machine Learning 실험을 구성](how-to-configure-auto-train.md)하세요.

## <a name="prerequisites"></a>사전 요구 사항

* Azure 구독 Azure 구독이 없는 경우 시작하기 전에 체험 계정을 만듭니다. 지금 [Azure Machine Learning 평가판 또는 유료 버전](https://azure.microsoft.com/free/)을 사용해 보세요.

* Azure Machine Learning 작업 영역 [Azure Machine Learning 작업 영역 만들기](how-to-manage-workspace.md)를 참조하세요. 

## <a name="get-started"></a>시작

1. [Azure Machine Learning Studio](https://ml.azure.com)에 로그인합니다. 

1. 구독 및 작업 영역을 선택합니다. 

1. 왼쪽 창으로 이동합니다. **작성** 섹션 아래에서 **자동화된 ML** 을 선택합니다.

[![Azure Machine Learning Studio 탐색 창](media/how-to-use-automated-ml-for-ml-models/nav-pane.png)](media/how-to-use-automated-ml-for-ml-models/nav-pane-expanded.png)

 실험을 처음 수행하는 경우 자동화된 빈 목록과 설명서에 대한 링크가 표시됩니다. 

그렇지 않으면 SDK로 생성된 실험을 포함하여 최근에 자동화된 ML 실험 목록이 표시됩니다. 

## <a name="create-and-run-experiment"></a>실험 만들기 및 실행

1. **+ 새 자동화된 ML 실행** 을 선택하고 양식을 채웁니다.

1. 스토리지 컨테이너에서 데이터 세트를 선택하거나 새 데이터 세트를 만듭니다. 데이터 세트는 로컬 파일, 웹 URL, 데이터 저장소 또는 Azure 오픈 데이터 세트에서 만들 수 있습니다. [데이터 세트 생성](how-to-create-register-datasets.md)에 대해 자세히 알아보세요.  

    >[!Important]
    > 학습 데이터 요구 사항:
    >* 데이터는 테이블 형식이어야 합니다.
    >* 예측하려는 값(대상 열)이 데이터에 있어야 합니다.

    1. 로컬 컴퓨터의 파일에서 새 데이터 세트를 만들려면 **+데이터 세트 생성** 을 선택한 다음 **로컬 파일에서** 를 선택합니다. 

    1. **기본 정보** 양식에서 데이터 세트에 고유한 이름을 지정하고 선택적 설명을 입력합니다. 

    1. **다음** 을 선택하여 **데이터 저장소 및 파일 선택 양식** 을 엽니다. 이 양식에서 데이터 세트를 업로드할 위치 또는 작업 영역에서 자동으로 만들어지는 기본 스토리지 컨테이너를 선택하거나 실험에 사용하려는 스토리지 컨테이너를 선택합니다. 
    
        1. 데이터가 가상 네트워크 뒤에 있는 경우 **유효성 검사 건너뛰기** 기능을 사용하도록 설정하여 작업 영역이 데이터에 액세스할 수 있도록 해야 합니다. 자세한 내용은 [Azure 가상 네트워크에서 Azure Machine Learning 스튜디오 사용](how-to-enable-studio-virtual-network.md)을 참조하세요. 
    
    1. 데이터 세트에 대한 데이터 파일을 업로드하려면 **찾아보기** 를 선택합니다. 

    1. **설정 및 미리 보기** 양식을 검토하여 정확히 지정했는지 확인합니다. 양식은 파일 형식에 따라 지능적으로 채워집니다. 

        필드| Description
        ----|----
        파일 형식| 파일에 저장된 데이터의 레이아웃 및 유형을 정의합니다.
        구분 기호| 일반 텍스트 또는 다른 데이터 스트림에서 별도의 독립적인 지역 간의 경계를 지정하기 위한 하나 이상의 문자입니다.
        Encoding| 데이터 세트를 읽는 데 사용할 문자 스키마 테이블을 식별합니다.
        열 머리글| 데이터 세트의 헤더(있는 경우)가 처리되는 방법을 나타냅니다.
        행 건너뛰기 | 데이터 세트에서 건너뛴 행(있는 경우)의 수를 나타냅니다.
    
        **다음** 을 선택합니다.

    1. **스키마** 양식은 **설정 및 미리 보기** 양식의 선택 사항에 따라 지능적으로 채워집니다. 여기서는 각 열의 데이터 형식을 구성하고, 열 이름을 검토하고, 실험에 **포함하지 않을** 열을 선택합니다. 
            
        **다음** 을 선택합니다.

    1. **세부 정보 확인** 양식은 이전에 **기본 정보** 및 **설정 및 미리 보기** 양식에 채운 정보를 요약한 것입니다. 프로파일링을 사용하도록 설정된 컴퓨팅을 사용하여 데이터 세트에 대한 데이터 프로필을 만드는 옵션도 있습니다. [데이터 프로파일링](how-to-connect-data-ui.md#profile)에 대한 자세한 정보

        **다음** 을 선택합니다.
1. 새로 만든 데이터 세트가 표시되면 선택합니다. 또한 데이터 세트 및 샘플 통계 미리 보기도 볼 수 있습니다. 

1. **실행 구성** 양식에서 **새로 만들기** 를 선택하고 실험 이름에 **Tutorial-automl-deploy** 를 입력합니다.

1. 대상 열을 선택합니다. 이 열은 예측을 수행하려는 열입니다.

1. 데이터 프로 파일링 및 학습 작업에 대 한 계산 형식을 선택 합니다. [계산 클러스터](concept-compute-target.md#azure-machine-learning-compute-managed) 또는 [계산 인스턴스](concept-compute-instance.md)를 선택할 수 있습니다. 
    
1. 기존 계산의 드롭다운 목록에서 계산을 선택 합니다.  새 계산을 만들려면 8 단계의 지침을 따르세요.

1. **새 컴퓨팅 만들기** 를 선택하여 이 실험에 대한 컴퓨팅 컨텍스트를 구성합니다.

    필드|Description
    ---|---
    컴퓨팅 이름| 컴퓨팅 컨텍스트를 식별하는 고유한 이름을 입력합니다.
    가상 머신 우선 순위| 우선 순위가 낮은 가상 머신은 더 저렴하지만 컴퓨팅 노드를 보장하지는 않습니다. 
    가상 머신 유형| 가상 머신 형식에 대해 CPU 또는 GPU를 선택합니다.
    가상 머신 크기| 컴퓨팅에 사용할 가상 머신 크기를 선택합니다.
    최소 / 최대 노드| 데이터를 프로파일링하려면 하나 이상의 노드를 지정해야 합니다. 컴퓨팅에 사용할 최대 노드 수를 입력합니다. AML 컴퓨팅의 경우 기본값은 6개 노드입니다.
    고급 설정 | 해당 설정을 사용하면 실험에 사용할 사용자 계정과 기존 가상 네트워크를 구성할 수 있습니다. 
    
    **만들기** 를 선택합니다. 새 컴퓨팅을 만드는 데 몇 분 정도 걸릴 수 있습니다.

    >[!NOTE]
    > 컴퓨팅 이름에는 선택하고 만드는 컴퓨팅에서 *프로파일링을 사용하도록 설정* 했는지 여부가 표시됩니다. (자세한 내용은 [데이터 프로파일링](how-to-connect-data-ui.md#profile) 섹션을 참조하세요.)

    **다음** 을 선택합니다.

1. **작업 유형 및 설정** 양식에서 작업 유형(분류, 회귀 또는 예측)을 선택합니다. 자세한 내용은 [지원되는 작업 형식](concept-automated-ml.md#when-to-use-automl-classification-regression-forecasting--computer-vision)을 참조하세요.

    1. **분류** 의 경우 딥 러닝을 사용할 수도 있습니다.
    
        딥 러닝을 사용하면 유효성 검사가 _train_validation split_ 으로 제한됩니다. [유효성 검사 옵션](how-to-configure-cross-validation-data-splits.md)에 대해 자세히 알아보세요.

    1. **예측** 의 경우 다음을 수행할 수 있습니다. 
    
        1. 딥 러닝 사용.
    
        1. ‘시간 열’ 선택: 이 열에는 사용될 시간 데이터가 포함됩니다.

        1. ‘예측 구간’ 선택: 모델이 얼마나 많은 미래의 시간 단위(분/시간/일/주/월/년)를 예측할 수 있는지 표시합니다. 나중에 예측 하기 위해 모델을 추가 해야 하며,이는 더 정확 하지 않게 됩니다. [예측 및 예측 구간에 대해 자세히 알아보세요](how-to-auto-train-forecast.md).

1. (선택 사항) 추가 구성 설정 보기: 학습 작업을 더 효율적으로 제어하는 데 사용할 수 있는 추가 설정입니다. 그렇지 않으면 실험 선택 및 데이터를 기반으로 기본값이 적용됩니다. 

    추가 구성|Description
    ------|------
    기본 메트릭| 모델의 점수를 매기는 데 사용되는 기본 메트릭입니다. [모델 메트릭에 대해 자세히 알아보세요](how-to-configure-auto-train.md#primary-metric).
    최상의 모델에 대한 설명 | 권장되는 최상의 모델에 대한 설명을 표시하려면 사용 또는 사용하지 않음을 선택합니다. <br> 이 기능은 현재 [특정 예측 알고리즘](how-to-machine-learning-interpretability-automl.md#interpretability-during-training-for-the-best-model)에서 사용할 수 없습니다. 
    차단된 알고리즘| 학습 작업에서 제외하려는 알고리즘을 선택합니다. <br><br> 알고리즘 허용은 [SDK 실험](how-to-configure-auto-train.md#supported-models)을 위해서만 가능합니다. <br> [각 작업 형식에 지원되는 모델](/python/api/azureml-automl-core/azureml.automl.core.shared.constants.supportedmodels)을 참조하세요.
    종료 조건| 다음 조건 중 하나가 충족되면 학습 작업이 중지됩니다. <br> *학습 작업 시간(시간)* : 학습 작업을 실행할 수 있는 기간입니다. <br> *메트릭 점수 임계값*:  모든 파이프라인에 대한 최소 메트릭 점수입니다. 이렇게 하면 도달하려는 목표 메트릭이 정의되어 있는 경우 학습 작업에 필요한 시간보다 더 많은 시간을 소비하지 않습니다.
    동시성| *최대 동시 반복 횟수*: 학습 작업에서 테스트할 최대 파이프라인(반복) 수입니다. 작업이 지정된 반복 횟수를 초과하여 실행되지 않습니다. 자동화된 ML이 [클러스터에서 여러 자식 요소 실행](how-to-configure-auto-train.md#multiple-child-runs-on-clusters)을 수행하는 방법을 자세히 알아보세요.

1. (선택사항) 기능화 설정 보기: **추가 구성 설정** 양식에서 **자동 기능화** 를 사용하도록 선택하면 기본 기능화 기술이 적용됩니다. **기능화 설정 보기** 에서 기본값을 변경하고 적절하게 사용자 지정할 수 있습니다. [기능화를 사용자 지정](#customize-featurization)하는 방법을 알아보세요. 

    ![기능화 설정이 호출되어 있는 작업 형식 대화 상자 선택을 보여 주는 스크린샷.](media/how-to-use-automated-ml-for-ml-models/view-featurization-settings.png)


1. **[선택 사항] 유효성 검사 및 테스트** 양식을 사용하여 다음을 수행할 수 있습니다. 

    1. 학습 작업에 사용할 유효성 검사 유형을 지정합니다. [교차 유효성 검사에 대해 자세히 알아보세요](how-to-configure-cross-validation-data-splits.md#prerequisites). 
    
        1. 예측 작업은 k-폴드 교차 유효성 검사만 지원합니다.
    
    1. 테스트 데이터 세트(미리 보기)를 제공하여 실험이 끝날 때 자동화된 ML 생성하는 권장 모델을 평가합니다. 테스트 데이터를 제공하면 실험이 끝날 때 테스트 실행이 자동으로 트리거됩니다. 이 테스트 실행은 자동화된 ML 권장한 최상의 모델에서만 실행됩니다. [원격 테스트 실행의 결과를](#view-remote-test-run-results-preview)얻는 방법을 알아봅니다.
    
        >[!IMPORTANT]
        > 생성된 모델을 평가하기 위한 테스트 데이터 세트를 제공하는 것은 미리 보기 기능입니다. 이 기능은 [실험적인](/python/api/overview/azure/ml/#stable-vs-experimental) 미리 보기 기능으로, 언제든지 변경할 수 있습니다.
        
        * 테스트 데이터는 권장 모델의 테스트 실행 결과를 바이어스하지 않도록 학습 및 유효성 검사와는 별도로 간주됩니다. [모델 유효성 검사 중 바이어스 에 대해 자세히 알아봅니다.](concept-automated-ml.md#training-validation-and-test-data)
        * 고유한 테스트 데이터 세트를 제공하거나 학습 데이터 세트의 백분율을 사용하도록 선택할 수 있습니다. 테스트 데이터는 [Azure Machine Learning TabularDataset](how-to-create-register-datasets.md#tabulardataset)형식이어야 합니다.         
        * 테스트 데이터 세트의 스키마는 학습 데이터 세트와 일치해야 합니다. 대상 열은 선택 사항이지만 대상 열이 표시되지 않으면 테스트 메트릭이 계산되지 않습니다.
        * 테스트 데이터 세트는 학습 데이터 세트 또는 유효성 검사 데이터 세트와 같아서는 안 됩니다.
        * 예측 실행은 학습/테스트 분할을 지원하지 않습니다.
        
        ![스크린샷은 유효성 검사 데이터 및 테스트 데이터를 선택할 수 있는 양식을 보여줍니다.](media/how-to-use-automated-ml-for-ml-models/validate-test-form.png)
        
## <a name="customize-featurization"></a>기능화 사용자 지정

**기능화** 양식에서 자동 기능화를 사용/사용하지 않을 수 있으며, 실험에 대한 자동 기능화 설정을 사용자 지정할 수 있습니다. 이 양식을 열려면 [실험 만들기 및 실행](#create-and-run-experiment) 섹션에 나와 있는 10단계를 참조하세요. 

다음 테이블은 현재 스튜디오를 통해 사용할 수 있는 사용자 지정을 요약해 보여 줍니다. 

열| 사용자 지정
---|---
Included | 학습에 포함할 열을 지정합니다.
기능 유형| 선택된 열에 대한 값 형식을 변경합니다.
다음으로 대체| 데이터에서 누락된 값을 어떤 값으로 대체할지 선택합니다.

![Azure Machine Learning 스튜디오 사용자 지정 기능화](media/how-to-use-automated-ml-for-ml-models/custom-featurization.png)

## <a name="run-experiment-and-view-results"></a>실험 실행 및 결과 보기

**마침** 을 선택하여 실험을 실행합니다. 실험 준비 프로세스는 최대 10분 정도 걸릴 수 있습니다. 각 파이프라인에서 실행을 완료하는 데 학습 작업에 2-3분 더 걸릴 수 있습니다.

> [!NOTE]
> 자동화된 ML이 채택하는 알고리즘에는 내재된 무작위성이 있어서 권장된 모델의 최종 메트릭 점수(예: 정확도)에 약간의 변동을 일으킬 수 있습니다. 또한 자동화된 ML은 필요한 경우 학습-테스트 분할, 학습-유효성 검사 분할 또는 교차 유효성 검사와 같은 데이터에 대한 작업을 수행합니다. 따라서 동일한 구성 설정과 기본 메트릭을 사용하여 여러 번 실험을 실행한 경우, 해당 요인으로 인해 각 실험의 최종 메트릭 점수에 변동이 있을 가능성이 높습니다. 

### <a name="view-experiment-details"></a>실험 세부 정보 보기

**세부 정보** 탭에 **실행 세부 정보** 화면이 열립니다. 이 화면에서는 실행 번호 옆의 위쪽에 상태 표시줄이 포함된 실험 실행 요약이 표시됩니다. 

**모델** 탭에는 메트릭 점수를 기준으로 정렬하여 만든 모델 목록이 있습니다. 기본적으로 선택한 메트릭에 따라 가장 높은 점수를 획득한 모델이 목록 맨 위에 표시됩니다. 학습 작업에서 더 많은 모델을 시도하면 모델이 목록에 추가됩니다. 이 방법을 사용하여 지금까지 생성된 모델에 대한 메트릭을 신속하게 비교할 수 있습니다.

![실행 세부 정보](./media/how-to-use-automated-ml-for-ml-models/explore-models.gif)

### <a name="view-training-run-details"></a>학습 실행 세부 정보 보기

학습 실행 세부 정보를 보려면 완료된 모델을 드릴다운합니다. **모델** 탭에서 모델 요약 및 선택한 모델에 사용된 하이퍼 매개 변수 등의 세부 정보를 봅니다. 

[![하이퍼 매개 변수 세부 정보](media/how-to-use-automated-ml-for-ml-models/hyperparameter-button.png)](media/how-to-use-automated-ml-for-ml-models/hyperparameter-details.png)

 **메트릭** 탭에서 모델 특정 성능 메트릭 차트도 볼 수 있습니다. [차트에 대해 자세히 알아보세요](how-to-understand-automated-ml.md).

![반복 세부 정보](media/how-to-use-automated-ml-for-ml-models/iteration-details-expanded.png)

데이터 변환 탭에서 이 모델을 생성하기 위해 적용된 데이터 전처리, 기능 엔지니어링, 크기 조정 기술 및 기계 학습 알고리즘에 대한 다이어그램을 볼 수 있습니다.

>[!IMPORTANT]
> 데이터 변환 탭은 미리 보기로 제공됩니다. 이 기능은 [실험적](/python/api/overview/azure/ml/#stable-vs-experimental)으로 간주되어야 하며 언제든지 변경할 수 있습니다.

![데이터 변환](./media/how-to-use-automated-ml-for-ml-models/data-transformation.png)

## <a name="view-remote-test-run-results-preview"></a>원격 테스트 실행 결과 보기(미리 보기)

테스트 데이터 세트를 지정하거나 실험 설정 중에 학습/테스트 분할을 선택한 경우 **유효성 검사 및 테스트** 양식에서 자동화된 ML 기본적으로 권장 모델을 자동으로 테스트합니다. 결과적으로 자동화된 ML 테스트 메트릭을 계산하여 권장 모델 및 해당 예측의 품질을 결정합니다. 

>[!IMPORTANT]
> 테스트 데이터 세트를 사용하여 모델을 테스트하여 생성된 모델을 평가하는 것은 미리 보기 기능입니다. 이 기능은 [실험적인](/python/api/overview/azure/ml/#stable-vs-experimental) 미리 보기 기능으로, 언제든지 변경할 수 있습니다.

> [!WARNING]
> 이 기능은 다음과 같은 자동화된 ML 시나리오에 사용할 수 없습니다.
>  * [Computer Vision 작업(미리 보기)](how-to-auto-train-image-models.md)
>  * [많은 모델 및 계층적 Time Series 예측 학습(미리 보기)](how-to-auto-train-forecast.md)
>  * [DNN(딥 러닝 신경망)이 사용되는 예측 작업](how-to-auto-train-forecast.md#enable-deep-learning)
>  * [로컬 컴퓨팅 또는 Azure Databricks 클러스터에서 자동화된 ML 실행](how-to-configure-auto-train.md#compute-to-run-experiment)

권장 모델의 테스트 실행 메트릭을 보려면
 
1. **모델** 페이지로 이동하여 최상의 모델을 선택합니다. 
1. 테스트 **결과(미리 보기) 탭을** 선택합니다. 
1. 원하는 실행을 선택하고 **메트릭 탭을 봅니다.**  ![자동으로 테스트되고 권장되는 모델의 테스트 결과 탭](./media/how-to-use-automated-ml-for-ml-models/test-best-model-results.png)
    
테스트 메트릭을 계산하는 데 사용되는 테스트 예측을 보려면 

1. 페이지 아래쪽으로 이동하고 **출력 데이터 세트** 아래의 링크를 선택하여 데이터 세트를 엽니다. 
1. **데이터 집합** 페이지에서 **탐색** 탭을 선택 하 여 테스트 실행의 예측을 봅니다.
    1. 또는 **출력 + 로그** 탭에서 예측 파일을 보고 다운로드할 수 있으며, **예측** 폴더를 확장 하 여 파일을 찾을 수도 있습니다 `predicted.csv` .

또는 출력 + 로그 탭, 예측 폴더를 차례로 확장 하 여 predictions.csv 파일을 찾을 수도 있습니다.

모델 테스트 실행은 작업 영역을 사용 하 여 만든 기본 데이터 저장소에 저장 된 predictions.csv 파일을 생성 합니다. 이 데이터 저장소는 동일한 구독의 모든 사용자에 게 표시 됩니다. 테스트 실행에 사용 되거나 생성 된 정보 중 하나를 개인 상태로 유지 해야 하는 경우 시나리오에는 테스트 실행을 사용 하지 않는 것이 좋습니다.

## <a name="test-an-existing-automated-ml-model-preview"></a>기존의 자동화 된 ML 모델 테스트 (미리 보기)

>[!IMPORTANT]
> 테스트 데이터 집합을 사용 하 여 모델을 테스트 하 여 생성 된 모델을 평가 하려면 미리 보기 기능을 사용 합니다. 이 기능은 [실험적인](/python/api/overview/azure/ml/#stable-vs-experimental) 미리 보기 기능으로, 언제든지 변경할 수 있습니다.

> [!WARNING]
> 다음 자동화 된 ML 시나리오에서는이 기능을 사용할 수 없습니다.
>  * [컴퓨터 시각 작업 (미리 보기)](how-to-auto-train-image-models.md)
>  * [많은 모델 및 hiearchical 시계열 예측 교육 (미리 보기)](how-to-auto-train-forecast.md)
>  * [심층 학습 신경망 (DNN)을 사용할 수 있는 예측 작업](how-to-auto-train-forecast.md#enable-deep-learning)
>  * [로컬 계산 또는 Azure Databricks 클러스터에서 자동화 된 ML 실행](how-to-configure-auto-train.md#compute-to-run-experiment)

실험을 완료 한 후 자동화 된 ML 생성 하는 모델을 테스트할 수 있습니다. 권장 되는 모델이 아닌 다른 자동화 된 ML 생성 된 모델을 테스트 하려는 경우 다음 단계를 수행 하 여 수행할 수 있습니다. 

1. 기존의 자동화 된 ML 실험 실행을 선택 합니다.  
1. 실행의 **모델** 탭으로 이동 하 고 테스트 하려는 완료 된 모델을 선택 합니다.
1. 모델 **정보** 페이지에서 **테스트 모델 (미리 보기)** 단추를 선택 하 여 **테스트 모델** 창을 엽니다.
1. **테스트 모델** 창에서 테스트 실행에 사용할 계산 클러스터 및 테스트 데이터 집합을 선택 합니다. 
1. **테스트** 단추를 선택 합니다. 테스트 데이터 집합의 스키마는 학습 데이터 집합과 일치 해야 하지만 **대상 열** 은 선택 사항입니다.
1. 모델 테스트 실행을 성공적으로 만들면 **세부 정보** 페이지에 성공 메시지가 표시 됩니다. **테스트 결과** 탭을 선택 하 여 실행 진행률을 확인 합니다.

1. 테스트 실행의 결과를 보려면 **세부 정보** 페이지를 열고 [원격 테스트 실행의 결과 보기](#view-remote-test-run-results-preview) 섹션에 있는 단계를 따릅니다. 

    ![테스트 모델 폼](./media/how-to-use-automated-ml-for-ml-models/test-model-form.png)
    

## <a name="model-explanations-preview"></a>모델 설명(미리 보기)

모델을 더 잘 이해하려면 모델 설명 대시보드를 사용하여 모델의 예측에 영향을 주는 데이터 기능(원시 또는 엔지니어링)을 확인할 수 있습니다. 

모델 설명 대시보드는 예측 및 설명과 함께 학습된 모델의 전체적인 분석을 제공합니다. 또한 개별 데이터 요소와 개별 기능 중요도를 살펴볼 수 있습니다. [설명 대시보드 시각화에 대해 자세히 알아보세요](how-to-machine-learning-interpretability-aml.md#visualizations).

특정 모델에 대한 설명을 보려면 다음을 수행합니다. 

1. **모델** 탭에서 살펴볼 모델을 선택합니다. 
1. **모델 설명** 단추를 선택하고 설명을 생성하는 데 사용할 수 있는 컴퓨팅을 제공합니다.
1. **자식 실행** 탭에서 상태를 확인합니다. 
1. 완료되면 설명 대시보드가 포함된 **설명(미리 보기)** 탭으로 이동합니다. 

    ![모델 설명 대시보드](media/how-to-use-automated-ml-for-ml-models/model-explanation-dashboard.png)

## <a name="deploy-your-model"></a>모델 배포

최상의 모델이 있으면 이제 새 데이터를 예측하기 위해 이 모델을 웹 서비스로 배포합니다.

>[!TIP]
> Python SDK를 사용한 `automl` 패키지를 통해 생성된 모델을 배포하려면 작업 영역에 [모델을 등록](how-to-deploy-and-where.md?tabs=python#register-a-model-from-an-azure-ml-training-run-1)해야 합니다. 
>
> 모델을 등록한 후에는 왼쪽 창에서 **모델** 을 선택하여 스튜디오에서 찾을 수 있습니다. 모델을 열면 화면 맨 위에 있는 **배포** 단추를 선택하고 **모델 배포** 섹션의 **2 단계** 에 설명된 지침을 따를 수 있습니다.

자동화된 ML을 사용하면 코드를 작성하지 않고도 모델을 배포할 수 있습니다.

1. 두 가지 배포 옵션은 다음과 같습니다. 

    + 옵션 1: 정의한 메트릭 조건에 따라 최상의 모델을 배포합니다. 
        1. 실험이 완료되면 화면 맨 위에서 **실행 1** 을 선택하여 부모 실행 페이지로 이동합니다. 
        1.  **최상의 모델 요약** 섹션에 나열된 모델을 선택합니다. 
        1. 창의 왼쪽 위에서 **배포** 를 선택합니다. 

    + 옵션 2: 이 실험에서 특정 모델을 반복해 배포하려면 다음을 수행합니다.
        1. **모델** 탭에서 원하는 모델을 선택합니다.
        1. 창의 왼쪽 위에서 **배포** 를 선택합니다.

1. **모델 배포** 창을 채웁니다.

    필드| 값
    ----|----
    속성| 배포에 대한 고유한 이름을 입력합니다.
    Description| 이 배포의 용도를 더 잘 식별할 수 있는 설명을 입력합니다.
    컴퓨팅 형식| 배포할 끝점의 유형 ( [*Azure Kubernetes Service)*](../aks/intro-kubernetes.md) 또는 [*ACI (azure Container Instance)*](../container-instances/container-instances-overview.md)를 선택 합니다.
    컴퓨팅 이름| *AKS에만 적용:* 배포하려는 AKS 클러스터의 이름을 선택합니다.
    인증 사용 | 토큰 기반 또는 키 기반 인증을 허용할지를 선택합니다.
    사용자 지정 배포 자산 사용| 사용자 고유의 채점 스크립트 및 환경 파일을 업로드하려면 이 기능을 사용하도록 설정합니다. 그렇지 않으면 자동화 된 ML 기본적으로 이러한 자산을 제공 합니다. [점수 매기기 스크립트에 대해 자세히 알아보세요](how-to-deploy-and-where.md).

    >[!Important]
    > 파일 이름은 32자 미만이어야 하며 영숫자로 시작하고 끝나야 합니다. 대시, 밑줄, 점 및 영숫자를 포함할 수 있습니다. 공백은 허용되지 않습니다.

    *고급* 메뉴에서는 [데이터 수집](how-to-enable-app-insights.md) 및 리소스 사용률 설정과 같은 기본 배포 기능을 제공합니다. 이러한 기본값을 재정의하려면 이 메뉴에서 해당 작업을 수행합니다.

1. **배포** 를 선택합니다. 배포가 완료되는 데 약 20분 정도 걸릴 수 있습니다.
    배포가 시작되면 **모델 요약** 탭이 나타납니다. **배포 상태** 섹션에서 배포 진행률을 확인하세요. 

이제 예측을 생성하는 운영 웹 서비스가 있습니다! [Power BI의 기본 제공 Azure Machine Learning 지원](/power-bi/connect-data/service-aml-integrate?context=azure%2fmachine-learning%2fcontext%2fml-context)에서 서비스를 쿼리하여 예측을 테스트할 수 있습니다.

## <a name="next-steps"></a>다음 단계

* [웹 서비스를 사용하는 방법을 알아봅니다](how-to-consume-web-service.md).
* [자동화된 Machine Learning 결과를 살펴봅니다](how-to-understand-automated-ml.md).
* Azure Machine Learning 및 [자동화된 Machine Learning에 대해 자세히 알아봅니다](concept-automated-ml.md).
