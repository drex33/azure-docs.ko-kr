---
title: IP 방화벽 규칙 구성
description: Azure Synapse Analytics에서 IP 방화벽 규칙을 구성하는 방법을 설명하는 문서입니다.
author: ashinMSFT
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: security
ms.date: 08/15/2021
ms.author: seshin
ms.reviewer: wiassaf
ms.openlocfilehash: a375c4c99e3b86706f9fed7bc52f3064995cd21c
ms.sourcegitcommit: 05dd6452632e00645ec0716a5943c7ac6c9bec7c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/17/2021
ms.locfileid: "122252788"
---
# <a name="azure-synapse-analytics-ip-firewall-rules"></a>Azure Synapse Analytics IP 방화벽 규칙

이 문서에서는 IP 방화벽 규칙을 설명하고 Azure Synapse Analytics에서 구성하는 방법을 설명합니다.

## <a name="ip-firewall-rules"></a>IP 방화벽 규칙

IP 방화벽 규칙은 각 요청이 시작된 IP 주소를 기준으로 하여 Synapse 작업 영역 액세스 권한을 부여하거나 거부합니다. 작업 영역에 대한 IP 방화벽 규칙을 구성할 수 있습니다. 작업 영역 수준에서 구성된 IP 방화벽 규칙은 작업 영역의 모든 퍼블릭 엔드포인트(전용 SQL 풀, 서버리스 SQL 풀 및 개발)에 적용됩니다.

## <a name="create-and-manage-ip-firewall-rules"></a>IP 방화벽 규칙 만들기 및 관리

Azure Synapse 작업 영역에 IP 방화벽 규칙을 추가하는 방법에는 두 가지가 있습니다. 작업 영역에 IP 방화벽을 추가하려면 **네트워킹** 을 선택하고, 작업 영역을 만드는 동안 **모든 IP 주소에서 연결 허용** 을 선택합니다.

> [!Important]
> 이 기능은 Managed VNet에 연결되지 않은 Azure Synapse 작업 영역에서만 사용할 수 있습니다.

:::image type="content" source="./media/synpase-workspace-ip-firewall/azure-synapse-workspace-networking-connections-all-ip-addresses.png" lightbox="./media/synpase-workspace-ip-firewall/azure-synapse-workspace-networking-connections-all-ip-addresses.png" alt-text="보안 탭 및 '모든 IP 주소에서 연결 허용' 확인란을 강조 표시하는 스크린샷.":::


작업 영역을 만든 후에 Synapse 작업 영역에 IP 방화벽 규칙을 추가할 수도 있습니다. Azure Portal의 **보안** 에서 **방화벽** 을 선택합니다. 새 IP 방화벽 규칙을 추가하려면 이름, 시작 IP 및 끝 IP를 지정합니다. 완료되면 **저장** 을 선택합니다.

:::image type="content" source="./media/synpase-workspace-ip-firewall/azure-synapse-workspace-networking-firewalls-add-client-ip.png" lightbox="./media/synpase-workspace-ip-firewall/azure-synapse-workspace-networking-firewalls-add-client-ip.png" alt-text="클라이언트 IP 추가 단추 및 규칙 필드를 강조 표시하는 Synapse 작업 영역의 네트워킹 페이지 스크린샷.":::

## <a name="connect-to-synapse-from-your-own-network"></a>자체 네트워크에서 Synapse에 연결

Synapse Studio를 사용하여 Synapse 작업 영역에 연결할 수 있습니다. SSMS(SQL Server Management Studio)를 사용하여 작업 영역의 SQL 리소스(전용 SQL 풀 및 서버리스 SQL 풀)에 연결할 수도 있습니다.

네트워크와 로컬 컴퓨터의 방화벽이 Synapse Studio에 대한 TCP 포트 80, 443 및 1443에서 나가는 통신을 허용해야 합니다.

또한 Synapse Studio용 UDP 포트 53에서 나가는 통신을 허용해야 합니다. SSMS 및 Power BI와 같은 도구를 사용하여 연결하려면 TCP 포트 1433에서 나가는 통신을 허용해야 합니다.


## <a name="next-steps"></a>다음 단계

[Azure Synapse 작업 영역](../quickstart-create-workspace.md) 만들기

[관리형 작업 영역 Virtual Network](./synapse-workspace-managed-vnet.md)를 사용하여 Azure Synapse 작업 영역 만들기