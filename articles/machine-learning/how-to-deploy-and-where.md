---
title: 기계 학습 모델 배포 방법
titleSuffix: Azure Machine Learning
description: 기계 학습 모델을 배포하는 방법과 위치에 대해 알아봅니다. Azure Container Instances, Azure Kubernetes Service 및 FPGA에 배포합니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.reviewer: larryfr
ms.date: 04/21/2021
ms.topic: how-to
ms.custom: devx-track-python, deploy, devx-track-azurecli, contperf-fy21q2, contperf-fy21q4
adobe-target: true
ms.openlocfilehash: b5514df10228a9e5638712976a0602fc33c8a990
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128604363"
---
# <a name="deploy-machine-learning-models-to-azure"></a>Azure에 기계 학습 모델 배포 

Azure 클라우드의 웹 서비스로 기계 학습 또는 딥 러닝 모델을 배포하는 방법을 알아봅니다.

> [!TIP]
> 관리형 온라인 엔드포인트(미리 보기)는 기본 인프라를 만들고 관리할 필요 없이 학습된 모델을 배포하는 방법을 제공합니다. 자세한 내용은 [관리형 온라인 엔드포인트(미리 보기)를 통해 기계 학습 모델 배포 및 채점](how-to-deploy-managed-online-endpoints.md)을 참조하세요.

워크플로는 모델을 배포하는 위치와 관계없이 유사합니다.

1. 모델 등록
1. 항목 스크립트 준비
1. 유추 구성 준비
1. 모델을 로컬로 배포하여 모든 것이 작동하는지 확인
1. 컴퓨팅 대상 선택
1. 클라우드에 모델 다시 배포
1. 결과 웹 서비스 테스트

기계 학습 배포 워크플로와 관련한 개념에 대한 자세한 내용은 [Azure Machine Learning을 사용한 모델 관리, 배포 및 모니터링](concept-model-management-and-deployment.md)을 참조하세요.

[!INCLUDE [endpoints-option](../../includes/machine-learning-endpoints-preview-note.md)]

## <a name="prerequisites"></a>필수 구성 요소

# <a name="azure-cli"></a>[Azure CLI](#tab/azcli)

