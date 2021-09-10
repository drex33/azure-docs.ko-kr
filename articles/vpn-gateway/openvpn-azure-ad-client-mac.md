---
title: 'P2S OpenVPN 프로토콜 연결을 위한 VPN Client 구성: Azure AD 인증: macOS'
description: VPN Gateway 지점 및 사이트 간 및 Azure Active Directory 인증을 사용하여 가상 네트워크에 연결하도록 macOS VPN 클라이언트를 구성하는 방법을 알아봅니다.
titleSuffix: Azure VPN Gateway
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 08/19/2021
ms.author: cherylmc
ms.openlocfilehash: f2b23c469e7ababb9aab439e33cffd2de6e20eae
ms.sourcegitcommit: d43193fce3838215b19a54e06a4c0db3eda65d45
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/20/2021
ms.locfileid: "122568327"
---
# <a name="azure-active-directory-authentication-configure-a-vpn-client-for-p2s-openvpn-protocol-connections---macos"></a>Azure Active Directory 인증: P2S OpenVPN 프로토콜 연결을 위한 VPN 클라이언트 구성 - macOS

이 문서는 지점 및 사이트 간 VPN과 Azure Active Directory 인증을 사용하여 가상 네트워크에 연결하기 위해 macOS 10.15 이상을 실행하는 컴퓨터용 VPN 클라이언트를 구성하는 데 도움이 됩니다. Azure AD를 사용하여 연결 및 인증하기 위해서는 Azure AD 테넌트를 구성해야 합니다. 자세한 내용은 [Azure AD 테넌트 구성](openvpn-azure-ad-tenant.md)을 참조하세요. 지점 및 사이트 간 연결에 대한 자세한 내용은 [지점 및 사이트 간 연결 정보](point-to-site-about.md)를 참조하세요.

> [!NOTE]
> * Azure AD 인증은 OpenVPN® 프로토콜 연결에 대해서만 지원되며 Azure VPN Client가 필요합니다.
> * Azure VPN Client는 현지 규정으로 인해 일부 지역에서 사용하지 못할 수 있습니다.
>

지점 및 사이트 간 VPN 연결을 사용하여 VNet에 연결하려는 모든 컴퓨터에 대해 다음을 수행해야 합니다.
 
* 컴퓨터에 Azure VPN Client를 다운로드합니다.
* VPN 설정을 포함하는 클라이언트 프로필을 구성합니다. 

여러 컴퓨터를 구성하려는 경우 한 컴퓨터에서 클라이언트 프로필을 만든 후 내보낸 다음 다른 컴퓨터로 가져올 수 있습니다.

## <a name="prerequisites"></a>필수 구성 요소

Azure AD를 사용하여 연결 및 인증하기 위해서는 Azure AD 테넌트를 구성해야 합니다. 자세한 내용은 [Azure AD 테넌트 구성](openvpn-azure-ad-tenant.md)을 참조하세요.

## <a name="to-download-the-azure-vpn-client"></a><a name="download"></a>Azure VPN 클라이언트를 다운로드하려면,

