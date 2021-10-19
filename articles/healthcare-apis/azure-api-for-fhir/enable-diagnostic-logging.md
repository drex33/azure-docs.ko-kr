---
title: Azure API for FHIR 진단 로깅 사용
description: 이 문서에서는 Azure API for FHIR® 진단 로깅을 사용하도록 설정하는 방법을 설명합니다.
services: healthcare-apis
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: conceptual
ms.reviewer: dseven
ms.author: cavoeg
author: zxue
ms.date: 03/03/2021
ms.openlocfilehash: a884dac90273e98868fed6bfe1cbed23b939d286
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122435560"
---
# <a name="enable-diagnostic-logging-in-azure-api-for-fhir"></a>Azure API for FHIR에서 진단 로깅 사용

이 문서에서는 Azure API for FHIR 진단 로깅을 사용하도록 설정하고 이러한 로그에 대한 몇 가지 샘플 쿼리를 검토할 수 있는 방법을 알아봅니다. 진단 로그에 대한 액세스는 규정 요구 사항(예: HIPAA)을 준수해야 하는 모든 의료 서비스에 필수적입니다. 진단 로그를 사용하도록 설정하는 Azure API for FHIR 기능은 Azure Portal [**진단 설정입니다.**](../../azure-monitor/essentials/diagnostic-settings.md) 

## <a name="view-and-download-fhir-metrics-data"></a>FHIR 메트릭 데이터 보기 및 다운로드

모니터링 | 메트릭을 볼 수 있습니다. 포털의 메트릭. 메트릭에는 요청 수, 평균 대기 시간, 오류 수, 데이터 크기, 사용된 RU, 용량을 초과한 요청 수, 가용성(%)이 포함됩니다. 아래 스크린샷은 지난 7일 동안 활동이 거의 없는 샘플 환경에 사용된 RU를 보여줍니다. Json 형식으로 데이터를 다운로드할 수 있습니다.

   :::image type="content" source="media/diagnostic-logging/fhir-metrics-rus-screen.png" alt-text="포털에서 메트릭 Azure API for FHIR" lightbox="media/diagnostic-logging/fhir-metrics-rus-screen.png":::

## <a name="enable-audit-logs"></a>감사 로그 사용
1. Azure API for FHIR 진단 로깅을 사용하도록 설정하려면 Azure Portal Azure API for FHIR 서비스를 선택합니다. 
2. 진단 **설정으로** 이동합니다. 

   :::image type="content" source="media/diagnostic-logging/diagnostic-settings-screen.png" alt-text="Azure FHIR 진단 설정 추가합니다." lightbox="media/diagnostic-logging/diagnostic-settings-screen.png":::

3. **+ 진단 설정 추가** 를 선택합니다

4. 설정의 이름을 입력합니다.

5. 진단 로그에 액세스하는 데 사용할 방법을 선택합니다.

    1. 감사 또는 수동 검사를 **위해 스토리지 계정에 보관합니다.** 사용하려는 스토리지 계정을 이미 만들어야 합니다.
    2. 타사 서비스 또는 사용자 지정 분석 솔루션으로의 스트리밍을 위해 **이벤트 허브로 스트리밍합니다.** 이 단계를 구성하려면 이벤트 허브 네임스페이스 및 이벤트 허브 정책을 만들어야 합니다.
    3. Azure Monitor **Log Analytics** 작업 영역으로 스트리밍합니다. 이 옵션을 선택하려면 Logs Analytics 작업 영역을 만들어야 합니다.

