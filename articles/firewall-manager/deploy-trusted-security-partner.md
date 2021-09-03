---
title: Azure Firewall Manager 보안 파트너 공급자 배포
description: Azure Portal를 사용하여 Azure Firewall Manager 보안 파트너 공급자를 배포하는 방법을 알아봅니다.
services: firewall-manager
author: vhorne
ms.service: firewall-manager
ms.topic: how-to
ms.date: 08/06/2021
ms.author: victorh
ms.openlocfilehash: 7b8dd13c5d2c3c080ca20115dfc41b23dd6e545e
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122528252"
---
# <a name="deploy-a-security-partner-provider"></a>보안 파트너 공급자 배포

Azure Firewall Manager의 *보안 파트너 공급자* 를 통해 익숙한 동급 최고의 타사 SECaaS(Security as a Service) 제품을 사용하여 사용자를 위해 인터넷 액세스를 보호할 수 있습니다.

지원되는 시나리오 또는 모범 사례 지침에 대한 자세한 정보는 [보안 파트너 공급자란?](trusted-security-partners.md)을 참조하세요.


이제 통합된 타사 SECaaS(Security as a service) 파트너를 사용할 수 있습니다. 

- **Zscaler**
- **[Check Point](check-point-overview.md)**
- **iboss**

## <a name="deploy-a-third-party-security-provider-in-a-new-hub"></a>새 허브에 타사 보안 공급자 배포

기존 허브에 타사 공급자를 배포하는 경우 이 섹션을 건너뜁니다.

1. https://portal.azure.com 에서 Azure Portal에 로그인합니다.
2. **검색** 에서 **Firewall Manager** 를 입력하고 **서비스** 에서 선택합니다.
3. **시작하기** 로 이동합니다. **보안 가상 허브 보기** 를 선택합니다.
4. **보안 가상 허브 새로 만들기** 를 선택합니다.
5. 구독 및 리소스 그룹을 입력하고, 지원되는 지역을 선택하고, 허브 및 Virtual WAN 정보를 추가합니다. 
6. **보안 파트너 공급자를 사용하도록 설정하기 위해 VPN 게이트웨이 포함** 을 선택합니다.
7. 요구 사항에 적합한 **게이트웨이 배율 단위** 를 선택합니다.
8. **다음:Azure Firewall** 을 선택합니다.
   > [!NOTE]
   > 보안 파트너 공급자는 VPN Gateway 터널을 사용하여 허브에 연결합니다. VPN Gateway를 삭제하면 보안 파트너 공급자 연결이 끊어집니다.
