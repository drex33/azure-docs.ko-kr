---
title: 일괄 처리 채점에 일괄 처리 엔드포인트 사용
titleSuffix: Azure Machine Learning
description: 이 문서에서는 일괄 처리 엔드포인트를 만들어 대량의 데이터를 지속적으로 일괄 처리 채점하는 방법을 알아봅니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: mlops
ms.topic: conceptual
author: tracych
ms.author: tracych
ms.reviewer: laobri
ms.date: 10/21/2021
ms.custom: how-to, devplatv2
ms.openlocfilehash: 966b8abf60e0569a683932824045253ac32c724e
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/04/2021
ms.locfileid: "131560493"
---
# <a name="use-batch-endpoints-preview-for-batch-scoring"></a>일괄 처리 채점에 일괄 처리 엔드포인트(미리 보기) 사용

일괄 처리 엔드포인트(미리 보기)를 사용하여 일괄 처리 채점을 수행하는 방법을 알아봅니다. 일괄 처리 엔드포인트는 일괄 처리 채점을 위해 모델을 호스트하는 프로세스를 간소화하므로 인프라가 아닌 기계 학습에 집중할 수 있습니다. 자세한 내용은 [Azure Machine Learning 엔드포인트(미리 보기)란?](concept-endpoints.md)을 참조하세요.

이 문서에서는 다음 작업을 수행하는 방법을 알아봅니다.

> [!div class="checklist"]
> * 일괄 처리 엔드포인트 및 기본 일괄 처리 배포 만들기
> * Azure CLI를 사용하여 일괄 처리 채점 작업 시작
> * 일괄 처리 채점 작업 실행 진행률 모니터링 및 채점 결과 확인
> * 기존 흐름에 영향을 주지 않고 자동 생성된 코드 및 환경을 사용하여 기존 엔드포인트에 새 MLflow 모델 배포
> * 새 배포를 테스트하고 기본 배포로 설정
> * 사용 중이 아닌 엔드포인트 및 배포 삭제

[!INCLUDE [preview disclaimer](../../includes/machine-learning-preview-generic-disclaimer.md)]

## <a name="prerequisites"></a>사전 요구 사항

