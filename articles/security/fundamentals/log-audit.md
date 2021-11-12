---
title: Azure 보안 로깅 및 감사 | Microsoft Docs
description: Azure에서 사용할 수 있는 로그와 얻을 수 있는 보안 인사이트에 대해 알아봅니다.
services: security
documentationcenter: na
author: TerryLanfear
manager: rkarlin
editor: TomSh
ms.assetid: ''
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/31/2019
ms.author: terrylan
ms.openlocfilehash: 5ac5d1557ab2ba247588971426e380e84d7b6e8e
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132342832"
---
# <a name="azure-security-logging-and-auditing"></a>Azure 보안 로깅 및 감사

Azure는 보안 정책 및 메커니즘의 차이를 식별할 수 있도록 다양한 구성 가능한 보안 감사 및 로깅 옵션을 제공합니다. 이 문서에서는 Azure에서 호스팅되는 서비스의 보안 로그 생성, 수집 및 분석에 대해 설명합니다.

> [!Note]
> 이 문서의 특정 권장 사항으로 인해 데이터, 네트워크 또는 컴퓨팅 리소스의 사용량이 증가하고, 라이선스 또는 구독 비용이 증가할 수 있습니다.

## <a name="types-of-logs-in-azure"></a>Azure의 로그 유형

클라우드 애플리케이션은 이동하는 부분이 많아 복잡합니다. 로깅 데이터는 애플리케이션에 대한 인사이트를 제공하고 다음에 도움이 될 수 있습니다.

- 과거의 문제를 해결하거나 잠재적인 문제를 방지
- 애플리케이션 성능 또는 유지 관리 효율성 향상
- 수동 개입이 없어도 되도록 작업을 자동화

Azure 로그는 다음과 같은 유형으로 분류됩니다.
* **제어/관리 로그** 는 Azure Resource Manager의 CREATE, UPDATE 및 DELETE 작업에 대한 정보를 제공합니다. 자세한 내용은 [Azure 활동 로그](../../azure-monitor/essentials/platform-logs-overview.md)를 참조하세요.

* **데이터 평면 로그** 는 Azure 리소스 사용의 일부로 발생한 이벤트에 대한 정보를 제공합니다. 이 로그 유형의 예로 VM(가상 머신)의 Windows 이벤트 시스템, 보안 및 애플리케이션 로그와 Azure Monitor를 통해 구성된 [진단 로그](../../azure-monitor/essentials/platform-logs-overview.md)가 있습니다.

* **처리된 이벤트** 는 사용자를 대신하여 처리되어 분석된 이벤트/경고에 대한 정보를 제공합니다. 이 유형의 예로는 microsoft defender for [cloud](../../security-center/security-center-introduction.md) 가 구독을 처리 및 분석 하 고 간결한 보안 경고를 제공 하는 [클라우드 경고에 대 한 microsoft defender](../../security-center/security-center-managing-and-responding-alerts.md) 가 있습니다.

다음 표에는 Azure에서 사용할 수 있는 가장 중요한 유형의 로그가 나와 있습니다.

