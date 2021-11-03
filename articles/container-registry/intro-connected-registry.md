---
title: 연결된 레지스트리란?
description: Azure Container Registry의 연결된 레지스트리 기능의 개요 및 시나리오
ms.author: memladen
ms.service: container-registry
ms.topic: overview
ms.date: 10/21/2021
ms.custom: references_regions, ignite-fall-2021
ms.openlocfilehash: ccc80f2ce5fb8393824686101d2069beee58efa4
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131029720"
---
# <a name="what-is-a-connected-registry"></a>연결된 레지스트리란? 

이 문서에서는 [Azure Container Registry](container-registry-intro.md)의 *연결된 레지스트리* 기능에 대해 알아봅니다. 연결된 레지스트리는 컨테이너 이미지 및 기타 OCI 아티팩트와 클라우드 기반 Azure Container Registry를 동기화하는 온-프레미스 또는 원격 복제본입니다. 연결된 레지스트리를 사용하여 레지스트리 아티팩트 온-프레미스에 대한 액세스 속도를 높이고, 예를 들어 [중첩된 IoT Edge](../iot-edge/tutorial-nested-iot-edge.md)를 사용하여 고급 시나리오를 빌드합니다.

> [!NOTE]
> 연결된 레지스트리는 **프리미엄** 컨테이너 레지스트리 서비스 계층의 미리 보기 기능이며, [제한 사항](#limitations)의 적용을 받습니다. 레지스트리 서비스 계층 및 제한에 대한 자세한 내용은 [Azure Container Registry 서비스 계층](container-registry-skus.md)을 참조하세요.

## <a name="available-regions"></a>사용 가능한 지역

* 아시아 동부
* 북유럽
* 서유럽
* 미국 동부

## <a name="scenarios"></a>시나리오

클라우드 기반 Azure Container Registry는 지역 복제, 통합 보안, Azure 관리형 스토리지 및 Azure 개발 및 배포 파이프라인과의 통합을 비롯한 [기능](container-registry-intro.md#key-features)을 제공합니다. 동시에 고객은 클라우드 투자를 온-프레미스 및 현장 솔루션으로 확장하고 있습니다.

온-프레미스 또는 원격 환경에서 필요한 성능 및 안정성으로 실행하려면 컨테이너 워크로드 주변에서 컨테이너 이미지 및 관련 아티팩트를 사용할 수 있어야 합니다. 연결된 레지스트리는 콘텐츠를 클라우드 기반 Azure Container Registry와 정기적으로 동기화하는 성능이 우수한 온-프레미스 레지스트리 솔루션을 제공합니다.

연결된 레지스트리에 대한 시나리오는 다음과 같습니다.

* 연결된 팩터리
* 지점 판매 소매점 위치
* 배송, 오일 드릴링, 마이닝 및 간헐적으로 연결된 기타 환경

## <a name="how-does-the-connected-registry-work"></a>연결된 레지스트리는 어떻게 작동하나요?

다음 이미지는 연결된 레지스트리에 대한 일반적인 배포 모델을 보여줍니다.

:::image type="content" source="media/intro-connected-registry/connected-registry-overview.svg" alt-text="연결된 레지스트리 개요":::

### <a name="deployment"></a>배포

연결된 각 레지스트리는 클라우드 기반 Azure Container Registry를 사용하여 관리하는 리소스입니다. 연결된 레지스트리 계층 구조의 상위 부모는 Azure 클라우드 또는 [Azure Stack Hub](/azure-stack/operator/azure-stack-overview)의 프라이빗 배포의 Azure Container Registry입니다.

Azure 도구를 사용하여 온-프레미스의 서버나 디바이스 또는 [Azure IoT Edge](../iot-edge/tutorial-nested-iot-edge.md)와 같은 컨테이너 워크로드 온-프레미스를 지원하는 환경에 연결된 레지스트리를 설치합니다.

연결된 레지스트리의 *활성화 상태* 는 온-프레미스에 배포되었는지 여부를 나타냅니다.

* **활성** - 연결된 레지스트리가 현재 온-프레미스에 배포되어 있습니다. 비활성화될 때까지 다시 배포할 수 없습니다. 
* **비활성** - 연결된 레지스트리가 온-프레미스에 배포되지 않습니다. 지금은 배포할 수 있습니다.  
 
### <a name="content-synchronization"></a>콘텐츠 동기화

연결된 레지스트리는 컨테이너 이미지와 OCI 아티팩트를 동기화하기 위해 클라우드 레지스트리에 정기적으로 액세스합니다. 

또한 클라우드 레지스트리에서 리포지토리의 하위 집합을 동기화하거나 특정 간격 동안에만 동기화하여 클라우드와 프레미스 간의 트래픽을 줄이도록 구성할 수도 있습니다.

### <a name="modes"></a>모드

연결된 레지스트리는 *ReadWrite* 또는 *ReadOnly* 의 두 가지 모드 중 하나로 작동할 수 있습니다.

- **ReadWrite 모드** - 기본 모드를 사용하면 클라이언트가 연결된 레지스트리에 아티팩트(읽기 및 쓰기)를 끌어오기 및 푸시할 수 있습니다. 연결된 레지스트리에 푸시되는 아티팩트는 클라우드 레지스트리와 동기화됩니다. 
        
  ReadWrite 모드는 로컬 개발 환경이 준비되어 있는 경우에 유용합니다. 이미지는 로컬 연결된 레지스트리로 푸시되고, 이 레지스트리에서 클라우드로 동기화됩니다.

- **ReadOnly 모드** - 연결된 레지스트리가 ReadOnly 모드인 경우 클라이언트는 아티팩트만 끌어오기(읽기)할 수 있습니다. 이 구성은 중첩된 IoT Edge 시나리오 또는 클라이언트가 작동할 컨테이너 이미지를 끌어와야 하는 다른 시나리오에 사용됩니다.

### <a name="registry-hierarchy"></a>레지스트리 계층 구조

연결된 각 레지스트리는 부모에 연결되어야 합니다. 상위 부모는 클라우드 레지스트리입니다. [중첩된 IoT Edge](overview-connected-registry-and-iot-edge.md)와 같은 계층적 시나리오의 경우 연결된 레지스트리를 두 모드 중 하나로 중첩할 수 있습니다. 클라우드 레지스트리에 연결된 부모는 두 모드 중 하나에서 작동할 수 있습니다. 

자식 레지스트리는 부모 기능과 호환되어야 합니다. 따라서 ReadWrite 및 ReadOnly 모드 연결된 레지스트리는 모두 ReadWrite 모드에서 작동하는 연결된 레지스트리의 자식일 수 있지만, ReadOnly 모드 레지스트리만 ReadOnly 모드에서 작동하는 연결된 레지스트리의 자식일 수 있습니다.  

## <a name="client-access"></a>클라이언트 액세스

온-프레미스 클라이언트는 Docker CLI와 같은 표준 도구를 사용하여 연결된 레지스트리에서 콘텐츠를 푸시하거나 끌어올 수 있습니다. 클라이언트 액세스를 관리하려면 연결된 각 레지스트리에 액세스하기 위해 Azure Container Registry [토큰][repository-scoped-permissions]을 만듭니다. 레지스트리에 있는 하나 이상의 리포지토리에 대한 끌어오기 또는 푸시 액세스를 위해 클라이언트 토큰의 범위를 지정할 수 있습니다.

또한 연결된 각 레지스트리는 부모 레지스트리와 정기적으로 통신해야 합니다. 이를 위해 레지스트리는 클라우드 레지스트리에 의해 동기화 토큰(*동기화 토큰*)을 발급합니다. 이 토큰은 동기화 및 관리 작업을 위해 부모 레지스트리로 인증하는 데 사용됩니다.

자세한 내용은 [연결된 레지스트리에 대한 액세스 관리][overview-connected-registry-access]를 참조하세요.

## <a name="limitations"></a>제한 사항

- 토큰 수 및 범위 맵은 단일 컨테이너 레지스트리에 대해 각각 20,000개로 [제한](container-registry-skus.md)됩니다. 이렇게 하면 연결된 모든 레지스트리에 동기화 및 클라이언트 토큰이 필요하기 때문에 클라우드 레지스트리에 대한 연결된 레지스트리 수가 간접적으로 제한됩니다.
- 범위 맵의 리포지토리 권한 수는 500개로 제한됩니다.
- 연결된 레지스트리에 대한 클라이언트 수는 현재 20개로 제한됩니다.
- 리포지토리/매니페스트/태그 메타데이터를 통한 [이미지 잠금](container-registry-image-lock.md)은 현재 연결된 레지스트리에 대해 지원되지 않습니다.
- ReadOnly 모드를 사용하는 연결된 레지스트리에서는 [리포지토리 삭제](container-registry-delete.md)가 지원되지 않습니다.
- 연결된 레지스트리에 대한 [리소스 로그](monitor-service-reference.md#resource-logs)는 현재 지원되지 않습니다.
- 연결된 레지스트리는 레지스트리의 홈 지역 데이터 엔드포인트와 결합됩니다. [지역 복제](container-registry-geo-replication.md)를 위한 자동 마이그레이션은 지원되지 않습니다.
- 연결된 레지스트리를 삭제하려면 컨테이너를 온-프레미스에서 수동으로 제거하고 클라우드에서 해당 범위 맵 또는 토큰을 제거해야 합니다.
- 연결된 레지스트리 동기화 제한 사항은 다음과 같습니다.
  - 연속 동기화의 경우:
    - `minMessageTtl`은(는) 1일입니다.
    - `maxMessageTtl`은(는) 90일입니다.
  - 동기화 창을 지정하려는 경우에 따라 간헐적으로 연결된 시나리오의 경우:
    - `minSyncWindow`은(는) 1시간입니다.
    - `maxSyncWindow`은(는) 7일입니다.

## <a name="next-steps"></a>다음 단계

이 개요에서 연결된 레지스트리 및 몇 가지 기본 개념에 대해 알아봤습니다. 다음 문서 중 하나를 계속 진행하여 연결된 레지스트리를 활용할 수 있는 특정 시나리오에 대해 알아봅니다.

> [!div class="nextstepaction"]
> [개요: 연결된 레지스트리 액세스][overview-connected-registry-access]
> 
> [!div class="nextstepaction"]
> [개요: 연결된 레지스트리 및 IoT Edge][overview-connected-registry-and-iot-edge]

<!-- LINKS - internal -->
[overview-connected-registry-access]:overview-connected-registry-access.md
[overview-connected-registry-and-iot-edge]:overview-connected-registry-and-iot-edge.md
[repository-scoped-permissions]: container-registry-repository-scoped-permissions.md
