---
title: 이미지 파트너 통합
description: 이 문서에서는 이미지 파트너 통합에 대해 설명합니다.
author: RiyazPishori
ms.topic: article
ms.date: 11/04/2019
ms.author: riyazp
ms.openlocfilehash: 85dff004bdaf61297d9f88766e4cadc97f7d6b88
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128624257"
---
# <a name="imagery-partner-integration"></a>이미지 파트너 통합

이 문서에서는 Azure FarmBeats Translator 구성 요소를 사용하여 이미지 데이터를 FarmBeats로 보내는 방법을 설명합니다. 농업 이미지 데이터는 다중 스펙트럼 카메라, 인공위성 및 드론과 같은 다양한 소스에서 생성할 수 있습니다. 농업 이미지 파트너는 FarmBeats와 통합하여 고객에게 팜에 대한 맞춤형 지도를 제공할 수 있습니다.

데이터를 사용할 수 있게 되면, FarmBeats 액셀러레이터를 통해 시각화할 수 있으며 잠재적으로 농업 기업 또는 고객 시스템 통합업체가 데이터 융합 및 ML/AI(기계 학습/인공 지능) 모델을 구축하는 데 사용할 수 있습니다.

FarmBeats는 다음과 같은 기능을 제공합니다.

- /ExtendedType API를 사용하여 사용자 지정 이미지 유형, 소스 및 파일 형식을 정의합니다.
- /Scene 및 /SceneFile API를 통해 다양한 소스에서 이미지 데이터를 수집합니다.

다음 정보는 모든 형태의 이미지를 FarmBeats 시스템으로 가져오는 데 중점을 두고 있습니다.

**Drone Imagery**(드론 이미지) 섹션을 선택하면 드론 orthomosaic의 고해상도 이미지를 보여주는 팝업이 열립니다. 파트너 소프트웨어에 액세스하여 드론 비행을 계획하고 원시 데이터를 얻을 수 있습니다. 경로 계획 및 orthomosaic 이미지 연결에 파트너의 소프트웨어를 계속 사용합니다.

드론 파트너는 고객이 자신의 계정을 Azure의 FarmBeats 인스턴스와 연결할 수 있도록 해야 합니다.

FarmBeats를 연결하려면 드론 파트너 소프트웨어에서 다음 자격 증명을 사용해야 합니다.

- API 엔드포인트
- 테넌트 ID
- 클라이언트 ID
- 클라이언트 암호

## <a name="api-development"></a>API 개발

