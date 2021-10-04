---
title: 차등 프라이버시 방법 - SmartNoise(미리 보기)
titleSuffix: Azure Machine Learning
description: SmartNoise 오픈 소스 라이브러리를 사용하여 차등 프라이버시 모범 사례를 Azure Machine Learning 모델에 적용하는 방법에 대해 알아봅니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: enterprise-readiness
ms.topic: how-to
ms.custom: responsible-ml
ms.author: slbird
author: slbird
ms.reviewer: luquinta
ms.date: 01/21/2020
ms.openlocfilehash: 907d31ee7e4dd3278ab3712a6ab0142b56462a1b
ms.sourcegitcommit: f29615c9b16e46f5c7fdcd498c7f1b22f626c985
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/04/2021
ms.locfileid: "129426191"
---
# <a name="use-differential-privacy-in-azure-machine-learning-preview"></a>Azure Machine Learning에서 차등 프라이버시 사용(미리 보기)

SmartNoise Python 오픈 소스 라이브러리를 사용하여 Azure Machine Learning 모델에 차등 프라이버시 모범 사례를 적용하는 방법에 대해 알아봅니다.

차등 프라이버시는 프라이버시의 골드 표준 정의입니다. 이 프라이버시 정의를 준수하는 시스템은 보조 정보를 소유한 악의적 사용자의 공격을 포함하여 다양한 데이터 재구성 및 재식별 공격에 강력한 보증을 제공합니다. [차등 프라이버시 작동 원리](./concept-differential-privacy.md)에 대해 자세히 알아봅니다.


## <a name="prerequisites"></a>필수 구성 요소

