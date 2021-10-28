---
title: Azure AD Domain Services의 네트워크 계획 및 연결 | Microsoft Docs
description: Azure Active Directory Domain Services를 실행할 때 연결에 사용되는 가상 네트워크 디자인 고려 사항 및 리소스에 대해 알아봅니다.
services: active-directory-ds
author: justinha
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 08/12/2021
ms.author: justinha
ms.openlocfilehash: 841d3b0db01938f42f56931bb370e25afe1651a6
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130216961"
---
# <a name="virtual-network-design-considerations-and-configuration-options-for-azure-active-directory-domain-services"></a>Azure Active Directory Domain Services의 가상 네트워크 디자인 고려 사항 및 구성 옵션

Azure AD DS(Azure Active Directory Domain Services)는 다른 애플리케이션과 워크로드에 대한 인증과 관리 서비스를 제공합니다. 네트워크 연결은 주요 구성 요소입니다. 가상 네트워크 리소스를 올바르게 구성하지 않으면 애플리케이션과 워크로드가 Azure AD DS에서 제공하는 기능을 사용하여 통신할 수 없습니다. 필요에 따라 Azure AD DS가 애플리케이션과 워크로드를 지원할 수 있도록 가상 네트워크 요구 사항을 계획하세요.

이 문서에서는 Azure AD DS를 지원하기 위한 Azure virtual network의 디자인 고려 사항과 요구 사항을 간략하게 설명합니다.

## <a name="azure-virtual-network-design"></a>Azure 가상 네트워크 디자인

네트워크 연결을 제공하고 애플리케이션과 서비스가 Azure AD DS 관리되는 도메인에 인증할 수 있도록 허용하려면 Azure 가상 네트워크와 서브넷을 사용합니다. 관리되는 도메인을 자체 가상 네트워크에 배포하는 것이 가장 좋습니다.

동일한 가상 네트워크에 별도의 애플리케이션 서브넷을 포함하여 관리 VM이나 가벼운 애플리케이션 워크로드를 호스트할 수 있습니다. 일반적으로 Azure AD DS 가상 네트워크에 피어링된 더 크거나 복잡한 애플리케이션 워크로드의 경우 별도의 가상 네트워크가 있는 것이 가장 좋습니다.

다음 섹션에서 설명하는 가상 네트워크와 서브넷에 대한 요구 사항을 충족하는 경우 다른 디자인을 선택해도 됩니다.

Azure AD DS의 가상 네트워크를 설계할 때 다음과 같은 고려 사항이 적용됩니다.

