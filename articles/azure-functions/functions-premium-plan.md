---
title: Azure Functions 프리미엄 플랜
description: Azure Functions 프리미엄 플랜에 대한 세부 정보 및 구성 옵션(VNet, 콜드 부팅 없음, 무제한 실행 기간).
author: cachai2
ms.topic: conceptual
ms.date: 08/28/2020
ms.author: cachai
ms.custom:
- references_regions
- fasttrack-edit
- devx-track-azurecli
ms.openlocfilehash: c549f29e729eec4403f2943f51e8acb4a9f21b37
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130248396"
---
# <a name="azure-functions-premium-plan"></a>Azure Functions 프리미엄 플랜

Azure Functions Elastic 프리미엄 플랜은 함수 앱을 위한 동적 크기 조정 호스팅 옵션입니다. 다른 호스팅 플랜 옵션에 대해서는 [호스팅 플랜 문서](functions-scale.md)를 확인하세요.

>[!IMPORTANT]
>Azure Functions는 Azure App Service 플랫폼에서 실행됩니다. App Service 플랫폼에서 프리미엄 플랜 함수 앱을 호스팅하는 플랜은 *탄력적* 프리미엄 플랜이라고 하며 SKU 이름은 `EP1`과 같습니다. 프리미엄 플랜에서 함수 앱을 실행하기로 선택한 경우 `EP1`과 같이 "E"로 시작하는 SKU 이름으로 플랜을 만들어야 합니다. `P1V2`(프리미엄 V2 소형 플랜)과 같이 "P"로 시작하는 App Service 플랜 SKU 이름은 실제로 [전용 호스팅 계획](dedicated-plan.md)입니다. 탄력적 프리미엄이 아닌 전용이기 때문에 SKU 이름이 "P"로 시작하는 플랜은 동적으로 크기 조정되지 않으며 비용이 증가할 수 있습니다. 

프리미엄 플랜 호스팅은 함수에 다음과 같은 이점을 제공합니다.

* 영구적 웜 인스턴스로 콜드 부팅 방지
* 가상 네트워크 연결.
* 무제한 실행 기간, 60분 보장.
* 프리미엄 인스턴스 크기: 1코어, 2코어 및 4코어 인스턴스.
* 사용 플랜보다 더 예측 가능한 가격 책정.
* 여러 함수 앱을 사용하는 플랜을 위한 고밀도 앱 할당.

프리미엄 플랜을 사용하는 경우 [사용 플랜](consumption-plan.md)과 마찬가지로 Azure Functions 호스트의 인스턴스는 들어오는 이벤트의 수에 따라 추가되고 제거됩니다. 여러 함수 앱을 동일한 프리미엄 플랜에 배포할 수 있으며, 이 플랜을 통해 계산 인스턴스 크기, 기본 플랜 크기 및 최대 플랜 크기를 구성할 수 있습니다. 

## <a name="billing"></a>청구