- Azure 구독이 없는 경우 시작하기 전에 체험 계정을 만듭니다. 지금 [Azure Machine Learning 평가판 또는 유료 버전](https://azure.microsoft.com/free/)을 사용해 보세요.
- [Python 3](https://www.python.org/downloads/)

## <a name="install-smartnoise-python-libraries"></a>SmartNoise Python 라이브러리 설치

### <a name="standalone-installation"></a>독립 실행형 설치

라이브러리는 분산된 Spark 클러스터에서 작동하도록 설계되었으며 다른 패키지와 마찬가지로 설치할 수 있습니다.

아래 지침에서는 `python` 및 `pip` 명령이 `python3` 및 `pip3`에 매핑되는 것으로 가정합니다.

pip를 사용하여 [SmartNoise Python 패키지](https://pypi.org/project/opendp-smartnoise/)를 설치합니다.

`pip install opendp-smartnoise`

패키지가 설치되어 있는지 확인하려면 python 프롬프트를 시작하고 다음을 입력합니다.

```python
import opendp.smartnoise.core
import opendp.smartnoise.sql
```

가져오기를 성공하면 라이브러리가 설치되고 사용할 준비가 된 것입니다.

### <a name="docker-image-installation"></a>Docker 이미지 설치

SmartNoise 패키지를 Docker와 함께 사용할 수도 있습니다.

Spark, Jupyter 및 샘플 코드를 포함하는 Docker 컨테이너 내의 라이브러리를 사용하려면 `opendp/smartnoise` 이미지를 끌어옵니다.

[!INCLUDE [pull-image-include](../../includes/pull-image-include.md)]

```sh
docker pull opendp/smartnoise:privacy
```

이미지를 끌어온 후 Jupyter 서버를 시작합니다.

```sh
docker run --rm -p 8989:8989 --name smartnoise-run opendp/smartnoise:privacy
```

암호 `pass@word99`를 사용하여 `localhost`의 포트 `8989`에서 Jupyter 서버를 시작합니다. 위의 명령줄로 `smartnoise-privacy`라는 컨테이너를 시작했다고 가정하면 다음을 실행하여 Jupyter 서버에서 bash 터미널을 열 수 있습니다.

```sh
docker exec -it smartnoise-run bash
```

Docker 인스턴스는 종료 시 모든 상태를 지우므로 실행 중인 인스턴스에서 만든 Notebook은 모두 손실됩니다. 이를 해결하기 위해 시작할 때 컨테이너에 로컬 폴더를 탑재할 수 있습니다.

```sh
docker run --rm -p 8989:8989 --name smartnoise-run --mount type=bind,source=/Users/your_name/my-notebooks,target=/home/privacy/my-notebooks opendp/smartnoise:privacy
```

*my-notebooks* 폴더에서 만든 모든 Notebook은 로컬 파일 시스템에 저장됩니다.

## <a name="perform-data-analysis"></a>데이터 분석 수행

차등 프라이빗 릴리스를 준비하려면 데이터 원본, 통계 및 프라이버시 보호 수준을 나타내는 일부 프라이버시 매개 변수를 선택해야 합니다.  

이 샘플은 시민 인구 통계의 익명화된 레코드를 나타내는 캘리포니아 PUMS(공용 사용 마이크로 데이터)를 참조합니다.

```python
import os
import sys
import numpy as np
import opendp.smartnoise.core as sn

data_path = os.path.join('.', 'data', 'PUMS_california_demographics_1000', 'data.csv')
var_names = ["age", "sex", "educ", "race", "income", "married", "pid"]
```

이 예에서는 기간의 평균 및 분산을 계산합니다.  총 1.0의 `epsilon`을 사용합니다. epsilon은 프라이버시 매개 변수이며 계산하려는 두 수량에 대한 프라이버시 예산을 분산시킵니다. [프라이버시 메트릭](concept-differential-privacy.md#differential-privacy-metrics)에 대해 자세히 알아보세요.

```python
with sn.Analysis() as analysis:
    # load data
    data = sn.Dataset(path = data_path, column_names = var_names)

    # get mean of age
    age_mean = sn.dp_mean(data = sn.cast(data['age'], type="FLOAT"),
                          privacy_usage = {'epsilon': .65},
                          data_lower = 0.,
                          data_upper = 100.,
                          data_n = 1000
                         )
    # get variance of age
    age_var = sn.dp_variance(data = sn.cast(data['age'], type="FLOAT"),
                             privacy_usage = {'epsilon': .35},
                             data_lower = 0.,
                             data_upper = 100.,
                             data_n = 1000
                            )
analysis.release()

print("DP mean of age: {0}".format(age_mean.value))
print("DP variance of age: {0}".format(age_var.value))
print("Privacy usage: {0}".format(analysis.privacy_usage))
```

결과는 아래와 같습니다.

```text
DP mean of age: 44.55598845931517
DP variance of age: 231.79044646429134
Privacy usage: approximate {
  epsilon: 1.0
}
```

이 예에 대해 중요한 사항이 있습니다.  먼저 `Analysis` 개체는 데이터 처리 그래프를 나타냅니다.  이 예에서는 평균 및 분산이 동일한 원본 노드에서 계산됩니다.  그러나 입력을 임의의 방식으로 출력과 결합하는 더 복잡한 식을 포함할 수 있습니다.

분석 그래프에는 `data_upper` 및 `data_lower` 메타데이터가 포함되어 있으며,이는 기간의 하한과 상한을 지정합니다.  이러한 값은 차등 프라이버시를 보장하기 위해 정확하게 노이즈를 보정하는 데 사용됩니다.  이러한 값은 이상값 또는 누락 값을 처리하는 데도 사용됩니다.

마지막으로 분석 그래프는 소요된 총 프라이버시에 대한 예산을 추적합니다.

라이브러리를 사용하여 다음과 같은 여러 가지 메커니즘, 통계 및 유틸리티 함수를 사용하여 보다 복잡한 분석 그래프를 작성할 수 있습니다.

| 통계    | 메커니즘 | 공공 시설  |
| ------------- |------------|------------|
| 개수         | 가우스   | 캐스트       |
| 히스토그램     | 기하  | 고정   |
| 평균          | 라플라스    | 디지털화   |
| 분위수     |            | Assert     |
| 합계           |            | 대체 |
| 가변성/공변성(Covariance) |      | 변환  |

자세한 내용은 [데이터 분석 Notebook](https://github.com/opendifferentialprivacy/smartnoise-samples/blob/master/analysis/basic_data_analysis.ipynb)을 참조하세요.

## <a name="approximate-utility-of-differentially-private-releases"></a>차등 프라이빗 릴리스의 근사 유틸리티

차등 프라이버시는 노이즈 보정으로 작동하므로 릴리스 유틸리티는 프라이버시 위험도에 따라 달라질 수 있습니다.  일반적으로 샘플 크기가 커짐에 따라 각 개별을 보호하는 데 필요한 노이즈는 무시될 수 있지만 단일 개별을 대상으로 하는 릴리스의 결과에 과부화가 걸립니다.  분석가는 릴리스에 대한 정확도 정보를 검토하여 릴리스가 얼마나 유용한지 확인할 수 있습니다.

```python
with sn.Analysis() as analysis:
    # load data
    data = sn.Dataset(path = data_path, column_names = var_names)

    # get mean of age
    age_mean = sn.dp_mean(data = sn.cast(data['age'], type="FLOAT"),
                          privacy_usage = {'epsilon': .65},
                          data_lower = 0.,
                          data_upper = 100.,
                          data_n = 1000
                         )
analysis.release()

print("Age accuracy is: {0}".format(age_mean.get_accuracy(0.05)))
```

이 작업의 결과는 아래와 같이 표시됩니다.

```text
Age accuracy is: 0.2995732273553991
```

이 예에서는 위와 같이 평균을 계산하고 `get_accuracy` 함수를 사용하여 `alpha` 0.05의 정확도를 요청합니다. `alpha` 0.05는 95% 간격을 나타냅니다. 즉, 릴리스된 값이 보고된 정확도 범위 내에서 약 95%의 시간에 해당합니다.  이 예에서 보고된 정확도는 0.3입니다. 즉, 릴리스된 값이 폭 0.6의 간격, 약 95%의 시간 내에 있습니다.  릴리스된 값이 `alpha`에 지정된 비율로 보고된 정확도 범위를 벗어나고 범위 밖의 값이 어느 방향으로든 벗어날 수 있으므로 이 값을 오류 막대로 간주하는 것은 올바르지 않습니다.

분석가가 추가 프라이버시에 대한 비용을 발생시키지 않으면서 다른 `alpha` 값에 대해 `get_accuracy`를 쿼리하여 신뢰 구간을 좁히거나 넓힐 수 있습니다.

## <a name="generate-a-histogram"></a>히스토그램 생성

기본 제공 `dp_histogram` 함수는 다음 데이터 형식에 대해 차등 프라이빗 히스토그램을 만듭니다.

- 숫자 집합을 bin으로 분할해야 하는 연속 변수
- 두 개의 값만 사용할 수 있는 부울 또는 이분 변수
- 문자열로 열거된 고유 범주가 있는 범주 변수

다음은 연속 변수 히스토그램에 대한 bin을 지정하는 `Analysis` 예입니다.

```python
income_edges = list(range(0, 100000, 10000))

with sn.Analysis() as analysis:
    data = sn.Dataset(path = data_path, column_names = var_names)

    income_histogram = sn.dp_histogram(
            sn.cast(data['income'], type='int', lower=0, upper=100),
            edges = income_edges,
            upper = 1000,
            null_value = 150,
            privacy_usage = {'epsilon': 0.5}
        )
```

개별은 히스토그램 bin 사이에서 분할되므로 히스토그램에 많은 bin이 포함되어 있더라도 프라이버시 비용은 히스토그램당 한 번만 발생합니다.

히스토그램에 대한 자세한 내용은 [히스토그램 Notebook](https://github.com/opendifferentialprivacy/smartnoise-samples/blob/master/analysis/histograms.ipynb)을 참조하세요.

## <a name="generate-a-covariance-matrix"></a>공변성(Covariance) 행렬 생성

SmartNoise는 `dp_covariance` 함수를 사용하는 세 가지 기능을 제공합니다.

- 두 벡터 사이의 공변성
- 행렬의 공변성 행렬
- 행렬 쌍의 교차 공변성 행렬

다음은 스칼라 공변성을 계산하는 예입니다.

```python
with sn.Analysis() as analysis:
    wn_data = sn.Dataset(path = data_path, column_names = var_names)

    age_income_cov_scalar = sn.dp_covariance(
      left = sn.cast(wn_data['age'], 
      type = "FLOAT"), 
      right = sn.cast(wn_data['income'], 
      type = "FLOAT"), 
      privacy_usage = {'epsilon': 1.0},
      left_lower = 0., 
      left_upper = 100., 
      left_n = 1000, 
      right_lower = 0., 
      right_upper = 500_000.,
      right_n = 1000)
```

자세한 내용은 [공변성(Covariance) Notebook](
https://github.com/opendifferentialprivacy/smartnoise-samples/blob/master/analysis/covariance.ipynb)을 참조하세요.

## <a name="next-steps"></a>다음 단계

- [SmartNoise 샘플 Notebook](https://github.com/opendifferentialprivacy/smartnoise-samples/tree/master/analysis)을 살펴보세요.
