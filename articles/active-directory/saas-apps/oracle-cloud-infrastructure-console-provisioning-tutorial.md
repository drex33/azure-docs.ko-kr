---
title: '자습서: Azure Active Directory를 사용하여 자동 사용자 프로비저닝을 수행하도록 Oracle Cloud Infrastructure Console 구성 | Microsoft Docs'
description: 사용자 계정을 Azure AD에서 Oracle Cloud Infrastructure Console로 자동으로 프로비저닝 및 프로비저닝 해제하는 방법을 알아봅니다.
services: active-directory
author: twimmers
writer: twimmers
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 05/16/2021
ms.author: thwimmer
ms.openlocfilehash: 748e32a929e9681bb75a8d627eb9de11807c7c41
ms.sourcegitcommit: e6de87b42dc320a3a2939bf1249020e5508cba94
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/27/2021
ms.locfileid: "114709972"
---
# <a name="tutorial-configure-oracle-cloud-infrastructure-console-for-automatic-user-provisioning"></a>자습서: 자동 사용자 프로비저닝을 수행하도록 Oracle Cloud Infrastructure Console 구성
> [!NOTE]
> 사용자 지정 / BYOA 애플리케이션과 Oracle Cloud Infrastructure Console 또는 Oracle IDCS와의 통합은 지원되지 않습니다. 이 자습서에 설명된 대로 갤러리 애플리케이션을 사용하는 것이 지원됩니다. 갤러리 애플리케이션은 Oracle SCIM 서버에서 작동하도록 사용자 지정되었습니다. 

