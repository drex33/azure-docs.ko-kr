---
title: 자동화된 ML 실험 문제 해결
titleSuffix: Azure Machine Learning
description: 자동화된 Machine Learning 실험에서 문제를 해결하는 방법을 알아봅니다.
author: nibaccam
ms.author: nibaccam
ms.reviewer: nibaccam
services: machine-learning
ms.service: machine-learning
ms.subservice: automl
ms.date: 03/08/2021
ms.topic: troubleshooting
ms.custom: devx-track-python, automl, references_regions
ms.openlocfilehash: 3634cce207462695fdb7fadfecfb2f3f520d621f
ms.sourcegitcommit: f29615c9b16e46f5c7fdcd498c7f1b22f626c985
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/04/2021
ms.locfileid: "129428106"
---
# <a name="troubleshoot-automated-ml-experiments-in-python"></a>Python에서 자동화된 ML 실험 문제 해결

이 가이드에서는 [Azure Machine Learning SDK](/python/api/overview/azure/ml/intro)를 사용하여 자동화된 Machine Learning 실험의 알려진 문제를 식별하고 해결하는 방법에 대해 알아봅니다.

## <a name="version-dependencies"></a>버전 종속성

**최신 패키지 버전에 대한 `AutoML` 종속성은 호환성을 저해합니다**. 이전 `AutoML` 패키지에 고정 된 이전 버전과 현재 고정된 최신 버전은 호환되지 않으므로 SDK 버전 1.13.0 이후 모델은 이전 SDK에서 로드되지 않습니다.

다음과 같은 오류가 예상됩니다.

* 다음과 같은 모듈을 찾을 수 없음 오류

  `No module named 'sklearn.decomposition._truncated_svd`

* 다음과 같은 가져오기 오류

  `ImportError: cannot import name 'RollingOriginValidator'`,
* 다음과 같은 특성 오류

  `AttributeError: 'SimpleImputer' object has no attribute 'add_indicator`

해결 방법은 `AutoML` SDK 교육 버전에 따라 달라집니다.

* `AutoML`SDK 교육 버전이 1.13.0보다 큰 경우 `pandas == 0.25.1` 및 `scikit-learn==0.22.1`이 필요합니다.

    * 버전이 일치하지 않는 경우 다음을 사용하여 scikit-learn 및/또는 pandas를 올바른 버전으로 업그레이드합니다.

      ```bash
          pip install --upgrade pandas==0.25.1
          pip install --upgrade scikit-learn==0.22.1
      ```

* `AutoML`SDK 교육 버전이 1.12.0보다 작거나 같은 경우 및 `pandas == 0.23.4` 및 `sckit-learn==0.20.3`이 필요합니다.
  * 버전이 일치하지 않는 경우 다음을 사용하여 scikit-learn 및/또는 pandas를 올바른 버전으로 다운그레이드합니다.
  
    ```bash
      pip install --upgrade pandas==0.23.4
      pip install --upgrade scikit-learn==0.20.3
    ```

## <a name="setup"></a>설치 프로그램

버전 1.0.76 이후의 `AutoML` 패키지 변경 내용을 적용하려면 새 버전으로 업데이트하기 전에 이전 버전을 제거해야 합니다.

* **`ImportError: cannot import name AutoMLConfig`**

    SDK를 v1.0.76 이전 버전에서 v1.0.76 이상으로 업그레이드한 후에 이 오류가 발생하는 경우 `pip uninstall azureml-train automl`을 실행한 후 `pip install azureml-train-automl`을 실행하여 오류를 해결합니다. automl_setup.cmd 스크립트는 이 작업을 자동으로 수행합니다.

