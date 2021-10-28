---
title: ID 보호 및 B2B 사용자 - Azure Active Directory
description: B2B 사용자에 대해 ID 보호를 사용합니다.
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: conceptual
ms.date: 05/03/2021
ms.author: joflore
author: MicrosoftGuyJFlo
manager: karenhoran
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0f3a5efbb9c990173c7012b9ff6f938fed33f1ad
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124734565"
---
# <a name="identity-protection-and-b2b-users"></a>ID 보호 및 B2B 사용자

ID 보호는 Azure AD 사용자의 손상된 자격 증명을 검색합니다. 사용자의 자격 증명이 손상된 것으로 검색되면 다른 사용자가 해당 암호를 가지고 있고 이를 불법적으로 사용할 수 있음을 의미합니다. 계정에 대한 추가 위험을 방지하려면 악의적인 행위자가 손상된 암호를 더 이상 사용할 수 없도록 암호를 안전하게 다시 설정해야 합니다. ID 보호는 손상될 수 있는 계정을 "위험"으로 표시합니다.

조직 자격 증명을 사용하여 게스트 사용자로 다른 조직에 로그인할 수 있습니다. 이 프로세스를 [B2B(business-to-business) 협업](../external-identities/what-is-b2b.md)이라고 합니다. 조직은 사용자의 자격 증명이 [위험](concept-identity-protection-risks.md)으로 간주되는 경우 해당 사용자가 로그인하지 못하도록 차단하는 정책을 구성할 수 있습니다. 계정이 위험에 노출되어 게스트 사용자로 다른 조직에 로그인할 수 없도록 차단된 경우 다음 단계를 사용하여 계정을 자체 수정할 수 있습니다. 조직에서 셀프 서비스 암호 재설정을 사용하도록 설정하지 않은 경우 관리자가 수동으로 계정을 수정해야 합니다.

## <a name="how-to-unblock-your-account"></a>계정 차단을 해제하는 방법 

게스트 사용자로 다른 조직에 로그인하려고 하고 위험으로 인해 차단된 경우 "계정이 차단되었습니다. 계정에 의심스러운 활동이 검색되었습니다."라는 차단 메시지가 표시됩니다. 

![게스트 계정이 차단되었습니다. 조직의 관리자에게 문의하세요.](./media/concept-identity-protection-b2b/risky-guest-user-blocked.png)

