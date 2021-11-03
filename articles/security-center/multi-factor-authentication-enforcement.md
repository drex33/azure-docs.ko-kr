---
title: Microsoft Defender for Cloud의 MFA 보안 권장 사항
description: 클라우드 용 Microsoft Defender를 사용 하 여 Azure 구독에 multi-factor authentication을 적용 하는 방법을 알아봅니다.
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: conceptual
ms.date: 03/10/2021
ms.author: memildin
ms.custom: ignite-fall-2021
ms.openlocfilehash: 3ba741d97e3a9537b64957ce419b06208fd2a528
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131102812"
---
# <a name="manage-multi-factor-authentication-mfa-enforcement-on-your-subscriptions"></a>구독에 대한 MFA(다단계 인증) 적용 관리

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

사용자를 인증하는 데 암호만 사용하는 경우 공격 벡터를 열어 둡니다. 사용자는 종종 취약한 암호를 사용하거나 여러 서비스에 대해 다시 사용합니다. [MFA](https://www.microsoft.com/security/business/identity/mfa)를 사용하도록 설정하면 계정이 더 안전하게 유지되고 사용자는 SSO(Single Sign-On)를 사용하여 거의 모든 애플리케이션에서 인증을 받을 수 있습니다.

조직에서 소유하는 라이선스에 따라 Azure AD(Active Directory) 사용자에 대해 MFA를 사용하도록 설정하는 방법에는 여러 가지가 있습니다. 이 페이지에서는 클라우드의 Microsoft Defender 컨텍스트에서 각에 대 한 세부 정보를 제공 합니다.


## <a name="mfa-and-microsoft-defender-for-cloud"></a>클라우드 용 MFA 및 Microsoft Defender 

클라우드의 Defender는 높은 값을 MFA에 배치 합니다. 보안 점수를 가장 많이 제공하는 보안 제어는 **MFA를 사용하도록 설정** 하는 것입니다. 

MFA 사용 제어의 권장 사항은 구독 사용자에 대해 권장되는 방법을 충족하고 있는지 확인합니다.

- 구독에서 소유자 권한이 있는 계정에 MFA를 사용하도록 설정해야 합니다.
- 구독에서 쓰기 권한이 있는 계정에 MFA를 사용하도록 설정해야 합니다.

MFA를 사용 하도록 설정 하는 세 가지 방법, 즉 보안 기본값, 사용자별 할당, CA (조건부 액세스) 정책의 두 가지 권장 사항을 준수할 수 있습니다. 이러한 각 옵션은 아래에 설명되어 있습니다.

### <a name="free-option---security-defaults"></a>무료 옵션 - 보안 기본값
무료 버전의 Azure AD를 사용하는 경우에는 [보안 기본값](../active-directory/fundamentals/concept-fundamentals-security-defaults.md)을 사용하여 테넌트에서 다단계 인증을 사용하도록 설정합니다.

### <a name="mfa-for-microsoft-365-business-e3-or-e5-customers"></a>Microsoft 365 Business, E3 또는 E5 고객에 대한 MFA
Microsoft 365를 사용하는 고객은 **사용자별 할당** 을 사용할 수 있습니다. 이 시나리오에서는 모든 로그인 이벤트에서 모든 사용자에 대해 Azure AD MFA를 사용하거나 사용하지 않도록 설정합니다. 사용자의 하위 집합 또는 특정 시나리오에서 다단계 인증을 사용하도록 설정할 수 있는 기능은 없으며, Office 365 포털을 통해 관리할 수 있습니다.

### <a name="mfa-for-azure-ad-premium-customers"></a>Azure AD Premium 고객에 대한 MFA
향상된 사용자 환경을 위해 **CA(조건부 액세스) 정책** 옵션에 대해 Azure AD Premium P1 또는 P2로 업그레이드합니다. CA 정책을 구성하려면 [Azure AD(Active Directory) 테넌트 권한](../active-directory/roles/permissions-reference.md)이 필요합니다.

CA 정책은 다음을 수행해야 합니다.
- MFA 적용
- Microsoft Azure 관리 앱 ID(797f4846-ba00-4fd7-ba43-dac1f8f63013) 또는 모든 앱 포함
- Microsoft Azure 관리 앱 ID를 제외하지 않음

**Azure AD Premium P1** 고객은 Azure AD CA를 사용하여 비즈니스 요구 사항에 맞게 특정 시나리오 또는 이벤트 중에 사용자에게 다단계 인증을 사용할지 묻는 메시지를 표시할 수 있습니다. 이 기능을 포함하는 다른 라이선스: Enterprise Mobility + Security E3, Microsoft 365 F1 및 Microsoft 365 E3

**Azure AD Premium P2** 는 가장 강력한 보안 기능과 향상된 사용자 환경을 제공합니다. 이 라이선스는 Azure AD Premium P1 기능에 [위험 기반 조건부 액세스](../active-directory/conditional-access/howto-conditional-access-policy-risk.md)를 추가합니다. 위험 기반 CA는 사용자의 패턴에 적응하고 다단계 인증 프롬프트를 최소화합니다. 이 기능을 포함하는 다른 라이선스: Enterprise Mobility + Security E5 또는 Microsoft 365 E5

[Azure 조건부 액세스 설명서](../active-directory/conditional-access/overview.md)에서 자세히 알아보세요.

## <a name="identify-accounts-without-multi-factor-authentication-mfa-enabled"></a>MFA(다단계 인증)를 사용하지 않는 계정 식별

Defender for Cloud 권장 사항 세부 정보 페이지에서 또는 Azure Resource Graph를 사용 하 여 MFA를 사용 하지 않는 사용자 계정 목록을 볼 수 있습니다.

### <a name="view-the-accounts-without-mfa-enabled-in-the-azure-portal"></a>Azure Portal에서 MFA를 사용하지 않는 계정 보기
권장 사항 세부 정보 페이지의 **비정상 리소스** 목록에서 구독을 선택하거나 **작업 수행** 을 선택하면 목록이 표시됩니다.

### <a name="view-the-accounts-without-mfa-enabled-using-azure-resource-graph"></a>Azure Resource Graph를 사용하여 MFA를 사용하지 않는 계정 보기
MFA를 사용하지 않는 계정을 확인하려면 다음 Azure Resource Graph 쿼리를 사용합니다. 이 쿼리는 "구독에서 소유자 권한이 있는 계정에 MFA를 사용하도록 설정해야 합니다."라는 권장 사항의 모든 비정상 리소스 - 계정을 반환합니다. 

1. **Azure Resource Graph Explorer** 를 엽니다.

    :::image type="content" source="./media/multi-factor-authentication-enforcement/opening-resource-graph-explorer.png" alt-text="Azure Resource Graph Explorer 시작** 권장 사항 페이지" :::

1. 다음 쿼리를 입력하고 **쿼리 실행** 을 선택합니다.

    ```kusto
    securityresources
     | where type == "microsoft.security/assessments"
     | where properties.displayName == "MFA should be enabled on accounts with owner permissions on your subscription"
     | where properties.status.code == "Unhealthy"
    ```

1. `additionalData` 속성은 MFA가 적용되지 않은 계정에 대한 계정 개체 ID의 목록을 표시합니다. 

    > [!NOTE]
    > 계정은 계정 소유자의 개인 정보를 보호하기 위해 계정 이름이 아닌 개체 ID로 표시됩니다.

> [!TIP]
> 또는 클라우드 용 Defender REST API 메서드 [평가-Get](/rest/api/securitycenter/assessments/get)을 사용할 수 있습니다.


## <a name="faq---mfa-in-defender-for-cloud"></a>FAQ-클라우드에 대 한 Defender의 MFA

- [이미 CA 정책을 사용 하 여 MFA를 적용 하 고 있습니다. 클라우드 권장 사항에 대 한 Defender가 여전히 제공 되는 이유는 무엇 인가요?](#were-already-using-ca-policy-to-enforce-mfa-why-do-we-still-get-the-defender-for-cloud-recommendations)
- [타사 MFA 도구를 사용 하 여 MFA를 적용 하 고 있습니다. 클라우드 권장 사항에 대 한 Defender가 여전히 제공 되는 이유는 무엇 인가요?](#were-using-a-third-party-mfa-tool-to-enforce-mfa-why-do-we-still-get-the-defender-for-cloud-recommendations)
- [클라우드에 대 한 사용 권한이 없는 사용자 계정을 "MFA 필요"로 표시 하는 이유는 무엇 인가요?](#why-does-defender-for-cloud-show-user-accounts-without-permissions-on-the-subscription-as-requiring-mfa)
- [PIM을 사용하여 MFA를 적용하고 있습니다. PIM 계정이 비규격으로 표시되는 이유는 무엇인가요?](#were-enforcing-mfa-with-pim-why-are-pim-accounts-shown-as-noncompliant)
- [일부 계정을 면제 또는 해제할 수 있나요?](#can-i-exempt-or-dismiss-some-of-the-accounts)
- [클라우드의 id 및 액세스 보호를 위해 Defender에 대 한 제한이 있나요?](#are-there-any-limitations-to-defender-for-clouds-identity-and-access-protections)

### <a name="were-already-using-ca-policy-to-enforce-mfa-why-do-we-still-get-the-defender-for-cloud-recommendations"></a>이미 CA 정책을 사용하여 MFA를 적용하고 있습니다. 클라우드 권장 사항에 대 한 Defender가 여전히 제공 되는 이유는 무엇 인가요?
권장 사항이 여전히 생성되는 이유를 조사하려면 MFA CA 정책에서 다음 구성 옵션을 확인합니다.

- MFA CA 정책의 **사용자** 섹션(또는 **그룹** 섹션의 그룹 중 하나)에 계정을 포함했습니다.
- Azure 관리 앱 ID(797f4846-ba00-4fd7-ba43-dac1f8f63013) 또는 모든 앱이 MFA CA 정책의 **앱** 섹션에 포함되어 있습니다.
- Azure 관리 앱 ID는 MFA CA 정책의 **앱** 섹션에서 제외되어 있지 않습니다.

### <a name="were-using-a-third-party-mfa-tool-to-enforce-mfa-why-do-we-still-get-the-defender-for-cloud-recommendations"></a>타사 MFA 도구를 사용하여 MFA를 적용하고 있습니다. 클라우드 권장 사항에 대 한 Defender가 여전히 제공 되는 이유는 무엇 인가요?
클라우드의 MFA 권장 사항에 대 한 Defender는 타사 MFA 도구 (예: DUO)를 지원 하지 않습니다.

권장 사항이 조직과 관련이 없는 경우에는 [보안 점수에서 리소스 및 권장 사항 제외](exempt-resource.md)에 설명된 대로 "완화됨"으로 표시하는 것이 좋습니다. [권장 사항을 사용하지 않도록 설정할](tutorial-security-policy.md#disable-security-policies-and-disable-recommendations) 수도 있습니다.

### <a name="why-does-defender-for-cloud-show-user-accounts-without-permissions-on-the-subscription-as-requiring-mfa"></a>클라우드에 대 한 사용 권한이 없는 사용자 계정을 "MFA 필요"로 표시 하는 이유는 무엇 인가요?
클라우드의 MFA 권장 사항에 대 한 Defender는 [AZURE RBAC](../role-based-access-control/role-definitions-list.md) 역할 및 [azure 클래식 구독 관리자](../role-based-access-control/classic-administrators.md) 역할을 참조 합니다. 이러한 역할이 있는 계정이 없는지 확인합니다.

### <a name="were-enforcing-mfa-with-pim-why-are-pim-accounts-shown-as-noncompliant"></a>PIM을 사용하여 MFA를 적용하고 있습니다. PIM 계정이 비규격으로 표시되는 이유는 무엇인가요?
클라우드의 MFA 권장 사항에 대 한 Defender는 현재 PIM 계정을 지원 하지 않습니다. 이러한 계정을 사용자 및 그룹 섹션의 CA 정책에 추가할 수 있습니다.

### <a name="can-i-exempt-or-dismiss-some-of-the-accounts"></a>일부 계정을 면제 또는 해제할 수 있나요?
MFA를 사용하지 않는 일부 계정을 제외하는 기능은 현재 지원되지 않습니다.  

### <a name="are-there-any-limitations-to-defender-for-clouds-identity-and-access-protections"></a>클라우드의 id 및 액세스 보호를 위해 Defender에 대 한 제한이 있나요?
클라우드 id 및 액세스 보호를 위한 Defender에는 몇 가지 제한 사항이 있습니다.

- 600개 이상의 계정을 사용하는 구독에는 ID 권장 사항을 사용할 수 없습니다. 이 경우 이러한 권장 사항은 "사용할 수 없는 평가" 아래에 나열됩니다.
- CSP(클라우드 솔루션 공급자) 파트너의 관리 에이전트에는 ID 권장 사항을 사용할 수 없습니다.
- ID 권장 사항은 PIM(Privileged Identity Management) 시스템을 사용하여 관리되는 계정을 식별하지 않습니다. PIM 도구를 사용하는 경우 **액세스 및 권한 관리** 제어에서 부정확한 결과가 표시될 수 있습니다.


## <a name="next-steps"></a>다음 단계
다른 Azure 리소스 유형에 적용되는 권장 사항에 대해 자세히 알아보려면 다음 문서를 참조하세요.

- [Microsoft Defender for Cloud에서 네트워크 보호](protect-network-resources.md)
