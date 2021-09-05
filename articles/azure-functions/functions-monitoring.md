---
title: Azure Functions 모니터링
description: Azure Application Insights를 Azure Functions와 함께 사용하여 함수 실행을 모니터링하는 방법을 알아봅니다.
ms.assetid: 501722c3-f2f7-4224-a220-6d59da08a320
ms.topic: conceptual
ms.date: 10/14/2020
ms.custom: devx-track-csharp, fasttrack-edit, contperf-fy21q2, devx-track-js
ms.openlocfilehash: 0967e440a661d6bf51dc5206e5c21cdda45db073
ms.sourcegitcommit: e0ef8440877c65e7f92adf7729d25c459f1b7549
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/09/2021
ms.locfileid: "113565133"
---
# <a name="monitor-azure-functions"></a>Azure Functions 모니터링

[Azure Functions](functions-overview.md)는 함수를 모니터링할 수 있도록 [Azure Application Insights](../azure-monitor/app/app-insights-overview.md)와 기본적으로 통합됩니다. 이 문서에서는 Azure Functions 모니터링을 위해 Azure에서 제공하는 모니터링 기능에 대한 개요를 제공합니다.

Application Insights는 로그, 성능 및 오류 데이터를 수집합니다. 성능 변칙을 자동으로 감지하고 강력한 분석 도구를 사용하여 더 쉽게 문제를 진단하고 함수 사용 방법을 더 잘 파악할 수 있습니다. 이러한 도구는 함수에 대한 성능 및 사용 편의성을 지속적으로 개선하는 데 도움이 되도록 설계되었습니다. 로컬 함수 앱 프로젝트를 개발할 때도 Application Insights을 사용할 수 있습니다. 자세한 내용은 [Application Insights란?](../azure-monitor/app/app-insights-overview.md)을 참조하세요.

