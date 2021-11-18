---
title: Azure Active Directory에서 플래그가 지정된 로그인이란?
description: Azure Active Directory에서 플래그가 지정된 로그인에 대해 간략히 설명합니다.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: karenhoran
editor: ''
ms.assetid: e2b3d8ce-708a-46e4-b474-123792f35526
ms.service: active-directory
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 11/12/2021
ms.author: markvi
ms.reviewer: tspring
ms.collection: M365-identity-device-management
ms.openlocfilehash: 056ed3df8e9658b00917680aa2ad291f3529d1c8
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/15/2021
ms.locfileid: "132484578"
---
# <a name="what-are-flagged-sign-ins-in-azure-active-directory"></a>Azure Active Directory에서 플래그가 지정된 로그인이란?

사용자가 로그인하지 못한 경우 IT 관리자는 최대한 빨리 문제를 해결하여 사용자의 차단을 해제하려고 합니다. 로그인 로그에서 사용 가능한 데이터의 양으로 인해 올바른 정보를 찾는 것이 어려울 수 있습니다.

이 문서에서는 관련 문제를 쉽게 찾을 수 있도록 하여 사용자 로그인 문제를 해결하는 데 걸리는 시간을 크게 향상시키는 기능에 대해 간략히 설명합니다.




## <a name="what-it-is"></a>의미

Azure AD 로그인 이벤트는 테넌트의 사용자 로그인 및 인증 구성에서 무엇이 옳고 그른지를 이해하는 데 중요합니다. 그러나 Azure AD는 하루에 80억 개 이상의 인증을 처리하므로 관리자가 중요한 로그인 이벤트를 찾기 어려울 수 있는 로그인 이벤트가 너무 많이 발생할 수 있습니다. 즉, 로그인 이벤트의 수가 급격히 증가하면 많은 수의 이벤트 볼륨에서 도움이 필요한 사용자의 신호를 놓칠 수 있습니다.

플래그가 지정된 로그인은 도움이 필요한 사용자 로그인의 신호 대 노이즈 비율을 높이기 위한 기능입니다. 이 기능은 도움이 필요한 로그인 오류에 대한 사용자의 인식을 높이고 관리자와 지원 센터 작업자가 적합한 이벤트를 더 빠르고 효율적으로 찾을 수 있도록 하기 위한 것입니다. 플래그가 지정된 로그인 이벤트에는 하나의 추가 항목과 함께 다른 로그인 이벤트와 동일한 정보가 포함됩니다. 또한 사용자가 관리자의 검토를 위해 플래그를 이벤트에 지정했음을 나타냅니다.
 
플래그가 지정된 로그인은 로그인 페이지에 오류가 표시될 때 플래그 지정을 사용하도록 설정한 다음, 해당 오류를 재현할 수 있는 기능을 제공합니다. 그러면 로그인에 대한 Azure AD 보고 블레이드에서 오류 이벤트가 "검토 플래그 지정됨"으로 표시됩니다.

요약하면 플래그가 지정된 로그인을 사용하여 다음을 수행할 수 있습니다.

- 사용자가 테넌트 관리자의 도움이 필요한 로그인 오류를 **표시** 할 수 있도록 합니다.

- 사용자가 해결해야 하는 로그인 오류를 찾는 프로세스를 **간소화** 합니다.

- 최종 사용자가 플래그를 이벤트에 지정하는 것 이외의 작업을 수행할 필요 없이 지원 센터 직원이 사용자가 사전에 도움을 원하는 문제를 **찾을** 수 있도록 합니다.

## <a name="how-it-works"></a>작동 방식

플래그가 지정된 로그인은 브라우저를 사용하여 로그인하고 인증 오류를 받을 때 플래그 지정을 사용하도록 설정하는 기능을 제공합니다. 사용자에게 로그인 오류가 표시되면 플래그 지정을 사용하도록 선택할 수 있습니다. 다음 20분 동안 동일한 브라우저와 클라이언트 디바이스 또는 컴퓨터에서 해당 사용자의 로그인 이벤트는 로그인 보고서에 "검토 플래그 지정됨: 예"로 표시됩니다. 플래그 지정은 20분 후에 자동으로 해제됩니다.

### <a name="user-how-to-flag-an-error"></a>사용자: 플래그를 오류에 지정하는 방법

