---
title: Azure 가상 네트워크 모니터링
description: Azure 가상 네트워크를 모니터링하는 방법을 알아보려면 여기를 시작하세요.
services: virtual-network
author: duongau
ms.author: duau
ms.service: virtual-network
ms.topic: how-to
ms.custom: subject-monitoring
ms.date: 06/29/2021
ms.openlocfilehash: bec4d18ebc762ecf573281341e907db4376f26b2
ms.sourcegitcommit: 8b7d16fefcf3d024a72119b233733cb3e962d6d9
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/16/2021
ms.locfileid: "114297640"
---
# <a name="monitoring-azure-virtual-network"></a>Azure 가상 네트워크 모니터링

Azure 리소스를 사용하는 중요한 애플리케이션 및 비즈니스 프로세스가 있는 경우 이와 같은 리소스의 가용성, 성능 및 작업을 모니터링할 수 있습니다. 

이 문서에서는 Azure 가상 네트워크에서 생성된 모니터링 데이터에 대해 설명합니다. Azure 가상 네트워크에서는 [Azure Monitor](../azure-monitor/overview.md)를 사용합니다. 이 기능을 사용하는 모든 Azure 서비스에 공통되는 Azure Monitor 기능에 익숙하지 않은 경우, [Azure Monitor로 Azure 리소스 모니터링](../azure-monitor/essentials/monitor-azure-resource.md)을 읽어 보세요.

## <a name="monitoring-data"></a>데이터 모니터링 

Azure 가상 네트워크는 [Azure 리소스의 모니터링 데이터](../azure-monitor/essentials/monitor-azure-resource.md#monitoring-data)에 설명된 다른 Azure 리소스와 동일한 종류의 모니터링 데이터를 수집합니다. 

Azure 가상 네트워크에서 생성된 메트릭 및 로그 메트릭에 대한 자세한 내용은 [Azure 가상 네트워크 데이터 참조 모니터링](monitor-virtual-network-reference.md)을 참조하세요.

## <a name="collection-and-routing"></a>수집 및 라우팅

플랫폼 메트릭 및 활동 로그는 자동으로 수집되고 저장되지만 진단 설정을 사용하여 다른 위치로 라우팅할 수 있습니다.  

리소스 로그는 진단 설정을 만들고 하나 이상의 위치로 라우팅할 때까지 수집 및 저장되지 않습니다.

Azure Portal, CLI 또는 PowerShell을 사용한 진단 설정 만들기의 자세한 프로세스는 [Azure에서 플랫폼 로그 및 메트릭을 수집하는 진단 설정 만들기](../azure-monitor/essentials/diagnostic-settings.md)를 참조하세요. 진단 설정을 만들 때 수집할 로그 범주를 지정합니다. *Azure 가상 네트워크* 의 범주는 [Azure 가상 네트워크 모니터링 데이터 참조](monitor-virtual-network-reference.md#resource-logs)에 나열됩니다.

> [!IMPORTANT]
> 이러한 설정을 사용하려면 추가 Azure 서비스(스토리지 계정, 이벤트 허브 또는 Log Analytics)가 필요하므로 비용이 늘어날 수 있습니다. 예상 비용을 계산하려면 [Azure 가격 계산기](https://azure.microsoft.com/pricing/calculator)를 방문하세요.

수집할 수 있는 메트릭 및 로그에 대해서는 다음 섹션에서 설명합니다.

## <a name="analyzing-metrics"></a>메트릭 분석

Azure Monitor는 현재 메트릭 탐색기에서 *Azure 가상 네트워크* 메트릭 분석을 지원하지 않습니다. *Azure 가상 네트워크* 메트릭을 보려면 분석할 가상 네트워크의 **모니터링** 아래에 있는 **메트릭** 을 선택합니다.

:::image type="content" source="./media/monitor-virtual-network/metrics.png" alt-text="Virtual Network에 대한 메트릭 대시보드의 스크린샷." lightbox="./media/monitor-virtual-network/metrics-expanded.png":::

Azure 가상 네트워크에 대해 수집되는 플랫폼 메트릭의 목록은 [Azure 가상 네트워크 데이터 참조 메트릭 모니터링](monitor-virtual-network-reference.md#metrics)을 참조하세요.

[Azure Monitor에서 지원되는 모든 리소스 메트릭](../azure-monitor/essentials/metrics-supported.md) 목록을 참조하세요.

## <a name="analyzing-logs"></a>로그 분석

Azure 가상 네트워크는 리소스 로그를 지원하지 않습니다.

가상 네트워크의 리소스에 대해 수집되는 리소스 로그의 유형 목록은 [가상 네트워크 데이터 참조 모니터링](monitor-virtual-network-reference.md#resource-logs)을 참조하세요.   

[활동 로그](../azure-monitor/essentials/activity-log.md)는 구독 수준의 이벤트에 대한 인사이트를 제공하는 Azure의 플랫폼 로그 유형입니다. 활동 로그는 독립적으로 보거나 Azure Monitor Logs로 라우팅할 수 있습니다. 여기서 Log Analytics를 사용하여 훨씬 더 복잡한 쿼리를 수행할 수 있습니다.  

## <a name="alerts"></a>경고

Azure Monitor 경고는 모니터링 데이터에서 중요한 조건이 발견될 때 사용자에게 사전에 알립니다. 이를 통해 고객이 알기 전에 시스템 문제를 식별하고 해결할 수 있습니다. [메트릭](../azure-monitor/alerts/alerts-metric-overview.md), [로그](../azure-monitor/alerts/alerts-unified-log.md) 및 [활동 로그](../azure-monitor/alerts/activity-log-alerts.md)에서 경고를 설정할 수 있습니다. 서로 다른 형식의 경고에는 장점과 단점이 있습니다.

다음 표에는 Azure 가상 네트워크에 대한 일반적인 경고 규칙과 권장 작업 경고 규칙이 나와 있습니다.

| 경고 유형 | 조건 | 설명  |
|:---|:---|:---|
| Virtual Network 만들기 또는 업데이트 | 이벤트 수준: 모두 선택됨, 상태: 모두 선택됨, 이벤트 시작: 모든 서비스 및 사용자 | 사용자가 가상 네트워크를 만들거나 구성을 변경하는 경우 |
| Virtual Network 삭제 | 이벤트 수준: 모두 선택됨, 상태: 시작됨 | 사용자가 가상 네트워크를 삭제하는 경우 |

## <a name="next-steps"></a>다음 단계

* Azure 가상 네트워크에서 만든 메트릭, 로그 및 기타 중요한 값에 대한 참조는 [가상 네트워크 데이터 참조 모니터링](monitor-virtual-network-reference.md)을 참조하세요.
* Azure 리소스 모니터링에 대한 자세한 내용은 [Azure Monitor를 사용하여 Azure 리소스 모니터링](../azure-monitor/overview.md)을 참조하세요.