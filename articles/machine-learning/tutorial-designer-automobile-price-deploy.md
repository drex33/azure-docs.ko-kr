---
title: '자습서: 디자이너- 코드 없는 모델 배포'
titleSuffix: Azure Machine Learning
description: Azure Machine Learning 디바이너를 사용하여 자동차 가격을 예측하는 기계 학습 모델을 배포합니다.
author: likebupt
ms.author: keli19
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
ms.date: 10/21/2021
ms.custom: designer, FY21Q4-aml-seo-hack, contperf-fy21q4
ms.openlocfilehash: 3348bddec5700ba2a5ddf1112ad20f9319e04d79
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/04/2021
ms.locfileid: "131554890"
---
# <a name="tutorial-designer---deploy-a-machine-learning-model"></a>자습서: 디자이너 - 기계 학습 모델 배포

디자이너를 사용하여 자동차의 가격을 예측하는 기계 학습 모델을 배포합니다. 이 자습서는 2부로 구성된 시리즈 중 제2부입니다.


[자습서의 1부](tutorial-designer-automobile-price-train-score.md)에서는 자동차 가격에 대한 선형 회귀 모델을 학습했습니다. 2부에서는 모델을 배포하여 다른 사용자가 사용할 수 있는 기회를 제공합니다. 이 자습서에서는 다음을 수행합니다.

> [!div class="checklist"]
> * 실시간 유추 파이프라인을 만듭니다.
> * 유추 클러스터를 만듭니다.
> * 실시간 엔드포인트를 배포합니다.
> * 실시간 엔드포인트를 테스트합니다.

## <a name="prerequisites"></a>사전 요구 사항

[이 자습서의 1부](tutorial-designer-automobile-price-train-score.md)를 완료하여 디자이너에서 Machine Learning 모델을 학습하고 채점하는 방법을 알아보세요.

[!INCLUDE [machine-learning-missing-ui](../../includes/machine-learning-missing-ui.md)]

## <a name="create-a-real-time-inference-pipeline"></a>실시간 유추 파이프라인 만들기

파이프라인을 배포하려면 먼저 학습 파이프라인을 실시간 유추 파이프라인으로 변환해야 합니다. 이 프로세스는 학습 구성 요소를 제거하고 요청을 처리하기 위해 웹 서비스 입력 및 출력을 추가합니다.

### <a name="create-a-real-time-inference-pipeline"></a>실시간 유추 파이프라인 만들기

1. 파이프라인 캔버스의 위쪽에서 **유추 파이프라인 만들기** > **실시간 유추 파이프라인** 을 차례로 선택합니다.

    :::image type="content" source="./media/tutorial-designer-automobile-price-deploy/tutorial2-create-inference-pipeline.png" alt-text="파이프라인 만들기 단추를 찾을 수 있는 위치를 보여주는 스크린샷":::

    이제 파이프라인이 다음과 비슷할 것입니다. 

   ![배포 준비 후 예상되는 파이프라인 구성을 보여주는 스크린샷](./media/tutorial-designer-automobile-price-deploy/real-time-inference-pipeline.png)

    **유추 파이프라인 만들기** 를 선택하면 다음과 같은 여러 작업이 수행됩니다.
    
    * 학습된 모델은 구성 요소 팔레트에 **데이터 세트** 구성 요소로 저장됩니다. **내 데이터 세트** 아래에서 찾을 수 있습니다.
    * **모델 학습** 및 **데이터 분할** 과 같은 학습 구성 요소가 제거됩니다.
    * 저장된 학습된 모델이 파이프라인에 다시 추가됩니다.
    * **웹 서비스 입력** 및 **웹 서비스 출력** 구성 요소가 추가됩니다. 이러한 구성 요소는 사용자 데이터가 파이프라인에 입력되는 위치와 데이터가 반환되는 위치를 보여줍니다.

    > [!NOTE]
    > 기본적으로 **Web Service 입력** 은 동일한 다운스트림 포트에 연결되는 구성 요소 출력 데이터와 동일한 데이터 스키마를 필요로 합니다. 이 샘플에서 **Web Service 입력** 및 **자동차 가격 데이터(원시)** 는 동일한 다운스트림 구성 요소에 연결되므로 **Web Service 입력** 은 **자동차 가격 데이터(원시)** 및 대상 변수 열 `price`가 스키마에 포함된 것과 동일한 데이터 스키마를 필요로 합니다.
    > 그러나 일반적으로 데이터를 채점할 때 대상 변수 값을 알 수 없습니다. 이러한 경우 **데이터 세트에서 열 선택** 구성 요소를 사용하여 유추 파이프라인에서 대상 변수 열을 제거할 수 있습니다. 대상 변수 열을 제거하는 **데이터 세트에서 열 선택** 출력이 **Web Service 입력** 구성 요소의 출력과 동일한 포트에 연결되어 있는지 확인합니다.

