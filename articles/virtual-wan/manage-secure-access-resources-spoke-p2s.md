---
title: P2S 클라이언트용 스포크 VNet의 리소스에 대한 보안 액세스 관리
titleSuffix: Azure Virtual WAN
description: 이 문서는 Azure Virtual WAN 및 Azure Firewall 규칙을 사용하여 사용자 VPN(지점 및 사이트 간) 클라이언트의 가상 네트워크에 대한 보안 액세스를 관리하는 데 도움이 됩니다.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: how-to
ms.date: 07/29/2021
ms.author: cherylmc
ms.openlocfilehash: e46f9c5e3d59c4f30d0a9a0a9dddcf98cd636e40
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122528636"
---
# <a name="manage-secure-access-to-resources-in-spoke-vnets-for-user-vpn-clients"></a>사용자 VPN 클라이언트용 스포크 VNet의 리소스에 대한 보안 액세스 관리

이 문서에서는 Virtual WAN 및 Azure Firewall 규칙과 필터를 사용하여 지점 및 사이트 간 IKEv2 또는 VPN 연결 열기를 통해 Azure의 리소스로 연결에 대한 보안 액세스를 관리하는 방법을 설명합니다. 이 구성은 Azure 리소스에 대한 액세스를 제한하거나 Azure에서 리소스를 보호하려는 원격 사용자가 있는 경우에 유용합니다.

이 문서의 단계는 다음 다이어그램에서 아키텍처를 만들어 사용자 VPN 클라이언트에서 가상 허브에 연결된 스포크 VNet의 특정 리소스(VM1)에 액세스하도록 허용하지만 다른 리소스(VM2)에는 액세스하지 못하도록 하는 데 도움이 됩니다. 이 아키텍처 예를 기본 지침으로 사용합니다.

:::image type="content" source="./media/manage-secure-access-resources-spoke-p2s/diagram.png" alt-text="다이어그램: 보안 가상 허브" :::

## <a name="prerequisites"></a>필수 구성 요소

[!INCLUDE [Prerequisites](../../includes/virtual-wan-before-include.md)]

* 사용하려는 인증 구성에 사용 가능한 값이 있습니다. RADIUS 서버, Azure Active Directory 인증서 또는 [인증서 생성 및 내보내기](../vpn-gateway/vpn-gateway-certificates-point-to-site.md)가 해당됩니다.

## <a name="create-a-virtual-wan"></a>가상 WAN 만들기

[!INCLUDE [Create virtual WAN](../../includes/virtual-wan-create-vwan-include.md)]

## <a name="define-p2s-configuration-parameters"></a><a name= "p2s-config"></a>P2S 구성 매개 변수 정의

P2S(지점 및 사이트 간) 구성은 원격 클라이언트 연결에 사용되는 매개 변수를 정의합니다. 이 섹션은 P2S 구성 매개 변수를 정의한 다음, VPN 클라이언트 프로필에 사용할 구성을 만드는 데 도움이 됩니다. 따르는 지침은 사용하려는 인증 방법에 따라 달라집니다.

### <a name="authentication-methods"></a>인증 방법

인증 방법을 선택할 때 세 가지 선택 사항이 있습니다. 방법마다 특별한 요구 사항이 있습니다. 다음 방법 중 하나를 선택한 다음, 단계를 완료합니다.

* **Azure Active Directory 인증:** 다음을 가져옵니다.

   * Azure AD 테넌트에 등록된 Azure VPN 엔터프라이즈 애플리케이션의 **애플리케이션 ID**
   * **발급자**. 예: `https://sts.windows.net/your-Directory-ID`.
   * **Azure AD 테넌트**. 예: `https://login.microsoftonline.com/your-Directory-ID`.

* **Radius 기반 인증:** Radius 서버 IP, Radius 서버 비밀 및 인증서 정보를 가져옵니다.

* **Azure 인증서:** 이 구성에서는 인증서가 필요합니다. 인증서를 생성하거나 가져와야 합니다. 클라이언트마다 클라이언트 인증서가 필요합니다. 또한 루트 인증서 정보(공개 키)를 업로드해야 합니다. 필요한 인증서에 대한 자세한 내용은 [인증서 생성 및 내보내기](../vpn-gateway/vpn-gateway-certificates-point-to-site.md)를 참조하세요.

