---
title: Azure Functions 크기 조정 및 호스팅
description: Azure Functions 소비 계획과 프리미엄 계획 중 하나를 선택하는 방법을 알아봅니다.
ms.assetid: 5b63649c-ec7f-4564-b168-e0a74cb7e0f3
ms.topic: conceptual
ms.date: 08/17/2020
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: a2c760e4b1ca923496d11002f2e30b72672f32df
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124778403"
---
# <a name="azure-functions-hosting-options"></a>Azure Functions 호스팅 옵션

Azure에서 함수 앱을 만들 때는 앱의 호스팅 계획을 선택해야 합니다. Azure Functions에 대해 사용 가능한 세 가지 기본 호스팅 계획으로는 [소비 계획](consumption-plan.md), [프리미엄 계획](functions-premium-plan.md) 및 [전용(App Service) 계획](dedicated-plan.md)을 꼽을 수 있습니다. 모든 호스팅 계획은 Linux 및 Windows 가상 머신 모두에서 GA(일반 제공) 대상입니다.

선택한 호스팅 계획에 따라 다음 동작이 결정됩니다.

* 함수 앱의 크기 조정 방법
* 각 함수 앱 인스턴스에 사용 가능한 리소스
* Azure Virtual Network 연결 등의 고급 기능 지원

이 문서에서는 [Kubernetes 기반 호스팅](functions-kubernetes-keda.md)과 함께 다양한 호스팅 계획 간의 자세한 비교를 제공합니다.

> [!NOTE]
> Kubernetes 클러스터에서 함수를 호스트하도록 선택하는 경우 [Azure Arc 지원 Kubernetes 클러스터](../azure-arc/kubernetes/overview.md)를 사용하는 것이 좋습니다. Azure Arc 지원 Kubernetes 클러스터에서 호스팅은 현재 미리 보기로 제공됩니다. 자세한 내용은 [Azure Arc의 App Service, Functions 및 Logic Arc](../app-service/overview-arc-integration.md)를 참조하세요.  

## <a name="overview-of-plans"></a>계획 개요

다음은 Functions의 세 가지 주요 호스팅 계획이 가지는 이점에 대한 요약입니다.

