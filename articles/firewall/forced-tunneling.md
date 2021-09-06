---
title: Azure Firewall 강제 터널링
description: 추가 처리를 위해 인터넷 바인딩된 트래픽을 추가 방화벽 또는 네트워크 가상 어플라이언스로 라우팅하도록 강제 터널링을 구성할 수 있습니다.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 08/13/2021
ms.author: victorh
ms.openlocfilehash: b3d52451713c8fc504487aa293d566264f4eadb6
ms.sourcegitcommit: 2d412ea97cad0a2f66c434794429ea80da9d65aa
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/14/2021
ms.locfileid: "122530110"
---
# <a name="azure-firewall-forced-tunneling"></a>Azure Firewall 강제 터널링

새 Azure Firewall을 구성하는 경우 인터넷으로 직접 이동하는 대신 인터넷에 바인딩된 모든 트래픽을 지정된 다음 홉으로 라우팅할 수 있습니다. 예를 들어 BGP를 통해 또는 UDR(사용자 정의 경로)을 사용하여 인터넷으로 전달되기 전에 온-프레미스 에지 방화벽 또는 기타 NVA(네트워크 가상 어플라이언스)로 네트워크 트래픽을 처리하도록 강제하는 기본 경로가 있을 수 있습니다. 이 구성을 지원하려면 강제 Tunnel 구성이 활성화된 Azure Firewall을 만들어야 합니다. 이는 서비스 중단을 방지하기 위한 필수 요구 사항입니다. 기존 방화벽인 경우 강제 Tunnel 모드에서 방화벽을 다시 만들어 이 구성을 지원해야 합니다. 자세한 내용은 강제 Tunnel 모드에서 방화벽 중지 및 다시 시작에 대한 [Azure Firewall FAQ](firewall-faq.yml#how-can-i-stop-and-start-azure-firewall)를 참조하세요.

## <a name="forced-tunneling-configuration"></a>강제 터널링 구성

아래와 같이 강제 Tunnel 모드를 활성화하여 방화벽을 만드는 동안 강제 터널링을 구성할 수 있습니다. 강제 터널링을 지원하기 위해 서비스 관리 트래픽은 고객 트래픽과 구분됩니다. 연결된 공용 IP 주소에는 **AzureFirewallManagementSubnet**(최소 서브넷 크기/26)이라는 추가 전용 서브넷이 필요합니다. 

강제 터널링 모드에서 Azure Firewall 서비스는 *운영* 목적으로 관리 서브넷(AzureFirewallManagementSubnet)을 통합합니다. 기본적으로 서비스는 시스템 제공 경로 테이블을 관리 서브넷에 연결합니다. 이 서브넷에서 허용되는 유일한 경로는 인터넷에 대한 기본 경로이며 *게이트웨이 전파* 경로가 비활성화되어야 합니다. 방화벽을 만들 때 고객 경로 테이블을 관리 서브넷에 연결하지 않습니다. 

:::image type="content" source="media/forced-tunneling/forced-tunneling-configuration.png" alt-text="강제 터널링 구성":::

이 구성 내에서 *AzureFirewallSubnet* 은 이제 온-프레미스 방화벽 또는 NVA 경로를 포함하여 트래픽이 인터넷에 전달되기 전에 트래픽을 처리할 수 있습니다. 이 서브넷에서 **게이트웨이 경로 전파** 가 사용하도록 설정된 경우에는 BGP를 통해 해당 경로를 *AzureFirewallSubnet* 에 게시할 수도 있습니다.

예를 들어 온-프레미스 디바이스에 도달하기 위해 VPN Gateway를 다음 홉으로 설정하여 *AzureFirewallSubnet* 에서 기본 경로를 만들 수 있습니다. 또는 **게이트웨이 경로 전파** 를 사용하도록 설정하여 적절한 온-프레미스 네트워크 경로를 가져올 수 있습니다.

:::image type="content" source="media/forced-tunneling/route-propagation.png" alt-text="가상 네트워크 게이트웨이 경로 전파":::

강제 터널링을 사용하도록 설정하면 인터넷 바인딩된 트래픽이 AzureFirewallSubnet의 방화벽 개인 IP 주소 중 하나로 SNAT되어 온-프레미스 방화벽에서 원본을 숨깁니다.

조직에서 개인 네트워크에 대해 공용 IP 주소 범위를 사용하면 Azure Firewall은 AzureFirewallSubnet의 방화벽 개인 IP 주소 중 하나에 트래픽을 SNAT합니다. 하지만 공용 IP 주소 범위를 SNAT하지 **않도록** Azure Firewall을 구성할 수 있습니다. 자세한 내용은 [Azure Firewall SNAT 개인 IP 주소 범위](snat-private-range.md)를 참조하세요.

강제 터널링을 지원하도록 Azure Firewall을 구성하면 구성을 실행 취소할 수 없습니다. 방화벽에서 다른 모든 IP 구성을 제거하면 관리 IP 구성도 제거되고 방화벽의 할당이 취소됩니다. 관리 IP 구성에 할당된 공용 IP 주소를 제거할 수 없지만 다른 공용 IP 주소를 할당할 수 있습니다.

## <a name="next-steps"></a>다음 단계

- [자습서: Azure Portal을 사용하여 하이브리드 네트워크에서 Azure Firewall 배포 및 구성](tutorial-hybrid-portal.md)
