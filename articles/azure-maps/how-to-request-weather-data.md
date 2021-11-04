---
title: Azure Maps Weather Services를 사용하여 실시간 및 예측 날씨 데이터 요청
description: Microsoft Azure Maps Weather Services를 사용하여 실시간(현재) 및 예측(분, 시간별, 일별) 날씨 데이터를 요청하는 방법 알아보기
author: stevemunk
ms.author: v-munksteve
ms.date: 10/28/2021
ms.topic: how-to
ms.service: azure-maps
services: azure-maps
ms.custom: mvc
ms.openlocfilehash: 6ee9fefcb0675f35eebfe78b980d6e04d44f5a72
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131435266"
---
# <a name="request-real-time-and-forecasted-weather-data-using-azure-maps-weather-services"></a>Azure Maps Weather Services를 사용하여 실시간 및 예측 날씨 데이터 요청

Azure Maps [Weather Services](/rest/api/maps/weather)는 개발자가 매우 동적인 기록, 실시간 및 예측 날씨 데이터와 시각화를 솔루션에 통합할 수 있도록 하는 RESTful API 집합입니다. 이 문서에서는 실시간 및 예측 날씨 데이터를 요청하는 방법을 보여줍니다.

이 문서에서는 다음에 대해 알아봅니다:

* [Get Current Conditions API](/rest/api/maps/weather/getcurrentconditions)를 사용하여 실시간(현재) 날씨 데이터를 요청합니다.
* [Get Severe Weather Alerts API](/rest/api/maps/weather/getsevereweatheralerts)를 사용하여 심각한 날씨 경보를 요청합니다.
* [Get Daily Forecast API](/rest/api/maps/weather/getdailyforecast)를 사용하여 일별 예측을 요청합니다.
* [Get Hourly Forecast API](/rest/api/maps/weather/gethourlyforecast)를 사용하여 시간별 예측을 요청합니다.
* [Get Minute Forecast API](/rest/api/maps/weather/getminuteforecast)를 사용하여 분 단위 예측을 요청합니다.

이 비디오는 Azure Maps Weather Services에 대한 REST 호출 예제를 제공합니다.

</br>

