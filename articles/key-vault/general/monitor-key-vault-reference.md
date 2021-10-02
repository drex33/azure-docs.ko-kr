---
title: 모니터링 Azure Key Vault 데이터 참조
description: Key Vault 모니터링할 때 필요한 중요한 참조 자료
author: msmbaldwin
ms.topic: reference
ms.author: mbaldwin
ms.service: key-vault
ms.custom: subject-monitoring
ms.date: 07/07/2021
ms.openlocfilehash: d3404dbd28c662734fe7319f83b69b4c45234332
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/01/2021
ms.locfileid: "129356180"
---
# <a name="monitoring-key-vault-data-reference"></a>Key Vault 데이터 참조 모니터링

[Key Vault 모니터링](monitor-key-vault.md) 데이터를 수집하고 분석하는 자세한 내용은 모니터링 Key Vault 참조하세요.

## <a name="metrics"></a>메트릭


이 섹션에서는 Key Vault 대해 수집된 모든 자동으로 수집된 플랫폼 메트릭을 나열합니다.  

|메트릭 유형 | 리소스 공급자/형식 네임스페이스<br/> 및 개별 메트릭 링크 |
|-------|-----|
| Key Vault | [Microsoft.KeyVault/vaults](/azure/azure-monitor/essentials/metrics-supported#microsoftkeyvaultvaults) |
| 관리형 HSM | [Microsoft.KeyVault/managedhsms](/azure/azure-monitor/essentials/resource-logs-categories#microsoftkeyvaultmanagedhsms) 

### <a name="key-vault-metrics"></a>Key Vault 메트릭

리소스 공급자 및 유형: [Microsoft.KeyVault/vaults](/azure/azure-monitor/essentials/metrics-supported#microsoftkeyvaultvaults)

| 이름 | 메트릭 | 단위 | 유형 | Description |
|:-------|:-----|:------------|:------------------|
| 전체 자격 증명 모음 가용성 | 가용성      | 백분율    | 평균 | 자격 증명 모음 요청 가용성            | 
| 전체 자격 증명 모음 채도 | SaturationShoebox | 백분율 | 평균| 사용된 자격 증명 모음 용량 | 
| Service API 총 방문 횟수 | ServiceApiHit | 개수 | 개수 | 총 서비스 API 적중 횟수 |
| 전체 Service API 대기 시간 | ServiceApiLatency | 밀리초 | 평균 | 서비스 API 요청의 전체 대기 시간 |
| Service API 총 결과 | ServiceApiResult | 개수 | 개수 | 총 서비스 API 결과 수 |

보다 자세한 정보는 [Azure Monitor에서 지원되는 모든 플랫폼 메트릭](/azure/azure-monitor/essentials/metrics-supported)을 참조하세요.

## <a name="metric-dimensions"></a>메트릭 차원

메트릭 차원에 대한 자세한 내용은 [다차원 메트릭](/azure/azure-monitor/essentials/data-platform-metrics#multi-dimensional-metrics)을 참조하세요.

Key Vault 메트릭과 연결된 차원은 다음과 같습니다.

- ActivityType
- ActivityName
- TransactionType
- StatusCode
- StatusCodeClass

## <a name="resource-logs"></a>리소스 로그

이 섹션에서는 Key Vault 위해 수집할 수 있는 리소스 로그 유형을 나열합니다.

참조는 [Microsoft.KeyVault/vaults 목록을 참조하세요.](/azure/azure-monitor/essentials/resource-logs-categories#microsoftkeyvaultvaults)  자세한 내용은 [로깅 Azure Key Vault 참조하세요.](logging.md)

|리소스 로그 유형 | 리소스 공급자/형식 네임스페이스<br/> 및 개별 메트릭 링크 |
|-------|-----|
| Key Vault | [Microsoft.KeyVault/vaults](/azure/azure-monitor/essentials/resource-logs-categories#microsoftkeyvaultmanagedhsms) |
| 관리형 HSM | [Microsoft.KeyVault/managedhsms](/azure/azure-monitor/essentials/resource-logs-categories#microsoftkeyvaultvaults) 

## <a name="azure-monitor-logs-tables"></a>Azure Monitor Logs 테이블

이 섹션에서는 Key Vault 관련되고 Log Analytics에서 쿼리에 사용할 수 있는 모든 Azure Monitor Logs Kusto 테이블을 참조합니다. 

|리소스 종류 | 참고 |
|-------|-----|
| [Key Vault](/azure/azure-monitor/reference/tables/tables-resourcetype#key-vaults) | |

모든 Azure Monitor Logs/Log Analytics 테이블에 대한 참조는 [Azure Monitor 로그 테이블 참조](/azure/azure-monitor/reference/tables/tables-resourcetype)를 확인하세요.

### <a name="diagnostics-tables"></a>진단 테이블

Key Vault [Azure Diagnostics,](/azure/azure-monitor/reference/tables/azurediagnostics) [Azure 활동](/azure/azure-monitor/reference/tables/azureactivity) 테이블 및 Azure [Metrics](/azure/azure-monitor/reference/tables/azuremetrics) 테이블을 사용하여 리소스 로그 정보를 저장합니다. 다음 열이 관련됩니다.

**Azure Diagnostics**

| 속성 | Description |
|:--- |:---|
| _ResourceId | 레코드가 연결된 리소스의 고유 식별자입니다. |
| CallerIPAddress | 가용성에 따라 UPN 클레임 또는 SPN 클레임 작업을 수행한 사용자의 IP 주소입니다. |
| DurationMs | 밀리초 단위의 작업 기간입니다. |
| httpStatusCode_d | 요청에서 반환한 HTTP 상태 코드(예: *200*)입니다. |
| Level | 이벤트의 수준입니다. Critical, Error, Warning, Informational 및 Verbose 값 중 하나입니다. |
| OperationName | 작업의 이름(예: 경고) |
| properties_s |  |
| Region_s | |
| requestUri_s | 클라이언트 요청의 URI입니다. |
| 리소스 | |
| ResourceProvider | 메트릭을 보고하는 Azure 리소스의 리소스 공급자입니다. |
| ResultSignature | |
| TimeGenerated | 레코드를 만든 날짜 및 시간 |

## <a name="see-also"></a>참조

- Azure Key Vault 모니터링에 대 한 설명은 [monitoring Azure Key Vault](monitor-key-vault.md) 를 참조 하세요.
- Azure 리소스 모니터링에 대한 자세한 내용은 [Azure Monitor를 사용하여 Azure 리소스 모니터링](/azure/azure-monitor/essentials/monitor-azure-resource)을 참조하세요.
