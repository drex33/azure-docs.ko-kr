---
title: AutoML 및 Azure Databricks를 통한 개발
titleSuffix: Azure Machine Learning
description: Azure Machine Learning 및 Azure Databricks에서 개발 환경을 설정하는 방법을 알아봅니다. AutoML에서 Databricks 및 Databricks에 대한 Azure ML SDK를 사용합니다.
services: machine-learning
author: rastala
ms.author: roastala
ms.service: machine-learning
ms.subservice: automl
ms.reviewer: larryfr
ms.date: 10/21/2021
ms.topic: how-to
ms.custom: devx-track-python
ms.openlocfilehash: c46803ae300acfe36163edcb92770c069ae67382
ms.sourcegitcommit: c434baa76153142256d17c3c51f04d902e29a92e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/10/2021
ms.locfileid: "132179900"
---
# <a name="set-up-a-development-environment-with-azure-databricks-and-automl-in-azure-machine-learning"></a>Azure Machine Learning에서 Azure Databricks 및 AutoML을 사용하여 개발 환경 설정 

Azure Databricks 및 자동화된 ML을 사용하는 Azure Machine Learning에서 개발 환경을 구성하는 방법에 대해 알아봅니다.

Azure Databricks는 Azure 클라우드의 확장 가능한 Apache Spark 플랫폼에서 대규모의 집약적 기계 학습 워크플로를 실행하는 데 적합합니다. CPU 또는 GPU 기반 컴퓨팅 클러스터가 있는 Notebook 기반 공동 작업 환경을 제공합니다.

다른 기계 학습 개발 환경에 대한 자세한 내용은 [Python 개발 환경 설정](how-to-configure-environment.md)을 참조하세요.


## <a name="prerequisite"></a>필수 조건

