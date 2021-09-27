---
title: Azure Migrate 검색 및 평가에서 종속성 분석
description: Azure Migrate 검색 및 평가를 사용하여 평가에 대한 종속성 분석을 사용하는 방법을 설명합니다.
ms.topic: conceptual
author: vineetvikram
ms.author: vivikram
ms.manager: abhemraj
ms.date: 03/18/2021
ms.openlocfilehash: 55e8efc7b49f14f6eb4cfe399866e53df29e3e36
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124767065"
---
# <a name="dependency-analysis"></a>종속성 분석

이 문서에서는 Azure Migrate: 검색 및 평가에서 종속성 분석에 대해 설명합니다.

종속성 분석은 검색된 온-프레미스 서버 간의 종속성을 식별합니다. 이는 다음과 같은 장점을 제공합니다.

- 보다 정확하게, 보다 확실하게 평가할 수 있도록 서버를 그룹으로 수집할 수 있습니다.
- 함께 마이그레이션해야 하는 서버를 식별할 수 있습니다. 이는 Azure로 마이그레이션할 앱 배포의 일부인 서버를 모를 경우 특히 유용합니다.
- 서버가 사용 중인지 여부와 마이그레이션 대신 해제할 수 있는 서버를 식별할 수 있습니다.
- 종속성을 분석하면 마이그레이션 후 누락되는 것이 없는지 확인하므로 갑작스러운 중단을 방지할 수 있습니다.
- 종속성 분석에 대한 일반적인 질문을 [검토](common-questions-discovery-assessment.md#what-is-dependency-visualization)합니다.

## <a name="analysis-types"></a>분석 형식

종속성 분석을 배포하는 옵션은 두 가지가 있습니다.

**옵션** | **세부 정보** | **퍼블릭 클라우드** | **Azure Government**
----  |---- | ----
**에이전트 없음** | VSphere API를 사용하여 VMware 서버에서 데이터를 폴링합니다.<br/><br/> 서버에 에이전트를 설치할 필요가 없습니다.<br/><br/> 이 옵션은 현재 VMware의 서버에만 해당됩니다. | 지원됨. | 지원됨.
**에이전트 기반 분석** | Azure Monitor의 [서비스 맵 솔루션](../azure-monitor/vm/service-map.md)을 사용하여 종속성 시각화 및 분석을 사용하도록 설정합니다.<br/><br/> 분석하려는 각 온-프레미스 서버에 에이전트를 설치해야 합니다. | 지원됨 | 지원되지 않습니다.

## <a name="agentless-analysis"></a>에이전트 없는 분석

에이전트 없는 종속성 분석은 사용하도록 설정된 서버에서 TCP 연결 데이터를 캡처하여 작동합니다. 서버에 에이전트가 설치되지 않습니다. 동일한 원본 서버와 프로세스 및 대상 서버, 프로세스, 포트를 사용하는 연결은 종속성에 따라 논리적으로 그룹화됩니다. 지도 뷰에서 캡처된 종속성 데이터를 시각화하거나 CSV로 내보낼 수 있습니다. 분석하려는 서버에 에이전트가 설치되어 있지 않습니다.

### <a name="dependency-data"></a>종속성 데이터

종속성 데이터 검색이 시작된 후 폴링이 시작됩니다.

- Azure Migrate 어플라이언스는 데이터를 수집하기 위해 서버에서 5분마다 TCP 연결 데이터를 폴링합니다.
- 데이터는 vSphere API를 사용하여 vCenter Server를 통해 게스트 서버에서 수집됩니다.
- 폴링은 다음 데이터를 수집합니다.

    - 활성 연결이 있는 프로세스의 이름.
    - 활성 연결이 있는 프로세스를 실행하는 애플리케이션의 이름.
    - 활성 연결의 대상 포트.

- 수집된 데이터는 Azure Migrate 어플라이언스에서 처리되어 ID 정보를 추론하고 6시간마다 Azure Migrate로 전송됩니다.


## <a name="agent-based-analysis"></a>에이전트 기반 분석

에이전트 기반 분석의 경우 Azure Migrate: 검색 및 평가는 Azure Monitor의 [서비스 맵](../azure-monitor/vm/service-map.md) 솔루션을 사용합니다. 분석하려는 각 서버에 [Microsoft Monitoring Agent/Log Analytics 에이전트](../azure-monitor/agents/agents-overview.md#log-analytics-agent)와 [종속성 에이전트](../azure-monitor/agents/agents-overview.md#dependency-agent)를 설치합니다.

### <a name="dependency-data"></a>종속성 데이터

에이전트 기반 분석은 다음 데이터를 제공합니다.

- 원본 서버 이름, 프로세스, 애플리케이션 이름입니다.
- 대상 서버 이름, 프로세스, 애플리케이션 이름 및 포트입니다.
- 연결 수, 대기 시간 및 데이터 전송 정보가 수집되어 Log Analytics 쿼리에 사용할 수 있습니다.

## <a name="compare-agentless-and-agent-based"></a>에이전트 없는 시각화와 에이전트 기반 시각화 비교

다음 표에는 에이전트 없는 시각화와 에이전트 기반 시각화의 차이점이 요약되어 있습니다.

**요구 사항** | **에이전트 없음** | **에이전트 기반**
--- | --- | ---
**지원** | VMware의 서버에만 사용할 수 있습니다. 지원되는 운영 체제를 [검토](migrate-support-matrix-vmware.md#dependency-analysis-requirements-agentless)하세요. | GA(일반 공급) 상태입니다.
**에이전트** | 분석하려는 서버에 에이전트가 필요하지 않습니다. | 분석하려는 각 온-프레미스 서버에 에이전트가 필요합니다.
**Log Analytics** | 필수 아님. | Azure Migrate는 종속성 분석에 대한 [Azure Monitor 로그](../azure-monitor/logs/log-query-overview.md)의 [서비스 맵](../azure-monitor/vm/service-map.md) 솔루션을 사용합니다.<br/><br/> Log Analytics 작업 영역을 프로젝트와 연결합니다. 작업 영역은 미국 동부, 동남 아시아 또는 서유럽 지역에 있어야 합니다. 작업 영역은 [서비스 맵 지원되는](../azure-monitor/vm/vminsights-configure-workspace.md#supported-regions) 지역에 있어야 합니다.
**처리** | TCP 연결 데이터를 캡처합니다. 검색 후 5분 간격으로 데이터를 수집합니다. | 서버에 설치된 서비스 맵 에이전트는 각 프로세스의 TCP 프로세스 및 인바운드/아웃바운드 연결에 대한 데이터를 수집합니다.
**Data** | 원본 서버 이름, 프로세스, 애플리케이션 이름입니다.<br/><br/> 대상 서버 이름, 프로세스, 애플리케이션 이름 및 포트입니다. | 원본 서버 이름, 프로세스, 애플리케이션 이름입니다.<br/><br/> 대상 서버 이름, 프로세스, 애플리케이션 이름 및 포트입니다.<br/><br/> 연결 수, 대기 시간 및 데이터 전송 정보가 수집되어 Log Analytics 쿼리에 사용할 수 있습니다. 
**시각화** | 단일 서버의 종속성 맵은 1시간에서 30일 사이의 기간으로 표시됩니다. | 단일 서버의 종속성 맵입니다.<br/><br/> 서버 그룹의 종속성 맵입니다.<br/><br/>  맵은 1시간 기간으로만 표시됩니다.<br/><br/> 맵 보기에서 그룹의 서버를 추가 및 제거합니다.
데이터 내보내기 | 지난 30일 간의 데이터는 CSV 형식으로 다운로드할 수 있습니다. | 데이터는 Log Analytics를 사용하여 쿼리할 수 있습니다.



## <a name="next-steps"></a>다음 단계

- 에이전트 기반 종속성 시각화를 [설정](how-to-create-group-machine-dependencies.md)합니다.
- VMware 서버에 대한 에이전트 없는 종속성 시각화를 [사용해 보세요](how-to-create-group-machine-dependencies-agentless.md).
- 종속성 시각화에 대한 [일반적인 질문](common-questions-discovery-assessment.md#what-is-dependency-visualization)을 검토합니다.
