---
title: Azure Synapse 연결 설정
description: Azure Synapse Analytics에서 연결 설정을 구성하는 방법을 설명하는 문서
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: security
ms.date: 08/05/2021
author: ashinMSFT
ms.author: seshin
ms.reviewer: jrasnick, wiassaf
ms.openlocfilehash: 58086bcbe321b7d50d6f5d0e41a9723dfbe512ed
ms.sourcegitcommit: 6c6b8ba688a7cc699b68615c92adb550fbd0610f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "121861653"
---
# <a name="azure-synapse-analytics-connectivity-settings"></a>Azure Synapse Analytics 연결 설정

이 문서에서는 Azure Synapse Analytics의 연결 설정 및 해당하는 경우 이를 구성하는 방법을 설명합니다.

## <a name="public-network-access"></a>공용 네트워크 액세스 

공용 네트워크 액세스 기능을 사용하여 Azure Synapse 작업 영역에 대해 들어오는 공용 네트워크 연결을 허용할 수 있습니다. 

- 공용 네트워크 액세스가 **사용 안 함** 인 경우 [프라이빗 엔드포인트](synapse-workspace-managed-private-endpoints.md)를 통해서만 작업 영역에 연결할 수 있습니다. 
- 공용 네트워크 액세스가 **사용** 인 경우 공용 네트워크에서도 작업 영역에 연결할 수 있습니다. 작업 영역을 만드는 동안과 그 이후에 이 기능을 관리할 수 있습니다. 

> [!IMPORTANT]
> 이 기능은 [Azure Synapse Analytics 관리형 가상 네트워크](synapse-workspace-managed-vnet.md)에 연결된 Azure Synapse 작업 영역에서만 사용할 수 있습니다. 그러나 관리형 VNet과의 연결과 무관하게, Synapse 작업 영역을 공용 네트워크에 열 수는 있습니다. 

**사용 안 함** 옵션을 선택하면 사용자가 구성한 방화벽 규칙이 적용되지 않습니다. 또한 방화벽 규칙이 Synapse 포털의 네트워크 설정에서 회색으로 표시됩니다. 공용 네트워크 액세스를 다시 사용하도록 설정하면 방화벽 구성이 다시 적용됩니다. 

> [!TIP]
> 사용으로 되돌리면 방화벽 규칙 편집 전에 잠시 기다립니다.

### <a name="configure-public-network-access-when-you-create-your-workspace"></a>작업 영역을 만들 때 공용 네트워크 액세스 구성

1.    [Azure Portal](https://aka.ms/azureportal)에서 작업 영역을 만들 때 **네트워킹** 탭을 선택합니다.
2.    관리형 가상 네트워크에서 **사용** 을 선택하여 작업 영역을 관리형 가상 네트워크에 연결하고 공용 네트워크 액세스를 허용합니다. 

       :::image type="content" source="./media/connectivity-settings/create-synapse-workspace-managed-virtual-network-1.png" alt-text="Synapse 작업 영역 만들기, 네트워킹 탭, 관리형 가상 네트워크 설정" lightbox="media/connectivity-settings/create-synapse-workspace-managed-virtual-network-1.png":::

3. **공용 네트워크 액세스** 에서 **사용 안 함** 을 선택하여 작업 영역에 대한 공용 액세스를 거부합니다. 작업 영역에 대한 공용 액세스를 허용하려면 **사용** 을 선택합니다.

   :::image type="content" source="./media/connectivity-settings/create-synapse-workspace-public-network-access-2.png" alt-text="Synapse 작업 영역 만들기, 네트워킹 탭, 공용 네트워크 액세스 설정" lightbox="media/connectivity-settings/create-synapse-workspace-public-network-access-2.png"::: 

4.    작업 영역 만들기 흐름의 나머지 단계를 완료합니다.

### <a name="configure-public-network-access-after-you-create-your-workspace"></a>작업 영역을 만든 뒤 공용 네트워크 액세스 구성

1.    [Azure Portal](https://aka.ms/azureportal)에서 Synapse 작업 영역을 선택합니다.
2.    왼쪽 탐색에서 **네트워킹** 을 선택합니다.
3.    작업 영역에 대한 공용 액세스를 거부하려면 **사용 안 함** 을 선택합니다. 작업 영역에 대한 공용 액세스를 허용하려면 **사용** 을 선택합니다.

       :::image type="content" source="./media/connectivity-settings/synapse-workspace-networking-public-network-access-3.png" alt-text="기존 Synapse 작업 영역에서 네트워킹 탭, 공용 네트워크 액세스 설정이 사용임" lightbox="media/connectivity-settings/synapse-workspace-networking-public-network-access-3.png"::: 

4.    사용하지 않도록 설정된 경우 **방화벽 규칙** 이 회색으로 표시되어 방화벽 규칙이 적용되지 않습니다. 방화벽 규칙 구성은 그대로 유지됩니다. 

       :::image type="content" source="./media/connectivity-settings/synapse-workspace-networking-firewall-rules-4.png" alt-text="기존 Synapse 작업 영역에서 네트워킹 탭, 공용 네트워크 액세스 설정이 사용하지 않도록 설정, 방화벽 규칙 유의" lightbox="media/connectivity-settings/synapse-workspace-networking-firewall-rules-4.png"::: 
 
5.    **저장** 을 선택하여 변경 내용을 저장합니다. 네트워크 설정이 저장된 것을 확인하는 알림이 나타납니다.

## <a name="connection-policy"></a>연결 정책
Azure Synapse Analytics의 Synapse SQL에 대한 연결 정책이 *기본값* 으로 설정되어 있습니다. Azure Synapse Analytics에서는 이를 변경할 수 없습니다. [여기](../../azure-sql/database/connectivity-architecture.md#connection-policy)에서 Azure Synapse Analytics의 Synapse SQL 연결에 영향을 주는 방법에 대해 자세히 알아볼 수 있습니다. 

## <a name="minimal-tls-version"></a>최소 TLS 버전
Azure Synapse Analytics의 Synapse SQL은 모든 TLS 버전을 사용하는 연결을 허용합니다. Azure Synapse Analytics에서는 Synapse SQL에 대한 최소 TLS 버전을 설정할 수 없습니다.

## <a name="next-steps"></a>다음 단계

 - [Azure Synapse 작업 영역 만들기](./synapse-workspace-ip-firewall.md)의 단계에 따라 새 작업 영역을 만듭니다.