---
title: Notebook 코드를 Python 스크립트로 변환
titleSuffix: Azure Machine Learning
description: MLOpsPython 코드 템플릿을 사용하여 기계 학습 실험 Notebooks를 프로덕션 준비 코드로 전환합니다. 그런 다음, 해당 코드를 테스트, 배포 및 자동화할 수 있습니다.
author: bjcmit
ms.author: brysmith
ms.service: machine-learning
ms.subservice: mlops
ms.topic: tutorial
ms.date: 10/21/2021
ms.custom: devx-track-python
ms.openlocfilehash: caaaf01d8b1baa5a36726996f24b244f0d9afb46
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/04/2021
ms.locfileid: "131555669"
---
# <a name="tutorial-convert-ml-experiments-to-production-python-code"></a>자습서: ML 실험을 프로덕션 Python 코드로 변환

이 자습서에서는 MLOpsPython 코드 템플릿 및 Azure Machine Learning을 사용하여 Jupyter Notebooks를 Python 스크립트로 변환하여 테스트 및 자동화에 익숙해지는 방법을 알아봅니다. 일반적으로 이 프로세스는 Jupyter Notebook에서 실험 / 학습 코드를 가져와서 Python 스크립트로 변환하는 데 사용됩니다. 그런 다음, 프로덕션 환경에서 이러한 스크립트를 테스트 및 CI/CD 자동화에 사용할 수 있습니다. 

기계 학습 프로젝트에는 실제 데이터 세트를 사용하여 Jupyter Notebook과 같은 Agile 도구로 가설을 테스트하는 실험이 필요합니다. 모델을 프로덕션에 사용할 준비가 되면, 모델 코드를 프로덕션 코드 리포지토리에 배치해야 합니다. 프로덕션 코드 리포지토리에 배치할 모델 코드를 Python 스크립트로 변환해야 하는 경우가 있습니다. 이 자습서에서는 실험 코드를 Python 스크립트로 내보내는 방법에 권장되는 방식을 설명합니다.

이 자습서에서는 다음 작업 방법을 알아봅니다.

> [!div class="checklist"]
> * 불필요한 코드 정리
> * Jupyter Notebook 코드를 함수로 리팩터링
> * 관련 작업을 위한 Python 스크립트 만들기
> * 단위 테스트 만들기

## <a name="prerequisites"></a>사전 요구 사항