[!INCLUDE [Define parameters](../../includes/virtual-wan-p2s-configuration-include.md)]

## <a name="create-the-hub-and-gateway"></a><a name="hub"></a>허브 및 게이트웨이 만들기

이 섹션에서는 지점 및 사이트 간 게이트웨이를 사용하여 가상 허브를 만듭니다. 구성할 때 다음 예 값을 사용할 수 있습니다.

* **허브 개인 IP 주소 공간:** 10.0.0.0/24
* **클라이언트 주소 풀**: 10.5.0.0/16
* **사용자 지정 DNS 서버:** DNS 서버를 최대 5개까지 나열할 수 있습니다.

[!INCLUDE [Create hub](../../includes/virtual-wan-p2s-hub-include.md)]

## <a name="generate-vpn-client-configuration-files"></a><a name="generate"></a>VPN 클라이언트 구성 파일 생성

이 섹션에서는 구성 프로필 파일을 생성하고 다운로드합니다. 이러한 파일은 클라이언트 컴퓨터에서 네이티브 VPN 클라이언트를 구성하는 데 사용됩니다. 클라이언트 프로필 파일 콘텐츠에 대한 자세한 내용은 [지점 및 사이트 간 구성 - 인증서](../vpn-gateway/point-to-site-vpn-client-configuration-azure-cert.md)를 참조하세요.

[!INCLUDE [Download profile](../../includes/virtual-wan-p2s-download-profile-include.md)]

## <a name="configure-vpn-clients"></a><a name="clients"></a>VPN 클라이언트 구성

다운로드한 프로필을 사용하여 원격 액세스 클라이언트를 구성합니다. 각 운영 체제에 대한 프로시저가 다르므로 시스템에 적용되는 지침을 따르세요.

[!INCLUDE [Configure clients](../../includes/virtual-wan-p2s-configure-clients-include.md)]

## <a name="connect-the-spoke-vnet"></a><a name="connect-spoke"></a>스포크 VNet 연결

이 섹션에서는 허브와 VNet 간의 연결을 만듭니다.

[!INCLUDE [Connect spoke virtual network](../../includes/virtual-wan-connect-vnet-hub-include.md)]

## <a name="create-virtual-machines"></a><a name="create-vm"></a>가상 머신 만들기

이 섹션에서는 VNet, VM1 및 VM2에 VM 2개를 만듭니다. 네트워크 다이어그램에서 10.18.0.4 및 10.18.0.5를 사용합니다. VM을 구성할 때 만든 가상 네트워크(네트워킹 탭에 있는)를 선택해야 합니다. VM을 만드는 단계는 [빠른 시작: VM 만들기](../virtual-machines/windows/quick-create-portal.md)를 참조하세요.

## <a name="secure-the-virtual-hub"></a><a name="secure"></a>가상 허브 보안

표준 가상 허브에는 스포크 가상 네트워크의 리소스를 보호하기 위한 기본 제공 보안 정책이 없습니다. 보안 가상 허브는 Azure Firewall 또는 타사 공급자를 사용하여 들어오고 나가는 트래픽을 관리해 Azure에서 리소스를 보호합니다.

[Virtual WAN 허브에서 Azure Firewall 구성](howto-firewall.md) 문서를 수행하여 허브를 보안 허브로 변환합니다.

## <a name="create-rules-to-manage-and-filter-traffic"></a><a name= "create-rules"></a>트래픽을 관리하고 필터링하는 규칙 만들기

Azure Firewall 동작을 지시하는 규칙을 만듭니다. 허브를 보호하면 Azure 리소스에 액세스하기 전에 가상 허브에 진입하는 모든 패킷에 방화벽 처리가 적용됩니다.

이러한 단계를 완료하면 VPN 사용자가 개인 IP 주소 10.18.0.4를 사용하여 VM에 액세스하도록 허용하지만 개인 IP 주소 10.18.0.5를 사용하여 VM에 액세스하도록 허용하지 **않는** 아키텍처가 생성됩니다.

