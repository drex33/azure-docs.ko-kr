---
title: '자습서: Azure Active Directory로 자동 사용자 프로비전을 위한 Workplace by Facebook 구성 | Microsoft Docs'
description: 자동 사용자 프로비저닝을 구성하기 위해 Workplace by Facebook 및 Azure AD(Azure Active Directory)에서 수행해야 하는 단계를 알아봅니다.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 07/22/2021
ms.author: jeedes
ms.openlocfilehash: a927232bab058ff461ff1158739a6b4d390e1c56
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128679468"
---
# <a name="tutorial-configure-workplace-by-facebook-for-automatic-user-provisioning"></a>자습서: 사용자 프로비전을 위한 Workplace by Facebook 구성

이 자습서에서는 자동 사용자 프로비저닝을 구성하기 위해 Workplace by Facebook 및 Azure AD(Azure Active Directory)에서 수행해야 하는 단계를 설명합니다. 구성된 경우 Azure AD는 Azure AD 프로비저닝 서비스를 사용하여 사용자를 [Workplace by Facebook](https://work.workplace.com/)에 자동으로 프로비저닝 및 프로비저닝 해제합니다. 이 서비스의 기능, 작동 방법 및 질문과 대답에 대한 중요한 내용은 [Azure Active Directory를 사용하여 SaaS 애플리케이션의 사용자를 자동으로 프로비저닝 및 프로비저닝 해제](../app-provisioning/user-provisioning.md)를 참조하세요.

## <a name="capabilities-supported"></a>지원되는 기능
> [!div class="checklist"]
> * Workplace by Facebook에서 사용자 만들기
> * Workplace by Facebook에서 더 이상 액세스할 필요가 없는 사용자 제거
> * Azure AD와 Workplace by Facebook 간에 사용자 특성을 동기화된 상태로 유지
> * Workplace by Facebook에 대한 [Single Sign-On](./workplacebyfacebook-tutorial.md)(권장)

>[!VIDEO https://www.youtube.com/embed/oF7I0jjCfrY]

## <a name="prerequisites"></a>사전 요구 사항

이 자습서에 설명된 시나리오에서는 사용자에게 이미 다음 필수 구성 요소가 있다고 가정합니다.

* [Azure AD 테넌트](../develop/quickstart-create-new-tenant.md) 
* 프로비저닝을 구성할 [권한](../roles/permissions-reference.md)이 있는 Azure AD의 사용자 계정(예: 애플리케이션 관리자, 클라우드 애플리케이션 관리자, 애플리케이션 소유자 또는 전역 관리자)
* Workplace by Facebook Single Sign-On이 설정된 구독

> [!NOTE]
> 이 자습서의 단계를 테스트하기 위해 프로덕션 환경을 사용하는 것은 바람직하지 않습니다.

> [!NOTE]
> 이 통합은 Azure AD 미국 정부 클라우드 환경에서도 사용할 수 있습니다. 이 애플리케이션은 Azure AD 미국 정부 클라우드 애플리케이션 갤러리에서 찾을 수 있으며 퍼블릭 클라우드에서와 동일한 방법으로 구성할 수 있습니다.

이 자습서의 단계를 테스트하려면 다음 권장 사항을 준수해야 합니다.

- 꼭 필요한 경우가 아니면 프로덕션 환경을 사용하지 마세요.
- Azure AD 평가판 환경이 없으면 [여기](https://azure.microsoft.com/pricing/free-trial/)에서 1개월 평가판을 얻을 수 있습니다.

## <a name="step-1-plan-your-provisioning-deployment"></a>1단계. 프로비저닝 배포 계획
1. [프로비저닝 서비스의 작동 방식](../app-provisioning/user-provisioning.md)에 대해 알아봅니다.
2. [프로비저닝 범위](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)에 있는 사용자를 결정합니다.
3. [Azure AD와 Workplace by Facebook 간에 매핑](../app-provisioning/customize-application-attributes.md)할 데이터를 결정합니다.

## <a name="step-2-configure-workplace-by-facebook-to-support-provisioning-with-azure-ad"></a>2단계. Azure AD에서 프로비저닝을 지원하도록 Workplace by Facebook 구성

프로비전 서비스를 구성하고 사용하도록 설정하기 전에 Workplace by Facebook 앱에 액세스해야 하는 사용자를 나타내는 Azure AD의 사용자를 결정해야 합니다. 결정했으면 다음 지시에 따라 이러한 사용자를 Workplace by Facebook 앱에 할당할 수 있습니다.

*   단일 Azure AD 사용자를 Workplace by Facebook에 할당하여 프로비전 구성을 테스트하는 것이 좋습니다. 추가 사용자를 나중에 할당할 수 있습니다.

*   Workplace by Facebook에 사용자를 할당할 때 유효한 사용자 역할을 선택해야 합니다. "기본 액세스" 역할은 프로비전에 작동하지 않습니다.

## <a name="step-3-add-workplace-by-facebook-from-the-azure-ad-application-gallery"></a>3단계: Azure AD 애플리케이션 갤러리에서 Workplace by Facebook 추가

Azure AD 애플리케이션 갤러리에서 Workplace by Facebook을 추가하여 Workplace by Facebook에 대한 프로비저닝 관리를 시작합니다. 이전에 SSO에 대해 Workplace by Facebook을 설정한 경우 동일한 애플리케이션을 사용할 수 있습니다. 그러나 처음 통합을 테스트하는 경우 별도의 앱을 만드는 것이 좋습니다. [여기](../manage-apps/add-application-portal.md)를 클릭하여 갤러리에서 애플리케이션을 추가하는 방법에 대해 자세히 알아봅니다.

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>4단계. 프로비저닝 범위에 있는 사용자 정의 

Azure AD 프로비저닝 서비스를 사용하면 애플리케이션에 대한 할당 또는 사용자/그룹의 특성을 기반으로 프로비저닝되는 사용자의 범위를 지정할 수 있습니다. 할당을 기준으로 앱에 프로비저닝할 사용자의 범위를 선택하려면 다음 [단계](../manage-apps/assign-user-or-group-access-portal.md)를 사용하여 애플리케이션에 사용자를 할당할 수 있습니다. 사용자 또는 그룹의 특성만을 기준으로 프로비저닝할 사용자의 범위를 선택하려면 [여기](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md) 설명된 대로 범위 지정 필터를 사용할 수 있습니다. 

* 사용자를 Workplace by Facebook에 할당하는 경우 **기본 액세스** 이외의 역할을 선택해야 합니다. 기본 액세스 역할이 있는 사용자는 프로비저닝에서 제외되고 프로비저닝 로그에 실질적으로 권한을 부여받지 않은 것으로 표시됩니다. 애플리케이션에서 사용할 수 있는 유일한 역할이 기본 액세스 역할인 경우에는 [애플리케이션 매니페스트를 업데이트](../develop/howto-add-app-roles-in-azure-ad-apps.md)하여 역할을 더 추가할 수 있습니다. 

* 소규모로 시작합니다. 모든 사용자에게 배포하기 전에 소수의 사용자 집합으로 테스트합니다. 할당된 사용자로 프로비저닝 범위가 설정된 경우 앱에 하나 또는 두 개의 사용자를 할당하여 범위를 제어할 수 있습니다. 모든 사용자 및 그룹으로 범위가 설정된 경우 [특성 기반 범위 지정 필터](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)를 지정할 수 있습니다. 

## <a name="step-5-configure-automatic-user-provisioning-to-workplace-by-facebook"></a>5단계. Workplace by Facebook에 대한 자동 사용자 프로비저닝 구성
이 섹션에서는 Azure AD의 사용자 할당에 따라 Workplace by Facebook 앱에서 사용자를 만들고, 업데이트하고, 사용 해제하도록 Azure AD 프로비저닝 서비스를 구성하는 단계를 안내합니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다. **엔터프라이즈 애플리케이션**, **모든 애플리케이션** 을 차례로 선택합니다.

    ![엔터프라이즈 애플리케이션 블레이드](common/enterprise-applications.png)

2. 애플리케이션 목록에서 **Workplace by Facebook** 을 선택합니다.

    ![애플리케이션 목록의 Workplace by Facebook 링크](common/all-applications.png)

3. **프로비전** 탭을 선택합니다.

    ![프로비저닝 옵션이 호출된 관리 옵션의 스크린샷](common/provisioning.png)

4. **프로비전 모드** 를 **자동** 으로 설정합니다.

    ![자동 옵션이 호출된 프로비저닝 모드 드롭다운 목록의 스크린샷](common/provisioning-automatic.png)

5. **관리자 자격 증명** 섹션 아래에서 **권한 부여** 를 클릭합니다. Workplace by Facebook의 권한 부여 페이지로 리디렉션됩니다. Workplace by Facebook 사용자 이름을 입력하고 **계속** 단추를 클릭합니다. **연결 테스트** 를 클릭하여 Azure AD가 Workplace by Facebook에 연결할 수 있는지 확인합니다. 연결에 실패하면 Workplace by Facebook 계정에 관리자 권한이 있는지 확인하고 다시 시도합니다.

    ![스크린샷은 권한 부여 옵션이 있는 관리자 자격 증명 대화 상자를 보여 줍니다.](./media/workplacebyfacebook-provisioning-tutorial/provisioning.png)

    ![권한 부여](./media/workplacebyfacebook-provisioning-tutorial/workplacelogin.png)

6. **알림 이메일** 필드에 프로비저닝 오류 알림을 받을 개인 또는 그룹의 메일 주소를 입력하고, **오류가 발생할 경우 메일 알림 보내기** 확인란을 선택합니다.

    ![알림 이메일](common/provisioning-notification-email.png)

7. **저장** 을 선택합니다.

8. **매핑** 섹션에서 **Azure Active Directory 사용자를 Workplace by Facebook에 동기화** 를 선택합니다.

9. **특성-매핑** 섹션에서 Azure AD에서 Workplace by Facebook으로 동기화되는 사용자 특성을 검토합니다. **일치** 속성으로 선택한 특성은 업데이트 작업 시 Workplace by Facebook의 사용자 계정을 일치시키는 데 사용됩니다. [일치하는 대상 특성](../app-provisioning/customize-application-attributes.md)을 변경하는 경우 Workplace by Facebook API에서 해당 특성에 따라 사용자 필터링을 지원하는지 확인해야 합니다. **저장** 단추를 선택하여 변경 내용을 커밋합니다.

   |attribute|Type|
   |---|---|
   |userName|String|
   |displayName|String|
   |활성|부울|
   |title|부울|
   |emails[type eq "work"].value|String|
   |name.givenName|String|
   |name.familyName|String|
   |name.formatted|String|
   |addresses[type eq "work"].formatted|String|
   |addresses[type eq "work"].streetAddress|String|
   |addresses[type eq "work"].locality|String|
   |addresses[type eq "work"].region|String|
   |addresses[type eq "work"].country|String|
   |addresses[type eq "work"].postalCode|String|
   |addresses[type eq "other"].formatted|String|
   |phoneNumbers[type eq "work"].value|String|
   |phoneNumbers[type eq "mobile"].value|String|
   |phoneNumbers[type eq "fax"].value|String|
   |externalId|String|
   |preferredLanguage|String|
   |urn:scim:schemas:extension:enterprise:1.0.manager|String|
   |urn:scim:schemas:extension:enterprise:1.0.department|String|
   |urn:scim:schemas:extension:enterprise:1.0.division|String|
   |urn:scim:schemas:extension:enterprise:1.0.organization|String|
   |urn:scim:schemas:extension:enterprise:1.0.costCenter|String|
   |urn:scim:schemas:extension:enterprise:1.0.employeeNumber|String|
   |urn:scim:schemas:extension:facebook:auth_method:1.0:auth_method|String|
   |urn:scim:schemas:extension:facebook:frontline:1.0.is_frontline|부울|
   |urn:scim:schemas:extension:facebook:starttermdates:1.0.startDate|정수|


10. 범위 지정 필터를 구성하려면 [범위 지정 필터 자습서](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)에서 제공하는 다음 지침을 참조합니다.

11. Workplace by Facebook에 대한 Azure AD 프로비저닝 서비스를 사용하도록 설정하려면 **설정** 섹션에서 **프로비전 상태** 를 **켜기** 로 변경합니다.

    ![프로비전 상태 켜기로 전환](common/provisioning-toggle-on.png)

12. **설정** 섹션의 **범위** 에서 원하는 값을 선택하여 Workplace by Facebook에 프로전하려는 사용자를 정의합니다.

    ![프로비전 범위](common/provisioning-scope.png)

13. 프로비전할 준비가 되면 **저장** 을 클릭합니다.

    ![프로비전 구성 저장](common/provisioning-configuration-save.png)

이 작업은 **설정** 섹션의 **범위** 에 정의된 모든 사용자의 초기 동기화 주기를 시작합니다. 초기 주기는 Azure AD 프로비저닝 서비스가 실행되는 동안 약 40분마다 발생하는 후속 주기보다 더 많은 시간이 걸립니다. 

## <a name="step-6-monitor-your-deployment"></a>6단계. 배포 모니터링
프로비저닝을 구성한 후에는 다음 리소스를 사용하여 배포를 모니터링합니다.

1. [프로비저닝 로그](../reports-monitoring/concept-provisioning-logs.md)를 사용하여 어떤 사용자가 성공적으로 프로비저닝되었는지 확인합니다.
2. [진행률 표시줄](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md)을 통해 프로비저닝 주기 상태와 완료 정도를 확인합니다.
3. 프로비저닝 구성이 비정상 상태로 보이면 애플리케이션이 격리됩니다. 격리 상태에 대한 자세한 내용은 [여기](../app-provisioning/application-provisioning-quarantine-status.md)를 참조하세요.

## <a name="troubleshooting-tips"></a>문제 해결 팁
*  사용자가 성공적으로 생성되지 않은 것을 확인하고 코드가 "1789003"인 감사 로그 이벤트가 있는 경우 사용자가 확인되지 않은 도메인에서 온 것임을 의미합니다.
*  사용자에게 '오류: 메일 필드 없음' 오류가 발생하는 경우가 있습니다. Facebook에서 반환된 메일 오류인 'HTTP 요청을 처리하는 동안 예외가 발생했습니다.'를 제공해야 합니다. 자세한 내용은 이 예외의 'Response' 속성에서 반환된 HTTP 응답을 참조하세요. 이 작업을 0번 다시 시도했습니다. 이 날짜 이후에 다시 시도됩니다. 이 오류는 userPrincipalName이 아닌 고객이 메일을 Facebook 메일로 매핑하지만 일부 사용자에게 메일 특성이 없기 때문에 발생합니다. 오류를 방지하고 Facebook에서 Workplace로 실패한 사용자를 프로비저닝하려면 Facebook 메일 특성에서 Workplace로의 특성 매핑을 Coalesce([mail],[userPrincipalName])로 수정하거나 Facebook에서 Workplace의 사용자를 할당 취소하거나 해당 사용자에 대한 메일 주소를 프로비저닝합니다.  
*  작업 공간에는 [이메일 주소가 없는 사용자](https://www.workplace.com/resources/tech/account-management/email-less#enable)가 있을 수 있는 옵션이 있습니다. 작업 공간 측에서 이 설정을 전환하는 경우 이메일을 사용하지 않는 사용자가 작업 공간에서 성공적으로 생성되려면 Azure 쪽에서 프로비전을 다시 시작해야 합니다.  


## <a name="change-log"></a>로그 변경

* 2020/09/10 - 엔터프라이즈 특성 "division", "organization", "costCenter" 및 "employeeNumber"에 대한 지원이 추가되었습니다. 사용자 지정 특성 "startDate", "auth_method" 및 "frontline"에 대한 지원이 추가되었습니다.
* 07/22/2021 - 메일을 Facebook 메일에 매핑하지만 일부 사용자에게 메일 특성이 없는 고객을 위한 문제 해결 팁을 업데이트했습니다.

## <a name="additional-resources"></a>추가 리소스

* [엔터프라이즈 앱에 대한 사용자 계정 프로비전 관리](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Azure Active Directory로 애플리케이션 액세스 및 Single Sign-On을 구현하는 방법](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>다음 단계

* [프로비저닝 작업에 대한 로그를 검토하고 보고서를 받아보는 방법을 알아봅니다](../app-provisioning/check-status-user-account-provisioning.md).
