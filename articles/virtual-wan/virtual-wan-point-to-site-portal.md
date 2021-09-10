---
title: '자습서: Azure Virtual WAN을 사용하여 Azure에 지점 및 사이트 간 연결 만들기'
description: 이 자습서에서는 Azure Virtual WAN을 사용하여 Azure에 대한 사용자 VPN(지점 및 사이트 간) 연결을 만드는 방법을 알아봅니다.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: tutorial
ms.date: 08/23/2021
ms.author: cherylmc
ms.openlocfilehash: e08123ce666efebf7db2e4c07167125a76b46609
ms.sourcegitcommit: 28cd7097390c43a73b8e45a8b4f0f540f9123a6a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/24/2021
ms.locfileid: "122779909"
---
# <a name="tutorial-create-a-user-vpn-connection-using-azure-virtual-wan"></a>자습서: Azure Virtual WAN을 사용하여 사용자 VPN 연결 만들기

이 자습서에서는 Virtual WAN을 사용하여 사용자 VPN(P2S) 구성을 사용하는 OpenVPN 또는 IPsec/IKE(IKEv2) VPN 연결로 Azure의 리소스에 연결하는 방법을 보여줍니다. 이 연결 유형을 사용하려면 각 연결되는 클라이언트 컴퓨터에 고유 VPN 클라이언트를 구성해야 합니다.
* Azure AD 인증을 사용해서 사용자 VPN 연결을 만들려면 대신 [사용자 VPN 연결 구성 - Azure Active Directory 인증](virtual-wan-point-to-site-azure-ad.md) 문서를 따라야 합니다.
* Virtual WAN에 대한 자세한 내용은 [Virtual WAN 개요](virtual-wan-about.md)를 참조하세요.

이 자습서에서는 다음 작업 방법을 알아봅니다.

> [!div class="checklist"]
> * 가상 WAN 만들기
> * 사용자 VPN 구성 만들기
> * 가상 허브 및 게이트웨이 만들기
> * 클라이언트 구성 파일 생성
> * VPN 클라이언트 구성
> * VNet에 연결
> * 가상 WAN 보기
> * 설정 수정

:::image type="content" source="./media/virtual-wan-about/virtualwanp2s.png" alt-text="가상 WAN 다이어그램.":::

## <a name="prerequisites"></a>필수 구성 요소

[!INCLUDE [Before beginning](../../includes/virtual-wan-before-include.md)]

## <a name="create-a-virtual-wan"></a><a name="wan"></a>Virtual WAN 만들기

[!INCLUDE [Create a virtual WAN](../../includes/virtual-wan-create-vwan-include.md)]

## <a name="create-a-user-vpn-configuration"></a><a name="p2sconfig"></a>사용자 VPN 구성 만들기

사용자 VPN(P2S) 구성은 연결할 원격 클라이언트의 매개 변수를 정의합니다. 따라야 하는 지침은 사용하려는 인증 방법에 따라 달라집니다.

다음 단계에서는 인증 방법을 선택할 때 세 가지 옵션이 있습니다. 각 방법마다 특별한 요구 사항이 있습니다. 다음 방법 중 하나를 선택하여 단계를 완료합니다.

* **Azure 인증서:** 이 구성에서는 인증서가 필요합니다. 인증서를 생성하거나 획득해야 합니다. 각 클라이언트에 대해 클라이언트 인증서가 필요합니다. 또한 루트 인증서 정보(공개 키)를 업로드해야 합니다. 필요한 인증서에 대한 자세한 내용은 [인증서 생성 및 내보내기](certificates-point-to-site.md)를 참조하세요.

* **Azure Active Directory 인증:** 이 구성에 필요한 특정 단계가 포함된 [사용자 VPN 연결 구성 - Azure Active Directory 인증](virtual-wan-point-to-site-azure-ad.md) 문서를 사용합니다.

* **Radius 기반 인증:** Radius 서버 IP, Radius 서버 비밀 및 인증서 정보를 가져옵니다.

### <a name="configuration-steps"></a>구성 단계

[!INCLUDE [Create P2S configuration](../../includes/virtual-wan-p2s-configuration-include.md)]

## <a name="create-a-virtual-hub-and-gateway"></a><a name="hub"></a>가상 허브 및 게이트웨이 만들기