1. Apple Store에서 [Azure VPN Client](https://apps.apple.com/us/app/azure-vpn-client/id1553936137)를 다운로드합니다.
1. 컴퓨터에 클라이언트를 설치합니다.

## <a name="to-import-a-connection-profile"></a><a name="import"></a>연결 프로필을 가져오려면

1. 프로필 파일을 다운로드하고 압축을 풉니다. 단계는 [VPN 클라이언트 프로필 파일 작업](about-vpn-profile-download.md)을 참조하세요.
1. Azure VPN Client 페이지에서 **가져오기** 를 선택합니다.

   :::image type="content" source="media/openvpn-azure-ad-client-mac/import-1.png" alt-text="Azure VPN Client 가져오기 선택의 스크린샷":::
1. 가져올 프로필 파일로 이동하여 선택한 다음 **열기** 를 클릭합니다.

   :::image type="content" source="media/openvpn-azure-ad-client-mac/import-2.png" alt-text="열기를 클릭한 Azure VPN Client 가져오기의 스크린샷":::
1. 연결 프로필 정보를 확인한 다음 **저장** 을 클릭합니다.

   :::image type="content" source="media/openvpn-azure-ad-client-mac/import-3.png" alt-text="가져온 프로필 설정을 저장하는 Azure VPN Client의 스크린샷":::
1. VPN 연결 창에서 저장한 연결 프로필을 선택합니다. 그런 다음, **연결** 을 클릭합니다.

   :::image type="content" source="media/openvpn-azure-ad-client-mac/import-4.png" alt-text="연결을 클릭하는 Azure VPN Client의 스크린샷":::
1. 연결되면 상태가 **연결됨** 으로 변경됩니다. 세션에서 연결을 끊으려면 **연결 끊기** 를 클릭합니다.

   :::image type="content" source="media/openvpn-azure-ad-client-mac/import-5.png" alt-text="Azure VPN Client 연결 상태 및 연결 끊기 단추의 스크린샷":::

## <a name="to-create-a-connection-manually"></a><a name="manual"></a> 수동으로 연결을 만들려면

1. Azure VPN Client를 엽니다. 새 연결을 만들려면 **추가** 를 선택합니다.

   :::image type="content" source="media/openvpn-azure-ad-client-mac/add-1.png" alt-text="추가를 선택하는 Azure VPN Client의 스크린샷":::

1. **Azure VPN Client** 페이지에서 프로필 설정을 구성할 수 있습니다.

   :::image type="content" source="media/openvpn-azure-ad-client-mac/add-2.png" alt-text="Azure VPN Client 프로필 설정의 스크린샷.":::

   다음 설정을 구성합니다.

   * **연결 이름:** 연결 프로필을 참조할 이름입니다.
   * **VPN 서버:** 이 이름은 서버를 참조하는 데 사용할 이름입니다. 여기서 선택한 이름은 서버의 정식 이름이 아니어도 됩니다.
   * **서버 유효성 검사**
     * **인증서 정보:** 인증서 CA.
     * **서버 비밀:** 서버 비밀입니다.
   * **클라이언트 인증**
     * **인증 유형:** Azure Active Directory
     * **테넌트:** 테넌트의 이름입니다.
     * **발급자:** 발급자의 이름입니다.
1. 필드를 입력한 후에 **저장** 을 클릭합니다.
1. VPN 연결 창에서 구성한 연결 프로필을 선택합니다. 그런 다음, **연결** 을 클릭합니다.

   :::image type="content" source="media/openvpn-azure-ad-client-mac/add-3.png" alt-text="Azure VPN Client 연결의 스크린샷":::
1. 자격 증명을 사용하여 로그인하여 연결합니다.

   :::image type="content" source="media/openvpn-azure-ad-client-mac/add-4.png" alt-text="연결을 위한 Azure VPN Client 로그인의 스크린샷":::
1. 연결되면 **연결됨** 상태가 표시됩니다. 연결을 끊으려면 **연결 끊기** 를 클릭하여 연결을 끊습니다.

   :::image type="content" source="media/openvpn-azure-ad-client-mac/add-5.png" alt-text="Azure VPN Client 연결 및 연결 해제 단추의 스크린샷":::

## <a name="to-remove-a-connection-profile"></a><a name="remove"></a>연결 프로필을 제거하려면

컴퓨터에서 VPN 연결 프로필을 제거할 수 있습니다. 

1. Azure VPN Client로 이동합니다.
1. 제거할 VPN 연결을 선택하고 드롭다운을 클릭한 다음 **제거** 를 선택합니다.

   :::image type="content" source="media/openvpn-azure-ad-client-mac/remove-1.png" alt-text="삭제 스크린샷":::
1. **VPN 연결을 제거할까요?** 상자에서 **제거** 를 클릭합니다.
   :::image type="content" source="media/openvpn-azure-ad-client-mac/remove-2.png" alt-text="제거의 스크린샷.":::

## <a name="next-steps"></a>다음 단계

자세한 내용은 [Azure AD 인증을 사용하는 P2S Open VPN 연결에 대한 Azure Active Directory 테넌트 만들기](openvpn-azure-ad-tenant.md)를 참조하세요.
