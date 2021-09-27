---
title: 공용 다중 테넌트 App Service 대한 가용성 영역 지원
description: 앱이 영역 중복되도록 App Service 배포하는 방법을 알아봅니다.
author: seligj95
ms.topic: article
ms.date: 09/01/2021
ms.author: jordanselig
ms.custom: references_regions
ms.openlocfilehash: 1fb6959ab6e47ad631ea3cac208a3dc52ca41e41
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124832715"
---
# <a name="availability-zone-support-for-public-multi-tenant-app-service"></a>공용 다중 테넌트 App Service 대한 가용성 영역 지원

Microsoft Azure App Service 앱에 [고가용성을](https://en.wikipedia.org/wiki/High_availability) 가능하게 하는 [AZ(가용성 영역)에](../availability-zones/az-overview.md) 배포할 수 있습니다. 이 아키텍처를 영역 중복이라고도 합니다.

앱은 ASP(App Service 계획)에 있으며 App Service 계획은 단일 배율 단위로 존재합니다. App Service 영역 중복으로 구성된 경우 플랫폼은 선택한 지역의 세 영역 모두에 App Service 계획의 VM 인스턴스를 자동으로 분산합니다. 3보다 큰 용량을 지정하고 인스턴스 수를 3으로 나눌 경우 인스턴스가 균등하게 분산됩니다. 그렇지 않으면 3*N을 초과하는 인스턴스 수는 나머지 하나 또는 두 개의 영역에 분산됩니다.

## <a name="requirements"></a>요구 사항

영역 중복성은 App Service 계획의 속성입니다. 다음은 영역 중복을 사용하도록 설정하기 위한 현재 요구 사항/제한 사항입니다.

- Windows 및 Linux가 모두 지원됩니다.
- **Premium v2** 또는 **Premium v3** App Service 계획이 필요합니다.
- 최소 인스턴스 수 3개
  - 3개 미만의 인스턴스 수를 지정하는 경우 플랫폼은 백그라운드에서 이 최소 개수를 적용합니다.
- 다음 지역 중에서 사용하도록 설정할 수 있습니다.
  - 미국 서부 2
  - 미국 서부 3
  - 미국 중부
  - 미국 동부
  - 미국 동부 2
  - 캐나다 중부
  - 브라질 남부
  - 북유럽
  - 서유럽
  - 독일 중서부
  - 프랑스 중부
  - 영국 남부
  - 일본 동부
  - 동남아시아
  - 오스트레일리아 동부
- 영역 중복성은 **새** App Service 계획을 만들 때만 지정할 수 있습니다.
  - 현재는 기존 App Service 계획을 변환할 수 없습니다. 영역 중복성을 지원하는 새 App Service 계획을 만드는 방법에 대한 자세한 내용은 다음 글머리 기호를 참조하세요.
- 영역 중복성은 App Service 공간의 최신 부분에서만 지원됩니다.
  - 현재 Pv3에서 실행 중인 경우 영역 중복성을 지원하는 공간이 이미 있을 수 있습니다. 이 시나리오에서는 새 App Service 계획을 만들고 새 App Service 계획을 만들 때 영역 중복성을 지정할 수 있습니다.
  - Pv3 또는 영역 중복을 지원하는 배율 단위를 사용하지 않거나, 지원되지 않는 지역에 있거나, 확실하지 않은 경우 아래 단계를 수행합니다.
    - 지원되는 지역에 새 리소스 그룹 만들기
        - 이렇게 하면 App Service 제어 평면이 영역 중복성을 지원하는 선택한 지역에서 배율 단위를 찾을 수 있습니다.
    - 새 **리소스** 그룹을 사용하여 선택한 지역에 새 App Service 계획(및 앱) 만들기
    - 새 App Service 계획을 만들 때 zoneRedundant 속성(아래에 설명)이 true로 설정되어 있는지 확인합니다.
- [ARM(Azure Resource Manager) 템플릿을](../azure-resource-manager/templates/overview.md) 사용하여 만들어야 합니다.

영역이 중단되면 App Service 플랫폼에서 손실된 인스턴스를 감지하고 자동으로 새 대체 인스턴스를 찾으려고 시도합니다. 또한 자동 크기 조정을 구성하고 더 많은 인스턴스가 필요하다고 결정한 경우 자동 크기 조정은 더 많은 인스턴스를 추가하도록 App Service 요청합니다(자동 크기 조정 동작은 App Service 플랫폼 동작과 독립적임). 손실된 인스턴스를 다시 채우기 위해 최선을 다해 발생하기 때문에 영역 축소 시나리오에서 추가 인스턴스에 대한 요청이 성공한다는 보장은 없습니다. 권장되는 해결 방법은 이 문서의 다음 섹션에 설명된 대로 영역 손실을 고려하도록 App Service 계획을 프로비전하는 것입니다.

영역 중복성을 사용하도록 설정된 App Service 계획에 배포된 애플리케이션은 동일한 지역의 다른 영역이 중단되더라도 계속 실행되고 트래픽을 제공합니다. 그러나 App Service 계획 크기 조정, 애플리케이션 만들기, 애플리케이션 구성 및 애플리케이션 게시를 포함한 비 런타임 동작은 다른 가용성 영역 중단으로 인한 영향을 받을 수 있습니다. App Service 계획의 영역 중복성은 배포된 애플리케이션에 대한 지속적인 작동 시간만 보장합니다.

App Service 플랫폼이 영역 중복 App Service 계획에 인스턴스를 할당하는 경우 [기본 Azure Virtual Machine Scale Sets 에서 제공하는 최상의 작업 영역 분산을](../virtual-machine-scale-sets/virtual-machine-scale-sets-use-availability-zones.md#zone-balancing)사용합니다. 각 영역에 동일한 수의 VM이 있거나 App Service 계획에서 사용하는 다른 모든 영역에 +/- 1개의 VM이 있는 경우 App Service 계획이 "균형"으로 조정됩니다.

## <a name="how-to-deploy-a-zone-redundant-app-service"></a>영역 중복 App Service 배포하는 방법

현재 ARM 템플릿을 사용하여 영역 중복 App Service 만들어야 합니다. ARM 템플릿을 통해 만든 App Service 계획은 Azure Portal 및 CLI 도구를 통해 보고 상호 작용할 수 있습니다. ARM 템플릿은 App Service 계획의 초기 생성에만 필요합니다.

영역 중복 App Service 지정하기 위해 ARM 템플릿에 필요한 유일한 변경 사항은 [Microsoft.Web/serverfarms](/azure/templates/microsoft.web/serverfarms?tabs=json) 리소스의 새 ***zoneRedundant** _ 속성(필수) 및 선택적으로 App Service 계획 인스턴스 수(_*_용량_*_)입니다. 용량을 지정하지 않으면 플랫폼의 기본값은 3입니다. _*_zoneRedundant_*_ 속성은 _*_true로_*_ 설정해야 하며 워크로드 요구 사항에 따라 _ *_capacity_**를 설정해야 하지만 3개 이하로 설정해야 합니다. 용량을 선택하는 좋은 규칙은 인스턴스 영역 하나가 손실되어 예상 부하를 처리할 수 있는 충분한 용량을 남겨 두도록 애플리케이션에 충분한 인스턴스를 보장하는 것입니다.

> [!TIP]
> 인스턴스 용량을 결정하려면 다음 계산을 사용할 수 있습니다.
>
> 플랫폼이 3개 영역에 VM을 분산하고 1개 영역 이상의 실패를 고려해야 하므로 최대 워크로드 인스턴스 수를 영역/(zones-1) 또는 3/2의 요소에 곱해야 합니다. 예를 들어 일반적인 최대 워크로드에 4개의 인스턴스가 필요한 경우 6개의 인스턴스((2/3 * 6개 인스턴스) = 4개의 인스턴스를 프로비전해야 합니다.
>

아래 ARM 템플릿 조각은 새 ***zoneRedundant** _ property 및 _ capacity **_사양을_* 보여줍니다.

```json
"resources": [
  {
    "type": "Microsoft.Web/serverfarms",
    "apiVersion": "2018-02-01",
    "name": "your-appserviceplan-name-here",
    "location": "West US 3",
    "sku": {
        "name": "P1v3",
        "tier": "PremiumV3",
        "size": "P1v3",
        "family": "Pv3",
        "capacity": 3
    },
    "kind": "app",
    "properties": {
        "zoneRedundant": true
    }
  }
]
```

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [ARM 템플릿을 만들고 배포하는 방법 알아보기](../azure-resource-manager/templates/quickstart-create-templates-use-visual-studio-code.md)

> [!div class="nextstepaction"]
> [ARM 빠른 시작 템플릿](https://azure.microsoft.com/resources/templates/)