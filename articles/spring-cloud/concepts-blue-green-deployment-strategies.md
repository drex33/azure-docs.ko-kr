---
title: Azure Spring Cloud의 파란색-녹색 배포 전략
description: 이 항목에서는 Azure Spring Cloud의 파란색-녹색 배포에 대한 두 가지 접근 방식을 설명합니다.
author: karlerickson
ms.author: karler
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 11/12/2021
ms.custom: devx-track-java
ms.openlocfilehash: 8b019c5cc8e7e7a8ed132cc6adbf0d4c7d0495a4
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/15/2021
ms.locfileid: "132486262"
---
# <a name="blue-green-deployment-strategies-in-azure-spring-cloud"></a>Azure Spring Cloud의 파란색-녹색 배포 전략

이 문서에서는 Azure Spring Cloud의 파란색-녹색 배포 지원에 대해 설명합니다.

Azure Spring Cloud(표준 계층 이상)는 모든 앱에 대해 두 개의 배포를 허용하며,이 중 하나만 프로덕션 트래픽을 받습니다. 일반적으로 이 패턴을 파란색-녹색 배포라고 합니다. 파란색-녹색 배포에 대한 Azure Spring Cloud의 지원은 [CD(지속적인 배포)](/devops/deliver/what-is-continuous-delivery) 파이프라인 및 엄격한 자동화 테스트와 함께 높은 신뢰도를 가지고 민첩한 애플리케이션 배포를 가능하게 합니다.

## <a name="alternating-deployments"></a>대체 배포

Azure Spring Cloud를 사용하여 파란색-녹색 배포를 구현하는 가장 간단한 방법은 두 개의 고정 배포를 만들고 프로덕션 트래픽을 수신하지 않는 배포에 항상 배포하는 것입니다. [Azure Pipelines에 대한 Azure Spring Cloud 작업](/azure/devops/pipelines/tasks/deploy/azure-spring-cloud)을 사용하면 `UseStagingDeployment` 플래그를 `true`로 설정하기만 하면 이러한 방식으로 배포할 수 있습니다.

다음은 교대로 반복되는 배포 방법의 작동 방식입니다.

애플리케이션에 `deployment1` 및 `deployment2`의 두 가지 배치가 있다고 가정합니다. 현재 `deployment1`은 프로덕션 배포로 설정되어 있으며 애플리케이션의 `v3` 버전을 실행 중입니다.

이렇게 하면 `deployment2`이 스테이징 배포가 됩니다. 따라서 CD(지속적인 배포) 파이프라인을 실행할 준비가 되면 앱의 다음 버전인 버전 `v4`을 스테이징 배포 `deployment2`에 배포합니다.

![두 가지 배포: deployment1이 프로덕션 트래픽 수신](media/spring-cloud-blue-green-patterns/alternating-deployments-1.png)

`v4`가 `deployment2`에서 시작된 후 개인 테스트 엔드포인트를 통해 자동 및 수동 테스트를 실행하여 `v4`가 모든 기대치를 충족하는지 확인할 수 있습니다.

![이제 V4가 deployment2에 배포되고 테스트 진행](media/spring-cloud-blue-green-patterns/alternating-deployments-2.png)

`v4`에 확신이 있으면 `deployment2`를 프로덕션 배포로 설정하여 모든 프로덕션 트래픽을 수신할 수 있습니다. 롤백이 필요한 중요한 문제를 발견한 경우 `v3`은 `deployment1`에서 계속 실행됩니다.

![이제 deployment2의 V4가 프로덕션 트래픽 수신](media/spring-cloud-blue-green-patterns/alternating-deployments-3.png)

이제 `deployment1`이 스테이징 배치입니다. 따라서 다음 배포 파이프라인 실행은 `deployment1`에 배포됩니다.

![deployment1에 배포된 V5](media/spring-cloud-blue-green-patterns/alternating-deployments-4.png)

이제 `deployment1`의 프라이빗 테스트 엔드포인트에서 `V5`를 테스트할 수 있습니다.

![deployment1에서 테스트된 V5](media/spring-cloud-blue-green-patterns/alternating-deployments-5.png)

마지막으로 `v5`이 모든 기대치를 충족하면 `deployment1`를 다시 한 번 프로덕션 배포로 설정하여 `v5`가 모든 프로덕션 트래픽을 수신하도록 합니다.

![V5가 deployment1에서 트래픽 수신](media/spring-cloud-blue-green-patterns/alternating-deployments-6.png)

### <a name="tradeoffs-of-the-alternating-deployments-approach"></a>교대로 반복되는 배포 방식의 장단점

교대로 반복되는 배포 방식은 새 배포를 만들 필요가 없기 때문에 간단하고 빠릅니다. 그러나 다음 섹션에 설명된 대로 몇 가지 단점이 있습니다.

#### <a name="persistent-staging-deployment"></a>영구 스테이징 배포

