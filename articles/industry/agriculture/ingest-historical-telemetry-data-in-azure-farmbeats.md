---
title: 기록 원격 분석 데이터 수집
description: 이 문서에서는 기록 원격 분석 데이터를 수집하는 방법을 설명합니다.
author: RiyazPishori
ms.topic: article
ms.date: 11/04/2019
ms.author: riyazp
ms.custom: has-adal-ref
ms.openlocfilehash: 2ee6102d68b14000453c9a67f06b0b745273fff4
ms.sourcegitcommit: 02d443532c4d2e9e449025908a05fb9c84eba039
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/06/2021
ms.locfileid: "108741188"
---
# <a name="ingest-historical-telemetry-data"></a>기록 원격 분석 데이터 수집

이 문서에서는 기록 센서 데이터를 Azure FarmBeats로 수집하는 방법을 설명합니다.

디바이스, 센서 등과 같은 IoT(사물 인터넷) 리소스에서 기록 데이터를 수집하는 것은 FarmBeats의 일반적인 시나리오입니다. 디바이스와 센서에 대한 메타데이터를 만든 다음 FarmBeats에 대한 기록 데이터를 정규형으로 수집합니다.

## <a name="before-you-begin"></a>시작하기 전에

이 문서를 진행하기 전에 IoT 디바이스에서 기록 데이터를 수집했고 FarmBeats를 설치했는지 확인합니다. 또한 다음 단계에 설명된 대로 파트너 액세스가 가능하도록 설정해야 합니다.

## <a name="enable-partner-access"></a>파트너 액세스 사용

Azure FarmBeats 인스턴스에 대한 파트너 통합이 가능하도록 설정해야 합니다. 이 단계에서는 디바이스 파트너로 Azure FarmBeats 인스턴스에 액세스할 수 있는 클라이언트를 만들고 후속 단계에서 필요한 다음 값을 제공합니다.

