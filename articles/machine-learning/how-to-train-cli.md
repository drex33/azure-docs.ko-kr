---
title: CLI를 통해 모델 학습(v2)
titleSuffix: Azure Machine Learning
description: Machine Learning에 대한 Azure CLI 확장을 사용한 모델 학습(작업 만들기) 방법을 알아봅니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
author: amibp
ms.author: amipatel
ms.date: 10/21/2021
ms.reviewer: laobri
ms.custom: devx-track-azurecli, devplatv2
ms.openlocfilehash: b849293d94b435fa74378d80d8989e6075f78d86
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/15/2021
ms.locfileid: "132493669"
---
# <a name="train-models-with-the-cli-v2-preview"></a>CLI를 통해 모델 학습(v2)(미리 보기)

Azure CLI 확장인 Azure Machine Learning CLI(v2)를 사용하면 추적 및 감사 가능한 모델 수명 주기를 통해 Azure 컴퓨팅을 스케일 업 및 아웃하는 동시에 모델 학습 프로세스를 가속화할 수 있습니다.

기계 학습 모델을 학습시키는 과정은 반복 프로세스입니다. 최신 도구를 사용하면 더 많은 모델에 더 많은 데이터를 전보다 훨씬 더 빠르게 학습시킬 수 있습니다. 하이퍼 매개 변수 튜닝과 알고리즘 선택 같은 이전의 지루한 수동 프로세스가 자동화되는 경우가 많습니다. Azure Machine Learning CLI(v2)를 사용하면 하이퍼스페이스 스윕을 통해 [작업 영역에서](concept-workspace.md) 작업(및 모델)을 추적하고, 고성능 Azure 컴퓨팅에서 확장하고, 분산 학습을 활용하여 스케일 아웃할 수 있습니다.

[!INCLUDE [preview disclaimer](../../includes/machine-learning-preview-generic-disclaimer.md)]

## <a name="prerequisites"></a>사전 요구 사항

