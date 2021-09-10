---
title: Triton을 사용한 고성능 모델 제공(미리 보기)
titleSuffix: Azure Machine Learning
description: Azure Machine Learning에서 NVIDIA Triton Inference Server를 사용하여 모델을 배포하는 방법을 알아봅니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: gopalv
author: gvashishtha
ms.date: 05/17/2021
ms.topic: how-to
ms.reviewer: larryfr
ms.custom: deploy, devx-track-azurecli
ms.openlocfilehash: 559ef637f6deebaad8cc5c57d7503930cde73c08
ms.sourcegitcommit: 47fac4a88c6e23fb2aee8ebb093f15d8b19819ad
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/26/2021
ms.locfileid: "122967384"
---
# <a name="high-performance-serving-with-triton-inference-server-preview"></a>Triton Inference Server를 사용한 고성능 서비스 제공(미리 보기) 

[NVIDIA Triton Inference Server](https://aka.ms/nvidia-triton-docs)를 사용하여 모델 추론에 사용되는 웹 서비스의 성능을 향상하는 방법에 대해 알아봅니다.

추론 모델을 배포하는 방법 중 하나는 웹 서비스입니다. 예를 들어 Azure Kubernetes Service 또는 Azure Container Instances에 대한 배포입니다. 기본적으로 Azure Machine Learning은 웹 서비스 배포에 대한 단일 스레드 범용 웹 프레임워크를 사용합니다.

Triton은 추론에 최적화된 프레임워크입니다. 더 뛰어난 GPU 사용률과 추론 비용 효과를 제공합니다. 서버 쪽에서 들어오는 요청을 일괄 처리하고 추론을 위해 이러한 일괄 처리를 제출합니다. 일괄 처리는 GPU 리소스를 더 효율적으로 활용하며 Triton의 핵심 부분입니다.

> [!IMPORTANT]
> Azure Machine Learning에서 Triton을 사용한 배포는 현재 __미리 보기__ 상태입니다. 미리 보기 기능에는 고객 지원이 적용되지 않을 수 있습니다. 자세한 내용은 [Microsoft Azure 미리 보기에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

> [!TIP]
> 이 문서의 코드 조각은 설명 목적으로 작성되었으며 전체 솔루션을 표시하지 않을 수 있습니다. 작동하는 예제 코드는 [Azure Machine Learning의 엔드투엔드 Triton 샘플](https://aka.ms/triton-aml-sample)을 참조하세요.

> [!NOTE]
> [NVIDIA Triton Inference Server](https://aka.ms/nvidia-triton-docs)는 Azure Machine Learning에 통합된 오픈 소스 타사 소프트웨어입니다.

## <a name="prerequisites"></a>필수 구성 요소

* **Azure 구독**. 구독이 없는 경우[Azure Machine Learning 평가판 또는 유료 버전](https://azure.microsoft.com/free/)을 사용해 보세요.
* Azure Machine Learning을 사용하여 [모델을 배포하는 방법 및 위치](how-to-deploy-and-where.md)에 대한 지식
* [Python용 Azure Machine Learning SDK](/python/api/overview/azure/ml/) **또는** [Azure CLI](/cli/azure/) 및 [기계 학습 확장](reference-azure-machine-learning-cli.md)
* 로컬 테스트를 위한 유효한 Docker 설치. Docker를 설치하고 유효성을 검사하는 방법에 대한 자세한 내용은 Docker 설명서의 [소개 및 설정](https://docs.docker.com/get-started/)을 참조하세요.

## <a name="architectural-overview"></a>아키텍처 개요

자체 모델에 Triton을 사용하기 전에 Azure Machine Learning에서 작동하는 방식과 기본 배포와의 차이점을 이해하는 것이 중요합니다.

**Triton을 사용하지 않는 기본 배포**

* 여러 [Gunicorn](https://gunicorn.org/) 작업자가 들어오는 요청을 동시에 처리하기 시작합니다.
* 이러한 작업자는 전처리, 모델 호출 및 후처리를 처리합니다. 
* 클라이언트가 __Azure ML 채점 URI__ 를 사용합니다. 예들 들어 `https://myservice.azureml.net/score`입니다.

:::image type="content" source="./media/how-to-deploy-with-triton/normal-deploy.png" alt-text="비 Triton 일반 배포 아키텍처 다이어그램":::

**Triton을 사용하여 직접 배포**

* 요청은 Triton 서버로 직접 이동합니다.
* Triton이 일괄 처리로 요청을 처리하여 GPU 사용률을 최대화합니다.
* 클라이언트가 __Triton URI__ 를 사용하여 요청을 수행합니다. 예들 들어 `https://myservice.azureml.net/v2/models/${MODEL_NAME}/versions/${MODEL_VERSION}/infer`입니다.

:::image type="content" source="./media/how-to-deploy-with-triton/triton-deploy.png" alt-text="Triton만 사용하고 Python 미들웨어를 사용하지 않는 추론 구성 배포":::


## <a name="deploying-triton-without-python-pre--and-post-processing"></a>Python 전처리 및 후처리 없이 Triton 배포

먼저 다음 단계에 따라 Triton Inference Server가 모델을 제공할 수 있는지 확인합니다.

### <a name="optional-define-a-model-config-file"></a>(선택 사항) 모델 구성 파일 정의

모델 구성 파일은 Triton에게 예상되는 입력 수와 해당 입력의 차원을 알려 줍니다. 구성 파일을 만드는 방법에 대한 자세한 내용은 NVIDIA 설명서의 [모델 구성](https://aka.ms/nvidia-triton-docs)을 참조하세요.

> [!TIP]
> Triton Inference Server를 시작할 때 `--strict-model-config=false` 옵션을 사용합니다. 그러므로 ONNX 또는 TensorFlow 모델에 `config.pbtxt` 파일을 제공할 필요가 없습니다.
> 
> 이 옵션에 대한 자세한 내용은 NVIDIA 설명서의 [생성된 모델 구성](https://aka.ms/nvidia-triton-docs)을 참조하세요.

### <a name="use-the-correct-directory-structure"></a>올바른 디렉터리 구조 사용

Azure Machine Learning을 사용하여 모델을 등록할 때 개별 파일 또는 디렉터리 구조를 등록할 수 있습니다. Triton을 사용하려면 `triton`이라는 디렉터리를 포함하는 디렉터리 구조에 모델이 등록되어야 합니다. 이 디렉터리의 일반적인 구조는 다음과 같습니다.

```bash
models
    - triton
        - model_1
            - model_version
                - model_file
            - config_file
        - model_2
            ...
```

> [!IMPORTANT]
> 이 디렉터리 구조는 Triton 모델 리포지토리이며 모델이 Triton에서 작동하는 데 필요합니다. 자세한 내용은 NVIDIA 설명서의 [Triton 모델 리포지토리](https://aka.ms/nvidia-triton-docs)를 참조하세요.

### <a name="register-your-triton-model"></a>Triton 모델 등록

# <a name="azure-cli"></a>[Azure CLI](#tab/azcli)

```azurecli-interactive
az ml model register -n my_triton_model -p models --model-framework=Multi
```

`az ml model register`에 대한 자세한 내용은 [참조 설명서](/cli/azure/ml/model)에서 확인하세요.

모델을 Azure Machine Learning에 등록할 때 `--model-path  -p` 매개 변수의 값은 Triton 모델 리포지토리의 부모 폴더 이름이어야 합니다.
위의 예제에서 `--model-path`는 'models'입니다.

`--name  -n` 매개 변수의 값(이 예에서는 `my_triton_models`)은 Azure Machine Learning 작업 영역에 알려져 있는 모델 이름입니다. 

# <a name="python"></a>[Python](#tab/python)


[!notebook-python[] (~/Azureml-examples-main/python-sdk/experimental/deploy-triton/1.bidaf-ncd-local.ipynb?name=register-model)]

자세한 내용은 [모델 클래스](/python/api/azureml-core/azureml.core.model.model)에 대한 설명서를 참조하세요.

---

### <a name="deploy-your-model"></a>모델 배포

# <a name="azure-cli"></a>[Azure CLI](#tab/azcli)

Azure Machine Learning을 통해 생성된 'aks-gpu'라는 GPU 지원 Azure Kubernetes Service 클러스터가 있는 경우 다음 명령을 사용하여 모델을 배포할 수 있습니다.

```azurecli
az ml model deploy -n triton-webservice -m triton_model:1 --dc deploymentconfig.json --compute-target aks-gpu
```

# <a name="python"></a>[Python](#tab/python)

[!notebook-python[] (~/Azureml-examples-main/python-sdk/experimental/deploy-triton/1.bidaf-ncd-local.ipynb?name=deploy-webservice)]

---

모델 배포에 대한 자세한 내용은 [이 설명서](how-to-deploy-and-where.md)를 참조하세요.

[!INCLUDE [endpoints-option](../../includes/machine-learning-endpoints-preview-note.md)]

### <a name="call-into-your-deployed-model"></a>배포된 모델 호출

먼저 채점 URI 및 전달자 토큰을 가져옵니다.

# <a name="azure-cli"></a>[Azure CLI](#tab/azcli)

```azurecli
az ml service show --name=triton-webservice
```
# <a name="python"></a>[Python](#tab/python)

[!notebook-python[] (~/Azureml-examples-main/python-sdk/experimental/deploy-triton/1.bidaf-ncd-local.ipynb?name=get-keys)]

---

다음을 수행하여 서비스가 실행 중인지 확인합니다. 

# <a name="azure-cli"></a>[Azure CLI](#tab/azcli)

```azurecli
```{bash}
!curl -v $scoring_uri/v2/health/ready -H 'Authorization: Bearer '"$service_key"''
```

이 명령은 다음과 같은 정보를 반환합니다. `200 OK`가 표시되는데 이 상태는 웹 서버가 실행 중임을 의미합니다.

```{bash}
*   Trying 127.0.0.1:8000...
* Connected to localhost (127.0.0.1) port 8000 (#0)
> GET /v2/health/ready HTTP/1.1
> Host: localhost:8000
> User-Agent: curl/7.71.1
> Accept: */*
>
* Mark bundle as not supporting multiuse
< HTTP/1.1 200 OK
HTTP/1.1 200 OK
```
# <a name="python"></a>[Python](#tab/python)

[!notebook-python[] (~/Azureml-examples-main/python-sdk/experimental/deploy-triton/1.bidaf-ncd-local.ipynb?name=query-service)]

---


상태 확인을 수행한 후에는 추론을 위해 Triton에 데이터를 전송하는 클라이언트를 만들 수 있습니다. 클라이언트를 만드는 방법에 대한 자세한 내용은 NVIDIA 설명서의 [클라이언트 예제](https://aka.ms/nvidia-client-examples)를 참조하세요. [Triton GitHub에도 Python 샘플](https://aka.ms/nvidia-triton-docs)이 있습니다.

## <a name="clean-up-resources"></a>리소스 정리

Azure Machine Learning 작업 영역을 계속 사용할 계획이지만 배포된 서비스를 제거하려는 경우 다음 옵션 중 하나를 사용합니다.


# <a name="azure-cli"></a>[Azure CLI](#tab/azcli)

```azurecli
az ml service delete -n triton-densenet-onnx
```
# <a name="python"></a>[Python](#tab/python)

[!notebook-python[] (~/Azureml-examples-main/python-sdk/experimental/deploy-triton/1.bidaf-ncd-local.ipynb?name=delete-service)]

---

## <a name="how-to-use-azure-machine-learning-triton-inference-server-container-image"></a>Azure Machine Learning Triton 유추 서버 컨테이너 이미지를 사용하는 방법

새로운 [CLI(v2)](https://docs.microsoft.com/cli/azure/ml?view=azure-cli-latest)를 사용하여 Azure Machine Learning Triton 유추 서버 컨테이너 이미지를 사용하는 방법에 대해 알아봅니다. 아래 예제에서는 [온라인 엔드포인트 및 배포](concept-endpoints.md#what-are-online-endpoints-preview) 개념을 사용합니다. 

1. [단일 Triton 모델을 배포](https://github.com/Azure/azureml-examples/blob/main/cli/deploy-triton-managed-online-endpoint.sh)합니다.
1. [Triton 여러 모델을 배포](https://github.com/Azure/azureml-examples/blob/main/cli/deploy-triton-multiple-models-online-endpoint.sh)합니다.
1. [Triton 앙상블 모델을 배포](https://github.com/Azure/azureml-examples/blob/main/cli/deploy-triton-ensemble-managed-online-endpoint.sh)합니다.
1. [Triton 예제](https://github.com/Azure/azureml-examples/tree/main/cli/endpoints/online/triton)를 체크 아웃하세요.

## <a name="troubleshoot"></a>문제 해결

* [실패한 배포의 문제를 해결](how-to-troubleshoot-deployment.md)하고, 모델을 배포할 때 발생할 수 있는 일반적인 오류를 해결하는 방법을 알아봅니다.

* 배포 로그에 **TritonServer를 시작하지 못함** 이 표시되는 경우 [Nvidia의 오픈 소스 설명서](https://github.com/triton-inference-server/server)를 참조하세요.

## <a name="next-steps"></a>다음 단계

* [Azure Machine Learning의 엔드투엔드 Triton 샘플 확인](https://aka.ms/aml-triton-sample)
* [Triton 클라이언트 예제](https://aka.ms/nvidia-client-examples) 확인
* [Triton Inference Server 설명서](https://aka.ms/nvidia-triton-docs) 읽기
* [Azure Kubernetes Service로 배포](how-to-deploy-azure-kubernetes-service.md)
* [웹 서비스 업데이트](how-to-deploy-update-web-service.md)
* [프로덕션 환경에서 모델용 데이터 수집](how-to-enable-data-collection.md)
