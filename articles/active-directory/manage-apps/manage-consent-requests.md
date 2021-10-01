---
title: Azure Active Directory에서 애플리케이션에 대한 동의 관리 및 동의 요청 평가
description: 사용자 동의가 사용하지 않도록 설정되거나 제한되는 경우 동의 요청을 관리하는 방법 및 Azure Active Directory 애플리케이션에 대한 테넌트 전체 관리자 동의에 대한 요청을 평가하는 방법에 대해 알아봅니다.
services: active-directory
author: davidmu1
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: how-to
ms.date: 08/25/2021
ms.author: davidmu
ms.reviewer: phsignor
ms.openlocfilehash: a6bf1ec1616d45edd08b943b55f2859d7942c908
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/03/2021
ms.locfileid: "123425855"
---
# <a name="managing-consent-to-applications-and-evaluating-consent-requests"></a>애플리케이션에 대한 동의 관리 및 동의 요청 평가

Microsoft는 사용자가 선택한 권한과 확인된 게시자의 앱에 대해서만 동의할 수 있도록 [사용자 동의를 제한](../../active-directory/manage-apps/configure-user-consent.md)할 것을 권장합니다. 이 정책을 충족하지 않는 앱의 경우 의사 결정 프로세스는 조직의 보안 및 ID 관리자 팀과 함께 중앙 집중화됩니다.

최종 사용자 동의를 사용하지 않도록 설정하거나 제한한 후에는 업무상 중요한 애플리케이션을 계속 사용할 수 있는 동시에 조직이 안전하게 유지되도록 하기 위해 몇 가지 중요한 고려 사항이 있습니다. 이러한 단계는 조직의 지원 팀과 IT 관리자에게 미치는 영향을 최소화하는 동시에 타사 애플리케이션에서 관리되지 않는 계정을 사용하지 못하도록 하는 데 중요합니다.

