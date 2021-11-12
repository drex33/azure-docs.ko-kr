---
title: Azure App Service Environment에 대한 가용성 영역 지원
description: 앱이 영역 중복되도록 App Service Environment를 배포하는 방법을 알아봅니다.
author: ccompy
ms.assetid: 24e3e7eb-c160-49ff-8d46-e947818ef186
ms.topic: article
ms.date: 07/05/2021
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: 551146d18fa17aac365d62dbd1f8cbaa2d23a389
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132284763"
---
# <a name="availability-zone-support-for-app-service-environments"></a>Azure App Service Environment에 대한 가용성 영역 지원

> [!NOTE]
> 이 문서에서는 격리된 App Service 요금제와 함께 사용되는 App Service Environment v2에 관해 설명합니다.
> 

ASE(App Service Environment)는 AZ(가용성 영역)에 배포할 수 있습니다.  고객은 ILB(내부 부하 분산 장치) ASE를 Azure 지역 내 특정 AZ에 배포할 수 있습니다. ILB ASE를 특정 AZ에 고정하는 경우 ILB ASE에서 사용하는 리소스는 지정된 AZ에 고정되거나 영역 중복 방식으로 배포됩니다.  

ILB ASE는 특정 영역에 고정되므로 AZ에 명시적으로 배포된 ILB ASE는 영역 리소스로 간주합니다. 다음 ILB ASE 종속성은 지정된 영역에 고정됩니다.

- ASE의 내부 부하 분산 장치 IP 주소
- ASE에서 웹 애플리케이션을 관리하고 실행하는 데 사용하는 컴퓨팅 리소스

영역 ILB ASE에 배포된 웹 애플리케이션의 원격 파일 스토리지는 ZRS(영역 중복 스토리지)를 사용합니다.

이 문서에 설명된 단계를 따라야 ILB ASE가 자동으로 영역 방식으로 배포됩니다. 공용 IP 주소를 사용하여 외부 ASE를 특정 가용성 영역에 고정할 수 없습니다. 

영역 ILB ASE는 다음 지역에서 만들 수 있습니다.

- 오스트레일리아 동부
- 캐나다 중부
- 미국 중부
- 미국 동부
- 미국 동부 2
- 미국 동부 2(EUAP)
- 프랑스 중부 
- 일본 동부
- 북유럽
- 서유럽
- 동남아시아
- 영국 남부
- 미국 서부 2

영역 ILB ASE에 배포된 애플리케이션은 계속 실행되고 같은 지역의 다른 영역이 중단되는 경우에도 해당 ASE에 대한 트래픽을 제공합니다.  애플리케이션 서비스 플랜 스케일링, 애플리케이션 만들기, 애플리케이션 구성, 애플리케이션 게시를 포함한 런타임 이외 동작은 다른 가용성 영역에서 발생하는 중단의 영향을 계속 받을 수 있습니다. 영역 ILB ASE의 영역 고정 배포는 이미 배포된 애플리케이션의 지속적인 작동 시간만 보장합니다.

## <a name="how-to-deploy-an-app-service-environment-in-an-availability-zone"></a>가용성 영역에서 App Service Environment를 배포하는 방법

영역 ILB ASE는 ARM 템플릿을 사용하여 만들어야 합니다. 영역 ILB ASE는 ARM 템플릿을 통해 생성된 후 Azure Portal 및 CLI를 통해 보고 상호 작용할 수 있습니다.  ARM 템플릿은 영역 ILB ASE를 처음 만드는 경우에만 필요합니다.

영역 ILB ASE를 지정하기 위해 ARM 템플릿에서 필요한 유일한 변경 내용은 새로운 **zones** 속성입니다. _ *_zones_** 속성은 ILB ASE를 고정해야 하는 논리적 가용성 영역에 따라 “1”, “2” 또는 “3” 값으로 설정해야 합니다.

아래 예제 ARM 템플릿 코드 조각에서는 ILB ASE를 영역 2에 고정하도록 지정하는 새로운 ***zones*** 속성을 보여 줍니다.

```json
"resources": [
    {
        "type": "Microsoft.Web/hostingEnvironments",
        "kind": "ASEV2",
        "name": "yourASENameHere",
        "apiVersion": "2015-08-01",
        "location": "your location here",
        "zones": [
            "2"
        ],
        "properties": {
            "name": "yourASENameHere",
            "location": "your location here",
            "ipSslAddressCount": 0,
            "internalLoadBalancingMode": "3",
            "dnsSuffix": "contoso-internal.com",
            "virtualNetwork": {
                "Id": "/subscriptions/your-subscription-id-here/resourceGroups/your-resource-group-here/providers/Microsoft.Network/virtualNetworks/your-vnet-name-here",
                "Subnet": "yourSubnetNameHere"
            }
        }
    }
]
```

앱 영역을 중복으로 만들려면 두 개의 영역 ILB ASE를 배포해야 합니다. 두 영역 ILB ASE는 개별 가용성 영역에 있어야 합니다. 그런 다음, 각 ILB ASE에 앱을 배포해야 합니다. 앱이 생성된 후 부하 분산 솔루션을 구성해야 합니다. 권장 솔루션은 영역 ILB ASE의 [영역 중복 Application Gateway](../../application-gateway/application-gateway-autoscaling-zone-redundant.md) 업스트림을 배포하는 것입니다. 

## <a name="in-region-data-residency"></a>지역 내 데이터 보존

가용성 영역에 배포된 ILB ASE는 영역 ILB ASE가 배포된 지역 내 고객 데이터만 저장합니다. 웹 사이트 파일 콘텐츠와 App Service에 저장된 고객이 제공한 설정 및 비밀은 둘 다 영역 ILB ASE가 배포된 지역 내에 남아 있습니다.

고객은 “가용성 영역에서 App Service Environment를 배포하는 방법” 섹션의 앞부분에 설명된 단계에 따라 단일 지역 데이터 보존을 확인합니다. 이 단계에 따라 App Service Environment를 구성하면 가용성 영역에 배포된 App Service Environment는 [Azure 보안 센터](https://azuredatacentermap.azurewebsites.net/)에 지정된 요구 사항을 포함한 지역 내 데이터 보존 요구 사항을 충족합니다.

고객은 다음 단계에 따라 App Service Environment가 단일 지역에 데이터를 저장하도록 올바르게 구성되었는지 유효성을 검사할 수 있습니다. 

1. [리소스 탐색기](https://resources.azure.com)를 사용하여 App Service Environment의 ARM 리소스로 이동합니다.  ASE는 *providers/Microsoft.Web/hostingEnvironments* 아래에 나열됩니다.
2. *zones* 속성이 ARM JSON 구문 보기에 있고 값이 “1”, “2” 또는 “3”인 단일 값 JSON 배열을 포함하는 경우 ASE는 영역별로 배포되고 고객 데이터는 동일한 지역에 남아 있습니다.
2. *zones* 속성이 존재하지 않거나 속성에 이전에 지정된 유효한 영역 값이 없는 경우 ASE는 영역별로 배포되지 않고 고객 데이터는 동일한 지역에만 저장되지 않습니다.
