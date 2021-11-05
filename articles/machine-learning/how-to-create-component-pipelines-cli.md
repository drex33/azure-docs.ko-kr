---
title: 구성 요소 기반 ML 파이프라인 만들기 및 실행(CLI)
titleSuffix: Azure Machine Learning
description: Azure Machine Learning CLI를 사용하여 기계 학습 파이프라인을 만들고 실행합니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: laobri
author: lobrien
ms.date: 10/21/2021
ms.topic: how-to
ms.custom: devplatv2
ms.openlocfilehash: 71e611855023becf474337b309f510244507bc96
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/05/2021
ms.locfileid: "131848461"
---
# <a name="create-and-run-machine-learning-pipelines-using-components-with-the-azure-machine-learning-cli-preview"></a>Azure Machine Learning CLI(미리 보기)에서 구성 요소를 사용하여 기계 학습 파이프라인 만들기 및 실행

이 문서에서는 Azure CLI 및 구성 요소를 사용하여 [기계 학습 파이프라인을](concept-ml-pipelines.md) 만들고 실행하는 방법을 알아봅니다(자세한 내용은 [Azure Machine Learning 구성 요소가란?).](concept-component.md) 구성 [요소를 사용하지 않고 파이프라인을 만들](how-to-train-cli.md#build-a-training-pipeline)수 있지만 구성 요소는 가장 많은 유연성과 재사용을 제공합니다. AzureML Pipelines YAML에서 정의하고 CLI에서 실행하거나, Python에서 작성하거나, 끌어서 놓기 UI를 통해 AzureML Studio 디자이너에서 작성할 수 있습니다. 이 문서에서는 CLI에 중점을 둡니다.

[!INCLUDE [preview disclaimer](../../includes/machine-learning-preview-generic-disclaimer.md)]

## <a name="prerequisites"></a>필수 구성 요소

* Azure 구독이 없는 경우 시작하기 전에 체험 계정을 만듭니다. [Azure Machine Learning 평가판 또는 유료 버전](https://azure.microsoft.com/free/)을 사용해 보세요.

* 파이프라인 및 관련 리소스에 대한 [Azure Machine Learning 작업 영역이](how-to-manage-workspace.md) 필요합니다.

* [Machine Learning에 대한 Azure CLI 확장 설치 및 설정](how-to-configure-cli.md)

* 다음과 같이 예제 리포지토리를 복제합니다.

    ```azurecli-interactive
    git clone https://github.com/Azure/azureml-examples --depth 1
    cd azureml-examples/cli/jobs/pipelines-with-components/
    ```

## <a name="introducing-machine-learning-pipelines"></a>기계 학습 파이프라인 소개

AzureML의 Pipelines 기계 학습 작업 컬렉션을 워크플로로 시퀀스할 수 있습니다. 데이터 과학자는 일반적으로 데이터 준비, 학습, 점수 매기기 등과 같은 개별 작업에 초점을 맞춘 스크립트를 반복합니다. 이러한 모든 스크립트가 준비되면 파이프라인은 이러한 스크립트 컬렉션을 다음과 같은 프로덕션 품질 프로세스에 연결하는 데 도움이 됩니다.

| 이점 | 설명 |
| --- | --- |
| 자체 포함 | Pipelines 몇 시간 또는 며칠 동안 자체 포함된 방식으로 실행되어 업스트림 데이터를 가져와서 처리하고 수동 개입 없이 이후 스크립트에 전달할 수 있습니다. |
| 강력한 | Pipelines 처리 능력이 있는 클라우드에서 호스트되는 대규모 컴퓨팅 클러스터에서 실행되거나 최상의 모델을 찾기 위해 수천 개의 스윕을 수행할 수 있습니다. | 
| 반복 가능한 & 자동화 가능 | Pipelines 새 데이터를 실행 및 처리하고 ML 모델을 업데이트하도록 예약하여 ML 워크플로를 반복 가능하게 만들 수 있습니다. | 
| 재현할 | Pipelines 모든 활동을 기록하고 중간 데이터를 포함한 모든 출력을 클라우드에 유지하여 재현 가능한 결과를 생성할 수 있으며 규정 준수 및 감사 요구 사항을 충족하는 데 도움이 됩니다. |

Azure에는 다른 유형의 파이프라인이 있습니다. Azure Data Factory 파이프라인은 데이터-데이터 파이프라인을 강력하게 지원하지만 Azure Pipelines CI/CD 자동화에 가장 적합한 선택입니다. [Machine Learning 파이프라인을 서로 다른 파이프라인 과 비교합니다.](concept-ml-pipelines.md#which-azure-pipeline-technology-should-i-use)

## <a name="create-your-first-pipeline"></a>첫 번째 파이프라인 만들기

`cli/jobs/pipelines-with-components/basics` [ `azureml-examples` 리포지토리의](https://github.com/Azure/azureml-examples)디렉터리에서 `3a_basic_pipeline` 하위 디렉터리로 이동합니다(해당 디렉터리의 이전 예제는 구성 요소가 아닌 파이프라인을 표시함). 다음 명령을 통해 사용 가능한 컴퓨팅 리소스를 나열합니다. 

```azurecli
az ml compute list
```

없는 경우 다음을 실행하여 라는 클러스터를 만듭니다. `cpu-cluster`

```azurecli
az ml compute create -n cpu-cluster --type amlcompute --min-instances 0 --max-instances 10
```

이제 다음 명령을 사용하여 파이프라인 작업을 만듭니다.

```azurecli
az ml job create --file pipeline.yml
```

다음을 포함하여 파이프라인 작업에 대한 정보가 포함된 JSON 사전을 받아야 합니다.

| 키 | Description | 
| --- | --- | 
| `name` | 작업의 GUID 기반 이름입니다. | 
| `experiment_name` | Studio에서 작업을 구성할 이름입니다. | 
| `services.Studio.endpoint` | 파이프라인 작업을 모니터링하고 검토하기 위한 URL입니다. | 
| `status` | 작업의 상태. 이 시점에서 발생할 수 `Preparing` 있습니다. |

### <a name="review-a-component"></a>구성 요소 검토 

 , 및 의 Python 소스 코드를 간단히 `componentA_src` `componentB_src` `componentC_src` 살펴보십시오. 보듯이 이러한 각 디렉터리에는 Python에서 약간 다른 "Hello World" 프로그램이 포함되어 있습니다. 

`ComponentA.yaml`를 열어 첫 번째 구성 요소가 정의되는 방법을 확인합니다. 

:::code language="yaml" source="~/azureml-examples-main/cli/jobs/pipelines-with-components/basics/3a_basic_pipeline/componentA.yml":::

현재 미리 보기에서는 형식의 구성 요소만 `command` 지원됩니다. 은 `name` 고유 식별자이며 Studio에서 구성 요소를 설명하는 데 사용되며 이름 `display_name` 를 제공하는 데 사용됩니다. `version`키-값 쌍을 사용하면 이전 버전과의 재현 가능성을 유지하면서 파이프라인 구성 요소를 발전할 수 있습니다. 

값의 모든 `code.local_path` 파일은 처리를 위해 Azure에 업로드됩니다. 

`environment`섹션에서는 구성 요소가 실행되는 소프트웨어 환경을 지정할 수 있습니다. 이 경우 구성 요소는 에 지정된 대로 기본 Docker 이미지를 `environment.image` 사용합니다. 자세한 내용은 [Azure Machine Learning & 소프트웨어 환경 사용을](how-to-use-environments.md)참조하세요. 

마지막으로, `command` 키를 사용하여 실행할 명령을 지정합니다.

> [!NOTE]
> 의 값은 `command` 으로 `>-` 시작하며, YAML은 "block-chomping을 포함하는 접기 스타일"입니다. 이렇게 하면 명확성을 위해 여러 줄의 텍스트에 명령을 쓸 수 있습니다.

구성 요소 및 해당 사양에 대한 자세한 내용은 [Azure Machine Learning 구성 요소란?을 참조하세요.](concept-component.md)

### <a name="review-the-pipeline-specification"></a>파이프라인 사양 검토

예제 디렉터리에서 `pipeline.yaml` 파일은 다음 코드와 같습니다.

:::code language="yaml" source="~/azureml-examples-main/cli/jobs/pipelines-with-components/basics/3a_basic_pipeline/pipeline.yml":::

Studio에서 작업의 `services.Studio.endpoint` URL(작업을 만들 때 또는 작업을 만든 후 `job create` 명령의 `job show` 값)을 열면 파이프라인의 그래프 표현이 표시됩니다.

:::image type="content" source="media/how-to-create-component-pipelines-cli/pipeline-graph.png" lightbox="media/how-to-create-component-pipelines-cli/pipeline-graph.png" alt-text="Studio에서 파이프라인의 그래프 표현":::

이 파이프라인의 구성 요소 간에는 어떠한 의존도도 없습니다. 일반적으로 파이프라인에는 의존도가 있으며, 이 페이지에서는 이를 시각적으로 표시합니다. 이러한 구성 요소는 서로 종속되지 않으며 에 `cpu-cluster` 충분한 노드가 있기 때문에 동시에 실행되었습니다. 

파이프라인 그래프에서 구성 요소를 두 번 클릭하면 구성 요소의 자식 실행에 대한 세부 정보를 볼 수 있습니다. 

:::image type="content" source="media/how-to-create-component-pipelines-cli/component-details.png" alt-text="구성 요소 자식 실행의 세부 정보 및 출력을 보여주는 스크린샷" lightbox="media/how-to-create-component-pipelines-cli/component-details.png"::: 

## <a name="upload-and-use-data"></a>데이터 업로드 및 사용

이 `3b_pipeline_with_data` 예제에서는 파이프라인에서 입력 및 출력 데이터 흐름 및 스토리지를 정의하는 방법을 보여 있습니다. 

경로를 사용하여 파이프라인 YAML 파일에서 파이프라인에 대한 입력 데이터 디렉터리를 `inputs` 정의합니다. 경로를 사용하여 출력 및 중간 데이터 디렉터리를 `outputs` 정의합니다. `jobs.<JOB_NAME>.inputs`다음 이미지와 같이 및 경로에서 이러한 정의를 사용합니다. `jobs.<JOB_NAME>.outputs`

:::image type="content" source="media/how-to-create-component-pipelines-cli/inputs-and-outputs.png" alt-text="입력 및 출력 경로가 작업 입력 및 출력 경로에 매핑하는 방법을 보여주는 이미지" lightbox="media/how-to-create-component-pipelines-cli/inputs-and-outputs.png":::

1. `inputs.pipeline_sample_input_data`경로는 키 식별자를 만들고 디렉터리에서 입력 데이터를 `local_path` 업로드합니다. 이 식별자는 `${{inputs.pipeline_sample_input_data}}` 키의 값으로 `jobs.componentA_job.inputs.componentA_input` 사용됩니다. 
1. `jobs.componentA_job.outputs.componentA_output` `${{jobs.componentA_job.outputs.componentA_output` 다음 단계의 키 값으로 사용되는 식별자(}})의 `jobs.componentB_job.inputs.componentB_input` 경로입니다. 
1. 구성 요소 A와 마찬가지로 구성 요소 B의 출력은 구성 요소 C에 대한 입력으로 사용됩니다.
1. 파이프라인의 `outputs.final_pipeline_output` 키는 키 값으로 사용되는 식별자의 `jobs.componentC_job.outputs.componentC_output` 원본입니다. 즉, 구성 요소 C의 출력은 파이프라인의 최종 출력입니다.

이 파이프라인에 대한 Studio의 시각화는 다음과 같습니다. 

:::image type="content" source="media/how-to-create-component-pipelines-cli/pipeline-graph-dependencies.png" alt-text="데이터 의존도가 있는 파이프라인의 Studio 그래프 보기를 보여주는 스크린샷" lightbox="media/how-to-create-component-pipelines-cli/pipeline-graph-dependencies.png":::

가 로 표시되는 것을 볼 수 `inputs.pipeline_sample_input_data` `Dataset` 있습니다. `jobs.<COMPONENT_NAME>.inputs`및 `outputs` 경로의 키는 파이프라인 구성 요소 간에 데이터 흐름으로 표시됩니다.

`3b_pipeline_with_data`샘플 리포지토리의 하위Directory로 전환하고 다음을 실행하여 이 예제를 실행할 수 있습니다.

`az ml job create --file pipeline.yaml`

### <a name="access-data-in-your-script"></a>스크립트의 데이터에 액세스

구성 요소에 대한 입력 및 출력 디렉터리 경로는 스크립트에 인수로 전달됩니다. 인수의 이름은 또는 경로의 YAML 파일에서 지정한 키가 `inputs` `outputs` 됩니다. 예를 들면 다음과 같습니다.

```python
import argparse

parser = argparse.ArgumentParser()
parser.add_argument("--componentA_input", type=str)
parser.add_argument("--componentA_output", type=str)

args = parser.parse_args()

print("componentA_input path: %s" % args.componentA_input)
print("componentA_output path: %s" % args.componentA_output)

```

입력의 경우 파이프라인 오케스트레이터는 클라우드 저장소에서 데이터를 다운로드(또는 탑재)하고 각 작업에서 실행되는 스크립트에 대해 읽을 로컬 폴더로 사용할 수 있도록 합니다. 이 동작은 스크립트가 로컬로 실행되고 클라우드 컴퓨팅에서 실행되는 사이에 수정할 필요가 없음을 의미합니다. 마찬가지로 출력의 경우 스크립트는 클라우드 저장소에 탑재 및 동기화되거나 스크립트가 완료된 후 업로드되는 로컬 폴더에 씁니다. 키워드를 사용하여 입력에 대해 `mode` 다운로드 및 탑재를 지정하고 출력에 대해 업로드 및 탑재를 지정할 수 있습니다. 

## <a name="create-a-preparation-train-evaluate-pipeline"></a>준비-학습-평가 파이프라인 만들기

기계 학습 파이프라인에 대한 일반적인 시나리오 중 하나에는 세 가지 주요 단계가 있습니다.

1. 데이터 준비
1. 학습
1. 모델 평가

이러한 각 단계에는 여러 구성 요소가 있을 수 있습니다. 예를 들어 데이터 준비 단계에는 학습 데이터를 로드하고 변환하는 별도의 단계가 있을 수 있습니다. 예제 리포지토리에는 디렉터리에 엔드투엔드 예제 파이프라인이 포함되어 `cli/jobs/pipelines-with-components/nyc_taxi_data_regression` 있습니다. 

는 `job.yml` 필수 `type: pipeline` 키-값 쌍으로 시작합니다. 그런 다음 입력 및 출력을 다음과 같이 정의 합니다.

:::code language="yaml" source="~/azureml-examples-main/cli/jobs/pipelines-with-components/nyc_taxi_data_regression/job.yml" range="5-22":::

앞에서 설명한 대로 이러한 항목은 파이프라인에 대 한 입력 데이터를 지정 합니다 .이 경우에는의 데이터 집합과 `./data` 별도의 경로에 저장 되는 파이프라인의 중간 및 최종 출력을 지정 합니다. 이러한 입력 및 출력 항목 내의 이름은 `inputs` 개별 작업의 및 항목에 포함 된 값이 됩니다 `outputs` . 

:::code language="yaml" source="~/azureml-examples-main/cli/jobs/pipelines-with-components/nyc_taxi_data_regression/job.yml" range="26-72":::

`jobs.train_job.outputs.model_output`는 다음 다이어그램에 표시 된 것 처럼 예측 작업 및 점수 매기기 작업 모두에 대 한 입력으로 사용 됩니다. 

:::image type="content" source="media/how-to-create-component-pipelines-cli/regression-graph.png" alt-text="NYC taxi-요금 예측 작업의 파이프라인 그래프" lightbox="media/how-to-create-component-pipelines-cli/regression-graph.png":::

## <a name="caching--reuse"></a>캐싱 및 재사용  

기본적으로 해당 입력이 변경 된 구성 요소만 다시 실행 됩니다. `is_deterministic`구성 요소 사양 YAML의 키를로 설정 하 여이 동작을 변경할 수 있습니다 `False` . 이에 대 한 일반적인 요구는 고정 된 위치나 URL에서 업데이트 된 데이터를 로드 하는 구성 요소입니다. 

## <a name="faq"></a>FAQ

### <a name="how-do-i-change-the-location-of-the-outputs-generated-by-the-pipeline"></a>파이프라인에서 생성 되는 출력의 위치를 변경 어떻게 할까요??
`settings`파이프라인 작업에서 섹션을 사용 하 여 파이프라인의 모든 작업에 대해 다른 데이터 저장소를 지정할 수 있습니다 ( [이 예제](https://github.com/Azure/azureml-examples/blob/cli-preview/cli/jobs/pipelines-with-components/basics/1a_e2e_local_components/pipeline.yml)에서는 줄 25-26 참조). 특정 작업 또는 특정 출력에 대해 다른 데이터 저장소를 지정 하는 것은 현재 지원 되지 않습니다. 또한 출력이 데이터 저장소에 저장 되는 경로를 지정 하는 것은 현재 지원 되지 않습니다.

### <a name="how-do-i-specify-a-compute-that-can-be-used-by-all-jobs"></a>모든 작업에서 사용할 수 있는 계산을 지정 어떻게 할까요??
계산을 명시적으로 언급 하지 않는 작업에서 사용 되는 파이프라인 작업 수준에서 계산을 지정할 수 있습니다. ( [이 예제](https://github.com/Azure/azureml-examples/blob/cli-preview/cli/jobs/pipelines-with-components/basics/1a_e2e_local_components/pipeline.yml)에서는 줄 28 참조)

### <a name="what-job-types-are-supported-in-the-pipeline-job"></a>파이프라인 작업에서 지원 되는 작업 유형은 무엇입니까?
현재 릴리스는 명령 및 구성 요소 작업 유형을 지원 합니다. 구성 요소 작업 유형에는 명령 구성 요소만 지원 됩니다. 이후 릴리스에서 스윕 등의 더 많은 작업 유형을 지원 합니다.

### <a name="what-are-the-different-modes-that-i-use-with-inputs-or-outputs"></a>입/출력에 사용 하는 다양 한 모드는 무엇 인가요?
| 범주 | 허용 모드 | 기본값 |
| --- | --- | --- |
| 데이터 집합 입력 | `ro_mount` 및 `download` | `ro_mount` |
| URI 입력 | `ro_mount`, `rw_mount`, `download` | `ro_mount` | 
| 출력 | `rw_mount`, `upload` | `rw_mount` | 

### <a name="when-do-i-use-command-jobs-vs-component-jobs"></a>명령 작업 및 구성 요소 작업을 언제 사용 하나요?
명령 작업을 빠르게 반복 하 여 파이프라인에 연결할 수 있습니다. 그러나 이렇게 하면 파이프라인이 모놀리식 됩니다. 다른 파이프라인에서 파이프라인의 단계 중 하나를 사용 해야 하는 경우 작업 정의, 스크립트, 환경 등을 복사 해야 합니다. 파이프라인에서 개별 단계를 재사용 하 고 팀의 다른 사용자를 위해 쉽게 이해 하 고 사용할 수 있도록 하려는 경우 추가 단계를 만들고 등록 하는 것이 좋습니다. 구성 요소를 사용 하 여 고려해 야 할 다른 이유는 끌어서 놓기 디자이너 UI를 사용 하 여 Pipelines를 빌드하는 것입니다. 작업은 작업 영역에 등록 되지 않으므로 디자이너 캔버스에서 끌어서 놓을 수 없습니다.

### <a name="im-doing-distributed-training-in-my-component-the-component-which-is-registered-specifies-distributed-training-settings-including-node-count-how-can-i-change-the-number-of-nodes-used-during-runtime-the-optimal-number-of-nodes-is-best-determined-at-runtime-so-i-dont-want-to-update-the-component-and-register-a-new-version"></a>내 구성 요소에서 분산 교육을 수행 합니다. 등록 된 구성 요소는 노드 수를 포함 하 여 분산 학습 설정을 지정 합니다. 런타임 중에 사용 되는 노드 수를 변경 하려면 어떻게 해야 하나요? 최적의 노드 수는 런타임에 결정 하는 것이 좋지만 구성 요소를 업데이트 하 고 새 버전을 등록 하지 않으려고 합니다.

구성 요소 작업의 재정의 섹션을 사용 하 여 리소스 및 배포 설정을 변경할 수 있습니다. [이 예제에서는 TensorFlow를 사용](https://github.com/Azure/azureml-examples/blob/cli-preview/cli/jobs/pipelines-with-components/basics/6a_tf_hello_world/) 하거나 [PyTorch을 사용 하는 예제](https://github.com/Azure/azureml-examples/blob/cli-preview/cli/jobs/pipelines-with-components/basics/6y_pytorch_hello_world)를 참조 하세요.  

### <a name="how-can-i-define-an-environment-with-conda-dependencies-inside-a-component"></a>구성 요소 내에서 conda 종속성이 있는 환경을 정의 하려면 어떻게 해야 하나요?
[이 예제](https://github.com/Azure/azureml-examples/blob/cli-preview/cli/jobs/pipelines-with-components/basics/5c_env_conda_file)를 참조하세요.
 

## <a name="next-steps"></a>다음 단계

- 동료 또는 고객과 파이프라인을 공유하려면 [기계 학습 파이프라인 게시](how-to-deploy-pipelines.md)를 참조하세요.
- [GitHub의 Jupyter Notebook](https://aka.ms/aml-pipeline-readme)을 사용하여 기계 학습 파이프라인을 추가로 살펴보기
- [azureml-pipelines-core](/python/api/azureml-pipeline-core/) 패키지 및 [azureml-pipelines-steps](/python/api/azureml-pipeline-steps/) 패키지에 대한 SDK 참조 도움말을 확인하세요.
- 파이프라인 디버깅 및 문제 해결에 대 한 팁은 [machine learning 파이프라인 문제 해결](how-to-debug-pipelines.md) 을 참조 하세요.
- [Jupyter 노트북을 사용하여 이 서비스 검색](samples-notebooks.md) 문서를 따라 노트북을 실행하는 방법을 알아봅니다.
