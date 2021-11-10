---
title: '자습서: Azure Active Directory를 사용하여 자동 사용자 프로비저닝을 수행하도록 Code42 구성 | Microsoft Docs'
description: 사용자 계정을 Azure AD에서 Code42로 자동으로 프로비저닝 및 프로비저닝을 해제하는 방법을 알아봅니다.
services: active-directory
documentationcenter: ''
author: twimmers
writer: twimmers
manager: beatrizd
ms.assetid: ddcb950b-3f9a-4ebb-bf78-4ec42d16d52d
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 09/23/2020
ms.author: thwimmer
ms.openlocfilehash: c9af2f7123d37823e0acb0a2e244cc0ce40dbc68
ms.sourcegitcommit: 5af89a2a7b38b266cc3adc389d3a9606420215a9
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/08/2021
ms.locfileid: "131988410"
---
# <a name="tutorial-configure-code42-for-automatic-user-provisioning"></a>자습서: 자동 사용자 프로비저닝을 수행하도록 Code42 구성

이 자습서에서는 자동 사용자 프로비저닝을 구성하기 위해 Code42 및 Azure AD(Azure Active Directory) 모두에서 수행해야 하는 단계에 대해 설명합니다. 구성되면 Azure AD에서 Azure AD 프로비저닝 서비스를 사용하여 사용자 및 그룹을 [Code42](https://www.crashplan.com/)로 자동으로 프로비저닝 및 프로비저닝을 해제합니다. 이 서비스의 기능, 작동 방법 및 질문과 대답에 대한 중요한 내용은 [Azure Active Directory를 사용하여 SaaS 애플리케이션의 사용자를 자동으로 프로비저닝 및 프로비저닝 해제](../app-provisioning/user-provisioning.md)를 참조하세요.


## <a name="capabilities-supported"></a>지원되는 기능
> [!div class="checklist"]
> * Code42에서 사용자 만들기
> * Code42에서 더 이상 액세스할 필요가 없는 사용자 제거
> * 사용자 특성을 Azure AD와 Code42 간에 동기화된 상태로 유지
> * Code42에서 그룹 및 그룹 멤버 자격 프로비저닝
> * Code42에 [Single Sign-On](./code42-tutorial.md) 사용(추천)

## <a name="prerequisites"></a>사전 요구 사항

이 자습서에 설명된 시나리오에서는 사용자에게 이미 다음 필수 구성 요소가 있다고 가정합니다.

* [Azure AD 테넌트](../develop/quickstart-create-new-tenant.md)
* 프로비저닝을 구성할 [권한](../roles/permissions-reference.md)이 있는 Azure AD의 사용자 계정(예: 애플리케이션 관리자, 클라우드 애플리케이션 관리자, 애플리케이션 소유자 또는 전역 관리자).
* ID 관리를 사용하도록 설정된 Code42 테넌트
* [고객 클라우드 관리자](https://support.code42.com/Administrator/Cloud/Monitoring_and_managing/Roles_reference#Customer_Cloud_Admin) 권한이 있는 Code42 사용자 계정

> [!NOTE]
> 이 통합은 Azure AD 미국 정부 클라우드 환경에서도 사용할 수 있습니다. 이 애플리케이션은 Azure AD 미국 정부 클라우드 애플리케이션 갤러리에서 찾을 수 있으며 퍼블릭 클라우드에서와 동일한 방법으로 구성할 수 있습니다.

## <a name="step-1-plan-your-provisioning-deployment"></a>1단계. 프로비저닝 배포 계획
1. [프로비저닝 서비스의 작동 방식](../app-provisioning/user-provisioning.md)에 대해 알아봅니다.
2. [프로비저닝 범위](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)에 있는 사용자를 결정합니다.
3. [Azure AD와 Code42 간에 매핑](../app-provisioning/customize-application-attributes.md)할 데이터를 결정합니다.

## <a name="step-2-configure-code42-to-support-provisioning-with-azure-ad"></a>2단계. Azure AD를 사용하여 프로비저닝을 지원하도록 Code42 구성

이 섹션에서는 Code42 콘솔의 Identity Management(ID 관리) 섹션에서 Azure AD를 프로비저닝 공급자로 구성하는 단계를 안내합니다. 이렇게 하면 Code42에서 Azure AD로부터 프로비저닝 요청을 안전하게 받을 수 있습니다. Azure AD를 사용하여 프로비저닝하기 전에 [Code42의 지원 설명서](https://support.code42.com/Administrator/Cloud/Configuring/Introduction_to_SCIM_provisioning/How_to_provision_users_to_Code42_from_Azure_AD)를 검토하는 것이 좋습니다.

### <a name="to-create-a-provisioning-provider-in-code42s-console"></a>Code42의 콘솔에서 프로비저닝 공급자를 만들려면 다음을 수행합니다.

1. Code42 콘솔에 로그인합니다. **Administration(관리)** 을 선택하여 탐색 메뉴를 펼칩니다. **Settings(설정)** , **Identity Management(ID 관리)** 를 차례로 선택합니다.
2. **프로비전** 탭을 선택합니다. 그런 다음, **Add provisioning provider(프로비저닝 공급자 추가)** 메뉴를 펼치고, **Add SCIM provider(SCIM 공급자 추가)** 를 선택합니다.
3. **Display name(표시 이름)** 필드에서 프로비저닝 공급자에 대한 고유한 이름을 입력합니다. **Authentication credential type(인증 자격 증명 유형)** 을 **OAuth token(OAuth 토큰)** 으로 설정합니다. **Next(다음)** 를 선택하여 자격 증명을 생성합니다.

> [!NOTE]
>* 다음 단계에서 필요한 **기준 URL** 및 **토큰** 을 요구하는 메시지가 표시될 때까지 이 창을 열어 둡니다.
>* 또는 나중에 참조할 수 있도록 이 정보를 임시 위치에 복사합니다.

## <a name="step-3-add-code42-from-the-azure-ad-application-gallery"></a>3단계: Azure AD 애플리케이션 갤러리에서 Code42 추가

Azure AD 애플리케이션 갤러리에서 Code42를 추가하여 Code42로 프로비저닝 관리를 시작합니다. 이전에 SSO를 Code42에 사용하도록 설정한 경우 동일한 애플리케이션을 사용할 수 있습니다. 그러나 처음 통합을 테스트하는 경우 별도의 앱을 만드는 것이 좋습니다. [여기](../manage-apps/add-application-portal.md)를 클릭하여 갤러리에서 애플리케이션을 추가하는 방법에 대해 자세히 알아봅니다.

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>4단계. 프로비저닝 범위에 있는 사용자 정의

Azure AD 프로비저닝 서비스를 사용하면 애플리케이션에 대한 할당 또는 사용자/그룹의 특성을 기반으로 프로비저닝되는 사용자의 범위를 지정할 수 있습니다. 할당을 기준으로 앱에 프로비저닝할 사용자의 범위를 선택하려면 다음 [단계](../manage-apps/assign-user-or-group-access-portal.md)를 사용하여 애플리케이션에 사용자 및 그룹을 할당할 수 있습니다. 사용자 또는 그룹의 특성만을 기준으로 프로비저닝할 사용자의 범위를 선택하려면 [여기](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md) 설명된 대로 범위 지정 필터를 사용할 수 있습니다.

* 사용자 및 그룹을 Code42에 할당할 때 **기본 액세스** 이외의 역할을 선택해야 합니다. 기본 액세스 역할이 있는 사용자는 프로비저닝에서 제외되고 프로비저닝 로그에 실질적으로 권한을 부여받지 않은 것으로 표시됩니다. 애플리케이션에서 사용할 수 있는 유일한 역할이 기본 액세스 역할인 경우에는 [애플리케이션 매니페스트를 업데이트](../develop/howto-add-app-roles-in-azure-ad-apps.md)하여 역할을 더 추가할 수 있습니다.

* 소규모로 시작합니다. 모든 사용자에게 배포하기 전에 소수의 사용자 및 그룹 집합으로 테스트합니다. 할당된 사용자 및 그룹으로 프로비저닝 범위가 설정된 경우 앱에 하나 또는 두 개의 사용자 또는 그룹을 할당하여 범위를 제어할 수 있습니다. 모든 사용자 및 그룹으로 범위가 설정된 경우 [특성 기반 범위 지정 필터](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)를 지정할 수 있습니다.


## <a name="step-5-configure-automatic-user-provisioning-to-code42"></a>5단계. Code42에 대한 자동 사용자 프로비저닝 구성

이 섹션에서는 Azure AD의 사용자 및/또는 그룹 할당에 따라 TestApp에서 사용자 및/또는 그룹을 만들고, 업데이트하고, 사용 해제하도록 Azure AD 프로비저닝 서비스를 구성하는 단계를 안내합니다.

### <a name="to-configure-automatic-user-provisioning-for-code42-in-azure-ad"></a>Azure AD에서 Code42에 대한 자동 사용자 프로비저닝을 구성하려면 다음을 수행합니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다. **엔터프라이즈 애플리케이션**, **모든 애플리케이션** 을 차례로 선택합니다.

    ![엔터프라이즈 애플리케이션 블레이드](common/enterprise-applications.png)

2. 애플리케이션 목록에서 **Code42** 를 선택합니다.

    ![애플리케이션 목록의 Code42 링크](common/all-applications.png)

3. **프로비전** 탭을 선택합니다.

    ![프로비저닝 탭](common/provisioning.png)

4. **프로비전 모드** 를 **자동** 으로 설정합니다.

    ![프로비저닝 탭 자동](common/provisioning-automatic.png)

5. **관리자 자격 증명** 섹션 아래에서 이전에 Code42에서 검색한 **SCIM 2.0 base URL(SCIM 2.0 기준 URL) 및 Access Token(액세스 토큰)** 값을 각각 **테넌트 URL** 및 **비밀 토큰** 에 입력합니다. **연결 테스트** 를 클릭하여 Azure AD에서 Code42에 연결할 수 있는지 확인합니다. 연결이 실패하면 Code42 계정에 관리자 권한이 있는지 확인하고 다시 시도합니다.

    ![토큰](common/provisioning-testconnection-tenanturltoken.png)

6. **알림 이메일** 필드에 프로비저닝 오류 알림을 받을 개인 또는 그룹의 메일 주소를 입력하고, **오류가 발생할 경우 메일 알림 보내기** 확인란을 선택합니다.

    ![알림 이메일](common/provisioning-notification-email.png)

7. **저장** 을 선택합니다.

8. **매핑** 섹션 아래에서 **Azure Active Directory 사용자를 Code42에 동기화** 를 선택합니다.

9. **특성 매핑** 섹션에서 Azure AD에서 Code42로 동기화되는 사용자 특성을 검토합니다. **일치** 속성으로 선택한 특성은 업데이트 작업을 위해 Code42의 사용자 계정을 일치시키는 데 사용됩니다. [일치하는 대상 특성](../app-provisioning/customize-application-attributes.md)을 변경하도록 선택하는 경우 Code42 API에서 해당 특성에 따라 사용자를 필터링하도록 지원하는지 확인해야 합니다. **저장** 단추를 선택하여 변경 내용을 커밋합니다.

   |attribute|Type|
   |---|---|
   |userName|String|
   |활성|부울|
   |title|String|
   |emails[type eq "work"].value|String|
   |name.givenName|String|
   |name.familyName|String|
   |addresses[type eq "work"].locality|String|
   |addresses[type eq "work"].region|String|
   |addresses[type eq "work"].country|String|
   |externalId|String|
   |userType|String|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:department|String|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:division|String|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:manager|참조|

10. **매핑** 섹션 아래에서 **Azure Active Directory 그룹을 Code42에 동기화** 를 선택합니다.

11. **특성 매핑** 섹션에서 Azure AD에서 Code42로 동기화되는 그룹 특성을 검토합니다. **일치** 속성으로 선택한 특성은 업데이트 작업을 위해 Code42의 그룹을 일치시키는 데 사용됩니다. **저장** 단추를 선택하여 변경 내용을 커밋합니다.

      |attribute|Type|
      |---|---|
      |displayName|String|
      |externalId|String|
      |members|참조|

12. 범위 지정 필터를 구성하려면 [범위 지정 필터 자습서](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)에서 제공하는 다음 지침을 참조합니다.

13. Azure AD 프로비저닝 서비스를 Code42에 사용하도록 설정하려면 **설정** 섹션에서 **프로비전 상태** 를 **켜기** 로 변경합니다.

    ![프로비전 상태 켜기로 전환](common/provisioning-toggle-on.png)

14. **설정** 의 **범위** 섹션에서 원하는 값을 선택하여 Code42에 프로비저닝하려는 사용자 및/또는 그룹을 정의합니다.

    ![프로비전 범위](common/provisioning-scope.png)

15. 프로비전할 준비가 되면 **저장** 을 클릭합니다.

    ![프로비전 구성 저장](common/provisioning-configuration-save.png)

이 작업은 **설정** 의 **범위** 섹션에 정의된 모든 사용자 및/또는 그룹의 초기 동기화 주기를 시작합니다. 초기 주기는 Azure AD 프로비저닝 서비스가 실행되는 동안 약 40분마다 발생하는 후속 주기보다 더 많은 시간이 걸립니다.

## <a name="step-6-monitor-your-deployment"></a>6단계. 배포 모니터링
프로비저닝을 구성한 후에는 다음 리소스를 사용하여 배포를 모니터링합니다.

1. [프로비저닝 로그](../reports-monitoring/concept-provisioning-logs.md)를 사용하여 어떤 사용자가 성공적으로 프로비저닝되었는지 확인합니다.
2. [진행률 표시줄](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md)을 통해 프로비저닝 주기 상태와 완료 정도를 확인합니다.
3. 프로비저닝 구성이 비정상 상태로 보이면 애플리케이션이 격리됩니다. 격리 상태에 대한 자세한 내용은 [여기](../app-provisioning/application-provisioning-quarantine-status.md)를 참조하세요.

## <a name="additional-resources"></a>추가 리소스

* [엔터프라이즈 앱에 대한 사용자 계정 프로비전 관리](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Azure Active Directory로 애플리케이션 액세스 및 Single Sign-On을 구현하는 방법](../manage-apps/what-is-single-sign-on.md)
* [Code42에서 SCIM 그룹을 기반으로 하여 조직 할당 구성](https://support.code42.com/Administrator/Cloud/Configuring/Introduction_to_SCIM_provisioning/How_to_provision_users_to_Code42_from_Azure_AD#Step_6:_Choose_an_organization_mapping_method)
* [Code42에서 SCIM 그룹을 기반으로 하여 역할 할당 구성](https://support.code42.com/Administrator/Cloud/Configuring/Introduction_to_SCIM_provisioning/How_to_provision_users_to_Code42_from_Azure_AD#Step_7:_Configure_role_mapping)

## <a name="next-steps"></a>다음 단계

* [프로비저닝 작업에 대한 로그를 검토하고 보고서를 받아보는 방법을 알아봅니다](../app-provisioning/check-status-user-account-provisioning.md).