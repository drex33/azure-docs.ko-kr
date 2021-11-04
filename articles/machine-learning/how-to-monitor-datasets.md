---
title: 데이터 세트에서 데이터 드리프트 검색(미리 보기)
titleSuffix: Azure Machine Learning
description: Azure Learning에서 데이터 드리프트 검색을 설정하는 방법을 알아봅니다. 데이터 세트 모니터(미리 보기)를 만들고, 데이터 드리프트를 모니터링하고, 경고를 설정합니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: mldata
ms.reviewer: sgilley
ms.author: wibuchan
author: buchananwp
ms.date: 10/21/2021
ms.topic: how-to
ms.custom: data4ml, contperf-fy21q2
ms.openlocfilehash: 05d7136b48f206cc9d091f5f73ee763cc24135fa
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/04/2021
ms.locfileid: "131558823"
---
# <a name="detect-data-drift-preview-on-datasets"></a>데이터 세트에서 데이터 드리프트(미리 보기) 검색

데이터 드리프트를 모니터링하고 드리프트가 높을 때 경고를 설정하는 방법을 알아봅니다.  

Azure Machine Learning 데이터 세트 모니터(미리 보기)를 사용하여 다음을 수행할 수 있습니다.
* **데이터의 드리프트를 분석** 하여 시간별로 어떻게 변화하는지 파악합니다.
* **모델 데이터를 모니터링** 하여 학습 데이터 세트와 서비스 제공 데이터 세트의 차이를 파악합니다.  먼저 [배포된 모델에서 모델 데이터를 수집](how-to-enable-data-collection.md)합니다.
* **새로운 데이터를 모니터링** 하여 기준선과 대상 데이터 세트 간의 차이를 파악합니다.
* **데이터의 특징을 프로파일링** 하여 시간이 지나면서 통계 속성이 어떻게 변하는지 추적합니다.
* 잠재적 문제에 대한 조기 경고를 위해 **데이터 드리프트에 대한 경고를 설정** 합니다. 
* 데이터가 너무 많이 드리프트되었다고 확인되는 경우 **[새 데이터 세트 버전을 만듭니다](how-to-version-track-datasets.md)** .

[Azure Machine Learning 데이터 세트](how-to-create-register-datasets.md)는 모니터를 만드는 데 사용됩니다. 데이터 세트에는 타임스탬프 열이 포함되어야 합니다.

데이터 드리프트 메트릭은 Python SDK 또는 Azure Machine Learning 스튜디오에서 볼 수 있습니다.  다른 메트릭 및 인사이트는 Azure Machine Learning 작업 영역과 연결된 [Azure Application Insights](../azure-monitor/app/app-insights-overview.md) 리소스를 통해 제공됩니다.

> [!IMPORTANT]
> 데이터 세트에 대한 데이터 드리프트 검색은 현재 공개 미리 보기로 제공됩니다.
> 미리 보기 버전은 서비스 수준 계약 없이 제공되며 프로덕션 워크로드에는 권장되지 않습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다. 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

## <a name="prerequisites"></a>사전 요구 사항

