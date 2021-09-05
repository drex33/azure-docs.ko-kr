---
title: SSTP에서 OpenVPN 또는 IKEv2로 전환하는 방법
titleSuffix: Azure VPN Gateway
description: SSTP에서 OpenVPN 프로토콜 또는 IKEv2로 전환하여 128개 동시 연결 SSTP 제한을 극복하는 방법을 알아봅니다.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 06/04/2021
ms.author: alzam
ms.openlocfilehash: af136cab65034be5cd0f3ec18d22492a826678d0
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122536870"
---
# <a name="transition-to-openvpn-protocol-or-ikev2-from-sstp"></a>SSTP에서 OpenVPN 프로토콜 또는 IKEv2로 전환

P2S(지점 및 사이트 간) VPN 게이트웨이 연결을 사용하면 개별 클라이언트 컴퓨터에서 가상 네트워크에 대한 안전한 연결을 만들 수 있습니다. P2S 연결은 클라이언트 컴퓨터에서 시작하여 설정됩니다. 이 문서는 [Resource Manager 배포 모델](../azure-resource-manager/management/deployment-models.md)에 적용되며, OpenVPN 프로토콜 또는 IKEv2로 전환하여 SSTP의 128 동시 연결 제한을 해결하는 방법에 대해 설명합니다.

## <a name="what-protocol-does-p2s-use"></a><a name="protocol"></a>P2S는 어떤 프로토콜을 사용합니까?

지점 및 사이트 간 VPN에서는 다음 프로토콜 중 하나를 사용할 수 있습니다.

* **OpenVPN&reg; 프로토콜**, SSL/TLS 기반 VPN 프로토콜입니다. 대부분의 방화벽에서 SSL이 사용되는 443 아웃바운드 TCP 포트를 열기 때문에 SSL VPN 솔루션이 방화벽을 통과할 수 있습니다. OpenVPN은 Android, iOS(버전 11.0 이상), Windows, Linux, Mac 디바이스(macOS 버전 10.13 이상)에서 연결하는 데 사용할 수 있습니다.

* **SSTP(Secure Socket Tunneling Protocol)** - 독점적인 SSL 기반 VPN 프로토콜입니다. 대부분의 방화벽에서 SSL이 사용되는 443 아웃바운드 TCP 포트를 열기 때문에 SSL VPN 솔루션이 방화벽을 통과할 수 있습니다. SSTP는 Windows 디바이스에서만 지원됩니다. Azure는 SSTP가 설치된 모든 Windows 버전(Windows 7 이상)을 지원합니다. **SSTP는 게이트웨이 SKU에 관계없이 최대 128개의 동시 연결을 지원합니다**.

* IKEv2 VPN - 표준 기반 IPsec VPN 솔루션입니다. IKEv2 VPN은 Mac 디바이스(macOS 버전 10.11 이상)에서 연결하는 데 사용할 수 있습니다.


>[!NOTE]
>P2S용 IKEv2 및 OpenVPN은 [Resource Manager 배포 모델](../azure-resource-manager/management/deployment-models.md)에서만 사용할 수 있습니다. 클래식 배포 모델에서는 사용할 수 없습니다. 기본 게이트웨이 SKU는 IKEv2 또는 OpenVPN 프로토콜을 지원하지 않습니다. 기본 SKU를 사용하고 있는 경우, 프로덕션 SKU Virtual Network Gateway를 삭제하고 다시 만들어야 합니다.
>

## <a name="migrating-from-sstp-to-ikev2-or-openvpn"></a>SSTP에서 IKEv2 또는 OpenVPN으로 마이그레이션

VPN 게이트웨이에 대해 128개가 넘는 동시 P2S 연결을 지원 하고 싶지만, SSTP를 사용하고 있는 경우가 있을 수 있습니다. 이 경우 IKEv2 또는 OpenVPN 프로토콜로 이동해야 합니다.

### <a name="option-1---add-ikev2-in-addition-to-sstp-on-the-gateway"></a>옵션 1 - Gateway에서 SSTP 외에 IKEv2 추가

이것이 가장 간단한 옵션입니다. SSTP 및 IKEv2는 동일한 게이트웨이에 공존할 수 있으며, 더 많은 동시 연결을 제공할 수 있습니다. 단순히 기존 게이트웨이에서 IKEv2를 사용하도록 설정하고 클라이언트를 다시 다운로드할수 있습니다.

기존 SSTP VPN 게이트웨이에 IKEv2를 추가 해도 기존 클라이언트에는 영향을 주지 않으며, 작은 배치(Batch)에서 IKEv2를 사용하도록 구성하거나, 새 클라이언트가 IKEv2를 사용하도록 구성할 수 있습니다. Windows 클라이언트에서 SSTP 및 IKEv2를 모두 구성하는 경우, 먼저 IKEV2를 사용하여 연결을 시도하고, 실패하면 SSTP로 대체합니다.

