---
title: Azure 가상 네트워크 데이터 참조 모니터링
description: Azure 가상 네트워크를 모니터링할 때 필요한 중요 참조 자료입니다.
author: duongau
ms.topic: reference
ms.author: duau
ms.service: expressroute
ms.custom: subject-monitoring
ms.date: 06/29/2021
ms.openlocfilehash: 2494c0c0cdb2b4a7b06b4cf246f70a46deb8a3a4
ms.sourcegitcommit: 98308c4b775a049a4a035ccf60c8b163f86f04ca
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/30/2021
ms.locfileid: "113109866"
---
# <a name="monitoring-azure-virtual-network-data-reference"></a>Azure 가상 네트워크 데이터 참조 모니터링

Azure 가상 네트워크의 모니터링 데이터를 수집하고 분석하는 방법에 관한 자세한 내용은 [Azure 가상 네트워크 모니터링](monitor-virtual-network.md)을 참조하세요.

## <a name="metrics"></a>메트릭

해당 섹션에서는 Azure 가상 네트워크에 대해 수집되는 모든 자동 수집 플랫폼 메트릭을 나열합니다.  

| 메트릭 유형 | 리소스 공급자/형식 네임스페이스<br/> 및 개별 메트릭 링크 |
|-------|-----|
| 가상 네트워크 | [Microsoft.Network/virtualNetworks](../azure-monitor/essentials/metrics-supported.md#microsoftnetworkvirtualnetworks) |
| 네트워크 인터페이스 | [Microsoft.Network/networkInterfaces](../azure-monitor/essentials/metrics-supported.md#microsoftnetworknetworkinterfaces) |
| 공용 IP 주소 | [Microsoft.Network/publicIPAddresses](../azure-monitor/essentials/metrics-supported.md#microsoftnetworkpublicipaddresses) |
| NAT 게이트웨이 | [Microsoft.Network/natGateways](../azure-monitor/essentials/metrics-supported.md#microsoftnetworkpublicipaddresses)

보다 자세한 정보는 [Azure Monitor에서 지원되는 모든 플랫폼 메트릭](../azure-monitor/essentials/metrics-supported.md)을 참조하세요.

## <a name="metric-dimensions"></a>메트릭 차원

메트릭 차원에 대한 자세한 내용은 [다차원 메트릭](../azure-monitor/essentials/data-platform-metrics.md#multi-dimensional-metrics)을 참조하세요.

Azure 가상 네트워크에는 해당 메트릭과 관련된 다음 차원이 있습니다.

### <a name="dimensions-for-nat-gateway"></a>NAT 게이트웨이의 차원

| 차원 이름 | Description |
| ------------------- | ----------------- |
| **방향(외부 - 내부)** | 트래픽 흐름의 방향입니다. 지원되는 값은 내부 및 외부입니다. |
| **프로토콜** | 전송 프로토콜 유형입니다. 지원되는 값은 TCP 및 UDP입니다. |

## <a name="resource-logs"></a>리소스 로그

이 섹션에는 Azure 가상 네트워크에서 사용되는 리소스에 대해 수집할 수 있는 리소스 로그 유형이 나열되어 있습니다. 

[Azure Monitor에서 지원되는 모든 리소스 로그 범주 유형](../azure-monitor/essentials/resource-logs-schema.md) 목록을 참조하세요.

|리소스 로그 유형 | 리소스 공급자/형식 네임스페이스<br/> 및 개별 메트릭 링크 |
|-------|-----|
| 네트워크 보안 그룹 | [Microsoft.Network/networksecuritygroups](../azure-monitor/essentials/resource-logs-categories.md#microsoftnetworknetworksecuritygroups) |
| 공용 IP 주소 | [Microsoft.Network/publicIPAddresses](../azure-monitor/essentials/resource-logs-categories.md#microsoftnetworkpublicipaddresses) |

## <a name="azure-monitor-logs-tables"></a>Azure Monitor 로그 테이블

이 섹션은 Azure 가상 네트워크와 관련되고 Log Analytics의 쿼리에 사용할 수 있는 모든 Azure Monitor 로그 Kusto 테이블을 참조합니다. 

|리소스 종류 | 참고 |
|-------|-----|
| 가상 네트워크 | [Microsoft.Network/virtualNetworks](/azure/azure-monitor/reference/tables/tables-resourcetype#virtual-networks) |
| 네트워크 인터페이스 | [Microsoft.Network/networkInterface](/azure/azure-monitor/reference/tables/tables-resourcetype#network-interfaces) |
| 공용 IP 주소 | [Microsoft.Network/publicIP](/azure/azure-monitor/reference/tables/tables-resourcetype#public-ip-addresses) |

### <a name="diagnostics-tables"></a>진단 테이블

**가상 네트워크**

Azure 가상 네트워크에는 진단 로그가 없습니다.

## <a name="activity-log"></a>활동 로그

다음 테이블에는 활동 로그에서 만들어질 수 있는 Azure 가상 네트워크 관련 작업이 나열되어 있습니다.

| 작업(Operation) | Description |
|:---|:---|
| 모든 관리 작업 | Azure 가상 네트워크의 만들기, 업데이트, 삭제를 포함한 모든 관리 작업입니다. |
| 가상 네트워크 만들기 또는 업데이트 | 가상 네트워크가 만들어지거나 업데이트되었습니다. |
| 가상 네트워크 삭제 | 가상 네트워크가 삭제되었습니다.|

활동 로그 항목의 스키마에 관한 자세한 내용은 [활동 로그 스키마](../azure-monitor/essentials/activity-log-schema.md)를 참조하세요.

## <a name="see-also"></a>참고 항목

- Azure 가상 네트워크 모니터링에 관한 설명은 [Azure 가상 네트워크 모니터링](monitor-virtual-network.md)을 참조하세요.
- Azure 리소스 모니터링에 대한 자세한 내용은 [Azure Monitor를 사용하여 Azure 리소스 모니터링](../azure-monitor/essentials/monitor-azure-resource.md)을 참조하세요.