- CLI(v2)를 사용하려면 Azure 구독이 있어야 합니다. Azure 구독이 없는 경우 시작하기 전에 체험 계정을 만듭니다. 지금 [Azure Machine Learning 평가판 또는 유료 버전](https://azure.microsoft.com/free/)을 사용해 보세요.
- [CLI(v2) 를 설치하고 설정합니다.](how-to-configure-cli.md)

> [!TIP]
> 작업 YAML에 대한 스키마 유효성 검사 및 자동 완성 기능을 갖춘 개발 환경의 경우 Visual Studio Code 및 [Azure Machine Learning 확장을](how-to-setup-vs-code.md)사용합니다.

### <a name="clone-examples-repository"></a>예제 리포지토리 복제

학습 예제를 실행하려면 먼저 예제 리포지토리를 복제하고 `cli` 디렉터리로 변경합니다.

:::code language="azurecli" source="~/azureml-examples-main/cli/misc.sh" id="git_clone":::

를 사용하면 `--depth 1` 리포지토리에 대한 최신 커밋만 복제되어 작업을 완료하는 시간이 줄어듭니다.

### <a name="create-compute"></a>컴퓨팅 만들기

명령줄에서 Azure Machine Learning 컴퓨팅 클러스터를 만들 수 있습니다. 예를 들어 다음 명령은 `cpu-cluster`라는 클러스터 하나와 `gpu-cluster`라는 클러스터 하나를 만듭니다.

:::code language="azurecli" source="~/azureml-examples-main/setup-repo/create-compute.sh" id="create_computes":::

이 시점에서는 컴퓨팅에 대한 요금이 청구되지 않으며 `cpu-cluster` `gpu-cluster` 작업이 제출될 때까지 0개 노드로 유지됩니다. [AmlCompute의 비용을 관리하고 최적화](how-to-manage-optimize-cost.md#use-azure-machine-learning-compute-cluster-amlcompute)하는 방법에 대해 자세히 알아보세요.

이 문서의 다음 예제 작업은 `cpu-cluster` 또는 `gpu-cluster` 중 하나를 사용합니다. 필요에 따라 이 문서 전체의 예제 작업에서 이러한 이름을 클러스터의 이름으로 조정합니다. 컴퓨팅 만들기 옵션에 대한 자세한 내용을 보려면 `az ml compute create -h`를 사용하세요.

## <a name="hello-world"></a>Hello World

Azure Machine Learning CLI(v2)에서 작업은 YAML 형식으로 작성됩니다. 작업은 다음을 집계합니다.

- 실행할 버전
- 실행 방법
- 실행 위치

"hello world" 작업에는 세 항목이 모두 있습니다.

:::code language="yaml" source="~/azureml-examples-main/cli/jobs/basics/hello-world.yml":::

> [!WARNING]
> Python은 작업에 사용되는 환경에 설치해야 합니다. `apt-get update -y && apt-get install python3 -y`필요한 경우 Dockerfile에서 를 실행하여 설치하거나 Python이 이미 설치된 기본 이미지에서 파생합니다.

> [!TIP]
> 전체 예제에서는 Azure Machine Learning 확장명 를 사용하여 `$schema:` [VSCode에서](how-to-setup-vs-code.md)YAML 파일을 제작하는 경우 스키마 유효성 검사 및 자동 완성을 허용합니다.

다음을 실행할 수 있습니다.

:::code language="azurecli" source="~/azureml-examples-main/cli/train.sh" id="hello_world":::

> [!TIP]
> `--web`매개 변수는 기본 웹 브라우저를 사용하여 Azure Machine Learning Studio에서 작업을 열려고 시도합니다. `--stream`매개 변수를 사용하여 로그를 콘솔로 스트리밍하고 추가 명령을 차단할 수 있습니다.

## <a name="overriding-values-on-create-or-update"></a>만들기 또는 업데이트 시 값 재정의

YAML 작업 사양 값은 작업을 만들거나 업데이트할 때 를 사용하여 `--set` 재정의할 수 있습니다. 예를 들면 다음과 같습니다.

:::code language="azurecli" source="~/azureml-examples-main/cli/train.sh" id="hello_world_set":::

## <a name="job-names"></a>작업 이름

및 이외의 대부분의 `az ml job` `create` `list` `--name/-n` 명령에는 스튜디오에서 작업의 이름 또는 "실행 ID"인 가 필요합니다. 작업 영역별로 고유해야 하며 만드는 동안 작업의 속성을 직접 설정해서는 안 `name` 됩니다. Azure Machine Learning 설정되지 않은 경우 작업 이름에 대해 임의 GUID를 생성합니다. 이 GUID는 CLI에서 작업 만들기의 출력에서 가져오거나 스튜디오 및 MLflow API에서 "실행 ID" 속성을 복사하여 가져올 수 있습니다.

스크립트 및 CI/CD 흐름에서 작업을 자동화하려면 를 추가하여 출력을 쿼리하고 제거하여 작업을 만들 때 작업의 이름을 캡처할 수 `--query name -o tsv` 있습니다. 세부 사항은 셸에 따라 다르지만 Bash의 경우 다음과 같습니다.

:::code language="azurecli" source="~/azureml-examples-main/cli/train.sh" id="hello_world_name":::

그런 다음 `$run_id` , 또는 와 같은 후속 명령에서 `update` `show` 를 `stream` 사용합니다.

:::code language="azurecli" source="~/azureml-examples-main/cli/train.sh" id="hello_world_show":::

## <a name="organize-jobs"></a>작업 구성

작업을 구성하려면 표시 이름, 실험 이름, 설명 및 태그를 설정할 수 있습니다. 설명은 스튜디오에서 Markdown 구문을 지원합니다. 이러한 속성은 작업을 만든 후 변경이 가능합니다. 전체 예제:

:::code language="yaml" source="~/azureml-examples-main/cli/jobs/basics/hello-world-org.yml":::

이 작업을 실행할 수 있습니다. 여기서 이러한 속성은 스튜디오에 즉시 표시됩니다.

:::code language="azurecli" source="~/azureml-examples-main/cli/train.sh" id="hello_world_org":::

를 사용하면 `--set` 작업을 만든 후 변경 가능한 값을 업데이트할 수 있습니다.

:::code language="azurecli" source="~/azureml-examples-main/cli/train.sh" id="hello_world_org_set":::

## <a name="environment-variables"></a>환경 변수

작업에서 사용할 환경 변수를 설정할 수 있습니다.

:::code language="yaml" source="~/azureml-examples-main/cli/jobs/basics/hello-world-env-var.yml":::

다음 작업을 실행할 수 있습니다.

:::code language="azurecli" source="~/azureml-examples-main/cli/train.sh" id="hello_world_env_var":::

> [!WARNING]
> 에서 `inputs` 인수를 매개 변수화하는 데 를 사용해야 `command` 합니다. [입력 및 출력을 참조하세요.](#inputs-and-outputs)

## <a name="track-models-and-source-code"></a>모델 및 소스 코드 추적

프로덕션 기계 학습 모델은 감사 가능해야 합니다(재현할 수 없는 경우). 지정된 모델의 소스 코드를 추적하는 것이 중요합니다. Azure Machine Learning 소스 코드의 스냅샷을 만들어 작업과 함께 유지합니다. 또한 Git 리포지토리에서 작업을 실행하는 경우 원본 리포지토리 및 커밋이 유지됩니다.

> [!TIP]
> 예제 리포지토리를 따라 실행 중인 경우 원본 리포지토리를 확인하고 지금까지 실행된 작업 중 하나에서 스튜디오에서 커밋할 수 있습니다.

소스 코드 디렉터리에 대한 경로로 값을 사용하여 작업에서 키를 지정할 수 `code.local_path` 있습니다. 디렉터리 스냅샷이 생성되고 작업과 함께 업로드됩니다. 디렉터리 내용은 작업의 작업 디렉터리에서 직접 사용할 수 있습니다.

> [!WARNING]
> 소스 코드에는 모델 학습을 위한 대용량 데이터 입력이 포함되지 않아야 합니다. 대신 [데이터 입력을 사용합니다.](#data-inputs) 소스 코드 `.gitignore` 디렉터리에서 파일을 사용하여 스냅샷에서 파일을 제외할 수 있습니다. 스냅샷 크기에 대한 제한은 300MB 또는 2000개 파일입니다.

코드를 지정하는 작업을 살펴보겠습니다.

:::code language="yaml" source="~/azureml-examples-main/cli/jobs/basics/hello-mlflow.yml":::

Python 스크립트는 로컬 소스 코드 디렉터리에 있습니다. 그런 다음 명령을 `python` 호출하여 스크립트를 실행합니다. 다른 프로그래밍 언어에도 동일한 패턴을 적용할 수 있습니다.

> [!WARNING]
> 이 문서에 표시된 작업의 "hello" 제품군은 데모용이며 권장 모범 사례를 반드시 따르는 것은 아닙니다. 또는 를 사용하여 `&&` 시퀀스에서 많은 명령을 실행하는 것은 권장되지 않습니다. 대신 소스 코드 디렉터리에 있는 스크립트 파일에 명령을 작성하고 에서 스크립트를 호출하는 것이 `command` 좋습니다. 를 통해 위에 표시된 대로 에 대한 의존성 `command` `pip install` 설치는 권장되지 않습니다. 대신 모든 작업 의존성 을 환경의 일부로 지정해야 합니다. 자세한 내용은 [CLI(v2)를 사용하여 환경을 관리하는 방법을](how-to-manage-environments-v2.md) 참조하세요.

### <a name="model-tracking-with-mlflow"></a>MLflow를 통해 모델 추적

모델을 계속 진행하는 동안 데이터 과학자는 모델 매개 변수 및 학습 메트릭을 추적할 수 있어야 합니다. Azure Machine Learning MLflow 추적과 통합되어 모델, 아티팩트, 메트릭 및 매개 변수를 작업에 로깅할 수 있습니다. Python 스크립트에서 MLflow를 사용하려면 `import mlflow` `mlflow.log_*` 학습 코드에서 또는 `mlflow.autolog()` API를 추가하고 호출합니다.

> [!WARNING]
> `mlflow`및 `azureml-mlflow` 패키지는 MLflow 추적 기능을 위해 Python 환경에 설치해야 합니다.

> [!TIP]
> `mlflow.autolog()`호출은 많은 인기 있는 프레임워크에서 지원되며 대부분의 로깅을 처리합니다.

매개 변수, 메트릭 및 아티팩트 로그에 사용하는 위의 작업에서 호출된 Python 스크립트를 살펴보겠습니다. `mlflow`

:::code language="python" source="~/azureml-examples-main/cli/jobs/basics/src/hello-mlflow.py":::

Azure Machine Learning 통해 클라우드에서 이 작업을 실행할 수 있습니다. 여기서 추적 및 감사할 수 있습니다.

:::code language="azurecli" source="~/azureml-examples-main/cli/train.sh" id="hello_mlflow":::

### <a name="query-metrics-with-mlflow"></a>MLflow를 사용하여 메트릭 쿼리

작업을 실행한 후에는 작업의 실행 결과와 기록된 메트릭을 쿼리할 수 있습니다. Python은 CLI보다 이 작업에 더 적합합니다. 을 통해 실행 및 해당 메트릭을 `mlflow` 쿼리하고, 분석을 위해 Pandas 데이터 프레임과 같은 친숙한 개체에 로드할 수 있습니다.

먼저 Azure Machine Learning 작업 영역에 대한 MLflow 추적 URI를 검색합니다.

:::code language="azurecli" source="~/azureml-examples-main/cli/train.sh" id="mlflow_uri":::

MLflow를 가져온 Python 환경에서 이 명령의 `mlflow.set_tracking_uri(<YOUR_TRACKING_URI>)` 출력을 사용합니다. MLflow 호출은 이제 Azure Machine Learning 작업 영역의 작업에 해당합니다.

## <a name="inputs-and-outputs"></a>입력 및 출력

작업에는 일반적으로 입력 및 출력이 있습니다. 입력은 하이퍼 매개 변수 최적화를 위한 모델 매개 변수 또는 컴퓨팅 대상에 탑재되거나 다운로드되는 클라우드 데이터 입력일 수 있습니다. 출력(메트릭 무시)은 기본 출력 또는 명명된 데이터 출력에 쓰거나 복사할 수 있는 아티팩트입니다.

### <a name="literal-inputs"></a>리터럴 입력

리터럴 입력은 명령에서 직접 확인 됩니다. 리터럴 입력을 사용 하도록 "hello 세계" 작업을 수정할 수 있습니다.

:::code language="yaml" source="~/azureml-examples-main/cli/jobs/basics/hello-world-input.yml":::

이 작업을 실행할 수 있습니다.

:::code language="azurecli" source="~/azureml-examples-main/cli/train.sh" id="hello_world_input":::

`--set`를 사용 하 여 입력을 재정의할 수 있습니다.

:::code language="azurecli" source="~/azureml-examples-main/cli/train.sh" id="hello_world_input_set":::

모델 학습에서 하이퍼 매개 변수 스윕에 대 한 작업에 대 한 리터럴 입력 [을 검색 공간 입력으로 변환할](#search-space-inputs) 수 있습니다.

### <a name="search-space-inputs"></a>검색 공간 입력

스윕 작업의 경우 리터럴 입력을 선택할 수 있는 검색 공간을 지정할 수 있습니다. 검색 공간 입력에 대 한 전체 옵션 범위는 [스윕 작업 YAML 구문 참조](reference-yaml-job-sweep.md)를 참조 하세요.

> [!WARNING]
> 스윕 작업은 현재 파이프라인 작업에서 지원 되지 않습니다.

인수를 사용 하 고 임의의 메트릭을 기록 하는 간단한 Python 스크립트를 사용 하 여 개념을 살펴보겠습니다.

:::code language="python" source="~/azureml-examples-main/cli/jobs/basics/src/hello-sweep.py":::

그리고 해당 스윕 작업을 만듭니다.

:::code language="yaml" source="~/azureml-examples-main/cli/jobs/basics/hello-sweep.yml":::

다음을 실행 합니다.

:::code language="azurecli" source="~/azureml-examples-main/cli/train.sh" id="hello_sweep":::

### <a name="data-inputs"></a>데이터 입력

데이터 입력은 작업 계산의 로컬 파일 시스템에 있는 경로로 확인 됩니다. 에서 blob 컨테이너에 공개적으로 호스트 되는 클래식 Iri 데이터 집합을 살펴보겠습니다 `https://azuremlexamples.blob.core.windows.net/datasets/iris.csv` .

Iri CSV 파일에 대 한 경로를 인수로 사용 하는 Python 스크립트를 작성 하 고, 데이터 프레임로 읽어 오고, 처음 5 개 줄을 인쇄 하 고, 디렉터리에 저장할 수 있습니다 `outputs` .

:::code language="python" source="~/azureml-examples-main/cli/jobs/basics/src/hello-iris.py":::

Azure storage URI 입력을 지정 하 여 로컬 파일 시스템에 데이터를 탑재 하거나 다운로드할 수 있습니다. 단일 파일을 지정할 수 있습니다.

:::code language="yaml" source="~/azureml-examples-main/cli/jobs/basics/hello-iris-file.yml":::

실행합니다.

:::code language="azurecli" source="~/azureml-examples-main/cli/train.sh" id="iris_file":::

또는 전체 폴더를 지정 합니다.

:::code language="yaml" source="~/azureml-examples-main/cli/jobs/basics/hello-iris-folder.yml":::

실행합니다.

:::code language="azurecli" source="~/azureml-examples-main/cli/train.sh" id="iris_folder":::

#### <a name="private-data"></a>개인 데이터

데이터 저장소를 통해 Azure Machine Learning에 연결 된 Azure Blob Storage 또는 Azure Data Lake Storage의 개인 데이터에 대해서는 입력 데이터 형식의 Azure Machine Learning uri를 사용할 수 있습니다 `azureml://datastores/<DATASTORE_NAME>/paths/<PATH_TO_DATA>` . 예를 들어, 데이터 저장소에 해당 하는 Blob 컨테이너에 있는 라는 디렉터리에 Iri CSV를 업로드 하는 경우 `/example-data/` `workspaceblobstore` 데이터 저장소에서 해당 파일을 사용 하도록 이전 작업을 수정할 수 있습니다.

> [!WARNING]
> 에서 같은 위치에 Iri CSV를 복사 하지 않은 경우 이러한 작업을 실행할 수 없습니다 `workspaceblobstore` .

:::code language="yaml" source="~/azureml-examples-main/cli/jobs/basics/hello-iris-datastore-file.yml":::

또는 전체 디렉터리:

:::code language="yaml" source="~/azureml-examples-main/cli/jobs/basics/hello-iris-datastore-folder.yml":::

### <a name="default-outputs"></a>기본 출력

`./outputs` 및 `./logs` 디렉터리는 Azure Machine Learning에서 별도로 처리합니다. 작업 중에 이러한 디렉터리에 파일을 작성 하는 경우 이러한 파일은 작업이 완료 된 후에도 계속 액세스할 수 있도록 작업에 업로드 됩니다. `./outputs` 폴더는 작업이 종료될 때 업로드되는 반면, `./logs`에 기록된 파일은 실시간으로 업로드됩니다. 작업 중에 TensorBoard 로그와 같은 로그를 스트리밍하려면 후자를 사용합니다.

"Hello 세계" 작업을 다음으로 인쇄 하는 대신 기본 출력 디렉터리의 파일로 출력 하도록 수정할 수 있습니다 `stdout` .

:::code language="yaml" source="~/azureml-examples-main/cli/jobs/basics/hello-world-output.yml":::

이 작업을 실행할 수 있습니다.

:::code language="azurecli" source="~/azureml-examples-main/cli/train.sh" id="hello_world_output":::

다음 디렉터리에 있는 로그를 다운로드 합니다 `helloworld.txt` `<RUN_ID>/outputs/` .

:::code language="azurecli" source="~/azureml-examples-main/cli/train.sh" id="hello_world_output_download":::

### <a name="data-outputs"></a>데이터 출력

명명 된 데이터 출력을 지정할 수 있습니다. 이렇게 하면 기본 데이터 저장소에 디렉터리가 생성 되 고 기본적으로 읽기/쓰기가 탑재 됩니다.

이전 "hello 세계" 작업을 수정 하 여 명명 된 데이터 출력에 쓸 수 있습니다.

:::code language="yaml" source="~/azureml-examples-main/cli/jobs/basics/hello-world-output-data.yml":::

## <a name="hello-pipelines"></a>Hello 파이프라인

파이프라인 작업은 여러 작업을 병렬로 또는 순차적으로 실행할 수 있습니다. 파이프라인의 단계 간에 입/출력 종속성이 있으면 종속 단계가 완료 된 후에 실행 됩니다.

"Hello 세계" 작업을 두 개의 작업으로 분할할 수 있습니다.

:::code language="yaml" source="~/azureml-examples-main/cli/jobs/basics/hello-pipeline.yml":::

다음을 실행 합니다.

:::code language="azurecli" source="~/azureml-examples-main/cli/train.sh" id="hello_pipeline":::

"Hello" 및 "세계" 작업은 계산 대상에 사용 가능한 리소스가 있으면 각각 병렬로 실행 됩니다.

파이프라인의 단계 간에 데이터를 전달 하려면 "hello" 작업에서 데이터 출력을 정의 하 고 이전 출력을 참조 하는 "세계" 작업에서 해당 입력을 정의 합니다.

:::code language="yaml" source="~/azureml-examples-main/cli/jobs/basics/hello-pipeline-io.yml":::

다음을 실행 합니다.

:::code language="azurecli" source="~/azureml-examples-main/cli/train.sh" id="hello_pipeline_io":::

이번에는 "hello" 작업이 완료 된 후 "세계" 작업이 실행 됩니다.

파이프라인의 작업 간에 공통 설정이 중복 되지 않게 하려면 작업 외부에서 설정할 수 있습니다.

:::code language="yaml" source="~/azureml-examples-main/cli/jobs/basics/hello-pipeline-settings.yml":::

다음을 실행할 수 있습니다.

:::code language="azurecli" source="~/azureml-examples-main/cli/train.sh" id="hello_pipeline_settings":::

개별 작업에 대 한 해당 설정은 파이프라인 작업의 일반 설정을 재정의 합니다. 지금 까지의 개념은 "A", "B" 및 "C" 작업을 사용 하 여 3 단계 파이프라인 작업으로 결합할 수 있습니다. "C" 작업에는 "B" 작업에 대 한 데이터 종속성이 있으며 "A" 작업은 독립적으로 실행 될 수 있습니다. 또한 "A" 작업은 개별적으로 설정 된 환경을 사용 하 고 해당 입력 중 하나를 최상위 수준 파이프라인 작업 입력에 바인딩합니다.

:::code language="yaml" source="~/azureml-examples-main/cli/jobs/basics/hello-pipeline-abc.yml":::

다음을 실행할 수 있습니다.

:::code language="azurecli" source="~/azureml-examples-main/cli/train.sh" id="hello_pipeline_abc":::

## <a name="train-a-model"></a>모델 학습

이 시점에서 모델은 아직 학습 되지 않았습니다. `sklearn`MLflow 추적을 사용 하 여 Python 스크립트에 일부 코드를 추가 하 여 IRI CSV에서 모델을 학습 하겠습니다.

:::code language="python" source="~/azureml-examples-main/cli/jobs/single-step/scikit-learn/iris/src/main.py":::

Scikit 프레임 워크는 autologging에 대해 MLflow에서 지원 되므로 `mlflow.autolog()` 스크립트의 단일 호출은 모든 모델 매개 변수, 학습 메트릭, 모델 아티팩트 및 몇 가지 추가 아티팩트 (이 경우 혼동 행렬 이미지)를 기록 합니다.

클라우드에서이를 실행 하려면를 작업으로 지정 합니다.

:::code language="yaml" source="~/azureml-examples-main/cli/jobs/single-step/scikit-learn/iris/job.yml":::

다음을 실행 합니다.

:::code language="azurecli" source="~/azureml-examples-main/cli/train.sh" id="sklearn_iris":::

모델을 등록 하려면 출력을 다운로드 하 고 로컬 디렉터리에서 모델을 만들 수 있습니다.

:::code language="azurecli" source="~/azureml-examples-main/cli/train.sh" id="sklearn_download_register_model":::

## <a name="sweep-hyperparameters"></a>하이퍼 매개 변수 스윕

하이퍼 매개 변수를 통해 스윕 되도록 이전 작업을 수정할 수 있습니다.

:::code language="yaml" source="~/azureml-examples-main/cli/jobs/single-step/scikit-learn/iris/job-sweep.yml":::

다음을 실행 합니다.

:::code language="azurecli" source="~/azureml-examples-main/cli/train.sh" id="sklearn_sweep":::

> [!TIP]
> 스튜디오에서 "자식 실행" 탭을 확인 하 여 진행률을 모니터링 하 고 매개 변수 차트를 봅니다.

스윕 옵션에 대 한 자세한 내용은 [스윕 작업 YAML 구문 참조](reference-yaml-job-sweep.md)를 참조 하세요.

## <a name="distributed-training"></a>분산 학습

Azure Machine Learning은 PyTorch, TensorFlow 및 MPI 기반 분산 교육을 지원 합니다. 자세한 내용은 [작업 YAML 구문 참조 명령의 distributed 섹션](reference-yaml-job-command.md#distribution-configurations) 을 참조 하세요.

예를 들어 distributed PyTorch를 사용 하 여 CIFAR 데이터 집합에서 CNN (신경망)을 학습할 수 있습니다. 전체 스크립트는 [예제 리포지토리에서 사용할 수 있습니다](https://github.com/Azure/azureml-examples/tree/main/cli/jobs/single-step/pytorch/cifar-distributed).

의 CIFAR-10 데이터 집합은 `torchvision` 디렉터리를 포함 하는 디렉터리를 입력으로 예상 합니다 `cifar-10-batches-py` . 압축 된 소스를 다운로드 하 고 로컬 디렉터리에 추출할 수 있습니다.

:::code language="azurecli" source="~/azureml-examples-main/setup-repo/create-datasets.sh" id="download_untar_cifar":::

그런 다음 기본 데이터 저장소에 업로드 되는 로컬 디렉터리에서 Azure Machine Learning 데이터 집합을 만듭니다.

:::code language="azurecli" source="~/azureml-examples-main/setup-repo/create-datasets.sh" id="create_cifar":::

필요에 따라 로컬 파일 및 디렉터리를 제거 합니다.

:::code language="azurecli" source="~/azureml-examples-main/setup-repo/create-datasets.sh" id="cleanup_cifar":::

데이터 집합 (파일만)은 `dataset` 데이터 입력의 키를 사용 하 여 작업에서 참조 될 수 있습니다. 형식은 이며,이는 `azureml:<DATASET_NAME>:<DATASET_VERSION>` 생성 된 CIFAR 데이터 집합의 경우 `azureml:cifar-10-example:1` 입니다.

데이터 집합을 준비 하면 distributed PyTorch 작업을 작성 하 여 모델을 학습 시킬 수 있습니다.

:::code language="yaml" source="~/azureml-examples-main/cli/jobs/single-step/pytorch/cifar-distributed/job.yml":::

다음을 실행 합니다.

:::code language="azurecli" source="~/azureml-examples-main/cli/train.sh" id="pytorch_cifar":::

## <a name="build-a-training-pipeline"></a>학습 파이프라인 빌드

위의 CIFAR-10 예제는 파이프라인 작업으로 변환 됩니다. 이전 작업은 파이프라인의 오케스트레이션에 대해 3 개의 작업으로 분할 될 수 있습니다.

- Bash 스크립트를 실행 하 여 다운로드 하 고 추출 하는 "데이터 가져오기" `cifar-10-batches-py`
- 분산 PyTorch를 사용 하 여 데이터를 가져오고 모델을 학습 하는 "학습 모델"
- 데이터 및 학습 된 모델을 사용 하 고 정확도를 평가 하는 "평가-모델"

"학습 모델" 및 "평가 모델"은 "데이터 가져오기" 작업의 출력에 종속 됩니다. 또한 "평가-모델"은 "학습 모델" 작업의 출력에 종속 됩니다. 따라서 세 작업은 순차적으로 실행 됩니다.

파이프라인 작업 내에서이 세 작업을 오케스트레이션 할 수 있습니다.

:::code language="yaml" source="~/azureml-examples-main/cli/jobs/pipelines/cifar-10/job.yml":::

실행합니다.

:::code language="azurecli" source="~/azureml-examples-main/cli/train.sh" id="pipeline_cifar":::

재사용 가능한 구성 요소를 사용 하 여 Pipelines를 작성할 수도 있습니다. 자세한 내용은 [Azure Machine Learning CLI (미리 보기)를 사용 하 여 구성 요소 기반 기계 학습 파이프라인 만들기 및 실행](how-to-create-component-pipelines-cli.md)을 참조 하세요.

## <a name="next-steps"></a>다음 단계

- [관리형 온라인 엔드포인트(미리 보기)를 통해 기계 학습 모델 배포 및 채점](how-to-deploy-managed-online-endpoints.md)
