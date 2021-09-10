---
title: 일괄 처리 채점에 일괄 처리 엔드포인트 사용
titleSuffix: Azure Machine Learning
description: 이 문서에서는 일괄 처리 엔드포인트를 만들어 대량의 데이터를 지속적으로 일괄 처리 채점하는 방법을 알아봅니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
author: tracych
ms.author: tracych
ms.reviewer: laobri
ms.date: 8/11/2021
ms.custom: how-to, devplatv2
ms.openlocfilehash: b68ba3f0221aa97307e746d192de65b2915d2e4b
ms.sourcegitcommit: 34aa13ead8299439af8b3fe4d1f0c89bde61a6db
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/18/2021
ms.locfileid: "122568110"
---
# <a name="use-batch-endpoints-preview-for-batch-scoring"></a>일괄 처리 채점에 일괄 처리 엔드포인트(미리 보기) 사용

이 문서에서는 일괄 처리 엔드포인트(미리 보기)를 사용하여 일괄 처리 채점을 수행하는 방법을 알아봅니다. 일괄 처리 엔드포인트는 일괄 처리 채점을 위해 모델을 호스트하는 프로세스를 간소화하므로 인프라가 아닌 기계 학습에 집중할 수 있습니다. 일괄 처리 엔드포인트를 만든 후 HTTP 라이브러리 및 REST API를 사용하여 Azure CLI 또는 모든 플랫폼에서 일괄 처리 채점 작업을 트리거할 수 있습니다. 자세한 내용은 [Azure Machine Learning 엔드포인트(미리 보기)란?](concept-endpoints.md)을 참조하세요.

이 문서에서는 다음 작업을 수행하는 방법을 알아봅니다.

> [!div class="checklist"]
> * MLflow 모델에 대한 코드 없는 환경을 사용하여 일괄 처리 엔드포인트 만들기
> * 일괄 처리 엔드포인트 세부 정보 확인
> * Azure CLI를 사용하여 일괄 처리 채점 작업 시작
> * 일괄 처리 채점 작업 실행 진행률 모니터링 및 채점 결과 확인
> * 일괄 처리 엔드포인트에 새 배포 추가
> * REST를 사용하여 일괄 처리 채점 작업 시작

[!INCLUDE [preview disclaimer](../../includes/machine-learning-preview-generic-disclaimer.md)]

## <a name="prerequisites"></a>전제 조건

