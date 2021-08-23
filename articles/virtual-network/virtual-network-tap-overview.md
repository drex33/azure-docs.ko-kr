---
title: Azure 가상 네트워크 TAP 개요 | Microsoft Docs
description: 가상 네트워크 TAP에 대해 알아봅니다. 가상 네트워크 TAP은 패킷 수집기에 스트리밍될 수 있는 가상 머신 네트워크 트래픽의 전체 복사본을 제공합니다.
services: virtual-network
documentationcenter: na
author: karthikananth
manager: ganesr
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: NA
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/14/2019
ms.author: kaanan
ms.openlocfilehash: 9a096f630fd32521d5890be2072eff75719fb54a
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122535638"
---
# <a name="virtual-network-tap"></a>가상 네트워크 TAP
> [!IMPORTANT]
> 가상 네트워크 TAP 미리 보기는 현재 모든 Azure 지역에서 보류 중입니다. 구독 ID을 사용하여 <azurevnettap@microsoft.com>로 메일을 보내주시면, 향후에 미리 보기에 대한 업데이트가 있을 때 알려드리겠습니다. 그동안은 [Azure Marketplace 제품](https://azuremarketplace.microsoft.com/marketplace/apps/category/networking?page=1&subcategories=appliances%3Ball&search=Network%20Traffic&filters=partners)에서 제공하는 [패킷 브로커 파트너 솔루션](#virtual-network-tap-partner-solutions)을 통해 TAP/네트워크 보기 기능을 제공하는 에이전트 기반 또는 NVA 솔루션을 사용할 수 있습니다.

Azure 가상 네트워크 TAP(터미널 액세스 지점)을 사용하면 네트워크 패킷 수집기 또는 분석 도구로 가상 머신 네트워크 트래픽을 지속적으로 스트리밍할 수 있습니다. 수집기 또는 분석 도구는 [네트워크 가상 어플라이언스](https://azure.microsoft.com/solutions/network-appliances/) 파트너를 통해 제공됩니다. 가상 네트워크 TAP 사용의 유효성이 검사된 파트너 솔루션 목록은 [파트너 솔루션](#virtual-network-tap-partner-solutions)을 참조하세요.
다음 그림은 가상 네트워크 TAP이 작동하는 방법을 보여 줍니다. 가상 네트워크에 배포된 가상 머신에 연결된 [네트워크 인터페이스](virtual-network-network-interface.md)에 TAP 구성을 추가할 수 있습니다. 대상은 모니터링된 네트워크 인터페이스 또는 [피어링된 가상](virtual-network-peering-overview.md) 네트워크와 동일한 가상 네트워크의 가상 네트워크 IP 주소입니다. 가상 네트워크 TAP에 대한 수집기 솔루션은 고가용성을 위해 Azure 내부 부하 분산 장치 배후에 배포될 수 있습니다.
![가상 네트워크 TAP이 작동하는 방법](./media/virtual-network-tap/architecture.png)

## <a name="prerequisites"></a>필수 구성 요소

가상 네트워크 TAP을 만들기 전에 미리 보기에 등록됐다는 확인 메일을 받아야 합니다. 그러면 같은 Azure 지역의 TAP 트래픽 집계를 위해 [Azure Resource Manager](../azure-resource-manager/management/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 배포 모델 및 파트너 솔루션을 사용하여 하나 이상의 가상 머신을 만들 수 있습니다. 가상 네트워크에 파트너 솔루션이 없다면 [파트너 솔루션](#virtual-network-tap-partner-solutions)을 참조하여 하나의 솔루션을 배포합니다. 동일한 가상 네트워크 TAP 리소스를 사용하여 동일하거나 다른 구독의 여러 네트워크 인터페이스에서 트래픽을 집계할 수 있습니다. 모니터링된 네트워크 인터페이스가 다른 구독에 있는 경우 구독은 동일한 Azure Active Directory 테넌트에 연결되어야 합니다. 또한 모니터링된 네트워크 인터페이스 및 TAP 트래픽 집계를 위한 대상 엔드포인트는 동일한 지역의 피어링된 가상 네트워크에 있을 수 있습니다. 이 배포 모델을 사용하는 경우 [가상 네트워크 피어링](virtual-network-peering-overview.md)이 가상 네트워크 TAP을 구성하기 전에 사용하도록 설정되어 있어야 합니다.

## <a name="permissions"></a>권한

네트워크 인터페이스에 TAP 구성을 적용하는 데 사용하는 계정은 다음 표에서 필요한 작업이 할당된 [네트워크 기여자](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) 역할 또는 [사용자 지정](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 역할에 할당되어야 합니다.

| 작업 | 이름 |
|---|---|
| Microsoft.Network/virtualNetworkTaps/* | 가상 네트워크 TAP 리소스를 만들고,업데이트하고, 읽고, 삭제해야 함 |
| Microsoft.Network/networkInterfaces/read | TAP이 구성될 네트워크 인터페이스 리소스를 읽어야 함 |
| Microsoft.Network/tapConfigurations/* | 네트워크 인터페이스에서 TAP 구성을 만들고, 업데이트하고, 읽고, 삭제해야 함 |


## <a name="virtual-network-tap-partner-solutions"></a>가상 네트워크 TAP 파트너 솔루션

### <a name="network-packet-brokers"></a>네트워크 패킷 브로커

- [Azure용 GigaVUE Cloud Suite](https://www.gigamon.com/solutions/cloud/public-cloud/gigavue-cloud-suite-azure.html)
- [Ixia CloudLens](https://www.ixiacom.com/cloudlens/cloudlens-azure)
- [Nubeva Prisms](https://www.nubeva.com/azurevtap)
- [Big Switch Big Monitoring Fabric](https://www.bigswitch.com/)

### <a name="security-analytics-networkapplication-performance-management"></a>보안 분석, 네트워크/애플리케이션 성능 관리

- [활성 보안](https://awakesecurity.com/technology-partners/microsoft-azure/)
- [Cisco Stealthwatch 클라우드](https://blogs.cisco.com/security/cisco-stealthwatch-cloud-and-microsoft-azure-reliable-cloud-infrastructure-meets-comprehensive-cloud-security)
- [Darktrace](https://www.darktrace.com/en/azure/)
- [ExtraHop Reveal(x)](https://www.extrahop.com/partners/tech-partners/microsoft/)
- [Fidelis Cybersecurity](https://www.fidelissecurity.com/technology-partners/microsoft-azure )
- [Flowmon](https://www.flowmon.com/blog/azure-vtap)
- [NetFort LANGuardian](https://www.netfort.com/languardian/solutions/visibility-in-azure-network-tap/)
- [Netscout vSTREAM]( https://www.netscout.com/marketplace-azure)
- [Noname 보안](https://nonamesecurity.com/)
- [Riverbed SteelCentral AppResponse]( https://www.riverbed.com/products/steelcentral/steelcentral-appresponse-11.html)
- [RSA NetWitness® Platform](https://www.rsa.com/content/dam/en/solution-brief/rsa-netwitness-platform-overview-for-federal-agencies.pdf)
- [Vectra Cognito](https://vectra.ai/microsoftazure)



## <a name="next-steps"></a>다음 단계

- [가상 네트워크 TAP을 만드는](tutorial-tap-virtual-network-cli.md) 방법에 대해 알아봅니다.
