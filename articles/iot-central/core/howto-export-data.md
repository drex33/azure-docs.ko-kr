---
title: Azure IoT Central에서 데이터 내보내기 | Microsoft Docs
description: 새 데이터 내보내기를 사용하여 IoT 데이터를 Azure 및 사용자 지정 클라우드 대상으로 내보내는 방법입니다.
services: iot-central
author: lizross
ms.author: lizross
ms.date: 06/04/2021
ms.topic: how-to
ms.service: iot-central
ms.custom: contperf-fy21q1, contperf-fy21q3
ms.openlocfilehash: c3747922f96e3bf1c9240d72f2bd65b3a5ac660e
ms.sourcegitcommit: 61e7a030463debf6ea614c7ad32f7f0a680f902d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/28/2021
ms.locfileid: "129093935"
---
# <a name="export-iot-data-to-cloud-destinations-using-data-export"></a>데이터 내보내기를 사용하여 IoT 데이터를 클라우드 대상으로 내보내기

이 문서에서는 Azure IoT Central에서 데이터 내보내기를 사용하는 방법을 설명합니다. 이 기능을 사용하여 IoT Central 애플리케이션에서 필터링되고 보강된 IoT 데이터를 지속적으로 내보낼 수 있습니다. 데이터 내보내기는 웜 경로 인사이트, 분석 및 스토리지를 위해 변경 내용을 클라우드 솔루션의 다른 부분에 거의 실시간으로 푸시합니다.

예를 들어 다음을 수행할 수 있습니다.

- 원격 분석, 속성 변경, 디바이스 연결, 디바이스 수명 주기 및 디바이스 템플릿 수명 주기 데이터를 JSON 형식으로 거의 실시간으로 지속적으로 내보냅니다.
- 데이터 스트림을 필터링하여 사용자 지정 조건과 일치하는 데이터를 내보냅니다.
- 데이터 스트림을 디바이스의 사용자 지정 값 및 속성 값으로 보강합니다.
- 데이터를 Azure Event Hubs, Azure Service Bus, Azure Blob Storage 및 웹후크 엔드포인트와 같은 대상으로 보냅니다.

> [!Tip]
> 데이터 내보내기를 켜면 그 시점 이후의 데이터만 가져옵니다. 현재 데이터 내보내기가 꺼져 있는 동안에는 데이터를 검색할 수 없습니다. 더 많은 기록 데이터를 유지하려면 데이터 내보내기를 초기에 켜세요.

## <a name="prerequisites"></a>필수 구성 요소

