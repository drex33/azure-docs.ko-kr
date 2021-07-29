---
title: 'P2S VPN 연결을 위한 Azure AD 테넌트 만들기: Azure AD 인증'
titleSuffix: Azure VPN Gateway
description: P2S Azure AD 인증 - OpenVPN 프로토콜을 위한 Azure AD 테넌트를 설정하는 방법을 알아봅니다.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 05/27/2021
ms.author: cherylmc
ms.openlocfilehash: 34ea18e67752ed28986a08c4132ca10fbedce3c6
ms.sourcegitcommit: 1b698fb8ceb46e75c2ef9ef8fece697852c0356c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/28/2021
ms.locfileid: "110652480"
---
# <a name="create-an-azure-active-directory-tenant-for-p2s-openvpn-protocol-connections"></a>P2S OpenVPN 프로토콜 연결을 위한 Azure Active Directory 테넌트 만들기

지점 및 사이트 간을 사용하여 VNet에 연결할 때 사용할 프로토콜을 선택할 수 있습니다. 사용하는 프로토콜에 따라 사용 가능한 인증 옵션이 결정됩니다. Azure Active Directory 인증을 사용하려는 경우 OpenVPN 프로토콜을 사용할 때 이 작업을 수행할 수 있습니다. 이 문서는 Azure AD 테넌트를 설정하는 데 도움이 됩니다. 지점 및 사이트 간의 프로토콜 및 인증에 대한 자세한 내용은 [지점 및 사이트 간의 VPN 정보](point-to-site-about.md)를 참조하세요.

[!INCLUDE [OpenVPN note](../../includes/vpn-gateway-openvpn-auth-include.md)]

## <a name="1-verify-azure-ad-tenant"></a><a name="tenant"></a>1. Azure AD 테넌트 확인

Azure AD 테넌트가 있는지 확인합니다. Azure AD 테넌트가 없는 경우 [새 테넌트 만들기](../active-directory/fundamentals/active-directory-access-create-new-tenant.md) 문서의 단계를 사용하여 새로 만들 수 있습니다.

* 조직 구성 이름
* 초기 도메인 이름

   :::image type="content" source="./media/openvpn-create-azure-ad-tenant/newtenant.png" alt-text="디렉터리 만들기 페이지의 스크린샷" border="false":::

## <a name="2-create-azure-ad-tenant-users"></a><a name="users"></a>2. Azure AD 테넌트 사용자 만들기

Azure AD 테넌트에는 전역 관리자 계정 및 사용자 계정이 필요합니다. 사용자 계정은 임베딩 계정(서비스 계정)으로 사용됩니다. Azure AD 테넌트 사용자 계정을 만들 때 만들려는 사용자 유형에 대한 디렉터리 역할을 조정합니다.

[사용자 추가 또는 삭제 - Azure Active Directory](../active-directory/fundamentals/add-users-azure-active-directory.md)의 단계를 사용하여 Azure AD 테넌트에 둘 이상의 사용자를 만듭니다. 계정 유형을 만들려면 **디렉터리 역할** 을 변경해야 합니다.

* 전역 관리자
* 사용자

## <a name="3-enable-azure-ad-authentication-on-the-vpn-gateway"></a><a name="enable-authentication"></a>3. VPN 게이트웨이에서 Azure AD 인증 사용 설정

1. 인증에 사용할 디렉터리의 디렉터리 ID를 찾습니다. Active Directory 페이지의 속성 섹션에 나열되어 있습니다.

   :::image type="content" source="./media/openvpn-create-azure-ad-tenant/directory-id.png" alt-text="디렉터리 속성을 보여주는 스크린샷" lightbox="./media/openvpn-create-azure-ad-tenant/directory-id.png":::

1. 디렉터리 ID를 복사합니다.

1. **전역 관리자** 역할이 할당된 사용자로 Azure Portal에 로그인합니다.

