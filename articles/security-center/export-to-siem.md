---
title: Microsoft Defender for Cloud에서 SIEM(보안 정보 및 이벤트 관리) 시스템 및 기타 모니터링 솔루션으로 경고 스트리밍
description: Microsoft Sentinel, 타사IEM, SOAR 또는 ITSM 솔루션으로 보안 경고를 스트리밍하는 방법을 알아봅니다.
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: how-to
ms.date: 10/06/2020
ms.author: memildin
ms.openlocfilehash: 34fcb3b4a8771f9c2b635a0f8e2c5a43ddf896a3
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131428618"
---
# <a name="stream-alerts-to-a-siem-soar-or-it-service-management-solution"></a>SIEM, SOAR 또는 IT 서비스 관리 솔루션에 대한 경고 스트리밍

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

Microsoft Defender for Cloud는 가장 인기 있는 SIEM(보안 정보 및 이벤트 관리), SOAR(Security Orchestration Automated Response) 및 ITSM(IT Service Management) 솔루션으로 보안 경고를 스트리밍할 수 있습니다.

다음과 같은 Azure 네이티브 도구를 사용하면 현재 사용 중인 가장 인기 있는 모든 솔루션에서 경고 데이터를 볼 수 있습니다.

- **Microsoft Sentinel**
- **Splunk Enterprise and Splunk Cloud**
- **IBM의 QRadar**
- **ServiceNow**
- **ArcSight**
- **Power BI**
- **Palo Alto Networks**

## <a name="stream-alerts-to-microsoft-sentinel"></a>Microsoft Sentinel에 경고 스트리밍 

Defender for Cloud는 기본적으로 Azure의 클라우드 네이티브 SIEM 및 SOAR 솔루션인 Microsoft Sentinel과 통합됩니다. 

[Microsoft Sentinel 에 대해 자세히 알아보세요.](../sentinel/overview.md)

### <a name="microsoft-sentinels-connectors-for-defender-for-cloud"></a>Microsoft Sentinel의 Defender for Cloud 커넥터

Microsoft Sentinel에는 구독 및 테넌트 수준에서 Microsoft Defender for Cloud에 대한 기본 제공 커넥터가 포함되어 있습니다.

