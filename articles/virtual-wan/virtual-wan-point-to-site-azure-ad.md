---
title: Azure Active Directory 인증을 이용하여 P2S 사용자 VPN 연결 구성
titleSuffix: Azure Virtual WAN
description: Virtual WAN 사용자 VPN(지점-사이트)에 대해 Azure Active Directory 인증을 구성하는 방법에 대해 알아봅니다.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: how-to
ms.date: 08/20/2021
ms.author: cherylmc
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 38006136ddd5a5331699b2768c35ebd794e563a8
ms.sourcegitcommit: 28cd7097390c43a73b8e45a8b4f0f540f9123a6a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/24/2021
ms.locfileid: "122778591"
---
# <a name="configure-a-point-to-site-user-vpn-connection---azure-active-directory-authentication"></a>지점 및 사이트 간 사용자 VPN 연결 구성 - Azure Active Directory 인증

이 문서에서는 OpenVPN VPN 연결을 통해 Azure에서 리소스에 연결하도록 Virtual WAN의 사용자 VPN에 대한 Azure AD 인증을 구성하는 방법을 보여 줍니다. Azure Active Directory 인증은 OpenVPN 프로토콜을 사용하는 게이트웨이에서만 사용할 수 있습니다. Virtual WAN에 대한 자세한 내용은 [Virtual WAN 개요](virtual-wan-about.md)를 참조하세요.

[!INCLUDE [OpenVPN note](../../includes/vpn-gateway-openvpn-auth-include.md)]

이 문서에서는 다음 방법을 설명합니다.

* 가상 WAN 만들기
* 사용자 VPN 구성 만들기
* Virtual WAN 사용자 VPN 프로필 다운로드
* 가상 허브 만들기
* 허브를 편집하여 P2S 게이트웨이 추가
* 가상 허브에 VNet 연결
* 사용자 VPN 클라이언트 구성 다운로드 및 적용
* 가상 WAN 보기

:::image type="content" source="./media/virtual-wan-about/virtualwanp2s.png" alt-text="Virtual WAN 다이어그램":::

## <a name="before-you-begin"></a>시작하기 전에

구성을 시작하기 전에 다음 기준을 충족하는지 확인합니다.

* 연결하려는 가상 네트워크가 있습니다. 온-프레미스 네트워크의 어떤 서브넷도 연결하려는 가상 네트워크 서브넷과 중첩되지 않는지 확인합니다. Azure Portal에서 가상 네트워크를 만들려면 [빠른 시작](../virtual-network/quick-create-portal.md)을 참조하세요.

* 가상 네트워크에 가상 네트워크 게이트웨이가 없습니다. 가상 네트워크에 게이트웨이(VPN 또는 ExpressRoute)가 있으면 모든 게이트웨이를 제거해야 합니다. 이 구성을 사용하려면 가상 네트워크가 Virtual WAN 허브 게이트웨이에 연결되어야 합니다.

* 허브 지역의 IP 주소 범위를 확보합니다. 허브는 Virtual WAN에서 만들고 사용하는 가상 네트워크입니다. 허브에 지정하는 주소 범위는 연결하는 기존 가상 네트워크와 겹칠 수 없습니다. 온-프레미스에 연결하는 주소 범위와도 겹칠 수 없습니다. 온-프레미스 네트워크 구성에 있는 IP 주소 범위를 잘 모른다면 세부 정보를 알고 있는 다른 사람의 도움을 받으세요.

* Azure 구독이 아직 없는 경우 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

## <a name="create-a-virtual-wan"></a><a name="wan"></a>Virtual WAN 만들기

