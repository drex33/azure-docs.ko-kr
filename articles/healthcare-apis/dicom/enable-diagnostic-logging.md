---
title: DICOM 서비스에서 진단 로깅 사용-Azure 의료 Api
description: 이 문서에서는 DICOM 서비스에서 진단 로깅을 사용 하도록 설정 하는 방법을 설명 합니다.
author: stevewohl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: conceptual
ms.date: 07/10/2021
ms.author: aersoy
ms.openlocfilehash: 51a162ccbad5813a559cf27c26adcd964adeaf07
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122435190"
---
# <a name="enable-diagnostic-logging-in-the-dicom-service"></a>DICOM 서비스에서 진단 로깅 사용

> [!IMPORTANT]
> Azure 의료 Api는 현재 미리 보기로 제공 됩니다. [Microsoft Azure 미리 보기에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)에는 베타 또는 미리 보기로 제공되거나 아직 일반 공급으로 릴리스되지 않은 Azure 기능에 적용되는 추가 약관이 포함되어 있습니다.

이 문서에서는 DICOM 서비스에서 진단 로깅을 사용 하도록 설정 하 고 이러한 로그에 대 한 몇 가지 샘플 쿼리를 검토할 수 있는 방법에 대해 설명 합니다. 규정 요구 사항을 준수 해야 하는 의료 서비스에는 진단 로그에 대 한 액세스가 반드시 필요 합니다. DICOM 서비스의 기능을 통해 진단 로그는 Azure Portal의 [진단 설정](../../azure-monitor/essentials/diagnostic-settings.md) 입니다. 

## <a name="enable-audit-logs"></a>감사 로그 사용

1. 진단 로깅 DICOM 서비스를 사용 하도록 설정 하려면 Azure Portal에서 DICOM 서비스를 선택 합니다.
2. **활동 로그** 블레이드를 선택 하 고 **진단 설정** 을 선택 합니다.

   [![Azure 활동 로그. ](media/dicom-activity-log.png) ](media/dicom-activity-log.png#lightbox)

3. **+ 진단 설정 추가** 를 선택합니다.

   [![진단 설정을 추가 합니다. ](media/add-diagnostic-settings.png) ](media/add-diagnostic-settings.png#lightbox)

4. **진단 설정 이름을** 입력 합니다.

   [![진단 설정을 구성 합니다. ](media/configure-diagnostic-settings.png) ](media/configure-diagnostic-settings.png#lightbox)

5. 진단 로그에 액세스 하기 위한 **범주** 및 **대상** 세부 정보를 선택 합니다.

   * Azure Monitor에서 **Log Analytics 작업 영역으로 보냅니다** . 이 옵션을 선택 하려면 먼저 로그 분석 작업 영역을 만들어야 합니다. 플랫폼 로그에 대 한 자세한 내용은 [Azure platform Logs 개요](../../azure-monitor/essentials/platform-logs-overview.md)를 참조 하세요.
   * 감사 또는 수동 검사를 위해 **저장소 계정에 보관** 합니다. 사용 하려는 저장소 계정이 이미 만들어져 있어야 합니다.
   * 타사 서비스 또는 사용자 지정 분석 솔루션에서 수집 하기 위해 **이벤트 허브로 스트림** 합니다. 이 단계를 구성 하려면 먼저 event hubs 네임 스페이스 및 이벤트 허브 정책을 만들어야 합니다.
   * Azure에서 파트너 조직으로 작업 중인 **파트너 솔루션에 보냅니다** . 잠재적 파트너 통합에 대 한 자세한 내용은 [Azure 파트너 솔루션 설명서](../../partner-solutions/overview.md) 를 참조 하세요.

     지원 되는 메트릭에 대 한 자세한 내용은 [Azure Monitor에서 지원 되는 메트릭](.././../azure-monitor/essentials/metrics-supported.md)을 참조 하세요.

6. **저장** 을 선택합니다.


   > [!Note] 
   > Log Analytics에 첫 번째 로그가 표시 되는 데 최대 15 분이 걸릴 수 있습니다. 또한 DICOM 서비스를 한 리소스 그룹에서 다른 리소스 그룹으로 이동 하는 경우 이동이 완료 되 면 설정을 업데이트 합니다. 
 
   진단 로그로 작업 하는 방법에 대 한 자세한 내용은 [Azure 리소스 로그 설명서](../../azure-monitor/essentials/platform-logs-overview.md) 를 참조 하세요.

## <a name="audit-log-details"></a>감사 로그 세부 정보

DICOM 서비스는 감사 로그에서 다음 필드를 반환 합니다. 

|필드 이름  |Type  |참고  |
|---------|---------|---------|
|correlationId|String|상관관계 ID
|category|String|로그 범주 (현재 ' AuditLogs '가 있습니다.) 
|operationName|String|작업 유형 (예: 검색, 저장, 쿼리 등)을 설명 합니다. 
|time|DateTime|이벤트의 날짜와 시간입니다. 
|resourceId|String| 리소스에 대 한 Azure 경로입니다.
|identity|동적|Id 정보를 포함 하는 일반 속성 모음 (현재 DICOM에는 적용 되지 않음)
|callerIpAddress|String|호출자의 IP 주소입니다.
|Location|String|요청을 처리 한 서버의 위치입니다.
|uri|String|요청 URI입니다.
|resultType|String| 현재 사용 가능한 값이 시작, 성공 또는 실패입니다.
|resultSignature|Int|HTTP 상태 코드 (예: 200)
|properties|String|리소스 유형, 리소스 이름, 구독 ID, 감사 동작 등을 비롯 한 속성에 대해 설명 합니다.
|형식|String|로그 유형 (이 경우 항상 MicrosoftHealthcareApisAuditLog).
|Level|String|로그 수준 (정보, 오류)입니다.
|operationVersion|String| 현재 비어 있습니다. Api 버전을 표시 하는 데 활용 됩니다.


## <a name="sample-queries"></a>샘플 쿼리

다음은 로그 데이터를 탐색 하는 데 사용할 수 있는 몇 가지 기본적인 Application Insights 쿼리입니다.

다음 쿼리를 실행 하 여 **100** 최신 로그를 확인 합니다.

```Application Insights
MicrosoftHealthcareApisAuditLogs
| limit 100
```

다음 쿼리를 실행 하 여 **DICOM 리소스 유형별로** 작업을 그룹화 합니다.

```Application Insights
MicrosoftHealthcareApisAuditLogs 
| summarize count() by DICOMResourceType
```

다음 쿼리를 실행 하 여 실패 한 모든 결과를 가져옵니다 **.**

```Application Insights
MicrosoftHealthcareApisAuditLogs 
| where ResultType == "Failed" 
```

## <a name="conclusion"></a>결론

진단 로그에 대 한 액세스 권한은 서비스를 모니터링 하 고 규정 준수 보고서를 제공 하는 데 필수적입니다. DICOM 서비스를 사용 하면 진단 로그를 통해 이러한 작업을 수행할 수 있습니다. 

## <a name="next-steps"></a>다음 단계
이 문서에서는 DICOM 서비스에 대해 감사 로그를 사용 하도록 설정 하는 방법을 알아보았습니다. Azure 활동 로그에 대 한 자세한 내용은을 참조 하세요.
 
>[!div class="nextstepaction"]
>[Azure 활동 로그 이벤트 스키마](.././../azure-monitor/essentials/activity-log-schema.md)
