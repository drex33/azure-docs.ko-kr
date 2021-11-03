---
title: 보안 Azure Purview 계정에 액세스
description: Azure Data Factory에서 개인 끝점을 통해 방화벽으로 보호 되는 Azure 부서의 범위 계정에 액세스 하는 방법에 대해 알아봅니다.
ms.author: jingwang
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.custom:
- seo-lt-2019
- references_regions
ms.date: 09/02/2021
ms.openlocfilehash: 51da9cba732c5654709d0c1e84398035d4675cc8
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131005730"
---
# <a name="access-a-secured-azure-purview-account-from-azure-data-factory"></a>Azure Data Factory에서 보안 Azure 부서의 범위 계정에 액세스

이 문서에서는 다양 한 통합 시나리오에 대 한 Azure Data Factory에서 보안 Azure 부서의 범위 계정에 액세스 하는 방법을 설명 합니다.

## <a name="azure-purview-private-endpoint-deployment-scenarios"></a>Azure Purview 프라이빗 엔드포인트 배포 시나리오

Azure 부서의 범위 계정에 대 한 [azure 개인 끝점](../private-link/private-endpoint-overview.md) 을 사용 하 여 개인 링크를 통해 VNet (가상 네트워크)에서 카탈로그로 안전 하 게 액세스할 수 있습니다. 부서의 범위는 *계정* 개인 끝점, *포털* 개인 *끝점 및 수집 개인 끝점 등 다양* 한 액세스 요구 사항에 대해 다양 한 유형의 개인 요소를 제공 합니다. [부서의 범위 개인 끝점 개념 개요](../purview/catalog-private-link.md#conceptual-overview)에서 자세히 알아보세요. 

부서의 범위 계정이 방화벽으로 보호 되 고 공용 액세스를 거부 하는 경우, 부서의 범위에 성공적으로 연결할 수 Data Factory 있도록 개인 끝점을 설정 하는 아래 검사 목록을 따라야 합니다. 

| 시나리오                                                     | 필수 부서의 범위 개인 끝점                           |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| [파이프라인 및 보고서 계보를 실행 하 여 부서의 범위](tutorial-push-lineage-to-purview.md) | 부서의 범위에 계보를 푸시하는 Data Factory 파이프라인의 경우 부서의 범위 ***account** _ _*_및 수집_*_ 전용 끝점이 필요 합니다. <br>-_ * Azure Integration Runtime * *를 사용 하는 경우 관리 되는 [개인 끝점의 부서의 범위](#managed-private-endpoints-for-purview) 섹션에 있는 단계에 따라 관리 되는 Data Factory 가상 네트워크에 관리 되는 개인 끝점을 만듭니다.<br>- **자체 호스팅 Integration Runtime** 를 사용 하는 경우 [이 섹션](../purview/catalog-private-link-end-to-end.md#option-2---enable-account-portal-and-ingestion-private-endpoint-on-existing-azure-purview-accounts) 의 단계에 따라 *계정을* 만들고 통합 런타임의 가상 네트워크 *에서 개인 끝점을 수집* 합니다. |
| [ADF UI에서 부서의 범위를 사용 하 여 데이터 검색 및 탐색](how-to-discover-explore-purview-data.md) | Data Factory authoring UI의 위쪽 가운데에 있는 검색 표시줄을 사용 하 여 부서의 범위 데이터를 검색 하 고 작업을 수행 하려면 Data Factory Studio를 실행 하는 가상 네트워크에서 부서의 범위 ***account** _ 및 _*_portal_*_ 개인 끝점을 만들어야 합니다. [_Account * 및 *포털* 개인 끝점 사용](../purview/catalog-private-link-account-portal.md#option-2---enable-account-and-portal-private-endpoint-on-existing-azure-purview-accounts)의 단계를 따릅니다. |

## <a name="managed-private-endpoints-for-purview"></a>부서의 범위에 대 한 관리 되는 개인 끝점

[관리 되는 개인 끝점](managed-virtual-network-private-endpoint.md#managed-private-endpoints) 은 Azure 리소스에 대 한 개인 링크를 설정 하는 Virtual Network Azure Data Factory 관리 되는 전용 끝점입니다. 방화벽으로 보호 되는 Azure 부서의 범위 계정에 파이프라인 및 보고서 계보를 실행 하는 경우 "가상 네트워크 구성" 옵션을 사용 하도록 설정 된 Azure Integration Runtime 만든 다음 부서의 범위 ***account** _ *_및 _ 수집 * 관리_* 되는 개인 끝점을 다음과 같이 만듭니다.

### <a name="create-managed-private-endpoints"></a>관리 되는 개인 끝점 만들기

부서의 범위 on Data Factory authoring UI에 대 한 관리 되는 개인 끝점을 만들려면:

1. Azure 부서의 범위 **관리** 로 이동 하  ->  고 **편집** 을 클릭 하 여 기존에 연결 된 부서의 범위 계정을 편집 하거나, **부서의 범위 계정으로 커넥트** 를 클릭 하 여 새 부서의 범위 계정에 연결 합니다.

2. **관리 되는 개인 끝점 만들기** 에 대해 **예** 를 선택 합니다. 이 옵션을 보려면 data factory에서 "가상 네트워크 구성" 옵션을 사용 하도록 설정 된 Azure Integration Runtime가 하나 이상 있어야 합니다.

3. **+ 모두 만들기** 단추를 클릭 하 여 **부서의 범위 관리 되는 리소스-Blob storage, Queue storage 및 Event Hubs 네임 스페이스에 대 한 _계정_*_ 개인 끝점 및 _* 수집 _ 개인 끝점을 포함 하 여 필요한 부서의 범위 개인 끝점을 일괄 만듭니다 *.*** 부서의 범위 계정에는 부서의 범위 관리 되는 리소스의 정보를 검색 하는 데 Data Factory 사용할 수 있는 최소 _ 판독기 역할이 있어야 합니다.

   :::image type="content" source="./media/how-to-access-secured-purview-account/purview-create-all-managed-private-endpoints.png" alt-text="연결 된 부서의 범위 계정에 대 한 관리 되는 개인 끝점을 만듭니다.":::

4. 다음 페이지에서 개인 끝점의 이름을 지정 합니다. 접미사를 사용 하 여 수집 개인 끝점의 이름을 생성 하는 데에도 사용 됩니다.

   :::image type="content" source="./media/how-to-access-secured-purview-account/name-purview-private-endpoints.png" alt-text="연결 된 부서의 범위 계정에 대 한 관리 되는 개인 끝점의 이름을로 합니다.":::

5. **만들기** 를 클릭 하 여 개인 끝점을 만듭니다. 만든 후에는 [부서의 범위 소유자가 승인한](#approve-private-endpoint-connections)개인 끝점 요청이 4 개 생성 됩니다.

이러한 batch 관리 개인 끝점 생성은 부서의 범위 UI에만 제공 됩니다. 관리 되는 전용 끝점을 프로그래밍 방식으로 만들려면 이러한 Pe를 개별적으로 만들어야 합니다. 부서의 범위 관리 리소스의 정보는 부서의 범위 계정 > 관리 되는 리소스 Azure Portal >에서 찾을 수 있습니다.

### <a name="approve-private-endpoint-connections"></a>개인 끝점 연결 승인

부서의 범위에 대 한 관리 되는 개인 끝점을 만든 후 "보류 중" 상태가 먼저 표시 됩니다. 부서의 범위 소유자는 각 리소스에 대 한 개인 끝점 연결을 승인 해야 합니다.

부서의 범위 개인 끝점 연결을 승인할 수 있는 권한이 있는 경우 Data Factory UI에서 다음을 수행 합니다. 

1.   ->  **Azure 부서의 범위**  ->  **편집** 관리로 이동 합니다.
2. 개인 끝점 목록에서 각 개인 끝점 이름 옆의 **편집** (연필) 단추를 클릭 합니다.
3. **Azure Portal에서 승인 관리** 를 클릭 하 여 리소스로 이동 합니다.
4. 지정 된 리소스에서 **네트워킹**  ->  **개인 끝점 연결** 로 이동 하 여 승인 합니다. 개인 끝점의 이름은 설명으로 `data_factory_name.your_defined_private_endpoint_name` "data_factory_name에서 요청"으로 지정 됩니다.
5. 모든 개인 끝점에 대해이 작업을 반복 합니다.

부서의 범위 개인 끝점 연결을 승인할 수 있는 권한이 없는 경우 다음과 같이 부서의 범위 계정 소유자에 게 문의 합니다.

- *계정* 개인 끝점의 경우 승인 하려면 Azure Portal-> 부서의 범위 계정 > 네트워킹-> 개인 끝점 연결로 이동 합니다.
- 수집 *개인 끝점의 경우 Azure Portal* > 부서의 범위 계정 > 관리 되는 리소스로 이동 하 고, Storage 계정 및 Event Hubs 네임 스페이스를 각각 클릭 하 고, 네트워킹-> 개인 끝점 연결 페이지에서 개인 끝점 연결을 승인 합니다.

### <a name="monitor-managed-private-endpoints"></a>관리 되는 개인 끝점 모니터링

다음 두 위치에서 부서의 범위에 대해 생성 된 관리 되는 개인 끝점을 모니터링할 수 있습니다.

-   ->  **Azure 부서의 범위**  ->  **편집** 관리로 이동 하 여 기존에 연결 된 부서의 범위 계정을 엽니다. 모든 관련 개인 끝점을 보려면 부서의 범위 계정에 대 한 **읽기 권한자** 역할이 있어야 Data Factory 부서의 범위 관리 되는 리소스의 정보를 검색할 수 있습니다. 그렇지 않으면 *계정* 개인 끝점 (경고 포함)만 표시 됩니다.
- 관리 되는 개인 끝점 **관리** 로 이동 하 여  ->   데이터 팩터리에서 생성 된 관리 되는 모든 개인 끝점을 볼 수 있습니다. 부서의 범위 계정에 대 한 **독자** 역할이 하나 이상 있는 경우 관련 개인 끝점을 함께 그룹화 하는 부서의 범위 표시 됩니다. 그렇지 않으면 목록에 별도로 표시 됩니다.

## <a name="nextsteps"></a>다음 단계 

- [Azure Purview에 Data Factory 연결](connect-data-factory-to-azure-purview.md)
- [자습서: Data Factory 계보 데이터를 Azure Purview에 푸시](tutorial-push-lineage-to-purview.md)
- [Purview를 사용하여 ADF의 데이터 검색 및 살펴보기](how-to-discover-explore-purview-data.md)
