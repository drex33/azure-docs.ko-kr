---
title: Anomaly Detector Python 클라이언트 라이브러리 빠른 시작
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 11/25/2020
ms.author: mbullwin
ms.openlocfilehash: 84510afb4a6bac20f7b0496bf99a3e2a5d2571f9
ms.sourcegitcommit: 4cd97e7c960f34cb3f248a0f384956174cdaf19f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/08/2021
ms.locfileid: "132040369"
---
Python용 Anomaly Detector 클라이언트 라이브러리를 시작합니다. 서비스에서 제공하는 알고리즘을 사용하여 패키지 시작을 설치하려면 다음 단계를 따르세요. Anomaly Detector 서비스를 사용하면 업계, 시나리오 또는 데이터 양에 관계없이 가장 적합한 모델을 자동으로 사용하여 시계열 데이터의 변칙을 찾을 수 있습니다.

Python용 Anomaly Detector 클라이언트 라이브러리를 사용하여 다음을 수행할 수 있습니다.

* 일괄 요청으로 시계열 데이터 세트 전체에서 변칙 검색
* 시계열에서 최신 데이터 요소의 변칙 상태 검색
* 데이터 세트에서 추세 변화 포인트 검색

[라이브러리 참조 설명서](https://go.microsoft.com/fwlink/?linkid=2090370) | [라이브러리 소스 코드](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cognitiveservices/azure-cognitiveservices-anomalydetector) | [패키지(PyPi)](https://pypi.org/project/azure-ai-anomalydetector/) | [GitHub에서 샘플 코드 찾기](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/anomalydetector/azure-ai-anomalydetector/samples)

## <a name="prerequisites"></a>사전 요구 사항

* [Python 3.x](https://www.python.org/)
* [Pandas 데이터 분석 라이브러리](https://pandas.pydata.org/)
* Azure 구독 - [체험 구독 만들기](https://azure.microsoft.com/free/cognitive-services)
* Azure 구독을 보유한 후에는 Azure Portal에서 <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesAnomalyDetector"  title="Anomaly Detector 리소스 만들기"  target="_blank">Anomaly Detector 리소스 </a>를 만들어 키와 엔드포인트를 가져옵니다. 배포될 때까지 기다렸다가 **리소스로 이동** 단추를 클릭합니다.
    * 애플리케이션을 Anomaly Detector API에 연결하려면 만든 리소스의 키와 엔드포인트가 필요합니다. 이 빠른 시작의 뒷부분에 나오는 코드에 키와 엔드포인트를 붙여넣습니다.
    평가판 가격 책정 계층(`F0`)을 통해 서비스를 사용해보고, 나중에 프로덕션용 유료 계층으로 업그레이드할 수 있습니다.


## <a name="setting-up"></a>설치

[!INCLUDE [anomaly-detector-environment-variables](../environment-variables.md)]

### <a name="create-a-new-python-application"></a>새 Python 애플리케이션 만들기

 새 Python 파일을 만들고 다음 라이브러리를 가져옵니다.

```python
import os
from azure.ai.anomalydetector import AnomalyDetectorClient
from azure.ai.anomalydetector.models import DetectRequest, TimeSeriesPoint, TimeGranularity, \
    AnomalyDetectorError
from azure.core.credentials import AzureKeyCredential
import pandas as pd
```

환경 변수, 시계열 데이터 파일의 경로 및 구독의 Azure 위치로 키에 대한 변수를 만듭니다. 예들 들어 `westus2`입니다.

```python
SUBSCRIPTION_KEY = os.environ["ANOMALY_DETECTOR_KEY"]
ANOMALY_DETECTOR_ENDPOINT = os.environ["ANOMALY_DETECTOR_ENDPOINT"]
TIME_SERIES_DATA_PATH = os.path.join("./sample_data", "request-data.csv")
```

### <a name="install-the-client-library"></a>클라이언트 라이브러리 설치

Python을 설치한 후, 다음을 사용하여 클라이언트 라이브러리를 설치할 수 있습니다.

```console
pip install --upgrade azure-ai-anomalydetector
```

## <a name="object-model"></a>개체 모델

Anomaly Detector 클라이언트는 키를 사용하여 Azure에 인증하는 [AnomalyDetectorClient](https://github.com/Azure/azure-sdk-for-python/blob/0b8622dc249969c2f01c5d7146bd0bb36bb106dd/sdk/cognitiveservices/azure-cognitiveservices-anomalydetector/azure/cognitiveservices/anomalydetector/_anomaly_detector_client.py) 개체입니다. 클라이언트는 전체 데이터 세트에 대해 [detect_entire_series](https://github.com/Azure/azure-sdk-for-python/blob/bf9d44f2a50aea46a59c4cb83ccfccaff5e2b218/sdk/anomalydetector/azure-ai-anomalydetector/azure/ai/anomalydetector/operations/_anomaly_detector_client_operations.py#L26)를 사용하거나 최신 데이터 포인트에 대해 [detect_last_point](https://github.com/Azure/azure-sdk-for-python/blob/bf9d44f2a50aea46a59c4cb83ccfccaff5e2b218/sdk/anomalydetector/azure-ai-anomalydetector/azure/ai/anomalydetector/operations/_anomaly_detector_client_operations.py#L87)를 사용하여 변칙 검색을 수행할 수 있습니다. [detect_change_point](https://github.com/Azure/azure-sdk-for-python/blob/bf9d44f2a50aea46a59c4cb83ccfccaff5e2b218/sdk/anomalydetector/azure-ai-anomalydetector/azure/ai/anomalydetector/aio/operations_async/_anomaly_detector_client_operations_async.py#L142) 함수는 추세의 변화를 표시하는 포인트를 검색합니다.

시계열 데이터는 일련의 [TimeSeriesPoints](https://github.com/Azure/azure-sdk-for-python/blob/bf9d44f2a50aea46a59c4cb83ccfccaff5e2b218/sdk/anomalydetector/azure-ai-anomalydetector/azure/ai/anomalydetector/models/_models_py3.py#L370)로 전송됩니다. `DetectRequest` 개체에는 데이터를 설명하는 속성(예: `TimeGranularity`) 및 변칙 검색용 매개 변수가 포함됩니다.

Anomaly Detector 응답은 사용된 메서드에 따라 [LastDetectResponse](/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.models.lastdetectresponse), [EntireDetectResponse](/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.models.entiredetectresponse) 또는 [ChangePointDetectResponse](https://github.com/Azure/azure-sdk-for-python/blob/bf9d44f2a50aea46a59c4cb83ccfccaff5e2b218/sdk/anomalydetector/azure-ai-anomalydetector/azure/ai/anomalydetector/models/_models_py3.py#L107) 개체입니다.

## <a name="code-examples"></a>코드 예제

이러한 코드 조각은 Python용 Anomaly Detector 클라이언트 라이브러리를 사용하여 다음을 수행하는 방법을 보여줍니다.

* [클라이언트 인증](#authenticate-the-client)
* [파일에서 시계열 데이터 세트 로드](#load-time-series-data-from-a-file)
* [전체 데이터 세트에서 변칙 검색](#detect-anomalies-in-the-entire-data-set)
* [최신 데이터 요소의 변칙 상태 검색](#detect-the-anomaly-status-of-the-latest-data-point)
* [데이터 세트에서 변화 포인트 검색](#detect-change-points-in-the-data-set)

## <a name="authenticate-the-client"></a>클라이언트 인증

Azure 위치 변수를 엔드포인트에 추가하고 키를 사용하여 클라이언트를 인증합니다.

```python
client = AnomalyDetectorClient(AzureKeyCredential(SUBSCRIPTION_KEY), ANOMALY_DETECTOR_ENDPOINT)
```

## <a name="load-time-series-data-from-a-file"></a>파일에서 시계열 데이터 로드

[GitHub](https://github.com/Azure-Samples/AnomalyDetector/blob/master/example-data/request-data.csv)에서 이 빠른 시작의 예제 데이터를 다운로드합니다.
1. 브라우저에서 **Raw** 를 마우스 오른쪽 단추로 클릭합니다.
2. **다른 이름으로 링크 저장** 을 클릭합니다.
3. 해당 파일을 .csv 파일로 애플리케이션 디렉터리에 저장합니다.

이 시계열 데이터는 .csv 파일로 형식이 지정되며 Anomaly Detector API로 전송됩니다.

Pandas 라이브러리의 `read_csv()` 메서드를 사용하여 데이터 파일을 로드하고 데이터 시리즈를 저장할 빈 목록 변수를 만듭니다. 파일 전체에서 반복하고 데이터를 `TimeSeriesPoint` 개체로 추가합니다. 이 개체에는 .csv 데이터 파일의 행에 있는 타임스탬프와 숫자 값이 포함됩니다.

```python
series = []
data_file = pd.read_csv(TIME_SERIES_DATA_PATH, header=None, encoding='utf-8', parse_dates=[0])
for index, row in data_file.iterrows():
    series.append(TimeSeriesPoint(timestamp=row[0], value=row[1]))
```

시계열과 해당 데이터 포인트의 세분성(또는 `TimeGranularity`)을 사용하여 `DetectRequest` 개체를 만듭니다. 예들 들어 `TimeGranularity.daily`입니다.

```python
request = DetectRequest(series=series, granularity=TimeGranularity.daily)
```

입력 인수 설명: 'series': 요청에 필요합니다. 배열/목록 형식이어야 하며, 12포인트를 초과하지만 8,640포인트 미만이어야 합니다. 타임스탬프를 기준으로 오름차순으로 정렬해야 하며 중복된 타임스탬프를 사용할 수 없습니다. 'granularity': 요청에 필요합니다. ['daily', 'minutely', 'hourly', 'weekly', 'monthly', 'yearly', 'secondly'] 중 하나일 수 있습니다.
'customInterval': 0보다 큰 정수여야 합니다.
'period': 0 이상의 정수여야 합니다.
'maxAnomalyRatio': 시리즈 포인트의 50% 미만이어야 합니다(0 < maxAnomalyRatio < 0.5).
'sensitivity': 0에서 99 사이의 정수여야 합니다.

## <a name="detect-anomalies-in-the-entire-data-set"></a>전체 데이터 세트에서 변칙 검색

API를 호출하여 클라이언트의 `detect_entire_series` 메서드를 사용하여 전체 시계열 데이터에서 변칙을 검색합니다. 반환된 [EntireDetectResponse](/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.models.entiredetectresponse) 개체를 저장합니다. 응답의 `is_anomaly` 목록을 반복하고 `true` 값의 인덱스를 출력합니다. 이러한 값은 변칙 데이터 요소의 인덱스와 일치합니다(발견된 경우).

```python
print('Detecting anomalies in the entire time series.')

try:
    response = client.detect_entire_series(request)
except AnomalyDetectorError as e:
    print('Error code: {}'.format(e.error.code), 'Error message: {}'.format(e.error.message))
except Exception as e:
    print(e)

if any(response.is_anomaly):
    print('An anomaly was detected at index:')
    for i, value in enumerate(response.is_anomaly):
        if value:
            print(i)
else:
    print('No anomalies were detected in the time series.')
```

## <a name="detect-the-anomaly-status-of-the-latest-data-point"></a>최신 데이터 요소의 변칙 상태 검색

Anomaly Detector API를 호출하여 클라이언트의 `detect_last_point` 메서드를 통해 최신 데이터 요소가 변칙인지 확인하고 반환된 `LastDetectResponse` 개체를 저장합니다. 응답의 `is_anomaly` 값은 해당 포인트의 변칙 상태를 지정하는 부울 값입니다.  

```python
print('Detecting the anomaly status of the latest data point.')

try:
    response = client.detect_last_point(request)
except AnomalyDetectorError as e:
    print('Error code: {}'.format(e.error.code), 'Error message: {}'.format(e.error.message))
except Exception as e:
    print(e)

if response.is_anomaly:
    print('The latest point is detected as anomaly.')
else:
    print('The latest point is not detected as anomaly.')
```

## <a name="detect-change-points-in-the-data-set"></a>데이터 세트에서 변화 포인트 검색

API를 호출하여 클라이언트의 `detect_change_point` 메서드로 시계열 데이터의 변화 포인트를 검색합니다. 반환된 `ChangePointDetectResponse` 개체를 저장합니다. 응답의 `is_change_point` 목록을 반복하고 `true` 값의 인덱스를 출력합니다. 이러한 값은 추세 변화 포인트의 인덱스에 해당합니다(발견된 경우).

```python
print('Detecting change points in the entire time series.')

try:
    response = client.detect_change_point(request)
except AnomalyDetectorError as e:
    print('Error code: {}'.format(e.error.code), 'Error message: {}'.format(e.error.message))
except Exception as e:
    print(e)

if any(response.is_change_point):
    print('An change point was detected at index:')
    for i, value in enumerate(response.is_change_point):
        if value:
            print(i)
else:
    print('No change point were detected in the time series.')
```

## <a name="run-the-application"></a>애플리케이션 실행

`python` 명령 및 파일 이름을 사용하여 애플리케이션을 실행합니다.

[!INCLUDE [anomaly-detector-next-steps](../quickstart-cleanup-next-steps.md)]
