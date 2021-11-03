---
title: '자습서: IoT 공간 분석 구현 | Microsoft Azure Maps'
description: IoT Hub를 Microsoft Azure Maps 서비스 API와 통합하는 방법에 대한 자습서
author: stevemunk
ms.author: v-munksteve
ms.date: 10/28/2021
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
ms.custom: mvc
ms.openlocfilehash: 7ab98fa40ddc2321f9640d2e7451fc5c55064580
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131455362"
---
# <a name="tutorial-implement-iot-spatial-analytics-by-using-azure-maps"></a>자습서: Azure Maps를 사용하여 IoT 공간 분석 구현

IoT 시나리오에서는 일반적으로 시간과 공간에서 발생하는 관련 이벤트를 캡처하고 추적합니다. 차량 관리, 자산 추적, 모바일 및 스마트 도시 애플리케이션을 예로 들 수 있습니다. 이 자습서에서는 Azure Maps API를 사용하여 중고차 렌탈 이동을 추적하는 솔루션을 안내합니다.

이 자습서에서는 다음 작업을 수행합니다.

> [!div class="checklist"]
>
> * 차량 추적 데이터를 기록하기 위해 Azure 스토리지 계정을 만듭니다.
> * 데이터 업로드 API를 사용하여 Azure Maps Data Service에 지오펜스를 업로드합니다.
> * Azure IoT Hub에 허브를 만들고 디바이스를 등록합니다.
> * Azure Maps 공간 분석을 기반으로 하는 비즈니스 논리를 구현하는 Azure Functions에서 함수를 만듭니다.
> * Azure Event Grid를 통해 Azure 함수에서 IoT 디바이스 원격 분석 이벤트를 구독합니다.
> * IoT Hub 메시지 라우팅을 사용하여 원격 분석 이벤트를 필터링합니다.

## <a name="prerequisites"></a>필수 조건

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.

