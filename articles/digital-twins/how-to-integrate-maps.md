---
title: Azure Maps와 통합
titleSuffix: Azure Digital Twins
description: Azure Functions를 사용하여 트윈 그래프와 Azure Digital Twins 알림으로 Azure Maps 실내 맵을 업데이트할 수 있는 함수를 만드는 방법을 참조하세요.
author: baanders
ms.author: baanders
ms.date: 1/19/2021
ms.topic: how-to
ms.service: digital-twins
ms.reviewer: baanders
ms.openlocfilehash: dd161468cda08a0046f2f46a79a973e4bbf8b49a
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122566717"
---
# <a name="use-azure-digital-twins-to-update-an-azure-maps-indoor-map"></a>Azure Digital Twins를 사용하여 Azure Maps 실내 맵 업데이트

이 문서에서는 [Azure Maps](../azure-maps/about-azure-maps.md)를 사용하여 *실내 맵* 에 표시되는 정보를 업데이트하기 위해 Azure Digital Twins 데이터를 사용하는 데 필요한 단계를 안내합니다. Azure Digital Twins는 IoT 디바이스 관계의 그래프를 저장하고 여러 엔드포인트에 원격 분석을 라우팅합니다. 이를 통해 맵에서 정보 오버레이를 업데이트하는 데 완벽한 서비스를 사용할 수 있습니다.

이 방법에는 다음이 포함됩니다.

1. [Azure Functions](../azure-functions/functions-overview.md)의 함수에 트윈 업데이트 이벤트를 보내도록 Azure Digital Twins 인스턴스 구성
2. Azure Maps 실내 맵 기능 상태 세트를 업데이트하는 함수 생성
3. Azure Digital Twins 그래프에서 맵 ID와 기능 상태 세트 ID를 저장하는 방법

### <a name="prerequisites"></a>사전 요구 사항

* [엔드투엔드 솔루션 연결](./tutorial-end-to-end.md)의 Azure Digital Twins를 참조하세요.
    * 추가 엔드포인트 및 경로를 사용하여 이 트윈을 확장합니다. 또한 이 자습서의 함수 앱에 다른 함수를 추가합니다. 
