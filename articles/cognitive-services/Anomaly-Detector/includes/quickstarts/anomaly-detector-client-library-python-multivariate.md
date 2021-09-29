---
title: Anomaly Detector Python 다변량 클라이언트 라이브러리 빠른 시작
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 04/29/2021
ms.author: mbullwin
ms.openlocfilehash: 0ac9f337ed24a3e440fe877998a40181853d5657
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128909095"
---
Python용 Anomaly Detector 다변량 클라이언트 라이브러리를 시작합니다. 서비스에서 제공하는 알고리즘을 사용하여 패키지 시작을 설치하려면 다음 단계를 따르세요. 새로운 다변량 변칙 검색 API를 통해 개발자는 기계 학습 기술 또는 레이블이 지정된 데이터 없이도 메트릭 그룹에서 변칙을 검색하는 고급 AI를 쉽게 통합할 수 있습니다. 서로 다른 신호 간의 종속성 및 상호 상관 관계는 자동으로 주요 요소로 계산됩니다. 이를 통해 복잡한 시스템의 오류를 사전에 보호할 수 있습니다.

Python용 Anomaly Detector 다변량 클라이언트 라이브러리를 사용하여 다음을 수행합니다.

* 시계열 그룹에서 시스템 수준 이상 징후를 감지합니다.
* 개별 시계열이 많은 것을 알 수 없는 경우 문제를 감지하기 위해 모든 신호를 확인해야 합니다.
* 시스템 상태의 다양한 양상을 측정하는 수천 개 유형의 센서를 사용하여 고가의 물리적 자산을 사전 예방적으로 유지 관리.

