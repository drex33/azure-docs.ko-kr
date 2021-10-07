---
title: '자습서: AutoML -코드 없는 분류 모델 학습'
titleSuffix: Azure Machine Learning
description: 스튜디오 UI에서 Azure Machine Learning 자동화된 ML을 사용하여 한 줄의 코드도 작성하지 않고 분류 모델을 학습합니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: automl
ms.topic: tutorial
author: cartacioS
ms.author: sacartac
ms.reviewer: nibaccam
ms.date: 07/01/2021
ms.custom: automl, FY21Q4-aml-seo-hack, contperf-fy21q4
ms.openlocfilehash: e3664e9a42a502509e74c43643d47f18e153ff6e
ms.sourcegitcommit: f29615c9b16e46f5c7fdcd498c7f1b22f626c985
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/04/2021
ms.locfileid: "129429968"
---
# <a name="tutorial-train-a-classification-model-with-no-code-automl-in-the-azure-machine-learning-studio"></a>자습서: Azure Machine Learning 스튜디오에서 코드가 없는 AutoML을 사용하여 분류 모델 학습

Azure Machine Learning 스튜디오에서 Azure Machine Learning 자동화된 ML을 사용하여 코드 없는 AutoML을 통해 분류 모델을 학습하는 방법에 대해 알아봅니다. 이 분류 모델은 클라이언트가 금융 기관의 정기 예금을 구독할지 예측합니다.

자동화된 ML을 사용하면 시간이 많이 걸리는 작업을 자동화할 수 있습니다. 자동화된 기계 학습은 사용자가 선택한 성공 메트릭을 기반으로 최상의 모델을 발견할 수 있도록 알고리즘과 하이퍼 매개 변수의 여러 조합을 빠르게 반복합니다.

이 자습서에서는 코드를 작성하지 않고 스튜디오 인터페이스를 사용하여 학습을 수행합니다.  다음 작업을 수행하는 방법을 알아봅니다.

> [!div class="checklist"]
> * Azure Machine Learning 작업 영역을 만듭니다.
> * 자동화된 기계 학습 실험을 실행합니다.
> * 모델 세부 정보를 살펴봅니다.
> * 권장된 모델을 배포합니다.

또한 다음과 같은 다른 모델 유형에 대해 자동화된 기계 학습을 시도해 보세요.

* 코드 없는 예측 예제는 [자습서: 수요 예측 및 AutoML](tutorial-automated-ml-forecast.md)을 참조하세요.
* 회귀 모델의 코드 첫번째 예는 [자습서: AutoML을 사용하는 회귀 모델](tutorial-auto-train-models.md)을 참조하세요.

## <a name="prerequisites"></a>필수 조건

