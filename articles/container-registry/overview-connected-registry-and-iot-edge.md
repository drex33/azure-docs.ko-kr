---
title: Azure IoT Edge에서 연결된 레지스트리 사용
description: 계층적 IoT Edge 시나리오의 연결된 Azure Container Registry 개요
author: toddysm
ms.author: memladen
ms.service: container-registry
ms.topic: overview
ms.date: 08/24/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: 3f5be0a18c2185dffa685291121c8a730d60ef52
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131053197"
---
# <a name="using-connected-registry-with-azure-iot-edge"></a>Azure IoT Edge에서 연결된 레지스트리 사용

이 문서에서는 계층적 [IoT Edge](../iot-edge/about-iot-edge.md) 시나리오에서 Azure [연결된 레지스트리](intro-connected-registry.md)를 사용하는 방법을 알아봅니다. 연결된 컨테이너 레지스트리는 IoT Edge 모듈로 배포할 수 있으며, 계층 구조의 디바이스에 필요한 컨테이너 이미지를 제공하는 데 필수적인 역할을 합니다.

## <a name="what-is-a-hierarchical-iot-edge-deployment"></a>계층적 IoT Edge 배포란?

Azure IoT Edge를 사용하면 계층 구조 레이어로 구성된 네트워크에서 IoT Edge 디바이스를 배포할 수 있습니다. 계층 구조의 각 레이어는 그 아래 레이어에 있는 디바이스의 메시지와 요청을 처리하는 [게이트웨이 디바이스](../iot-edge/iot-edge-as-gateway.md)입니다. 최상위 계층만 클라우드에 연결되고 하위 계층은 인접한 북쪽 및 남쪽 계층과만 통신할 수 있도록 디바이스의 계층 구조를 구성할 수 있습니다. 이러한 네트워크 계층화는 [ISA-95 standard](https://en.wikipedia.org/wiki/ANSI/ISA-95)를 따르는 대부분의 산업 네트워크의 기초입니다.

IoT Edge 디바이스의 계층 구조를 만드는 방법을 알아보려면 [자습서: IoT Edge 디바이스의 계층 구조 만들기][tutorial-nested-iot-edge]를 참조하세요.

## <a name="how-do-i-use-connected-registry-in-hierarchical-iot-edge-scenarios"></a>계층적 IoT Edge 시나리오에서 연결된 레지스트리를 사용하려면 어떻게 해야 하나요?

다음 이미지에서는 연결된 레지스트리를 사용하여 IoT Edge의 계층적 배포를 지원하는 방법을 보여 줍니다. 회색 실선은 실제 네트워크 흐름을 나타내며, 파선은 구성 요소와 연결된 레지스트리 간의 논리적 통신을 표시합니다.

![연결된 레지스트리 및 계층적 IoT Edge 배포](media/overview-connected-registry-and-iot-edge/connected-registry-iot-edge-overview.svg)

### <a name="top-layer"></a>위쪽 계층

예제 아키텍처의 최상위 레이어인 “*레이어 5: Enterprise Network*”는 IT에서 관리하며 Azure 클라우드에서 Contoso용 컨테이너 레지스트리에 액세스할 수 있습니다. 연결된 레지스트리는 IoT Edge VM에 IoT Edge 모듈로 배포되며, 클라우드 레지스트리와 직접 통신하여 이미지 및 아티팩트를 풀/푸시할 수 있습니다. 

연결된 레지스트리는 기본 [ReadWrite 모드](intro-connected-registry.md#modes)에서 작동하는 것으로 표시됩니다. 이 연결된 레지스트리의 클라이언트는 이미지 및 아티팩트를 풀/푸시할 수 있습니다. 푸시된 이미지는 클라우드 레지스트리와 동기화됩니다. 해당 레이어에서 푸시가 필요하지 않은 경우 연결된 레지스트리를 [ReadOnly 모드](intro-connected-registry.md#modes)에서 작동하도록 변경할 수 있습니다.

이 수준에서 연결된 레지스트리를 IoT Edge 모듈로 배포하는 단계는 [빠른 시작 - IoT Edge 디바이스에 연결된 레지스트리 배포][quickstart-deploy-connected-registry-iot-edge-cli]를 참조하세요.

### <a name="nested-layers"></a>중첩 레이어

다음 하위 레이어인 “*레이어 4: 사이트 비즈니스 계획 및 물류*”는 레이어 5와만 통신하도록 구성되어 있습니다. 따라서 레이어 4에 IoT Edge VM을 배포하면 대신 레이어 5의 연결된 레지스트리에서 모듈 이미지를 풀해야 합니다. 

ReadOnly 모드에서 작동하는 연결된 레지스트리를 배포하여 하위 레이어를 서비스할 수도 있습니다. 이 내용은 “*레이어 3: 산업 보안 영역*”의 IoT Edge VM과 함께 나와 있습니다. 해당 VM은 “*레이어 4*”의 연결된 레지스트리에서 모듈 이미지를 풀해야 합니다. 하위 레이어의 클라이언트를 서비스해야 하는 경우 ReadOnly 모드의 연결된 레지스트리를 레이어 3에 배포하는 등의 작업을 수행할 수 있습니다.

이 아키텍처에서 각 레이어에 배포된 연결된 레지스트리는 이미지를 상위 레이어의 연결된 레지스트리와 동기화하도록 구성됩니다. 연결된 레지스트리는 IoT Edge 모듈로 배포되며 배포 및 네트워크 라우팅에 IoT Edge 메커니즘을 활용합니다.

중첩된 IoT Edge 디바이스에 연결된 레지스트리를 배포하는 단계는 [빠른 시작: 중첩된 IoT Edge 디바이스에 연결된 레지스트리 배포][tutorial-deploy-connected-registry-nested-iot-edge-cli]를 참조하세요.

## <a name="next-steps"></a>다음 단계

이 개요에서는 계층적 IoT Edge 시나리오에서 연결된 레지스트리를 사용하는 것에 대해 배웠습니다. 다음 문서를 계속 진행하여 연결된 레지스트리를 구성하고 IoT Edge 디바이스에 배포하는 방법을 알아봅니다.

> [!div class="nextstepaction"]
> [빠른 시작 - CLI를 사용하여 연결된 레지스트리 만들기][quickstart-connected-registry-cli]

> [!div class="nextstepaction"]
> [빠른 시작 - 포털을 사용하여 연결된 레지스트리 만들기][quickstart-connected-registry-portal]

> [!div class="nextstepaction"]
> [빠른 시작 - IoT Edge 디바이스에 연결된 레지스트리 배포][quickstart-deploy-connected-registry-iot-edge-cli]

> [!div class="nextstepaction"]
> [자습서: 중첩된 IoT Edge 디바이스에 연결된 레지스트리 배포][tutorial-deploy-connected-registry-nested-iot-edge-cli]

<!-- LINKS - internal -->
[quickstart-connected-registry-cli]:quickstart-connected-registry-cli.md
[quickstart-connected-registry-portal]:quickstart-connected-registry-portal.md
[quickstart-deploy-connected-registry-iot-edge-cli]:quickstart-deploy-connected-registry-iot-edge-cli.md
[tutorial-nested-iot-edge]:../iot-edge/tutorial-nested-iot-edge.md
[tutorial-deploy-connected-registry-nested-iot-edge-cli]: tutorial-deploy-connected-registry-nested-iot-edge-cli.md
