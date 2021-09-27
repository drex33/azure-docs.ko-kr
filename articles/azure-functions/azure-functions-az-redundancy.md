---
title: 탄력적 Premium 계획에 대한 가용성 영역 지원 Azure Functions
description: 탄력적 Premium 계획에서 고가용성 함수 애플리케이션에 Azure Functions 가용성 영역 중복을 사용하는 방법을 알아봅니다.
ms.topic: conceptual
ms.author: johnguo
ms.date: 09/07/2021
ms.custom: references_regions
ms.openlocfilehash: 796f4db37429e73dd1c3d50dc71b0a599977d861
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128705708"
---
# <a name="azure-functions-support-for-availability-zone-redundancy"></a>가용성 영역 중복성에 대한 Azure Functions 지원

Azure Functions 대한 AZ(가용성 영역) 지원은 이제 App Service(Elastic Premium 및 전용) 계획에서 사용할 수 있습니다. 영역 중복 Azure 함수 애플리케이션은 가용성을 높이기 위해 가용성 영역 간에 인스턴스의 균형을 자동으로 조정합니다. 이 문서에서는 탄력적 Premium 함수 계획에 대한 영역 중복 지원에 중점을 둡니다. 전용 계획에 대한 영역 중복성은 [여기를 참조하세요.](../app-service/how-to-zone-redundancy.md)

## <a name="overview"></a>개요

[가용성 영역은](../availability-zones/az-overview.md#availability-zones) 데이터 센터 오류로부터 애플리케이션 및 데이터를 보호하는 고가용성 제품입니다. 가용성 영역은 Azure 지역 내의 고유한 물리적 위치입니다. 각 영역은 독립된 전원, 냉각 및 네트워킹을 갖춘 하나 이상의 데이터 센터로 구성됩니다. 복원력을 보장하기 위해 사용하도록 설정된 모든 지역에 최소 3개의 별도 영역이&#39;. 영역 내에서 컴퓨팅, 스토리지, 네트워킹 및 데이터 리소스를 공동 배치하고 다른 영역에 복제하여 애플리케이션 아키텍처에 고가용성을 구축할 수 있습니다.

영역 중복 함수 앱은 앱이 실행되는 인스턴스를 해당 지역의 가용성 영역 간에 자동으로 분산합니다. 영역 중복 탄력적 Premium 앱의 경우 앱이 규모 확장 및 축소되더라도 앱이 실행되는 인스턴스는 가용성 영역 간에 균등하게 분산됩니다.

## <a name="requirements"></a>요구 사항

> [!IMPORTANT]
> 함수 앱에 대한 [스토리지 계정을](storage-considerations.md#storage-account-requirements) 선택할 때 [ZRS(영역 중복 스토리지 계정)를](../storage/common/storage-redundancy.md#zone-redundant-storage)사용해야 합니다. 그렇지 않으면 영역 중단의 경우 Functions는 Storage 종속성으로 인해 예기치 않은 동작을 표시할 수 있습니다. 

- Windows 및 Linux가 모두 지원됩니다.
- [탄력적 Premium](functions-premium-plan.md) 또는 전용 호스팅 계획에서 호스트되어야 합니다. 전용(App Service) 호스팅 계획의 영역 중복에 대한 지침은 [여기에서](../app-service/how-to-zone-redundancy.md)찾을 수 있습니다.
  - AZ(가용성 영역) 지원은 현재 [소비](consumption-plan.md) 계획의 함수 앱에 사용할 수 없습니다.
- 영역 중복 계획은 최소 인스턴스 수를 3으로 지정해야 합니다.
- 탄력적 Premium 계획의 함수 앱에는 항상 준비된 [최소 인스턴스](functions-premium-plan.md#always-ready-instances) 수가 3개여야 합니다.
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
- 이때 [ARM 템플릿](../azure-resource-manager/templates/index.yml)을 통해 를 만들어야 합니다.

## <a name="how-to-deploy-a-function-app-on-a-zone-redundant-premium-plan"></a>영역 중복 Premium 계획에 함수 앱을 배포하는 방법

영역 중복 Elastic Premium Functions 계획을 처음 만들려면 [ARM 템플릿을](../azure-resource-manager/templates/quickstart-create-templates-use-visual-studio-code.md)통해 배포해야 합니다. 그런 다음, 성공적으로 만들어지면 Azure Portal 및 CLI 도구를 통해 함수 계획을 보고 상호 작용할 수 있습니다. ARM 템플릿은 함수 계획의 초기 생성에만 필요합니다. Premium 계획에서 Functions 호스팅에 대한 가이드는 [여기에서](functions-infrastructure-as-code.md#deploy-on-premium-plan)찾을 수 있습니다. 영역 중복 계획을 만들고 배포한 후에는 새 계획에서 호스트되는 모든 함수 앱이 영역 중복이 됩니다. 

영역 중복 함수 계획을 만드는 동안 유의해야 할 유일한 속성은 새 **zoneRedundant** 속성 및 함수 계획 인스턴스 수(**용량**) 필드입니다. **zoneRedundant** 속성은 **true로** 설정해야 하며 **용량** 속성은 워크로드 요구 사항에 따라 설정해야 하지만 3보다 적어야 합니다. 적절한 용량 선택은 여러 요인과 고가용성/내결함성 전략에 따라 달라집니다. 인스턴스 영역 하나가 손실되어 예상 부하를 처리할 수 있는 충분한 용량이 남도록 애플리케이션에 충분한 인스턴스를 확보하는 것이 좋습니다.

> [!IMPORTANT]
> 탄력적 프리미엄, 영역 중복 함수 계획에 호스트되는 Azure 함수 앱에는 [항상 준비된 최소 인스턴스](functions-premium-plan.md#always-ready-instances) 수가 3개여야 합니다. 이는 영역 중복 함수 앱에 항상 영역당 하나 이상의 작업자를 충족할 수 있는 충분한 인스턴스가 있도록 하기 위한 것입니다.

다음은 새 **zoneRedundant** 필드 및 **용량** 사양을 보여 Premium 영역 중복 함수 계획에 대한 ARM 템플릿 조각입니다.

```
    "resources": [
        {
            "type": "Microsoft.Web/serverfarms",
            "apiVersion": "2021-01-15",
            "name": "your_plan_name_here",
            "location": "Central US",
            "sku": {
                "name": "EP3",
                "tier": "ElasticPremium",
                "size": "EP3",
                "family": "EP", 
                "capacity": 3
            },
            "kind": "elastic",
            "properties": {
                "perSiteScaling": false,
                "elasticScaleEnabled": true,
                "maximumElasticWorkerCount": 20,
                "isSpot": false,
                "reserved": false,
                "isXenon": false,
                "hyperV": false,
                "targetWorkerCount": 0,
                "targetWorkerSizeId": 0, 
                "zoneRedundant": true
            }
        }
    ]
```

자세한 내용은 [Azure Functions 함수 앱에 대한 리소스 배포 자동화를 참조하세요.](functions-infrastructure-as-code.md)
