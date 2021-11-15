---
title: Azure AD Domain Services에 대한 Azure AD 애플리케이션 프록시 배포 | Microsoft Docs
description: Azure Active Directory Domain Services 관리되는 도메인에서 Azure Active Directory 애플리케이션 프록시를 배포 및 구성하여 원격 작업자를 위한 내부 애플리케이션에 안전한 액세스를 제공하는 방법 알아보기
services: active-directory-ds
author: justinha
manager: daveba
ms.assetid: 938a5fbc-2dd1-4759-bcce-628a6e19ab9d
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: how-to
ms.date: 07/09/2020
ms.author: justinha
ms.openlocfilehash: dbe44535f535eb2f0deb5ef30515cc188d3d5b22
ms.sourcegitcommit: 1f29603291b885dc2812ef45aed026fbf9dedba0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/29/2021
ms.locfileid: "129235424"
---
# <a name="deploy-azure-ad-application-proxy-for-secure-access-to-internal-applications-in-an-azure-active-directory-domain-services-managed-domain"></a>Azure Active Directory Domain Services 관리되는 도메인의 내부 애플리케이션에 안전하게 액세스할 수 있도록 Azure AD 애플리케이션 프록시 배포

Azure AD Domain Services(Azure AD DS)를 사용하여 온-프레미스에서 실행되는 레거시 애플리케이션을 Azure로 리프트 앤 시프트할 수 있습니다. 그러면 Azure AD(Active Directory) 애플리케이션 프록시를 통해 Azure AD DS 관리되는 도메인의 내부 애플리케이션 부분을 안전하게 게시하여 원격 작업자를 지원할 수 있으므로 인터넷을 통해 액세스할 수 있습니다.

Azure AD 애플리케이션 프록시를 처음 사용하고 더 알아보고 싶은 경우 [내부 애플리케이션에 안전한 원격 액세스 제공하는 방법](../active-directory/app-proxy/application-proxy.md)을 참조하세요.

이 문서에서는 관리되는 도메인의 애플리케이션에 대해 안전한 액세스를 제공하기 위해 Azure AD 애플리케이션 프록시 커넥터를 만들고 구성하는 방법을 보여줍니다.

## <a name="before-you-begin"></a>시작하기 전에

이 문서를 완료하는 데 필요한 리소스와 권한은 다음과 같습니다.

