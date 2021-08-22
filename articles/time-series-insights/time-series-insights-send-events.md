---
title: 환경으로 이벤트 보내기 - Azure Time Series Insights | Microsoft Docs
description: 이벤트 허브를 구성하고, 샘플 애플리케이션을 실행하고, Azure Time Series Insights 환경으로 이벤트를 전송하는 방법을 알아봅니다.
ms.service: time-series-insights
services: time-series-insights
author: tedvilutis
ms.author: tvilutis
manager: cnovak
ms.reviewer: orspodek
ms.devlang: csharp
ms.workload: big-data
ms.topic: conceptual
ms.date: 09/30/2020
ms.custom: seodec18
ms.openlocfilehash: b603539f3b4b7178d5929bcf61ee5dec9fe0d25b
ms.sourcegitcommit: 4f185f97599da236cbed0b5daef27ec95a2bb85f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/19/2021
ms.locfileid: "112371273"
---
# <a name="send-events-to-an-azure-time-series-insights-gen1-environment-by-using-an-event-hub"></a>이벤트 허브를 사용하여 Azure Time Series Insights Gen1 환경으로 이벤트 보내기

> [!CAUTION]
> 이는 Gen1 문서입니다.

이 문서는 Azure Event Hubs에서 이벤트 허브를 생성 및 구성하는 방법을 설명합니다. 또한 Event Hubs에서 Azure Time Series Insights로 이벤트를 푸시하는 샘플 애플리케이션을 실행하는 방법을 설명합니다. JSON 형식의 이벤트가 있는 기존 이벤트 허브가 있는 경우 해당 자습서를 건너뛰고 [Azure Time Series Insights](./tutorial-set-up-environment.md)에서 환경을 봅니다.

## <a name="configure-an-event-hub"></a>이벤트 허브 구성

