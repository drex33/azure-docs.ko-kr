---
title: Azure Firewall 로그 및 메트릭 개요
description: 방화벽 로그를 사용하여 Azure Firewall을 모니터링할 수 있습니다. 또한 Azure Firewall 리소스에서 작업을 감사하려면 활동 로그를 사용할 수 있습니다.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 04/02/2021
ms.author: victorh
ms.openlocfilehash: 504ed485115f48b252027431a5b69e2a6b5f33f3
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124763579"
---
# <a name="azure-firewall-logs-and-metrics"></a>Azure Firewall 로그 및 메트릭

방화벽 로그를 사용하여 Azure Firewall을 모니터링할 수 있습니다. 또한 Azure Firewall 리소스에서 작업을 감사하려면 활동 로그를 사용할 수 있습니다.

이러한 로그 중 일부는 포털을 통해 액세스할 수 있습니다. 로그를 [Azure Monitor 로그](../azure-monitor/insights/azure-networking-analytics.md), 스토리지 및 Event Hubs로 보내고 Azure Monitor 로그 또는 Excel 및 Power BI와 같은 다른 도구에서 분석합니다.

메트릭은 경량이며 거의 실시간 시나리오를 지원할 수 있으므로 경고 및 신속한 문제 감지에 유용합니다.

## <a name="diagnostic-logs"></a>진단 로그

 다음 진단 로그는 Azure Firewall에 사용할 수 있습니다.

* **애플리케이션 규칙 로그**

   애플리케이션 규칙 로그는 각 Azure Firewall에 대해 사용하도록 설정하는 경우에만 스토리지 계정에 저장되고, 이벤트 허브로 스트리밍되며 Azure Monitor 로그로 전송됩니다. 허용/거부된 연결에 대한 로그에서 구성된 애플리케이션 규칙 결과 중 하나와 일치하는 각 새 연결입니다. 데이터는 다음 예제와 같이 JSON 형식으로 로깅됩니다.

   ```
   Category: application rule logs.
   Time: log timestamp.
   Properties: currently contains the full message.
   note: this field will be parsed to specific fields in the future, while maintaining backward compatibility with the existing properties field.
   ```

   ```json
   {
    "category": "AzureFirewallApplicationRule",
    "time": "2018-04-16T23:45:04.8295030Z",
    "resourceId": "/SUBSCRIPTIONS/{subscriptionId}/RESOURCEGROUPS/{resourceGroupName}/PROVIDERS/MICROSOFT.NETWORK/AZUREFIREWALLS/{resourceName}",
    "operationName": "AzureFirewallApplicationRuleLog",
    "properties": {
        "msg": "HTTPS request from 10.1.0.5:55640 to mydestination.com:443. Action: Allow. Rule Collection: collection1000. Rule: rule1002"
    }
   }
   ```

   ```json
   {
     "category": "AzureFirewallApplicationRule",
     "time": "2018-04-16T23:45:04.8295030Z",
     "resourceId": "/SUBSCRIPTIONS/{subscriptionId}/RESOURCEGROUPS/{resourceGroupName}/PROVIDERS/MICROSOFT.NETWORK/AZUREFIREWALLS/{resourceName}",
     "operationName": "AzureFirewallApplicationRuleLog",
     "properties": {
         "msg": "HTTPS request from 10.11.2.4:53344 to www.bing.com:443. Action: Allow. Rule Collection: ExampleRuleCollection. Rule: ExampleRule. Web Category: SearchEnginesAndPortals"
     }
   }
   ```

