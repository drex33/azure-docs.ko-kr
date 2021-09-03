---
title: Azure AD Domain Services에 대한 DNS 관리 | Microsoft Docs
description: DNS 서버 도구를 설치하여 DNS를 관리하고 Azure Active Directory Domain Services 관리되는 도메인에 대한 조건부 전달자를 만드는 방법을 알아봅니다.
author: justinha
manager: daveba
ms.assetid: 938a5fbc-2dd1-4759-bcce-628a6e19ab9d
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: how-to
ms.date: 06/18/2021
ms.author: justinha
ms.openlocfilehash: 935abb1a1eae362e3036c323da35aa5fd7b9b018
ms.sourcegitcommit: e6de87b42dc320a3a2939bf1249020e5508cba94
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/27/2021
ms.locfileid: "114707973"
---
# <a name="administer-dns-and-create-conditional-forwarders-in-an-azure-active-directory-domain-services-managed-domain"></a>Azure Active Directory Domain Services 관리되는 도메인에서 DNS를 관리하고 조건부 전달자 만들기

Azure AD DS에는 관리되는 도메인에 대한 이름 확인을 제공하는 DNS(Domain Name System) 서버가 포함되어 있습니다. 이 DNS 서버에는 기본 제공 DNS 레코드와 서비스를 실행하는 데 사용할 수 있는 주요 구성 요소에 대한 업데이트가 포함되어 있습니다.

사용자 고유의 애플리케이션 및 서비스를 실행하는 경우 도메인에 연결되지 않은 머신에 대한 DNS 레코드를 만들고, 부하 분산 장치에 대한 가상 IP 주소를 구성하거나, 외부 DNS 전달자를 설정해야 할 수 있습니다. *AAD DC 관리자* 그룹에 속한 사용자에게는 Azure AD DS 관리되는 도메인에 대한 DNS 관리 권한이 부여되고 사용자 지정 DNS 레코드를 만들고 편집할 수 있습니다.

하이브리드 환경에서 온-프레미스 AD DS 환경과 같은 다른 DNS 네임스페이스에서 구성된 DNS 영역 및 레코드는 관리되는 도메인에 동기화되지 않습니다. 다른 DNS 네임스페이스의 명명된 리소스를 확인하려면 환경에서 기존 DNS 서버를 가리키는 조건부 전달자를 만들고 사용합니다.

이 문서에서는 DNS 서버 도구를 설치한 다음 DNS 콘솔을 사용하여 Azure AD DS에서 레코드를 관리하고 조건부 전달자를 만드는 방법을 보여 줍니다.

>[!NOTE]
>서버 수준 DNS 전달자를 만들거나 변경하는 기능은 지원되지 않으며 Azure AD DS 관리되는 도메인과 관련된 문제가 발생합니다.

## <a name="before-you-begin"></a>시작하기 전에

이 문서를 완료하는 데 필요한 리소스와 권한은 다음과 같습니다.

