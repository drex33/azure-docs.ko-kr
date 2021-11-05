---
title: Azure AD Domain Services에서 그룹 정책 만들기 및 관리 | Microsoft Docs
description: 기본 제공 GPO(그룹 정책 개체)를 편집하고 Azure Active Directory Domain Services 관리되는 도메인에서 고유한 사용자 지정 정책을 만드는 방법을 알아봅니다.
author: justinha
manager: daveba
ms.assetid: 938a5fbc-2dd1-4759-bcce-628a6e19ab9d
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: how-to
ms.date: 07/26/2021
ms.author: justinha
ms.openlocfilehash: 07ce4db1e3cca141ccbddde61f9dc196da801698
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131044458"
---
# <a name="administer-group-policy-in-an-azure-active-directory-domain-services-managed-domain"></a>Azure Active Directory Domain Services 관리되는 도메인에서 그룹 정책 관리

Azure AD DS(Azure Active Directory Domain Services)의 사용자 및 컴퓨터 개체에 대한 설정은 GPO(그룹 정책 개체)를 사용하여 관리되는 경우가 많습니다. Azure AD DS에는 *AADDC 사용자* 및 *AADDC 컴퓨터* 컨테이너에 대한 기본 제공 GPO가 포함됩니다. 이러한 기본 제공 GPO를 사용자 지정하여 사용자 환경에 필요한 대로 그룹 정책을 구성할 수 있습니다. *Azure AD DC 관리자* 그룹의 멤버는 Azure AD DS 도메인에서 그룹 정책 관리 권한이 있으며 사용자 지정 GPO 및 OU(조직 구성 단위)를 만들 수도 있습니다. 그룹 정책의 정의 및 작동 방식에 대한 자세한 내용은 [그룹 정책 개요][group-policy-overview]를 참조하세요.

하이브리드 환경에서 온-프레미스 AD DS 환경에 구성된 그룹 정책은 Azure AD DS와 동기화되지 않습니다. Azure AD DS에서 사용자 또는 컴퓨터에 대한 구성 설정을 정의하려면 기본 GPO 중 하나를 편집하거나 사용자 지정 GPO를 만듭니다.

이 문서에서는 그룹 정책 관리 도구를 설치한 다음, 기본 제공 GPO를 편집하고 사용자 지정 GPO를 만드는 방법을 보여 줍니다.

Azure의 머신 및 [하이브리드 연결](../azure-arc/servers/overview.md)을 비롯한 서버 관리 전략에 관심이 있는 경우, [그룹 정책 콘텐츠를 [Azure Policy](../governance/policy/overview.md) [게스트 구성](../governance/policy/concepts/guest-configuration.md) 기능으로 변환](../governance/policy/how-to/guest-configuration-create-group-policy.md)하는 방법을 읽어 보십시오.

## <a name="before-you-begin"></a>시작하기 전에

이 문서를 완료하는 데 필요한 리소스와 권한은 다음과 같습니다.