- Azure Machine Learning 작업 영역 자세한 내용은 [Azure Machine Learning 작업 영역 만들기](how-to-manage-workspace.md)를 참조하세요.
- 모델. 학습된 모델이 없는 경우 [이 자습서](https://aka.ms/azml-deploy-cloud)에서 제공하는 모델 및 종속성 파일을 사용할 수 있습니다.
- [Machine Learning Service에 대한 Azure CLI(명령줄 인터페이스) 확장](reference-azure-machine-learning-cli.md)
- [컴퓨팅 인스턴스](how-to-create-manage-compute-instance.md)와 같이 Docker를 실행할 수 있는 머신입니다.

# <a name="python"></a>[Python](#tab/python)

- Azure Machine Learning 작업 영역 자세한 내용은 [Azure Machine Learning 작업 영역 만들기](how-to-manage-workspace.md)를 참조하세요.
- 모델. 학습된 모델이 없는 경우 [이 자습서](https://aka.ms/azml-deploy-cloud)에서 제공하는 모델 및 종속성 파일을 사용할 수 있습니다.
- [Python용 Azure Machine Learning SDK(소프트웨어 개발 키트)](/python/api/overview/azure/ml/intro)
- [컴퓨팅 인스턴스](how-to-create-manage-compute-instance.md)와 같이 Docker를 실행할 수 있는 머신입니다.
---

## <a name="connect-to-your-workspace"></a>작업 영역에 연결

# <a name="azure-cli"></a>[Azure CLI](#tab/azcli)

수행

```azurecli-interactive
az login
az account set -s <my subscription>
az ml workspace list --resource-group=<my resource group>
```

액세스 권한이 있는 작업 영역을 봅니다.

# <a name="python"></a>[Python](#tab/python)

```python
from azureml.core import Workspace
ws = Workspace(subscription_id="<subscription_id>",
               resource_group="<resource_group>",
               workspace_name="<workspace_name>")
```

SDK를 사용하여 작업 영역에 연결하는 방법에 대한 자세한 내용은 [Python용 Azure Machine Learning SDK](/python/api/overview/azure/ml/intro#workspace)를 참조하세요.


---

## <a name="register-your-model"></a><a id="registermodel"></a> 모델 등록

배포된 기계 학습 서비스의 일반적인 상황은 다음 구성 요소가 필요하다는 것입니다.
    
 + 배포하려는 특정 모델을 나타내는 리소스(예: pytorch 모델 파일)
 + 서비스에서 실행될 코드로, 지정된 입력에 대해 모델을 실행합니다.

Azure Machine Learning을 사용하면 배포를 두 개의 개별 구성 요소로 구분하여 동일한 코드를 유지하면서도 모델을 업데이트할 수 있습니다. 코드와 _별도로_ 모델을 업로드하는 메커니즘을 "모델 등록"으로 정의합니다.

모델을 등록할 때 모델을 클라우드(작업 영역의 기본 스토리지 계정)에 업로드한 다음, 웹 서비스가 실행 중인 동일한 컴퓨팅에 탑재합니다.

다음 예제에서는 모델을 등록하는 방법을 보여줍니다.

[!INCLUDE [trusted models](../../includes/machine-learning-service-trusted-model.md)]

# <a name="azure-cli"></a>[Azure CLI](#tab/azcli)

### <a name="register-a-model-from-a-local-file"></a>로컬 파일에서 모델 등록

[!notebook-python[] (~/azureml-examples-main/python-sdk/tutorials/deploy-local/2.deploy-local-cli.ipynb?name=register-model-from-local-file-code)]

`-p`를 등록하려는 폴더 또는 파일의 경로로 설정합니다.

`az ml model register`에 대한 자세한 내용은 [참조 설명서](/cli/azure/ext/azure-cli-ml/ml/model)에서 확인하세요.

### <a name="register-a-model-from-an-azure-ml-training-run"></a>Azure ML 학습 실행에서 모델 등록

```azurecli-interactive
az ml model register -n bidaf_onnx --asset-path outputs/model.onnx --experiment-name myexperiment --run-id myrunid --tag area=qna
```

[!INCLUDE [install extension](../../includes/machine-learning-service-install-extension.md)]

`--asset-path` 매개 변수는 모델의 클라우드 위치를 의미합니다. 이 예제에서는 단일 파일의 경로를 사용합니다. 모델 등록에 여러 파일을 포함하려면 `--asset-path`를 파일이 담긴 폴더의 경로로 설정합니다.

`az ml model register`에 대한 자세한 내용은 [참조 설명서](/cli/azure/ml/model)에서 확인하세요.

# <a name="python"></a>[Python](#tab/python)

### <a name="register-a-model-from-a-local-file"></a>로컬 파일에서 모델 등록

모델의 로컬 경로를 제공하여 모델을 등록할 수 있습니다. 로컬 머신에서 폴더 또는 단일 파일의 경로를 제공할 수 있습니다.
<!-- pyhton nb call -->
[!notebook-python[] (~/azureml-examples-main/python-sdk/tutorials/deploy-local/1.deploy-local.ipynb?name=register-model-from-local-file-code)]


모델 등록에 여러 파일을 포함하려면 `model_path`를 파일이 담긴 폴더의 경로로 설정합니다.

자세한 내용은 [모델 클래스](/python/api/azureml-core/azureml.core.model.model)에 대한 설명서를 참조하세요.


### <a name="register-a-model-from-an-azure-ml-training-run"></a>Azure ML 학습 실행에서 모델 등록

  SDK를 사용한 모델 학습에서, 모델의 학습 방법에 따라 [Run](/python/api/azureml-core/azureml.core.run.run) 개체 또는 [AutoMLRun](/python/api/azureml-train-automl-client/azureml.train.automl.run.automlrun) 개체를 받을 수 있습니다. 각 개체를 사용하여 실험 실행에서 만든 모델을 등록할 수 있습니다.

  + `azureml.core.Run` 개체에서 모델을 등록합니다.
 
    ```python
    model = run.register_model(model_name='bidaf_onnx',
                               tags={'area': 'qna'},
                               model_path='outputs/model.onnx')
    print(model.name, model.id, model.version, sep='\t')
    ```

    `model_path` 매개 변수는 모델의 클라우드 위치를 의미합니다. 이 예제에서는 단일 파일의 경로를 사용합니다. 모델 등록에 여러 파일을 포함하려면 `model_path`를 파일이 담긴 폴더의 경로로 설정합니다. 자세한 내용은 [Run.register_model](/python/api/azureml-core/azureml.core.run.run#register-model-model-name--model-path-none--tags-none--properties-none--model-framework-none--model-framework-version-none--description-none--datasets-none--sample-input-dataset-none--sample-output-dataset-none--resource-configuration-none----kwargs-) 설명서를 참조하세요.

  + `azureml.train.automl.run.AutoMLRun` 개체에서 모델을 등록합니다.

    ```python
        description = 'My AutoML Model'
        model = run.register_model(description = description,
                                   tags={'area': 'qna'})

        print(run.model_id)
    ```

    이 예제에서는 `metric` 및 `iteration` 매개 변수를 지정하지 않으므로 최적의 기본 메트릭을 통한 반복이 등록됩니다. 실행에서 반환된 `model_id` 값을 모델 이름 대신 사용합니다.

    자세한 내용은 [AutoMLRun.register_model](/python/api/azureml-train-automl-client/azureml.train.automl.run.automlrun#register-model-model-name-none--description-none--tags-none--iteration-none--metric-none-) 설명서를 참조하세요.

    `AutoMLRun`에서 등록된 모델을 배포하려면 [Azure Machine Learning 스튜디오에서 한 번 클릭 배포 단추](how-to-use-automated-ml-for-ml-models.md#deploy-your-model)를 통해 그렇게 하는 것이 좋습니다. 

---

## <a name="define-a-dummy-entry-script"></a>더미 항목 스크립트 정의

[!INCLUDE [write entry script](../../includes/machine-learning-dummy-entry-script.md)]


## <a name="define-an-inference-configuration"></a>유추 구성 정의

유추 구성은 웹 서비스를 초기화할 때 사용할 Docker 컨테이너 및 파일을 설명합니다. 웹 서비스를 배포할 때 하위 디렉터리를 포함하여 원본 디렉터리 내의 모든 파일이 압축되어 클라우드에 업로드됩니다.

아래 유추 구성에서는 기계 학습 배포가 `./source_dir` 디렉터리의 `echo_score.py` 파일을 사용하여 들어오는 요청을 처리하고, `project_environment` 환경에서 지정한 Python 패키지에 Docker 이미지를 사용합니다.

프로젝트 환경을 만들 때 [Azure Machine Learning 추론 큐레이트 환경](concept-prebuilt-docker-images-inference.md#list-of-prebuilt-docker-images-for-inference)을 기본 Docker 이미지로 사용할 수 있습니다. 위에서 필요한 종속성을 설치하고 결과 Docker 이미지를 작업 영역과 연결된 리포지토리에 저장합니다.

> [!NOTE]
> Azure 기계 학습 [추론 원본 디렉터리](/python/api/azureml-core/azureml.core.model.inferenceconfig?view=azure-ml-py#constructor&preserve-view=true) 업로드는 **.gitignore** 또는 **.amlignore** 를 준수하지 않습니다.

# <a name="azure-cli"></a>[Azure CLI](#tab/azcli)

최소 유추 구성은 다음과 같이 작성할 수 있습니다.

:::code language="json" source="~/azureml-examples-main/python-sdk/tutorials/deploy-local/dummyinferenceconfig.json":::

이 파일을 `dummyinferenceconfig.json`이라는 이름으로 저장합니다.


유추 구성에 대한 자세한 내용은 [이 문서를 참조](./reference-azure-machine-learning-cli.md#inference-configuration-schema)하세요. 

# <a name="python"></a>[Python](#tab/python)

다음 예제에서는 위에서 정의한 더미 채점 스크립트를 사용하여 pip 종속성이 없는 최소 환경을 만드는 방법을 보여 줍니다.

[!notebook-python[] (~/azureml-examples-main/python-sdk/tutorials/deploy-local/1.deploy-local.ipynb?name=inference-configuration-code)]

환경에 대한 자세한 내용은 [학습 및 배포 환경 만들기 및 관리](how-to-use-environments.md)를 참조하세요.

유추 구성에 대한 자세한 내용은 [InferenceConfig](/python/api/azureml-core/azureml.core.model.inferenceconfig) 클래스 설명서를 참조하세요.

---


## <a name="define-a-deployment-configuration"></a>배포 구성 정의

배포 구성은 기본 웹 서비스의 구성 세부 정보뿐만 아니라 실행하는 데 필요한 웹 서비스에 대해 예약할 메모리 및 코어의 양을 지정합니다. 예를 들어 배포 구성을 사용하여 서비스에 2기가바이트의 메모리, 2개의 CPU 코어, 1개의 GPU 코어가 필요하며 자동 크기 조정을 사용하도록 지정할 수 있습니다.

배포 구성에 사용할 수 있는 옵션은 선택한 컴퓨팅 대상에 따라 다릅니다. 로컬 배포에서는 웹 서비스가 제공되는 포트를 지정할 수 있습니다.

# <a name="azure-cli"></a>[Azure CLI](#tab/azcli)

[!INCLUDE [aml-local-deploy-config](../../includes/machine-learning-service-local-deploy-config.md)]

자세한 내용은 [이 참조](./reference-azure-machine-learning-cli.md#deployment-configuration-schema)에서 확인하세요.

# <a name="python"></a>[Python](#tab/python)

로컬 배포 구성을 만들려면 다음을 수행합니다.

[!notebook-python[] (~/azureml-examples-main/python-sdk/tutorials/deploy-local/1.deploy-local.ipynb?name=deployment-configuration-code)]

---

## <a name="deploy-your-machine-learning-model"></a>기계 학습 모델 배포

이제 모델을 배포할 준비가 되었습니다. 

[!INCLUDE [aml-deploy-service](../../includes/machine-learning-deploy-service.md)]


## <a name="call-into-your-model"></a>모델에 대한 호출

에코 모델이 성공적으로 배포되었는지 확인해 보겠습니다. 간단한 활동성 요청뿐만 아니라 채점 요청을 수행할 수 있어야 합니다.

# <a name="azure-cli"></a>[Azure CLI](#tab/azcli)
<!-- cli nb call -->

[!notebook-python[] (~/azureml-examples-main/python-sdk/tutorials/deploy-local/2.deploy-local-cli.ipynb?name=call-into-model-code)]

# <a name="python"></a>[Python](#tab/python)
<!-- python nb call -->
[!notebook-python[] (~/azureml-examples-main/python-sdk/tutorials/deploy-local/1.deploy-local.ipynb?name=call-into-model-code)]

---

## <a name="define-an-entry-script"></a>항목 스크립트 정의

이제 모델을 실제로 로드할 차례입니다. 먼저 항목 스크립트를 수정합니다.


:::code language="python" source="~/azureml-examples-main/python-sdk/tutorials/deploy-local/source_dir/score.py":::

이 파일을 `source_dir` 내부에 `score.py`로 저장합니다.

`AZUREML_MODEL_DIR` 환경 변수를 사용하여 등록된 모델을 찾습니다. 이제 일부 pip 패키지를 추가했습니다.

# <a name="azure-cli"></a>[Azure CLI](#tab/azcli)

:::code language="json" source="~/azureml-examples-main/python-sdk/tutorials/deploy-local/inferenceconfig.json":::

이 파일을 `inferenceconfig.json`으로 저장합니다. 

# <a name="python"></a>[Python](#tab/python)

```python
env = Environment(name='myenv')
python_packages = ['nltk', 'numpy', 'onnxruntime']
for package in python_packages:
    env.python.conda_dependencies.add_pip_package(package)

inf_config = InferenceConfig(environment=env, source_directory='./source_dir', entry_script='./score.py')
```

자세한 내용은 [LocalWebservice](/python/api/azureml-core/azureml.core.webservice.local.localwebservice), [Model.deploy()](/python/api/azureml-core/azureml.core.model.model#deploy-workspace--name--models--inference-config-none--deployment-config-none--deployment-target-none--overwrite-false-) 및 [Webservice](/python/api/azureml-core/azureml.core.webservice.webservice)에 대한 설명서를 참조하세요.

---
Deploy your service again: null
---

# <a name="azure-cli"></a>[Azure CLI](#tab/azcli)

`bidaf_onnx:1`을 모델 이름 및 해당 버전 번호로 바꿉니다.

[!notebook-python[] (~/azureml-examples-main/python-sdk/tutorials/deploy-local/2.deploy-local-cli.ipynb?name=re-deploy-model-code)]

# <a name="python"></a>[Python](#tab/python)

[!notebook-python[] (~/azureml-examples-main/python-sdk/tutorials/deploy-local/1.deploy-local.ipynb?name=re-deploy-model-code)]

[!notebook-python[] (~/azureml-examples-main/python-sdk/tutorials/deploy-local/1.deploy-local.ipynb?name=re-deploy-model-print-logs)]

자세한 내용은 [Model.deploy()](/python/api/azureml-core/azureml.core.model.model#deploy-workspace--name--models--inference-config-none--deployment-config-none--deployment-target-none--overwrite-false-) 및 [Webservice](/python/api/azureml-core/azureml.core.webservice.webservice)에 대한 설명서를 참조하세요.

---
그런 다음, 서비스에 게시 요청을 보낼 수 있는지 확인합니다.

# <a name="azure-cli"></a>[Azure CLI](#tab/azcli)

[!notebook-python[] (~/azureml-examples-main/python-sdk/tutorials/deploy-local/2.deploy-local-cli.ipynb?name=send-post-request-code)]

# <a name="python"></a>[Python](#tab/python)

[!notebook-python[] (~/azureml-examples-main/python-sdk/tutorials/deploy-local/1.deploy-local.ipynb?name=send-post-request-code)]

---

## <a name="choose-a-compute-target"></a>컴퓨팅 대상 선택

컴퓨팅 대상을 선택할 때 아래 다이어그램을 참조하세요.

[![컴퓨팅 대상을 선택하는 방법](./media/how-to-deploy-and-where/how-to-choose-target.png)](././media/how-to-deploy-and-where/how-to-choose-target.png#lightbox)

[!INCLUDE [aml-deploy-target](../../includes/aml-compute-target-deploy.md)]

## <a name="re-deploy-to-cloud"></a>클라우드에 다시 배포

서비스가 로컬로 작동하는지 확인하고 원격 컴퓨팅 대상을 선택하면 클라우드에 배포할 준비가 된 것입니다. 

선택한 컴퓨팅 대상에 해당하도록 배포 구성을 변경합니다. 이 경우 Azure Container Instances입니다.

# <a name="azure-cli"></a>[Azure CLI](#tab/azcli)

배포 구성에 사용할 수 있는 옵션은 선택한 컴퓨팅 대상에 따라 다릅니다.

:::code language="json" source="~/azureml-examples-main/python-sdk/tutorials/deploy-local/re-deploymentconfig.json":::

이 파일을 `re-deploymentconfig.json`로 저장합니다.

자세한 내용은 [이 참조](./reference-azure-machine-learning-cli.md#deployment-configuration-schema)에서 확인하세요.

# <a name="python"></a>[Python](#tab/python)

[!notebook-python[] (~/azureml-examples-main/python-sdk/tutorials/deploy-local/1.deploy-local.ipynb?name=deploy-model-on-cloud-code)]

---

서비스를 다시 배포합니다.


# <a name="azure-cli"></a>[Azure CLI](#tab/azcli)

`bidaf_onnx:1`을 모델 이름 및 해당 버전 번호로 바꿉니다.



[!notebook-python[] (~/azureml-examples-main/python-sdk/tutorials/deploy-local/2.deploy-local-cli.ipynb?name=deploy-model-on-cloud-code)]

# <a name="python"></a>[Python](#tab/python)


[!notebook-python[] (~/azureml-examples-main/python-sdk/tutorials/deploy-local/1.deploy-local.ipynb?name=re-deploy-service-code)]

[!notebook-python[] (~/azureml-examples-main/python-sdk/tutorials/deploy-local/1.deploy-local.ipynb?name=re-deploy-service-print-logs)]

자세한 내용은 [Model.deploy()](/python/api/azureml-core/azureml.core.model.model#deploy-workspace--name--models--inference-config-none--deployment-config-none--deployment-target-none--overwrite-false-) 및 [Webservice](/python/api/azureml-core/azureml.core.webservice.webservice)에 대한 설명서를 참조하세요.

---


## <a name="call-your-remote-webservice"></a>원격 웹 서비스 호출

원격으로 배포하는 경우 키 인증을 사용하도록 설정했을 수 있습니다. 아래 예제에서는 유추 요청을 만들기 위해 Python을 사용하여 서비스 키를 얻는 방법을 보여 줍니다.

[!notebook-python[] (~/azureml-examples-main/python-sdk/tutorials/deploy-local/1.deploy-local.ipynb?name=call-remote-web-service-code)]

[!notebook-python[] (~/azureml-examples-main/python-sdk/tutorials/deploy-local/1.deploy-local.ipynb?name=call-remote-webservice-print-logs)]



다른 언어로 된 더 많은 예제 클라이언트는 [웹 서비스를 사용하는 클라이언트 애플리케이션](how-to-consume-web-service.md)에 대한 문서를 참조하세요.

### <a name="understanding-service-state"></a>서비스 상태 해석

모델이 배포되는 과정에서 완전히 배포되면 서비스 상태가 변하는 것을 확인할 수 있습니다.

다음 표에서는 이러한 여러 서비스 상태에 대해 설명합니다.

| 웹 서비스 상태 | 설명 | 최종 상태?
| ----- | ----- | ----- |
| 전환 중 | 서비스가 배포 진행 중입니다. | 아니요 |
| 비정상 | 서비스가 배포되었지만 현재 연결할 수 없습니다.  | 아니요 |
| 예약되지 않음 | 리소스가 부족하여 지금은 서비스를 배포할 수 없습니다. | 아니요 |
| 실패 | 오류 또는 충돌 때문에 서비스를 배포하지 못했습니다. | 예 |
| 정상 | 서비스가 정상 상태이며 엔드포인트를 사용할 수 있습니다. | 예 |

> [!TIP]
> 배포할 때는 컴퓨팅 대상에 대한 Docker 이미지를 ACR(Azure Container Registry)로부터 빌드하고 로드합니다. 기본적으로 Azure Machine Learning은 *기본* 서비스 계층을 사용하는 ACR을 만듭니다. 작업 영역에 대한 ACR을 표준 또는 프리미엄 계층으로 변경하면 이미지를 빌드하고 컴퓨팅 대상에 로드하는 시간을 줄일 수 있습니다. 자세한 내용은 [Azure Container Registry 서비스 계층](../container-registry/container-registry-skus.md)을 참조하세요.

> [!NOTE]
> AKS(Azure Kubernetes Service)에 모델을 배포하는 경우 해당 클러스터에 대해 [Azure Monitor](../azure-monitor/containers/container-insights-enable-existing-clusters.md)를 사용하도록 설정하는 것이 좋습니다. 이렇게 하면 전반적인 클러스터 상태와 리소스 사용을 해석할 수 있습니다. 다음 리소스도 유용할 수 있습니다.
>
> * [AKS 클러스터에 영향을 주는 Resource Health 이벤트 확인](../aks/aks-resource-health.md)
> * [Azure Kubernetes Service 진단](../aks/concepts-diagnostics.md)
>
> 비정상 또는 오버로드 상태인 클러스터에 모델 배포를 시도하면 문제가 발생하게 됩니다. AKS 클러스터 문제 해결에 도움이 필요한 경우 AKS 고객 지원팀에 문의하세요.

## <a name="delete-resources"></a>리소스 삭제

# <a name="azure-cli"></a>[Azure CLI](#tab/azcli)


[!notebook-python[] (~/azureml-examples-main/python-sdk/tutorials/deploy-local/2.deploy-local-cli.ipynb?name=delete-resource-code)]

[!notebook-python[] (~/azureml-examples-main/python-sdk/tutorials/deploy-local/2.deploy-local-cli.ipynb?name=delete-your-resource-code)]

배포된 웹 서비스를 삭제하려면 `az ml service delete <name of webservice>`를 사용합니다.

작업 영역에서 등록된 모델을 삭제하려면 `az ml model delete <model id>`를 사용합니다.

[웹 서비스 삭제](/cli/azure/ml(v1)/computetarget/create#az_ml_service_delete) 및 [모델 삭제](/cli/azure/ml/model#az_ml_model_delete)에 대해 자세히 알아보세요.

# <a name="python"></a>[Python](#tab/python)

[!notebook-python[] (~/azureml-examples-main/python-sdk/tutorials/deploy-local/1.deploy-local.ipynb?name=delete-resource-code)]

배포된 웹 서비스를 삭제하려면 `service.delete()`를 사용합니다.
등록된 모델을 삭제하려면 `model.delete()`를 사용합니다.

자세한 내용은 [WebService.delete()](/python/api/azureml-core/azureml.core.webservice%28class%29#delete--) 및 [Model.delete()](/python/api/azureml-core/azureml.core.model.model#delete--)에 대한 설명서를 참조하세요.

---

## <a name="next-steps"></a>다음 단계

* [실패한 배포 문제 해결](how-to-troubleshoot-deployment.md)
* [웹 서비스 업데이트](how-to-deploy-update-web-service.md)
* [Azure Machine Learning 스튜디오에서 자동화된 ML 실행에 대한 한 번 클릭 배포](how-to-use-automated-ml-for-ml-models.md#deploy-your-model)
* [TLS를 사용하여 Azure Machine Learning을 통해 웹 서비스 보호](how-to-secure-web-service.md)
* [Application Insights를 사용하여 Azure Machine Learning 모델 모니터링](how-to-enable-app-insights.md)
* [모델 배포에 대한 이벤트 경고 및 트리거 만들기](how-to-use-event-grid.md)
