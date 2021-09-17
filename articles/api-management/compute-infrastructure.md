---
title: Azure API Management 계산 플랫폼
description: API Management 서비스 인스턴스를 호스트 하는 데 사용 되는 계산 플랫폼에 대해 알아봅니다.
author: dlepow
ms.service: api-management
ms.topic: conceptual
ms.date: 08/23/2021
ms.author: danlep
ms.custom: ''
ms.openlocfilehash: 15bc0aa0c4a657bbb6f363a9c2786c1d4f9116ee
ms.sourcegitcommit: f2d0e1e91a6c345858d3c21b387b15e3b1fa8b4c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/07/2021
ms.locfileid: "123544445"
---
# <a name="compute-platform-for-azure-api-management"></a>Azure API Management에 대 한 계산 플랫폼

Azure API Management는 클라우드 PaaS (platform as a service)로 서 서비스를 호스트 하 고 실행 하는 데 사용 되는 인프라의 많은 세부 정보를 요약 합니다. 기본 리소스에 대해 몰라도 대부분의 API Management 인스턴스를 만들고 관리 하 고 확장할 수 있습니다.

서비스 기능을 향상 시키기 위해, 서비스를 호스트 하는 Azure compute 리소스 (여러 [서비스 계층](api-management-features.md)의 인스턴스) API Management 계산 플랫폼 버전을 업그레이드 하 고 있습니다. 이 문서에서는 업그레이드 및 API Management의 계산 플랫폼의 주 버전에 대 한 컨텍스트를 제공 `stv1` `stv2` 합니다.

API Management 인스턴스의 작업에서이 업그레이드에 대 한 영향을 최소화 했습니다. 그러나 인스턴스가 [Azure virtual network](virtual-network-concepts.md)에 연결 된 경우 인스턴스가 플랫폼 버전으로 업그레이드 될 때 일부 네트워크 구성 설정을 변경 해야 `stv2` 합니다.

## <a name="compute-platform-versions"></a>계산 플랫폼 버전

| Version | Description | Architecture | API Management 계층 |
| -------| ----------| ----------- | ------- |
| `stv2` | 단일 테 넌 트 v2 | [가상 머신 확장 집합](../virtual-machine-scale-sets/overview.md) | 개발자, 기본, 표준 및 프리미엄 |
| `stv1` |  단일 테 넌 트 v1 | [클라우드 서비스 (클래식)](../cloud-services/cloud-services-choose-me.md) | 개발자, 기본, 표준 및 프리미엄 |
| `mtv1` | 다중 테 넌 트 v1 |  [App service](../app-service/overview.md) | Consumption |


## <a name="how-do-i-know-which-platform-hosts-my-api-management-instance"></a>내 API Management 인스턴스를 호스트 하는 플랫폼을 확인 어떻게 할까요?

### <a name="developer-basic-standard-and-premium-tiers"></a>개발자, 기본, 표준 및 Premium 계층

* **2021 4 월** 이후의 Azure Portal 사용 하 여 만들거나 업데이트 한 가상 네트워크 연결을 사용 하는 인스턴스 또는 API Management REST API 버전 **2021-01-01-preview** 이상을 사용한 인스턴스는 플랫폼에서 호스트 됩니다. `stv2`
* Premium 계층 인스턴스에서 [영역 중복성](zone-redundancy.md) 을 사용 하도록 설정 하는 경우 플랫폼에서 호스팅됩니다. `stv2`
* 그렇지 않으면 인스턴스가 플랫폼에서 호스팅됩니다. `stv1`

> [!TIP]
> API 버전부터 `2021-04-01-preview` API Management 인스턴스에는 `PlatformVersion` 이 플랫폼 정보를 표시 하는 읽기 전용 속성이 있습니다. 

### <a name="consumption-tier"></a>소비 계층

* 모든 인스턴스는 플랫폼에서 호스팅됩니다. `mtv1`

## <a name="how-do-i-upgrade-to-the-stv2-platform"></a>플랫폼으로 업그레이드할 어떻게 할까요? `stv2` 있나요? 

업데이트는 Developer, Basic, Standard 또는 Premium 계층의 인스턴스에만 가능 합니다. 

다음을 사용 하 여 API Management 인스턴스에서 가상 네트워크 연결 또는 가용성 영역 구성을 만들거나 업데이트 합니다.

* [Azure Portal](https://portal.azure.com)
* API 버전 **2021-01-01-preview** 이상을 지정 하는 Azure REST API 또는 ARM 템플릿

> [!IMPORTANT]
> Azure [가상 네트워크](virtual-network-concepts.md)에 연결 된 인스턴스의 계산 플랫폼 버전을 업데이트 하는 경우:
> * 표준 SKU [공용 IPv4 주소](../virtual-network/public-ip-addresses.md#standard) 리소스를 제공 해야 합니다.
> * API Management 인스턴스의 VIP 주소가 변경 됩니다.

## <a name="next-steps"></a>다음 단계

* API Management에서 [가상 네트워크](virtual-network-concepts.md) 를 사용 하는 방법에 대해 자세히 알아보세요.
* [영역 중복성](zone-redundancy.md)에 대해 자세히 알아보세요.