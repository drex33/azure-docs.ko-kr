---
title: Azure Functions 모범 사례
description: Azure에서 실행되는 효율적인 함수 코드를 디자인, 배포 및 유지 관리하기 위한 모범 사례를 알아봅니다.
ms.assetid: 9058fb2f-8a93-4036-a921-97a0772f503c
ms.topic: conceptual
ms.date: 08/30/2021
ms.openlocfilehash: 2cae69069488131e195785992e8d5f939e13473e
ms.sourcegitcommit: 3ef5a4eed1c98ce76739cfcd114d492ff284305b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128708114"
---
# <a name="best-practices-for-reliable-azure-functions"></a>신뢰할 수 있는 Azure Functions 대한 모범 사례

Azure Functions Azure, 타사 서비스 및 온-프레미스 시스템에서 발생하는 이벤트에 의해 트리거되는 코드를 구현하는 기능을 사용하여 기존 Azure App Service 애플리케이션 플랫폼을 확장하는 이벤트 기반의 주문형 컴퓨팅 환경입니다. 함수를 사용하면 데이터 원본 또는 메시징 솔루션에 연결하여 솔루션을 빌드할 수 있으며, 이를 통해 이벤트를 보다 쉽게 처리하고 대응할 수 있습니다. 함수는 여러 통합 구성 요소와 복잡한 Azure 데이터 센터에서 실행됩니다. 호스트된 클라우드 환경에서는 VM이 가끔 다시 시작하거나 이동할 수 있으며 시스템 업그레이드가 발생할 것으로 예상됩니다. 또한 함수 앱은 외부 API, Azure 서비스 및 기타 데이터베이스에 의존할 가능성이 높으며, 주기적인 불안정성이 발생하기 쉽습니다. 

이 문서에서는 클라우드 기반 환경에서 정상 상태를 유지하고 효율적으로 작동하는 효율적인 함수 앱을 디자인하고 배포하기 위한 몇 가지 모범 사례를 자세히 설명합니다.

## <a name="choose-the-correct-hosting-plan"></a>올바른 호스팅 계획 선택 

Azure에서 함수 앱을 만들 때는 앱의 호스팅 계획을 선택해야 합니다. 선택하는 계획은 성능, 안정성 및 비용에 영향을 줍니다. Functions에 사용할 수 있는 세 가지 기본 호스팅 계획이 있습니다. 

+ [사용 계획](consumption-plan.md)
+ [프리미엄 계획](functions-premium-plan.md)
+ [전용(App Service) 계획.](dedicated-plan.md)

Linux 또는 Windows 실행하는 경우 모든 호스팅 계획이 GA(일반 제공)됩니다.

App Service 플랫폼의 컨텍스트에서 함수를 동적으로 호스트하는 데 사용되는 Premium 계획은 EP(탄력적 Premium 계획)입니다. Premium 이라는 다른 전용(App Service) 계획이 있습니다. 자세한 내용은 Premium [계획](functions-premium-plan.md) 문서를 참조하세요.

선택한 호스팅 계획에 따라 다음 동작이 결정됩니다.

+   수요에 따라 함수 앱의 크기를 조정하는 방법 및 인스턴스 할당을 관리하는 방법입니다.
+   각 함수 앱 인스턴스에 사용 가능한 리소스
+   Azure Virtual Network 연결 등의 고급 기능 지원

올바른 호스팅 계획을 선택하는 방법 및 계획 간의 자세한 비교에 대한 자세한 내용은 [호스팅 옵션 Azure Functions 참조하세요.](functions-scale.md)

