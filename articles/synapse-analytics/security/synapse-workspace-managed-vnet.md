---
title: 관리형 가상 네트워크
description: Azure Synapse Analytics의 관리형 가상 네트워크에 대해 설명하는 문서
author: ashinMSFT
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: security
ms.date: 08/16/2021
ms.author: seshin
ms.reviewer: wiassaf
ms.openlocfilehash: 9866a2c773193cc20bd6b9e193e025fa65eddcc6
ms.sourcegitcommit: 8000045c09d3b091314b4a73db20e99ddc825d91
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/19/2021
ms.locfileid: "122446209"
---
# <a name="azure-synapse-analytics-managed-virtual-network"></a>Azure Synapse Analytics 관리형 가상 네트워크

이 문서에서는 Azure Synapse Analytics의 관리형 Virtual Network에 대해 설명합니다.

## <a name="managed-workspace-virtual-network"></a>관리형 작업 영역 Virtual Network

Azure Synapse 작업 영역을 만들 때 작업 영역을 Microsoft Azure Virtual Network와 연결하도록 선택할 수 있습니다. 작업 영역과 연결된 Virtual Network는 Azure Synapse에서 관리합니다. 이 Virtual Network는 *관리형 작업 영역 Virtual Network* 라고 합니다.

관리형 작업 영역 Virtual Network는 다음과 같은 네 가지 가치를 제공합니다.

- 관리형 작업 영역 Virtual Network를 사용하면 Azure Synapse에 대한 Virtual Network 관리 부담에서 벗어날 수 있습니다.
- Azure Synapse 관리 트래픽이 Virtual Network에 진입하도록 허용하기 위해 자체 Virtual Network에서 인바운드 NSG 규칙을 구성할 필요가 없습니다. 이러한 NSG 규칙을 잘못 구성하면 서비스가 중단됩니다.
- 최대 부하를 기준으로 Spark 클러스터의 서브넷을 만들 필요가 없습니다.
- 관리형 작업 영역 Virtual Network는 관리형 프라이빗 엔드포인트와 함께 데이터 반출을 방지합니다. 관리형 작업 영역 Virtual Network가 연결된 작업 영역에는 관리형 프라이빗 엔드포인트만 만들 수 있습니다.

관리형 작업 영역 Virtual Network가 연결된 작업 영역을 만들면 작업 영역이 네트워크에서 다른 작업 영역과 격리됩니다. Azure Synapse는 작업 영역에 데이터 통합, 서버리스 Apache Spark 풀, 전용 SQL 풀 및 서버리스 SQL 풀과 같은 다양한 분석 기능을 제공합니다.

작업 영역에 관리형 작업 영역 Virtual Network가 있으면 데이터 통합 및 Spark 리소스가 해당 Virtual Network에 배포됩니다. 또한 관리형 작업 영역 Virtual Network는 각 Spark 클러스터가 자체 서브넷에 있기 때문에 Spark 작업에 대한 사용자 수준 격리를 제공합니다.

전용 SQL 풀 및 서버리스 SQL 풀은 다중 테넌트 기능이므로 관리형 작업 영역 Virtual Network 외부에 상주합니다. 작업 영역 내에서 전용 SQL 풀 및 서버리스 SQL 풀과의 통신에는 Azure 프라이빗 링크가 사용됩니다. 이러한 프라이빗 링크는 관리형 작업 영역 Virtual Network가 연결된 작업 영역을 만들 때 자동으로 생성됩니다.

>[!IMPORTANT]
>작업 영역이 생성된 후에는 이 작업 영역 구성을 변경할 수 없습니다. 예를 들어 관리형 작업 영역 Virtual Network가 연결되지 않은 작업 영역을 다시 구성한 후 Virtual Network를 연결할 수 없습니다. 마찬가지로 관리형 작업 영역 Virtual Network가 연결된 작업 영역을 다시 구성한 후 Virtual Network 연결을 해제할 수 없습니다.

## <a name="create-an-azure-synapse-workspace-with-a-managed-workspace-virtual-network"></a>관리형 작업 영역 Virtual Network가 연결된 Azure Synapse 작업 영역 만들기

아직 등록하지 않은 경우 네트워크 리소스 공급자를 등록합니다. 리소스 공급자를 등록하면 구독이 리소스 공급자에서 작동하도록 구성됩니다. [등록](../../azure-resource-manager/management/resource-providers-and-types.md)할 때 리소스 공급자 목록에서 *Microsoft.Network* 를 선택합니다.