* Azure 구독 Azure 구독이 아직 없는 경우 [체험 계정](https://azure.microsoft.com/free/)을 만듭니다.

* [**bankmarketing_train.csv**](https://automlsamplenotebookdata.blob.core.windows.net/automl-sample-notebook-data/bankmarketing_train.csv) 데이터 파일을 다운로드합니다. **y** 열은 고객이 정기 예금에 가입했는지 여부를 나타내며, 뒷부분에서 이 자습서의 예측에 대한 대상 열로 식별됩니다. 

## <a name="create-a-workspace"></a>작업 영역 만들기

Azure Machine Learning 작업 영역은 기계 학습 모델을 실험하고, 학습시키고, 배포하는 데 사용하는 클라우드의 기본 리소스입니다. Azure 구독 및 리소스 그룹을 서비스에서 사용하기 쉬운 개체에 연결합니다. 

[작업 영역을 만드는 방법](how-to-manage-workspace.md)에는 여러 가지가 있습니다. 이 자습서에서는 Azure 리소스를 관리하기 위한 웹 기반 콘솔인 Azure Portal을 통해 작업 영역을 만듭니다.

[!INCLUDE [aml-create-portal](../../includes/aml-create-in-portal.md)]

>[!IMPORTANT] 
> **작업 영역** 및 **구독** 을 적어 둡니다. 올바른 작업 영역에 실험을 만들려면 이 정보가 필요합니다. 

## <a name="sign-in-to-the-studio"></a>스튜디오에 로그인

모든 기술 수준의 데이터 과학 전문가용 데이터 과학 시나리오를 수행하기 위한 기계 학습 도구를 포함하는 통합 웹 인터페이스인 https://ml.azure.com 에서 Azure Machine Learning Studio를 통해 다음 실험 설정 및 실행 단계를 완료합니다. 이 Studio는 Internet Explorer 브라우저에서 지원되지 않습니다.

1. [Azure Machine Learning Studio](https://ml.azure.com)에 로그인합니다.

1. 해당 구독과 직접 만든 작업 영역을 선택합니다.

1. **시작** 을 선택합니다.

1. 왼쪽 창의 **작성** 섹션에서 **자동화된 ML** 을 선택합니다.

   이는 자동화된 ML의 첫 번째 실험이므로 빈 목록과 설명서에 대한 링크가 표시됩니다.

   ![시작 페이지](./media/tutorial-first-experiment-automated-ml/get-started.png)

1. **+새 자동화된 ML 실행** 을 선택합니다. 

## <a name="create-and-load-dataset"></a>데이터 세트 생성 및 로드

실험을 구성하기 전에 데이터 파일을 Azure Machine Learning 데이터 세트 형식으로 작업 영역에 업로드합니다. 이렇게 하면 데이터의 형식이 실험에 맞게 적절히 지정되도록 할 수 있습니다.

1. **+데이터 세트 만들기** 드롭다운에서 **로컬 파일에서** 를 선택하여 새 데이터 세트를 만듭니다. 

    1. **기본 정보** 양식에서 데이터 세트에 이름을 지정하고 선택적 설명을 입력합니다. 자동화된 ML 인터페이스는 현재 테이블 형식 데이터 세트만 지원하므로 데이터 세트 형식은 기본적으로 *테이블 형식* 이어야 합니다.

    1. 왼쪽 아래에서 **다음** 을 선택합니다.

    1. **데이터 저장소 및 파일 선택** 양식에서 작업 영역을 만드는 동안 자동으로 설정된 기본 데이터 저장소 **workspaceblobstore(Azure Blob Storage)** 를 선택합니다. 여기서 데이터 파일을 업로드하여 작업 영역에서 사용할 수 있도록 합니다.

    1. **찾아보기** 를 선택합니다.
    
    1. 로컬 컴퓨터에 있는 **bankmarketing_train.csv** 파일을 선택합니다. 이는 [구성 요소](https://automlsamplenotebookdata.blob.core.windows.net/automl-sample-notebook-data/bankmarketing_train.csv)로 다운로드한 파일입니다.

    1. 데이터 세트에 고유 이름을 지정하고 선택적 설명을 입력합니다. 

    1. 왼쪽 아래에서 **다음** 을 선택하여 작업 영역을 만드는 동안 자동으로 설정된 기본 컨테이너에 데이터 세트를 업로드합니다.  
    
       업로드가 완료되면, **설정 및 미리 보기** 양식이 파일 형식에 따라 미리 채워집니다. 
       
    1. **설정 및 미리 보기** 양식이 다음과 같이 채워졌는지 확인하고 **다음** 을 선택합니다.
        
        필드|Description| 자습서에서 사용하는 값
        ---|---|---
        파일 형식|파일에 저장된 데이터의 레이아웃 및 유형을 정의합니다.| 구분됨
        구분 기호|일반 텍스트 또는 기타 데이터 스트림에서 개별의 독립된 지역 간의&nbsp; 경계를 지정하는 데 사용하는 하나 이상의 문자입니다. |쉼표
        Encoding|데이터 세트를 읽는 데 사용할 문자 스키마 테이블을 식별합니다.| UTF-8
        열 머리글| 데이터 세트의 헤더(있는 경우)가 처리되는 방법을 나타냅니다.| 모든 파일의 머리글이 동일함
        행 건너뛰기 | 데이터 세트에서 건너뛴 행(있는 경우)의 수를 나타냅니다.| None

    1. **스키마** 폼을 사용하여 이 실험을 위해 데이터를 추가로 구성할 수 있습니다. 이 예제에서는 **day_of_week** 의 토글 스위치를 포함하지 않도록 선택합니다. **다음** 을 선택합니다.
         ![스키마 양식](./media/tutorial-first-experiment-automated-ml/schema-tab-config.gif)
    1. **확인 세부 정보** 양식의 정보가 이전에 **기본 정보, 데이터 저장소 및 파일 선택** 및 **설정 및 미리 보기** 양식에 입력한 정보와 일치하는지 확인합니다.
    
    1. **만들기** 를 선택하여 데이터 세트 만들기를 완료합니다.
    
    1. 데이터 세트가 목록에 표시되면 선택합니다.
    
    1. **데이터 미리 보기** 를 검토하여 **day_of_week** 가 포함되지 않았는지 확인한 다음, **닫기** 를 선택합니다.

    1. **다음** 을 선택합니다.

## <a name="configure-run"></a>실행 구성

데이터를 로드하고 구성한 후 실험을 설정할 수 있습니다. 이 설정에는 컴퓨팅 환경의 크기를 선택하고 예측할 열을 지정하는 등의 실험 디자인 작업이 포함되어 있습니다. 

1. **새로 만들기** 라디오 단추를 선택합니다.

1. 다음과 같이 **실행 구성** 양식을 채웁니다.
    1. 이 실험 이름 `my-1st-automl-experiment`를 입력합니다.

    1. 예측하려는 항목인 대상 열로 **y** 를 선택합니다. 이 열은 클라이언트가 정기 예금에 가입했는지 여부를 나타냅니다.
    
    1. **+새 컴퓨팅 만들기** 를 선택하고 컴퓨팅 대상을 구성합니다. 컴퓨팅 대상은 학습 스크립트를 실행하거나 서비스 배포를 호스트하는 데 사용되는 로컬 또는 클라우드 기반 리소스 환경입니다. 이 실험에서는 클라우드 기반 컴퓨팅을 사용합니다. 
        1. **Virtual Machine** 양식을 채워서 컴퓨팅을 설정합니다.

            필드 | Description | 자습서에서 사용하는 값
            ----|---|---
            가상 &nbsp;머신&nbsp; 우선 순위 |실험의 우선 순위를 선택합니다.| 전용
            가상 &nbsp;머신&nbsp; 유형| 컴퓨팅에 사용할 가상 머신 유형을 선택합니다.|CPU(중앙 처리 장치)
            Virtual&nbsp;machine&nbsp;size| 컴퓨팅에 사용할 가상 머신 크기를 선택합니다. 데이터 및 실험 유형에 따라 권장 크기 목록이 제공됩니다. |Standard_DS12_V2
        
        1. **다음** 을 선택하여 **설정 구성 양식** 을 채웁니다.
        
            필드 | Description | 자습서에서 사용하는 값
            ----|---|---
            컴퓨팅 이름 |  컴퓨팅 컨텍스트를 식별하는 고유한 이름입니다. | automl-compute
            최소 / 최대 노드| 데이터를 프로파일링하려면 하나 이상의 노드를 지정해야 합니다.|최소 노드: 1<br>최대 노드: 6
            규모 축소 전 유휴 시간(초) | 클러스터가 최소 노드 수까지 자동으로 축소되기 전의 유휴 시간입니다.|1800(기본값)
            고급 설정 | 실험에 사용할 가상 네트워크를 구성하고 권한을 부여하는 설정입니다.| None               

        1. **만들기** 를 선택하여 컴퓨팅 대상을 만듭니다. 

            **이 작업은 완료하는 데 몇 분이 소요됩니다.** 

             ![설정 페이지](./media/tutorial-first-experiment-automated-ml/compute-settings.png)

        1. 만들기가 완료되면 드롭다운 목록에서 새 컴퓨팅 대상을 선택합니다.

    1. **다음** 을 선택합니다.

1. **작업 및 설정 선택** 양식에서 기계 학습 작업 유형 및 구성 설정을 지정하여 자동화된 ML 실험의 설정을 완료합니다.
    
    1.  기계 학습 작업 유형으로 **분류** 를 선택합니다.

    1. **추가 구성 설정 보기** 를 선택하고 다음과 같이 필드를 채웁니다. 이러한 설정은 학습 작업을 더 효율적으로 제어하기 위한 것입니다. 그렇지 않으면 실험 선택 및 데이터를 기반으로 기본값이 적용됩니다.

        추가&nbsp;구성|Description|Value&nbsp;for&nbsp;tutorial
        ------|---------|---
        기본 메트릭| 기계 학습 알고리즘을 측정할 평가 메트릭입니다.|AUC_weighted
        최상의 모델에 대한 설명| 자동화된 ML에서 만든 최상의 모델에 대한 설명 가능성을 자동으로 표시합니다.| 사용
        차단된 알고리즘 | 학습 작업에서 제외할 알고리즘| None
        종료 기준| 조건을 충족하는 경우 학습 작업이 중지됩니다. |학습&nbsp;작업&nbsp;시간(시): 1 <br> 메트릭&nbsp;점수&nbsp;임계값: None
        유효성 검사 | 교차 유효성 검사 유형 및 테스트 수를 선택합니다.|유효성 검사 유형:<br>&nbsp;k겹&nbsp;교차 유효성 검사 <br> <br> 유효성 검사 수: 2
        동시성| 반복당 실행되는 최대 병렬 반복 수| 최대&nbsp;동시&nbsp;반복: 5
        
        **저장** 을 선택합니다.
    
1. **마침** 을 선택하여 실험을 실행합니다. 실험 준비가 시작되면 위쪽의 **실행 상태** 를 나타내는 **실행 세부 정보** 화면이 열립니다. 이 상태는 실험이 진행되면서 업데이트됩니다. 실험 상태를 알려주는 알림이 스튜디오의 오른쪽 위 모서리에도 표시됩니다.

>[!IMPORTANT]
> 실험 실행을 준비하기 위한 준비는 **10-15분** 이 걸립니다.
> 실행이 시작되면 **각 반복에 대해 2-3분 더** 소요됩니다.  <br> <br>
> 프로덕션 환경에서는 시간이 더 걸릴 수 있습니다. 그러나 이 자습서에서는 다른 사용자가 실행하는 동안 실행이 완료되면 **모델** 탭에서 테스트된 알고리즘을 살펴보는 것이 좋습니다. 

##  <a name="explore-models"></a>모델 살펴보기

**모델** 탭으로 이동하여 테스트한 알고리즘(모델)을 확인합니다. 기본적으로 모델은 완료되면 메트릭 점수를 기준으로 정렬됩니다. 이 자습서의 경우 선택한 **AUC_weighted** 메트릭에 따라 가장 높은 점수를 획득한 모델이 목록 맨 위에 표시됩니다.

모든 실험 모델이 완료될 때까지 기다리는 동안 완료된 모델의 **알고리즘 이름** 을 선택하여 성능 세부 정보를 살펴봅니다. 

다음에서는 **세부 정보** 및 **메트릭** 탭으로 이동하여 선택한 모델의 속성, 메트릭 및 성능 차트를 확인합니다. 

![반복 실행 세부 정보](./media/tutorial-first-experiment-automated-ml/run-detail.gif)

## <a name="model-explanations"></a>모델 설명

모델이 완료되기를 기다리는 동안 모델 설명을 살펴보고 특정 모델의 예측에 영향을 주는 데이터 기능(원시 또는 엔지니어링)을 확인할 수도 있습니다. 

이러한 모델 설명은 요청 시 생성될 수 있으며, **설명(미리 보기)** 탭의 일부인 모델 설명 대시보드에 요약되어 있습니다.

모델 설명 생성 
 
1. 상단의 **실행 1** 을 선택하여 **모델** 화면으로 이동합니다. 
1. **모델** 탭을 선택합니다.
1. 이 자습서에서는 첫 번째 **MaxAbsScaler, LightGBM** 모델을 선택합니다.
1. 상단에 있는 **모델 설명** 단추를 선택합니다. 오른쪽에 **모델 설명** 창이 표시됩니다. 
1. 이전에 만든 **automl-compute** 를 선택합니다. 이 컴퓨팅 클러스터는 모델 설명을 생성하기 위해 자식 실행을 시작합니다.
1. 하단에서 **만들기** 를 선택합니다. 화면 상단에 녹색 성공 메시지가 표시됩니다. 
    >[!NOTE]
    > 설명 가능성 실행을 완료하는 데 약 2~5분이 소요됩니다.
1. **설명(미리 보기)** 단추를 선택합니다. 이 탭은 설명 가능성 실행이 완료되면 입력됩니다.
1. 왼쪽에서 창을 확장하고 **기능** 에서 **원시** 가 표시된 행을 선택합니다. 
1. 오른쪽의 **집계 기능 중요도** 탭을 선택합니다. 이 차트에서는 선택한 모델의 예측에 영향을 미치는 데이터 기능을 보여줍니다. 

    이 예시에서 기간이 이 모델의 예측에 가장 큰 영향을 미치는 듯합니다.
    
    ![모델 설명 대시보드](media/tutorial-first-experiment-automated-ml/model-explanation-dashboard.png)

## <a name="deploy-the-best-model"></a>최적의 모델 배포

자동화된 기계 학습 인터페이스를 사용하면 몇 단계 안에 최적 모델을 웹 서비스로 배포할 수 있습니다. 배포는 모델 통합이므로 새 데이터를 예측하고 잠재적인 기회 영역을 식별할 수 있습니다. 

이 실험에서 웹 서비스에 배포한다는 것은 이제 금융 기관이 잠재적 정기 예금 고객을 식별할 수 있는 반복적이고 확장 가능한 솔루션을 확보했다는 것을 의미합니다. 

실험 실행이 완료되었는지 확인합니다. 이렇게 하려면 화면 상단에 있는 **실행 1** 을 선택하여 부모 실행 페이지로 다시 이동합니다. **완료** 상태가 화면 왼쪽 위에 표시됩니다. 

실험 실행이 완료되면 **세부 정보** 페이지가 **최상의 모델 요약** 섹션으로 채워집니다. **AUC_weighted** 메트릭에 따라 이 실험에서 가장 적합한 모델은 **VotingEnsemble** 입니다.  

이 모델을 배포할 것이며, 배포 시간이 약 20분 정도 걸립니다. 배포 프로세스에는 모델 등록, 리소스 생성, 웹 서비스에 대한 구성을 포함한 몇 가지 단계가 수반됩니다.

1. **VotingEnsemble** 을 선택하여 모델별 페이지를 엽니다.

1. 왼쪽 위에서 **배포** 단추를 선택합니다.

1. 다음과 같이 **모델 배포** 창을 채웁니다.

    필드| 값
    ----|----
    배포 이름| my-automl-deploy
    배포 설명| 첫 번째 자동화된 기계 학습 실험 배포
    컴퓨팅 형식 | ACI(Azure Compute Instance) 선택
    인증 사용| 사용 안 함 
    사용자 지정 배포 사용| 사용 안 함 기본 드라이버 파일(점수 매기기 스크립트) 및 환경 파일을 자동으로 생성할 수 있습니다. 
    
    이 예제에서는 *고급* 메뉴에 제공된 기본값을 사용합니다. 

1. **배포** 를 선택합니다.  

    녹색 성공 메시지가 **실행** 화면의 맨 위에 표시되고 **모델 요약** 창에는 상태 메시지가 **배포 상태** 아래에 나타납니다. 주기적으로 **새로 고침** 을 선택하여 배포 상태를 확인합니다.
    
이제 예측을 생성하는 운영 웹 서비스가 생겼습니다. 

[**다음 단계**](#next-steps)로 이동하여 새 웹 서비스를 사용하는 방법을 자세히 알아보고 Power BI의 기본 제공 Azure Machine Learning 지원을 사용하여 예측을 테스트합니다.

## <a name="clean-up-resources"></a>리소스 정리

배포 파일은 데이터 및 실험 파일보다 크기 때문에 더 많은 저장 비용이 발생합니다. 작업 영역 및 실험 파일을 유지하려는 경우에는 배포 파일만 삭제하여 계정 비용을 최소화할 수 있습니다. 또는 어떤 파일도 사용할 계획이 없으면 전체 리소스 그룹을 삭제합니다.  

### <a name="delete-the-deployment-instance"></a>배포 인스턴스 삭제

다른 자습서에서 사용하거나 탐색할 수 있도록 리소스 그룹과 작업 영역을 유지하려면 https:\//ml.azure.com/의 Azure Machine Learning에서 배포 인스턴스만 삭제합니다. 

1. [Azure Machine Learning](https://ml.azure.com/)으로 이동합니다. 작업 영역으로 이동한 다음, 왼쪽 **자산** 창 아래에서 **엔드포인트** 를 선택합니다. 

1. 삭제하려는 배포를 선택하고 **삭제** 를 선택합니다. 

1. **계속** 을 선택합니다.

### <a name="delete-the-resource-group"></a>리소스 그룹 삭제

[!INCLUDE [aml-delete-resource-group](../../includes/aml-delete-resource-group.md)]

## <a name="next-steps"></a>다음 단계

이 자동화된 기계 학습 자습서에서는 Azure Machine Learning의 자동화된 ML 인터페이스를 사용하여 분류 모델을 만들고 배포했습니다. 자세한 내용과 다음 단계는 아래 문서를 참조하세요.

> [!div class="nextstepaction"]
> [웹 서비스 사용](/power-bi/connect-data/service-aml-integrate?context=azure%2fmachine-learning%2fcontext%2fml-context)

+ [자동화된 기계 학습](concept-automated-ml.md)에 대한 자세한 정보
+ 분류 메트릭 및 차트에 대한 자세한 내용은 [자동화된 기계 학습 결과 이해](how-to-understand-automated-ml.md) 문서를 참조하세요.
+ [기능화](how-to-configure-auto-features.md#featurization)에 대해 자세히 알아보세요.
+ [데이터 프로파일링](how-to-connect-data-ui.md#profile)에 대한 자세한 정보


>[!NOTE]
> 이 은행 마케팅 데이터 세트는 [Creative Commons(CCO: Public Domain) 라이선스](https://creativecommons.org/publicdomain/zero/1.0/)에서 사용이 허가됩니다. 데이터베이스의 개별 콘텐츠에 대한 모든 권한은 [데이터베이스 콘텐츠 라이선스](https://creativecommons.org/publicdomain/zero/1.0/)를 따르며 [Kaggle](https://www.kaggle.com/janiobachmann/bank-marketing-dataset)에서 사용할 수 있습니다. 이 데이터 세트는 원래 [UCI Machine Learning 데이터베이스](https://archive.ics.uci.edu/ml/datasets/bank+marketing) 내에 제공됩니다.<br><br>
> [Moro 외, 2014] S. Moro, P. Cortez 및 P. Rita. A Data-Driven Approach to Predict the Success of Bank Telemarketing. Decision Support Systems, Elsevier, 62:22-31, 2014년 6월.
