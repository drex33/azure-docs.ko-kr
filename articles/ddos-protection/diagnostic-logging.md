---
title: Azure DDoS Protection 표준 보고서 및 흐름 로그
description: 보고서 및 흐름 로그를 구성하는 방법을 알아봅니다.
services: ddos-protection
documentationcenter: na
author: aletheatoh
ms.service: ddos-protection
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/28/2020
ms.author: yitoh
ms.openlocfilehash: ea85ca0cf1160b4ad738ea45ce33e72d07dc5fbf
ms.sourcegitcommit: deb5717df5a3c952115e452f206052737366df46
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/23/2021
ms.locfileid: "122681398"
---
# <a name="view-and-configure-ddos-diagnostic-logging"></a>DDoS 진단 로깅 보기 및 구성

Azure DDoS Protection 표준은 DDoS 공격 분석을 통해 자세한 공격 인사이트와 시각화를 제공합니다. DDoS 공격으로부터 자신의 가상 네트워크를 보호하는 고객은 공격 완화 보고서 및 완화 흐름 로그를 통해 공격 트래픽 및 공격을 완화하는 데 수행된 작업에 대해 자세히 파악할 수 있습니다. DDoS 공격 기간 동안 상세 메트릭을 비롯한 다양한 원격 분석이 Azure Monitor를 통해 노출됩니다. DDoS Protection에서 노출하는 Azure Monitor 메트릭에 대한 경고를 구성할 수 있습니다. Azure Monitor 진단 인터페이스를 통해 로깅을 [Azure Sentinel](../sentinel/connect-azure-ddos-protection.md), Splunk(Azure Event Hubs), OMS Log Analytics 및 Azure Storage와 통합하면 고급 분석을 사용할 수 있습니다.

Azure DDoS Protection 표준에는 다음 진단 로그를 사용할 수 있습니다. 

- **DDoSProtectionNotifications**: 알림은 공용 IP 리소스가 공격을 받고 있을 때 공격 완화 작업이 끝나면 사용자에게 알려줍니다.
- **DDoSMitigationFlowLogs**: 공격 완화 흐름 로그를 사용하면 활성 DDoS 공격 중에 삭제된 트래픽, 전달된 트래픽 및 기타 흥미로운 데이터 요소를 거의 실시간으로 검토할 수 있습니다. 이벤트 허브를 통해 이 데이터의 지속적인 스트림을 Azure Sentinel 또는 타사 SIEM 시스템으로 수집하여 거의 실시간으로 모니터링하고, 잠재적인 작업을 수행하며, 방어 작업의 필요성을 해결할 수 있습니다.
- **DDoSMitigationReports**: 공격 완화 보고서는 집계된 Netflow 프로토콜 데이터를 사용하여 리소스에 대한 공격에 대한 자세한 정보를 제공합니다. 공용 IP 리소스가 공격을 받을 때마다 완화가 시작되는 즉시 보고서 생성이 시작됩니다. 5분마다 생성되는 증분 보고서와 전체 완화 기간에 대한 사후 완화 보고서가 있습니다. 이렇게 하면 DDoS 공격이 더 오랜 시간 지속되는 경우 5분마다 완화 보고서의 최신 스냅샷을 볼 수 있고 공격 완화가 완료되면 전체 요약을 볼 수 있습니다. 
- **Allmetrics**: DDoS 공격 기간 동안 사용 가능한 모든 메트릭을 제공합니다. 

이 자습서에서 학습할 방법은 다음과 같습니다.

> [!div class="checklist"]
> * 알림, 완화 보고서 및 완화 흐름 로그를 포함하는 DDoS 진단 로그를 구성합니다. 
> * 정의된 범위의 모든 공용 IP에 대해 진단 로깅을 사용하도록 설정합니다.
> * 통합 문서에서 로그 데이터를 봅니다.

## <a name="prerequisites"></a>필수 구성 요소

- Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.
- 이 자습서의 단계를 완료하려면 먼저 [Azure DDoS 표준 보호 계획](manage-ddos-protection.md)을 만들고 가상 네트워크에서 DDoS Protection 표준을 사용하도록 설정해야 합니다.
- DDoS는 가상 네트워크 내의 리소스에 할당된 공용 IP 주소를 모니터링합니다. 가상 네트워크에 공용 IP 주소가 있는 리소스가 없으면 먼저 공용 IP 주소를 사용하여 리소스를 만들어야 합니다. Azure App Service Environment를 제외하고 [Azure 서비스용 가상 네트워크](../virtual-network/virtual-network-for-azure-services.md#services-that-can-be-deployed-into-a-virtual-network)(백 엔드 가상 머신이 가상 네트워크에 있는 Azure Load Balancer 포함)에 나열된 Resource Manager(클래식 아님)를 통해 배포된 모든 리소스의 공용 IP 주소를 모니터링할 수 있습니다. 이 자습서를 계속 진행하려면 [Windows](../virtual-machines/windows/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 또는 [Linux](../virtual-machines/linux/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 가상 머신을 빠르게 만들면 됩니다.    

## <a name="configure-ddos-diagnostic-logs"></a>DDoS 진단 로그 구성

환경 내 모든 공용 IP에서 진단 로깅을 자동으로 사용하도록 설정하려면 [스크립트를 사용하여 모든 공용 IP에 대해 진단 로깅을 사용하도록 설정](#enable-diagnostic-logging-on-all-public-ips)으로 건너뜁니다.

1. 포털의 왼쪽 위에서 **모든 서비스** 를 선택합니다.
2. **필터** 상자에서 *모니터* 를 입력합니다. 결과에 **모니터** 가 표시되면 해당 모니터를 선택합니다.
3. **설정** 아래에서 **진단 설정** 을 선택합니다.
4. 로깅하려는 공용 IP 주소가 포함된 **구독** 및 **리소스 그룹** 을 선택합니다.
5. **리소스 종류** 에 대해 **공용 IP 주소** 를 선택한 다음, 로깅하려는 특정 공용 IP 주소를 선택합니다.
6. **진단 설정 추가** 를 선택합니다. **범주 세부 정보** 에서 다음 옵션을 필요한 만큼 선택한 다음 **저장** 을 선택합니다.

    ![DDoS 진단 설정](./media/ddos-attack-telemetry/ddos-diagnostic-settings.png)

7. **대상 세부 정보** 아래에서 필요한 만큼 다음 옵션을 선택합니다.

    - **스토리지 계정에 보관**: 데이터가 Azure Storage 계정에 기록됩니다. 이 옵션에 대해 자세히 알아보려면 [리소스 로그 보관](../azure-monitor/essentials/resource-logs.md?toc=%2fazure%2fvirtual-network%2ftoc.json#send-to-azure-storage)을 참조하세요.
    - **이벤트 허브로의 스트림**: 로그 수신기에서 Azure Event Hub를 사용하여 로그를 선택할 수 있도록 합니다. 이벤트 허브는 Splunk 또는 기타 SIEM 시스템과 통합할 수 있습니다. 이 옵션에 대한 자세한 내용은 [이벤트 허브로 리소스 로그 스트림](../azure-monitor/essentials/resource-logs.md?toc=%2fazure%2fvirtual-network%2ftoc.json#send-to-azure-event-hubs)을 참조하세요.
    - **Log Analytics에 보내기**: Azure Monitor 서비스에 로그를 씁니다. 이 옵션에 대한 자세한 내용은 [Azure Monitor 로그에서 사용할 로그 수집](../azure-monitor/essentials/resource-logs.md?toc=%2fazure%2fvirtual-network%2ftoc.json#send-to-log-analytics-workspace)을 참조하세요.

### <a name="query-ddos-protection-logs-in-log-analytics-workspace"></a>로그 분석 작업 영역에서 DDOS 보호 로그 쿼리

#### <a name="ddosprotectionnotifications-logs"></a>DDoSProtectionNotifications 로그

1. **Log Analytics 작업 영역** 블레이드 아래에서 로그 분석 작업 영역을 선택합니다.

4. **일반** 아래에서 **로그** 를 클릭합니다.

5. 쿼리 탐색기에서 다음 Kusto 쿼리를 입력하고 시간 범위를 사용자 지정으로 변경한 후 시간 범위를 지난 3개월로 변경합니다. 그런 다음, 실행을 누릅니다.

    ```kusto
    AzureDiagnostics
    | where Category == "DDoSProtectionNotifications"
    ```

#### <a name="ddosmitigationflowlogs"></a>DDoSMitigationFlowLogs

1. 이제 쿼리를 다음으로 변경하고 동일한 시간 범위를 유지한 후 실행을 누릅니다.

    ```kusto
    AzureDiagnostics
    | where Category == "DDoSMitigationFlowLogs"
    ```

#### <a name="ddosmitigationreports"></a>DDoSMitigationReports

1. 이제 쿼리를 다음으로 변경하고 동일한 시간 범위를 유지한 후 실행을 누릅니다.

    ```kusto
    AzureDiagnostics
    | where Category == "DDoSMitigationReports"
    ```

### <a name="log-schemas"></a>로그 스키마

다음 표에는 필드 이름 및 설명이 나와 있습니다.

# <a name="ddosprotectionnotifications"></a>[DDoSProtectionNotifications](#tab/DDoSProtectionNotifications)

| 필드 이름 | Description |
| --- | --- |
| **TimeGenerated** | 알림을 만든 날짜 및 시간(UTC)입니다. |
| **ResourceId** | 공용 IP 주소의 리소스 ID입니다. |
| **범주** | 알림의 경우 `DDoSProtectionNotifications`입니다.|
| **ResourceGroup** | 공용 IP 주소 및 가상 네트워크를 포함하는 리소스 그룹입니다. |
| **SubscriptionId** | DDoS Protection 계획 구독 ID입니다. |
| **리소스** | 공용 IP 주소의 이름입니다. |
| **ResourceType** | 항상 `PUBLICIPADDRESS`입니다. |
| **OperationName** | 알림의 경우 `DDoSProtectionNotifications`입니다.  |
| **Message** | 공격에 대한 세부 정보입니다. |
| **유형** | 알림의 유형입니다. 가능한 값에는 `MitigationStarted`가 포함됩니다. `MitigationStopped`. |
| **PublicIpAddress** | 공용 IP 주소입니다. |

# <a name="ddosmitigationflowlogs"></a>[DDoSMitigationFlowLogs](#tab/DDoSMitigationFlowLogs)

| 필드 이름 | Description |
| --- | --- |
| **TimeGenerated** | 흐름 로그가 생성된 날짜 및 시간(UTC)입니다. |
| **ResourceId** | 공용 IP 주소의 리소스 ID입니다. |
| **범주** | 흐름 로그의 경우 `DDoSMitigationFlowLogs`입니다.|
| **ResourceGroup** | 공용 IP 주소 및 가상 네트워크를 포함하는 리소스 그룹입니다. |
| **SubscriptionId** | DDoS Protection 계획 구독 ID입니다. |
| **리소스** | 공용 IP 주소의 이름입니다. |
| **ResourceType** | 항상 `PUBLICIPADDRESS`입니다. |
| **OperationName** | 흐름 로그의 경우 `DDoSMitigationFlowLogs`입니다. |
| **Message** | 공격에 대한 세부 정보입니다. |
| **SourcePublicIpAddress** | 공용 IP 주소로의 트래픽을 생성하는 클라이언트의 공용 IP 주소입니다. |
| **SourcePort** | 0에서 65535 사이의 포트 번호입니다. |
| **DestPublicIpAddress** | 공용 IP 주소입니다. |
| **DestPort** | 0에서 65535 사이의 포트 번호입니다. |
| **프로토콜** | 프로토콜 유형입니다. 가능한 값에는 `tcp`, `udp`, `other`가 포함됩니다.|

# <a name="ddosmitigationreports"></a>[DDoSMitigationReports](#tab/DDoSMitigationReports)

| 필드 이름 | Description |
| --- | --- |
| **TimeGenerated** | 보고서가 만들어진 날짜 및 시간(UTC)입니다. |
| **ResourceId** | 공용 IP 주소의 리소스 ID입니다. |
| **범주** | 알림의 경우 `DDoSMitigationReports`입니다.|
| **ResourceGroup** | 공용 IP 주소 및 가상 네트워크를 포함하는 리소스 그룹입니다. |
| **SubscriptionId** | DDoS Protection 계획 구독 ID입니다. |
| **리소스** | 공용 IP 주소의 이름입니다. |
| **ResourceType** | 항상 `PUBLICIPADDRESS`입니다. |
| **OperationName** | 완화 보고서의 경우 `DDoSMitigationReports`입니다. |
| **ReportType** | 가능한 값에는 `Incremental`, `PostMitigation`이 포함됩니다.|
| **MitigationPeriodStart** | 완화가 시작된 날짜 및 시간(UTC)입니다.  |
| **MitigationPeriodEnd** | 완화가 종료된 날짜 및 시간(UTC)입니다. |
| **IPAddress** | 공용 IP 주소입니다. |
| **AttackVectors** |  공격 유형을 분석한 것입니다. 키에는 `TCP SYN flood`, `TCP flood`, `UDP flood`, `UDP reflection`, `Other packet flood`가 포함됩니다.|
| **TrafficOverview** |  공격 트래픽을 분석한 것입니다. 키에는 `Total packets`, `Total packets dropped`, `Total TCP packets`, `Total TCP packets dropped`, `Total UDP packets`, `Total UDP packets dropped`, `Total Other packets`, `Total Other packets dropped`가 포함됩니다. |
| **프로토콜** | 관련 프로토콜을 분석한 것입니다. 키에는 `TCP`, `UDP`, `Other`가 포함됩니다. |
| **DropReasons** | 삭제된 패킷의 이유를 분석한 것입니다. 키에는 `Protocol violation invalid TCP syn`, `Protocol violation invalid TCP`, `Protocol violation invalid UDP`, `UDP reflection`, `TCP rate limit exceeded`, `UDP rate limit exceeded`, `Destination limit exceeded`, `Other packet flood`, `Rate limit exceeded`, `Packet was forwarded to service`가 포함됩니다. |
| **TopSourceCountries** | 들어오는 트래픽에 대한 상위 10개 원본 국가를 분석한 것입니다. |
| **TopSourceCountriesForDroppedPackets** | 완료된 공격 트래픽의 상위 10개 원본 국가를 분석한 것입니다. |
| **TopSourceASNs** | 들어오는 트래픽에 대한 상위 10개 원본 ASN(자치 시스템 번호)을 분석한 것입니다.  |
| **SourceContinents** | 들어오는 트래픽의 원본 대륙을 분석한 것입니다. |
***

## <a name="enable-diagnostic-logging-on-all-public-ips"></a>모든 공용 IP에서 진단 로깅 사용

이 [기본 제공 정책](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F752154a7-1e0f-45c6-a880-ac75a7e4f648)은 정의된 범위의 모든 공개 IP 로그에 대한 진단 로깅을 자동으로 사용하도록 설정합니다. 기본 제공 정책의 전체 목록은 [Azure DDoS Protection 표준에 대한 Azure Policy 기본 제공 정의](policy-reference.md)를 참조하세요.

## <a name="view-log-data-in-workbooks"></a>통합 문서에서 로그 데이터 보기

### <a name="azure-sentinel-data-connector"></a>Azure Sentinel 데이터 커넥터

로그를 Azure Sentinel에 연결하고, 통합 문서에서 데이터를 보고 분석하고, 사용자 지정 경고를 만들고, 조사 프로세스에 통합할 수 있습니다. Azure Sentinel에 연결하려면 [Azure Sentinel에 연결](../sentinel/connect-azure-ddos-protection.md)을 참조하세요. 

![Azure Sentinel DDoS 커넥터](./media/ddos-attack-telemetry/azure-sentinel-ddos.png)

### <a name="azure-ddos-protection-workbook"></a>Azure DDoS Protection 통합 문서

[이 ARM(Azure Resource Manager) 템플릿](https://aka.ms/ddosworkbook)을 사용하여 공격 분석 통합 문서를 배포할 수 있습니다. 이 통합 문서를 사용하면 여러 개의 필터링 가능한 패널에서 공격 데이터를 시각화하여 위험 요인을 쉽게 이해할 수 있습니다. 

[![Azure에 배포](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2FAzure-Network-Security%2Fmaster%2FAzure%20DDoS%20Protection%2FWorkbook%20-%20Azure%20DDOS%20monitor%20workbook%2FAzureDDoSWorkbook_ARM.json)

![DDoS Protection 통합 문서](./media/ddos-attack-telemetry/ddos-attack-analytics-workbook.png)

## <a name="validate-and-test"></a>유효성 검사 및 테스트

DDoS 공격을 시뮬레이션하여 로그의 유효성을 검사하려면 [DDoS 탐지 유효성 검사](test-through-simulations.md)를 참조하세요.

## <a name="next-steps"></a>다음 단계

이 자습서에서는 다음 작업 방법을 알아보았습니다.

- 알림, 완화 보고서 및 완화 흐름 로그를 포함하는 DDoS 진단 로그를 구성합니다. 
- 정의된 범위의 모든 공용 IP에 대해 진단 로깅을 사용하도록 설정합니다.
- 통합 문서에서 로그 데이터를 봅니다.

공격 알림을 구성하는 방법을 알아보려면 다음 자습서를 계속 진행합니다.

> [!div class="nextstepaction"]
> [DDoS 보호 경고 보기 및 구성](alerts.md)