* **automl_setup 실패**

  * Windows에서는 Anaconda 프롬프트에서 automl_setup을 실행합니다. [Miniconda 설치](https://docs.conda.io/en/latest/miniconda.html)

  * Conda 64비트 버전 4.4.10 이상이 설치되어 있는지 확인합니다. `conda info` 명령을 사용하여 비트를 확인할 수 있습니다. `platform`은 Windows의 경우 `win-64`, Mac의 경우 `osx-64`여야 합니다. 버전을 확인하려면 `conda -V` 명령을 사용합니다. 이전 버전이 설치되어 있는 경우 `conda update conda` 명령을 사용하여 업데이트할 수 있습니다. 실행하여 32비트를 확인합니다. 

  * conda가 설치되어 있는지 확인합니다. 

  * Linux - `gcc: error trying to exec 'cc1plus'`

    1. `gcc: error trying to exec 'cc1plus': execvp: No such file or directory` 오류가 발생하면 Linux 배포용 GCC 빌드 도구를 설치합니다. 예를 들어 Ubuntu에서는 `sudo apt-get install build-essential` 명령을 사용합니다.

    1. 새 이름을 automl_setup에 대한 첫 번째 매개 변수로 전달하여 새 conda 환경을 만듭니다. `conda env list`를 사용하여 기존 conda 환경을 보고 `conda env remove -n <environmentname>`을 사용하여 제거합니다.

* **automl_setup_linux sh 실패**: automl_setup_linus. sh가 Ubuntu Linux에서 `unable to execute 'gcc': No such file or directory` 오류와 함께 실패합니다.

  1. 아웃바운드 포트 53 및 80을 사용하도록 설정했는지 확인합니다. Azure 가상 머신에서 VM을 선택하고 **네트워킹** 을 클릭하여 Azure Portal에서 이 작업을 수행할 수 있습니다.
  1. `sudo apt-get update` 명령을 실행합니다.
  1. `sudo apt-get install build-essential --fix-missing` 명령을 실행합니다.
  1. `automl_setup_linux.sh`를 다시 실행합니다.

* **configuration.ipynb 실패**:

  * 로컬 conda의 경우 먼저 `automl_setup`이 성공적으로 실행되었는지 확인합니다.
  * subscription_id가 올바른지 확인합니다. 모든 서비스를 선택한 다음, 구독을 선택하여 Azure Portal에서 subscription_id를 찾습니다. "<" 및 ">" 문자는 subscription_id 값에 포함되지 않아야 합니다. 예를 들어 `subscription_id = "12345678-90ab-1234-5678-1234567890abcd"`에는 유효한 형식이 있습니다.
  * 구독에 대한 기여자 또는 소유자 액세스 권한이 있는지 확인합니다.
  * 지역이 지원되는 지역 `eastus2`, `eastus`, `westcentralus`, `southeastasia`, `westeurope`, `australiaeast`, `westus2`, `southcentralus` 중 하나인지 확인합니다.
  * Azure Portal을 사용하여 지역에 대한 액세스 권한을 확인합니다.
  
* **workspace.from_config 실패**:

  `ws = Workspace.from_config()` 호출이 실패할 경우:

  1. configuration.ipynb Notebook이 성공적으로 실행되었는지 확인합니다.
  1. `configuration.ipynb`가 실행된 폴더에 속하지 않는 폴더에서 Notebook을 실행하는 경우 aml_config 폴더 이 폴더에 포함된 config.json 파일을 새 폴더에 복사합니다. Workspace.from_config는 Notebook 폴더 또는 해당 부모 폴더에 대한 config.json을 읽습니다.
  1. 새 구독, 리소스 그룹, 작업 영역 또는 지역이 사용 중인 경우에는 `configuration.ipynb` Notebook을 다시 실행해야 합니다. 지정된 구독에서 지정된 리소스 그룹에 작업 영역이 이미 있는 경우에만 config.json을 직접 변경할 수 있습니다.
  1. 지역을 변경하려면 작업 영역, 리소스 그룹 또는 구독을 변경합니다. `Workspace.create`는 작업 영역이 이미 있는 경우 새로 만들거나 업데이트하지 않습니다. 지정된 지역이 다른 경우에도 마찬가지입니다.

## <a name="tensorflow"></a>TensorFlow

SDK 1.5.0 버전부터 자동화된 Machine Learning은 기본적으로 TensorFlow 모델을 설치하지 않습니다. TensorFlow를 설치하고 자동화된 ML 실험에 사용하려면 `tensorflow==1.12.0`를 통해 `CondaDependencies`을 설치합니다.

```python
  from azureml.core.runconfig import RunConfiguration
  from azureml.core.conda_dependencies import CondaDependencies
  run_config = RunConfiguration()
  run_config.environment.python.conda_dependencies = CondaDependencies.create(conda_packages=['tensorflow==1.12.0'])
```

## <a name="numpy-failures"></a>Numpy 오류

* **Windows에서 `import numpy` 실패**: 일부 Windows 환경에서는 최신 Python 3.6.8 버전을 사용하여 numpy를 로드하는 동안 오류가 발생합니다. 이 문제가 발생하면 Python 3.6.7 버전으로 시도하세요.

* **`import numpy` 실패**: 자동화된 ml conda 환경에서 TensorFlow 버전을 확인합니다. 지원되는 버전은 1.13 미만입니다. 버전이 1.13 이상인 경우 환경에서 TensorFlow를 제거합니다.

TensorFlow의 버전을 확인하고 다음과 같이 제거할 수 있습니다.

  1. 명령 셸을 시작하고 자동화된 ml 패키지가 설치된 conda 환경을 활성화합니다.
  1. `pip freeze`를 입력하고 `tensorflow`를 검색합니다. 발견되면 나열된 버전이 1.13 미만이어야 합니다.
  1. 나열된 버전이 지원되는 버전이 아닌 경우 명령 셸에서 `pip uninstall tensorflow`를 입력하고 y를 입력하여 확인합니다.

## `jwt.exceptions.DecodeError`

정확한 오류 메시지: `jwt.exceptions.DecodeError: It is required that you pass in a value for the "algorithms" argument when calling decode()`.

SDK 버전이 1.17.0 이하인 경우에는 설치 중에 지원되지 않는 PyJWT 버전이 생성될 수 있습니다. 자동화된 ml conda 환경의 PyJWT 버전이 지원되는 버전인지 확인합니다. 이는 PyJWT 2.0.0 미만 버전입니다.

PyJWT의 버전은 다음과 같이 확인할 수 있습니다.

1. 명령 셸을 시작하고 자동화된 ML 패키지가 설치된 conda 환경을 활성화합니다.

1. `pip freeze`를 입력하고 `PyJWT`를 검색합니다. 발견되면 나열된 버전이 2.0.0 미만이어야 합니다.

나열된 버전이 지원되는 버전이 아닌 경우:

1. 최신 버전의 AutoML SDK로 업그레이드하는 것이 좋습니다. `pip install -U azureml-sdk[automl]`

1. 이를 실행할 수 없는 경우 환경에서 PyJWT를 제거하고 다음과 같이 올바른 버전을 설치합니다.

    1. 명령 셸에서 `pip uninstall PyJWT`를 입력하고 `y`를 입력하여 확인합니다.
    1. `pip install 'PyJWT<2.0.0'`을 사용하여 설치합니다.
  

## <a name="data-access"></a>데이터 액세스
 
자동화된 ML 실행의 경우 AzureFile 스토리지에 연결하는 파일 데이터 저장소에 적절한 인증 자격 증명이 있는지 확인해야 합니다. 그렇지 않으면 다음 메시지가 발생합니다. [데이터 액세스 인증 자격 증명을 업데이트](how-to-train-with-datasets.md#azurefile-storage)하는 방법을 알아봅니다.

오류 메시지: `Could not create a connection to the AzureFileService due to missing credentials. Either an Account Key or SAS token needs to be linked the default workspace blob store.`

## <a name="databricks"></a>Databricks
[Databricks를 사용하여 자동화된 ML 실험을 구성하는 방법](how-to-configure-databricks-automl-environment.md#troubleshooting)을 참조하세요.


## <a name="forecasting-r2-score-is-always-zero"></a>예측 R2 점수가 항상 0인 경우

 제공된 학습 데이터에서 마지막 `n_cv_splits` + `forecasting_horizon` 데이터 요소에 대한 동일한 값이 포함된 시계열이 있는 경우 이 문제가 발생합니다.

시계열에서 이 패턴이 예상되는 경우 기본 메트릭을 **정규화된 제곱 평균 오차** 로 전환할 수 있습니다.

## <a name="failed-deployment"></a>실패한 배포

 SDK 1.18.0 이하 버전의 경우 배포를 위해 만든 기본 이미지가 `ImportError: cannot import name cached_property from werkzeug` 오류와 함께 실패할 수 있습니다.

  다음 단계를 통해 이 문제를 해결할 수 있습니다.

  1. 모델 패키지 다운로드
  1. 패키지 압축 풀기
  1. 압축을 푼 자산을 사용하여 배포

## <a name="azure-functions-application"></a>Azure Functions 애플리케이션
  
  자동화된 ML은 현재 Azure Functions 애플리케이션을 지원하지 않습니다. 

## <a name="sample-notebook-failures"></a>샘플 Notebook 오류

 샘플 Notebook이 속성, 메서드 또는 라이브러리가 없다는 오류와 함께 실패하면 다음을 수행합니다.

* Jupyter Notebook에서 올바른 커널을 선택했는지 확인합니다. 이 커널은 Notebook 페이지의 오른쪽 위에 표시됩니다. 기본값은 *azure_automl* 입니다. 커널은 Notebook의 일부로 저장됩니다. 새 conda 환경으로 전환하는 경우 Notebook에서 새 커널을 선택해야 합니다.

  * Azure Notebooks의 경우 Python 3.6이어야 합니다.
  * 로컬 conda 환경의 경우에는 automl_setup에서지정된 conda 환경 이름이어야 합니다.

* 사용 중인 SDK 버전에 대한 Notebook이 있는지 확인하려면 다음을 수행합니다.
  * Jupyter Notebook 셀에서 `azureml.core.VERSION`을 실행하여 SDK 버전을 확인합니다.
  * 다음 단계에 따라 GitHub에서 이전 버전의 샘플 Notebook을 다운로드할 수 있습니다.
    1. `Branch` 단추 선택
    1. `Tags` 탭으로 이동
    1. 버전 선택

## <a name="next-steps"></a>다음 단계

+ [자동화된 Machine Learning을 사용하여 회귀 모델을 학습시키는 방법](tutorial-auto-train-models.md) 또는 [원격 리소스에서 자동화된 Machine Learning을 사용하여 학습하는 방법](concept-automated-ml.md#local-remote)에 대해 자세히 알아봅니다.

+ [모델 배포 방법 및 위치](how-to-deploy-and-where.md)에 대해 자세히 알아봅니다.
