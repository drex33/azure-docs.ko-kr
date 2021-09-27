---
title: 관리형 온라인 엔드포인트에 대한 비용 보기(미리 보기)
titleSuffix: Azure Machine Learning
description: Azure Machine Learning에서 관리형 온라인 엔드포인트에 대한 비용을 확인하는 방법을 알아봅니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.date: 05/03/2021
ms.topic: conceptual
ms.custom: how-to, deploy, devplatv2
ms.openlocfilehash: bb07d499007cbbc0cb502611056aaef583cbb16a
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128588811"
---
# <a name="view-costs-for-an-azure-machine-learning-managed-online-endpoint-preview"></a>Azure Machine Learning 관리형 온라인 엔드포인트(미리 보기)에 대한 비용 보기

관리형 온라인 엔드포인트(미리 보기)에 대한 비용을 확인하는 방법을 알아봅니다. 엔드포인트에 대한 비용은 연결된 작업 영역에 따라 누적됩니다. 태그를 사용하여 특정 엔드포인트에 대한 비용을 볼 수 있습니다.

[!INCLUDE [preview disclaimer](../../includes/machine-learning-preview-generic-disclaimer.md)]

> [!IMPORTANT]
> 이 문서는 Azure Machine Learning 관리형 온라인 엔드포인트(미리 보기)에 대한 비용을 보는 데만 적용됩니다. 관리형 온라인 엔드포인트는 태그를 사용하여 비용을 추적해야 하므로 다른 리소스와 다릅니다. 다른 Azure 리소스의 비용을 확인하는 방법에 대한 자세한 내용은 [빠른 시작: 비용 분석을 통한 비용 탐색 및 분석](../cost-management-billing/costs/quick-acm-cost-analysis.md)을 참조하세요.

## <a name="prerequisites"></a>전제 조건

- Azure Machine Learning 관리형 온라인 엔드포인트(미리 보기)를 배포합니다.
- 엔드포인트가 배포된 구독에 대해 [청구 읽기 권한자](../role-based-access-control/role-assignments-portal.md) 이상의 액세스 권한이 있어야 합니다.

## <a name="view-costs"></a>비용 보기

구독에 대한 **비용 분석** 페이지로 이동합니다.

1. [Azure Portal](https://portal.azure.com)에서 구독에 대한 **비용 분석** 을 선택합니다.

    [![관리형 온라인 엔드포인트 비용 분석: 왼쪽의 "비용 분석" 단추 주위에 빨간색 상자가 표시된 Azure Portal의 구독 스크린샷.](./media/how-to-view-online-endpoints-costs/online-endpoints-cost-analysis.png)](./media/how-to-view-online-endpoints-costs/online-endpoints-cost-analysis.png#lightbox)

Azure Machine learning 작업 영역 리소스로 데이터 범위를 지정하는 필터를 만듭니다.

1. 왼쪽 탐색 모음에서 **필터 추가** 를 선택합니다.

1. 첫 번째 필터 드롭다운에서 필터 형식에 대해 **리소스** 를 선택합니다.

1. 두 번째 필터 드롭다운에서 Azure Machine Learning 작업 영역을 선택합니다.

    [![관리형 온라인 엔드포인트 비용 분석: 오른쪽 위에 있는 "필터 추가" 단추 주위에 빨간색 상자가 표시된 비용 분석 보기의 스크린샷.](./media/how-to-view-online-endpoints-costs/online-endpoints-cost-analysis-add-filter.png)](./media/how-to-view-online-endpoints-costs/online-endpoints-cost-analysis-add-filter.png#lightbox)

태그 필터를 만들어 관리형 온라인 엔드포인트 및/또는 관리형 온라인 배포를 표시합니다.
1. **필터 추가** > **태그** > **azuremlendpoint**: "\<your endpoint name>"을 선택합니다. 
1. **필터 추가** > **태그** > **azuremldeployment**: "\<your deployment name>"을 선택합니다.

    > [!NOTE]
    > 이 이미지의 달러 값은 가상이며 실제 비용을 반영하지 않습니다.

    [![관리형 온라인 엔드포인트 비용 분석: 오른쪽 위에 있는 "태그" 단추 주위에 빨간색 상자가 표시된 비용 분석 보기의 스크린샷.](./media/how-to-view-online-endpoints-costs/online-endpoints-cost-analysis-select-endpoint-deployment.png)](./media/how-to-view-online-endpoints-costs/online-endpoints-cost-analysis-select-endpoint-deployment.png#lightbox)

## <a name="next-steps"></a>다음 단계
- [엔드포인트란?](concept-endpoints.md)
- [관리형 온라인 엔드포인트](./how-to-monitor-online-endpoints.md)를 모니터링하는 방법을 알아봅니다.
- [Azure CLI를 사용하여 관리형 온라인 엔드포인트를 배포하는 방법](how-to-deploy-managed-online-endpoints.md)
- [스튜디오를 사용하여 관리형 온라인 엔드포인트를 배포하는 방법](how-to-use-managed-online-endpoint-studio.md)