* [Azure Maps Creator를 사용해서 Indoor Maps 만들기](../azure-maps/tutorial-creator-indoor-maps.md)의 Azure Maps를 따라 *기능 상태 세트* 로 Azure Maps Indoor Maps를 만듭니다.
    * [기능 상태 세트](../azure-maps/creator-indoor-maps.md#feature-statesets)는 방 또는 장비와 같은 데이터 세트 기능에 할당된 동적 속성의 컬렉션(상태)입니다. 위의 Azure Maps 자습서에서 기능 상태 세트는 맵에 표시되는 방 상태를 저장합니다.
    * 기능 *상태 세트 ID* 및 Azure Maps *구독 키* 가 필요합니다.

### <a name="topology"></a>토폴로지

아래 이미지는 이 자습서의 실내 맵 통합 요소가 더 큰 엔드투엔드 Azure Digital Twins 시나리오에 적합한 경우를 보여 줍니다.

:::image type="content" source="media/how-to-integrate-maps/maps-integration-topology.png" alt-text="엔드투엔드 시나리오에서 실내 맵 통합 요소를 강조하는 Azure 서비스의 다이어그램." lightbox="media/how-to-integrate-maps/maps-integration-topology.png":::

## <a name="create-a-function-to-update-a-map-when-twins-update"></a>트윈 업데이트 시 맵을 업데이트하는 함수 만들기

먼저 모든 트윈 업데이트 이벤트를 이벤트 그리드 토픽으로 전달하기 위해 Azure Digital Twins에서 경로를 만듭니다. 그런 다음, 함수를 사용하여 이러한 업데이트 메시지를 읽고 Azure Maps에서 기능 상태 세트를 업데이트합니다. 

## <a name="create-a-route-and-filter-to-twin-update-notifications"></a>트윈 업데이트 알림을 위한 경로 및 필터 만들기

Azure Digital Twins 인스턴스는 트윈의 상태가 업데이트될 때마다 트윈 업데이트 이벤트를 내보낼 수 있습니다. 위에 링크된 Azure Digital Twins [엔드투엔드 솔루션 연결](./tutorial-end-to-end.md)에서는 온도계를 사용하여 방의 트윈에 연결된 온도 특성을 업데이트하는 시나리오를 안내합니다. 트윈에 대한 업데이트 알림을 구독하고 해당 정보를 사용하여 맵을 업데이트함으로써 해당 솔루션을 확장합니다.

이 패턴은 IoT 디바이스가 아닌 방 트윈에서 직접 읽어 매핑 논리를 업데이트할 필요 없이 온도에 대한 기본 데이터 원본을 유연하게 변경할 수 있도록 합니다. 예를 들어 맵 논리를 업데이트할 필요 없이, 여러 개의 온도계를 추가하거나 다른 방과 온도계를 공유하도록 이 방을 설정할 수 있습니다.

1. Azure Digital Twins 인스턴스에서 이벤트를 수신하는 이벤트 그리드 토픽을 만듭니다.
    ```azurecli-interactive
    az eventgrid topic create --resource-group <your-resource-group-name> --name <your-topic-name> --location <region>
    ```

2. 이벤트 그리드 토픽을 Azure Digital Twins에 연결하는 엔드포인트를 만듭니다.
    ```azurecli-interactive
    az dt endpoint create eventgrid --endpoint-name <Event-Grid-endpoint-name> --eventgrid-resource-group <Event-Grid-resource-group-name> --eventgrid-topic <your-Event-Grid-topic-name> --dt-name <your-Azure-Digital-Twins-instance-name>
    ```

3. Azure Digital Twins에서 엔드포인트에 트윈 업데이트 이벤트를 전송하는 경로를 만듭니다.

    >[!NOTE]
    >Cloud Shell에는 `az dt route`, `az dt model`, `az dt twin` 명령 그룹에 영향을 주는 **알려진 문제** 가 있습니다.
    >
    >이 문제를 해결하려면 명령을 실행하기 전에 Cloud Shell에서 `az login`을 실행하거나 Cloud Shell 대신 [로컬 CLI](/cli/azure/install-azure-cli)를 사용합니다. 이에 대한 자세한 내용은 [문제 해결: Azure Digital Twins의 알려진 문제](troubleshoot-known-issues.md#400-client-error-bad-request-in-cloud-shell)를 참조하세요.

    ```azurecli-interactive
    az dt route create --dt-name <your-Azure-Digital-Twins-instance-name> --endpoint-name <Event-Grid-endpoint-name> --route-name <my-route> --filter "type = 'Microsoft.DigitalTwins.Twin.Update'"
    ```

## <a name="create-a-function-to-update-maps"></a>맵을 업데이트하는 함수 만들기

엔드투엔드 자습서([엔드투엔드 솔루션 연결](./tutorial-end-to-end.md))에서 함수 앱 내에 **Event Grid 트리거 함수** 를 만들려고 합니다. 이 함수는 이러한 알림의 압축을 풀고 Azure Maps 기능 상태 세트에 업데이트를 보내서 한 방의 온도를 업데이트합니다.

참조 정보는 [Azure Functions에 대한 Azure Event Grid 트리거](../azure-functions/functions-bindings-event-grid-trigger.md)문서를 참조하세요.

함수 코드를 다음 코드로 바꿉니다. 이렇게 하면 공간 트윈에 대한 업데이트만 필터링하고 업데이트된 온도를 읽고 해당 정보를 Azure Maps로 보냅니다.

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/updateMaps.cs":::

함수 앱에서 두 개의 환경 변수를 설정해야 합니다. 하나는 [Azure Maps 기본 구독 키](../azure-maps/quick-demo-map-app.md#get-the-primary-key-for-your-account)이고 하나는 [Azure Maps 상태 세트 ID](../azure-maps/tutorial-creator-indoor-maps.md#create-a-feature-stateset)입니다.

```azurecli-interactive
az functionapp config appsettings set --name <your-function-app-name> --resource-group <your-resource-group> --settings "subscription-key=<your-Azure-Maps-primary-subscription-key>"
az functionapp config appsettings set --name <your-function-app-name>  --resource-group <your-resource-group> --settings "statesetID=<your-Azure-Maps-stateset-ID>"
```

### <a name="view-live-updates-on-your-map"></a>맵에서 라이브 업데이트 보기

라이브 업데이트 온도를 보려면 다음 단계를 수행합니다.

1. Azure Digital Twins [엔드투엔드 솔루션 연결](tutorial-end-to-end.md)에서 **DeviceSimulator** 프로젝트를 실행하여 시뮬레이션된 IoT 데이터 보내기를 시작합니다. 이에 대한 지침은 [시뮬레이션 구성 및 실행](././tutorial-end-to-end.md#configure-and-run-the-simulation) 섹션에 있습니다.
2. [Azure Maps Indoor 모듈](../azure-maps/how-to-use-indoor-module.md)을 사용하여 Azure Maps Creator에서 만든 실내 맵을 렌더링할 수 있습니다.
    1. [Azure Maps Indoor Maps 모듈 사용](../azure-maps/how-to-use-indoor-module.md)에 있는 Indoor Maps의 [예: Indoor Maps 모듈 사용](../azure-maps/how-to-use-indoor-module.md#example-use-the-indoor-maps-module) 섹션에서 HTML을 로컬 파일에 복사합니다.
    1. 로컬 HTML 파일에서 *subscription key*, *tilesetId*, *statesetID* 를 사용자의 값으로 바꿉니다.
    1. 브라우저에서 파일을 엽니다.

두 샘플 모두 호환 범위로 온도를 보내므로 30초마다 맵에 121호 방 업데이트의 색이 표시됩니다.

:::image type="content" source="media/how-to-integrate-maps/maps-temperature-update.png" alt-text="121호 방이 주황색으로 표시된 사무실 맵의 스크린샷.":::

## <a name="store-your-maps-information-in-azure-digital-twins"></a>Azure Digital Twins에 맵 정보 저장

이제 맵 정보를 업데이트하는 하드 코드된 솔루션이 있으므로 Azure Digital Twins 그래프를 사용하여 실내 맵을 업데이트하는 데 필요한 모든 정보를 저장할 수 있습니다. 여기에는 각 맵과 위치의 상태 세트 ID, 맵 구독 ID, 기능 ID가 포함됩니다. 

이 특정 예제에 대한 솔루션에는 각 최상위 공간에서 상태 세트 ID를 업데이트하고 구독 ID 특성을 매핑하고 기능 ID를 포함하도록 각 방을 업데이트하는 작업이 포함됩니다. 트윈 그래프를 초기화할 때 이러한 값을 한 번 설정한 다음, 각 트윈 업데이트 이벤트에 대해 해당 값을 쿼리해야 합니다.

토폴로지의 구성에 따라 맵의 세분성과 관련하여 서로 다른 수준으로 이러한 세 가지 특성을 저장할 수 있습니다.

## <a name="next-steps"></a>다음 단계

트윈 그래프에서 정보를 관리, 업그레이드, 검색하는 방법에 대한 자세한 내용은 다음 참조를 참조하세요.

* [Digital Twins 관리](./how-to-manage-twin.md)
* [쌍 그래프 쿼리](./how-to-query-graph.md)
