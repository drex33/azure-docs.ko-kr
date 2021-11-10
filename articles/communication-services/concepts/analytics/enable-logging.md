---
title: Azure Communication Services-Enable Azure Monitor
titleSuffix: An Azure Communication Services concept document
description: 진단 설정 사용하여 Communications Services 로그 및 메트릭 구성
author: timmitchell
services: azure-communication-services
ms.author: timmitchell
ms.date: 10/25/2021
ms.topic: conceptual
ms.service: azure-communication-services
ms.subservice: data
ms.openlocfilehash: f9de0567c3c07a10b780c4932a09e155f0e2ddea
ms.sourcegitcommit: 838413a8fc8cd53581973472b7832d87c58e3d5f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/10/2021
ms.locfileid: "132139932"
---
# <a name="enable-logs-via-diagnostic-settings-in-azure-monitor"></a>Azure Monitor 진단 설정 통해 로그 사용

## <a name="overview"></a>개요

Communications Services는 [Azure Monitor 로그 개요](/azure/azure-monitor/logs/data-platform-logs) 및 Azure Monitor [메트릭을](/azure/azure-monitor/essentials/data-platform-metrics)통해 모니터링 및 분석 기능을 제공합니다. 각 Azure 리소스에는 다음 조건을 정의하는 자체 진단 설정이 필요합니다.

- 설정에 정의된 대상으로 전송되는 로그 및 메트릭 데이터의 범주. 사용 가능한 범주는 리소스 유형에 따라 달라집니다.
- 로그를 보낼 하나 이상의 대상입니다. 현재 대상에는 Log Analytics 작업 영역, Event Hubs 및 Azure Storage가 포함됩니다.
- 단일 진단 설정으로 각 대상 중 하나만 정의할 수 있습니다. 데이터를 2개 이상의 특정 대상 유형(예: 두 개의 다른 Log Analytics 작업 영역) 으로 보내려면 여러 개의 설정을 만듭니다. 각 리소스에는 최대 5개의 진단 설정이 있을 수 있습니다.

다음은 Communications Services에 대한 로그 및 메트릭 만들기를 시작하도록 Azure Monitor 리소스를 구성하기 위한 지침입니다. 모든 Azure 리소스에서 진단 설정 사용하는 자세한 설명서는 진단 [설정](/azure/azure-monitor/essentials/diagnostic-settings)를 참조하세요.

이러한 지침은 다음 Communications Services 로그에 적용됩니다.

- [호출 요약 및 호출 진단 로그](call-logs-azure-monitor.md) 미리 보기

## <a name="access-diagnostic-settings"></a>진단 설정 액세스

Communications Services에 대한 진단 설정 액세스하려면 Azure Portal 내의 Communications Services 홈페이지로 이동하여 시작합니다.

:::image type="content" source="media\enable-logging\portal-home-go-to-acs-resource.png" alt-text="Communications Services 리소스":::

왼쪽 탐색 창의 모니터링 섹션 내에서 "진단 설정"을 클릭합니다.

:::image type="content" source="media\enable-logging\resource-diagnostic-settings-nav.png" alt-text="탐색의 진단 설정":::

"진단 설정 추가" 링크를 클릭합니다(Communications Services에 사용할 수 있는 다양한 로그 및 메트릭 원본 참고).

:::image type="content" source="media\enable-logging\diagnostic-setting-add.png" alt-text="진단 설정 범주 세부 정보":::

## <a name="adding-a-diagnostic-setting"></a>진단 설정 추가

그러면 진단 설정의 이름을 선택하라는 메시지가 표시됩니다. 이는 모니터링하는 Azure 리소스가 많은 경우에 유용합니다. 또한 로그 또는 메트릭으로 모니터링하려는 로그 및 메트릭 데이터 원본을 선택하라는 메시지가 표시됩니다. 차이점에 대한 자세한 내용은 [Azure Monitor 데이터 플랫폼을](/azure/azure-monitor/data-platform) 참조하세요.

:::image type="content" source="media\enable-logging\diagnostic-setting-categories-details-acs.png" alt-text="진단 설정 추가":::

## <a name="choose-destinations"></a>대상 선택

로그를 저장할 대상을 선택하라는 메시지도 표시됩니다. 플랫폼 로그 및 메트릭을 다음 표의 대상으로 보낼 수 있습니다. 자세한 내용은 Azure Monitor 설명서에 포함되어 있습니다. [플랫폼 로그 및 메트릭을 다른 대상으로 보내는 진단 설정 만들기](/azure/azure-monitor/essentials/diagnostic-settings?tabs=CMD)

| 대상 | Description |
|:------------|:------------|
| [Log Analytics 작업 영역](/azure/azure-monitor/logs/design-logs-deployment) | Log Analytics 작업 영역에 로그 및 메트릭을 보내면 강력한 로그 쿼리를 사용하여 Azure Monitor 수집한 다른 모니터링 데이터로 분석하고 경고 및 시각화와 같은 다른 Azure Monitor 기능을 사용할 수 있습니다. |
| [Event Hubs](/azure/event-hubs/) | 로그 및 메트릭을 이벤트 허브로 보내면 타사 SIEM과 같은 외부 시스템 및 기타 로그 분석 솔루션으로 데이터를 스트리밍할 수 있습니다. |
| [Azure Storage 계정](/azure/storage/blobs/) | Azure Storage 계정에 로그 및 메트릭을 보관하면 감사, 정적 분석, 백업에 유용합니다. Azure Storage는 Azure Monitor 로그 및 Log Analytics 작업 영역보다 비용이 적고 로그를 무기한으로 보관할 수 있습니다. |

다음 설정은 Communications Services 리소스 내에서 표시되는 내용의 예입니다.

:::image type="content" source="media\enable-logging\diagnostic-setting-destination-acs.png" alt-text="진단 설정 대상 세부 정보":::

이러한 옵션은 모두 특정 스토리지 요구 사항에 맞게 조정할 수 있는 실행 가능하고 유연한 옵션입니다. 그러나 Log Analytics 작업 영역 옵션을 선택하면 다른 기능과 기본 제공 분석 인사이트가 제공됩니다.

## <a name="log-analytics-workspace-for-additional-analytics-features"></a>추가 분석 기능을 위한 Log Analytics 작업 영역

Log Analytics 작업 영역 대상으로 로그를 보내도록 선택하면 일반적으로 및 [Communications Services에](/azure/azure-monitor/logs/log-analytics-overview) 대해 Azure Monitor 내에서 더 많은 기능을 사용할 수 있습니다. Log Analytics는 Azure Portal 내의 도구로, Azure Monitor 로그 및 메트릭 및 [통합 문서,](/azure/azure-monitor/visualize/workbooks-overview) [경고,](/azure/azure-monitor/alerts/alerts-log) [알림 작업,](/azure/azure-monitor/alerts/action-groups)REST API [액세스](https://dev.loganalytics.io/)등과 같은 데이터로 [쿼리를](/azure/azure-monitor/logs/queries) 만들고 편집하고 실행하는 데 사용됩니다.

Communications Services 로그의 경우 데이터를 신속하게 분석하고 이해할 수 있는 초기 인사이트 집합을 제공하는 유용한 [기본 쿼리 팩을](/azure/azure-monitor/logs/query-packs#default-query-pack) 제공했습니다. 이러한 쿼리 팩은 [Communications Services용 Log Analytics 에 설명되어 있습니다.](log-analytics.md) 또한 [Communications Services용](insights.md)통합 문서 로그에 설명된 통합 문서를 사용하여 많은 인사이트 및 시각화를 만들었습니다.
