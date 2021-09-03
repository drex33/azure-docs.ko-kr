---
title: 연결 모니터에서 연결 모니터로 마이그레이션
titleSuffix: Azure Network Watcher
description: 연결 모니터에서 연결 모니터로 마이그레이션하는 방법을 알아봅니다.
services: network-watcher
documentationcenter: na
author: vinynigam
ms.service: network-watcher
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/07/2021
ms.author: vinigam
ms.openlocfilehash: c9130b83d0d4491152e05157c9cb52dfb89231a1
ms.sourcegitcommit: 98308c4b775a049a4a035ccf60c8b163f86f04ca
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/30/2021
ms.locfileid: "113105744"
---
# <a name="migrate-to-connection-monitor-from-connection-monitor-classic"></a>연결 모니터(클래식)에서 연결 모니터로 마이그레이션

> [!IMPORTANT]
> 2021년 7월 1일부터 연결 모니터(클래식)에 새 연결 모니터를 추가할 수 없지만, 2021년 7월 1일 이전에 만든 기존 연결 모니터는 계속 사용할 수 있습니다. 현재 워크로드에 대한 서비스 중단을 최소화하려면 2024년 2월 29일 이전에 연결 모니터(클래식)에서 [Azure Network Watcher의 새 연결 모니터로 마이그레이션](migrate-to-connection-monitor-from-connection-monitor-classic.md)합니다.

몇 번의 클릭만으로 가동 중지 시간 없이 기존 연결 모니터를 개선된 새 연결 모니터로 마이그레이션할 수 있습니다. 혜택에 대해 자세히 알아보려면 [연결 모니터](./connection-monitor-overview.md)를 참조하세요.

## <a name="key-points-to-note"></a>주의할 주요 사항

마이그레이션은 다음과 같은 결과를 생성하는 데 도움이 됩니다.

* 에이전트 및 방화벽 설정이 그대로 작동합니다. 변경할 필요가 없습니다. 
* 기존 연결 모니터는 연결 모니터 > 테스트 그룹 > 테스트 형식에 매핑됩니다. **편집** 을 선택하여 새 연결 모니터의 속성 보기 및 수정을 할 수 있고, 연결 모니터를 변경할 템플릿을 다운로드할 수 있으며, Azure Resource Manager를 통해 템플릿을 제출할 수 있습니다. 
* Network Watcher 확장을 포함하는 Azure 가상 머신은 작업 영역과 메트릭 모두에 데이터를 보냅니다. 연결 모니터는 이전 메트릭(ProbesFailedPercent 및 AverageRoundtripMs) 대신 새 메트릭(ChecksFailedPercent 및 RoundTripTimeMs)을 통해 데이터를 사용할 수 있습니다. 이전 메트릭은 ProbesFailedPercent -> ChecksFailedPercent 및 AverageRoundtripMs -> RoundTripTimeMs로 새 메트릭으로 마이그레이션됩니다.
* 데이터 모니터링:
   * **경고**: 새 메트릭으로 자동으로 마이그레이션됩니다.
   * **대시보드 및 통합**: 메트릭 집합을 수동으로 편집해야 합니다. 
    
## <a name="prerequisites"></a>필수 구성 요소

1. 사용자 지정 작업 영역을 사용하고 있는 경우 Log Analytics 작업 영역의 지역 및 구독에서 Network Watcher가 사용하도록 설정되어 있어야 합니다. 그렇지 않으면 “마이그레이션을 시도하기 전에 선택 구독 및 선택된 LA 작업 영역 위치에서 Network Watcher 확장을 사용하도록 설정하세요"라는 오류가 표시됩니다.
1. 연결 모니터(클래식)에서 원본으로 사용되는 가상 머신에서 더 이상 Network Watcher 확장을 사용하도록 설정할 수 없는 경우 다음과 같은 오류 메시지가 표시됩니다. "하나 이상의 Azure 가상 머신에 Network Watcher 확장이 설치되어 있지 않으므로 다음 테스트를 포함하는 연결 모니터를 가져올 수 없습니다. Network Watcher 확장을 설치하고 새로 고침을 클릭하여 가져옵니다."



## <a name="migrate-the-connection-monitors"></a>연결 모니터 마이그레이션

1. 이전 연결 모니터를 새로운 연결 모니터로 마이그레이션하려면 **연결 모니터** 를 선택한 다음 **연결 모니터 마이그레이션** 을 선택합니다.

    ![연결 모니터에 대한 연결 모니터의 마이그레이션을 보여 주는 스크린샷입니다.](./media/connection-monitor-2-preview/migrate-cm-to-cm-preview.png)
    
1. 마이그레이션하려는 연결 모니터와 구독을 선택한 다음 **선택 항목 마이그레이션** 을 선택합니다. 