* 활성화된 Azure 구독.
    * Azure 구독이 없는 경우 [계정을 만듭니다](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* 온-프레미스 디렉터리 또는 클라우드 전용 디렉터리와 동기화되어 구독과 연결된 Azure Active Directory 테넌트
    * 필요한 경우 [Azure Active Directory 테넌트를 만들거나][create-azure-ad-tenant][Azure 구독을 계정에 연결합니다][associate-azure-ad-tenant].
    * **Azure AD Premium 라이선스** 를 사용하려면 Azure AD 애플리케이션 프록시가 필요합니다.
* Azure AD 테넌트에서 사용하도록 설정되고 구성된 Azure Active Directory Domain Services 관리되는 도메인
    * 필요한 경우 [Azure Active Directory Domain Services 관리형 도메인을 만들고 구성합니다][create-azure-ad-ds-instance].

## <a name="create-a-domain-joined-windows-vm"></a>도메인 조인 Windows VM 만들기

환경에서 실행 중인 애플리케이션으로 트래픽을 라우팅하려면 Azure AD 애플리케이션 프록시 커넥터 구성 요소를 설치합니다. 이 Azure AD 애플리케이션 프록시 커넥터는 관리되는 도메인에 조인한 Windows Server VM(가상 머신)에 설치되어야 합니다. 일부 애플리케이션의 경우 커넥터가 설치된 여러 서버를 배포할 수 있습니다. 이 배포 옵션을 선택하면 가용성이 높아지고 더 많은 인증 부하를 처리하는 데 도움이 됩니다.

Azure AD 애플리케이션 프록시 커넥터를 실행하는 VM은 관리되는 도메인과 동일하거나 피어링된 가상 네트워크에 있어야 합니다. 그런 다음 애플리케이션 프록시를 사용하여 게시하는 애플리케이션을 호스트하는 VM도 동일한 Azure 가상 네트워크에 배포해야 합니다.

Azure AD 애플리케이션 프록시 커넥터용 VM을 만들려면 다음 단계를 완료합니다.

1. [사용자 지정 OU를 만듭니다](create-ou.md). 이 사용자 지정 OU를 관리할 수 있는 권한을 관리되는 도메인 내의 사용자에게 위임할 수 있습니다. Azure AD 애플리케이션 프록시 및 애플리케이션을 실행하는 VM은 기본 *AAD DC 컴퓨터* OU가 아닌 사용자 지정 OU의 일부여야 합니다.
1. 관리되는 도메인에 Azure AD 애플리케이션 프록시 커넥터를 실행하는 가상 머신 및 애플리케이션을 실행하는 [가상 머신을 도메인 조인][create-join-windows-vm]합니다. 이전 단계에서 사용자 지정 OU에 이러한 컴퓨터 계정을 만듭니다.

## <a name="download-the-azure-ad-application-proxy-connector"></a>Azure AD 애플리케이션 프록시 커넥터 다운로드

Azure AD 애플리케이션 프록시 커넥터를 다운로드하려면 다음 단계를 수행합니다. 다운로드한 설치 파일은 다음 섹션에서 앱 프록시 VM에 복사됩니다.

1. Azure AD에서 *Enterprise 관리자* 권한이 있는 사용자 계정으로 [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. 포털 상단에서 **Azure Active Directory** 를 검색하여 선택한 후 **Enterprise 애플리케이션** 을 선택합니다.
1. 왼쪽 메뉴에서 **애플리케이션 프록시** 를 선택합니다. 첫 번째 커넥터를 만들고 앱 프록시를 사용하도록 설정하려면 **커넥터 다운로드** 링크를 선택합니다.
1. 다운로드 페이지에서 사용 조건 및 개인정보처리방침에 동의한 후 **사용 약관 동의 및 다운로드** 를 선택합니다.

    ![Azure AD 앱 프록시 커넥터 다운로드](./media/app-proxy/download-app-proxy-connector.png)

## <a name="install-and-register-the-azure-ad-application-proxy-connector"></a>Azure AD 애플리케이션 프록시 커넥터 설치 및 등록

Azure AD 애플리케이션 프록시 커넥터로 VM을 사용할 준비가 되면 이제 Azure Portal에서 다운로드한 설치 파일을 복사하여 실행합니다.

1. Azure AD 애플리케이션 프록시 커넥터 설치 파일을 VM에 복사합니다.
1. 설정 파일(예: *AADApplicationProxyConnectorInstaller.exe*)을 실행합니다. 소프트웨어 사용 조건에 동의합니다.
1. 설치하는 동안 Azure AD Directory의 애플리케이션 프록시를 사용하여 커넥터를 등록하라는 메시지가 표시됩니다.
   * Azure AD Directory의 전역 관리자에 대한 자격 증명을 제공합니다. Azure AD 전역 관리자 자격 증명은 포털의 Azure 자격 증명과 다를 수 있습니다.

        > [!NOTE]
        > 커넥터를 등록하는 데 사용되는 전역 관리자 계정은 애플리케이션 프록시 서비스를 사용할 수 있는 동일한 디렉터리에 있어야 합니다.
        >
        > 예를 들어, Azure AD 도메인이 *contoso.com* 인 경우, 전역 관리자는 `admin@contoso.com`이거나 해당 도메인에서 다른 유효한 별칭이어야 합니다.

   * 커넥터를 설치하는 VM에 Internet Explorer 보안 강화 구성이 켜져 있으면 등록 화면이 블록될 수 있습니다. 액세스를 허용하려면 오류 메시지의 지침에 따라 설치하는 동안 Internet Explorer 보안 강화를 끕니다.
   * 커넥터 등록에 실패한 경우 [애플리케이션 프록시 문제 해결](../active-directory/app-proxy/application-proxy-troubleshoot.md)을 참조하세요.
1. 설정 종료 시 아웃바운드 프록시를 사용하는 환경에 대한 노트가 표시됩니다. 아웃바운드 프록시를 통해 작동하도록 Azure AD 애플리케이션 프록시 커넥터를 구성하려면 제공되는 스크립트(예: `C:\Program Files\Microsoft AAD App Proxy connector\ConfigureOutBoundProxy.ps1`)를 실행하세요.
1. 다음 예제와 같이 Azure Portal의 애플리케이션 프록시 페이지에 새 커넥터가 *활성* 상태로 나열됩니다.

    ![Azure Portal에서 활성으로 표시된 새 Azure AD 애플리케이션 프록시 커넥터](./media/app-proxy/connected-app-proxy.png)

> [!NOTE]
> Azure AD 애플리케이션 프록시를 통해 인증하는 애플리케이션에 고가용성을 제공하기 위해 여러 VM에 커넥터를 설치할 수 있습니다. 관리되는 도메인에 조인된 다른 서버에 커넥터를 설치하려면 이전 섹션에 나열된 것과 동일한 단계를 반복합니다.

## <a name="enable-resource-based-kerberos-constrained-delegation"></a>리소스 기반 Kerberos 제한 위임 사용

IWA(Windows 통합 인증)를 사용하여 애플리케이션에 Single Sign-On을 사용하려는 경우 사용자를 가장하여 사용자 대신 토큰을 보내고 받을 수 있도록 Azure AD 애플리케이션 프록시 커넥터 사용 권한을 부여합니다. 이러한 권한을 부여하려면 관리되는 도메인에서 리소스에 액세스하도록 커넥터에 대한 KCD(Kerberos 제한 위임)를 구성합니다. 관리되는 도메인에서 도메인 관리자 권한이 없기 때문에 관리되는 도메인에서는 기존 계정 수준의 KCD를 구성할 수 없습니다. 대신 리소스 기반 KCD를 사용합니다.

자세한 내용은 [Azure Active Directory Domain Services에서 KCD(Kerberos 제한 위임) 구성](deploy-kcd.md)을 참조하세요.

> [!NOTE]
> 다음 PowerShell cmdlet을 실행하려면 Azure AD 테넌트에서 *Azure AD DC 관리자* 그룹의 구성원인 사용자 계정에 로그인해야 합니다.
>
> 앱 프록시 커넥터 VM 및 애플리케이션 VM의 컴퓨터 계정은 리소스 기반 KCD를 구성할 수 있는 권한이 있는 사용자 지정 OU에 있어야 합니다. 기본 제공 *AAD DC 컴퓨터* 컨테이너의 컴퓨터 계정에 대해서는 리소스 기반 KCD를 구성할 수 없습니다.

Azure AD 애플리케이션 프록시 커넥터가 설치되어 있는 컴퓨터에 대한 설정을 검색하려면 [Get-ADComputer][Get-ADComputer]를 사용합니다. 도메인 조인 관리 VM에서 *Azure AD DC 관리자* 그룹의 구성원인 사용자 계정으로 로그인하고 다음 cmdlet을 실행합니다.

다음 예제에서는 *appproxy.aaddscontoso.com* 이라는 컴퓨터 계정에 대한 정보를 가져옵니다. 이전 단계에서 구성된 Azure AD 애플리케이션 프록시 VM에 대한 고유한 컴퓨터 이름을 제공합니다.

```powershell
$ImpersonatingAccount = Get-ADComputer -Identity appproxy.aaddscontoso.com
```

Azure AD 애플리케이션 프록시 뒤에서 앱을 실행하는 각 애플리케이션 서버에 대해 리소스 기반 KCD를 구성하려면 [Set-ADComputer][Set-ADComputer] PowerShell cmdlet을 사용합니다. 다음 예제에서는 Azure AD 애플리케이션 프록시 커넥터에 *appserver.aaddscontoso.com* 컴퓨터를 사용할 수 있는 권한이 부여됩니다.

```powershell
Set-ADComputer appserver.aaddscontoso.com -PrincipalsAllowedToDelegateToAccount $ImpersonatingAccount
```

여러 Azure AD 애플리케이션 프록시 커넥터를 배포하는 경우 각 커넥터 인스턴스에 대해 리소스 기반 KCD를 구성해야 합니다.

## <a name="next-steps"></a>다음 단계

Azure AD DS와 통합된 Azure AD 애플리케이션 프록시를 사용하여 사용자가 액세스할 수 있도록 애플리케이션을 게시합니다. 자세한 내용은 [Azure AD 애플리케이션 프록시를 사용하여 애플리케이션 게시](../active-directory/app-proxy/application-proxy-add-on-premises-application.md)를 참조하세요.

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[create-join-windows-vm]: join-windows-vm.md
[azure-bastion]: ../bastion/tutorial-create-host-portal.md
[Get-ADComputer]: /powershell/module/activedirectory/get-adcomputer
[Set-ADComputer]: /powershell/module/activedirectory/set-adcomputer