이 자습서에서는 자동 사용자 프로비저닝을 구성하기 위해 Oracle Cloud Infrastructure Console 및 Azure AD(Azure Active Directory) 모두에서 수행해야 하는 단계에 대해 설명합니다. 구성되면 Azure AD에서 Azure AD 프로비저닝 서비스를 사용하여 사용자 및 그룹을 [Oracle Cloud Infrastructure Console](https://www.oracle.com/cloud/free/?source=:ow:o:p:nav:0916BCButton&intcmp=:ow:o:p:nav:0916BCButton)로 자동으로 프로비저닝 및 프로비저닝 해제합니다. 이 서비스의 기능, 작동 방법 및 질문과 대답에 대한 중요한 내용은 [Azure Active Directory를 사용하여 SaaS 애플리케이션의 사용자를 자동으로 프로비저닝 및 프로비저닝 해제](../app-provisioning/user-provisioning.md)를 참조하세요. 


## <a name="capabilities-supported"></a>지원되는 기능
> [!div class="checklist"]
> * Oracle Cloud Infrastructure Console에서 사용자 만들기
> * Oracle Cloud Infrastructure Console에서 더 이상 액세스할 필요가 없는 사용자 제거
> * 사용자 특성을 Azure AD와 Oracle Cloud Infrastructure Console 간에 동기화된 상태로 유지
> * Oracle Cloud Infrastructure Console에서 그룹 및 그룹 멤버 자격 프로비저닝
> * Oracle Cloud Infrastructure Console에 [Single Sign-On](./oracle-cloud-tutorial.md) 사용(추천)

## <a name="prerequisites"></a>사전 요구 사항

이 자습서에 설명된 시나리오에서는 사용자에게 이미 다음 필수 구성 요소가 있다고 가정합니다.

* [Azure AD 테넌트](../develop/quickstart-create-new-tenant.md) 
* 프로비저닝을 구성할 [권한](../roles/permissions-reference.md)이 있는 Azure AD의 사용자 계정(예: 애플리케이션 관리자, 클라우드 애플리케이션 관리자, 애플리케이션 소유자 또는 전역 관리자). 
* Oracle Cloud Infrastructure Console [테넌트](https://www.oracle.com/cloud/sign-in.html?intcmp=OcomFreeTier&source=:ow:o:p:nav:0916BCButton).
* 관리자 권한이 있는 Oracle Cloud Infrastructure Console의 사용자 계정.

## <a name="step-1-plan-your-provisioning-deployment"></a>1단계. 프로비저닝 배포 계획
1. [프로비저닝 서비스의 작동 방식](../app-provisioning/user-provisioning.md)에 대해 알아봅니다.
2. [프로비저닝 범위](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)에 있는 사용자를 결정합니다.
3. [Azure AD와 Oracle Cloud Infrastructure Console 간에 매핑](../app-provisioning/customize-application-attributes.md)할 데이터를 결정합니다. 

## <a name="step-2-configure-oracle-cloud-infrastructure-console-to-support-provisioning-with-azure-ad"></a>2단계. Azure AD를 사용하여 프로비저닝을 지원하도록 Oracle Cloud Infrastructure Console 구성

1. Oracle Cloud Infrastructure Console의 관리 포털에 로그인합니다. 화면의 왼쪽 위 모서리에서 **Identity > Federation(페더레이션)** 으로 차례로 이동합니다.

    ![Oracle 관리](./media/oracle-cloud-infratstructure-console-provisioning-tutorial/identity.png)

2. 페이지에서 Oracle Identity Cloud Service Console 옆에 표시된 URL을 클릭합니다.

    ![Oracle URL](./media/oracle-cloud-infratstructure-console-provisioning-tutorial/url.png)

3. **Add Identity Provider(ID 공급자 추가)** 를 클릭하여 새 ID 공급자를 만듭니다. 테넌트 URL의 일부로 사용할 IdP ID를 저장합니다. **Applications(애플리케이션)** 탭 옆에 있는 더하기 아이콘을 클릭하여 OAuth Client 및 Grant IDCS Identity Domain Administrator AppRole을 만듭니다.

    ![Oracle 클라우드 아이콘](./media/oracle-cloud-infratstructure-console-provisioning-tutorial/add.png)

4. 아래 스크린샷에 따라 애플리케이션을 구성합니다. 구성이 완료되면 **Save(저장)** 를 클릭합니다.

    ![Oracle 구성](./media/oracle-cloud-infratstructure-console-provisioning-tutorial/configuration.png)

    ![Oracle Token Issuance Policy(Oracle 토큰 발급 정책)](./media/oracle-cloud-infratstructure-console-provisioning-tutorial/token-issuance.png)

5. 애플리케이션의 구성 탭 아래에서 **General Information(일반 정보)** 옵션을 펼쳐 클라이언트 ID 및 클라이언트 암호를 검색합니다.

    ![Oracle 토큰 생성](./media/oracle-cloud-infratstructure-console-provisioning-tutorial/general-information.png)

6. 비밀 토큰을 생성하려면 클라이언트 ID와 클라이언트 암호를 **클라이언트 ID:클라이언트 암호** 형식으로 Base64로 인코딩합니다. 참고 - 이 값은 줄 바꿈을 사용하지 않도록 설정하고 생성해야 합니다(base64 -w 0). 비밀 토큰을 저장합니다. 이 값은 Azure Portal에 있는 Oracle Cloud Infrastructure Console 애플리케이션에 대한 [프로비저닝] 탭의 **비밀 토큰** 필드에 입력됩니다.

## <a name="step-3-add-oracle-cloud-infrastructure-console-from-the-azure-ad-application-gallery"></a>3단계: Azure AD 애플리케이션 갤러리에서 Oracle Cloud Infrastructure Console 추가

Azure AD 애플리케이션 갤러리에서 Oracle Cloud Infrastructure Console을 추가하여 Oracle Cloud Infrastructure Console로 프로비저닝 관리를 시작합니다. 이전에 SSO를 Oracle Cloud Infrastructure Console에 사용하도록 설정한 경우 동일한 애플리케이션을 사용할 수 있습니다. 그러나 처음 통합을 테스트하는 경우 별도의 앱을 만드는 것이 좋습니다. [여기](../manage-apps/add-application-portal.md)를 클릭하여 갤러리에서 애플리케이션을 추가하는 방법에 대해 자세히 알아봅니다. 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>4단계. 프로비저닝 범위에 있는 사용자 정의 

Azure AD 프로비저닝 서비스를 사용하면 애플리케이션에 대한 할당 또는 사용자/그룹의 특성을 기반으로 프로비저닝되는 사용자의 범위를 지정할 수 있습니다. 할당을 기준으로 앱에 프로비저닝할 사용자의 범위를 선택하려면 다음 [단계](../manage-apps/assign-user-or-group-access-portal.md)를 사용하여 애플리케이션에 사용자 및 그룹을 할당할 수 있습니다. 사용자 또는 그룹의 특성만을 기준으로 프로비저닝할 사용자의 범위를 선택하려면 [여기](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md) 설명된 대로 범위 지정 필터를 사용할 수 있습니다. 

* 사용자 및 그룹을 Oracle Cloud Infrastructure Console에 할당할 때 **기본 액세스** 이외의 역할을 선택해야 합니다. 기본 액세스 역할이 있는 사용자는 프로비저닝에서 제외되고 프로비저닝 로그에 실질적으로 권한을 부여받지 않은 것으로 표시됩니다. 애플리케이션에서 사용할 수 있는 유일한 역할이 기본 액세스 역할인 경우에는 [애플리케이션 매니페스트를 업데이트](../develop/howto-add-app-roles-in-azure-ad-apps.md)하여 역할을 더 추가할 수 있습니다. 

* 소규모로 시작합니다. 모든 사용자에게 배포하기 전에 소수의 사용자 및 그룹 집합으로 테스트합니다. 할당된 사용자 및 그룹으로 프로비저닝 범위가 설정된 경우 앱에 하나 또는 두 개의 사용자 또는 그룹을 할당하여 범위를 제어할 수 있습니다. 모든 사용자 및 그룹으로 범위가 설정된 경우 [특성 기반 범위 지정 필터](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)를 지정할 수 있습니다. 


## <a name="step-5-configure-automatic-user-provisioning-to-oracle-cloud-infrastructure-console"></a>5단계. Oracle Cloud Infrastructure Console에 대한 자동 사용자 프로비저닝 구성 

이 섹션에서는 Azure AD의 사용자 및/또는 그룹 할당에 따라 TestApp에서 사용자 및/또는 그룹을 만들고, 업데이트하고, 사용 해제하도록 Azure AD 프로비저닝 서비스를 구성하는 단계를 안내합니다.

### <a name="to-configure-automatic-user-provisioning-for-oracle-cloud-infrastructure-console-in-azure-ad"></a>Azure AD에서 Oracle Cloud Infrastructure Console에 대한 자동 사용자 프로비저닝을 구성하려면 다음을 수행합니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다. **엔터프라이즈 애플리케이션**, **모든 애플리케이션** 을 차례로 선택합니다.

    ![엔터프라이즈 애플리케이션 블레이드](common/enterprise-applications.png)

2. 애플리케이션 목록에서 **Oracle Cloud Infrastructure Console** 을 선택합니다.

    ![애플리케이션 목록의 Oracle Cloud Infrastructure Console 링크](common/all-applications.png)

3. **프로비전** 탭을 선택합니다.

    ![프로비저닝 옵션이 호출된 관리 옵션의 스크린샷](common/provisioning.png)

4. **프로비전 모드** 를 **자동** 으로 설정합니다.

    ![자동 옵션이 호출된 프로비저닝 모드 드롭다운 목록의 스크린샷](common/provisioning-automatic.png)

5. **관리자 자격 증명** 섹션 아래에서 **테넌트 URL** 을 `https://<IdP ID>.identity.oraclecloud.com/admin/v1` 형식으로 입력합니다. 예: `https://idcs-0bfd023ff2xx4a98a760fa2c31k92b1d.identity.oraclecloud.com/admin/v1`. **비밀 토큰** 에 이전에 검색된 비밀 토큰 값을 입력합니다. **연결 테스트** 를 클릭하여 Azure AD에서 Oracle Cloud Infrastructure Console에 연결할 수 있는지 확인합니다. 연결이 실패하면 Oracle Cloud Infrastructure Console 계정에 관리자 권한이 있는지 확인하고 다시 시도합니다.

    ![테넌트 URL 및 비밀 토큰을 입력할 수 있는 관리자 자격 증명 대화 상자를 보여 주는 스크린샷](./media/oracle-cloud-infratstructure-console-provisioning-tutorial/provisioning.png)

6. **알림 이메일** 필드에 프로비저닝 오류 알림을 받을 개인 또는 그룹의 메일 주소를 입력하고, **오류가 발생할 경우 메일 알림 보내기** 확인란을 선택합니다.

    ![알림 이메일](common/provisioning-notification-email.png)

7. **저장** 을 선택합니다.

8. **매핑** 섹션 아래에서 **Azure Active Directory 사용자를 Oracle Cloud Infrastructure Console에 동기화** 를 선택합니다.

9. **특성 매핑** 섹션에서 Azure AD에서 Oracle Cloud Infrastructure Console로 동기화되는 사용자 특성을 검토합니다. **일치** 속성으로 선택한 특성은 업데이트 작업을 위해 Oracle Cloud Infrastructure Console의 사용자 계정을 일치시키는 데 사용됩니다. [일치하는 대상 특성](../app-provisioning/customize-application-attributes.md)을 변경하도록 선택하는 경우 Oracle Cloud Infrastructure Console API에서 해당 특성에 따라 사용자를 필터링하도록 지원하는지 확인해야 합니다. **저장** 단추를 선택하여 변경 내용을 커밋합니다.

    |attribute|Type|
    |---|---|
    |displayName|String|
    |userName|String|
    |활성|부울|
    |title|String|
    |emails[type eq "work"].value|String|
    |preferredLanguage|String|
    |name.givenName|String|
    |name.familyName|String|
    |addresses[type eq "work"].formatted|String|
    |addresses[type eq "work"].locality|String|
    |addresses[type eq "work"].region|String|
    |addresses[type eq "work"].postalCode|String|
    |addresses[type eq "work"].country|String|
    |addresses[type eq "work"].streetAddress|String|
    |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:employeeNumber|String|
    |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:department|String|
    |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:costCenter|String|
    |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:division|String|
    |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:manager|참조|
    |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:organization|String|
    |urn:ietf:params:scim:schemas:oracle:idcs:extension:user:User:bypassNotification|부울|
    |urn:ietf:params:scim:schemas:oracle:idcs:extension:user:User:isFederatedUser|부울|

10. **매핑** 섹션 아래에서 **Azure Active Directory 그룹을 Oracle Cloud Infrastructure Console에 동기화** 를 선택합니다.

11. **특성 매핑** 섹션에서 Azure AD에서 Oracle Cloud Infrastructure Console로 동기화되는 그룹 특성을 검토합니다. **일치** 속성으로 선택한 특성은 업데이트 작업을 위해 Oracle Cloud Infrastructure Console의 그룹을 일치시키는 데 사용됩니다. **저장** 단추를 선택하여 변경 내용을 커밋합니다.

    | attribute | Type |
    |--|--|
    | displayName | String |
    | externalId | String |
    | members | 참조 |

12. 범위 지정 필터를 구성하려면 [범위 지정 필터 자습서](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)에서 제공하는 다음 지침을 참조합니다.

13. Azure AD 프로비저닝 서비스를 Oracle Cloud Infrastructure Console에 사용하도록 설정하려면 **설정** 섹션에서 **프로비저닝 상태** 를 **켜기** 로 변경합니다.

    ![프로비전 상태 켜기로 전환](common/provisioning-toggle-on.png)

14. **설정** 의 **범위** 섹션에서 원하는 값을 선택하여 Oracle Cloud Infrastructure Console에 프로비저닝하려는 사용자 및/또는 그룹을 정의합니다.

    ![프로비전 범위](common/provisioning-scope.png)

15. 프로비전할 준비가 되면 **저장** 을 클릭합니다.

    ![프로비전 구성 저장](common/provisioning-configuration-save.png)

이 작업은 **설정** 의 **범위** 섹션에 정의된 모든 사용자 및/또는 그룹의 초기 동기화 주기를 시작합니다. 초기 주기는 Azure AD 프로비저닝 서비스가 실행되는 동안 약 40분마다 발생하는 후속 주기보다 더 많은 시간이 걸립니다. 

## <a name="step-6-monitor-your-deployment"></a>6단계. 배포 모니터링
프로비저닝을 구성한 후에는 다음 리소스를 사용하여 배포를 모니터링합니다.

* [프로비저닝 로그](../reports-monitoring/concept-provisioning-logs.md)를 사용하여 어떤 사용자가 성공적으로 프로비저닝되었는지 확인합니다.
* [진행률 표시줄](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md)을 통해 프로비저닝 주기 상태와 완료 정도를 확인합니다.
* 프로비저닝 구성이 비정상 상태로 보이면 애플리케이션이 격리됩니다. 격리 상태에 대한 자세한 내용은 [여기](../app-provisioning/application-provisioning-quarantine-status.md)를 참조하세요.

## <a name="additional-resources"></a>추가 리소스

* [엔터프라이즈 앱에 대한 사용자 계정 프로비전 관리](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Azure Active Directory로 애플리케이션 액세스 및 Single Sign-On을 구현하는 방법](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>다음 단계

* [프로비저닝 작업에 대한 로그를 검토하고 보고서를 받아보는 방법을 알아봅니다](../app-provisioning/check-status-user-account-provisioning.md).
