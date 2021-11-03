---
title: 스튜디오에서 관리형 온라인 엔드포인트(미리 보기) 사용
titleSuffix: Azure Machine Learning
description: Azure Machine Learning 스튜디오를 사용하여 관리형 온라인 엔드포인트(미리 보기)를 만들고 사용하는 방법을 알아봅니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: mlops
ms.topic: how-to
ms.custom: how-to, managed online endpoints, devplatv2
ms.author: ssambare
author: shivanissambare
ms.reviewer: peterlu
ms.date: 05/25/2021
ms.openlocfilehash: d195ce6019f200b3cd3b264aacf09a8cbdb30c5f
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131057593"
---
# <a name="create-and-use-managed-online-endpoints-preview-in-the-studio"></a>스튜디오에서 관리형 온라인 엔드포인트(미리 보기) 만들기 및 사용

스튜디오를 사용하여 Azure Machine Learning에서 관리형 온라인 엔드포인트(미리 보기)를 만들고 관리하는 방법을 알아봅니다. 관리형 온라인 엔드포인트를 사용하여 프로덕션 규모 배포를 간소화합니다. 관리형 온라인 엔드포인트에 대한 자세한 내용은 [엔드포인트란?](concept-endpoints.md)을 참조하세요.

이 문서에서는 다음 방법을 설명합니다.

> [!div class="checklist"]
> * 관리형 온라인 엔드포인트 만들기
> * 관리형 온라인 엔드포인트 보기
> * 관리형 온라인 엔드포인트 업데이트
> * 관리형 온라인 엔드포인트 및 배포 삭제

[!INCLUDE [preview disclaimer](../../includes/machine-learning-preview-generic-disclaimer.md)]

## <a name="prerequisites"></a>사전 요구 사항

