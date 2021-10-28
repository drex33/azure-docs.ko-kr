---
title: 조건부 액세스 관련 로그인 문제 해결 - Azure Active Directory
description: 이 문서에서는 조건부 액세스 정책으로 인해 예기치 않은 결과가 발생하는 경우 수행할 작업을 설명합니다.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: troubleshooting
ms.date: 10/16/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: karenhoran
ms.reviewer: calebb, martinco
ms.collection: M365-identity-device-management
ms.openlocfilehash: d53f67c4035d8b97bd3117c5386b371b711a5e7b
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128606725"
---
# <a name="troubleshooting-sign-in-problems-with-conditional-access"></a>조건부 액세스 관련 로그인 문제 해결

이 문서의 정보는 오류 메시지 및 Azure AD 로그인 로그를 사용하여 조건부 액세스와 관련된 예기치 않은 로그인 결과의 문제를 해결하는 데 사용할 수 있습니다.

## <a name="select-all-consequences"></a>“모든” 결과 선택

조건부 액세스 프레임워크는 뛰어난 구성 유연성을 제공합니다. 그러나 훌륭한 유연성은 잘못된 결과를 방지하기 위해 릴리스하기 전에 각 구성 정책을 신중하게 검토해야 한다는 것을 의미하기도 합니다. 이 컨텍스트에서 **모든 사용자 / 그룹 / 응용 프로그램** 등 완전한 집합에 영향을 미치는 할당에 특별한 주의를 기울여야 합니다.

조직은 다음과 같은 구성을 사용하지 않는 것이 좋습니다.

**모든 사용자에 대한 모든 클라우드 앱:**

- **액세스 차단** - 이 구성은 전체 조직을 차단합니다.
- **디바이스를 준수 상태로 표시해야 함** - 디바이스를 아직 등록하지 않은 사용자의 경우 이 정책은 Intune 포털에 대한 액세스를 비롯한 모든 액세스를 차단합니다. 등록된 디바이스가 없는 관리자의 경우 이 정책은 정책을 변경하기 위해 Azure Portal로 다시 돌아가지 않도록 차단합니다.
- **하이브리드 Azure AD 도메인 조인된 디바이스 필요** - 이 액세스 차단 정책은 하이브리드 Azure AD 조인된 디바이스가 없는 조직의 모든 사용자에 대해 액세스를 차단할 수 있습니다.
- **앱 보호 정책 필요** -Intune 정책이 없는 경우 이 액세스 차단 정책도 조직의 모든 사용자에 대해 액세스를 차단할 수 있습니다. Intune 앱 보호 정책이 있는 클라이언트 애플리케이션을 사용하지 않는 관리자의 경우 이 정책은 Intune 및 Azure와 같은 포털을 다시 가져올 수 없도록 차단합니다.

**모든 사용자 경우 모든 클라우드 앱, 모든 디바이스 플랫폼은 다음과 같습니다.**

- **액세스 차단** - 이 구성은 전체 조직을 차단합니다.

## <a name="conditional-access-sign-in-interrupt"></a>조건부 액세스 로그인 중단

첫 번째 방법은 표시되는 오류 메시지를 검토하는 것입니다. 웹 브라우저를 사용하는 경우 로그인하는 데 문제가 있는 경우 오류 페이지 자체에 자세한 정보가 포함됩니다. 이 정보만으로도 문제를 설명하고 솔루션을 제안할 수 있습니다.

![로그인 오류 - 규격 디바이스 필요](./media/troubleshoot-conditional-access/image1.png)

위의 오류에서 메시지는 회사의 모바일 디바이스 관리 정책을 충족하는 디바이스 또는 클라이언트 애플리케이션에서만 애플리케이션에 액세스할 수 있음을 알려줍니다. 이 경우 애플리케이션 및 디바이스는 해당 정책을 충족하지 않습니다.

## <a name="azure-ad-sign-in-events"></a>Azure AD 로그인 이벤트

로그인 중단에 대한 자세한 정보를 가져오는 두 번째 방법은 Azure AD 로그인 이벤트를 검토하여 적용된 조건부 액세스 정책 또는 정책을 확인하는 것입니다.

초기 오류 페이지에서 **자세한 정보** 를 클릭하여 문제에 대한 자세한 정보를 확인할 수 있습니다. **자세한 정보** 를 클릭하면 사용자가 확인한 특정 오류 이벤트에 대한 Azure AD 로그인 이벤트를 검색하거나 Microsoft에서 지원 인시던트를 열 때 도움이 되는 문제 해결 정보가 표시됩니다.

![웹 브라우저 로그인을 중단한 조건부 액세스에 대한 상세한 정보](./media/troubleshoot-conditional-access/image2.png)

적용되는 조건부 액세스 정책 및 준수 이유를 확인하려면 다음을 수행합니다.