* 활성화된 Azure 구독.
    * Azure 구독이 없는 경우 [계정을 만듭니다](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* 온-프레미스 디렉터리 또는 클라우드 전용 디렉터리와 동기화되어 구독과 연결된 Azure Active Directory 테넌트
    * 필요한 경우 [Azure Active Directory 테넌트를 만들거나][create-azure-ad-tenant][Azure 구독을 계정에 연결합니다][associate-azure-ad-tenant].
* Azure AD 테넌트에서 사용하도록 설정되고 구성된 Azure Active Directory Domain Services 관리되는 도메인
    * 필요한 경우 자습서를 완료하여 [Azure Active Directory Domain Services 관리되는 도메인을 만들고 구성][create-azure-ad-ds-instance]합니다.
* Azure AD DS 가상 네트워크에서 다른 DNS 네임스페이스를 호스트하는 위치로 연결
    * [Azure ExpressRoute][expressroute] 또는 [Azure VPN Gateway][vpn-gateway] 연결을 사용하여 이 연결을 제공할 수 있습니다.
* 관리되는 도메인에 조인된 Windows Server 관리 VM입니다.
    * 필요한 경우 자습서를 완료하여 [Windows Server VM을 만들어 관리되는 도메인에 조인시킵니다][create-join-windows-vm].
* Azure AD 테넌트의 *Azure AD DC Administrators* 그룹에 속한 멤버인 사용자 계정

## <a name="install-dns-server-tools"></a>DNS 서버 도구 설치

관리되는 도메인에서 DNS 레코드를 만들고 수정하려면 DNS 서버 도구를 설치해야 합니다. 이러한 도구는 Windows Server의 기능으로 설치할 수 있습니다. 관리 도구를 Windows 클라이언트에 설치하는 방법에 대한 자세한 내용은 [RSAT(원격 서버 관리 도구) 설치][install-rsat]를 참조하세요.

1. 관리 VM에 로그인합니다. Azure Portal을 사용하여 연결하는 방법에 대한 단계는 [Windows Server VM에 연결][connect-windows-server-vm]을 참조하세요.
1. VM에 로그인할 때 **서버 관리자** 가 기본적으로 열리지 않는 경우 **시작** 메뉴를 선택한 다음, **서버 관리자** 를 선택합니다.
1. **서버 관리자** 창의 *대시보드* 창에서 **역할 및 기능 추가** 를 선택합니다.
1. *역할 및 기능 추가 마법사* 의 **시작하기 전에** 페이지에서 **다음** 을 선택합니다.
1. *설치 유형* 에서 **역할 기반 또는 기능 기반 설치** 옵션을 선택한 상태로 두고, **다음** 을 선택합니다.
1. **서버 선택** 페이지의 서버 풀에서 현재 VM(예: *myvm.aaddscontoso.com*), **다음** 을 차례로 선택합니다.
1. **서버 역할** 페이지에서 **다음** 을 클릭합니다.
1. **기능** 페이지에서 **원격 서버 관리 도구** 노드, **역할 관리 도구** 노드를 차례로 펼칩니다. 역할 관리 도구 목록에서 **DNS 서버 도구** 기능을 선택합니다.

    ![사용 가능한 역할 관리 도구 목록에서 DNS 서버 도구 설치를 선택합니다.](./media/manage-dns/install-dns-tools.png)

1. **확인** 페이지에서 **설치** 를 선택합니다. DNS 서버 도구를 설치하는 데 1~2분 정도 걸릴 수 있습니다.
1. 기능 설치가 완료되면 **닫기** 를 선택하여 **역할 및 기능 추가** 마법사를 종료합니다.

## <a name="open-the-dns-management-console-to-administer-dns"></a>DNS 관리 콘솔을 열어 DNS 관리

DNS 서버 도구가 설치된 상태에서 관리되는 도메인의 DNS 레코드를 관리할 수 있습니다.

> [!NOTE]
> 관리되는 도메인의 DNS를 관리하려면 *AAD DC 관리자* 그룹의 구성원인 사용자 계정에 로그인해야 합니다.

1. 시작 화면에서 **관리 도구** 를 선택합니다. 이전 섹션에서 설치된 **DNS** 를 비롯하여 사용 가능한 관리 도구 목록이 표시됩니다. **DNS** 를 선택하여 DNS 관리 콘솔을 시작합니다.
1. **DNS 서버에 연결** 대화 상자에서 **다음 컴퓨터** 를 선택한 다음 관리되는 도메인의 DNS 도메인 이름(예: *aaddscontoso.com*)을 입력합니다.

    ![DNS 콘솔에서 관리되는 도메인에 연결](./media/manage-dns/connect-dns-server.png)

1. DNS 콘솔이 지정된 관리되는 도메인에 연결됩니다. **정방향 조회 영역** 또는 **역방향 조회 영역** 을 펼쳐 필요한 DNS 항목을 만들거나 필요에 따라 기존 레코드를 편집합니다.

    ![DNS 콘솔 - 도메인 관리](./media/manage-dns/dns-manager.png)

> [!WARNING]
> DNS 서버 도구를 사용하여 레코드를 관리하는 경우 Azure AD DS에서 사용되는 기본 제공 DNS 레코드를 삭제하거나 수정하지 않도록 해야 합니다. 기본 제공 DNS 레코드는 도메인 DNS 레코드, 이름 서버 레코드 및 DC 위치에 사용되는 기타 레코드를 포함합니다. 이러한 레코드를 수정하는 경우 가상 네트워크에서 도메인 서비스가 중단됩니다.

## <a name="create-conditional-forwarders"></a>조건부 전달자 만들기

Azure AD DS DNS 영역에는 관리되는 도메인 자체에 대한 영역 및 레코드만 포함되어야 합니다. 다른 DNS 네임스페이스에서 명명된 리소스를 확인하기 위해 관리되는 도메인에 추가 영역을 만들지 마세요. 대신 관리되는 도메인의 조건부 전달자를 사용하여 해당 리소스에 대한 주소를 확인하기 위해 DNS 서버에 이동 위치를 알릴 수 있습니다.

조건부 전달자는 쿼리를 전달할 DNS 도메인(예: *contoso.com*)을 정의할 수 있는 DNS 서버의 구성 옵션입니다. 해당 도메인의 레코드에 대한 쿼리를 확인하려는 로컬 DNS 서버 대신 DNS 쿼리가 해당 도메인에 대해 구성된 DNS로 전달됩니다. 이 구성은 관리되는 도메인에 중복 레코드가 있는 로컬 DNS 영역을 만들어 해당 리소스를 반영하지 않기 때문에 올바른 DNS 레코드가 반환되도록 합니다.

관리되는 도메인에서 조건부 전달자를 만들려면 다음 단계를 완료합니다.

1. DNS 영역(예: *aaddscontoso.com*)을 선택합니다.
1. **조건부 전달자** 를 선택한 다음 오른쪽을 선택하고 **새 조건부 전달자...** 를 선택합니다.
1. 다른 **DNS 도메인** *(예: contoso.com)* 을 입력한 다음, 아래 예제와 같이 해당 네임스페이스에 대한 DNS 서버의 IP 주소를 입력합니다.

    ![DNS 서버에 대한 조건부 전달자 추가 및 구성](./media/manage-dns/create-conditional-forwarder.png)

1. **Active Directory에 이 조건부 전달자를 저장하고 다음과 같이 복제** 확인란을 선택하고 다음 예제와 같이 ‘이 도메인의 모든 DNS 서버’ 옵션을 선택합니다.

    ![DNS 콘솔 - 이 도메인의 모든 DNS 서버 선택](./media/manage-dns/store-in-domain.png)

    > [!IMPORTANT]
    > 조건부 전달자가 ‘도메인’ 대신 ‘포리스트’에 저장되면 조건부 전달자가 실패합니다. 

1. 조건부 전달자를 만들려면 **확인** 을 선택합니다.

이제 관리되는 도메인에 연결된 VM의 다른 네임스페이스에 있는 리소스의 이름이 올바르게 확인되어야 합니다. 조건부 전달자에 구성된 DNS 도메인에 대한 쿼리는 관련 DNS 서버로 전달됩니다.

## <a name="next-steps"></a>다음 단계

DNS 관리에 대한 자세한 내용은 [Technet의 DNS 도구 문서](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc753579(v=ws.11))를 참조하세요.

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[expressroute]: ../expressroute/expressroute-introduction.md
[vpn-gateway]: ../vpn-gateway/vpn-gateway-about-vpngateways.md
[create-join-windows-vm]: join-windows-vm.md
[tutorial-create-management-vm]: tutorial-create-management-vm.md
[connect-windows-server-vm]: join-windows-vm.md#connect-to-the-windows-server-vm

<!-- EXTERNAL LINKS -->
[install-rsat]: /windows-server/remote/remote-server-administration-tools#BKMK_Thresh