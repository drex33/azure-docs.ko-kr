---
title: 코드 변경 없이 앱 모니터링 - Azure Monitor Application Insights에 대한 자동 계측 - Microsoft Docs
description: Azure Monitor Application Insights의 자동 계측 개요 - 코드리스 애플리케이션 성능 관리
ms.topic: conceptual
author: MS-jgol
ms.author: jgol
ms.date: 08/31/2021
ms.openlocfilehash: 3f6af0beb887f66f4696707dcc0e0f408004509a
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124827628"
---
# <a name="what-is-auto-instrumentation-for-azure-monitor-application-insights"></a>Azure Monitor application insights에 대 한 자동 계측 이란?

자동 계측 기능을 사용 하면 코드를 변경 하지 않고 Application Insights로 응용 프로그램 모니터링을 사용 하도록 설정할 수 있습니다.  

Application Insights는 다양한 리소스 공급자와 통합되며 여러 환경에서 작동합니다. 기본적으로 사용 하도록 설정 하 고, 일부 경우에는 원격 분석을 자동으로 수집 하는 에이전트를 구성 하면 됩니다. 언제 든 지 Application Insights 리소스에 메트릭, 요청 및 종속성이 표시 됩니다 .이를 통해 잠재적인 문제의 원인을 파악 하 고 종단 간 트랜잭션 보기를 사용 하 여 근본 원인을 분석할 수 있습니다.

## <a name="supported-environments-languages-and-resource-providers"></a>지원되는 환경, 언어 및 리소스 공급자

새 통합을 추가 하는 중에 자동 계측 기능 매트릭스가 복잡해 집니다. 아래 표에는 다양한 리소스 공급자, 언어 및 환경에 대한 지원의 현재 상태를 보여 줍니다.