* Azure Machine Learning 사용하려면 Azure 구독이 있어야 합니다. Azure 구독이 없는 경우 시작하기 전에 체험 계정을 만듭니다. 지금 [Azure Machine Learning 평가판 또는 유료 버전](https://azure.microsoft.com/free/)을 사용해 보세요.

* Azure CLI 및 `ml` 확장을 설치합니다. [CLI(v2)(미리 보기) 설치, 설정 및 사용의](how-to-configure-cli.md)설치 단계를 따릅니다.

* Azure 리소스 그룹이 없고 사용자(또는 사용하는 서비스 주체)에게 권한이 있어야 하는 경우 Azure 리소스 그룹을 `Contributor` 만듭니다. 리소스 그룹 만들기에 대한 자세한 내용은 [CLI(v2)(미리 보기) 설치, 설정 및 사용을](how-to-configure-cli.md)참조하세요. 

* 아직 없는 경우 Azure Machine Learning 작업 영역을 만듭니다. 작업 영역 만들기는 [CLI(v2)(미리 보기) 설치, 설정 및 사용을](how-to-configure-cli.md)참조하세요. 

* Azure CLI 대한 기본 작업 영역 및 리소스 그룹을 구성합니다. Machine Learning CLI 명령에는 `--workspace/-w` 및 `--resource-group/-g` 매개 변수가 필요합니다. 기본값을 구성하면 값이 여러 번 전달하지 않도록 방지할 수 있습니다. 명령줄에서 이러한 항목을 재정의할 수 있습니다. 다음 코드를 실행하여 기본값을 설정합니다. 자세한 내용은 [CLI(v2)(미리 보기) 설치, 설정 및 사용](how-to-configure-cli.md)을 참조하세요.

```azurecli
az account set -s "<subscription ID>"
az configure --defaults group="<resource group>" workspace="<workspace name>" location="<location>"
```

### <a name="clone-the-example-repository"></a>예제 리포지토리 복제

다음 명령을 실행하여 [AzureML 예제 리포지토리를](https://github.com/Azure/azureml-examples) 복제하고 `cli` 디렉터리로 이동합니다. 이 문서에서는 의 자산을 사용하고 `/cli/endpoints/batch` 종단일 작업 예제는 `/cli/batch-score.sh` 입니다.

```azurecli
git clone https://github.com/Azure/azureml-examples 
cd azureml-examples/cli
```

엔드포인트 이름을 설정합니다. `YOUR_ENDPOINT_NAME`을 Azure 지역 내의 고유한 이름으로 대체합니다.

Unix의 경우 다음 명령을 실행합니다.

:::code language="azurecli" source="~/azureml-examples-cli-preview/cli/batch-score.sh" ID="set_variables" :::

Windows의 경우 다음 명령을 실행합니다.

```azurecli
set ENDPOINT_NAME="<YOUR_ENDPOINT_NAME>"
```

> [!NOTE]
> Batch 엔드포인트 이름은 Azure 지역 내에서 고유해야 합니다. 예를 들어 westus2에는 mybatbatdpoint라는 이름의 일괄 처리 엔드포인트가 하나만 있을 수 있습니다.

### <a name="create-compute"></a>컴퓨팅 만들기

Batch 엔드포인트는 로컬이 아닌 클라우드 컴퓨팅 리소스에서만 실행됩니다. 클라우드 컴퓨팅 리소스는 재사용 가능한 가상 컴퓨터 클러스터입니다. 다음 코드를 실행하여 Azure Machine Learning 컴퓨팅 클러스터를 만듭니다. 이 문서의 다음 예제에서는 여기에서 만든 라는 컴퓨팅을 `batch-cluster` 사용합니다. 필요에 따라 조정하고 를 사용하여 컴퓨팅을 `azureml:<your-compute-name>` 참조합니다.

:::code language="azurecli" source="~/azureml-examples-cli-preview/cli/batch-score.sh" ID="create_compute" :::

> [!NOTE]
> 일괄 처리 엔드포인트가 호출되고 일괄 처리 채점 작업이 제출될 때까지 클러스터가 0개 노드로 유지되기 때문에 이 시점에서 컴퓨팅에 대한 요금이 청구되지 않습니다. [AmlCompute에 대한 비용 관리 및 최적화에](how-to-manage-optimize-cost.md#use-azure-machine-learning-compute-cluster-amlcompute)대해 자세히 알아봅니다.

## <a name="understand-batch-endpoints-and-batch-deployments"></a>일괄 처리 엔드포인트 및 일괄 처리 배포 이해

일괄 처리 엔드포인트는 클라이언트가 일괄 처리 채점 작업을 트리거하기 위해 호출할 수 있는 HTTPS 엔드포인트입니다. 일괄 처리 채점 작업은 여러 입력의 점수를 매기는 작업입니다(자세한 내용은 [일괄 처리 엔드포인트란?).](concept-endpoints.md#what-are-batch-endpoints-preview) 일괄 처리 배포는 실제 일괄 처리 채점을 수행하는 모델을 호스팅하는 컴퓨팅 리소스 집합입니다. 하나의 일괄 처리 엔드포인트에는 여러 일괄 처리 배포가 있을 수 있습니다. 

> [!TIP]
> 일괄 처리 배포 중 하나가 엔드포인트에 대한 기본 배포로 사용됩니다. 기본 배포는 엔드포인트가 호출될 때 실제 일괄 처리 채점을 수행하는 데 사용됩니다. [일괄 처리 엔드포인트 및 일괄 처리 배포에](concept-endpoints.md#what-are-batch-endpoints-preview)대해 자세히 알아봅니다.

다음 YAML 파일은 배치 엔드포인트를 만들기 위해 CLI 명령에 포함할 수 있는 [일괄 처리 엔드포인트를](#create-a-batch-endpoint)정의합니다. 리포지토리에서 이 파일은 에 `/cli/endpoints/batch/batch-endpoint.yml` 있습니다.

:::code language="yaml" source="~/azureml-examples-cli-preview/cli/endpoints/batch/batch-endpoint.yml":::

다음 표에서는 엔드포인트 YAML의 주요 속성에 대해 설명합니다. 전체 일괄 처리 엔드포인트 YAML 스키마는 [CLI(v2) 일괄 처리 엔드포인트 YAML 스키마 를 참조하세요.](./reference-yaml-endpoint-batch.md)

| 키 | 설명 |
| --- | ----------- |
| `$schema` | [선택 사항] YAML 스키마입니다. 위 예제의 스키마를 브라우저에서 보면 일괄 처리 엔드포인트 YAML 파일에서 사용할 수 있는 모든 옵션을 볼 수 있습니다. |
| `name` | 일괄 처리 엔드포인트의 이름입니다. Azure 지역 수준에서 고유해야 합니다.|
| `auth_mode` | 일괄 처리 엔드포인트에 대한 인증 방법입니다. 현재 Azure Active Directory 토큰 기반 `aad_token` 인증()만 지원됩니다. |
| `defaults.deployment_name` | 엔드포인트의 기본 배포로 사용할 배포의 이름입니다. |

일괄 처리 배포를 만들려면 다음 항목이 모두 필요합니다.
* 모델 파일 또는 를 사용하여 참조된 작업 영역의 등록된 `azureml:<model-name>:<model-version>` 모델입니다. 
* 모델의 점수를 매기는 코드입니다.
* 모델이 실행되는 환경입니다. Conda를 사용하는 Docker 이미지이거나 를 사용하여 참조된 작업 영역에 이미 등록된 환경일 수 `azureml:<environment-name>:<environment-version>` 있습니다.
* 및 리소스 설정을 사용하여 참조된 미리 만든 `azureml:<compute-name>` 컴퓨팅입니다.

Azure ML 엔터티를 참조하는 방법에 대한 자세한 내용은 [Azure ML 엔터티 참조를 참조하세요.](reference-yaml-core-syntax.md#referencing-an-azure-ml-entity)

예제 리포지토리에는 필요한 모든 파일이 포함되어 있습니다. 다음 YAML 파일은 모든 필수 입력 및 선택적 설정을 사용하여 일괄 처리 배포를 정의합니다. CLI 명령에 이 파일을 포함시켜 일괄 처리 배포 를 [만들 수 있습니다.](#create-a-batch-deployment) 리포지토리에서 이 파일은 에 `/cli/endpoints/batch/nonmlflow-deployment.yml` 있습니다. 

:::code language="yaml" source="~/azureml-examples-cli-preview/cli/endpoints/batch/nonmlflow-deployment.yml":::

다음 표에서는 배포 YAML의 주요 속성에 대해 설명합니다. 전체 일괄 배포 YAML 스키마는 [CLI(v2) 일괄 처리 배포 YAML 스키마 를 참조하세요.](./reference-yaml-deployment-batch.md)

| 키 | 설명 |
| --- | ----------- |
| `$schema` | [선택 사항] YAML 스키마입니다. 위 예제의 스키마를 브라우저에서 확인하여 배치 배포 YAML 파일에 사용 가능한 모든 옵션을 확인할 수 있습니다. |
| `name` | 배포의 이름입니다. |
| `endpoint_name` | 배포를 만들 엔드포인트의 이름입니다. |
| `model` | 일괄 처리 채점에 사용할 모델입니다. 이 예제에서는 를 사용하여 인라인으로 모델을 `local_path` 정의합니다. 모델 파일은 자동으로 업로드되고 자동 생성되는 이름 및 버전으로 등록됩니다. 자세한 옵션은 [모델 스키마를](reference-yaml-model.md#yaml-syntax) 따릅니다. 프로덕션 시나리오에 대한 모범 사례로 모델을 별도로 만들고 여기에서 참조해야 합니다. 기존 모델을 참조하려면 `azureml:<model-name>:<model-version>` 구문을 사용합니다. |
| `code_configuration.code.local_path` | 모델 점수를 매기는 모든 Python 소스 코드를 포함하는 디렉터리입니다. |
| `code_configuration.scoring_script` | 위의 디렉터리에 있는 Python 파일입니다. 이 파일에는 `init()` 함수와 `run()` 함수가 있어야 합니다. 비용이 `init()` 많이 들거나 일반적인 준비에 함수를 사용합니다(예: 메모리에 모델 로드). `init()` 는 프로세스를 시작할 때 한 번만 호출됩니다. `run(mini_batch)`를 사용하여 각 항목의 점수를 매길 수 있습니다. `mini_batch`의 값은 파일 경로 목록입니다. `run()`함수는 pandas DataFrame 또는 배열을 반환해야 합니다. 반환된 각 요소는 에서 입력 요소를 한 번 성공적으로 실행했음을 `mini_batch` 나타냅니다. 입력과 출력의 상관 관계를 확인하기에 충분한 데이터가 응답에 포함되어 있는지 `run()` 확인합니다. |
| `environment` | 모델을 점수 매기기 위한 환경입니다. 이 예제에서는 및 을 사용하여 환경 인라인을 `conda_file` `image` 정의합니다. `conda_file`에 대한 의존도가 설치됩니다. `image` 환경은 자동으로 생성되는 이름 및 버전으로 등록됩니다. 자세한 옵션은 [환경 스키마를](reference-yaml-environment.md#yaml-syntax) 따릅니다. 프로덕션 시나리오에 대한 모범 사례로, 환경을 별도로 만들고 여기에서 참조해야 합니다. 기존 환경을 참조하려면 `azureml:<environment-name>:<environment-version>` 구문을 사용합니다. |
| `compute` | 일괄 처리 채점을 실행할 컴퓨팅입니다. 이 예제에서는 처음에 만든 를 사용하고 `batch-cluster` 구문을 사용하여 `azureml:<compute-name>` 참조합니다. |
| `resources.instance_count` | 각 일괄 처리 채점 작업에 사용할 인스턴스 수입니다. |
| `max_concurrency_per_instance` | [선택 사항] 인스턴스당 최대 병렬 `scoring_script` 실행 수입니다. |
| `mini_batch_size` | [선택 사항] `scoring_script`에서 한 번의 `run()` 호출로 처리할 수 있는 파일 수입니다. |
| `output_action` | [선택 사항] 출력 파일에서 출력을 구성하는 방법 `append_row`는 반환된 모든 `run()` 출력 결과를 라는 단일 파일에 병합합니다. `output_file_name` `summary_only` 는 출력 결과를 병합하지 않고 만 `error_threshold` 계산합니다. |
| `output_file_name` | [선택 사항] 에 대한 일괄 처리 점수 매기기 출력 파일의 `append_row` `output_action` 이름입니다. |
| `retry_settings.max_retries` | [선택 사항] 실패한 `scoring_script` `run()`에 대한 최대 시도 횟수입니다. |
| `retry_settings.timeout` | [선택 사항] 미니 일괄 처리의 점수를 매기기 위한 에 대한 시간 `scoring_script` 제한(초)입니다. `run()` |
| `error_threshold` | [선택 사항] 무시해야 하는 입력 파일 점수 매기기 실패 횟수입니다. 전체 입력에 대한 오류 수가 이 값을 초과하면 일괄 처리 점수 매기기 작업이 종료됩니다. 이 예제에서는 `-1` 일괄 처리 점수 매기기 작업을 종료하지 않고 허용되는 오류 수를 나타내는 를 사용합니다. | 
| `logging_level` | [선택 사항] 로그의 세부 정보 표시입니다. 세부 정보 표시가 증가하는 값은 WARNING, INFO 및 DEBUG입니다. |

###  <a name="understand-the-scoring-script"></a>채점 스크립트 이해

앞서 언급했듯이 에는 `code_configuration.scoring_script` 다음 두 함수가 포함되어야 합니다.

- `init()`: 비용이 많이 들거나 일반적인 준비에 이 함수를 사용합니다. 예를 들어 모델을 글로벌 개체에 로드하는 데 사용합니다. 이 함수는 프로세스를 시작할 때 한 번만 호출됩니다.
-  `run(mini_batch)`: 이 함수는 각각에 대해 `mini_batch` 호출되고 실제 채점을 수행합니다. 
    -  `mini_batch`: `mini_batch` 값은 파일 경로 목록입니다.
    -  `response`: `run()` 메서드는 pandas 데이터 프레임 또는 배열을 반환해야 합니다. 반환된 각 출력 요소는 입력 에서 입력 요소를 한 번 성공적으로 실행했음을 `mini_batch` 나타냅니다. 입력과 출력 결과의 상관 관계를 지정하는 데 충분한 데이터(예: 각 입력 요소의 식별자)가 응답에 포함되어 있는지 `run()` 확인합니다. 

이 예제에서는 `/cli/endpoints/batch/mnist/code/digit_identification.py`를 사용합니다. 모델은 배포 중에 `init()` `AZUREML_MODEL_DIR` 만든 모델 폴더의 경로인 에서 로드됩니다. `run(mini_batch)` 는 에서 각 파일을 반복하고 `mini_batch` 실제 모델 채점을 수행하고 출력 결과를 반환합니다.

## <a name="deploy-with-batch-endpoints-and-run-batch-scoring"></a>일괄 처리 엔드포인트를 통해 배포 및 일괄 처리 점수 매기기 실행

이제 일괄 처리 엔드포인트를 통해 모델을 배포하고 일괄 처리 채점을 실행해 보겠습니다.

### <a name="create-a-batch-endpoint"></a>일괄 처리 엔드포인트 만들기

일괄 처리 엔드포인트를 만드는 가장 간단한 방법은 만 제공하는 다음 코드를 실행하는 `--name` 것입니다.

:::code language="azurecli" source="~/azureml-examples-cli-preview/cli/batch-score.sh" ID="create_batch_endpoint" :::

YAML 파일을 사용하여 일괄 처리 엔드포인트를 만들 수도 있습니다. `--file`위의 명령에 매개 변수를 추가하고 YAML 파일 경로를 지정합니다.

### <a name="create-a-batch-deployment"></a>일괄 처리 배포 만들기

다음 코드를 실행하여 라는 일괄 처리 배포를 일괄 처리 엔드포인트 아래에 만들고 `nonmlflowdp` 기본 배포로 설정합니다. 

:::code language="azurecli" source="~/azureml-examples-cli-preview/cli/batch-score.sh" ID="create_batch_deployment_set_default" :::

> [!TIP]
> `--set-default`매개 변수는 새로 만든 배포를 엔드포인트의 기본 배포로 설정합니다. 특히 첫 번째 배포를 만들 때 엔드포인트의 새 기본 배포를 만드는 편리한 방법입니다. 프로덕션 시나리오의 모범 사례로, 새 배포를 기본값으로 설정하지 않고 만들고, 확인하고, 나중에 기본 배포를 업데이트하는 것이 좋습니다. 자세한 내용은 새 [모델 배포 섹션을](#deploy-a-new-model) 참조하세요.

### <a name="check-batch-endpoint-and-deployment-details"></a>일괄 처리 엔드포인트 및 배포 세부 정보 확인

`show`를 사용하여 엔드포인트 및 배포 세부 정보를 확인합니다.

일괄 처리 배포를 확인하려면 다음 코드를 실행합니다.

:::code language="azurecli" source="~/azureml-examples-cli-preview/cli/batch-score.sh" ID="check_batch_deployment_detail" :::

일괄 처리 엔드포인트를 확인하려면 다음 코드를 실행합니다. 새로 만든 배포가 기본 배포로 설정되면 `nonmlflowdp` `defaults.deployment_name` 응답에서 가 표시됩니다.

:::code language="azurecli" source="~/azureml-examples-cli-preview/cli/batch-score.sh" ID="check_batch_endpooint_detail" :::

### <a name="invoke-the-batch-endpoint-to-start-a-batch-scoring-job"></a>일괄 처리 엔드포인트를 호출하여 일괄 처리 채점 작업 시작

일괄 처리 엔드포인트 호출은 일괄 처리 채점 작업을 트리거합니다. 작업은 `name` 호출 응답에서 반환되며 일괄 처리 점수 매기기 진행 상황을 추적하는 데 사용할 수 있습니다. 일괄 처리 채점 작업은 일정 기간 동안 실행됩니다. 전체 입력을 여러 개로 `mini_batch` 분할하고 컴퓨팅 클러스터에서 병렬로 처리합니다. `scoring_scrip` `run()` 하나는 하나를 `mini_batch` 가져와서 인스턴스의 프로세스로 처리합니다. 일괄 처리 채점 작업 출력은 클라우드 스토리지, 작업 영역의 기본 Blob Storage 또는 지정한 스토리지에 저장됩니다.

#### <a name="invoke-the-batch-endpoint-with-different-input-options"></a>다른 입력 옵션을 통해 일괄 처리 엔드포인트 호출

CLI 또는 REST를 엔드포인트에 사용할 수 `invoke` 있습니다. REST 환경의 경우 [REST에서 일괄 처리 엔드포인트(미리 보기) 사용을](how-to-deploy-batch-with-rest.md) 참조하세요.

CLI에서 데이터 입력을 지정하는 세 가지 옵션이 `invoke` 있습니다.

* __옵션 1: 클라우드의 데이터__

    `--input-path`Azure Machine Learning 등록된 데이터 저장소에서 폴더(접두사 `folder:` 사용) 또는 파일(접두사 사용)을 지정하는 데 `file:` 사용합니다. 데이터 URI의 구문은 `folder:azureml://datastores/<datastore-name>/paths/<data-path>/` 폴더 및 특정 파일에 대한 구문입니다. `file:azureml://datastores/<datastore-name>/paths/<data-path>/<file-name>` 데이터 URI에 대한 자세한 내용은 [Azure Machine Learning 데이터 참조 URI를 참조하세요.](reference-yaml-core-syntax.md#azure-ml-data-reference-uri)

    이 예제에서는 수천 개의 직접 쓴 숫자를 포함하는 의 폴더에서 공개적으로 사용할 수 있는 데이터를 `https://pipelinedata.blob.core.windows.net/sampledata/mnist` 사용합니다. 일괄 처리 채점 작업의 이름은 호출 응답에서 반환됩니다. 다음 코드를 실행하여 이 데이터를 사용하여 일괄 처리 엔드포인트를 호출합니다. `--query name` 는 호출 응답에서 작업 이름만 반환하도록 추가되며, 나중에 [일괄 처리 점수 매기기 작업 실행 진행률 모니터링](#monitor-batch-scoring-job-execution-progress) 및 일괄 처리 [점수 매기기 결과 확인에](#check-batch-scoring-results)사용됩니다. 전체 호출 응답을 보려면 를 `--query name -o tsv` 제거합니다. 매개 변수에 대한 자세한 내용은 `--query` [쿼리 Azure CLI 명령 출력을 참조하세요.](/cli/azure/query-azure-cli)

    :::code language="azurecli" source="~/azureml-examples-cli-preview/cli/batch-score.sh" ID="start_batch_scoring_job" :::

* __옵션 2: 등록된 데이터 세트__

    Azure Machine Learning 등록된 데이터 세트를 전달하려면 를 `--input-dataset` 사용합니다. 데이터 세트를 만들려면 명령을 확인하고 `az ml dataset create -h` 데이터 [세트 스키마](reference-yaml-dataset.md#yaml-syntax)를 따릅니다.

    > [!NOTE]
    > 이전 버전의 CLI 및 Python SDK를 사용하여 만든 FileDataset도 사용할 수 있습니다. TabularDataset는 지원되지 않습니다. 

    ```azurecli
    az ml batch-endpoint invoke --name $ENDPOINT_NAME --input-dataset azureml:<dataset-name>:<dataset-version>
    ```

* __옵션 3: 로컬로 저장된 데이터__

    를 사용하여 `--input-local-path` 로컬에 저장된 데이터 파일을 전달합니다. 데이터 파일은 자동으로 업로드되고 자동 생성되는 이름 및 버전으로 등록됩니다.

    ```azurecli
    az ml batch-endpoint invoke --name $ENDPOINT_NAME --input-local-path <local-path>
    ```

#### <a name="configure-the-output-location-and-overwrite-settings"></a>출력 위치 구성 및 설정 덮어쓰기

일괄 처리 점수 매기기 결과는 기본적으로 작업 이름(시스템 생성 GUID)으로 명명된 폴더 내에 있는 작업 영역의 기본 Blob 저장소에 저장됩니다. 일괄 처리 엔드포인트를 호출할 때 점수 매기기 출력을 저장할 위치를 구성할 수 있습니다. Azure Machine Learning 등록된 데이터 저장소에서 를 구성하려면 를 `--output-path` `folder:` 사용합니다. 의 `--output-path` `folder:` 구문은 과 `--input-path` `folder:` 동일합니다. `--set output_file_name=<your-file-name>`모든 점수 매기기 결과를 포함하는 하나의 출력 파일(배포 YAML에 지정)을 원하는 경우 를 사용하여 새 출력 파일 이름을 `output_action=append_row` 구성합니다.

> [!IMPORTANT]
> 고유한 출력 위치를 사용해야 합니다. 출력 파일이 있으면 일괄 처리 채점 작업이 실패합니다. 

컴퓨팅 리소스를 최대한 활용하고 성능을 향상시키기 위해 를 호출할 때 일부 설정을 덮어쓸 수 있습니다. 

* 를 사용하여 `--instance-count` 을 덮어쓰는 `instance_count` 데 사용합니다. 예를 들어 대량의 데이터 입력의 경우 더 많은 인스턴스를 사용하여 엔드투엔드 일괄 처리 채점을 가속화할 수 있습니다.
* 를 사용하여 `--mini-batch-size` 을 덮어쓰는 `mini_batch_size` 데 사용합니다. 미니 일괄 처리 수는 총 입력 파일 수 및 mini_batch_size 따라 결정됩니다. mini_batch_size 작을수록 더 많은 미니 일괄 처리가 생성됩니다. 미니 일괄 처리는 병렬로 실행할 수 있지만 추가 예약 및 호출 오버헤드가 있을 수 있습니다. 
* `max_retries`, `timeout` 및 `error_threshold`를 비롯한 다른 설정을 덮어쓰려면 `--set`을 사용합니다. 이러한 설정은 다른 워크로드에 대한 종단간 일괄 처리 점수 매기기 시간에 영향을 미칠 수 있습니다.

를 호출할 때 출력 위치를 지정하고 설정을 덮어쓰려면 다음 코드를 실행합니다. 이 예제에서는 작업 영역의 기본 Blob Storage에서 엔드포인트와 이름이 같은 폴더에 출력을 저장하고 임의 파일 이름을 사용하여 출력 위치의 고유성을 보장합니다. 코드는 Unix에서 작동해야 합니다. 을 고유한 폴더 및 파일 이름으로 대체합니다.

:::code language="azurecli" source="~/azureml-examples-cli-preview/cli/batch-score.sh" ID="start_batch_scoring_job_configure_output_settings" :::

### <a name="monitor-batch-scoring-job-execution-progress"></a>일괄 처리 채점 작업 실행 진행률 모니터링

일괄 처리 채점 작업은 일반적으로 전체 입력 집합을 처리하는 데 다소 시간이 소요됩니다. 

CLI를 사용하여 작업을 볼 수 `job show` 있습니다. 다음 코드를 실행하여 이전 엔드포인트 호출에서 작업 상태를 확인합니다. 작업 명령에 대해 자세히 알아보려면 를 `az ml job -h` 실행합니다.

:::code language="azurecli" source="~/azureml-examples-cli-preview/cli/batch-score.sh" ID="check_job_status" :::

### <a name="check-batch-scoring-results"></a>일괄 처리 채점 결과 확인

아래 단계에 따라 작업이 완료되면 Azure Storage Explorer 점수 매기기 결과를 확인합니다.

1. 다음 코드를 실행하여 Azure Machine Learning Studio에서 일괄 처리 채점 작업을 엽니다. 작업 스튜디오 링크는 의 응답에도 `invoke` 값으로 `interactionEndpoints.Studio.endpoint` 포함됩니다.

    :::code language="azurecli" source="~/azureml-examples-cli-preview/cli/batch-score.sh" ID="show_job_in_studio" :::

1. 실행 그래프에서 `batchscoring` 단계를 선택합니다.
1. 출력 __+ 로그 탭을__ 선택한 **다음, 데이터 출력 표시를 선택합니다.**
1. __데이터 출력에서 아이콘을__ 선택하여 __Storage Explorer__ 엽니다.

:::image type="content" source="media/how-to-use-batch-endpoint/view-data-outputs.png" alt-text="데이터 출력 위치 보기를 보여주는 스튜디오 스크린샷" lightbox="media/how-to-use-batch-endpoint/view-data-outputs.png" :::

Storage Explorer 점수 매기기 결과는 다음 샘플 페이지와 비슷합니다.

:::image type="content" source="media/how-to-use-batch-endpoint/scoring-view.png" alt-text="점수 매기기 출력의 스크린샷" lightbox="media/how-to-use-batch-endpoint/scoring-view.png":::

## <a name="deploy-a-new-model"></a>새 모델 배포

일괄 처리 엔드포인트가 있으면 계속해서 모델을 구체화하고 새 배포를 추가할 수 있습니다.

### <a name="create-a-new-batch-deployment-hosting-an-mlflow-model"></a>MLflow 모델을 호스팅하는 새 일괄 처리 배포 만들기

기존 일괄 처리 엔드포인트에서 새 일괄 처리 배포를 만들되 기본 배포로 설정하지 않려면 다음 코드를 실행합니다.

:::code language="azurecli" source="~/azureml-examples-cli-preview/cli/batch-score.sh" ID="create_new_deployment_not_default" :::

`--set-default`는 사용되지 않습니다. 일괄 `show` 처리 엔드포인트를 다시 사용하면 의 변경이 표시되지 `defaults.deployment_name` 않습니다. 

이 예제에서는 MLflow를 사용하여 학습 및 추적된 모델( `/cli/endpoints/batch/autolog_nyc_taxi` )을 사용합니다. `scoring_script` 및 는 `environment` 모델의 메타데이터를 사용하여 자동으로 생성될 수 있으며 YAML 파일에서 를 지정할 필요가 없습니다. MLflow에 대한 자세한 내용은 [MLflow 및 Azure Machine Learning(미리 보기)를](how-to-use-mlflow.md)통해 ML 모델 학습 및 추적을 참조하세요.

다음은 예제에서 MLflow 모델을 배포하는 데 사용하는 YAML 파일로, 필요한 최소 속성만 포함합니다. 리포지토리의 소스 파일은 `/cli/endpoints/batch/mlflow-deployment.yml` 입니다.

:::code language="yaml" source="~/azureml-examples-cli-preview/cli/endpoints/batch/mlflow-deployment.yml":::

> [!NOTE]
> `scoring_script` 및 `environment` 자동 생성은 Python 함수 모델 버전 및 열 기반 모델 서명만 지원합니다.

### <a name="test-a-non-default-batch-deployment"></a>기본이 아닌 일괄 처리 배포 테스트

기본이 아닌 새 배포를 테스트하려면 다음 코드를 실행합니다. 이 예제에서는 에서 공개적으로 사용할 수 있는 csv 파일을 허용하는 다른 모델을 `https://pipelinedata.blob.core.windows.net/sampledata/nytaxi/taxi-tip-data.csv` 사용합니다.

:::code language="azurecli" source="~/azureml-examples-cli-preview/cli/batch-score.sh" ID="test_new_deployment" :::

`--deployment-name`는 새 배포 이름을 지정하는 데 사용됩니다. 이 매개 변수를 사용하면 기본이 아닌 배포를 사용할 수 `invoke` 있으며 일괄 처리 엔드포인트의 기본 배포는 업데이트되지 않습니다.

### <a name="update-the-default-batch-deployment"></a>기본 일괄 처리 배포 업데이트

엔드포인트의 기본 일괄 처리 배포를 업데이트하려면 다음 코드를 실행합니다.

:::code language="azurecli" source="~/azureml-examples-cli-preview/cli/batch-score.sh" ID="update_default_deployment" :::

이제 일괄 `show` 처리 엔드포인트가 다시 표시되면 `defaults.deployment_name` 가 로 `mlflowdp` 설정됩니다. 매개 `invoke` 변수 없이 직접 일괄 처리 엔드포인트를 만들 수 `--deployment-name` 있습니다.

### <a name="optional-update-the-deployment"></a>(선택 사항) 배포 업데이트

배포(예: 코드, 모델, 환경 또는 설정 업데이트)를 업데이트하려면 YAML 파일을 업데이트한 다음 를 `az ml batch-deployment update` 실행합니다. 를 사용하여 YAML 파일 없이 업데이트할 수도 `--set` 있습니다. `az ml batch-deployment update -h`자세한 내용을 확인합니다.

## <a name="delete-the-batch-endpoint-and-the-deployment"></a>일괄 처리 엔드포인트 및 배포 삭제

이전 일괄 처리 배포를 사용하지 않려면 다음 코드를 실행하여 삭제해야 합니다. `--yes` 는 삭제를 확인하는 데 사용됩니다.

::: code language="azurecli" source="~/azureml-examples-cli-preview/cli/batch-score.sh" ID="delete_deployment" :::

다음 코드를 실행하여 일괄 처리 엔드포인트 및 모든 기본 배포를 삭제합니다. 일괄 처리 채점 작업은 삭제되지 않습니다.

::: code language="azurecli" source="~/azureml-examples-cli-preview/cli/batch-score.sh" ID="delete_endpoint" :::

## <a name="next-steps"></a>다음 단계

* [스튜디오의 Batch 엔드포인트](how-to-use-batch-endpoints-studio.md)
* [일괄 처리 채점을 위한 REST(미리 보기)를 통해 모델 배포](how-to-deploy-batch-with-rest.md)
* [일괄 처리 엔드포인트 문제 해결](how-to-troubleshoot-batch-endpoints.md)