* **네트워크 규칙 로그**

   네트워크 규칙 로그는 각 Azure Firewall에 대해 사용하도록 설정하는 경우에만 스토리지 계정에 저장되고, 이벤트 허브로 스트리밍되며 Azure Monitor 로그로 전송됩니다. 허용/거부된 연결에 대한 로그에서 구성된 네트워크 규칙 결과 중 하나와 일치하는 각 새 연결입니다. 데이터는 각각 다음 예제와 같이 JSON 형식으로 로깅됩니다.

   ```
   Category: network rule logs.
   Time: log timestamp.
   Properties: currently contains the full message.
   note: this field will be parsed to specific fields in the future, while maintaining backward compatibility with the existing properties field.
   ```

   ```json
  {
    "category": "AzureFirewallNetworkRule",
    "time": "2018-06-14T23:44:11.0590400Z",
    "resourceId": "/SUBSCRIPTIONS/{subscriptionId}/RESOURCEGROUPS/{resourceGroupName}/PROVIDERS/MICROSOFT.NETWORK/AZUREFIREWALLS/{resourceName}",
    "operationName": "AzureFirewallNetworkRuleLog",
    "properties": {
        "msg": "TCP request from 111.35.136.173:12518 to 13.78.143.217:2323. Action: Deny"
    }
   }

   ```

* **DNS 프록시 로그**

   DNS 프록시 규칙 로그는 각 Azure Firewall에 대해 사용하도록 설정하는 경우에만 스토리지 계정에 저장되고, 이벤트 허브로 스트리밍되며 Azure Monitor 로그로 전송됩니다. 이 로그는 DNS 프록시를 사용하여 구성된 DNS 서버에 대한 DNS 메시지를 추적합니다. 데이터는 다음 예제와 같이 JSON 형식으로 로깅됩니다.


   ```
   Category: DNS proxy logs.
   Time: log timestamp.
   Properties: currently contains the full message.
   note: this field will be parsed to specific fields in the future, while maintaining backward compatibility with the existing properties field.
   ```

   성공:
   ```json
   {
     "category": "AzureFirewallDnsProxy",
     "time": "2020-09-02T19:12:33.751Z",
     "resourceId": "/SUBSCRIPTIONS/{subscriptionId}/RESOURCEGROUPS/{resourceGroupName}/PROVIDERS/MICROSOFT.NETWORK/AZUREFIREWALLS/{resourceName}",
     "operationName": "AzureFirewallDnsProxyLog",
     "properties": {
         "msg": "DNS Request: 11.5.0.7:48197 – 15676 AAA IN md-l1l1pg5lcmkq.blob.core.windows.net. udp 55 false 512 NOERROR - 0 2.000301956s"
     }
   }
   ```

   실패:

   ```json
   {
     "category": "AzureFirewallDnsProxy",
     "time": "2020-09-02T19:12:33.751Z",
     "resourceId": "/SUBSCRIPTIONS/{subscriptionId}/RESOURCEGROUPS/{resourceGroupName}/PROVIDERS/MICROSOFT.NETWORK/AZUREFIREWALLS/{resourceName}",
     "operationName": "AzureFirewallDnsProxyLog",
     "properties": {
         "msg": " Error: 2 time.windows.com.reddog.microsoft.com. A: read udp 10.0.1.5:49126->168.63.129.160:53: i/o timeout”
     }
   }
   ```

   메시지 형식:

   `[client’s IP address]:[client’s port] – [query ID] [type of the request] [class of the request] [name of the request] [protocol used] [request size in bytes] [EDNS0 DO (DNSSEC OK) bit set in the query] [EDNS0 buffer size advertised in the query] [response CODE] [response flags] [response size] [response duration]`

로그 저장에는 세 가지 옵션이 있습니다.

* **Storage 계정** - 로그를 장기간 저장하고 필요할 때 검토하는 경우에 가장 적합합니다.
* **이벤트 허브** - 다른 SEIM(보안 정보 및 이벤트 관리) 도구와 통합하여 리소스에 대한 알림을 얻을 수 있는 좋은 옵션입니다.
* **Azure Monitor 로그**: Azure Monitor 로그는 일반적으로 애플리케이션을 실시간으로 모니터링하거나 추세를 파악하는 데 가장 적합합니다.