[!INCLUDE [Create hub](../../includes/virtual-wan-p2s-hub-include.md)]

## <a name="generate-client-configuration-files"></a><a name="download"></a>클라이언트 구성 파일 생성

사용자 VPN(P2S)을 사용하여 VNet에 연결할 때는 연결할 운영 체제에 고유하게 설치된 VPN 클라이언트를 사용합니다. VPN 클라이언트에 필요한 모든 구성 설정은 VPN 클라이언트 구성 zip 파일에 포함되어 있습니다. zip 파일의 설정을 통해 VPN 클라이언트를 쉽게 구성할 수 있습니다. 생성하는 VPN 클라이언트 구성 파일은 게이트웨이의 사용자 VPN 구성과 연관됩니다. 이 섹션에서는 VPN 클라이언트 구성을 위해 사용되는 파일을 생성하고 다운로드합니다.

[!INCLUDE [Download profile](../../includes/virtual-wan-p2s-download-profile-include.md)]

## <a name="configure-vpn-clients"></a><a name="configure-client"></a>VPN 클라이언트 구성

다운로드한 프로필 패키지를 사용하여 원격 액세스 VPN 클라이언트를 구성합니다. 운영 체제마다 절차가 다릅니다. 시스템에 적용되는 지침을 따르세요.
클라이언트 구성이 완료되면 연결할 수 있습니다.

[!INCLUDE [Configure clients](../../includes/virtual-wan-p2s-configure-clients-include.md)]

## <a name="connect-vnet-to-hub"></a><a name="connect-vnet"></a>허브에 VNet 연결

이 섹션에서는 가상 허브 및 VNet 사이에 연결을 만듭니다. 이 자습서에서는 라우팅 설정을 구성할 필요가 없습니다.

[!INCLUDE [Connect virtual network](../../includes/virtual-wan-connect-vnet-hub-include.md)]

## <a name="view-a-virtual-wan"></a><a name="viewwan"></a>가상 WAN 보기

1. **가상 WAN** 으로 이동합니다.

1. **개요** 페이지의 맵에 있는 각 점은 허브를 나타냅니다.

1. **허브 및 연결** 섹션에서 허브 상태, 사이트, 지역, VPN 연결 상태 및 입/출력 바이트를 볼 수 있습니다.

## <a name="modify-settings"></a>설정 수정

### <a name="modify-client-address-pool"></a><a name="address-pool"></a>클라이언트 주소 풀 수정

[!INCLUDE [Modify client address pool](../../includes/virtual-wan-client-address-pool-include.md)]

### <a name="modify-dns-servers"></a><a name="dns"></a>DNS 서버 수정

1. **Virtual HUB -> 사용자 VPN(지점 및 사이트 간)** 으로 이동합니다.

1. **사용자 지정 DNS 서버** 옆의 값을 클릭하여 **사용자 VPN 게이트웨이 편집** 페이지를 엽니다.

1. **사용자 VPN 게이트웨이 편집** 페이지에서 **사용자 지정 DNS 서버** 필드를 편집합니다. **사용자 지정 DNS 서버** 텍스트 상자에 DNS 서버 IP 주소를 입력합니다. DNS 서버를 최대 5개까지 지정할 수 있습니다.

1. 페이지 하단에 있는 **편집** 을 클릭하여 설정의 유효성을 검사합니다.

1. **확인** 을 클릭하여 설정을 저장합니다. 이 페이지의 모든 변경 작업을 완료하는 데 최대 30분이 걸릴 수 있습니다.

## <a name="clean-up-resources"></a><a name="cleanup"></a>리소스 정리

만든 리소스가 더 이상 필요하지 않은 경우 해당 리소스를 삭제합니다. 일부 Virtual WAN 리소스는 종속성으로 인해 특정 순서로 삭제해야 합니다. 삭제를 완료하는 데 약 30분이 걸릴 수 있습니다.

[!INCLUDE [Delete resources](../../includes/virtual-wan-resource-cleanup.md)]

## <a name="next-steps"></a>다음 단계


> [!div class="nextstepaction"]
> * [스포크 VNet의 리소스에 대한 보안 액세스 관리](manage-secure-access-resources-spoke-p2s.md)