* Azure AD DS는 가상 네트워크와 동일한 Azure 지역에 배포되어야 합니다.
    * 이번에는 Azure AD 테넌트당 하나의 관리되는 도메인만 배포할 수 있습니다. 관리되는 도메인은 단일 지역에 배포됩니다. [Azure AD DS를 지원하는 지역](https://azure.microsoft.com/global-infrastructure/services/?products=active-directory-ds&regions=all)에서 가상 네트워크를 만들거나 선택해야 합니다.
* 애플리케이션 워크로드를 호스트하는 다른 Azure 지역과 가상 네트워크의 근접성을 고려합니다.
    * 대기 시간을 최소화하려면 핵심 애플리케이션을 관리되는 도메인의 가상 네트워크 서브넷과 가깝거나 동일한 지역에 유지합니다. Azure 가상 네트워크 간에 가상 네트워크 피어링 또는 VPN(가상 사설망) 연결을 사용할 수 있습니다. 해당 연결 옵션은 다음 섹션에서 설명합니다.
* 가상 네트워크는 관리되는 도메인에서 제공하는 서비스 이외의 DNS 서비스를 사용할 수 없습니다.
    * Azure AD DS는 자체 DNS 서비스를 제공합니다. DNS 서비스 주소를 사용하도록 가상 네트워크를 구성해야 합니다. 추가적인 네임스페이스에 대한 이름 확인은 조건부 전달자를 사용하여 수행할 수 있습니다.
    * VM을 비롯한 다른 DNS 서버의 직접 쿼리에 사용자 지정 DNS 서버 설정을 사용할 수 없습니다. 가상 네트워크의 리소스는 관리되는 도메인에서 제공하는 DNS 서비스를 사용해야 합니다.

> [!IMPORTANT]
> 서비스를 사용한 후에는 Azure AD DS를 다른 가상 네트워크로 이동할 수 없습니다.

관리되는 도메인은 Azure 가상 네트워크의 서브넷에 연결됩니다. 다음 사항을 고려하여 Azure AD DS를 위한 이 서브넷을 디자인합니다.

* 관리되는 도메인은 자체 서브넷에 배포해야 합니다. 기존 서브넷이나 게이트웨이 서브넷을 사용해서는 안 됩니다.
* 관리되는 도메인을 배포하는 동안 네트워크 보안 그룹이 만들어집니다. 이 네트워크 보안 그룹에는 올바른 서비스 통신에 필요한 규칙이 포함되어 있습니다.
    * 고유의 사용자 지정 규칙을 사용하여 기존 네트워크 보안 그룹을 만들거나 사용하지 마세요.
* 관리되는 도메인에는 3~5개의 IP 주소가 필요합니다. 서브넷 IP 주소 범위가 3~5개의 주소를 제공할 수 있는지 확인합니다.
    * 사용 가능한 IP 주소를 제한하면 관리되는 도메인에서 두 개의 도메인 컨트롤러를 유지하지 못할 수 있습니다.

다음 예제 다이어그램에서는 관리되는 도메인에 자체 서브넷이 있고, 외부 연결용 게이트웨이 서브넷이 있으며, 애플리케이션 워크로드가 가상 네트워크 내의 연결된 서브넷에 있는 유효한 디자인을 간략하게 설명합니다.

![권장되는 서브넷 디자인](./media/active-directory-domain-services-design-guide/vnet-subnet-design.png)

## <a name="connections-to-the-azure-ad-ds-virtual-network"></a>Azure AD DS 가상 네트워크에 연결

이전 섹션에서 설명한 것처럼 Azure의 단일 가상 네트워크에만 관리되는 도메인을 만들 수 있으며, Azure AD 테넌트당 하나의 관리되는 도메인만 만들 수 있습니다. 이 아키텍처에 따라 애플리케이션 워크로드를 호스트하는 하나 이상의 가상 네트워크를 관리되는 도메인의 가상 네트워크에 연결해야 할 수 있습니다.

다음 방법 중 하나를 사용하여 다른 Azure 가상 네트워크에서 호스트되는 애플리케이션 워크로드를 연결할 수 있습니다.

* 가상 네트워크 피어링
* VPN(가상 사설망)

### <a name="virtual-network-peering"></a>가상 네트워크 피어링

가상 네트워크 피어링은 Azure 백본 네트워크를 통해 동일한 지역에 있는 두 개의 가상 네트워크를 연결하는 메커니즘입니다. 글로벌 가상 네트워크 피어링은 Azure 지역에서 가상 네트워크를 연결할 수 있습니다. 일단 피어링되면 두 개의 가상 네트워크는 VM과 같은 리소스가 개인 IP 주소를 사용하여 서로 직접 통신할 수 있도록 합니다. 가상 네트워크 피어링을 사용하면 다른 가상 네트워크에 배포된 애플리케이션 워크로드를 사용하여 관리되는 도메인을 배포할 수 있습니다.

![피어링을 사용하여 가상 네트워크 연결](./media/active-directory-domain-services-design-guide/vnet-peering.png)

자세한 내용은 [Azure 가상 네트워크 피어링 개요](../virtual-network/virtual-network-peering-overview.md)를 참조하세요.

### <a name="virtual-private-networking-vpn"></a>VPN(가상 사설망)

가상 네트워크를 온-프레미스 사이트 위치에 구성할 수 있는 것과 동일한 방식으로 가상 네트워크를 다른 가상 네트워크(VNet 간)에 연결할 수 있습니다. 두 연결 모두 VPN 게이트웨이를 사용하여 IPsec/IKE를 통한 보안 터널을 만듭니다. 이 연결 모델을 사용하면 관리되는 도메인을 Azure 가상 네트워크에 배포한 후 온-프레미스 위치 또는 다른 클라우드를 연결할 수 있습니다.

![VPN Gateway를 사용하여 가상 네트워크 연결](./media/active-directory-domain-services-design-guide/vnet-connection-vpn-gateway.jpg)

VPN(가상 사설망)을 사용하는 방법에 대한 자세한 내용은 [Azure Portal을 사용하여 VNet 간 VPN 게이트웨이 연결 구성](../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md)을 참조하세요.

## <a name="name-resolution-when-connecting-virtual-networks"></a>가상 네트워크를 연결할 때 이름 확인

관리되는 도메인의 가상 네트워크에 연결된 가상 네트워크에는 일반적으로 자체 DNS 설정이 있습니다. 가상 네트워크를 연결하는 경우 관리되는 도메인에서 제공하는 서비스를 확인하기 위해 연결하는 가상 네트워크의 이름 확인을 자동으로 구성하지 않습니다. 애플리케이션 워크로드에서 관리되는 도메인을 찾을 수 있게 하려면 연결하는 가상 네트워크의 이름 확인을 구성해야 합니다.

연결하는 가상 네트워크를 지원하는 DNS 서버에서 조건부 DNS 전달자를 통해 또는 관리되는 도메인의 가상 네트워크에서 동일한 DNS IP 주소를 통해 이름 확인 기능을 사용할 수 있습니다.

## <a name="network-resources-used-by-azure-ad-ds"></a>Azure AD DS에서 사용하는 네트워크 리소스

관리되는 도메인은 배포 중에 일부 네트워킹 리소스를 만듭니다. 해당 리소스는 관리되는 도메인의 성공적인 운영과 관리에 필요하며 수동으로 구성하면 안 됩니다.

| Azure 리소스                          | Description |
|:----------------------------------------|:---|
| 네트워크 인터페이스 카드                  | Azure AD DS는 Windows Server에서 Azure VM으로 실행되는 두 개의 DC(도메인 컨트롤러)에서 관리되는 도메인을 호스트합니다. 각 VM에는 가상 네트워크 서브넷에 연결하는 가상 네트워크 인터페이스가 있습니다. |
| 동적 표준 공용 IP 주소      | Azure AD DS는 표준 SKU 공용 IP 주소를 사용하여 동기화 및 관리 서비스와 통신합니다. 공용 IP 주소에 대한 자세한 내용은 [Azure의 IP 주소 유형과 할당 메서드](../virtual-network/ip-services/public-ip-addresses.md)를 참조하세요. |
| Azure 표준 부하 분산 장치            | Azure AD DS는 NAT(Network Address Translation) 및 부하 분산(보안 LDAP와 함께 사용되는 경우)에 표준 SKU 부하 분산 장치를 사용합니다. Azure 부하 분산 장치에 대한 자세한 내용은 [Azure Load Balancer란?](../load-balancer/load-balancer-overview.md)을 참조하세요. |
| NAT(Network Address Translation) 규칙 | Azure AD DS는 부하 분산 장치에서 보안 PowerShell 원격에 대한 두 개의 인바운드 NAT 규칙을 만들고 사용합니다. 표준 SKU 부하 분산 장치를 사용하는 경우 아웃바운드 NAT 규칙도 있습니다. 기본 SKU 부하 분산 장치의 경우 아웃바운드 NAT 규칙이 필요하지 않습니다. |
| 부하 분산 장치 규칙                     | TCP 포트 636에서 보안 LDAP에 대해 관리되는 도메인을 구성하면 부하 분산 장치에서 세 가지 규칙이 만들어지고 트래픽을 분산하는 데 사용됩니다. |

> [!WARNING]
> Azure AD DS에서 만든 네트워크 리소스를 삭제하거나 수정해서는 안 됩니다(예: 부하 분산 장치나 규칙을 수동으로 구성). 네트워크 리소스를 삭제하거나 수정하면 Azure AD DS 서비스 중단이 발생할 수 있습니다.

## <a name="network-security-groups-and-required-ports"></a>네트워크 보안 그룹과 필수 포트

[NSG(네트워크 보안 그룹)](../virtual-network/network-security-groups-overview.md)에는 Azure 가상 네트워크의 네트워크 트래픽을 허용하거나 거부하는 규칙 목록이 포함되어 있습니다. 관리되는 도메인을 배포할 때 서비스에서 인증 및 관리 기능을 제공할 수 있는 규칙 집합이 포함된 네트워크 보안 그룹이 만들어집니다. 이 기본 네트워크 보안 그룹은 관리되는 도메인이 배포되는 가상 네트워크 서브넷과 연결됩니다.

다음 섹션에서는 네트워크 보안 그룹과 인바운드 및 아웃바운드 포트 요구 사항을 다룹니다.

### <a name="inbound-connectivity"></a>인바운드 연결

관리되는 도메인에서 인증 및 관리 서비스를 제공하려면 다음 네트워크 보안 그룹 인바운드 규칙이 필요합니다. 관리되는 도메인이 배포된 가상 네트워크 서브넷에 대한 네트워크 보안 그룹 규칙을 편집하거나 삭제하지 마세요.

| 인바운드 포트 번호 | 프로토콜 | 원본                             | 대상 | 작업 | 필수 | 목적 |
|:-----------:|:--------:|:----------------------------------:|:-----------:|:------:|:--------:|:--------|
| 5986        | TCP      | AzureActiveDirectoryDomainServices | 모두         | 허용  | 예      | 도메인 관리 |
| 3389        | TCP      | CorpNetSaw                         | 모두         | Allow  | Optional      | 지원 디버깅 |

이러한 규칙을 적용하는 Azure 표준 부하 분산 장치가 생성됩니다. 이 네트워크 보안 그룹은 Azure AD DS를 보호하며, 관리되는 도메인이 제대로 작동하는 데 꼭 필요합니다. 이 네트워크 보안 그룹을 삭제하지 마세요. 이 보안 그룹이 없으면 부하 분산 장치가 제대로 작동하지 않습니다.

필요한 경우 [Azure PowerShell을 사용하여 필요한 네트워크 보안 그룹 및 규칙을 만들](powershell-create-instance.md#create-a-network-security-group) 수 있습니다.

> [!WARNING]
> 잘못 구성된 네트워크 보안 그룹 또는 사용자 정의 경로 테이블을 관리되는 도메인이 배포된 서브넷과 연결하면 도메인을 서비스하고 관리하는 Microsoft의 기능을 방해할 수 있습니다. Azure AD 테넌트와 관리되는 도메인 간의 동기화도 중단됩니다. 동기화, 패치 또는 관리를 중단할 수 있는 지원되지 않는 구성을 방지하려면 나열된 모든 요구 사항을 따르세요.
>
> 보안 LDAP를 사용하는 경우 외부 트래픽을 허용하는 데 필요한 TCP 포트 636 규칙을 추가할 수도 있습니다. 이 규칙을 추가하더라도 네트워크 보안 그룹 규칙에 대한 지원이 끊기지 않습니다. 자세한 내용은 [인터넷을 통한 보안 LDAP 액세스 잠금](tutorial-configure-ldaps.md#lock-down-secure-ldap-access-over-the-internet)을 참조하세요.
>
> Azure SLA는 잘못 구성된 네트워크 보안 그룹 또는 사용자 정의 경로 테이블에 의해 업데이트 또는 관리가 차단된 배포에는 적용되지 않습니다. 네트워크 구성이 손상되면 보안 패치가 적용되지 않을 수도 있습니다.

### <a name="outbound-connectivity"></a>아웃바운드 연결

아웃바운드 연결의 경우 **AllowVnetOutbound** 및 **AllowInternetOutBound** 를 유지하거나 다음 표에 나열된 ServiceTags를 사용하여 아웃바운드 트래픽을 제한할 수 있습니다. AzureUpdateDelivery용 ServiceTag는 [PowerShell](powershell-create-instance.md)을 통해 추가해야 합니다.

필터링된 아웃바운드 트래픽은 클래식 배포에서 지원되지 않습니다.


| 아웃바운드 포트 번호 | 프로토콜 | 원본 | 대상   | 작업 | 필수 | 용도 |
|:--------------------:|:--------:|:------:|:-------------:|:------:|:--------:|:-------:|
| 443 | TCP   | 모두    | AzureActiveDirectoryDomainServices| Allow  | 예      | Azure AD Domain Services 관리 서비스와의 통신. |
| 443 | TCP   | 모두    | AzureMonitor                      | 허용  | 예      | 가상 머신 모니터링. |
| 443 | TCP   | 모두    | 스토리지                           | 허용  | 예      | Azure Storage와의 통신.   | 
| 443 | TCP   | 모두    | AzureActiveDirectory              | Allow  | 예      | Azure Active Directory와의 통신.  |
| 443 | TCP   | 모두    | AzureUpdateDelivery               | Allow  | 예      | Windows 업데이트와 통신.  | 
| 80  | TCP   | 모두    | AzureFrontDoor.FirstParty         | Allow  | 예      | Windows 업데이트에서 패치 다운로드.    |
| 443 | TCP   | 모두    | GuestAndHybridManagement          | Allow  | 예      | 보안 패치의 자동화된 관리.   |


### <a name="port-5986---management-using-powershell-remoting"></a>포트 5986 - PowerShell 원격을 사용하여 관리

* 관리되는 도메인에서 PowerShell 원격을 사용하여 관리 작업을 수행하는 데 사용됩니다.
* 이 포트에 대한 액세스 권한이 없으면 관리되는 도메인을 업데이트, 구성, 백업 또는 모니터링할 수 없습니다.
* Resource Manager 기반의 가상 네트워크를 사용하는 관리되는 도메인의 경우 이 포트에 대한 인바운드 액세스를 *AzureActiveDirectoryDomainServices* 서비스 태그로 제한할 수 있습니다.
    * 클래식 기반의 가상 네트워크를 사용하는 관리되는 레거시 도메인의 경우 이 포트에 대한 인바운드 액세스를 다음 원본 IP 주소로 제한할 수 있습니다. *52.180.183.8*, *23.101.0.70*, *52.225.184.198*, *52.179.126.223*, *13.74.249.156*, *52.187.117.83*, *52.161.13.95*, *104.40.156.18*, *104.40.87.209*.

    > [!NOTE]
    > 2017년에 Azure AD Domain Services를 Azure Resource Manager 네트워크에 호스트할 수 있게 되었습니다. 그 후로 Azure Resource Manager의 최신 기능을 사용하여 더욱 안전한 서비스를 구축할 수 있었습니다. Azure Resource Manager 배포가 클래식 배포를 완전히 대체하므로 Azure AD DS 클래식 가상 네트워크 배포는 2023년 3월 1일에 사용 중지됩니다.
    >
    > 자세한 내용은 [공식 사용 중단 알림](https://azure.microsoft.com/updates/we-are-retiring-azure-ad-domain-services-classic-vnet-support-on-march-1-2023/)을 참조하세요.

### <a name="port-3389---management-using-remote-desktop"></a>포트 3389 - 원격 데스크톱을 사용하여 관리

* 관리되는 도메인의 도메인 컨트롤러에 대한 원격 데스크톱 연결에 사용됩니다.
* 기본 네트워크 보안 그룹 규칙은 *CorpNetSaw* 서비스 태그를 사용하여 트래픽을 추가로 제한합니다.
    * 이 서비스 태그는 Microsoft 회사 네트워크의 보안 액세스 워크스테이션만 관리되는 도메인에 원격 데스크톱을 사용하도록 허용합니다.
    * 액세스는 관리 또는 문제 해결 시나리오와 같은 비즈니스 타당성이 있는 경우에만 허용됩니다.
* 이 규칙은 ‘거부’로 설정하고 필요한 경우에만 ‘허용’으로 설정할 수 있습니다.  대부분의 관리와 모니터링 작업은 PowerShell 원격을 사용하여 수행됩니다. RDP는 Microsoft가 고급 문제 해결을 위해 관리되는 도메인에 원격으로 연결해야 하는 드문 경우에만 사용됩니다.


이 네트워크 보안 그룹 규칙을 편집하려고 하는 경우 포털에서 *CorpNetSaw* 서비스 태그를 수동으로 선택할 수 없습니다. *CorpNetSaw* 서비스 태그를 사용하는 규칙을 수동으로 구성하려면 Azure PowerShell 또는 Azure CLI를 사용해야 합니다.

예를 들어 다음 스크립트를 사용하여 RDP를 허용하는 규칙을 만들 수 있습니다. 

```powershell
Get-AzNetworkSecurityGroup -Name "nsg-name" -ResourceGroupName "resource-group-name" | Add-AzNetworkSecurityRuleConfig -Name "new-rule-name" -Access "Allow" -Protocol "TCP" -Direction "Inbound" -Priority "priority-number" -SourceAddressPrefix "CorpNetSaw" -SourcePortRange "*" -DestinationPortRange "3389" -DestinationAddressPrefix "*" | Set-AzNetworkSecurityGroup
```

## <a name="user-defined-routes"></a>사용자 정의 경로

사용자 정의 경로는 기본적으로 생성되지 않으며 Azure AD DS가 제대로 작동하는 데 필요하지 않습니다. 경로 테이블을 사용해야 하는 경우 *0.0.0.0* 경로를 변경하지 마세요. 이 경로를 변경하면 Azure AD DS가 중단되고 관리되는 도메인이 지원되지 않는 상태가 됩니다.

또한 각 Azure 서비스 태그에 포함된 IP 주소에서 관리되는 도메인의 서브넷으로 인바운드 트래픽을 라우팅해야 합니다. 서비스 태그 및 연결된 IP 주소에 대한 자세한 내용은 [Azure IP 범위와 서비스 태그 - 퍼블릭 클라우드](https://www.microsoft.com/en-us/download/details.aspx?id=56519)를 참조하세요.

> [!CAUTION]
> Azure 데이터 센터 IP 범위는 통지 없이 변경할 수 있습니다. 최신 IP 주소가 있는지 확인하는 프로세스가 있는지 확인합니다.

## <a name="next-steps"></a>다음 단계

Azure AD DS에 사용되는 일부 네트워크 리소스와 연결 옵션에 대한 자세한 내용은 다음 문서를 참조하세요.

* [Azure 가상 네트워크 피어링](../virtual-network/virtual-network-peering-overview.md)
* [Azure VPN Gateway](../vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md)
* [Azure 네트워크 보안 그룹](../virtual-network/network-security-groups-overview.md)