---
title: '자습서: Azure Active Directory로 자동 사용자 프로비저닝을 위한 SchoolStream ASA 구성 | Microsoft Docs'
description: 사용자 계정을 Azure AD에서 SchoolStream ASA로 자동으로 프로비전 및 프로비전 해제하는 방법을 알아봅니다.
services: active-directory
author: twimmers
writer: twimmers
manager: beatrizd
ms.assetid: ac594768-7b76-4e5a-b46e-8f1cb41f2754
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 08/27/2021
ms.author: thwimmer
ms.openlocfilehash: 3f3b6fa4a1dcd87371fe2c75de2dc89b60d92eb1
ms.sourcegitcommit: f2d0e1e91a6c345858d3c21b387b15e3b1fa8b4c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/07/2021
ms.locfileid: "123544781"
---
# <a name="tutorial-configure-schoolstream-asa-for-automatic-user-provisioning-in-schoolstream-asa"></a>자습서: SchoolStream ASA에서 자동 사용자 프로비저닝을 위한 SchoolStream ASA 구성

이 자습서에서는 자동 사용자 프로비저닝을 구성하기 위해 SchoolStream ASA 및 Azure AD(Azure Active Directory)에서 수행해야 하는 단계를 설명합니다. 구성된 경우 Azure AD는 Azure AD 프로비저닝 서비스를 사용하여 사용자 및 그룹을 [SchoolStream ASA](https://www.ssk12.com/)로 자동으로 프로비전 및 프로비전 해제합니다. 이 서비스의 기능, 작동 방법 및 질문과 대답에 대한 중요한 내용은 [Azure Active Directory를 사용하여 SaaS 애플리케이션의 사용자를 자동으로 프로비저닝 및 프로비저닝 해제](../app-provisioning/user-provisioning.md)를 참조하세요. 


## <a name="capabilities-supported"></a>지원되는 기능
> [!div class="checklist"]
> * SchoolStream ASA에서 사용자 만들기 
> * SchoolStream ASA에서 더 이상 액세스할 필요가 없는 사용자 제거
> * Azure AD와 SchoolStream ASA 간에 사용자 특성을 동기화된 상태로 유지
> * SchoolStream ASA에 대한 [Single Sign-On](../manage-apps/add-application-portal-setup-oidc-sso.md)(권장)


## <a name="prerequisites"></a>필수 구성 요소

이 자습서에 설명된 시나리오에서는 사용자에게 이미 다음 필수 구성 요소가 있다고 가정합니다.

* [Azure AD 테넌트](../develop/quickstart-create-new-tenant.md) 
* 프로비저닝을 구성할 [권한](../roles/permissions-reference.md)이 있는 Azure AD의 사용자 계정(예: 애플리케이션 관리자, 클라우드 애플리케이션 관리자, 애플리케이션 소유자 또는 전역 관리자). 
* SchoolStream 웹 사이트. 없는 경우 [SchoolStream 지원](mailto:support@rtresponse.com)에 문의하세요.

## <a name="step-1-plan-your-provisioning-deployment"></a>1단계. 프로비저닝 배포 계획
1. [프로비저닝 서비스의 작동 방식](../app-provisioning/user-provisioning.md)에 대해 알아봅니다.
1. [프로비저닝 범위](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)에 있는 사용자를 결정합니다.
1. [Azure AD와 SchoolStream ASA 간에 매핑](../app-provisioning/customize-application-attributes.md)할 데이터를 결정합니다. 

## <a name="step-2-configure-schoolstream-asa-to-support-provisioning-with-azure-ad"></a>2단계. Azure AD에서 프로비전을 지원하도록 SchoolStream ASA 구성

1. [SchoolStream 지원](mailto:support@rtresponse.com)에 문의하여 SchoolStream ASA 통합을 요청합니다. 이 경우 **Azure AD 테넌트 ID** 와 **SchoolStream 웹 사이트 URL** 을 제공해야 합니다.

1. SchoolStream에서 SchoolStream 웹 사이트 및 Azure AD 테넌트 ID를 매핑하면 **비밀 토큰** 및 SchoolStream ASA **테넌트 URL** 을 받게 됩니다.

## <a name="step-3-add-schoolstream-asa-from-the-azure-ad-application-gallery"></a>3단계: Azure AD 애플리케이션 갤러리에서 SchoolStream ASA 추가

Azure AD에서 SchoolStream ASA에 대한 프로비전 관리를 시작하려면 Azure AD 애플리케이션 갤러리에서 SchoolStream ASA를 추가해야 합니다. 

1. Azure Portal에 회사 또는 학교 계정, 개인 Microsoft 계정으로 로그인합니다.
2. 왼쪽 탐색 창에서 **Azure Active Directory** 서비스를 선택합니다.
3. **엔터프라이즈 애플리케이션** 으로 이동한 다음, **모든 애플리케이션** 을 선택합니다.
4. 새 애플리케이션을 추가하려면 **새 애플리케이션** 을 선택합니다.
5. **Azure AD 갤러리 찾아보기** 섹션의 검색 상자에서 **SchoolStream ASA** 를 입력합니다.
6. 결과 패널에서 **SchoolStream ASA** 를 선택한 다음, **앱에 가입** 합니다. 앱이 테넌트에 추가될 때까지 잠시 동안 기다려 주세요.


이전에 SSO에 대해 SchoolStream ASA를 설정한 경우 동일한 애플리케이션을 사용할 수 있습니다. 그러나 처음 통합을 테스트하는 경우 별도의 앱을 만드는 것이 좋습니다. [여기](../manage-apps/add-application-portal.md)를 클릭하여 갤러리에서 애플리케이션을 추가하는 방법에 대해 자세히 알아봅니다. 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>4단계. 프로비저닝 범위에 있는 사용자 정의 

Azure AD 프로비저닝 서비스를 사용하면 애플리케이션에 대한 할당 또는 사용자/그룹의 특성을 기반으로 프로비저닝되는 사용자의 범위를 지정할 수 있습니다. 할당을 기준으로 앱에 프로비저닝할 사용자의 범위를 선택하려면 다음 [단계](../manage-apps/assign-user-or-group-access-portal.md)를 사용하여 애플리케이션에 사용자 및 그룹을 할당할 수 있습니다. 사용자 또는 그룹의 특성만을 기준으로 프로비저닝할 사용자의 범위를 선택하려면 [여기](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md) 설명된 대로 범위 지정 필터를 사용할 수 있습니다. 

* 사용자 및 그룹을 SchoolStream ASA에 할당할 때 **기본 액세스** 이외의 역할을 선택해야 합니다. 기본 액세스 역할이 있는 사용자는 프로비저닝에서 제외되고 프로비저닝 로그에 실질적으로 권한을 부여받지 않은 것으로 표시됩니다. 애플리케이션에서 사용할 수 있는 유일한 역할이 기본 액세스 역할인 경우에는 [애플리케이션 매니페스트를 업데이트](../develop/howto-add-app-roles-in-azure-ad-apps.md)하여 역할을 더 추가할 수 있습니다. 

* 소규모로 시작합니다. 모든 사용자에게 배포하기 전에 소수의 사용자 및 그룹 집합으로 테스트합니다. 할당된 사용자 및 그룹으로 프로비저닝 범위가 설정된 경우 앱에 하나 또는 두 개의 사용자 또는 그룹을 할당하여 범위를 제어할 수 있습니다. 모든 사용자 및 그룹으로 범위가 설정된 경우 [특성 기반 범위 지정 필터](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)를 지정할 수 있습니다. 


## <a name="step-5-configure-automatic-user-provisioning-to-schoolstream-asa"></a>5단계. SchoolStream ASA에 대한 자동 사용자 프로비저닝 구성 

이 섹션에서는 Azure AD의 사용자 및/또는 그룹 할당에 따라 SchoolStream ASA에서 사용자 및/또는 그룹을 만들고, 업데이트하고, 사용 해제하도록 Azure AD 프로비전 서비스를 구성하는 단계를 안내합니다.

### <a name="to-configure-automatic-user-provisioning-for-schoolstream-asa-in-azure-ad"></a>Azure AD에서 SchoolStream ASA에 대한 자동 사용자 프로비저닝을 구성하려면 다음을 수행합니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다. **엔터프라이즈 애플리케이션**, **모든 애플리케이션** 을 차례로 선택합니다.

    ![엔터프라이즈 애플리케이션 블레이드](common/enterprise-applications.png)

1. 애플리케이션 목록에서 **SchoolStream ASA** 를 선택합니다.

    ![애플리케이션 목록의 SchoolStream ASA 링크](common/all-applications.png)

1. **프로비전** 탭을 선택합니다.

    ![프로비저닝 탭](common/provisioning.png)

1. 프로비전을 처음 구성하는 경우 **시작** 을 선택합니다.

    ![프로비전 시작](media/schoolstream-asa-provisioning-tutorial/provisioning-get-started.png)
    
1. **프로비전 모드** 를 **자동** 으로 설정합니다.

    ![프로비저닝 탭 자동](media/schoolstream-asa-provisioning-tutorial/provisioning-automatic.png)

1. **관리자 자격 증명** 섹션에서 SchoolStream ASA **테넌트 URL** 및 **비밀 토큰** 을 입력합니다. **연결 테스트** 를 클릭하여 Azure AD에서 SchoolStream ASA에 연결할 수 있는지 확인합니다. 연결에 실패하면 SchoolStream ASA 계정에 관리자 권한이 있는지 확인하고 다시 시도합니다.

    ![토큰](common/provisioning-testconnection-tenanturltoken.png)

1. **저장** 을 선택하여 **설정** 섹션을 확인합니다.

1. **설정** 섹션의 **알림 이메일** 필드에서 프로비전 오류 알림을 받을 개인 또는 그룹의 이메일 주소를 입력하고, **오류가 발생할 경우 이메일 알림 보내기** 확인란을 선택합니다.

    ![알림 이메일](common/provisioning-notification-email.png)

1. **매핑** 섹션에서 **Azure Active Directory 사용자 프로비전** 을 선택합니다.

1. 아래쪽에서 **새 매핑 추가** 를 선택합니다.

1. **특성 편집** 대화 상자에서, 
    
   * **매핑 유형** 필드의 드롭다운에서 **직접** 을 선택합니다.
   * **원본 특성** 필드의 드롭다운에서 **extensionAttribute1** 을 선택합니다.
   * **null인 경우 기본값(선택 사항)** 필드에서 **Azure AD 테넌트 ID** 를 입력합니다.
   * **대상 특성** 필드의 드롭다운에서 **urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:organization** 을 선택합니다. 
   * **이 특성을 사용하여 개체 일치** 필드의 드롭다운에서 **아니요** 를 선택합니다.
   * **이 매핑 적용** 필드의 드롭다운에서 **항상** 을 선택합니다.
   * **확인** 을 선택합니다.

       ![특성 편집](media/schoolstream-asa-provisioning-tutorial/add-mappings-attribute.png)

1. **특성 매핑** 섹션에서 Azure AD에서 SchoolStream ASA로 동기화되는 사용자 특성을 검토합니다. **일치** 속성으로 선택한 특성은 업데이트 작업 시 SchoolStream ASA의 사용자 계정을 일치시키는 데 사용됩니다. [일치하는 대상 특성](../app-provisioning/customize-application-attributes.md)을 변경하는 경우 SchoolStream ASA API에서 해당 특성에 따라 사용자 필터링을 지원하는지 확인해야 합니다.



   |attribute|Type|필터링에 지원됨|
   |---|---|---|
   |userName|String|&check;
   |활성|부울|   
   |displayName|String|
   |emails[type eq "work"].value|String|
   |preferredLanguage|String|
   |name.givenName|String|
   |name.familyName|String|
   |name.formatted|String|
   |phoneNumbers[type eq "mobile"].value|String|
   |externalId|String|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:organization|String| 

13. **저장** 단추를 선택하여 변경 내용을 커밋합니다. **애플리케이션** 탭으로 돌아가서 **프로비전 수정** 을 선택하여 계속할 수 있습니다.

1. 범위 지정 필터를 구성하려면 [범위 지정 필터 자습서](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)에서 제공하는 다음 지침을 참조합니다.

1. Azure AD 프로비전 서비스를 SchoolStream ASA에 사용하도록 설정하려면 **설정** 섹션에서 **프로비전 상태** 를 **켜기** 로 변경합니다.

    ![프로비전 상태 켜기로 전환](common/provisioning-toggle-on.png)

1. **설정** 섹션의 **범위** 에서 원하는 값을 선택하여 SchoolStream ASA에 프로비전하려는 사용자 및/또는 그룹을 정의합니다.

    ![프로비전 범위](common/provisioning-scope.png)

1. 프로비전할 준비가 되면 **저장** 을 클릭합니다.

    ![프로비전 구성 저장](common/provisioning-configuration-save.png)

이 작업은 **설정** 의 **범위** 섹션에 정의된 모든 사용자 및/또는 그룹의 초기 동기화 주기를 시작합니다. 초기 주기는 Azure AD 프로비저닝 서비스가 실행되는 동안 약 40분마다 발생하는 후속 주기보다 더 많은 시간이 걸립니다. 

## <a name="step-6-monitor-your-deployment"></a>6단계. 배포 모니터링
프로비저닝을 구성한 후에는 다음 리소스를 사용하여 배포를 모니터링합니다.

* [프로비저닝 로그](../reports-monitoring/concept-provisioning-logs.md)를 사용하여 어떤 사용자가 성공적으로 프로비저닝되었는지 확인합니다.
* [진행률 표시줄](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md)을 통해 프로비저닝 주기 상태와 완료 정도를 확인합니다.
* 프로비저닝 구성이 비정상 상태로 보이면 애플리케이션이 격리됩니다. 격리 상태에 대한 자세한 내용은 [여기](../app-provisioning/application-provisioning-quarantine-status.md)를 참조하세요.  

## <a name="more-resources"></a>추가 리소스

* [엔터프라이즈 앱에 대한 사용자 계정 프로비전 관리](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Azure Active Directory로 애플리케이션 액세스 및 Single Sign-On을 구현하는 방법](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>다음 단계

* [프로비저닝 작업에 대한 로그를 검토하고 보고서를 받아보는 방법을 알아봅니다](../app-provisioning/check-status-user-account-provisioning.md).