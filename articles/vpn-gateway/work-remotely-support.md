---
title: 원격 작업과 지점 및 사이트 간 VPN 게이트웨이
titleSuffix: Azure VPN Gateway
description: 코로나19 전염병으로 인해 VPN Gateway 지점 및 사이트 간 연결을 사용하여 원격으로 작업하는 방법에 대해 알아봅니다.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 09/28/2021
ms.author: alzam
ms.openlocfilehash: b0988547105f953c0665ea753007c0725055fe61
ms.sourcegitcommit: e8c34354266d00e85364cf07e1e39600f7eb71cd
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/29/2021
ms.locfileid: "129208714"
---
# <a name="remote-work-using-azure-vpn-gateway-point-to-site"></a>지점 및 사이트 간 Azure VPN Gateway를 사용한 원격 작업

>[!NOTE]
>이 문서에서는 Azure VPN Gateway, Azure, Microsoft 네트워크 및 Azure 파트너 에코시스템을 활용하여 원격으로 작업하고 코로나19 위기로 인해 직면한 네트워크 문제를 완화하는 방법을 설명합니다.
>

이 문서에서는 조직에서 사용자를 위한 원격 액세스를 설정하거나 COVID-19 유행 동안 추가 용량으로 기존 솔루션을 보완하는 데 사용할 수 있는 옵션에 대해 설명합니다.

지점 및 사이트 간 Azure 솔루션은 클라우드 기반이며 재택 근무를 위해 증가하는 사용자 수요를 충족하기 위해 신속하게 프로비저닝할 수 있습니다. 증가된 용량이 더 이상 필요하지 않으면 쉽게 스케일 업할 수 있으며 쉽고 빠르게 끌 수 있습니다.

## <a name="about-point-to-site-vpn"></a><a name="p2s"></a>지점 및 사이트 간 VPN 연결 정보

P2S(지점 및 사이트 간) VPN 게이트웨이 연결을 사용하면 개별 클라이언트 컴퓨터에서 가상 네트워크에 대한 안전한 연결을 만들 수 있습니다. P2S 연결은 클라이언트 컴퓨터에서 시작하여 설정됩니다. 이 솔루션은 집 또는 회의실과 같은 원격 위치에서 Azure VNet 또는 온-프레미스 데이터 센터에 연결하려는 재택 근무자에게 유용합니다. 이 문서에서는 다양한 시나리오에 따라 사용자가 원격으로 작업할 수 있도록 하는 방법을 설명합니다.

아래 표는 클라이언트 운영 체제와 사용 가능한 인증 옵션을 보여 줍니다. 이미 사용 중인 클라이언트 OS에 따라 인증 방법을 선택하는 것이 좋습니다. 예를 들어 연결해야 하는 클라이언트 운영 체제가 혼합되어 있는 경우 인증서 기반 인증을 사용하여 OpenVPN을 선택합니다. 또한 지점 및 사이트 간 VPN은 경로 기반 VPN Gateway에서만 지원됩니다.

![클라이언트 운영 체제 및 사용 가능한 인증 옵션을 보여 주는 스크린샷](./media/working-remotely-support/os-table.png "OS")

## <a name="scenario-1---users-need-access-to-resources-in-azure-only"></a><a name="scenario1"></a>시나리오 1 - 사용자는 Azure의 리소스에만 액세스해야 합니다.

이 시나리오에서 원격 사용자는 Azure에 있는 리소스에만 액세스하면 됩니다.

![Azure의 리소스에만 액세스해야 하는 사용자를 위해 지점 및 사이트 간 시나리오를 보여 주는 다이어그램](./media/working-remotely-support/scenario1.png "시나리오 1")

높은 수준에서 사용자가 Azure 리소스에 안전하게 연결할 수 있도록 하려면 다음 단계를 수행해야 합니다.

1. 가상 네트워크 게이트웨이를 만듭니다(존재하지 않는 경우).
2. 게이트웨이에서 지점 및 사이트 간 VPN을 구성합니다.

   * 인증서 인증의 경우 [이 링크](vpn-gateway-howto-point-to-site-resource-manager-portal.md#creategw)를 따릅니다.
   * OpenVPN의 경우 [이 링크](vpn-gateway-howto-openvpn.md)를 따릅니다.
   * Azure AD 인증의 경우 [이 링크](openvpn-azure-ad-tenant.md)를 따릅니다.
   * 지점 및 사이트 간 연결 문제를 해결하려면 [이 링크](vpn-gateway-troubleshoot-vpn-point-to-site-connection-problems.md)를 따릅니다.
3. VPN 클라이언트 구성 다운로드 및 배포
4. 인증서(인증서 인증을 선택한 경우)를 클라이언트에 배포합니다.
5. Azure VPN에 연결

## <a name="scenario-2---users-need-access-to-resources-in-azure-andor-on-prem-resources"></a><a name="scenario2"></a>시나리오 2 - 사용자는 Azure 리소스 및/또는 온-프레미스 리소스에 액세스해야 합니다.

이 시나리오에서 원격 사용자는 Azure 및 온-프레미스 데이터 센터에 있는 리소스에 액세스해야 합니다.

![Azure 리소스에 액세스해야 하는 사용자를 위한 지점 및 사이트 간 시나리오를 보여 주는 다이어그램.](./media/working-remotely-support/scenario2.png "시나리오 2")

높은 수준에서 사용자가 Azure 리소스에 안전하게 연결할 수 있도록 하려면 다음 단계를 수행해야 합니다.

1. 가상 네트워크 게이트웨이를 만듭니다(존재하지 않는 경우).
2. 게이트웨이에서 지점 및 사이트 간 VPN을 구성합니다([시나리오 1](#scenario1) 참조).
3. BGP를 사용하도록 설정한 Azure 가상 네트워크 게이트웨이에서 사이트 간 터널을 구성합니다.
4. 온-프레미스 디바이스를 구성하여 Azure 가상 네트워크 게이트웨이에 연결합니다.
5. Azure Portal에서 지점 및 사이트 간 프로필을 다운로드하고 클라이언트에 배포합니다.

사이트 간 VPN 터널을 설정하는 방법을 알아보려면 [이 링크](./tutorial-site-to-site-portal.md)를 참조하세요.

## <a name="faq-for-native-azure-certificate-authentication"></a><a name="faqcert"></a>네이티브 Azure 인증서 인증에 대한 FAQ

[!INCLUDE [vpn-gateway-point-to-site-faq-include](../../includes/vpn-gateway-faq-p2s-azurecert-include.md)]

## <a name="faq-for-radius-authentication"></a><a name="faqradius"></a>RADIUS 인증에 대한 FAQ

[!INCLUDE [vpn-gateway-point-to-site-faq-include](../../includes/vpn-gateway-faq-p2s-radius-include.md)]

## <a name="next-steps"></a>다음 단계

* [P2S 연결 구성 - Azure AD 인증](openvpn-azure-ad-tenant.md)

* [P2S 연결 구성 - RADIUS 인증](point-to-site-how-to-radius-ps.md)

* [P2S 연결 구성 - Azure 네이티브 인증서 인증](vpn-gateway-howto-point-to-site-rm-ps.md)

**"OpenVPN"은 OpenVPN Inc의 상표입니다.**