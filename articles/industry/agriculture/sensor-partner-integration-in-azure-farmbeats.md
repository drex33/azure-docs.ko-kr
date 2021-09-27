---
title: 센서 파트너 통합
description: 이 문서에서는 센서 파트너 통합에 대해 설명합니다.
author: RiyazPishori
ms.topic: article
ms.date: 11/04/2019
ms.author: riyazp
ms.openlocfilehash: 99b87fd0066d67d09979965bab3a67d1b549afd4
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128599509"
---
# <a name="sensor-partner-integration"></a>센서 파트너 통합

이 문서에서는 센서 파트너 통합을 가능하게 하는 Azure FarmBeats **변환기** 구성 요소에 대한 정보를 제공합니다.

이 구성 요소를 사용하면 파트너가 FarmBeats Datahub API를 사용하여 FarmBeats와 통합하고 고객 디바이스 데이터 및 원격 분석을 FarmBeats Datahub로 보낼 수 있습니다. FarmBeats에서 데이터를 사용할 수 있게 되면 데이터가 FarmBeats Accelerator를 사용하여 시각화되며, 데이터 융합 및 기계 학습/인공 지능 모델 빌드에 사용될 수 있습니다.

## <a name="before-you-start"></a>시작하기 전에

변환기 구성 요소를 개발하려면 FarmBeats API에 액세스할 수 있는 다음 자격 증명이 필요합니다.

- API 엔드포인트
- 테넌트 ID
- 클라이언트 ID
- 클라이언트 암호
- EventHub 연결 문자열

