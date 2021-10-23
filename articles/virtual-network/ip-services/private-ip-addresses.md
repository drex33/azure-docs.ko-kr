---
title: Azure의 개인 IP 주소
titlesuffix: Azure Virtual Network
description: Azure의 개인 IP 주소에 관해 알아봅니다.
services: virtual-network
documentationcenter: na
author: asudbring
ms.service: virtual-network
ms.subservice: ip-services
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/28/2020
ms.author: allensu
ms.openlocfilehash: 57862739ec73dffb6ba6fde657a212d878f6da6d
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130251151"
---
# <a name="private-ip-addresses"></a>개인 IP 주소
개인 IP를 사용하면 Azure의 리소스 간 통신이 가능합니다. 

리소스는 다음과 같습니다:
* 다음과 같은 Azure 서비스:
    * 가상 머신 네트워크 인터페이스
    * 내부 부하 분산 장치(ILB)
    * 애플리케이션 게이트웨이
* [가상 네트워크](../../virtual-network/virtual-networks-overview.md)에 있습니다.
* VPN Gateway 또는 ExpressRoute 회로를 통한 온-프레미스 네트워크입니다.

개인 IP를 사용하면 공용 IP 주소를 사용하지 않고도 이러한 리소스와 통신할 수 있습니다.

## <a name="allocation-method"></a>할당 방법

Azure는 리소스가 있는 가상 네트워크 서브넷의 주소 범위에서 리소스에 개인 IP 주소를 할당합니다.

Azure는 각 서브넷 주소 범위에서 처음 네 개의 주소를 사용하도록 설정합니다. 주소를 리소스에 할당할 수 없습니다. 예를 들어 서브넷의 주소 범위가 10.0.0.0/16이면 주소 10.0.0.0-10.0.0.3 및 10.0.255.255를 사용할 수 없습니다. 서브넷 주소 범위 내의 IP 주소는 한 번에 하나의 리소스에만 할당할 수 있습니다. 

개인 IP 주소를 지정하는 방법에는 두 가지가 있습니다:

- **동적**: Azure는 사용 가능한 다음 할당되지 않은 또는 예약되지 않은 IP 주소를 서브넷의 주소 범위에 할당합니다. 예를 들어 Azure는 주소 10.0.0.4-10.0.0.9가 다른 리소스에 이미 할당된 경우 10.0.0.10를 새 리소스에 할당합니다. 

    동적이 기본 할당 방법입니다. 할당된 동적 IP 주소는 네트워크 인터페이스가 다음과 같은 경우 해제됩니다:
    
    * 삭제됨
    * 동일한 가상 네트워크 내의 다른 서브넷에 다시 할당됩니다.
    * 할당 방법이 고정으로 변경되고 다른 IP 주소가 지정됩니다. 
    
    기본적으로 할당 메서드를 동적에서 고정으로 변경하는 경우 Azure는 이전에 동적으로 할당된 주소를 고정 주소로 할당합니다.

- **고정적**: Azure는 할당되지 않은 또는 예약되지 않은 IP 주소를 선택하고 서브넷의 주소 범위에 할당합니다. 

    예를 들어 서브넷의 주소 범위는 10.0.0.0/16이고 주소 10.0.0.4-10.0.0.9는 다른 리소스에 할당됩니다. 10.0.0.10-10.0.255.254 사이의 모든 주소를 할당할 수 있습니다. 고정 주소는 네트워크 인터페이스가 삭제되는 경우에만 해제됩니다. 
    
    할당 방법이 변경되면 Azure는 고정 IP를 동적 IP로 할당합니다. 주소가 서브넷에서 사용 가능한 다음 주소가 아닌 경우에도 재할당이 발생합니다. 네트워크 인터페이스가 다른 서브넷에 할당되면 주소가 변경됩니다.
    
    네트워크 인터페이스를 다른 서브넷에 할당하려면 할당 방법을 고정에서 동적으로 변경합니다. 네트워크 인터페이스를 다른 서브넷에 할당한 다음 할당 방법을 다시 고정으로 변경합니다. 새 서브넷의 주소 범위에서 IP 주소를 할당합니다.
    
## <a name="virtual-machines"></a>가상 머신

하나 이상의 개인 IP 주소가 하나 이상의 **네트워크 인터페이스** 에 할당됩니다. 네트워크 인터페이스는 [Windows](../../virtual-machines/windows/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 또는 [Linux](../../virtual-machines/linux/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 가상 머신에 할당됩니다. 각 개인 IP 주소에 대한 할당 방법을 동적 또는 고정으로 지정할 수 있습니다.

### <a name="internal-dns-hostname-resolution-for-virtual-machines"></a>내부 DNS 호스트 이름 확인(가상 머신)

Azure 가상 머신은 기본적으로 [Azure 관리 DNS 서버](../../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#azure-provided-name-resolution)로 구성됩니다. 사용자 지정 DNS 서버를 명시적으로 구성할 수 있습니다. 이러한 DNS 서버는 동일한 가상 네트워크 내에 있는 가상 머신에 대한 내부 이름 확인을 제공합니다.

가상 머신의 개인 IP 주소에 대한 호스트 이름 매핑이 Azure 관리 DNS 서버에 추가됩니다. 

호스트 이름은 VM에 다음이 있는 경우에 주 네트워크 인터페이스의 기본 IP에 매핑됩니다:

* 여러 네트워크 인터페이스
* 여러 IP 주소
* 둘 다

Azure 관리 DNS로 구성된 VM은 동일한 가상 네트워크 내에서 호스트 이름을 확인합니다. 사용자 지정 DNS 서버를 사용하여 연결된 가상 네트워크에서 VM의 호스트 이름을 확인합니다.

## <a name="internal-load-balancers-ilb--application-gateways"></a>ILB(내부 부하 분산 장치) 및 애플리케이션 게이트웨이

다음의 **프런트 엔드** 구성에 개인 IP 주소를 할당할 수 있습니다:

* [Azure ILB](../../load-balancer/load-balancer-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)(내부 부하 분산 장치)
* [Azure Application Gateway](../../application-gateway/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 

이 개인 IP 주소는 내부 엔드포인트 역할을 합니다. 내부 엔드포인트는 가상 네트워크 및 연결된 원격 네트워크 내의 리소스에만 액세스할 수 있습니다. 동적 또는 고정 IP를 할당할 수 있습니다.

## <a name="at-a-glance"></a>요약
다음 표는 개인 IP를 리소스에 연결할 수 있는 속성을 표시합니다. 

사용할 수 있는 할당 방법도 함께 표시됩니다:

* 동적
* 정적

| 최상위 리소스 | IP 주소 연결 | 동적 | 정적 |
| --- | --- | --- | --- |
| 가상 머신 |네트워크 인터페이스 |예 |예 |
| 부하 분산 장치 |프런트 엔드 구성 |예 |예 |
| 프런트 엔드 |프런트 엔드 구성 |예 |예 |

## <a name="limits"></a>제한
IP 주소 지정에 적용되는 제한은 Azure에서 [네트워킹 제한](../../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#networking-limits)의 전체 집합에서 찾을 수 있습니다. 제한은 지역별, 구독별로 적용됩니다. 비즈니스에 따라 최대한도까지 기본 제한을 증가시키려면 [고객 지원팀에 문의하세요](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

## <a name="next-steps"></a>다음 단계
[Azure의 공용 IP 주소](public-ip-addresses.md)에 대해 알아보기
* [Azure Portal을 사용하여 고정 개인 IP 주소를 사용하는 VM 배포](./virtual-networks-static-private-ip-arm-pportal.md)