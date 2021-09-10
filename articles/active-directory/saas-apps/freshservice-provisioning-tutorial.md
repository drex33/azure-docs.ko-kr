---
title: '자습서: Azure Active Directory로 자동 사용자 프로비저닝을 위한 Freshservice Provisioning 구성 | Microsoft Docs'
description: 사용자 계정을 Azure AD에서 Freshservice Provisioning으로 자동으로 프로비전 및 프로비전 해제하는 방법을 알아봅니다.
services: active-directory
documentationcenter: ''
author: twimmers
writer: twimmers
manager: beatrizd
ms.assetid: e03ec65a-25ef-4c91-a364-36b2f007443c
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 08/09/2021
ms.author: thwimmer
ms.openlocfilehash: 2f5ee2257652bdfa07081a558a8723c334c11ed9
ms.sourcegitcommit: 5f659d2a9abb92f178103146b38257c864bc8c31
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/17/2021
ms.locfileid: "122327202"
---
# <a name="tutorial-configure-freshservice-provisioning-for-automatic-user-provisioning"></a>자습서: 자동 사용자 프로비저닝을 위한 Freshservice Provisioning 구성

이 자습서에서는 자동 사용자 프로비저닝을 구성하기 위해 Freshservice Provisioning 및 Azure AD(Azure Active Directory)에서 수행해야 하는 단계를 설명합니다. 구성된 경우 Azure AD는 Azure AD 프로비저닝 서비스를 사용하여 사용자를 [Freshservice Provisioning](https://effy.co.in/)에 자동으로 프로비전 및 프로비전 해제합니다. 이 서비스의 기능, 작동 방법 및 질문과 대답에 대한 중요한 내용은 [Azure Active Directory를 사용하여 SaaS 애플리케이션의 사용자를 자동으로 프로비저닝 및 프로비저닝 해제](../manage-apps/user-provisioning.md)를 참조하세요. 


## <a name="capabilities-supported"></a>지원되는 기능
> [!div class="checklist"]
> * Freshservice Provisioning에서 사용자 만들기
> * Freshservice Provisioning에서 더 이상 액세스할 필요가 없는 사용자 제거
> * Azure AD와 Freshservice Provisioning 간에 사용자 특성을 동기화된 상태로 유지

## <a name="prerequisites"></a>사전 요구 사항

이 자습서에 설명된 시나리오에서는 사용자에게 이미 다음 필수 구성 요소가 있다고 가정합니다.

* [Azure AD 테넌트](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant) 
* 프로비저닝을 구성할 [권한](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles)이 있는 Azure AD의 사용자 계정(예: 애플리케이션 관리자, 클라우드 애플리케이션 관리자, 애플리케이션 소유자 또는 전역 관리자). 
* 조직 관리자 권한이 있는 [Freshservice 계정](https://www.freshservice.com)

## <a name="step-1-plan-your-provisioning-deployment"></a>1단계. 프로비저닝 배포 계획
1. [프로비저닝 서비스의 작동 방식](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning)에 대해 알아봅니다.
2. [프로비저닝 범위](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts)에 있는 사용자를 결정합니다.
3. [Azure AD와 Freshservice Provisioning 간에 매핑](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes)할 데이터를 결정합니다. 

## <a name="step-2-configure-freshservice-provisioning-to-support-provisioning-with-azure-ad"></a>2단계. Azure AD에서 프로비저닝을 지원하도록 Freshservice Provisioning 구성

1. Freshservice 계정에서 **Freshservice 관리자** > **앱** > **앱 가져오기** 로 이동하여 마켓플레이스에서 **Azure Provisioning(SCIM)** 앱을 설치합니다.
2. 구성 화면에서 **Freshservice 도메인**(예: `acme.freshservice.com`) 및 **조직 관리자 API 키** 를 제공합니다.
3. **계속** 을 클릭합니다.
4. **전달자 토큰** 을 강조 표시하고 복사합니다. 이 값은 Azure Portal에 있는 Freshservice Provisioning 애플리케이션에 대한 프로비저닝 탭의 **비밀 토큰** 필드에 입력됩니다.
5. **설치** 를 클릭하여 설치를 완료합니다.
6. **테넌트 URL** 은 `https://scim.freshservice.com/scim/v2`입니다. 이 값은 Azure Portal에 있는 Freshservice Provisioning 애플리케이션에 대한 프로비저닝 탭의 **테넌트 URL** 필드에 입력됩니다.

## <a name="step-3-add-freshservice-provisioning-from-the-azure-ad-application-gallery"></a>3단계. Azure AD 애플리케이션 갤러리에서 Freshservice Provisioning 추가

Azure AD 애플리케이션 갤러리에서 Freshservice Provisioning을 추가하여 Freshservice Provisioning에 대한 프로비저닝 관리를 시작합니다. [여기](https://docs.microsoft.com/azure/active-directory/manage-apps/add-gallery-app)를 클릭하여 갤러리에서 애플리케이션을 추가하는 방법에 대해 자세히 알아봅니다. 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>4단계. 프로비저닝 범위에 있는 사용자 정의 

Azure AD 프로비저닝 서비스를 사용하면 애플리케이션에 대한 할당 또는 사용자의 특성을 기반으로 프로비저닝되는 사용자의 범위를 지정할 수 있습니다. 할당을 기준으로 앱에 프로비저닝할 사용자의 범위를 선택하려면 다음 [단계](../manage-apps/assign-user-or-group-access-portal.md)를 사용하여 애플리케이션에 사용자를 할당할 수 있습니다. 사용자의 특성만을 기준으로 프로비저닝할 사용자의 범위를 선택하는 경우 [여기](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts)에 설명된 대로 범위 지정 필터를 사용할 수 있습니다. 

* Freshservice Provisioning에 사용자를 할당할 때는 **기본 액세스** 이외의 역할을 선택해야 합니다. 기본 액세스 역할이 있는 사용자는 프로비저닝에서 제외되고 프로비저닝 로그에 실질적으로 권한을 부여받지 않은 것으로 표시됩니다. 애플리케이션에서 사용할 수 있는 유일한 역할이 기본 액세스 역할인 경우에는 [애플리케이션 매니페스트를 업데이트](https://docs.microsoft.com/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps)하여 역할을 더 추가할 수 있습니다. 

* 소규모로 시작합니다. 모든 사용자에게 배포하기 전에 소수의 사용자 집합으로 테스트합니다. 할당된 사용자로 프로비저닝 범위가 설정된 경우 앱에 하나 또는 두 개의 사용자를 할당하여 범위를 제어할 수 있습니다. 모든 사용자 및 그룹으로 범위가 설정된 경우 [특성 기반 범위 지정 필터](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts)를 지정할 수 있습니다. 


## <a name="step-5-configure-automatic-user-provisioning-to-freshservice-provisioning"></a>5단계. Freshservice Provisioning에 대한 자동 사용자 프로비저닝 구성 

이 섹션에서는 Azure AD의 사용자 할당에 따라 Freshservice Provisioning에서 사용자를 만들고, 업데이트하고, 사용 해제하도록 Azure AD 프로비저닝 서비스를 구성하는 단계를 안내합니다.

### <a name="to-configure-automatic-user-provisioning-for-freshservice-provisioning-in-azure-ad"></a>Azure AD에서 Freshservice Provisioning에 대한 자동 사용자 프로비저닝을 구성하려면 다음을 수행합니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다. **엔터프라이즈 애플리케이션**, **모든 애플리케이션** 을 차례로 선택합니다.

    ![엔터프라이즈 애플리케이션 블레이드](common/enterprise-applications.png)

2. 애플리케이션 목록에서 **Freshservice Provisioning** 을 선택합니다.

    ![애플리케이션 목록의 Freshservice Provisioning 링크](common/all-applications.png)

3. **프로비전** 탭을 선택합니다.

    ![프로비저닝 탭](common/provisioning.png)

4. **프로비전 모드** 를 **자동** 으로 설정합니다.

    ![프로비저닝 탭 자동](common/provisioning-automatic.png)

5. **관리자 자격 증명** 섹션 아래에 Freshservice Provisioning 테넌트 URL 및 비밀 토큰을 입력합니다. **연결 테스트** 를 클릭하여 Azure AD가 Freshservice Provisioning에 연결할 수 있는지 확인합니다. 연결이 실패하면 Freshservice Provisioning 계정에 관리자 권한이 있는지 확인한 후 다시 시도합니다.

    ![토큰](common/provisioning-testconnection-tenanturltoken.png)

6. **알림 이메일** 필드에 프로비저닝 오류 알림을 받을 개인 또는 그룹의 메일 주소를 입력하고, **오류가 발생할 경우 메일 알림 보내기** 확인란을 선택합니다.

    ![알림 이메일](common/provisioning-notification-email.png)

7. **저장** 을 선택합니다.

8. **매핑** 섹션에서 **Azure Active Directory 사용자를 Freshservice Provisioning에 동기화** 를 선택합니다.

9. **특성 매핑** 섹션에서, Azure AD에서 Freshservice Provisioning으로 동기화되는 사용자 특성을 검토합니다. **일치** 속성으로 선택한 특성은 업데이트 작업 시 Freshservice Provisioning의 사용자 계정의 일치 여부를 확인하는 데 사용됩니다. [일치하는 대상 특성](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes)을 변경하는 경우 Freshservice Provisioning API에서 해당 특성에 따라 사용자 필터링을 지원하는지 확인해야 합니다. **저장** 단추를 선택하여 변경 내용을 커밋합니다.

   |attribute|Type|필터링에 지원됨|
   |---|---|---|
   |userName|String|&check;|
   |활성|부울|
   |emails[type eq "work"].value|String|
   |displayName|String|
   |name.givenName|String|
   |name.familyName|String|
   |phoneNumbers[type eq "work"].value|String|
   |phoneNumbers[type eq "mobile"].value|String|
   |addresses[type eq "work"].formatted|String|
   |locale|String|
   |title|String|
   |timezone|String|
   |externalId|String|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:department|String|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:manager|참조|
   |urn:ietf:params:scim:schemas:extension:freshservice:2.0:User:isAgent|String|

> [!NOTE]
> 애플리케이션 요구를 충족시키기 위해 아래 단계를 수행하여 사용자 지정 확장 특성을 스키마에 추가할 수 있습니다.
> * 매핑 아래에서 **Azure Active Directory 사용자 프로비전** 을 선택합니다.
> * 페이지 맨 아래에서 **고급 옵션 표시** 를 선택합니다.
> * **Freshservice에 대한 특성 목록 편집** 을 선택합니다.
> * 특성 목록의 맨 아래에, 제공된 필드에 사용자 지정 특성에 대한 정보를 입력합니다. 사용자 지정 특성 URN 네임스페이스는 아래 예제에 표시된 패턴을 따라야 합니다. **CustomAttribute** 는 애플리케이션의 요구 사항에 따라 사용자 지정될 수 있습니다. 예: urn:ietf:params:scim:schemas:extension:freshservice:2.0:User:**isAgent**.
> * 사용자 지정 특성에 대해 적절한 데이터 형식을 선택하고 **저장** 을 클릭합니다.
> * 기본 매핑 화면으로 돌아가서 **새 매핑 추가** 를 클릭합니다. 사용자 지정 특성이 **대상 특성** 목록 드롭다운에 표시됩니다.

10. 범위 지정 필터를 구성하려면 [범위 지정 필터 자습서](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md)에서 제공하는 다음 지침을 참조합니다.

11. Freshservice Provisioning에 대한 Azure AD 프로비저닝 서비스를 사용하도록 설정하려면 **설정** 섹션에서 **프로비저닝 상태** 를 **켜기** 로 변경합니다.

    ![프로비전 상태 켜기로 전환](common/provisioning-toggle-on.png)

12. **설정** 섹션의 **범위** 에서 원하는 값을 선택하여 Freshservice Provisioning에 프로비전하려는 사용자를 정의합니다.

    ![프로비전 범위](common/provisioning-scope.png)

13. 프로비전할 준비가 되면 **저장** 을 클릭합니다.

    ![프로비전 구성 저장](common/provisioning-configuration-save.png)

이 작업은 **설정** 섹션의 **범위** 에 정의된 모든 사용자의 초기 동기화 주기를 시작합니다. 초기 주기는 Azure AD 프로비저닝 서비스가 실행되는 동안 약 40분마다 발생하는 후속 주기보다 더 많은 시간이 걸립니다. 

## <a name="step-6-monitor-your-deployment"></a>6단계. 배포 모니터링
프로비저닝을 구성한 후에는 다음 리소스를 사용하여 배포를 모니터링합니다.

1. [프로비저닝 로그](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-provisioning-logs)를 사용하여 어떤 사용자가 성공적으로 프로비저닝되었는지 확인합니다.
2. [진행률 표시줄](https://docs.microsoft.com/azure/active-directory/app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user)을 통해 프로비저닝 주기 상태와 완료 정도를 확인합니다.
3. 프로비저닝 구성이 비정상 상태로 보이면 애플리케이션이 격리됩니다. 격리 상태에 대한 자세한 내용은 [여기](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-quarantine-status)를 참조하세요.

## <a name="additional-resources"></a>추가 리소스

* [엔터프라이즈 앱에 대한 사용자 계정 프로비전 관리](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Azure Active Directory로 애플리케이션 액세스 및 Single Sign-On을 구현하는 방법](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>다음 단계

* [프로비저닝 작업에 대한 로그를 검토하고 보고서를 받아보는 방법을 알아봅니다](../manage-apps/check-status-user-account-provisioning.md).
