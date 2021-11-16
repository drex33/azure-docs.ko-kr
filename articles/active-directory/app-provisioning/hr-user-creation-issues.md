---
title: HR 프로비저닝과 관련된 사용자 생성 문제 해결
description: HR 프로비저닝과 관련된 사용자 생성 문제를 해결하는 방법을 알아봅니다.
services: active-directory
author: kenwith
manager: karenh444
ms.service: active-directory
ms.subservice: app-provisioning
ms.topic: troubleshooting
ms.workload: identity
ms.date: 10/27/2021
ms.author: kenwith
ms.reviewer: chmutali
ms.openlocfilehash: 9bdbbc44b07201b4a1e10a42f607df2a43e971ad
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131482843"
---
# <a name="troubleshoot-hr-user-creation-issues"></a>HR 사용자 생성 문제 해결

## <a name="creation-fails-due-to-null--empty-values"></a>Null/빈 값으로 인해 만들기 실패 

**적용 대상:**
* 온-프레미스 Active Directory 사용자 프로비저닝에 대한 Workday
* Azure Active Directory 사용자 프로비저닝에 대한 Workday
* 온-프레미스 Active Directory 사용자 프로비저닝에 대한 SAP SuccessFactors
* Azure Active Directory 사용자 프로비저닝에 대한 SAP SuccessFactors

| | |
|-- | -- |
| **문제점** | 인바운드 프로비저닝 앱을 성공적으로 구성했습니다. HR 앱에서 Null 또는 빈 값을 가져옵니다. `InvalidAttributeSyntax-LdapErr: The syntax is invalid. The parameter is incorrect. Error in attribute conversion operation, data 0, v3839` 오류 메시지와 함께 만들기 작업이 실패합니다. |
| **원인** | 프로비저닝 서비스에는 기본적인 Null 값 처리를 위한 기본 논리가 없습니다. 프로비저닝 서비스에서 원본 앱으로부터 빈 문자열을 가져오면 값을 "있는 그대로" 대상 앱에 전달하려고 합니다. 이 경우 온-프레미스 Active Directory에서 빈 문자열 값 설정을 지원하지 않으므로 위의 오류가 표시됩니다. |
| **해결 방법** | 프로비저닝 로그를 확인합니다. Null 또는 빈 문자열 값을 받는 대상 Active Directory의 특성을 식별합니다. 식 매핑을 사용하도록 이러한 특성에 대한 특성 매핑을 업데이트합니다. 아래의 권장 해결 방법을 참조하세요. |

**권장 해결 방법**

  Workday에서 AD 특성 `jobTitle`에 매핑된 `BusinessTitle` 특성이 Null이거나 비어 있을 수 있다고 가정해 보겠습니다. 
  * 옵션 1: [IIF](functions-for-customizing-application-data.md#iif), [IsNullOrEmpty](functions-for-customizing-application-data.md#isnullorempty), [Coalesce](functions-for-customizing-application-data.md#coalesce) 또는 [IsPresent](functions-for-customizing-application-data.md#ispresent)와 같은 함수를 사용하여 비어 있거나 Null 값을 확인하는 식을 정의하고 공백이 아닌 리터럴 값을 전달합니다. 
  
     `IIF(IsNullOrEmpty([BusinessTitle]),"N/A",[BusinessTitle])`

  * 옵션 2: [IgnoreFlowIfNullOrEmpty](functions-for-customizing-application-data.md#ignoreflowifnullorempty) 함수를 사용하여 온-프레미스 Active Directory/Azure AD에 보낸 페이로드에서 비어 있거나 Null 특성을 삭제합니다. 
  
     `IgnoreFlowIfNullOrEmpty([BusinessTitle])` 


## <a name="next-steps"></a>다음 단계

* [Azure AD와 Workday 통합 시나리오 및 웹 서비스 호출에 대한 자세한 정보](workday-integration-reference.md)
* [프로비저닝 작업에 대한 로그를 검토하고 보고서를 받아보는 방법을 알아봅니다](check-status-user-account-provisioning.md).