1. 전역 관리자, 보안 관리자 또는 전역 읽기 권한자로 **Azure Portal** 에 로그인합니다.
1. **Azure Active Directory** > **로그인** 으로 이동합니다.
1. 검토할 로그인에 대한 이벤트를 찾습니다. 필터 및 열을 추가하거나 제거하여 불필요한 정보를 필터링합니다.
   1. 필터를 추가하여 범위 좁히기:
      1. **상관 관계 ID** - 조사할 특정 이벤트가 있는 경우입니다.
      1. **조건부 액세스** - 정책 실패 및 성공을 확인합니다. 결과를 제한하는 실패만 표시하도록 필터 범위를 지정합니다.
      1. **사용자 이름** - 특정 사용자와 관련된 정보를 확인합니다.
      1. **날짜** - 해당 시간 프레임으로 범위가 지정됩니다.

   ![로그인 로그에서 조건부 액세스 필터 선택](./media/troubleshoot-conditional-access/image3.png)

1. 사용자의 로그인 실패에 해당하는 로그인 이벤트를 찾은 후에는 **조건부 액세스** 탭을 선택합니다. 조건부 액세스 탭에는 로그인 중단을 일으킨 특정 정책이 표시됩니다.
   1. **문제 해결 및 지원** 탭의 정보는 규정 준수 요구 사항을 충족하지 않는 디바이스와 같은 로그인 실패에 대한 명확한 원인을 제공할 수 있습니다.
   1. 자세히 조사하려면 **정책 이름** 을 클릭하여 정책 구성으로 드릴다운합니다. **정책 이름** 을 클릭하면 검토 및 편집을 위해 선택한 정책에 대한 정책 구성 사용자 인터페이스가 표시됩니다.
   1. 조건부 액세스 정책 평가에 사용된 **클라이언트 사용자** 및 **디바이스 세부 정보** 는 **기본 정보**, **위치**, **디바이스 정보**, **인증 세부 정보**, 로그인 이벤트의 **추가 세부 정보** 탭에서도 사용할 수 있습니다.

### <a name="policy-details"></a>정책 세부 정보

로그인 이벤트에서 정책의 오른쪽에 있는 줄임표를 선택하면 정책 세부 정보가 표시됩니다. 이를 통해 관리자는 정책이 성공적으로 적용된 이유에 대한 추가 정보를 제공합니다.

   ![로그인 이벤트 조건부 액세스 탭](./media/troubleshoot-conditional-access/image5.png)

   ![정책 세부 정보(미리 보기)](./media/troubleshoot-conditional-access/policy-details.png)

왼쪽은 로그인 시 수집되는 세부 정보를 제공하고 오른쪽에는 적용된 조건부 액세스 정책의 요구 사항을 충족하는지 여부에 대한 세부 정보가 제공됩니다. 조건부 액세스 정책은 모든 조건이 충족되거나 구성되지 않은 경우에만 적용됩니다.

이벤트의 정보가 로그인 결과를 이해하기에 충분하지 않거나 정책을 조정하여 원하는 결과를 얻을 수 없는 경우 지원 인시던트가 열립니다. 해당 로그인 이벤트의 **문제 해결 및 지원** 탭으로 이동하 고 **새 지원 요청 생성** 를 선택합니다.

![로그인 이벤트의 문제 해결 및 지원 탭](./media/troubleshoot-conditional-access/image6.png)

인시던트를 제출하는 경우 인시던트 제출 세부 정보의 로그인 이벤트에서 요청 ID와 시간 및 날짜를 제공합니다. 이 정보를 통해 Microsoft 지원이 이벤트를 찾을 수 있습니다.

### <a name="conditional-access-error-codes"></a>조건부 액세스 오류 코드

| 로그인 오류 코드 | 오류 문자열 |
| --- | --- |
| 53000 | DeviceNotCompliant |
| 53001 | DeviceNotDomainJoined |
| 53002 | ApplicationUsedIsNotAnApprovedApp |
| 53003 | BlockedByConditionalAccess |
| 53004 | ProofUpBlockedDueToRisk |

## <a name="what-to-do-if-you-are-locked-out-of-the-azure-portal"></a>Azure Portal이 잠긴 경우 어떻게 해야 하나요?

조건부 액세스 정책의 잘못된 설정으로 인해 Azure Portal이 잠긴 경우:

- 아직 차단되지 않은 조직 내 다른 관리자가 있는지 확인합니다. Azure Portal에 액세스할 수 있는 관리자는 로그인에 영향을 주는 정책을 사용하지 않도록 설정할 수 있습니다. 
- 정책을 업데이트할 수 있는 조직의 관리자가 없는 경우 지원 요청을 제출합니다. Microsoft 지원은 액세스를 차단하는 조건부 액세스 정책을 검토하고 확인 시 업데이트할 수 있습니다.

## <a name="next-steps"></a>다음 단계

- [Azure Active Directory 포털의 로그인 활동 보고서](../reports-monitoring/concept-sign-ins.md)
- [What If 도구를 사용하여 조건부 액세스 문제 해결](troubleshoot-conditional-access-what-if.md)
