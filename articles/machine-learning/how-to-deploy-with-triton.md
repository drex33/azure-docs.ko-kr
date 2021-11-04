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
ms.openlocfilehash: c62a446aace060bf55606462b04b323cb44bf2cb
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/04/2021
ms.locfileid: "131553847"
---
# <a name="high-performance-serving-with-triton-inference-server-preview"></a>Triton Inference Server를 사용한 고성능 서비스 제공(미리 보기) 

Azure Machine Learning에서 [관리 되는 온라인 끝점](concept-endpoints.md#managed-online-endpoints)을 사용 하 여 [NVIDIA Triton 유추 서버](https://aka.ms/nvidia-triton-docs) 를 사용 하는 방법을 알아봅니다.

Triton는 유추를 위해 최적화 된 다중 프레임 워크 오픈 소스 소프트웨어입니다. TensorFlow, ONNX Runtime, PyTorch, NVIDIA TensorRT 등과 같은 인기 있는 기계 학습 프레임 워크를 지원 합니다. CPU 또는 GPU 워크 로드에 사용할 수 있습니다.

이 문서에서는 관리 되는 온라인 끝점에 Triton 및 모델을 배포 하는 방법에 대해 알아봅니다. CLI (명령줄) 및 Azure Machine Learning studio를 사용 하 여에 대 한 정보가 제공 됩니다.

[!INCLUDE [preview disclaimer](../../includes/machine-learning-preview-generic-disclaimer.md)]

> [!NOTE]
> [NVIDIA Triton Inference Server](https://aka.ms/nvidia-triton-docs)는 Azure Machine Learning에 통합된 오픈 소스 타사 소프트웨어입니다.

## <a name="prerequisites"></a>필수 구성 요소

[!INCLUDE [basic prereqs](../../includes/machine-learning-cli-prereqs.md)]

* 작동 하는 Python 3.8 이상 환경

* Azure 구독에 대 한 NCv3 시리즈 Vm에 액세스 합니다.

    > [!IMPORTANT]
    > 이 일련의 Vm을 사용 하려면 구독에 대 한 할당량 증가를 요청 해야 할 수 있습니다. 자세한 내용은 [NCv3 시리즈](/azure/virtual-machines/ncv3-series)를 참조 하세요.

[!INCLUDE [clone repo & set defaults](../../includes/machine-learning-cli-prepare.md)]

NVIDIA Triton 유추 서버에는 특정 모델 리포지토리 구조가 필요 합니다. 여기에는 모델 버전의 각 모델 및 하위 디렉터리에 대 한 디렉터리가 있습니다. 각 모델 버전 하위 디렉터리의 내용은 모델 유형 및 모델을 지 원하는 백 엔드의 요구 사항에 따라 결정 됩니다. 모든 모델 리포지토리 구조를 보려면 [https://github.com/triton-inference-server/server/blob/main/docs/model_repository.md#model-files](https://github.com/triton-inference-server/server/blob/main/docs/model_repository.md#model-files)

이 문서의 정보는 ONNX 형식에 저장 된 모델 사용을 기반으로 하므로 모델 리포지토리의 디렉터리 구조는 `<model-repository>/<model-name>/1/model.onnx` 입니다. 특히이 모델에서는 이미지 id를 수행 합니다.

## <a name="deploy-using-cli-v2"></a>CLI를 사용 하 여 배포 (v2)

이 섹션에서는 Machine Learning 확장 (v2)과 함께 Azure CLI를 사용 하 여 관리 되는 온라인 끝점에 Triton를 배포할 수 있는 방법을 보여 줍니다.

1. 여러 명령의 경로에 입력을 방지 하려면 다음 명령을 사용 하 여 환경 변수를 설정 `BASE_PATH` 합니다. 이 변수는 모델 및 연결 된 YAML 구성 파일이 있는 디렉터리를 가리킵니다.

    ```azurecli
    BASE_PATH=endpoints/online/triton/single-model
    ```

1. 다음 명령을 사용 하 여 생성 될 끝점의 이름을 설정 합니다. 이 예제에서는 끝점에 대해 임의의 이름을 만듭니다.

    :::code language="azurecli" source="~/azureml-examples-cli-preview/cli/deploy-triton-managed-online-endpoint.sh" ID="set_endpoint_name":::

1. 다음 명령을 사용 하 여 Python 요구 사항을 설치 합니다.

    ```azurecli
    pip install numpy
    pip install tritonclient[http]
    pip install pillow
    pip install gevent
    ```

1. 끝점에 대 한 YAML 구성 파일을 만듭니다. 다음 예제에서는 끝점의 이름 및 인증 모드를 구성 합니다. 다음 명령에 사용 되는 항목은 이전에 `/cli/endpoints/online/triton/single-model/create-managed-endpoint.yml` 복제 한 azureml-예제 리포지토리의에 있습니다.

    __만들기-관리 되는 끝점. yaml__

    :::code language="yaml" source="~/azureml-examples-cli-preview/cli/endpoints/online/triton/single-model/create-managed-endpoint.yaml":::

1. YAML 구성을 사용 하 여 새 끝점을 만들려면 다음 명령을 사용 합니다.

    :::code language="azurecli" source="~/azureml-examples-cli-preview/cli/deploy-triton-managed-online-endpoint.sh" ID="create_endpoint":::

1. 배포에 대 한 YAML 구성 파일을 만듭니다. 다음 예제에서는 이전 단계에서 만든 끝점에 __blue__ 라는 배포를 구성 합니다. 다음 명령에 사용 되는 항목은 이전에 `/cli/endpoints/online/triton/single-model/create-managed-deployment.yml` 복제 한 azureml-예제 리포지토리의에 있습니다.

    > [!IMPORTANT]
    > NCD (Triton)가 작동 하려면 **`model_format`** 를로 설정 **`Triton`** 해야 합니다. 자세한 내용은 [CLI (v2) model YAML 스키마를 확인 하세요](reference-yaml-model.md).
    >
    > 이 배포에서는 Standard_NC6s_v3 VM을 사용 합니다. 이 VM을 사용 하려면 먼저 구독에 대 한 할당량 증가를 요청 해야 할 수도 있습니다. 자세한 내용은 [NCv3 시리즈](/azure/virtual-machines/ncv3-series)를 참조 하세요.

    :::code language="yaml" source="~/azureml-examples-cli-preview/cli/endpoints/online/triton/single-model/create-managed-deployment.yaml":::

1. YAML 구성을 사용 하 여 배포를 만들려면 다음 명령을 사용 합니다.

    :::code language="azurecli" source="~/azureml-examples-cli-preview/cli/deploy-triton-managed-online-endpoint.sh" ID="create_deployment":::

### <a name="invoke-your-endpoint"></a>끝점 호출

배포가 완료 되 면 다음 명령을 사용 하 여 배포 된 끝점에 대 한 점수 매기기 요청을 만듭니다. 

> [!TIP]
> Azureml 예 `/cli/endpoints/online/triton/single-model/triton_densenet_scoring.py` 리포지토리의 파일은 점수 매기기에 사용 됩니다. 끝점에 전달 되는 이미지는 크기, 유형 및 형식 요구 사항을 충족 하 고 예측 된 레이블을 표시 하기 위한 사후 처리를 수행 해야 합니다. 는 `triton_densenet_scoring.py` 라이브러리를 사용 하 여 `tritonclient.http` Triton 유추 서버와 통신 합니다.

1. 끝점 점수 매기기 uri를 가져오려면 다음 명령을 사용 합니다.

    :::code language="azurecli" source="~/azureml-examples-cli-preview/cli/deploy-triton-managed-online-endpoint.sh" ID="get_scoring_uri":::

1. 인증 토큰을 가져오려면 다음 명령을 사용 합니다.

    :::code language="azurecli" source="~/azureml-examples-cli-preview/cli/deploy-triton-managed-online-endpoint.sh" ID="get_token":::

1. 끝점을 사용 하 여 데이터의 점수를 매기는 다음 명령을 사용 합니다. 이는 최소라의 이미지를 https://aka.ms/peacock-pic) 끝점에 제출 합니다.

    :::code language="azurecli" source="~/azureml-examples-cli-preview/cli/deploy-triton-managed-online-endpoint.sh" ID="check_scoring_of_model":::

    스크립트의 응답은 다음 텍스트와 유사 합니다.

    ```
    Is server ready - True
    Is model ready - True
    /azureml-examples/cli/endpoints/online/triton/single-model/densenet_labels.txt
    84 : PEACOCK
    ```

### <a name="delete-your-endpoint-and-model"></a>끝점 및 모델 삭제

끝점을 완료 한 후에는 다음 명령을 사용 하 여 삭제 합니다.

:::code language="azurecli" source="~/azureml-examples-cli-preview/cli/deploy-triton-managed-online-endpoint.sh" ID="delete_endpoint":::

다음 명령을 사용 하 여 모델을 삭제 합니다.

```azurecli
az ml model delete --name $MODEL_NAME --version $MODEL_VERSION
```

## <a name="deploy-using-azure-machine-learning-studio"></a>Azure Machine Learning studio를 사용 하 여 배포

이 섹션에서는 [Azure Machine Learning studio](https://ml.azure.com)를 사용 하 여 Triton를 관리 되는 온라인 끝점에 배포할 수 있는 방법을 보여 줍니다.

1. 다음 YAML 및 CLI 명령을 사용 하 여 Triton 형식으로 모델을 등록 합니다. YAML은에서 densenet-onnx 모델을 사용 합니다. [https://github.com/Azure/azureml-examples/tree/main/cli/endpoints/online/triton/single-model](https://github.com/Azure/azureml-examples/tree/main/cli/endpoints/online/triton/single-model)

    __triton-모델 .yaml__

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

    다음 스크린샷은 등록 된 모델이 Azure Machine Learning studio의 __모델 페이지__ 에서 표시 되는 방식을 보여 줍니다.

    :::image type="content" source="media/how-to-deploy-with-triton/triton-model-format.png" lightbox="media/how-to-deploy-with-triton/triton-model-format.png" alt-text="모델 페이지에서 Triton 모델 형식을 보여 주는 스크린샷":::


1. [스튜디오](https://ml.azure.com)에서 작업 영역을 선택 하 고 끝점 또는 __모델__ __페이지를 사용__ 하 여 끝점 배포를 만듭니다.

    # <a name="endpoints-page"></a>[엔드포인트 페이지](#tab/endpoint)

    1. __끝점__ 페이지에서 **+ 만들기 (미리 보기)** 를 선택 합니다.

        :::image type="content" source="media/how-to-deploy-with-triton/create-option-from-endpoints-page.png" lightbox="media/how-to-deploy-with-triton/create-option-from-endpoints-page.png" alt-text="끝점 UI 페이지에서 만들기 옵션을 보여 주는 스크린샷":::

    1. 끝점에 대 한 이름 및 인증 유형을 입력 한 후 __다음__ 을 선택 합니다.
    1. 모델을 선택 하는 경우 이전에 등록 된 Triton 모델을 선택 합니다. __다음__ 을 선택하여 계속합니다.

    1. Triton format에 등록 된 모델을 선택 하는 경우 마법사의 환경 단계에서 점수 매기기 스크립트 및 환경이 필요 하지 않습니다.

        :::image type="content" source="media/how-to-deploy-with-triton/ncd-triton.png" lightbox="media/how-to-deploy-with-triton/ncd-triton.png" alt-text="Triton 모델에 필요한 코드 및 환경을 보여 주는 스크린샷":::

    1. 모델을 끝점에 배포 하는 마법사를 완료 합니다.

        :::image type="content" source="media/how-to-deploy-with-triton/review-screen-triton.png" lightbox="media/how-to-deploy-with-triton/review-screen-triton.png" alt-text="NCD 검토 화면을 보여 주는 스크린샷":::

    # <a name="models-page"></a>[모델 페이지](#tab/models)

    1. Triton 모델을 선택 하 고 __배포__ 를 선택 합니다. 메시지가 표시 되 면 __실시간 끝점에 배포 (미리 보기)를__ 선택 합니다.

        :::image type="content" source="media/how-to-deploy-with-triton/deploy-from-models-page.png" lightbox="media/how-to-deploy-with-triton/deploy-from-models-page.png" alt-text="모델 UI에서 모델을 배포 하는 방법을 보여 주는 스크린샷":::

    1. 모델을 끝점에 배포 하는 마법사를 완료 합니다.

    ---

## <a name="next-steps"></a>다음 단계

자세한 내용은 다음 문서를 검토하세요.

- [REST를 사용하여 모델 배포(미리 보기)](how-to-deploy-with-rest.md)
- [스튜디오에서 관리형 온라인 엔드포인트(미리 보기) 만들기 및 사용](how-to-use-managed-online-endpoint-studio.md)
- [온라인 엔드포인트에 대한 안전한 롤아웃(미리 보기)](how-to-safely-rollout-managed-endpoints.md)
- [관리 되는 온라인 끝점을 자동 크기 조정 하는 방법](how-to-autoscale-endpoints.md)
- [Azure Machine Learning 관리형 온라인 엔드포인트(미리 보기)에 대한 비용 보기](how-to-view-online-endpoints-costs.md)
- [관리 되는 온라인 끝점 및 관리 id (미리 보기)를 사용 하 여 Azure 리소스에 액세스](how-to-access-resources-from-endpoints-managed-identities.md)
- [관리형 온라인 엔드포인트 배포 문제 해결](how-to-troubleshoot-managed-online-endpoints.md)