6. **AuditLogs** 및/또는 **AllMetrics 를** 선택합니다. 메트릭에는 서비스 이름, 가용성, 데이터 크기, 총 대기 시간, 총 요청, 총 오류 및 타임스탬프가 포함됩니다. [지원되는 메트릭](../../azure-monitor/essentials/metrics-supported.md#microsofthealthcareapisservices)에 대한 자세한 내용을 확인할 수 있습니다. 

   :::image type="content" source="media/diagnostic-logging/fhir-diagnostic-setting.png" alt-text="Azure FHIR 진단 설정. AuditLogs 및/또는 AllMetrics를 선택합니다." lightbox="media/diagnostic-logging/fhir-diagnostic-setting.png":::

7. **저장** 을 선택합니다.


> [!Note] 
> 첫 번째 로그가 Log Analytics에 표시될 때 최대 15분이 걸릴 수 있습니다. 또한 Azure API for FHIR 리소스 그룹 또는 구독에서 다른 리소스 그룹으로 이동하는 경우 이동이 완료되면 설정을 업데이트합니다. 
 
진단 로그를 작업하는 방법에 대한 자세한 내용은 [Azure 리소스 로그 설명서를 참조하세요.](../../azure-monitor/essentials/platform-logs-overview.md)

## <a name="audit-log-details"></a>감사 로그 세부 정보
현재 Azure API for FHIR 서비스는 감사 로그에 다음 필드를 반환합니다. 

|필드 이름  |Type  |참고  |
|---------|---------|---------|
|CallerIdentity|동적|ID 정보를 포함하는 제네릭 속성 백
|CallerIdentityIssuer|String|발급자 
|CallerIdentityObjectId|String|Object_id 
|CallerIPAddress|String|호출자의 IP 주소 
|CorrelationId|String| 상관관계 ID
|FhirResourceType|String|작업이 실행된 리소스 종류입니다.
|LogCategory|String|로그 범주(현재 'AuditLogs' LogCategory를 반환함)
|Location|String|요청을 처리한 서버의 위치(예: 미국 중남부)
|OperationDuration|Int|이 요청을 완료하는 데 걸린 시간(초)
|OperationName|String| 작업 유형(예: update, search-type)에 대해 설명합니다.
|RequestUri|String|요청 URI 
|ResultType|String|현재 사용 가능한 값은 **시작,** **성공** 또는 **실패입니다.**
|StatusCode|Int|HTTP 상태 코드입니다. (예: 200) 
|TimeGenerated|DateTime|이벤트의 날짜 및 시간|
|속성|String| fhirResourceType의 속성에 대해 설명합니다.
|SourceSystem|String| 원본 시스템(이 경우 항상 Azure)
|TenantId|String|테넌트 ID
|Type|String|로그 유형(이 경우 항상 MicrosoftHealthcareApisAuditLog)
|_ResourceId|String|리소스에 대한 세부 정보

## <a name="sample-queries"></a>샘플 쿼리

다음은 로그 데이터를 탐색하는 데 사용할 수 있는 몇 가지 기본 애플리케이션 Insights 쿼리입니다.

이 쿼리를 실행하여 **100개의 가장 최근** 로그를 확인합니다.

```Application Insights
MicrosoftHealthcareApisAuditLogs
| limit 100
```

이 쿼리를 실행하여 **FHIR 리소스 종류별로** 작업을 그룹화합니다.

```Application Insights
MicrosoftHealthcareApisAuditLogs 
| summarize count() by FhirResourceType
```

이 쿼리를 실행 하 여 실패 한 모든 **결과** 가져오기

```Application Insights
MicrosoftHealthcareApisAuditLogs 
| where ResultType == "Failed" 
```

## <a name="conclusion"></a>결론 
진단 로그에 대 한 액세스 권한은 서비스를 모니터링 하 고 규정 준수 보고서를 제공 하는 데 필수적입니다. FHIR 용 Azure API를 통해 진단 로그를 통해 이러한 작업을 수행할 수 있습니다. 
 
FHIR은 HL7의 등록 상표이며, HL7의 사용 허가 하에 사용됩니다.

## <a name="next-steps"></a>다음 단계
이 문서에서는 FHIR 용 Azure API에 대 한 감사 로그를 사용 하도록 설정 하는 방법을 알아보았습니다. 다음으로, FHIR 용 Azure API에서 구성할 수 있는 다른 추가 설정에 대해 알아봅니다.
 
>[!div class="nextstepaction"]
>[추가 설정](azure-api-for-fhir-additional-settings.md)