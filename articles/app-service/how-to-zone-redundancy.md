---
title: 공용 다중 테 넌 트 App Service에 대 한 가용성 영역 지원
description: 앱이 영역 중복 되도록 App Service를 배포 하는 방법을 알아봅니다.
author: seligj95
ms.topic: article
ms.date: 11/16/2021
ms.author: jordanselig
ms.custom: references_regions
ms.openlocfilehash: e0388695708e8ab3c47ea8049038fb95384dabd2
ms.sourcegitcommit: 05c8e50a5df87707b6c687c6d4a2133dc1af6583
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/16/2021
ms.locfileid: "132551296"
---
# <a name="availability-zone-support-for-public-multi-tenant-app-service"></a>공용 다중 테 넌 트 App Service에 대 한 가용성 영역 지원

Microsoft Azure App Service는 업무상 중요 한 워크 로드에 대 한 복원 력 및 안정성을 달성할 수 있도록 [가용성 영역 (AZ)](../availability-zones/az-overview.md) 에 배포할 수 있습니다. 이 아키텍처를 영역 중복성이 라고도 합니다.

앱이 ASP (App Service 계획)에 있고 App Service 요금제는 단일 배율 단위에 있습니다. App Service 영역 중복으로 구성 된 경우 플랫폼은 선택한 지역에 있는 세 영역 모두에서 App Service 계획의 VM 인스턴스를 자동으로 분산 합니다. 3 보다 큰 용량을 지정 하 고 인스턴스 수를 3으로 나눌 경우 인스턴스가 균등 하 게 분산 됩니다. 그렇지 않으면 3 * 보다 큰 인스턴스 수가 나머지 하나 또는 두 개의 영역에 분산 됩니다. 영역 중복으로 구성 되지 않은 App Services의 경우 VM 인스턴스는 선택한 지역의 단일 영역에 배치 됩니다.

## <a name="requirements"></a>요구 사항

영역 중복성은 App Service 계획의 속성입니다. 영역 중복성을 사용 하기 위한 현재 요구 사항/제한 사항은 다음과 같습니다.

- Windows 및 Linux가 모두 지원 됨
- **Premium v2** 또는 **Premium v3** App Service 계획이 필요 합니다.
- 최소 3 개 인스턴스 수
  - 세 개 미만의 인스턴스 수를 지정 하는 경우 플랫폼은 백그라운드에서이 최소 개수를 적용 합니다.
- 다음 지역에서 사용할 수 있습니다.
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
- **새** App Service 계획을 만드는 경우에만 영역 중복성을 지정할 수 있습니다.
  - 현재 기존 App Service 계획을 변환할 수 없습니다. 영역 중복성을 지 원하는 새 App Service 계획을 만드는 방법에 대 한 자세한 내용은 다음 글머리 기호를 참조 하세요.
- 영역 중복성은 App Service 공간의 최신 부분 에서만 지원 됩니다.
  - 현재 Pv3에서 실행 하는 경우 영역 중복성을 지 원하는 공간이 이미 있을 수 있습니다. 이 시나리오에서는 새 App Service 계획을 만들고 새 App Service 계획을 만들 때 영역 중복성을 지정할 수 있습니다.
  - 영역 중복성을 지 원하는 Pv3 또는 배율 단위를 사용 하지 않거나 지원 되지 않는 지역에 있거나 잘 모르겠으면 다음 단계를 수행 합니다.
    - 지원 되는 지역에서 새 리소스 그룹 만들기
        - 이렇게 하면 App Service 컨트롤 평면이 영역 중복성을 지 원하는 선택 된 영역에서 배율 단위를 찾을 수 있습니다.
    - **새** 리소스 그룹을 사용 하 여 선택한 지역에 새 App Service 계획 (및 앱) 만들기
    - 새 App Service 계획을 만들 때 아래 설명 된 zoneRedundant 속성을 true로 설정 해야 합니다.
- [ARM (Azure Resource Manager) 템플릿을](../azure-resource-manager/templates/overview.md) 사용 하 여 만들어야 합니다.