브라우저에서 [Azure 포털](https://portal.azure.com) 로 이동하고 Azure 계정으로 로그인합니다.

[!INCLUDE [Create a virtual WAN](../../includes/virtual-wan-create-vwan-include.md)]

## <a name="create-a-user-vpn-configuration"></a><a name="user-config"></a>사용자 VPN 구성 만들기

사용자 VPN 구성은 원격 클라이언트 연결에 대한 매개 변수를 정의합니다. 사용할 사용자 VPN 구성을 지정해야 하므로 P2S 설정으로 가상 허브를 구성하기 전에 사용자 VPN 구성을 만드는 것이 중요합니다.

1. **Virtual WAN -> 사용자 VPN 구성** 페이지로 이동하여 **+사용자 VPN 구성 만들기** 를 클릭합니다.

   :::image type="content" source="./media/virtual-wan-point-to-site-azure-ad/user-vpn.png" alt-text="사용자 VPN 구성 만들기의 스크린샷":::
1. **기본 사항** 페이지에서 매개 변수를 지정합니다.

   :::image type="content" source="./media/virtual-wan-point-to-site-azure-ad/basics.png" alt-text="기본 사항 페이지의 스크린샷":::

   * **구성 이름** - 사용자 VPN 구성을 호출하려는 이름을 입력합니다.
    * **터널 유형** 의 드롭다운에서 OpenVPN을 선택합니다.
1. **Azure Active Directory** 를 클릭하여 페이지를 엽니다.

   :::image type="content" source="./media/virtual-wan-point-to-site-azure-ad/values.png" alt-text="Azure Active Directory 페이지의 스크린샷":::

    **Azure Active Directory** 를 **예** 로 전환하고 테넌트 세부 정보를 기반으로 다음 값을 입력합니다. 포털의 엔터프라이즈 애플리케이션용 Azure Active Directory 페이지에서 필요한 값을 볼 수 있습니다.
   * **인증 방법**: Azure Active Directory를 선택합니다.
   * **대상** - Azure AD 테넌트에 등록된 [Azure VPN](openvpn-azure-ad-tenant.md) 엔터프라이즈 애플리케이션의 애플리케이션 ID를 입력합니다. 
   * **발급자** - `https://sts.windows.net/<your Directory ID>/`
   * **AAD 테넌트** - `https://login.microsoftonline.com/<your Directory ID>`
1. **만들기** 를 클릭하여 사용자 VPN 구성을 만듭니다. 연습 뒷부분에서 이 구성을 선택합니다.

## <a name="create-an-empty-hub"></a><a name="site"></a>빈 허브 만들기

이 연습에서는 빈 가상 허브를 만듭니다. 다음 섹션에서는 기존 허브에 게이트웨이를 추가합니다. 그러나 이러한 단계를 결합하여 P2S 게이트웨이 설정으로 한 번에 허브를 만들 수 있습니다.

[!INCLUDE [Create an empty hub](../../includes/virtual-wan-empty-hub-include.md)]

## <a name="add-a-p2s-gateway-to-a-hub"></a><a name="hub"></a>허브에 P2S 게이트웨이 추가

이 섹션에서는 기존 가상 허브에 게이트웨이를 추가하는 방법을 보여 줍니다. 허브가 업데이트를 완료해야 하므로 이 단계는 최대 30분이 소요될 수 있습니다.

1. Virtual WAN 아래의 **허브** 페이지로 이동합니다.
1. VPN 서버 구성을 연결할 허브를 선택하고, 줄임표( **...** )를 클릭하여 메뉴를 표시합니다. 그런 다음, **가상 허브 편집** 을 클릭합니다.

   :::image type="content" source="media/virtual-wan-point-to-site-azure-ad/select-hub.png" alt-text="메뉴에서 선택한 가상 허브 편집을 보여 주는 스크린샷." lightbox="media/virtual-wan-point-to-site-azure-ad/select-hub.png":::

1. **가상 허브 편집** 페이지에서 **VPN 사이트에 대한 VPN 게이트웨이 포함** 및 **지점 및 사이트 간 게이트웨이 포함** 확인란을 선택하여 설정을 표시합니다. 그런 다음 값을 구성합니다.

   :::image type="content" source="./media/virtual-wan-point-to-site-azure-ad/edit-virtual-hub.png" alt-text="가상 허브 편집 페이지를 보여 주는 스크린샷":::

   * **게이트웨이 배율 단위**: 게이트웨이 배율 단위를 선택합니다. 배율 단위는 사용자 VPN 게이트웨이의 집계 용량을 나타냅니다. 40 이상의 게이트웨이 배율 단위를 선택하는 경우 클라이언트 주소 풀을 적절하게 계획합니다. 이 설정이 클라이언트 주소 풀에 영향을 주는 방법에 대한 자세한 내용은 [클라이언트 주소 풀 정보](about-client-address-pools.md)를 참조하세요. 게이트웨이 배율 단위에 대한 자세한 내용은 [FAQ](virtual-wan-faq.md#for-user-vpn-point-to-site--how-many-clients-are-supported)를 참조하세요.
   * **사용자 VPN 구성**: 이전에 만든 구성을 선택합니다.
   * **클라이언트 주소 풀**: VPN 클라이언트에 IP 주소를 할당하게 될 클라이언트 주소 풀을 지정합니다. 이 설정은 다음과 같은 게이트웨이 배율 단위에 해당합니다. 
1. **확인** 을 클릭합니다. 허브를 업데이트하는 데 최대 30분이 걸릴 수 있습니다.

## <a name="connect-vnet-to-hub"></a><a name="connect-vnet"></a>허브에 VNet 연결

이 섹션에서는 가상 허브 및 VNet 사이에 연결을 만듭니다.

[!INCLUDE [Connect virtual network](../../includes/virtual-wan-connect-vnet-hub-include.md)]

## <a name="download-user-vpn-profile"></a><a name="device"></a>사용자 VPN 프로필 다운로드

VPN 클라이언트에 필요한 모든 구성 설정은 VPN 클라이언트 구성 zip 파일에 포함되어 있습니다. zip 파일의 설정을 통해 VPN 클라이언트를 쉽게 구성할 수 있습니다. 생성하는 VPN 클라이언트 구성 파일은 게이트웨이의 사용자 VPN 구성과 연관됩니다. 이 섹션에서는 VPN 클라이언트 구성을 위해 사용되는 파일을 생성하고 다운로드합니다.

[!INCLUDE [Download profile](../../includes/virtual-wan-p2s-download-profile-include.md)]

## <a name="configure-user-vpn-clients"></a>사용자 VPN 클라이언트 구성

연결되는 각 컴퓨터에는 클라이언트가 설치되어 있어야 합니다. 이전 단계에서 다운로드한 VPN 사용자 클라이언트 프로필 파일을 사용하여 각 클라이언트를 구성합니다. 연결하려는 운영 체제와 관련된 문서를 사용합니다.

### <a name="to-configure-macos-vpn-clients-preview"></a>macOS VPN 클라이언트(미리 보기) 구성

**macOS** 클라이언트 지침은 [VPN 클라이언트 구성 - macOS(미리 보기)](openvpn-azure-ad-client-mac.md)를 참조하세요.

### <a name="to-configure-windows-vpn-clients"></a>Windows VPN 클라이언트 구성

[!INCLUDE [Download Azure VPN client](../../includes/vpn-gateway-download-vpn-client.md)]

#### <a name="to-import-a-vpn-client-profile-windows"></a><a name="import"></a>VPN 클라이언트 프로필 가져오기(Windows)

1. 페이지에서 **가져오기** 를 선택합니다.

   :::image type="content" source="./media/virtual-wan-point-to-site-azure-ad/import/import-1.png" alt-text="가져오기 페이지를 보여 주는 스크린샷":::

1. 프로필 xml 파일을 찾아서 선택합니다. 파일이 선택된 상태에서 **열기** 를 선택합니다.

    ![파일을 선택할 수 있는 열기 대화 상자를 보여 주는 스크린샷.](./media/virtual-wan-point-to-site-azure-ad/import/import2.jpg)

1. 프로필 이름을 지정하고, **저장** 을 선택합니다.

    ![추가된 연결 이름과 선택한 저장 단추를 보여 주는 스크린샷.](./media/virtual-wan-point-to-site-azure-ad/import/import3.jpg)

1. **연결** 을 선택하여 VPN에 연결합니다.

    ![방금 만든 연결에 대한 연결 단추를 보여 주는 스크린샷.](./media/virtual-wan-point-to-site-azure-ad/import/import4.jpg)

1. 연결되면 아이콘이 녹색으로 바뀌고 **연결됨** 으로 표시됩니다.

    ![연결된 상태의 연결과 연결 해제 옵션을 보여 주는 스크린샷.](./media/virtual-wan-point-to-site-azure-ad/import/import5.jpg)

#### <a name="to-delete-a-client-profile---windows"></a><a name="delete"></a>클라이언트 프로필 삭제 - Windows

1. 삭제하려는 클라이언트 프로필 옆의 줄임표(...)를 선택합니다. 그런 다음, **제거** 를 선택합니다.

    ![메뉴에서 선택한 제거 작업을 보여 주는 스크린샷.](./media/virtual-wan-point-to-site-azure-ad/delete/delete1.jpg)

1. **제거** 를 선택하여 삭제합니다.

    ![제거하거나 취소하는 옵션이 있는 확인 대화 상자를 보여 주는 스크린샷.](./media/virtual-wan-point-to-site-azure-ad/delete/delete2.jpg)

#### <a name="diagnose-connection-issues---windows"></a><a name="diagnose"></a>연결 문제 진단 - Windows

1. 연결 문제를 진단하려면 **진단** 도구를 사용할 수 있습니다. 진단하려는 VPN 연결 옆에 있는 줄임표(...)를 선택하여 메뉴를 표시합니다. 그런 다음, **진단** 을 선택합니다.

    ![메뉴에서 선택한 진단 작업을 보여 주는 스크린샷.](./media/virtual-wan-point-to-site-azure-ad/diagnose/diagnose1.jpg)

1. **연결 속성** 페이지에서 **진단 실행** 을 선택합니다.

    ![연결에 대한 진단 실행 단추를 보여 주는 스크린샷.](./media/virtual-wan-point-to-site-azure-ad/diagnose/diagnose2.jpg)

1. 자격 증명을 사용하여 로그인합니다.

    ![이 작업에 대한 로그인 대화 상자를 보여 주는 스크린샷.](./media/virtual-wan-point-to-site-azure-ad/diagnose/diagnose3.jpg)

1. 진단 결과를 살펴봅니다.

    ![진단의 결과를 보여 주는 스크린샷.](./media/virtual-wan-point-to-site-azure-ad/diagnose/diagnose4.jpg)

## <a name="view-your-virtual-wan"></a><a name="viewwan"></a>가상 WAN 보기

1. 가상 WAN 탭으로 이동합니다.
2. 개요 페이지의 맵에 있는 각 점은 허브를 나타냅니다.
3. 허브 및 연결 섹션에서 허브 상태, 사이트, 지역, VPN 연결 상태 및 입/출력 바이트를 볼 수 있습니다.

## <a name="clean-up-resources"></a><a name="cleanup"></a>리소스 정리

만든 리소스가 더 이상 필요하지 않은 경우 해당 리소스를 삭제합니다. 일부 Virtual WAN 리소스는 종속성으로 인해 특정 순서로 삭제해야 합니다. 삭제를 완료하는 데 약 30분이 걸릴 수 있습니다.

[!INCLUDE [Delete resources](../../includes/virtual-wan-resource-cleanup.md)]

## <a name="next-steps"></a>다음 단계

가상 WAN에 대해 자세히 알아보려면 [가상 WAN 개요](virtual-wan-about.md) 페이지를 참조하세요.