1. **제출** 을 선택하고 1부에서 사용한 것과 동일한 컴퓨팅 대상 및 실험을 사용합니다.

    첫 번째 실행인 경우 파이프라인이 실행을 완료하는 데 최대 20분이 걸릴 수 있습니다. 기본 컴퓨팅 설정의 최소 노드 크기는 0입니다. 즉, 디자이너가 유휴 상태가 된 후에 리소스를 할당해야 합니다. 컴퓨팅 리소스가 이미 할당되었기 때문에 반복되는 파이프라인 실행은 시간이 덜 걸립니다. 또한 디자이너는 각 구성 요소에 대해 캐시된 결과를 사용하여 효율성을 더욱 향상시킵니다.

1. **배포** 를 선택합니다.

## <a name="create-an-inferencing-cluster"></a>유추 클러스터 만들기

표시되는 대화 상자에서 기존 AKS(Azure Kubernetes Service) 클러스터에서 선택하여 모델을 배포할 수 있습니다. AKS 클러스터가 없는 경우 다음 단계에 따라 클러스터를 만듭니다.

1. 표시되는 대화 상자에서 **컴퓨팅** 을 선택하여 **컴퓨팅** 페이지로 이동합니다.

1. 탐색 리본에서 **유추 클러스터** >  **+ 새로 만들기** 를 선택합니다.

    ![새 유추 클러스터 창으로 이동하는 방법을 보여주는 스크린샷](./media/tutorial-designer-automobile-price-deploy/new-inference-cluster.png)
   
1. 유추 클러스터 창에서 새 Kubernetes 서비스를 구성합니다.

1. **컴퓨팅 이름** 에 *aks-compute* 를 입력합니다.
    
1. **지역** 에 사용할 수 있는 주변 지역을 선택합니다.

1. **만들기** 를 선택합니다.

    > [!NOTE]
    > 새 AKS 클러스터를 만드는 데 약 15분이 걸립니다. **유추 클러스터** 페이지에서 프로비저닝 상태를 확인할 수 있습니다.
    >

## <a name="deploy-the-real-time-endpoint"></a>실시간 엔드포인트 배포

AKS 서비스 프로비저닝이 완료된 후에는 실시간 유추 파이프라인으로 돌아가서 배포를 완료합니다.

1. 캔버스 위에서 **배포** 를 선택합니다.

1. **새 실시간 엔드포인트 배포** 를 선택합니다. 

1. 만든 AKS 클러스터를 선택합니다.

    :::image type="content" source="./media/tutorial-designer-automobile-price-deploy/setup-endpoint.png" alt-text="새 실시간 엔드포인트를 설정하는 방법을 보여주는 스크린샷":::

    실시간 엔드포인트에 대한 **고급** 설정을 변경할 수도 있습니다.
    
    |고급 설정|Description|
    |---|---|
    |Application Insights 진단 및 데이터 수집 사용| 배포된 엔드포인트에서 데이터를 수집하도록 Azure Application Ingishts를 설정할지 여부를 지정합니다. </br> 기본값: false |
    |점수 매기기 시간 제한| 웹 서비스 호출에 대한 점수 매기기에 적용할 시간 제한(밀리초)입니다.</br>기본적으로: 60000|
    |자동 크기 조정 사용|   자동 크기 조정을 웹 서비스에 사용하도록 설정할지 여부입니다.</br>기본값: true|
    |최소 복제본| 이 웹 서비스의 크기를 자동으로 조정할 때 사용할 최소 컨테이너 수입니다.</br>기본적으로: 1|
    |최대 복제본| 이 웹 서비스의 크기를 자동으로 조정할 때 사용할 최대 컨테이너 수입니다.</br> 기본적으로: 10|
    |대상 사용률|자동 크기 조정기에서 이 웹 서비스에 대해 유지하려고 시도해야 하는 목표 사용률(백분율)입니다.</br> 기본적으로: 70|
    |새로 고침 기간|자동 크기 조정기에서 이 웹 서비스의 크기를 조정하려고 시도하는 빈도(초)입니다.</br> 기본적으로: 1|
    |CPU 예약 용량|이 웹 서비스에 할당할 CPU 코어 수입니다.</br> 기본적으로: 0.1|
    |메모리 예약 용량|이 웹 서비스에 할당할 메모리 양(GB)입니다.</br> 기본적으로: 0.5|
        