**IKEv2는 비표준 UDP 포트를 사용하므로, 이러한 포트가 사용자의 방화벽에서 차단되지 않도록해야 합니다. 사용 중인 포트는 UDP 500 및 4500입니다.**

기존 게이트웨이에 IKEv2를 추가하려면, 포털에서 Virtual Network Gateway 아래의 "지점 및 사이트 간 구성" 탭으로 이동하고, 드롭다운 상자에서 **IKEv2 및 SSTP (SSL)** 를 선택하면 됩니다.

!["터널 유형" 드롭다운이 열려 있는 "지점 및 사이트 간 구성" 페이지와 "IKEv2 및 SSTP (SSL)"가 선택되어 있음을 보여 주는 스크린샷](./media/ikev2-openvpn-from-sstp/sstptoikev2.png "IKEv2")


### <a name="option-2---remove-sstp-and-enable-openvpn-on-the-gateway"></a>옵션 2 - 게이트웨이에서 SSTP 제거 및 OpenVPN 사용

SSTP 및 OpenVPN은 모두 TLS 기반 프로토콜이므로, 동일한 게이트웨이에 공존할 수 없습니다. SSTP에서 OpenVPN에서 이동하기로 결정한 경우, SSTP를 사용하지 않도록 설정하고, 게이트웨이에서 OpenVPN을 사용하도록 설정해야 합니다. 이 작업을 수행하면 클라이언트에서 새 프로필이 구성될 때까지 기존 클라이언트의 VPN Gateway에 대한 연결이 끊어집니다.

원하는 경우 IKEv2와 함께 OpenVPN을 사용하도록 설정할 수 있습니다. OpenVPN은 TLS 기반이며, 표준 TCP 443 포트를 사용합니다. OpenVPN으로 전환하려면, 포털에서 Virtual Network Gateway 아래의 "지점 및 사이트 간 구성" 탭으로 이동하고, 드롭다운 상자에서 **OpenVPN (SSL)** 또는 **IKEv2 및 OPENVPN (SSL)** 을 선택합니다.

![지점 및 사이트 간](./media/ikev2-openvpn-from-sstp/sstptoopenvpn.png "OpenVPN")

게이트웨이가 구성된 후에는 [OpenVPN 클라이언트를 배포하고 구성할](./vpn-gateway-howto-openvpn-clients.md) 때까지 기존 클라이언트를 연결할 수 없습니다.

Windows 10을 사용하는 경우, [Wndows 용 Azure VPN Client](./openvpn-azure-ad-client.md#to-download-the-azure-vpn-client)를 사용할 수도 있습니다.


## <a name="frequently-asked-questions"></a>질문과 대답
### <a name="what-are-the-client-configuration-requirements"></a>클라이언트 구성 요구 사항은 어떻게 되나요?

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

### <a name="which-gateway-skus-support-p2s-vpn"></a><a name="gwsku"></a>어떤 게이트웨이 SKU에서 P2S VPN을 지원하나요?

[!INCLUDE [aggregate throughput sku](../../includes/vpn-gateway-table-gwtype-aggtput-include.md)]

* 게이트웨이 SKU 권장 사항에 대해서는 [VPN Gateway 설정 정보](vpn-gateway-about-vpn-gateway-settings.md#gwsku)를 참조하세요.

>[!NOTE]
>기본 SKU는 IKEv2 또는 RADIUS 인증을 지원하지 않습니다.
>

### <a name="what-ikeipsec-policies-are-configured-on-vpn-gateways-for-p2s"></a><a name="IKE/IPsec policies"></a>P2S용 VPN 게이트웨이에 구성된 IKE/IPsec 정책은 무엇인가요?


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

### <a name="what-tls-policies-are-configured-on-vpn-gateways-for-p2s"></a><a name="TLS policies"></a>P2S용 VPN 게이트웨이에 구성된 TLS 정책은 무엇인가요?
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

### <a name="how-do-i-configure-a-p2s-connection"></a><a name="configure"></a>P2S 연결은 어떻게 구성하나요?

P2S 구성에는 몇 가지 특정한 단계가 필요합니다. 다음 문서에는 P2S 구성을 수행하는 단계와 VPN 클라이언트 디바이스를 구성하기 위한 링크가 포함되어 있습니다.

* [P2S 연결 구성 - RADIUS 인증](point-to-site-how-to-radius-ps.md)

* [P2S 연결 구성 - Azure 네이티브 인증서 인증](vpn-gateway-howto-point-to-site-rm-ps.md)

* [OpenVPN 구성](vpn-gateway-howto-openvpn.md)

## <a name="next-steps"></a>다음 단계

* [P2S 연결 구성 - RADIUS 인증](point-to-site-how-to-radius-ps.md)

* [P2S 연결 구성 - Azure 네이티브 인증서 인증](vpn-gateway-howto-point-to-site-rm-ps.md)

**"OpenVPN"은 OpenVPN Inc.의 상표입니다.**