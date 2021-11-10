---
title: Azure Active Directory의 프로비저닝 로그 | Microsoft Docs
description: Azure Active Directory의 프로비저닝 로그 개요입니다.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: karenhoran
editor: ''
ms.assetid: 4b18127b-d1d0-4bdc-8f9c-6a4c991c5f75
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 4/25/2021
ms.author: markvi
ms.reviewer: arvinh
ms.collection: M365-identity-device-management
ms.openlocfilehash: 167ba276023191abd894081e8540a83b8b4b0bee
ms.sourcegitcommit: 27ddccfa351f574431fb4775e5cd486eb21080e0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/08/2021
ms.locfileid: "131997194"
---
# <a name="provisioning-logs-in-azure-active-directory"></a>Azure Active Directory의 프로비저닝 로그

IT 관리자가 IT 환경의 운영 상태를 파악하려고 합니다. 시스템 상태 정보를 사용하여 잠재적 이슈에 대응해야 하는지 여부와 방법을 평가할 수 있습니다. 

이 목표를 지원하기 위해 Azure Active Directory 포털은 다음 세 가지 활동 로그에 대한 액세스를 제공합니다.

- **[로그인](concept-sign-ins.md)** – 로그인 및 사용자가 리소스를 사용하는 방법에 대한 정보입니다.
- **[감사](concept-audit-logs.md)** – 사용자 및 그룹 관리 또는 테넌트 리소스에 적용된 업데이트와 같이 테넌트에 적용된 변경 사항에 대한 정보입니다.
- **[프로비저닝](concept-provisioning-logs.md)** – ServiceNow의 그룹 생성 또는 Workday에서 가져온 사용자와 같이 프로비저닝 서비스에서 수행하는 활동입니다.


이 글에서는 프로비저닝 로그에 대한 개요를 제공합니다. 


## <a name="what-can-you-do-with-it"></a>프로비전 로그로 무엇을 할 수 있나요?

프로비저닝 로그를 사용하여 다음과 같은 질문에 대한 답을 찾을 수 있습니다.

-  ServiceNow에서 성공적으로 만든 그룹은 무엇인가요?

-  Adobe에서 성공적으로 제거한 사용자는 누구인가요?

-  Active Directory에서 성공적으로 만든 Workday의 사용자는 누구인가요? 


## <a name="who-can-access-it"></a>액세스할 수 있는 사용자는 누구인가요?

프로비저닝 로그의 데이터에 액세스할 수 있는 사용자는 다음과 같습니다.

- 애플리케이션 소유자(자신의 애플리케이션에 대한 로그)

- 보안 관리자, 보안 읽기 권한자, 보고서 읽기 권한자, 보안 운영자, 애플리케이션 관리자 및 클라우드 애플리케이션 관리자 역할의 사용자