2. [Azure Maps 계정을 만듭니다](quick-demo-map-app.md#create-an-azure-maps-account).

3. 기본 키 또는 구독 키라고도 하는 [기본 구독 키를 가져옵니다](quick-demo-map-app.md#get-the-primary-key-for-your-account). 자세한 내용은 [Azure Maps의 인증 관리](how-to-manage-authentication.md)를 참조하세요.

4. [리소스 그룹을 만듭니다](../azure-resource-manager/management/manage-resource-groups-portal.md#create-resource-groups). 이 자습서에서는 리소스 그룹 이름을 *ContosoRental* 이라고 지정하지만 원하는 이름을 선택할 수 있습니다.

5. [rentalCarSimulation C# 프로젝트](https://github.com/Azure-Samples/iothub-to-azure-maps-geofencing/tree/master/src/rentalCarSimulation)를 다운로드합니다.

이 자습서에서는 [Postman](https://www.postman.com/) 애플리케이션을 사용하지만, 다른 API 개발 환경을 선택해도 됩니다.

## <a name="use-case-rental-car-tracking"></a>사용 사례: 렌터카 추적

자동차 렌트 회사에서 렌터카의 위치 정보, 주행 거리 및 작동 상태를 기록하려고 합니다. 또한 이 회사는 차량이 정확히 허가된 지역을 벗어날 때마다 이 정보를 저장하려고 합니다.

렌터카에는 원격 분석 데이터를 IoT Hub로 정기적으로 보내는 IoT 디바이스가 장착되어 있습니다. 원격 분석에는 현재 위치가 포함되며, 자동차의 엔진이 작동하고 있는지 여부가 표시됩니다. 디바이스 위치 스키마는 [지리 공간 데이터에 대한 IoT 플러그 앤 플레이 스키마](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v1-preview/schemas/geospatial.md)를 준수합니다. 렌터카의 디바이스 원격 분석 스키마는 다음 JSON 코드와 같습니다.

```JSON
{
    "data": {
        "properties": {
            "Engine": "ON"
        },
        "systemProperties": {
            "iothub-content-type": "application/json",
            "iothub-content-encoding": "utf-8",
            "iothub-connection-device-id": "ContosoRentalDevice",
            "iothub-connection-auth-method": "{\"scope\":\"device\",\"type\":\"sas\",\"issuer\":\"iothub\",\"acceptingIpFilterRule\":null}",
            "iothub-connection-auth-generation-id": "636959817064335548",
            "iothub-enqueuedtime": "2019-06-18T00:17:20.608Z",
            "iothub-message-source": "Telemetry"
        },
        "body": {
            "location": {
                "type": "Point",
                "coordinates": [ -77.025988698005662, 38.9015330523316 ]
            }
        }
    }
}
```

이 자습서에서는 한 대의 차량만 추적합니다. Azure 서비스를 설정한 후 [rentalCarSimulation C# 프로젝트](https://github.com/Azure-Samples/iothub-to-azure-maps-geofencing/tree/master/src/rentalCarSimulation)를 다운로드하여 차량 시뮬레이터를 실행해야 합니다. 이벤트에서 함수 실행에 이르는 전체 프로세스는 다음 단계로 요약됩니다.

1. 차량 내 디바이스는 원격 분석 데이터를 IoT Hub로 보냅니다.

2. 자동차 엔진이 작동 중인 경우 허브는 원격 분석 데이터를 Event Grid에 게시합니다.

3. 디바이스 원격 분석 이벤트에 대한 이벤트 구독으로 인해 Azure 함수가 트리거됩니다.

4. 이 함수는 차량 디바이스 위치 좌표, 이벤트 시간 및 디바이스 ID를 기록합니다. 그런 다음, [Spatial Geofence Get API](/rest/api/maps/spatial/getgeofence)를 사용하여 지오펜스 외부에서 자동차를 주행했는지 확인합니다. 지오펜스 경계를 벗어나 주행한 경우 함수는 이벤트에서 수신한 위치 데이터를 Blob 컨테이너에 저장합니다. 또한 이 함수는 [역방향 주소 검색](/rest/api/maps/search/getsearchaddressreverse)을 쿼리하여 좌표 위치를 주소로 변환하고 나머지 디바이스 위치 데이터와 함께 저장합니다.

다음 다이어그램에서는 시스템에 대한 개략적인 개요를 보여줍니다.

   :::image type="content" source="./media/tutorial-iot-hub-maps/system-diagram.png" border="false" alt-text="시스템 개요 다이어그램":::

다음 그림에는 지오펜스 영역이 파란색으로 표시되어 있습니다. 렌터카의 경로는 녹색 선으로 표시되어 있습니다.

   :::image type="content" source="./media/tutorial-iot-hub-maps/geofence-route.png" border="false" alt-text="지오펜스 경로를 보여주는 그림":::

## <a name="create-an-azure-storage-account"></a>Azure Storage 계정 만들기

차량 위치 이탈 추적 데이터를 저장하기 위해 리소스 그룹에 [범용 v2 스토리지 계정](../storage/common/storage-account-overview.md)을 만듭니다. 리소스 그룹을 만들지 않은 경우 [ 리소스 그룹 만들기](../azure-resource-manager/management/manage-resource-groups-portal.md#create-resource-groups)의 지침을 따릅니다. 이 자습서에서는 리소스 그룹 이름을 *ContosoRental* 이라고 지정합니다.

스토리지 계정을 만들려면 [스토리지 계정 만들기](../storage/common/storage-account-create.md?tabs=azure-portal)의 지침을 따릅니다. 이 자습서에서는 스토리지 계정 이름을 *contosorentalstorage* 라고 지정하지만, 일반적으로 원하는 대로 이름을 지정할 수 있습니다.

스토리지 계정을 성공적으로 만든 후에는 로깅 데이터를 저장할 컨테이너를 만들어야 합니다.

1. 새로 만든 스토리지 계정으로 이동합니다. **기본 정보** 섹션에서 **컨테이너** 링크를 클릭합니다.

    :::image type="content" source="./media/tutorial-iot-hub-maps/containers.png" alt-text="Blob 스토리지용 컨테이너의 스크린샷":::

2. 왼쪽 위 모서리에서 **+ 컨테이너** 를 선택합니다. 브라우저의 오른쪽에 패널이 나타납니다. 컨테이너의 이름을 *contoso-rental-logs* 라고 지정하고 **만들기** 를 선택합니다.

     :::image type="content" source="./media/tutorial-iot-hub-maps/container-new.png" alt-text="Blob 컨테이너 만들기 스크린샷":::

3. 스토리지 계정의 **액세스 키** 창으로 이동하여 **key1** 섹션의 **스토리지 계정 이름** 과 **키** 값을 복사합니다. "Azure 함수 만들기 및 Event Grid 구독 추가" 섹션에서 두 값이 모두 필요합니다.

    :::image type="content" source="./media/tutorial-iot-hub-maps/access-keys.png" alt-text="스토리지 계정 이름 및 키 복사 스크린샷":::

## <a name="upload-a-geofence"></a>지오펜스 업로드

다음으로, [Postman 앱](https://www.getpostman.com)을 사용하여 Azure Maps에 [지오펜스를 업로드](./geofence-geojson.md)합니다. 지오펜스는 렌트 차량에 허가된 지리적 영역을 정의합니다. Azure 함수에 지오펜스를 사용하여 자동차가 지오펜스 영역 밖으로 이동했는지 여부를 확인합니다.

다음 단계에 따라 Azure Maps 데이터 업로드 API를 사용하여 지오펜스를 업로드합니다.

1. Postman 앱을 열고 **새로 만들기** 를 다시 선택합니다. **새로 만들기** 창에서 **HTTP 요청** 을 선택하고, 해당 요청의 이름을 입력합니다.

2. 작성기 탭에서 **POST** HTTP 메서드를 선택하고, 다음 URL을 입력하여 데이터 업로드 API에 지오펜스를 업로드합니다. `{Your-Azure-Maps-Primary-Subscription-key}`를 기본 구독 키로 바꿔야 합니다.

    ```HTTP
    https://us.atlas.microsoft.com/mapData?subscription-key={Your-Azure-Maps-Primary-Subscription-key}&api-version=2.0&dataFormat=geojson
    ```

    URL 경로에서 `dataFormat` 매개 변수에 대한 `geojson` 값은 업로드되는 데이터의 형식을 나타냅니다.

3. 입력 형식으로 **본문** > **원시** 를 선택하고, 드롭다운 목록에서 **JSON** 을 선택합니다. [JSON 데이터 파일을 열고](https://raw.githubusercontent.com/Azure-Samples/iothub-to-azure-maps-geofencing/master/src/Data/geofence.json?token=AKD25BYJYKDJBJ55PT62N4C5LRNN4) JSON을 본문 섹션에 복사합니다. **보내기** 를 선택합니다.

4. **보내기** 를 선택하고 요청이 처리될 때까지 기다립니다. 요청이 완료되면 응답의 **헤더** 탭으로 이동합니다. **Operation-Location** 키의 값인 `status URL`을 복사합니다.

    ```http
    https://us.atlas.microsoft.com/mapData/operations/{operationId}?api-version=2.0
    ```

5. API 호출의 상태를 확인하려면 `status URL`에 대한 **GET** HTTP 요청을 만듭니다. 인증을 위해 기본 구독 키를 URL에 추가해야 합니다. **GET** 요청은 다음 URL과 같습니다.

   ```HTTP
   https://us.atlas.microsoft.com/mapData/{operationId}/status?api-version=2.0&subscription-key={Your-Azure-Maps-Primary-Subscription-key}
   ```

6. 요청이 성공적으로 완료되면 응답 창에서 **헤더** 탭을 선택합니다. **Resource-Location** 키의 값인 `resource location URL`을 복사합니다.  `resource location URL`에는 업로드된 데이터의 고유 식별자(`udid`)가 포함됩니다. 이 자습서에서 나중에 사용할 수 있도록 `udid`를 복사합니다.

    :::image type="content" source="./media/tutorial-iot-hub-maps/resource-location-url.png" alt-text="리소스 위치 URL을 복사합니다.":::

## <a name="create-an-iot-hub"></a>IoT 허브 만들기

IoT Hub는 IoT 애플리케이션과 이 애플리케이션이 관리하는 디바이스 간에 안전하고 안정적인 양방향 통신을 지원합니다. 이 자습서에서는 차량 내 디바이스에서 정보를 가져와서 렌터카의 위치를 확인하려고 합니다. 이 섹션에서는 *ContosoRental* 리소스 그룹 내에 IoT 허브를 만듭니다. 이 허브는 디바이스 원격 분석 이벤트 게시를 담당합니다.

*ContosoRental* 리소스 그룹에서 IoT 허브를 만들려면 [IoT 허브 만들기](../iot-develop/quickstart-send-telemetry-iot-hub.md?pivots=programming-language-csharp#create-an-iot-hub)의 단계를 따릅니다.

## <a name="register-a-device-in-your-iot-hub"></a>IoT 허브에 디바이스 등록

디바이스가 IoT 허브 ID 레지스트리에 등록되지 않으면 IoT 허브에 연결할 수 없습니다. 여기서는 *InVehicleDevice* 라는 이름으로 단일 디바이스를 만듭니다. IoT 허브 내에서 디바이스를 만들고 등록하려면 [IoT 허브에서 새 디바이스 등록](../iot-hub/iot-hub-create-through-portal.md#register-a-new-device-in-the-iot-hub)의 단계를 따릅니다. 디바이스의 기본 연결 문자열을 복사해 둡니다. 나중에 필요합니다.

## <a name="create-a-function-and-add-an-event-grid-subscription"></a>함수 만들기 및 Event Grid 구독 추가

Azure Functions는 컴퓨팅 인프라를 명시적으로 프로비저닝하거나 관리할 필요 없이 작은 코드("함수")를 실행할 수 있는 서버리스 컴퓨팅 서비스입니다. 자세한 내용은 [Azure Functions](../azure-functions/functions-overview.md)를 참조하세요.

함수는 특정 이벤트에 의해 "트리거"됩니다. 여기서는 Event Grid 트리거로 트리거되는 함수를 만듭니다. IoT Hub 디바이스 원격 분석 이벤트에 대한 이벤트 구독을 만들어서 트리거와 함수 간의 관계를 만듭니다. 디바이스 원격 분석 이벤트가 발생하면 함수가 엔드포인트로 호출되고, 이전에 IoT Hub에 등록한 디바이스와 관련된 데이터를 받습니다.

다음은 [함수에 포함할 C# 스크립트 코드](https://github.com/Azure-Samples/iothub-to-azure-maps-geofencing/blob/master/src/Azure%20Function/run.csx)입니다.

이제 Azure 함수를 설정합니다.

1. Azure Portal 대시보드에서 **리소스 만들기** 를 선택합니다. 검색 텍스트 상자에 **함수 앱** 을 입력합니다. **함수 앱** > **만들기** 를 선택합니다.

1. **함수 앱** 만들기 페이지에서 함수 앱의 이름을 지정합니다. **리소스 그룹** 의 드롭다운 목록에서 **ContosoRental** 을 선택합니다. **런타임 스택** 으로 **.NET** 을 선택합니다. **버전** 으로 **3.1** 을 선택합니다.  페이지 맨 아래에서 **다음: 호스팅 >** 을 선택합니다.

    :::image type="content" source="./media/tutorial-iot-hub-maps/rental-app.png" alt-text="함수 앱 만들기 스크린샷":::

1. **스토리지 계정** 에서 [Azure 스토리지 계정 만들기](#create-an-azure-storage-account)에서 만든 스토리지 계정을 선택합니다. **검토 + 만들기** 를 선택합니다.

1. 함수 앱 세부 정보를 검토하고, **만들기** 를 선택합니다.

1. 앱이 만들어지면 앱에 함수를 추가합니다. 함수 앱으로 돌아갑니다. **함수** 창으로 이동합니다. 페이지 맨 위에서 **+ 추가** 를 선택합니다. 함수 템플릿 패널이 나타납니다. 패널을 아래로 스크롤하여 **Azure Event Grid 트리거** 를 선택합니다.

     >[!IMPORTANT]
    > **Azure Event Hub 트리거** 와 **Azure Event Grid 트리거** 템플릿은 이름이 비슷합니다. **Azure Event Grid 트리거** 템플릿을 선택합니다.

    :::image type="content" source="./media/tutorial-iot-hub-maps/function-create.png" alt-text="함수 만들기 스크린샷":::

1. 함수에 이름을 지정합니다. 이 자습서에서는 *GetGeoFunction* 이라는 이름을 사용하지만, 일반적으로 원하는 이름을 사용할 수 있습니다. **함수 만들기** 를 선택합니다.

1. 왼쪽 메뉴에서 **코드 + 테스트** 창을 선택합니다. [C# 스크립트](https://github.com/Azure-Samples/iothub-to-azure-maps-geofencing/blob/master/src/Azure%20Function/run.csx)를 복사하여 코드 창에 붙여넣습니다.

     :::image type="content" source="./media/tutorial-iot-hub-maps/function-code.png" alt-text="복사/함수 창에 코드 붙여넣기 스크린샷":::

1. C# 코드에서 다음 매개 변수를 바꿉니다.
    * **SUBSCRIPTION_KEY** 를 Azure Maps 계정 기본 구독 키로 바꿉니다.
    * **UDID** 를 [지오펜스 업로드](#upload-a-geofence)에서 업로드한 지오펜스의 `udid`로 바꿉니다.
    * 스크립트의 `CreateBlobAsync` 함수는 이벤트당 Blob을 데이터 스토리지 계정에 만듭니다. **ACCESS_KEY**, **ACCOUNT_NAME** 및 **STORAGE_CONTAINER_NAME** 을 스토리지 계정의 액세스 키, 계정 이름 및 데이터 스토리지 컨테이너로 바꿉니다. 이러한 값은 [Azure 스토리지 계정 만들기](#create-an-azure-storage-account)에서 스토리지 계정을 만들 때 생성되었습니다.

1. 왼쪽 메뉴에서 **통합** 창을 선택합니다. 다이어그램에서 **Event Grid 트리거** 를 선택합니다. 트리거 이름(예: *eventGridEvent*)을 입력하고 **Event Grid 구독 만들기** 를 선택합니다.

     :::image type="content" source="./media/tutorial-iot-hub-maps/function-integration.png" alt-text="이벤트 구독 추가 스크린샷":::

1. 구독 세부 정보를 입력합니다. 이벤트 구독에 이름을 지정합니다. **이벤트 스키마** 에 대해 **Event Grid 스키마** 를 선택합니다. **토픽 유형** 에는 **Azure IoT Hub 계정** 을 선택합니다. **리소스 그룹** 의 경우 이 자습서의 시작 부분에서 만든 리소스 그룹을 선택합니다. **리소스** 의 경우 "Azure IoT 허브 만들기"에서 만든 IoT 허브를 선택합니다. **이벤트 형식 필터** 에는 **디바이스 원격 분석** 을 선택합니다.

   이러한 옵션을 선택하면 **토픽 유형** 이 **IoT Hub** 로 변경됩니다. **시스템 토픽 이름** 에는 리소스와 동일한 이름을 사용할 수 있습니다. 마지막으로 **엔드포인트 세부 정보** 섹션에서 **엔드포인트 선택** 을 선택합니다. 모든 설정을 적용하고 **선택 확인** 을 선택합니다.

    :::image type="content" source="./media/tutorial-iot-hub-maps/function-create-event-subscription.png" alt-text="이벤트 구독 만들기 스크린샷":::

1. 설정을 검토합니다. 엔드포인트가 이 섹션의 시작 부분에서 만든 함수를 지정하는지 확인합니다. **만들기** 를 선택합니다.

    :::image type="content" source="./media/tutorial-iot-hub-maps/function-create-event-subscription-confirm.png" alt-text="이벤트 구독 만들기 확인 스크린샷":::

1. 이제 **트리거 편집** 패널로 돌아왔습니다. **저장** 을 선택합니다.

## <a name="filter-events-by-using-iot-hub-message-routing"></a>IoT Hub 메시지 라우팅을 사용하여 이벤트 필터링

Event Grid 구독을 Azure 함수에 추가하면 지정된 IoT 허브에 메시지 경로가 자동으로 만들어집니다. 메시지 라우팅을 사용하면 다양한 데이터 형식을 다양한 엔드포인트로 라우팅할 수 있습니다. 예를 들어 디바이스 원격 분석 메시지, 디바이스 수명 주기 이벤트 및 디바이스 쌍 변경 이벤트를 라우팅할 수 있습니다. 자세한 내용은 [IoT Hub 메시지 라우팅 사용](../iot-hub/iot-hub-devguide-messages-d2c.md)을 참조하세요.

:::image type="content" source="./media/tutorial-iot-hub-maps/hub-route.png" alt-text="IoT 허브의 메시지 라우팅 스크린샷":::

예제 시나리오에서는 렌터카가 이동할 때만 메시지를 수신하려고 합니다. `Engine` 속성이 **"ON"** 인 이벤트를 필터링하는 라우팅 쿼리를 만듭니다. 라우팅 쿼리를 만들려면 **RouteToEventGrid** 경로를 선택하고, **라우팅 쿼리** 를 **"Engine='ON'"** 으로 바꿉니다. 그런 다음 **저장** 을 선택합니다. 이제 IoT 허브는 엔진이 켜진 디바이스 원격 분석만 게시합니다.

:::image type="content" source="./media/tutorial-iot-hub-maps/hub-filter.png" alt-text="라우팅 메시지 필터링 스크린샷":::

>[!TIP]
>IoT 디바이스-클라우드 메시지를 쿼리하는 다양한 방법이 있습니다. 메시지 라우팅에 대한 자세한 내용은 [IoT Hub 메시지 라우팅 사용](../iot-hub/iot-hub-devguide-routing-query-syntax.md)을 참조하세요.

## <a name="send-telemetry-data-to-iot-hub"></a>IoT Hub에 원격 분석 데이터 보내기

이제 Azure 함수가 실행 중이면 원격 분석 데이터를 IoT 허브로 보낼 수 있습니다. 그러면 허브에서 원격 분석 데이터를 Event Grid로 라우팅합니다. C# 애플리케이션을 사용하여 렌터카의 차량 내부 디바이스에 대한 위치 데이터를 시뮬레이션합니다. 애플리케이션을 실행하려면 개발 컴퓨터에 .NET Core SDK 2.1.0 이상이 필요합니다. 다음 단계에 따라 시뮬레이션된 원격 분석 데이터를 IoT 허브에 보냅니다.

1. [rentalCarSimulation](https://github.com/Azure-Samples/iothub-to-azure-maps-geofencing/tree/master/src/rentalCarSimulation) C# 프로젝트를 아직 다운로드하지 않았으면 다운로드합니다.

2. 원하는 텍스트 편집기에서 `simulatedCar.cs` 파일을 열고, `connectionString`의 값을 디바이스 등록 시 저장한 값으로 바꿉니다. 파일의 변경 내용을 저장합니다.

3. 머신에 .NET Core가 설치되어 있는지 확인합니다. 로컬 터미널 창에서 C# 프로젝트의 루트 폴더로 이동하고, 다음 명령을 실행하여 시뮬레이션된 디바이스 애플리케이션에 필요한 패키지를 설치합니다.

    ```cmd/sh
    dotnet restore
    ```

4. 동일한 터미널에서 다음 명령을 실행하여 렌터카 시뮬레이션 애플리케이션을 빌드하고 실행합니다.

    ```cmd/sh
    dotnet run
    ```

  로컬 터미널은 다음과 같습니다.

:::image type="content" source="./media/tutorial-iot-hub-maps/terminal.png" alt-text="터미널 출력 스크린샷":::

이제 Blob 스토리지 컨테이너를 열면 차량이 지오펜스 외부에 있었던 위치에 대한 4개의 Blob을 볼 수 있습니다.

:::image type="content" source="./media/tutorial-iot-hub-maps/blob.png" alt-text="컨테이너 내부의 Blob 보기 스크린샷":::

다음 맵은 지오펜스 외부에 있는 차량 네 대의 위치 지점을 보여줍니다. 각 위치는 일정한 시간 간격으로 기록되었습니다.

:::image type="content" source="./media/tutorial-iot-hub-maps/violation-map.png" alt-text="위치 이탈 맵 스크린샷":::

## <a name="explore-azure-maps-and-iot"></a>Azure Maps 및 IoT 살펴보기

이 자습서에서 사용되는 Azure Maps API를 살펴보려면 다음을 참조하세요.

* [Get Search Address Reverse](/rest/api/maps/search/getsearchaddressreverse)
* [지오펜스 가져오기](/rest/api/maps/spatial/getgeofence)

Azure Maps REST API의 전체 목록은 다음을 참조하세요.

* [Azure Maps REST API](/rest/api/maps/spatial/getgeofence)

* [IoT 플러그 앤 플레이](../iot-develop/index.yml)

IoT용 Azure Certified인 디바이스 목록을 가져오려면 다음을 방문하세요.

* [Azure Certified 디바이스](https://devicecatalog.azure.com/)

## <a name="clean-up-resources"></a>리소스 정리

정리가 필요한 리소스가 없습니다.

## <a name="next-steps"></a>다음 단계

디바이스에서 클라우드로 또는 그 반대로 원격 분석을 보내는 방법에 대한 자세한 내용은 다음을 참조하세요.

> [!div class="nextstepaction"]
> [디바이스에서 원격 분석 데이터 보내기](../iot-develop/quickstart-send-telemetry-iot-hub.md?pivots=programming-language-csharp)