1. 다음으로, 관리자 동의를 제공합니다. 브라우저의 주소 표시줄에서 배포 위치와 관련된 URL을 복사하여 붙여넣습니다.

   공용

   ```
   https://login.microsoftonline.com/common/oauth2/authorize?client_id=41b23e61-6c1e-4545-b367-cd054e0ed4b4&response_type=code&redirect_uri=https://portal.azure.com&nonce=1234&prompt=admin_consent
   ````

   Azure Government

   ```
   https://login.microsoftonline.us/common/oauth2/authorize?client_id=51bb15d4-3a4f-4ebf-9dca-40096fe32426&response_type=code&redirect_uri=https://portal.azure.us&nonce=1234&prompt=admin_consent
   ````

   Microsoft Cloud 독일

   ```
   https://login-us.microsoftonline.de/common/oauth2/authorize?client_id=538ee9e6-310a-468d-afef-ea97365856a9&response_type=code&redirect_uri=https://portal.microsoftazure.de&nonce=1234&prompt=admin_consent
   ````

    Azure China 21Vianet

    ```
    https://login.chinacloudapi.cn/common/oauth2/authorize?client_id=49f817b6-84ae-4cc0-928c-73f27289b3aa&response_type=code&redirect_uri=https://portal.azure.cn&nonce=1234&prompt=admin_consent
    ```

   > [!NOTE]
   > 동의를 제공하기 위해 Azure AD 테넌트의 기본이 아닌 전역 관리자 계정을 사용하는 경우 URL에서 "common"을 Azure AD 디렉터리 ID로 바꾸세요. 다른 특정 경우에도 "common"을 디렉터리 ID로 바꾸어야 할 수도 있습니다.
   >

1. 메시지가 표시되면 **전역 관리자** 계정을 선택합니다.

   :::image type="content" source="./media/openvpn-create-azure-ad-tenant/pick.png" alt-text="계정 선택 페이지가 표시된 스크린샷" border="false":::
1. 메시지가 표시되면 **수락** 을 선택합니다.

   :::image type="content" source="./media/openvpn-create-azure-ad-tenant/accept.jpg" alt-text="사용 권한 요청됨 조직 대신 수락 메시지와 세부 정보 및 수락 옵션을 보여주는 스크린샷." border="false":::
1. Azure AD 아래의 **엔터프라이즈 애플리케이션** 에서 **Azure VPN** 이 표시됩니다.

   :::image type="content" source="./media/openvpn-create-azure-ad-tenant/azurevpn.png" alt-text="모든 애플리케이션 페이지를 보여주는 스크린샷." lightbox="./media/openvpn-create-azure-ad-tenant/azurevpn.png" :::
1. 작동하는 지점-사이트 간 환경이 아직 없는 경우 지침에 따라 환경을 만듭니다. [지점 및 사이트 간 VPN 만들기](vpn-gateway-howto-point-to-site-resource-manager-portal.md)를 참조하여 지점 및 사이트 간 VPN 게이트웨이를 만듭니다.

    > [!IMPORTANT]
    > 기본 SKU는 OpenVPN에서 지원되지 않습니다.

1. **지점 및 사이트 간 구성** 으로 이동하고 **OpenVPN(SSL)** 을 **터널 유형** 으로 선택하여 VPN 게이트웨이에서 Azure AD 인증을 사용 설정합니다. **Azure Active Directory** 를 **인증 유형** 으로 선택하고 **Azure Active Directory** 섹션에 정보를 입력합니다.

   * **테넌트:** Azure AD 테넌트의 TenantID ```https://login.microsoftonline.com/{AzureAD TenantID}/```

   * **대상 그룹:** "Azure VPN" Azure AD Enterprise 앱의 애플리케이션 ID

       * Azure Public에 41b23e61-6c1e-4545-b367-cd054e0ed4b4 입력
       * Azure Government에 51bb15d4-3a4f-4ebf-9dca-40096fe32426 입력
       * Azure 독일에 538ee9e6-310a-468d-afef-ea97365856a9 입력
       * Azure 중국 21Vianet에 49f817b6-84ae-4cc0-928c-73f27289b3aa 입력


   * **발급자**: 보안 토큰 서비스의 URL ```https://sts.windows.net/{AzureAD TenantID}/```


   :::image type="content" source="./media/openvpn-create-azure-ad-tenant/azure-ad-auth-portal.png" alt-text="Azure Active Directory 설정, 터널 유형 및 인증 유형에 대한 설정을 보여주는 스크린샷" border="false":::

   > [!NOTE]
   > `AadIssuerUri` 값의 끝에 후행 슬래시를 포함해야 합니다. 그렇지 않으면 연결이 실패할 수 있습니다.
   >

1. **VPN 클라이언트 다운로드** 링크를 클릭하여 프로필을 만들고 다운로드합니다.

1. 다운로드한 zip 파일의 압축을 풉니다.

1. 압축 해제된 ‘AzureVPN’ 폴더로 이동합니다.

1. ‘azurevpnconfig.xml’ 파일의 위치를 기록합니다. azurevpnconfig.xml에는 VPN 연결에 대한 설정이 포함되어 있으며, Azure VPN Client 애플리케이션으로 직접 가져올 수 있습니다. 또한 이메일 또는 다른 방법을 통해 연결해야 하는 모든 사용자에게 이 파일을 배포할 수 있습니다. 성공적으로 연결하려면 사용자에게 유효한 Azure AD 자격 증명이 필요합니다.

## <a name="next-steps"></a>다음 단계

VPN 클라이언트 프로필을 만들고 구성합니다. [P2S VPN 연결을 위한 VPN 클라이언트 구성](openvpn-azure-ad-client.md)을 참조하세요.
