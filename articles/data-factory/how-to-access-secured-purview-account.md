---
title: 보안 Azure Purview 계정에 액세스
description: Azure Data Factory 프라이빗 엔드포인트를 통해 방화벽으로 보호되는 Azure Purview 계정에 액세스하는 방법에 대해 알아봅니다.
ms.author: jingwang
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.custom:
- seo-lt-2019
- references_regions
ms.date: 09/02/2021
ms.openlocfilehash: 32bf7849a8d15b455fc6028ac94009ebd46791e4
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/03/2021
ms.locfileid: "123441474"
---
# <a name="access-a-secured-azure-purview-account-from-azure-data-factory"></a>Azure Data Factory 보안 Azure Purview 계정에 액세스

이 문서에서는 다양한 통합 시나리오에 대해 Azure Data Factory 보안 Azure Purview 계정에 액세스하는 방법을 설명합니다.

## <a name="azure-purview-private-endpoint-deployment-scenarios"></a>Azure Purview 프라이빗 엔드포인트 배포 시나리오

Azure Purview 계정에 [Azure 프라이빗 엔드포인트를](../private-link/private-endpoint-overview.md) 사용하여 Private Link 통해 VNet(가상 네트워크)에서 카탈로그로 안전하게 액세스할 수 있습니다. Purview는 *계정* 프라이빗 엔드포인트, *포털* 프라이빗 엔드포인트 및 프라이빗 *엔드포인트의* 다양한 액세스 요구 사항에 대해 다양한 유형의 프라이빗 지점을 제공합니다. [Purview 프라이빗 엔드포인트 개념 개요](../purview/catalog-private-link.md#conceptual-overview)에서 자세히 알아보세요. 

Purview 계정이 방화벽으로 보호되고 공용 액세스를 거부하는 경우 아래 검사 목록에 따라 Data Factory Purview에 성공적으로 연결할 수 있도록 프라이빗 엔드포인트를 설정해야 합니다. 

| 시나리오                                                     | 필수 Purview 프라이빗 엔드포인트                           |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| [파이프라인 실행 및 Purview에 계보 보고](tutorial-push-lineage-to-purview.md) | Data Factory 파이프라인이 Purview에 계보를 푸시하려면 Purview ***account** _ 및 _*_ingestion_*_ 프라이빗 엔드포인트가 필요합니다. <br>- _*Azure Integration Runtime**를 사용하는 경우 [Purview에 대한 관리형 프라이빗 엔드포인트](#managed-private-endpoints-for-purview) 섹션의 단계에 따라 Data Factory 관리형 가상 네트워크에 관리형 프라이빗 엔드포인트를 만듭니다.<br>- **자체 호스팅 Integration Runtime** 사용하는 경우 이 [섹션의](../purview/catalog-private-link-end-to-end.md#option-2---enable-account-portal-and-ingestion-private-endpoint-on-existing-azure-purview-accounts) 단계에 따라 통합 런타임의 가상 네트워크에 *계정* 및 프라이빗 *엔드포인트를* 생성합니다. |
| [ADF UI에서 Purview를 사용하여 데이터 검색 및 탐색](how-to-discover-explore-purview-data.md) | Data Factory 제작 UI의 맨 위에 있는 검색 표시줄을 사용하여 Purview 데이터를 검색하고 작업을 수행하려면 Data Factory Studio를 시작하는 가상 네트워크에서 Purview ***계정** _ 및 _*_포털_*_ 프라이빗 엔드포인트를 만들어야 합니다. [_account* 및 *포털* 프라이빗 엔드포인트 사용의](../purview/catalog-private-link-account-portal.md#option-2---enable-account-and-portal-private-endpoint-on-existing-azure-purview-accounts)단계를 따릅니다. |

## <a name="managed-private-endpoints-for-purview"></a>Purview에 대한 관리되는 프라이빗 엔드포인트

[관리되는 프라이빗 엔드포인트는](managed-virtual-network-private-endpoint.md#managed-private-endpoints) Azure 리소스에 대한 프라이빗 링크를 설정하는 Azure Data Factory Managed Virtual Network 만든 프라이빗 엔드포인트입니다. 파이프라인을 실행하고 방화벽으로 보호된 Azure Purview 계정에 계보를 보고하는 경우 "가상 네트워크 구성" 옵션을 사용하도록 설정된 Azure Integration Runtime 만든 다음 다음과 같이 Purview ***계정** _ 및 _ *_ingestion_** 관리형 프라이빗 엔드포인트를 만듭니다.

### <a name="create-managed-private-endpoints"></a>관리형 프라이빗 엔드포인트 만들기

Data Factory 작성 UI에서 Purview에 대한 관리형 프라이빗 엔드포인트를 만들려면 다음을 수행합니다.

1. Azure Purview **관리로** 이동하고  ->   **편집을** 클릭하여 기존에 연결된 Purview 계정을 편집하거나 **Purview 계정에 커넥트** 클릭하여 새 Purview 계정에 연결합니다.

2. **관리형 프라이빗 엔드포인트 만들기** 에 대해 **예를** 선택합니다. 이 옵션을 보려면 데이터 팩터리에서 "가상 네트워크 구성" 옵션을 사용하는 Azure Integration Runtime 하나 이상 있어야 합니다.

3. **+ 모두 만들기** 단추를 클릭하여 Purview **관리형 리소스(Blob Storage, Queue Storage 및 Event Hubs 네임스페이스에 대한 _계정_*_ 프라이빗 엔드포인트 및 _*_ingestion_ _ 프라이빗 엔드포인트 포함)를 일괄 처리하여 필요한 Purview 프라이빗 *엔드포인트를 만듭니다.*** Purview 관리 리소스의 정보를 검색하려면 Data Factory Purview 계정에 대한 _ 읽기 역할 이상 있어야 합니다.

   :::image type="content" source="./media/how-to-access-secured-purview-account/purview-create-all-managed-private-endpoints.png" alt-text="연결된 Purview 계정에 대한 관리형 프라이빗 엔드포인트를 만듭니다.":::

4. 다음 페이지에서 프라이빗 엔드포인트의 이름을 지정합니다. 이 이름은 접미사뿐만 아니라 개인 엔드포인트를 검색하기 위한 이름을 생성하는 데 사용됩니다.

   :::image type="content" source="./media/how-to-access-secured-purview-account/name-purview-private-endpoints.png" alt-text="연결된 Purview 계정에 대한 관리형 프라이빗 엔드포인트의 이름을 지정합니다.":::

5. **만들기를** 클릭하여 프라이빗 엔드포인트를 만듭니다. 만든 후에 [Purview 소유자의 승인을 받아야](#approve-private-endpoint-connections)하는 4개의 프라이빗 엔드포인트 요청이 생성됩니다.

이러한 일괄 처리 관리 프라이빗 엔드포인트 만들기는 Data Factory UI에서만 제공됩니다. 관리형 프라이빗 엔드포인트를 프로그래밍 방식으로 만들려면 해당 PES를 개별적으로 만들어야 합니다. Purview 계정 -> 관리되는 리소스를 Azure Portal -> Purview 관리 리소스의 정보를 찾을 수 있습니다.

### <a name="approve-private-endpoint-connections"></a>프라이빗 엔드포인트 연결 승인

Purview에 대한 관리형 프라이빗 엔드포인트를 만든 후 먼저 "보류 중" 상태가 표시됩니다. Purview 소유자는 각 리소스에 대한 프라이빗 엔드포인트 연결을 승인해야 합니다.

Purview 프라이빗 엔드포인트 연결을 승인할 수 있는 권한이 있는 경우 Data Factory UI에서 다음을 수행합니다. 

1. Azure   ->  **Purview**  ->  **편집** 관리로 이동
2. 프라이빗 엔드포인트 목록에서 각  프라이빗 엔드포인트 이름 옆에 있는 편집(연필) 단추를 클릭합니다.
3. **Azure Portal 승인 관리를** 클릭하여 리소스로 가져옵니다.
4. 지정된 리소스에서 네트워킹   ->  **프라이빗 엔드포인트 연결로** 이동하여 승인합니다. 프라이빗 엔드포인트의 이름은 `data_factory_name.your_defined_private_endpoint_name` "data_factory_name 요청"이라는 설명과 함께 로 지정됩니다.
5. 모든 프라이빗 엔드포인트에 대해 이 작업을 반복합니다.

Purview 프라이빗 엔드포인트 연결을 승인할 수 있는 권한이 없는 경우 Purview 계정 소유자에게 다음과 같이 수행하도록 요청합니다.

- *계정* 프라이빗 엔드포인트의 경우 Azure Portal -> Purview 계정 -> 네트워킹 -> 프라이빗 엔드포인트 연결로 이동하여 승인합니다.
- 프라이빗 *엔드포인트를 Azure Portal* -> Purview 계정 -> 관리되는 리소스로 이동하고, Storage 계정 및 Event Hubs 네임스페이스를 각각 클릭하고, 네트워킹 -> 프라이빗 엔드포인트 연결 페이지에서 프라이빗 엔드포인트 연결을 승인합니다.

### <a name="monitor-managed-private-endpoints"></a>관리형 프라이빗 엔드포인트 모니터링

Purview에 대해 만든 관리형 프라이빗 엔드포인트를 다음 두 곳에서 모니터링할 수 있습니다.

- Azure   ->  **Purview**  ->  **편집** 관리로 이동하여 기존에 연결된 Purview 계정을 엽니다. 모든 관련 프라이빗 엔드포인트를 보려면 Purview 관리되는 리소스의 정보를 검색하려면 Data Factory Purview 계정에 대한 **읽기** 역할 이상의 읽기 역할이 있어야 합니다. 그렇지 않으면 경고가 있는 *계정* 프라이빗 엔드포인트만 표시됩니다.
-   ->  **관리되는 프라이빗 엔드포인트** 관리로 이동하면 데이터 팩터리에서 만든 관리되는 프라이빗 엔드포인트가 모두 표시됩니다. Purview 계정에 대한 **읽기** 역할 이상의 경우 Purview 관련 프라이빗 엔드포인트가 함께 그룹화되는 것을 볼 수 있습니다. 그렇지 않으면 목록에 별도로 표시됩니다.

## <a name="nextsteps"></a>다음 단계 

- [Azure Purview에 Data Factory 연결](connect-data-factory-to-azure-purview.md)
- [자습서: Data Factory 계보 데이터를 Azure Purview에 푸시](tutorial-push-lineage-to-purview.md)
- [Purview를 사용하여 ADF의 데이터 검색 및 살펴보기](how-to-discover-explore-purview-data.md)