* 활성화된 Azure 구독.
    * Azure 구독이 없는 경우 [계정을 만듭니다](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* 온-프레미스 디렉터리 또는 클라우드 전용 디렉터리와 동기화되어 구독과 연결된 Azure Active Directory 테넌트
    * 필요한 경우 [Azure Active Directory 테넌트를 만들거나][create-azure-ad-tenant][Azure 구독을 계정에 연결합니다][associate-azure-ad-tenant].
* Azure AD 테넌트에서 사용하도록 설정되고 구성된 Azure Active Directory Domain Services 관리되는 도메인
    * 필요한 경우 자습서를 완료하여 [Azure Active Directory Domain Services의 관리되는 도메인을 만들고 구성][create-azure-ad-ds-instance]합니다.
* Azure AD DS 관리되는 도메인에 연결된 Windows Server 관리 VM입니다.
    * 필요한 경우 자습서를 완료하여 [Windows Server VM을 만들어 관리되는 도메인에 조인시킵니다][create-join-windows-vm].
* Azure AD 테넌트의 *Azure AD DC Administrators* 그룹에 속한 멤버인 사용자 계정

> [!NOTE]
> 관리 워크스테이션에 새 템플릿을 복사하여 그룹 정책 관리 템플릿을 사용할 수 있습니다. *.admx* 파일을 `%SYSTEMROOT%\PolicyDefinitions`로 복사하고 로캘 관련 *.adml* 파일을 `%SYSTEMROOT%\PolicyDefinitions\[Language-CountryRegion]`으로 복사합니다. 여기서 `Language-CountryRegion`은 *.adml* 파일의 언어 및 지역과 일치합니다.
>
> 예를 들어 영어, 미국 버전의 *.adml* 파일을 `\en-us` 폴더에 복사합니다.

## <a name="install-group-policy-management-tools"></a>그룹 정책 관리 도구 설치

GPO(그룹 정책 개체)를 만들고 구성하려면 그룹 정책 관리 도구를 설치해야 합니다. 이러한 도구는 Windows Server의 기능으로 설치할 수 있습니다. 관리 도구를 Windows 클라이언트에 설치하는 방법에 대한 자세한 내용은 [RSAT(원격 서버 관리 도구) 설치][install-rsat]를 참조하세요.

1. 관리 VM에 로그인합니다. Azure Portal을 사용하여 연결하는 방법에 대한 단계는 [Windows Server VM에 연결][connect-windows-server-vm]을 참조하세요.
1. VM에 로그인하면 **서버 관리자** 가 기본적으로 열립니다. 그렇지 않은 경우 **시작** 메뉴에서 **서버 관리자** 를 선택합니다.
1. **서버 관리자** 창의 *대시보드* 창에서 **역할 및 기능 추가** 를 선택합니다.
1. *역할 및 기능 추가 마법사* 의 **시작하기 전에** 페이지에서 **다음** 을 선택합니다.
1. *설치 유형* 에서 **역할 기반 또는 기능 기반 설치** 옵션을 선택한 상태로 두고, **다음** 을 선택합니다.
1. **서버 선택** 페이지의 서버 풀에서 현재 VM(예: *myvm.aaddscontoso.com*), **다음** 을 차례로 선택합니다.
1. **서버 역할** 페이지에서 **다음** 을 클릭합니다.
1. **기능** 페이지에서 **그룹 정책 관리** 기능을 선택합니다.

    ![기능 페이지에서 '그룹 정책 관리' 설치](./media/active-directory-domain-services-admin-guide/install-rsat-server-manager-add-roles-gp-management.png)

1. **확인** 페이지에서 **설치** 를 선택합니다. 그룹 정책 관리 도구를 설치하는 데 1-2분 정도 걸릴 수 있습니다.
1. 기능 설치가 완료되면 **닫기** 를 선택하여 **역할 및 기능 추가** 마법사를 종료합니다.

## <a name="open-the-group-policy-management-console-and-edit-an-object"></a>그룹 정책 관리 콘솔을 열고 개체 편집

관리되는 도메인의 사용자 및 컴퓨터에 대해 기본 GPO(그룹 정책 개체)가 존재합니다. 이전 섹션에서 설치된 그룹 정책 관리 기능을 사용하여 기존 GPO를 보고 편집해 보겠습니다. 다음 섹션에서는 사용자 지정 GPO를 만듭니다.

> [!NOTE]
> 관리되는 도메인에서 그룹 정책을 관리하려면 *AAD DC Administrators* 그룹의 구성원인 사용자 계정에 로그인해야 합니다.

1. 시작 화면에서 **관리 도구** 를 선택합니다. 이전 섹션에 설치된 **그룹 정책 관리** 를 비롯한 사용 가능한 관리 도구 목록이 표시됩니다.
1. GPMC(그룹 정책 관리 콘솔)를 열려면 **그룹 정책 관리** 를 선택합니다.

    ![그룹 정책 관리 콘솔을 열어 그룹 정책 개체를 편집할 준비를 합니다.](./media/active-directory-domain-services-admin-guide/gp-management-console.png)

관리되는 도메인에는 *AADDC 컴퓨터* 컨테이너용과 *AADDC 사용자* 컨테이너용인 두 기본 제공 GPO(그룹 정책 개체)가 있습니다. 이러한 GPO를 사용자 지정하여 관리되는 도메인에서 필요에 따라 그룹 정책을 구성할 수 있습니다.

1. **그룹 정책 관리** 콘솔에서 **포리스트: aaddscontoso.com** 노드를 확장합니다. 그런 다음, **도메인** 노드를 확장합니다.

    *AADDC 컴퓨터* 용 및 *AADDC 사용자* 용의 두 기본 제공 컨테이너가 있습니다. 이러한 각 컨테이너에는 기본 GPO가 적용됩니다.

    ![기본 'AADDC 컴퓨터' 및 'AADDC 사용자' 컨테이너에 적용되는 기본 제공 GPO](./media/active-directory-domain-services-admin-guide/builtin-gpos.png)

1. 이러한 기본 제공 GPO를 사용자 지정하여 관리되는 도메인의 특정 그룹 정책을 구성할 수 있습니다. *AADDC 컴퓨터 GPO* 와 같은 GPO 중 하나를 마우스 오른쪽 단추로 선택하고 **편집...** 을 선택합니다.

    ![기본 제공 GPO 중 하나를 '편집'하는 옵션 선택](./media/active-directory-domain-services-admin-guide/edit-builtin-gpo.png)

1. *계정 정책* 과 같이 GPO를 사용자 지정할 수 있는 그룹 정책 관리 편집기 도구가 열립니다.

    ![그룹 정책 관리 편집기 스크린샷](./media/active-directory-domain-services-admin-guide/gp-editor.png)

    완료되면 **파일 > 저장** 을 선택하여 정책을 저장합니다. 컴퓨터는 기본적으로 90분마다 그룹 정책을 새로 고치고 변경한 내용을 적용합니다.

## <a name="create-a-custom-group-policy-object"></a>사용자 지정 그룹 정책 개체 만들기

유사한 정책 설정을 그룹화하려면 모든 필수 설정을 단일 기본 GPO에 적용하는 대신 GPO를 추가로 만드는 경우가 많습니다. Azure AD DS를 사용하여 사용자 고유의 사용자 지정 그룹 정책 개체를 만들거나 가져온 후 사용자 지정 OU에 연결할 수 있습니다. 먼저 사용자 지정 OU를 만들어야 하는 경우 [관리되는 도메인에서 사용자 지정 OU 만들기](create-ou.md)를 참조하세요.

1. **그룹 정책 관리** 콘솔에서 *MyCustomOU* 와 같은 사용자 지정 OU(조직 구성 단위)를 선택합니다. OU를 마우스 오른쪽 단추로 클릭하고 **이 도메인에서 GPO를 만들어 여기에 연결...** 을 클릭합니다.

    ![그룹 정책 관리 콘솔에서 사용자 지정 GPO 만들기](./media/active-directory-domain-services-admin-guide/gp-create-gpo.png)

1. 새 GPO의 이름(예: *내 사용자 지정 GPO*)을 지정하고 **확인** 을 선택합니다. 필요에 따라 기존 GPO 및 정책 옵션 세트를 이 사용자 지정 GPO의 기준으로 사용할 수 있습니다.

    ![새 사용자 지정 GPO의 이름 지정](./media/active-directory-domain-services-admin-guide/gp-specify-gpo-name.png)

1. 사용자 지정 GPO가 만들어지고 사용자 지정 OU로 연결됩니다. 이제 정책 설정을 구성하려면 사용자 지정 GPO를 마우스 오른쪽 단추로 선택하고 **편집...** 을 선택합니다.

    ![사용자 지정 GPO를 '편집'하는 옵션 선택](./media/active-directory-domain-services-admin-guide/gp-gpo-created.png)

1. GPO를 사용자 지정할 수 있도록 **그룹 정책 관리 편집기** 가 열립니다.

    ![필요에 따라 설정을 구성하도록 GPO 사용자 지정](./media/active-directory-domain-services-admin-guide/gp-customize-gpo.png)

    완료되면 **파일 > 저장** 을 선택하여 정책을 저장합니다. 컴퓨터는 기본적으로 90분마다 그룹 정책을 새로 고치고 변경한 내용을 적용합니다.

## <a name="next-steps"></a>다음 단계

그룹 정책 관리 콘솔을 사용하여 구성할 수 있는 사용 가능한 그룹 정책 설정에 대한 자세한 내용은 [그룹 정책 기본 설정 항목 작업][group-policy-console]을 참조하세요.

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[create-join-windows-vm]: join-windows-vm.md
[tutorial-create-management-vm]: tutorial-create-management-vm.md
[connect-windows-server-vm]: join-windows-vm.md#connect-to-the-windows-server-vm

<!-- EXTERNAL LINKS -->
[group-policy-overview]: /previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/hh831791(v=ws.11)
[install-rsat]: /windows-server/remote/remote-server-administration-tools#BKMK_Thresh
[group-policy-console]: /previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/dn789194(v=ws.11)