- [MLOpsPython 템플릿](https://github.com/microsoft/MLOpsPython/generate)을 생성하고 `experimentation/Diabetes Ridge Regression Training.ipynb` 및 `experimentation/Diabetes Ridge Regression Scoring.ipynb` 노트북을 사용합니다. 이러한 노트북은 실험에서 프로덕션으로 변환하는 예로 사용 됩니다. 이러한 Notebooks는 [https://github.com/microsoft/MLOpsPython/tree/master/experimentation](https://github.com/microsoft/MLOpsPython/tree/master/experimentation)에서 찾을 수 있습니다.
- `nbconvert`설치 [Installation](https://nbconvert.readthedocs.io/en/latest/install.html)(설치) 페이지의 __Installing nbconvert__(nbconvert 설치) 섹션에 있는 설치 지침을 따릅니다.

## <a name="remove-all-nonessential-code"></a>불필요한 코드 모두 제거

실험 중에 작성된 일부 코드는 탐색 목적으로만 사용됩니다. 따라서 실험 코드를 프로덕션 코드로 변환하는 첫 번째 단계는 이런 불필요한 코드를 제거하는 것입니다. 불필요한 코드를 제거하면 코드를 더 쉽게 유지 관리할 수 있습니다. 이 섹션에서는 `experimentation/Diabetes Ridge Regression Training.ipynb` 노트북에서 코드를 제거합니다. `X`와 `y`의 모양을 인쇄하는 명령문과 `features.describe`를 호출하는 셀은 데이터 탐색만을 위한 코드이며 제거될 수 있습니다. 불필요한 코드를 제거하고 나면 `experimentation/Diabetes Ridge Regression Training.ipynb`는 Markdown이 없는 다음 코드처럼 표시됩니다.

```python
from sklearn.datasets import load_diabetes
from sklearn.linear_model import Ridge
from sklearn.metrics import mean_squared_error
from sklearn.model_selection import train_test_split
import joblib
import pandas as pd

sample_data = load_diabetes()

df = pd.DataFrame(
    data=sample_data.data,
    columns=sample_data.feature_names)
df['Y'] = sample_data.target

X = df.drop('Y', axis=1).values
y = df['Y'].values

X_train, X_test, y_train, y_test = train_test_split(
    X, y, test_size=0.2, random_state=0)
data = {"train": {"X": X_train, "y": y_train},
        "test": {"X": X_test, "y": y_test}}

args = {
    "alpha": 0.5
}

reg_model = Ridge(**args)
reg_model.fit(data["train"]["X"], data["train"]["y"])

preds = reg_model.predict(data["test"]["X"])
mse = mean_squared_error(preds, y_test)
metrics = {"mse": mse}
print(metrics)

model_name = "sklearn_regression_model.pkl"
joblib.dump(value=reg, filename=model_name)
```

## <a name="refactor-code-into-functions"></a>코드를 함수로 리팩터링

둘째, Jupyter 코드를 함수로 리팩터링해야 합니다. 코드를 함수로 리팩터링하면 단위 테스트가 쉬워지고 코드 유지 관리가 더 수월해집니다 이 섹션에서는 다음을 리팩터링합니다.

- 당뇨병 릿지 회귀 학습 노트북(`experimentation/Diabetes Ridge Regression Training.ipynb`)
- 당뇨병 릿지 회귀 채점 노트북(`experimentation/Diabetes Ridge Regression Scoring.ipynb`)

### <a name="refactor-diabetes-ridge-regression-training-notebook-into-functions"></a>당뇨병 릿지 회귀 학습 노트북을 함수로 리팩터링

`experimentation/Diabetes Ridge Regression Training.ipynb`에서 다음 단계를 완료합니다.

1. 데이터 프레임을 테스트로 분할하고 데이터를 학습시키는 `split_data` 함수를 만듭니다. 이 함수는 데이터 프레임 `df`를 매개 변수로 사용하며, `train` 및 `test` 키를 포함하는 사전을 반환합니다.

    *Split Data into Training and Validation Sets(데이터를 학습 및 유효성 검사 세트로 분할)* 제목 아래의 코드를 `split_data` 함수로 이동하고, `data` 개체를 반환하도록 코드를 수정합니다.

1. `train_model`이라는 함수를 만듭니다. 이 함수는 `data` 및 `args` 매개 변수를 사용하고 학습된 모델을 반환합니다.

    *Training Model on Training Set(학습 세트의 모델 학습)* 제목 아래의 코드를 `train_model` 함수로 이동하고, `reg_model` 개체를 반환하도록 코드를 수정합니다. `args` 사전을 제거합니다. 이 값은 `args` 매개 변수에서 제공됩니다.

1. `get_model_metrics`라는 함수를 만듭니다. 이 함수는 `reg_model` 및 `data` 매개 변수를 사용하며, 모델을 평가한 후 학습된 모델에 대한 메트릭 사전을 반환합니다.

    *Validate Model on Validation Set(유효성 검사 세트의 모델 유효성 검사)* 제목 아래의 코드를 `get_model_metrics` 함수로 이동하고, `metrics` 개체를 반환하도록 코드를 수정합니다.

세 함수는 다음과 같아야 합니다.

```python
# Split the dataframe into test and train data
def split_data(df):
    X = df.drop('Y', axis=1).values
    y = df['Y'].values

    X_train, X_test, y_train, y_test = train_test_split(
        X, y, test_size=0.2, random_state=0)
    data = {"train": {"X": X_train, "y": y_train},
            "test": {"X": X_test, "y": y_test}}
    return data


# Train the model, return the model
def train_model(data, args):
    reg_model = Ridge(**args)
    reg_model.fit(data["train"]["X"], data["train"]["y"])
    return reg_model


# Evaluate the metrics for the model
def get_model_metrics(reg_model, data):
    preds = reg_model.predict(data["test"]["X"])
    mse = mean_squared_error(preds, data["test"]["y"])
    metrics = {"mse": mse}
    return metrics
```

계속 `experimentation/Diabetes Ridge Regression Training.ipynb`에서 다음 단계를 완료합니다.

1. `main`이라는 함수를 새로 만듭니다. 이 함수는 매개 변수를 사용하지 않고 아무것도 반환하지 않습니다.
1. "데이터 로드" 제목 아래의 코드를 `main` 함수로 이동합니다.
1. 다음과 같이 새로 작성된 함수에 대한 호출을 `main` 함수에 추가합니다.
    ```python
    # Split Data into Training and Validation Sets
    data = split_data(df)
    ```

    ```python
    # Train Model on Training Set
    args = {
        "alpha": 0.5
    }
    reg = train_model(data, args)
    ```

    ```python
    # Validate Model on Validation Set
    metrics = get_model_metrics(reg, data)
    ```
1. "모델 저장" 제목 아래의 코드를 `main` 함수로 이동합니다.

`main` 함수는 다음 코드와 같은 모양입니다.

```python
def main():
    # Load Data
    sample_data = load_diabetes()

    df = pd.DataFrame(
        data=sample_data.data,
        columns=sample_data.feature_names)
    df['Y'] = sample_data.target

    # Split Data into Training and Validation Sets
    data = split_data(df)

    # Train Model on Training Set
    args = {
        "alpha": 0.5
    }
    reg = train_model(data, args)

    # Validate Model on Validation Set
    metrics = get_model_metrics(reg, data)

    # Save Model
    model_name = "sklearn_regression_model.pkl"

    joblib.dump(value=reg, filename=model_name)
```

이 단계에서는 첫 번째 셀의 import 문 외에는 함수에 없는 코드가 Notebook에 남아 있으면 안 됩니다.

`main` 함수를 호출하는 문을 추가합니다.

```python
main()
```

리팩터링 후 `experimentation/Diabetes Ridge Regression Training.ipynb`는 Markdown 없이 다음 코드처럼 표시됩니다.

```python
from sklearn.datasets import load_diabetes
from sklearn.linear_model import Ridge
from sklearn.metrics import mean_squared_error
from sklearn.model_selection import train_test_split
import pandas as pd
import joblib


# Split the dataframe into test and train data
def split_data(df):
    X = df.drop('Y', axis=1).values
    y = df['Y'].values

    X_train, X_test, y_train, y_test = train_test_split(
        X, y, test_size=0.2, random_state=0)
    data = {"train": {"X": X_train, "y": y_train},
            "test": {"X": X_test, "y": y_test}}
    return data


# Train the model, return the model
def train_model(data, args):
    reg_model = Ridge(**args)
    reg_model.fit(data["train"]["X"], data["train"]["y"])
    return reg_model


# Evaluate the metrics for the model
def get_model_metrics(reg_model, data):
    preds = reg_model.predict(data["test"]["X"])
    mse = mean_squared_error(preds, data["test"]["y"])
    metrics = {"mse": mse}
    return metrics


def main():
    # Load Data
    sample_data = load_diabetes()

    df = pd.DataFrame(
        data=sample_data.data,
        columns=sample_data.feature_names)
    df['Y'] = sample_data.target

    # Split Data into Training and Validation Sets
    data = split_data(df)

    # Train Model on Training Set
    args = {
        "alpha": 0.5
    }
    reg = train_model(data, args)

    # Validate Model on Validation Set
    metrics = get_model_metrics(reg, data)

    # Save Model
    model_name = "sklearn_regression_model.pkl"

    joblib.dump(value=reg, filename=model_name)

main()
```

### <a name="refactor-diabetes-ridge-regression-scoring-notebook-into-functions"></a>당뇨병 릿지 회귀 채점 노트북을 함수로 리팩터링

`experimentation/Diabetes Ridge Regression Scoring.ipynb`에서 다음 단계를 완료합니다.

1. `init`라는 함수를 새로 만듭니다. 이 함수는 매개 변수를 사용하지 않고 아무것도 반환하지 않습니다.
1. "모델 로드" 제목 아래에 있는 코드를 `init` 함수에 복사합니다.

`init` 함수는 다음 코드와 같은 모양입니다.

```python
def init():
    model_path = Model.get_model_path(
        model_name="sklearn_regression_model.pkl")
    model = joblib.load(model_path)
```

`init` 함수가 만들어지면, "모델 로드" 제목 아래에 있는 모든 코드를 다음과 같이 `init`에 대한 단일 호출로 바꿉니다.

```python
init()
```

`experimentation/Diabetes Ridge Regression Scoring.ipynb`에서 다음 단계를 완료합니다.

1. `run`이라는 함수를 새로 만듭니다. 이 함수는 매개 변수 `raw_data` 및 `request_headers`를 사용하고 다음과 같이 결과 사전을 반환합니다.

    ```python
    {"result": result.tolist()}
    ```

1. "데이터 준비" 및 "데이터 채점" 제목 아래에 있는 코드를 `run` 함수에 복사합니다.

    `run` 함수는 다음 코드와 같은 모양입니다. (`raw_data` 및 `request_headers` 변수를 설정하는 명령문을 제거해야 합니다. 이것은 `run` 함수가 호출될 때 사용됩니다.)

    ```python
    def run(raw_data, request_headers):
        data = json.loads(raw_data)["data"]
        data = numpy.array(data)
        result = model.predict(data)

        return {"result": result.tolist()}
    ```

`run` 함수가 만들어지면 "데이터 준비" 및 "데이터 채점" 제목 아래에 있는 모든 코드를 다음 코드로 바꿉니다.

```python
raw_data = '{"data":[[1,2,3,4,5,6,7,8,9,10],[10,9,8,7,6,5,4,3,2,1]]}'
request_header = {}
prediction = run(raw_data, request_header)
print("Test result: ", prediction)
```

이전 코드는 `raw_data` 및 `request_header` 변수를 설정하고 `raw_data` 및 `request_header`로 `run` 함수를 호출하고 예측을 출력합니다.

리팩터링 후 `experimentation/Diabetes Ridge Regression Scoring.ipynb`는 Markdown 없이 다음 코드처럼 표시됩니다.

```python
import json
import numpy
from azureml.core.model import Model
import joblib

def init():
    model_path = Model.get_model_path(
        model_name="sklearn_regression_model.pkl")
    model = joblib.load(model_path)

def run(raw_data, request_headers):
    data = json.loads(raw_data)["data"]
    data = numpy.array(data)
    result = model.predict(data)

    return {"result": result.tolist()}

init()
test_row = '{"data":[[1,2,3,4,5,6,7,8,9,10],[10,9,8,7,6,5,4,3,2,1]]}'
request_header = {}
prediction = run(test_row, {})
print("Test result: ", prediction)
```

## <a name="combine-related-functions-in-python-files"></a>Python 파일에서 관련 함수 결합

셋째, 코드 재사용이 수월하도록 관련 함수를 Python 파일에 병합해야 합니다. 이 섹션에서는 다음 노트북을 위한 Python 파일을 만듭니다.

- 당뇨병 릿지 회귀 학습 노트북(`experimentation/Diabetes Ridge Regression Training.ipynb`)
- 당뇨병 릿지 회귀 채점 노트북(`experimentation/Diabetes Ridge Regression Scoring.ipynb`)

### <a name="create-python-file-for-the-diabetes-ridge-regression-training-notebook"></a>당뇨병 릿지 회귀 학습 노트북용 Python 파일 만들기

명령 프롬프트에서 다음 명령문을 실행하여 Notebook을 실행 가능한 스크립트로 변환합니다. 여기에는 `nbconvert` 패키지와 `experimentation/Diabetes Ridge Regression Training.ipynb` 경로가 사용됩니다.

```
jupyter nbconvert "Diabetes Ridge Regression Training.ipynb" --to script --output train
```

Notebook이 `train.py`로 변환된 후에는 필요 없는 주석을 제거합니다. 파일 끝부분의 `main()` 호출을 다음 코드와 비슷한 조건부 호출로 바꿉니다.

```python
if __name__ == '__main__':
    main()
```

`train.py` 파일은 다음 코드와 같은 모양입니다.

```python
from sklearn.datasets import load_diabetes
from sklearn.linear_model import Ridge
from sklearn.metrics import mean_squared_error
from sklearn.model_selection import train_test_split
import pandas as pd
import joblib


# Split the dataframe into test and train data
def split_data(df):
    X = df.drop('Y', axis=1).values
    y = df['Y'].values

    X_train, X_test, y_train, y_test = train_test_split(
        X, y, test_size=0.2, random_state=0)
    data = {"train": {"X": X_train, "y": y_train},
            "test": {"X": X_test, "y": y_test}}
    return data


# Train the model, return the model
def train_model(data, args):
    reg_model = Ridge(**args)
    reg_model.fit(data["train"]["X"], data["train"]["y"])
    return reg_model


# Evaluate the metrics for the model
def get_model_metrics(reg_model, data):
    preds = reg_model.predict(data["test"]["X"])
    mse = mean_squared_error(preds, data["test"]["y"])
    metrics = {"mse": mse}
    return metrics


def main():
    # Load Data
    sample_data = load_diabetes()

    df = pd.DataFrame(
        data=sample_data.data,
        columns=sample_data.feature_names)
    df['Y'] = sample_data.target

    # Split Data into Training and Validation Sets
    data = split_data(df)

    # Train Model on Training Set
    args = {
        "alpha": 0.5
    }
    reg = train_model(data, args)

    # Validate Model on Validation Set
    metrics = get_model_metrics(reg, data)

    # Save Model
    model_name = "sklearn_regression_model.pkl"

    joblib.dump(value=reg, filename=model_name)

if __name__ == '__main__':
    main()
```

이제 터미널에서 `python train.py`를 실행하여 `train.py`를 호출할 수 있습니다.
`train.py`의 함수는 다른 파일에서도 호출할 수 있습니다.

MLOpsPython 리포지토리의 `diabetes_regression/training` 디렉터리에 있는 `train_aml.py` 파일은 Azure Machine Learning 실험 실행 컨텍스트에서 `train.py`에 정의된 함수를 호출합니다. 단위 테스트에서도 함수를 호출할 수 있으며, 이 내용은 이 가이드의 뒷부분에서 다루겠습니다.

### <a name="create-python-file-for-the-diabetes-ridge-regression-scoring-notebook"></a>당뇨병 릿지 회귀 채점 노트북용 Python 파일 만들기

`nbconvert` 패키지와 `experimentation/Diabetes Ridge Regression Scoring.ipynb` 경로가 사용되는 명령 프롬프트에서 다음 명령문을 실행하여 Notebook을 실행 가능한 스크립트로 변환합니다.

```
jupyter nbconvert "Diabetes Ridge Regression Scoring.ipynb" --to script --output score
```

Notebook이 `score.py`로 변환된 후에는 필요 없는 주석을 제거합니다. `score.py` 파일은 다음 코드와 같은 모양입니다.

```python
import json
import numpy
from azureml.core.model import Model
import joblib

def init():
    model_path = Model.get_model_path(
        model_name="sklearn_regression_model.pkl")
    model = joblib.load(model_path)

def run(raw_data, request_headers):
    data = json.loads(raw_data)["data"]
    data = numpy.array(data)
    result = model.predict(data)

    return {"result": result.tolist()}

init()
test_row = '{"data":[[1,2,3,4,5,6,7,8,9,10],[10,9,8,7,6,5,4,3,2,1]]}'
request_header = {}
prediction = run(test_row, request_header)
print("Test result: ", prediction)
```

`model` 변수는 스크립트 전체에서 볼 수 있도록 글로벌 변수여야 합니다. 다음 명령문을 `init` 함수의 시작 부분에 추가합니다.

```python
global model
```

이전 명령문을 추가하면 `init` 함수는 다음 코드와 같은 모양입니다.

```python
def init():
    global model

    # load the model from file into a global object
    model_path = Model.get_model_path(
        model_name="sklearn_regression_model.pkl")
    model = joblib.load(model_path)
```

## <a name="create-unit-tests-for-each-python-file"></a>각 Python 파일에 대한 단위 테스트 만들기

네 번째로, Python 함수에 대한 단위 테스트를 만듭니다. 단위 테스트는 함수 회귀로부터 코드를 보호하고 유지 관리를 더 쉽게 만들어 줍니다. 이 섹션에서는 `train.py`의 함수에 대한 단위 테스트를 만듭니다.

`train.py`에는 여러 함수가 포함되지만, 이 자습서에서는 Pytest 프레임워크를 사용하여 `train_model` 함수에 대한 단위 테스트 하나만 만듭니다. Pytest가 유일한 Python 단위 테스트 프레임워크는 아니지만 가장 자주 사용되는 것 중 하나입니다. 자세한 내용은 [Pytest](https://pytest.org)를 참조하세요.

단위 테스트는 일반적으로 다음과 같은 세가지 주요 동작을 포함합니다.

- 개체 정렬 - 필요한 개체 만들기 및 설정
- 개체에 대한 동작
- 예상되는 사항 어설션

단위 테스트는 하드 코딩된 데이터와 인수를 사용하여 `train_model`을 호출하고, 그 결과로 얻은 학습된 모델을 사용하여 예측을 수행하고 예측을 예상 값과 비교하여 `train_model`이 예상대로 작동하는지 확인합니다.

```python
import numpy as np
from code.training.train import train_model


def test_train_model():
    # Arrange
    X_train = np.array([1, 2, 3, 4, 5, 6]).reshape(-1, 1)
    y_train = np.array([10, 9, 8, 8, 6, 5])
    data = {"train": {"X": X_train, "y": y_train}}

    # Act
    reg_model = train_model(data, {"alpha": 1.2})

    # Assert
    preds = reg_model.predict([[1], [2]])
    np.testing.assert_almost_equal(preds, [9.93939393939394, 9.03030303030303])
```

## <a name="next-steps"></a>다음 단계

실험에서 프로덕션 코드로 변환하는 방법을 살펴보았으므로, 다음 링크를 통해 자세한 내용을 살펴보고 다음 단계를 수행하세요.

+ [MLOpsPython](https://github.com/microsoft/MLOpsPython/blob/master/docs/custom_model.md): Azure Pipelines 및 Azure Machine Learning을 사용하여 사용자 고유의 모델을 학습, 평가 및 배포할 수 있는 CI/CD 파이프라인 빌드
+ [Azure ML 실험 실행 및 메트릭 모니터링](./how-to-log-view-metrics.md)
+ [ML 웹 서비스 엔드포인트에서 데이터 모니터링 및 수집](./how-to-enable-app-insights.md)
