---
title: '자습서: Azure Active Directory로 자동 사용자 프로비전을 위한 Contentful 구성'
description: Azure Active Directory(Azure AD)에서 Contentful로 사용자 계정을 자동으로 프로비저닝 및 프로비저닝 해제하는 방법을 알아봅니다.
services: active-directory
documentationcenter: ''
author: twimmers
manager: beatrizd
ms.assetid: 3b761984-a9a0-4519-b23e-563438978de5
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 11/11/2020
ms.author: thwimmer
ms.openlocfilehash: 60647cbceed7b7917daa18e8b8c7ed6531ebe0c9
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131059122"
---
# <a name="tutorial-configure-contentful-for-automatic-user-provisioning"></a>자습서: 자동 사용자 프로비전을 위한 Contentful 구성

이 자습서에서는 자동 사용자 프로비저닝을 구성하기 위해 Contentful과 Azure AD(Azure Active Directory)에서 수행해야 하는 단계를 설명합니다. 구성되면 Azure AD에서 Azure AD 프로비저닝 서비스를 사용하여 사용자 및 그룹을 [Contentful](https://www.contentful.com/)으로 자동으로 프로비저닝 및 프로비저닝 해제합니다. 이 서비스의 기능 및 작동 방법에 대한 중요한 내용과 FAQ는 [Azure Active Directory를 사용하여 SaaS 애플리케이션으로 사용자 프로비저닝 및 프로비저닝 해제 자동화](../app-provisioning/user-provisioning.md)를 참조하세요. 

## <a name="capabilities-supported"></a>지원되는 기능

> [!div class="checklist"]
> * Contentful에서 사용자 만들기
> * Contentful에서 더 이상 액세스할 필요가 없는 사용자 제거
> * Azure AD와 Contentful 간에 사용자 특성을 동기화된 상태로 유지
> * Contentful에서 그룹 및 그룹 멤버 자격 프로비저닝
> * Contentful에 대한 [Single Sign-On](contentful-tutorial.md)(권장)

## <a name="prerequisites"></a>필수 구성 요소

이 자습서에 설명된 시나리오에서는 사용자에게 이미 다음 필수 구성 요소가 있다고 가정합니다.

* [Azure AD 테넌트](../develop/quickstart-create-new-tenant.md) 
* 프로비저닝을 구성할 [권한](../roles/permissions-reference.md)이 있는 Azure AD의 사용자 계정(예: 애플리케이션 관리자, 클라우드 애플리케이션 관리자, 애플리케이션 소유자 또는 전역 관리자). 
* SCIM(System for Cross-domain Identity Management) 프로비저닝 지원 구독이 있는 Contentful 조직 계정. 조직의 구독에 대한 문의 사항이 있으면 [Contentful 지원 팀](mailto:support@contentful.com)에 문의하십시오.
 
## <a name="plan-your-provisioning-deployment"></a>프로비저닝 배포 계획

1. [프로비저닝 서비스의 작동 방식](../app-provisioning/user-provisioning.md)에 대해 알아봅니다.
2. [프로비저닝 범위](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)에 있는 사용자를 결정합니다.
3. [Azure AD와 Contentful 간에 매핑](../app-provisioning/customize-application-attributes.md)할 데이터를 정합니다. 

## <a name="configure-contentful-to-support-provisioning-with-azure-ad"></a>Contentful이 Azure AD로 프로비저닝하도록 구성

1. Contentful에서 **Service 사용자** 계정을 만듭니다. Azure의 모든 프로비저닝 권한은 이 계정을 통해 제공됩니다. **소유자** 를 계정에 대한 조직 역할로 선택하는 것이 좋습니다.

2. **서비스 사용자** 로 Contentful에 로그인합니다.

3. 왼쪽 메뉴에서 **조직 설정** > **액세스 도구** > **사용자 프로비저닝** 을 선택합니다.

   ![액세스 도구 아래에 사용자 프로비저닝이 강조 표시된 Contentful 조직 설정 메뉴 스크린샷.](media/contentful-provisioning-tutorial/access.png)

4. **SCIM URL** 을 복사하고 저장합니다. Contentful 애플리케이션의 **프로비저닝** 탭에서 Azure Portal에 이 값을 입력합니다.

5. **개인용 액세스 토큰 생성** 을 선택합니다.

    ![개인용 액세스 토큰을 생성하는 SCIM URL을 보여 주는 스크린샷](media/contentful-provisioning-tutorial/generate.png)

6. 모달 창에서 개인용 액세스 토큰의 이름을 입력한 다음 **생성** 을 선택합니다.

7. SCIM URL과 비밀 토큰이 생성되었습니다. 이 값을 복사하여 저장합니다. Contentful 애플리케이션의 **프로비저닝** 탭에서 이 값을 Azure Portal에 입력합니다.

    ![C F P A T와 토큰 자리 표시자 이름이 강조 표시된 개인용 액세스 토큰 창의 스크린샷.](media/contentful-provisioning-tutorial/token.png)


Contentful 관리자 콘솔에서 프로비저닝을 구성하면서 질문이 있는 경우 [Contentful 지원 팀](mailto:support@contentful.com)에 문의하십시오.

## <a name="add-contentful-from-the-azure-ad-application-gallery"></a>Azure AD 애플리케이션 갤러리에서 Contentful 추가

Contentful에 대한 프로비저닝을 관리하려면 Azure AD 애플리케이션 갤러리에서 Contentful을 추가합니다. 이전에 Contentful의 Single Sign-On을 구성한 경우 동일한 애플리케이션을 사용할 수 있습니다. 그러나 통합을 처음으로 테스트할 때에는 별도의 앱을 만드는 것이 좋습니다. [갤러리에 애플리케이션을 추가](../manage-apps/add-application-portal.md)하는 방법을 알아봅니다. 

## <a name="define-who-will-be-in-scope-for-provisioning"></a>프로비저닝 범위에 있는 사용자 정의 

Azure AD 프로비저닝 서비스를 사용하여 애플리케이션 할당에 따라 또는 사용자/그룹의 특성에 따라 프로비저닝할 사용자의 범위를 지정할 수 있습니다. 

할당을 기준으로 앱에 프로비저닝할 사용자의 범위를 선택하려면 [애플리케이션에 사용자 및 그룹을 할당하는 단계](../manage-apps/assign-user-or-group-access-portal.md)를 완료합니다.

사용자 또는 그룹의 특성만을 기준으로 프로비저닝할 사용자의 범위를 지정하려면 범위 지정 필터를 사용하여 [사용자 계정을 프로비저닝하는 데 대한 조건 규칙을 정의하십시오](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 

* 사용자 및 그룹을 Contentful에 할당할 때 **기본 액세스** 이외의 역할을 선택해야 합니다. 기본 액세스 역할이 있는 사용자는 프로비저닝에서 제외되고 프로비저닝 로그에서 실질적으로 권한이 없는 것으로 표시됩니다. 애플리케이션에서 사용할 수 있는 유일한 역할이 기본 액세스 역할인 경우 [애플리케이션 매니페스트를 업데이트](../develop/howto-add-app-roles-in-azure-ad-apps.md)하여 더 많은 역할을 추가할 수 있습니다. 
* 소규모로 시작합니다. 모든 사용자에게 롤아웃하려면 먼저 소수의 사용자 및 그룹 세트를 사용하여 테스트합니다. 할당된 사용자 및 그룹으로 프로비저닝 범위가 설정된 경우 앱에 하나 또는 두 개의 사용자 또는 그룹을 할당하여 범위를 제어할 수 있습니다. 범위가 모든 사용자 및 그룹으로 설정되면 [특성 기반 범위 지정 필터](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)를 지정할 수 있습니다. 

## <a name="configure-automatic-user-provisioning-to-contentful"></a>Contentful에 자동 사용자 프로비저닝 구성 

이 섹션에서는 Azure AD의 사용자 또는 그룹 할당에 따라 TestApp에서 사용자 및 그룹을 만들고, 업데이트하고, 사용 해제하도록 Azure AD 프로비저닝 서비스를 구성하는 단계를 안내합니다.

### <a name="configure-automatic-user-provisioning-for-contentful-in-azure-ad"></a>Azure AD에서 Contentful에 대한 자동 사용자 프로비저닝 구성

1. [Azure Portal](https://portal.azure.com)에 로그인합니다. **엔터프라이즈 애플리케이션**, **모든 애플리케이션** 을 차례로 선택합니다.

   ![모든 애플리케이션이 강조 표시된 상태로 Azure Portal의 엔터프라이즈 애플리케이션 메뉴를 보여주는 스크린샷.](common/enterprise-applications.png)

2. 애플리케이션 목록에서 **Contentful** 을 선택합니다.

   ![애플리케이션 목록에 반환된 처음 20개의 결과를 보여주는 스크린샷.](common/all-applications.png)

3. **프로비전** 탭을 선택합니다.

   ![왼쪽 메뉴의 관리 섹션에 강조 표시된 프로비저닝 탭의 스크린샷.](common/provisioning.png)

4. **프로비전 모드** 를 **자동** 으로 설정합니다.

   ![자동이 강조 표시된 프로비저닝 모드 옵션을 보여주는 스크린샷.](common/provisioning-automatic.png)

5. **관리자 자격 증명** 섹션에서 Contentful 테넌트 URL과 비밀 토큰을 입력하십시오. Azure AD에서 Contentful에 연결할 수 있는지 확인하려면 **연결 테스트** 를 선택합니다. 연결에 실패하면 Contentful 계정에 관리자 권한이 있는지 확인하고 다시 시도합니다.

   ![연결 테스트 단추가 강조 표시된 테넌트 U R L과 비밀 토큰 텍스트 상자가 있는 스크린샷.](common/provisioning-testconnection-tenanturltoken.png)

6. **알림 이메일** 에서 프로비저닝 오류 알림을 받을 사용자 또는 그룹의 이메일 주소를 입력한 다음, **오류가 발생할 경우 이메일 알림 보내기** 확인란을 선택합니다.

   ![알림 이메일 텍스트 상자를 보여 주는 스크린샷.](common/provisioning-notification-email.png)

7. **저장** 을 선택합니다.

8. **매핑** 섹션에서 **Azure Active Directory 사용자를 Contentful와 동기화** 를 선택합니다.

9. **특성 매핑** 섹션에서 Azure AD에서 Contentful로 동기화되는 사용자 특성을 검토합니다. **일치** 속성으로 선택한 특성은 업데이트 작업 시 Contentful의 사용자 계정을 일치시키는 데 사용됩니다. [일치하는 대상 특성](../app-provisioning/customize-application-attributes.md)을 변경하는 경우 Contentful API에서 해당 특성에 따라 사용자 필터링을 지원하는지 확인해야 합니다. **저장** 단추를 선택하여 변경 내용을 커밋합니다.

   |attribute|Type|필터링에 지원됨|
   |---|---|---|
   |userName|String|&check;|
   |name.givenName|String|
   |name.familyName|String|

10. **매핑** 섹션에서 **Azure Active Directory 그룹을 Contentful에 동기화** 를 선택합니다.

11. **특성 매핑** 섹션에서 Azure AD에서 Contentful로 동기화되는 그룹 특성을 검토합니다. **일치** 속성으로 선택한 특성은 업데이트 작업을 위해 Contentful의 그룹을 일치시키는 데 사용됩니다. **저장** 단추를 선택하여 변경 내용을 커밋합니다.

    |attribute|Type|필터링에 지원됨|
    |---|---|---|
    |displayName|String|&check;|
    |members|참조|

12. 범위 지정 필터를 설정하려면 [범위 지정 필터 자습서](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)에 설명된 단계를 완료합니다.

13. Contentful에 대한 Azure AD 프로비저닝 서비스를 사용하도록 설정하려면 **설정** 섹션에서 **프로비저닝 상태** 를 **켜기** 로 선택합니다.

    ![프로비저닝 상태의 켜기와 끄기 토글을 보여주는 스크린샷.](common/provisioning-toggle-on.png)

14. Contentful로 프로비저닝하고자 하는 사용자나 그룹을 정의하려면 **설정** 섹션의 **범위** 에서 관련 옵션을 선택하십시오.

    ![범위 창에서 선택할 수 있는 옵션을 보여주는 스크린샷.](common/provisioning-scope.png)

15. 프로비저닝할 준비가 되면 **저장** 을 선택합니다.

    ![저장 단추와 취소 단추를 보여주는 스크린샷.](common/provisioning-configuration-save.png)

이 작업은 **설정** 아래의 **범위** 에 정의된 모든 사용자 및 그룹의 초기 동기화 주기를 시작합니다. 초기 주기는 Azure AD 프로비저닝 서비스가 실행되는 동안 약 40분마다 발생하는 후속 주기보다 더 많은 시간이 걸립니다. 

## <a name="monitor-your-deployment"></a>배포 모니터링

프로비저닝이 구성되면 다음 리소스를 사용하여 배포를 모니터링합니다.

* 어떤 사용자가 성공적으로 프로비저닝되었는지 확인하려면 [프로비저닝 로그](../reports-monitoring/concept-provisioning-logs.md)를 사용합니다.
* 프로비저닝 주기 상태와 완료 정도는 [진행률 표시줄](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md)을 통해 확인할 수 있습니다.
* 프로비저닝 구성이 비정상 상태로 보이면 애플리케이션이 격리됩니다. [격리 상태](../app-provisioning/application-provisioning-quarantine-status.md)에 대해 자세히 알아보세요.  

## <a name="next-steps"></a>다음 단계

* [프로비저닝 작업에 대한 로그를 검토하고 보고서를 받아보는 방법을 알아봅니다](../app-provisioning/check-status-user-account-provisioning.md).
* [엔터프라이즈 앱에 대한 사용자 계정 프로비전 관리](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Azure Active Directory로 애플리케이션 액세스 및 Single Sign-On을 구현하는 방법](../manage-apps/what-is-single-sign-on.md)