위의 자격 증명을 가져오려면 다음 섹션을 참조하세요. [디바이스 통합 사용](get-sensor-data-from-sensor-partner.md#enable-device-integration-with-farmbeats)

## <a name="translator-development"></a>변환기 개발

**REST API 기반 통합**

FarmBeats의 센서 데이터 통합 기능은 REST API를 통해 제공됩니다. 기능에는 메타데이터 정의, 디바이스 및 센서 프로비전, 디바이스 및 센서 관리가 포함됩니다.

**원격 분석 수집**

원격 분석 데이터는 처리를 위해 Azure Event Hubs에 게시된 정식 메시지에 매핑됩니다. Azure Event Hubs는 연결된 디바이스 및 애플리케이션으로부터 실시간 데이터(원격 분석) 수집을 가능하게 하는 서비스입니다.

**API 개발**

API에는 Swagger 기술 문서가 포함되어 있습니다. API 및 해당 요청 또는 응답에 대한 자세한 내용은 [Swagger](https://aka.ms/FarmBeatsSwagger)를 참조하세요.

**인증**

FarmBeats는 Microsoft Azure Active Directory 인증을 사용합니다. Azure App Service는 기본 제공 인증 및 권한 부여를 지원합니다.

자세한 내용은 [Azure Active Directory](../../app-service/overview-authentication-authorization.md)를 참조하세요.

FarmBeats Datahub는 전달자 인증을 사용하며 다음 자격 증명이 필요합니다.
   - 클라이언트 ID
   - 클라이언트 암호
   - 테넌트 ID

호출자는 이러한 자격 증명을 사용하여 액세스 토큰을 요청할 수 있습니다. 토큰은 다음과 같이 후속 API 요청의 헤더 섹션에서 전송해야 합니다.

```
headers = {"Authorization": "Bearer " + access_token, …} 
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


**머리글** | **설명 및 예**
--- | ---
콘텐츠 형식 | 요청 형식(Content-Type: `application/<format>` )입니다. FarmBeats Datahub API의 경우 형식은 JSON입니다. Content-Type: application/json
권한 부여 | API 호출을 수행하는 데 필요한 액세스 토큰을 지정합니다. 권한 부여: Bearer \<Access-Token\>
수락 | 응답 형식입니다. FarmBeats Datahub API의 경우 형식은 JSON입니다. Accept: application/json

**API 요청**

REST API 요청을 수행하려면 HTTP(GET, POST 또는 PUT) 메서드, API 서비스에 대한 URL, 쿼리, 데이터 전송, 업데이트 또는 삭제할 리소스에 대한 URI(Uniform Resource Identifier) 및 하나 이상의 HTTP 요청 헤더를 결합합니다. API 서비스에 대한 URL은 사용자가 제공하는 API 엔드포인트입니다. 샘플은 다음과 같습니다. `https://\<yourdatahub-website-name>.azurewebsites.net`

필요에 따라 GET 호출에 쿼리 매개 변수를 포함하여 응답에서 데이터를 필터링하고, 크기를 제한하고, 정렬할 수 있습니다.

다음 샘플 요청은 디바이스 목록을 가져오기 위한 것입니다.

```bash
curl -X GET "https://microsoft-farmbeats.azurewebsites.net/Device" -H "Content-Type: application/json" -H "Authorization: Bearer <Access-Token>"
```
대부분의 GET, POST 및 PUT 호출에는 JSON 요청 본문이 필요합니다.

다음 샘플 요청은 디바이스를 만들기 위한 것입니다. (이 샘플에는 요청 본문을 포함하는 입력 JSON이 있습니다.)

```bash
curl -X POST "https://microsoft-farmbeats.azurewebsites.net/Device" -H  "accept: application/json" -H  "Content-Type: application/json" -H "Authorization: Bearer <Access-Token>" -d "{  \"deviceModelId\": \"ID123\",  \"hardwareId\": \"MHDN123\",  \"reportingInterval\": 900,  \"name\": \"Device123\",  \"description\": \"Test Device 123\",}"
```

## <a name="data-format"></a>데이터 형식

JSON은 임의 데이터 구조의 단순한 텍스트 표현을 제공하는 공용 언어 독립적 데이터 형식입니다. 자세한 내용은 [json.org](http://json.org)를 참조하세요.

## <a name="metadata-specifications"></a>메타데이터 사양

FarmBeats Datahub에는 디바이스 파트너가 디바이스 또는 센서 메타데이터를 만들고 관리하는 데 사용할 수 있는 다음과 같은 API가 있습니다.

- /**DeviceModel**: DeviceModel은 제조업체 및 디바이스 유형(게이트웨이 또는 노드)과 같은 디바이스 메타데이터에 해당합니다.
- /**Device**: Device는 팜에 있는 물리적 디바이스에 해당합니다.
- /**SensorModel**: SensorModel은 제조업체, 센서 유형(아날로그 또는 디지털), 센서 측정값(예: 주변 온도 및 압력)과 같은 센서의 메타데이터에 해당합니다.
- /**Sensor**: Sensor는 값을 기록하는 실제 센서에 해당합니다. 센서는 일반적으로 디바이스 ID를 갖는 디바이스에 연결됩니다.

  DeviceModel | Description |
  --- | ---
  Type(node, gateway)  | 디바이스의 유형(노드 또는 게이트웨이)입니다. |
  제조업체  | 제조업체의 이름입니다. |
  ProductCode  | 디바이스 제품 코드 또는 모델 이름 또는 번호입니다. 예: EnviroMonitor#6800. |
  포트  | 포트 이름 및 유형(디지털 또는 아날로그)입니다.  |
  속성  | 리소스를 식별하는 이름입니다. 예를 들어 모델 이름 또는 제품 이름입니다. |
  Description  | 모델에 대한 의미 있는 설명을 제공합니다. |
  속성  | 제조업체로부터의 추가 속성입니다. |
  **디바이스** | **설명** |
  DeviceModelId  |연결된 디바이스 모델의 ID입니다. |
  HardwareId   |MAC 주소와 같은 디바이스의 고유 ID입니다.  |
  ReportingInterval |보고 간격(초)입니다. |
  위치    |디바이스 위도(-90 ~ + 90), 경도(-180 ~ 180), 고도(미터)입니다. |
  ParentDeviceId | 이 디바이스가 연결된 부모 디바이스의 ID입니다. 예를 들어 노드가 게이트웨이에 연결된 경우 노드는 게이트웨이로 parentDeviceID를 갖습니다. |
  속성  | 리소스를 식별하는 이름입니다. 디바이스 파트너는 디바이스 파트너 쪽 디바이스 이름과 일치하는 이름을 보내야 합니다. 디바이스 이름이 디바이스 파트너 쪽에서 사용자 정의되었으면 동일한 사용자 정의 이름을 FarmBeats에 전파해야 합니다.  |
  Description  | 의미 있는 설명을 제공합니다.  |
  속성  |제조업체로부터의 추가 속성입니다.  |
  **SensorModel** | **설명** |
  Type(analog, digital)  |아날로그 또는 디지털 센서를 언급합니다.|
  제조업체  | 제조업체의 이름입니다. |
  ProductCode  | 제품 코드 또는 모델 이름 또는 번호입니다. 예: RS-CO2-N01.  |
  SensorMeasures > Name  | 센서 측정값의 이름입니다. 소문자만 지원됩니다. 다른 깊이에서 측정하려면 깊이를 지정합니다. 예: soil_moisture_15cm. 이 이름은 원격 분석 데이터와 일치해야 합니다. |
  SensorMeasures > DataType  | 원격 분석 데이터 형식입니다. 현재 double이 지원됩니다. |
  SensorMeasures > Type  | 센서 원격 분석 데이터의 측정값 형식입니다. 시스템 정의 유형은 다음과 같습니다. AmbientTemperature, CO2, Depth, ElectricalConductivity, LeafWetness, Length, LiquidLevel, Nitrate, O2, PH, Phosphate, PointInTime, Potassium, Pressure, RainGauge, RelativeHumidity, Salinity, SoilMoisture, SoilTemperature, SolarRadiation, State, TimeDuration, UVRadiation, UVIndex, Volume, WindDirection, WindRun, WindSpeed, Evapotranspiration, PAR. 유형을 추가하려면 /ExtendedType API를 참조하세요.
  SensorMeasures > Unit | 센서 원격 분석 데이터의 단위입니다. 시스템 정의 단위는 다음과 같습니다. NoUnit, Celsius, Fahrenheit, Kelvin, Rankine, Pascal, Mercury, PSI, MilliMeter, CentiMeter, Meter, Inch, Feet, Mile, KiloMeter, MilesPerHour, MilesPerSecond, KMPerHour, KMPerSecond, MetersPerHour, MetersPerSecond, Degree, WattsPerSquareMeter, KiloWattsPerSquareMeter, MilliWattsPerSquareCentiMeter, MilliJoulesPerSquareCentiMeter, VolumetricWaterContent, Percentage, PartsPerMillion, MicroMol, MicroMolesPerLiter, SiemensPerSquareMeterPerMole, MilliSiemensPerCentiMeter, Centibar, DeciSiemensPerMeter, KiloPascal, VolumetricIonContent, Liter, MilliLiter, Seconds, UnixTimestamp, MicroMolPerMeterSquaredPerSecond, and InchesPerHour. 유형을 추가하려면 /ExtendedType API를 참조하세요.
  SensorMeasures > AggregationType  | 없음, 평균, 최대값, 최소값 또는 표준편차 중 하나입니다.
  SensorMeasures > Depth  | 센서의 깊이(센티미터)입니다. 예를 들어 지하 10cm에서 수분을 측정합니다.
  SensorMeasures > Description  | 측정에 대한 의미 있는 설명을 제공합니다.
  속성  | 리소스를 식별하는 이름입니다. 예를 들어 모델 이름 또는 제품 이름입니다.
  Description  | 모델에 대한 의미 있는 설명을 제공합니다.
  속성  | 제조업체로부터의 추가 속성입니다.
  **Sensor**  | **설명** |
  HardwareId  | 제조업체에서 설정한 센서의 고유 ID입니다.
  SensorModelId  | 연결된 센서 모델의 ID입니다.
  위치  | 센서 위도(-90 ~ + 90), 경도(-180 ~ 180), 고도(미터)입니다.
  Port > Name  |디바이스에서 센서가 연결된 포트의 이름 및 유형입니다. 이 이름은 디바이스 모델에 정의된 이름과 같아야 합니다.
  deviceId  | 센서가 연결된 디바이스의 ID입니다.
  속성  | 리소스를 식별하는 이름입니다. 예를 들어 센서 이름 또는 제품 이름과 모델 번호 또는 제품 코드입니다.
  Description  | 의미 있는 설명을 제공합니다.
  속성  | 제조업체로부터의 추가 속성입니다.

 각 개체 및 해당 속성에 대한 자세한 내용은 [Swagger](https://aka.ms/FarmBeatsDatahubSwagger)를 참조하세요.

 > [!NOTE]
 > API는 생성된 각 인스턴스의 고유 ID를 반환합니다. 이 ID는 변환기가 디바이스 관리 및 메타데이터 동기화를 위해 유지해야 합니다.


**메타데이터 동기화**

변환기는 메타데이터에 대한 업데이트를 전송해야 합니다. 예를 들어 업데이트 시나리오에는 디바이스 또는 센서 이름 변경, 디바이스 또는 센서 위치 변경이 있습니다.

변환기에는 FarmBeats의 사용자 게시 링크를 통해 설치된 새 디바이스 또는 센서를 추가할 수 있는 기능이 있어야 합니다. 마찬가지로, 사용자가 디바이스 또는 센서를 업데이트한 경우 FarmBeats에서 해당 디바이스 또는 센서에 대해 동일하게 업데이트해야 합니다. 디바이스 또는 센서를 업데이트해야 하는 일반적인 시나리오는 디바이스 위치 변경 또는 노드에 센서 추가입니다.


> [!NOTE]
> 디바이스 또는 센서 메타데이터에 대해서는 삭제가 지원되지 않습니다.
>
> 메타데이터를 업데이트하려면 디바이스 또는 센서에서 /Get/{id}를 호출하고, 변경된 속성을 업데이트한 다음, 사용자가 설정한 모든 속성이 손실되지 않도록 /Put/{id}를 실행해야 합니다.

### <a name="add-new-types-and-units"></a>새 유형 및 단위 추가

FarmBeats는 새 센서 측정 유형 및 단위를 추가하도록 지원합니다. /ExtendedType API에 대한 자세한 내용은 [Swagger](https://aka.ms/FarmBeatsSwagger)를 참조하세요.

## <a name="telemetry-specifications"></a>원격 분석 사양

원격 분석 데이터는 처리를 위해 Azure Event Hubs에 게시된 정식 메시지에 매핑됩니다. Azure Event Hubs는 연결된 디바이스 및 애플리케이션으로부터 실시간 데이터(원격 분석) 수집을 가능하게 하는 서비스입니다.

## <a name="send-telemetry-data-to-farmbeats"></a>FarmBeats로 원격 분석 데이터 보내기

FarmBeats에 원격 분석 데이터를 보내려면 FarmBeats에서 이벤트 허브로 메시지를 보내는 클라이언트를 만듭니다. 원격 분석 데이터에 대한 자세한 내용은 [이벤트 허브로 원격 분석 보내기](../../event-hubs/event-hubs-dotnet-standard-getstarted-send.md)를 참조하세요.

다음은 클라이언트로서 원격 분석을 지정된 이벤트 허브에 보내는 샘플 Python 코드입니다.

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

정식 메시지 형식은 다음과 같습니다.

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
원격 분석 JSON의 모든 키 이름은 소문자여야 합니다. 예를 들면 deviceid 및 sensordata입니다.

다음은 원격 분석 메시지의 예입니다.


```json
{
  "deviceid": "7f9b4b92-ba45-4a1d-a6ae-c6eda3a5bd12",
  "timestamp": "2019-06-22T06:55:02.7279559Z",
  "version" : "1",
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

> [!NOTE]
> 다음 섹션은 센서 파트너가 변환기 구성 요소를 개발할 때 참조할 수 있는 다른 변경 내용(예: UI, 오류 관리 등)과 관련이 있습니다.


## <a name="link-a-farmbeats-account"></a>FarmBeats 계정 연결

고객이 디바이스 또는 센서를 구입하고 배포한 후에는 디바이스 파트너의 SaaS(software as a service) 포털에서 디바이스 데이터 및 원격 분석에 액세스할 수 있습니다. 디바이스 파트너는 다음 자격 증명을 입력하는 방법을 제공하여 고객이 Azure의 FarmBeats 인스턴스에 계정을 연결할 수 있도록 할 수 있습니다.

   - 표시 이름(사용자가 이 통합의 이름을 정의하는 데 사용할 수 있는 선택적 필드)
   - API 엔드포인트
   - 테넌트 ID
   - 클라이언트 ID
   - 클라이언트 암호
   - EventHub 연결 문자열
   - 시작 날짜

   > [!NOTE]
   > 시작 날짜를 사용하면 기록 데이터 피드, 즉 사용자가 지정한 날짜의 데이터를 사용할 수 있습니다.

## <a name="unlink-farmbeats"></a>FarmBeats 연결 해제

디바이스 파트너는 고객이 기존 FarmBeats 통합의 연결을 해제할 수 있도록 할 수 있습니다. FarmBeats 연결 해제가 FarmBeats Datahub에서 만든 디바이스 또는 센서 메타데이터를 삭제하지 않습니다. 연결 해제는 다음을 수행합니다.

   - 원격 분석 흐름을 중지합니다.
   - 디바이스 파트너에서 통합 자격 증명을 삭제하고 지웁니다.

## <a name="edit-farmbeats-integration"></a>FarmBeats 통합 편집

디바이스 파트너는 클라이언트 암호 또는 연결 문자열이 변경된 경우 고객이 FarmBeats 통합 설정을 편집할 수 있도록 할 수 있습니다. 이 경우 다음 필드만 편집할 수 있습니다.

   - 표시 이름(해당하는 경우)
   - 클라이언트 암호(일반 텍스트가 아닌 "2x8***********" 형식 또는 표시/숨기기 기능에 표시되어야 함)
   - 연결 문자열(일반 텍스트가 아닌 "2x8***********" 형식 또는 표시/숨기기 기능에 표시되어야 함)

## <a name="view-the-last-telemetry-sent"></a>마지막으로 보낸 원격 분석 보기

디바이스 파트너는 고객이 전송된 마지막 원격 분석의 타임스탬프를 확인할 수 있도록 할 수 있습니다. 이 타임스탬프는 **전송된 원격 분석** 아래에 있으며 최신 원격 분석이 성공적으로 FarmBeats에 전송된 시간입니다.

## <a name="troubleshooting-and-error-management"></a>문제 해결 및 오류 관리

**문제 해결 옵션 또는 지원**

고객이 지정된 FarmBeats 인스턴스에서 디바이스 데이터 또는 원격 분석을 받을 수 없는 경우 디바이스 파트너는 문제 해결을 위한 지원 및 메커니즘을 제공해야 합니다.

**원격 분석 데이터 보존**

또한 오류 또는 데이터 손실이 발생하는 경우 원격 분석을 디버깅하거나 다시 보낼 수 있도록 미리 정의된 기간 동안 원격 분석 데이터를 유지해야 합니다.

**오류 관리 또는 오류 알림**

오류가 디바이스 또는 센서 메타데이터, 또는 디바이스 파트너 시스템의 데이터 통합 또는 원격 분석 데이터 흐름에 영향을 주는 경우 고객이 알림을 받아야 합니다. 또한 오류를 해결하는 메커니즘을 설계하고 구현해야 합니다.

**연결 검사 목록**

디바이스 제조업체 또는 파트너는 다음 검사 목록을 사용하여 고객이 제공한 자격 증명이 정확한지 확인할 수 있습니다.

   - 제공된 자격 증명을 사용하여 액세스 토큰을 받았는지 여부를 확인합니다.
   - 수신된 액세스 토큰을 사용하여 API 호출이 성공하는지 여부를 확인합니다.
   - EventHub 클라이언트 연결이 설정되는지 여부를 확인합니다.

## <a name="next-steps"></a>다음 단계

REST API에 대한 자세한 내용은 [REST API](rest-api-in-azure-farmbeats.md)를 참조하세요.