조직에서 이 계정을 사용하도록 설정하는 경우 셀프 서비스 암호 재설정을 사용하여 계정 차단을 해제하고 자격 증명을 안전한 상태로 되돌릴 수 있습니다.
1. [암호 재설정 포털](https://passwordreset.microsoftonline.com/)로 이동하여 암호 재설정을 시작합니다. 셀프 서비스 암호 재설정을 계정에 사용하도록 설정되어 있지 않고 계속 진행할 수 없는 경우 [아래](#how-to-remediate-a-users-risk-as-an-administrator) 정보를 사용하여 IT 관리자에게 문의하세요.
2. 셀프 서비스 암호 재설정을 계정에 사용하도록 설정된 경우 암호를 변경하기 전에 보안 방법을 사용하여 ID를 확인하라는 메시지가 표시됩니다. 도움이 필요하면 [회사 또는 학교 암호 재설정](https://support.microsoft.com/account-billing/reset-your-work-or-school-password-using-security-info-23dde81f-08bb-4776-ba72-e6b72b9dda9e) 문서를 참조하세요.
3. 암호를 성공적으로 안전하게 다시 설정하면 사용자 위험이 수정됩니다. 이제 게스트 사용자로 로그인을 다시 시도할 수 있습니다.

암호를 다시 설정한 후에도 위험으로 인해 게스트 사용자로 차단되는 경우 조직의 IT 관리자에게 문의하세요.

## <a name="how-to-remediate-a-users-risk-as-an-administrator"></a>관리자가 사용자의 위험을 수정하는 방법

ID 보호는 Azure AD 테넌트에 위험한 사용자를 자동으로 검색합니다. 이전에 ID 보호 보고서를 확인하지 않은 경우 위험한 사용자가 많이 있을 수 있습니다. 리소스 테넌트는 게스트 사용자에게 사용자 위험 정책을 적용할 수 있으므로 사용자가 이전에 자신의 위험한 상태를 인식하지 못한 경우에도 위험으로 인해 해당 사용자가 차단될 수 있습니다. 사용자가 위험으로 인해 다른 테넌트에서 게스트 사용자로 차단되었다고 보고하는 경우 계정을 보호하고 협업을 사용하도록 해당 사용자를 수정해야 합니다. 

### <a name="reset-the-users-password"></a>사용자 암호 재설정

Azure AD 보안 메뉴의 [위험한 사용자 보고서](https://portal.azure.com/#blade/Microsoft_AAD_IAM/SecurityMenuBlade/RiskyUsers)에서 '사용자' 필터를 사용하여 영향을 받는 사용자를 검색합니다. 보고서에서 영향을 받는 사용자를 선택하고, 위쪽 도구 모음에서 "암호 재설정"을 클릭합니다. 사용자에게 다음에 로그인할 때 변경해야 하는 임시 암호가 할당됩니다. 이 프로세스는 사용자 위험을 수정하고 자격 증명을 안전한 상태로 되돌립니다.

### <a name="manually-dismiss-users-risk"></a>사용자 위험을 수동으로 해제

Azure AD 포털에서 암호 재설정이 옵션이 아닌 경우 사용자 위험을 수동으로 해제하도록 선택할 수 있습니다. 이 프로세스로 인해 사용자는 더 이상 위험에 노출되지 않지만 기존 암호에는 영향을 주지 않습니다. ID를 안전한 상태로 되돌리려면 사용 가능한 모든 방법을 사용하여 사용자의 암호를 변경해야 합니다. 

사용자 위험을 해제하려면 Azure AD 보안 메뉴에서 [위험한 사용자 보고서](https://portal.azure.com/#blade/Microsoft_AAD_IAM/SecurityMenuBlade/RiskyUsers)로 이동합니다. '사용자' 필터를 사용하여 영향을 받는 사용자를 검색하고, 사용자를 클릭합니다. 위쪽 도구 모음에서 "사용자 위험 해제" 옵션을 클릭합니다. 이 작업을 완료하는 데 몇 분 정도 걸릴 수 있으며, 보고서에서 사용자 위험 상태가 업데이트됩니다.

ID 보호에 대한 자세한 내용은 [ID 보호란?](overview-identity-protection.md)을 참조하세요.

## <a name="how-does-identity-protection-work-for-b2b-users"></a>B2B 사용자에 대해 ID 보호가 작동하는 방법

B2B 협업 사용자에 대한 위험은 홈 디렉터리에서 평가됩니다. 해당 사용자의 실시간 로그인 위험은 리소스에 액세스하려고 할 때 리소스 디렉터리에서 평가됩니다. Azure AD B2B 협업을 통해 조직은 ID 보호를 사용하여 B2B 사용자에 대한 위험 기반 정책을 적용할 수 있습니다. 해당 정책은 두 가지 방법으로 구성됩니다:

- 관리자는 게스트 사용자를 포함하여 모든 앱에 적용되는 기본 제공 ID 보호 위험 기반 정책을 구성할 수 있습니다.
- 관리자는 게스트 사용자를 포함하여 로그인 위험을 조건으로 사용하는 조건부 액세스 정책을 구성할 수 있습니다.

## <a name="limitations-of-identity-protection-for-b2b-collaboration-users"></a>B2B 협업 사용자용 ID 보호 제한 사항

홈 디렉터리에 ID가 존재하기 때문에 리소스 디렉터리에서 B2B 협업 사용자를 위한 ID 보호 구현에 제한 사항이 있습니다. 기본 제한 사항은 다음과 같습니다:

- 게스트 사용자가 ID 보호 사용자 위험 정책을 트리거하여 암호를 재설정하면 **차단됩니다**. 해당 차단은 리소스 디렉터리에서 암호를 재설정할 수 없기 때문에 발생합니다.
- **게스트 사용자는 위험 사용자 보고서에 표시되지 않습니다**. 표시되지 않는 이유는 B2B 사용자의 홈 디렉터리에서 발생하는 위험 평가 때문입니다.
- 관리자는 리소스 디렉터리에서 **위험 B2B 협업 사용자를 해제하거나 수정할 수 없습니다**. 기능을 사용할 수 없는 이유는 리소스 디렉터리의 관리자가 B2B 사용자의 홈 디렉터리에 액세스할 수 없기 때문입니다.

### <a name="why-cant-i-remediate-risky-b2b-collaboration-users-in-my-directory"></a>내 디렉터리에서 위험 B2B 협업 사용자를 수정할 수 없는 이유는 무엇입니까?

B2B 사용자에 대한 위험 평가 및 수정은 홈 디렉터리에서 발생합니다. 이로 인해 게스트 사용자는 리소스 디렉터리의 위험 사용자 보고서에 표시되지 않으며 리소스 디렉터리 관리자는 해당 사용자에 대해 보안 암호 재설정을 강제할 수 없습니다.

### <a name="what-do-i-do-if-a-b2b-collaboration-user-was-blocked-due-to-a-risk-based-policy-in-my-organization"></a>조직의 위험 기반 정책으로 B2B 협업 사용자가 차단된 경우 어떻게 해야 하나요?

디렉터리의 위험 B2B 사용자가 위험 기반 정책에 의해 차단되는 경우 사용자는 홈 디렉터리에서 해당 위험을 수정해야 합니다. 사용자는 [위에서 설명한 대로](#how-to-unblock-your-account) 홈 디렉터리에서 안전한 암호를 다시 설정하여 해당 위험을 수정할 수 있습니다. 홈 디렉터리에서 셀프 서비스 암호 재설정을 사용하도록 설정하지 않은 경우 조직의 IT 담당자에게 문의하여 관리자가 수동으로 해당 위험을 해제하거나 암호를 재설정하도록 해야 합니다.

### <a name="how-do-i-prevent-b2b-collaboration-users-from-being-impacted-by-risk-based-policies"></a>B2B 협업 사용자가 위험 기반 정책의 영향을 받지 않도록 하려면 어떻게 해야 하나요?

조직의 위험 기반 조건부 액세스 정책에서 B2B 사용자를 제외하면 B2B 사용자가 위험 평가에 영향을 받거나 차단되지 않습니다. 해당 B2B 사용자를 제외하려면 Azure AD에서 조직의 모든 게스트 사용자를 포함하는 그룹을 만듭니다. 그런 다음, 이 그룹을 기본 제공 ID 보호 사용자 위험 및 로그인 위험 정책과 로그인 위험을 조건으로 사용하는 모든 조건부 액세스 정책에 대한 제외로 추가합니다.

## <a name="next-steps"></a>다음 단계

Azure AD B2B 협업에 대한 다음 문서를 살펴보세요.

- [Azure AD B2B 협업이란?](../external-identities/what-is-b2b.md)