스테이징 배포는 항상 실행 중이며 따라서 Azure Spring Cloud 인스턴스의 리소스를 소비합니다. 이렇게 하면 Azure Spring Cloud의 각 애플리케이션 리소스 요구 사항을 효과적으로 두 배로 늘릴 수 있습니다.

#### <a name="the-approval-race-condition"></a>승인 경합 상태

위의 애플리케이션에서 새 버전의 애플리케이션이 프로덕션 트래픽을 수신하기 전에 릴리스 파이프라인에 수동 승인이 필요하다고 가정합니다. 이로 인해 한 버전(`v6`)이 스테이징 배포에서 수동 승인을 기다리는 동안 배포 파이프라인이 다시 실행되어 최신 버전(`v7`)으로 덮어쓸 위험이 있습니다. 그런 다음 `v6`에 대한 승인이 부여되면 `v6`을 배포한 파이프라인이 스테이징 배포를 프로덕션으로 설정합니다. 그러나 이제 해당 배치에 배치되고 트래픽을 수신하는 것은 승인된 `v6`이 아니라 승인되지 않은 `v7`입니다.

![승인 경합 상태](media/spring-cloud-blue-green-patterns/alternating-deployments-race-condition.png)

모든 이전 버전의 배포 흐름이 완료되거나 중단될 때까지 한 버전의 배포 흐름을 시작할 수 없도록 하여 경합 상태를 방지할 수 있습니다. 승인 경합 상태를 방지하는 또 다른 방법은 명명된 배포 방식을 사용하는 것으로서 아래에 설명되어 있습니다.

## <a name="named-deployments"></a>명명된 배포

명명된 배포 방식에서 배포되는 애플리케이션의 새 버전마다 새 배포가 만들어집니다. 맞춤식 배포에서 애플리케이션을 테스트한 후에는 해당 배포를 프로덕션 배포로 설정합니다. 이전 버전을 포함하는 배포는 롤백이 필요하지 않다고 확신하는 동안만 지속되도록 허용할 수 있습니다.

아래 그림에서 버전 `v5`는 배치 `deployment-v5`에서 실행 중입니다. 배포가 이 버전에 대해 특별히 만들어졌으므로 배포 이름에 버전이 포함됩니다. 처음에는 다른 배포가 없습니다. 이제 `v6` 버전을 배포하기 위해 배포 파이프라인은 새 배포 `deployment-v6`을 만들고 여기에 앱 버전 `v6`을 배포합니다.

![명명된 배포에 새 버전 배포](media/spring-cloud-blue-green-patterns/named-deployment-1.png)

다른 버전을 병렬로 배포할 위험이 없습니다. 첫째, Azure Spring Cloud는 두 개의 배포가 이미 있는 상태에서 세 번째 배포를 만들도록 허용하지 않습니다. 둘째, 둘 이상의 배포가 있을 수 있는 경우에도 각 배포는 포함된 애플리케이션의 버전으로 식별됩니다. 따라서 `v6`의 배포를 조정하는 파이프라인은 `deployment-v6`을 프로덕션 배포로만 설정하려고 시도합니다.

![새 버전이 배포라는 프로덕션 트래픽 수신](media/spring-cloud-blue-green-patterns/named-deployment-2.png)

새 버전에 대해 생성된 배포에서 프로덕션 트래픽을 수신하고 나면 이전 버전을 포함하는 배포를 제거하여 향후 배포를 위한 공간을 확보해야 합니다. 새 버전에서 중요한 문제를 발견한 경우 이전 버전으로 롤백할 수 있도록 몇 분 또는 몇 시간 동안 연기할 수 있습니다.

![대체 기간 후 이전 배포를 삭제합니다.](media/spring-cloud-blue-green-patterns/named-deployment-3.png)

### <a name="tradeoffs-of-the-named-deployments-approach"></a>명명된 배포 방식의 장단점

명명된 배포 방식에는 다음과 같은 장점이 있습니다.

* 승인 경합 상태를 방지합니다.
* 사용하지 않을 때 스테이징 배포를 삭제하여 리소스 소비를 줄입니다.

그러나 다음 섹션에 설명된 대로 단점도 있습니다.

#### <a name="deployment-pipeline-failures"></a>배포 파이프라인 오류

배포가 시작된 시간과 스테이징 배포가 삭제된 시간 사이에 배포 파이프라인을 실행하려는 추가 시도가 실패합니다. 파이프라인은 새 배포를 만들려고 시도하므로 Azure Spring Cloud 애플리케이션당 두 개의 배포만 허용되므로 오류가 발생합니다.

따라서 배포 오케스트레이션에는 나중에 실패한 배포 프로세스를 다시 시도할 수 있는 수단 또는 모든 이전 버전에 대해 흐름이 완료될 때까지 각 버전의 배포 흐름이 큐에 대기 상태로 유지되도록 하는 수단이 있어야 합니다.

## <a name="next-steps"></a>다음 단계

* [Azure Spring Cloud에 대한 애플리케이션 배포 자동화](./how-to-cicd.md)
