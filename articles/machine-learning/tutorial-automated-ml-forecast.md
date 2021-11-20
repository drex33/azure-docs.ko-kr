---
title: '자습서: 수요 예측 및 AutoML'
titleSuffix: Azure Machine Learning
description: Azure Machine Learning의 자동화된 기계 학습(자동화된 ML) 인터페이스를 사용하여 코드를 작성하지 않고 수요 예측 모델을 학습 및 배포합니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: automl
ms.topic: tutorial
ms.author: sacartac
ms.reviewer: nibaccam
author: cartacioS
ms.date: 10/21/2021
ms.custom: automl
ms.openlocfilehash: b5b917e76e9dd36451d63934bfbb9d0332f4b571
ms.sourcegitcommit: b00a2d931b0d6f1d4ea5d4127f74fc831fb0bca9
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/20/2021
ms.locfileid: "132863768"
---
# <a name="tutorial-forecast-demand-with-automated-machine-learning"></a>자습서: 자동화된 기계 학습으로 수요 예측

Azure Machine Learning 스튜디오에서 자동화된 기계 학습을 사용하여 한 줄의 코드도 작성하지 않고 [시계열 예측 모델](concept-automated-ml.md#time-series-forecasting)을 만드는 방법에 대해 알아봅니다. 이 모델은 자전거 공유 서비스에 대한 임대 수요를 예측합니다.  

이 자습서에서는 코드를 작성하지 않고 스튜디오 인터페이스를 사용하여 학습을 수행합니다.  다음 작업을 수행하는 방법을 알아봅니다.

> [!div class="checklist"]
> * 데이터 세트를 만들고 로드합니다.
> * 자동화된 ML 실험을 구성하고 실행합니다.
> * 예측 설정을 지정합니다.
> * 실험 결과를 살펴봅니다.
> * 최적의 모델을 배포합니다.

또한 다음과 같은 다른 모델 유형에 대해 자동화된 기계 학습을 시도해 보세요.

* 분류 모델의 코드 없는 예제는 [자습서: Azure Machine Learning에서 자동화된 ML을 사용하여 분류 모델 만들기](tutorial-first-experiment-automated-ml.md)를 참조하세요.
* 회귀 모델의 코드 첫 번째 예제는 [자습서: 자동화된 기계 학습을 사용하여 택시 요금 예측](tutorial-auto-train-models.md)을 참조하세요.

## <a name="prerequisites"></a>사전 요구 사항

* Azure Machine Learning 작업 영역 [Azure Machine Learning 작업 영역 만들기](how-to-manage-workspace.md)를 참조하세요. 

* [bike-no.csv](https://github.com/Azure/azureml-examples/blob/main/python-sdk/tutorials/automl-with-azureml/forecasting-bike-share/bike-no.csv) 데이터 파일 다운로드

## <a name="sign-in-to-the-studio"></a>스튜디오에 로그인

이 자습서에서는 모든 기술 수준의 데이터 과학 전문가용 데이터 과학 시나리오를 수행하기 위한 기계 학습 도구를 포함하는 통합 웹 인터페이스인 Azure Machine Learning Studio에서 자동화된 ML 실험 실행을 만듭니다. 이 Studio는 Internet Explorer 브라우저에서 지원되지 않습니다.

1. [Azure Machine Learning Studio](https://ml.azure.com)에 로그인합니다.

1. 해당 구독과 직접 만든 작업 영역을 선택합니다.

1. **시작** 을 선택합니다.

1. 왼쪽 창의 **작성** 섹션에서 **자동화된 ML** 을 선택합니다.

1. **+새 자동화된 ML 실행** 을 선택합니다. 

## <a name="create-and-load-dataset"></a>데이터 세트 생성 및 로드

실험을 구성하기 전에 데이터 파일을 Azure Machine Learning 데이터 세트 형식으로 작업 영역에 업로드합니다. 이렇게 하면 데이터의 형식이 실험에 맞게 적절히 지정되도록 할 수 있습니다.

1. **데이터 세트 선택** 양식의 **+데이터 세트 만들기** 드롭다운에서 **로컬 파일에서** 를 선택하여 새 데이터 세트를 만듭니다. 

    1. **기본 정보** 양식에서 데이터 세트에 이름을 지정하고 선택적 설명을 입력합니다. Azure Machine Learning Studio의 자동화된 ML은 현재 테이블 형식 데이터 세트만 지원하므로 데이터 세트 형식은 기본적으로 **테이블 형식** 이어야 합니다.
    
    1. 왼쪽 아래에서 **다음** 을 선택합니다.

    1. **데이터 저장소 및 파일 선택** 양식에서 작업 영역을 만드는 동안 자동으로 설정된 기본 데이터 저장소 **workspaceblobstore(Azure Blob Storage)** 를 선택합니다. 데이터 파일을 업로드할 스토리지 위치입니다. 

    1. **찾아보기** 를 선택합니다. 
    
    1. 로컬 컴퓨터에 있는 **bike-no.csv** 파일을 선택합니다. 이는 [구성 요소](https://github.com/Azure/azureml-examples/blob/main/python-sdk/tutorials/automl-with-azureml/forecasting-bike-share/bike-no.csv)로 다운로드한 파일입니다.

    1. **다음** 을 선택합니다.

       업로드가 완료되면, 설정 및 미리 보기 양식이 파일 형식에 따라 미리 채워집니다. 
       
    1. **설정 및 미리 보기** 양식이 다음과 같이 채워졌는지 확인하고 **다음** 을 선택합니다.
        
        필드|Description| 자습서에서 사용하는 값
        ---|---|---
        파일 형식|파일에 저장된 데이터의 레이아웃 및 유형을 정의합니다.| 구분됨
        구분 기호|일반 텍스트 또는 기타 데이터 스트림에서 개별의 독립된 지역 간의&nbsp; 경계를 지정하는 데 사용하는 하나 이상의 문자입니다. |쉼표
        Encoding|데이터 세트를 읽는 데 사용할 문자 스키마 테이블을 식별합니다.| UTF-8
        열 머리글| 데이터 세트의 헤더(있는 경우)가 처리되는 방법을 나타냅니다.| 첫 번째 파일의 헤더 사용
        행 건너뛰기 | 데이터 세트에서 건너뛴 행(있는 경우)의 수를 나타냅니다.| None

    1. **스키마** 폼을 사용하여 이 실험을 위해 데이터를 추가로 구성할 수 있습니다. 
    
        1. 이 예에서는 **casual** 및 **registered** 열을 무시하도록 선택합니다. 이러한 열은 **cnt** 열의 하위 열이므로 포함하지 않습니다.

        1. 또한 이 예에서는 **속성** 및 **형식** 에 기본값을 그대로 사용합니다. 
        
        1. **다음** 을 선택합니다.

    1. **확인 세부 정보** 양식의 정보가 이전에 **기본 정보** 및 **설정 및 미리 보기** 양식에 입력한 정보와 일치하는지 확인합니다.

    1. **만들기** 를 선택하여 데이터 세트 만들기를 완료합니다.

    1. 데이터 세트가 목록에 표시되면 선택합니다.

    1. **다음** 을 선택합니다.

## <a name="configure-run"></a>실행 구성

데이터를 로드하고 구성한 후에는 원격 컴퓨팅 대상을 설정하고 예측하려는 데이터의 열을 선택합니다.

1. 다음과 같이 **실행 구성** 양식을 채웁니다.
    1. 실험 이름 `automl-bikeshare`를 입력합니다.

    1. 예측하려는 항목인 대상 열로 **cnt** 를 선택합니다. 이 열은 총 자전거 공유 대여 건수를 나타냅니다.

    1. 컴퓨팅 유형으로 **컴퓨팅 클러스터** 를 선택합니다. 

    1. **+새로 만들기** 를 선택하여 컴퓨팅 대상을 구성합니다. 자동화된 ML은 Azure Machine Learning 컴퓨팅만 지원합니다. 

        1. **가상 머신 선택** 양식을 채워 컴퓨팅을 설정합니다.

            필드 | Description | 자습서에서 사용하는 값
            ----|---|---
            가상 &nbsp;머신&nbsp; 우선 순위 |실험의 우선 순위를 선택합니다.| 전용
            가상 &nbsp;머신&nbsp; 유형| 컴퓨팅에 사용할 가상 머신 유형을 선택합니다.|CPU(중앙 처리 장치)
            Virtual&nbsp;machine&nbsp;size| 컴퓨팅에 사용할 가상 머신 크기를 선택합니다. 데이터 및 실험 유형에 따라 권장 크기 목록이 제공됩니다. |Standard_DS12_V2
        
        1. **다음** 을 선택하여 **설정 구성 양식** 을 채웁니다.
        
             필드 | Description | 자습서에서 사용하는 값
            ----|---|---
            컴퓨팅 이름 |  컴퓨팅 컨텍스트를 식별하는 고유한 이름입니다. | bike-compute
            최소 / 최대 노드| 데이터를 프로파일링하려면 하나 이상의 노드를 지정해야 합니다.|최소 노드: 1<br>최대 노드: 6
            규모 축소 전 유휴 시간(초) | 클러스터가 최소 노드 수까지 자동으로 축소되기 전의 유휴 시간입니다.|1800(기본값)
            고급 설정 | 실험에 사용할 가상 네트워크를 구성하고 권한을 부여하는 설정입니다.| None 
  
        1. **만들기** 를 선택하여 컴퓨팅 대상을 가져옵니다. 

            **이 작업은 완료하는 데 몇 분이 소요됩니다.** 

        1. 만들기가 완료되면 드롭다운 목록에서 새 컴퓨팅 대상을 선택합니다.

    1. **다음** 을 선택합니다.

## <a name="select-forecast-settings"></a>예측 설정 선택

기계 학습 작업 유형 및 구성 설정을 지정하여 자동화된 ML 실험의 설정을 완료합니다.

1. **작업 유형 및 설정** 양식에서 기계 학습 작업 유형으로 **시계열 예측** 을 선택합니다.

1. **시간 열** 로 **date** 를 선택하고 **시계열 식별자** 를 비워 둡니다. 

1. **예측 구간** 은 예측하려는 미래의 시간 길이입니다.  자동 검색을 선택 취소하고 필드에 14를 입력합니다. 

1. **추가 구성 설정 보기** 를 선택하고 다음과 같이 필드를 채웁니다. 이러한 설정은 학습 작업을 보다 효율적으로 제어하고 예측에 대한 설정을 지정하기 위한 것입니다. 그렇지 않으면 실험 선택 및 데이터를 기반으로 기본값이 적용됩니다.

    추가&nbsp;구성|Description|Value&nbsp;for&nbsp;tutorial
    ------|---------|---
    기본 메트릭| 기계 학습 알고리즘을 측정할 평가 메트릭입니다.|정규화된 제곱 평균 오차
    최상의 모델에 대한 설명| 자동화된 ML에서 만든 최상의 모델에 대한 설명 가능성을 자동으로 표시합니다.| 사용
    차단된 알고리즘 | 학습 작업에서 제외할 알고리즘| 최대 임의 트리
    추가 예측 설정| 이러한 설정은 모델의 정확도를 향상시키는 데 도움이 됩니다. <br><br> _**대상 지연 예측:**_ 대상 변수의 지연 시간을 얼마 전으로 구성할지 지정 <br> _**대상 이동 기간**_: *max, min* 및 *sum* 같은 기능이 생성되는 이동 기간의 크기 지정 | <br><br>대상&nbsp;지연&nbsp;예측: None <br> 대상&nbsp;이동&nbsp;기간&nbsp;크기: None
    종료 기준| 조건을 충족하는 경우 학습 작업이 중지됩니다. |학습&nbsp;작업&nbsp;시간(시): 3 <br> 메트릭&nbsp;점수&nbsp;임계값: None
    유효성 검사 | 교차 유효성 검사 유형 및 테스트 수를 선택합니다.|유효성 검사 유형:<br>&nbsp;k겹&nbsp;교차 유효성 검사 <br> <br> 유효성 검사 수: 5
    동시성| 반복당 실행되는 최대 병렬 반복 수| 최대&nbsp;동시&nbsp;반복: 6
    
    **저장** 을 선택합니다.

## <a name="run-experiment"></a>실험 실행

실험을 실행하려면 **마침** 을 선택합니다. **실행 세부 정보** 화면이 열리고 위쪽의 실행 번호 옆에 **실행 상태** 가 표시됩니다. 이 상태는 실험이 진행되면서 업데이트됩니다. 실험 상태를 알려주는 알림이 스튜디오의 오른쪽 위 모서리에도 표시됩니다.

>[!IMPORTANT]
> 실험 실행을 준비하기 위한 준비는 **10-15분** 이 걸립니다.
> 실행이 시작되면 **각 반복에 대해 2-3분 더** 소요됩니다.<br> <br>
> 이 프로세스는 시간이 오래 걸리므로 프로덕션 환경에서는 시간이 더 걸릴 수 있습니다. 기다리는 동안 **모델** 탭에서 테스트가 완료된 알고리즘을 살펴보는 것이 좋습니다. 

##  <a name="explore-models"></a>모델 살펴보기

**모델** 탭으로 이동하여 테스트한 알고리즘(모델)을 확인합니다. 기본적으로 모델은 완료되면 메트릭 점수를 기준으로 정렬됩니다. 이 자습서의 경우 선택한 **정규화된 제곱 평균 오차** 메트릭에 따라 가장 높은 점수를 획득한 모델이 목록 맨 위에 표시됩니다.

모든 실험 모델이 완료될 때까지 기다리는 동안 완료된 모델의 **알고리즘 이름** 을 선택하여 성능 세부 정보를 살펴봅니다. 

다음 예제에서는 **세부 정보** 및 **메트릭** 탭으로 이동하여 선택한 모델의 속성, 메트릭 및 성능 차트를 확인합니다. 

![실행 세부 정보](./media/tutorial-automated-ml-forecast/explore-models.gif)

## <a name="deploy-the-model"></a>모델 배포

Azure Machine Learning Studio에서 자동화된 기계 학습을 사용하면 몇 단계 안에 최적 모델을 웹 서비스로 배포할 수 있습니다. 배포는 모델 통합이므로 새 데이터를 예측하고 잠재적인 기회 영역을 식별할 수 있습니다. 

이 실험에서 웹 서비스에 대한 배포는 자전거 공유 회사에 이제 자전거 공유 임대 수요를 예측하기 위한 반복적이고 확장 가능한 웹 솔루션이 있음을 의미합니다. 

실행이 완료되면 화면 상단에 있는 **실행 1** 을 선택하여 부모 실행 페이지로 다시 이동합니다.

**모범 모델 요약** 섹션에서 **StackEnsemble** 은 **정규화된 제곱 평균 오차** 메트릭을 기반으로 이 실험의 컨텍스트에서 가장 적합한 모델로 간주됩니다.  

이 모델을 배포할 것이며, 배포 시간이 약 20분 정도 걸립니다. 배포 프로세스에는 모델 등록, 리소스 생성, 웹 서비스에 대한 구성을 포함한 몇 가지 단계가 수반됩니다.

1. **StackEnsemble** 을 선택하여 모델별 페이지를 엽니다.

1. 화면의 왼쪽 위 영역에 있는 **배포** 단추를 선택합니다.

1. 다음과 같이 **모델 배포** 창을 채웁니다.

    필드| 값
    ----|----
    배포 이름| bikeshare-deploy
    배포 설명| 자전거 공유 수요 배포
    컴퓨팅 형식 | ACI(Azure Compute Instance) 선택
    인증 사용| 사용 안 함 
    사용자 지정 배포 자산 사용| 사용 안 함 사용 안 함으로 설정하면 기본 드라이버 파일(점수 매기기 스크립트) 및 환경 파일을 자동으로 생성할 수 있습니다. 
    
    이 예제에서는 *고급* 메뉴에 제공된 기본값을 사용합니다. 

1. **배포** 를 선택합니다.  

    **실행** 화면 위쪽에 배포가 성공적으로 시작되었음을 나타내는 녹색 성공 메시지가 표시됩니다. 배포 진행률은 **모델 요약** 창의 **배포 상태** 에서 확인할 수 있습니다.
    
배포가 성공하면 예측을 생성하는 운영 웹 서비스가 생깁니다. 

[**다음 단계**](#next-steps)로 이동하여 새 웹 서비스를 사용하는 방법을 자세히 알아보고 Power BI의 기본 제공 Azure Machine Learning 지원을 사용하여 예측을 테스트합니다.

## <a name="clean-up-resources"></a>리소스 정리

배포 파일은 데이터 및 실험 파일보다 크기 때문에 더 많은 저장 비용이 발생합니다. 작업 영역 및 실험 파일을 유지하려는 경우에는 배포 파일만 삭제하여 계정 비용을 최소화할 수 있습니다. 또는 어떤 파일도 사용할 계획이 없으면 전체 리소스 그룹을 삭제합니다.  

### <a name="delete-the-deployment-instance"></a>배포 인스턴스 삭제

다른 자습서에서 사용하거나 탐색할 수 있도록 리소스 그룹과 작업 영역을 유지하려면 Azure Machine Learning Studio에서 배포 인스턴스만 삭제합니다. 

1. [Azure Machine Learning Studio](https://ml.azure.com/)로 이동합니다. 작업 영역으로 이동한 다음, 왼쪽 **자산** 창 아래에서 **엔드포인트** 를 선택합니다. 

1. 삭제하려는 배포를 선택하고 **삭제** 를 선택합니다. 

1. **계속** 을 선택합니다.

### <a name="delete-the-resource-group"></a>리소스 그룹 삭제

[!INCLUDE [aml-delete-resource-group](../../includes/aml-delete-resource-group.md)]

## <a name="next-steps"></a>다음 단계

이 자습서에서는 Azure Machine Learning Studio에서 자동화된 ML을 사용하여 자전거 공유 임대 수요를 예측하는 시계열 예측 모델을 만들고 배포합니다. 

새로 배포된 웹 서비스의 사용을 용이하게 하는 Power BI 지원 스키마를 만드는 방법에 대한 단계별 설명은 이 문서를 참조하세요.

> [!div class="nextstepaction"]
> [웹 서비스 사용](/power-bi/connect-data/service-aml-integrate?context=azure%2fmachine-learning%2fcontext%2fml-context)

+ [자동화된 기계 학습](concept-automated-ml.md)에 대한 자세한 정보
+ 분류 메트릭 및 차트에 대한 자세한 내용은 [자동화된 기계 학습 결과 이해](how-to-understand-automated-ml.md) 문서를 참조하세요.
+ [기능화](how-to-configure-auto-features.md#featurization)에 대해 자세히 알아보세요.
+ [데이터 프로파일링](how-to-connect-data-ui.md#profile)에 대한 자세한 정보

>[!NOTE]
> 이 자전거 공유 데이터 세트는 이 자습서에 맞게 수정되었습니다. 이 데이터 세트는 [Kaggle Competition](https://www.kaggle.com/c/bike-sharing-demand/data)의 일부로 제공되었고 원래는 [Capital Bikeshare](https://www.capitalbikeshare.com/system-data)를 통해 제공되었습니다. 또한 [UCI Machine Learning Database](http://archive.ics.uci.edu/ml/datasets/Bike+Sharing+Dataset) 내에서도 확인할 수 있습니다.<br><br>
> 원본: Fanaee-T, Hadi, and Gama, Joao, Event labeling combining ensemble detectors and background knowledge, Progress in Artificial Intelligence(2013): pp. 1-15, Springer Berlin Heidelberg.