프리미엄 플랜에 대한 청구는 코어 초 수와 여러 인스턴스에 할당된 메모리를 기준으로 합니다. 이 청구는 실행 및 사용된 메모리당 청구되는 사용 플랜과는 다릅니다. 프리미엄 플랜으로 실행 요금이 부과되지 않습니다. 플랜당 항상 하나 이상의 인스턴스를 할당해야 합니다. 이 청구로 인해 함수가 활성 상태인지 유휴 상태인지 관계없이 활성 플랜당 월별 최소 비용이 발생합니다. 프리미엄 플랜의 모든 함수 앱은 할당된 인스턴스를 공유합니다. 자세한 내용은 [Azure Functions 가격 책정 페이지](https://azure.microsoft.com/pricing/details/functions/)를 참조하세요.

## <a name="create-a-premium-plan"></a>프리미엄 플랜 만들기

Azure Portal에서 함수 앱을 만들 때는 사용 플랜이 기본값입니다. 프리미엄 플랜에서 실행되는 함수 앱을 만들려면 _탄력적 프리미엄_ SKU 중 하나를 사용하여 App Service 요금제를 명시적으로 만들어야 합니다. 만든 함수 앱은 이 플랜에서 호스트됩니다. Azure Portal을 사용하면 프리미엄 플랜과 함수 앱을 동시에 간편하게 만들 수 있습니다. 동일한 프리미엄 플랜에서 둘 이상의 함수 앱을 실행할 수 있지만 둘 다 동일한 운영 체제(Windows 또는 Linux)에서 실행되어야 합니다. 

다음 문서에서는 프로그래밍 방식으로 또는 Azure Portal에서 프리미엄 플랜을 사용하여 함수 앱을 만드는 방법을 보여줍니다.

+ [Azure Portal](create-premium-plan-function-app-portal.md)
+ [Azure CLI](scripts/functions-cli-create-premium-plan.md)
+ [Azure Resource Manager 템플릿](functions-infrastructure-as-code.md#deploy-on-premium-plan)

## <a name="eliminate-cold-starts"></a>콜드 부팅 제거

사용 플랜에서 이벤트 또는 실행이 발생하지 않으면 앱이 0개의 인스턴스로 확장될 수 있습니다. 새 이벤트가 발생하면 실행되는 앱의 새 인스턴스를 특수화해야 합니다. 앱에 따라 새 인스턴스를 특수화하는 데 시간이 걸릴 수 있습니다. 첫 번째 호출에서 발생하는 이러한 추가 대기 시간은 종종 앱 _콜드 부팅_ 이라고 합니다.

프리미엄 플랜은 함수에서 콜드 부팅을 효과적으로 제거하기 위해 함께 작동하는 두 가지 기능 즉, _상시 준비 인스턴스_ 및 _사전 준비 인스턴스_ 를 제공합니다. 

### <a name="always-ready-instances"></a>상시 준비 인스턴스

프리미엄 플랜에서는 지정된 수의 인스턴스에서 앱이 상시 준비되도록 할 수 있습니다. 상시 준비 인스턴스의 최대 수는 20입니다. 이벤트가 애플리케이션 트리거를 시작하면 먼저 상시 준비 인스턴스로 라우팅됩니다. 함수가 활성화되면 추가 인스턴스가 버퍼로서 준비됩니다. 이 버퍼는 크기 조정 중에 필요한 새 인스턴스의 콜드 부팅을 방지합니다. 이러한 버퍼링된 인스턴스를 [사전 준비 인스턴스](#pre-warmed-instances)라고 합니다. 상시 준비 인스턴스와 사전 준비 버퍼를 조합하여 앱에서 콜드 부팅을 효과적으로 제거할 수 있습니다.

> [!NOTE]
> 모든 프리미엄 플랜에는 항상 하나 이상의 활성(청구) 인스턴스가 있습니다.

# <a name="portal"></a>[포털](#tab/portal)

**함수 앱** 을 선택하고 **플랫폼 기능** 탭으로 이동한 다음, **스케일 아웃** 옵션을 선택하여 Azure Portal에서 상시 준비 인스턴스 수를 구성할 수 있습니다. 함수 앱 편집 창에서 상시 준비 인스턴스는 해당 앱에만 적용됩니다.

![탄력적 크기 조정 설정](./media/functions-premium-plan/scale-out.png)

# <a name="azure-cli"></a>[Azure CLI](#tab/azurecli)

Azure CLI를 사용하여 앱에 대해 상시 준비 인스턴스를 구성할 수도 있습니다.

```azurecli-interactive
az resource update -g <resource_group> -n <function_app_name>/config/web --set properties.minimumElasticInstanceCount=<desired_always_ready_count> --resource-type Microsoft.Web/sites
```
---

### <a name="pre-warmed-instances"></a>사전 준비 인스턴스

사전 준비 인스턴스는 크기 스케일링 및 활성화 이벤트 중에 버퍼로서 준비된 인스턴스입니다. 사전 준비 인스턴스는 최대 스케일 아웃 한도에 도달할 때까지 계속 버퍼링됩니다. 기본 사전 준비 인스턴스 수는 1이며, 대부분의 시나리오에서 이 값은 1로 유지되어야 합니다.

긴 예열이 필요한 앱의 경우(예: 사용자 지정 컨테이너 이미지) 이 버퍼를 늘려야 할 수 있습니다. 사전 준비 인스턴스는 모든 활성 인스턴스를 충분히 사용한 후에만 활성화됩니다.

상시 준비 인스턴스와 사전 준비 인스턴스가 함께 작동하는 방법의 예를 살펴보겠습니다. 프리미엄 함수 앱에는 5개의 상시 준비 인스턴스가 구성되어 있으며 기본적으로 1개의 사전 준비 인스턴스가 있습니다. 앱이 유휴 상태이고 트리거되는 이벤트가 없으면 앱이 프로비전되고 5개의 인스턴스로 실행됩니다. 현재는 상시 준비 인스턴스가 사용되지 않고 사전 준비 인스턴스가 할당되지 않으므로 사전 준비 인스턴스에 대한 요금이 청구되지 않습니다.

첫 번째 트리거가 발생하는 즉시, 5개의 상시 준비 인스턴스가 활성화되고 사전 준비 인스턴스가 할당됩니다. 이제 앱은 프로비전된 인스턴스 6개, 현재 활성 상태인 상시 준비 인스턴스 5개, 사전 준비 및 비활성 버퍼 6개로 실행됩니다. 실행 속도가 계속 증가하면 결국 5개의 활성 인스턴스가 사용됩니다. 플랫폼이 인스턴스 5개 이상으로 확장하기로 한 경우 사전 준비 인스턴스로 확장됩니다. 이러한 상황이 발생하면, 이제 6개의 활성 인스턴스가 사용되고 7번째 인스턴스가 즉시 프로비전되고 사전 준비 버퍼를 채웁니다. 이 확장 및 사전 준비 순서는 앱의 최대 인스턴스 수에 도달할 때까지 계속됩니다. 인스턴스는 사전 준비되거나 최댓값 이상으로 활성화되지 않습니다.

Azure CLI를 사용하여 앱에 대해 사전 준비 인스턴스 수를 수정할 수 있습니다.

```azurecli-interactive
az resource update -g <resource_group> -n <function_app_name>/config/web --set properties.preWarmedInstanceCount=<desired_prewarmed_count> --resource-type Microsoft.Web/sites
```

### <a name="maximum-function-app-instances"></a>최대 함수 앱 인스턴스

[플랜 최대 인스턴스 수](#plan-and-sku-settings) 외에 앱별 최댓값을 구성할 수 있습니다. 앱 최댓값은 [앱 확장 제한](./event-driven-scaling.md#limit-scale-out)을 사용하여 구성할 수 있습니다.

## <a name="private-network-connectivity"></a>프라이빗 네트워크 연결

프리미엄 플랜에 배포된 함수 앱은 [웹앱용 VNet 통합](../app-service/overview-vnet-integration.md)을 활용할 수 있습니다. 구성된 앱은 VNet 내의 리소스와 통신하거나 서비스 엔드포인트를 통해 보호할 수 있습니다. 들어오는 트래픽을 제한하기 위해 앱에서 IP 제한을 사용할 수도 있습니다.

프리미엄 플랜에서 함수 앱에 서브넷을 할당할 때 각 잠재적 인스턴스에 대해 충분한 IP 주소가 있는 서브넷이 필요합니다. 사용 가능한 주소가 100개 이상인 IP 블록이 필요합니다.

자세한 내용은 [VNet과 함수 앱 통합](functions-create-vnet.md)을 참조하세요.

## <a name="rapid-elastic-scale"></a>신속한 탄력적 확장

사용 플랜과 동일한 빠른 확장 논리를 사용하여 앱에 대한 추가 컴퓨팅 인스턴스가 자동으로 추가됩니다. 동일한 App Service 플랜의 앱은 개별 앱의 요구 사항에 따라 서로 독립적으로 확장됩니다. 그러나 가능한 경우 동일한 App Service 플랜의 함수 앱은 VM 리소스를 공유하여 비용을 절감합니다. VM과 연결된 앱의 수는 각 앱의 메모리 공간과 VM의 크기에 따라 다릅니다.

확장 작동 방식에 대한 자세한 내용은 [Azure Functions의 이벤트 기반 확장](event-driven-scaling.md)을 참조하세요.

## <a name="longer-run-duration"></a>더 긴 실행 지속 시간

사용 플랜의 Azure Functions는 한 번의 실행당 10분으로 제한됩니다. 프리미엄 플랜에서 런어웨이 실행을 방지하기 위해 실행 지속 시간은 기본적으로 30분입니다. 그러나 [host.json 구성을 수정](./functions-host-json.md#functiontimeout)하여 프리미엄 플랜 앱의 지속 시간을 무제한으로 설정할 수 있습니다. 무제한 지속 시간으로 설정하면 함수 앱이 최소 60분 동안 실행되도록 보장됩니다. 

## <a name="plan-and-sku-settings"></a>플랜 및 SKU 설정

플랜을 만들 때, 최소 인스턴스 수(또는 플랜 크기)와 최대 버스트 제한이라는 두 가지 플랜 크기 설정이 있습니다.

앱에서 상시 준비 인스턴스를 초과하는 인스턴스가 필요한 경우 인스턴스 수가 최대 버스트 제한에 도달할 때까지 계속 스케일 아웃할 수 있습니다. 인스턴스가 초 기준으로 실행되고 사용자에게 할당된 경우 플랜 크기를 초과하는 인스턴스에 대해서만 청구됩니다. 플랫폼을 사용하면 정의된 최대 한도까지 애플리케이션을 확장하는 것이 가장 좋습니다.

플랜 또는 해당 플랜에 배포된 함수 앱(**플랫폼 기능** 아래)에서 **스케일 아웃** 옵션을 선택하여 Azure Portal에서 플랜 크기 및 최댓값을 구성할 수 있습니다.

Azure CLI에서 최대 버스트 제한을 늘릴 수도 있습니다.

```azurecli-interactive
az functionapp plan update -g <resource_group> -n <premium_plan_name> --max-burst <desired_max_burst>
```

모든 플랜의 최솟값은 하나 이상의 인스턴스가 됩니다. 실제 최소 인스턴스 수는 플랜의 앱에서 요청한 상시 준비 인스턴스를 기반으로 자동 구성됩니다. 예를 들어 동일 플랜에서 앱 A가 5개의 상시 준비 인스턴스를 요청하고 앱 B가 2개의 상시 준비 인스턴스를 요청하는 경우 최소 플랜 크기는 5로 계산됩니다. 앱 A는 5개 모두에서 실행되고 앱 B는 2개에서만 실행됩니다.

> [!IMPORTANT]
> 함수 실행 여부와 관계없이 최소 인스턴스 수로 할당된 각 인스턴스에 대해 요금이 청구됩니다.

대부분의 경우 이 자동 계산된 최솟값으로 충분합니다. 그러나 최솟값 이상으로 확장하는 것이 좋습니다. 드물지만, 추가 인스턴스를 사용할 수 없는 경우 특정 시간 스케일 아웃이 지연될 수 있습니다. 최솟값을 자동 계 된 최솟값보다 높게 설정하여 스케일 아웃 전에 인스턴스를 예약합니다.

Azure CLI를 사용하여 플랜의 계산된 최솟값을 늘릴 수 있습니다.

```azurecli-interactive
az functionapp plan update -g <resource_group> -n <premium_plan_name> --min-instances <desired_min_instances>
```

### <a name="available-instance-skus"></a>사용 가능한 인스턴스 SKU

플랜을 만들거나 확장할 때 세 가지 인스턴스 크기 중에서 선택할 수 있습니다. 프로비전된 코어 수와 메모리의 총 개수에 대해 요금이 청구되며, 이는 각 인스턴스가 사용자에게 할당되는 시간(초) 기준입니다. 필요에 따라 앱이 여러 인스턴스로 자동 스케일 아웃될 수 있습니다.

|SKU|코어|메모리|스토리지|
|--|--|--|--|
|EP1|1|3.5GB|250GB|
|EP2|2|7GB|250GB|
|EP3|4|14GB|250GB|

### <a name="memory-usage-considerations"></a>메모리 사용량 고려 사항

메모리가 더 많은 컴퓨터에서 실행한다고 해서 함수 앱이 항상 사용 가능한 메모리를 모두 사용하는 것은 아닙니다.

예를 들어 JavaScript 함수 앱은 Node.js의 기본 메모리 제한에 의해 제한됩니다. 이 고정 메모리 제한을 늘리려면 `--max-old-space-size=<max memory in MB>` 값으로 앱 설정 `languageWorkers:node:arguments`를 추가합니다.

메모리가 4GB 넘는 플랜의 경우 [일반 설정](../app-service/configure-common.md#configure-general-settings)에서 비트 수 플랫폼 설정이 `64 Bit`로 설정되어 있는지 확인합니다.

## <a name="region-max-scale-out"></a>지역 최대 스케일 아웃

다음은 각 지역 및 OS 구성에서 단일 플랜에 대해 현재 지원되는 최대 스케일 아웃 값입니다.

[Azure 웹 사이트](https://azure.microsoft.com/global-infrastructure/services/?products=functions)에서 함수의 전체 지역 가용성을 참조하세요.

|지역| Windows | Linux |
|--| -- | -- |
|오스트레일리아 중부| 100 | 사용할 수 없음 |
|오스트레일리아 중부 2| 100 | 사용할 수 없음 |
|오스트레일리아 동부| 100 | 20 |
|오스트레일리아 남동부 | 100 | 20 |
|브라질 남부| 100 | 20 |
|캐나다 중부| 100 | 20 |
|인도 중부| 100 | 20 |
|미국 중부| 100 | 20 |
|중국 동부 2| 100 | 20 |
|중국 북부 2| 100 | 20 |
|동아시아| 100 | 20 |
|미국 동부 | 100 | 40 |
|미국 동부 2| 100 | 20 |
|프랑스 중부| 100 | 20 |
|독일 중서부| 100 | 20 |
|일본 동부| 100 | 20 |
|일본 서부| 100 | 20 |
|JIO 인도 서부| 100 | 20 |
|한국 중부| 100 | 20 |
|한국 남부| 사용할 수 없음 | 20 |
|미국 중북부| 100 | 20 |
|북유럽| 100 | 20 |
|노르웨이 동부| 100 | 20 |
|남아프리카 북부| 100 | 20 |
|미국 중남부| 100 | 20 |
|인도 남부 | 100 | 사용할 수 없음 |
|동남 아시아| 100 | 20 |
|스위스 북부| 100 | 20 |
|스위스 서부| 100 | 20 |
|아랍에미리트 북부| 100 | 20 |
|영국 남부| 100 | 20 |
|영국 서부| 100 | 20 |
|USGov 애리조나| 100 | 20 |
|USGov 텍사스| 100 | 사용할 수 없음 |
|USGov 버지니아| 100 | 20 |
|USNat 동부| 100 | 사용할 수 없음 |
|USNat 서부| 100 | 사용할 수 없음 |
|USSec 동부| 100 | 20 |
|USSec 서부| 100 | 20 |
|미국 중서부| 100 | 20 |
|서유럽| 100 | 20 |
|인도 서부| 100 | 20 |
|미국 서부| 100 | 20 |
|미국 서부 2| 100 | 20 |
|미국 서부 3| 100 | 20 |

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [Azure Functions 호스팅 옵션 이해](functions-scale.md)