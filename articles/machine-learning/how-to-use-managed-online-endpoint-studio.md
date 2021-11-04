---
title: 스튜디오에서 관리형 온라인 엔드포인트(미리 보기) 사용
titleSuffix: Azure Machine Learning
description: Azure Machine Learning 스튜디오를 사용하여 관리형 온라인 엔드포인트(미리 보기)를 만들고 사용하는 방법을 알아봅니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: mlops
ms.topic: how-to
ms.custom: how-to, managed online endpoints, devplatv2, studio
ms.author: ssambare
author: shivanissambare
ms.reviewer: laobri
ms.date: 10/21/2021
ms.openlocfilehash: fee2a8211b90c2b7dbc06a1e64f047e28031eaa6
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/04/2021
ms.locfileid: "131560383"
---
# <a name="create-and-use-managed-online-endpoints-preview-in-the-studio"></a>스튜디오에서 관리형 온라인 엔드포인트(미리 보기) 만들기 및 사용

스튜디오를 사용하여 Azure Machine Learning에서 관리형 온라인 엔드포인트(미리 보기)를 만들고 관리하는 방법을 알아봅니다. 관리형 온라인 엔드포인트를 사용하여 프로덕션 규모 배포를 간소화합니다. 관리형 온라인 엔드포인트에 대한 자세한 내용은 [엔드포인트란?](concept-endpoints.md)을 참조하세요.

이 문서에서는 다음 방법을 설명합니다.

> [!div class="checklist"]
> * 관리형 온라인 엔드포인트 만들기
> * 관리형 온라인 엔드포인트 보기
> * 관리 되는 온라인 끝점에 배포 추가
> * 관리형 온라인 엔드포인트 업데이트
> * 관리형 온라인 엔드포인트 및 배포 삭제

[!INCLUDE [preview disclaimer](../../includes/machine-learning-preview-generic-disclaimer.md)]