Application Insights 계측이 Azure Functions에 기본 제공되므로 함수 앱을 Application Insights 리소스에 연결하려면 유효한 계측 키가 필요합니다. Azure에서 함수 앱 리소스를 만들 때 애플리케이션 설정에 계측 키가 추가됩니다. 함수 앱에 이 키가 아직 없는 경우 [수동으로 설정](configure-monitoring.md#enable-application-insights-integration)할 수 있습니다.  

## <a name="application-insights-pricing-and-limits"></a>Application Insights 가격 책정 및 제한

무료로 처리되는 데이터의 양에 대한 일일 한도를 제공하는 Azure Functions와 Application Insights 통합을 사용해 볼 수 있습니다.

개발하는 동안 Application Insights를 사용하도록 설정하면 테스트 중에 이 한도에 도달할 수 있습니다. Azure는 일일 한도에 가까워지면 포털 및 이메일 알림을 제공합니다. 이러한 경고를 놓치고 제한에 도달하면 Application Insights 쿼리에 새 로그가 표시되지 않습니다. 불필요하게 문제 해결에 시간을 낭비하는 일이 없도록 이러한 제한을 잘 알고 있어야 합니다. 자세한 내용은 [Application Insights에서 가격 책정 및 데이터 볼륨 관리](../azure-monitor/app/pricing.md)를 참조하세요.

> [!IMPORTANT]
> Application Insights에는 최대 부하 시 실행이 완료될 때 원격 분석 데이터를 너무 많이 생성하지 않도록 방지하는 [샘플링](../azure-monitor/app/sampling.md) 기능이 포함되어 있습니다. 샘플링은 기본적으로 사용하도록 설정됩니다. 데이터가 누락된 것 같으면 샘플링 설정을 특정 모니터링 시나리오에 맞게 조정하면 됩니다. 자세한 내용은 [샘플링 구성](configure-monitoring.md#configure-sampling)을 참조하세요.

함수 앱에 사용할 수 있는 Application Insights 기능의 전체 목록은 [Azure Functions에 대한 Application Insights 지원 기능](../azure-monitor/app/azure-functions-supported-features.md)에 자세히 설명되어 있습니다.

## <a name="application-insights-integration"></a>Application Insights 통합

일반적으로 함수 앱을 만들 때 Application Insights 인스턴스를 만듭니다. 이 경우 통합에 필요한 계측 키는 이미 `APPINSIGHTS_INSTRUMENTATIONKEY`라는 애플리케이션 설정으로 설정되어 있습니다. 어떤 이유로 함수 앱에 계측 키 집합이 없는 경우 [Application Insights 통합을 사용하도록 설정](configure-monitoring.md#enable-application-insights-integration)해야 합니다.  

> [!IMPORTANT]
> Azure Government와 같은 소버린 클라우드에는 계측 키 대신 Application Insights 연결 문자열(`APPLICATIONINSIGHTS_CONNECTION_STRING`)을 사용해야 합니다. 자세한 내용은 [APPLICATIONINSIGHTS_CONNECTION_STRING 참조](functions-app-settings.md#applicationinsights_connection_string)를 참조하세요.

## <a name="collecting-telemetry-data"></a>원격 분석 데이터 수집

Application Insights 통합을 사용하도록 설정하면 원격 분석 데이터가 연결된 Application Insights 인스턴스로 전송됩니다. 이 데이터에는 Functions 호스트에 의해 생성된 로그, 함수 코드에서 작성된 추적 및 성능 데이터가 포함됩니다. 

>[!NOTE]
>함수 및 Functions 호스트의 데이터 외에도 [Functions 크기 조정 컨트롤러](#scale-controller-logs)에서 데이터를 수집할 수 있습니다.   

### <a name="log-levels-and-categories"></a>로그 수준 및 범주

애플리케이션 코드에서 추적을 작성하는 경우 추적에 로그 수준을 할당해야 합니다. 로그 수준은 추적에서 수집되는 데이터의 양을 제한하는 방법을 제공합니다.  

[!INCLUDE [functions-log-levels](../../includes/functions-log-levels.md)]

로그 수준에 대한 자세한 내용은 [로그 수준 구성](configure-monitoring.md#configure-log-levels)을 참조하세요.

기록된 항목을 범주에 할당하여 함수 앱의 특정 원본에서 생성된 원격 분석을 보다 효과적으로 제어할 수 있습니다. 범주를 통해 수집된 데이터에 대한 분석을 보다 쉽게 실행할 수 있습니다. 함수 코드에서 작성된 추적은 함수 이름에 따라 개별 범주에 할당됩니다. 카테고리에 대한 자세한 내용은 [카테고리 구성](configure-monitoring.md#configure-categories)을 참조하세요.

### <a name="custom-telemetry-data"></a>사용자 지정 원격 분석 데이터

[C#](functions-dotnet-class-library.md#log-custom-telemetry-in-c-functions), [JavaScript](functions-reference-node.md#log-custom-telemetry) 및 [Python](functions-reference-python.md#log-custom-telemetry)에서 Application Insights SDK를 사용하여 사용자 지정 원격 분석 데이터를 작성할 수 있습니다.

### <a name="dependencies"></a>종속성

Functions 버전 2.x부터 런타임은 특정 클라이언트 SDK를 사용하는 바인딩에 대한 종속성에 대한 데이터를 자동으로 수집합니다. Application Insights는 다음 종속성에 대한 데이터를 수집합니다.

+ Azure Cosmos DB 
+ Azure Event Hubs
+ Azure Service Bus
+ Azure Storage 서비스(Blob, 큐 및 테이블)

`SqlClient`를 사용하는 HTTP 요청 및 데이터베이스 호출도 캡처됩니다. Application Insights에서 지원하는 전체 종속성 목록은 [자동으로 추적되는 종속성](../azure-monitor/app/asp-net-dependencies.md#automatically-tracked-dependencies)을 참조하세요.

Application Insights는 수집된 종속성 데이터의 _애플리케이션 맵_ 을 생성합니다. 다음은 Queue Storage 출력 바인딩이 있는 HTTP 트리거 함수의 애플리케이션 맵 예입니다.  

![종속성이 있는 애플리케이션 맵](./media/functions-monitoring/app-map.png)

종속성은 `Information` 수준에서 작성됩니다. `Warning` 이상에서 필터링하면 종속성 데이터가 표시되지 않습니다. 또한 사용자 범위 외에서 자동 종속성 수집이 발생합니다. 종속성 데이터를 캡처하려면 수준이 호스트의 사용자 범위(`Function.<YOUR_FUNCTION_NAME>.User`) 외부에서 `Information` 이상으로 설정되어 있는지 확인합니다.

자동 종속성 데이터 수집 외에도 언어별 Application Insights SDK 중 하나를 사용하여 사용자 지정 종속성 정보를 로그에 작성할 수도 있습니다. 사용자 지정 종속성 작성 방법에 대한 예는 다음 언어별 예 중 하나를 참조하세요.

+ [C# 함수의 사용자 지정 원격 분석 로깅](functions-dotnet-class-library.md#log-custom-telemetry-in-c-functions)
+ [JavaScript 함수의 사용자 지정 원격 분석 로깅](functions-reference-node.md#log-custom-telemetry) 
+ [Python 함수의 사용자 지정 원격 분석 로깅](functions-reference-python.md#log-custom-telemetry)

## <a name="writing-to-logs"></a>로그에 작성 

로그 및 사용하는 API에 작성하는 방법은 함수 앱 프로젝트의 언어에 따라 달라집니다.   
함수에서 로그를 작성하는 방법에 대한 자세한 내용은 언어에 대한 개발자 가이드를 참조하세요.

+ [C#(.NET 클래스 라이브러리)](functions-dotnet-class-library.md#logging)
+ [Java](functions-reference-java.md#logger)
+ [JavaScript](functions-reference-node.md#write-trace-output-to-logs) 
+ [PowerShell](functions-reference-powershell.md#logging)
+ [Python](functions-reference-python.md#logging)

## <a name="analyze-data"></a>데이터 분석

기본적으로 함수 앱에서 수집된 데이터는 Application Insights에 저장됩니다. [Azure Portal](https://portal.azure.com)에서 Application Insights는 원격 분석 데이터의 다양한 시각화 세트를 제공합니다. 오류 로그를 확인하고 이벤트와 메트릭을 쿼리할 수 있습니다. 수집된 데이터를 보고 쿼리하는 방법에 대한 기본 예를 포함한 자세한 내용은 [Application Insights에서 Azure Functions 원격 분석](analyze-telemetry-data.md)을 참조하세요. 

## <a name="streaming-logs"></a>스트리밍 로그

애플리케이션을 개발하는 동안 Azure에서 실행될 때 거의 실시간으로 로그에 기록되는 항목을 보고 싶은 경우가 있습니다.

함수 실행에 의해 생성되는 로그 데이터의 스트리밍을 확인하는 방법에는 두 가지가 있습니다.

* **기본 제공 로그 스트리밍**: App Service 플랫폼을 사용하여 애플리케이션 로그 파일의 스트림을 볼 수 있습니다. 이 스트림은 [로컬 개발](functions-develop-local.md)에서 함수를 디버그할 때와 포털에서 **테스트** 탭을 사용할 때 표시되는 출력에 해당합니다. 모든 로그 기반 정보가 표시됩니다. 자세한 내용은 [로그 스트리밍](../app-service/troubleshoot-diagnostic-logs.md#stream-logs)을 참조하세요. 이 스트리밍 방법은 단일 인스턴스만 지원하며, 사용 계획을 사용하는 Linux에서 실행되는 앱에는 사용할 수 없습니다.

* **라이브 메트릭 스트림**: 함수 앱이 [Application Insights에 연결](configure-monitoring.md#enable-application-insights-integration)되는 경우 Azure Portal에서 [라이브 메트릭 스트림](../azure-monitor/app/live-stream.md)을 사용하여 로그 데이터 및 기타 메트릭을 거의 실시간으로 볼 수 있습니다. 사용 계획을 사용하는 여러 인스턴스 또는 Linux에서 실행되는 함수를 모니터링할 때 이 방법을 사용합니다. 이 방법은 [샘플링된 데이터](configure-monitoring.md#configure-sampling)를 사용합니다.

로그 스트림은 포털과 대부분의 로컬 개발 환경에서 볼 수 있습니다. 로그 스트리밍을 사용하는 방법을 알아보려면 [Azure Functions에서 스트리밍 실행 로그 사용](streaming-logs.md)을 참조하세요.

## <a name="diagnostic-logs"></a>진단 로그

‘이 기능은 미리 보기로 제공됩니다.’ 

Application Insights를 사용하여 원격 분석 데이터를 장기 스토리지 또는 다른 분석 서비스로 내보낼 수 있습니다.  

또한 Functions는 Azure Monitor와 통합되기 때문에 진단 설정을 사용하여 Azure Monitor 로그를 비롯한 다양한 대상에 원격 분석 데이터를 보낼 수도 있습니다. 자세한 내용은 [Azure Monitor 로그를 사용한 Azure Functions 모니터링](functions-monitor-log-analytics.md)을 참조하세요.

## <a name="scale-controller-logs"></a>컨트롤러 로그 크기 조정

‘이 기능은 미리 보기로 제공됩니다.’ 

[Azure Functions 크기 조정 컨트롤러](./event-driven-scaling.md#runtime-scaling)는 앱이 실행되는 Azure Functions 호스트의 인스턴스를 모니터링합니다. 이 컨트롤러는 현재 성능에 따라 인스턴스를 추가하거나 제거할 시기에 대한 결정을 내립니다. 크기 조정 컨트롤러가 함수 앱에 대해 내리는 결정을 더 잘 이해하기 위해 크기 조정 컨트롤러가 Application Insights에 로그를 내보내도록 할 수 있습니다. 다른 서비스에서 분석을 위해 생성된 로그를 Blob 스토리지에 저장할 수도 있습니다. 

이 기능을 사용하도록 설정하려면 `SCALE_CONTROLLER_LOGGING_ENABLED`라는 애플리케이션 설정을 함수 앱 설정에 추가합니다. 이 방법을 알아보려면 [크기 조정 컨트롤러 로그 구성](configure-monitoring.md#configure-scale-controller-logs)을 참조하세요.

## <a name="azure-monitor-metrics"></a>Azure Monitor 메트릭

Application Insights에서 수집한 로그 기반 원격 분석 데이터 외에도 [Azure Monitor 메트릭](../azure-monitor/essentials/data-platform-metrics.md)에서 함수 앱이 실행되는 방법에 대한 데이터도 가져올 수 있습니다. 자세한 내용은 [Azure Functions에서 Azure Monitor 메트릭 사용](monitor-metrics.md)을 참조하세요.

## <a name="report-issues"></a>문제 보고

Application Insights의 Functions 통합 문제를 보고하거나 제안 사항 또는 요청 사항을 보내려면 [GitHub에서 문제를 만듭니다](https://github.com/Azure/Azure-Functions/issues/new).

## <a name="next-steps"></a>다음 단계

자세한 내용은 다음 리소스를 참조하세요.

* [Application Insights](/azure/application-insights/)
* [ASP.NET Core 로깅](/aspnet/core/fundamentals/logging/)
