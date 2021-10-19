---
title: FHIR 서비스에서 진단 설정 보기 및 사용 - Azure Healthcare API
description: 이 문서에서는 FHIR 서비스에서 진단 설정을 사용하도록 설정하고 감사 로그에 대한 몇 가지 샘플 쿼리를 검토하는 방법을 설명합니다.
services: healthcare-apis
author: ginalee-dotcom
ms.service: healthcare-apis
ms.topic: how-to
ms.date: 10/12/2021
ms.author: zxue
ms.openlocfilehash: 7574d4abf20e07019016796e937cf80c7d915128
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/14/2021
ms.locfileid: "130187759"
---
# <a name="view-and-enable-diagnostic-settings-in-the-fhir-service"></a>FHIR 서비스에서 진단 설정 보기 및 사용

진단 로그에 대한 액세스는 모든 의료 서비스에 필수적입니다. HIPAA(Health Insurance Portability and Accountability Act)와 같은 규정 요구 사항을 준수해야 합니다. 이 문서에서는 Azure Healthcare API 내의 FHIR 서비스에서 진단 로그에 대한 설정을 선택하는 방법을 알아봅니다. 이러한 로그에 대한 몇 가지 샘플 쿼리도 검토합니다.

> [!IMPORTANT]
> Azure Healthcare API 서비스는 현재 미리 보기로 제공됩니다. [Microsoft Azure 미리 보기에 대한 추가 사용 약관에는](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) 베타, 미리 보기 또는 아직 일반 공급으로 릴리스되지 않은 Azure 기능에 적용되는 약관이 포함됩니다.

## <a name="steps-to-enable-diagnostic-logs"></a>진단 로그를 사용하도록 설정하는 단계

1. Azure Portal FHIR 서비스를 선택합니다.

2. **모니터링** 아래에서 **진단 설정** 을 선택합니다.