- [provisioningLogs 권한](../roles/custom-enterprise-app-permissions.md#full-list-of-permissions)이 있는 사용자 지정 역할의 사용자

- 전역 관리자

## <a name="what-azure-ad-license-do-you-need"></a>필요한 Azure AD 라이선스는 무엇인가요?

프로비저닝 작업 보고서를 보려면 연결된 Azure AD Premium 라이선스가 테넌트에 있어야 합니다. Azure AD 버전을 업그레이드하려면 [Azure Active Directory Premium 시작](../fundamentals/active-directory-get-started-premium.md)을 참조하세요. 


## <a name="how-can-you-access-it"></a>어떻게 액세스할 수 있나요? 

로그 데이터에 액세스하기 위해 다음 옵션을 사용할 수 있습니다.

- Azure Portal

- 프로비저닝 로그를 [Azure Monitor](../app-provisioning/application-provisioning-log-analytics.md)로 스트림합니다. 이 방법을 사용하면 데이터 보존을 연장하고 사용자 지정 대시보드, 경고 및 쿼리를 빌드할 수 있습니다.

- [Microsoft Graph API](/graph/api/resources/provisioningobjectsummary)에서 프로비저닝 로그를 쿼리합니다.

- 프로비저닝 로그를 CSV 또는 JSON 파일로 다운로드합니다.



## <a name="where-can-you-find-it-in-the-azure-portal"></a>Azure Portal에서 로그를 찾을 수 있는 위치는 어디인가요?

Azure Portal은 로그에 액세스하는 몇 가지 옵션을 제공합니다. 예를 들어 Azure Active Directory 메뉴의 **모니터링** 섹션에서 로그를 열 수 있습니다.  

![프로비저닝 로그 열기](./media/concept-sign-ins/sign-ins-logs-menu.png)

[https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/SignIns](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/SignIns) 링크를 사용하여 로그인 로그에 직접 액세스할 수도 있습니다.












[Azure Portal](https://portal.azure.com)에 있는 **Azure Active Directory** 창의 **모니터링** 섹션에서 **프로비저닝 로그** 를 선택하여 프로비저닝 로그에 액세스할 수 있습니다. 일부 프로비저닝 레코드가 포털에 표시되는 데 최대 2시간이 걸릴 수 있습니다.

![프로비저닝 로그에 액세스하기 위한 선택 항목을 보여 주는 스크린샷](./media/concept-provisioning-logs/access-provisioning-logs.png "프로비저닝 로그")



## <a name="what-is-the-default-view"></a>기본 보기는 무엇인가요?

프로비저닝 로그에는 다음 항목을 보여 주는 기본 목록 보기가 있습니다.

- ID
- 작업
- 원본 시스템
- 대상 시스템
- 상태
- 날짜


![프로비저닝 로그의 기본 열을 보여 주는 스크린샷](./media/concept-provisioning-logs/default-columns.png "기본 열")

도구 모음에서 **열** 을 선택하여 목록 보기를 사용자 지정할 수 있습니다.

![열을 사용자 지정하기 위한 단추를 보여 주는 스크린샷](./media/concept-provisioning-logs/column-chooser.png "열 선택")

이 영역을 사용하면 추가 필드를 표시하거나 이미 표시된 필드를 제거할 수 있습니다.

![몇 가지 항목이 선택된 사용 가능한 열을 보여 주는 스크린샷](./media/concept-provisioning-logs/available-columns.png "사용 가능한 열")

자세한 정보를 가져오려면 목록 보기에서 항목을 선택합니다.

![자세한 정보를 보여 주는 스크린샷](./media/concept-provisioning-logs/steps.png "필터")


## <a name="filter-provisioning-activities"></a>프로비저닝 작업 필터링

프로비저닝 데이터를 필터링할 수 있습니다. 일부 필터 값은 테넌트에 따라 동적으로 채워집니다. 예를 들어 테넌트에 "만들기" 이벤트가 없는 경우 **만들기** 필터 옵션이 없습니다.

기본 보기에서 선택할 수 있는 필터는 다음과 같습니다.

- **ID**
- **날짜**
- **상태**
- **작업**


![필터 값을 보여 주는 스크린샷](./media/concept-provisioning-logs/default-filter.png "필터")

**ID** 필터를 사용하면 관심 있는 이름 또는 ID를 지정할 수 있습니다. 이 ID는 사용자, 그룹, 역할 또는 다른 개체일 수 있습니다. 

개체의 이름 또는 ID를 기준으로 검색할 수 있습니다. ID는 시나리오에 따라 달라집니다. 예를 들어 개체를 Azure AD에서 Salesforce로 프로비저닝하는 경우 원본 ID는 Azure AD에 있는 사용자의 개체 ID입니다. 대상 ID는 Salesforce에 있는 사용자의 ID입니다. Workday에서 Active Directory로 프로비저닝하는 경우 원본 ID는 Workday 작업자 직원 ID입니다. 

> [!NOTE]
> 사용자 이름이 **ID** 열에 항상 표시되지 않을 수 있습니다. 항상 하나의 ID가 있습니다. 


**날짜** 필터를 사용하면 반환되는 데이터의 시간 범위를 정의할 수 있습니다. 가능한 값은 다음과 같습니다.

- 1개월
- 7 일
- 30일
- 24시간
- 사용자 지정 시간 간격

사용자 지정 시간 프레임을 선택하면 시작 날짜와 종료 날짜를 구성할 수 있습니다.

**상태** 필터를 사용하면 다음을 선택할 수 있습니다.

- **모두**
- **Success**
- **실패**
- **생략**

**작업** 필터를 사용하면 다음 작업을 필터링할 수 있습니다.

- **만들기** 
- **업데이트**
- **삭제**
- 사용 안 함
- **기타**

기본 보기의 필터 외에도 다음 필터를 설정할 수 있습니다.

![필터로 추가할 수 있는 필드를 보여 주는 스크린샷](./media/concept-provisioning-logs/add-filter.png "필드 선택")

- **작업 ID**: 고유한 작업 ID는 프로비저닝을 사용하도록 설정한 각 애플리케이션과 연결됩니다.   

- **주기 ID**: 주기 ID는 프로비저닝 주기를 고유하게 식별합니다. 이 ID를 제품 지원과 공유하여 해당 이벤트가 발생한 주기를 조회할 수 있습니다.

- **변경 ID**: 변경 ID는 프로비저닝 이벤트에 대한 고유 식별자입니다. 이 ID를 제품 지원과 공유하여 프로비저닝 이벤트를 조회할 수 있습니다.   

- **원본 시스템**: ID를 프로비저닝하는 위치를 지정할 수 있습니다. 예를 들어 개체를 Azure AD에서 ServiceNow로 프로비저닝하는 경우 원본 시스템은 Azure AD입니다. 

- **대상 시스템**: ID가 프로비저닝되는 위치를 지정할 수 있습니다. 예를 들어 개체를 Azure AD에서 ServiceNow로 프로비저닝하는 경우 대상 시스템은 ServiceNow입니다. 

- **애플리케이션**: 특정 문자열이 포함된 표시 이름이 있는 애플리케이션의 레코드만 표시할 수 있습니다.

## <a name="provisioning-details"></a>프로비저닝 세부 정보 

프로비저닝 목록 보기에서 항목을 선택하면 해당 항목에 대한 자세한 정보가 표시됩니다. 세부 정보는 다음 탭으로 그룹화됩니다.

![프로비저닝 세부 정보가 포함된 4개의 탭을 보여 주는 스크린샷](./media/concept-provisioning-logs/provisioning-tabs.png "탭")

- **단계**: 개체를 프로비저닝하기 위해 수행하는 단계를 간략하게 설명합니다. 개체 프로비저닝은 다음 4개 단계로 구성할 수 있습니다.
  
  1. 개체를 가져옵니다.
  1. 개체가 범위 내에 있는지 확인합니다.
  1. 원본과 대상 간의 개체를 일치시킵니다.
  1. 개체를 프로비저닝합니다(만들기, 업데이트, 삭제 또는 사용 안 함).

  ![단계 탭의 프로비저닝 단계를 보여 주는 스크린샷](./media/concept-provisioning-logs/steps.png "필터")

- **문제 해결 및 권장 사항**: 오류 코드와 이유를 제공합니다. 오류 정보는 오류가 발생한 경우에만 사용할 수 있습니다.

- **수정된 속성**: 이전 값과 새 값을 표시합니다. 이전 값이 없으면 해당 열이 비어 있습니다.

- **요약**: 발생한 상황에 대한 개요와 원본 및 대상 시스템의 개체에 대한 식별자를 제공합니다.

## <a name="download-logs-as-csv-or-json"></a>CSV 또는 JSON으로 로그 다운로드

Azure Portal의 로그로 이동하고 **다운로드** 를 선택하여 나중에 사용할 프로비저닝 로그를 다운로드할 수 있습니다. 선택한 필터 조건에 따라 파일이 필터링됩니다. 다운로드 크기와 시간을 줄이기 위해 필터를 가능한 한 구체적으로 지정합니다. 

CSV 다운로드에는 다음 세 개의 파일이 포함됩니다.

* **ProvisioningLogs**: 프로비저닝 단계 및 수정된 속성을 제외한 모든 로그를 다운로드합니다.
* **ProvisioningLogs_ProvisioningSteps**: 프로비저닝 단계 및 변경 ID가 포함되어 있습니다. 변경 ID를 사용하여 다른 두 파일과 이벤트를 조인할 수 있습니다.
* **ProvisioningLogs_ModifiedProperties**: 변경된 특성과 변경 ID가 포함되어 있습니다. 변경 ID를 사용하여 다른 두 파일과 이벤트를 조인할 수 있습니다.

#### <a name="open-the-json-file"></a>JSON 파일 열기
JSON 파일을 열려면 [Microsoft Visual Studio Code](https://aka.ms/vscode)와 같은 텍스트 편집기를 사용합니다. Visual Studio Code는 파일을 더 쉽게 읽을 수 있도록 구문 강조 표시를 제공합니다. 또한 [Microsoft Edge](https://aka.ms/msedge)와 같이 편집할 수 없는 형식의 브라우저를 사용하여 JSON 파일을 열 수 있습니다. 

#### <a name="prettify-the-json-file"></a>JSON 파일 꾸미기
JSON 파일은 다운로드 크기를 줄이기 위해 축소된 형식으로 다운로드됩니다. 이 형식을 사용하면 페이로드를 읽는 것이 어려울 수 있습니다. 다음 두 가지 옵션을 확인하여 파일을 꾸밉니다.

- [Visual Studio Code를 사용하여 JSON 형식을 지정](https://code.visualstudio.com/docs/languages/json#_formatting)합니다.

- PowerShell을 사용하여 JSON 형식을 지정합니다. 이 스크립트는 JSON을 탭과 공백이 포함된 형식으로 출력합니다. 

  ` $JSONContent = Get-Content -Path "<PATH TO THE PROVISIONING LOGS FILE>" | ConvertFrom-JSON`

  `$JSONContent | ConvertTo-Json > <PATH TO OUTPUT THE JSON FILE>`

#### <a name="parse-the-json-file"></a>JSON 파일 구문 분석

PowerShell을 사용하여 JSON 파일로 작업하는 몇 가지 샘플 명령은 다음과 같습니다. 편안하게 사용할 수 있는 모든 프로그래밍 언어를 사용할 수 있습니다.  

먼저 다음 명령을 실행하여 [JSON 파일을 읽습니다](/powershell/module/microsoft.powershell.utility/convertfrom-json).

` $JSONContent = Get-Content -Path "<PATH TO THE PROVISIONING LOGS FILE>" | ConvertFrom-JSON`

이제 시나리오에 따라 데이터를 구문 분석할 수 있습니다. 다음은 몇 가지 예입니다. 

- JSON 파일의 모든 작업 ID를 출력합니다.

  `foreach ($provitem in $JSONContent) { $provitem.jobId }`

- "만들기" 작업이었던 이벤트에 대한 모든 변경 ID를 출력합니다.

  `foreach ($provitem in $JSONContent) { `
  `   if ($provItem.action -eq 'Create') {`
  `       $provitem.changeId `
  `   }`
  `}`

## <a name="what-you-should-know"></a>알아야 할 사항

다음은 보고서를 프로비저닝하기 위한 몇 가지 팁 및 고려 사항입니다.

- Azure Portal에서는 프로비저닝 데이터를 프리미엄 버전이 있는 경우 30일 동안, 체험 버전이 있는 경우 7일 동안 저장합니다. 프로비저닝 로그는 30일을 초과하여 보존하기 위해 [Log Analytics](../app-provisioning/application-provisioning-log-analytics.md)에 게시할 수 있습니다. 

- 변경 ID 특성은 고유 식별자로 사용할 수 있습니다. 예를 들어 제품 지원과 상호 작용할 때 유용합니다.

- 범위에 포함되지 않은 사용자에 대해 건너뛴 이벤트를 표시할 수 있습니다. 이는 특히 동기화 범위가 모든 사용자 및 그룹으로 설정된 경우에 필요합니다. 이 서비스는 범위를 벗어난 개체를 포함하여 테넌트의 모든 개체를 평가합니다. 

- 프로비저닝 로그는 현재 정부 클라우드에서 사용할 수 없습니다. 프로비저닝 로그에 액세스할 수 없는 경우 임시 해결 방법으로 감사 로그를 사용합니다. 

- 프로비저닝 로그는 역할 가져오기를 표시하지 않습니다(AWS, Salesforce 및 Zendesk에 적용됨). 역할 가져오기에 대한 로그는 감사 로그에서 확인할 수 있습니다. 

## <a name="error-codes"></a>오류 코드

다음 표를 사용하여 프로비저닝 로그에서 발견한 오류를 해결하는 방법을 자세히 알아봅니다. 누락된 오류 코드의 경우 이 페이지의 아래쪽에 있는 링크를 사용하여 피드백을 제공합니다. 

|오류 코드|Description|
|---|---|
|Conflict, EntryConflict|Azure AD 또는 애플리케이션에서 충돌하는 특성 값을 수정합니다. 또는 충돌하는 사용자 계정을 일치시키고 인계해야 하는 경우 일치하는 특성 구성을 검토합니다. 일치 특성을 구성하는 방법에 대한 자세한 내용은 [설명서](../app-provisioning/customize-application-attributes.md)를 참조하세요.|
|TooManyRequests|대상 앱이 오버로드되어 너무 많은 요청을 받고 있으므로 사용자를 업데이트하려는 이 시도가 거부되었습니다. 수행할 작업이 없습니다. 이 시도는 자동으로 중지됩니다. 이 문제는 Microsoft에도 알려줍니다.|
|InternalServerError |대상 앱에서 예기치 않은 오류를 반환했습니다. 대상 애플리케이션의 서비스 문제로 인해 이 작업이 작동하지 않을 수 있습니다. 이 시도는 40분 후에 자동으로 중지됩니다.|
|InsufficientRights, MethodNotAllowed, NotPermitted, Unauthorized| Azure AD가 대상 애플리케이션에서 인증되었지만 업데이트를 수행할 수 있는 권한이 없습니다. 각 애플리케이션 [자습서](../saas-apps/tutorial-list.md)와 함께 대상 애플리케이션에서 제공한 지침을 검토합니다.|
|UnprocessableEntity|대상 애플리케이션에서 예기치 않은 응답을 반환했습니다. 대상 애플리케이션의 구성이 올바르지 않거나 대상 애플리케이션의 서비스 문제로 인해 이 작업이 작동하지 않을 수 있습니다.|
|WebExceptionProtocolError |대상 애플리케이션에 연결하는 동안 HTTP 프로토콜 오류가 발생했습니다. 수행할 작업이 없습니다. 이 시도는 40분 후에 자동으로 중지됩니다.|
|InvalidAnchor|프로비저닝 서비스에서 이전에 만들었거나 일치하는 사용자가 더 이상 존재하지 않습니다. 사용자가 존재하는지 확인합니다. 모든 사용자에 대한 새 일치를 적용하려면 Microsoft Graph API를 사용하여 [작업을 다시 시작](/graph/api/synchronization-synchronizationjob-restart?tabs=http&view=graph-rest-beta&preserve-view=true)합니다. <br><br>프로비저닝을 다시 시작하면 초기 주기가 트리거되며, 이를 완료하는 데 시간이 걸릴 수 있습니다. 또한 프로비저닝을 다시 시작하면 프로비저닝 서비스가 작동하는 데 사용하는 캐시도 삭제됩니다. 즉, 테넌트의 모든 사용자 및 그룹을 다시 평가해야 하며 특정 프로비저닝 이벤트가 삭제될 수 있습니다.|
|NotImplemented | 대상 앱에서 예기치 않은 응답을 반환했습니다. 앱의 구성이 올바르지 않거나 대상 앱의 서비스 문제로 인해 이 작업이 작동하지 않을 수 있습니다. 각 애플리케이션 [자습서](../saas-apps/tutorial-list.md)와 함께 대상 애플리케이션에서 제공한 지침을 검토합니다. |
|MandatoryFieldsMissing, MissingValues |필수 값이 없으므로 사용자를 만들 수 없습니다. 원본 레코드에서 누락된 특성 값을 수정하거나, 일치하는 특성 구성을 검토하여 필수 필드가 생략되지 않았는지 확인합니다. 일치 특성을 구성하는 방법에 대해 [자세히 알아보세요](../app-provisioning/customize-application-attributes.md).|
|SchemaAttributeNotFound |대상 애플리케이션에 없는 특성이 지정되었으므로 이 작업을 수행할 수 없습니다. 특성 사용자 지정에 대한 [설명서](../app-provisioning/customize-application-attributes.md)를 참조하여 구성이 올바른지 확인합니다.|
|InternalError |Azure AD 프로비저닝 서비스 내에서 내부 서비스 오류가 발생했습니다. 수행할 작업이 없습니다. 이 시도는 40분 후에 자동으로 다시 시도됩니다.|
|InvalidDomain |특성 값에 잘못된 도메인 이름이 포함되어 있으므로 이 작업을 수행할 수 없습니다. 사용자의 도메인 이름을 업데이트하거나 대상 애플리케이션의 허용 목록에 추가합니다. |
|제한 시간 |대상 애플리케이션에서 응답하는 데 너무 오래 걸려 이 작업을 완료할 수 없습니다. 수행할 작업이 없습니다. 이 시도는 40분 후에 자동으로 다시 시도됩니다.|
|LicenseLimitExceeded|이 사용자에게 사용 가능한 라이선스가 없으므로 대상 애플리케이션에서 사용자를 만들 수 없습니다. 대상 애플리케이션에 대한 추가 라이선스를 얻습니다. 또는 사용자 할당 및 특성 매핑 구성을 검토하여 올바른 사용자에게 올바른 특성이 할당되었는지 확인합니다.|
|DuplicateTargetEntries  |대상 애플리케이션에서 둘 이상의 사용자에게 구성된 일치 특성이 있으므로 이 작업을 완료할 수 없습니다. 대상 애플리케이션에서 중복 사용자를 제거하거나 [특성 매핑을 다시 구성](../app-provisioning/customize-application-attributes.md)합니다.|
|DuplicateSourceEntries | 둘 이상의 사용자에게 구성된 일치 특성이 있으므로 이 작업을 완료할 수 없습니다. 중복 사용자를 제거하거나 [특성 매핑을 다시 구성](../app-provisioning/customize-application-attributes.md)합니다.|
|ImportSkipped | 각 사용자가 평가되면 시스템에서 원본 시스템의 사용자를 가져오려고 시도합니다. 이 오류는 일반적으로 가져올 사용자에게 특성 매핑에 정의된 일치 속성이 없을 때 발생합니다. 일치 특성에 대한 사용자 개체에 값이 없는 경우 시스템에서 범위 지정, 일치 또는 변경 내용 내보내기를 평가할 수 없습니다. 이 오류가 있는 경우 아직 사용자에 대한 범위를 평가하지 않았으므로 사용자가 범위 내에 있음을 나타내는 것은 아닙니다.|
|EntrySynchronizationSkipped | 프로비저닝 서비스에서 원본 시스템을 성공적으로 쿼리하고 사용자를 식별했습니다. 사용자에 대한 추가 작업을 수행하지 않았으므로 건너뛰었습니다. 사용자가 범위를 벗어났거나 더 이상 변경할 필요 없이 이미 대상 시스템에 있을 수 있습니다.|
|SystemForCrossDomainIdentityManagementMultipleEntriesInResponse| 사용자 또는 그룹을 검색하기 위한 GET 요청의 응답에서 여러 사용자 또는 그룹을 받았습니다. 시스템은 응답에서 하나의 사용자 또는 그룹만 받아합니다. [예를 들어](../app-provisioning/use-scim-to-provision-users-and-groups.md#get-group) GET 요청을 수행하여 그룹을 검색하고 멤버를 제외하는 필터를 제공하는 경우 SCIM(System for Cross-Domain Identity Management) 엔드포인트에서 멤버를 반환하면 이 오류가 발생합니다.|

## <a name="next-steps"></a>다음 단계

* [사용자 프로비저닝 상태 확인](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md)
* [Azure AD 갤러리 애플리케이션에 대해 사용자 프로비전 구성 문제](../app-provisioning/application-provisioning-config-problem.md)
* [프로비저닝 로그에 대한 Graph API](/graph/api/resources/provisioningobjectsummary)
