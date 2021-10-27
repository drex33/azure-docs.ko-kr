---
title: Azure Digital Twins란?
titleSuffix: Azure Digital Twins
description: Azure Digital Twins 개요, 서비스 구성 및 더 광범위한 클라우드 솔루션에서 사용할 수 있는 방법에 대해 설명합니다.
author: baanders
ms.author: baanders
ms.date: 10/5/2021
ms.topic: overview
ms.service: digital-twins
ms.openlocfilehash: 818f001666baa55c1c015974932a5d291a82614d
ms.sourcegitcommit: 147910fb817d93e0e53a36bb8d476207a2dd9e5e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/18/2021
ms.locfileid: "130134149"
---
# <a name="what-is-azure-digital-twins"></a>Azure Digital Twins란?

**Azure Digital Twins** 는 건물, 공장, 농장, 에너지 네트워크, 철도, 경기장 등의 전체 환경(전체 도시)의 디지털 모델을 기반으로 트윈 그래프를 생성할 수 있는 PaaS(Platform as a Service) 제품입니다. 이러한 디지털 모델을 사용하 여 더 나은 제품, 최적화된 작업, 비용 절감 및 혁신적인 고객 환경을 구동하는 정보를 얻을 수 있습니다.

Azure Digital Twins는 광범위한 클라우드 솔루션에서 실제 IoT 디바이스를 나타내는 디지털 트윈 아키텍처를 설계하는 데 사용할 수 있으며, IoT Hub 디바이스 쌍에 연결하여 실시간 데이터를 주고 받을 수 있습니다.

> [!NOTE]
> *IoT Hub 디지털 트윈* 은 Azure Digital Twins 서비스의 *디바이스 쌍* 과 다릅니다. *IoT Hub 디바이스 쌍* 은 연결하는 각 IoT 디바이스에 대해 IoT 허브에 의해 유지 관리되는 반면 *디지털 트윈* 은 디지털 모델에 의해 정의되고 Azure Digital Twins 내에서 인스턴스화된 모든 항목을 표현할 수 있습니다. 

Azure Digital Twins 위에 도메인 전문 지식을 활용하여 다음과 같은 사용자 지정, 연결된 솔루션을 빌드합니다:
* 모든 환경을 모델링하고 디지털 쌍을 확장 가능하고 안전한 방식으로 생활 속으로 가져옵니다.
* IoT 장치 및 기존 비즈니스 시스템과 같은 자산과 연결합니다.
* 강력한 이벤트 시스템을 사용하여 동적 비즈니스 논리 및 데이터 처리를 빌드합니다.
* Azure 데이터, 분석 및 AI 서비스와 통합하여 과거를 추적하고 미래를 예측하는 데 도움을 줍니다.

## <a name="azure-digital-twins-capabilities"></a>Azure Digital Twins 기능

Azure Digital Twins에서 제공하는 기능에 대한 요약은 다음과 같습니다.

### <a name="open-modeling-language"></a>오픈 모델링 언어

Azure Digital Twins에서 [모델](concepts-models.md)이라는 사용자 지정 쌍 유형을 사용하여 실제 환경에서 사람, 장소 및 사물을 나타내는 디지털 엔터티를 정의합니다. 

이러한 모델 정의를 비즈니스를 설명하는 특수 어휘로 간주할 수 있습니다. 예를 들어 빌딩 관리 솔루션의 경우 빌딩, 층 및 엘리베이터와 같은 모델을 정의할 수 있습니다. 그런 다음 이러한 모델을 기반으로 한 **디지털 쌍** 을 만들어 특정 환경을 나타낼 수 있습니다.

[!INCLUDE [digital-twins-versus-device-twins](../../includes/digital-twins-versus-device-twins.md)]

모델은 [DTDL(Digital Twins 정의 언어)](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md)라는 JSON과 같은 언어로 정의되며, 상태 속성, 원격 분석 이벤트, 명령, 구성 요소 및 관계에 따라 쌍을 설명합니다.
* 모델은 엔터티 간에 의미 체계 **관계** 를 정의하여 해당 상호 작용을 반영하는 그래프에 트윈을 연결할 수 있도록 합니다. 모델을 세계 설명에서 명사로, 관계를 동사로 간주할 수 있습니다.
* 또한 모델 상속을 사용하여 쌍을 특수화할 수도 있습니다. 한 모델은 다른 모델에서 상속할 수 있습니다.

