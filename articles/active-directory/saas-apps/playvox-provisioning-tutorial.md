---
title: '자습서: Azure Active Directory를 사용하여 자동 사용자 프로비저닝을 수행하도록 Playvox 구성 | Microsoft Docs'
description: Azure AD에서 Playvox로 사용자 계정을 자동으로 프로비저닝하고 프로비저닝을 해제하는 방법을 알아봅니다.
services: active-directory
documentationcenter: ''
author: Zhchia
writer: Zhchia
manager: beatrizd
ms.assetid: c31c20ab-f6cd-40e1-90ad-fa253ecbc0f8
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/18/2020
ms.author: Zhchia
ms.openlocfilehash: 3c7efca5e052c2d0680aa7ca3e1b6d27bfdd7d11
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96862482"
---
# <a name="tutorial-configure-playvox-for-automatic-user-provisioning"></a>자습서: 자동 사용자 프로비저닝을 위한 Playvox 구성

이 자습서에서는 자동 사용자 프로비저닝을 구성하기 위해 Playvox와 Azure AD(Azure Active Directory)에서 따라야 하는 단계를 설명합니다. 프로비저닝이 구성된 경우 Azure AD는 Azure AD 프로비저닝 서비스를 사용하여 사용자와 그룹을 [Playvox](https://www.playvox.com)에 자동으로 프로비저닝하거나 프로비저닝을 해제합니다. 이 서비스의 기능과 작동 방법에 대한 중요한 내용과 자주 묻는 질문은 [Azure Active Directory를 사용하여 SaaS 애플리케이션으로 사용자 프로비저닝 및 프로비전 해제 자동화](../app-provisioning/user-provisioning.md)를 참조하세요.

## <a name="capabilities-supported"></a>지원되는 기능
> [!div class="checklist"]
> * Playvox에서 사용자 만들기
> * Playvox에서 더 이상 액세스할 필요가 없는 사용자 제거
> * Azure AD와 Playvox 간 사용자 특성을 동기화된 상태로 유지

## <a name="prerequisites"></a>필수 구성 요소

이 자습서의 시나리오에서는 사용자에게 이미 다음 필수 구성 요소가 있다고 가정합니다.

* [Azure AD 테넌트](../develop/quickstart-create-new-tenant.md)
* 프로비저닝을 구성할 수 있는 [권한](../roles/permissions-reference.md)이 있는 Azure AD의 사용자 계정. 예를 들어 계정에는 애플리케이션 관리자, 클라우드 애플리케이션 관리자, 애플리케이션 소유자 또는 전역 관리자 역할이 있을 수 있습니다.
* 슈퍼 관리자 권한이 있는 [Playvox](https://www.playvox.com)의 사용자 계정

## <a name="step-1-plan-your-provisioning-deployment"></a>1단계: 프로비저닝 배포 계획

1. [프로비저닝 서비스의 작동 방식](../app-provisioning/user-provisioning.md)에 대해 알아봅니다.

2. [프로비저닝 범위](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)에 포함될 사용자를 결정합니다.

3. [Azure AD와 Playvox 간에 매핑](../app-provisioning/customize-application-attributes.md)할 데이터를 결정합니다.

## <a name="step-2-configure-playvox-to-support-provisioning-by-using-azure-ad"></a>2단계: Azure AD를 사용하여 프로비저닝을 지원하도록 Playvox 구성

1. Playvox 관리 콘솔에 로그인하고 **설정 > API 키** 로 이동합니다.

2. **API 키 만들기** 를 선택합니다.

    ![Playvox 사용자 인터페이스에서 API 키 만들기 단추의 위치를 보여 주는 부분 스크린샷](media/playvox-provisioning-tutorial/create.png)

3. API 키에 대해 의미 있는 이름을 입력한 다음, **저장** 을 선택합니다. API 키가 생성되면 **닫기** 를 선택합니다.

4. 만든 API 키에서 **세부 정보** 아이콘을 선택합니다.

    ![Playvox 사용자 인터페이스에서 세부 정보 아이콘(돋보기 그림)의 위치를 보여 주는 부분 스크린샷](media/playvox-provisioning-tutorial/api.png)

5. **BASE64 KEY** 값을 복사하여 저장합니다. 나중에 Azure Portal에서 Playvox 애플리케이션의 **프로비저닝** 탭에 있는 **비밀 토큰** 텍스트 상자에 이 값을 입력해야 합니다.

    ![BASE64 KEY 값이 강조 표시된 세부 정보 API 키 메시지 상자의 스크린샷](media/playvox-provisioning-tutorial/token.png)

## <a name="step-3-add-playvox-from-the-azure-ad-application-gallery"></a>3단계: Azure AD 애플리케이션 갤러리에서 Playvox 추가

Playvox에 대한 프로비저닝 관리를 시작하려면 애플리케이션 갤러리로부터 Azure AD 테넌트에 Playvox를 추가합니다. 자세히 알아보려면 [빠른 시작: 애플리케이션을 Azure AD(Azure Active Directory) 테넌트에 추가](../manage-apps/add-application-portal.md)를 참조하세요.

이전에 Playvox에서 SSO(Single Sign-On)를 설정한 경우 동일한 애플리케이션을 사용할 수 있습니다. 그러나 통합을 처음으로 테스트할 때에는 별도의 앱을 만드는 것이 좋습니다.

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>4단계: 프로비저닝 범위에 있는 사용자 정의

Azure AD 프로비저닝 서비스를 사용하면 애플리케이션에 대한 할당이나 사용자 또는 그룹의 특성을 기준으로 프로비저닝되는 사용자의 범위를 지정할 수 있습니다. 할당을 기준으로 앱에 프로비저닝할 사용자의 범위를 지정하려면 [Azure Active Directory에서 앱에 대한 사용자 할당 관리](../manage-apps/assign-user-or-group-access-portal.md)를 참조하여 사용자와 그룹을 애플리케이션에 할당하는 방법을 알아봅니다. 사용자 또는 그룹의 특성만을 기준으로 프로비저닝할 사용자의 범위를 지정하려면 [범위 지정 필터를 사용한 특성 기반 애플리케이션 프로비저닝](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)에서 설명하는 대로 범위 지정 필터를 사용합니다.

다음 사항을 기억하세요.

* Playvox에 사용자를 할당할 때 기본 액세스 이외의 역할을 선택해야 합니다. 기본 액세스 역할이 있는 사용자는 프로비저닝에서 제외되고 프로비저닝 로그에 실질적으로 권한을 부여받지 않은 것으로 표시됩니다. 애플리케이션에서 사용할 수 있는 유일한 역할이 기본 액세스 역할인 경우 [애플리케이션 매니페스트를 업데이트](../develop/howto-add-app-roles-in-azure-ad-apps.md)하여 다른 역할을 추가할 수 있습니다.

* 소규모로 시작합니다. 모든 사용자에게 배포하기 전에 소수의 사용자 또는 그룹 집합으로 테스트합니다. 할당된 사용자 또는 그룹을 기반으로 프로비저닝 범위가 설정된 경우 앱에 하나 또는 두 개의 사용자 또는 그룹만 할당하여 집합의 크기를 제어할 수 있습니다. 프로비저닝 범위에 모든 사용자와 그룹이 포함된 경우 [특성 기반 범위 지정 필터](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)를 지정하여 테스트 집합의 크기를 제한할 수 있습니다.

## <a name="step-5-configure-automatic-user-provisioning-to-playvox"></a>5단계: Playvox에 대한 자동 사용자 프로비저닝 구성

이 섹션에서는 Azure AD의 사용자 또는 그룹 할당을 기반으로 사용자 또는 그룹을 만들고, 업데이트하고, 사용 해제하도록 Azure AD 프로비저닝 서비스를 구성하는 단계를 안내합니다.

Azure AD에서 Playvox에 대한 자동 사용자 프로비저닝을 구성하려면 다음을 수행합니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다. **엔터프라이즈 애플리케이션** 을 선택한 다음, **모든 애플리케이션** 을 선택합니다.

    ![엔터프라이즈 애플리케이션 및 모든 애플리케이션 항목이 강조 표시된 Azure Portal의 부분 스크린샷](common/enterprise-applications.png)

2. 애플리케이션 목록에서 **Playvox** 를 검색하여 선택합니다.

    ![애플리케이션 검색 상자가 강조 표시된 애플리케이션 목록의 부분 스크린샷](common/all-applications.png)

3. **프로비전** 탭을 선택합니다.

    ![프로비저닝 메뉴 항목을 보여 주는 부분 스크린샷](common/provisioning.png)

4. **프로비전 모드** 를 **자동** 으로 설정합니다.

    ![프로비저닝 모드 드롭다운 목록 상자에서 선택한 자동 옵션을 보여 주는 프로비저닝 탭의 부분 스크린샷](common/provisioning-automatic.png)

5. **관리자 자격 증명** 섹션에서 Playvox **테넌트 URL** 을 다음과 같이 입력합니다.

    `https://{tenant}.playvox.com/scim/v1`

    앞서 2단계에서 복사한 **비밀 토큰** 을 입력합니다. 그런 다음, **연결 테스트** 를 선택하여 Azure AD에서 Playvox에 연결할 수 있는지 확인합니다. 연결에 실패하면 Playvox 계정에 관리자 권한이 있는지 확인하고 다시 시도합니다.

    ![테넌트 URL과 비밀 토큰 텍스트 상자가 화면에 나타나고 연결 테스트 링크가 강조 표시된 관리자 자격 증명 섹션을 보여 주는 부분 스크린샷](common/provisioning-testconnection-tenanturltoken.png)

6. **알림 메일** 텍스트 상자에 프로비저닝 오류 알림을 받을 사용자 또는 그룹의 메일 주소를 입력합니다. 그런 다음, **오류가 발생할 경우 이메일 알림 보내기** 확인란을 선택합니다.

    ![알림 메일 텍스트 상자와 메일 알림 확인란을 보여 주는 부분 스크린샷](common/provisioning-notification-email.png)

7. **저장** 을 선택합니다.

8. **매핑** 섹션에서 **Azure Active Directory 사용자를 Playvox에 동기화** 를 선택합니다.

9. **특성 매핑** 섹션에서 Azure AD에서 Playvox로 동기화되는 사용자 특성을 검토합니다. **일치** 속성으로 선택한 특성은 업데이트 작업을 위해 Playvox의 사용자 계정을 일치시키는 데 사용됩니다. [일치하는 대상 특성](../app-provisioning/customize-application-attributes.md)을 변경하려는 경우 Playvox API에서 해당 특성을 기준으로 하는 사용자 필터링을 지원하는지 확인해야 합니다. 변경 내용을 커밋하려면 **저장** 을 선택합니다.

   |attribute|Type|필터링에 지원됨|
   |---|---|---|
   |userName|String|&check;|
   |활성|부울|
   |displayName|String|
   |emails[type eq "work"].value|String|
   |name.givenName|String|
   |name.familyName|String|
   |name.formatted|String|
   |externalId|String|

10. 범위 지정 필터를 구성하려면 [범위 지정 필터 자습서](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)의 지침을 참조하세요.

11. Azure AD 프로비저닝 서비스를 Playvox에 사용하도록 설정하려면 **설정** 섹션에서 **프로비저닝 상태** 를 **켜짐** 으로 변경합니다.

    ![켜짐으로 설정된 프로비저닝 상태를 보여 주는 설정 섹션의 부분 스크린샷](common/provisioning-toggle-on.png)

12. **설정** 섹션의 **범위** 에서 원하는 값을 선택하여 Playvox에 프로비저닝할 사용자 또는 그룹을 정의합니다.

    ![범위 드롭다운 목록 상자를 보여 주는 설정 섹션의 부분 스크린샷](common/provisioning-scope.png)

13. 프로비저닝할 준비가 되면 **저장** 을 선택합니다.

    ![저장 및 취소 옵션을 보여 주는 부분 스크린샷](common/provisioning-configuration-save.png)

이 작업은 **설정** 의 **범위** 섹션에 정의된 모든 사용자 및/또는 그룹의 초기 동기화 주기를 시작합니다. 초기 주기는 이후의 주기보다 오래 걸립니다. Azure AD 프로비저닝 서비스가 실행되고 있으면 약 40분 간격으로 이후 주기가 진행됩니다.

## <a name="step-6-monitor-your-deployment"></a>6단계: 배포 모니터링

프로비저닝이 구성되면 다음 리소스를 사용하여 배포를 모니터링합니다.

* [프로비저닝 로그](../reports-monitoring/concept-provisioning-logs.md)를 사용하여 어떤 사용자가 성공적으로 프로비저닝되었는지 여부를 확인합니다.
* [진행률 표시줄](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md)을 통해 프로비저닝 주기 상태와 완료 정도를 확인합니다.
* 프로비저닝 구성이 비정상 상태로 보이면 애플리케이션이 격리됩니다. 격리 상태에 대한 자세한 내용은 [격리 상태의 애플리케이션 프로비저닝](../app-provisioning/application-provisioning-quarantine-status.md)을 참조하세요.

## <a name="additional-resources"></a>추가 리소스

* [엔터프라이즈 앱에 대한 사용자 계정 프로비전 관리](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Azure Active Directory로 애플리케이션 액세스 및 Single Sign-On을 구현하는 방법](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>다음 단계

* [프로비저닝 작업에 대한 로그를 검토하고 보고서를 받아보는 방법을 알아봅니다](../app-provisioning/check-status-user-account-provisioning.md).