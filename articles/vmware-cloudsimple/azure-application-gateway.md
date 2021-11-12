---
title: VMware 가상 머신과 함께 Azure Application Gateway 사용
description: Azure Application Gateway를 사용하여 CloudSimple 프라이빗 클라우드 환경에서 VMware 가상 머신에서 실행되는 웹 서버에 대한 들어오는 웹 트래픽을 관리하는 방법을 설명합니다.
author: suzizuber
ms.author: v-szuber
ms.date: 08/16/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 15e10ed71702350aaadbac76ea02f445dd846cc1
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132322800"
---
# <a name="use-azure-application-gateway-with-vmware-virtual-machines-in-the-cloudsimple-private-cloud-environment"></a>CloudSimple 프라이빗 클라우드 환경에서 VMware 가상 머신과 함께 Azure Application Gateway 사용

Azure Application Gateway를 사용하여 CloudSimple 프라이빗 클라우드 환경에서 VMware 가상 머신에서 실행되는 웹 서버에 대한 들어오는 웹 트래픽을 관리할 수 있습니다.

퍼블릭-프라이빗 하이브리드 배포 시 Azure Application Gateway를 활용하면 애플리케이션에 대한 웹 트래픽을 관리하고, 보안 프런트 엔드를 제공하고, VMware 환경에서 실행되는 서비스에 대한 TLS 처리를 오프로드할 수 있습니다. Azure Application Gateway는 구성된 규칙 및 상태 프로브에 따라 들어오는 웹 트래픽을 VMware 환경에 상주하는 백 엔드 풀 인스턴스로 라우팅합니다.

이 Azure Application Gateway 솔루션을 사용하려면 다음을 수행해야 합니다.

* Azure 구독이 있어야 합니다.
* Azure Virtual Network와 가상 네트워크 내의 서브넷을 만들고 구성합니다.
* NSG 규칙을 만들고 구성한 후 ExpressRoute를 사용하여 vNet을 CloudSimple 프라이빗 클라우드에 피어링합니다.
* 프라이빗 클라우드를 만들고 구성합니다.
* Azure Application Gateway를 만들고 구성합니다.

## <a name="azure-application-gateway-deployment-scenario"></a>Azure Application Gateway 배포 시나리오

이 시나리오에서 Azure Application Gateway는 Azure Virtual Network에서 실행됩니다. 가상 네트워크는 ExpressRoute 회로를 통해 프라이빗 클라우드에 연결됩니다. 프라이빗 클라우드의 모든 서브넷은 가상 네트워크 서브넷에서 IP에 연결할 수 있습니다.

![Azure Virtual Network의 Azure Load Balancer](media/load-balancer-use-case.png)

## <a name="how-to-deploy-the-solution"></a>솔루션 배포 방법

배포 과정은 다음과 같은 작업으로 구성됩니다.

1. [필수 구성 요소가 충족되는지 확인](#1-verify-prerequisites)
2. [프라이빗 클라우드에 Azure Virtual Network 연결](#2-connect-your-azure-virtual-network-to-your-private-cloud)
3. [Azure Application Gateway 배포](#3-deploy-an-azure-application-gateway)
4. [프라이빗 클라우드에서 웹 서버 VM 풀 만들기 및 구성](#4-create-and-configure-a-web-server-vm-pool-in-your-private-cloud)

## <a name="1-verify-prerequisites"></a>1. 필수 구성 요소 확인

다음 필수 구성 요소가 충족되었는지 확인합니다.

* Azure Resource Manager 및 가상 네트워크가 이미 생성되었습니다.
* Azure Virtual Network 내에 전용 서브넷(Application Gateway의 경우)이 이미 만들어졌습니다.
* CloudSimple 프라이빗 클라우드가 이미 만들어졌습니다.
* 가상 네트워크의 IP 서브넷과 프라이빗 클라우드의 서브넷 간에 IP 충돌이 없습니다.

## <a name="2-connect-your-azure-virtual-network-to-your-private-cloud"></a>2. 프라이빗 클라우드에 Azure Virtual Network 연결

프라이빗 클라우드에 Azure Virtual Network를 연결하려면 다음 프로세스를 수행합니다.

1. [CloudSimple 포털에서 ExpressRoute 피어링 정보를 복사합니다](virtual-network-connection.md).

2. [Azure Virtual Network에 대한 가상 네트워크 게이트웨이를 구성합니다](../expressroute/expressroute-howto-add-gateway-portal-resource-manager.md).

3. [가상 네트워크를 CloudSimple ExpressRoute 회로에 연결합니다](../expressroute/expressroute-howto-linkvnet-portal-resource-manager.md#connect-a-vnet-to-a-circuit---different-subscription).

4. [가상 네트워크를 ExpressRoute 회로에 연결하기 위해 복사한 피어링 정보를 사용합니다](virtual-network-connection.md).

## <a name="3-deploy-an-azure-application-gateway"></a>3. Azure Application Gateway 배포

이에 대한 자세한 지침은 [Azure Portal에서 경로 기반 라우팅 규칙을 사용하여 애플리케이션 게이트웨이 만들기](../application-gateway/create-url-route-portal.md)에 나와 있습니다. 다음은 필수 단계를 요약한 것입니다.

1. 구독 및 리소스 그룹에 가상 네트워크를 만듭니다.
2. 가상 네트워크 내에서 전용 서브넷으로 사용할 서브넷을 만듭니다.
3. 표준 Application Gateway를 만듭니다(필요에 따라 WAF를 사용하도록 설정). Azure Portal 홈 페이지의 왼쪽 상단에 있는 **리소스** > **네트워킹** > **Application Gateway** 를 클릭합니다. 표준 SKU 및 크기를 선택하고 Azure 구독, 리소스 그룹 및 위치 정보를 제공합니다. 필요한 경우 이 애플리케이션 게이트웨이에 대한 새 공용 IP를 만들고 애플리케이션 게이트웨이에 대한 가상 네트워크 및 전용 서브넷에 대한 세부 정보를 제공합니다.
4. 가상 머신을 사용하여 백 엔드 풀을 추가하고 애플리케이션 게이트웨이에 추가합니다.

## <a name="4-create-and-configure-a-web-server-vm-pool-in-your-private-cloud"></a>4. 프라이빗 클라우드에서 웹 서버 VM 풀 만들기 및 구성

VCenter에서 원하는 OS와 웹 서버(예: Windows/IIS 또는 Linux/Apache)를 사용하여 VM을 만듭니다. 프라이빗 클라우드의 웹 계층에 대해 지정된 서브넷/VLAN을 선택합니다. 웹 서버 VM의 vNIC가 하나 이상 웹 계층 서브넷에 있는지 확인합니다.
