---
title: Azure Security Center에서 SIEM(보안 정보 및 이벤트 관리) 시스템 및 기타 모니터링 솔루션으로 경고 스트리밍
description: Azure Sentinel, 타사 SIEM, SOAR 또는 ITSM 솔루션으로 경고를 스트리밍하는 방법을 알아봅니다.
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: how-to
ms.date: 10/06/2020
ms.author: memildin
ms.openlocfilehash: f2014d2ecea91ae650ec1fb1d730ee9b64c66c98
ms.sourcegitcommit: 86ca8301fdd00ff300e87f04126b636bae62ca8a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/16/2021
ms.locfileid: "122530274"
---
# <a name="stream-alerts-to-a-siem-soar-or-it-service-management-solution"></a>SIEM, SOAR 또는 IT 서비스 관리 솔루션에 대한 경고 스트리밍

Azure Security Center는 보안 경고를 가장 인기 있는 SIEM(보안 정보 및 이벤트 관리), SOAR(보안 오케스트레이션 자동 응답) 및 ITSM(IT 서비스 관리) 솔루션으로 스트리밍할 수 있습니다.

다음과 같은 Azure 네이티브 도구를 사용하면 현재 사용 중인 가장 인기 있는 모든 솔루션에서 경고 데이터를 볼 수 있습니다.

- **Azure Sentinel**
- **Splunk Enterprise and Splunk Cloud**
- **IBM의 QRadar**
- **ServiceNow**
- **ArcSight**
- **Power BI**
- **Palo Alto Networks**

## <a name="stream-alerts-to-azure-sentinel"></a>Azure Sentinel로 경고 스트리밍 

Security Center는 기본적으로 Azure의 클라우드 네이티브 SIEM 및 SOAR 솔루션인 Azure Sentinel과 통합됩니다. 

[Azure Sentinel에 대해 자세히 알아보기](../sentinel/overview.md)

### <a name="azure-sentinels-connectors-for-security-center"></a>Security Center에 대한 Azure Sentinel 커넥터

Azure Sentinel에는 구독 및 테넌트 수준에서 Azure Security Center에 대한 기본 제공 커넥터가 포함되어 있습니다.

