---
title: IoT Hub에 대한 디바이스 업데이트에서 IoT 플러그 앤 플레이를 사용하는 방법 이해 | Microsoft Docs
description: IoT Hub용 디바이스 업데이트를 사용하여 OTA(Over The Air) 업데이트를 지원하는 디바이스를 검색하고 관리합니다.
author: ValOlson
ms.author: valls
ms.date: 2/14/2021
ms.topic: conceptual
ms.service: iot-hub-device-update
ms.openlocfilehash: e53aa866465e7a1409a966a5bab6147d304caddf
ms.sourcegitcommit: 0ede6bcb140fe805daa75d4b5bdd2c0ee040ef4d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/20/2021
ms.locfileid: "122605333"
---
# <a name="device-update-for-iot-hub-and-iot-plug-and-play"></a>IoT Hub 및 IoT 플러그 앤 플레이에 대한 디바이스 업데이트

IoT Hub용 디바이스 업데이트는 [IoT 플러그 앤 플레이](../iot-develop/index.yml)를 사용하여 OTA(Over The Air) 업데이트를 지원하는 디바이스를 검색하고 관리합니다. 디바이스 업데이트 서비스는 IoT Plug and Play 인터페이스를 사용하여 디바이스에서 속성과 메시지를 보내고 받습니다. IoT Hub에 대한 디바이스 업데이트에는 아래에 설명된 대로 IoT 디바이스에서 다음과 같은 인터페이스와 모델 ID를 구현해야 합니다.