몇 번의 클릭만으로 기존 연결 모니터를 연결 모니터로 마이그레이션했습니다. CM(클래식)에서 CM으로 마이그레이션하고 나면 CM(클래식)에서 모니터를 볼 수 없습니다.

이제 연결 모니터 속성을 사용자 지정하고, 기본 작업 영역을 변경하고, 템플릿을 다운로드하고, 마이그레이션 상태를 확인할 수 있습니다. 

마이그레이션이 시작된 후 다음과 같은 변경 내용이 발생합니다. 
* Azure Resource Manager 리소스가 새로운 연결 모니터로 변경됩니다.
    * 연결 모니터의 이름, 지역 및 구독은 변경되지 않은 상태로 유지됩니다. 리소스 ID는 영향을 받지 않습니다.
    * 연결 모니터가 사용자 지정되지 않으면 기본 Log Analytics 작업 영역은 구독 및 연결 모니터의 지역에서 만들어집니다. 이 작업 영역은 모니터링 데이터가 저장되는 위치입니다. 테스트 결과 데이터도 메트릭에 저장됩니다.
    * 각 테스트는 *defaultTestGroup* 이라는 테스트 그룹으로 마이그레이션됩니다.
    * 원본 및 대상 엔드포인트는 새 테스트 그룹에서 만들어지고 사용됩니다. 기본 이름은 *defaultSourceEndpoint* 및 *defaultDestinationEndpoint* 입니다.
    * 대상 포트 및 프로브 간격은 *defaultTestConfiguration* 이라는 테스트 구성으로 이동됩니다. 프로토콜은 포트 값을 기준으로 설정됩니다. 성공 임계값 및 기타 선택적 속성은 비워둡니다.
* 메트릭 경고는 연결 모니터 메트릭 경고로 마이그레이션됩니다. 메트릭이 다르므로 변경됩니다. 자세한 내용은 [연결 모니터로 네트워크 연결 모니터링](./connection-monitor-overview.md#metrics-in-azure-monitor)을 참조하세요.
* 마이그레이션된 연결 모니터는 더 이상 이전 연결 모니터 솔루션으로 표시되지 않습니다. 이제 연결 모니터에서만 사용 가능한 상태가 됩니다.
* SIEM(보안 정보 및 이벤트 관리) 시스템과의 통합 및 모든 외부 통합(예: Power BI 및 Grafana의 대시보드)은 수동으로 마이그레이션해야 합니다. 이는 설정을 마이그레이션하려면 수행해야 하는 유일한 수동 단계입니다.

## <a name="common-errors-encountered"></a>일반적인 오류 발생

다음은 마이그레이션 중에 발생하는 몇 가지 일반적인 오류입니다. 

| Error  |    이유   |
|---|---|
|하나 이상의 구독/지역 조합에 Network Watcher가 사용하도록 설정되어 있지 않으므로 다음 연결 모니터를 가져올 수 없습니다. Network Watcher를 사용하도록 설정하고 새로 고침을 클릭하여 가져옵니다. 연결 모니터 목록 - {0}   |  이 오류는 사용자가 CM(클래식)에서 연결 모니터로 테스트를 마이그레이션하고 CM(클래식)의 하나 이상의 구독 및 지역에서 Network Watcher 확장이 사용하도록 설정되지 않은 경우 발생합니다. 사용자는 구독에서 NW 확장을 사용하도록 설정하고 새로 고침하여 다시 마이그레이션하기 전에 가져와야 합니다.   |
|하나 이상의 Azure Virtual Machines에 Network Watcher 확장이 설치되어 있지 않으므로 다음 테스트가 있는 연결 모니터를 가져올 수 없습니다. Network Watcher 확장을 설치하고 새로 고침을 클릭하여 가져옵니다. 테스트 목록 - {0} |    이 오류는 사용자가 CM(클래식)에서 연결 모니터로 테스트를 마이그레이션하고 있고 CM(클래식)의 하나 이상의 Azure VM에 Network Watcher 확장이 설치되어 있지 않을 때 발생합니다. 사용자는 다시 마이그레이션하기 전에 Azure VM에 NW 확장을 설치하고 새로 고쳐야 합니다. |
|표시할 행 없음   |  이 오류는 사용자가 CM(클래식)에서 CM으로 구독을 마이그레이션하려고 하지만 구독에 CM(클래식)이 만들어지지 않은 경우 발생합니다. |

## <a name="next-steps"></a>다음 단계

연결 모니터에 대해 자세히 알아보려면 다음을 참조하세요.
* [네트워크 성능 모니터에서 연결 모니터로 마이그레이션](./migrate-to-connection-monitor-from-network-performance-monitor.md)
* [Azure Portal을 사용하여 연결 모니터 만들기](./connection-monitor-create-using-portal.md)


    
 
    