3. **+ 진단 설정 추가를** 선택합니다.

   [![Azure Portal 진단 설정 페이지의 스크린샷. ](media/diagnostic-logs/fhir-diagnostic-settings-screen.png) ](media/diagnostic-logs/fhir-diagnostic-settings-screen.png#lightbox)

4. 진단 **설정 이름** 를 입력합니다.
 
   [![감사 로그를 ](media/diagnostic-logs/fhir-diagnostic-settings-add.png) 사용하거나 사용하지 않도록 설정하기 위한 대상 세부 정보 및 확인란의 스크린샷 ](media/diagnostic-logs/fhir-diagnostic-settings-add.png#lightbox)

5. 로그에 액세스하는 데 사용할 방법을 선택합니다.

   - **Log Analytics 작업 영역으로 보내기는** 로그 및 메트릭을 Azure Monitor Log Analytics 작업 영역으로 보내는 것입니다. 이 옵션을 선택하려면 Log Analytics 작업 영역을 만들어야 합니다.
   
   - **스토리지 계정에 보관은** 감사 또는 수동 검사를 위한 것입니다. 사용하려는 스토리지 계정을 이미 만들어야 합니다. 보존 옵션은 스토리지 계정에만 적용됩니다. 보존 정책의 범위는 1~365일입니다. 보존 정책을 적용하고 데이터를 계속 보존하지 않으려면 보존(일)을 0으로 설정합니다.

   - **이벤트 허브로의 스트림은** 타사 서비스 또는 사용자 지정 분석 솔루션에 의해 스트리밍됩니다. 이 옵션을 구성하려면 이벤트 허브 네임스페이스 및 이벤트 허브 정책을 만들어야 합니다.
   
   - Azure에서 지원하는 파트너 솔루션을 사용하도록 설정한 경우 파트너 솔루션으로 **보내기를** 선택해야 합니다. 자세한 내용은 [파트너의 솔루션으로 Azure 확장을 참조하세요.](../../partner-solutions/overview.md)

6. **AuditLogs를** 선택합니다.

7. **저장** 을 선택합니다.

> [!NOTE]
> 해당 방법을 선택한 경우 첫 번째 로그가 Log Analytics 작업 영역에 표시되는 데 최대 15분이 걸릴 수 있습니다. FHIR 서비스가 한 리소스 그룹 또는 구독에서 다른 리소스 그룹으로 이동되면 이동이 완료된 후 설정을 업데이트합니다.


## <a name="diagnostic-log-details"></a>진단 로그 세부 정보

현재 FHIR 서비스는 진단 로그에 다음 필드를 반환합니다.

|필드 이름|Type|참고|
|----------|----|-----|
|`CallerIdentity` |동적|ID 정보를 포함하는 제네릭 속성 백입니다.|
|`CallerIdentityIssuer` | String| 발급자입니다.|
|`CallerIdentityObjectId` | String| 개체 ID입니다.|
|`CallerIPAddress` | String| 호출자의 IP 주소입니다.|
|`CorrelationId` | String| 상관관계 ID입니다.|
|`FhirResourceType` | String| 작업이 실행된 리소스 종류입니다.|
|`LogCategory` | String| 로그 범주 (이 문서에서는 `AuditLogs` 를 반환합니다.)|
|`Location` | String| 요청을 처리한 서버의 위치입니다. 예: `South Central US`|
|`OperationDuration` | Int| 이 요청을 완료하는 데 걸린 시간(초)입니다.|
|`OperationName` | String| 작업의 유형입니다. 예를 들어 `update` 또는 `search-type`입니다.|
|`RequestUri` | String| 요청 URI입니다.|
|`ResultType` | String| 로그의 상태입니다. 사용 가능한 값은 `Started` , 또는 입니다. `Succeeded` `Failed`|
|`StatusCode` | Int| HTTP 상태 코드입니다. 예: `200`|
|`TimeGenerated` | DateTime| 이벤트의 날짜 및 시간입니다.|
|`Properties` | String| 의 `FhirResourceType` 속성입니다.|
|`SourceSystem` | String| 원본 시스템입니다. 이 경우 항상 `Azure` 입니다.|
|`TenantId` | String | 테넌트 ID입니다.|
|`Type` | String| 로그의 유형입니다. 이 경우에는 항상 `MicrosoftHealthcareApisAuditLog` 입니다.|
|`_ResourceId` | String| 리소스에 대한 세부 정보입니다.|       
        
## <a name="sample-queries"></a>샘플 쿼리

이러한 기본 Application Insights 쿼리를 사용하여 로그 데이터를 탐색할 수 있습니다.

- 다음 쿼리를 실행하여 *100개의 가장 최근* 로그를 확인합니다.

  `Insights
  MicrosoftHealthcareApisAuditLogs
  | limit 100`

- 다음 쿼리를 실행하여 *FHIR 리소스 종류별로* 작업을 그룹화합니다.

  `Insights
  MicrosoftHealthcareApisAuditLogs 
  | summarize count() by FhirResourceType`

- 다음 쿼리를 실행하여 실패한 모든 *결과를 얻습니다.*

  `Insights
  MicrosoftHealthcareApisAuditLogs 
  | where ResultType == "Failed"`   

## <a name="conclusion"></a>결론

진단 로그에 대한 액세스 권한은 서비스를 모니터링하고 규정 준수 보고서를 제공하는 데 필수적입니다. 이 문서에서는 FHIR 서비스에 대해 이러한 로그를 사용하도록 설정하는 방법을 배웠습니다. 

> [!NOTE]
> Azure Healthcare API 서비스를 일반적으로 사용할 수 있게 되면 메트릭이 추가됩니다.

## <a name="next-steps"></a>다음 단계

FHIR 서비스에 대한 개요는 다음을 참조하세요.

>[!div class="nextstepaction"]
>[FHIR 서비스란?](overview.md)   

(FHIR&#174;)는 HL7의 등록 상표이며 [HL7의](https://hl7.org/fhir/) 권한으로 사용됩니다.