## <a name="prerequisites"></a>사전 요구 사항
- Azure Machine Learning 작업 영역 자세한 내용은 [Azure Machine Learning 작업 영역 만들기](how-to-manage-workspace.md)를 참조하세요.
- 예제 리포지토리- [AzureML 예제 리포지토리](https://github.com/Azure/azureml-examples)를 복제 합니다. 이 문서에서는 `/cli/endpoints/online`의 자산을 사용합니다.

## <a name="create-a-managed-online-endpoint-preview"></a>관리형 온라인 엔드포인트(미리 보기) 만들기

스튜디오를 사용하여 브라우저에서 직접 관리형 온라인 엔드포인트(미리 보기)를 만듭니다. 스튜디오에서 관리형 온라인 엔드포인트를 만들 때 초기 배포를 정의해야 합니다. 비어 있는 관리형 온라인 엔드포인트를 만들 수 없습니다.

1. [Azure Machine Learning Studio](https://ml.azure.com)로 이동합니다.
1. 왼쪽 탐색 모음에서 **엔드포인트** 페이지를 선택합니다.
1. **+ 만들기(미리 보기)** 를 선택합니다.

:::image type="content" source="media/how-to-create-managed-online-endpoint-studio/endpoint-create-managed-online-endpoint.png" lightbox="media/how-to-create-managed-online-endpoint-studio/endpoint-create-managed-online-endpoint.png" alt-text="끝점 탭에서 관리 되는 온라인 끝점을 만들기 위한 스크린샷":::

:::image type="content" source="media/how-to-create-managed-online-endpoint-studio/online-endpoint-wizard.png" lightbox="media/how-to-create-managed-online-endpoint-studio/online-endpoint-wizard.png" alt-text="관리 되는 온라인 끝점 만들기 마법사의 스크린샷":::

### <a name="follow-the-setup-wizard-to-configure-your-managed-online-endpoint"></a>설치 마법사에 따라 관리형 온라인 엔드포인트를 구성합니다.

1. 다음에서 샘플 [모델](https://github.com/Azure/azureml-examples/tree/main/cli/endpoints/online/model-1/model) 및 [점수 매기기 스크립트](https://github.com/Azure/azureml-examples/blob/main/cli/endpoints/online/model-1/onlinescoring/score.py) 를 사용할 수 있습니다. [https://github.com/Azure/azureml-examples/tree/main/cli/endpoints/online/model-1](https://github.com/Azure/azureml-examples/tree/main/cli/endpoints/online/model-1)
1. 마법사의 **환경** 단계에서 **0.24.1-ubuntu 18.04-py37** 큐 레이트 Environment를 선택할 수 있습니다.

스튜디오의 **모델** 페이지에서 관리형 온라인 엔드포인트를 만들 수도 있습니다. 이 방법을 사용하면 기존의 관리형 온라인 배포에 모델을 쉽게 추가할 수 있습니다.

1. [Azure Machine Learning Studio](https://ml.azure.com)로 이동합니다.
1. 왼쪽 탐색 모음에서 **모델** 페이지를 선택합니다.
1. 모델 이름 옆에 있는 원을 선택하여 모델을 선택합니다.
1.   >  **실시간 끝점에 배포 배포 (미리 보기)를** 선택 합니다.

:::image type="content" source="media/how-to-create-managed-online-endpoint-studio/deploy-from-models-page.png" lightbox="media/how-to-create-managed-online-endpoint-studio/deploy-from-models-page.png" alt-text="모델 UI에서 관리 되는 온라인 끝점을 만드는 스크린샷":::

## <a name="view-managed-online-endpoints-preview"></a>관리형 온라인 엔드포인트(미리 보기) 보기

**엔드포인트** 페이지에서 관리형 온라인 엔드포인트(미리 보기)를 볼 수 있습니다. 엔드포인트 세부 정보 페이지를 사용하여 엔드포인트 URI, 상태, 테스트 도구, 작업 모니터, 배포 로그 및 샘플 사용 코드를 비롯한 중요한 정보를 찾을 수 있습니다.

1. 왼쪽 탐색 모음에서 **엔드포인트** 를 선택합니다.
1. (선택 사항) **관리형** 컴퓨팅 유형만 표시하도록 **컴퓨팅 유형** 에 대한 **필터** 를 만듭니다.
1. 엔드포인트 이름을 선택하여 엔드포인트 세부 정보 페이지를 봅니다.

:::image type="content" source="media/how-to-create-managed-online-endpoint-studio/managed-endpoint-details-page.png" lightbox="media/how-to-create-managed-online-endpoint-studio/managed-endpoint-details-page.png" alt-text="관리 되는 끝점 세부 정보 보기의 스크린샷":::

### <a name="test"></a>테스트

엔드포인트 세부 정보 페이지의 **테스트** 탭을 사용하여 관리형 온라인 배포를 테스트합니다. 샘플 입력을 입력하고 결과를 봅니다.

1. 엔드포인트의 세부 정보 페이지에서 **테스트** 탭을 선택합니다.
1. 드롭다운을 사용하여 테스트하려는 배포를 선택합니다.
1. 샘플 입력을 입력합니다.
1. **테스트** 를 선택합니다.

:::image type="content" source="media/how-to-create-managed-online-endpoint-studio/test-deployment.png" lightbox="media/how-to-create-managed-online-endpoint-studio/test-deployment.png" alt-text="브라우저에서 직접 샘플 데이터를 제공 하 여 배포를 테스트 하는 스크린샷":::

### <a name="monitoring"></a>모니터링

**모니터링** 탭을 사용하여 관리형 온라인 엔드포인트에 대한 상위 수준 작업 모니터 그래프를 볼 수 있습니다.

모니터링 탭을 사용하려면 엔드포인트를 만들 때 "**Application Insight 진단 및 데이터 수집 사용**"을 선택해야 합니다.

:::image type="content" source="media/how-to-create-managed-online-endpoint-studio/monitor-endpoint.png" lightbox="media/how-to-create-managed-online-endpoint-studio/monitor-endpoint.png" alt-text="스튜디오에서 끝점 수준 메트릭을 모니터링 하는 스크린샷":::

추가 모니터 및 경고를 보는 방법에 대한 자세한 내용은 [관리형 온라인 엔드포인트를 모니터링하는 방법](how-to-monitor-online-endpoints.md)을 참조하세요.

## <a name="add-a-deployment-to-a-managed-online-endpoint"></a>관리 되는 온라인 끝점에 배포 추가

기존 관리 되는 온라인 끝점에 배포를 추가할 수 있습니다.

**끝점 세부 정보 페이지** 에서

1. [엔드포인트 세부 정보 페이지](#view-managed-online-endpoints-preview)에서 **+ 배포 추가** 단추를 선택합니다.
2. 지침에 따라 배포를 완료합니다.

:::image type="content" source="media/how-to-create-managed-online-endpoint-studio/add-deploy-option-from-endpoint-page.png" lightbox="media/how-to-create-managed-online-endpoint-studio/add-deploy-option-from-endpoint-page.png" alt-text="끝점 세부 정보 페이지에서 배포 추가 옵션의 스크린샷":::

또는 **모델** 페이지를 사용하여 배포를 추가할 수 있습니다.

1. 왼쪽 탐색 모음에서 **모델** 페이지를 선택합니다.
1. 모델 이름 옆에 있는 원을 선택하여 모델을 선택합니다.
1.   >  **실시간 끝점에 배포 배포 (미리 보기)를** 선택 합니다.
1. 기존의 관리형 온라인 엔드포인트에 배포하도록 선택합니다.

:::image type="content" source="media/how-to-create-managed-online-endpoint-studio/select-existing-managed-endpoints.png" lightbox="media/how-to-create-managed-online-endpoint-studio/select-existing-managed-endpoints.png" alt-text="모델 페이지에서 배포 추가 옵션의 스크린샷":::

> [!NOTE]
> 새 배포를 추가할 때 엔드포인트에서 배포 간의 트래픽 균형을 조정할 수 있습니다.
>
> :::image type="content" source="media/how-to-create-managed-online-endpoint-studio/adjust-deployment-traffic.png" lightbox="media/how-to-create-managed-online-endpoint-studio/adjust-deployment-traffic.png" alt-text="슬라이더를 사용 하 여 여러 배포에서 트래픽 분산을 제어 하는 방법에 대 한 스크린샷":::

## <a name="update-managed-online-endpoints-preview"></a>관리형 온라인 엔드포인트(미리 보기) 업데이트

Azure Machine Learning studio에서 배포 트래픽 비율 및 인스턴스 수를 업데이트할 수 있습니다.

### <a name="update-deployment-traffic-allocation"></a>배포 트래픽 할당 업데이트

**배포 트래픽 할당** 을 사용하여 들어오는 요청 중에서 엔드포인트의 각 배포로 이동하는 비율을 제어합니다.

1. 엔드포인트 세부 정보 페이지에서 **트래픽 업데이트** 를 선택합니다.
2. 트래픽을 조정하고 **업데이트** 를 선택합니다.

> [!TIP]
> **총 트래픽 백분율** 의 집계가 0%(트래픽 사용 안 함) 또는 100%(트래픽 사용)여야 합니다.

### <a name="update-deployment-instance-count"></a>배포 인스턴스 수 업데이트

다음 지침에 따라 인스턴스 수를 조정하여 개별 배포를 스케일 업 또는 다운합니다.

1. 엔드포인트 세부 정보 페이지에서 다음을 수행합니다. 업데이트하려는 배포의 카드를 찾습니다.
1. 배포 세부 정보 카드에서 **편집 아이콘** 을 선택합니다.
1. 인스턴스 수를 업데이트합니다.
1. **업데이트** 를 선택합니다.

## <a name="delete-managed-online-endpoints-and-deployments-preview"></a>관리형 온라인 엔드포인트 및 배포(미리 보기) 삭제

전체 관리형 온라인 엔드포인트(미리 보기) 및 연결된 배포(미리 보기)를 삭제하는 방법을 알아봅니다. 또는 관리형 온라인 엔드포인트에서 개별 배포를 삭제합니다.

### <a name="delete-a-managed-online-endpoint"></a>관리형 온라인 엔드포인트 삭제

관리형 온라인 엔드포인트를 삭제하면 연결된 배포도 삭제됩니다.

1. [Azure Machine Learning Studio](https://ml.azure.com)로 이동합니다.
1. 왼쪽 탐색 모음에서 **엔드포인트** 페이지를 선택합니다.
1. 모델 이름 옆에 있는 원을 선택하여 엔드포인트를 선택합니다.
1. **삭제** 를 선택합니다.

또는 [엔드포인트 세부 정보 페이지](#view-managed-online-endpoints-preview)에서 직접 관리형 온라인 엔드포인트를 삭제할 수도 있습니다. 

### <a name="delete-an-individual-deployment"></a>개별 배포 삭제

다음 단계에 따라 관리형 온라인 엔드포인트에서 개별 배포를 삭제할 수 있습니다. 이렇게 해도 관리형 온라인 엔드포인트의 다른 배포에는 영향을 주지 않습니다.

> [!NOTE]
> 트래픽이 할당된 배포는 삭제할 수 없습니다. 배포를 삭제하려면 먼저 배포에 대한 [트래픽 할당을 0%로 설정](#update-deployment-traffic-allocation)해야 합니다.

1. [Azure Machine Learning Studio](https://ml.azure.com)로 이동합니다.
1. 왼쪽 탐색 모음에서 **엔드포인트** 페이지를 선택합니다.
1. 관리형 온라인 엔드포인트를 선택합니다.
1. 엔드포인트 세부 정보 페이지에서 삭제하려는 배포를 찾습니다.
1. **삭제 아이콘** 을 선택합니다.

## <a name="next-steps"></a>다음 단계

이 문서에서는 Azure Machine Learning 관리형 온라인 엔드포인트를 사용하는 방법을 알아보았습니다. 다음 단계를 살펴보세요.

- [엔드포인트란?](concept-endpoints.md)
- [Azure CLI를 사용하여 관리형 온라인 엔드포인트를 배포하는 방법](how-to-deploy-managed-online-endpoints.md)
- [REST를 사용하여 모델 배포(미리 보기)](how-to-deploy-with-rest.md)
- [관리형 온라인 엔드포인트를 모니터링하는 방법](how-to-monitor-online-endpoints.md)
- [관리형 온라인 엔드포인트 배포 및 채점(미리 보기) 문제 해결](./how-to-troubleshoot-online-endpoints.md)
- [Azure Machine Learning 관리형 온라인 엔드포인트(미리 보기)에 대한 비용 보기](how-to-view-online-endpoints-costs.md)
- [Azure Machine Learning을 사용하여 리소스 할당량 관리 및 증가](how-to-manage-quotas.md#azure-machine-learning-managed-online-endpoints-preview)