- API 엔드포인트: Datahub URL(예: https://\<datahub>.azurewebsites.net)입니다.
- 테넌트 ID
- 클라이언트 ID
- 클라이언트 암호
- EventHub 연결 문자열

다음 단계를 수행합니다.

> [!NOTE]
> 다음 단계를 수행하려면 관리자여야 합니다.

1. [https://manage.visualstudio.com](https://portal.azure.com/ ) 에 로그인합니다.

2. **FarmBeats 버전 1.2.7 이상인 경우 a, b 및 c 단계를 건너뛰고 3단계로 이동합니다.** FarmBeats 버전은 FarmBeats UI의 오른쪽 위 모서리에서 **설정** 아이콘을 선택하여 확인할 수 있습니다.

      a.  **Azure Active Directory** > **앱 등록** 으로 이동합니다.

      b. FarmBeats 배포의 일환으로 만든 **앱 등록** 을 선택합니다. FarmBeats 데이터 허브와 이름이 같습니다.

      다. **API 표시** > **클라이언트 애플리케이션 추가** 를 차례로 선택하고, **04b07795-8ddb-461a-bbee-02f9e1bf7b46** 을 입력하고, **권한 부여 범위** 를 선택합니다. 그러면 Azure CLI(Cloud Shell)에 액세스하여 아래 단계를 수행할 수 있습니다.

3. Cloud Shell을 엽니다. 이 옵션은 Azure Portal의 오른쪽 위 모서리에 있는 도구 모음에서 사용할 수 있습니다.

    ![Azure Portal 도구 모음](./media/get-drone-imagery-from-drone-partner/navigation-bar-1.png)

4. 환경이 **PowerShell** 로 설정되어 있는지 확인합니다. 기본적으로 Bash로 설정되어 있습니다.

    ![PowerShell 도구 모음 설정](./media/get-sensor-data-from-sensor-partner/power-shell-new-1.png)

5. 홈 디렉터리로 이동합니다.

    ```azurepowershell-interactive
    cd
    ```

6. 다음 명령을 실행합니다. 그러면 Azure AD 요청에 사용하도록 인증된 계정이 연결됩니다.

    ```azurepowershell-interactive
    Connect-AzureAD
    ```

7. 다음 명령을 실행합니다. 그러면 스크립트가 홈 디렉터리에 다운로드됩니다.

    ```azurepowershell-interactive 

    wget –q https://aka.ms/farmbeatspartnerscriptv3 -O ./generatePartnerCredentials.ps1

    ```

8. 다음 스크립트를 실행합니다. 이 스크립트는 **Azure Active Directory** > **개요** 페이지에서 가져올 수 있는 테넌트 ID를 요청합니다.

    ```azurepowershell-interactive

    ./generatePartnerCredentials.ps1

    ```

9. 화면의 지침에 따라 **API 엔드포인트**, **테넌트 ID**, **클라이언트 ID**, **클라이언트 암호** 및 **EventHub 연결 문자열** 에 대한 값을 캡처합니다.


## <a name="create-device-or-sensor-metadata"></a>디바이스 또는 센서 메타데이터 만들기

 필요한 자격 증명이 있으면 디바이스와 센서를 정의할 수 있습니다. 이렇게 하려면 FarmBeats API를 호출하여 메타데이터를 만듭니다. 위의 섹션에서 만든 클라이언트 앱으로 API를 호출해야 합니다.

 FarmBeats Datahub에는 디바이스 또는 센서 메타데이터를 생성 및 관리할 수 있는 다음과 같은 API가 있습니다.

 > [!NOTE]
 > 파트너는 메타데이터를 읽고, 만들고, 업데이트할 수 있는 권한만 있습니다. **삭제 옵션은 파트너에게 제한됩니다.**

- /**DeviceModel**: DeviceModel은 제조업체 및 디바이스 유형(게이트웨이 또는 노드)과 같은 디바이스 메타데이터에 해당합니다.
- /**Device**: Device는 팜에 있는 물리적 디바이스에 해당합니다.
- /**SensorModel**: SensorModel은 제조업체, 센서 유형(아날로그 또는 디지털), 센서 측정값(예: 주변 온도 및 압력)과 같은 센서의 메타데이터에 해당합니다.
- /**Sensor**: Sensor는 값을 기록하는 실제 센서에 해당합니다. 센서는 일반적으로 디바이스 ID를 갖는 디바이스에 연결됩니다.

| DeviceModel | 제안 |
|--|--|
| Type(node, gateway) | 디바이스 유형 - 노드 또는 게이트웨이 |
| 제조업체 | 제조업체의 이름입니다. |
| ProductCode | 디바이스 제품 코드 또는 모델 이름 또는 번호입니다. 예: EnviroMonitor#6800. |
| 포트 | 포트 이름 및 유형(디지털 또는 아날로그)입니다. |
| 속성 | 리소스를 식별하는 이름입니다. 예를 들어 모델 이름 또는 제품 이름입니다. |
| Description | 모델에 대한 의미 있는 설명을 제공합니다. |
| 속성 | 제조업체로부터의 추가 속성입니다. |
| **디바이스** |  |
| DeviceModelId | 연결된 디바이스 모델의 ID입니다. |
| HardwareId | MAC 주소와 같은 디바이스의 고유 ID입니다. |
| ReportingInterval | 보고 간격(초)입니다. |
| 위치 | 디바이스 위도(-90 ~ + 90), 경도(-180 ~ 180), 고도(미터)입니다. |
| ParentDeviceId | 이 디바이스가 연결된 부모 디바이스의 ID입니다. 예를 들어 게이트웨이에 연결된 노드입니다. 노드에는 parentDeviceId가 게이트웨이로 있습니다. |
| Name | 리소스를 식별하는 이름입니다. 디바이스 파트너는 파트너 쪽 디바이스 이름과 일치하는 이름을 보내야 합니다. 파트너 디바이스 이름이 사용자 정의되었으면 동일한 사용자 정의 이름을 FarmBeats에 전파해야 합니다. |
| Description | 의미 있는 설명을 제공합니다. |
| 속성 | 제조업체로부터의 추가 속성입니다. |
| **SensorModel** |  |
| Type(analog, digital) | 센서의 유형(아날로그 또는 디지털)입니다. |
| 제조업체 | 센서의 제조업체입니다. |
| ProductCode | 제품 코드 또는 모델 이름 또는 번호입니다. 예: RS-CO2-N01. |
| SensorMeasures > Name | 센서 측정값의 이름입니다. 소문자만 지원됩니다. 다른 깊이에서 측정하려면 깊이를 지정합니다. 예: soil_moisture_15cm. 이 이름은 원격 분석 데이터와 일치해야 합니다. |
| SensorMeasures > DataType | 원격 분석 데이터 형식입니다. 현재 double이 지원됩니다. |
| SensorMeasures > Type | 센서 원격 분석 데이터의 측정값 형식입니다. 시스템 정의 유형은 AmbientTemperature, CO2, Depth, ElectricalConductivity, LeafWetness, Length, LiquidLevel, Nitrate, O2, PH, Phosphate, PointInTime, Potassium, Pressure, RainGauge, RelativeHumidity, Salinity, SoilMoisture, SoilTemperature, SolarRadiation, State, TimeDuration, UVRadiation, UVIndex, Volume, WindDirection, WindRun, WindSpeed, Evapotranspiration, PAR입니다. 유형을 추가하려면 /ExtendedType API를 참조하세요. |
| SensorMeasures > Unit | 센서 원격 분석 데이터의 단위입니다. 시스템 정의 단위는 NoUnit, Celsius, Fahrenheit, Kelvin, Rankine, Pascal, Mercury, PSI, MilliMeter, CentiMeter, Meter, Inch, Feet, Mile, KiloMeter, MilesPerHour, MilesPerSecond, KMPerHour, KMPerSecond, MetersPerHour, MetersPerSecond, Degree, WattsPerSquareMeter, KiloWattsPerSquareMeter, MilliWattsPerSquareCentiMeter, MilliJoulesPerSquareCentiMeter, VolumetricWaterContent, Percentage, PartsPerMillion, MicroMol, MicroMolesPerLiter, SiemensPerSquareMeterPerMole, MilliSiemensPerCentiMeter, Centibar, DeciSiemensPerMeter, KiloPascal, VolumetricIonContent, Liter, MilliLiter, Seconds, UnixTimestamp, MicroMolPerMeterSquaredPerSecond, InchesPerHour입니다. 더 추가하려면 /ExtendedType API를 참조하세요. |
| SensorMeasures > AggregationType | 값은 average, maximum, minimum 또는 StandardDeviation일 수 있습니다. |
| Name | 리소스를 식별하는 이름입니다. 예를 들어 모델 이름 또는 제품 이름입니다. |
| Description | 모델에 대한 의미 있는 설명을 제공합니다. |
| 속성 | 제조업체로부터의 추가 속성입니다. |
| **Sensor** |  |
| HardwareId | 제조업체에서 설정한 센서의 고유 ID입니다. |
| SensorModelId | 연결된 센서 모델의 ID입니다. |
| 위치 | 센서 위도(-90 ~ + 90), 경도(-180 ~ 180), 고도(미터)입니다. |
| Port > Name | 디바이스에서 센서가 연결된 포트의 이름 및 유형입니다. 디바이스 모델에 정의된 이름과 같아야 합니다. |
| DeviceID | 센서가 연결된 디바이스의 ID입니다. |
| 속성 | 리소스를 식별하는 이름입니다. 예를 들어 센서 이름 또는 제품 이름과 모델 번호 또는 제품 코드입니다. |
| Description | 의미 있는 설명을 제공합니다. |
| 속성 | 제조업체로부터의 추가 속성입니다. |

개체에 대한 자세한 내용은 [Swagger](https://aka.ms/FarmBeatsDatahubSwagger)를 참조하세요.

### <a name="api-request-to-create-metadata"></a>메타데이터를 만들기 위한 API 요청

API 요청을 하려면 HTTP(POST) 메서드, API 서비스에 대한 URL 및 리소스에 대한 URI를 결합하여 요청을 쿼리하거나, 요청에 데이터를 제출하거나, 요청을 생성 또는 삭제할 수 있습니다. 그런 다음, 하나 이상의 HTTP 요청 헤더를 추가합니다. API 서비스에 대한 URL은 API 엔드포인트, 즉 Datahub URL(https://\<yourdatahub>.azurewebsites.net)입니다.

### <a name="authentication"></a>인증

FarmBeats Datahub는 전달자 인증을 사용하며, 여기에는 이전 섹션에서 생성된 다음 자격 증명이 필요합니다.

- 클라이언트 ID
- 클라이언트 암호
- 테넌트 ID

호출자는 이러한 자격 증명을 사용하여 액세스 토큰을 요청할 수 있습니다. 토큰은 다음과 같이 후속 API 요청의 헤더 섹션에서 전송해야 합니다.

```
headers = *{"Authorization": "Bearer " + access_token, …}*
```

다음 샘플 Python 코드는 FarmBeats에 대한 후속 API 호출에 사용할 수 있는 액세스 토큰을 제공합니다. 

```python
import requests
import json
import msal

# Your service principal App ID
CLIENT_ID = "<CLIENT_ID>"
# Your service principal password
CLIENT_SECRET = "<CLIENT_SECRET>"
# Tenant ID for your Azure subscription
TENANT_ID = "<TENANT_ID>"

AUTHORITY_HOST = 'https://login.microsoftonline.com'
AUTHORITY = AUTHORITY_HOST + '/' + TENANT_ID

ENDPOINT = "https://<yourfarmbeatswebsitename-api>.azurewebsites.net"
SCOPE = ENDPOINT + "/.default"

context = msal.ConfidentialClientApplication(CLIENT_ID, authority=AUTHORITY, client_credential=CLIENT_SECRET)
token_response = context.acquire_token_for_client(SCOPE)
# We should get an access token here
access_token = token_response.get('access_token')
```

**HTTP 요청 헤더**

FarmBeats Datahub에 대한 API 호출을 수행할 때 지정해야 하는 가장 일반적인 요청 헤더는 다음과 같습니다.

- **Content-Type**: application/json
- **Authorization**: Bearer <Access-Token>
- **Accept**: application/json

### <a name="input-payload-to-create-metadata"></a>메타데이터를 만들기 위한 입력 페이로드

DeviceModel


```json
{
  "type": "Node",
  "manufacturer": "string",
  "productCode": "string",
  "ports": [
    {
      "name": "string",
      "type": "Analog"
    }
  ],
  "name": "string",
  "description": "string",
  "properties": {
    "additionalProp1": {},
    "additionalProp2": {},
    "additionalProp3": {}
  }
}
```

디바이스

```json
{
  "deviceModelId": "string",
  "hardwareId": "string",
  "farmId": "string",
  "reportingInterval": 0,
  "location": {
    "latitude": 0,
    "longitude": 0,
    "elevation": 0
  },
  "parentDeviceId": "string",
  "name": "string",
  "description": "string",
  "properties": {
    "additionalProp1": {},
    "additionalProp2": {},
    "additionalProp3": {}
  }
}
```

SensorModel

```json
{
  "type": "Analog",
  "manufacturer": "string",
  "productCode": "string",
  "sensorMeasures": [
    {
      "name": "string",
      "dataType": "Double",
      "type": "string",
      "unit": "string",
      "aggregationType": "None",
      "depth": 0,
      "description": "string"
    }
  ],
  "name": "string",
  "description": "string",
  "properties": {
    "additionalProp1": {},
    "additionalProp2": {},
    "additionalProp3": {}
  }
}

```
센서

```json
{
  "hardwareId": "string",
  "sensorModelId": "string",
  "location": {
    "latitude": 0,
    "longitude": 0,
    "elevation": 0
  },
  "depth": 0,
  "port": {
    "name": "string",
    "type": "Analog"
  },
  "deviceId": "string",
  "name": "string",
  "description": "string",
  "properties": {
    "additionalProp1": {},
    "additionalProp2": {},
    "additionalProp3": {}
  }
}
```

다음 샘플 요청은 디바이스를 만듭니다. 이 요청에는 요청 본문이 포함된 페이로드로 입력 JSON이 있습니다.

```bash
curl -X POST "https://<datahub>.azurewebsites.net/Device" -H
"accept: application/json" -H  "Content-Type: application/json" -H
"Authorization: Bearer <Access-Token>" -d "{  \"deviceModelId\": \"ID123\",  \"hardwareId\": \"MHDN123\",
\"reportingInterval\": 900,  \"name\": \"Device123\",
\"description\": \"Test Device 123\"}" *
```

다음은 Python의 샘플 코드입니다. 이 샘플에 사용된 액세스 토큰은 인증 시 수신되는 것과 동일합니다.

```python
import requests
import json

# Got access token - Calling the Device Model API

headers = {
    "Authorization": "Bearer " + access_token,
    "Content-Type" : "application/json"
    }
payload = '{"type" : "Node", "productCode" : "TestCode", "ports": [{"name": "port1","type": "Analog"}], "name" : "DummyDevice"}'
response = requests.post(ENDPOINT + "/DeviceModel", data=payload, headers=headers)
```

> [!NOTE]
> API는 생성된 각 인스턴스의 고유 ID를 반환합니다. 해당 원격 분석 메시지를 보내려면 ID를 유지해야 합니다.

### <a name="send-telemetry"></a>원격 분석 전송

FarmBeats에서 디바이스와 센서를 만들었으면 연결된 원격 측정 메시지를 보낼 수 있습니다.

### <a name="create-a-telemetry-client"></a>원격 분석 클라이언트 만들기

원격 분석 데이터를 처리를 위해 Azure Event Hubs에 보내야 합니다. Azure Event Hubs는 연결된 디바이스 및 애플리케이션으로부터 실시간 데이터(원격 분석) 수집을 가능하게 하는 서비스입니다. FarmBeats에 원격 분석 데이터를 보내려면 FarmBeats에서 이벤트 허브로 메시지를 보내는 클라이언트를 만듭니다. 원격 분석 데이터를 보내는 방법에 대한 자세한 내용은 [Azure Event Hubs](../../event-hubs/event-hubs-dotnet-standard-getstarted-send.md)를 참조하세요.

### <a name="send-a-telemetry-message-as-the-client"></a>클라이언트로서 원격 분석 메시지 보내기

Event Hubs 클라이언트로 연결을 설정한 후 JSON으로 이벤트 허브에 메시지를 보낼 수 있습니다.

다음은 클라이언트로서 원격 분석 데이터를 지정된 이벤트 허브에 보내는 샘플 Python 코드입니다.

```python
import azure
from azure.eventhub import EventHubClient, Sender, EventData, Receiver, Offset
EVENTHUBCONNECTIONSTRING = "<EventHub Connection String provided by customer>"
EVENTHUBNAME = "<EventHub Name provided by customer>"

write_client = EventHubClient.from_connection_string(EVENTHUBCONNECTIONSTRING, eventhub=EVENTHUBNAME, debug=False)
sender = write_client.add_sender(partition="0")
write_client.run()
for i in range(5):
    telemetry = "<Canonical Telemetry message>"
    print("Sending telemetry: " + telemetry)
    sender.send(EventData(telemetry))
write_client.stop()

```

기록 센서 데이터 형식을 Azure FarmBeats에서 인식하는 정규 형식으로 변환합니다. 정식 메시지 형식은 다음과 같습니다.

```json
{
"deviceid": "<id of the Device created>",
"timestamp": "<timestamp in ISO 8601 format>",
"version" : "1",
"sensors": [
    {
      "id": "<id of the sensor created>",
      "sensordata": [
        {
          "timestamp": "< timestamp in ISO 8601 format >",
          "<sensor measure name (as defined in the Sensor Model)>": <value>
        },
        {
          "timestamp": "<timestamp in ISO 8601 format>",
          "<sensor measure name (as defined in the Sensor Model)>": <value>
        }
      ]
    }
 ]
}
```

해당 디바이스 및 센서를 추가한 후 이전 섹션에서 설명한 대로 원격 분석 메시지에서 디바이스 ID 및 센서 ID를 가져옵니다.

다음은 원격 분석 메시지의 예입니다.


 ```json
{
  "deviceid": "7f9b4b92-ba45-4a1d-a6ae-c6eda3a5bd12",
  "timestamp": "2019-06-22T06:55:02.7279559Z",
  "version": "1",
  "sensors": [
    {
      "id": "d8e7beb4-72de-4eed-9e00-45e09043a0b3",
      "sensordata": [
        {
          "timestamp": "2019-06-22T06:55:02.7279559Z",
          "hum_max": 15
        },
        {
          "timestamp": "2019-06-22T06:55:02.7279559Z",
          "hum_min": 42
        }
      ]
    },
    {
      "id": "d8e7beb4-72de-4eed-9e00-45e09043a0b3",
      "sensordata": [
        {
          "timestamp": "2019-06-22T06:55:02.7279559Z",
          "hum_max": 20
        },
        {
          "timestamp": "2019-06-22T06:55:02.7279559Z",
          "hum_min": 89
        }
      ]
    }
  ]
}
```

## <a name="troubleshooting"></a>문제 해결

### <a name="cant-view-telemetry-data-after-ingesting-historicalstreaming-data-from-your-sensors"></a>센서에서 기록/스트리밍 데이터를 수집한 후 원격 분석 데이터를 볼 수 없음

**증상**: 디바이스 또는 센서가 배포되고 FarmBeats에 디바이스/센서를 만들었고 EventHub로 원격 분석을 수집했지만 FarmBeats에서 원격 분석 데이터를 가져오거나 볼 수 없습니다.

**정정 작업**:

1. 적절한 파트너 등록을 완료했는지 확인합니다. Datahub Swagger로 가서 /Partner API로 이동하고 Get을 수행하여 파트너가 등록되었는지 확인할 수 있습니다. 그렇지 않으면 [여기 단계](get-sensor-data-from-sensor-partner.md#enable-device-integration-with-farmbeats)에 따라 파트너를 추가합니다.

2. 파트너 클라이언트 자격 증명을 사용하여 메타데이터(DeviceModel, Device, SensorModel, Sensor)를 만들었는지 확인합니다.

3. 올바른 원격 분석 메시지 형식을 사용했는지 확인합니다(아래에 명시된 대로).

```json
{
"deviceid": "<id of the Device created>",
"timestamp": "<timestamp in ISO 8601 format>",
"version" : "1",
"sensors": [
    {
      "id": "<id of the sensor created>",
      "sensordata": [
        {
          "timestamp": "< timestamp in ISO 8601 format >",
          "<sensor measure name (as defined in the Sensor Model)>": <value>
        },
        {
          "timestamp": "<timestamp in ISO 8601 format>",
          "<sensor measure name (as defined in the Sensor Model)>": <value>
        }
      ]
    }
 ]
}
```

## <a name="next-steps"></a>다음 단계

REST API 기반 통합 세부 정보에 대한 자세한 내용은 [REST API](rest-api-in-azure-farmbeats.md)를 참조하세요.