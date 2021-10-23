---
title: Azure API Management 컴퓨팅 플랫폼
description: API Management 서비스 인스턴스를 호스트하는 데 사용되는 컴퓨팅 플랫폼에 대해 알아봅니다.
author: dlepow
ms.service: api-management
ms.topic: conceptual
ms.date: 08/23/2021
ms.author: danlep
ms.custom: ''
ms.openlocfilehash: 23d54c2725e48b1d587fb641c593be0c8d63958f
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130248643"
---
# <a name="compute-platform-for-azure-api-management"></a>Azure API Management 컴퓨팅 플랫폼

클라우드 PaaS(Platform-as-a-Service)인 Azure API Management 서비스를 호스트하고 실행하는 데 사용되는 인프라의 많은 세부 정보를 추상화합니다. 기본 리소스에 대해 알 필요 없이 API Management 인스턴스의 대부분의 측면을 만들고, 관리하고, 확장할 수 있습니다.

서비스 기능을 향상시키기 위해 API Management 컴퓨팅 플랫폼 버전(서비스를 호스트하는 Azure 컴퓨팅 리소스)을 여러 서비스 계층의 인스턴스로 [업그레이드합니다.](api-management-features.md) 이 문서에서는 업그레이드 및 API Management 컴퓨팅 플랫폼의 주요 버전인 및 에 대한 컨텍스트를 `stv1` `stv2` 제공합니다.

이 업그레이드가 API Management 인스턴스의 작업에 미치는 영향을 최소화했습니다. 그러나 인스턴스가 [Azure 가상 네트워크에](virtual-network-concepts.md)연결된 경우 인스턴스가 플랫폼 버전으로 업그레이드할 때 일부 네트워크 구성 설정을 변경해야 `stv2` 합니다.

## <a name="compute-platform-versions"></a>컴퓨팅 플랫폼 버전

| 버전 | 설명 | Architecture | API Management 계층 |
| -------| ----------| ----------- | ------- |
| `stv2` | 단일 테넌트 v2 | [가상 머신 확장 집합](../virtual-machine-scale-sets/overview.md) | 개발자, 기본, 표준 및 프리미엄 |
| `stv1` |  단일 테넌트 v1 | [클라우드 서비스(클래식)](../cloud-services/cloud-services-choose-me.md) | 개발자, 기본, 표준 및 프리미엄 |
| `mtv1` | 다중 테넌트 v1 |  [App service](../app-service/overview.md) | Consumption |


## <a name="how-do-i-know-which-platform-hosts-my-api-management-instance"></a>내 API Management 인스턴스를 호스트하는 플랫폼을 알고 어떻게 할까요??

### <a name="developer-basic-standard-and-premium-tiers"></a>개발자, 기본, 표준 및 Premium 계층

* 2021년 4월 이후 Azure Portal 사용하거나 API Management REST API 버전 **2021-01-01-preview** 이상 버전을 사용하여 만들거나 업데이트한 가상 네트워크 연결이 있는 인스턴스는  `stv2` 플랫폼에서 호스트됩니다.
* Premium 계층 인스턴스에서 [영역 중복성을](zone-redundancy.md) 사용하도록 설정한 경우 플랫폼에서 호스팅됩니다. `stv2`
* 그렇지 않으면 인스턴스가 플랫폼에서 호스트됩니다. `stv1`

> [!TIP]
> API 버전부터 `2021-04-01-preview` API Management 인스턴스에는 이 플랫폼 정보를 표시하는 읽기 전용 `PlatformVersion` 속성이 있습니다. 

### <a name="consumption-tier"></a>소비 계층

* 모든 인스턴스가 플랫폼에서 호스트됩니다. `mtv1`

## <a name="how-do-i-upgrade-to-the-stv2-platform"></a>플랫폼으로 업그레이드할 어떻게 할까요? `stv2` 있나요? 

개발자, 기본, 표준 또는 Premium 계층의 인스턴스에 대해서만 업데이트가 가능합니다. 

다음을 사용하여 API Management 인스턴스에서 가상 네트워크 연결 또는 가용성 영역 구성을 만들거나 업데이트합니다.

* [Azure Portal](https://portal.azure.com)
* API 버전 **2021-01-01-preview** 이상 지정 Azure REST API 또는 ARM 템플릿

> [!IMPORTANT]
> Azure [가상 네트워크에](virtual-network-concepts.md)연결된 인스턴스의 컴퓨팅 플랫폼 버전을 업데이트하는 경우:
> * 표준 SKU [공용 IPv4 주소](../virtual-network/ip-services/public-ip-addresses.md#standard) 리소스를 제공해야 합니다.
> * API Management 인스턴스의 VIP 주소가 변경됩니다.

## <a name="next-steps"></a>다음 단계

* API Management [가상 네트워크](virtual-network-concepts.md) 사용에 대해 자세히 알아보세요.
* [영역 중복성에](zone-redundancy.md)대해 자세히 알아보세요.