관리형 작업 영역 Virtual Network가 연결된 Azure Synapse 작업 영역을 만들려면 Azure Portal에서 **네트워킹** 탭을 선택하고 **관리형 가상 네트워크 사용** 확인란을 선택합니다.

확인란을 선택하지 않으면 작업 영역에 Virtual Network가 연결되지 않습니다.

>[!IMPORTANT]
>관리형 작업 영역 Virtual Network가 연결된 작업 영역에는 프라이빗 링크만 사용할 수 있습니다.

:::image type="content" source="./media/synpase-workspace-ip-firewall/azure-synapse-analytics-networking-managed-virtual-network-outbound-traffic.png" lightbox="./media/synpase-workspace-ip-firewall/azure-synapse-analytics-networking-managed-virtual-network-outbound-traffic.png" alt-text="관리형 가상 네트워크 옵션이 활성화되고 승인된 대상에만 아웃바운드 데이터 트래픽 허용 옵션이 예로 설정된 Synapse 작업 영역 네트워킹 만들기 페이지의 스크린샷.":::

관리형 작업 영역 Virtual Network를 작업 영역과 연결하도록 선택한 후에는 관리형 작업 영역 Virtual Network에서 [관리형 프라이빗 엔드포인트](./synapse-workspace-managed-private-endpoints.md)를 사용하여 승인된 대상으로만 아웃바운드 연결을 허용하여 데이터 반출로부터 보호할 수 있습니다. **예** 를 선택하여 관리형 작업 영역 Virtual Network의 아웃바운드 트래픽을 관리형 프라이빗 엔드포인트를 통해 제한합니다. 



:::image type="content" source="./media/synpase-workspace-ip-firewall/azure-synapse-workspace-managed-virtual-network-allow-outbound-traffic.png" lightbox="./media/synpase-workspace-ip-firewall/azure-synapse-workspace-managed-virtual-network-allow-outbound-traffic.png" alt-text="승인된 대상에만 아웃바운드 데이터 트래픽 허용 옵션이 예로 설정된 관리형 가상 네트워크 페이지의 스크린샷.":::

작업 영역에서 모든 대상으로의 아웃바운드 트래픽을 허용하려면 **아니요** 를 선택합니다.

Azure Synapse 작업 영역에서 관리형 프라이빗 엔드포인트가 만들어지는 대상을 제어할 수도 있습니다. 기본적으로 구독이 속한 동일한 AAD 테넌트의 리소스에 대한 관리형 프라이빗 엔드프인트가 허용됩니다. 구독이 속한 것과 다른 AAD 테넌트의 리소스에 대한 관리형 프라이빗 엔드포인트를 만들려면 **+ 추가** 를 선택하여 해당 AAD 테넌트를 추가할 수 있습니다. 드롭다운에서 AAD 테넌트를 선택하거나 AAD 테넌트 ID를 수동으로 입력할 수 있습니다.

:::image type="content" source="./media/synpase-workspace-ip-firewall/azure-synapse-workspace-managed-virtual-network-private-endpoints-azure-ad.png" lightbox="./media/synpase-workspace-ip-firewall/azure-synapse-workspace-managed-virtual-network-private-endpoints-azure-ad.png" alt-text="Azure 테넌트용 추가 단추가 강조 표시된 관리형 가상 네트워크 페이지의 스크린샷.":::

작업 영역을 만든 후 Azure Portal에서 **개요** 를 선택하여 Azure Synapse 작업 영역이 관리형 작업 영역 Virtual Network에 연결되었는지 확인할 수 있습니다.

:::image type="content" source="./media/synpase-workspace-ip-firewall/azure-synapse-analytics-overview-managed-virtual-network-enabled.png" lightbox="./media/synpase-workspace-ip-firewall/azure-synapse-analytics-overview-managed-virtual-network-enabled.png" alt-text="관리형 가상 네트워크가 사용하도록 설정되어 있음을 나타내는 Azure Synapse 작업 영역 개요 페이지의 스크린샷.":::

## <a name="next-steps"></a>다음 단계

[Azure Synapse 작업 영역](../quickstart-create-workspace.md) 만들기

[관리형 프라이빗 엔드포인트](./synapse-workspace-managed-private-endpoints.md)에 대한 자세한 정보

[데이터 원본에 대한 관리형 프라이빗 엔드포인트 만들기](./how-to-create-managed-private-endpoints.md)