데이터 내보내기 기능을 사용하려면 [V3 애플리케이션](howto-faq.yml#how-do-i-get-information-about-my-application-)이 있어야 하며 [데이터 내보내기](howto-manage-users-roles.md) 권한이 있어야 합니다.

V2 애플리케이션이 있는 경우 [V2를 V3 IoT Central 애플리케이션으로 마이그레이션](howto-migrate.md)을 참조하세요.

## <a name="set-up-export-destination"></a>내보내기 대상 설정

데이터 내보내기를 구성하려면 먼저 내보내기 대상이 있어야 합니다. 현재 사용할 수 있는 대상 유형은 다음과 같습니다.

- Azure Event Hubs
- Azure Service Bus 큐
- Azure Service Bus 항목
- Azure Blob Storage
- 웹후크

### <a name="create-an-event-hubs-destination"></a>Event Hubs 대상 만들기

내보낼 기존 Event Hubs 네임스페이스가 없는 경우 다음 단계를 수행합니다.

1. [Azure Portal에서 새 Event Hubs 네임스페이스](https://ms.portal.azure.com/#create/Microsoft.EventHub)를 만듭니다. [Azure Event Hubs 문서](../../event-hubs/event-hubs-create.md)에서 자세히 알아볼 수 있습니다.

1. Event Hubs 네임스페이스에서 이벤트 허브를 만듭니다. 네임스페이스로 이동한 다음, 맨 위에서 **+ 이벤트 허브** 를 선택하여 이벤트 허브 인스턴스를 만듭니다.

1. IoT Central에서 데이터 내보내기를 설정할 때 사용할 키를 생성합니다.

    - 만든 이벤트 허브 인스턴스를 선택합니다.
    - **설정 > 공유 액세스 정책** 을 차례로 선택합니다.
    - 새 키를 만들거나 **보내기** 권한이 있는 기존 키를 선택합니다.
    - 기본 또는 보조 연결 문자열을 복사합니다. 이 연결 문자열을 사용하여 IoT Central에서 새 대상을 설정합니다.
    - 또는 전체 Event Hubs 네임스페이스에 대한 연결 문자열을 생성할 수 있습니다.
        1. Azure Portal에서 Event Hubs 네임스페이스로 이동합니다.
        2. **설정** 아래에서 **공유 액세스 정책** 을 선택합니다.
        3. 새 키를 만들거나 **보내기** 권한이 있는 기존 키를 선택합니다.
        4. 주 또는 보조 연결 문자열 중 하나를 복사합니다.
        
### <a name="create-a-service-bus-queue-or-topic-destination"></a>Service Bus 큐 또는 토픽 대상 만들기

내보낼 기존 Service Bus 네임스페이스가 없는 경우 다음 단계를 수행합니다.

1. [Azure Portal에서 새 Service Bus 네임스페이스](https://ms.portal.azure.com/#create/Microsoft.ServiceBus.1.0.5)를 만듭니다. [Azure Service Bus 문서](../../service-bus-messaging/service-bus-create-namespace-portal.md)에서 자세히 알아볼 수 있습니다.

1. 내보낼 큐 또는 토픽을 만들려면 Service Bus 네임스페이스로 이동하고, **+ 큐** 또는 **+ 토픽** 을 선택합니다.

1. IoT Central에서 데이터 내보내기를 설정할 때 사용할 키를 생성합니다.

    - 만든 큐 또는 토픽을 선택합니다.
    - **설정/공유 액세스 정책** 을 선택합니다.
    - 새 키를 만들거나 **보내기** 권한이 있는 기존 키를 선택합니다.
    - 기본 또는 보조 연결 문자열을 복사합니다. 이 연결 문자열을 사용하여 IoT Central에서 새 대상을 설정합니다.
    - 또는 전체 Service Bus 네임스페이스에 대한 연결 문자열을 생성할 수 있습니다.
        1. Azure Portal에서 Service Bus 네임스페이스로 이동합니다.
        2. **설정** 아래에서 **공유 액세스 정책** 을 선택합니다.
        3. 새 키를 만들거나 **보내기** 권한이 있는 기존 키를 선택합니다.
        4. 주 또는 보조 연결 문자열 중 하나를 복사합니다.

### <a name="create-an-azure-blob-storage-destination"></a>Azure Blob Storage 대상 만들기

내보낼 기존 Azure 스토리지 계정이 없는 경우 다음 단계를 수행합니다.

1. [Azure Portal에서 새 스토리지 계정](https://ms.portal.azure.com/#create/Microsoft.StorageAccount-ARM)을 만듭니다. 새 [Azure Blob Storage 계정](../../storage/blobs/storage-quickstart-blobs-portal.md) 또는 [Azure Data Lake Storage v2 스토리지 계정](../../storage/common/storage-account-create.md)을 만드는 방법에 대해 자세히 알아볼 수 있습니다. 데이터 내보내기는 블록 Blob을 지원하는 스토리지 계정에만 데이터를 쓸 수 있습니다. 다음 목록에는 알려진 호환되는 스토리지 계정 유형이 나와 있습니다.

    |성능 계층|계정 유형|
    |-|-|
    |Standard|범용 V2|
    |Standard|범용 V1|
    |Standard|Blob Storage|
    |Premium|블록 Blob 스토리지|

1. 스토리지 계정에서 컨테이너를 만들려면 스토리지 계정으로 이동합니다. **Blob 서비스** 에서 **Blob 찾아보기** 를 선택합니다. 맨 위에서 **+ 컨테이너** 를 선택하여 새 컨테이너를 만듭니다.

1. **설정 > 액세스 키** 로 차례로 이동하여 스토리지 계정에 대한 연결 문자열을 생성합니다. 두 연결 문자열 중 하나를 복사합니다.

### <a name="create-a-webhook-endpoint"></a>웹후크 엔드포인트 만들기

데이터를 공개적으로 사용할 수 있는 HTTP 웹후크 엔드포인트로 내보낼 수 있습니다. [RequestBin](https://requestbin.net/)을 사용하여 테스트 웹후크 엔드포인트를 만들 수 있습니다. RequestBin은 요청 제한에 도달하면 요청을 제한합니다.

1. [RequestBin](https://requestbin.net/)을 엽니다.
2. 새 RequestBin을 만들고 **Bin URL** 을 복사합니다. 데이터 내보내기를 테스트할 때 이 URL을 사용합니다.

## <a name="set-up-data-export"></a>데이터 내보내기 설정

이제 데이터를 내보낼 대상이 있으므로 IoT Central 애플리케이션에서 데이터 내보내기를 설정합니다.

1. IoT Central 애플리케이션에 로그인합니다.

1. 왼쪽 창에서 **데이터 내보내기** 를 선택합니다.

    > [!Tip]
    > 왼쪽 창에 **데이터 내보내기** 가 표시되지 않으면 앱에서 데이터 내보내기를 구성할 수 있는 권한이 없는 것입니다. 관리자에게 데이터 내보내기를 설정하도록 요청합니다.

1. **+ 새 내보내기** 를 선택합니다.

1. 새 내보내기의 표시 이름을 입력하고, 데이터 내보내기가 **사용** 인지 확인합니다.

1. 내보낼 데이터 형식을 선택합니다. 다음 표에는 지원되는 데이터 내보내기 형식이 나와 있습니다.

    | 데이터 형식 | Description | 데이터 형식 |
    | :------------- | :---------- | :----------- |
    |  원격 분석 | 디바이스에서 원격 분석 메시지를 거의 실시간으로 내보냅니다. 내보낸 각 메시지에는 정규화된 원래 디바이스 메시지의 전체 내용이 포함됩니다.   |  [원격 분석 메시지 형식](#telemetry-format)   |
    | 속성 변경 | 디바이스 및 클라우드 속성에 대한 변경 내용을 거의 실시간으로 내보냅니다. 읽기 전용 디바이스 속성의 경우 reported 값의 변경 내용을 내보냅니다. 읽기-쓰기 속성의 경우 reported 값과 desired 값을 모두 내보냅니다. | [속성 변경 메시지 형식](#property-changes-format) |
    | 디바이스 연결 | 디바이스 연결 및 연결 끊김 이벤트를 내 보냅니다. | [디바이스 연결 메시지 형식](#device-connectivity-changes-format) |
    | 디바이스 수명 주기 | 디바이스 등록, 삭제, 프로비저닝, 사용, 사용 안 함, displayNameChanged 및 deviceTemplateChanged 이벤트를 내보냅니다. | [디바이스 수명 주기 변경 메시지 형식](#device-lifecycle-changes-format) |
    | 디바이스 템플릿 수명 주기 | 만들기, 업데이트 및 삭제를 포함하여 게시된 디바이스 템플릿 변경 내용을 내보냅니다. | [디바이스 템플릿 수명 주기 변경 메시지 형식](#device-template-lifecycle-changes-format) | 

1. 필요에 따라 필터를 추가하여 내보내는 데이터의 양을 줄입니다. 각 데이터 내보내기 형식에 사용할 수 있는 다양한 유형의 필터가 있습니다.<a name="DataExportFilters"></a>
    
    | 데이터 형식 | 사용 가능한 필터| 
    |--------------|------------------|
    |원격 분석|<ul><li>디바이스 이름, 디바이스 ID, 디바이스 템플릿 및 디바이스가 시뮬레이션되었는지 여부 필터링</li><li>필터 조건과 일치하는 원격 분석만 포함하는 필터 스트림</li><li>필터 조건과 일치하는 속성이 있는 디바이스의 원격 분석만 포함하는 필터 스트림</li><li>필터 조건과 일치하는 *메시지 속성* 이 있는 원격 분석만 포함하는 필터 스트림. *메시지 속성*(*애플리케이션 속성* 이라고도 함)은 디바이스 SDK를 사용하는 디바이스에서 선택적으로 보내는 각 원격 분석 메시지의 키 값 쌍 모음으로 보냅니다. 메시지 속성 필터를 만들려면 찾고 있는 메시지 속성 키를 입력하고 조건을 지정합니다. 지정된 필터 조건과 일치하는 속성이 있는 원격 분석 메시지만 내보냅니다. [IoT Hub 문서에서 애플리케이션 속성에 대해 자세히 알아보세요](../../iot-hub/iot-hub-devguide-messages-construct.md). </li></ul>|
    |속성 변경|<ul><li>디바이스 이름, 디바이스 ID, 디바이스 템플릿 및 디바이스가 시뮬레이션되었는지 여부 필터링</li><li>필터 조건과 일치하는 속성 변경만 포함하는 필터 스트림</li></ul>|
    |디바이스 연결|<ul><li>장치 이름, 장치 ID, 장치 템플릿, 조직 및 장치가 시뮬레이션 된 경우 필터링</li><li>필터 조건과 일치하는 속성이 있는 디바이스의 변경만 포함하는 필터 스트림</li></ul>|
    |디바이스 수명 주기|<ul><li>디바이스 이름, 디바이스 ID, 디바이스 템플릿 및 디바이스가 프로비저닝, 사용 또는 시뮬레이션되었는지 여부 필터링</li><li>필터 조건과 일치하는 속성이 있는 디바이스의 변경만 포함하는 필터 스트림</li></ul>|
    |디바이스 템플릿 수명 주기|<ul><li>디바이스 템플릿 기준 필터</li></ul>|
    
1. 필요에 따라 추가 키-값 쌍 메타데이터를 사용하여 내보낸 메시지를 보강합니다. 원격 분석, 속성 변경, 디바이스 연결 및 디바이스 수명 주기 데이터 내보내기 유형에 대해 다음과 같은 보강을 사용할 수 있습니다. <a name="DataExportEnrichmnents"></a>
    - **사용자 지정 문자열**: 각 메시지에 사용자 지정 정적 문자열을 추가합니다. 아무 키나 입력하고 문자열 값을 입력합니다.
    - 각 메시지에 추가되는 **속성**:
       - 장치 이름, 장치 템플릿 이름, 사용, 조직, 프로 비전 됨, 시뮬레이션 됨 등의 장치 메타 데이터
       - 현재 디바이스는 각 메시지에 속성 또는 클라우드 속성 값을 보고했습니다. 내보낸 메시지가 지정된 속성이 없는 디바이스에서 제공된 경우 내보낸 메시지에서 보강을 받지 않습니다.

1. 새 대상을 추가하거나 이미 만든 대상을 추가합니다. **새 항목 만들기** 링크를 선택하고, 다음 정보를 추가합니다.

    - **대상 이름**: IoT Central에서 표시되는 대상 이름입니다.
    - **대상 유형**: 대상 유형을 선택합니다. 대상을 아직 설정하지 않은 경우 [내보내기 대상 설정](#set-up-export-destination)을 참조하세요.
    - Azure Event Hubs, Azure Service Bus 큐 또는 토픽의 경우 리소스에 대한 연결 문자열을 붙여넣고, 필요한 경우 대/소문자를 구분하는 이벤트 허브, 큐 또는 토픽 이름을 입력합니다.
    - Azure Blob Storage의 경우 리소스에 대한 연결 문자열을 붙여넣고, 필요한 경우 대/소문자를 구분하는 컨테이너 이름을 입력합니다.
    - 웹후크의 경우 웹후크 엔드포인트의 콜백 URL을 붙여넣습니다. 필요에 따라 웹후크 권한 부여(OAuth 2.0 및 권한 부여 토큰)를 구성하고 사용자 지정 헤더를 추가할 수 있습니다. 
        - OAuth 2.0의 경우 클라이언트 자격 증명 흐름만 지원됩니다. 대상이 저장되면 IoT Central에서 OAuth 공급자와 통신하여 권한 부여 토큰을 검색합니다. 이 토큰은 이 대상으로 보내는 모든 메시지에 대한 "Authorization" 헤더에 연결됩니다.
        - 권한 부여 토큰의 경우 이 대상으로 보내는 모든 메시지에 대해 "Authorization" 헤더에 직접 연결되는 토큰 값을 지정할 수 있습니다.
    - **만들기** 를 선택합니다.

1. **+ 대상** 을 선택하고, 드롭다운에서 대상을 선택합니다. 단일 내보내기에 최대 5개의 대상을 추가할 수 있습니다.

1. 내보내기 설정이 완료되면 **저장** 을 선택합니다. 몇 분 후 대상에 데이터가 표시됩니다.

## <a name="monitor-your-export"></a>내보내기 모니터링

IoT Central에서 내보내기 상태를 확인할 수 있을 뿐만 아니라 [Azure Monitor](../../azure-monitor/overview.md)를 사용하여 내보내는 데이터의 양과 내보내기 오류를 확인할 수도 있습니다. Azure Portal의 차트, REST API 또는 PowerShell/Azure CLI의 쿼리를 사용하여 내보내기 및 디바이스 상태 메트릭에 액세스할 수 있습니다. 현재 Azure Monitor에서 모니터링할 수 있는 데이터 내보내기 메트릭은 다음과 같습니다.

- 필터를 적용하기 전에 내보기 위해 들어오는 메시지의 수
- 필터를 통과하는 메시지의 수
- 대상으로 성공적으로 내보낸 메시지의 수
- 오류가 발생한 횟수입니다.

자세히 알아보려면 [애플리케이션 상태 모니터링](howto-manage-iot-central-from-portal.md#monitor-application-health)을 참조하세요.

## <a name="destinations"></a>대상

### <a name="azure-blob-storage-destination"></a>Azure Blob Storage 대상

데이터는 분당 한 번씩 내보내지며, 각 파일에는 이전 내보내기 이후의 변경 내용 일괄 처리가 포함됩니다. 내보낸 데이터는 JSON 형식으로 저장됩니다. 스토리지 계정에서 내보낸 데이터의 기본 경로는 다음과 같습니다.

- 원격 분석: _{container}/{app-id}/{partition_id}/{YYYY}/{MM}/{dd}/{hh}/{mm}/{filename}_
- 속성 변경: _{container}/{app-id}/{partition_id}/{YYYY}/{MM}/{dd}/{hh}/{mm}/{filename}_

Azure Portal에서 내보낸 파일을 찾아보려면 해당 파일로 이동하여 **Blob 편집** 을 선택합니다.

### <a name="azure-event-hubs-and-azure-service-bus-destinations"></a>Azure Event Hubs 및 Azure Service Bus 대상

데이터는 거의 실시간으로 내냅니다. 데이터는 메시지 본문에 있으며 UTF-8로 인코딩된 JSON 형식입니다.

메시지의 주석 또는 시스템 속성 모음에는 메시지 본문의 해당 필드와 동일한 값이 있는 `iotcentral-device-id`, `iotcentral-application-id`, `iotcentral-message-source` 및 `iotcentral-message-type` 필드가 포함됩니다.

### <a name="webhook-destination"></a>웹후크 대상

웹후크 대상의 경우 데이터를 거의 실시간으로 내보냅니다. 메시지 본문의 데이터는 Event Hubs 및 Service Bus와 동일한 형식입니다.

## <a name="telemetry-format"></a>원격 분석 형식

내보낸 각 메시지에는 디바이스에서 메시지 본문에 보낸 전체 메시지의 정규화된 형식이 포함됩니다. 메시지는 JSON 형식이며 UTF-8로 인코딩됩니다. 각 메시지에 포함되는 정보는 다음과 같습니다.

- `applicationId`: IoT Central 애플리케이션의 ID
- `messageSource`: 메시지의 원본(`telemetry`)
- `deviceId`: 원격 분석 메시지를 보낸 디바이스의 ID
- `schema`: 페이로드 스키마의 이름 및 버전
- `templateId`: 디바이스와 연결된 디바이스 템플릿의 ID
- `enqueuedTime`: IoT Central에서 이 메시지를 받은 시간
- `enrichments`: 내보내기에 설정된 모든 보강
- `module`: 이 메시지를 보낸 IoT Edge 모듈 이 필드는 메시지가 IoT Edge 모듈에서 온 경우에만 나타납니다.
- `component`: 이 메시지를 보낸 구성 요소 이 필드는 메시지에 전송된 기능이 디바이스 템플릿의 구성 요소로 모델링된 경우에만 나타납니다.
- `messageProperties`: 디바이스에서 메시지와 함께 보낸 추가 속성. 이러한 속성은 *애플리케이션 속성* 이라고도 합니다. [IoT Hub 문서에서 자세히 알아보세요](../../iot-hub/iot-hub-devguide-messages-construct.md).

Event Hubs 및 Service Bus의 경우 IoT Central은 디바이스로부터 메시지를 받은 후 새 메시지를 빠르게 내보냅니다. 각 메시지의 사용자 속성(애플리케이션 속성이라고도 함)에는 `iotcentral-device-id`, `iotcentral-application-id` 및 `iotcentral-message-source`가 자동으로 포함됩니다.

Blob 스토리지의 경우 메시지는 1분에 한 번씩 일괄 처리하여 내보냅니다.

다음 예제에서는 내보낸 원격 분석 메시지를 보여 줍니다.

```json

{
    "applicationId": "1dffa667-9bee-4f16-b243-25ad4151475e",
    "messageSource": "telemetry",
    "deviceId": "1vzb5ghlsg1",
    "schema": "default@v1",
    "templateId": "urn:qugj6vbw5:___qbj_27r",
    "enqueuedTime": "2020-08-05T22:26:55.455Z",
    "telemetry": {
        "Activity": "running",
        "BloodPressure": {
            "Diastolic": 7,
            "Systolic": 71
        },
        "BodyTemperature": 98.73447010562934,
        "HeartRate": 88,
        "HeartRateVariability": 17,
        "RespiratoryRate": 13
    },
    "enrichments": {
      "userSpecifiedKey": "sampleValue"
    },
    "module": "VitalsModule",
    "component": "DeviceComponent",
    "messageProperties": {
      "messageProp": "value"
    }
}
```
### <a name="message-properties"></a>메시지 속성

원격 분석 메시지에는 원격 분석 페이로드 외에도 메타데이터에 대한 속성이 있습니다. 이전 코드 조각에서는 `deviceId` 및 `enqueuedTime`과 같은 시스템 메시지의 예제를 보여 줍니다. 시스템 메시지 속성에 대한 자세한 내용은 [D2C IoT Hub 메시지의 시스템 속성](../../iot-hub/iot-hub-devguide-messages-construct.md#system-properties-of-d2c-iot-hub-messages)을 참조하세요.

사용자 지정 메타데이터를 원격 분석 메시지에 추가해야 하는 경우 속성을 원격 분석 메시지에 추가할 수 있습니다. 예를 들어 디바이스에서 메시지를 만들 때 타임스탬프를 추가해야 합니다.

다음 코드 조각에서는 디바이스에서 `iothub-creation-time-utc` 속성을 만들 때 이 속성을 메시지에 추가하는 방법을 보여 줍니다.

> [!IMPORTANT]
> 이 타임스탬프의 형식은 표준 시간대 정보가 없는 UTC여야 합니다. 예를 들어 `2021-04-21T11:30:16Z`는 유효하고 `2021-04-21T11:30:16-07:00`은 잘못된 것입니다.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
async function sendTelemetry(deviceClient, index) {
  console.log('Sending telemetry message %d...', index);
  const msg = new Message(
    JSON.stringify(
      deviceTemperatureSensor.updateSensor().getCurrentTemperatureObject()
    )
  );
  msg.properties.add("iothub-creation-time-utc", new Date().toISOString());
  msg.contentType = 'application/json';
  msg.contentEncoding = 'utf-8';
  await deviceClient.sendEvent(msg);
}
```

# <a name="java"></a>[Java](#tab/java)

```java
private static void sendTemperatureTelemetry() {
  String telemetryName = "temperature";
  String telemetryPayload = String.format("{\"%s\": %f}", telemetryName, temperature);

  Message message = new Message(telemetryPayload);
  message.setContentEncoding(StandardCharsets.UTF_8.name());
  message.setContentTypeFinal("application/json");
  message.setProperty("iothub-creation-time-utc", Instant.now().toString());

  deviceClient.sendEventAsync(message, new MessageIotHubEventCallback(), message);
  log.debug("My Telemetry: Sent - {\"{}\": {}°C} with message Id {}.", telemetryName, temperature, message.getMessageId());
  temperatureReadings.put(new Date(), temperature);
}
```

# <a name="c"></a>[C#](#tab/csharp)

```csharp
private async Task SendTemperatureTelemetryAsync()
{
  const string telemetryName = "temperature";

  string telemetryPayload = $"{{ \"{telemetryName}\": {_temperature} }}";
  using var message = new Message(Encoding.UTF8.GetBytes(telemetryPayload))
  {
      ContentEncoding = "utf-8",
      ContentType = "application/json",
  };
  message.Properties.Add("iothub-creation-time-utc", DateTime.UtcNow.ToString("yyyy-MM-ddTHH:mm:ssZ"));
  await _deviceClient.SendEventAsync(message);
  _logger.LogDebug($"Telemetry: Sent - {{ \"{telemetryName}\": {_temperature}°C }}.");
}
```

# <a name="python"></a>[Python](#tab/python)

```python
async def send_telemetry_from_thermostat(device_client, telemetry_msg):
    msg = Message(json.dumps(telemetry_msg))
    msg.custom_properties["iothub-creation-time-utc"] = datetime.now(timezone.utc).isoformat()
    msg.content_encoding = "utf-8"
    msg.content_type = "application/json"
    print("Sent message")
    await device_client.send_message(msg)
```

---

다음 코드 조각에서는 Blob 스토리지로 내보낸 메시지의 이 속성을 보여 줍니다.

```json
{
  "applicationId":"5782ed70-b703-4f13-bda3-1f5f0f5c678e",
  "messageSource":"telemetry",
  "deviceId":"sample-device-01",
  "schema":"default@v1",
  "templateId":"urn:modelDefinition:mkuyqxzgea:e14m1ukpn",
  "enqueuedTime":"2021-01-29T16:45:39.143Z",
  "telemetry":{
    "temperature":8.341033560421833
  },
  "messageProperties":{
    "iothub-creation-time-utc":"2021-01-29T16:45:39.021Z"
  },
  "enrichments":{}
}
```

## <a name="property-changes-format"></a>속성 변경 형식

각 메시지 또는 레코드는 디바이스 및 클라우드 속성에 대한 변경 내용을 나타냅니다. 내보낸 메시지에 포함되는 정보는 다음과 같습니다.

- `applicationId`: IoT Central 애플리케이션의 ID
- `messageSource`: 메시지의 원본(`properties`)
- `messageType`: `cloudPropertyChange`, `devicePropertyDesiredChange` 또는 `devicePropertyReportedChange`
- `deviceId`: 원격 분석 메시지를 보낸 디바이스의 ID
- `schema`: 페이로드 스키마의 이름 및 버전
- `enqueuedTime`: IoT Central에서 이 변경을 검색한 시간
- `templateId`: 디바이스와 연결된 디바이스 템플릿의 ID
- `properties`: 변경된 속성 및 값의 이름을 포함하여 변경된 속성의 배열입니다. 속성이 구성 요소 또는 IoT Edge 모듈 내에서 모델링되는 경우 구성 요소 및 모듈 정보가 포함됩니다.
- `enrichments`: 내보내기에 설정된 모든 보강
- 
Event Hubs 및 Service Bus의 경우 IoT Central은 새 메시지 데이터를 이벤트 허브 또는 Service Bus 큐 또는 토픽으로 거의 실시간으로 내보냅니다. 각 메시지의 사용자 속성(애플리케이션 속성이라고도 함)에는 `iotcentral-device-id`, `iotcentral-application-id`, `iotcentral-message-source` 및 `iotcentral-message-type`이 자동으로 포함됩니다.

Blob 스토리지의 경우 메시지는 1분에 한 번씩 일괄 처리하여 내보냅니다.

다음 예제에서는 Azure Blob Storage에서 받은 내보낸 속성 변경 메시지를 보여 줍니다.

```json
{
    "applicationId": "1dffa667-9bee-4f16-b243-25ad4151475e",
    "messageSource": "properties",
    "messageType": "cloudPropertyChange",
    "deviceId": "18a985g1fta",
    "schema": "default@v1",
    "templateId": "urn:qugj6vbw5:___qbj_27r",
    "enqueuedTime": "2020-08-05T22:37:32.942Z",
    "properties": [{
        "name": "MachineSerialNumber",
        "value": "abc",
        "module": "VitalsModule",
        "component": "DeviceComponent"
    }],
    "enrichments": {
        "userSpecifiedKey" : "sampleValue"
    }
}
```
## <a name="device-connectivity-changes-format"></a>디바이스 연결 변경 형식

각 메시지 또는 레코드는 단일 디바이스에서 발생한 연결 이벤트를 나타냅니다. 내보낸 메시지에 포함되는 정보는 다음과 같습니다.

- `applicationId`: IoT Central 애플리케이션의 ID
- `messageSource`: 메시지의 원본(`deviceConnectivity`)
- `messageType`: `connected` 또는 `disconnected`
- `deviceId`: 변경된 디바이스의 ID
- `schema`: 페이로드 스키마의 이름 및 버전
- `templateId`: 디바이스와 연결된 디바이스 템플릿의 ID
- `enqueuedTime`: IoT Central에서 이 변경이 발생한 시간
- `enrichments`: 내보내기에 설정된 모든 보강

Event Hubs 및 Service Bus의 경우 IoT Central은 새 메시지 데이터를 이벤트 허브 또는 Service Bus 큐 또는 토픽으로 거의 실시간으로 내보냅니다. 각 메시지의 사용자 속성(애플리케이션 속성이라고도 함)에는 `iotcentral-device-id`, `iotcentral-application-id`, `iotcentral-message-source` 및 `iotcentral-message-type`이 자동으로 포함됩니다.

Blob 스토리지의 경우 메시지는 1분에 한 번씩 일괄 처리하여 내보냅니다.

다음 예에서는 Azure Blob Storage에서 수신된 내보낸 디바이스 연결 메시지를 보여 줍니다.

```json
{
  "applicationId": "1dffa667-9bee-4f16-b243-25ad4151475e",
  "messageSource": "deviceConnectivity",
  "messageType": "connected",
  "deviceId": "1vzb5ghlsg1",
  "schema": "default@v1",
  "templateId": "urn:qugj6vbw5:___qbj_27r",
  "enqueuedTime": "2021-04-05T22:26:55.455Z",
  "enrichments": {
    "userSpecifiedKey": "sampleValue"
  }
}

```
## <a name="device-lifecycle-changes-format"></a>디바이스 수명 주기 변경 형식

각 메시지 또는 레코드는 단일 디바이스에 대한 변경 내용을 나타냅니다. 내보낸 메시지에 포함되는 정보는 다음과 같습니다.

- `applicationId`: IoT Central 애플리케이션의 ID
- `messageSource`: 메시지의 원본(`deviceLifecycle`)
- `messageType`: 발생한 변경 유형. `registered`, `deleted`, `provisioned`, `enabled`, `disabled`, `displayNameChanged` 및 `deviceTemplateChanged` 중 하나입니다.
- `deviceId`: 변경된 디바이스의 ID
- `schema`: 페이로드 스키마의 이름 및 버전
- `templateId`: 디바이스와 연결된 디바이스 템플릿의 ID
- `enqueuedTime`: IoT Central에서 이 변경이 발생한 시간
- `enrichments`: 내보내기에 설정된 모든 보강

Event Hubs 및 Service Bus의 경우 IoT Central은 새 메시지 데이터를 이벤트 허브 또는 Service Bus 큐 또는 토픽으로 거의 실시간으로 내보냅니다. 각 메시지의 사용자 속성(애플리케이션 속성이라고도 함)에는 `iotcentral-device-id`, `iotcentral-application-id`, `iotcentral-message-source` 및 `iotcentral-message-type`이 자동으로 포함됩니다.

Blob 스토리지의 경우 메시지는 1분에 한 번씩 일괄 처리하여 내보냅니다.

다음 예제에서는 Azure Blob Storage에서 받은 내보낸 디바이스 수명 주기 메시지를 보여 줍니다.

```json
{
  "applicationId": "1dffa667-9bee-4f16-b243-25ad4151475e",
  "messageSource": "deviceLifecycle",
  "messageType": "registered",
  "deviceId": "1vzb5ghlsg1",
  "schema": "default@v1",
  "templateId": "urn:qugj6vbw5:___qbj_27r",
  "enqueuedTime": "2021-01-01T22:26:55.455Z",
  "enrichments": {
    "userSpecifiedKey": "sampleValue"
  }
}
```
## <a name="device-template-lifecycle-changes-format"></a>디바이스 템플릿 수명 주기 변경 형식

각 메시지 또는 레코드는 게시된 단일 디바이스 템플릿에 대한 단일 변경 내용을 나타냅니다. 내보낸 메시지에 포함되는 정보는 다음과 같습니다.

- `applicationId`: IoT Central 애플리케이션의 ID
- `messageSource`: 메시지의 원본(`deviceTemplateLifecycle`)
- `messageType`: `created`, `updated` 또는 `deleted`
- `schema`: 페이로드 스키마의 이름 및 버전
- `templateId`: 디바이스와 연결된 디바이스 템플릿의 ID
- `enqueuedTime`: IoT Central에서 이 변경이 발생한 시간
- `enrichments`: 내보내기에 설정된 모든 보강

Event Hubs 및 Service Bus의 경우 IoT Central은 새 메시지 데이터를 이벤트 허브 또는 Service Bus 큐 또는 토픽으로 거의 실시간으로 내보냅니다. 각 메시지의 사용자 속성(애플리케이션 속성이라고도 함)에는 `iotcentral-device-id`, `iotcentral-application-id`, `iotcentral-message-source` 및 `iotcentral-message-type`이 자동으로 포함됩니다.

Blob 스토리지의 경우 메시지는 1분에 한 번씩 일괄 처리하여 내보냅니다.

다음 예제에서는 Azure Blob Storage에서 받은 내보낸 디바이스 수명 주기 메시지를 보여 줍니다.

```json
{
  "applicationId": "1dffa667-9bee-4f16-b243-25ad4151475e",
  "messageSource": "deviceTemplateLifecycle",
  "messageType": "created",
  "schema": "default@v1",
  "templateId": "urn:qugj6vbw5:___qbj_27r",
  "enqueuedTime": "2021-01-01T22:26:55.455Z",
  "enrichments": {
    "userSpecifiedKey": "sampleValue"
  }
}
```

## <a name="comparison-of-legacy-data-export-and-data-export"></a>레거시 데이터 내보내기 및 데이터 내보내기 비교

다음 표는 [기존 데이터 내보내기](howto-export-data-legacy.md)와 데이터 내보내기 기능의 차이점을 보여 줍니다.

| 기능  | 레거시 데이터 내보내기 | 새 데이터 내보내기 |
| :------------- | :---------- | :----------- |
| 사용 가능한 데이터 형식 | 원격 분석, 디바이스, 디바이스 템플릿 | 원격 분석, 속성 변경, 디바이스 연결 변경, 디바이스 수명 주기 변경, 디바이스 템플릿 수명 주기 변경 |
| 필터링 | 없음 | 내보내는 데이터 형식에 따라 달라집니다. 원격 분석의 경우 원격 분석, 메시지 속성, 속성 값을 기준으로 필터링합니다. |
| 보강 | 없음 | 디바이스의 사용자 지정 문자열 또는 속성 값을 사용하여 보강합니다. |
| 대상 | Azure Event Hubs, Azure Service Bus 큐 및 토픽, Azure Blob Storage | 레거시 데이터 내보내기 및 웹후크의 경우와 동일합니다.|
| 지원되는 애플리케이션 버전 | V2, V3 | V3만 |
| 주목할 만한 제한 | 앱당 5개 내보내기, 내보내기당 1개 대상 | 앱당 10개 내보내기-대상 연결 |

## <a name="next-steps"></a>다음 단계

이제 새 데이터 내보내기를 사용하는 방법을 알아보았으므로 다음 단계에서는 [IoT Central에서 분석을 사용하는 방법](./howto-create-analytics.md)을 알아봅니다.
