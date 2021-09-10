---
title: Azure Virtual Desktop 호스트 풀 부하 분산 - Azure
description: Azure Virtual Desktop 환경에 대한 호스트 풀 부하 분산 메서드에 대해 알아봅니다.
author: Heidilohr
ms.topic: conceptual
ms.date: 10/12/2020
ms.author: helohr
manager: femila
ms.openlocfilehash: 6b5316f25725f90be63fb5fbbc135285ad5bce3e
ms.sourcegitcommit: abf31d2627316575e076e5f3445ce3259de32dac
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/15/2021
ms.locfileid: "114204127"
---
# <a name="host-pool-load-balancing-methods"></a>호스트 풀 부하 분산 메서드

>[!IMPORTANT]
>이 콘텐츠는 Azure Resource Manager Azure Virtual Desktop 개체를 통해 Azure Virtual Desktop에 적용됩니다. Azure Resource Manager 개체 없이 Azure Virtual Desktop(클래식)을 사용하는 경우 [이 문서](./virtual-desktop-fall-2019/host-pool-load-balancing-2019.md)를 참조하세요.

Azure Virtual Desktop은 두 가지 부하 분산 메서드를 지원합니다. 각 메서드는 호스트 풀의 리소스에 연결할 때 사용자 세션을 호스트할 세션 호스트를 결정합니다.

Azure Virtual Desktop에서 다음과 같은 부하 분산 메서드를 사용할 수 있습니다.

- 폭 우선 부하 분산을 사용하면 호스트 풀의 세션 호스트 전체에 사용자 세션을 균등하게 분산할 수 있습니다.
- 깊이 우선 부하 분산을 사용하면 호스트 풀의 사용자 세션을 통해 세션 호스트의 포화 상태를 지정할 수 있습니다. 첫 번째 세션 호스트가 해당 세션 제한 임계값에 도달하면 부하 분산 장치는 해당 제한에 도달할 때까지 사용자 연결을 호스트 풀의 다음 세션 호스트로 보냅니다.

각 호스트 풀은 특정 유형의 부하 분산만 구성할 수 있습니다. 그러나 두 부하 분산 메서드는 어떤 호스트 풀에 있든 관계없이 다음 동작을 공유합니다.

- 사용자가 호스트 풀에 이미 세션이 있고 해당 세션에 다시 연결하는 경우 부하 분산 장치는 기존 세션을 사용하여 세션 호스트로 성공적으로 리디렉션됩니다. 이 동작은 세션 호스트의 AllowNewConnections 속성이 False로 설정된 경우에도 적용됩니다.
- 사용자에게 호스트 풀에 아직 세션이 없는 경우 부하 분산 장치는 부하 분산 중 AllowNewConnections 속성이 False로 설정된 세션 호스트를 고려하지 않습니다.

## <a name="breadth-first-load-balancing-method"></a>폭 우선 부하 분산 메서드

폭 우선 부하 분산 방법을 사용하면 사용자 연결을 분산하여 이 시나리오에 맞게 최적화할 수 있습니다. 이 메서드는 풀링된 가상 데스크톱 환경에 연결하는 사용자에게 최상의 환경을 제공하려는 조직에 적합합니다.

폭 우선 메서드는 먼저 새 연결을 허용하는 세션 호스트를 쿼리합니다. 그런 다음, 이 메서드는 세션 수가 가장 적은 세션 호스트 세트의 절반에서 세션 호스트를 임의로 선택합니다. 예를 들어 11, 12, 13, 14, 15, 16, 17, 18 및 19개 세션이 포함된 9개의 머신이 있는 경우 새로 만든 세션이 자동으로 첫 번째 머신으로 이동하지 않습니다. 대신 세션 수가 가장 적은 처음 5개 머신(11, 12, 13, 14, 15)으로 이동할 수 있습니다.

## <a name="depth-first-load-balancing-method"></a>깊이 우선 부하 분산 메서드

깊이 우선 부하 분산 메서드를 사용하면 한 번에 하나의 세션 호스트를 포화 상태로 만들어 이 시나리오를 최적화할 수 있습니다. 이 메서드는 호스트 풀에 할당한 가상 머신의 수를 보다 세부적으로 제어하려는 비용에 민감한 조직에 적합합니다.

깊이 우선 메서드는 먼저 새 연결을 허용하고 최대 세션 제한을 초과하지 않은 세션 호스트를 쿼리합니다. 그런 다음, 이 메서드는 세션 수가 가장 많은 세션 호스트를 선택합니다. 연결된 경우 메서드는 쿼리의 첫 번째 세션 호스트를 선택합니다.

>[!IMPORTANT]
>깊이 우선 부하 분산 알고리즘은 최대 세션 호스트 제한에 따라 세션을 세션 호스트에 배포합니다. 이 매개 변수는 깊이 우선 부하 분산 알고리즘을 사용하는 경우에 필요합니다. 가능한 최상의 사용자 환경을 위해 최대 세션 호스트 제한 매개 변수를 환경에 가장 적합한 숫자로 변경해야 합니다.
