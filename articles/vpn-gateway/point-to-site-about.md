---
title: Azure 지점 및 사이트 간 VPN 연결 정보
titleSuffix: Azure VPN Gateway
description: 지점 및 사이트 간 VPN에 대해 알아봅니다.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 07/28/2021
ms.author: cherylmc
ms.openlocfilehash: f0c37bf151c786559e005006a8fff407b735bbf8
ms.sourcegitcommit: 1e9139680ca51f55ac965c4dd6dd82bf2fd43675
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/04/2021
ms.locfileid: "133542328"
---
# <a name="about-point-to-site-vpn"></a>지점 및 사이트 간 VPN 연결 정보

P2S(지점 및 사이트 간) VPN 게이트웨이 연결을 사용하면 개별 클라이언트 컴퓨터에서 가상 네트워크에 대한 안전한 연결을 만들 수 있습니다. P2S 연결은 클라이언트 컴퓨터에서 시작하여 설정됩니다. 이 솔루션은 집 또는 회의실과 같은 원격 위치에서 Azure VNet에 연결하려는 재택 근무자에게 유용합니다. 또한 P2S VPN은 VNet에 연결해야 하는 클라이언트가 몇 개만 있는 경우 S2S VPN 대신 사용할 수 있는 유용한 솔루션입니다. 이 문서는 [Resource Manager 배포 모델](../azure-resource-manager/management/deployment-models.md)에 적용됩니다.

## <a name="what-protocol-does-p2s-use"></a><a name="protocol"></a>P2S는 어떤 프로토콜을 사용합니까?

지점 및 사이트 간 VPN에서는 다음 프로토콜 중 하나를 사용할 수 있습니다.

* **OpenVPN® 프로토콜** 로, SSL/TLS 기반 VPN 프로토콜입니다. 대부분의 방화벽에서 TLS가 사용되는 TCP 포트 443 아웃바운드를 열기 때문에 TLS VPN 솔루션이 방화벽을 통과할 수 있습니다. OpenVPN은 Android, iOS(버전 11.0 이상), Windows, Linux, Mac 디바이스(macOS 버전 10.13 이상)에서 연결하는 데 사용할 수 있습니다.

* 독점 TLS 기반 VPN 프로토콜인 **SSTP (Secure Socket Tunneling protocol)** 대부분의 방화벽에서 TLS가 사용되는 TCP 포트 443 아웃바운드를 열기 때문에 TLS VPN 솔루션이 방화벽을 통과할 수 있습니다. SSTP는 Windows 디바이스에서만 지원됩니다. Azure는 SSTP가 설치되고 TLS 1.2를 지원하는 모든 Windows 버전(Windows 8.1 이상)을 지원합니다.

* **IKEV2 vpn**, 표준 기반 IPsec vpn 솔루션. IKEv2 VPN은 Mac 디바이스(macOS 버전 10.11 이상)에서 연결하는 데 사용할 수 있습니다.


>[!NOTE]
>P2S용 IKEv2 및 OpenVPN은 [Resource Manager 배포 모델](../azure-resource-manager/management/deployment-models.md)에서만 사용할 수 있습니다. 클래식 배포 모델에서는 사용할 수 없습니다.
>

## <a name="how-are-p2s-vpn-clients-authenticated"></a><a name="authentication"></a>P2S VPN 클라이언트는 어떻게 인증됩니까?

Azure에서 P2S VPN 연결을 허용하기 전에 먼저 사용자를 인증해야 합니다. 연결하는 사용자를 인증하기 위해 Azure에서 제공하는 두 가지 메커니즘이 있습니다.

### <a name="authenticate-using-native-azure-certificate-authentication"></a>Azure 기본 인증서 인증을 사용하여 인증

Azure 기본 인증서 인증을 사용하는 경우 디바이스에 있는 클라이언트 인증서가 연결하는 사용자를 인증하는 데 사용됩니다. 클라이언트 인증서는 신뢰할 수 있는 루트 인증서에서 생성되어 각 클라이언트 컴퓨터에 설치됩니다. 엔터프라이즈 솔루션을 사용하여 생성된 루트 인증서를 사용하거나 자체 서명된 인증서를 생성할 수 있습니다.

클라이언트 인증서의 유효성 검사는 VPN 게이트웨이에서 수행되며, P2S VPN 연결을 설정하는 동안 발생합니다. 루트 인증서는 유효성 검사에 필요하며 Azure에 업로드해야 합니다.

### <a name="authenticate-using-native-azure-active-directory-authentication"></a>네이티브 Azure Active Directory 인증을 사용하여 인증

