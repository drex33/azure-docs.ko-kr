---
title: '자습서: Azure Active Directory로 자동 사용자 프로비전을 위한 Asana 구성 | Microsoft Docs'
description: 사용자 계정을 Azure AD에서 Asana로 자동으로 프로비저닝 및 프로비저닝 해제하는 방법을 알아봅니다.
services: active-directory
author: twimmers
writer: twimmers
manager: beatrizd
ms.assetid: 274810a2-bd74-4500-95f1-c720abf23541
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/27/2019
ms.author: thwimmer
ms.openlocfilehash: fca7efc24770b9a920c88082566d0e7509ffe513
ms.sourcegitcommit: 5af89a2a7b38b266cc3adc389d3a9606420215a9
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/08/2021
ms.locfileid: "131990430"
---
# <a name="tutorial-configure-asana-for-automatic-user-provisioning"></a>자습서: 자동 사용자 프로비전을 위한 Asana 구성

이 자습서에서는 자동 사용자 프로비저닝을 구성하기 위해 Asana 및 Azure AD(Azure Active Directory) 모두에서 수행해야 하는 단계를 설명합니다. 구성되면 Azure AD에서 Azure AD 프로비전 서비스를 사용하여 사용자 및 그룹을 [Asana](https://www.asana.com/)로 자동으로 프로비전 및 프로비전 해제합니다. 이 서비스의 기능, 작동 방법 및 질문과 대답에 대한 중요한 내용은 [Azure Active Directory를 사용하여 SaaS 애플리케이션의 사용자를 자동으로 프로비저닝 및 프로비저닝 해제](../app-provisioning/user-provisioning.md)를 참조하세요. 


## <a name="capabilities-supported"></a>지원되는 기능
> [!div class="checklist"]
> * Asana에서 사용자를 만듭니다.
> * Asana에서 더 이상 액세스할 필요가 없는 사용자를 제거합니다.
> * 사용자 특성을 Azure AD와 Asana 간에 동기화된 상태로 유지합니다.
> * Asana에서 그룹 및 그룹 멤버 자격을 프로비전합니다.
> * Asana에 대한 [Single Sign-On](asana-tutorial.md)을 수행합니다(권장).

## <a name="prerequisites"></a>사전 요구 사항

이 자습서에 설명된 시나리오에서는 사용자에게 이미 다음 필수 구성 요소가 있다고 가정합니다.

* Azure AD 테넌트
* [Enterprise](https://www.asana.com/pricing) 계획 이상을 사용하도록 설정된 Asana 테넌트
* 관리자 권한이 있는 Asana의 사용자 계정

> [!NOTE]
> Azure AD 프로비전 통합에는 Asana에 제공되는 [Asana API](https://asana.com/developers/api-reference/users)가 사용됩니다.

## <a name="step-1-plan-your-provisioning-deployment"></a>1단계. 프로비저닝 배포 계획
1. [프로비저닝 서비스의 작동 방식](../app-provisioning/user-provisioning.md)에 대해 알아봅니다.
1. [프로비저닝 범위](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)에 있는 사용자를 결정합니다.
1. [Azure AD와 Asana 간에 매핑](../app-provisioning/customize-application-attributes.md)할 데이터를 결정합니다. 

## <a name="step-2-configure-asana-to-support-provisioning-with-azure-ad"></a>2단계. Azure AD를 사용하여 프로비전을 지원하도록 Asana 구성
 > [!TIP]
 > Asana에 대해 SAML 기반 Single Sign-On을 사용하도록 설정하려면 Azure Portal에 제공된 지침을 따릅니다. Single Sign-On은 자동 프로비전과 별개로 구성할 수 있습니다. 하지만 이 두 가지 기능은 서로 보완적입니다.

### <a name="generate-secret-token-in-asana"></a>Asana에서 비밀 토큰 생성

* 관리자 계정을 사용하여 [Asana](https://app.asana.com/)에 로그인합니다.
* 맨 위 막대에서 프로필 사진을 선택하고 현재 조직 이름 설정을 선택합니다.
* **서비스 계정** 탭으로 이동합니다.
* **서비스 계정 추가** 를 선택합니다.
* **이름** 및 **정보** 와 프로필 사진을 필요에 따라 업데이트합니다. **토큰** 에서 토큰을 복사하고, [변경 내용 저장]에서 선택합니다.

## <a name="step-3-add-asana-from-the-azure-ad-application-gallery"></a>3단계: Azure AD 애플리케이션 갤러리에서 Asana 추가

Azure AD 애플리케이션 갤러리에서 Asana를 추가하여 Asana에 대한 프로비전 관리를 시작합니다. 이전에 SSO를 사용하도록 Asana를 설정한 경우 동일한 애플리케이션을 사용할 수 있습니다. 그러나 처음 통합을 테스트하는 경우 별도의 앱을 만드는 것이 좋습니다. [여기](../manage-apps/add-application-portal.md)를 클릭하여 갤러리에서 애플리케이션을 추가하는 방법에 대해 자세히 알아봅니다. 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>4단계. 프로비저닝 범위에 있는 사용자 정의 

Azure AD 프로비저닝 서비스를 사용하면 애플리케이션에 대한 할당 또는 사용자/그룹의 특성을 기반으로 프로비저닝되는 사용자의 범위를 지정할 수 있습니다. 할당을 기준으로 앱에 프로비저닝할 사용자의 범위를 선택하려면 다음 [단계](../manage-apps/assign-user-or-group-access-portal.md)를 사용하여 애플리케이션에 사용자 및 그룹을 할당할 수 있습니다. 사용자 또는 그룹의 특성만을 기준으로 프로비저닝할 사용자의 범위를 선택하려면 [여기](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md) 설명된 대로 범위 지정 필터를 사용할 수 있습니다.

* 사용자 및 그룹을 Asana에 할당하는 경우 **기본 액세스** 이외의 역할을 선택해야 합니다. 기본 액세스 역할이 있는 사용자는 프로비저닝에서 제외되고 프로비저닝 로그에 실질적으로 권한을 부여받지 않은 것으로 표시됩니다. 애플리케이션에서 사용할 수 있는 유일한 역할이 기본 액세스 역할인 경우 [애플리케이션 매니페스트를 업데이트](../develop/howto-add-app-roles-in-azure-ad-apps.md)하여 더 많은 역할을 추가할 수 있습니다. 

* 소규모로 시작합니다. 모든 사용자에게 배포하기 전에 소수의 사용자 및 그룹 집합으로 테스트합니다. 할당된 사용자 및 그룹으로 프로비저닝 범위가 설정된 경우 앱에 하나 또는 두 개의 사용자 또는 그룹을 할당하여 범위를 제어할 수 있습니다. 모든 사용자 및 그룹으로 범위가 설정된 경우 [특성 기반 범위 지정 필터](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)를 지정할 수 있습니다. 


## <a name="step-5-configure-automatic-user-provisioning-to-asana"></a>5단계. Asana에 대한 자동 사용자 프로비저닝 구성 

이 섹션에서는 Azure AD의 사용자 및/또는 그룹 할당에 따라 Asana에서 사용자 및 그룹을 만들고, 업데이트하고, 사용 해제하도록 Azure AD 프로비전 서비스를 구성하는 단계를 안내합니다.

### <a name="to-configure-automatic-user-provisioning-for-asana-in-azure-ad"></a>Azure AD에서 Asana에 대한 자동 사용자 프로비저닝을 구성하려면 다음을 수행합니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다. **엔터프라이즈 애플리케이션**, **모든 애플리케이션** 을 차례로 선택합니다.

    ![엔터프라이즈 애플리케이션 블레이드](common/enterprise-applications.png)

1. 애플리케이션 목록에서 **Asana** 를 선택합니다.

    ![애플리케이션 목록의 Asana 링크](common/all-applications.png)

1. **프로비전** 탭을 선택합니다.

    ![프로비저닝 탭](common/provisioning.png)

1. **프로비전 모드** 를 **자동** 으로 설정합니다.

    ![프로비저닝 탭 자동](common/provisioning-automatic.png)

1. **관리자 자격 증명** 섹션에서 Asana에서 제공한 Asana 테넌트 URL 및 비밀 토큰을 입력합니다. **연결 테스트** 를 클릭하여 Azure AD에서 Asana에 연결할 수 있는지 확인합니다. 연결이 실패하면 Asana에 문의하여 계정 설정을 확인합니다.

    ![토큰](common/provisioning-testconnection-tenanturltoken.png)

1. **알림 이메일** 필드에 프로비저닝 오류 알림을 받을 개인 또는 그룹의 메일 주소를 입력하고, **오류가 발생할 경우 메일 알림 보내기** 확인란을 선택합니다.

    ![알림 이메일](common/provisioning-notification-email.png)

1. **저장** 을 선택합니다.

1. **매핑** 섹션에서 **Azure Active Directory 사용자를 Asana에 동기화** 를 선택합니다.

1. **특성 매핑** 섹션에서 Azure AD에서 Asana로 동기화되는 사용자 특성을 검토합니다. **일치** 속성으로 선택한 특성은 업데이트 작업 시 Asana의 사용자 계정을 일치시키는 데 사용됩니다. [일치하는 대상 특성](../app-provisioning/customize-application-attributes.md)을 변경하도록 선택하는 경우 Asana API에서 해당 특성에 따라 사용자를 필터링하도록 지원하는지 확인해야 합니다. **저장** 단추를 선택하여 변경 내용을 커밋합니다.

   |attribute|Type|필터링에 지원됨|Asana에 필요|
   |---|---|---|---|
   |userName|String|&check;|&check;|   
   |활성|부울|||
   |name.formatted|String|||
   |preferredLanguage|String|||
   |title|String|||
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:department|String|||


1. **매핑** 섹션 아래에서 **Azure Active Directory 그룹을 Asana에 동기화** 를 선택합니다.

1. **특성 매핑** 섹션에서 Azure AD에서 Asana로 동기화되는 그룹 특성을 검토합니다. **일치** 속성으로 선택한 특성은 업데이트 작업을 위해 Asana의 그룹을 일치시키는 데 사용됩니다. **저장** 단추를 선택하여 변경 내용을 커밋합니다.

      |attribute|Type|필터링에 지원됨|Asana에 필요|
      |---|---|---|---|
      |displayName|String|&check;|&check;      
      |members|참조|||

1. 범위 지정 필터를 구성하려면 [범위 지정 필터 자습서](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)에서 제공하는 다음 지침을 참조합니다.

1. Azure AD 프로비전 서비스를 Asana에 사용하도록 설정하려면 **설정** 섹션에서 **프로비전 상태** 를 **켜기** 로 변경합니다.

    ![프로비전 상태 켜기로 전환](common/provisioning-toggle-on.png)

1. **설정** 섹션의 **범위** 에서 적절한 값을 선택하여 Asana에 프로비전하려는 사용자 및 그룹을 정의합니다.

    ![프로비전 범위](common/provisioning-scope.png)

1. 프로비저닝할 준비가 되면 **저장** 을 클릭합니다.

    ![프로비전 구성 저장](common/provisioning-configuration-save.png)

이 작업은 **설정** 의 **범위** 섹션에 정의된 모든 사용자 및/또는 그룹의 초기 동기화 주기를 시작합니다. 초기 주기는 Azure AD 프로비전 서비스가 실행되는 동안 약 40분마다 발생하는 후속 주기보다 실행하는 데 더 오래 걸립니다. 

## <a name="step-6-monitor-your-deployment"></a>6단계. 배포 모니터링
프로비저닝을 구성한 후에는 다음 리소스를 사용하여 배포를 모니터링합니다.

* [프로비저닝 로그](../reports-monitoring/concept-provisioning-logs.md)를 사용하여 어떤 사용자가 성공적으로 프로비저닝되었는지 확인합니다.
* [진행률 표시줄](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md)을 통해 프로비저닝 주기 상태와 완료 정도를 확인합니다.
* 프로비저닝 구성이 비정상 상태로 보이면 애플리케이션이 격리됩니다. 격리 상태에 대한 자세한 내용은 [여기](../app-provisioning/application-provisioning-quarantine-status.md)를 참조하세요.  

## <a name="change-log"></a>로그 변경

* 2021년 11월 6일 - **externalId, name.givenName 및 name.familyName** 에 대한 지원이 삭제되었습니다. **preferredLanguage, title 및 urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:department** 에 대한 지원이 추가되었습니다. 그리고 **그룹 프로비전** 이 사용하도록 설정되었습니다.

## <a name="more-resources"></a>추가 리소스

* [엔터프라이즈 앱에 대한 사용자 계정 프로비전 관리](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Azure Active Directory로 애플리케이션 액세스 및 Single Sign-On을 구현하는 방법](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>다음 단계

* [프로비저닝 작업에 대한 로그를 검토하고 보고서를 받아보는 방법을 알아봅니다](../app-provisioning/check-status-user-account-provisioning.md).