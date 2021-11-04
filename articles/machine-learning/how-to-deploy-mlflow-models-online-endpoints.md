---
title: 관리형 online 끝점 (미리 보기)에 MLflow 모델 배포
titleSuffix: Azure Machine Learning
description: Azure에서 자동으로 관리 되는 웹 서비스로 MLflow 모델을 배포 하는 방법을 알아봅니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: ssambare
author: shivanissambare
ms.date: 11/03/2021
ms.topic: how-to
ms.reviewer: larryfr
ms.custom: deploy, mlflow, devplatv2, no-code-deployment
ms.openlocfilehash: 14f9ccedf7962a6eab56f4c8081c9859ca905e19
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/04/2021
ms.locfileid: "131570018"
---
# <a name="deploy-mlflow-models-to-managed-online-endpoint-preview"></a>관리형 online 끝점 (미리 보기)에 MLflow 모델 배포

이 문서에서는 [관리 되는 온라인 끝점](concept-endpoints.md#managed-online-endpoints) (미리 보기)에 [mlflow](https://www.mlflow.org) 모델을 배포 하는 방법에 대해 알아봅니다. MLflow 모델을 관리 되는 온라인 끝점에 배포 하는 경우 코드를 배포 하지 않습니다. 점수 매기기 스크립트 및 환경이 필요 하지 않습니다. 

[!INCLUDE [preview disclaimer](../../includes/machine-learning-preview-generic-disclaimer.md)]

## <a name="prerequisites"></a>사전 요구 사항

[!INCLUDE [basic cli prereqs](../../includes/machine-learning-cli-prereqs.md)]

* MLflow 모델이 있어야 합니다. 이 문서의 예는의 모델을 기반으로 합니다 [https://github.com/Azure/azureml-examples/cli/endpoints/online/mlflow](https://github.com/Azure/azureml-examples/cli/endpoints/online/mlflow) .

[!INCLUDE [clone repo & set defaults](../../includes/machine-learning-cli-prepare.md)]

이 문서에서 사용 된이 코드 조각에서 `ENDPOINT_NAME` 환경 변수는 만들고 사용할 끝점의 이름을 포함 합니다. 이를 설정 하려면 CLI에서 다음 명령을 사용 합니다. 를 `<YOUR_ENDPOINT_NAME>` 끝점의 이름으로 바꿉니다.

:::code language="azurecli" source="~/azureml-examples-cli-preview/cli/deploy-managed-online-endpoint-mlflow.sh" ID="set_endpoint_name":::

## <a name="deploy-using-cli-v2"></a>CLI를 사용 하 여 배포 (v2)

이 예에서는 CLI (v2)를 사용 하 여 관리 되는 온라인 끝점에 MLflow 모델을 배포할 수 있는 방법을 보여 줍니다.

1. 끝점에 대 한 YAML 구성 파일을 만듭니다. 다음 예제에서는 끝점의 이름 및 인증 모드를 구성 합니다.

    __만들기-끝점. yaml__

    :::code language="yaml" source="~/azureml-examples-cli-preview/cli/endpoints/online/mlflow/create-endpoint.yaml":::

1. YAML 구성을 사용 하 여 새 끝점을 만들려면 다음 명령을 사용 합니다.

    :::code language="azurecli" source="~/azureml-examples-cli-preview/cli/deploy-managed-online-endpoint-mlflow.sh" ID="create_endpoint":::

1. 배포에 대 한 YAML 구성 파일을 만듭니다. 다음 예에서는 `sklearn-diabetes` 이전 단계에서 만든 끝점에 대 한 모델 배포를 구성 합니다.

    > [!IMPORTANT]
    > MLflow 비 코드 배포 (NCD)가 작동 하려면를로 설정 하 **`model_format`** **`mlflow`** 는 것이 필수입니다. 자세한 내용은 [CLI (v2) 모델 YAML 스키마](reference-yaml-model.md)를 참조 하세요.

    __비 기능 학습-배포. yaml__

    :::code language="yaml" source="~/azureml-examples-cli-preview/cli/endpoints/online/mlflow/sklearn-deployment.yaml":::

1. YAML 구성을 사용 하 여 배포를 만들려면 다음 명령을 사용 합니다.

    :::code language="azurecli" source="~/azureml-examples-cli-preview/cli/deploy-managed-online-endpoint-mlflow.sh" ID="create_sklearn_deployment":::

### <a name="invoke-the-endpoint"></a>엔드포인트 호출

배포가 완료 되 면 다음 명령을 사용 하 여 배포 된 끝점에 대 한 점수 매기기 요청을 만듭니다. 이 명령에 사용 되는 [샘플 요청-](https://github.com/Azure/azureml-examples/blob/5e5d9264be15a157dd6635c2fffc341669c8cb31/cli/endpoints/online/mlflow/sample-request-sklearn.json) 파일 스페이스 파일은 `/cli/endpoints/online/mlflow` azure-예제 리포지토리의 디렉터리에 있습니다.

:::code language="azurecli" source="~/azureml-examples-cli-preview/cli/deploy-managed-online-endpoint-mlflow.sh" ID="test_sklearn_deployment":::

응답은 다음 텍스트와 유사 합니다.

```json
[ 
  11633.100167144921,
  8522.117402884991
]
```

### <a name="delete-endpoint"></a>엔드포인트 삭제

끝점을 완료 한 후에는 다음 명령을 사용 하 여 삭제 합니다.

:::code language="azurecli" source="~/azureml-examples-cli-preview/cli/deploy-managed-online-endpoint-mlflow.sh" ID="delete_endpoint":::

## <a name="deploy-using-azure-machine-learning-studio"></a>Azure Machine Learning studio를 사용 하 여 배포

이 예에서는 [Azure Machine Learning studio](https://ml.azure.com)를 사용 하 여 관리 되는 온라인 끝점에 mlflow 모델을 배포할 수 있는 방법을 보여 줍니다.

1. 다음 YAML 및 CLI 명령을 사용 하 여 MLflow 형식으로 모델을 등록 합니다. YAML은에서 scikit MLflow 모델을 사용 [https://github.com/Azure/azureml-examples/cli/endpoints/online/mlflow](https://github.com/Azure/azureml-examples/cli/endpoints/online/mlflow]) 합니다.

    __샘플-mlflow-모델 .yaml__

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

2. [스튜디오](https://ml.azure.com)에서 작업 영역을 선택 하 고 끝점 또는 __모델__ __페이지를 사용__ 하 여 끝점 배포를 만듭니다.

    # <a name="endpoints-page"></a>[엔드포인트 페이지](#tab/endpoint)

    1. __끝점__ 페이지에서 **+ 만들기 (미리 보기)** 를 선택 합니다.

        :::image type="content" source="media/how-to-deploy-mlflow-models-online-endpoints/create-from-endpoints.png" lightbox="media/how-to-deploy-mlflow-models-online-endpoints/create-from-endpoints.png" alt-text="끝점 UI 페이지에서 만들기 옵션을 보여 주는 스크린샷":::

    1. 끝점에 대 한 이름 및 인증 유형을 입력 한 후 __다음__ 을 선택 합니다.
    1. 모델을 선택 하는 경우 이전에 등록 된 MLflow 모델을 선택 합니다. __다음__ 을 선택하여 계속합니다.

    1. MLflow 형식으로 등록 된 모델을 선택 하는 경우 마법사의 환경 단계에서 점수 매기기 스크립트 및 환경이 필요 하지 않습니다.

        :::image type="content" source="media/how-to-deploy-mlflow-models-online-endpoints/ncd-wizard.png" lightbox="media/how-to-deploy-mlflow-models-online-endpoints/ncd-wizard.png" alt-text="MLflow 모델에 필요한 코드 및 환경을 보여 주는 스크린샷":::

    1. 모델을 끝점에 배포 하는 마법사를 완료 합니다.

        :::image type="content" source="media/how-to-deploy-mlflow-models-online-endpoints/review-screen-ncd.png" lightbox="media/how-to-deploy-mlflow-models-online-endpoints/review-screen-ncd.png" alt-text="NCD 검토 화면을 보여 주는 스크린샷":::

    # <a name="models-page"></a>[모델 페이지](#tab/models)

    1. MLflow 모델을 선택한 다음 __배포__ 를 선택 합니다. 메시지가 표시 되 면 __실시간 끝점에 배포 (미리 보기)를__ 선택 합니다.

        :::image type="content" source="media/how-to-deploy-mlflow-models-online-endpoints/deploy-from-models-ui.png" lightbox="media/how-to-deploy-mlflow-models-online-endpoints/deploy-from-models-ui.png" alt-text="모델 UI에서 모델을 배포 하는 방법을 보여 주는 스크린샷":::

    1. 모델을 끝점에 배포 하는 마법사를 완료 합니다.

    ---

## <a name="deploy-models-after-a-training-job"></a>학습 작업 후 모델 배포

이 섹션에서는 [학습 작업](how-to-train-cli.md)을 완료 한 후 관리 되는 온라인 끝점에 모델을 배포 하는 방법을 이해 하는 데 도움을 줍니다.

1. 학습 작업에서 출력을 다운로드 합니다. 출력에는 모델 폴더가 포함 됩니다.

    > [!NOTE]
    > `mlflow.autolog()`학습 스크립트에를 사용한 경우 작업 실행 기록에 모델 아티팩트가 표시 됩니다. Azure Machine Learning은 MLflow의 추적 기능과 통합됩니다. `mlflow.autolog()`를 몇 가지 일반적인 ML 프레임워크에 사용하여 모델 매개 변수, 성능 메트릭, 모델 아티팩트 및 기능 중요도 그래프를 로그할 수 있습니다.
    >
    > 자세한 내용은 [CLI를 사용 하 여 모델 학습](how-to-train-cli.md#model-tracking-with-mlflow)을 참조 하세요. 또한 GitHub 리포지토리에서 [학습 작업 샘플](https://github.com/Azure/azureml-examples/tree/cli-preview/cli/jobs/single-step) 을 참조 하세요.

    # <a name="azure-machine-learning-studio"></a>[Azure Machine Learning Studio](#tab/studio)

    :::image type="content" source="media/how-to-deploy-mlflow-models-online-endpoints/download-output-logs.png" lightbox="media/how-to-deploy-mlflow-models-online-endpoints/download-output-logs.png" alt-text="실험 실행에서 출력과 로그를 다운로드 하는 방법을 보여 주는 스크린샷":::

    # <a name="cli"></a>[CLI](#tab/cli)

    ```azurecli
    az ml job download -n $run_id --outputs
    ```

2. 다운로드 한 파일을 사용 하 여 배포 하려면 스튜디오 또는 Azure 명령줄 인터페이스를 사용할 수 있습니다. 배포용 출력의 model 폴더를 사용 합니다.

    * [Azure Machine Learning studio를 사용 하 여 배포](how-to-deploy-mlflow-models-online-endpoints.md#deploy-using-azure-machine-learning-studio)합니다.
    * [Azure Machine Learning CLI (v2)를 사용 하 여 배포](how-to-deploy-mlflow-models-online-endpoints.md#deploy-using-cli-v2)합니다.

## <a name="next-steps"></a>다음 단계

자세한 내용은 다음 문서를 검토하세요.

- [REST를 사용하여 모델 배포(미리 보기)](how-to-deploy-with-rest.md)
- [스튜디오에서 관리형 온라인 엔드포인트(미리 보기) 만들기 및 사용](how-to-use-managed-online-endpoint-studio.md)
- [온라인 엔드포인트에 대한 안전한 롤아웃(미리 보기)](how-to-safely-rollout-managed-endpoints.md)
- [관리 되는 온라인 끝점을 자동 크기 조정 하는 방법](how-to-autoscale-endpoints.md)
- [일괄 처리 채점에 일괄 처리 엔드포인트(미리 보기) 사용](how-to-use-batch-endpoint.md)
- [Azure Machine Learning 관리형 온라인 엔드포인트(미리 보기)에 대한 비용 보기](how-to-view-online-endpoints-costs.md)
- [관리 되는 온라인 끝점 및 관리 id (미리 보기)를 사용 하 여 Azure 리소스에 액세스](how-to-access-resources-from-endpoints-managed-identities.md)
- [관리형 온라인 엔드포인트 배포 문제 해결](how-to-troubleshoot-managed-online-endpoints.md)