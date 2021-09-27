---
title: 'P2S VPN 및 인증서 인증을 사용하여 VNet에 연결: 포털'
titleSuffix: Azure VPN Gateway
description: VPN Gateway 지점 및 사이트 간 연결과 자체 서명 또는 CA 발급 인증서를 사용하여 Windows, macOS 및 Linux 클라이언트를 VNet에 안전하게 연결하는 방법을 알아봅니다.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 08/19/2021
ms.author: cherylmc
ms.openlocfilehash: 70bb2f54d6e56574a5cbbb98d1c0c467f4715f3d
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124818147"
---
# <a name="configure-a-point-to-site-vpn-connection-using-azure-certificate-authentication-azure-portal"></a>Azure 인증서 인증을 사용하여 지점 및 사이트 간 VPN 연결 구성: Azure Portal

이 문서는 Windows, Linux 또는 macOS를 실행하는 개별 클라이언트를 Azure VNet에 안전하게 연결하는 데 도움이 됩니다. 지점 및 사이트 간 VPN 연결은 집 또는 회의에서 원격 통신하는 경우와 같이 원격 위치에서 VNet에 연결하려는 경우에 유용합니다. 또한 VNet에 연결해야 하는 몇 가지 클라이언트만 있는 경우 사이트 간 VPN 대신 P2S를 사용할 수도 있습니다. P2S 연결을 작동하는 데는 VPN 디바이스 또는 공용 IP 주소가 필요하지 않습니다. P2S는 SSTP(Secure Socket Tunneling Protocol) 또는 IKEv2를 통한 VPN 연결을 만듭니다. 지점 및 사이트 간 VPN에 대한 자세한 내용은 [지점 및 사이트 간 VPN 정보](point-to-site-about.md)를 참조하세요.

:::image type="content" source="./media/vpn-gateway-howto-point-to-site-resource-manager-portal/point-to-site-diagram.png" alt-text="컴퓨터에서 Azure VNet - 지점 및 사이트 간 연결로의 연결을 보여 주는 다이어그램":::

지점 및 사이트 간 VPN에 대한 자세한 내용은 [지점 및 사이트 간 VPN 정보](point-to-site-about.md)를 참조하세요. Azure PowerShell을 사용하여 해당 구성을 만들려면 [Azure PowerShell을 사용하여 지점-사이트 간 VPN 구성](vpn-gateway-howto-point-to-site-rm-ps.md)을 참조하세요.

[!INCLUDE [P2S basic architecture](../../includes/vpn-gateway-p2s-architecture.md)]

## <a name="prerequisites"></a>사전 요구 사항