Azure AD 인증을 사용하면 사용자가 자신의 Azure Active Directory 자격 증명을 사용하여 Azure에 연결할 수 있습니다. 네이티브 Azure AD 인증은 OpenVPN 프로토콜 및 Windows 10에서만 지원되며 [Azure VPN Client](https://go.microsoft.com/fwlink/?linkid=2117554)를 사용해야 합니다.

네이티브 Azure AD 인증을 사용하여 Azure AD의 조건부 액세스뿐만 아니라 VPN에 MFA(Multi-Factor Authentication) 기능을 활용할 수 있습니다.

상위 수준에서 다음 단계를 수행하여 Azure AD 인증을 구성해야 합니다.

1. [Azure AD 테넌트 구성](openvpn-azure-ad-tenant.md)

2. [게이트웨이에서 Azure AD 인증 사용](openvpn-azure-ad-tenant.md#enable-authentication)

3. [Azure VPN Client 다운로드 및 구성](https://go.microsoft.com/fwlink/?linkid=2117554)


### <a name="authenticate-using-active-directory-ad-domain-server"></a>Azure AD(Active Directory) 도메인 서버를 사용하여 인증

AD 도메인 인증을 사용하면 사용자가 자신의 조직 도메인 자격 증명을 사용하여 Azure에 연결할 수 있습니다. AD 서버와 통합되는 RADIUS 서버가 필요합니다. 또한 조직에서 기존 RADIUS 배포를 활용할 수도 있습니다.

RADIUS 서버를 온-프레미스 또는 Azure VNet에 배포할 수 있습니다. 인증하는 동안 Azure VPN Gateway에서 통과 역할을 수행하여 RADIUS 서버와 연결된 디바이스 간에 인증 메시지를 전달합니다. 따라서 RADIUS 서버에 대한 게이트웨이 연결 가능성이 중요합니다. RADIUS 서버가 온-프레미스에 있는 경우 연결 가능성을 위해 Azure에서 온-프레미스 사이트로의 VPN S2S 연결이 필요합니다.

RADIUS 서버는 AD 인증서 서비스와 통합할 수도 있습니다. 이렇게 하면 Azure 인증서 인증 대신 P2S 인증서 인증용 RADIUS 서버 및 엔터프라이즈 인증서 배포를 사용할 수 있습니다. 장점은 루트 인증서와 해지된 인증서를 Azure에 업로드할 필요가 없다는 것입니다.

또한 RADIUS 서버는 다른 외부 ID 시스템과 통합할 수도 있습니다. 이렇게 하면 다단계 옵션을 포함하여 P2S VPN에 대한 많은 인증 옵션이 제공됩니다.

![온-프레미스 사이트와의 지점 및 사이트 간 VPN을 보여 주는 다이어그램.](./media/point-to-site-about/p2s.png)

## <a name="what-are-the-client-configuration-requirements"></a>클라이언트 구성 요구 사항은 어떻게 되나요?

>[!NOTE]
>Windows 클라이언트의 경우 클라이언트 디바이스에서 Azure로 VPN 연결을 시작하려면 클라이언트 디바이스에 대한 관리자 권한이 있어야 합니다.
>

사용자는 P2S용 Windows 및 Mac 디바이스에서 VPN 기본 클라이언트를 사용합니다. Azure는 이러한 기본 클라이언트에서 Azure에 연결하는 데 필요한 설정을 포함하고 있는 VPN 클라이언트 구성 zip 파일을 제공합니다.

* Windows 디바이스의 경우 VPN 클라이언트 구성은 사용자가 자신의 디바이스에 설치하는 설치 관리자 패키지로 구성됩니다.
* Mac 디바이스의 경우 사용자가 디바이스에 설치하는 mobileconfig 파일로 구성됩니다.

또한 zip 파일에서 중요한 Azure 쪽 설정 일부에 대한 값을 제공하므로 이러한 디바이스에 대한 사용자 고유의 프로필을 만들 수 있습니다. 일부 값에는 VPN 게이트웨이 주소, 구성된 터널 종류, 경로 및 게이트웨이 유효성 검사용 루트 인증서가 포함됩니다.

>[!NOTE]
>[!INCLUDE [TLS version changes](../../includes/vpn-gateway-tls-change.md)]
>

## <a name="which-gateway-skus-support-p2s-vpn"></a><a name="gwsku"></a>어떤 게이트웨이 SKU에서 P2S VPN을 지원하나요?

[!INCLUDE [aggregate throughput sku](../../includes/vpn-gateway-table-gwtype-aggtput-include.md)]

* 게이트웨이 SKU 권장 사항에 대해서는 [VPN Gateway 설정 정보](vpn-gateway-about-vpn-gateway-settings.md#gwsku)를 참조하세요.

>[!NOTE]
>기본 SKU는 IKEv2 또는 RADIUS 인증을 지원하지 않습니다.
>

## <a name="what-ikeipsec-policies-are-configured-on-vpn-gateways-for-p2s"></a><a name="IKE/IPsec policies"></a>P2S용 VPN 게이트웨이에 구성된 IKE/IPsec 정책은 무엇인가요?


**IKEv2**

| **암호화** | **무결성** | **PRF** | **DH 그룹** |
|--|--|--|--|
| GCM_AES256 | GCM_AES256 | SHA384 | GROUP_24 |
| GCM_AES256 | GCM_AES256 | SHA384 | GROUP_14 |
| GCM_AES256 | GCM_AES256 | SHA384 | GROUP_ECP384 |
| GCM_AES256 | GCM_AES256 | SHA384 | GROUP_ECP256 |
| GCM_AES256 | GCM_AES256 | SHA256 | GROUP_24 |
| GCM_AES256 | GCM_AES256 | SHA256 | GROUP_14 |
| GCM_AES256 | GCM_AES256 | SHA256 | GROUP_ECP384 |
| GCM_AES256 | GCM_AES256 | SHA256 | GROUP_ECP256 |
| AES256 | SHA384 | SHA384 | GROUP_24 |
| AES256 | SHA384 | SHA384 | GROUP_14 |
| AES256 | SHA384 | SHA384 | GROUP_ECP384 |
| AES256 | SHA384 | SHA384 | GROUP_ECP256 |
| AES256 | SHA256 | SHA256 | GROUP_24 |
| AES256 | SHA256 | SHA256 | GROUP_14 |
| AES256 | SHA256 | SHA256 | GROUP_ECP384 |
| AES256 | SHA256 | SHA256 | GROUP_ECP256 |
| AES256 | SHA256 | SHA256 | GROUP_2 |

**IPsec**

| **암호화** | **무결성** | **PFS 그룹** |
|--|--|--|
| GCM_AES256 | GCM_AES256 | GROUP_NONE |
| GCM_AES256 | GCM_AES256 | GROUP_24 |
| GCM_AES256 | GCM_AES256 | GROUP_14 |
| GCM_AES256 | GCM_AES256 | GROUP_ECP384 |
| GCM_AES256 | GCM_AES256 | GROUP_ECP256 |
| AES256 | SHA256 | GROUP_NONE |
| AES256 | SHA256 | GROUP_24 |
| AES256 | SHA256 | GROUP_14 |
| AES256 | SHA256 | GROUP_ECP384 |
| AES256 | SHA256 | GROUP_ECP256 |
| AES256 | SHA1 | GROUP_NONE |

## <a name="what-tls-policies-are-configured-on-vpn-gateways-for-p2s"></a><a name="TLS policies"></a>P2S용 VPN 게이트웨이에 구성된 TLS 정책은 무엇인가요?
**TLS**

|**정책** |
|---| 
|TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256 |
|TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384 |
|TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256 |
|TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384 |
|TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA256 |
|TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384 |
|TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256 |
|TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384 |
|TLS_RSA_WITH_AES_128_GCM_SHA256 |
|TLS_RSA_WITH_AES_256_GCM_SHA384 |
|TLS_RSA_WITH_AES_128_CBC_SHA256 |
|TLS_RSA_WITH_AES_256_CBC_SHA256 |

## <a name="how-do-i-configure-a-p2s-connection"></a><a name="configure"></a>P2S 연결은 어떻게 구성하나요?

P2S 구성에는 몇 가지 특정한 단계가 필요합니다. 다음 문서에는 P2S 구성을 수행하는 단계와 VPN 클라이언트 디바이스를 구성하기 위한 링크가 포함되어 있습니다.

* [P2S 연결 구성 - RADIUS 인증](point-to-site-how-to-radius-ps.md)

* [P2S 연결 구성 - Azure 네이티브 인증서 인증](vpn-gateway-howto-point-to-site-rm-ps.md)

* [OpenVPN 구성](vpn-gateway-howto-openvpn.md)

### <a name="to-remove-the-configuration-of-a-p2s-connection"></a>P2S 연결 구성 제거

PowerShell 또는 CLI를 사용하여 연결의 구성을 제거할 수 있습니다. 예제를 보려면 [FAQ](vpn-gateway-vpn-faq.md#removeconfig)를 참조하세요.

## <a name="how-does-p2s-routing-work"></a>P2S 라우팅은 어떻게 작동하나요?

다음 문서를 참조하세요.

* [지점 및 사이트 간 VPN 라우팅 정보](vpn-gateway-about-point-to-site-routing.md)

* [사용자 지정 경로를 보급하는 방법](vpn-gateway-p2s-advertise-custom-routes.md)

## <a name="faqs"></a>FAQ

인증에 따라 P2S에 대한 여러 FAQ 섹션이 있습니다.

* [FAQ - 인증서 인증](vpn-gateway-vpn-faq.md#P2S)

* [FAQ - RADIUS 인증](vpn-gateway-vpn-faq.md#P2SRADIUS)
 
## <a name="next-steps"></a>다음 단계

* [P2S 연결 구성 - RADIUS 인증](point-to-site-how-to-radius-ps.md)

* [P2S 연결 구성 - Azure 인증서 인증](vpn-gateway-howto-point-to-site-rm-ps.md)

**"OpenVPN"은 OpenVPN Inc.의 상표입니다.**