## <a name="process-changes-and-education"></a>변경 내용 및 교육 처리

 1. 사용자가 동의 화면에서 직접 관리자 승인을 요청할 수 있도록 [관리자 동의 워크플로](configure-admin-consent-workflow.md)를 사용하도록 설정하는 것이 좋습니다.

 2. 모든 관리자가 [권한 및 동의 프레임워크](../develop/consent-framework.md)를 이해하고, [동의 확인 프롬프트](../develop/application-consent-experience.md)가 작동하는 방법, [테넌트 전체 관리자 동의에 대한 요청을 평가](#evaluating-a-request-for-tenant-wide-admin-consent)하는 방법을 이해해야 합니다.
 3. 사용자가 애플리케이션에 대한 관리자 승인을 요청하고 필요한 경우 업데이트하도록 조직의 기존 프로세스를 검토합니다. 프로세스가 변경된 경우:
    * 관련 설명서, 모니터링, 자동화 등을 업데이트합니다.
    * 영향을 받는 모든 사용자, 개발자, 지원 팀 및 IT 관리자에게 프로세스 변경 사항을 전달합니다.

## <a name="auditing-and-monitoring"></a>감사 및 모니터링

1. 보증이 없거나 의심스러운 애플리케이션이 이전에 데이터에 대한 액세스 권한을 부여받지 않았는지 확인하기 위해 조직에서 [앱을 감사하고 권한을 부여](../../security/fundamentals/steps-secure-identity.md#audit-apps-and-consented-permissions)합니다.

2. OAuth 동의를 요청하는 의심스러운 애플리케이션에 대한 추가 모범 사례 및 보호를 위해 [Office 365의 불법 동의 검색 및 재구성](/microsoft-365/security/office-365-security/detect-and-remediate-illicit-consent-grants)을 검토합니다.

3. 조직에 적절한 라이선스가 있는 경우:

    * [Microsoft Cloud App Security의 OAuth 애플리케이션 감사 기능](/cloud-app-security/investigate-risky-oauth)을 추가로 사용합니다.
    * [Azure Monitor Workbooks를 사용하여 권한 및 동의 관련 작업을 모니터링](../reports-monitoring/howto-use-azure-monitor-workbooks.md)합니다. *동의 Insights* 통합 문서는 실패한 동의 요청 수에 따른 앱 보기를 제공합니다. 이는 관리자가 관리자 동의를 부여할지 여부를 검토하고 결정할 수 있도록 애플리케이션의 우선 순위를 지정하는 데 유용할 수 있습니다.

### <a name="additional-considerations-for-reducing-friction"></a>마찰을 줄이기 위한 추가 고려 사항

이미 사용 중인 신뢰할 수 있는 비즈니스에 중요한 애플리케이션에 대한 영향을 최소화하려면 많은 사용자 동의가 부여된 애플리케이션에 관리자의 동의를 사전에 부여하는 것이 좋습니다.

1. 로그인 로그 또는 동의 부여 작업을 기반으로 하여 이미 조직에 추가된 앱의 인벤토리를 사용합니다. PowerShell [스크립트](https://gist.github.com/psignoret/41793f8c6211d2df5051d77ca3728c09)를 사용하여 많은 수의 사용자 동의 부여로 애플리케이션을 쉽고 빠르게 검색할 수 있습니다.

2. 아직 관리자 동의가 부여되지 않은 상위 애플리케이션을 평가합니다.

   > [!IMPORTANT]
   > 조직에서 많은 사용자가 이미 동의한 경우에도 테넌트 전체 관리자 동의를 부여하기 전에 애플리케이션을 신중하게 평가합니다.

3. 승인된 각 애플리케이션에 대해 아래에서 설명하는 방법 중 하나를 사용하여 테넌트 전체 관리자 동의를 부여합니다.

4. 승인된 각 애플리케이션에 대해 [사용자 액세스를 제한](configure-user-consent.md)하는 것이 좋습니다.

## <a name="evaluating-a-request-for-tenant-wide-admin-consent"></a>테넌트 전체 관리자 동의에 대한 요청 평가

테넌트 전체 관리자 동의를 부여하는 것은 중요한 작업입니다.  전체 조직을 대신하여 사용 권한을 부여하고, 높은 권한의 작업을 시도하는 사용 권한을 포함할 수 있습니다. 예를 들면 역할 관리, 모든 사서함 또는 모든 사이트에 대한 모든 권한, 전체 사용자 가장이 있습니다.

테넌트 전체 관리자 동의를 부여하기 전에 허용되는 액세스 수준에 대해 애플리케이션 및 애플리케이션 게시자를 신뢰할 수 있는지 확인해야 합니다. 애플리케이션을 제어하는 사용자 및 애플리케이션이 권한을 요청하는 이유를 알지 못하는 경우 *동의를 부여하지 않습니다*.

다음 목록에서는 관리자 동의를 부여하는 요청을 평가할 때 고려해야 할 몇 가지 권장 사항을 제공합니다.

* **Microsoft ID 플랫폼의 [권한 및 동의 프레임워크](../develop/consent-framework.md)를 이해합니다.**

* **[위임 권한 및 애플리케이션 권한](../develop/v2-permissions-and-consent.md#permission-types) 간의 차이점을 이해합니다.**

   애플리케이션 권한을 통해 애플리케이션은 사용자 개입 없이 전체 조직의 데이터에 액세스할 수 있습니다. 위임된 권한을 통해 애플리케이션은 특정 시점에 애플리케이션에 로그인한 사용자를 대신하여 작업을 수행할 수 있습니다.

* **요청된 권한을 이해합니다.**

   애플리케이션에서 요청하는 권한은 [동의 확인 프롬프트](../develop/application-consent-experience.md)에 나열됩니다. 권한 제목을 확장하면 권한의 설명이 표시됩니다. 애플리케이션 권한에 대한 설명은 일반적으로 "로그인한 사용자가 없는 상태"로 끝을 맺습니다. 위임된 권한에 대한 설명은 일반적으로 "로그인한 사용자를 대신하여"로 끝을 맺습니다. Microsoft Graph API에 대한 권한은 [Microsoft Graph 권한 참조](/graph/permissions-reference)에 설명되어 있습니다. 노출된 권한을 이해하려면 다른 API에 대한 설명서를 참조하세요.

   요청된 권한을 이해하지 못하는 경우 *동의를 부여하지 않습니다*.

* **권한을 요청하는 애플리케이션 및 애플리케이션을 게시한 사용자를 이해합니다.**

   다른 애플리케이션처럼 보이는 악의적인 애플리케이션을 주의해야 합니다.

   애플리케이션 또는 해당 게시자의 적법성이 확실하지 않다면 *동의를 부여하지 않습니다*. 대신 추가적으로 확인합니다(예: 애플리케이션 게시자에게서 직접 확인).

* **요청된 권한이 애플리케이션에서 원하는 기능과 일치하는지 확인합니다.**

   예를 들어 SharePoint 사이트 관리를 제공하는 애플리케이션은 모든 사이트 모음을 읽기 위해 위임된 액세스 권한을 필요로 할 수 있지만 모든 사서함에 대한 전체 액세스 또는 디렉터리의 전체 가장 권한이 반드시 필요한 것은 아닙니다.

   애플리케이션에 필요한 것보다 많은 권한을 요청하는 것으로 의심되는 경우 *동의를 부여하지 않습니다*. 자세한 내용은 애플리케이션 게시자에 게 문의하세요.

## <a name="granting-consent-as-an-administrator"></a>관리자로서 권한 부여

### <a name="granting-tenant-wide-admin-consent"></a>테넌트 전체 관리자 동의 부여

Azure Portal, Azure AD PowerShell 또는 동의 확인 프롬프트 자체에서 테넌트 전체 관리자 동의를 부여하는 단계별 지침은 [애플리케이션에 대한 테넌트 전체 관리자 동의 부여](grant-admin-consent.md)를 참조하세요.

### <a name="granting-consent-on-behalf-of-a-specific-user"></a>특정 사용자를 대신하여 동의 부여

관리자는 전체 조직에 대한 동의를 부여하는 대신 [Microsoft Graph API](/graph/use-the-api)를 사용하여 단일 사용자 대신 위임된 권한에 대한 동의를 부여할 수도 있습니다. 자세한 내용은 [사용자를 대신하여 액세스 권한 가져오기](/graph/auth-v2-user)를 참조하세요.

## <a name="limiting-user-access-to-applications"></a>애플리케이션에 대한 사용자 액세스 제한

테넌트 전체 관리자 동의가 부여된 경우에도 애플리케이션에 대한 사용자 액세스는 계속 제한될 수 있습니다. 애플리케이션에 사용자 할당을 요구하는 방법에 대한 자세한 내용은 [사용자 및 그룹을 할당하는 방법](./assign-user-or-group-access-portal.md)을 참조하세요.

추가적인 복잡한 시나리오를 처리하는 방법을 비롯한 광범위한 개요는 [애플리케이션 액세스 관리에 Azure AD 사용](what-is-access-management.md)을 참조하세요.

## <a name="disable-all-future-user-consent-operations-to-any-application"></a>모든 애플리케이션에 대한 모든 이후 사용자 동의 작업을 사용하지 않도록 설정

전체 디렉터리에 대한 사용자 동의를 사용하지 않도록 설정하면 모든 애플리케이션에 대한 최종 사용자 동의가 방지됩니다. 관리자는 여전히 사용자의 동작에 동의할 수 있습니다. 애플리케이션 동의 및 동의하거나 동의하지 않을 수 있는 이유에 대해 자세히 알아보려면 [사용자 및 관리자 동의 이해하기](../develop/howto-convert-app-to-be-multi-tenant.md)를 참조하세요.

전체 디렉터리에서 모든 이후 사용자 동의 작업을 비활성화하려면 다음 단계를 따릅니다.

1. [**Azure Portal**](https://portal.azure.com/)을 열고 **전역 관리자** 권한으로 로그인합니다.
2. 왼쪽 주 탐색 메뉴의 맨 위에서 **모든 서비스** 를 클릭하여 **Azure Active Directory 확장** 을 엽니다.
3. 필터 검색 상자에 **“Azure Active Directory**”를 입력하고 **Azure Active Directory** 항목을 선택합니다.
4. **엔터프라이즈 애플리케이션** 을 선택하고 **관리** 섹션에서 **사용자 설정** 을 선택합니다.
:::image type="content" source="media/manage-consent-requests/disable-user-consent-operations.png" alt-text="모든 앱에 대해 사용자 동의 작업 사용 안 함":::
5. **사용자가 앱이 사용자 대신 회사 데이터에 액세스하는 것에 동의할 수 있음** 토글을 **아니요** 로 설정하여 모든 이후 사용자 동의 작업을 사용하지 않도록 설정하고 **저장** 단추를 클릭합니다.

## <a name="next-steps"></a>다음 단계

* [ID 인프라를 보호하기 위한 5단계](../../security/fundamentals/steps-secure-identity.md#before-you-begin-protect-privileged-accounts-with-mfa)
* [관리자 동의 워크플로 구성](configure-admin-consent-workflow.md)
* [최종 사용자가 애플리케이션에 동의하는 방법 구성](configure-user-consent.md)
* [Microsoft ID 플랫폼의 권한 및 동의](../develop/v2-permissions-and-consent.md)