Azure 구독이 있는지 확인합니다. Azure 구독이 아직 없는 경우 [MSDN 구독자 혜택](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details)을 활성화하거나 [무료 계정](https://azure.microsoft.com/pricing/free-trial)에 등록할 수 있습니다.

### <a name="example-values"></a><a name="example"></a>예제 값

다음 값을 사용하여 테스트 환경을 만들거나 이 값을 참조하여 이 문서의 예제를 보다 정확하게 이해할 수 있습니다.

**VNet**

* **VNet 이름:** VNet1
* **주소 공간:** 10.1.0.0/16<br>이 예제에서는 하나의 주소 공간만 사용합니다. VNet에는 둘 이상의 주소 공간을 포함할 수 있습니다.
* **서브넷 이름:** FrontEnd
* **서브넷 주소 범위:** 10.1.0.0/24
* **구독:** 구독이 2개 이상 있는 경우 올바른 구독을 사용 중인지 확인합니다.
* **리소스 그룹:** TestRG1
* **위치:** 미국 동부

**가상 네트워크 게이트웨이**

* **가상 네트워크 게이트웨이 이름:** VNet1GW
* **게이트웨이 유형:** VPN
* **VPN 유형:** 경로 기반
* **SKU:** VpnGw2
* **세대:** 2세대
* **게이트웨이 서브넷 주소 범위:** 10.1.255.0/27
* **공용 IP 주소 이름:** VNet1GWpip

**연결 형식 및 클라이언트 주소 풀**

* **연결 형식:** 지점 및 사이트 간
* **클라이언트 주소 풀:** 172.16.201.0/24<br>이 지점 및 사이트 간 연결을 사용하여 VNet에 연결되는 VPN 클라이언트는 클라이언트 주소 풀에서 IP 주소를 받습니다.

## <a name="create-a-vnet"></a><a name="createvnet"></a>VNet 만들기

이 섹션에서는 가상 네트워크를 만듭니다.

[!INCLUDE [About cross-premises addresses](../../includes/vpn-gateway-cross-premises.md)]

[!INCLUDE [Basic Point-to-Site VNet](../../includes/vpn-gateway-basic-vnet-rm-portal-include.md)]

## <a name="create-the-vpn-gateway"></a><a name="creategw"></a>VPN 게이트웨이 만들기

이 단계에서는 VNet용 가상 네트워크 게이트웨이를 만듭니다. 종종 선택한 게이트웨이 SKU에 따라 게이트웨이를 만드는 데 45분 이상 걸릴 수 있습니다.

>[!NOTE]
>기본 게이트웨이 SKU는 IKEv2 또는 RADIUS 인증을 지원하지 않습니다. Mac 클라이언트를 가상 네트워크에 연결하려는 경우 기본 SKU를 사용하지 마세요.
>

[!INCLUDE [About gateway subnets](../../includes/vpn-gateway-about-gwsubnet-portal-include.md)]

[!INCLUDE [Create a vpn gateway](../../includes/vpn-gateway-add-gw-portal-include.md)]
[!INCLUDE [Configure PIP settings](../../includes/vpn-gateway-add-gw-pip-portal-include.md)]

배포 상태는 게이트웨이에 대한 [개요] 페이지에서 확인할 수 있습니다. 게이트웨이를 만든 후 포털에서 가상 네트워크를 살펴보면 게이트웨이에 할당된 IP 주소를 볼 수 있습니다. 게이트웨이가 연결된 디바이스로 표시됩니다.

[!INCLUDE [NSG warning](../../includes/vpn-gateway-no-nsg-include.md)]

## <a name="generate-certificates"></a><a name="generatecert"></a>인증서 생성

지점 및 사이트 간 VPN 연결을 통해 VNet에 연결되는 클라이언트를 인증하기 위해 Azure에 의해 인증서가 사용됩니다. 루트 인증서를 얻었으면 Azure에 공개 키 정보를 [업로드](#uploadfile)합니다. 그러면 루트 인증서는 P2S를 통한 가상 네트워크 연결을 위해 Azure에서 '신뢰할 수 있는' 것으로 간주됩니다. 또한 신뢰할 수 있는 루트 인증서에서 클라이언트 인증서를 생성한 후 각 클라이언트 컴퓨터에 인증서를 설치합니다. 클라이언트 인증서는 VNet에 대한 연결을 시작할 때 해당 클라이언트를 인증하는 데 사용됩니다.

### <a name="generate-a-root-certificate"></a><a name="getcer"></a>루트 인증서 생성

[!INCLUDE [root-certificate](../../includes/vpn-gateway-p2s-rootcert-include.md)]

### <a name="generate-client-certificates"></a><a name="generateclientcert"></a>클라이언트 인증서 생성

[!INCLUDE [generate-client-cert](../../includes/vpn-gateway-p2s-clientcert-include.md)]

## <a name="add-the-vpn-client-address-pool"></a><a name="addresspool"></a>VPN 클라이언트 주소 풀 추가

클라이언트 주소 풀은 사용자가 지정한 개인 IP 주소 범위입니다. 지점 및 사이트 간 VPN을 통해 연결하는 클라이언트는 동적으로 이 범위의 IP 주소를 수신합니다. 연결 원본이 되는 온-프레미스 위치 또는 연결 대상이 되는 VNet과 겹치지 않는 개인 IP 주소 범위를 사용합니다. 여러 프로토콜을 구성하고 SSTP가 프로토콜 중 하나인 경우 구성된 주소 풀이 구성된 프로토콜 간에 동일하게 분할됩니다.

1. 가상 네트워크 게이트웨이가 생성된 후에는 가상 네트워크 게이트웨이 페이지의 **설정** 섹션으로 이동합니다. **설정** 에서 **지점-사이트 간 구성** 을 선택합니다. **지금 구성** 을 선택하여 구성 페이지를 엽니다.

   :::image type="content" source="./media/vpn-gateway-howto-point-to-site-resource-manager-portal/configure-now.png" alt-text="지점 및 사이트 간 구성 페이지" lightbox="./media/vpn-gateway-howto-point-to-site-resource-manager-portal/configure-now.png":::
1. **지점-사이트 간 구성** 페이지의 **주소 풀** 상자에서 사용하려는 개인 IP 주소 범위를 추가합니다. VPN 클라이언트는 동적으로 지정된 범위에서 IP 주소를 수신합니다. 최소 서브넷 마스크는 활성/수동 구성의 경우 29비트이고 활성/활성 구성의 경우 28비트입니다.
1. 인증 및 터널 종류를 구성하려면 다음 섹션을 계속 진행합니다.

## <a name="specify-tunnel-type-and-authentication-type"></a><a name="type"></a>터널 유형 및 인증 유형 지정

이 섹션에서는 터널 유형 및 인증 유형을 지정합니다. 지점 및 사이트 간 구성 페이지에서 터널 유형 또는 인증 유형이 표시되지 않으면 게이트웨이에서 기본 SKU를 사용하고 있는 것입니다. 기본 SKU는 IKEv2 또는 RADIUS 인증을 지원하지 않습니다. 해당 설정을 사용하려면 다른 게이트웨이 SKU를 사용하여 게이트웨이를 삭제하고 다시 만들어야 합니다.

### <a name="tunnel-type"></a><a name="tunneltype"></a>터널 종류

**지점 및 사이트 간 구성** 페이지에서 **터널 유형** 을 선택합니다. 터널 유형을 선택할 때 다음을 참고하세요.

* Android와 Linux의 strongSwan 클라이언트 및 iOS와 macOS의 네이티브 IKEv2 VPN 클라이언트는 IKEv2 터널 유형만 사용하여 연결합니다.
* Windows 클라이언트는 IKEv2를 먼저 시도하고 연결되지 않는 경우 SSTP로 대체합니다.
* OpenVPN 클라이언트를 사용하여 OpenVPN 터널 종류에 연결할 수 있습니다.

### <a name="authentication-type"></a><a name="authenticationtype"></a>인증 유형

**인증 유형** 으로 **Azure 인증서** 를 선택합니다.

   :::image type="content" source="./media/vpn-gateway-howto-point-to-site-resource-manager-portal/authentication.png" alt-text="Azure 인증서가 선택된 인증 유형의 스크린샷" :::

## <a name="upload-root-certificate-public-key-information"></a><a name="uploadfile"></a>루트 인증서 퍼블릭 키 정보 업로드

이 섹션에서는 공용 루트 인증서 데이터를 Azure에 업로드합니다. 공용 인증서 데이터가 업로드되면 Azure는 이 데이터를 사용하여 신뢰할 수 있는 루트 인증서에서 생성된 클라이언트 인증서를 설치한 클라이언트를 인증합니다.

1. **루트 인증서** 섹션에서 **가상 네트워크 게이트웨이 -> 지점 및 사이트 간 구성** 페이지로 이동합니다. 이 섹션은 인증 유형에 대해 **Azure 인증서** 를 선택한 경우에만 표시됩니다.
1. 이전 단계에서 루트 인증서를 **Base-64 인코딩된 X.509(.CER)** 파일로 내보내야 합니다. 이 형식으로 내보내야 텍스트 편집기에서 인증서를 열 수 있습니다. 프라이빗 키를 내보낼 필요가 없습니다.

   :::image type="content" source="./media/vpn-gateway-howto-point-to-site-resource-manager-portal/base-64.png" alt-text="Base-64로 인코딩된 X.509로 내보내기를 보여 주는 스크린샷" :::
1. 메모장과 같은 텍스트 편집기에서 인증서를 엽니다. 인증서 데이터를 복사하는 경우 캐리지 리턴 또는 줄 바꿈 없이 하나의 연속 줄로 텍스트를 복사합니다. 캐리지 리턴 및 줄 바꿈을 보려면 '기호 표시/모든 문자 표시'에 대한 텍스트 편집기의 보기를 수정해야 할 수도 있습니다. 하나의 연속 줄로만 다음 섹션을 복사합니다.

   :::image type="content" source="./media/vpn-gateway-howto-point-to-site-resource-manager-portal/notepadroot.png" alt-text="메모장의 루트 인증서 정보를 보여 주는 스크린샷" border="false":::
1. **루트 인증서** 섹션에서 최대 20개의 신뢰할 수 있는 루트 인증서를 추가할 수 있습니다.

   * 인증서 데이터를 **공용 인증서 데이터** 필드에 붙여넣습니다. 
   * 인증서의 **이름을 지정** 합니다. 

   :::image type="content" source="./media/vpn-gateway-howto-point-to-site-resource-manager-portal/root-certificate.png" alt-text="인증서 데이터 필드의 스크린샷" :::
1. 페이지 위쪽에서 **저장** 을 선택하여 모든 구성 설정을 저장합니다.

   :::image type="content" source="./media/vpn-gateway-howto-point-to-site-resource-manager-portal/save-configuration.png" alt-text="저장이 선택된 P2S 구성의 스크린샷" :::

## <a name="install-exported-client-certificate"></a><a name="installclientcert"></a>내보낸 클라이언트 인증서 설치

클라이언트 인증서를 생성하는 데 사용한 것 외의 클라이언트 컴퓨터에서 P2S 연결을 만들려는 경우 클라이언트 인증서를 설치해야 합니다. 클라이언트 인증서를 설치하는 경우 클라이언트 인증서를 내보낼 때 만든 암호가 필요합니다.

전체 인증서 체인(즉, 기본값)과 함께.pfx로 클라이언트 인증서를 내보냈는지 확인합니다. 그렇지 않은 경우, 루트 인증서 정보가 클라이언트 컴퓨터에 존재하지 않으며 클라이언트를 제대로 인증할 수 없습니다.

설치 단계는 [클라이언트 인증서 설치](point-to-site-how-to-vpn-client-install-azure-cert.md)를 참조하세요.

## <a name="configure-settings-for-vpn-clients"></a><a name="clientconfig"></a>VPN 클라이언트에 대한 설정 구성

P2S를 사용하여 가상 네트워크 게이트웨이에 연결하기 위해 각 컴퓨터는 운영 체제의 일부로 기본적으로 설치된 VPN 클라이언트를 사용합니다. 예를 들어 Windows 컴퓨터에서 VPN 설정으로 이동하면 별도의 VPN 클라이언트를 설치하지 않고 VPN 연결을 추가할 수 있습니다. 클라이언트 구성 패키지를 사용하여 각 VPN 클라이언트를 구성합니다. 클라이언트 구성 패키지에는 자신이 만든 VPN 게이트웨이와 관련한 설정이 포함되어 있습니다. 

VPN 클라이언트 구성 파일을 생성하고 설치하는 단계는 [Azure 인증서 인증 P2S 구성에 VPN 클라이언트 구성 파일 만들기 및 설치](point-to-site-vpn-client-configuration-azure-cert.md)를 참조하세요.

## <a name="connect-to-azure"></a><a name="connect"></a>Azure에 연결

### <a name="to-connect-from-a-windows-vpn-client"></a>Windows VPN 클라이언트에서 연결

[!INCLUDE [Connect from a Windows client](../../includes/vpn-gateway-p2s-connect-windows-client.md)]

[!INCLUDE [verifies client certificates](../../includes/vpn-gateway-certificates-verify-client-cert-include.md)]

### <a name="to-connect-from-a-mac-vpn-client"></a>Mac VPN 클라이언트에서 연결하려면

네트워크 대화 상자에서 사용하려는 클라이언트 프로필을 찾고 [VpnSettings.xml](point-to-site-vpn-client-configuration-azure-cert.md#installmac)에서 설정을 지정한 다음 **연결** 을 선택합니다. 자세한 지침은 [VPN 클라이언트 구성 파일 생성 및 설치 - macOS](./point-to-site-vpn-client-configuration-azure-cert.md#installmac)를 참조하세요. 

연결에 문제가 발생하면 가상 네트워크 게이트웨이가 기본 SKU를 사용하지 않는지 확인합니다. 기본 SKU는 Mac 클라이언트에서 지원되지 않습니다.

   :::image type="content" source="./media/point-to-site-vpn-client-configuration-azure-cert/select-connect.png" alt-text="연결 단추를 보여 주는 스크린샷" lightbox="./media/point-to-site-vpn-client-configuration-azure-cert/expanded/select-connect.png":::

## <a name="to-verify-your-connection"></a><a name="verify"></a>연결 확인

이러한 지침은 Windows 클라이언트에 적용됩니다.

1. VPN 연결이 활성인지를 확인하려면, 관리자 권한 명령 프롬프트를 열고 *ipconfig/all* 을 실행합니다.
2. 결과를 확인합니다. 받은 IP 주소가 구성에 지정한 지점 및 사이트 VPN 클라이언트 주소 풀 내의 주소 중 하나인지 확인합니다. 결과는 다음 예제와 비슷합니다.

   ```
   PPP adapter VNet1:
      Connection-specific DNS Suffix .:
      Description.....................: VNet1
      Physical Address................:
      DHCP Enabled....................: No
      Autoconfiguration Enabled.......: Yes
      IPv4 Address....................: 172.16.201.3(Preferred)
      Subnet Mask.....................: 255.255.255.255
      Default Gateway.................:
      NetBIOS over Tcpip..............: Enabled
   ```

## <a name="to-connect-to-a-virtual-machine"></a><a name="connectVM"></a>가상 컴퓨터에 연결하려면

이러한 지침은 Windows 클라이언트에 적용됩니다.

[!INCLUDE [Connect to a VM](../../includes/vpn-gateway-connect-vm.md)]

* DNS 서버 IP 주소가 VNet에 지정된 후에 VPN 클라이언트 구성 패키지가 생성되었는지 확인합니다. DNS 서버 IP 주소를 업데이트한 경우 새 VPN 클라이언트 구성 패키지를 생성하고 설치합니다.

* 'ipconfig'를 사용하여 연결하는 컴퓨터의 이더넷 어댑터에 할당된 IPv4 주소를 확인합니다. IP 주소가 연결하는 VNet의 주소 범위 또는 VPNClientAddressPool의 주소 범위 내에 있는 경우 이 주소를 겹치는 주소 공간이라고 합니다. 주소 공간이 이러한 방식으로 겹치면 네트워크 트래픽이 Azure에 도달하지 않고 로컬 네트워크에 남아 있습니다.

## <a name="to-add-or-remove-trusted-root-certificates"></a><a name="add"></a>신뢰할 수 있는 루트 인증서를 추가 또는 제거하려면

Azure에서 신뢰할 수 있는 루트 인증서를 추가 및 제거할 수 있습니다. 루트 인증서를 제거하면 해당 루트에서 생성된 인증서가 있는 클라이언트를 인증하지 못하게 됩니다. 따라서 연결할 수도 없습니다. 클라이언트를 인증하고 연결하려는 경우 Azure에 (업로드된)신뢰할 수 있는 루트 인증서에서 생성된 새 클라이언트 인증서를 설치해야 합니다.

Azure에 최대 20개의 신뢰할 수 있는 루트 인증서 .cer 파일을 추가할 수 있습니다. 지침은 [신뢰할 수 있는 루트 인증서 업로드](#uploadfile) 섹션을 참조하세요.

신뢰할 수 있는 루트 인증서를 제거하려면 다음을 수행합니다.

1. 가상 네트워크 게이트웨이에 대한 **지점 및 사이트 간 구성** 페이지로 이동합니다.
1. 페이지의 **루트 인증서** 섹션에서 제거할 인증서를 찾습니다.
1. 인증서 옆의 줄임표를 선택한 다음, **제거** 를 선택합니다.

## <a name="to-revoke-a-client-certificate"></a><a name="revokeclient"></a>클라이언트 인증서를 해지하려면

클라이언트 인증서를 해지할 수 있습니다. 인증서 해지 목록에서 개별 클라이언트 인증서를 기반으로 하는 지점 및 사이트 간 연결을 선택적으로 거부할 수 있습니다. 이것은 신뢰할 수 있는 루트 인증서를 제거하는 것과 다릅니다. Azure에서 신뢰할 수 있는 루트 인증서 .cer를 제거하면, 해지된 루트 인증서로 생성/서명된 모든 클라이언트 인증서에 대한 액세스 권한도 해지됩니다. 루트 인증서가 아닌 클라이언트 인증서를 해지하면 루트 인증서에서 생성된 다른 인증서를 인증에 계속 사용할 수 있습니다.

해지된 클라이언트 인증서를 사용하는 동안 개별 사용자의 세분화된 액세스 제어를 위해 일반적으로 루트 인증서를 사용하여 팀 또는 조직 수준에서 액세스를 관리합니다.

해지 목록에 지문을 추가하여 클라이언트 인증서를 해지할 수 있습니다.

1. 클라이언트 인증서 지문을 검색합니다. 자세한 내용은 [인증서의 지문을 검색하는 방법](/dotnet/framework/wcf/feature-details/how-to-retrieve-the-thumbprint-of-a-certificate)을 참조하세요.
1. 텍스트 편집기에 정보를 복사하고 연속 문자열이 되도록 공백을 모두 제거합니다.
1. 가상 네트워크 게이트웨이 **지점 및 사이트 간 구성** 페이지로 이동합니다. [신뢰할 수 있는 루트 인증서를 업로드](#uploadfile)하는 데 사용한 것과 동일한 페이지입니다.
1. **해지된 인증서** 섹션에서 인증서에 대한 이름(인증서 CN이 아니어도 됨)을 입력합니다.
1. 지문 문자열을 **지문** 필드에 복사하여 붙여 넣습니다.
1. 지문의 유효성이 검사되고 해당 지문이 해지 목록에 자동으로 추가됩니다. 목록이 업데이트되고 있음을 알리는 메시지가 화면에 표시됩니다. 
1. 업데이트가 완료된 후에는 인증서를 더 이상 연결에 사용할 수 없습니다. 이 인증서를 사용하여 연결하려는 클라이언트에서 인증서가 더 이상 유효하지 않다고 하는 메시지를 받습니다.

## <a name="point-to-site-faq"></a><a name="faq"></a>지점 및 사이트 간 FAQ

자주 묻는 질문은 [FAQ](vpn-gateway-vpn-faq.md#P2S)를 참조하세요.

## <a name="next-steps"></a>다음 단계
연결이 완료되면 가상 네트워크에 가상 머신을 추가할 수 있습니다. 자세한 내용은 [Virtual Machines](../index.yml)를 참조하세요. 네트워킹 및 가상 머신에 대한 자세한 내용은 [Azure 및 Linux VM 네트워크 개요](../virtual-network/network-overview.md)를 참조하세요.

P2S 문제 해결 정보는 [Azure 지점 및 사이트 간 연결 문제 해결](vpn-gateway-troubleshoot-vpn-point-to-site-connection-problems.md)을 참조하세요.