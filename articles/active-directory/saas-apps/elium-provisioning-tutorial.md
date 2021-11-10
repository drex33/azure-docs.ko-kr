---
title: '자습서: Azure Active Directory를 사용하여 자동으로 사용자를 프로비저닝하도록 Elium 구성 | Microsoft Docs'
description: 사용자 계정을 Elium에 자동으로 프로비저닝 및 프로비저닝을 해제하도록 Azure Active Directory를 구성하는 방법을 알아봅니다.
services: active-directory
author: twimmers
writer: twimmers
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 08/19/2019
ms.author: thwimmer
ms.openlocfilehash: fd99228da135e8a0a2eb2c1413243537efe7efc8
ms.sourcegitcommit: 5af89a2a7b38b266cc3adc389d3a9606420215a9
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/08/2021
ms.locfileid: "131990428"
---
# <a name="tutorial-configure-elium-for-automatic-user-provisioning"></a>자습서: 자동으로 사용자를 프로비저닝하도록 Elium 구성

이 자습서에서는 사용자 또는 그룹을 Elium에 자동으로 프로비저닝 및 프로비저닝을 해제하도록 Azure AD(Azure Active Directory)를 구성하는 방법을 보여줍니다.

> [!NOTE]
> 이 자습서에서는 Azure AD 사용자 프로비저닝 서비스를 기반으로 구축된 커넥터에 대해 설명합니다. 이 서비스의 기능 및 작동 방법에 대한 중요한 내용과 FAQ는 [Azure Active Directory를 사용하여 SaaS 애플리케이션으로 사용자 프로비저닝 및 프로비저닝 해제 자동화](../app-provisioning/user-provisioning.md)를 참조하세요.
>
> 이 커넥터는 현재 미리 보기로 제공되고 있습니다. 미리 보기로 제공되는 Azure 기능의 일반 사용 약관은 [Microsoft Azure 미리 보기에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

## <a name="prerequisites"></a>필수 조건

이 자습서에서는 다음과 같은 사전 요구 사항이 충족되었다고 가정합니다.

* Azure AD 테넌트
* [Elium 테넌트](https://www.elium.com/pricing/)
* 관리자 권한이 있는 Elium의 사용자 계정

> [!NOTE]
> 이 통합은 Azure AD 미국 정부 클라우드 환경에서도 사용할 수 있습니다. 이 애플리케이션은 Azure AD 미국 정부 클라우드 애플리케이션 갤러리에서 찾을 수 있으며 퍼블릭 클라우드에서와 동일한 방법으로 구성할 수 있습니다.

## <a name="assigning-users-to-elium"></a>Elium에 사용자 할당

Azure AD는 *할당* 이라는 개념을 사용하여 선택한 앱에 대한 액세스 권한을 어떤 사용자에게 부여할 것인지 결정합니다. 자동 사용자 프로비저닝의 컨텍스트에서 Azure AD의 애플리케이션에 할당된 사용자 및 그룹만 동기화됩니다.

자동 사용자 프로비저닝을 구성하고 사용하도록 설정하려면 먼저 Elium에 액세스해야 하는 Azure AD의 사용자 및 그룹을 결정합니다. 그런 다음, [엔터프라이즈 앱에 사용자 또는 그룹 할당](../manage-apps/assign-user-or-group-access-portal.md)의 단계에 따라 해당 사용자 및 그룹을 Elium에 할당합니다.

## <a name="important-tips-for-assigning-users-to-elium"></a>사용자를 Elium에 할당하기 위한 주요 팁 

단일 Azure AD 사용자를 Elium에 할당하여 자동 사용자 프로비저닝 구성을 테스트하는 것이 좋습니다. 추가 사용자 또는 그룹은 나중에 할당할 수 있습니다.

사용자를 Elium에 할당할 때 할당 대화 상자에서 유효한 애플리케이션 관련 역할(사용 가능한 경우)을 선택해야 합니다. **기본 액세스** 역할이 있는 사용자는 프로비저닝에서 제외됩니다.

## <a name="set-up-elium-for-provisioning"></a>프로비저닝을 수행하도록 Elium 설정

Azure AD를 사용하여 자동 사용자 프로비저닝을 수행하도록 Elium을 구성하려면 먼저 Elium에서 SCIM(System for Cross-domain Identity Management) 프로비저닝을 사용하도록 설정해야 합니다. 다음 단계를 수행합니다.

1. Elium에 로그인하고 **내 프로필** > **설정** 으로 이동합니다.

    ![Elium의 설정 메뉴 항목](media/Elium-provisioning-tutorial/setting.png)

1. 왼쪽 아래 모서리에 있는 **고급** 에서 **보안** 을 선택합니다.

    ![Elium의 보안 링크](media/Elium-provisioning-tutorial/security.png)

1. **테넌트 URL** 및 **비밀 토큰** 값을 복사합니다. 이러한 값은 나중에 Azure Portal에서 Elium 애플리케이션의 **프로비저닝** 탭에 있는 해당 필드에 사용됩니다.

    ![Elium의 테넌트 URL 및 비밀 토큰 필드](media/Elium-provisioning-tutorial/token.png)

## <a name="add-elium-from-the-gallery"></a>갤러리에서 Elium 추가

Azure AD를 사용하여 자동 사용자 프로비저닝을 수행하도록 Elium을 구성하려면 Azure AD 애플리케이션 갤러리의 Elium을 관리형 SaaS 애플리케이션 목록에 추가해야 합니다. 다음 단계를 수행합니다.

1. [Azure Portal](https://portal.azure.com) 의 왼쪽 탐색 패널에서 **Azure Active Directory** 를 선택합니다.

    ![Azure Active Directory 메뉴 항목](common/select-azuread.png)

1. **엔터프라이즈 애플리케이션** 으로 이동한 다음, **모든 애플리케이션** 을 선택합니다.

     ![Azure AD 엔터프라이즈 애플리케이션 블레이드](common/enterprise-applications.png)

1. 새 애플리케이션을 추가하려면 창 위쪽에서 **새 애플리케이션** 을 선택합니다.

    ![새 애플리케이션 링크](common/add-new-app.png)

1. 검색 상자에 **Elium** 을 입력하고, 결과 패널에서 **Elium** 을 선택한 다음, **추가** 단추를 클릭하여 애플리케이션을 추가합니다.

    ![갤러리 검색 상자](common/search-new-app.png)

## <a name="configure-automatic-user-provisioning-to-elium"></a>Elium에 대한 자동 사용자 프로비저닝 구성

이 섹션에서는 Azure AD의 사용자 및 그룹 할당에 따라 Elium에서 사용자 및 그룹을 만들고, 업데이트하고, 사용 해제하도록 Azure AD 프로비저닝 서비스를 구성하는 단계를 안내합니다.

> [!TIP]
> [Elium Single Sign-On 자습서](Elium-tutorial.md)의 지침에 따라 SAML(Security Assertion Markup Language)을 기반으로 Elium에 Single Sign-On를 사용하도록 선택할 수도 있습니다. Single Sign-On은 자동 사용자 프로비저닝과 별개로 구성할 수 있지만, 두 기능은 상호 보완적입니다.

Azure AD에서 Elium에 대한 자동 사용자 프로비저닝을 구성하려면 다음 단계를 수행합니다.

1. [Azure Portal](https://portal.azure.com)에 로그인하고 **엔터프라이즈 애플리케이션**, **모든 애플리케이션** 을 차례로 선택합니다.

    ![Azure AD 엔터프라이즈 애플리케이션 블레이드](common/enterprise-applications.png)

1. 애플리케이션 목록에서 **Elium** 을 선택합니다.

    ![엔터프라이즈 애플리케이션 블레이드의 애플리케이션 목록](common/all-applications.png)

1. **프로비전** 탭을 선택합니다.

    ![엔터프라이즈 애플리케이션 블레이드의 프로비저닝 탭](common/provisioning.png)

1. **프로비전 모드** 를 **자동** 으로 설정합니다.

    ![프로비저닝 모드의 자동 설정](common/provisioning-automatic.png)

1. **관리자 자격 증명** 섹션에서 **테넌트 URL** 필드에 **\<tenantURL\>/scim/v2** 를 입력합니다. (**tenantURL** 은 이전에 Elium 관리 콘솔에서 검색한 값입니다.) 또한 **비밀 토큰** 필드에 Elium **비밀 토큰** 값을 입력합니다. 마지막으로 **연결 테스트** 를 선택하여 Azure AD에서 Elium에 연결할 수 있는지 확인합니다. 연결이 실패하면 Elium 계정에 관리자 권한이 있는지 확인하고 다시 시도합니다.

    ![자격 증명의 테넌트 URL 및 비밀 토큰 필드](common/provisioning-testconnection-tenanturltoken.png)

1. **알림 이메일** 필드에서 프로비저닝 오류 알림을 받을 사용자 또는 그룹의 이메일 주소를 입력합니다. 그런 다음, **오류가 발생할 경우 이메일 알림 보내기** 확인란을 선택합니다.

    ![알림 이메일](common/provisioning-notification-email.png)

1. **저장** 을 클릭합니다.

1. **매핑** 섹션에서 **Azure Active Directory 사용자를 Elium에 동기화** 를 선택합니다.

    ![Azure AD 사용자를 Elium에 매핑하기 위한 동기화 링크](media/Elium-provisioning-tutorial/usermapping.png)

1. **특성 매핑** 섹션에서, Azure AD에서 Elium으로 동기화되는 사용자 특성을 검토합니다. **일치** 속성으로 선택한 특성은 업데이트 작업 시 Elium의 사용자 계정을 일치시키는 데 사용됩니다. 변경 내용을 커밋하려면 **저장** 을 선택합니다.

    ![Azure AD와 Elium 간의 특성 매핑](media/Elium-provisioning-tutorial/userattribute.png)

1. 범위 지정 필터를 구성하려면 [범위 지정 필터 자습서](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)의 지침을 따릅니다.

1. Elium에 Azure AD 프로비저닝 서비스를 사용하도록 설정하려면 **설정** 섹션에서 **프로비전 상태** 를 **켜기** 로 변경합니다.

    ![[켜기]로 설정된 프로비전 상태](common/provisioning-toggle-on.png)

1. **설정** 섹션의 **범위** 드롭다운 목록 상자에서 원하는 값을 선택하여 Elium에 프로비저닝할 사용자 및 그룹을 정의합니다.

    ![프로비저닝 범위 목록 상자](common/provisioning-scope.png)

1. 프로비저닝할 준비가 되면 **저장** 을 선택합니다.

    ![프로비저닝 구성 저장 단추](common/provisioning-configuration-save.png)

이 작업은 **설정** 섹션의 **범위** 에 정의된 모든 사용자 및 그룹의 첫 번째 동기화를 시작합니다. 두 번째 동기화부터는 첫 번째 동기화보다 시간이 적게 걸립니다. 프로비저닝에 필요한 시간에 대한 자세한 내용은 [사용자를 프로비저닝하는 데 걸리는 시간은 어느 정도인가요?](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md#how-long-will-it-take-to-provision-users)를 참조하세요.

**현재 상태** 섹션을 사용하여 진행 상황을 모니터링하고, 프로비저닝 작업 보고서에 대한 링크를 따라 이동할 수 있습니다. 프로비저닝 작업 보고서는 Elium에서 Azure AD 프로비저닝 서비스가 수행한 모든 작업을 설명합니다. 자세한 내용은 [사용자 프로비저닝 상태 확인](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md)을 참조하세요. Azure AD 프로비저닝 로그를 읽으려면 [자동 사용자 계정 프로비저닝에 대한 보고](../app-provisioning/check-status-user-account-provisioning.md)를 참조하세요.

## <a name="additional-resources"></a>추가 리소스

* [Enterprise 앱에 대한 사용자 계정 프로비저닝 관리](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Azure Active Directory로 애플리케이션 액세스 및 Single Sign-On을 구현하는 방법](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>다음 단계

* [프로비저닝 작업에 대한 로그를 검토하고 보고서를 받아보는 방법을 알아봅니다](../app-provisioning/check-status-user-account-provisioning.md).