Azure Machine Learning 작업 영역. 없는 경우 [Azure Portal](how-to-manage-workspace.md), [Azure CLI](how-to-manage-workspace-cli.md#create-a-workspace) 및 [Azure Resource Manager 템플릿](how-to-create-workspace-template.md)을 통해 Azure Machine Learning 작업 영역을 만들 수 있습니다.


## <a name="azure-databricks-with-azure-machine-learning-and-automl"></a>Azure Machine Learning 및 AutoML과 Azure Databricks

Azure Databricks는 Azure Machine Learning 및 해당 AutoML 기능과 통합됩니다. 

다음의 경우 Azure Databricks를 사용할 수 있습니다.

+ Spark MLlib를 사용하여 모델을 학습하고 ACI/AKS에 모델을 배포합니다.
+ Azure ML SDK를 사용하여 [자동화된 기계 학습](concept-automated-ml.md) 기능을 사용합니다.
+ [Azure Machine Learning 파이프라인](concept-ml-pipelines.md)의 컴퓨팅 대상으로 사용합니다.

## <a name="set-up-a-databricks-cluster"></a>Databricks 클러스터 설정

[Databricks 클러스터](/azure/databricks/scenarios/quickstart-create-databricks-workspace-portal)를 만듭니다. 일부 설정은 Databricks에서 자동화된 기계 학습을 위해 SDK를 설치하는 경우에만 적용됩니다.

**클러스터를 만드는 데 몇 분이 걸립니다.**

다음 설정을 사용합니다.

| 설정 |적용 대상| 값 |
|----|---|---|
| 클러스터 이름 |always| yourclustername |
| Databricks Runtime 버전 |always| 런타임 7.3 LTS - ML 않음|
| Python 버전 |always| 3 |
| 작업자 유형 <br>(최대 동시 반복 횟수를 결정합니다.) |자동화된 ML<br>전용| 메모리 최적화 VM 기본 설정 |
| 작업자 |always| 2 이상 |
| 자동 크기 조정 사용 |자동화된 ML<br>전용| 선택 취소 |

계속 진행하기 전에 클러스터가 실행될 때까지 기다립니다.

## <a name="add-the-azure-ml-sdk-to-databricks"></a>Databricks에 Azure ML SDK 추가

클러스터가 실행되면 [라이브러리를 생성](https://docs.databricks.com/user-guide/libraries.html#create-a-library)하여 적절한 Azure Machine Learning SDK 패키지를 클러스터에 연결합니다. 

자동화된 ML을 사용하려면 [AutoML을 사용하여 Azure ML SDK 추가](#add-the-azure-ml-sdk-with-automl-to-databricks)로 건너뜁니다.


1. 라이브러리를 저장하려는 현재 작업 영역 폴더를 마우스 오른쪽 단추로 클릭합니다. **만들기** > **라이브러리** 를 선택합니다.
    
    > [!TIP]
    > 이전 SDK 버전이 있는 경우 클러스터의 설치된 라이브러리에서 선택을 취소하고 휴지통으로 이동합니다. 새 SDK 버전을 설치하고 클러스터를 다시 시작합니다. 다시 시작한 후 문제가 발생하면 클러스터를 분리하고 다시 연결합니다.

1. 다음 옵션을 선택합니다(다른 SDK 설치는 지원되지 않음).

   |SDK 추가 &nbsp;패키지&nbsp;|원본|PyPi&nbsp;이름&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;|
   |----|---|---|
   |Databricks의 경우| Python 업로드 Egg 또는 PyPI | azureml-sdk[databricks]|

   > [!WARNING]
   > 다른 SDK 추가 기능을 설치할 수 없습니다. [`databricks`] 옵션만 선택합니다.

   * **모든 클러스터에 자동으로 연결** 을 선택하지 않습니다.
   * 클러스터 이름 옆에 있는 **연결** 을 선택합니다.

1. 상태가 **연결됨** 으로 변경될 때까지 오류를 모니터링합니다. 몇 분 정도 걸릴 수 있습니다.  이 단계가 실패하는 경우:

   다음을 수행하여 클러스터를 다시 시작합니다.
   1. 왼쪽 창에서 **클러스터** 를 선택합니다.
   1. 테이블에서 클러스터 이름을 선택합니다.
   1. **라이브러리** 탭에서 **다시 시작** 을 선택합니다.

   성공적인 설치는 다음과 같습니다. 

  ![Databricks용 Azure Machine Learning SDK](./media/how-to-configure-environment/amlsdk-withoutautoml.jpg) 

## <a name="add-the-azure-ml-sdk-with-automl-to-databricks"></a>Databricks에 AutoML을 사용하여 Azure ML SDK 추가
Databricks Runtime 7.3 LTS(ML *않음)를* 사용하여 클러스터를 만든 경우 Notebook의 첫 번째 셀에서 다음 명령을 실행하여 AML SDK를 설치합니다.

```
%pip install --upgrade --force-reinstall -r https://aka.ms/automl_linux_requirements.txt
```
Databricks Runtime 7.0 이하의 경우 [초기화 스크립트](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/azure-databricks/automl/README.md)를 사용하여 Azure Machine Learning SDK를 설치합니다.

### <a name="automl-config-settings"></a>AutoML 구성 설정

AutoML 구성에서 Azure Databricks를 사용하는 경우 다음 매개 변수를 추가합니다.

- ```max_concurrent_iterations```은 클러스터의 작업자 노드 수를 기반으로 합니다.
- ```spark_context=sc```는 기본 스파크 컨텍스트를 기반으로 합니다.

## <a name="ml-notebooks-that-work-with-azure-databricks"></a>Azure Databricks에서 작동하는 ML Notebooks

사용해보기:
+ 많은 샘플 Notebook을 사용할 수 있지만 **[이러한 샘플 Notebook](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/azure-databricks)만 Azure Databricks에서 작동합니다.**

+ 작업 영역에서 직접 이러한 샘플을 가져옵니다. 아래 참조: ![가져오기 선택](./media/how-to-configure-environment/azure-db-screenshot.png)
![가져오기 패널](./media/how-to-configure-environment/azure-db-import.png)

+ [학습 컴퓨팅으로 Databricks를 사용하여 파이프라인을 만드는](./how-to-create-machine-learning-pipelines.md) 방법을 알아봅니다.

## <a name="troubleshooting"></a>문제 해결

* **Databricks의 자동화된 기계 학습 실행 취소**: Azure Databricks에서 자동화된 기계 학습 기능을 사용하는 경우 실행을 취소하고 새 실험 실행을 시작하려면 Azure Databricks 클러스터를 다시 시작합니다.

* **Databricks > 자동화된 기계 학습 10회 반복**: 자동화된 기계 학습 설정에서 10개보다 많은 반복이 있는 경우 실행을 제출할 때 `show_output`을 `False`로 설정합니다.

* **Azure Machine Learning SDK 및 자동화된 기계 학습용 Databricks 위젯**: Notebook이 HTML 위젯을 구문 분석할 수 없기 때문에 Azure Machine Learning SDK 위젯은 Databricks Notebook에서 지원되지 않습니다. Azure Databricks Notebook 셀에서 이 Python 코드를 사용하여 포털에서 위젯을 볼 수 있습니다.

    ```
    displayHTML("<a href={} target='_blank'>Azure Portal: {}</a>".format(local_run.get_portal_url(), local_run.id))
    ```

* **패키지 설치 실패**

    추가 패키지가 설치되면 Azure Databricks에서 Azure Machine Learning SDK 설치가 실패합니다. `psutil` 같은 일부 패키지가 충돌을 일으킬 수 있습니다. 라이브러리 버전을 동결하여 패키지를 설치하면 설치 오류를 방지할 수 있습니다. 이 문제는 Azure Machine Learning SDK가 아니라 Databricks와 관련이 있습니다. 다른 라이브러리에서도 이 문제가 발생할 수 있습니다. 예제:
    
    ```python
    psutil cryptography==1.5 pyopenssl==16.0.0 ipython==2.2.0
    ```

    또는 설치 문제가 계속되는 경우 Python 라이브러리를 사용하여 초기화 스크립트를 사용할 수 있습니다. 이 방법은 공식적으로 지원되지 않습니다. 자세한 내용은 [클러스터 범위 초기화 스크립트](/azure/databricks/clusters/init-scripts#cluster-scoped-init-scripts)를 참조하세요.

* **가져오기 오류: `pandas._libs.tslibs`에서 `Timedelta` 이름을 가져올 수 없음**: 자동화된 기계 학습을 사용할 때 이 오류가 표시되면 Notebook에서 다음 두 줄을 실행합니다.
    ```
    %sh rm -rf /databricks/python/lib/python3.7/site-packages/pandas-0.23.4.dist-info /databricks/python/lib/python3.7/site-packages/pandas
    %sh /databricks/python/bin/pip install pandas==0.23.4
    ```

* **가져오기 오류: 'pandas.core.indexes'라는 모듈이 없음**: 자동화된 기계 학습을 사용할 때 이 오류가 표시되는 경우:

    1. Azure Databricks 클러스터에 두 개의 패키지를 설치하려면 이 명령을 실행합니다.
    
       ```bash
       scikit-learn==0.19.1
       pandas==0.22.0
       ```
    
    1. 클러스터를 분리했다가 Notebook에 다시 연결합니다.
    
    이러한 단계를 수행해도 문제가 해결되지 않으면 클러스터를 다시 시작합니다.

* **FailToSendFeather**: Azure Databricks 클러스터에서 데이터를 읽을 때 `FailToSendFeather` 오류가 표시되면 다음 해결 방법을 참조하세요.
    
    * `azureml-sdk[automl]` 패키지를 최신 버전으로 업그레이드합니다.
    * `azureml-dataprep` 버전 1.1.8 이상을 추가합니다.
    * `pyarrow` 버전 0.11 이상을 추가합니다.
  

## <a name="next-steps"></a>다음 단계

- MNIST 데이터 세트를 사용하여 Azure Machine Learning에서 [모델을 학습](tutorial-train-models-with-aml.md)합니다.
- [Python용 Azure Machine Learning SDK](/python/api/overview/azure/ml/intro) 참조를 참조하세요.
