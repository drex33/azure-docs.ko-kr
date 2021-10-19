---
title: Azure 의료 Api에 대 한 로깅
description: 이 문서에서는 로깅 작동 방법 및 Azure 의료 Api에 대 한 로깅을 사용 하도록 설정 하는 방법을 설명 합니다.
services: healthcare-apis
author: ginalee-dotcom
ms.service: healthcare-apis
ms.topic: tutorial
ms.date: 08/16/2021
ms.author: ginle
ms.openlocfilehash: b71e6088a1a60c17ec16c1a5a265cf20fec53777
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124787530"
---
# <a name="logging-for-azure-healthcare-apis-preview"></a>Azure 의료 Api (미리 보기)에 대 한 로깅

> [!IMPORTANT]
> Azure 의료 Api는 현재 미리 보기로 제공 됩니다. [Microsoft Azure 미리 보기에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)에는 베타 또는 미리 보기로 제공되거나 아직 일반 공급으로 릴리스되지 않은 Azure 기능에 적용되는 추가 약관이 포함되어 있습니다.

Azure 플랫폼은 세 가지 유형의 로그, 활동 로그, 리소스 로그 및 Azure Active Directory 로그를 제공 합니다. [활동 로그](../azure-monitor/essentials/platform-logs-overview.md)에 대 한 자세한 내용을 참조 하세요. 이 문서에서는 Azure 의료 Api에 대 한 로깅이 작동 하는 방식에 대해 설명 합니다.

## <a name="auditlogs"></a>AuditLogs
활동 로그는 Azure Portal에서 각 Azure 리소스에 대해 사용할 수 있지만 의료 Api는 두 가지 범주의 로그 인 AuditLogs 및 DiagnosticLogs를 포함 하는 리소스 로그를 내보냅니다.

- AuditLogs는 사용자 또는 응용 프로그램이 FHIR 서비스에 액세스할 때 호출자의 ip 주소 및 리소스 url과 같은 의료 서비스에 대 한 감사 내역을 제공 합니다. 각 서비스는 필수 속성을 내보내고 선택적으로 추가 속성을 구현 합니다.
- DiagnosticLogs는 로그 수준 (정보, 경고 또는 오류) 및 로그 메시지와 같은 서비스 작업에 대 한 통찰력을 제공 합니다.

현재 의료 Api는 공개 미리 보기용 AuditLogs만 지원 합니다. DiagnosticLogs는 서비스를 일반적으로 사용할 수 있을 때 사용할 수 있습니다.

다음은 감사 로그의 한 예입니다.

```
{
    "time": "2021-08-02 16:01:29Z",
    "resourceId": "/SUBSCRIPTIONS/xxx/RESOURCEGROUPS/xxx/PROVIDERS/MICROSOFT.HEALTHCAREAPIS/SERVICES/xxx",
    "operationName": "Microsoft.HealthcareApis/services/fhir-R4/search-type",
    "category": "AuditLogs",
    "resultType": "Started",
    "resultSignature": 0,
    "durationMs": 0,
    "callerIpAddress": "::ffff:73.164.17.31",
    "correlationId": "5d04211aaf172d43b83d9eb500464ec5",
    "identity": {
        "claims": {
            "iss": "https://sts.windows.net/xxx/",
            "oid": "xxx"
        }
    },
    "level": "Informational",
    "location": "South Central US",
    "uri": "https://xxx.azurehealthcareapis.com:443/Patient",
    "properties": {
        "fhirResourceType": "Patient"
    }
}
```

## <a name="next-steps"></a>다음 단계

[여기](../azure-monitor/essentials/metrics-supported.md)Azure Monitor에서 의료 api에 대해 지원 되는 최신 메트릭을 찾을 수 있습니다.

DICOM 서비스 로그 및 메트릭에 대 한 자세한 내용은 [여기](./dicom/enable-diagnostic-logging.md)를 참조 하세요.

IoT Connector 로그 및 메트릭에 대 한 자세한 내용은 [여기](./azure-api-for-fhir/iot-metrics-display.md)를 참조 하세요.