- [구독 수준에서 Azure Sentinel로 경고 스트리밍](../sentinel/connect-azure-security-center.md)
- [테넌트의 모든 구독을 Azure Sentinel에 연결](https://techcommunity.microsoft.com/t5/azure-sentinel/azure-security-center-auto-connect-to-sentinel/ba-p/1387539) 

Azure Defender를 Azure Sentinel에 연결하면 Azure Sentinel로 수집된 Azure Defender 경고의 상태가 두 서비스 간에 동기화됩니다. 예를 들어 Azure Defender에서 경고가 닫히면 해당 경고는 Azure Sentinel에서도 닫힘으로 표시됩니다. Azure Defender에서 경고 상태를 변경하면 동기화된 Azure Sentinel 경고를 포함하는 Azure Sentinel **인시던트** 의 상태에 영향을 미치지 "않고"* 동기화된 경고 자체에만 영향을 미칩니다.

미리 보기 기능인 **양방향 경고 동기화** 를 사용하면 원래 Azure Defender 경고의 상태가 해당 Azure Defender 경고의 복사본이 포함된 Azure Sentinel 인시던트와 자동으로 동기화됩니다. 따라서 예를 들어 Azure Defender 경고가 포함된 Azure Sentinel 인시던트가 닫히면 Azure Defender가 해당 원본 경고를 자동으로 닫습니다.

자세한 내용은 [Azure Security Center에서 Azure Defender 경고 연결](../sentinel/connect-azure-security-center.md)을 참조하세요.

> [!NOTE]
> 양방향 경고 동기화 기능은 Azure Government 클라우드에서 사용할 수 없습니다. 

### <a name="configure-ingestion-of-all-audit-logs-into-azure-sentinel"></a>모든 감사 로그를 Azure Sentinel에 수집하도록 구성 

Azure Sentinel에서 Security Center 경고를 조사하는 또 다른 대안은 감사 로그를 Azure Sentinel로 스트리밍하는 것입니다.
    - [Windows 보안 이벤트 연결](../sentinel/connect-windows-security-events.md)
    - [Syslog를 사용하여 Linux 기반 소스에서 데이터 수집](../sentinel/connect-syslog.md)
    - [Azure 활동 로그에서 데이터 연결](../sentinel/connect-azure-activity.md)

> [!TIP]
> 분석을 위해 Azure Sentinel에 수집된 데이터 볼륨과 Azure Monitor Log Analytics 작업 영역에 저장된 데이터 볼륨을 기준으로 Azure Sentinel에 요금이 청구됩니다. Azure Sentinel은 유연하고 예측 가능한 가격 책정 모델을 제공합니다. [Azure Sentinel 가격 책정 페이지에서 자세히 알아보세요](https://azure.microsoft.com/pricing/details/azure-sentinel/).


## <a name="stream-alerts-with-microsoft-graph-security-api"></a>Microsoft Graph Security API를 사용하여 경고 스트리밍

Security Center는 기본적으로 Microsoft Graph Security API와 통합되어 있습니다. 따라서 구성할 필요가 없고 추가 비용도 발생하지 않습니다. 

이 API를 사용하여 **전체 테넌트**(및 기타 여러 Microsoft 보안 제품의 데이터)에서 타사 SIEM 및 기타 인기 있는 플랫폼으로 경고를 스트리밍할 수 있습니다.

- **Splunk Enterprise 및 Splunk Cloud** - [Splunk용 Microsoft Graph Security API 추가 항목 사용](https://splunkbase.splunk.com/app/4564/) 
- **Power BI** - [Power BI Desktop에서 Microsoft Graph Security API에 연결](/power-bi/connect-data/desktop-connect-graph-security)
- **ServiceNow** - [지침에 따라 ServiceNow Store에서 Microsoft Graph Security API 애플리케이션 설치 및 구성](https://docs.servicenow.com/bundle/orlando-security-management/page/product/secops-integration-sir/secops-integration-ms-graph/task/ms-graph-install.html)
- **QRadar** - [Microsoft Graph API를 통한 Azure Security Center용 IBM의 디바이스 지원 모듈](https://www.ibm.com/support/knowledgecenter/SS42VS_DSM/com.ibm.dsm.doc/c_dsm_guide_ms_azure_security_center_overview.html) 
- **Palo Alto Networks**, **Anomali**, **Lookout**, **InSpark** 등 - [Microsoft Graph Security API](https://www.microsoft.com/security/business/graph-security-api#office-MultiFeatureCarousel-09jr2ji)

[Microsoft Graph Security API에 대해 자세히 알아보기](https://www.microsoft.com/security/business/graph-security-api)


## <a name="stream-alerts-with-azure-monitor"></a>Azure Monitor를 사용하여 경고 스트리밍 

경고를 **ArcSight**, **Splunk**, **SumoLogic**, Syslog 서버, **LogRhythm**, **Logz.io Cloud Observability Platform** 및 기타 모니터링 솔루션으로 스트리밍합니다. Azure Event Hubs를 통해 Azure Monitor를 사용하여 Security Center 연결:

1. [연속 내보내기](continuous-export.md)를 사용하도록 설정하여 구독 수준에서 전용 Azure Event Hubs로 Security Center 경고를 스트리밍합니다. 
    > [!TIP]
    > Azure Policy를 사용하여 관리 그룹 수준에서 이 작업을 수행하려면 [연속 내보내기 자동화 구성을 규모에 맞게 만들기](continuous-export.md?tabs=azure-policy#configure-continuous-export-at-scale-using-the-supplied-policies)를 참조하세요.

1. [Azure Monitor의 기본 제공 커넥터를 사용하여 Azure Event Hubs를 기본 솔루션에 연결](../azure-monitor/essentials/stream-monitoring-data-event-hubs.md#partner-tools-with-azure-monitor-integration)합니다.

1. 필요에 따라 원시 로그를 Azure Event Hubs로 스트리밍하고 기본 솔루션에 연결합니다. [사용 가능한 모니터링 데이터](../azure-monitor/essentials/stream-monitoring-data-event-hubs.md#monitoring-data-available)에 대해 자세히 알아보세요.

> [!TIP]
> 내보낸 데이터 형식의 이벤트 스키마를 보려면 [Event Hubs 이벤트 스키마](https://aka.ms/ASCAutomationSchemas)를 참조하세요.


## <a name="next-steps"></a>다음 단계

이 페이지에서는 SIEM, SOAR 또는 ITSM 도구에서 Azure Security Center 경고 데이터를 사용할 수 있는지 확인하는 방법에 대해 설명했습니다. 관련 자료는 다음을 참조하세요.

- [Azure Sentinel이란?](../sentinel/overview.md)
- [Azure Security Center의 경고 유효성 검사](security-center-alert-validation.md) - 경고가 올바르게 구성되었는지 확인
- [Security Center 데이터 연속 내보내기](continuous-export.md)