- [구독 수준에서 Microsoft Sentinel에 경고 스트리밍](../sentinel/connect-azure-security-center.md)
- [테넌트에서 모든 구독을 Microsoft Sentinel로 커넥트](https://techcommunity.microsoft.com/t5/azure-sentinel/azure-security-center-auto-connect-to-sentinel/ba-p/1387539) 

Defender for Cloud를 Microsoft Sentinel에 연결하면 Microsoft Sentinel에 검색되는 Defender for Cloud 경고의 상태가 두 서비스 간에 동기화됩니다. 따라서 예를 들어 Defender for Cloud에서 경고가 닫힌 경우 해당 경고는 Microsoft Sentinel에서도 닫힌 것으로 표시됩니다. Defender for Cloud에서 경고 상태를 변경하면 동기화된 Microsoft Sentinel 경고가 포함된 Microsoft Sentinel **인시던트의** 상태와 동기화된 경고 자체의 상태만 영향을 미칩니다.

양방향 **경고 동기화** 미리 보기 기능을 사용하도록 설정하면 원래 Defender for Cloud 경고의 상태가 해당 Defender for Cloud 경고의 복사본을 포함하는 Microsoft Sentinel 인시던트와 자동으로 동기화됩니다. 따라서 예를 들어 Defender for Cloud 경고가 포함된 Microsoft Sentinel 인시던트 경고가 닫히면 Defender for Cloud는 해당 원래 경고를 자동으로 닫습니다.

[Microsoft Defender for Cloud의 커넥트 Defender for Cloud 경고에서](../sentinel/connect-azure-security-center.md)자세히 알아보세요.

> [!NOTE]
> 양방향 경고 동기화 기능은 Azure Government 클라우드에서 사용할 수 없습니다. 

### <a name="configure-ingestion-of-all-audit-logs-into-microsoft-sentinel"></a>Microsoft Sentinel에 대한 모든 감사 로그의 스트리밍 구성 

Microsoft Sentinel에서 Defender for Cloud 경고를 조사하는 또 다른 대안은 감사 로그를 Microsoft Sentinel로 스트리밍하는 것입니다.
    - [Windows 보안 이벤트 연결](../sentinel/connect-windows-security-events.md)
    - [Syslog를 사용하여 Linux 기반 소스에서 데이터 수집](../sentinel/connect-syslog.md)
    - [Azure 활동 로그에서 데이터 연결](../sentinel/data-connectors-reference.md#azure-activity)

> [!TIP]
> Microsoft Sentinel은 Microsoft Sentinel에서 분석을 위해 수집된 데이터 볼륨에 따라 청구되며 Azure Monitor Log Analytics 작업 영역에 저장됩니다. Microsoft Sentinel은 유연하고 예측 가능한 가격 책정 모델을 제공합니다. [자세한 내용은 Microsoft Sentinel 가격 책정 페이지 를 참조하세요.](https://azure.microsoft.com/pricing/details/azure-sentinel/)


## <a name="stream-alerts-with-microsoft-graph-security-api"></a>Microsoft Graph Security API를 사용하여 경고 스트리밍

Defender for Cloud는 Microsoft Graph 보안 API 통합됩니다. 따라서 구성할 필요가 없고 추가 비용도 발생하지 않습니다. 

이 API를 사용하여 **전체 테넌트**(및 기타 여러 Microsoft 보안 제품의 데이터)에서 타사 SIEM 및 기타 인기 있는 플랫폼으로 경고를 스트리밍할 수 있습니다.

- **Splunk Enterprise 및 Splunk Cloud** - [Splunk용 Microsoft Graph Security API 추가 항목 사용](https://splunkbase.splunk.com/app/4564/) 
- **Power BI** - [Power BI Desktop에서 Microsoft Graph Security API에 연결](/power-bi/connect-data/desktop-connect-graph-security)
- **ServiceNow** - [지침에 따라 ServiceNow Store에서 Microsoft Graph Security API 애플리케이션 설치 및 구성](https://docs.servicenow.com/bundle/orlando-security-management/page/product/secops-integration-sir/secops-integration-ms-graph/task/ms-graph-install.html)
- **QRadar**  -  [Microsoft Graph API를 통한 Microsoft Defender for Cloud용 IBM의 디바이스 지원 모듈](https://www.ibm.com/support/knowledgecenter/SS42VS_DSM/com.ibm.dsm.doc/c_dsm_guide_ms_azure_security_center_overview.html) 
- **Palo Alto Networks**, **Anomali**, **Lookout**, **InSpark** 등 - [Microsoft Graph Security API](https://www.microsoft.com/security/business/graph-security-api#office-MultiFeatureCarousel-09jr2ji)

[Microsoft Graph Security API에 대해 자세히 알아보기](https://www.microsoft.com/security/business/graph-security-api)


## <a name="stream-alerts-with-azure-monitor"></a>Azure Monitor를 사용하여 경고 스트리밍 

경고를 **ArcSight**, **Splunk**, **SumoLogic**, Syslog 서버, **LogRhythm**, **Logz.io Cloud Observability Platform** 및 기타 모니터링 솔루션으로 스트리밍합니다. Azure Event Hubs 통해 Azure Monitor와 Cloud용 Defender를 연결합니다.

1. [지속적인 내보내기를](continuous-export.md) 사용하도록 설정하여 Defender for Cloud 경고를 구독 수준에서 전용 Azure Event Hub로 스트리밍합니다. 
    > [!TIP]
    > Azure Policy를 사용하여 관리 그룹 수준에서 이 작업을 수행하려면 [연속 내보내기 자동화 구성을 규모에 맞게 만들기](continuous-export.md?tabs=azure-policy#configure-continuous-export-at-scale-using-the-supplied-policies)를 참조하세요.

1. [Azure Monitor의 기본 제공 커넥터를 사용하여 Azure Event Hubs를 기본 솔루션에 연결](../azure-monitor/essentials/stream-monitoring-data-event-hubs.md#partner-tools-with-azure-monitor-integration)합니다.

1. 필요에 따라 원시 로그를 Azure Event Hubs로 스트리밍하고 기본 솔루션에 연결합니다. [사용 가능한 모니터링 데이터](../azure-monitor/essentials/stream-monitoring-data-event-hubs.md#monitoring-data-available)에 대해 자세히 알아보세요.

> [!TIP]
> 내보낸 데이터 형식의 이벤트 스키마를 보려면 [Event Hubs 이벤트 스키마](https://aka.ms/ASCAutomationSchemas)를 참조하세요.


## <a name="next-steps"></a>다음 단계

이 페이지에서는 선택한 SIEM, SOAR 또는 ITSM 도구에서 Microsoft Defender for Cloud 경고 데이터를 사용할 수 있도록 하는 방법을 설명했습니다. 관련 자료는 다음을 참조하세요.

- [Microsoft Sentinel이란?](../sentinel/overview.md)
- [Microsoft Defender for Cloud의 경고 유효성 검사](alert-validation.md) - 경고가 올바르게 구성되었는지 확인합니다.
- [지속적으로 Defender for Cloud 데이터 내보내기](continuous-export.md)