>[!VIDEO https://channel9.msdn.com/Shows/Internet-of-Things-Show/Azure-Maps-Weather-services-for-developers/player?format=ny]

## <a name="prerequisites"></a>사전 요구 사항

1. [Azure Maps 계정을 만듭니다](quick-demo-map-app.md#create-an-azure-maps-account).
2. 기본 키 또는 구독 키라고도 하는 [기본 구독 키를 가져옵니다](quick-demo-map-app.md#get-the-primary-key-for-your-account). Azure Maps의 인증에 대한 자세한 내용은 [Azure Maps의 인증 관리](./how-to-manage-authentication.md)를 참조하세요.

    >[!IMPORTANT]
    >[Get Minute Forecast API](/rest/api/maps/weather/getminuteforecast)에는 Gen 1(S1) 또는 Gen 2 가격 책정 계층이 필요합니다.  다른 모든 API에는 S0 가격 책정 계층 키가 필요합니다.

이 자습서에서는 [Postman](https://www.postman.com/) 애플리케이션을 사용하지만 다른 API 개발 환경을 선택할 수도 있습니다.

## <a name="request-real-time-weather-data"></a>실시간 날씨 데이터 요청

[Get Current Conditions API](/rest/api/maps/weather/getcurrentconditions)는 특정 좌표 위치에 대한 강수량, 온도 및 바람과 같은 자세한 날씨 상황을 반환합니다. 또한 특정 위치에 대해 지난 6 시간 또는 24 시간 동안의 관측 값을 검색할 수 있습니다. 이 응답에는 관측 날짜 및 시간, 날씨 상황에 대한 간단한 설명, 날씨 아이콘, 강수량, 강수량 표시기 플래그 및 온도와 같은 세부 정보가 포함됩니다. RealFeel™ 온도 및 자외선(UV) 지수도 반환됩니다.

이 예제에서는 [Get Current Conditions API](/rest/api/maps/weather/getcurrentconditions)를 사용하여 워싱턴주 시애틀에 위치한 좌표에서 현재 날씨 상황을 검색합니다.

1. Postman 앱을 엽니다. **새로 만들기** 를 선택하여 요청을 만듭니다. **새로 만들기** 창에서 **HTTP 요청** 을 선택합니다. 요청에 대한 **요청 이름** 을 입력합니다. 

2. 작성기 탭에서 **GET** HTTP 메서드를 선택하고 다음 URL을 입력합니다. 이 요청 및 이 문서에 언급된 기타 요청에 대한 `{Azure-Maps-Primary-Subscription-key}`를 기본 구독 키로 바꿉니다.

    ```http
    https://atlas.microsoft.com/weather/currentConditions/json?api-version=1.0&query=47.60357,-122.32945&subscription-key={Your-Azure-Maps-Primary-Subscription-key}
    ```

3. 파란색 **보내기** 단추를 클릭합니다. 응답 본문에는 현재 날씨 정보가 포함됩니다.

    ```json
    {
    "results": [
        {
            "dateTime": "2020-10-19T20:39:00+00:00",
            "phrase": "Cloudy",
            "iconCode": 7,
            "hasPrecipitation": false,
            "isDayTime": true,
            "temperature": {
                "value": 12.4,
                "unit": "C",
                "unitType": 17
            },
            "realFeelTemperature": {
                "value": 13.7,
                "unit": "C",
                "unitType": 17
            },
            "realFeelTemperatureShade": {
                "value": 13.7,
                "unit": "C",
                "unitType": 17
            },
            "relativeHumidity": 87,
            "dewPoint": {
                "value": 10.3,
                "unit": "C",
                "unitType": 17
            },
            "wind": {
                "direction": {
                    "degrees": 23.0,
                    "localizedDescription": "NNE"
                },
                "speed": {
                    "value": 4.5,
                    "unit": "km/h",
                    "unitType": 7
                }
            },
            "windGust": {
                "speed": {
                    "value": 9.0,
                    "unit": "km/h",
                    "unitType": 7
                }
            },
            "uvIndex": 1,
            "uvIndexPhrase": "Low",
            "visibility": {
                "value": 9.7,
                "unit": "km",
                "unitType": 6
            },
            "obstructionsToVisibility": "",
            "cloudCover": 100,
            "ceiling": {
                "value": 1494.0,
                "unit": "m",
                "unitType": 5
            },
            "pressure": {
                "value": 1021.2,
                "unit": "mb",
                "unitType": 14
            },
            "pressureTendency": {
                "localizedDescription": "Steady",
                "code": "S"
            },
            "past24HourTemperatureDeparture": {
                "value": -2.1,
                "unit": "C",
                "unitType": 17
            },
            "apparentTemperature": {
                "value": 15.0,
                "unit": "C",
                "unitType": 17
            },
            "windChillTemperature": {
                "value": 12.2,
                "unit": "C",
                "unitType": 17
            },
            "wetBulbTemperature": {
                "value": 11.3,
                "unit": "C",
                "unitType": 17
            },
            "precipitationSummary": {
                "pastHour": {
                    "value": 0.0,
                    "unit": "mm",
                    "unitType": 3
                },
                "past3Hours": {
                    "value": 0.0,
                    "unit": "mm",
                    "unitType": 3
                },
                "past6Hours": {
                    "value": 0.0,
                    "unit": "mm",
                    "unitType": 3
                },
                "past9Hours": {
                    "value": 0.0,
                    "unit": "mm",
                    "unitType": 3
                },
                "past12Hours": {
                    "value": 0.0,
                    "unit": "mm",
                    "unitType": 3
                },
                "past18Hours": {
                    "value": 0.0,
                    "unit": "mm",
                    "unitType": 3
                },
                "past24Hours": {
                    "value": 0.4,
                    "unit": "mm",
                    "unitType": 3
                }
            },
            "temperatureSummary": {
                "past6Hours": {
                    "minimum": {
                        "value": 12.2,
                        "unit": "C",
                        "unitType": 17
                    },
                    "maximum": {
                        "value": 14.0,
                        "unit": "C",
                        "unitType": 17
                    }
                },
                "past12Hours": {
                    "minimum": {
                        "value": 12.2,
                        "unit": "C",
                        "unitType": 17
                    },
                    "maximum": {
                        "value": 14.0,
                        "unit": "C",
                        "unitType": 17
                    }
                },
                "past24Hours": {
                    "minimum": {
                        "value": 12.2,
                        "unit": "C",
                        "unitType": 17
                    },
                    "maximum": {
                        "value": 15.6,
                        "unit": "C",
                        "unitType": 17
                    }
                }
            }
        }
    ]
    }
    ```

## <a name="request-severe-weather-alerts"></a>심각한 날씨 경보 요청

[Azure Maps Get Severe Weather Alerts API](/rest/api/maps/weather/getsevereweatheralerts)는 정부 공식 기상청과 주요 전 세계 기상 경보 공급자 모두에서 전 세계적으로 사용할 수 있는 심각한 날씨 경보를 반환합니다. 이 서비스는 경보 유형, 범주, 수준 및 허리케인, 뇌우, 번개, 폭염 또는 산불과 같이 요청된 위치에 대한 활성 심각 경보에 대한 세부 정보를 반환할 수 있습니다. 예를 들어 물류 관리자는 사업장 위치 및 계획된 경로와 함께 심각한 날씨 상황을 지도에 시각화하고 운전자 및 현지 근로자와 더욱 긴밀하게 조정할 수 있습니다.

이 예제에서는 [Get Severe Weather Alerts API](/rest/api/maps/weather/getsevereweatheralerts)를 사용하여 와이오밍주 샤이엔에 위치한 좌표에서 현재 날씨 상황을 검색합니다.

>[!NOTE]
>이 예제에서는 작성 시의 심각한 날씨 경보를 검색합니다. 요청된 위치에 더 이상 심각한 날씨 경보가 없을 수 있습니다. 이 예제를 실행할 때 실제 심각한 경보 데이터를 검색하려면 다른 좌표 위치에서 데이터를 검색해야 합니다.

1. Postman 앱에서 **새로 만들기** 를 선택하여 요청을 만듭니다. **새로 만들기** 창에서 **HTTP 요청** 을 선택합니다. 요청에 대한 **요청 이름** 을 입력합니다.

2. 작성기 탭에서 **GET** HTTP 메서드를 선택하고 다음 URL을 입력합니다. 이 요청 및 이 문서에 언급된 기타 요청에 대한 `{Azure-Maps-Primary-Subscription-key}`를 기본 구독 키로 바꿉니다.

    ```http
    https://atlas.microsoft.com/weather/severe/alerts/json?api-version=1.0&query=41.161079,-104.805450&subscription-key={Your-Azure-Maps-Primary-Subscription-key}
    ```

3. 파란색 **보내기** 단추를 클릭합니다. 심각한 날씨 경보가 없는 경우 응답 본문에 빈 `results[]` 배열이 포함됩니다. 심각한 날씨 경보가 있는 경우 응답 본문에 다음 JSON 응답과 같은 내용이 포함됩니다:

    ```json
    {
    "results": [
        {
            "countryCode": "US",
            "alertId": 2194734,
            "description": {
                "localized": "Red Flag Warning",
                "english": "Red Flag Warning"
            },
            "category": "FIRE",
            "priority": 54,
            "source": "U.S. National Weather Service",
            "sourceId": 2,
            "alertAreas": [
                {
                    "name": "Platte/Goshen/Central and Eastern Laramie",
                    "summary": "Red Flag Warning in effect until 7:00 PM MDT.  Source: U.S. National Weather Service",
                    "startTime": "2020-10-05T15:00:00+00:00",
                    "endTime": "2020-10-06T01:00:00+00:00",
                    "latestStatus": {
                        "localized": "Continue",
                        "english": "Continue"
                    },
                    "alertDetails": "...RED FLAG WARNING REMAINS IN EFFECT FROM 9 AM THIS MORNING TO\n7 PM MDT THIS EVENING FOR STRONG GUSTY WINDS AND LOW HUMIDITY...\n\n* WHERE...Fire weather zones 303, 304, 305, 306, 307, 308, 309,\n  and 310 in southeast Wyoming. Fire weather zone 313 in Nebraska.\n\n* WIND...West to northwest 15 to 30 MPH with gusts around 40 MPH.\n\n* HUMIDITY...10 to 15 percent.\n\n* IMPACTS...Any fires that develop will likely spread rapidly.\n  Outdoor burning is not recommended.\n\nPRECAUTIONARY/PREPAREDNESS ACTIONS...\n\nA Red Flag Warning means that critical fire weather conditions\nare either occurring now...or will shortly. A combination of\nstrong winds...low relative humidity...and warm temperatures can\ncontribute to extreme fire behavior.\n\n&&",
                    "alertDetailsLanguageCode": "en"
                }
            ]
            },...
        ]
    }
    ```

## <a name="request-daily-weather-forecast-data"></a>일별 일기예보 데이터 요청

[Get Daily Forecast API](/rest/api/maps/weather/getdailyforecast)는 온도 및 바람과 같은 상세한 일별 일기예보를 반환합니다. 요청은 지정된 좌표 위치에 대해 1일, 5일, 10일, 15일, 25일 또는 45일 중 반환할 날 수를 지정할 수 있습니다. 응답에는 온도, 바람, 강수량, 대기 질 및 UV 지수와 같은 세부 정보가 포함됩니다.  이 예제에서는 `duration=5`를 설정하여 5일 동안 요청합니다.

>[!IMPORTANT]
>S0 가격 책정 계층에서는 다음 1일, 5일, 10일 및 15일에 대한 일일 예보를 요청할 수 있습니다. Gen 1(S1) 또는 Gen 2 가격 책정 계층에서 다음 25일 및 45일에 대한 일일 예보를 요청할 수 있습니다.

이 예제에서는 [Get Daily Forecast API](/rest/api/maps/weather/getdailyforecast)를 사용하여 워싱턴주 시애틀에 위치한 좌표에 대한 5일간의 일기예보를 검색합니다.

1. Postman 앱에서 **새로 만들기** 를 선택하여 요청을 만듭니다. **새로 만들기** 창에서 **HTTP 요청** 을 선택합니다. 요청에 대한 **요청 이름** 을 입력합니다.

2. 작성기 탭에서 **GET** HTTP 메서드를 선택하고 다음 URL을 입력합니다. 이 요청 및 이 문서에 언급된 기타 요청에 대한 `{Azure-Maps-Primary-Subscription-key}`를 기본 구독 키로 바꿉니다.

    ```http
    https://atlas.microsoft.com/weather/forecast/daily/json?api-version=1.0&query=47.60357,-122.32945&duration=5&subscription-key={Your-Azure-Maps-Primary-Subscription-key}
    ```

3. 파란색 **보내기** 단추를 클릭합니다. 응답 본문에는 5일간의 일기예보 데이터가 포함됩니다. 간단히 하기 위해 아래 JSON 응답은 첫째 날의 예측을 보여줍니다.

    ```json
    {
    "summary": {
        "startDate": "2020-10-18T17:00:00+00:00",
        "endDate": "2020-10-19T23:00:00+00:00",
        "severity": 2,
        "phrase": "Snow, mixed with rain at times continuing through Monday evening and a storm total of 3-6 cm",
        "category": "snow/rain"
    },
    "forecasts": [
        {
            "date": "2020-10-19T04:00:00+00:00",
            "temperature": {
                "minimum": {
                    "value": -1.1,
                    "unit": "C",
                    "unitType": 17
                },
                "maximum": {
                    "value": 1.3,
                    "unit": "C",
                    "unitType": 17
                }
            },
            "realFeelTemperature": {
                "minimum": {
                    "value": -6.0,
                    "unit": "C",
                    "unitType": 17
                },
                "maximum": {
                    "value": 0.5,
                    "unit": "C",
                    "unitType": 17
                }
            },
            "realFeelTemperatureShade": {
                "minimum": {
                    "value": -6.0,
                    "unit": "C",
                    "unitType": 17
                },
                "maximum": {
                    "value": 0.7,
                    "unit": "C",
                    "unitType": 17
                }
            },
            "hoursOfSun": 1.8,
            "degreeDaySummary": {
                "heating": {
                    "value": 18.0,
                    "unit": "C",
                    "unitType": 17
                },
                "cooling": {
                    "value": 0.0,
                    "unit": "C",
                    "unitType": 17
                }
            },
            "airAndPollen": [
                {
                    "name": "AirQuality",
                    "value": 23,
                    "category": "Good",
                    "categoryValue": 1,
                    "type": "Ozone"
                },
                {
                    "name": "Grass",
                    "value": 0,
                    "category": "Low",
                    "categoryValue": 1
                },
                {
                    "name": "Mold",
                    "value": 0,
                    "category": "Low",
                    "categoryValue": 1
                },
                {
                    "name": "Ragweed",
                    "value": 0,
                    "category": "Low",
                    "categoryValue": 1
                },
                {
                    "name": "Tree",
                    "value": 0,
                    "category": "Low",
                    "categoryValue": 1
                },
                {
                    "name": "UVIndex",
                    "value": 0,
                    "category": "Low",
                    "categoryValue": 1
                }
            ],
            "day": {
                "iconCode": 22,
                "iconPhrase": "Snow",
                "hasPrecipitation": true,
                "precipitationType": "Mixed",
                "precipitationIntensity": "Light",
                "shortPhrase": "Chilly with snow, 2-4 cm",
                "longPhrase": "Chilly with snow, accumulating an additional 2-4 cm",
                "precipitationProbability": 90,
                "thunderstormProbability": 0,
                "rainProbability": 54,
                "snowProbability": 85,
                "iceProbability": 8,
                "wind": {
                    "direction": {
                        "degrees": 36.0,
                        "localizedDescription": "NE"
                    },
                    "speed": {
                        "value": 9.3,
                        "unit": "km/h",
                        "unitType": 7
                    }
                },
                "windGust": {
                    "direction": {
                        "degrees": 70.0,
                        "localizedDescription": "ENE"
                    },
                    "speed": {
                        "value": 25.9,
                        "unit": "km/h",
                        "unitType": 7
                    }
                },
                "totalLiquid": {
                    "value": 4.3,
                    "unit": "mm",
                    "unitType": 3
                },
                "rain": {
                    "value": 0.5,
                    "unit": "mm",
                    "unitType": 3
                },
                "snow": {
                    "value": 2.72,
                    "unit": "cm",
                    "unitType": 4
                },
                "ice": {
                    "value": 0.0,
                    "unit": "mm",
                    "unitType": 3
                },
                "hoursOfPrecipitation": 9.0,
                "hoursOfRain": 1.0,
                "hoursOfSnow": 9.0,
                "hoursOfIce": 0.0,
                "cloudCover": 96
            },
            "night": {
                "iconCode": 29,
                "iconPhrase": "Rain and snow",
                "hasPrecipitation": true,
                "precipitationType": "Mixed",
                "precipitationIntensity": "Light",
                "shortPhrase": "Showers of rain and snow",
                "longPhrase": "A couple of showers of rain or snow this evening; otherwise, cloudy; storm total snowfall 1-3 cm",
                "precipitationProbability": 65,
                "thunderstormProbability": 0,
                "rainProbability": 60,
                "snowProbability": 54,
                "iceProbability": 4,
                "wind": {
                    "direction": {
                        "degrees": 16.0,
                        "localizedDescription": "NNE"
                    },
                    "speed": {
                        "value": 16.7,
                        "unit": "km/h",
                        "unitType": 7
                    }
                },
                "windGust": {
                    "direction": {
                        "degrees": 1.0,
                        "localizedDescription": "N"
                    },
                    "speed": {
                        "value": 35.2,
                        "unit": "km/h",
                        "unitType": 7
                    }
                },
                "totalLiquid": {
                    "value": 4.3,
                    "unit": "mm",
                    "unitType": 3
                },
                "rain": {
                    "value": 3.0,
                    "unit": "mm",
                    "unitType": 3
                },
                "snow": {
                    "value": 0.79,
                    "unit": "cm",
                    "unitType": 4
                },
                "ice": {
                    "value": 0.0,
                    "unit": "mm",
                    "unitType": 3
                },
                "hoursOfPrecipitation": 4.0,
                "hoursOfRain": 1.0,
                "hoursOfSnow": 3.0,
                "hoursOfIce": 0.0,
                "cloudCover": 94
            },
            "sources": [
                "AccuWeather"
            ]
        },...
    ]
    }
    ```

## <a name="request-hourly-weather-forecast-data"></a>시간별 일기예보 데이터 요청

[Get Hourly Forecast API](/rest/api/maps/weather/gethourlyforecast)는 지정된 좌표 위치에 대해 다음 1시간, 12시간, 24시간(1일), 72시간(3일), 120시간(5일) 및 240시간(10일)에 대한 시간별 자세한 일기예보를 반환합니다. API는 온도, 습도, 바람, 강수량 및 UV 지수와 같은 세부 정보를 반환합니다.

>[!IMPORTANT]
>S0 가격 책정 계층에서는 다음 1시간, 12시간, 24시간(1일), 72시간(3일)에 대한 시간별 예보를 요청할 수 있습니다. Gen 1(S1) 또는 Gen 2 가격 책정 계층에서 다음 120시간(5일) 및 240시간(10일)에 대한 시간별 예보를 요청할 수 있습니다.

이 예제에서는 [Get Hourly Forecast API](/rest/api/maps/weather/gethourlyforecast)를 사용하여 워싱턴주 시애틀에 위치한 좌표에 대한 향후 12시간 동안의 시간별 일기예보를 검색합니다.

1. Postman 앱에서 **새로 만들기** 를 선택하여 요청을 만듭니다. **새로 만들기** 창에서 **HTTP 요청** 을 선택합니다. 요청에 대한 **요청 이름** 을 입력합니다.

2. 작성기 탭에서 **GET** HTTP 메서드를 선택하고 다음 URL을 입력합니다. 이 요청 및 이 문서에 언급된 기타 요청에 대한 `{Azure-Maps-Primary-Subscription-key}`를 기본 구독 키로 바꿉니다.

    ```http
    https://atlas.microsoft.com/weather/forecast/hourly/json?api-version=1.0&query=47.60357,-122.32945&duration=12&subscription-key={Your-Azure-Maps-Primary-Subscription-key}
    ```

3. 파란색 **보내기** 단추를 클릭합니다. 응답 본문에는 다음 12시간 동안의 일기예보 데이터가 포함됩니다. 간단히 하기 위해 아래 JSON 응답은 첫 번째 시간의 예측을 보여줍니다.

    ```json
    {
    "forecasts": [
        {
            "date": "2020-10-19T21:00:00+00:00",
            "iconCode": 12,
            "iconPhrase": "Showers",
            "hasPrecipitation": true,
            "precipitationType": "Rain",
            "precipitationIntensity": "Light",
            "isDaylight": true,
            "temperature": {
                "value": 14.7,
                "unit": "C",
                "unitType": 17
            },
            "realFeelTemperature": {
                "value": 13.3,
                "unit": "C",
                "unitType": 17
            },
            "wetBulbTemperature": {
                "value": 12.0,
                "unit": "C",
                "unitType": 17
            },
            "dewPoint": {
                "value": 9.5,
                "unit": "C",
                "unitType": 17
            },
            "wind": {
                "direction": {
                    "degrees": 242.0,
                    "localizedDescription": "WSW"
                },
                "speed": {
                    "value": 9.3,
                    "unit": "km/h",
                    "unitType": 7
                }
            },
            "windGust": {
                "speed": {
                    "value": 14.8,
                    "unit": "km/h",
                    "unitType": 7
                }
            },
            "relativeHumidity": 71,
            "visibility": {
                "value": 9.7,
                "unit": "km",
                "unitType": 6
            },
            "cloudCover": 100,
            "ceiling": {
                "value": 1128.0,
                "unit": "m",
                "unitType": 5
            },
            "uvIndex": 1,
            "uvIndexPhrase": "Low",
            "precipitationProbability": 51,
            "rainProbability": 51,
            "snowProbability": 0,
            "iceProbability": 0,
            "totalLiquid": {
                "value": 0.3,
                "unit": "mm",
                "unitType": 3
            },
            "rain": {
                "value": 0.3,
                "unit": "mm",
                "unitType": 3
            },
            "snow": {
                "value": 0.0,
                "unit": "cm",
                "unitType": 4
            },
            "ice": {
                "value": 0.0,
                "unit": "mm",
                "unitType": 3
            }
        }...
    ]
    }
    ```

## <a name="request-minute-by-minute-weather-forecast-data"></a>분 단위 일기예보 데이터 요청

 [Get Minute Forecast API](/rest/api/maps/weather/getminuteforecast)는 다음 120분 동안 지정된 위치에 대한 분 단위 예측을 반환합니다. 사용자는 1분, 5분, 15분 간격으로 일기예보를 요청할 수 있습니다. 응답에는 강수 유형(비, 눈 또는 둘 다 포함), 시작 시각 및 강수 강도 값 (dBZ)과 같은 세부 정보가 포함됩니다.

이 예제에서는 [Get Minute Forecast API](/rest/api/maps/weather/getminuteforecast)를 사용하여 워싱턴주 시애틀에 위치한 좌표에 대한 분 단위 일기예보를 검색합니다. 다음 120분 동안 일기예보가 제공됩니다. 쿼리는 15분 간격으로 예측이 제공되도록 요청하지만 매개 변수를 1분 또는 5분으로 조정할 수 있습니다.

1. Postman 앱에서 **새로 만들기** 를 선택하여 요청을 만듭니다. **새로 만들기** 창에서 **HTTP 요청** 을 선택합니다. 요청에 대한 **요청 이름** 을 입력합니다.

2. 작성기 탭에서 **GET** HTTP 메서드를 선택하고 다음 URL을 입력합니다. 이 요청 및 이 문서에 언급된 기타 요청에 대한 `{Azure-Maps-Primary-Subscription-key}`를 기본 구독 키로 바꿉니다.

    ```http
    https://atlas.microsoft.com/weather/forecast/minute/json?api-version=1.0&query=47.60357,-122.32945&interval=15&subscription-key={Your-Azure-Maps-Primary-Subscription-key}
    ```

3. 파란색 **보내기** 단추를 클릭합니다. 응답 본문에는 다음 120분 동안 15분 간격의 일기예보 데이터가 포함됩니다.

    ```json
    {
    "summary": {
        "briefPhrase60": "No precipitation for at least 60 min",
        "shortPhrase": "No precip for 120 min",
        "briefPhrase": "No precipitation for at least 120 min",
        "longPhrase": "No precipitation for at least 120 min",
        "iconCode": 7
    },
    "intervalSummaries": [
        {
            "startMinute": 0,
            "endMinute": 119,
            "totalMinutes": 120,
            "shortPhrase": "No precip for %MINUTE_VALUE min",
            "briefPhrase": "No precipitation for at least %MINUTE_VALUE min",
            "longPhrase": "No precipitation for at least %MINUTE_VALUE min",
            "iconCode": 7
        }
    ],
    "intervals": [
        {
            "startTime": "2020-10-19T20:51:00+00:00",
            "minute": 0,
            "dbz": 0.0,
            "shortPhrase": "No Precipitation",
            "iconCode": 7,
            "cloudCover": 100
        },
        {
            "startTime": "2020-10-19T21:06:00+00:00",
            "minute": 15,
            "dbz": 0.0,
            "shortPhrase": "No Precipitation",
            "iconCode": 7,
            "cloudCover": 100
        },
        {
            "startTime": "2020-10-19T21:21:00+00:00",
            "minute": 30,
            "dbz": 0.0,
            "shortPhrase": "No Precipitation",
            "iconCode": 7,
            "cloudCover": 100
        },
        {
            "startTime": "2020-10-19T21:36:00+00:00",
            "minute": 45,
            "dbz": 0.0,
            "shortPhrase": "No Precipitation",
            "iconCode": 7,
            "cloudCover": 100
        },
        {
            "startTime": "2020-10-19T21:51:00+00:00",
            "minute": 60,
            "dbz": 0.0,
            "shortPhrase": "No Precipitation",
            "iconCode": 7,
            "cloudCover": 100
        },
        {
            "startTime": "2020-10-19T22:06:00+00:00",
            "minute": 75,
            "dbz": 0.0,
            "shortPhrase": "No Precipitation",
            "iconCode": 7,
            "cloudCover": 100
        },
        {
            "startTime": "2020-10-19T22:21:00+00:00",
            "minute": 90,
            "dbz": 0.0,
            "shortPhrase": "No Precipitation",
            "iconCode": 7,
            "cloudCover": 100
        },
        {
            "startTime": "2020-10-19T22:36:00+00:00",
            "minute": 105,
            "dbz": 0.0,
            "shortPhrase": "No Precipitation",
            "iconCode": 7,
            "cloudCover": 100
        }
        ]
    }
    ```

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [Azure Maps Weather Services 개념](./weather-services-concepts.md)

> [!div class="nextstepaction"]
> [Azure Maps Weather Services REST API](/rest/api/maps/weather)
