---
title: 관리되는 온라인 엔드포인트에 MLflow 모델 배포(미리 보기)
titleSuffix: Azure Machine Learning
description: MLflow 모델을 Azure에서 자동으로 관리되는 웹 서비스로 배포하는 방법을 알아봅니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: ssambare
author: shivanissambare
ms.date: 11/03/2021
ms.topic: how-to
ms.reviewer: larryfr
ms.custom: deploy, mlflow, devplatv2, no-code-deployment
ms.openlocfilehash: 9e0b4944ee077248b2be2a11fdd4bacbc5af9824
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/05/2021
ms.locfileid: "131842611"
---
# <a name="deploy-mlflow-models-to-managed-online-endpoint-preview"></a>관리되는 온라인 엔드포인트에 MLflow 모델 배포(미리 보기)

이 문서에서는 [관리되는 온라인 엔드포인트(미리](concept-endpoints.md#managed-online-endpoints) 보기)에 [MLflow](https://www.mlflow.org) 모델을 배포하는 방법을 알아봅니다. MLflow 모델을 관리되는 온라인 엔드포인트에 배포하는 경우 코드 없는 배포입니다. 점수 매기기 스크립트 및 환경이 필요하지 않습니다. 

[!INCLUDE [preview disclaimer](../../includes/machine-learning-preview-generic-disclaimer.md)]

## <a name="prerequisites"></a>필수 구성 요소

[!INCLUDE [basic cli prereqs](../../includes/machine-learning-cli-prereqs.md)]

* MLflow 모델이 있어야 합니다. 이 문서의 예제는 의 모델을 기반으로 [https://github.com/Azure/azureml-examples/tree/cli-preview/cli/endpoints/online/mlflow](https://github.com/Azure/azureml-examples/tree/cli-preview/cli/endpoints/online/mlflow) 합니다.

[!INCLUDE [clone repo & set defaults](../../includes/machine-learning-cli-prepare.md)]

이 문서에 사용된 이 코드 조각에서 `ENDPOINT_NAME` 환경 변수에는 만들고 사용할 엔드포인트의 이름이 포함됩니다. 이를 설정하려면 CLI에서 다음 명령을 사용합니다. `<YOUR_ENDPOINT_NAME>`를 엔드포인트의 이름으로 대체합니다.

:::code language="azurecli" source="~/azureml-examples-cli-preview/cli/deploy-managed-online-endpoint-mlflow.sh" ID="set_endpoint_name":::

## <a name="deploy-using-cli-v2"></a>CLI를 사용하여 배포(v2)

이 예제에서는 CLI(v2)를 사용하여 관리되는 온라인 엔드포인트에 MLflow 모델을 배포하는 방법을 보여 드립니다.

> [!IMPORTANT]
> MLflow 코드 없는 배포의 경우 **[로컬 엔드포인트를 통한 테스트는](how-to-deploy-managed-online-endpoints.md#deploy-and-debug-locally-by-using-local-endpoints)** 현재 지원되지 않습니다.

1. 엔드포인트에 대한 YAML 구성 파일을 만듭니다. 다음 예제에서는 엔드포인트의 이름 및 인증 모드를 구성합니다.

    __create-endpoint.yaml__

    :::code language="yaml" source="~/azureml-examples-cli-preview/cli/endpoints/online/mlflow/create-endpoint.yaml":::

1. YAML 구성을 사용하여 새 엔드포인트를 만들려면 다음 명령을 사용합니다.

    :::code language="azurecli" source="~/azureml-examples-cli-preview/cli/deploy-managed-online-endpoint-mlflow.sh" ID="create_endpoint":::

1. 배포에 대한 YAML 구성 파일을 만듭니다. 다음 예제에서는 이전 단계에서 `sklearn-diabetes` 만든 엔드포인트에 모델의 배포를 구성합니다.

    > [!IMPORTANT]
    > MLflow NCD(코드 없는 배포)가 작동하려면 를 **`model_format`** **`mlflow`** 로 설정해야 합니다. 자세한 내용은 [CLI(v2) 모델 YAML 스키마 를 참조하세요.](reference-yaml-model.md)

    __sklearn-deployment.yaml__

    :::code language="yaml" source="~/azureml-examples-cli-preview/cli/endpoints/online/mlflow/sklearn-deployment.yaml":::

1. YAML 구성을 사용하여 배포를 만들려면 다음 명령을 사용합니다.

    :::code language="azurecli" source="~/azureml-examples-cli-preview/cli/deploy-managed-online-endpoint-mlflow.sh" ID="create_sklearn_deployment":::

### <a name="invoke-the-endpoint"></a>엔드포인트 호출

배포가 완료되면 다음 명령을 사용하여 배포된 엔드포인트에 점수 매기기 요청을 수행합니다. 이 명령에 사용되는 [sample-request-sklearn.json](https://github.com/Azure/azureml-examples/blob/cli-preview/cli/endpoints/online/mlflow/sample-request-sklearn.json) 파일은 `/cli/endpoints/online/mlflow` azure-examples 리포지토리의 디렉터리에 있습니다.

:::code language="azurecli" source="~/azureml-examples-cli-preview/cli/deploy-managed-online-endpoint-mlflow.sh" ID="test_sklearn_deployment":::

응답은 다음 텍스트와 유사합니다.

```json
[ 
  11633.100167144921,
  8522.117402884991
]
```

### <a name="delete-endpoint"></a>엔드포인트 삭제

엔드포인트가 완료되면 다음 명령을 사용하여 삭제합니다.

:::code language="azurecli" source="~/azureml-examples-cli-preview/cli/deploy-managed-online-endpoint-mlflow.sh" ID="delete_endpoint":::

## <a name="deploy-using-azure-machine-learning-studio"></a>Azure Machine Learning Studio를 사용하여 배포

이 예제에서는 [Azure Machine Learning Studio를](https://ml.azure.com)사용하여 관리되는 온라인 엔드포인트에 MLflow 모델을 배포하는 방법을 보여 드립니다.

1. 다음 YAML 및 CLI 명령을 사용하여 모델을 MLflow 형식으로 등록합니다. YAML은 의 scikit-learn MLflow 모델을 [https://github.com/Azure/azureml-examples/tree/cli-preview/cli/endpoints/online/mlflow](https://github.com/Azure/azureml-examples/tree/cli-preview/cli/endpoints/online/mlflow) 사용합니다.

    __sample-create-mlflow-model.yaml__

    ```yaml
    $schema: https://azuremlschemas.azureedge.net/latest/model.schema.json
    name: sklearn-diabetes-mlflow
    version: 1
    local_path: sklearn-diabetes/model
    model_format: mlflow
    description: Scikit-learn MLflow model.
    ```


    ```azurecli
    az ml model create -f sample-create-mlflow-model.yaml
    ```

2. [스튜디오에서](https://ml.azure.com)작업 영역을 선택한 __다음, 엔드포인트__ 또는 __모델__ 페이지를 사용하여 엔드포인트 배포를 만듭니다.

    # <a name="endpoints-page"></a>[엔드포인트 페이지](#tab/endpoint)

    1. __엔드포인트__ 페이지에서 **+만들기(미리 보기)** 를 선택합니다.

        :::image type="content" source="media/how-to-deploy-mlflow-models-online-endpoints/create-from-endpoints.png" lightbox="media/how-to-deploy-mlflow-models-online-endpoints/create-from-endpoints.png" alt-text="엔드포인트 UI 페이지의 만들기 옵션을 보여주는 스크린샷.":::

    1. 엔드포인트의 이름 및 인증 유형을 입력한 후 __다음을__ 선택합니다.
    1. 모델을 선택할 때 이전에 등록된 MLflow 모델을 선택합니다. __다음__ 을 선택하여 계속합니다.

    1. MLflow 형식으로 등록된 모델을 선택하면 마법사의 환경 단계에서 채점 스크립트 및 환경이 필요하지 않습니다.

        :::image type="content" source="media/how-to-deploy-mlflow-models-online-endpoints/ncd-wizard.png" lightbox="media/how-to-deploy-mlflow-models-online-endpoints/ncd-wizard.png" alt-text="MLflow 모델에 필요한 코드 및 환경을 보여주는 스크린샷":::

    1. 마법사를 완료하여 모델을 엔드포인트에 배포합니다.

        :::image type="content" source="media/how-to-deploy-mlflow-models-online-endpoints/review-screen-ncd.png" lightbox="media/how-to-deploy-mlflow-models-online-endpoints/review-screen-ncd.png" alt-text="NCD 검토 화면을 보여주는 스크린샷":::

    # <a name="models-page"></a>[모델 페이지](#tab/models)

    1. MLflow 모델을 선택한 다음, __배포를__ 선택합니다. 메시지가 표시되면 __실시간 엔드포인트에 배포(미리 보기)__ 를 선택합니다.

        :::image type="content" source="media/how-to-deploy-mlflow-models-online-endpoints/deploy-from-models-ui.png" lightbox="media/how-to-deploy-mlflow-models-online-endpoints/deploy-from-models-ui.png" alt-text="모델 UI에서 모델을 배포하는 방법을 보여주는 스크린샷":::

    1. 마법사를 완료하여 모델을 엔드포인트에 배포합니다.

    ---

## <a name="deploy-models-after-a-training-job"></a>학습 작업 후 모델 배포

이 섹션은 [학습 작업을](how-to-train-cli.md)완료한 후 관리되는 온라인 엔드포인트에 모델을 배포하는 방법을 이해하는 데 도움이 됩니다.

1. 학습 작업에서 출력을 다운로드합니다. 출력에는 모델 폴더가 포함됩니다.

    > [!NOTE]
    > 학습 스크립트에서 를 사용한 경우 `mlflow.autolog()` 작업 실행 기록에 모델 아티팩트 가 표시됩니다. Azure Machine Learning은 MLflow의 추적 기능과 통합됩니다. `mlflow.autolog()`를 몇 가지 일반적인 ML 프레임워크에 사용하여 모델 매개 변수, 성능 메트릭, 모델 아티팩트 및 기능 중요도 그래프를 로그할 수 있습니다.
    >
    > 자세한 내용은 CLI를 통해 [모델 학습을 참조하세요.](how-to-train-cli.md#model-tracking-with-mlflow) 또한 GitHub 리포지토리에서 [학습 작업 샘플을](https://github.com/Azure/azureml-examples/tree/cli-preview/cli/jobs/single-step) 참조하세요.

    # <a name="azure-machine-learning-studio"></a>[Azure Machine Learning Studio](#tab/studio)

    :::image type="content" source="media/how-to-deploy-mlflow-models-online-endpoints/download-output-logs.png" lightbox="media/how-to-deploy-mlflow-models-online-endpoints/download-output-logs.png" alt-text="실험 실행에서 출력 및 로그를 다운로드하는 방법을 보여주는 스크린샷":::

    # <a name="cli"></a>[CLI](#tab/cli)

    ```azurecli
    az ml job download -n $run_id --outputs
    ```

2. 다운로드한 파일을 사용하여 배포하려면 스튜디오 또는 Azure 명령줄 인터페이스를 사용할 수 있습니다. 배포를 위해 출력의 model 폴더를 사용합니다.

    * [Azure Machine Learning Studio를 사용하여 배포합니다.](how-to-deploy-mlflow-models-online-endpoints.md#deploy-using-azure-machine-learning-studio)
    * [Azure Machine Learning CLI(v2)를 사용하여 배포합니다.](how-to-deploy-mlflow-models-online-endpoints.md#deploy-using-cli-v2)

## <a name="next-steps"></a>다음 단계

자세한 내용은 다음 문서를 검토하세요.

- [REST를 사용하여 모델 배포(미리 보기)](how-to-deploy-with-rest.md)
- [스튜디오에서 관리형 온라인 엔드포인트(미리 보기) 만들기 및 사용](how-to-use-managed-online-endpoint-studio.md)
- [온라인 엔드포인트에 대한 안전한 롤아웃(미리 보기)](how-to-safely-rollout-managed-endpoints.md)
- [관리되는 온라인 엔드포인트의 크기를 자동으로 조정하는 방법](how-to-autoscale-endpoints.md)
- [일괄 처리 채점에 일괄 처리 엔드포인트(미리 보기) 사용](how-to-use-batch-endpoint.md)
- [Azure Machine Learning 관리형 온라인 엔드포인트(미리 보기)에 대한 비용 보기](how-to-view-online-endpoints-costs.md)
- [관리형 온라인 엔드포인트 및 관리 ID(미리 보기)를 통해 Azure 리소스에 액세스](how-to-access-resources-from-endpoints-managed-identities.md)
- [관리형 온라인 엔드포인트 배포 문제 해결](how-to-troubleshoot-managed-online-endpoints.md)