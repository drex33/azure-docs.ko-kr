---
title: '자습서: Azure Active Directory를 사용하여 자동으로 사용자를 프로비저닝하도록 SolarWinds Service Desk(이전 명칭은 Samanage) 구성 | Microsoft Docs'
description: 사용자 계정을 Azure AD에서 SolarWinds Service Desk(이전 명칭은 Samanage)로 자동으로 프로비저닝 및 프로비저닝을 해제하는 방법을 알아봅니다.
services: active-directory
author: twimmers
writer: twimmers
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/13/2020
ms.author: thwimmer
ms.openlocfilehash: 3999575e13a1bb12478f6a9270dafa7c18c22f35
ms.sourcegitcommit: 9339c4d47a4c7eb3621b5a31384bb0f504951712
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/14/2021
ms.locfileid: "113759647"
---
# <a name="tutorial-configure-solarwinds-service-desk-previously-samanage-for-automatic-user-provisioning"></a>자습서: 자동으로 사용자를 프로비저닝하도록 SolarWinds Service Desk(이전 명칭은 Samanage) 구성

이 자습서에서는 자동으로 사용자를 프로비저닝하려면 SolarWinds Service Desk(이전 명칭은 Samanage) 및 Azure AD(Azure Active Directory)에서 수행해야 하는 단계를 설명합니다. 구성이 완료되면 Azure AD는 Azure AD 프로비저닝 서비스를 사용하여 사용자 및 그룹을 [SolarWinds Service Desk](https://www.samanage.com/pricing/)(이전 명칭은 Samanage)로 자동으로 프로비저닝 및 프로비저닝을 해제합니다. 이 서비스의 기능, 작동 방법 및 질문과 대답에 대한 중요한 내용은 [Azure Active Directory를 사용하여 SaaS 애플리케이션의 사용자를 자동으로 프로비저닝 및 프로비저닝 해제](../app-provisioning/user-provisioning.md)를 참조하세요.

## <a name="migrate-to-the-new-solarwinds-service-desk-application"></a>새 SolarWinds Service Desk 애플리케이션으로 마이그레이션

SolarWinds Service Desk와 통합된 기존 시스템이 있는 경우 다음 섹션에서 예정된 변경 내용을 확인하세요. SolarWinds Service Desk를 처음으로 설정하는 경우 이 섹션을 건너뛰고 **지원되는 기능** 으로 이동할 수 있습니다.

#### <a name="whats-changing"></a>변경 내용은?

* Azure AD 쪽의 변경 내용: Samange에서 사용자를 프로비저닝하는 권한 부여 방법은 현재까지 **기본 권한 부여** 입니다. 곧 권한 부여 방법이 **장기 비밀 토큰** 으로 변경됩니다.


#### <a name="what-do-i-need-to-do-to-migrate-my-existing-custom-integration-to-the-new-application"></a>기존 사용자 지정 통합을 새 애플리케이션으로 마이그레이션하려면 어떻게 해야 하나요?

통합된 기존 SolarWinds Service Desk가 있고 해당 관리자 자격 증명이 유효한 경우 **아무 것도 할 필요가 없습니다**. 고객은 자동으로 새 애플리케이션으로 마이그레이션됩니다. 이 프로세스는 전적으로 백그라운드에서 수행됩니다. 기존 자격 증명이 만료되거나 애플리케이션에 대한 액세스 권한을 다시 부여해야 하는 경우에는 장기 비밀 토큰을 생성해야 합니다. 새 토큰을 생성하려면 이 문서의 2단계를 참조하세요.


#### <a name="how-can-i-tell-if-my-application-has-been-migrated"></a>애플리케이션이 마이그레이션되었는지 어떻게 알 수 있나요? 

애플리케이션이 마이그레이션되면 **관리자 자격 증명** 섹션의 **관리 사용자 이름** 및 **관리자 암호** 필드가 단일 **비밀 토큰** 필드로 바뀝니다.

## <a name="capabilities-supported"></a>지원되는 기능

> [!div class="checklist"]
> * SolarWinds Service Desk에서 사용자 만들기
> * 사용자에게 더 이상 액세스 권한이 필요 없으면 SolarWinds Service Desk에서 사용자 제거
> * Azure AD와 SolarWinds Service Desk 간에 사용자 특성을 동기화된 상태로 유지
> * SolarWinds Service Desk에서 그룹 및 그룹 구성원 프로비저닝
> * SolarWinds Service Desk [Single Sign-On](./samanage-tutorial.md)(권장)

## <a name="prerequisites"></a>사전 요구 사항

이 자습서에 설명된 시나리오에서는 사용자에게 이미 다음 필수 구성 요소가 있다고 가정합니다.

* [Azure AD 테넌트](../develop/quickstart-create-new-tenant.md) 
* 프로비저닝을 구성할 [권한](../roles/permissions-reference.md)이 있는 Azure AD의 사용자 계정(예: 애플리케이션 관리자, 클라우드 애플리케이션 관리자, 애플리케이션 소유자 또는 전역 관리자). 
* Professional 패키지가 포함된 [SolarWinds Service Desk 테넌트](https://www.samanage.com/pricing/)
* 관리자 권한이 있는 SolarWinds Service Desk의 사용자 계정

> [!Note]
> 역할을 가져올 때 Azure Active Directory에서 역할을 수동으로 편집하면 안 됩니다.

## <a name="step-1-plan-your-provisioning-deployment"></a>1단계. 프로비저닝 배포 계획
1. [프로비저닝 서비스의 작동 방식](../app-provisioning/user-provisioning.md)에 대해 알아봅니다.
2. [프로비저닝 범위](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)에 있는 사용자를 결정합니다.
3. [Azure AD와 SolarWinds Service Desk 간에 매핑](../app-provisioning/customize-application-attributes.md)할 데이터를 결정합니다. 

## <a name="step-2-configure-solarwinds-service-desk-to-support-provisioning-with-azure-ad"></a>2단계. Azure AD에서 프로비저닝을 지원하도록 SolarWinds Service Desk 구성

인증에 사용할 비밀 토큰을 생성하려면 [자습서: API 통합을 위한 토큰 인증](https://help.samanage.com/s/article/Tutorial-Tokens-Authentication-for-API-Integration-1536721557657)을 참조하세요.

## <a name="step-3-add-solarwinds-service-desk-from-the-azure-ad-application-gallery"></a>3단계: Azure AD 애플리케이션 갤러리에서 SolarWinds Service Desk 추가

Azure AD 애플리케이션 갤러리에서 SolarWinds Service Desk를 추가하여 SolarWinds Service Desk에 대한 프로비저닝 관리를 시작합니다. 이전에 SolarWinds Service Desk에 SSO를 사용하도록 설정한 경우 동일한 애플리케이션을 사용해도 됩니다. 그러나 처음 통합을 테스트하는 경우 별도의 앱을 만드는 것이 좋습니다. [여기](../manage-apps/add-application-portal.md)를 클릭하여 갤러리에서 애플리케이션을 추가하는 방법에 대해 자세히 알아봅니다. 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>4단계. 프로비저닝 범위에 있는 사용자 정의 

Azure AD 프로비저닝 서비스를 사용하면 애플리케이션에 대한 할당 또는 사용자/그룹의 특성을 기반으로 프로비저닝되는 사용자의 범위를 지정할 수 있습니다. 할당을 기준으로 앱에 프로비저닝할 사용자의 범위를 선택하려면 다음 [단계](../manage-apps/assign-user-or-group-access-portal.md)를 사용하여 애플리케이션에 사용자 및 그룹을 할당할 수 있습니다. 사용자 또는 그룹의 특성만을 기준으로 프로비저닝할 사용자의 범위를 선택하려면 [여기](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md) 설명된 대로 범위 지정 필터를 사용할 수 있습니다. 

* SolarWinds Service Desk에 사용자 및 그룹을 할당할 때 **기본 액세스** 가 아닌 다른 역할을 선택해야 합니다. 기본 액세스 역할이 있는 사용자는 프로비저닝에서 제외되고 프로비저닝 로그에 실질적으로 권한을 부여받지 않은 것으로 표시됩니다. 애플리케이션에서 사용할 수 있는 유일한 역할이 기본 액세스 역할인 경우에는 [애플리케이션 매니페스트를 업데이트](../develop/howto-add-app-roles-in-azure-ad-apps.md)하여 역할을 더 추가할 수 있습니다. 

* 소규모로 시작합니다. 모든 사용자에게 배포하기 전에 소수의 사용자 및 그룹 집합으로 테스트합니다. 할당된 사용자 및 그룹으로 프로비저닝 범위가 설정된 경우 앱에 하나 또는 두 개의 사용자 또는 그룹을 할당하여 범위를 제어할 수 있습니다. 모든 사용자 및 그룹으로 범위가 설정된 경우 [특성 기반 범위 지정 필터](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)를 지정할 수 있습니다. 


## <a name="step-5-configure-automatic-user-provisioning-to-solarwinds-service-desk"></a>5단계. SolarWinds Service Desk에 대한 자동 사용자 프로비저닝 구성 

이 섹션에서는 Azure AD의 사용자 및/또는 그룹 할당에 따라 TestApp에서 사용자 및/또는 그룹을 만들고, 업데이트하고, 사용 해제하도록 Azure AD 프로비저닝 서비스를 구성하는 단계를 안내합니다.

### <a name="to-configure-automatic-user-provisioning-for-solarwinds-service-desk-in-azure-ad"></a>Azure AD에서 SolarWinds Service Desk에 대한 자동 사용자 프로비저닝을 구성하려면 다음을 수행합니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다. **엔터프라이즈 애플리케이션**, **모든 애플리케이션** 을 차례로 선택합니다.

    ![엔터프라이즈 애플리케이션 블레이드](common/enterprise-applications.png)

2. 애플리케이션 목록에서 **SolarWinds Service Desk** 를 선택합니다.

3. **프로비전** 탭을 선택합니다.

    ![[프로비저닝] 탭이 선택된 것을 보여주는 스크린샷](common/provisioning.png)

4. **프로비전 모드** 를 **자동** 으로 설정합니다.

    ![[프로비저닝 모드]가 [자동]으로 설정된 것을 보여주는 스크린샷](common/provisioning-automatic.png)

5. **관리자 자격 증명** 섹션 아래의 **테넌트 URL** 에 `https://api.samanage.com`을 입력합니다.  **비밀 토큰** 에 이전에 검색된 비밀 토큰 값을 입력합니다. **연결 테스트** 를 선택하여 Azure AD에서 SolarWinds Service Desk에 연결할 수 있는지 확인합니다. 연결에 실패하면 SolarWinds Service Desk 계정에 관리자 권한이 있는지 확인하고 다시 시도합니다.

    ![[연결 테스트] 단추가 선택된 것을 보여주는 스크린샷](./media/samanage-provisioning-tutorial/provisioning.png)

6. **알림 이메일** 필드에 프로비저닝 오류 알림을 받을 개인 또는 그룹의 메일 주소를 입력하고, **오류가 발생할 경우 메일 알림 보내기** 확인란을 선택합니다.

    ![알림 이메일](common/provisioning-notification-email.png)

7. **저장** 을 선택합니다.

8. **매핑** 섹션에서 **Azure Active Directory 사용자를 SolarWinds Service Desk에 동기화** 를 선택합니다.

9. **특성 매핑** 섹션에서, Azure AD에서 SolarWinds Service Desk로 동기화되는 사용자 특성을 검토합니다. **일치** 속성으로 선택한 특성은 업데이트 작업 시 SolarWinds Service Desk의 사용자 계정을 일치시키는 데 사용됩니다. [일치하는 대상 특성](../app-provisioning/customize-application-attributes.md)을 변경하는 경우 SolarWinds Service Desk API에서 해당 특성에 따라 사용자 필터링을 지원하는지 확인해야 합니다. **저장** 단추를 선택하여 변경 내용을 커밋합니다.

      ![Samange 사용자 매핑](./media/samanage-provisioning-tutorial/user-attributes.png)

10. **매핑** 섹션에서 **Azure Active Directory 그룹을 SolarWinds Service Desk에 동기화** 를 선택합니다.

11. **특성 매핑** 섹션에서, Azure AD에서 SolarWinds Service Desk로 동기화되는 그룹 특성을 검토합니다. **일치** 속성으로 선택한 특성은 업데이트 작업 시 SolarWinds Service Desk의 그룹을 일치시키는 데 사용됩니다. **저장** 단추를 선택하여 변경 내용을 커밋합니다.

      ![Samange 그룹 매핑](./media/samanage-provisioning-tutorial/group-attributes.png)

12. 범위 지정 필터를 구성하려면 [범위 지정 필터 자습서](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)에서 제공하는 다음 지침을 참조합니다.

13. SolarWinds Service Desk에 Azure AD 프로비저닝 서비스를 사용하도록 설정하려면 **설정** 섹션에서 **프로비전 상태** 를 **켜기** 로 변경합니다.

    ![프로비전 상태 켜기로 전환](common/provisioning-toggle-on.png)

14. **설정** 의 **범위** 섹션에서 원하는 값을 선택하여 SolarWinds Service Desk에 프로비저닝하려는 사용자 및/또는 그룹을 정의합니다.

    ![프로비전 범위](common/provisioning-scope.png)

15. 프로비저닝할 준비가 되면 **저장** 을 선택합니다.

    ![프로비전 구성 저장](common/provisioning-configuration-save.png)

이 작업은 **설정** 의 **범위** 섹션에 정의된 모든 사용자 및/또는 그룹의 초기 동기화 주기를 시작합니다. 초기 주기는 Azure AD 프로비저닝 서비스가 실행되는 동안 약 40분마다 발생하는 후속 주기보다 더 많은 시간이 걸립니다. 

## <a name="step-6-monitor-your-deployment"></a>6단계. 배포 모니터링
프로비저닝을 구성한 후에는 다음 리소스를 사용하여 배포를 모니터링합니다.

1. [프로비저닝 로그](../reports-monitoring/concept-provisioning-logs.md)를 사용하여 어떤 사용자가 성공적으로 프로비저닝되었는지 확인합니다.
2. [진행률 표시줄](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md)을 통해 프로비저닝 주기 상태와 완료 정도를 확인합니다.
3. 프로비저닝 구성이 비정상 상태로 보이면 애플리케이션이 격리됩니다. 격리 상태에 대한 자세한 내용은 [여기](../app-provisioning/application-provisioning-quarantine-status.md)를 참조하세요.

## <a name="connector-limitations"></a>커넥터 제한 사항

**모든 사용자 및 그룹 동기화** 옵션을 선택하고 SolarWinds Service Desk **역할** 특성의 값을 구성하면 **Null인 경우 기본값** 상자가

- {"displayName":"role"} 형식으로 표시됩니다. 여기서 role은 원하는 기본값입니다.

## <a name="change-log"></a>로그 변경

* 2020/09/14 - `https://github.com/ravitmorales` 에 따라 두 SaaS 자습서의 회사 이름이 Samanage에서 SolarWinds Service Desk로 변경되었습니다(이전 명칭은 Samanage).
* 2020/04/22 - 권한 부여 방법이 기본 인증에서 장기 비밀 토큰으로 업데이트되었습니다.

## <a name="additional-resources"></a>추가 리소스

* [엔터프라이즈 앱에 대한 사용자 계정 프로비전 관리](../app-provisioning/configure-automatic-user-provisioning-portal.md)

## <a name="next-steps"></a>다음 단계

* [프로비저닝 작업에 대한 로그를 검토하고 보고서를 받아보는 방법을 알아봅니다](../app-provisioning/check-status-user-account-provisioning.md).