| 로그 범주 | 로그 형식 | 사용량 | 통합 |
| ------------ | -------- | ------ | ----------- |
|[활동 로그](../../azure-monitor/essentials/platform-logs-overview.md)|Azure Resource Manager 리소스에 대한 제어 평면 이벤트|    구독의 리소스에서 수행된 작업에 대한 인사이트를 제공합니다.|    Rest API, [Azure Monitor](../../azure-monitor/essentials/platform-logs-overview.md)|
|[Azure 리소스 로그](../../azure-monitor/essentials/platform-logs-overview.md)|구독에서 Azure Resource Manager 리소스 작업에 대한 빈번한 데이터| 리소스 자체에서 수행한 작업에 대한 인사이트를 제공합니다.| Azure Monitor|
|[Azure Active Directory 보고](../../active-directory/reports-monitoring/overview-reports.md)|로그 및 보고서 | 사용자 및 그룹 관리에 대한 사용자 로그인 활동 및 시스템 활동 정보를 보고합니다.|[그래프 API](../../active-directory/develop/microsoft-graph-intro.md)|
|[가상 머신 및 클라우드 서비스](../../azure-monitor/vm/monitor-virtual-machine.md)|Windows 이벤트 로그 서비스 및 Linux Syslog|   가상 머신에서 시스템 데이터와 로깅 데이터를 캡처하고 사용자가 선택한 스토리지 계정으로 해당 데이터를 전송합니다.|   Azure Monitor의 Windows([WAD](../../azure-monitor/agents/diagnostics-extension-overview.md)[Microsoft Azure Diagnostics 스토리지] 사용) 및 Linux|
|[Azure Storage 분석](/rest/api/storageservices/fileservices/storage-analytics)|스토리지 로깅(스토리지 계정에 대한 메트릭 데이터 제공)|추적 요청에 대한 인사이트를 제공하고, 사용 추세를 분석하며, 스토리지 계정과 관련된 문제를 진단합니다.| REST API 또는 [클라이언트 라이브러리](/dotnet/api/overview/azure/storage)|
|[NSG(네트워크 보안 그룹) 흐름 로그](../../network-watcher/network-watcher-nsg-flow-logging-overview.md)|JSON 형식(규칙에 따라 아웃바운드 및 인바운드 흐름 표시)|네트워크 보안 그룹을 통해 수신 및 송신 IP 트래픽에 대한 정보를 표시합니다.|[Azure Network Watcher](../../network-watcher/network-watcher-monitoring-overview.md)|
|[Application Insight](../../azure-monitor/app/app-insights-overview.md)|로그, 예외 및 사용자 지정 진단|  여러 플랫폼에서 웹 개발자를 위한 APM(애플리케이션 성능 모니터링) 서비스를 제공합니다.| REST API, [Power BI](https://powerbi.microsoft.com/documentation/powerbi-azure-and-power-bi/)|
|[데이터 처리/보안 경고](../../security-center/security-center-introduction.md)|   클라우드 경고에 대 한 Microsoft Defender, Azure Monitor 로그 경고| 보안 정보 및 경고를 제공합니다.|  REST API, JSON|

## <a name="log-integration-with-on-premises-siem-systems"></a>온-프레미스 SIEM 시스템과 로그 통합
[클라우드 경고를 위해 defender를 통합](../../security-center/security-center-partner-integration.md) 하면 클라우드 경고에 대해 defender를 동기화 하는 방법, azure 진단 로그에서 수집 된 가상 컴퓨터 보안 이벤트 및 Azure Monitor 로그 또는 siem 솔루션으로 azure 감사 로그를 동기화 하는 방법을 설명 합니다.

## <a name="next-steps"></a>다음 단계

- [감사 및 로깅](management-monitoring-overview.md): 가시성을 유지하고 시기 적절한 보안 경고에 빠르게 대응하여 데이터를 보호합니다.

- [Azure 내에서 보안 로깅 및 감사 로그 수집](https://azure.microsoft.com/resources/videos/security-logging-and-audit-log-collection/): Azure 인스턴스에서 올바른 보안 및 감사 로그를 수집하도록 이러한 설정을 적용합니다.

- [사이트 모음에 대한 감사 설정 구성](https://support.office.com/article/Configure-audit-settings-for-a-site-collection-A9920C97-38C0-44F2-8BCB-4CF1E2AE22D2?ui=&rs=&ad=US): 사이트 모음 관리자인 경우 개별 사용자의 작업 기록과 특정 날짜 범위 동안 수행된 작업 기록을 검색할 수 있습니다.

- [Microsoft 365 Defender 포털에서 감사 로그 검색](/microsoft-365/compliance/search-the-audit-log-in-security-and-compliance): Microsoft 365 Defender 포털을 사용 하 여 통합 된 감사 로그를 검색 하 고 조직에서 사용자 및 관리자 작업을 볼 수 있습니다.