|환경/리소스 공급자          | .NET            | .NET Core       | Java            | Node.js         | Python          |
|---------------------------------------|-----------------|-----------------|-----------------|-----------------|-----------------|
|Windows의 Azure App Service           | GA, OnBD*       | GA, 옵트인      | 공개 미리 보기  | 공개 미리 보기  | 지원되지 않음   |
|Linux의 Azure App Service             | 해당 없음             | 지원되지 않음   | GA              | GA              | 지원되지 않음   |
|Azure Functions - 기본                | GA, OnBD*       | GA, OnBD*       | GA, OnBD*       | GA, OnBD*       | GA, OnBD*       |
|Azure Functions 종속성         | 지원되지 않음   | 지원되지 않음   | 공개 미리 보기  | 지원되지 않음   | [확장](monitor-functions.md#distributed-tracing-for-python-function-apps) 을 통해   |
|Azure Spring Cloud                     | 지원되지 않음   | 지원되지 않음   | 공개 미리 보기  | 지원되지 않음   | 지원되지 않음   |
|Azure Kubernetes Service               | 해당 없음             | 지원되지 않음   | 에이전트를 통해   | 지원되지 않음   | 지원되지 않음   |
|Azure VM Windows                      | 공개 미리 보기  | 지원되지 않음   | 에이전트를 통해   | 지원되지 않음   | 지원되지 않음   |
|온-프레미스 VM 창                | GA, 옵트인      | 지원되지 않음   | 에이전트를 통해   | 지원되지 않음   | 지원되지 않음   |
|독립 실행형 에이전트 - 모든 env.            | 지원되지 않음   | 지원되지 않음   | GA              | 지원되지 않음   | 지원되지 않음   |

\* OnBD가 기본값으로 설정되어 있습니다. 지원되는 환경에서 앱을 배포하면 자동으로 Application Insights를 사용하도록 설정됩니다. 

## <a name="azure-app-service"></a>Azure App Service

### <a name="windows"></a>Windows

Windows에서 Azure App Service에 대 한 응용 프로그램 모니터링은 **[ASP.NET](./azure-web-apps-net.md)** (기본적으로 사용), **[ASP.NET Core](./azure-web-apps-net-core.md)**, **[Java](./azure-web-apps-java.md)** (공개 미리 보기) 및 **[Node.js](./azure-web-apps-nodejs.md)** 응용 프로그램에 사용할 수 있습니다. Python 앱을 모니터링하려면 코드에 [SDK](./opencensus-python.md)를 추가하세요.

> [!NOTE]
> Windows의 경우 응용 프로그램 모니터링은 App Service의 코드 기반/관리 서비스에 대해 현재 사용할 수 있습니다. App Service의 Windows 컨테이너에서 앱에 대한 모니터링은 Application Insights와의 통합을 통해 아직 지원되지 않습니다.

### <a name="linux"></a>Linux
포털을 통해 App Service에서 Linux에서 실행 되는 **[Java](./azure-web-apps-java.md?)**, **[Node.js](./azure-web-apps-nodejs.md?tabs=linux)** 및 **[ASP.NET Core](./azure-web-apps-net-core.md?tabs=linux)(미리 보기)** 앱에 대 한 모니터링을 사용 하도록 설정할 수 있습니다. 

[Python](./opencensus-python.md)의 경우 SDK를 사용 합니다.

## <a name="azure-functions"></a>Azure Functions

기본값으로 Azure Functions에 대한 기본적인 모니터링을 사용하여 로그, 성능, 오류 데이터 및 HTTP 요청을 수집합니다. Java 애플리케이션의 경우 분산 추적을 통해 다양한 모니터링을 사용하도록 설정하고 엔드투엔드 트랜잭션 세부 정보를 얻을 수 있습니다. Java용 기능은 Windows용 공개 미리 보기 상태이며 [Azure Portal에서 사용하도록 설정](./monitor-functions.md)할 수 있습니다.

## <a name="azure-spring-cloud"></a>Azure Spring Cloud

### <a name="java"></a>Java 
Azure Spring Cloud에서 실행되는 Java 앱에 대한 애플리케이션 모니터링은 포털에 통합되어 있으며, 기존 및 새로 만든 Azure Spring Cloud 리소스 모두에 대해 Azure Portal에서 직접 Application Insights를 사용하도록 설정할 수 있습니다.  

## <a name="azure-kubernetes-service"></a>Azure Kubernetes Service

Azure Kubernetes Service의 코드리스 계측은 현재 [독립 실행형 에이전트](./java-in-process-agent.md)를 통해 Java 애플리케이션에 사용할 수 있습니다. 

## <a name="azure-windows-vms-and-virtual-machine-scale-set"></a>Azure Windows VM 및 가상 머신 확장 집합

Azure Vm 및 가상 머신 확장 집합에 대 한 자동 계측은 [.net](./azure-vm-vmss-apps.md) 및 [Java](./java-in-process-agent.md) 에서 사용할 수 있습니다 .이 환경은 포털에 통합 되지 않습니다. 모니터링은 독립 실행형 솔루션의 몇 가지 단계를 통해 사용 하도록 설정 되며 코드를 변경할 필요가 없습니다.  

## <a name="on-premises-servers"></a>온-프레미스 서버
[Java 앱](./java-in-process-agent.md)용 및 [.NET 애플리케이션용 온-프레미스 Windows 서버](./status-monitor-v2-overview.md)에 대한 모니터링을 쉽게 사용하도록 설정할 수 있습니다.

## <a name="other-environments"></a>기타 환경
다양한 Java 독립 실행형 에이전트는 모든 환경에서 작동하므로 코드를 계측할 필요가 없습니다. Application Insights를 사용하도록 설정하고 Java 에이전트의 놀라운 기능을 보려면 [이 가이드를 따르세요](./java-in-process-agent.md). 에이전트는 공개 미리 보기 상태이며 모든 지역에서 사용할 수 있습니다. 

## <a name="next-steps"></a>다음 단계

* [Application Insights 개요](./app-insights-overview.md)
* [애플리케이션 맵](./app-map.md)
* [엔드투엔드 성능 모니터링](../app/tutorial-performance.md)
