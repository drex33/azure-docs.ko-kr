---
title: '자습서: Azure Active Directory로 자동 사용자 프로비전을 위한 G Suite 구성 | Microsoft Docs'
description: 사용자 계정을 Azure AD에서 G Suite로 자동으로 프로비전 및 프로비전 해제하는 방법을 알아봅니다.
services: active-directory
author: twimmers
writer: twimmers
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/18/2021
ms.author: thwimmer
ms.openlocfilehash: fca85673ba31b71eda9e9b4242062093ae588aed
ms.sourcegitcommit: 91915e57ee9b42a76659f6ab78916ccba517e0a5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/15/2021
ms.locfileid: "130046984"
---
# <a name="tutorial-configure-g-suite-for-automatic-user-provisioning"></a>자습서: 자동 사용자 프로비전을 위한 G Suite 구성

이 자습서에서는 자동 사용자 프로비저닝을 구성하기 위해 G Suite 및 Azure AD(Azure Active Directory) 모두에서 수행해야 하는 단계에 대해 설명합니다. 구성되면 Azure AD에서 Azure AD 프로비저닝 서비스를 사용하여 사용자 및 그룹을 [G Suite](https://gsuite.google.com/)로 자동으로 프로비저닝 및 프로비저닝 해제합니다. 이 서비스의 기능, 작동 방법 및 질문과 대답에 대한 중요한 내용은 [Azure Active Directory를 사용하여 SaaS 애플리케이션의 사용자를 자동으로 프로비저닝 및 프로비저닝 해제](../app-provisioning/user-provisioning.md)를 참조하세요. 

> [!NOTE]
> 이 자습서에서는 Azure AD 사용자 프로비저닝 서비스에 기반하여 구축된 커넥터에 대해 설명합니다. 이 서비스의 기능, 작동 방법 및 질문과 대답에 대한 중요한 내용은 [Azure Active Directory를 사용하여 SaaS 애플리케이션의 사용자를 자동으로 프로비저닝 및 프로비저닝 해제](../app-provisioning/user-provisioning.md)를 참조하세요.

## <a name="capabilities-supported"></a>지원되는 기능
> [!div class="checklist"]
> * G Suite에서 사용자 만들기
> * G Suite에서 더 이상 액세스할 필요가 없는 사용자 제거
> * 사용자 특성을 Azure AD와 Clarizen One 간에 동기화된 상태로 유지
> * G Suite에서 그룹 및 그룹 멤버 자격 프로비저닝
> * G Suite에 [Single Sign-On](./google-apps-tutorial.md) 사용(추천)

## <a name="prerequisites"></a>사전 요구 사항

이 자습서에 설명된 시나리오에서는 사용자에게 이미 다음 필수 구성 요소가 있다고 가정합니다.

* [Azure AD 테넌트](../develop/quickstart-create-new-tenant.md) 
* 프로비저닝을 구성할 [권한](../roles/permissions-reference.md)이 있는 Azure AD의 사용자 계정(예: 애플리케이션 관리자, 클라우드 애플리케이션 관리자, 애플리케이션 소유자 또는 전역 관리자). 
* [G Suite 테넌트](https://gsuite.google.com/pricing.html)
* 관리자 권한이 있는 G Suite의 사용자 계정

## <a name="step-1-plan-your-provisioning-deployment"></a>1단계. 프로비저닝 배포 계획
1. [프로비저닝 서비스의 작동 방식](../app-provisioning/user-provisioning.md)에 대해 알아봅니다.
2. [프로비저닝 범위](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)에 있는 사용자를 결정합니다.
3. [Azure AD와 G Suite 간에 매핑](../app-provisioning/customize-application-attributes.md)할 데이터를 결정합니다. 

## <a name="step-2-configure-g-suite-to-support-provisioning-with-azure-ad"></a>2단계. Azure AD를 사용하여 프로비저닝을 지원하도록 G Suite 구성

Azure AD를 사용하여 자동 사용자 프로비저닝을 수행하도록 G Suite를 구성하려면 먼저 G Suite에서 SCIM 프로비저닝을 사용하도록 설정해야 합니다.

1. 관리자 계정으로 [G Suite 관리 콘솔](https://admin.google.com/)에 로그인하고, **Security(보안)** 를 선택합니다. 링크가 보이지 않으면 화면 아래쪽에 있는 **기타 컨트롤** 메뉴에 숨겨져 있을 수 있습니다.

    ![G Suite Security](./media/g-suite-provisioning-tutorial/gapps-security.png)

2. **보안** 페이지에서 **API 참조** 를 선택합니다.

    ![G Suite API](./media/g-suite-provisioning-tutorial/gapps-api.png)

3. **API 액세스 사용** 을 선택합니다.

    ![G Suite API 사용](./media/g-suite-provisioning-tutorial/gapps-api-enabled.png)

    > [!IMPORTANT]
   > G Suite에 프로비저닝하려는 모든 사용자에 대해 Azure AD의 해당 사용자 이름은 사용자 지정 도메인에 **연결되어야** 합니다. 예를 들면 bob@contoso.onmicrosoft.com과 같은 사용자 이름은 G Suite에서 허용되지 않습니다. 반면에 bob@contoso.com은 허용됩니다. [여기](../fundamentals/add-custom-domain.md)의 지침에 따라 기존 사용자의 도메인을 변경할 수 있습니다.

4. Azure AD를 사용하여 원하는 사용자 지정 도메인이 추가되고 확인되면 G Suite를 사용하여 다시 확인해야 합니다. G Suite에서 도메인을 확인하려면 다음 단계를 참조하세요.

    a. [G Suite 관리 콘솔](https://admin.google.com/)에서 **Domains(도메인)** 를 선택합니다.

    ![G Suite Domains](./media/g-suite-provisioning-tutorial/gapps-domains.png)

    b. **Add a domain or a domain alias(도메인 또는 도메인 별칭 추가)** 를 선택합니다.

    ![G Suite 도메인 추가](./media/g-suite-provisioning-tutorial/gapps-add-domain.png)

    c. **다른 도메인 추가** 를 선택하고 추가하려는 도메인 이름을 입력합니다.

    ![G Suite 다른 도메인 추가](./media/g-suite-provisioning-tutorial/gapps-add-another.png)

    d. **Continue and verify domain ownership(계속해서 도메인 소유권 확인)** 을 선택합니다. 그런 다음 도메인 이름을 소유하고 있는지 확인하는 단계를 따릅니다. Google에서 도메인을 확인하는 방법에 대한 포괄적인 지침은 [사이트 소유권 확인](https://support.google.com/webmasters/answer/35179)을 참조하세요.

    e. G Suite에 추가하려는 추가 도메인에 대해 이전 단계를 반복합니다.

5. 다음으로, G Suite에서 사용자 프로비저닝을 관리하는 데 사용하려는 관리자 계정을 결정합니다. **Admin Roles(관리자 역할)** 로 이동합니다.

    ![G Suite 관리자](./media/g-suite-provisioning-tutorial/gapps-admin.png)

6. 해당 계정의 **Admin role(관리자 역할)** 에서 해당 역할에 대한 **Privileges(권한)** 를 편집합니다. 이 계정을 프로비전에 사용할 수 있도록 모든 **관리자 API 권한** 이 사용하도록 설정되어 있는지 확인합니다.

    ![G Suite 관리자 권한](./media/g-suite-provisioning-tutorial/gapps-admin-privileges.png)

## <a name="step-3-add-g-suite-from-the-azure-ad-application-gallery"></a>3단계: Azure AD 애플리케이션 갤러리에서 G Suite 추가

Azure AD 애플리케이션 갤러리에서 G Suite를 추가하여 G Suite로 프로비저닝 관리를 시작합니다. 이전에 SSO를 G Suite에 사용하도록 설정한 경우 동일한 애플리케이션을 사용할 수 있습니다. 그러나 처음 통합을 테스트하는 경우 별도의 앱을 만드는 것이 좋습니다. [여기](../manage-apps/add-application-portal.md)를 클릭하여 갤러리에서 애플리케이션을 추가하는 방법에 대해 자세히 알아봅니다. 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>4단계. 프로비저닝 범위에 있는 사용자 정의 

Azure AD 프로비저닝 서비스를 사용하면 애플리케이션에 대한 할당 또는 사용자/그룹의 특성을 기반으로 프로비저닝되는 사용자의 범위를 지정할 수 있습니다. 할당을 기준으로 앱에 프로비저닝할 사용자의 범위를 선택하려면 다음 [단계](../manage-apps/assign-user-or-group-access-portal.md)를 사용하여 애플리케이션에 사용자 및 그룹을 할당할 수 있습니다. 사용자 또는 그룹의 특성만을 기준으로 프로비저닝할 사용자의 범위를 선택하려면 [여기](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md) 설명된 대로 범위 지정 필터를 사용할 수 있습니다. 

* 사용자 및 그룹을 G Suite에 할당할 때 **기본 액세스** 이외의 역할을 선택해야 합니다. 기본 액세스 역할이 있는 사용자는 프로비저닝에서 제외되고 프로비저닝 로그에 실질적으로 권한을 부여받지 않은 것으로 표시됩니다. 애플리케이션에서 사용할 수 있는 유일한 역할이 기본 액세스 역할인 경우에는 [애플리케이션 매니페스트를 업데이트](../develop/howto-add-app-roles-in-azure-ad-apps.md)하여 역할을 더 추가할 수 있습니다. 

* 소규모로 시작합니다. 모든 사용자에게 배포하기 전에 소수의 사용자 및 그룹 집합으로 테스트합니다. 할당된 사용자 및 그룹으로 프로비저닝 범위가 설정된 경우 앱에 하나 또는 두 개의 사용자 또는 그룹을 할당하여 범위를 제어할 수 있습니다. 모든 사용자 및 그룹으로 범위가 설정된 경우 [특성 기반 범위 지정 필터](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)를 지정할 수 있습니다. 


## <a name="step-5-configure-automatic-user-provisioning-to-g-suite"></a>5단계. G Suite에 대한 자동 사용자 프로비저닝 구성 

이 섹션에서는 Azure AD의 사용자 및/또는 그룹 할당에 따라 TestApp에서 사용자 및/또는 그룹을 만들고, 업데이트하고, 사용 해제하도록 Azure AD 프로비저닝 서비스를 구성하는 단계를 안내합니다.

> [!NOTE]
> G Suite 디렉터리 API 엔드포인트에 대한 자세한 내용은 [디렉터리 API 참조 설명서](https://developers.google.com/admin-sdk/directory)를 참조하세요.

### <a name="to-configure-automatic-user-provisioning-for-g-suite-in-azure-ad"></a>Azure AD에서 G Suite에 대한 자동 사용자 프로비저닝을 구성하려면 다음을 수행합니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다. **엔터프라이즈 애플리케이션**, **모든 애플리케이션** 을 차례로 선택합니다. 사용자가 `portal.azure.com`에 로그인해야 하며, `aad.portal.azure.com`은 사용할 수 없습니다.

    ![엔터프라이즈 애플리케이션 블레이드](./media/g-suite-provisioning-tutorial/enterprise-applications.png)

    ![모든 애플리케이션 블레이드](./media/g-suite-provisioning-tutorial/all-applications.png)

2. 애플리케이션 목록에서 **G Suite** 를 선택합니다.

    ![애플리케이션 목록의 G Suite 링크](common/all-applications.png)

3. **프로비전** 탭을 선택합니다. **시작** 을 클릭합니다.

    ![프로비저닝 옵션이 호출된 관리 옵션의 스크린샷](common/provisioning.png)

      ![시작 블레이드](./media/g-suite-provisioning-tutorial/get-started.png)

4. **프로비전 모드** 를 **자동** 으로 설정합니다.

    ![자동 옵션이 호출된 프로비저닝 모드 드롭다운 목록의 스크린샷](common/provisioning-automatic.png)

5. **관리자 자격 증명** 섹션 아래에서 **권한 부여** 를 클릭합니다. 새 브라우저 창에서 Google 권한 부여 대화 상자로 리디렉션됩니다.

      ![G Suite 권한 부여](./media/g-suite-provisioning-tutorial/authorize-1.png)

6. G Suite 테넌트를 변경할 수 있는 Azure AD 권한을 부여할지 확인합니다. **수락** 을 선택합니다.

     ![G Suite 테넌트 권한 부여](./media/g-suite-provisioning-tutorial/gapps-auth.png)

7. Azure Portal에서 **연결 테스트** 를 클릭하여 Azure AD에서 G Suite에 연결할 수 있는지 확인합니다. 연결이 실패하면 G Suite 계정에 관리자 권한이 있는지 확인하고 다시 시도합니다. 그런 다음 **권한 부여** 단계를 다시 시도합니다.

6. **알림 이메일** 필드에 프로비저닝 오류 알림을 받을 개인 또는 그룹의 메일 주소를 입력하고, **오류가 발생할 경우 메일 알림 보내기** 확인란을 선택합니다.

    ![알림 이메일](common/provisioning-notification-email.png)

7. **저장** 을 선택합니다.

8. **매핑** 섹션에서 **Azure Active Directory 사용자 프로비전** 을 선택합니다.

9. **특성 매핑** 섹션에서 Azure AD에서 G Suite로 동기화되는 사용자 특성을 검토합니다. **일치** 속성으로 선택한 특성은 업데이트 작업을 위해 G Suite의 사용자 계정을 일치시키는 데 사용됩니다. [일치하는 대상 특성](../app-provisioning/customize-application-attributes.md)을 변경하도록 선택하는 경우 G Suite API에서 해당 특성에 따라 사용자를 필터링하도록 지원하는지 확인해야 합니다. **저장** 단추를 선택하여 변경 내용을 커밋합니다.

   |attribute|Type|
   |---|---|
   |primaryEmail|String|
   |relations.[type eq "manager"].value|String|
   |name.familyName|String|
   |name.givenName|String|
   |일시 중단됨|String|
   |externalIds.[type eq "custom"].value|String|
   |externalIds.[type eq "organization"].value|String|
   |addresses.[type eq "work"].country|String|
   |addresses.[type eq "work"].streetAddress|String|
   |addresses.[type eq "work"].region|String|
   |addresses.[type eq "work"].locality|String|
   |addresses.[type eq "work"].postalCode|String|
   |emails.[type eq "work"].address|String|
   |organizations.[type eq "work"].department|String|
   |organizations.[type eq "work"].title|String|
   |phoneNumbers.[type eq "work"].value|String|
   |phoneNumbers.[type eq "mobile"].value|String|
   |phoneNumbers.[type eq "work_fax"].value|String|
   |emails.[type eq "work"].address|String|
   |organizations.[type eq "work"].department|String|
   |organizations.[type eq "work"].title|String|
   |phoneNumbers.[type eq "work"].value|String|
   |phoneNumbers.[type eq "mobile"].value|String|
   |phoneNumbers.[type eq "work_fax"].value|String|
   |addresses.[type eq "home"].country|String|
   |addresses.[type eq "home"].formatted|String|
   |addresses.[type eq "home"].locality|String|
   |addresses.[type eq "home"].postalCode|String|
   |addresses.[type eq "home"].region|String|
   |addresses.[type eq "home"].streetAddress|String|
   |addresses.[type eq "other"].country|String|
   |addresses.[type eq "other"].formatted|String|
   |addresses.[type eq "other"].locality|String|
   |addresses.[type eq "other"].postalCode|String|
   |addresses.[type eq "other"].region|String|
   |addresses.[type eq "other"].streetAddress|String|
   |addresses.[type eq "work"].formatted|String|
   |changePasswordAtNextLogin|String|
   |emails.[type eq "home"].address|String|
   |emails.[type eq "other"].address|String|
   |externalIds.[type eq "account"].value|String|
   |externalIds.[type eq "custom"].customType|String|
   |externalIds.[type eq "customer"].value|String|
   |externalIds.[type eq "login_id"].value|String|
   |externalIds.[type eq "network"].value|String|
   |gender.type|String|
   |GeneratedImmutableId|String|
   |ID|String|
   |ims.[type eq "home"].protocol|String|
   |ims.[type eq "other"].protocol|String|
   |ims.[type eq "work"].protocol|String|
   |includeInGlobalAddressList|String|
   |ipWhitelisted|String|
   |organizations.[type eq "school"].costCenter|String|
   |organizations.[type eq "school"].department|String|
   |organizations.[type eq "school"].domain|String|
   |organizations.[type eq "school"].fullTimeEquivalent|String|
   |organizations.[type eq "school"].location|String|
   |organizations.[type eq "school"].name|String|
   |organizations.[type eq "school"].symbol|String|
   |organizations.[type eq "school"].title|String|
   |organizations.[type eq "work"].costCenter|String|
   |organizations.[type eq "work"].domain|String|
   |organizations.[type eq "work"].fullTimeEquivalent|String|
   |organizations.[type eq "work"].location|String|
   |organizations.[type eq "work"].name|String|
   |organizations.[type eq "work"].symbol|String|
   |OrgUnitPath|String|
   |phoneNumbers.[type eq "home"].value|String|
   |phoneNumbers.[type eq "other"].value|String|
   |websites.[type eq "home"].value|String|
   |websites.[type eq "other"].value|String|
   |websites.[type eq "work"].value|String|
   

10. **매핑** 섹션 아래에서 **Azure Active Directory 그룹 프로비저닝** 을 선택합니다.

11. **특성 매핑** 섹션에서 Azure AD에서 G Suite로 동기화되는 그룹 특성을 검토합니다. **일치** 속성으로 선택한 특성은 업데이트 작업을 위해 G Suite의 그룹을 일치시키는 데 사용됩니다. **저장** 단추를 선택하여 변경 내용을 커밋합니다.

      |attribute|Type|
      |---|---|
      |이메일|String|
      |멤버|String|
      |name|String|
      |description|String|

12. 범위 지정 필터를 구성하려면 [범위 지정 필터 자습서](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)에서 제공하는 다음 지침을 참조합니다.

13. Azure AD 프로비저닝 서비스를 G Suite에 사용하도록 설정하려면 **설정** 섹션에서 **프로비저닝 상태** 를 **켜기** 로 변경합니다.

    ![프로비전 상태 켜기로 전환](common/provisioning-toggle-on.png)

14. **설정** 의 **범위** 섹션에서 원하는 값을 선택하여 G Suite에 프로비저닝하려는 사용자 및/또는 그룹을 정의합니다.

    ![프로비전 범위](common/provisioning-scope.png)

15. 프로비전할 준비가 되면 **저장** 을 클릭합니다.

    ![프로비전 구성 저장](common/provisioning-configuration-save.png)

이 작업은 **설정** 의 **범위** 섹션에 정의된 모든 사용자 및/또는 그룹의 초기 동기화 주기를 시작합니다. 초기 주기는 Azure AD 프로비저닝 서비스가 실행되는 동안 약 40분마다 발생하는 후속 주기보다 더 많은 시간이 걸립니다.

> [!NOTE]
> 사용자에게 이미 Azure AD 사용자의 이메일 주소를 사용하는 기존 개인/소비자 계정이 있는 경우 디렉터리 동기화를 수행하기 전에 Google Transfer Tool을 사용하여 해결할 수 있는 몇 가지 문제가 발생할 수 있습니다.

## <a name="step-6-monitor-your-deployment"></a>6단계. 배포 모니터링
프로비저닝을 구성한 후에는 다음 리소스를 사용하여 배포를 모니터링합니다.

1. [프로비저닝 로그](../reports-monitoring/concept-provisioning-logs.md)를 사용하여 어떤 사용자가 성공적으로 프로비저닝되었는지 확인합니다.
2. [진행률 표시줄](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md)을 통해 프로비저닝 주기 상태와 완료 정도를 확인합니다.
3. 프로비저닝 구성이 비정상 상태로 보이면 애플리케이션이 격리됩니다. 격리 상태에 대한 자세한 내용은 [여기](../app-provisioning/application-provisioning-quarantine-status.md)를 참조하세요.  

## <a name="change-log"></a>로그 변경

* 2020년 10월 17일 - 추가 G Suite 사용자 및 그룹 특성에 대한 지원이 추가되었습니다.
* 2020년 10월 17일 - [여기](https://developers.google.com/admin-sdk/directory)에 정의된 것과 일치하도록 G Suite 대상 특성 이름이 업데이트되었습니다.
* 2020년 10월 17일 - 기본 특성 매핑이 업데이트되었습니다.
* 2021/03/18 - 이제 모든 새 사용자에 대한 ID 대신 관리자 이메일이 동기화됩니다. 관리자를 ID로 프로비저닝된 기존 사용자의 경우 "full" 범위를 가진 [Microsoft Graph](/graph/api/synchronization-synchronizationjob-restart?preserve-view=true&tabs=http&view=graph-rest-beta)를 통해 다시 시작하여 이메일이 프로비저닝되도록 할 수 있습니다. 이 변경 내용은 GSuite 프로비저닝 작업에만 영향을 주며 Goov2OutDelta로 시작하는 이전 프로비저닝 작업에는 영향을 주지 않습니다. 관리자 이메일은 사용자를 처음 만들 때 또는 관리자가 변경될 때 프로비저닝됩니다. 관리자가 이메일 주소를 변경하면 관리자 이메일이 프로비저닝되지 않습니다. 

## <a name="additional-resources"></a>추가 리소스

* [엔터프라이즈 앱에 대한 사용자 계정 프로비전 관리](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Azure Active Directory로 애플리케이션 액세스 및 Single Sign-On을 구현하는 방법](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>다음 단계

* [프로비저닝 작업에 대한 로그를 검토하고 보고서를 받아보는 방법을 알아봅니다](../app-provisioning/check-status-user-account-provisioning.md).