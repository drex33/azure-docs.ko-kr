---
title: HR 프로비저닝과 관련된 쓰기 저장 문제 해결
description: HR 프로비저닝과 관련된 관리자 업데이트 문제를 해결하는 방법을 알아봅니다.
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
ms.openlocfilehash: ff6f9d291b52b7028fbccdc001bc10d60c8c1450
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131482841"
---
# <a name="troubleshoot-hr-write-back-issues"></a>HR 쓰기 저장 문제 해결

## <a name="null-and-empty-values-not-processed-as-expected"></a>Null 및 빈 값이 예상대로 처리되지 않음
**적용 대상:**
* Workday 쓰기 저장
* SAP SuccessFactors 쓰기 저장

| | |
|-- | -- |
| **문제점** | 쓰기 저장 앱을 성공적으로 구성했습니다. Azure AD 앱에서 Null 또는 빈 값을 가져옵니다. 프로비저닝 서비스가 HR 앱에서 해당 이메일 또는 전화 번호 값을 지울 것으로 예상합니다. 그러나 작업은 실패합니다. |
| **원인** | 프로비저닝 서비스에는 기본적인 Null 값 처리 논리가 없습니다. 프로비저닝 서비스에서 원본 앱으로부터 빈 문자열을 가져오면 값을 "있는 그대로" 대상 앱에 전달하려고 합니다. Workday 또는 SuccessFactors가 빈 값을 처리할 수 없는 경우 오류가 반환됩니다. |
| **해결 방법** | 아래 권장 사항에 따라 식 매핑을 사용하도록 특성 매핑을 업데이트합니다. |

**권장 해결 방법**

  SAP SuccessFactors 특성 `businessPhoneNumber`에 매핑된 `telephoneNumber` 특성이 Azure AD에서 Null이거나 비어 있을 수 있다고 가정해 보겠습니다. 
  * 옵션 1: [IIF](functions-for-customizing-application-data.md#iif), [IsNullOrEmpty](functions-for-customizing-application-data.md#isnullorempty), [Coalesce](functions-for-customizing-application-data.md#coalesce) 또는 [IsPresent](functions-for-customizing-application-data.md#ispresent)와 같은 함수를 사용하여 비어 있거나 Null 값을 확인하는 식을 정의하고 공백이 아닌 리터럴 값(예: 이 경우 000-000-0000)을 전달합니다. 
  
     `IIF(IsNullOrEmpty([telephoneNumber]),"000-000-0000",[telephoneNumber])`

  * 옵션 2: [IgnoreFlowIfNullOrEmpty](functions-for-customizing-application-data.md#ignoreflowifnullorempty) 함수를 사용하여 SuccessFactors에 보낸 페이로드에서 비어 있거나 Null 특성을 삭제합니다. 
  
     `IgnoreFlowIfNullOrEmpty([telephoneNumber])` 



## <a name="next-steps"></a>다음 단계

* [Azure AD와 Workday 통합 시나리오 및 웹 서비스 호출에 대한 자세한 정보](workday-integration-reference.md)
* [프로비저닝 작업에 대한 로그를 검토하고 보고서를 받아보는 방법을 알아봅니다](check-status-user-account-provisioning.md).