1. **배포** 를 선택합니다. 

    배포가 완료된 후 캔버스 위의 성공 알림이 나타납니다. 몇 분 정도 걸릴 수 있습니다.

> [!TIP]
> 실시간 엔드포인트 설정 상자에서 **컴퓨팅 형식** 에 대해 **Azure Container Instance** 를 선택하는 경우 **ACI(Azure Container Instance)** 에 배포할 수도 있습니다.
> Azure Container Instance는 테스트 또는 개발에 사용됩니다. 48GB 미만의 RAM이 필요한 소규모 CPU 기반 워크로드의 경우 ACI를 사용합니다.

## <a name="test-the-real-time-endpoint"></a>실시간 엔드포인트 테스트

배포가 완료된 후 **엔드포인트** 페이지로 이동하여 실시간 엔드포인트를 볼 수 있습니다.

1. **엔드포인트** 페이지에서 배포한 엔드포인트를 선택합니다.

    **세부 정보** 탭에서 REST URI, Swagger 정의, 상태 및 태그와 같은 추가 정보를 볼 수 있습니다.

    **사용** 탭에서 샘플 사용 코드, 보안 키를 찾아 인증 방법을 설정할 수 있습니다.

    **배포 로그** 탭에서 실시간 엔드포인트의 자세한 배포 로그를 찾을 수 있습니다.

1. 엔드포인트를 테스트하려면 **테스트** 탭으로 이동합니다. 여기에서 테스트 데이터를 입력하고 **테스트** 를 선택하여 엔드포인트의 출력을 확인할 수 있습니다.

웹 서비스 사용에 대한 자세한 내용은 [웹 서비스로 배포된 모델 사용](how-to-consume-web-service.md)을 참조하세요.

## <a name="limitations"></a>제한 사항

### <a name="update-inference-pipeline"></a>유추 파이프라인 업데이트

학습 파이프라인을 수정하는 경우 학습 파이프라인을 다시 제출하고, 유추 파이프라인을 **업데이트** 하고, 유추 파이프라인을 다시 실행해야 합니다.

학습된 모델만 유추 파이프라인에서 업데이트되고, 데이터 변환은 업데이트되지 않습니다.

업데이트된 변환을 유추 파이프라인에서 사용하려면 변환 구성 요소의 변환 출력을 데이터 세트로 등록해야 합니다.

![변환 데이터 세트를 등록하는 방법을 보여주는 스크린샷](./media/tutorial-designer-automobile-price-deploy/register-transformation-dataset.png)

그런 다음, 수동으로 유추 파이프라인의 **TD** 구성 요소를 등록된 데이터 세트로 바꿉니다.

![변환 구성 요소를 바꾸는 방법을 보여주는 스크린샷](./media/tutorial-designer-automobile-price-deploy/replace-td-module.png)

그런 다음, 업데이트된 모델과 변환이 적용된 유추 파이프라인을 제출하고 배포할 수 있습니다.

### <a name="deploy-real-time-endpoint"></a>실시간 엔드포인트 배포

datstore 액세스 제한으로 인해 유추 파이프라인에 **데이터 가져오기** 또는 **데이터 내보내기** 구성 요소가 포함되어 있으면 실시간 엔드포인트에 배포할 때 자동으로 제거됩니다.

## <a name="clean-up-resources"></a>리소스 정리

[!INCLUDE [aml-ui-cleanup](../../includes/aml-ui-cleanup.md)]

## <a name="next-steps"></a>다음 단계

이 자습서에서는 디자이너에서 기계 학습 모델을 만들고, 배포하고, 사용하는 방법에 대한 주요 단계를 알아보았습니다. 디자이너를 사용하는 방법에 대해 자세히 알아보려면 다음 링크를 참조하세요.

+ [디자이너 샘플](samples-designer.md): 디자이너를 사용하여 다른 유형의 문제를 해결하는 방법을 알아봅니다.
+ [Azure 가상 네트워크에서 Azure Machine Learning Studio를 사용](how-to-enable-studio-virtual-network.md)합니다.