- Azure Machine Learning 작업 영역 자세한 내용은 [Azure Machine Learning 작업 영역 만들기](how-to-manage-workspace.md)를 참조하세요.
- [작업 영역에 등록된 모델](how-to-deploy-and-where.md#registermodel)
- 배포된 모델의 채점 파일. 모델을 등록하고 채점 파일을 만드는 방법에 대한 단계별 예제는 [자습서: 이미지 분류](tutorial-train-models-with-aml.md)를 참조하세요.
- 작업 영역에 등록된 사용자 지정 환경 **또는** Python 환경을 사용하는 Docker 컨테이너 레지스트리 이미지. 환경에 대한 자세한 내용은 [Azure Machine Learning에서 소프트웨어 환경 만들기 및 사용](how-to-use-environments.md)을 참조하세요.

## <a name="create-a-managed-online-endpoint-preview"></a>관리형 온라인 엔드포인트(미리 보기) 만들기

스튜디오를 사용하여 브라우저에서 직접 관리형 온라인 엔드포인트(미리 보기)를 만듭니다. 스튜디오에서 관리형 온라인 엔드포인트를 만들 때 초기 배포를 정의해야 합니다. 비어 있는 관리형 온라인 엔드포인트를 만들 수 없습니다.

1. [Azure Machine Learning Studio](https://ml.azure.com)로 이동합니다.
1. 왼쪽 탐색 모음에서 **엔드포인트** 페이지를 선택합니다.
1. **+ 만들기(미리 보기)** 를 선택합니다.

:::image type="content" source="media/how-to-create-managed-online-endpoint-studio/endpoint-create-managed-online-endpoint.png" alt-text="[엔드포인트] 탭에서 관리형 온라인 엔드포인트 만들기":::

스튜디오의 **모델** 페이지에서 관리형 온라인 엔드포인트를 만들 수도 있습니다. 이 방법을 사용하면 기존의 관리형 온라인 배포에 모델을 쉽게 추가할 수 있습니다.

1. [Azure Machine Learning Studio](https://ml.azure.com)로 이동합니다.
1. 왼쪽 탐색 모음에서 **모델** 페이지를 선택합니다.
1. 모델 이름 옆에 있는 원을 선택하여 모델을 선택합니다.
1. **배포** > **엔드포인트에 배포(미리 보기)** 를 선택합니다.

설치 마법사에 따라 관리형 온라인 엔드포인트를 구성합니다.

:::image type="content" source="media/how-to-create-managed-online-endpoint-studio/models-page-deployment-latest.png" alt-text="[모델] 탭에서 관리형 온라인 엔드포인트 만들기":::

## <a name="view-managed-online-endpoints-preview"></a>관리형 온라인 엔드포인트(미리 보기) 보기

**엔드포인트** 페이지에서 관리형 온라인 엔드포인트(미리 보기)를 볼 수 있습니다. 엔드포인트 세부 정보 페이지를 사용하여 엔드포인트 URI, 상태, 테스트 도구, 작업 모니터, 배포 로그 및 샘플 사용 코드를 비롯한 중요한 정보를 찾을 수 있습니다.

1. 왼쪽 탐색 모음에서 **엔드포인트** 를 선택합니다.
1. (선택 사항) **관리형** 컴퓨팅 유형만 표시하도록 **컴퓨팅 유형** 에 대한 **필터** 를 만듭니다.
1. 엔드포인트 이름을 선택하여 엔드포인트 세부 정보 페이지를 봅니다.

### <a name="test"></a>테스트

엔드포인트 세부 정보 페이지의 **테스트** 탭을 사용하여 관리형 온라인 배포를 테스트합니다. 샘플 입력을 입력하고 결과를 봅니다.

1. 엔드포인트의 세부 정보 페이지에서 **테스트** 탭을 선택합니다.
1. 드롭다운을 사용하여 테스트하려는 배포를 선택합니다.
1. 샘플 입력을 입력합니다.
1. **테스트** 를 선택합니다.

:::image type="content" source="media/how-to-create-managed-online-endpoint-studio/test-deployment.png" alt-text="브라우저에서 직접 샘플 데이터를 제공하여 배포 테스트":::

### <a name="monitoring"></a>모니터링

**모니터링** 탭을 사용하여 관리형 온라인 엔드포인트에 대한 상위 수준 작업 모니터 그래프를 볼 수 있습니다.

모니터링 탭을 사용하려면 엔드포인트를 만들 때 "**Application Insight 진단 및 데이터 수집 사용**"을 선택해야 합니다.

:::image type="content" source="media/how-to-create-managed-online-endpoint-studio/monitor-endpoint.png" alt-text="스튜디오에서 엔드포인트 수준 메트릭 모니터링":::

추가 모니터 및 경고를 보는 방법에 대한 자세한 내용은 [관리형 온라인 엔드포인트를 모니터링하는 방법](how-to-monitor-online-endpoints.md)을 참조하세요.

## <a name="update-managed-online-endpoints-preview"></a>관리형 온라인 엔드포인트(미리 보기) 업데이트

관리형 온라인 엔드포인트(미리 보기)를 업데이트하여 배포를 더 추가하고 트래픽 할당을 조정하는 방법을 알아봅니다.

### <a name="add-a-managed-online-deployment"></a>관리형 온라인 배포 추가

다음 지침을 사용하여 기존의 관리형 온라인 엔드포인트에 배포를 추가합니다.

1. [엔드포인트 세부 정보 페이지](#view-managed-online-endpoints-preview)에서 **+ 배포 추가** 단추를 선택합니다.
2. 지침에 따라 배포를 완료합니다.

또는 **모델** 페이지를 사용하여 배포를 추가할 수 있습니다.

1. 왼쪽 탐색 모음에서 **모델** 페이지를 선택합니다.
1. 모델 이름 옆에 있는 원을 선택하여 모델을 선택합니다.
1. **배포** > **엔드포인트에 배포(미리 보기)** 를 선택합니다.
1. 기존의 관리형 온라인 엔드포인트에 배포하도록 선택합니다.

> [!NOTE]
> 새 배포를 추가할 때 엔드포인트에서 배포 간의 트래픽 균형을 조정할 수 있습니다.
>
> :::image type="content" source="media/how-to-create-managed-online-endpoint-studio/adjust-deployment-traffic.png" alt-text="슬라이더를 사용하여 여러 배포 간의 트래픽 분산 제어":::

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