개념: 
* [IoT 플러그 앤 플레이 디바이스 클라이언트](../iot-develop/concepts-developer-guide-device.md?pivots=programming-language-csharp)를 이해합니다. 
* [Device Update 에이전트를 구현](https://github.com/Azure/iot-hub-device-update/blob/main/docs/agent-reference/how-to-build-agent-code.md)하는 방법을 확인합니다.

## <a name="adu-core-interface"></a>ADU Core 인터페이스

'ADUCoreInterface' 인터페이스는 업데이트 작업 및 메타데이터를 디바이스로 보내고 디바이스에서 업데이트 상태를 수신하는 데 사용됩니다. 'ADU Core' 인터페이스는 두 개의 개체 속성으로 분할됩니다.

모델에 필요한 구성 요소 이름은 이 인터페이스를 구현할 때 **"Azuredeviceupdateagent"** 입니다. [Azure IoT 플러그 앤 플레이 구성 요소에 대해 알아보기](../iot-develop/concepts-modeling-guide.md)

### <a name="agent-metadata"></a>에이전트 메타데이터

에이전트 메타데이터에는 디바이스 또는 디바이스 업데이트 에이전트가 디바이스 업데이트 서비스에 정보 및 상태를 전송하는 데 사용하는 필드가 포함되어 있습니다.

|이름|스키마|Direction|설명|예제|
|----|------|---------|-----------|-----------|
|resultCode|정수|디바이스-클라우드|마지막 업데이트 작업 결과에 대한 정보를 포함하는 코드입니다. 성공 또는 실패에 대해 작성할 수 있으며 [http 상태 코드 사양](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html)을 따라야 합니다.|500|
|extendedResultCode|정수|디바이스-클라우드|결과에 대한 추가 정보를 포함하는 코드입니다. 성공 또는 실패에 대해 작성할 수 있습니다.|0x80004005|
|state|정수|디바이스-클라우드|디바이스 업데이트 에이전트의 현재 상태를 나타내는 정수입니다. 자세한 내용은 다음을 참조하세요. |유휴 상태|
|installedUpdateId|문자열|디바이스-클라우드|현재 설치된 업데이트의 ID입니다(디바이스 업데이트를 통해). 이 값은 Device Update를 통해 업데이트를 수행한 적이 없는 디바이스에 대해 업데이트 ID JSON 또는 null을 캡처하는 문자열입니다.|“{\"provider\":\"contoso\",\"name\":\"image-update\",\"version\":\"1.0.0\"}”|
|`deviceProperties`|맵|디바이스-클라우드|제조업체 및 모델을 포함하는 속성 집합입니다.|자세한 내용은 다음을 참조하세요.

#### <a name="state"></a>시스템 상태

디바이스 업데이트 서비스에서 작업을 받은 후 디바이스 업데이트 에이전트에서 보고한 상태입니다. `State`는 디바이스 업데이트 서비스에서 디바이스 업데이트 에이전트로 전송된 `Action`(아래 `Actions` 참조)에 대한 응답으로 보고됩니다. 디바이스 업데이트 서비스와 디바이스 업데이트 에이전트 간 흐름의 요청에 대해서는 [개요 워크플로](understand-device-update.md#device-update-agent)를 참조하세요.

|이름|값|Description|
|---------|-----|-----------|
|유휴 상태|0|디바이스가 디바이스 업데이트 서비스로부터 작업을 수신할 준비가 되었습니다. 업데이트에 성공하면 상태가 `Idle` 상태로 돌아갑니다.|
|다운로드 성공|2|다운로드가 완료되었습니다.|
|InstallSucceeded|4|설치가 완료되었습니다.|
|실패|255|업데이트하는 동안 오류가 발생했습니다.|

#### <a name="device-properties"></a>디바이스 속성

제조업체 및 모델을 포함하는 속성 집합입니다.

|이름|스키마|Direction|설명|
|----|------|---------|-----------|
|제조업체|문자열|디바이스-클라우드|`deviceProperties`를 통해 보고된 디바이스의 디바이스 제조업체입니다. 이 속성은 두 위치 중 하나에서 읽습니다. 'AzureDeviceUpdateCore' 인터페이스는 먼저 [구성 파일](device-update-configuration-file.md) 파일에서 ' aduc_manufacturer ' 값을 읽으려고 시도합니다.  구성 파일에 값이 작성되지 않은 경우, 기본값으로 ADUC_DEVICEPROPERTIES_MANUFACTURER에 대한 컴파일 시간 정의를 보고합니다. 이 속성은 부팅 시에만 보고됩니다. 기본값 ‘Contoso’|
|model|문자열|디바이스-클라우드|`deviceProperties`를 통해 보고된 디바이스의 디바이스 모델입니다. 이 속성은 두 위치 중 하나에서 읽습니다. AzureDeviceUpdateCore 인터페이스는 먼저 [구성 파일](device-update-configuration-file.md) 파일에서 'aduc_model' 값을 읽으려고 시도합니다.  구성 파일에 값이 채워지지 않은 경우, 기본적으로 ADUC_DEVICEPROPERTIES_MODEL에 대한 컴파일 시간 정의를 보고합니다. 이 속성은 부팅 시에만 보고됩니다. 기본값 ‘Video’|
|aduVer|문자열|디바이스-클라우드|디바이스에서 실행되는 디바이스 업데이트 에이전트의 버전입니다. 이 값은 컴파일 시간 동안 ENABLE_ADU_TELEMETRY_REPORTING이 1(true)로 설정된 경우에만 빌드에서 읽습니다. 고객은 값을 0(false)으로 설정하여 버전 보고를 사용하지 않도록 선택할 수 있습니다. [디바이스 업데이트 에이전트 속성을 사용자 지정하는 방법](https://github.com/Azure/iot-hub-device-update/blob/main/docs/agent-reference/how-to-build-agent-code.md)|
|doVer|문자열|디바이스-클라우드|디바이스에서 실행되는 전송 최적화 에이전트의 버전입니다. 컴파일 시간 동안 ENABLE_ADU_TELEMETRY_REPORTING이 1(true)로 설정된 경우에만 빌드에서 값을 읽습니다. 고객은 값을 0(false)으로 설정하여 버전 보고를 중단하도록 선택할 수 있습니다. [전송 최적화 에이전트 속성을 사용자 지정하는 방법](https://github.com/microsoft/do-client/blob/main/README.md#building-do-client-components)|

IoT Hub 디바이스 쌍 샘플
```json
 "azureDeviceUpdateAgent": {
                           "__t": "c",
                           "client": {
                                     "state": 0,
                                     "resultCode": 200,
                                     "extendedResultCode": 0,
                                     "deviceProperties": {
                                                         "manufacturer": "Contoso",
                                                         "model": "Video",
                                                         "aduVer": "DU;agent/0.6.0",
                                                         "doVer": "DU;lib/v0.4.0,DU;agent/v0.4.0,DU;plugin-apt/v0.2.0"
                                                         },
                                     "installedUpdateId": "{\"provider\":\"Contoso\",\"name\":\"SampleUpdate1\",\"version\":\"1.0.4\"}"
                                     },
                            }
```

참고: 디바이스 또는 모듈은 요소가 구성 요소를 참조함을 표시하기 위해 {"__t": "c"} 마커를 추가해야 합니다. [여기](../iot-develop/concepts-convention.md#sample-multiple-components-writable-property)에서 자세히 알아보세요.

### <a name="service-metadata"></a>서비스 메타데이터

서비스 메타데이터에는 디바이스 업데이트 에이전트에 작업 및 데이터를 전달하기 위해 디바이스 업데이트 서비스에서 사용하는 필드가 포함되어 있습니다.

|이름|스키마|Direction|설명|
|----|------|---------|-----------|
|action|정수|클라우드-디바이스|에이전트에서 수행해야 하는 작업에 해당하는 정수입니다. 값은 다음과 같습니다.|
|updateManifest|문자열|클라우드-디바이스|업데이트 콘텐츠를 설명하는 데 사용됩니다. [가져오기 매니페스트](import-update.md#create-a-device-update-import-manifest)에서 생성됨|
|updateManifestSignature|JSON 개체|클라우드-디바이스|원본 확인에 사용되는 JSON 웹 키가 있는 JWS(JSON 웹 서명)입니다.|
|fileUrls|맵|클라우드-디바이스|`DownloadUri`로의 `FileHash` 매핑입니다. 다운로드할 파일과 파일이 올바르게 다운로드되었는지 확인하는 데 사용할 해시를 에이전트에 알려줍니다.|

#### <a name="action"></a>작업

`Actions` 다음은 디바이스 업데이트 서비스의 지시에 따라 디바이스 업데이트 에이전트에서 수행하는 작업을 나타냅니다. 디바이스 업데이트 에이전트는 수신된 `Action`을 처리하는 `State`(위 `State` 섹션 참조)를 보고합니다. 디바이스 업데이트 서비스와 디바이스 업데이트 에이전트 간 흐름의 요청에 대해서는 [개요 워크플로](understand-device-update.md#device-update-agent)를 참조하세요.

|이름|값|Description|
|---------|-----|-----------|
|다운로드|0|게시된 콘텐츠 또는 업데이트와 필요한 기타 콘텐츠 다운로드|
|설치|1|콘텐츠 또는 업데이트를 설치합니다. 일반적으로 이는 설치 관리자에서 콘텐츠나 업데이트를 호출하는 것을 의미합니다.|
|적용|2|업데이트를 완료합니다. 필요한 경우 시스템을 다시 부팅하도록 신호를 보냅니다.|
|취소|255|현재 작업 처리를 중지하고 `Idle`로 돌아갑니다. `Failed` 상태인 에이전트에 `Idle`로 돌아가라고 알려주는 데에도 사용합니다.|

## <a name="device-information-interface"></a>디바이스 정보 인터페이스

디바이스 정보 인터페이스는 [IoT 플러그 앤 플레이 아키텍처](../iot-develop/overview-iot-plug-and-play.md) 내에서 사용되는 개념입니다. 디바이스의 하드웨어 및 운영 체제에 대한 정보를 제공하는 디바이스-클라우드 속성을 포함합니다. IoT Hub의 디바이스 업데이트는 원격 분석과 진단에 DeviceInformation.manufacturer 및 DeviceInformation.model 속성을 사용합니다. 디바이스 정보 인터페이스에 대한 자세한 내용은 이 [예제](https://devicemodels.azure.com/dtmi/azure/devicemanagement/deviceinformation-1.json)를 참조하세요.

모델에 필요한 구성 요소 이름은 이 인터페이스를 구현할 때 **Deviceinformation** 입니다. [Azure IoT 플러그 앤 플레이 구성 요소 알아보기](../iot-develop/concepts-modeling-guide.md)

|Name|유형|스키마|Direction|설명|예제|
|----|----|------|---------|-----------|-----------|
|제조업체|속성|문자열|디바이스-클라우드|디바이스 제조업체의 회사 이름입니다. OEM(원본 장비 제조업체)의 이름과 같을 수 있습니다.|Contoso|
|model|속성|문자열|디바이스-클라우드|디바이스 모델 이름 또는 ID입니다.|IoT Edge 디바이스|
|swVersion|속성|문자열|디바이스-클라우드|디바이스에 있는 소프트웨어 버전입니다. swVersion은 펌웨어의 버전일 수 있습니다.|4.15.0-122|
|osName|속성|문자열|디바이스-클라우드|디바이스에 있는 운영 체제의 이름입니다.|Ubuntu Server 18.04|
|processorArchitecture|속성|문자열|디바이스-클라우드|디바이스의 프로세서 아키텍처입니다.|ARM64|
|processorManufacturer|속성|문자열|디바이스-클라우드|디바이스에 있는 프로세서 제조업체의 이름입니다.|Microsoft|
|totalStorage|속성|문자열|디바이스-클라우드|디바이스에서 사용 가능한 총 스토리지(KB)입니다.|2048|
|totalMemory|속성|문자열|디바이스-클라우드|디바이스에서 사용 가능한 총 메모리(KB)입니다.|256|

## <a name="model-id"></a>모델 ID 

모델 ID는 스마트 디바이스가 IoT 플러그인을 사용하여 Azure IoT 애플리케이션에 해당 기능을 보급하는 방법입니다. Azure IoT 애플리케이션에 기능을 보급하는 스마트 디바이스를 빌드하는 방법에 대해 자세히 알아보세요. [IoT 플러그 앤 플레이 디바이스 개발자 가이드](../iot-develop/concepts-developer-guide-device.md)

IoT Hub에 대한 디바이스 업데이트에는 IoT 플러그 앤 플레이 스마트 디바이스가 디바이스 연결의 일부로 **"dtmi:AzureDeviceUpdate;1"** 값을 사용하는 모델 ID를 알리기 위해 필요합니다. [모델 ID 공지 방법 알아보기](../iot-develop/concepts-developer-guide-device.md#model-id-announcement).