[라이브러리 참조 설명서](/python/api/azure-ai-anomalydetector/azure.ai.anomalydetector) | [라이브러리 소스 코드](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/anomalydetector/azure-ai-anomalydetector) | [패키지(PyPi)](https://pypi.org/project/azure-ai-anomalydetector/3.0.0b3/) | [샘플 코드](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/anomalydetector/azure-ai-anomalydetector/samples/sample_multivariate_detect.py)

## <a name="prerequisites"></a>사전 요구 사항

* [Python 3.x](https://www.python.org/)
* [Pandas 데이터 분석 라이브러리](https://pandas.pydata.org/)
* Azure 구독 - [체험 구독 만들기](https://azure.microsoft.com/free/cognitive-services)
* Azure 구독을 보유한 후에는 Azure Portal에서 <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesAnomalyDetector"  title="Anomaly Detector 리소스 만들기"  target="_blank">Anomaly Detector 리소스 </a>를 만들어 키와 엔드포인트를 가져옵니다. 배포될 때까지 기다렸다가 **리소스로 이동** 단추를 클릭합니다.
    * 애플리케이션을 Anomaly Detector API에 연결하려면 만든 리소스의 키와 엔드포인트가 필요합니다. 이 빠른 시작의 뒷부분에 나오는 코드에 키와 엔드포인트를 붙여넣습니다.
    평가판 가격 책정 계층(`F0`)을 통해 서비스를 사용해보고, 나중에 프로덕션용 유료 계층으로 업그레이드할 수 있습니다.


## <a name="setting-up"></a>설치

### <a name="install-the-client-library"></a>클라이언트 라이브러리 설치

Python을 설치한 후 다음을 사용하여 클라이언트 라이브러리를 설치할 수 있습니다.

```console
pip install pandas
pip install --upgrade azure-ai-anomalydetector
```

### <a name="create-a-new-python-application"></a>새 Python 애플리케이션 만들기

 새 Python 파일을 만들고 다음 라이브러리를 가져옵니다.

```python
import os
import time
from datetime import datetime

from azure.ai.anomalydetector import AnomalyDetectorClient
from azure.ai.anomalydetector.models import DetectionRequest, ModelInfo
from azure.core.credentials import AzureKeyCredential
from azure.core.exceptions import HttpResponseError
```

환경 변수, 시계열 데이터 파일의 경로 및 구독의 Azure 위치로 키에 대한 변수를 만듭니다. 

> [!NOTE]
> 항상 두 키 중 하나를 사용할 수 있습니다. 이는 보안 키 회전을 허용하기 위한 것입니다. 이 빠른 시작의 목적을 위해 첫 번째 키를 사용합니다. 

```python
subscription_key = "ANOMALY_DETECTOR_KEY"
anomaly_detector_endpoint = "ANOMALY_DETECTOR_ENDPOINT"
```



## <a name="code-examples"></a>코드 예제

이러한 코드 조각은 Python용 Anomaly Detector 클라이언트 라이브러리를 사용하여 다음을 수행하는 방법을 보여줍니다.

* [클라이언트 인증](#authenticate-the-client)
* [모델 학습](#train-the-model)
* [변칙 검색](#detect-anomalies)
* [모델 내보내기](#export-model)
* [모델 삭제](#delete-model)

## <a name="authenticate-the-client"></a>클라이언트 인증

새 Anomaly Detector 클라이언트를 인스턴스화하려면 Anomaly Detector 구독 키 및 연결된 엔드포인트를 전달해야 합니다. 또한 데이터 원본을 설정합니다.  

Anomaly Detector 다변량 API를 사용하려면 먼저 고유한 모델을 학습해야 합니다. 학습 데이터는 다음 요구 사항을 충족하는 여러 시계열 세트입니다.

각 시계열은 헤더 행으로 "timestamp" 및 "value"(모두 소문자)라는 2개(및 2개만)의 열이 포함된 CSV 파일이어야 합니다. "timestamp" 값은 ISO 8601을 준수해야 합니다. "value"는 소수 자릿수가 있는 정수 또는 소수일 수 있습니다. 예를 들면 다음과 같습니다.

|timestamp | 값|
|-------|-------|
|2019-04-01T00:00:00Z| 5|
|2019-04-01T00:01:00Z| 3.6|
|2019-04-01T00:02:00Z| 4|
|`...`| `...` |

각 CSV 파일의 이름을 모델 학습에 사용되는 다른 변수 뒤에 지정해야 합니다. 예를 들면 "temperature.csv" 및 "humidity.csv"입니다. 모든 CSV 파일은 하위 폴더 없이 하나의 zip 파일로 압축되어야 합니다. zip 파일에는 원하는 이름을 사용할 수 있습니다. zip 파일을 Azure Blob Storage에 업로드해야 합니다. zip 파일에 대한 Blob SAS(공유 액세스 서명) URL을 생성한 후에는 학습에 사용할 수 있습니다. Azure Blob Storage에서 SAS URL을 생성하는 방법은 이 문서를 참조하세요.

```python
class MultivariateSample():

def __init__(self, subscription_key, anomaly_detector_endpoint, data_source=None):
    self.sub_key = subscription_key
    self.end_point = anomaly_detector_endpoint

    # Create an Anomaly Detector client

    # <client>
    self.ad_client = AnomalyDetectorClient(AzureKeyCredential(self.sub_key), self.end_point)
    # </client>

    self.data_source = "YOUR_SAMPLE_ZIP_FILE_LOCATED_IN_AZURE_BLOB_STORAGE_WITH_SAS"
```

## <a name="train-the-model"></a>모델 학습

먼저 모델을 학습시키고, 학습 중에 모델의 상태를 확인하여 학습 완료 시기를 파악한 다음, 검색 단계로 이동할 때 필요한 최신 모델 ID를 검색합니다.

```python
def train(self, start_time, end_time):
    # Number of models available now
    model_list = list(self.ad_client.list_multivariate_model(skip=0, top=10000))
    print("{:d} available models before training.".format(len(model_list)))
    
    # Use sample data to train the model
    print("Training new model...(it may take a few minutes)")
    data_feed = ModelInfo(start_time=start_time, end_time=end_time, source=self.data_source)
    response_header = \
    self.ad_client.train_multivariate_model(data_feed, cls=lambda *args: [args[i] for i in range(len(args))])[-1]
    trained_model_id = response_header['Location'].split("/")[-1]
    
    # Model list after training
    new_model_list = list(self.ad_client.list_multivariate_model(skip=0, top=10000))
    
    # Wait until the model is ready. It usually takes several minutes
    model_status = None
    while model_status != ModelStatus.READY and model_status != ModelStatus.FAILED:
        model_info = self.ad_client.get_multivariate_model(trained_model_id).model_info
        model_status = model_info.status
        time.sleep(10)

    if model_status == ModelStatus.FAILED:
        print("Creating model failed.")
        print("Errors:")
        if model_info.errors:
            for error in model_info.errors:
                print("Error code: {}. Message: {}".format(error.code, error.message))
        else:
            print("None")
        return None

    if model_status == ModelStatus.READY:
        # Model list after training
        new_model_list = list(self.ad_client.list_multivariate_model(skip=0, top=10000))
        print("Done.\n--------------------")
        print("{:d} available models after training.".format(len(new_model_list)))

    # Return the latest model id
    return trained_model_id
```

## <a name="detect-anomalies"></a>변칙 검색

`detect_anomaly` 및 `get_dectection_result`를 사용하여 데이터 원본 내에 변칙이 있는지 확인합니다. 앞서 학습한 모델의 모델 ID를 전달해야 합니다.

```python
def detect(self, model_id, start_time, end_time):
    # Detect anomaly in the same data source (but a different interval)
    try:
        detection_req = DetectionRequest(source=self.data_source, start_time=start_time, end_time=end_time)
        response_header = self.ad_client.detect_anomaly(model_id, detection_req,
                                                        cls=lambda *args: [args[i] for i in range(len(args))])[-1]
        result_id = response_header['Location'].split("/")[-1]
    
        # Get results (may need a few seconds)
        r = self.ad_client.get_detection_result(result_id)
        while r.summary.status != DetectionStatus.READY and r.summary.status != DetectionStatus.FAILED:
            r = self.ad_client.get_detection_result(result_id)
            time.sleep(2)

        if r.summary.status == DetectionStatus.FAILED:
            print("Detection failed.")
            print("Errors:")
            if r.summary.errors:
                for error in r.summary.errors:
                    print("Error code: {}. Message: {}".format(error.code, error.message))
            else:
                print("None")
            return None
    except HttpResponseError as e:
        print('Error code: {}'.format(e.error.code), 'Error message: {}'.format(e.error.message))
    except Exception as e:
        raise e
    return r
```

## <a name="export-model"></a>모델 내보내기

> [!NOTE]
> 내보내기 명령은 컨테이너화된 환경에서 Anomaly Detector 다변량 모델을 실행할 수 있도록 하는 데 사용됩니다. 이는 현재 다변량에 대해 지원되지 않지만 나중에 지원이 추가될 예정입니다.

모델을 내보내려면 `export_model`을 사용하고 내보내려는 모델의 모델 ID를 전달합니다.

```python
def export_model(self, model_id, model_path="model.zip"):

    # Export the model
    model_stream_generator = self.ad_client.export_model(model_id)
    with open(model_path, "wb") as f_obj:
        while True:
            try:
                f_obj.write(next(model_stream_generator))
            except StopIteration:
                break
            except Exception as e:
                raise e
```

## <a name="delete-model"></a>모델 삭제

모델을 삭제하려면 `delete_multivariate_model`을 사용하고 삭제하려는 모델의 모델 ID를 전달합니다.

```python
def delete_model(self, model_id):

    # Delete the mdoel
    self.ad_client.delete_multivariate_model(model_id)
    model_list_after_delete = list(self.ad_client.list_multivariate_model(skip=0, top=10000))
    print("{:d} available models after deletion.".format(len(model_list_after_delete)))
```

## <a name="run-the-application"></a>애플리케이션 실행

애플리케이션을 실행하기 전에 새로 만든 함수를 호출하는 코드를 추가해야 합니다.

```python
if __name__ == '__main__':
    subscription_key = "ANOMALY_DETECTOR_KEY"
    anomaly_detector_endpoint = "ANOMALY_DETECTOR_ENDPOINT"

    # Create a new sample and client
    sample = MultivariateSample(subscription_key, anomaly_detector_endpoint, data_source=None)

    # Train a new model
    model_id = sample.train(datetime(2021, 1, 1, 0, 0, 0), datetime(2021, 1, 2, 12, 0, 0))

    # Reference
    result = sample.detect(model_id, datetime(2021, 1, 2, 12, 0, 0), datetime(2021, 1, 3, 0, 0, 0))
    print("Result ID:\t", result.result_id)
    print("Result summary:\t", result.summary)
    print("Result length:\t", len(result.results))

    # Export model
    sample.export_model(model_id, "model.zip")

    # Delete model
    sample.delete_model(model_id)

```

실행하기 전에 이 빠른 시작이 파생된 [전체 샘플 코드](https://github.com/Azure-Samples/AnomalyDetector/blob/master/ipython-notebook/API%20Sample/Multivariate%20API%20Demo%20Notebook.ipynb)에서 프로젝트를 확인하는 것이 도움이 될 수 있습니다.

또한 시작하는 데 도움이 되는 [자세한 Jupyter Notebook](https://github.com/Azure-Samples/AnomalyDetector/blob/master/ipython-notebook/API%20Sample/Multivariate%20API%20Demo%20Notebook.ipynb)도 있습니다.

`python` 명령 및 파일 이름을 사용하여 애플리케이션을 실행합니다.


## <a name="clean-up-resources"></a>리소스 정리

Cognitive Services 구독을 정리하고 제거하려면 리소스나 리소스 그룹을 삭제하면 됩니다. 리소스 그룹을 삭제하면 해당 리소스 그룹에 연결된 다른 모든 리소스가 함께 삭제됩니다.

* [포털](../../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>다음 단계

* [Anomaly Detector API란?](../../overview-multivariate.md)
* [Anomaly Detector API를 사용하는 경우의 모범 사례입니다.](../../concepts/best-practices-multivariate.md)