영역이 다운 되는 경우 App Service 플랫폼은 손실 된 인스턴스를 검색 하 여 자동으로 새 대체 인스턴스를 찾으려고 시도 합니다. 자동 크기 조정이 구성 되어 있는 경우 더 많은 인스턴스가 필요 하다 고 판단 하는 경우에도 자동 크기 조정 기능을 통해 인스턴스를 추가 하 App Service 요청을 실행 합니다 (자동 크기 조정 동작은 App Service 플랫폼 동작과 무관). 손상 된 인스턴스를 다시 채우는 작업은 가장 효율적으로 수행 되므로 영역 축소 시나리오의 추가 인스턴스에 대 한 요청이 성공 한다는 보장이 없습니다. 권장 되는 솔루션은이 문서의 다음 섹션에 설명 된 대로 영역 손실을 고려 하 여 App Service 계획을 프로 비전 하는 것입니다.

영역 중복성에 대해 사용 하도록 설정 된 App Service 계획에 배포 된 응용 프로그램은 계속 실행 되며 동일한 지역의 다른 영역이 중단 되는 경우에도 트래픽을 제공 합니다. 그러나 App Service 계획 크기 조정, 응용 프로그램 만들기, 응용 프로그램 구성 및 응용 프로그램 게시를 비롯 한 런타임 동작이 다른 가용성 영역 중단의 영향을 받을 수 있습니다. App Service 요금제에 대 한 영역 중복성은 배포 된 응용 프로그램의 작동 시간을 계속 보장 합니다.

App Service 플랫폼이 영역 중복 App Service 계획에 인스턴스를 할당 하는 경우 [기본 Azure Virtual Machine Scale Sets에서 제공 하는 최상의 영역 분산](../virtual-machine-scale-sets/virtual-machine-scale-sets-use-availability-zones.md#zone-balancing)을 사용 합니다. 각 영역에 동일한 수의 Vm이 있거나 App Service 계획에서 사용 하는 다른 모든 영역에는 +/-1 VM이 있는 경우 App Service 요금제는 "균형 조정" 됩니다.

## <a name="how-to-deploy-a-zone-redundant-app-service"></a>영역 중복 App Service를 배포 하는 방법

현재는 ARM 템플릿을 사용 하 여 영역 중복 App Service를 만들어야 합니다. ARM 템플릿을 통해 만든 후에는 Azure Portal 및 CLI 도구를 통해 App Service 계획을 보고 상호 작용할 수 있습니다. ARM 템플릿은 App Service 계획을 처음 만들 때만 필요 합니다.

ARM 템플릿에서 영역 중복 App Service 지정 하는 데 필요한 변경 사항은 새로운 ***zoneRedundant** _ 속성 (필수)이 고 필요에 따라 [Microsoft 웹/serverfarms](/azure/templates/microsoft.web/serverfarms?tabs=json) 리소스의 App Service 계획 인스턴스 수 (_*_용량_*_)입니다. 용량을 지정 하지 않으면 플랫폼의 기본값은 3입니다. _*_ZoneRedundant_*_ 속성은 _*_true_*_ 로 설정 해야 하 고 _ *_capacity_**는 워크 로드 요구 사항에 따라 설정 해야 하지만 3 개 미만으로 설정 해야 합니다. 용량을 선택 하는 것이 좋은 것은 응용 프로그램에 충분 한 인스턴스를 확보 하는 것입니다. 즉, 한 인스턴스의 영역을 손실 하면 예상 되는 부하를 처리 하기에 충분 한 용량이 남게

> [!TIP]
> 인스턴스 용량을 결정 하려면 다음 계산을 사용할 수 있습니다.
>
> 플랫폼은 3 개 영역에 Vm을 분산 하 고 최소한 1 개 영역에 대 한 오류를 고려해 야 하며, 최대 작업 인스턴스 수를 영역/(영역-1) 또는 3/2의 요소로 곱합니다. 예를 들어 일반적인 최대 워크 로드에 4 개의 인스턴스가 필요한 경우 6 개 인스턴스 (2/3 * 6 개 인스턴스) = 4 개의 인스턴스를 프로 비전 해야 합니다.
>

다음 ARM 템플릿 코드 조각은 새로운 ***zoneRedundant** _ 속성 및 _ *_용량_** 사양을 보여줍니다.

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
> [ARM 템플릿을 만들고 배포 하는 방법 알아보기](../azure-resource-manager/templates/quickstart-create-templates-use-visual-studio-code.md)

> [!div class="nextstepaction"]
> [ARM 빠른 시작 템플릿](https://azure.microsoft.com/resources/templates/)