| 계획 | 이점 |
| --- | --- |  
|**[소비 계획](consumption-plan.md)**| 크기를 자동으로 조정하고 함수가 실행 중일 때만 컴퓨팅 리소스에 대한 비용을 지불합니다.<br/><br/>소비 계획에서 Functions 호스트의 인스턴스는 들어오는 이벤트의 수에 따라 동적으로 추가되고 제거됩니다.<br/><br/> ✔ 기본 호스팅 계획<br/>✔ 함수가 실행되는 경우에만 지불<br/>✔ 높은 부하 기간 동안에도 자동으로 크기 조정|  
|**[프리미엄 계획](functions-premium-plan.md)**|유휴 상태 후 지연 없이 애플리케이션을 실행하고, 더 강력한 인스턴스에서 실행되고, 가상 네트워크에 연결하는 사전 준비가 된 작업자를 사용하여 수요에 따라 크기가 자동으로 조정됩니다. <br/><br/>다음과 같은 상황에서 Azure Functions 프리미엄 계획을 고려합니다. <br/><br/>✔ 함수 앱을 계속해서 또는 거의 계속해서 실행<br/>✔ 소형 실행 수도 많고 실행 요금도 높지만 소비 계획에서 GB 초는 낮음<br/>✔ 소비 계획에서 제공하는 것보다 많은 CPU 또는 메모리 옵션이 필요함<br/>✔ 코드가 소비 계획에 허용된 최대 실행 시간보다 오래 실행해야 함<br/>✔ 가상 네트워크 연결처럼 소비 계획에서 사용할 수 없는 기능이 필요함<br/>✔ 함수를 실행할 사용자 지정 Linux 이미지를 제공하려고 합니다. |  
|**[전용 계획](dedicated-plan.md)** |App Service 계획 내에서 함수를 정기적인 [App Service 계획 요금](https://azure.microsoft.com/pricing/details/app-service/windows/)으로 실행합니다.<br/><br/>[Durable Functions](durable/durable-functions-overview.md)를 사용할 수 없는 장기 실행 시나리오에 적합합니다. 다음 상황에서 App Service 플랜을 고려합니다.<br/><br/>✔ 이미 다른 App Service 인스턴스를 실행하고 있는 기존의 활용도가 낮은 VM이 있음<br/>✔ 예측 크기 조정 및 비용이 필요함|  

이 문서의 비교 테이블에는 다음과 같이 가장 많은 양의 컨트롤과 격리를 제공하여 그 안에서 함수 앱을 실행할 수 있는 호스팅 옵션도 포함되어 있습니다.  

| 호스팅 옵션 | 세부 정보 |
| --- | --- |  
|**[ASE](dedicated-plan.md)** | ASE(Azure App Service Environment)는 App Service 앱을 높은 확장성으로 안전하게 실행하기 위해 완전히 격리된 전용 환경을 제공하는 App Service 기능입니다.<br/><br/>ASE는 다음을 필요로 하는 애플리케이션 워크로드에 적합합니다. <br/><br/>✔ 매우 높은 확장성<br/>✔ 완전 컴퓨팅 격리 및 보안된 네트워크 액세스<br/>✔ 높은 메모리 사용량|  
| **Kubernetes**<br/>([직접](functions-kubernetes-keda.md) 또는<br/>[Azure Arc](../app-service/overview-arc-integration.md)) | Kubernetes는 Kubernetes 플랫폼을 기반으로 실행되는 완전히 격리된 전용 환경을 제공합니다.<br/><br/> Kubernetes는 다음을 필요로 하는 애플리케이션 워크로드에 적합합니다. <br/>✔ 사용자 지정 하드웨어 요구 사항<br/>✔ 격리 및 보안된 네트워크 액세스<br/>✔ 하이브리드 또는 다중 클라우드 환경에서 실행하는 기능<br/>✔ 기존 Kubernetes 애플리케이션 및 서비스와 함께 실행|  

이 문서의 나머지 테이블에서는 다양한 기능과 동작에 대한 계획을 비교합니다. 동적 호스팅 계획(소비 및 프리미엄) 간의 가격 비교에 대한 내용은 [Azure Functions 가격 책정 페이지](https://azure.microsoft.com/pricing/details/functions/)를 참조하세요. 다양한 전용 계획 옵션의 가격 책정에 대한 내용은 [App Service 가격 책정 페이지](https://azure.microsoft.com/pricing/details/app-service/windows/)를 참조하세요. 

## <a name="operating-systemruntime"></a>운영 체제/런타임

다음 테이블에서는 호스팅 계획에 대해 지원되는 운영 체제 및 언어 런타임 지원을 보여줍니다.

| | Linux<sup>1</sup><br/>코드 전용 | Windows<sup>2</sup><br/>코드 전용 | Linux<sup>1,3</sup><br/>Docker 컨테이너 |
| --- | --- | --- | --- |
| **[소비 계획](consumption-plan.md)** | .NET Core<br/>Node.js<br/>Java<br/>Python | .NET Core<br/>Node.js<br/>Java<br/>PowerShell Core | 지원되지 않음  |
| **[프리미엄 계획](functions-premium-plan.md)** | .NET Core<br/>Node.js<br/>Java<br/>Python|.NET Core<br/>Node.js<br/>Java<br/>PowerShell Core |.NET Core<br/>Node.js<br/>Java<br/>PowerShell Core<br/>Python  | 
| **[전용 계획](dedicated-plan.md)** | .NET Core<br/>Node.js<br/>Java<br/>Python|.NET Core<br/>Node.js<br/>Java<br/>PowerShell Core |.NET Core<br/>Node.js<br/>Java<br/>PowerShell Core<br/>Python |
| **[ASE](dedicated-plan.md)** | .NET Core<br/>Node.js<br/>Java<br/>Python |.NET Core<br/>Node.js<br/>Java<br/>PowerShell Core  |.NET Core<br/>Node.js<br/>Java<br/>PowerShell Core<br/>Python | 
| **[Kubernetes(직접)](functions-kubernetes-keda.md)** | 해당 없음 | 해당 없음 |.NET Core<br/>Node.js<br/>Java<br/>PowerShell Core<br/>Python |
| **[Azure Arc(미리 보기)](../app-service/overview-arc-integration.md)** | .NET Core<br/>Node.js<br/>Java<br/>Python | 해당 없음 |.NET Core<br/>Node.js<br/>Java<br/>PowerShell Core<br/>Python |

<sup>1</sup> Linux는 Python 런타임 스택에 대해 지원되는 유일한 운영 체제입니다. <br/>
<sup>2</sup> Windows는 PowerShell 런타임 스택에 대해 지원되는 유일한 운영 체제입니다.<br/>
<sup>3</sup> Linux는 Docker 컨테이너에 대해 지원되는 유일한 운영 체제입니다.<br/>

[!INCLUDE [Timeout Duration section](../../includes/functions-timeout-duration.md)]

## <a name="scale"></a>확장

다음 테이블에서는 다양한 호스팅 계획의 크기 조정 동작을 비교합니다.

| 계획 | 확장 | 최대 인스턴스 수 |
| --- | --- | --- |
| **[소비 계획](consumption-plan.md)** | [이벤트 기반](event-driven-scaling.md) 높은 부하 기간 동안에도 자동으로 규모 확장 Azure Functions 인프라는 들어오는 트리거 이벤트의 수를 기반으로 Functions 호스트의 추가 인스턴스를 추가하여 CPU 및 메모리 리소스의 크기를 조정합니다. | 200 |
| **[프리미엄 계획](functions-premium-plan.md)** | [이벤트 기반](event-driven-scaling.md) 높은 부하 기간 동안에도 자동으로 규모 확장 Azure Functions 인프라는 해당 함수가 트리거되는 이벤트의 수를 기반으로 Functions 호스트의 추가 인스턴스를 추가하여 CPU 및 메모리 리소스의 크기를 조정합니다. |100|
| **[전용 계획](dedicated-plan.md)** <sup>1</sup> | 크기 수동/자동 조정 |10-20|
| **[ASE](dedicated-plan.md)** <sup>1</sup> | 크기 수동/자동 조정 |100 |
| **[Kubernetes](functions-kubernetes-keda.md)**  | [KEDA를](https://keda.sh)사용하여 Kubernetes 클러스터에 대한 이벤트 기반 자동 크기 조정 | 클러스터에&nbsp;따라&nbsp;다름&nbsp;&nbsp;|

<sup>1</sup> 다양한 App Service 계획 옵션에 대한 특정 제한은 [App Service 계획 제한](../azure-resource-manager/management/azure-subscription-service-limits.md#app-service-limits)을 참조하세요.

## <a name="cold-start-behavior"></a>콜드 부팅 동작

| 계획 | 세부 정보 | 
| -- | -- |
| **[소비&nbsp;계획](consumption-plan.md)** | 유휴 상태일 때 앱의 크기를 0으로 조정할 수 있습니다. 즉, 일부 요청은 시작 시 추가 대기 시간이 있을 수 있습니다.  소비 계획에는 이미 함수 호스트 및 언어 프로세스가 실행 중인 사전 준비된 자리 표시자 함수에서 끌어오는 것을 포함해 콜드 부팅 시간을 줄이는 데 도움이 되는 몇 가지 최적화가 있습니다. |
| **[프리미엄 계획](functions-premium-plan.md)** | 영구적 사전 준비 인스턴스로 콜드 부팅 방지 |
| **[전용 계획](dedicated-plan.md)** | 전용 계획에서 실행하는 경우 Functions 호스트를 지속적으로 실행할 수 있습니다. 즉, 콜드 부팅은 실제로 문제가 되지 않습니다. |
| **[ASE](dedicated-plan.md)** | 전용 계획에서 실행하는 경우 Functions 호스트를 지속적으로 실행할 수 있습니다. 즉, 콜드 부팅은 실제로 문제가 되지 않습니다. |
| **[Kubernetes](functions-kubernetes-keda.md)**  | KEDA 구성에 따라 콜드 부팅을 방지하도록 앱을 구성할 수 있습니다. 크기가 0으로 조정되도록 구성된 경우 새 이벤트에 대해 콜드 부팅이 발생합니다. 

## <a name="service-limits"></a>서비스 제한

[!INCLUDE [functions-limits](../../includes/functions-limits.md)]

## <a name="networking-features"></a>네트워킹 기능

[!INCLUDE [functions-networking-features](../../includes/functions-networking-features.md)]

## <a name="billing"></a>결제

| 계획 | 세부 정보 |
| --- | --- |
| **[소비 계획](consumption-plan.md)** | 함수가 실행되는 시간 만큼만 요금을 지불하면 됩니다. 청구는 실행 횟수, 실행 시간 및 사용된 메모리를 기반으로 하며, |
| **[프리미엄 계획](functions-premium-plan.md)** | 프리미엄 계획은 필요하고 사전 준비된 인스턴스에서 사용되는 메모리와 코어 초 수에 기반합니다. 계획당 최소 한 개의 인스턴스는 항상 사전 준비된 상태로 유지되어야 합니다. 이 계획은 가장 예측 가능한 가격 책정을 제공합니다. |
| **[전용 계획](dedicated-plan.md)** | 웹앱과 같은 다른 App Service 리소스에서와 마찬가지로 App Service 계획에서 함수 앱에 대해 동일한 비용을 지불합니다.|
| **[ASE(App Service Environment)](dedicated-plan.md)** | 인프라에 대해 대금을 지급하고 ASE 크기에 따라 변경되지 않는 ASE의 월정액이 있습니다. 또한 App Service 계획 vCPU당 비용이 발생합니다. ASE에 호스트되는 모든 앱은 격리 가격 책정 SKU에 해당합니다. |
| **[Kubernetes](functions-kubernetes-keda.md)**| Kubernetes 클러스터의 요금만 지불하면 되며 Functions에 대한 추가 요금 청구는 없습니다. 함수 앱은 일반 앱처럼 클러스터 기반 애플리케이션 워크로드로 실행됩니다. |

## <a name="next-steps"></a>다음 단계

+ [Azure Functions의 배포 기술](functions-deployment-technologies.md) 
+ [Azure Functions 개발자 가이드](functions-reference.md)
