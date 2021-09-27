---
title: MFA용 VPN Gateway RADIUS 인증과 NPS 통합
titleSuffix: Azure VPN Gateway
description: Multi-Factor Authentication을 위한 NPS 서버와 Azure VPN Gateway RADIUS 인증을 통합하는 방법에 대해 알아봅니다.
services: vpn-gateway
author: ahmadnyasin
manager: dcscontentpm
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 09/16/2019
ms.author: genli
ms.openlocfilehash: 6fada4ca0ae24c2f3b859e02c55c7406cc44bb51
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124734968"
---
# <a name="integrate-azure-vpn-gateway-radius-authentication-with-nps-server-for-multi-factor-authentication"></a>Multi-Factor Authentication을 위한 NPS 서버와 Azure VPN Gateway RADIUS 인증 통합 

이 문서에서는 NPS(네트워크 정책 서버)를 Azure VPN Gateway RADIUS 인증과 통합하여 지점과 사이트 간 VPN 연결에 MFA(Multi-Factor Authentication)를 제공하는 방법을 설명합니다. 

## <a name="prerequisite"></a>필수 조건

MFA를 사용하려면 사용자가 Azure AD(Azure Active Directory)에 있어야 하며 이는 온-프레미스 또는 클라우드 환경에서 동기화되어야 합니다. 또한 사용자가 MFA에 대한 자동 등록 프로세스를 이미 완료해 놓아야 합니다.  자세한 내용은 [2단계 인증에 내 계정 설정](https://support.microsoft.com/account-billing/how-to-use-the-microsoft-authenticator-app-9783c865-0308-42fb-a519-8cf666fe0acc)을 참조하세요.

## <a name="detailed-steps"></a>자세한 단계

### <a name="step-1-create-a-virtual-network-gateway"></a>단계 1: 가상 네트워크 게이트웨이 만들기

1. [Azure 포털](https://portal.azure.com)에 로그온합니다.
2. 가상 네트워크 게이트웨이를 호스트하는 가상 네트워크에서 **서브넷** 을 선택한 후 **게이트웨이 서브넷** 을 선택하여 서브넷을 만듭니다. 

    ![게이트웨이 서브넷 추가 방법에 대한 이미지](./media/vpn-gateway-radiuis-mfa-nsp/gateway-subnet.png)
3. 다음 설정을 지정하여 가상 네트워크 게이트웨이를 만듭니다.

    - **게이트웨이 유형**: **VPN** 을 선택합니다.
    - **VPN 유형**: **경로 기반** 을 선택합니다.
    - **SKU**: 요구 사항에 따라 SKU 유형을 선택합니다.
    - **가상 네트워크**: 게이트웨이 서브넷을 만든 가상 네트워크를 선택합니다.

        ![가상 네트워크 게이트웨이 설정에 대한 이미지](./media/vpn-gateway-radiuis-mfa-nsp/create-vpn-gateway.png)


 
### <a name="step-2-configure-the-nps-for-azure-ad-mfa"></a>2단계 Azure AD MFA용 NPS 구성

1. NPS 서버에서 [Azure AD MFA용 NPS 확장을 설치](../active-directory/authentication/howto-mfa-nps-extension.md#install-the-nps-extension)합니다.
2. NPS 콘솔을 열고 **RADIUS 클라이언트** 를 마우스 오른쪽 단추로 클릭한 후 **새로 만들기** 를 선택합니다. 다음 설정을 지정하여 RADIUS 클라이언트를 만듭니다.

    - **식별 이름**: 이름을 입력합니다.
    - **주소(IP 또는 DNS)**: 1단계에서 만든 게이트웨이 서브넷을 입력합니다.
    - **공유 비밀**: 비밀 키를 입력하고 나중에 사용할 수 있도록 기억합니다.

      ![RADIUS 클라이언트 설정에 대한 이미지](./media/vpn-gateway-radiuis-mfa-nsp/create-radius-client1.png)

 
3.  **고급** 탭에서 공급업체 이름을 **RADIUS 표준** 으로 설정하고 **추가 옵션** 확인란이 선택되지 않았는지 확인합니다.

    ![RADIUS 클라이언트 고급 설정에 대한 이미지](./media/vpn-gateway-radiuis-mfa-nsp/create-radius-client2.png)

4. **정책** > **네트워크 정책** 으로 이동하고 **Microsoft 라우팅 및 원격 액세스 서버에 연결** 정책을 두 번 클릭하여 **액세스 권한 부여** 를 선택한 다음 **확인** 을 클릭합니다.

### <a name="step-3-configure-the-virtual-network-gateway"></a>3단계 가상 네트워크 게이트웨이 구성

1. [Azure Portal](https://portal.azure.com)에 로그온합니다.
2. 만든 가상 네트워크 게이트웨이를 엽니다. 게이트웨이 유형이 **VPN** 으로 설정되어 있고 VPN 유형이 **경로 기반** 인지 확인합니다.
3. **지점과 사이트 간 구성** > **지금 구성** 을 클릭한 후 다음 설정을 지정합니다.

    - **주소 풀**: 1단계에서 만든 게이트웨이 서브넷을 입력합니다.
    - **인증 유형**: **RADIUS 인증** 을 선택합니다.
    - **서버 IP 주소**: NPS 서버의 IP 주소를 입력합니다.

      ![지점과 사이트 간 설정에 대한 이미지](./media/vpn-gateway-radiuis-mfa-nsp/configure-p2s.png)

## <a name="next-steps"></a>다음 단계

- [Azure AD Multi-Factor Authentication](../active-directory/authentication/concept-mfa-howitworks.md)
- [기존 NPS 인프라를 Azure AD Multi-Factor Authentication과 통합](../active-directory/authentication/howto-mfa-nps-extension.md)