9. Azure Firewall을 배포하여 타사 서비스 공급자와 함께 프라이빗 트래픽을 필터링하고 인터넷 트래픽을 필터링하려면 Azure Firewall 정책을 선택합니다. [지원되는 시나리오](trusted-security-partners.md#key-scenarios)를 참조하세요.
10. 허브에 타사 보안 공급자만 배포하려는 경우 **Azure Firewall: 사용/사용 안 함** 을 선택하여 **사용 안 함** 으로 설정합니다. 
11. **다음: 보안 파트너 공급자** 를 선택합니다.
12. **보안 파트너 공급자** 를 **사용** 으로 설정합니다. 
13. 파트너를 선택합니다. 
14. 완료되면 **다음: 리뷰 + 만들기** 를 클릭합니다. 
15. 콘텐츠를 검토한 다음, **만들기** 를 선택합니다.

VPN 게이트웨이 배포를 완료하는 데 30분 이상이 걸릴 수 있습니다.

허브가 만들어졌는지 확인하려면 Azure Firewall Manager->보안 허브로 이동합니다. 허브->개요 페이지를 선택하여 파트너 이름과 상태를 **보안 연결 보류 중** 으로 표시합니다.

허브가 만들어지고 보안 파트너가 설정되면 계속해서 보안 공급자를 허브에 연결합니다.

## <a name="deploy-a-third-party-security-provider-in-an-existing-hub"></a>기존 허브에 타사 보안 공급자 배포

Virtual WAN에서 기존 허브를 선택하고 *보안 가상 허브* 로 변환할 수도 있습니다.

1. **시작** 에서 **보안 가상 허브 보기** 를 선택합니다.
2. **기존 허브 변환** 을 선택합니다.
3. 구독과 기존 허브를 선택합니다. 새 허브에 타사 공급자를 배포하려면 나머지 단계를 수행합니다.

타사 공급자를 사용하여 기존 허브를 보안 허브로 변환하려면 VPN 게이트웨이를 배포해야 합니다.

## <a name="configure-third-party-security-providers-to-connect-to-a-secured-hub"></a>보안 허브에 연결하도록 타사 보안 공급자 구성

가상 허브의 VPN Gateway에 대한 터널을 설정하려면 타사 공급자에 허브에 대한 액세스 권한이 있어야 합니다. 이 작업을 수행하려면 서비스 주체를 구독 또는 리소스 그룹에 연결하고 액세스 권한을 부여합니다. 그런 다음 해당 포털을 사용하여 타사에 자격 증명을 제공해야 합니다.

> [!NOTE]
> 타사 보안 공급자가 사용자 대신 VPN 사이트를 만듭니다. 이 VPN 사이트는 Azure Portal에 표시되지 않습니다.

### <a name="create-and-authorize-a-service-principal"></a>서비스 주체 만들기 및 권한 부여

1. Azure Active Directory(AD) 서비스 주체 만들기: 리디렉션 URL을 건너뛸 수 있습니다. 

   [방법: 포털을 사용하여 리소스에 액세스할 수 있는 Azure AD 애플리케이션 및 서비스 주체 만들기](../active-directory/develop/howto-create-service-principal-portal.md#register-an-application-with-azure-ad-and-create-a-service-principal)
2. 서비스 주체에 대한 액세스 권한 및 범위를 추가합니다.
   [방법: 포털을 사용하여 리소스에 액세스할 수 있는 Azure AD 애플리케이션 및 서비스 주체 만들기](../active-directory/develop/howto-create-service-principal-portal.md#register-an-application-with-azure-ad-and-create-a-service-principal)

   > [!NOTE]
   > 더 세분화된 컨트롤을 위해 리소스 그룹에만 액세스를 제한할 수 있습니다.

### <a name="visit-partner-portal"></a>파트너 포털 방문

1. 파트너에게 제공된 지침에 따라 설치를 완료합니다. 여기에는 검색하고 허브에 연결할 AAD 정보를 제출하고, 송신 정책을 업데이트하고, 연결 상태와 로그를 확인하는 작업이 포함됩니다.

   - [Zscaler: Microsoft Azure Virtual WAN 통합 구성](https://help.zscaler.com/zia/configuring-microsoft-azure-virtual-wan-integration).
   - [Check Point: Microsoft Azure Virtual WAN 통합 구성](https://sc1.checkpoint.com/documents/Infinity_Portal/WebAdminGuides/EN/CloudGuard-Connect-Azure-Virtual-WAN/Default.htm).
   - [iboss: Microsoft Azure Virtual WAN 통합 구성](https://www.iboss.com/blog/securing-microsoft-azure-with-iboss-saas-network-security). 
   
2. Azure의 Azure Virtual WAN 포털에서 터널 만들기 상태를 확인할 수 있습니다. Azure와 파트너 포털 모두에서 터널에 **연결됨** 이 표시되면 다음 단계에서 경로를 설정하고 파트너에게 인터넷 트래픽을 보낼 분기와 VNet를 선택합니다.

## <a name="configure-security-with-firewall-manager"></a>Firewall Manager를 사용하여 보안 구성

1. Azure Firewall Manager -> 보안 허브로 이동합니다. 
2. 허브를 선택합니다. 이제 허브 상태가 **보안 연결 보류** 가 아닌 **프로비저닝됨** 으로 표시됩니다.

   타사 공급자가 허브에 연결할 수 있는지 확인합니다. VPN 게이트웨이의 터널은 **연결됨** 상태여야 합니다. 이 상태는 이전 상태보다 허브와 타사 파트너 간의 연결 상태를 더 반영합니다.
3. 허브를 선택하고 **보안 구성** 으로 이동합니다.

   허브에 타사 공급자를 배포하면 허브를 *보안 가상 허브* 로 변환합니다. 이렇게 하면 타사 공급자가 0.0.0.0/0(기본값) 경로를 허브로 보급합니다. 그러나 이 기본 경로를 가져올 연결을 옵트인하지 않는 한, 허브에 연결된 VNet 연결 및 사이트는 이 경로를 가져오지 않습니다.
4. Azure Firewall을 통해 **인터넷 트래픽** 을 설정하고 신뢰할 수 있는 보안 파트너를 통해 **프라이빗 트래픽** 을 설정하여 Virtual WAN 보안을 구성합니다. 그러면 Virtual WAN의 개별 연결이 자동으로 보호됩니다.

   :::image type="content" source="media/deploy-trusted-security-partner/security-configuration.png" alt-text="보안 구성":::
5. 또한 조직에서 가상 네트워크 및 지점의 공용 IP 범위를 사용하는 경우 **프라이빗 트래픽 접두사** 를 사용하여 해당 IP 접두사를 명시적으로 지정해야 합니다. 공용 IP 주소 접두사는 개별적으로 지정하거나 집계로 지정할 수 있습니다.

   프라이빗 트래픽 접두사에 RFC1918 이외의 주소를 사용하는 경우 RFC1918이 아닌 프라이빗 트래픽에 SNAT를 사용하지 않도록 방화벽에 대한 SNAT 정책을 구성해야 할 수 있습니다. 기본적으로 Azure Firewall은 RFC1918 이외의 모든 트래픽을 SNAT합니다.

## <a name="branch-or-vnet-internet-traffic-via-third-party-service"></a>타사 서비스를 통한 분기 또는 VNet 인터넷 트래픽

그런 다음 VNet 가상 머신 또는 분기 사이트에서 인터넷에 액세스할 수 있는지 확인하고 트래픽이 타사 서비스로 전달되는지 확인할 수 있습니다.

경로 설정 단계를 완료한 후에 VNet 가상 머신과 분기 사이트에는 타사 서비스 경로의 0/0이 전송됩니다. 가상 머신에 RDP 또는 SSH할 수 없습니다. 로그인하려면 피어링된 VNet에 [Azure Bastion](../bastion/bastion-overview.md) 서비스를 배포합니다.

## <a name="next-steps"></a>다음 단계

- [자습서: Azure Portal을 통해 Azure Firewall Manager를 사용하여 클라우드 네트워크 보호](secure-cloud-network.md)