함수 앱을 만들 때 올바른 계획을 선택하는 것이 중요합니다. Functions는 주로 소비 계획과 탄력적 Premium 계획 간에 호스팅 계획을 전환하는 제한된 기능을 제공합니다. 자세한 내용은 [마이그레이션 계획을 참조하세요.](functions-how-to-use-azure-function-app-settings.md?tabs=portal#plan-migration) 

## <a name="configure-storage-correctly"></a>스토리지를 올바르게 구성

함수를 사용하려면 스토리지 계정을 함수 앱과 연결해야 합니다. 스토리지 계정 연결은 함수 호스트에서 트리거 관리 및 함수 실행 로깅과 같은 작업에 사용됩니다. 함수 앱의 크기를 동적으로 조정하는 경우에도 사용됩니다. 자세한 내용은 [Azure Functions Storage 고려 사항을](storage-considerations.md)참조하세요.

함수 앱에서 잘못 구성된 파일 시스템 또는 스토리지 계정은 함수의 성능 및 가용성에 영향을 줄 수 있습니다. 잘못 구성된 스토리지 계정 문제 해결에 대한 도움말은 [스토리지 문제 해결](functions-recover-storage-account.md) 문서를 참조하세요. 

### <a name="storage-connection-settings"></a>연결 설정 Storage

동적으로 크기를 조정하는 함수 앱은 스토리지 계정의 Azure Files 엔드포인트 또는 스케일 아웃 인스턴스와 연결된 파일 서버에서 실행할 수 있습니다. 이 동작은 다음 애플리케이션 설정에 의해 제어됩니다.

+ [WEBSITE_CONTENTAZUREFILECONNECTIONSTRING](functions-app-settings.md#website_contentazurefileconnectionstring)
+ [WEBSITE_CONTENTSHARE](functions-app-settings.md#website_contentshare)

이러한 설정은 Premium 계획 또는 Windows 소비 계획에서 실행하는 경우에만 지원됩니다.

Azure Portal 또는 Azure CLI 또는 Azure PowerShell 사용하여 함수 앱을 만들 때 필요한 경우 함수 앱에 대해 이러한 설정이 만들어집니다. ARM 템플릿(Azure Resource Manager 템플릿)에서 리소스를 만들 때 템플릿에도 를 포함해야 `WEBSITE_CONTENTAZUREFILECONNECTIONSTRING` 합니다. 

ARM 템플릿을 사용하는 첫 번째 배포에서는 생성되는 를 포함하지 `WEBSITE_CONTENTSHARE` 마세요.   

다음 ARM 템플릿 예제를 사용하여 이러한 설정을 올바르게 구성할 수 있습니다.

+ [사용 플랜](https://azure.microsoft.com/resources/templates/function-app-create-dynamic/)
+ [전용 계획](https://azure.microsoft.com/resources/templates/function-app-create-dedicated/)
+ [VNET 통합을 Premium 계획](https://azure.microsoft.com/resources/templates/function-premium-vnet-integration/)
+ [배포 슬롯이 있는 소비 계획](https://azure.microsoft.com/resources/templates/function-app-create-dynamic-slot/)

### <a name="storage-account-configuration"></a>Storage 계정 구성

함수 앱을 만들 때 Blob, Queue 및 Table 스토리지를 지원하는 범용 Azure Storage 계정을 만들거나 해당 계정에 연결해야 합니다. 함수는 트리거 관리 및 함수 실행 로깅과 같은 작업에 Azure Storage 의존합니다. 함수 앱에 대한 스토리지 계정 연결 문자열은 및 애플리케이션 설정에서 찾을 수 `AzureWebJobsStorage` `WEBSITE_CONTENTAZUREFILECONNECTIONSTRING` 있습니다.

이 스토리지 계정을 만들 때 고려해야 할 사항은 다음과 같습니다. 

+ 대기 시간을 줄이려면 함수 앱과 동일한 지역에 스토리지 계정을 만듭니다.

+ 프로덕션의 성능을 향상하려면 각 함수 앱에 대해 별도의 스토리지 계정을 사용합니다. Durable Functions 및 이벤트 허브 트리거 함수에서 특히 그렇습니다. 

+ Event Hub 트리거 함수의 경우 Data Lake [Storage 사용하도록 설정된](https://github.com/Azure/azure-functions-eventhubs-extension/issues/81)계정을 사용하지 마세요.

### <a name="handling-large-data-sets"></a>큰 데이터 집합 처리

Linux에서 실행하는 경우 파일 공유를 탑재하여 추가 스토리지를 추가할 수 있습니다. 공유 탑재는 함수가 대규모 기존 데이터 집합을 처리하는 편리한 방법입니다. 자세한 내용은 [파일 공유 탑재를 참조하세요.](storage-considerations.md#mount-file-shares)

## <a name="organize-your-functions"></a>함수 구성 

솔루션의 일부로 여러 함수를 개발하고 게시할 수 있습니다. 이러한 함수는 대개 단일 함수 앱으로 결합되지만 별도의 함수 앱에서 실행할 수도 있습니다. Premium 및 전용(App Service) 호스팅 계획에서 여러 함수 앱은 동일한 계획에서 실행하여 동일한 리소스를 공유할 수도 있습니다. 함수 및 함수 앱을 그룹화하는 방법은 전체 솔루션의 성능, 스케일링, 구성, 배포, 보안에 영향을 줄 수 있습니다. 

소비 및 Premium 계획의 경우 함수 앱의 모든 함수가 동적으로 함께 확장됩니다.

함수를 구성하는 방법에 대한 자세한 내용은 함수 조직 모범 사례 를 [참조하세요.](performance-reliability.md#function-organization-best-practices)

## <a name="optimize-deployments"></a>배포 최적화

함수 앱을 배포할 때는 Azure의 함수에 대한 배포 단위가 함수 앱이라는 점에 유의해야 합니다. 함수 앱의 모든 함수는 일반적으로 동일한 배포 패키지에서 동시에 배포됩니다.  

성공적인 배포를 위해 다음 옵션을 고려합니다.

+  배포 패키지에서 함수를 실행합니다. [패키지 접근 방식에서 이 실행을 통해](run-functions-from-deployment-package.md) 다음과 같은 이점이 제공됩니다.

    + 파일 복사 잠금 문제의 위험을 줄여줍니다. 
    + 다시 시작을 트리거하는 프로덕션 앱에 직접 배포할 수 있습니다. 
    + 패키지의 모든 파일을 앱에서 사용할 수 있습니다. 
    + ARM 템플릿 배포의 성능을 향상시킵니다.
    + 특히 대규모 npm 패키지 트리가 있는 JavaScript 함수의 경우 콜드 부팅 시간을 줄일 수 있습니다.

+ 지속적인 [배포를](functions-continuous-deployment.md) 사용하여 배포를 소스 제어 솔루션에 연결하는 것이 좋습니다. 연속 배포를 사용하면 배포 패키지에서 실행할 수도 있습니다.

+ [Premium 계획 호스팅의](functions-premium-plan.md)경우 새 인스턴스가 추가되면 대기 시간을 줄이기 위해 준비 트리거를 추가하는 것이 좋습니다. 자세한 내용은 [준비 트리거 Azure Functions](functions-bindings-warmup.md)참조하세요. 

## <a name="write-robust-functions"></a>강력한 함수 작성

함수의 일반 성능 및 가용성에 도움이 되는 함수 코드를 작성할 때 수행할 수 있는 몇 가지 디자인 원칙이 있습니다. 이러한 원칙은 다음과 같습니다.
 
+ [장기 실행 함수를 사용하지 않습니다.](performance-reliability.md#avoid-long-running-functions) 
+ [함수 간 통신을 계획합니다.](performance-reliability.md#cross-function-communication) 
+ [상태 비주문이 될 함수를 작성합니다.](performance-reliability.md#write-functions-to-be-stateless)
+ [방어 함수를 작성합니다.](performance-reliability.md#write-defensive-functions)

일시적 오류는 클라우드 컴퓨팅에서 일반적이므로 클라우드 기반 리소스에 액세스할 때 [재시도 패턴을](/azure/architecture/patterns/retry) 사용해야 합니다. 많은 트리거 및 바인딩이 이미 재시도를 구현합니다. 

## <a name="design-for-security"></a>보안을 위해 설계

보안은 함수를 사용할 준비가 된 후가 아니라 계획 단계에서 가장 잘 고려됩니다. 함수를 안전하게 개발하고 배포하는 방법을 알아보려면 [보안 Azure Functions.](security-concepts.md)  

## <a name="consider-concurrency"></a>동시성 고려

들어오는 이벤트의 결과로 함수 앱에 수요가 빌드되면 소비 및 Premium 계획에서 실행되는 함수 앱이 확장됩니다. 함수 앱이 로드에 응답하는 방법과 들어오는 이벤트를 처리하도록 트리거를 구성하는 방법을 이해하는 것이 중요합니다. 일반적인 개요는 [Azure Functions 이벤트 기반 크기 조정을](event-driven-scaling.md)참조하세요.

전용(App Service) 계획을 사용하려면 함수 앱을 확장하기 위해 제공해야 합니다. 

### <a name="worker-process-count"></a>작업자 프로세스 수

경우에 따라 확장 하기 전에 인스턴스에서 언어 작업자 프로세스 라는 여러 프로세스를 만들어 로드를 처리 하는 것이 더 효율적입니다. 허용 되는 언어 작업자 프로세스의 최대 수는 [FUNCTIONS_WORKER_PROCESS_COUNT](functions-app-settings.md#functions_worker_process_count) 설정에 의해 제어 됩니다. 이 설정의 기본값은 `1` 여러 프로세스가 사용 되지 않음을 의미 하는입니다. 최대 프로세스 수에 도달한 후 함수 앱은 부하를 처리 하기 위해 더 많은 인스턴스로 확장 됩니다. 이 설정은 호스트 프로세스에서 실행 되는 [c # 클래스 라이브러리 함수](functions-dotnet-class-library.md)에는 적용 되지 않습니다.

`FUNCTIONS_WORKER_PROCESS_COUNT`Premium 계획 또는 전용 (App Service) 계획에서를 사용 하는 경우 계획에서 제공 하는 코어 수를 염두에 두어야 합니다. 예를 들어 Premium 계획은 `EP2` 두 개의 코어를 제공 하므로 값으로 시작 하 `2` 고 필요에 따라 최대한 증가 해야 합니다.

### <a name="trigger-configuration"></a>트리거 구성

처리량 및 크기 조정을 계획할 때 여러 유형의 트리거에서 이벤트를 처리 하는 방법을 이해 하는 것이 중요 합니다. 일부 트리거를 사용 하면 일괄 처리 동작을 제어 하 고 동시성을 관리할 수 있습니다. 이러한 옵션의 값을 조정하면 호출된 함수의 요구에 맞게 각 인스턴스의 크기를 조정할 수 있습니다. 이러한 구성 옵션은 함수 앱의 모든 트리거에 적용 되며 응용 프로그램의 호스트. json 파일에서 유지 관리 됩니다. 설정 세부 정보는 특정 트리거 참조의 구성 섹션을 참조 하세요.

함수에서 메시지 스트림을 처리 하는 방법에 대해 자세히 알아보려면 [Azure Functions 신뢰할 수 있는 이벤트 처리](functions-reliable-event-processing.md)를 참조 하세요.

### <a name="plan-for-connections"></a>연결 계획

[소비 계획](consumption-plan.md) 에서 실행 되는 함수 앱에는 연결 제한이 적용 됩니다. 이러한 제한은 인스턴스당 적용 됩니다. 이러한 제한 및 일반적인 모범 사례로 인해 함수 코드에서 아웃 바운드 연결을 최적화 해야 합니다. 자세히 알아보려면 [Azure Functions에서 연결 관리](manage-connections.md)를 참조 하세요. 

### <a name="language-specific-considerations"></a>언어별 고려 사항

원하는 언어의 경우 다음 사항을 고려해 야 합니다.

# <a name="c"></a>[C#](#tab/csharp)

+ [비동기 코드를 사용 하지만 호출을 차단 하지 않습니다](performance-reliability.md#use-async-code-but-avoid-blocking-calls).

+ [취소 토큰을 사용](functions-dotnet-class-library.md?#cancellation-tokens) 합니다 (in-process에만 해당).

# <a name="java"></a>[Java](#tab/java)

+ CPU 바인딩된 작업 및 IO 바인딩 작업을 혼합 한 응용 프로그램의 경우 [더 많은 작업자 프로세스](functions-app-settings.md#functions_worker_process_count)를 사용 하는 것이 좋습니다.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

+ [ `async` 및를 `await` 사용 ](functions-reference-node.md#use-async-and-await)합니다.

+ [CPU 바인딩된 응용 프로그램에 대해 여러 작업자 프로세스를 사용](functions-reference-node.md?tabs=v2#scaling-and-concurrency)합니다.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

+ [동시성 고려 사항을 검토](functions-reference-powershell.md#concurrency)합니다.

# <a name="python"></a>[Python](#tab/python)

+ [Azure Functions에서 Python 앱의 처리량 성능 향상](python-scale-performance-reference.md)

---

## <a name="maximize-availability"></a>가용성 극대화

콜드 부팅은 서버 리스 아키텍처의 주요 고려 사항입니다. 자세히 알아보려면 [콜드 시작](event-driven-scaling.md#cold-start)을 참조 하세요. 시나리오에서 콜드 시작이 중요할 경우 서버 리스 서버에서 서버를 사용 하지 않는 [콜드 시작 이해](https://azure.microsoft.com/blog/understanding-serverless-cold-start/) 를 심층적으로 확인할 수 있습니다. 

Premium 계획은 동적 크기 조정을 유지 하면서 colds를 줄이기 위한 권장 계획입니다. 다음 지침을 사용 하 여 콜드 시작을 줄이고 세 가지 호스팅 계획 모두에서 가용성을 향상할 수 있습니다. 

| 계획 | 지침 |
| --- | --- | 
| **프리미엄 계획** | • [함수 앱에서 준비 트리거 구현](functions-bindings-warmup.md)<br/>• [Always-Ready 인스턴스 및 최대 버스트 제한 값을 설정 합니다](functions-premium-plan.md#plan-and-sku-settings) .<br/>• [가상 네트워크에서 HTTP가 아닌 트리거를 사용 하는 경우 가상 네트워크 트리거 지원 사용](functions-networking-options.md#premium-plan-with-virtual-network-triggers)|
| **전용 계획** | • [Azure App Service 상태 검사를 사용 하도록 설정 하 여 두 개 이상의 인스턴스를 실행](../app-service/monitor-instances-health-check.md) 합니다.<br/>• 자동 크기 조정 [구현](/azure/architecture/best-practices/auto-scaling)|
| **사용 플랜** | • 함수 앱의 크기를 조정 하는 방법에 대 한 한도를 인위적으로 설정 하지 않도록 바인딩 및 트리거에 대 한 단일 패턴 및 동시성 설정의 사용을 검토 합니다.<br/>• [ `functionAppScaleLimit` 확장을 제한할 수 있는 설정을 검토 합니다](event-driven-scaling.md#limit-scale-out) .<br/>• 개발 및 테스트 중에 설정 된 일일 사용 할당량 (GB-초) 제한을 확인 합니다. 프로덕션 환경에서이 한도를 제거 하는 것이 좋습니다. |

## <a name="monitor-effectively"></a>효과적으로 모니터링

Azure Functions은 Azure 애플리케이션 Insights와의 기본 제공 통합을 제공 하 여 코드에서 작성 된 함수 실행 및 추적을 모니터링 합니다. 자세히 알아보려면 [Azure Functions 모니터링](functions-monitoring.md)을 참조하세요. 또한 Azure Monitor는 함수 앱 자체의 상태를 모니터링 하는 기능을 제공 합니다. 자세한 내용은 [Azure Functions에서 Azure Monitor 메트릭 사용](monitor-metrics.md)을 참조하세요.

Application Insights 통합을 사용 하 여 함수를 모니터링 하는 경우 다음 사항을 고려해 야 합니다.

+ [Azurewebjobsdashboard](functions-app-settings.md#azurewebjobsdashboard) 응용 프로그램 설정이 제거 되었는지 확인 합니다. 이 설정은 이전 버전의 함수에서 지원 됩니다. 존재 하는 경우 제거 하면 `AzureWebJobsDashboard` 함수의 성능이 향상 됩니다. 

+  [Application Insights 로그](analyze-telemetry-data.md)를 검토 합니다. 찾으려는 데이터가 누락 된 경우 모니터링 시나리오를 더 잘 캡처하기 위해 샘플링 설정을 조정 하는 것이 좋습니다. 설정을 사용 하 여 `excludedTypes` 또는와 같은 특정 형식을 샘플링에서 제외할 수 있습니다 `Request` `Exception` . 자세한 내용은 [샘플링 구성](configure-monitoring.md?tabs=v2#configure-sampling)을 참조하세요.

Azure Functions를 사용 하 여 [시스템 생성 및 사용자 생성 로그를 Azure Monitor 로그에 보낼](functions-monitor-log-analytics.md)수도 있습니다. Azure Monitor 로그와의 통합은 현재 미리 보기 상태입니다. 

## <a name="build-in-redundancy"></a>중복성으로 빌드

비즈니스 요구 사항에 따라 데이터 센터 가동 중단 중에도 함수를 항상 사용할 수 있어야 할 수 있습니다. 다중 지역 접근 방법을 사용 하 여 중요 한 기능을 항상 실행 상태로 유지 하는 방법을 알아보려면 [지역 재해 복구 및 고가용성을 Azure Functions](functions-geo-disaster-recovery.md)를 참조 하세요.

## <a name="next-steps"></a>다음 단계

[함수 앱 관리](functions-how-to-use-azure-function-app-settings.md)