데이터 세트 모니터를 만들고 사용하려면 다음이 필요합니다.
* Azure 구독 Azure 구독이 없는 경우 시작하기 전에 체험 계정을 만듭니다. 지금 [Azure Machine Learning 평가판 또는 유료 버전](https://azure.microsoft.com/free/)을 사용해 보세요.
* [Azure Machine Learning 작업 영역](how-to-manage-workspace.md)
* azureml-datasets 패키지가 포함된 [Python용 Azure Machine Learning SDK가 설치](/python/api/overview/azure/ml/install)되어 있습니다.
* 데이터의 열, 파일 경로 또는 파일 이름에 타임스탬프가 지정되어 있는 구조화된(테이블 형식) 데이터

## <a name="what-is-data-drift"></a>데이터 드리프트란?

데이터 드리프트는 시간이 지나면서 모델 정확도가 저하되는 가장 큰 이유 중 하나입니다.  기계 학습 모델의 경우 데이터 드리프트는 모델의 성능 저하를 초래하는 모델 입력 데이터의 변화입니다.  데이터 드리프트를 모니터링하면 이러한 모델 성능 문제를 감지하는 데 도움이 됩니다.

데이터 드리프트의 원인은 다음과 같습니다.

- 업스트림 프로세스 변경(예: 센서가 교체되어 측정 단위가 인치에서 센티미터로 변경되는 경우) 
- 데이터 품질 문제(예: 센서가 고장 나서 항상 0을 읽는 경우)
- 자연 발생적인 데이터 드리프트(예: 계절에 따라 변화하는 평균 기온)
- 특징 간의 관계 변화 또는 공변량 변화 

Azure Machine Learning은 비교되는 데이터 세트의 복잡성을 추상화하는 단일 메트릭을 컴퓨팅하여 드리프트 검색을 간소화합니다.  이러한 데이터 세트에는 수백 개의 특징과 수만 개의 행이 있을 수 있습니다. 드리프트가 검색되면 어떤 특징이 드리프트를 유발하는지 드릴다운합니다.  그런 다음, 특징 수준 메트릭을 검사하여 드리프트의 근본 원인을 디버그하고 격리합니다.

이러한 하향식 접근 방식을 사용하면 기존의 규칙 기반 기술 대신 데이터를 쉽게 모니터링할 수 있습니다. 규칙 기반 기술(예 : 허용된 데이터 범위 또는 허용된 고유 값)은 시간이 많이 걸리고 오류가 발생하기 쉽습니다.

Azure Machine Learning에서는 데이터 세트 모니터를 사용하여 데이터 드리프트를 검색하고 경고합니다.
  
### <a name="dataset-monitors"></a>데이터 세트 모니터 

데이터 세트 모니터를 사용하여 다음을 수행할 수 있습니다.

* 데이터 세트의 새 데이터에 대한 데이터 드리프트를 검색하고 경고합니다.
* 드리프트에 대한 과거 데이터를 분석합니다.
* 시간별 새 데이터를 프로파일링합니다.

데이터 드리프트 알고리즘은 데이터의 전반적인 변화를 측정하고 추가 조사를 유발하는 특징을 알려줍니다. 데이터 세트 모니터는 `timeseries` 데이터 세트에서 새 데이터를 프로파일링하여 여러 가지 다른 메트릭을 생성합니다. 

사용자 지정 경고는 [Azure Application Insights](../azure-monitor/app/app-insights-overview.md)를 통해 모니터에서 생성된 모든 메트릭에 대해 설정할 수 있습니다. 데이터 세트 모니터를 사용하면 데이터 문제를 빠르게 파악하고 가능한 원인을 식별하여 문제를 디버그하는 시간을 줄일 수 있습니다.  

개념적으로 Azure Machine Learning에서 데이터 세트 모니터를 설정하는 세 가지 기본 시나리오가 있습니다.

시나리오 | Description
---|---
모델의 제공 데이터가 학습 데이터에서 벗어나는지 모니터링 | 이 시나리오의 결과는 모델의 정확도에 대한 프록시 모니터링으로 해석될 수 있습니다. 제공 데이터가 학습 데이터에서 벗어나면 모델 정확도가 저하되기 때문입니다.
시계열 데이터 세트가 이전 기간에서 벗어나는지 모니터링 | 이 시나리오는 보다 일반적이며 모델 빌드 업스트림 또는 다운스트림과 관련된 데이터 세트를 모니터링하는 데 사용할 수 있습니다.  대상 데이터 세트에는 타임스탬프 열이 있어야 합니다. 기준 데이터 세트는 대상 데이터 세트와 공통된 특징이 있는 테이블 형식의 데이터 세트일 수 있습니다.
과거 데이터에 대한 분석 수행 | 이 시나리오는 기록 데이터를 이해하고 데이터 세트 모니터 설정에서 결정을 내리는 데 사용할 수 있습니다.

데이터 세트 모니터는 다음 Azure 서비스에 따라 달라집니다.

|Azure 서비스  |Description  |
|---------|---------|
| *데이터 세트* | 드리프트는 Machine Learning 데이터 세트를 사용하여 학습 데이터를 검색하고 모델 학습을 위해 데이터를 비교합니다.  데이터의 프로필 생성은 보고된 메트릭(예: 최소, 최대, 고유 값, 고유 값 수 등) 중 일부를 생성하는 데 사용됩니다. |
| Azureml 파이프라인 및 컴퓨팅 | 드리프트 계산 작업은 azureml 파이프라인에서 호스트됩니다.  이 작업은 요청 시 또는 일정에 따라 트리거되어 드리프트 모니터 생성 시간에 구성된 컴퓨팅에서 실행됩니다.
| *애플리케이션 인사이트*| 드리프트는 기계 학습 작업 영역에 속하는 Application Insights에 메트릭을 내보냅니다.
| *Azure Blob 스토리지*| 드리프트는 Azure Blob Storage에 json 형식의 메트릭을 내보냅니다.

### <a name="baseline-and-target-datasets"></a>기준 및 대상 데이터 세트 

[Azure Machine Learning 데이터 세트](how-to-create-register-datasets.md)에서 데이터 드리프트를 모니터링합니다. 데이터 세트 모니터를 만들 때는 다음을 참조합니다.
* 기준 데이터 세트 - 일반적으로 모델용 학습 데이터 세트입니다.
* 대상 데이터 세트(일반적으로 모델 입력 데이터) - 시간별로 기준 데이터 세트와 비교됩니다. 이렇게 비교하려면 대상 데이터 세트에 타임스탬프 열이 지정되어 있어야 합니다.

모니터는 기준과 대상 데이터 세트를 비교합니다.

## <a name="create-target-dataset"></a>대상 데이터 세트 만들기

대상 데이터 세트에는 데이터의 열 또는 파일의 경로 패턴에서 파생된 가상 열에서 타임스탬프 열을 지정하여 `timeseries` 특성 집합이 필요합니다. 타임스탬프가 있는 데이터 세트는 [Python SDK](#sdk-dataset) 또는 [Azure Machine Learning 스튜디오](#studio-dataset)를 통해 만듭니다. 데이터 세트에 `timeseries` 특성을 추가하려면 타임스탬프를 나타내는 열이 지정되어 있어야 합니다. 데이터가 '{yyyy/MM/dd}'와 같은 시간 정보를 사용하여 폴더 구조로 분할된 경우, 경로 패턴 설정을 통해 가상 열을 만든 다음 '파티션 타임스탬프'로 설정하여 시계열 API 기능을 사용하도록 설정합니다.

# <a name="python"></a>[Python](#tab/python)
<a name="sdk-dataset"></a>

[`Dataset`](/python/api/azureml-core/azureml.data.tabulardataset#with-timestamp-columns-timestamp-none--partition-timestamp-none--validate-false----kwargs-) 클래스 [`with_timestamp_columns()`](/python/api/azureml-core/azureml.data.tabulardataset#with-timestamp-columns-timestamp-none--partition-timestamp-none--validate-false----kwargs-) 메서드는 데이터 세트의 타임스탬프 열을 정의합니다.

```python 
from azureml.core import Workspace, Dataset, Datastore

# get workspace object
ws = Workspace.from_config()

# get datastore object 
dstore = Datastore.get(ws, 'your datastore name')

# specify datastore paths
dstore_paths = [(dstore, 'weather/*/*/*/*/data.parquet')]

# specify partition format
partition_format = 'weather/{state}/{date:yyyy/MM/dd}/data.parquet'

# create the Tabular dataset with 'state' and 'date' as virtual columns 
dset = Dataset.Tabular.from_parquet_files(path=dstore_paths, partition_format=partition_format)

# assign the timestamp attribute to a real or virtual column in the dataset
dset = dset.with_timestamp_columns('date')

# register the dataset as the target dataset
dset = dset.register(ws, 'target')
```

> [!TIP]
> 데이터 세트의 `timeseries` 특성을 사용하는 전체 예제는 [예제 노트북](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/work-with-data/datasets-tutorial/timeseries-datasets/tabular-timeseries-dataset-filtering.ipynb) 또는 [데이터 세트 SDK 설명서](/python/api/azureml-core/azureml.data.tabulardataset#with-timestamp-columns-timestamp-none--partition-timestamp-none--validate-false----kwargs-)를 참조하세요.

# <a name="studio"></a>[스튜디오](#tab/azure-studio)

<a name="studio-dataset"></a>

Azure Machine Learning 스튜디오를 사용하여 데이터 세트를 만드는 경우에는 데이터 경로에 타임스탬프 정보가 포함되어 있는지 확인하고 데이터가 있는 모든 하위 폴더를 포함하고 파티션 형식을 설정해야 합니다.

다음 예에서는 하위 폴더인 *NoaaIsdFlorida/2019* 의 모든 데이터를 가져오고 파티션 형식은 타임스탬프의 연도, 월, 일을 지정합니다.

[![파티션 형식](./media/how-to-monitor-datasets/partition-format.png)](media/how-to-monitor-datasets/partition-format-expand.png)

**스키마** 설정에서 지정된 데이터 세트에 있는 가상 또는 실제 열에서 **타임스탬프** 열을 지정합니다. 이 형식은 데이터에 시간 구성 요소가 있음을 나타냅니다. 

:::image type="content" source="media/how-to-monitor-datasets/timestamp.png" alt-text="타임스탬프 설정":::

아래의 경우처럼 데이터가 날짜 또는 시간별로 이미 분할된 경우 **파티션 타임스탬프** 를 지정해도 됩니다. 이렇게 하면 날짜 처리 작업의 효율성을 높일 수 있고 학습 중에 활용 가능한 시계열 API를 사용할 수 있습니다.

:::image type="content" source="media/how-to-monitor-datasets/timeseries-partitiontimestamp.png" alt-text="파티션 타임스탬프":::

---

## <a name="create-dataset-monitor"></a>데이터 세트 모니터 만들기

새 데이터 세트에서 데이터 드리프트를 검색하고 경고하는 데이터 세트 모니터를 만듭니다.  [Python SDK](#sdk-monitor) 또는 [Azure Machine Learning 스튜디오](#studio-monitor)중 하나를 사용합니다.

# <a name="python"></a>[Python](#tab/python)
<a name="sdk-monitor"></a> 전체 세부 정보는 [데이터 드리프트에 대한 Python SDK 참조 문서](/python/api/azureml-datadrift/azureml.datadrift)를 확인하세요. 

다음 예제는 Python SDK를 사용하여 데이터 세트 모니터를 만드는 방법을 보여줍니다.

```python
from azureml.core import Workspace, Dataset
from azureml.datadrift import DataDriftDetector
from datetime import datetime

# get the workspace object
ws = Workspace.from_config()

# get the target dataset
target = Dataset.get_by_name(ws, 'target')

# set the baseline dataset
baseline = target.time_before(datetime(2019, 2, 1))

# set up feature list
features = ['latitude', 'longitude', 'elevation', 'windAngle', 'windSpeed', 'temperature', 'snowDepth', 'stationName', 'countryOrRegion']

# set up data drift detector
monitor = DataDriftDetector.create_from_datasets(ws, 'drift-monitor', baseline, target, 
                                                      compute_target='cpu-cluster', 
                                                      frequency='Week', 
                                                      feature_list=None, 
                                                      drift_threshold=.6, 
                                                      latency=24)

# get data drift detector by name
monitor = DataDriftDetector.get_by_name(ws, 'drift-monitor')

# update data drift detector
monitor = monitor.update(feature_list=features)

# run a backfill for January through May
backfill1 = monitor.backfill(datetime(2019, 1, 1), datetime(2019, 5, 1))

# run a backfill for May through today
backfill1 = monitor.backfill(datetime(2019, 5, 1), datetime.today())

# disable the pipeline schedule for the data drift detector
monitor = monitor.disable_schedule()

# enable the pipeline schedule for the data drift detector
monitor = monitor.enable_schedule()
```

> [!TIP]
> `timeseries` 데이터 세트 및 데이터 드리프트 감지기를 설정하는 전체 예제는 [예제 노트북](https://aka.ms/datadrift-notebook)을 참조하세요.


# <a name="studio"></a>[스튜디오](#tab/azure-studio)
<a name="studio-monitor"></a>

1. [스튜디오의 홈페이지](https://ml.azure.com)로 이동합니다.
1. 왼쪽의 **데이터 세트** 탭을 선택합니다. 
1. **데이터 세트 모니터** 를 선택합니다.
   ![모니터 목록](./media/how-to-monitor-datasets/monitor-list.png)

1. **+ 모니터 만들기** 단추를 클릭하고 **다음** 을 클릭하여 마법사를 계속 진행합니다.  

:::image type="content" source="media/how-to-monitor-datasets/wizard.png" alt-text="모니터 만들기 마법사":::

* **대상 데이터 세트 선택**.  대상 데이터 세트는 타임스탬프 열(데이터 드리프트에 대해 분석될)이 지정되어 있는 테이블 형식 데이터 세트입니다. 대상 데이터 세트에는 기준 데이터 세트와 공통된 특징이 있어야 하며, 새 데이터가 추가되는 `timeseries` 데이터 세트여야 합니다. 대상 데이터 세트의 기록 데이터를 분석하거나 새 데이터를 모니터링할 수 있습니다.

* **기준 데이터 세트 선택**.  시간별로 대상 데이터 세트를 비교하기 위한 기준으로 사용할 테이블 형식 데이터 세트를 선택합니다.  기준 데이터 세트에는 대상 데이터 세트와 공통된 특징이 있어야 합니다.  대상 데이터 세트의 조각을 사용할 시간 범위를 선택하거나 기준으로 사용할 별도의 데이터 세트를 지정합니다.

* **모니터 설정**.  다음 설정은 앞으로 생성될 예약된 데이터 세트 모니터 파이프라인에 대한 것입니다. 

    | 설정 | Description | 팁 | 변경 가능 | 
    | ------- | ----------- | ---- | ------- |
    | Name | 데이터 세트 모니터의 이름입니다. | | No |
    | 기능 | 시간별로 데이터 드리프트에 대해 분석될 특징 목록입니다. | 개념 드리프트를 측정하는 모델의 출력 특징으로 설정합니다. 시간이 지나면 자연적으로 드리프트되는 특징(월, 연도, 인덱스 등)은 포함하지 마십시오. 특징 목록을 조정한 후에는 기존 데이터 드리프트 모니터를 다시 채울 수 있습니다. | Yes | 
    | 컴퓨팅 대상 | 데이터 세트 모니터 작업을 실행하기 위한 Azure Machine Learning 컴퓨팅 대상 | | Yes | 
    | 사용 | 데이터 세트 모니터 파이프라인에서 일정을 사용하거나 사용하지 않도록 설정 | 백필 설정으로 기록 데이터를 분석하는 일정을 사용하지 않도록 설정합니다. 데이터 세트 모니터가 생성된 후 사용하도록 설정할 수 있습니다. | Yes | 
    | 빈도 | 백필을 실행하는 경우 기록 데이터를 분석하고 파이프라인 작업을 예약하는 데 사용할 빈도입니다. 옵션에는 매일, 매주 또는 매월이 포함됩니다. | 각 실행은 빈도에 따라 대상 데이터 세트의 데이터를 비교합니다. <li>매일: 대상 데이터 세트의 가장 최근 하루 전체(하루 종일)를 기준과 비교 <li>매주: 대상 데이터 세트의 가장 최근 한 주 전체(월요일~일요일)를 기준과 비교 <li>매월: 대상 데이터 세트의 가장 최근 한 달 전체를 기준과 비교 | No | 
    | 대기 시간 | 데이터가 데이터 세트에 도착하는 데 걸리는 시간(시간 단위)입니다. 예를 들어 데이터 세트가 캡슐화하는 SQL DB에 데이터가 도착하는 데 3일이 걸리면 대기 시간을 72로 설정합니다. | 데이터 세트 모니터를 만든 후에는 변경할 수 없습니다. | No | 
    | 이메일 주소 | 데이터 드리프트 백분율 임계값 위반을 기준으로 경고할 이메일 주소입니다. | 이메일은 Azure Monitor를 통해 전송됩니다. | Yes | 
    | 임계값 | 이메일 경고에 대한 데이터 드리프트 백분율 임계값입니다. | 작업 영역의 연결된 Application Insights 리소스에 있는 다른 많은 메트릭에 대해 추가 경고 및 이벤트를 설정할 수 있습니다. | Yes |

마법사를 마치면 결과 데이터 세트 모니터가 목록에 나타납니다. 선택하면 해당 모니터의 세부 정보 페이지로 이동됩니다.

---

## <a name="understand-data-drift-results"></a>데이터 드리프트 결과 이해

이 섹션에서는 Azure Studio의 **데이터 세트** / **데이터 세트 모니터** 페이지에 있는 데이터 세트 모니터링 결과를 보여줍니다.  설정을 업데이트할 수 있고 이 페이지에서 특정 기간에 대한 기존 데이터를 분석할 수 있습니다.  

데이터 드리프트의 규모에 대한 최상위 인사이트 및 추가로 조사할 특징에 대한 하이라이트부터 살펴봅니다.

:::image type="content" source="media/how-to-monitor-datasets/drift-overview.png" alt-text="드리프트 개요":::


| 메트릭 | Description | 
| ------ | ----------- | 
| 데이터 드리프트 규모 | 시간 경과에 따른 기준 데이터 세트와 대상 데이터 세트 간의 드리프트 비율입니다. 0에서 100까지 범위에서, 0은 동일한 데이터 세트를 나타내고 100은 Azure Machine Learning 데이터 드리프트 모델이 두 데이터 세트를 완전히 다르게 구분할 수 있음을 나타냅니다. 이 규모를 생성하는 데 기계 학습 기술이 사용되기 때문에 측정된 정확한 백분율에 노이즈가 예상됩니다. | 
| 상위 드리프팅 특징 | 가장 많이 드리프트되어 드리프트 규모 메트릭에 가장 많이 기여한 데이터 세트의 특징을 보여줍니다. 공변량 변화로 인해 특징의 중요성이 상대적으로 높아지도록 특징의 기본 분포를 반드시 변경해야 하는 것은 아닙니다. |
| 임계값 | 설정된 임계값을 초과하는 데이터 드리프트 규모는 경고를 트리거합니다. 이 기능은 모니터 설정에서 구성할 수 있습니다. | 

### <a name="drift-magnitude-trend"></a>드리프트 규모 추세

지정된 기간 동안 데이터 세트가 대상 데이터 세트와 얼마나 다른지 확인할 수 있습니다.  100%에 가까울수록 두 데이터 세트는 서로 더 많이 다릅니다.

:::image type="content" source="media/how-to-monitor-datasets/drift-magnitude.png" alt-text="드리프트 규모 추세":::

### <a name="drift-magnitude-by-features"></a>특징별 드리프트 규모

이 섹션에는 선택한 특징 분포의 변화에 대한 특징 수준의 인사이트는 물론 시간별 다른 통계도 포함되어 있습니다.

대상 데이터 세트도 시간별로 프로파일링됩니다. 각 특징의 기준 분포 사이 통계적 거리는 시간별로 대상 데이터 세트와 비교됩니다.  개념적으로 이것은 데이터 드리프트 규모와 유사합니다.  단, 이러한 통계적 거리는 모든 특징보다는 개별 특징에 대한 것입니다. 최소, 최대 및 평균도 사용할 수 있습니다.

Azure Machine Learning 스튜디오에서 그래프의 막대를 클릭하면 해당 날짜의 특징 수준 세부 정보를 볼 수 있습니다. 기본적으로 기준 데이터 세트의 분포 및 동일한 특징의 가장 최근 실행 분포가 표시됩니다.

:::image type="content" source="media/how-to-monitor-datasets/drift-by-feature.gif" alt-text="특징별 드리프트 규모":::

이러한 메트릭은 `DataDriftDetector` 개체의 `get_metrics()` 메서드를 통해 Python SDK에서 검색할 수도 있습니다.

### <a name="feature-details"></a>특징 세부 정보

마지막으로 아래로 스크롤하여 각 개별 특징에 대한 세부 정보를 봅니다.  차트 위의 드롭다운을 사용하면 특징을 선택할 수 있고 보려는 메트릭도 선택할 수 있습니다.

:::image type="content" source="media/how-to-monitor-datasets/numeric-feature.gif" alt-text="숫자형 특징 그래프 및 비교":::

차트의 메트릭은 특징 유형에 따라 달라집니다.

* 숫자형 특징

    | 메트릭 | Description |  
    | ------ | ----------- |  
    | 워서스테인 거리 | 기준 분포를 대상 분포로 변환하기 위한 최소 작업량입니다. |
    | 평균값 | 특징의 평균값입니다. |
    | 최솟값 | 특징의 최솟값입니다. |
    | 최댓값 | 특징의 최댓값입니다. |

* 범주형 특징
    
    | 메트릭 | Description |  
    | ------ | ----------- |  
    | 유클리디안 거리     |  범주형 열에 대해 계산됩니다. 유클리디안 거리는 두 데이터 세트에서 동일한 범주형 열의 경험적 분포에서 생성된 두 벡터에 대해 계산됩니다. 0은 경험적 분포에 차이가 없음을 나타냅니다.  0에서 멀어질수록 이 열은 더 많이 드리프트됩니다. 추세는 이 메트릭의 시계열 도표에서 관찰할 수 있으며 드리프트 특징을 발견하는 데 도움이 될 수 있습니다.  |
    | 고유한 값 | 특징에서 고유한 값(카디널리티)의 수입니다. |

이 차트에서 단일 날짜를 선택하면 표시된 특징에 대해 대상 날짜와 선택한 날짜 사이의 특징 분포를 비교할 수 있습니다. 숫자형 특징의 경우 두 가지 확률 분포를 보여줍니다.  특징이 숫자인 경우 막대형 차트가 표시됩니다.

:::image type="content" source="media/how-to-monitor-datasets/select-date-to-compare.gif" alt-text="대상과 비교할 날짜 선택":::

## <a name="metrics-alerts-and-events"></a>메트릭, 경고 및 이벤트

메트릭은 기계 학습 작업 영역과 연결된 [Azure Application Insights](../azure-monitor/app/app-insights-overview.md) 리소스에서 쿼리할 수 있습니다. 이메일/SMS/푸시/음성 또는 Azure Function과 같은 작업을 트리거하는 작업 그룹 및 사용자 지정 경고 규칙에 대한 설정을 포함하여 Application Insights의 모든 기능에 액세스할 수 있습니다. 자세한 내용은 전체 Application Insights 설명서를 참조하세요. 

시작하려면 [Azure Portal](https://portal.azure.com)로 이동하여 작업 영역의 **개요** 페이지를 선택합니다.  연결된 Application Insights 리소스는 오른쪽 끝에 있습니다.

[![Azure Portal 개요](./media/how-to-monitor-datasets/ap-overview.png)](media/how-to-monitor-datasets/ap-overview-expanded.png)

왼쪽 창의 모니터링 아래에서 로그(분석)를 선택합니다.

![Application Insights 개요](./media/how-to-monitor-datasets/ai-overview.png)

데이터 세트 모니터 메트릭은 `customMetrics`로 저장됩니다. 데이터 세트 모니터를 설정한 후 쿼리를 작성하고 실행하여 볼 수 있습니다.

[![로그 분석 쿼리](./media/how-to-monitor-datasets/simple-query.png)](media/how-to-monitor-datasets/simple-query-expanded.png)

경고 규칙을 설정하기 위한 메트릭을 식별한 후 새 경고 규칙을 만듭니다.

![새 경고 규칙](./media/how-to-monitor-datasets/alert-rule.png)

기존 작업 그룹을 사용하거나 새 작업 그룹을 만들어서 설정한 조건이 충족될 때 수행할 작업을 정의할 수 있습니다.

![새 작업 그룹](./media/how-to-monitor-datasets/action-group.png)


## <a name="troubleshooting"></a>문제 해결

데이터 드리프트 모니터의 제한 사항 및 알려진 문제:

* 기록 데이터를 분석할 때 시간 범위는 모니터의 빈도 설정 간격 31개로 제한됩니다. 
* 특징 목록이 지정되지 않은 경우(모든 특징이 사용됨) 특징은 200개로 제한됩니다.
* 컴퓨팅 크기는 데이터를 처리할 수 있을 만큼 커야 합니다.
* 데이터 세트에는 지정된 모니터 실행의 시작 및 종료 날짜 내의 데이터가 있어야 합니다.
* 데이터 세트 모니터는 50개 이상의 행이 포함된 데이터 세트에서만 작동합니다.
* 데이터 세트의 열 또는 특징은 다음 표의 조건에 따라 범주형 또는 숫자형으로 분류됩니다. 특징이 이러한 조건을 충족하지 않는 경우(예: 고유한 값이 100개를 초과하는 문자열 유형의 열) 데이터 드리프트 알고리즘에서 특징이 삭제되지만 여전히 프로파일링됩니다. 

    | 기능 유형 | 데이터 형식 | 조건 | 제한 사항 | 
    | ------------ | --------- | --------- | ----------- |
    | 범주 | 문자열, 부울, 정수, 부동 소수점 | 특징의 고유 값 개수가 100개 미만이고 행 수의 5% 미만입니다. | Null은 자체 범주로 처리됩니다. | 
    | 숫자 | 정수, 부동 소수점 | 특징의 값은 숫자형 데이터 형식이며 범주형 특징의 조건을 충족하지 않습니다. | null이 값의 15%를 초과하면 특징이 삭제됩니다. | 

* 데이터 드리프트 모니터를 만들었지만 Azure Machine Learning 스튜디오의 **데이터 세트 모니터** 페이지에서 데이터를 볼 수 없는 경우 다음을 시도해보세요.

    1. 페이지 맨 위에서 올바른 날짜 범위를 선택했는지 확인합니다.  
    1. **데이터 세트 모니터** 탭에서 실험 링크를 선택하여 실행 상태를 확인합니다.  이 링크는 테이블의 오른쪽 끝에 있습니다.
    1. 실행이 성공적으로 완료되면 드라이버 로그를 확인하여 생성된 메트릭 수를 확인하거나 경고 메시지가 있는지 확인합니다.  드라이버 로그는 실험을 클릭한 후 **출력 + 로그** 탭에서 찾을 수 있습니다.

* SDK `backfill()` 함수가 예상되는 출력을 생성하지 않는 경우 인증 문제가 원인일 수 있습니다.  이 함수에 전달할 컴퓨팅을 생성할 때 `Run.get_context().experiment.workspace.compute_targets`을 사용하지 마십시오.  대신 [ServicePrincipalAuthentication](/python/api/azureml-core/azureml.core.authentication.serviceprincipalauthentication)을 아래와 같이 사용하여 `backfill()` 함수에 전달할 컴퓨팅을 만듭니다. 

  ```python
   auth = ServicePrincipalAuthentication(
          tenant_id=tenant_id,
          service_principal_id=app_id,
          service_principal_password=client_secret
          )
   ws = Workspace.get("xxx", auth=auth, subscription_id="xxx", resource_group="xxx")
   compute = ws.compute_targets.get("xxx")
   ```

* 모델 데이터 수집기에서 데이터가 Blob Storage 계정에 도착하는 데 최대 10분(일반적으로 그 미만)이 걸릴 수 있습니다. 스크립트나 노트북에서 아래 셀이 실행되도록 10분 정도 기다립니다.

    ```python
    import time
    time.sleep(600)
    ```

## <a name="next-steps"></a>다음 단계

* [Azure Machine Learning 스튜디오](https://ml.azure.com) 또는 [Python Notebook](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/work-with-data/datadrift-tutorial/datadrift-tutorial.ipynb)으로 이동하여 데이터 세트 모니터를 설정합니다.
* [Azure Kubernetes Service에 배포된 모델](./how-to-enable-data-collection.md)에서 데이터 드리프트를 설정하는 방법을 참조하세요.
* [이벤트 그리드](how-to-use-event-grid.md)로 데이터 세트 드리프트 모니터를 설정합니다.
