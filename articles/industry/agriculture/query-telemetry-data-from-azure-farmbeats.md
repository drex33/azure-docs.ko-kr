---
title: 수집된 원격 분석 데이터 쿼리
description: 이 문서에서는 수집된 원격 분석 데이터를 쿼리하는 방법을 설명합니다.
author: sunasing
ms.topic: article
ms.date: 03/11/2020
ms.author: sunasing
ms.openlocfilehash: 88cf9236ca33eddd9f86a60c210aae253f8d9c2c
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/01/2021
ms.locfileid: "129361018"
---
# <a name="query-ingested-telemetry-data"></a>수집된 원격 분석 데이터 쿼리

이 문서에서는 Azure FarmBeats에서 수집한 센서 데이터를 쿼리하는 방법을 설명합니다.

디바이스, 센서 등과 같은 IoT(사물 인터넷) 리소스에서 데이터 수집은 FarmBeats에서의 일반적인 시나리오입니다. 디바이스와 센서에 대한 메타데이터를 만든 다음 FarmBeats에 대한 기록 데이터를 정규형으로 수집합니다. FarmBeats Datahub에서 센서 데이터를 사용할 수 있으면 동일한 쿼리를 실행하여 실행 가능한 인사이트를 생성하거나 모델을 빌드할 수 있습니다.

## <a name="before-you-begin"></a>시작하기 전에

이 문서를 진행하기 전에 FarmBeats를 설치했고, FarmBeats에 대한 IoT 디바이스에서 센서 원격 분석 데이터 수집했는지 확인합니다.

센서 원격 분석 데이터를 수집하려면 [기록 원격 분석 데이터 수집](ingest-historical-telemetry-data-in-azure-farmbeats.md)을 참조하세요.

계속하기 전에 API를 사용하여 수집한 원격 분석 데이터를 쿼리할 때 FarmBeats REST API에 대해 잘 알고 있어야 합니다. FarmBeats API에 대한 자세한 내용은 [FarmBeats REST APIs](rest-api-in-azure-farmbeats.md)를 참조하세요. **FarmBeats Datahub 엔드포인트에 대한 API 요청을 수행할 수 있는지 확인** 합니다.

## <a name="query-ingested-sensor-telemetry-data"></a>수집된 센서 원격 분석 데이터 쿼리

FarmBeats에서 원격 분석 데이터에 액세스하고 해당 데이터를 쿼리하는 방법에는 다음 두 가지가 있습니다.

- API 및
- TSI(Time Series Insights)

### <a name="query-using-rest-api"></a>REST API를 사용하여 쿼리

FarmBeats REST API를 사용하여 수집한 센서 원격 분석 데이터를 쿼리하는 단계를 수행합니다.

1. 관심이 있는 센서를 확인합니다. 이렇게 하려면 /Sensor API에 대해 GET 요청을 수행합니다.

> [!NOTE]
> 관심 있는 센서 개체의 **id** 및 **sensorModelId**.

2. 1단계에서 설명한 대로 **sensorModelId** 의 /SensorModel API에 대해 GET/{id}를 만듭니다. “센서 모델”에는 센서에서 수집한 원격 분석 데이터에 대한 모든 메타데이터와 세부 정보가 포함되어 있습니다. 예를 들어 **센서 모델** 개체 내의 **센서 측정값** 에는 센서에서 전송 하는 측정값과 해당 유형 및 단위에 대한 세부 정보가 있습니다. 예제:

  ```json
  {
      "name": "moist_soil_last <name of the sensor measure - this is what we will receive as part of the queried telemetry data>",
      "dataType": "Double <Data Type - eg. Double>",
      "type": "SoilMoisture <Type of measure eg. temperature, soil moisture etc.>",
      "unit": "Percentage <Unit of measure eg. Celsius, Percentage etc.>",
      "aggregationType": "None <either of None, Average, Maximum, Minimum, StandardDeviation>",
      "description": "<Description of the measure>"
  }
  ```
센서 모델에 대한 GET/{id} 호출의 응답을 기록해 둡니다.

3. 다음 입력 페이로드를 사용하여 /Telemetry API에 대한 POST 호출을 수행합니다.

  ```json
  {
    "sensorId": "<id of the sensor as noted in step 1>",
    "searchSpan": {
      "from": "<desired start timestamp in ISO 8601 format; default is UTC>",
      "to": "<desired end timestamp in ISO 8601 format; default is UTC>"
    },
    "filter": {
      "tsx": "string"
    },
    "projectedProperties": [
      {
        "additionalProp1": "string",
        "additionalProp2": "string",
        "additionalProp3": "string"
      }
    ]
  }
  ```
4. /Telemetry API의 응답은 다음과 같습니다.

  ```json
  {
    "timestamps": [
      "2020-XX-XXT07:30:00Z",
      "2020-XX-XXT07:45:00Z"
    ],
    "properties": [
      {
        "values": [
          "<id of the sensor>",
          "<id of the sensor>"
        ],
        "name": "Id",
        "type": "String"
      },
      {
        "values": [
          2.1,
          2.2
        ],
        "name": "moist_soil_last <name of the SensorMeasure as defined in the SensorModel object>",
        "type": "Double <Data Type of the value - eg. Double>"
      }
    ]
  }
  ```
위의 예제 응답에서 쿼리한 센서 원격 분석 데이터는 측정값 이름(“moist_soil_last”)과 두 타임스탬프에 보고된 원격 분석 데이터의 값과 함께 두 타임스탬프에 대한 데이터를 제공합니다. (2단계에서 설명한 대로) 연결된 센서 모델을 참조하여 보고된 값의 형식 및 단위를 해석해야 합니다.

### <a name="query-using-azure-time-series-insights-tsi"></a>Azure TSI(Time Series Insights)를 사용하여 쿼리

FarmBeats는 [Azure TSI(Azure Time Series Insights)](https://azure.microsoft.com/services/time-series-insights/)를 활용하여 고도로 컨텍스트화되고 시계열에 최적화된 데이터를 IoT 규모로 수집, 저장, 쿼리, 시각화할 수 있습니다.

원격 분석 데이터는 EventHub에서 수신된 다음 처리되어 FarmBeats 리소스 그룹 내의 TSI 환경으로 푸시됩니다. 그런 다음 데이터를 TSI에서 직접 쿼리할 수 있습니다. 자세한 내용은 [TSI 설명서](../../time-series-insights/time-series-insights-explorer.md)를 참조하세요.

다음 단계에 따라 TSI에서 데이터를 시각화합니다.

1. **Azure Portal** > **FarmBeats DataHub 리소스 그룹** > **Time Series Insights** 환경(tsi-xxxx) 선택 > **데이터 액세스 정책** 으로 이동합니다. 읽기 권한자 또는 기여자 액세스 권한이 있는 사용자 추가
2. **Time Series Insights** 환경(tsi-xxxx)의 **개요** 페이지로 이동하고 **Time Series Insights 탐색기 URL** 을 선택합니다. 이제 수집된 원격 분석 데이터를 시각화할 수 있습니다.

TSI는 원격 분석 데이터를 저장, 쿼리, 시각화하는 것 외에도 Power BI 대시보드로 통합할 수 있도록 합니다. 자세한 내용은 [여기](../../time-series-insights/how-to-connect-power-bi.md)를 참조하세요.

## <a name="next-steps"></a>다음 단계

이제 Azure FarmBeats 인스턴스에서 센서 데이터를 쿼리합니다. 이제, 팜의 [맵을 생성](generate-maps-in-azure-farmbeats.md#generate-maps)하는 방법을 알아봅니다.