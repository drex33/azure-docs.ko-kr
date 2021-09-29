---
title: '자습서: Azure Active Directory를 사용하여 자동 사용자 프로비저닝을 수행하도록 SAP Analytics Cloud 구성 | Microsoft Docs'
description: 사용자 계정을 Azure AD에서 SAP Analytics Cloud로 자동으로 프로비저닝 및 프로비저닝을 해제하는 방법을 알아봅니다.
services: active-directory
documentationcenter: ''
author: twimmers
writer: twimmers
manager: beatrizd
ms.assetid: 27d12989-efa8-4254-a4ad-8cb6bf09d839
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 08/13/2020
ms.author: thwimmer
ms.openlocfilehash: 84842b751c0e4351da13d26654147c9541af6973
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128643399"
---
# <a name="tutorial-configure-sap-analytics-cloud-for-automatic-user-provisioning"></a>자습서: 자동 사용자 프로비저닝을 수행하도록 SAP Analytics Cloud 구성

이 자습서에서는 자동 사용자 프로비저닝을 구성하기 위해 SAP Analytics Cloud 및 Azure AD(Azure Active Directory) 모두에서 수행해야 하는 단계에 대해 설명합니다. 구성되면 Azure AD에서 Azure AD 프로비저닝 서비스를 사용하여 사용자 및 그룹을 [SAP Analytics Cloud](https://www.sapanalytics.cloud/)로 자동으로 프로비저닝 및 프로비저닝을 해제합니다. 이 서비스의 기능, 작동 방법 및 질문과 대답에 대한 중요한 내용은 [Azure Active Directory를 사용하여 SaaS 애플리케이션의 사용자를 자동으로 프로비저닝 및 프로비저닝 해제](../app-provisioning/user-provisioning.md)를 참조하세요. 


## <a name="capabilities-supported"></a>지원되는 기능
> [!div class="checklist"]
> * SAP Analytics Cloud에서 사용자 만들기
> * SAP Analytics Cloud에서 더 이상 액세스할 필요가 없는 사용자 제거
> * 사용자 특성을 Azure AD와 SAP Analytics Cloud 간에 동기화된 상태로 유지
> * SAP Analytics Cloud에 [Single Sign-On](sapboc-tutorial.md) 사용(추천)

## <a name="prerequisites"></a>사전 요구 사항

이 자습서에 설명된 시나리오에서는 사용자에게 이미 다음 필수 구성 요소가 있다고 가정합니다.

* [Azure AD 테넌트](../develop/quickstart-create-new-tenant.md) 
* 프로비저닝을 구성할 [권한](../roles/permissions-reference.md)이 있는 Azure AD의 사용자 계정(예: 애플리케이션 관리자, 클라우드 애플리케이션 관리자, 애플리케이션 소유자 또는 전역 관리자). 
* SAP Analytics Cloud 테넌트
* 관리자 권한이 있는 SAP Identity Provisioning 관리 콘솔의 사용자 계정. Identity Provisioning 관리 콘솔에서 프록시 시스템에 액세스할 수 있는지 확인합니다. **Proxy Systems(프록시 시스템)** 타일이 표시되지 않으면 이 타일에 대한 액세스를 요청하는 **BC-IAM-IPS** 구성 요소에 대한 인시던트를 만듭니다.
* SAP Analytics Cloud의 권한 부여 클라이언트 자격 증명이 있는 OAuth 클라이언트 방법을 알아보려면 다음을 참조하세요. [OAuth 클라이언트 및 신뢰할 수 있는 ID 공급자 관리](https://help.sap.com/viewer/00f68c2e08b941f081002fd3691d86a7/release/en-US/4f43b54398fc4acaa5efa32badfe3df6.html)

## <a name="step-1-plan-your-provisioning-deployment"></a>1단계. 프로비저닝 배포 계획

1. [프로비저닝 서비스의 작동 방식](../app-provisioning/user-provisioning.md)에 대해 알아봅니다.
2. [프로비저닝 범위](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)에 있는 사용자를 결정합니다.
3. [Azure AD와 SAP Analytics Cloud 간에 매핑](../app-provisioning/customize-application-attributes.md)할 데이터를 결정합니다. 

## <a name="step-2-configure-sap-analytics-cloud-to-support-provisioning-with-azure-ad"></a>2단계. Azure AD를 사용하여 프로비저닝을 지원하도록 SAP Analytics Cloud 구성

1. 관리자 계정 권한으로 [SAP Identity Provisioning 관리 콘솔](https://ips-xlnk9v890j.dispatcher.us1.hana.ondemand.com/)에 로그인한 다음, **Proxy Systems(프록시 시스템)** 를 선택합니다.

   ![SAP Proxy Systems](./media/sap-analytics-cloud-provisioning-tutorial/sap-proxy-systems.png)

2. **속성** 을 선택합니다.

   ![SAP Proxy Systems Properties(속성)](./media/sap-analytics-cloud-provisioning-tutorial/sap-proxy-systems-properties.png)

3. **URL** 을 복사하고 `/api/v1/scim`을 URL에 추가합니다. 나중에 **테넌트 URL** 필드에서 사용할 수 있도록 이를 저장합니다.

   ![SAP Proxy Systems Properties URL](./media/sap-analytics-cloud-provisioning-tutorial/sap-proxy-systems-details-url.png)

4. [POSTMAN](https://www.postman.com/)을 사용하여 주소에 대한 POST HTTPS 호출(`<Token URL>?grant_type=client_credentials`)을 수행합니다. 여기서 `Token URL`은 **OAuth2TokenServiceURL** 필드의 URL입니다. 이 단계는 자동 프로비저닝을 구성할 때 [비밀 토큰] 필드에서 사용할 액세스 토큰을 생성하는 데 필요합니다.

   ![SAP IP Proxy Systems Properties OAuth](./media/sap-analytics-cloud-provisioning-tutorial/sap-proxy-systems-details-oauth.png)

5. Postman에서 **Basic Authentication(기본 인증)** 을 사용하고, OAuth 클라이언트 ID를 사용자로 설정하고, 비밀을 암호로 설정합니다. 이 호출에서 액세스 토큰을 반환합니다. 나중에 **비밀 토큰** 필드에서 사용할 수 있도록 이 토큰을 복사한 상태로 유지합니다.

   ![Postman POST 요청](./media/sap-analytics-cloud-provisioning-tutorial/postman-post-request.png)

## <a name="step-3-add-sap-analytics-cloud-from-the-azure-ad-application-gallery"></a>3단계: Azure AD 애플리케이션 갤러리에서 SAP Analytics Cloud 추가

Azure AD 애플리케이션 갤러리에서 SAP Analytics Cloud를 추가하여 SAP Analytics Cloud로 프로비저닝 관리를 시작합니다. 이전에 SSO를 SAP Analytics Cloud에 사용하도록 설정한 경우 동일한 애플리케이션을 사용할 수 있습니다. 그러나 처음 통합을 테스트하는 경우 별도의 앱을 만드는 것이 좋습니다. [여기](../manage-apps/add-application-portal.md)를 클릭하여 갤러리에서 애플리케이션을 추가하는 방법에 대해 자세히 알아봅니다. 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>4단계. 프로비저닝 범위에 있는 사용자 정의 

Azure AD 프로비저닝 서비스를 사용하면 애플리케이션에 대한 할당 또는 사용자/그룹의 특성을 기반으로 프로비저닝되는 사용자의 범위를 지정할 수 있습니다. 할당을 기준으로 앱에 프로비저닝할 사용자의 범위를 선택하려면 다음 [단계](../manage-apps/assign-user-or-group-access-portal.md)를 사용하여 애플리케이션에 사용자 및 그룹을 할당할 수 있습니다. 사용자 또는 그룹의 특성만을 기준으로 프로비저닝할 사용자의 범위를 선택하려면 [여기](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md) 설명된 대로 범위 지정 필터를 사용할 수 있습니다. 

* 사용자 및 그룹을 SAP Analytics Cloud에 할당할 때 **기본 액세스** 이외의 역할을 선택해야 합니다. 기본 액세스 역할이 있는 사용자는 프로비저닝에서 제외되고 프로비저닝 로그에 실질적으로 권한을 부여받지 않은 것으로 표시됩니다. 애플리케이션에서 사용할 수 있는 유일한 역할이 기본 액세스 역할인 경우에는 [애플리케이션 매니페스트를 업데이트](../develop/howto-add-app-roles-in-azure-ad-apps.md)하여 역할을 더 추가할 수 있습니다. 

* 소규모로 시작합니다. 모든 사용자에게 배포하기 전에 소수의 사용자 및 그룹 집합으로 테스트합니다. 할당된 사용자 및 그룹으로 프로비저닝 범위가 설정된 경우 앱에 하나 또는 두 개의 사용자 또는 그룹을 할당하여 범위를 제어할 수 있습니다. 모든 사용자 및 그룹으로 범위가 설정된 경우 [특성 기반 범위 지정 필터](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)를 지정할 수 있습니다. 


## <a name="step-5-configure-automatic-user-provisioning-to-sap-analytics-cloud"></a>5단계. SAP Analytics Cloud에 대한 자동 사용자 프로비저닝 구성 

이 섹션에서는 Azure AD의 사용자 및/또는 그룹 할당에 따라 TestApp에서 사용자 및/또는 그룹을 만들고, 업데이트하고, 사용 해제하도록 Azure AD 프로비저닝 서비스를 구성하는 단계를 안내합니다.

### <a name="to-configure-automatic-user-provisioning-for-sap-analytics-cloud-in-azure-ad"></a>Azure AD에서 SAP Analytics Cloud에 대한 자동 사용자 프로비저닝을 구성하려면 다음을 수행합니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다. **엔터프라이즈 애플리케이션**, **모든 애플리케이션** 을 차례로 선택합니다.

    ![엔터프라이즈 애플리케이션 블레이드](common/enterprise-applications.png)

2. 애플리케이션 목록에서 **SAP Analytics Cloud** 를 선택합니다.

    ![애플리케이션 목록의 SAP Analytics Cloud 링크](common/all-applications.png)

3. **프로비전** 탭을 선택합니다.

    ![프로비저닝 옵션이 호출된 관리 옵션의 스크린샷](common/provisioning.png)

4. **프로비전 모드** 를 **자동** 으로 설정합니다.

    ![자동 옵션이 호출된 프로비저닝 모드 드롭다운 목록의 스크린샷](common/provisioning-automatic.png)

5. **관리자 자격 증명** 섹션에서 이전에 **테넌트 URL** 에서 검색된 테넌트 URL 값을 입력합니다. **비밀 토큰** 에 이전에 검색된 액세스 토큰 값을 입력합니다. **연결 테스트** 를 클릭하여 Azure AD가 InVision에 연결할 수 있는지 확인합니다. 연결이 실패하면 SAP Analytics Cloud 계정에 관리자 권한이 있는지 확인하고 다시 시도합니다.

    ![스크린샷은 테넌트 URL 및 비밀 토큰을 입력할 수 있는 관리자 자격 증명 대화 상자를 보여 줍니다.](./media/sap-analytics-cloud-provisioning-tutorial/provisioning.png)

6. **알림 이메일** 필드에 프로비저닝 오류 알림을 받을 개인 또는 그룹의 메일 주소를 입력하고, **오류가 발생할 경우 메일 알림 보내기** 확인란을 선택합니다.

    ![알림 이메일](common/provisioning-notification-email.png)

7. **저장** 을 선택합니다.

8. **매핑** 섹션에서 **Azure Active Directory 사용자 프로비전** 을 선택합니다.

9. **특성 매핑** 섹션에서 Azure AD에서 SAP Analytics Cloud로 동기화되는 사용자 특성을 검토합니다. **일치** 속성으로 선택한 특성은 업데이트 작업을 위해 SAP Analytics Cloud의 사용자 계정을 일치시키는 데 사용됩니다. [일치하는 대상 특성](../app-provisioning/customize-application-attributes.md)을 변경하도록 선택하는 경우 SAP Analytics Cloud API에서 해당 특성에 따라 사용자를 필터링하도록 지원하는지 확인해야 합니다. **저장** 단추를 선택하여 변경 내용을 커밋합니다.

   |attribute|Type|필터링에 지원됨|
   |---|---|---|
   |userName|String|&check;|
   |name.givenName|String|
   |name.familyName|String|
   |활성|부울|
   |emails[type eq "work"].value|String|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:manager|String|

10. 범위 지정 필터를 구성하려면 [범위 지정 필터 자습서](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)에서 제공하는 다음 지침을 참조합니다.

11. Azure AD 프로비저닝 서비스를 SAP Analytics Cloud에 사용하도록 설정하려면 **설정** 섹션에서 **프로비전 상태** 를 **켜기** 로 변경합니다.

    ![프로비전 상태 켜기로 전환](common/provisioning-toggle-on.png)

12. **설정** 의 **범위** 섹션에서 원하는 값을 선택하여 SAP Analytics Cloud에 프로비저닝하려는 사용자 및/또는 그룹을 정의합니다.

    ![프로비전 범위](common/provisioning-scope.png)

13. 프로비전할 준비가 되면 **저장** 을 클릭합니다.

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

## <a name="next-steps"></a>다음 단계

* [프로비저닝 작업에 대한 로그를 검토하고 보고서를 받아보는 방법을 알아봅니다](../app-provisioning/check-status-user-account-provisioning.md).