## <a name="activity-logs"></a>활동 로그

   활동 로그 항목은 기본적으로 수집되고 Azure Portal에서 볼 수 있습니다.

   [Azure 활동 로그](../azure-monitor/essentials/activity-log.md)(이전에는 작업 로그 및 감사 로그라고 함)를 사용하여 Azure 구독에 제출된 모든 작업을 확인할 수 있습니다.

## <a name="metrics"></a>메트릭

Azure Monitor의 메트릭은 특정 시간에 시스템의 일부 측면을 설명하는 숫자 값입니다. 메트릭은 1분마다 수집되며, 자주 샘플링될 수 있으므로 경고에 유용합니다. 비교적 간단한 논리를 사용하여 경고를 신속하게 발생시킬 수 있습니다.

Azure Firewall에 사용할 수 있는 메트릭은 다음과 같습니다.

- **애플리케이션 규칙 적중 횟수** - 애플리케이션 규칙이 적중된 횟수입니다.

    단위: 개수

- **네트워크 규칙 적중 횟수** - 네트워크 규칙이 적중된 횟수입니다.

    단위: 개수

- **처리된 데이터** - 지정된 기간 동안 방화벽을 통과하는 데이터의 합계입니다.

    단위: 바이트

- **처리량** - 초당 방화벽을 통과하는 데이터의 속도입니다.

    단위: 초당 비트

- **방화벽 상태** - SNAT 포트 가용성을 기반으로 하는 방화벽의 상태를 나타냅니다.

    단위: 백분율

   이 메트릭에는 두 개의 차원이 있습니다.
  - 상태: 가능한 값은 *정상*, *저하됨*, *비정상* 입니다.
  - 이유: 해당 방화벽 상태에 대한 이유를 나타냅니다. 

     SNAT 포트를 95%를 초과하여 사용하는 경우 모두 사용된 것으로 간주되고 상태=**저하됨** 및 이유=**SNAT 포트** 인 상태가 50%입니다. 방화벽은 처리 트래픽을 유지하고 기존 연결은 영향을 받지 않습니다. 그러나 새 연결이 간헐적으로 설정되지 않을 수 있습니다.

     SNAT 포트가 95% 미만으로 사용되는 경우 방화벽은 정상으로 간주되고 상태는 100%로 표시됩니다.

     SNAT 포트 사용량이 보고되지 않으면 상태가 0%로 표시됩니다. 

- **SNAT 포트 사용률** - 방화벽에서 사용된 SNAT 포트의 백분율입니다.

    단위: 백분율

   방화벽에 공용 IP 주소를 더 추가하는 경우 SNAT 포트 사용률을 줄여 주는 더 많은 SNAT 포트를 사용할 수 있습니다. 또한 방화벽이 CPU 또는 처리량과 같은 다양한 이유로 스케일 아웃될 경우 추가 SNAT 포트도 사용할 수 있게 됩니다. 따라서 서비스가 스케일 아웃되었다는 이유만으로 공용 IP 주소를 추가하지 않고도 SNAT 포트 사용률이 일정 비율로 떨어질 수 있습니다. 사용 가능한 공용 IP 주소 수를 직접 제어하여 방화벽에서 사용할 수 있는 포트를 늘릴 수 있습니다. 그러나 방화벽 크기 조정을 직접 제어할 수는 없습니다.

   방화벽이 SNAT 포트 소진 상태로 실행 중인 경우 5개 이상의 공용 IP 주소를 추가해야 합니다. 이렇게 하면 사용 가능한 SNAT 포트 수가 늘어납니다. 자세한 내용은 [Azure Firewall 기능](features.md#multiple-public-ip-addresses)을 참조하세요.


## <a name="next-steps"></a>다음 단계

- Azure Firewall 로그 및 메트릭을 모니터링하는 방법은 [자습서: Azure Firewall 로그 모니터링](./firewall-diagnostics.md)을 참조하세요.

- Azure Monitor의 메트릭에 대한 자세한 내용은 [Azure Monitor의 메트릭](../azure-monitor/essentials/data-platform-metrics.md)을 참조하세요.