DTDL은 [IoT 플러그 앤 플레이](../iot-develop/overview-iot-plug-and-play.md) 및 [TSI(Time Series Insights)](../time-series-insights/overview-what-is-tsi.md)를 비롯한 다른 Azure IoT 서비스 전체에서 데이터 모델에 사용됩니다. 이 공통점 유형을 통해 Azure Digital Twins 솔루션을 Azure 에코시스템의 다른 부분과 연결하고 호환되도록 유지할 수 있습니다.

### <a name="live-execution-environment"></a>라이브 실행 환경

Azure Digital Twins의 디지털 모델은 실제 세계의 실시간, 최신 표현입니다. 사용자 지정 DTDL 모델에서 관계를 사용하 여 사용자 환경을 나타내는 **실시간 그래프** 에 쌍을 연결합니다.

그래프와 상호 작용하는 다음 인터페이스를 제공하는 [Azure Digital Twins Explorer](concepts-azure-digital-twins-explorer.md)에서 Azure Digital Twins 그래프를 시각화할 수 있습니다.

:::image type="content" source="media/concepts-azure-digital-twins-explorer/azure-digital-twins-explorer-demo.png" alt-text="디지털 트윈을 나타내는 노드의 그래프를 보여 주는 Azure Digital Twins Explorer의 스크린샷" lightbox="media/concepts-azure-digital-twins-explorer/azure-digital-twins-explorer-demo.png":::

Azure Digital Twins는 그래프를 데이터 처리 및 비즈니스 논리와 함께 최신 상태로 유지 하기 위한 다양한 **이벤트 시스템** 을 제공합니다. [Azure Functions](../azure-functions/functions-overview.md)등의 외부 계산 리소스를 연결하여 이 데이터 처리를 유연하고 사용자 지정된 방식으로 처리할 수 있습니다.

Azure Digital Twins의 강력한 **쿼리 API** 를 사용하여 라이브 실행 환경에서 인사이트를 추출할 수도 있습니다. API를 사용하면 속성 값, 관계, 관계 속성, 모델 정보 등을 비롯한 다양한 검색 조건으로 쿼리할 수 있습니다. 또한 사용자 환경에 대한 광범위한 인사이트를 수집하고 중요한 사용자 지정 질문에 응답하여 쿼리를 결합할 수 있습니다.

### <a name="input-from-iot-and-business-systems"></a>IoT 및 비즈니스 시스템의 입력

Azure Digital Twins의 실시간 실행 환경을 실제 환경의 최신 상태로 유지하기 위해 [IoT Hub](../iot-hub/about-iot-hub.md)를 사용하여 솔루션을 IoT 및 IoT Edge 장치에 연결할 수 있습니다. 이러한 허브 관리 장치는 쌍 그래프의 일부로 표시되며 모델을 구동하는 데이터를 제공합니다.

Azure Digital Twins를 사용하여 이 목적을 위해 새 IoT Hub을 만들거나 기존 IoT Hub을 이미 관리하는 장치와 연결할 수 있습니다.

REST API 또는 커넥터를 사용하여 다른 데이터 원본에서 Azure Digital Twins를 [Logic Apps](../logic-apps/logic-apps-overview.md)과 같은 다른 서비스에 연결할 수도 있습니다.

### <a name="output-to-adx-tsi-storage-and-analytics"></a>ADX, TSI, 스토리지, 분석으로 출력

추가 분석 또는 스토리지에 대해 Azure Digital Twins 모델의 데이터를 다운스트림 Azure 서비스로 라우팅할 수 있습니다. 이 기능은 [Event Hub](../event-hubs/event-hubs-about.md), [Event Grid](../event-grid/overview.md)또는 [Service Bus](../service-bus-messaging/service-bus-messaging-overview.md)를 사용하여 데이터 흐름을 구동하는 **이벤트 경로** 를 통해 제공됩니다.