API에는 Swagger 기술 문서가 포함되어 있습니다. API 및 해당 요청 또는 응답에 대한 자세한 내용은 [Swagger](https://aka.ms/FarmBeatsDatahubSwagger)를 참조하세요.

## <a name="authentication"></a>인증

FarmBeats는 Microsoft Azure AD(Azure [Active Directory](../../app-service/overview-authentication-authorization.md))를 사용합니다.  Azure App Service는 기본 제공 인증 및 권한 부여를 지원합니다. 

Azure AD에 대한 자세한 내용은 [Azure Active Directory](../../app-service/overview-authentication-authorization.md)를 참조하세요.   

FarmBeats Datahub는 전달자 인증을 사용하며 다음 자격 증명이 필요합니다.

- 클라이언트 ID
- 클라이언트 암호
- 테넌트 ID

호출자는 이전 자격 증명을 사용하여 액세스 토큰을 요청할 수 있으며, 이것은 후속 API 요청에서 헤더 섹션에 다음과 같이 포함하여 보내야 합니다.

```
headers = {"Authorization": "Bearer " + access_token, …} 
```

다음 Python 코드 샘플은 액세스 토큰을 검색합니다. 그런 다음, 해당 토큰을 FarmBeats에 대한 후속 API 호출에 사용할 수 있습니다.

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

## <a name="http-request-headers"></a>HTTP 요청 헤더

FarmBeats Datahub에 대한 API 호출을 수행할 때 지정해야 하는 가장 일반적인 요청 헤더는 다음과 같습니다.

**머리글** | **설명 및 예**
--- | ---
콘텐츠 형식  | 요청 형식입니다(Content-Type: application/\<format\>). FarmBeats Datahub API의 경우 형식은 JSON입니다. Content-Type: application/json
권한 부여 | API 호출을 수행하는 데 필요한 액세스 토큰을 지정합니다. 권한 부여: Bearer \<Access-Token\>
수락  | 응답 형식입니다. FarmBeats Datahub API의 경우 형식은 JSON입니다. Accept: application/json


## <a name="api-requests"></a>API 요청

REST API 요청을 수행하려면 다음을 결합합니다.

- HTTP 메서드(GET, POST 및 PUT)
- API 서비스에 대한 URL
- 리소스 URI(쿼리, 데이터 제출, 업데이트 또는 삭제용)
- 하나 이상의 HTTP 요청 헤더

필요에 따라 GET 호출에 쿼리 매개 변수를 포함하여 응답에서 데이터를 필터링하고, 크기를 제한하고, 정렬할 수 있습니다.

다음 샘플 요청은 디바이스 목록을 가져오는 요청입니다.

```bash
curl -X GET "https://microsoft-farmbeats.azurewebsites.net/Device" -H
"Content-Type: application/json" -H
"Authorization: Bearer <Access-Token>"
```

대부분의 GET, POST 및 PUT 호출에는 JSON 요청 본문이 필요합니다.

다음 샘플 요청은 디바이스를 만들기 위한 것입니다. 이 샘플에는 요청 본문을 포함하는 입력 JSON이 있습니다.


```bash
curl -X POST "https://microsoft-farmbeats.azurewebsites.net/Device" -H
"accept: application/json" -H
"Content-Type: application/json" -H "Authorization: Bearer <Access-Token>" -d
"{  \"deviceModelId\": \"ID123\",  \"hardwareId\": \"MHDN123\",  \"reportingInterval\": 900,
\"name\": \"Device123\",  \"description\": \"Test Device 123\",}"
```

## <a name="data-format"></a>데이터 형식

JSON은 임의 데이터 구조의 단순한 텍스트 표현을 제공하는 공용 언어 독립적 데이터 형식입니다. 자세한 내용은 [JSON org](https://JSON.org)를 참조하세요.

## <a name="ingest-imagery-into-farmbeats"></a>FarmBeats에 이미지 수집

파트너가 FarmBeats Datahub에 연결할 자격 증명을 확보하면 파트너는 Translator 구성 요소에서 다음 단계를 수행합니다.

1. 업로드할 이미지 유형에 따라 다음 필드에 대한 새 확장 유형을 만듭니다.

   - **장면 소스**: 예: drone_partner_name
   - **장면 형식**: 예: drone
   - **장면 파일 형식**: 예: chlorophyll index
   - **장면 파일 콘텐츠 형식**: 예: image/tiff

2. /Farms API를 호출하여 Azure FarmBeats 시스템 내에서 팜 목록을 가져옵니다.

3. 팜 목록에서 단일 팜을 선택할 수 있는 기능을 고객에게 제공합니다.

   파트너 시스템은 경로 계획과 드론 비행 및 이미지 수집을 수행할 파트너 소프트웨어 내에 팜이 표시되어야 합니다.

4. /Scene API를 호출하고 고유한 장면 ID로 새 장면을 만드는 데 필요한 세부 정보를 제공합니다.

5. FarmBeats 시스템에서 선택한 팜의 컨텍스트에서 필요한 이미지를 FarmBeats Datahub에 업로드하기 위한 Blob SAS URL을 수신합니다.

API 호출에 대한 자세한 흐름은 다음과 같습니다.

### <a name="step-1-extendedtype"></a>1단계: ExtendedType

/ExtendedType API를 확인하여 FarmBeats에서 형식 및 파일 원본을 사용할 수 있는지 확인합니다. 이렇게 하려면 /ExtendedType API에서 GET을 호출합니다.

시스템 정의 값은 다음과 같습니다.

```json
{
  "items": [
    {
      "id": "ae2aa527-7d27-42e2-8fcf-841937c651d7",
      "createdAt": "2019-06-17T07:32:30.1135716Z",
      "lastModifiedAt": "2019-10-25T09:47:42.5910344Z",
      "key": "SceneFileType",
      "value": [
        "evi",
        "ndvi",
        "ndwi",
        "tci",
        "soil-moisture",
        "sensor-placement",
        "sentinel-b01",
        "sentinel-b02",
        "sentinel-b03",
        "sentinel-b04",
        "sentinel-b05",
        "sentinel-b06",
        "sentinel-b07",
        "sentinel-b08",
        "sentinel-b8a",
        "sentinel-b09",
        "sentinel-b10",
        "sentinel-b11",
        "sentinel-b12",
        "cloud-mask",
        "farm-mask",
        "image/tiff",
        "orthomosaic"
      ],
      "description": "List of scene file types available in system. User can add more values."
    },
    {
      "id": "b7824d2e-3e43-46d3-8fd4-ec7ba8918d84",
      "createdAt": "2019-06-17T07:32:30.1135716Z",
      "lastModifiedAt": "2019-10-25T09:45:44.8914691Z",
      "key": "SceneFileContentType",
      "value": [
        "image/tiff",
        "image/png",
        "image/jpeg",
        "text/csv",
        "text/plain",
        "text/tab-separated-values",
        "application/json",
        "application/octet-stream",
        "orthomosaic"
      ],
      "description": "List of scene file content types available in system. User can add more values."
    },
    {
      "id": "21d2924d-8b3e-4ce4-a7f8-318e85c0f7f2",
      "createdAt": "2019-06-17T07:32:30.1135716Z",
      "lastModifiedAt": "2019-10-25T09:45:37.5096445Z",
      "key": "SceneSource",
      "value": [
        "sentinel-l1c",
        "sentinel-l2a",
        "farmbeats-model",
        "dji",
        "SlantRange-3P",
        "DJI"
      ],
      "description": "List of scene sources available in system. User can add more values."
    },
    {
      "id": "6e09ca69-cacf-4b53-b63a-53c659aae4a4",
      "createdAt": "2019-06-17T07:32:30.1135716Z",
      "lastModifiedAt": "2019-10-25T09:45:34.7483899Z",
      "key": "SceneType",
      "value": [
        "base-bands",
        "sensor-placement",
        "soil-moisture",
        "evi",
        "ndwi",
        "ndvi",
        "drone",
        "orthomosaic-drone"
      ],
      "description": "List of scene types available in system. User can add more values."
    },
    {
      "id": "55d8436b-3a86-4bea-87ab-e1b51226525f",
      "createdAt": "2019-06-17T07:32:30.1135716Z",
      "lastModifiedAt": "2019-06-17T07:32:30.1135716Z",
      "key": "SensorMeasureType",
      "value": [
        "AmbientTemperature",
        "CO2",
        "Depth",
        "ElectricalConductivity",
        "LeafWetness",
        "Length",
        "LiquidLevel",
        "Nitrate",
        "O2",
        "PH",
        "Phosphate",
        "PointInTime",
        "Potassium",
        "Pressure",
        "RainGauge",
        "RelativeHumidity",
        "Salinity",
        "SoilMoisture",
        "SoilTemperature",
        "SolarRadiation",
        "State",
        "TimeDuration",
        "UVRadiation",
        "UVIndex",
        "Volume",
        "WindDirection",
        "WindRun",
        "WindSpeed",
        "Evapotranspiration",
        "PAR"
      ],
      "description": "List of sensor measure types available in system. User can add more values."
    },
    {
      "id": "0dfd6e6b-df58-428f-9ab8-a0674bfdcbe5",
      "createdAt": "2019-06-17T07:32:30.1135716Z",
      "lastModifiedAt": "2019-06-17T07:32:30.1135716Z",
      "key": "SensorMeasureUnit",
      "value": [
        "NoUnit",
        "Celsius",
        "Fahrenheit",
        "Kelvin",
        "Rankine",
        "Pascal",
        "Mercury",
        "PSI",
        "MilliMeter",
        "CentiMeter",
        "Meter",
        "Inch",
        "Feet",
        "Mile",
        "KiloMeter",
        "MilesPerHour",
        "MilesPerSecond",
        "KMPerHour",
        "KMPerSecond",
        "MetersPerHour",
        "MetersPerSecond",
        "Degree",
        "WattsPerSquareMeter",
        "KiloWattsPerSquareMeter",
        "MilliWattsPerSquareCentiMeter",
        "MilliJoulesPerSquareCentiMeter",
        "VolumetricWaterContent",
        "Percentage",
        "PartsPerMillion",
        "MicroMol",
        "MicroMolesPerLiter",
        "SiemensPerSquareMeterPerMole",
        "MilliSiemensPerCentiMeter",
        "Centibar",
        "DeciSiemensPerMeter",
        "KiloPascal",
        "VolumetricIonContent",
        "Liter",
        "MilliLiter",
        "Seconds",
        "UnixTimestamp",
        "MicroMolPerMeterSquaredPerSecond",
        "InchesPerHour"
      ],
      "description": "List of sensor measure units available in system. User can add more values."
    }
  ]
}
```

이 단계는 일회성 설정입니다. 이 새 장면 형식의 범위는 Azure FarmBeats가 설치된 구독으로 제한됩니다.

예를 들어 SceneSource: "SlantRange"를 추가하려면 키 "SceneSource" 입력 페이로드를 사용하여 /ExtendedType API의 ID에 PUT을 수행합니다.

```json
{

      "key": "SceneSource",
      "value": [
        "sentinel-l1c",
        "sentinel-l2a",
        "farmbeats-model",
        "dji",
        "SlantRange-3P"
      ],
      "description": "List of scene sources available in system. User can add more values."
}

```

녹색 필드는 시스템 정의 장면 원본 값에 새로 추가된 것입니다.

### <a name="step-2-get-farm-details"></a>2단계: 팜 세부 정보 가져오기

장면(.tiff 또는 .csv 파일)은 팜의 컨텍스트에 있습니다. /Farm API에서 GET을 수행하여 팜 세부 정보를 가져와야 합니다. API는 FarmBeats에서 사용할 수 있는 팜 목록을 반환합니다. 데이터를 수집하려는 팜을 선택할 수 있습니다.

GET /Farm 응답:

```json
{
  "id": "07f3e09c-89aa-4619-8d50-e57fb083d8f9",
  "createdAt": "2019-11-01T13:55:41.8804663Z",
  "lastModifiedAt": "2019-11-01T13:55:41.8804663Z",
  "geometry": {
    "type": "Polygon",
    "coordinates": [
      [
        [
          78.34252251428694,
          17.402556352862675
        ],
        [
          78.34278000635095,
          17.407920852463022
        ],
        [
          78.34883106989963,
          17.40878079576528
        ],
        [
          78.3498181228054,
          17.403539173730607
        ],
        [
          78.34805859369493,
          17.39977166504127
        ],
        [
          78.34252251428694,
          17.402556352862675
        ]
      ]
    ]
  },
  "name": "Samplefarm",
  "properties": {
    "crops": "Corn",
    "address": "Sample street"
  }
}
 ```

### <a name="step-3-create-a-scene-id-post-call"></a>3단계: 장면 ID 만들기(POST 호출)

장면과 연결된 날짜, 시퀀스 및 팜 ID를 제공하는 지정된 정보로 새 장면(.tiff 또는 .csv 파일)을 만듭니다. 장면과 연결된 메타데이터는 기간 및 측정값 유형을 포함하는 속성에서 정의할 수 있습니다.

새 장면을 만들면 팜과 연결된 새 장면 ID가 만들어집니다. 장면 ID가 생성되면 사용자는 이것을 사용하여 새 파일(.tiff 또는 .csv)을 만들고 파일의 내용을 저장할 수 있습니다.

/Scene API의 POST 호출에 대한 예제 입력 페이로드:

```json
{
  "sceneId": "a0505928-c480-491b-ba31-d38285a28c1d",
  "type": "newtype",
  "contentType": "image/tiff",
  "name": "test scene file",
  "description": "test scene file description",
  "properties": {
    "additionalProp1": {},
    "additionalProp2": {},
    "additionalProp3": {}
  }
}
```

API 응답:

```json
{
  "id": "a0505928-c480-491b-ba31-d38285a28c1d",
  "createdAt": "2019-10-04T16:19:12.4838584Z",
  "lastModifiedAt": "2019-10-04T16:19:12.4838584Z",
  "type": "new type",
  "source": "SlantRange-3P",
  "farmId": "d41a33e7-b73e-480e-9279-0fcb3207332b",
  "date": "2019-10-04T16:13:39.064Z",
  "sequence": 5,
  "name": "test scene",
  "description": "test scene description",
  "properties": {}
}

```

**장면 파일 만들기**

3단계에서 반환된 장면 ID는 장면 파일에 대한 입력입니다. 장면 파일은 24시간 동안 유효한 SAS URL 토큰을 반환합니다.

사용자가 이미지 스트림을 프로그래밍 방식으로 업로드해야 하는 경우 Blob Storage SDK에서 장면 파일 ID, 위치 및 URL을 사용하여 메서드를 정의할 수 있습니다.

/SceneFile API의 POST 호출에 대한 예제 입력 페이로드:

```json
{
  "sceneId": "a0505928-c480-491b-ba31-d38285a28c1d",
  "type": "newtype",
  "contentType": "image/tiff",
  "name": "test scene file",
  "description": "test scene file description",
  "properties": {
    "additionalProp1": {},
    "additionalProp2": {},
    "additionalProp3": {}
  }
}
```
API 응답:

```json
{
  "uploadSASUrl": "https://storagej2lho.blob.core.windows.net/farm-scene/2019/a0505928-c480-491b-ba31-d38285a28c1d/e91139a7-4ebd-4e2f-b17c-c677822dc840?sv=2018-03-28&sr=b&sig=%2F1426JkDcIFE5g3d%2BjOevCVMIn%2FJo9YKwBn3La5zL8Y%3D&se=2019-10-05T16%3A23%3A57Z&sp=w",
  "id": "e91139a7-4ebd-4e2f-b17c-c677822dc840",
  "createdAt": "2019-10-04T16:23:57.1192916Z",
  "lastModifiedAt": "2019-10-04T16:23:57.1192916Z",
  "blobUrl": "https://storagej2lho.blob.core.windows.net/farm-scene/2019/a0505928-c480-491b-ba31-d38285a28c1d/e91139a7-4ebd-4e2f-b17c-c677822dc840",
  "sceneId": "a0505928-c480-491b-ba31-d38285a28c1d",
  "type": "newtype",
  "contentType": "image/tiff",
  "name": "test scene file",
  "description": "test scene file description",
  "properties": {}
}


```

/SceneFile API에 대한 POST 호출은 Azure Blob Storage 클라이언트 또는 라이브러리를 사용하여 .csv 또는 .tiff 파일을 업로드하는 데 사용할 수 있는 SAS 업로드 URL을 반환합니다.


## <a name="next-steps"></a>다음 단계

REST API 기반 통합 세부 정보에 대한 자세한 내용은 [REST API](rest-api-in-azure-farmbeats.md)를 참조하세요.