1. 이벤트 허브를 만드는 방법을 알아보려면 [Event Hubs 설명서](../event-hubs/index.yml)를 참조하세요.
1. 검색 상자에서 **Event Hubs** 를 검색합니다. 반환된 목록에서 **Event Hubs** 를 선택합니다.
1. 이벤트 허브를 선택합니다.
1. 이벤트 허브를 만들면 이벤트 허브 네임스페이스가 만들어집니다. 네임스페이스 내에 이벤트 허브를 아직 만들지 않은 경우 메뉴의 **엔터티** 아래에서 이벤트 허브를 만듭니다.

    [![이벤트 허브의 목록](media/send-events/tsi-connect-event-hub-namespace.png)](media/send-events/tsi-connect-event-hub-namespace.png#lightbox)

1. 이벤트 허브를 만든 후 이벤트 허브의 목록에서 선택합니다.
1. 메뉴의 **엔터티** 아래에서 **Event Hubs** 를 선택합니다.
1. 구성할 이벤트 허브의 이름을 선택합니다.
1. **개요** 아래에서 **소비자 그룹** 을 선택한 다음, **Consumer Group** 을 선택합니다.

    [![소비자 그룹 만들기](media/send-events/add-event-hub-consumer-group.png)](media/send-events/add-event-hub-consumer-group.png#lightbox)

1. Azure Time Series Insights 이벤트 원본에서 단독으로 사용하는 소비자 그룹을 만들어야 합니다.

    > [!IMPORTANT]
    > 이 소비자 그룹이 다른 서비스(예: Azure Stream Analytics 작업 또는 다른 Azure Time Series Insights 환경)에서 사용되지 못하도록 해야 합니다. 소비자 그룹을 다른 서비스에서 사용하는 경우 읽기 작업이 이 환경 및 다른 서비스 모두에 부정적인 영향을 미칩니다. 소비자 그룹으로 **$Default** 를 사용하는 경우 다른 읽기 권한자는 소비자 그룹을 잠재적으로 다시 사용할 수 있습니다.

1. 메뉴의 **설정** 아래에서 **공유 액세스 정책** 을 선택한 다음, **추가** 를 선택합니다.

    [![공유 액세스 정책을 선택한 다음, 추가 단추를 선택합니다.](media/send-events/add-shared-access-policy.png)](media/send-events/add-shared-access-policy.png#lightbox)

1. **새 공유 액세스 정책 추가** 창에서 **MySendPolicy** 라는 공유 액세스를 만듭니다. 이 문서 뒷부분의 C# 예제에서 이벤트를 보낼 때 이 공유 액세스 정책을 사용합니다.

    [![정책 이름 상자에 MySendPolicy 입력](media/send-events/configure-shared-access-policy-confirm.png)](media/send-events/configure-shared-access-policy-confirm.png#lightbox)

1. **클레임** 아래에 **보내기** 확인란을 선택합니다.

## <a name="add-an-azure-time-series-insights-instance"></a>Azure Time Series Insights 인스턴스를 추가

Azure Time Series Insights Gen2에서 TSM(시계열 모델)을 사용하여 들어오는 원격 분석에 컨텍스트 데이터를 추가할 수 있습니다. TSM에서는 태그나 신호를 ‘인스턴스’라고 하며, ‘인스턴스 필드’에 컨텍스트 데이터를 저장할 수 있습니다.  데이터는 **시계열 ID** 를 사용하여 쿼리 시에 조인됩니다. 이 문서의 뒷부분에서 사용할 샘플 가상 프로젝트에 대한 **시계열 ID** 는 `id`입니다. 인스턴스 필드에 데이터를 저장하는 방법에 대한 자세한 내용은 [시계열 모델](./concepts-model-overview.md) 개요를 참조하세요.

### <a name="create-an-azure-time-series-insights-event-source"></a>Azure Time Series Insights 이벤트 원본 만들기

1. 아직 이벤트 원본을 만들지 않은 경우 [이벤트 원본 만들기](./how-to-ingest-data-event-hub.md)에 대한 단계를 완료합니다.

1. `timeSeriesId`에 대한 값을 설정합니다. **시계열 ID** 에 대해 자세히 알아보려면 [시계열 모델](./concepts-model-overview.md)을 참조하세요.

### <a name="push-events-to-windmills-sample"></a>가상 샘플로 이벤트 푸시하기

1. 검색 창에서 **Event Hubs** 를 검색합니다. 반환된 목록에서 **Event Hubs** 를 선택합니다.

1. 이벤트 허브 인스턴스를 선택합니다.

1. **공유 액세스 정책** > **MySendPolicy** 로 이동합니다. **연결 문자열 - 기본 키** 의 값을 복사합니다.

    [![기본 키 연결 문자열의 값 복사](media/send-events/configure-sample-code-connection-string.png)](media/send-events/configure-sample-code-connection-string.png#lightbox)

1. [https://editor.swagger.io](<https://tsiclientsample.azurewebsites.net/windFarmGen.html>) 로 이동합니다. URL은 시뮬레이션된 가상 디바이스를 만들고 실행합니다.
1. 웹 페이지의 **이벤트 허브 연결 문자열** 상자에, [가상 입력 필드](#push-events-to-windmills-sample)에서 복사한 연결 문자열을 붙여넣습니다.

    [![이벤트 허브 연결 문자열 상자에 기본 키 연결 문자열 붙여넣기](media/send-events/configure-wind-mill-sim.png)](media/send-events/configure-wind-mill-sim.png#lightbox)

1. **시작하려면 클릭** 을 선택합니다.

    > [!TIP]
    > 또한 가상 시뮬레이터는 [Azure Time Series Insights GA 쿼리 APIs](/rest/api/time-series-insights/gen1-query)를 사용하여 페이로드로 사용할 수 있는 JSON을 만듭니다.

    > [!NOTE]
    > 시뮬레이터는 브라우저 탭이 닫힐 때까지 계속해서 데이터를 전송합니다.

1. Azure Portal에서 이벤트 허브로 돌아갑니다. **개요** 페이지에 이벤트 허브에서 수신한 새 이벤트가 표시됩니다.

    [![이벤트 허브에 대한 메트릭을 보여주는 이벤트 허브 개요 페이지](media/send-events/review-windmill-telemetry.png)](media/send-events/review-windmill-telemetry.png#lightbox)

## <a name="supported-json-shapes"></a>지원되는 JSON 셰이프

### <a name="example-one"></a>예 1

* **입력**: JSON 개체 하나.

    ```JSON
    {
        "id":"device1",
        "timestamp":"2016-01-08T01:08:00Z"
    }
    ```

* **출력**: 하나의 이벤트.

    |id|timestamp|
    |--------|---------------|
    |device1|2016-01-08T01:08:00Z|

### <a name="example-two"></a>예 2

* **입력**: 두 개의 JSON 개체가 포함된 JSON 배열. 각 JSON 개체는 이벤트로 변환됩니다.

    ```JSON
    [
        {
            "id":"device1",
            "timestamp":"2016-01-08T01:08:00Z"
        },
        {
            "id":"device2",
            "timestamp":"2016-01-17T01:17:00Z"
        }
    ]
    ```

* **출력**: 두 개의 이벤트.

    |id|timestamp|
    |--------|---------------|
    |device1|2016-01-08T01:08:00Z|
    |device2|2016-01-08T01:17:00Z|

### <a name="example-three"></a>예 3

* **입력**: 두 개의 JSON 개체를 담고 있는 중첩된 JSON 배열이 포함된 JSON 개체.

    ```JSON
    {
        "location":"WestUs",
        "events":[
            {
                "id":"device1",
                "timestamp":"2016-01-08T01:08:00Z"
            },
            {
                "id":"device2",
                "timestamp":"2016-01-17T01:17:00Z"
            }
        ]
    }
    ```

* **출력**: 두 개의 이벤트. **location** 속성은 각 이벤트로 복사됩니다.

    |위치|events.id|events.timestamp|
    |--------|---------------|----------------------|
    |WestUs|device1|2016-01-08T01:08:00Z|
    |WestUs|device2|2016-01-08T01:17:00Z|

### <a name="example-four"></a>예 4

* **입력**: 두 개의 JSON 개체를 담고 있는 중첩된 JSON 배열이 포함된 JSON 개체. 이 입력은 복합 JSON 개체로 글로벌 속성을 표시할 수 있음을 보여줍니다.

    ```JSON
    {
        "location":"WestUs",
        "manufacturer":{
            "name":"manufacturer1",
            "location":"EastUs"
        },
        "events":[
            {
                "id":"device1",
                "timestamp":"2016-01-08T01:08:00Z",
                "data":{
                    "type":"pressure",
                    "units":"psi",
                    "value":108.09
                }
            },
            {
                "id":"device2",
                "timestamp":"2016-01-17T01:17:00Z",
                "data":{
                    "type":"vibration",
                    "units":"abs G",
                    "value":217.09
                }
            }
        ]
    }
    ```

* **출력**: 두 개의 이벤트.

    |위치|manufacturer.name|manufacturer.location|events.id|events.timestamp|events.data.type|events.data.type|events.data.type|
    |---|---|---|---|---|---|---|---|
    |WestUs|manufacturer1|EastUs|device1|2016-01-08T01:08:00Z|압력|psi|108.09|
    |WestUs|manufacturer1|EastUs|device2|2016-01-08T01:17:00Z|vibration|abs G|217.09|

## <a name="next-steps"></a>다음 단계

* Azure Time Series Insights 탐색기에서 [나의 환경](https://insights.timeseries.azure.com)을 확인합니다.

* [IoT Hub 디바이스 메시지](../iot-hub/iot-hub-devguide-messages-construct.md)에 대해 자세히 알아보기