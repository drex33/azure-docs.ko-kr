---
title: Triton을 사용한 고성능 모델 제공(미리 보기)
titleSuffix: Azure Machine Learning
description: Azure Machine Learning에서 NVIDIA Triton Inference Server를 사용하여 모델을 배포하는 방법을 알아봅니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.date: 11/03/2021
ms.topic: how-to
ms.reviewer: larryfr
ms.author: ssambare
author: shivanissambare
ms.custom: deploy, devplatv2
ms.openlocfilehash: d4c65af505725f3f667dd2ad51902717352e4c42
ms.sourcegitcommit: 61f87d27e05547f3c22044c6aa42be8f23673256
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/09/2021
ms.locfileid: "132063668"
---
# <a name="high-performance-serving-with-triton-inference-server-preview"></a>Triton Inference Server를 사용한 고성능 서비스 제공(미리 보기) 

[관리되는 온라인 엔드포인트를](concept-endpoints.md#managed-online-endpoints)사용하여 Azure Machine Learning [NVIDIA Triton Inference Server를](https://aka.ms/nvidia-triton-docs) 사용하는 방법을 알아봅니다.

Triton은 유추에 최적화된 다중 프레임워크 오픈 소스 소프트웨어입니다. TensorFlow, ONNX 런타임, PyTorch, NVIDIA TensorRT 등과 같은 인기 있는 기계 학습 프레임워크를 지원합니다. CPU 또는 GPU 워크로드에 사용할 수 있습니다.

이 문서에서는 관리형 온라인 엔드포인트에 Triton 및 모델을 배포하는 방법을 알아봅니다. CLI(명령줄)와 Azure Machine Learning Studio를 모두 사용하는 경우 정보가 제공됩니다.

[!INCLUDE [preview disclaimer](../../includes/machine-learning-preview-generic-disclaimer.md)]

> [!NOTE]
> [NVIDIA Triton Inference Server](https://aka.ms/nvidia-triton-docs)는 Azure Machine Learning에 통합된 오픈 소스 타사 소프트웨어입니다.

## <a name="prerequisites"></a>필수 구성 요소

[!INCLUDE [basic prereqs](../../includes/machine-learning-cli-prereqs.md)]

* 작동하는 Python 3.8 이상 환경.

* Azure 구독에 대한 NCv3 시리즈 VM에 액세스합니다.

    > [!IMPORTANT]
    > 이 일련의 VM을 사용하려면 구독에 대한 할당량 증가를 요청해야 할 수 있습니다. 자세한 내용은 [NCv3 시리즈](/azure/virtual-machines/ncv3-series)를 참조하세요.

[!INCLUDE [clone repo & set defaults](../../includes/machine-learning-cli-prepare.md)]

NVIDIA Triton Inference Server에는 모델 버전의 각 모델 및 하위 디렉터리에 대한 디렉터리가 있는 특정 모델 리포지토리 구조가 필요합니다. 각 모델 버전 하위Directory의 내용은 모델 유형 및 모델을 지원하는 백 엔드의 요구 사항에 따라 결정됩니다. 모든 모델 리포지토리 구조를 보려면 [https://github.com/triton-inference-server/server/blob/main/docs/model_repository.md#model-files](https://github.com/triton-inference-server/server/blob/main/docs/model_repository.md#model-files)

이 문서의 정보는 ONNX 형식으로 저장된 모델 사용을 기반으로 하므로 모델 리포지토리의 디렉터리 구조는 `<model-repository>/<model-name>/1/model.onnx` 입니다. 특히 이 모델은 이미지 식별을 수행합니다.

## <a name="deploy-using-cli-v2"></a>CLI를 사용하여 배포(v2)

이 섹션에서는 Machine Learning 확장(v2)과 함께 Azure CLI 사용하여 관리되는 온라인 엔드포인트에 Triton을 배포하는 방법을 보여줍니다.

> [!IMPORTANT]
> Triton 코드 없는 배포의 경우 **[로컬 엔드포인트를 통한 테스트는](how-to-deploy-managed-online-endpoints.md#deploy-and-debug-locally-by-using-local-endpoints)** 현재 지원되지 않습니다.

1. 여러 명령의 경로에 입력하지 않으려면 다음 명령을 사용하여 `BASE_PATH` 환경 변수를 설정합니다. 이 변수는 모델 및 연결된 YAML 구성 파일이 있는 디렉터리를 가리킵니다.

    ```azurecli
    BASE_PATH=endpoints/online/triton/single-model
    ```

1. 다음 명령을 사용하여 만들 엔드포인트의 이름을 설정합니다. 이 예제에서는 엔드포인트에 대해 임의의 이름이 만들어집니다.

    :::code language="azurecli" source="~/azureml-examples-main/cli/deploy-triton-managed-online-endpoint.sh" ID="set_endpoint_name":::

1. 다음 명령을 사용하여 Python 요구 사항을 설치합니다.

    ```azurecli
    pip install numpy
    pip install tritonclient[http]
    pip install pillow
    pip install gevent
    ```

1. 엔드포인트에 대한 YAML 구성 파일을 만듭니다. 다음 예제에서는 엔드포인트의 이름 및 인증 모드를 구성합니다. 다음 명령에 사용되는 명령은 `/cli/endpoints/online/triton/single-model/create-managed-endpoint.yml` 이전에 복제한 azureml-examples 리포지점의 에 있습니다.

    __create-managed-endpoint.yaml__

    :::code language="yaml" source="~/azureml-examples-main/cli/endpoints/online/triton/single-model/create-managed-endpoint.yaml":::

1. YAML 구성을 사용하여 새 엔드포인트를 만들려면 다음 명령을 사용합니다.

    :::code language="azurecli" source="~/azureml-examples-main/cli/deploy-triton-managed-online-endpoint.sh" ID="create_endpoint":::

1. 배포에 대한 YAML 구성 파일을 만듭니다. 다음 예제에서는 이전 단계에서 만든 엔드포인트에 __blue라는__ 배포를 구성합니다. 다음 명령에 사용되는 명령은 `/cli/endpoints/online/triton/single-model/create-managed-deployment.yml` 이전에 복제한 azureml-examples 리포지점의 에 있습니다.

    > [!IMPORTANT]
    > Triton NCD(코드 없는 배포)가 작동하려면 를 **`model_format`** 로 **`Triton`** 설정해야 합니다. 자세한 내용은 [CLI(v2) 모델 YAML 스키마](reference-yaml-model.md)를 확인하세요.
    >
    > 이 배포는 Standard_NC6s_v3 VM을 사용합니다. 이 VM을 사용하려면 구독에 대한 할당량 증가를 요청해야 할 수 있습니다. 자세한 내용은 [NCv3 시리즈](/azure/virtual-machines/ncv3-series)를 참조하세요.

    :::code language="yaml" source="~/azureml-examples-main/cli/endpoints/online/triton/single-model/create-managed-deployment.yaml":::

1. YAML 구성을 사용하여 배포를 만들려면 다음 명령을 사용합니다.

    :::code language="azurecli" source="~/azureml-examples-main/cli/deploy-triton-managed-online-endpoint.sh" ID="create_deployment":::

### <a name="invoke-your-endpoint"></a>엔드포인트 호출

배포가 완료되면 다음 명령을 사용하여 배포된 엔드포인트에 점수 매기기 요청을 수행합니다. 

> [!TIP]
> `/cli/endpoints/online/triton/single-model/triton_densenet_scoring.py`azureml-examples 리포지점의 파일은 채점에 사용됩니다. 엔드포인트에 전달된 이미지는 크기, 형식 및 형식 요구 사항을 충족하기 위한 전처리와 예측된 레이블을 표시하는 후처리가 필요합니다. 는 `triton_densenet_scoring.py` 라이브러리를 사용하여 `tritonclient.http` Triton 유추 서버와 통신합니다.

1. 엔드포인트 점수 매기기 URI를 얻으려면 다음 명령을 사용합니다.

    :::code language="azurecli" source="~/azureml-examples-main/cli/deploy-triton-managed-online-endpoint.sh" ID="get_scoring_uri":::

1. 인증 토큰을 얻으려면 다음 명령을 사용합니다.

    :::code language="azurecli" source="~/azureml-examples-main/cli/deploy-triton-managed-online-endpoint.sh" ID="get_token":::

1. 엔드포인트를 사용하여 데이터의 점수를 매기려면 다음 명령을 사용합니다. peacock()의 이미지를 엔드포인트에 제출합니다. https://aka.ms/peacock-pic)

    :::code language="azurecli" source="~/azureml-examples-main/cli/deploy-triton-managed-online-endpoint.sh" ID="check_scoring_of_model":::

    스크립트의 응답은 다음 텍스트와 비슷합니다.

    ```
    Is server ready - True
    Is model ready - True
    /azureml-examples/cli/endpoints/online/triton/single-model/densenet_labels.txt
    84 : PEACOCK
    ```

### <a name="delete-your-endpoint-and-model"></a>엔드포인트 및 모델 삭제

엔드포인트를 완료했으면 다음 명령을 사용하여 삭제합니다.

:::code language="azurecli" source="~/azureml-examples-main/cli/deploy-triton-managed-online-endpoint.sh" ID="delete_endpoint":::

다음 명령을 사용하여 모델을 삭제합니다.

```azurecli
az ml model delete --name $MODEL_NAME --version $MODEL_VERSION
```

## <a name="deploy-using-azure-machine-learning-studio"></a>Azure Machine Learning Studio를 사용하여 배포

이 섹션에서는 [Azure Machine Learning Studio를](https://ml.azure.com)사용하여 관리되는 온라인 엔드포인트에 Triton을 배포하는 방법을 보여줍니다.

1. 다음 YAML 및 CLI 명령을 사용하여 모델을 Triton 형식으로 등록합니다. YAML은 다음의 densenet-onnx 모델을 사용합니다. [https://github.com/Azure/azureml-examples/tree/main/cli/endpoints/online/triton/single-model](https://github.com/Azure/azureml-examples/tree/main/cli/endpoints/online/triton/single-model)

    __create-triton-model.yaml__

    ```yml
    name: densenet-onnx-model
    version: 1
    local_path: ./models
    model_format: Triton
    description: Registering my Triton format model.
    ```

    ```azurecli
    az ml model create -f create-triton-model.yaml
    ```

    다음 스크린샷에서는 등록된 모델이 Azure Machine Learning Studio의 __모델 페이지에서__ 어떻게 표시되는지 보여 줍니다.

    :::image type="content" source="media/how-to-deploy-with-triton/triton-model-format.png" lightbox="media/how-to-deploy-with-triton/triton-model-format.png" alt-text="모델 페이지의 삼각 모델 형식을 보여주는 스크린샷.":::


1. [스튜디오에서](https://ml.azure.com)작업 영역을 선택한 __다음, 엔드포인트__ 또는 __모델__ 페이지를 사용하여 엔드포인트 배포를 만듭니다.

    # <a name="endpoints-page"></a>[엔드포인트 페이지](#tab/endpoint)

    1. __엔드포인트__ 페이지에서 **+만들기(미리 보기)** 를 선택합니다.

        :::image type="content" source="media/how-to-deploy-with-triton/create-option-from-endpoints-page.png" lightbox="media/how-to-deploy-with-triton/create-option-from-endpoints-page.png" alt-text="엔드포인트 UI 페이지의 만들기 옵션을 보여주는 스크린샷.":::

    1. 엔드포인트의 이름 및 인증 유형을 입력한 후 __다음을__ 선택합니다.
    1. 모델을 선택할 때 이전에 등록된 Triton 모델을 선택합니다. __다음__ 을 선택하여 계속합니다.

    1. Triton 형식으로 등록된 모델을 선택하면 마법사의 환경 단계에서 채점 스크립트 및 환경이 필요하지 않습니다.

        :::image type="content" source="media/how-to-deploy-with-triton/ncd-triton.png" lightbox="media/how-to-deploy-with-triton/ncd-triton.png" alt-text="Triton 모델에 필요한 코드 및 환경이 없는 스크린샷":::

    1. 마법사를 완료하여 모델을 엔드포인트에 배포합니다.

        :::image type="content" source="media/how-to-deploy-with-triton/review-screen-triton.png" lightbox="media/how-to-deploy-with-triton/review-screen-triton.png" alt-text="NCD 검토 화면을 보여주는 스크린샷":::

    # <a name="models-page"></a>[모델 페이지](#tab/models)

    1. Triton 모델을 선택한 다음, __배포를__ 선택합니다. 메시지가 표시되면 __실시간 엔드포인트에 배포(미리 보기)__ 를 선택합니다.

        :::image type="content" source="media/how-to-deploy-with-triton/deploy-from-models-page.png" lightbox="media/how-to-deploy-with-triton/deploy-from-models-page.png" alt-text="모델 UI에서 모델을 배포하는 방법을 보여주는 스크린샷":::

    1. 마법사를 완료하여 모델을 엔드포인트에 배포합니다.

    ---

## <a name="next-steps"></a>다음 단계

자세한 내용은 다음 문서를 검토하세요.

- [REST를 사용하여 모델 배포(미리 보기)](how-to-deploy-with-rest.md)
- [스튜디오에서 관리형 온라인 엔드포인트(미리 보기) 만들기 및 사용](how-to-use-managed-online-endpoint-studio.md)
- [온라인 엔드포인트에 대한 안전한 롤아웃(미리 보기)](how-to-safely-rollout-managed-endpoints.md)
- [관리되는 온라인 엔드포인트의 크기를 자동으로 조정하는 방법](how-to-autoscale-endpoints.md)
- [Azure Machine Learning 관리형 온라인 엔드포인트(미리 보기)에 대한 비용 보기](how-to-view-online-endpoints-costs.md)
- [관리형 온라인 엔드포인트 및 관리 ID(미리 보기)를 통해 Azure 리소스에 액세스](how-to-access-resources-from-endpoints-managed-identities.md)
- [관리형 온라인 엔드포인트 배포 문제 해결](how-to-troubleshoot-managed-online-endpoints.md)