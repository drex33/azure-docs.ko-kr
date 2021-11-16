---
title: Microsoft 센티널의 데이터 수집에 대 한 모범 사례
description: Microsoft 센티널에 데이터 원본을 연결할 때 사용 하는 모범 사례에 대해 알아봅니다.
services: sentinel
author: batamig
ms.author: bagol
ms.service: microsoft-sentinel
ms.subservice: microsoft-sentinel
ms.topic: conceptual
ms.date: 11/09/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: 17561127c040fc1aedac771093e0bfa6366c4d30
ms.sourcegitcommit: 2ed2d9d6227cf5e7ba9ecf52bf518dff63457a59
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/16/2021
ms.locfileid: "132524781"
---
#  <a name="data-collection-best-practices"></a>데이터 수집 모범 사례

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

이 섹션에서는 Microsoft 센티널 데이터 커넥터를 사용 하 여 데이터를 수집 하기 위한 모범 사례를 검토 합니다. 자세한 내용은 [데이터 원본 커넥트](connect-data-sources.md), [microsoft 센티널 데이터 커넥터 참조](data-connectors-reference.md)및 [microsoft 센티널 솔루션 카탈로그](sentinel-solutions-catalog.md)를 참조 하세요.

## <a name="prioritize-your-data-connectors"></a>데이터 커넥터 우선 순위 지정