1. Azure Portal에서 **Firewall Manager** 로 이동합니다.
1. 보안 아래에서 **Azure Firewall 정책** 을 선택합니다.
1. **Azure Firewall 정책 만들기** 를 선택합니다.
1. **정책 세부 정보** 에서 이름을 입력하고 가상 허브가 배포된 지역을 선택합니다.
1. **다음: DNS 설정(미리 보기)** 을 선택합니다.
1. **다음: 규칙** 을 선택합니다.
1. **규칙** 탭에서 **규칙 컬렉션 추가** 를 선택합니다.
1. 컬렉션 이름을 입력합니다. 형식을 **네트워크** 로 설정합니다. 우선 순위 값 **100** 을 추가합니다.
1. 다음 예와 같이 규칙, 원본 형식, 원본, 프로토콜, 대상 포트 및 대상 형식의 이름을 입력합니다. 그런 다음, **추가** 를 선택합니다. 이 규칙을 사용하면 VPN 클라이언트 풀의 모든 IP 주소가 개인 IP 주소 10.18.04를 통해 VM에 액세스할 수 있지만 가상 허브에 연결된 다른 리소스에서는 액세스할 수 없습니다. 원하는 아키텍처와 권한 규칙에 적합할 규칙을 만듭니다.

   :::image type="content" source="./media/manage-secure-access-resources-spoke-p2s/rules.png" alt-text="방화벽 규칙" :::

1. 완료되면 **다음: 위협 인텔리전스** 를 선택합니다.
1. 완료되면 **다음: 허브** 를 선택합니다.
1. **허브** 탭에서 **가상 허브 연결** 을 선택합니다.
1. 이전에 만든 가상 허브를 선택하고 **추가** 를 선택합니다.
1. **검토 + 만들기** 를 선택합니다.
1. **만들기** 를 선택합니다.

이 프로세스를 완료하는 데 5분 이상 걸릴 수 있습니다.

## <a name="route-traffic-through-azure-firewall"></a><a name="send"></a>Azure Firewall을 통해 트래픽 라우팅

이 섹션에서는 트래픽이 Azure Firewall을 통해 라우팅되는지 확인해야 합니다.

1. 포털의 **Firewall Manager** 에서 **보안 가상 허브** 를 선택합니다.
1. 만든 가상 허브를 선택합니다.
1. **설정** 에서 **보안 구성** 을 선택합니다.
1. **프라이빗 트래픽** 에서 **Azure Firewall을 통해 전송** 을 선택합니다.
1. Azure Firewall에서 VNet 연결과 분기 연결 프라이빗 트래픽을 보호하는지 확인합니다.
1. **저장** 을 선택합니다.

## <a name="validate"></a><a name="validate"></a>유효성 검사

보안 허브 설정을 확인합니다.

1. 클라이언트 디바이스에서 VPN을 통해 **보안 가상 허브** 에 연결합니다.
1. 클라이언트에서 IP 주소 **10.18.0.4** 를 Ping합니다. 응답이 표시되어야 합니다.
1. 클라이언트에서 IP 주소 **10.18.0.5** 를 Ping합니다. 응답이 표시되지 않아야 합니다.

### <a name="considerations"></a>고려 사항

* 보안 가상 허브의 **유효 경로 테이블** 에 방화벽의 프라이빗 트래픽에 대한 다음 홉이 있는지 확인합니다. 유효 경로 테이블에 액세스하려면 **가상 허브** 리소스로 이동합니다. **연결** 에서 **라우팅** 을 선택한 다음, **유효 경로** 를 선택합니다. 여기에서 **기본** 경로 테이블을 선택합니다.
* [규칙 만들기](#create-rules) 섹션에서 규칙을 만들었는지 확인합니다. 이러한 단계를 누락하면 만든 규칙이 실제로 허브에 연결되지 않으며 경로 테이블과 패킷 흐름에서 Azure Firewall을 사용하지 않습니다.

## <a name="next-steps"></a>다음 단계

* 가상 WAN에 대한 자세한 내용은 [가상 WAN FAQ](virtual-wan-faq.md)를 참조하세요.
* Azure Firewall에 대한 자세한 내용은 [Azure Firewall FAQ](../firewall/firewall-faq.yml)를 참조하세요.
