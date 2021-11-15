---
title: HR 프로비저닝과 관련된 사용자 업데이트 문제 해결
description: HR 프로비저닝과 관련된 사용자 업데이트 문제를 해결하는 방법을 알아봅니다.
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
ms.openlocfilehash: bdd4dbc7c0be9eec3a2df7bab4315ea8d8e59a37
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131482842"
---
# <a name="troubleshoot-hr-user-update-issues"></a>HR 사용자 업데이트 문제 해결

## <a name="null-and-empty-values-not-processed-as-expected"></a>Null 및 빈 값이 예상대로 처리되지 않음
**적용 대상:**
* 온-프레미스 Active Directory 사용자 프로비저닝에 대한 Workday
* Azure Active Directory 사용자 프로비저닝에 대한 Workday
* 온-프레미스 Active Directory 사용자 프로비저닝에 대한 SAP SuccessFactors
* Azure Active Directory 사용자 프로비저닝에 대한 SAP SuccessFactors

| | |
|-- | -- |
| **문제점** | 인바운드 프로비저닝 앱을 성공적으로 구성했습니다. HR 앱에서 Null 또는 빈 값을 가져옵니다. 프로비전 서비스에서 온-프레미스 Active Directory/Azure AD에서 해당 대상 특성 값을 지울 것으로 예상합니다. 그러나 `InvalidAttributeSyntax-LdapErr: The syntax is invalid. The parameter is incorrect. Error in attribute conversion operation, data 0, v3839`라는 오류 메시지와 함께 작업이 실패합니다. |
| **원인** | 프로비저닝 서비스에는 기본적인 Null 값 처리 논리가 없습니다. 프로비저닝 서비스에서 원본 앱으로부터 빈 문자열을 가져오면 값을 "있는 그대로" 대상 앱에 전달하려고 합니다. 이 경우 온-프레미스 Active Directory에서 빈 문자열 값 설정을 지원하지 않으므로 위의 오류가 표시됩니다. |
| **해결 방법** | 프로비저닝 로그를 확인합니다. Null 또는 빈 문자열 값을 받는 대상 Active Directory의 특성을 식별합니다. 식 매핑을 사용하도록 이러한 특성에 대한 특성 매핑을 업데이트합니다. 아래의 권장 해결 방법을 참조하세요. |

**권장 해결 방법**

  Workday에서 AD 특성 `jobTitle`에 매핑된 `BusinessTitle` 특성이 Null이거나 비어 있을 수 있다고 가정해 보겠습니다. 
  * 옵션 1: [IIF](functions-for-customizing-application-data.md#iif), [IsNullOrEmpty](functions-for-customizing-application-data.md#isnullorempty), [Coalesce](functions-for-customizing-application-data.md#coalesce) 또는 [IsPresent](functions-for-customizing-application-data.md#ispresent)와 같은 함수를 사용하여 비어 있거나 Null 값을 확인하는 식을 정의하고 공백이 아닌 리터럴 값을 전달합니다. 
  
     `IIF(IsNullOrEmpty([BusinessTitle]),"N/A",[BusinessTitle])`

  * 옵션 2: [IgnoreFlowIfNullOrEmpty](functions-for-customizing-application-data.md#ignoreflowifnullorempty) 함수를 사용하여 온-프레미스 Active Directory/Azure AD에 보낸 페이로드에서 비어 있거나 Null 특성을 삭제합니다. 
  
     `IgnoreFlowIfNullOrEmpty([BusinessTitle])` 

## <a name="some-workday-attribute-updates-are-missing"></a>일부 Workday 특성 업데이트가 누락됨
**적용 대상:**
* 온-프레미스 Active Directory 사용자 프로비저닝에 대한 Workday
* Azure Active Directory 사용자 프로비저닝에 대한 Workday

| | |
|-- | -- |
| **문제점** | Workday 인바운드 프로비저닝 앱을 성공적으로 구성하고 Workday 테넌트 URL에 성공적으로 연결했습니다. Workday의 특정 특성 업데이트 흐름이 지연되거나 경우에 따라 Workday의 특성 변경이 증분 동기화 중에 예상대로 이동하지 않는 것을 확인할 수 있습니다. |
| **원인** | 증분 동기화 중에 프로비저닝 앱에서 Workday 트랜잭션 로그로부터 주 Worker 엔터티에 대한 변경 내용을 쿼리하고, Workday의 트랜잭션 로그에서 추적된 변경 내용만 처리합니다. <br> Workday의 트랜잭션 로그에서 설정의 Workday 특성에 대한 변경 내용이 추적되지 않으면 Azure AD에서 해당 변경 내용을 가져올 수 없습니다. 예: *LocalReference* Workday 특성은 기본 특성 매핑의 일부이며 XPATH `wd:Worker/wd:Worker_Data/wd:Employment_Data/wd:Position_Data/wd:Business_Site_Summary_Data/wd:Local_Reference/wd:ID[@wd:type='Locale_ID']/text()`가 있습니다. 이 특성은 *Business_Site_Summary_Data* 엔터티의 일부입니다. Workday에서 이 특성 값이 변경되면 Workday 트랜잭션 로그에 표시되지 않습니다. 따라서 증분 동기화 중에 주 Worker 엔터티와 연결된 특성도 동기화 간격 동안 변경되는 경우에만 이 특성의 새 값이 표시됩니다. |
| **해결 방법** | 특정 Workday 특성에 대한 변경 내용이 이동하지 않는 이 동작이 자주 발생하는 경우 매주 또는 매월 전체 동기화를 정기적으로 실행하는 것이 좋습니다. |

## <a name="user-match-with-extensionattribute-not-working"></a>extensionAttribute를 통한 사용자 일치가 작동하지 않음
**적용 대상:**
* Azure Active Directory 사용자 프로비저닝에 대한 Workday
* Azure Active Directory 사용자 프로비저닝에 대한 SAP SuccessFactors

| | |
|-- | -- |
| **문제점** | Azure AD에서 *extensionAttribute3* 을 사용하여 직원 ID를 저장하고, 사용자 일치를 위해 이 특성을 Workday *WorkerID* 또는 SuccessFactors *personIdExternal* 특성에 매핑했다고 가정해 보겠습니다. 이 구성을 사용하면 프로비전 프로세스의 일치 단계가 실패합니다. 이 문제는 사용자 만들기와 업데이트 모두에 영향을 줍니다. |
| **원인** | **Azure AD Graph API** 의 `$filter` 매개 변수에서 [extensionAttributes 기준 필터링을 지원](https://docs.microsoft.com/previous-versions/azure/ad/graph/howto/azure-ad-graph-api-supported-queries-filters-and-paging-options#filter)하지 않으므로 Azure AD *OnPremisesExtensionAttributes*(`extensionAttributes1-15`)는 일치 특성으로 사용할 수 없습니다. |
| **해결 방법** | 일치 특성 쌍에서 Azure AD *OnPremisesExtensionAttributes*(`extensionAttributes1-15`)를 사용하지 않습니다. employeeID를 사용합니다. |



## <a name="next-steps"></a>다음 단계

* [Azure AD와 Workday 통합 시나리오 및 웹 서비스 호출에 대한 자세한 정보](workday-integration-reference.md)
* [프로비저닝 작업에 대한 로그를 검토하고 보고서를 받아보는 방법을 알아봅니다](check-status-user-account-provisioning.md).