1. 사용자가 로그인 중에 오류를 받습니다.
2. 사용자가 오류 페이지에서 **세부 정보 보기** 를 클릭합니다.
3. **문제 해결 세부 정보** 에서 **플래그 지정 사용** 을 클릭합니다. 텍스트가 **플래그 지정 사용 안 함** 으로 변경됩니다. 이제 플래그 지정이 사용하도록 설정되었습니다.
4. 브라우저 창을 닫습니다.
5. 동일한 브라우저 애플리케이션에서 새 브라우저 창을 열고, 실패한 동일한 로그인을 시도합니다. 
6.  이전에 표시된 로그인 오류를 재현합니다.

플래그 지정이 사용하도록 설정되면 동일한 브라우저 애플리케이션과 클라이언트를 사용해야 합니다. 그렇지 않으면 플래그가 이벤트에 지정되지 않습니다.


### <a name="admin-find-flagged-events-in-reports"></a>관리자: 보고서에서 플래그가 지정된 이벤트 찾기

1.  Azure AD 포털의 왼쪽 창에서 **로그인 로그** 를 선택합니다.
2.  **필터 추가** 를 클릭합니다.
3.  **필드 선택** 이라는 필터 메뉴에서 **검토 플래그 지정됨** 을 선택하고, **적용** 을 클릭합니다.
4.  사용자가 플래그를 지정한 모든 이벤트가 표시됩니다.
5.  필요한 경우 추가 필터를 적용하여 이벤트 보기를 구체화합니다.
6.  이벤트를 선택하여 발생한 상황을 검토합니다.


### <a name="admin-or-developer-find-flagged-events-using-ms-graph"></a>관리자 또는 개발자: MS Graph를 사용하여 플래그가 지정된 이벤트 찾기

로그인 보고 API를 사용하여 필터링된 쿼리를 통해 플래그가 지정된 로그인을 찾을 수 있습니다.

플래그가 지정된 모든 로그인 표시: `https://graph.microsoft.com/beta/auditLogs/signIns?&$filter=flaggedforReview eq true`

UPN별 특정 사용자에 대한 플래그가 지정된 로그인 쿼리(예: user@contoso.com): `https://graph.microsoft.com/beta/auditLogs/signIns?&$filter=flaggedforReview eq true and userPrincipalname eq 'user@contoso.com'`

특정 사용자 및 날짜에 대한 플래그가 지정된 로그인 쿼리: `https://graph.microsoft.com/beta/auditLogs/signIns?&$filter=flaggedforReview eq true and createdDateTime ge 2021-10-01 and userPrincipalname eq 'user@contoso.com'`
 
로그인 Graph API를 사용하는 방법에 대한 자세한 내용은 [signIn 리소스 종류](https://docs.microsoft.com/graph/api/resources/signin?view=graph-rest-1.0&preserve-view=true)를 참조하세요.



 
## <a name="who-can-create-flagged-sign-ins"></a>플래그가 지정된 로그인을 만들 수 있는 사용자

웹 페이지를 통해 Azure AD에 로그인하는 모든 사용자는 검토를 위해 로그인 플래그 지정을 사용할 수 있습니다. 멤버 및 게스트 사용자는 모두 검토를 위해 플래그를 로그인 오류에 지정할 수 있습니다. 

## <a name="who-can-review-flagged-sign-ins"></a>플래그가 지정된 로그인을 검토할 수 있는 사용자

플래그가 지정된 로그인 이벤트를 검토하려면 Azure AD 포털에서 로그인 보고서 이벤트를 읽을 수 있는 권한이 필요합니다. 자세한 내용은 [액세스할 수 있는 사용자는 누구인가요?](concept-sign-ins.md#who-can-access-it)를 참조하세요.


플래그를 로그인 실패에 지정하려면 추가 권한이 필요하지 않습니다.


## <a name="what-you-should-know"></a>알아야 할 사항 

이름은 비슷하지만 **플래그가 지정된 로그인** 과 **위험한 로그인** 은 서로 다른 기능입니다.

- 플래그가 지정된 로그인은 사용자가 지원을 요청하는 로그인 오류 이벤트입니다. 
- 위험한 로그인은 ID 보호의 기능입니다. 자세한 내용은 [Identity Protection이란?](../identity-protection/overview-identity-protection.md)을 참조하세요.




## <a name="next-steps"></a>다음 단계

- [Azure Active Directory 로그인 로그](concept-sign-ins.md)
- [Azure AD 로그인 진단 시나리오](concept-sign-in-diagnostics-scenarios.md)