사용자 환경에 가장 적합한 데이터 커넥터를 잘 모르겠으면 먼저 [무료 데이터 커넥터](azure-sentinel-billing.md#free-data-sources)를 모두 사용하도록 설정합니다.

무료 데이터 커넥터는 가능한 한 빨리 Microsoft 센티널의 값을 표시 하기 시작 하 고, 다른 데이터 커넥터 및 예산을 계속 계획 합니다.

[파트너](data-connectors-reference.md)와 [사용자 지정](create-custom-connector.md) 데이터 커넥터의 경우 먼저 가장 높은 우선 순위부터 시작하여 [Syslog](connect-syslog.md) 및 [CEF](connect-common-event-format.md) 커넥터와 Linux 기반 디바이스를 설정합니다.

데이터 수집에 너무 많은 비용이 소요되거나 속도가 너무 빠른 경우 [Azure Monitor 에이전트](../azure-monitor/agents/azure-monitor-agent-overview.md)를 사용하여 전달되는 로그를 중지하거나 필터링합니다.

> [!TIP]
> 사용자 지정 데이터 커넥터를 사용 하면 에이전트, Logstash 태 시 또는 API를 통해 현재 기본 제공 기능에서 지원 하지 않는 데이터 원본에서 Microsoft 센티널로 데이터를 수집할 수 있습니다. 자세한 내용은 [Microsoft 센티널 사용자 지정 커넥터를 만들기 위한 리소스](create-custom-connector.md)를 참조 하세요.
>

## <a name="filter-your-logs-before-ingestion"></a>수집 전에 로그 필터링

데이터가 Microsoft 센티널로 수집 전에 수집 된 로그 또는 로그 콘텐츠를 필터링 할 수 있습니다. 예를 들어 보안 작업과 관련이 없거나 중요하지 않은 로그를 필터링하거나 로그 메시지에서 원치 않는 세부 정보를 제거할 수 있습니다. 메시지 내용 필터링은 관련 없는 많은 세부 정보를 포함하는 Syslog, CEF 또는 Windows 기반 로그를 사용할 때 비용을 절감하려는 경우에도 유용할 수 있습니다.

다음 방법 중 하나를 사용하여 로그를 필터링합니다.

- **Azure Monitor 에이전트**. [Windows 보안 이벤트](connect-windows-security-events.md)를 수집하기 위해 Windows와 Linux 둘 다에서 지원됩니다. 지정한 이벤트만 수집하도록 에이전트를 구성하여 수집되는 로그를 필터링합니다.

- **Logstash**. 로그 메시지 변경을 포함하여 메시지 내용 필터링을 지원합니다. 자세한 내용은 [Logstash를 사용하여 연결](create-custom-connector.md#connect-with-logstash)을 참조하세요.

> [!IMPORTANT]
> Logstash를 사용하여 메시지 내용을 필터링하면 로그가 사용자 지정 로그로 수집되므로 모든 [무료 계층 로그](azure-sentinel-billing.md#free-data-sources)가 유료 계층 로그로 전환됩니다.
>
> 사용자 지정 로그도 자동으로 추가되지 않으므로 [분석 규칙](automate-incident-handling-with-automation-rules.md), [위협 헌팅](hunting.md), [통합 문서](get-visibility.md)로 수집해야 합니다. 또한 현재 [Machine Learning](bring-your-own-ml.md) 기능에서는 사용자 지정 로그가 지원되지 않습니다.
>

## <a name="alternative-data-ingestion-requirements"></a>대체 데이터 수집 요구 사항

다양한 문제로 인해 데이터 수집에 대한 표준 구성이 조직에서 제대로 작동하지 않을 수 있습니다. 다음 표에서는 일반적인 문제나 요구 사항, 가능한 해결 방법과 고려 사항을 설명합니다.

> [!NOTE]
> 아래에 나열된 많은 솔루션에는 사용자 지정 데이터 커넥터가 필요합니다. 자세한 내용은 [Microsoft 센티널 사용자 지정 커넥터를 만들기 위한 리소스](create-custom-connector.md)를 참조 하세요.
>

### <a name="on-premises-windows-log-collection"></a>온-프레미스 Windows 로그 수집


|문제/요구 사항  |가능한 해결 방법  |고려 사항  |
|---------|---------|---------|
|**로그 필터링 필요**     | Logstash 사용 <br><br>Azure Functions 사용 <br><br> LogicApps 사용 <br><br> 사용자 지정 코드 사용(.NET, Python)  |  필터링을 통해 비용을 절감 하 고 필요한 데이터만 수집 수 있지만 [Ueba](identify-threats-with-entity-behavior-analytics.md), [엔터티 페이지](identify-threats-with-entity-behavior-analytics.md#entity-pages), [기계 학습](bring-your-own-ml.md)및 [fusion](fusion.md)과 같은 일부 Microsoft 센티널 기능은 지원 되지 않습니다. <br><br>로그 필터링을 구성할 때 위협 헌팅 쿼리, 분석 규칙 등의 리소스를 업데이트해야 합니다.     |
|**에이전트를 설치할 수 없음**     |[Azure Monitor 에이전트](connect-windows-security-events.md#connector-options)에서 지원되는 Windows 이벤트 전달 사용       |   Windows 이벤트 전달을 사용하면 Windows 이벤트 수집기의 초당 부하 분산 이벤트가 10,000개 이벤트에서 500~1,000개 이벤트로 감소합니다.|
|**서버가 인터넷에 연결할 수 없음**     | [Log Analytics 게이트웨이](../azure-monitor/agents/gateway.md) 사용        | 에이전트에 대한 프록시를 구성하려면 게이트웨이 작동을 허용하는 추가 방화벽 규칙이 필요합니다.        |
|**수집 시 태그 지정 및 보강 필요**     |Logstash를 사용하여 ResourceID 삽입 <br><br>ARM 템플릿을 사용하여 온-프레미스 머신에 ResourceID 삽입 <br><br>별도의 작업 영역으로 리소스 ID 수집        | Log Analytics에서 사용자 지정 테이블에 대해 RBAC를 지원하지 않음 <br><br>Microsoft 센티널은 행 수준 RBAC를 지원 하지 않습니다. <br><br>**팁**: Microsoft 센티널에 대해 교차 작업 영역 디자인 및 기능을 채택 하는 것이 좋습니다.        |
|**분할 작업 및 보안 로그 필요**     | [Microsoft Monitor 에이전트 또는 Azure Monitor 에이전트](connect-windows-security-events.md) 멀티 홈 기능 사용        |  멀티 홈 기능을 사용하는 경우 에이전트 배포 오버헤드가 증가합니다.       |
|**사용자 지정 코드 필요**     |   특정 폴더 경로에서 파일 수집 <br><br>API 수집 사용 <br><br>PowerShell 사용 <br><br>Logstash 사용     |   로그 필터링 관련 문제가 있을 수 있습니다. <br><br>사용자 지정 방법은 지원되지 않습니다. <br><br>사용자 지정 커넥터에는 개발자 기술이 필요할 수 있습니다.       |
| | | |

### <a name="on-premises-linux-log-collection"></a>온-프레미스 Linux 로그 수집

|문제/요구 사항  |가능한 해결 방법  |고려 사항  |
|---------|---------|---------|
|**로그 필터링 필요**     | Syslog-NG 사용 <br><br>Rsyslog 사용 <br><br>에이전트에 대해 FluentD 구성 사용 <br><br> Azure Monitor 에이전트/Microsoft Monitoring 에이전트 사용 <br><br> Logstash 사용  |  일부 Linux 배포는 에이전트에서 지원되지 않을 수 있습니다. <br> <br>Syslog 또는 FluentD를 사용하려면 개발자 지식이 필요합니다. <br><br>자세한 내용은 [Microsoft 센티널 사용자 지정 커넥터를 만들기 위한](create-custom-connector.md)보안 이벤트 및 리소스를 [수집 하려면 Windows 서버에 커넥트](connect-windows-security-events.md) 를 참조 하세요.      |
|**에이전트를 설치할 수 없음**     |  syslog-ng 또는 rsyslog와 같은 Syslog 전달자 사용       |         |
|**서버가 인터넷에 연결할 수 없음**       | [Log Analytics 게이트웨이](../azure-monitor/agents/gateway.md) 사용        | 에이전트에 대한 프록시를 구성하려면 게이트웨이 작동을 허용하는 추가 방화벽 규칙이 필요합니다.          |
|**수집 시 태그 지정 및 보강 필요**      | 보강을 위해 Logstash를 사용하거나 API 또는 EventHubs와 같은 사용자 지정 방법 사용         | 필터링에 필요한 추가 작업이 있을 수도 있습니다.    |
|**분할 작업 및 보안 로그 필요**     |  멀티 호밍(multi-homing) 구성으로 [Azure Monitor 에이전트](connect-windows-security-events.md) 사용       |         |
|**사용자 지정 코드 필요**     |    Microsoft Monitoring(Log Analytics) 에이전트를 사용하여 사용자 지정 수집기 생성      |      |
| | | |

### <a name="endpoint-solutions"></a>엔드포인트 솔루션

EDR, 다른 보안 이벤트, Sysmon 등의 엔드포인트 솔루션에서 로그를 수집해야 하는 경우 다음 방법 중 하나를 사용합니다.

- **MTP 커넥터** - 엔드포인트용 Microsoft 365 Defender에서 로그를 수집합니다. 이 옵션은 추가 데이터 수집 비용을 발생시킵니다.
- **Windows 이벤트 전달**.

> [!NOTE]
> 부하 분산을 사용하면 작업 영역으로 처리될 수 있는 초당 이벤트 수가 감소합니다.
>

### <a name="office-data"></a>Office 데이터

표준 커넥터 데이터 외부에서 Microsoft Office 데이터를 수집해야 하는 경우 다음 솔루션 중 하나를 사용합니다.

|문제/요구 사항  |가능한 해결 방법  |고려 사항  |
|---------|---------|---------|
|**Teams, 메시지 추적, 피싱 데이터 등에서 원시 데이터 수집**     |    기본 제공 [Office 365 커넥터](./data-connectors-reference.md#microsoft-office-365) 기능을 사용한 후 다른 원시 데이터용 사용자 지정 커넥터 생성  |  이벤트를 해당 recordID에 매핑하는 작업은 어려울 수 있습니다.  |
|**국가, 부서 등을 분할하는 데 RBAC 필요**     | 데이터에 태그를 추가하고 필요한 각 분리에 대한 전용 작업 영역을 만들어 데이터 수집 사용자 지정|   사용자 지정 데이터 수집에는 추가 수집 비용이 있습니다.     |
|**단일 작업 영역에 여러 테넌트 필요**     |  Azure LightHouse와 통합 인시던트 보기를 사용하여 데이터 수집 사용자 지정|  사용자 지정 데이터 수집에는 추가 수집 비용이 있습니다.  <br><br>자세한 내용은 [작업 영역 및 테 넌 트 간에 Microsoft 센티널 확장](extend-sentinel-across-workspaces-tenants.md)을 참조 하세요.      |
| | | |

### <a name="cloud-platform-data"></a>클라우드 플랫폼 데이터

|문제/요구 사항  |가능한 해결 방법  |고려 사항  |
|---------|---------|---------|
|**다른 플랫폼의 로그 필터링**     | Logstash 사용 <br><br>Azure Monitor 에이전트/Microsoft Monitoring(Log Analytics) 에이전트 사용       |    사용자 지정 수집에는 추가 수집 비용이 있습니다. <br><br>모든 Windows 이벤트를 수집하거나 보안 이벤트만 수집해야 할 수 있습니다.     |
|**에이전트를 사용할 수 없음**     |   Windows 이벤트 전달 사용      | 리소스 간에 작업 부하를 분산해야 할 수 있습니다.        |
|**서버가 에어 갭 네트워크에 있음**     | [Log Analytics 게이트웨이](../azure-monitor/agents/gateway.md) 사용        | 에이전트에 대한 프록시를 구성하려면 게이트웨이 작동을 허용하는 방화벽 규칙이 필요합니다.        |
|**수집 시 RBAC, 태그 지정, 보강 필요**     |    Logstash 또는 Log Analytics API를 통해 사용자 지정 수집 생성     |  사용자 지정 테이블에서는 RBAC가 지원되지 않습니다. <br><br>행 수준 RBAC는 모든 테이블에서 지원되지 않습니다.       |
|     |         |         |

## <a name="next-steps"></a>다음 단계

자세한 내용은 다음을 참조하세요.

- [Microsoft 센티널 배포를 위한 배포 전 작업 및 필수 조건](prerequisites.md)
- [Microsoft 센티널의 모범 사례](best-practices.md)
- [데이터 원본 연결](connect-data-sources.md)
