---
title: 모니터링 Azure Key Vault 데이터 참조
description: Key Vault를 모니터링할 때 필요한 중요 한 참조 자료
author: msmbaldwin
ms.topic: reference
ms.author: mbaldwin
ms.service: key-vault
ms.custom: subject-monitoring
ms.date: 07/07/2021
ms.openlocfilehash: 16014586093d911e3ed35124e45e1ed2db41fe78
ms.sourcegitcommit: 48500a6a9002b48ed94c65e9598f049f3d6db60c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/26/2021
ms.locfileid: "129061108"
---
# <a name="monitoring-key-vault-data-reference"></a>모니터링 Key Vault 데이터 참조

Key Vault 모니터링 데이터 수집 및 분석에 대 한 자세한 내용은 [Key Vault 모니터링](monitor-key-vault.md) 을 참조 하세요.

## <a name="metrics"></a>메트릭


이 섹션에서는 Key Vault에 대해 수집 된 자동으로 수집 된 모든 플랫폼 메트릭을 나열 합니다.  

|메트릭 유형 | 리소스 공급자/형식 네임스페이스<br/> 및 개별 메트릭 링크 |
|-------|-----|
| Key Vault | [Microsoft.KeyVault/vaults](/azure-monitor/essentials/metrics-supported#microsoftkeyvaultvaults) |
| 관리형 HSM | [Microsoft. KeyVault/managedhsms](/azure-monitor/platform/resource-logs-categories.#microsoftkeyvaultmanagedhsms) 

### <a name="key-vault-metrics"></a>Key Vault 메트릭

리소스 공급자 및 유형: [Microsoft. KeyVault/자격](/azure-monitor/essentials/metrics-supported#microsoftkeyvaultvaults) 증명 모음

| 이름 | 메트릭 | 단위 | 유형 | 설명 |
|:-------|:-----|:------------|:------------------|
| 전체 자격 증명 모음 가용성 | 가용성      | 백분율    | 평균 | 자격 증명 모음 요청 가용성            | 
| 전체 자격 증명 모음 채도 | SaturationShoebox | 백분율 | 평균| 사용된 자격 증명 모음 용량 | 
| Service API 총 방문 횟수 | ServiceApiHit | 개수 | 개수 | 총 서비스 API 적중 횟수 |
| 전체 Service API 대기 시간 | ServiceApiLatency | 밀리초 | 평균 | 서비스 API 요청의 전체 대기 시간 |
| Service API 총 결과 | ServiceApiResult | 개수 | 개수 | Service API의 총 결과 수 |

보다 자세한 정보는 [Azure Monitor에서 지원되는 모든 플랫폼 메트릭](/azure-monitor/platform/metrics-supported)을 참조하세요.

## <a name="metric-dimensions"></a>메트릭 차원

메트릭 차원에 대한 자세한 내용은 [다차원 메트릭](/azure-monitor/platform/data-platform-metrics#multi-dimensional-metrics)을 참조하세요.

Key Vault에는 해당 메트릭과 관련 된 다음과 같은 차원이 있습니다.

- ActivityType
- ActivityName
- TransactionType
- StatusCode
- StatusCodeClass

## <a name="resource-logs"></a>리소스 로그

이 섹션에서는 Key Vault에 대해 수집할 수 있는 리소스 로그 유형을 나열 합니다.

참조에 대해서는 [Microsoft. KeyVault/자격](/azure-monitor/essentials/resource-logs-categories#microsoftkeyvaultvaults)증명 모음 목록을 참조 하십시오.  자세한 내용은 [Azure Key Vault 로깅](logging.md)을 참조 하세요.

|리소스 로그 유형 | 리소스 공급자/형식 네임스페이스<br/> 및 개별 메트릭 링크 |
|-------|-----|
| Key Vault | [Microsoft.KeyVault/vaults](/azure-monitor/essentials/resource-logs-categories#microsoftkeyvaultmanagedhsms) |
| 관리형 HSM | [Microsoft. KeyVault/managedhsms](/azure-monitor/essentials/resource-logs-categories#microsoftkeyvaultvaults) 

## <a name="azure-monitor-logs-tables"></a>Azure Monitor Logs 테이블

이 섹션에서는 Key Vault와 관련 된 모든 Azure Monitor 로그 테이블을 참조 하 고 Log Analytics 쿼리를 사용할 수 있습니다. 

|리소스 종류 | 참고 |
|-------|-----|
| [Key Vault](/azure-monitor/reference/tables/tables-resourcetype#key-vaults) | |

모든 Azure Monitor Logs/Log Analytics 테이블에 대한 참조는 [Azure Monitor 로그 테이블 참조](/azure-monitor/reference/tables/tables-resourcetype)를 확인하세요.

### <a name="diagnostics-tables"></a>진단 테이블

Key Vault는 [Azure 진단](/azure-monitor/reference/tables/azurediagnostics), [azure 활동](/azure-monitor/reference/tables/azureactivity) 테이블 및 [azure 메트릭](/azure-monitor/reference/tables/azuremetrics) 테이블을 사용 하 여 리소스 로그 정보를 저장 합니다. 다음 열이 관련됩니다.

**Azure Diagnostics**

| 속성 | Description |
|:--- |:---|
| _ResourceId | 레코드가 연결된 리소스의 고유 식별자입니다. |
| CallerIPAddress | 가용성을 기반으로 작업 UPN 클레임 또는 SPN 클레임을 수행한 사용자의 IP 주소입니다. |
| DurationMs | 밀리초 단위의 작업 기간입니다. |
| httpStatusCode_d | 요청에서 반환한 HTTP 상태 코드(예: *200*)입니다. |
| Level | 이벤트의 수준입니다. 중요, 오류, 경고, 정보 및 자세한 정보 표시 값 중 하나입니다. |
| OperationName | 작업 이름 (예: 경고) |
| properties_s |  |
| Region_s | |
| requestUri_s | 클라이언트 요청의 URI입니다. |
| 리소스 | |
| ResourceProvider | 메트릭을 보고 하는 Azure 리소스의 리소스 공급자입니다. |
| ResultSignature | |
| TimeGenerated | 레코드가 만들어진 날짜 및 시간 |

## <a name="see-also"></a>참조

- [모니터링 Azure Key Vault](monitor-key-vault.md) 대한 설명은 모니터링 Azure Key Vault 참조하세요.
- Azure 리소스 모니터링에 대한 자세한 내용은 [Azure Monitor를 사용하여 Azure 리소스 모니터링](/azure/azure-monitor/insights/monitor-azure-resources)을 참조하세요.