이벤트 경로를 사용하여 수행할 수 있는 몇 가지 작업은 다음과 같습니다:
* [ADX(Azure Data Explorer)용 Azure Digital Twins 쿼리 플러그 인](concepts-data-explorer-plugin.md)을 사용하여 쿼리할 디지털 트윈 데이터를 ADX에 전송
* [TSI(Time Series Insights)에 Azure Digital Twins를 연결](how-to-integrate-time-series-insights.md)하여 각 트윈의 시계열 기록 추적
* Time Series Insights의 시계열 모델을 Azure Digital Twins의 원본과 정렬
* [Azure Data Lake](../storage/blobs/data-lake-storage-introduction.md)에 Azure Digital Twins 데이터 저장
* [Azure Synapse Analytics](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md) 또는 기타 Microsoft 데이터 분석 도구를 사용하여 Azure Digital Twins 데이터 분석
* Logic Apps과 크기가 큰 워크플로 통합

이 옵션은 Azure Digital Twins를 더 큰 솔루션에 연결할 수 있는 또 다른 방법으로, 이러한 인사이트로 계속 작업하기 위해 사용자 지정 요구 사항을 지원합니다.

## <a name="azure-digital-twins-in-a-solution-context"></a>솔루션 컨텍스트의 Azure Digital Twins

Azure Digital Twins는 일반적으로 더 큰 IoT 솔루션의 일부로써 다른 Azure 서비스와 함께 사용됩니다. 

Azure Digital Twins를 사용하는 전체 솔루션의 샘플 아키텍처는 다음과 같습니다.
* Azure Digital Twins 서비스 인스턴스입니다. 이 서비스를 통해 해당 상태 및 오케스트레이션 이벤트 처리와 함께 쌍 모델 및 쌍 그래프가 저장됩니다.
* 모델을 구성하고 토폴로지를 만들고 쌍 그래프에서 인사이트를 추출하여 Azure Digital Twins 인스턴스를 구동하는 하나 이상의 클라이언트 앱입니다.
* Azure Digital Twins에 의해 생성된 이벤트를 처리하는 하나 이상의 외부 계산 리소스 또는 장치와 같이 연결된 데이터 원본입니다. 계산 리소스를 제공하는 일반적인 방법 중 하나는 [Azure Functions](../azure-functions/functions-overview.md)을 통하는 것입니다.
* IoT hub를 통해 장치 관리 및 IoT 데이터 스트림 기능을 제공합니다.
* 워크플로 통합(예: [Logic Apps](../logic-apps/logic-apps-overview.md), 콜드 스토리지, Azure Data Explorer, 시계열 통합 또는 분석)과 같은 작업을 처리하는 다운스트림 서비스입니다.

다음 다이어그램에서는 Azure Digital Twins 이 더 큰 Azure IoT 솔루션의 컨텍스트에서 발생하는 위치를 보여줍니다.

:::image type="content" source="media/overview/solution-context.png" alt-text="클라이언트 앱과 외부 계산 리소스 모두에서 입력 원본, 출력 서비스 및 양방향 통신을 보여 주는 다이어그램입니다." border="false" lightbox="media/overview/solution-context.png":::

## <a name="service-limits"></a>서비스 제한

[Azure Digital Twins 서비스 제한 문서](reference-service-limits.md)에서 Azure Digital Twins의 **서비스 제한** 에 대해 읽을 수 있습니다. 이 리소스는 서비스를 사용하여 서비스의 기능 및 요율 제한 사항을 이해하고 필요한 경우 조정할 수 있는 제한을 이해하는 데에도 유용할 수 있습니다.

## <a name="terminology"></a>용어

[Azure IoT 용어집](../iot-fundamentals/iot-glossary.md?toc=/azure/digital-twins/toc.json&bc=/azure/digital-twins/breadcrumb/toc.json)의 Azure Digital Twins를 포함하여 Azure IoT 서비스에서 **일반적인 IoT 용어** 및 해당 사용의 목록을 볼 수 있습니다. 이 리소스는 Azure Digital Twins를 시작하고 IoT 솔루션을 구축하는 동안 유용한 참조일 수 있습니다.

## <a name="next-steps"></a>다음 단계

* [Azure Digital Twins Explorer 시작](quickstart-azure-digital-twins-explorer.md) 및 [엔드투엔드 솔루션 빌드](tutorial-end-to-end.md)에서 Azure Digital Twins 작업에 대해 자세히 알아보려면 예제 시나리오를 참조하세요.

* 또는 Azure Digital Twins 개념에 대해 [DTDL 모델](concepts-models.md)에서 자세히 알아보세요.