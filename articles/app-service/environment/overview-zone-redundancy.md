---
title: App Service Environment의 영역 중복
description: App Service Environment의 영역 중복에 대한 개요입니다.
author: madsd
ms.topic: overview
ms.date: 11/15/2021
ms.author: madsd
ms.openlocfilehash: 3ce5a0925ae4c1dc1bc33fec73987455d612d661
ms.sourcegitcommit: 2ed2d9d6227cf5e7ba9ecf52bf518dff63457a59
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/16/2021
ms.locfileid: "132526805"
---
# <a name="availability-zone-support-for-app-service-environments"></a>Azure App Service Environment에 대한 가용성 영역 지원

> [!NOTE]
> 이 문서에서는 격리된 v2 App Service 요금제와 함께 사용되는 App Service Environment v3에 관해 설명합니다.
>

ASE(App Service Environment)는 [AZ(가용성 영역)](../../availability-zones/az-overview.md)에 배포할 수 있습니다. 이 아키텍처를 영역 중복이라고도 합니다. ASE가 영역 중복으로 구성되면 플랫폼에서 ASE의 App Service 요금제 인스턴스를 선택한 지역의 세 영역 모두에 자동으로 분산시킵니다. 3보다 큰 용량을 지정하고 인스턴스 수를 3으로 나눌 수 있는 경우 인스턴스가 균등하게 분산됩니다. 그렇지 않으면 3*N을 초과하는 인스턴스 수는 나머지 하나 또는 두 개의 영역에 분산됩니다.

ASE를 만들 때 영역 중복을 구성하면 해당 ASE에서 만든 모든 App Service 요금제는 영역 중복이 됩니다. 영역 중복은 *새* App Service Environment를 만들 때만 지정할 수 있습니다. 영역 중복은 [지역의 하위 집합](./overview.md#regions)에서만 지원됩니다.

영역의 가동이 중단되는 경우 App Service 플랫폼에서 손실된 인스턴스를 감지하고 새 대체 인스턴스를 자동으로 찾으려고 시도합니다. 또한 자동 크기 조정이 구성되어 있고 더 많은 인스턴스가 필요하다고 결정하는 경우 자동 크기 조정에서 더 많은 인스턴스를 추가하도록 App Service에 요청합니다(자동 크기 조정 동작은 App Service 플랫폼 동작과 관계없음). 손실된 인스턴스를 최선의 노력으로 다시 채우므로 영역 가동 중단 시나리오에서 인스턴스에 대한 요청이 성공한다는 보장은 없습니다. 권장되는 솔루션은 영역 손실을 고려하여 App Service 요금제의 크기를 조정하는 것입니다.

동일한 지역의 다른 영역이 중단되는 경우에도 영역 중복 ASE에 배포된 애플리케이션은 계속 실행되고 트래픽을 처리합니다. 그러나 App Service 요금제 크기 조정, 애플리케이션 만들기, 애플리케이션 구성 및 애플리케이션 게시를 포함한 비 런타임 동작은 다른 가용성 영역에서 발생하는 중단의 영향을 계속 받을 수 있습니다. App Service Environment의 영역 중복은 배포된 애플리케이션에 대한 지속적인 작동 시간만 보장합니다.

App Service 플랫폼에서 인스턴스를 ASE의 영역 중복 App Service 요금제에 할당하는 경우 [기본 Azure Virtual Machine Scale Sets에서 제공하는 최상의 영역 분산](../../virtual-machine-scale-sets/virtual-machine-scale-sets-use-availability-zones.md#zone-balancing)을 사용합니다. 각 영역에 동일한 수의 인스턴스가 있거나 App Service 요금제에서 사용하는 다른 모든 영역에 하나보다 많거나 적은 수의 인스턴스가 있는 경우 App Service 요금제는 "분산"됩니다.

## <a name="pricing"></a>가격 책정

 영역 중복 ASE에는 9개의 App Service 요금제 인스턴스에 대한 최소 요금이 부과됩니다. 9개 이상의 App Service 요금제 인스턴스가 있는 경우 가용성 영역 지원에 대한 추가 요금이 부과되지 않습니다. 영역 중복 ASE의 App Service 요금제에서 9개 미만의 인스턴스가 있는 경우(크기에 관계없이) 9개 및 실행하는 인스턴스 수 간의 차이에 대한 요금은 추가 Windows I1v2 인스턴스로 부과됩니다.

## <a name="next-steps"></a>다음 단계

* [가용성 영역](../../availability-zones/az-overview.md)에 대해 자세히 알아보기