* Azure 구독. Azure 구독이 아직 없는 경우 시작하기 전에 체험 계정을 만듭니다. 지금 [Azure Machine Learning 평가판 또는 유료 버전](https://azure.microsoft.com/free/)을 사용해 보세요.

* Azure CLI(명령줄 인터페이스) 및 ML 앱.

새 Machine Learning 확장에는 Azure CLI 버전 `>=2.15.0`이 필요합니다. 다음 요구 사항이 충족되는지 확인합니다.

```azurecli
az version
```

필요한 경우 Azure CLI를 업그레이드합니다.

```azurecli
az upgrade
```

> [!NOTE]
>
> `az upgrade` 명령은 버전 2.11.0에 추가되었으며 2.11.0 이전 버전에서는 작동하지 않습니다. [Azure CLI 설치](/cli/azure/update-azure-cli)의 설명에 따라 다시 설치하여 이전 버전을 업데이트할 수 있습니다.
>
> 이 명령은 기본적으로 설치된 모든 확장도 업데이트합니다. `az upgrade` 옵션에 대한 자세한 내용은 [명령 참조 페이지](/cli/azure/reference-index#az_upgrade)를 참조하세요.

Azure ML 확장을 추가하고 구성합니다.

```azurecli
az extension add -n ml
```

ML 확장 구성에 대한 자세한 내용은 [CLI(v2) 설치, 설정 및 사용(미리 보기)](how-to-configure-cli.md)을 참조하세요.

* 예제 리포지토리

[AzureML 예제 리포지토리](https://github.com/Azure/azureml-examples)를 복제합니다. 이 문서에서는 `/cli/endpoints/batch`의 자산을 사용합니다.

## <a name="create-a-compute-target"></a>컴퓨팅 대상 만들기

일괄 처리 채점은 로컬이 아닌 클라우드 컴퓨팅 리소스에서만 실행됩니다. 클라우드 컴퓨팅 리소스를 "컴퓨팅 대상"이라고 합니다. 컴퓨팅 대상은 일괄 처리 채점 워크플로를 실행할 수 있는 재사용 가능한 가상 컴퓨터입니다.

다음 코드를 실행하여 범용 [`AmlCompute`](/python/api/azureml-core/azureml.core.compute.amlcompute(class)?view=azure-ml-py&preserve-view=true) 대상을 만듭니다. 컴퓨팅 대상에 대한 자세한 내용은 [Azure Machine Learning에서 컴퓨팅 대상은 무엇인가요?](./concept-compute-target.md)를 참조하세요.

```azurecli
az ml compute create --name cpu-cluster --type amlcompute --min-instances 0 --max-instances 5
```

## <a name="create-a-batch-endpoint"></a>일괄 처리 엔드포인트 만들기

MLflow 모델을 사용하는 경우 코드 없는 일괄 처리 엔드포인트 만들기를 사용할 수 있습니다. 즉, 채점 스크립트 및 환경을 준비할 필요가 없으며 둘 다 자동 생성될 수 있습니다. 자세한 내용은 [MLflow 및 Azure Machine Learning을 통해 ML 모델 학습 및 추적(미리 보기)](how-to-use-mlflow.md)를 참조하세요.

:::code language="azurecli" source="~/azureml-examples-main/cli/batch-score.sh" ID="create_batch_endpoint" :::

다음은 MLFlow 일괄 처리 엔드포인트를 정의하는 YAML 파일입니다.

:::code language="yaml" source="~/azureml-examples-main/cli/endpoints/batch/create-batch-endpoint.yml":::

| 키 | 설명 |
| --- | ----------- |
| $schema | [선택 사항] YAML 스키마입니다. 위 예제의 스키마를 브라우저에서 보면 일괄 처리 엔드포인트 YAML 파일에서 사용할 수 있는 모든 옵션을 볼 수 있습니다. |
| name | 일괄 처리 엔드포인트의 이름으로, 영역 전체에서 고유해야 합니다. `name` 값은 채점 URI의 일부로 사용됩니다. 값은 영어 문자로 시작하고 숫자, 문자 및 `-` 기호를 혼합하여 사용해야 하며 숫자나 문자로 끝나야 합니다. 3자 이상이어야 합니다. 검증 정규식: `^[a-zA-Z][-a-zA-Z0-9]+[a-zA-Z0-9]$`|
| 형식 | 엔드포인트의 형식입니다. 일괄 처리 엔드포인트에 `batch`를 사용합니다. |
| auth_mode | Azure 토큰 기반 인증에 `aad_token`을 사용합니다. |
| traffic | 이 배포로 라우팅되는 트래픽 비율입니다. 일괄 처리 엔드포인트의 경우 `traffic`에 대해 유효한 값은 `0` 또는 `100`뿐입니다. `100` 트래픽 값이 있는 배포가 활성화되어 있습니다. 호출되면 모든 데이터가 활성 배포로 전송됩니다. |
| 배포 | 일괄 처리 엔드포인트에서 만들 배포의 목록입니다. 이 예제에는 `autolog-deployment`라는 이름의 배포 하나만 있습니다. |

배포 특성:

| 키 | 설명 |
| --- | ----------- |
| name | 배포의 이름입니다. |
| model | 일괄 처리 채점에 사용할 모델입니다. `name`, `version` 및 `local_path`를 사용하여 로컬 컴퓨터에서 모델을 업로드합니다. `azureml:` 접두사를 사용하여 작업 영역에서 기존 모델 리소스를 참조합니다. 예를 들어 `azureml: autolog:1`은 이름이 `autolog`인 모델의 버전 1을 가리킵니다. |
| compute.target | 컴퓨팅 대상. `azureml:` 접두사를 사용하여 작업 영역에서 기존 컴퓨팅 리소스를 참조합니다. 예를 들어 `azureml:cpu-cluster`는 이름이 `cpu-cluster`인 컴퓨팅 대상을 가리킵니다. |
| compute.instance_count | 일괄 처리 채점에 사용되는 컴퓨팅 노드의 수입니다. 기본값은 `1`입니다.|
| mini_batch_size | [선택 사항] `scoring_script`에서 한 번의 `run()` 호출로 처리할 수 있는 파일 수입니다. 기본값은 `10`입니다. |
| output_file_name | [선택 사항] 일괄 처리 채점 출력 파일의 이름입니다. 기본값은 `predictions.csv`입니다. |
| retry_settings.max_retries | [선택 사항] 실패한 `scoring_script` `run()`에 대한 최대 시도 횟수입니다. 기본값은 `3`입니다. |
| retry_settings.timeout | [선택 사항] `scoring_script` `run()`에 대한 시간 제한(초)입니다. 기본값은 `30`입니다. |
| error_threshold | [선택 사항] 무시해야 할 파일 실패 횟수입니다. 전체 입력의 오류 횟수가 이 값을 초과하면 작업이 중단됩니다. 오류 임계값은 `run()` 메서드로 전송되는 개별 미니 일괄 처리가 아닌 전체 입력에 적용됩니다. 기본값은 `-1`로, 실행을 종료하지 않고 모든 실패를 허용하도록 지정합니다. | 
| logging_level | [선택 사항] 로그의 세부 정보 표시입니다. 세부 정보 표시가 증가하는 값은 WARNING, INFO 및 DEBUG입니다. 기본값은 INFO입니다. |

## <a name="check-batch-endpoint-details"></a>일괄 처리 엔드포인트 세부 정보 확인

일괄 처리 엔드포인트를 만든 후 `show`를 사용하여 세부 정보를 확인할 수 있습니다. 반환된 데이터에서 특정 특성만 가져오려면 [`--query parameter`](/cli/azure/query-azure-cli)를 사용합니다.

:::code language="azurecli" source="~/azureml-examples-main/cli/batch-score.sh" ID="check_batch_endpooint_detail" :::

## <a name="start-a-batch-scoring-job-using-the-azure-cli"></a>Azure CLI를 사용하여 일괄 처리 채점 작업 시작

일괄 처리 채점 작업은 오프라인 작업으로 실행됩니다. 일괄 처리 채점은 대량 데이터를 처리하도록 설계되었습니다. 입력은 컴퓨팅 클러스터에서 병렬로 처리됩니다. 데이터 파티션은 노드의 프로세스에 할당됩니다. 여러 프로세스가 있는 단일 노드는 여러 파티션을 병렬로 실행합니다. 기본적으로 일괄 처리 채점 출력은 Blob 스토리지에 저장됩니다. 데이터 입력을 전달하면 Azure CLI를 사용하여 일괄 처리 채점 작업을 시작할 수 있습니다. 또한 출력 위치를 구성하고 최상의 성능을 얻기 위해 일부 설정을 덮어쓸 수도 있습니다.

### <a name="start-a-batch-scoring-job-with-different-input-options"></a>다른 입력 옵션을 사용하여 일괄 채점 작업 시작

데이터 입력을 지정하는 세 가지 옵션이 있습니다.

옵션 1: 등록된 데이터

`--input-data`를 사용하여 AML 등록 데이터를 전달합니다.

> [!NOTE]
> 미리 보기 중에는 FileDataset만 지원됩니다. 

```azurecli
az ml endpoint invoke --name mybatchedp --type batch --input-data azureml:<dataName>:<dataVersion>
```

옵션 2: 클라우드의 데이터

`--input-datastore`를 사용하여 AML에 등록 데이터 저장소를 지정하고 `--input-path`를 사용하여 데이터 저장소에서 상대 경로를 지정합니다.

```azurecli
az ml endpoint invoke --name mybatchedp --type batch --input-datastore azureml:<datastoreName> --input-path <relativePath>
```

데이터를 공개적으로 사용할 수 있는 경우 `--input-path`를 사용하여 공용 경로를 지정합니다.

제공된 예제를 사용하는 경우 다음 명령을 실행하여 일괄 처리 채점 작업을 시작할 수 있습니다.

```azurecli
az ml endpoint invoke --name mybatchedp --type batch --input-path https://pipelinedata.blob.core.windows.net/sampledata/nytaxi/taxi-tip-data.csv
```

옵션 3: 로컬로 저장된 데이터

```azurecli
az ml endpoint invoke --name mybatchedp --type batch --input-local-path <localPath>
```

### <a name="configure-the-output-location"></a>출력 위치 구성

일괄 처리 채점 결과는 기본적으로 작업 이름(시스템에서 생성된 GUID)으로 이름이 지정된 폴더 내에 있는 작업 영역의 기본 Blob 저장소에 저장됩니다. 일괄 처리 채점 작업을 시작할 때 채점 출력을 저장할 위치를 구성할 수 있습니다. `--output-datastore`를 사용하여 등록된 데이터 저장소를 구성하고 `--output-path`를 사용하여 상대 경로를 구성합니다. `--set output_file_name`을 사용하여 새 출력 파일 이름을 구성합니다.

> [!IMPORTANT]
> 고유한 출력 위치를 사용해야 합니다. 출력 파일이 있으면 일괄 처리 채점 작업이 실패합니다. 

```azurecli
az ml endpoint invoke --name mybatchedp --type batch --input-path https://pipelinedata.blob.core.windows.net/sampledata/nytaxi/taxi-tip-data.csv --output-datastore azureml:workspaceblobstore --output-path mypath --set output_file_name=mypredictions.csv
```

### <a name="overwrite-settings"></a>설정 덮어쓰기

컴퓨팅 리소스를 최대한 활용하고 성능을 향상시키기 위해 일괄 처리 채점 작업을 시작할 때 일부 설정을 덮어쓸 수 있습니다. 

* 다른 크기의 입력 데이터 사용되는 경우 `--mini-batch-size`를 사용하여 `mini_batch_size`를 덮어씁니다. 
* 이 작업에 다른 컴퓨팅 리소스가 필요한 경우 `--instance-count`를 사용하여 `instance_count`를 덮어씁니다. 
* `max_retries`, `timeout` 및 `error_threshold`를 비롯한 다른 설정을 덮어쓰려면 `--set`을 사용합니다.

```azurecli
az ml endpoint invoke --name mybatchedp --type batch --input-path https://pipelinedata.blob.core.windows.net/sampledata/nytaxi/taxi-tip-data.csv --set retry_settings.max_retries=1
```
:::code language="azurecli" source="~/azureml-examples-main/cli/batch-score.sh" ID="start_batch_scoring_job_with_new_settings" :::

## <a name="check-batch-scoring-job-execution-progress"></a>일괄 처리 채점 작업 실행 진행률 확인

일괄 처리 채점 작업은 일반적으로 전체 입력 집합을 처리하는 데 다소 시간이 소요됩니다. Azure Machine Learning 스튜디오에서 작업 진행 상황을 모니터링할 수 있습니다. 스튜디오 링크는 `invoke`의 응답에 `interactionEndpoints.Studio.endpoint`의 값으로 제공됩니다.

Azure CLI를 사용하여 상태와 함께 작업 세부 정보를 확인할 수도 있습니다.

호출 응답에서 작업 이름을 가져옵니다.

:::code language="azurecli" source="~/azureml-examples-main/cli/batch-score.sh" ID="start_batch_scoring_job" :::

일괄 처리 채점 작업의 세부 정보 및 상태를 확인하려면 `job show`를 사용합니다.

:::code language="azurecli" source="~/azureml-examples-main/cli/batch-score.sh" ID="check_job_status" :::

`job stream`을 사용하여 작업 로그를 스트리밍합니다.

:::code language="azurecli" source="~/azureml-examples-main/cli/batch-score.sh" ID="stream_job_logs_to_console" :::


## <a name="check-batch-scoring-results"></a>일괄 처리 채점 결과 확인

채점 결과를 보려면

1. 스튜디오로 이동합니다.
1. **실험** 으로 이동합니다.
1. 엔드포인트의 실행(`endpoint invoke`에 대한 응답의 `interactionEndpoints.Studio.endpoint` 값)으로 이동합니다.
1. 실행 그래프에서 `batchscoring`단계 내부를 클릭합니다.
1. 출력 + 로그 탭을 선택하고 **데이터 출력 표시** 를 선택합니다. 
1. **출력 보기** 아이콘 선택 :::image type="content" source="media/how-to-use-batch-endpoint/view-data-outputs.png" alt-text="보기 데이터 출력 위치를 보여주는 스튜디오 스크린샷" lightbox="media/how-to-use-batch-endpoint/view-data-outputs.png" :::
1. 팝업 패널에서 경로를 복사하고 "데이터 저장소 열기" 링크를 선택합니다.
1. 결과 Blobstore 페이지에서 위의 경로를 검색 상자에 붙여넣습니다. 폴더에서 채점 출력을 찾을 수 있습니다.
:::image type="content" source="media/how-to-use-batch-endpoint/scoring-view.gif" alt-text="점수 폴더 및 채점 출력을 여는 스크린샷" lightbox="media/how-to-use-batch-endpoint/scoring-view.gif":::

## <a name="add-a-deployment-to-the-batch-endpoint"></a>일괄 처리 엔드포인트에 배포 추가

하나의 일괄 처리 엔드포인트에 여러 배포가 있을 수 있습니다. 각 배포는 일괄 처리 채점을 위한 하나의 모델을 호스트합니다. 

### <a name="add-a-new-deployment"></a>새 배포 추가

다음 명령을 사용하여 기존 일괄 처리 엔드포인트에 새 배포를 추가합니다.

:::code language="azurecli" source="~/azureml-examples-main/cli/batch-score.sh" range="65" :::

이 샘플에서는 비 MLflow 모델을 사용합니다. 비 MLflow를 사용하는 경우 YAML 파일에서 환경 및 채점 스크립트를 지정해야 합니다.

:::code language="yaml" source="~/azureml-examples-main/cli/endpoints/batch/add-deployment.yml" :::

비 MLflow 모델에 대한 추가 배포 특성:

| 키 | 설명 |
| --- | ----------- |
| code_configuration.code.local_path | 모델을 채점하는 데 필요한 모든 소스 코드가 포함된 디렉터리입니다. |
| code_configuration.scoring_script | 위의 디렉터리에 있는 Python 파일입니다. 이 파일에는 `init()` 함수와 `run()` 함수가 있어야 합니다. 비용이 많이 드는 준비 또는 일반적인 준비에 `init()` 함수를 사용합니다. 예를 들어 모델을 글로벌 개체에 로드하는 데 사용합니다. 이 함수는 프로세스를 시작할 때 한 번만 호출됩니다. `run(mini_batch)`를 사용하여 각 항목의 점수를 매길 수 있습니다. `mini_batch`의 값은 파일 경로 목록입니다. `run()` 메서드는 pandas `DataFrame` 또는 배열을 반환해야 합니다. 이러한 반환된 요소는 공통 출력 파일에 추가됩니다. 반환되는 각 출력 요소는 입력 미니 일괄 처리의 입력 요소에 대한 성공적인 실행 하나를 나타냅니다. 입력을 특정 실행 출력 결과에 매핑하기에 충분한 데이터가 실행 결과에 포함되어 있는지 확인합니다. 실행 출력은 출력 파일에 기록되지만 순서대로 기록된다는 보장은 없으므로, 사용자는 출력의 키를 사용하여 올바른 입력에 매핑해야 합니다. |
| environment | 컴퓨팅 대상에서 모델을 채점하기 위한 환경입니다. 이름, 버전 및 경로를 지정하여 환경을 인라인으로 정의할 수 있습니다. `docker.image` 위에 설치할 의존성을 포함하려면 `conda_file`을 사용합니다. 기존 환경을 참조하려면 `azureml:` 접두사를 사용합니다. 예를 들어 `azureml: mnist-env:1`은 `mnist-env`라는 환경의 버전 1을 가리킵니다. |

배포 세부 정보를 검토하려면 다음을 실행합니다.

:::code language="azurecli" source="~/azureml-examples-main/cli/batch-score.sh" ID="check_batch_endpooint_detail" :::

### <a name="activate-the-new-deployment"></a>새 배포를 활성화합니다.

일괄 처리 유추의 경우 원하는 배포에 문의를 100% 보내야 합니다. 새로 만든 배포를 대상으로 설정하려면 다음을 사용합니다.

:::code language="azurecli" source="~/azureml-examples-main/cli/batch-score.sh" ID="switch_traffic" :::

배포 세부 정보를 다시 검사하면 변경 내용이 표시됩니다.

:::code language="azurecli" source="~/azureml-examples-main/cli/batch-score.sh" ID="check_batch_endpooint_detail" :::

이제 이 새 배포를 통해 일괄 처리 채점 작업을 호출할 수 있습니다.

:::code language="azurecli" source="~/azureml-examples-main/cli/batch-score.sh" ID="start_batch_scoring_job_with_new_settings" :::

## <a name="start-a-batch-scoring-job-using-rest"></a>REST를 사용하여 일괄 처리 채점 작업 시작

일괄 처리 엔드포인트에는 REST 액세스를 위한 채점 URI가 있습니다. REST를 사용하면 모든 플랫폼에서 HTTP 라이브러리를 사용하여 일괄 처리 채점 작업을 시작할 수 있습니다.

1. `scoring_uri`를 가져옵니다.  

:::code language="azurecli" source="~/azureml-examples-main/cli/batch-score.sh" ID="get_scoring_uri" :::

2. 액세스 토큰을 가져옵니다.

:::code language="azurecli" source="~/azureml-examples-main/cli/batch-score.sh" ID="get_token" :::


3. `scoring_uri`, 액세스 토큰 및 JSON 데이터를 사용하여 요청을 게시하고 일괄 처리 채점 작업을 시작합니다.

:::code language="azurecli" source="~/azureml-examples-main/cli/batch-score.sh" ID="start_batch_scoring_job_rest":::


## <a name="clean-up-resources"></a>리소스 정리

자신이 만든 리소스를 사용하지 않으려는 경우 요금이 발생하지 않도록 삭제하세요.

1. Azure Portal의 왼쪽 메뉴에서 **리소스 그룹** 을 선택합니다.
1. 리소스 그룹 목록에서 만든 리소스 그룹을 선택합니다.
1. **리소스 그룹 삭제** 를 선택합니다.
1. 리소스 그룹 이름을 입력합니다. 그런 다음, **삭제** 를 선택합니다.

또한 리소스 그룹을 유지하면서 단일 작업 영역을 삭제할 수도 있습니다. 작업 영역 속성을 표시한 다음, **삭제** 를 선택합니다.

## <a name="next-steps"></a>다음 단계

이 문서에서는 많은 양의 데이터를 채점할 수 있도록 배치 엔드포인트를 만들고 호출하는 방법을 배웠습니다. Azure Machine Learning에 대한 자세한 내용은 다음 문서를 참조하세요.

* [일괄 처리 엔드포인트 문제 해결](how-to-troubleshoot-batch-endpoints.md)
* [관리형 온라인 엔드포인트(미리 보기)를 통해 기계 학습 모델 배포 및 채점](how-to-deploy-managed-online-endpoints.md)
