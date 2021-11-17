---
title: 구성 요소 기반 ML 파이프라인 만들기 및 실행 (CLI)
titleSuffix: Azure Machine Learning
description: Azure Machine Learning CLI를 사용 하 여 기계 학습 파이프라인을 만들고 실행 합니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: laobri
author: lobrien
ms.date: 10/21/2021
ms.topic: how-to
ms.custom: devplatv2
ms.openlocfilehash: 7dafb069419c1ff42d2ec5358fbd8eb37465c88f
ms.sourcegitcommit: 05c8e50a5df87707b6c687c6d4a2133dc1af6583
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/16/2021
ms.locfileid: "132552344"
---
# <a name="create-and-run-machine-learning-pipelines-using-components-with-the-azure-machine-learning-cli-preview"></a>Azure Machine Learning CLI (미리 보기)를 사용 하 여 구성 요소를 사용 하 여 기계 학습 파이프라인 만들기 및 실행

이 문서에서는 Azure CLI 및 구성 요소를 사용 하 여 [기계 학습 파이프라인](concept-ml-pipelines.md) 을 만들고 실행 하는 방법에 대해 알아봅니다. 자세한 내용은 [Azure Machine Learning 구성 요소인 무엇입니까?](concept-component.md)를 참조 하세요. [구성 요소를 사용 하지 않고 파이프라인을 만들](how-to-train-cli.md#build-a-training-pipeline)수 있지만, 구성 요소는 최대한의 유연성과 재사용을 제공 합니다. azureml Pipelines는 yaml에서 정의 되 고, Python으로 작성 되거나, 끌어서 놓기 UI를 사용 하 여 azureml Studio Designer에서 구성 될 수 있습니다. 이 문서에서는 CLI에 대해 집중적으로 설명 합니다.

[!INCLUDE [preview disclaimer](../../includes/machine-learning-preview-generic-disclaimer.md)]

## <a name="prerequisites"></a>필수 구성 요소

* Azure 구독이 없는 경우 시작하기 전에 체험 계정을 만듭니다. [Azure Machine Learning 평가판 또는 유료 버전](https://azure.microsoft.com/free/)을 사용해 보세요.

* 파이프라인 및 관련 리소스에 대 한 [Azure Machine Learning 작업 영역이](how-to-manage-workspace.md) 필요 합니다.

* [Machine Learning에 대한 Azure CLI 확장 설치 및 설정](how-to-configure-cli.md)

* 다음과 같이 예제 리포지토리를 복제합니다.

    ```azurecli-interactive
    git clone https://github.com/Azure/azureml-examples --depth 1
    cd azureml-examples/cli/jobs/pipelines-with-components/
    ```

## <a name="introducing-machine-learning-pipelines"></a>Machine learning 파이프라인 소개

AzureML의 Pipelines를 사용 하면 기계 학습 작업의 컬렉션을 워크플로로 시퀀싱 할 수 있습니다. 데이터 과학자는 일반적으로 데이터 준비, 학습, 점수 매기기 등의 개별 작업에 초점을 맞춘 스크립트를 사용 하 여 반복 합니다. 이러한 스크립트가 모두 준비 되 면 파이프라인은 이러한 스크립트의 컬렉션을 프로덕션 품질 프로세스에 연결 하는 데 도움이 됩니다.

| 이점 | 설명 |
| --- | --- |
| 자체 포함 | Pipelines은 몇 시간 또는 며칠 동안 자체 포함 된 방식으로 실행 될 수 있습니다. 즉, 업스트림 데이터를 가져와 처리 하 고 처리 한 후에 수동 작업을 수행 하지 않고 나중에 스크립트에 전달할 수 있습니다. |
| 강력한 | Pipelines는 대량 데이터 집합을 고속 처리 하는 처리 능력을 가진 클라우드에서 호스트 되는 대량 계산 클러스터에서 실행 하거나 최상의 모델을 찾기 위해 수천 개의 스윕을 수행할 수 있습니다. | 
| 반복 가능한 & 자동화 가능한 | Pipelines를 실행 하 고 새 데이터를 처리 하 고 ML 모델을 업데이트 하 여 ML 워크플로를 반복 가능 하 게 만들 수 있습니다. | 
| 가능한 | 모든 활동을 기록 하 고 중간 데이터를 비롯 한 모든 출력을 클라우드에 유지 하 여 규정 준수 및 감사 요구 사항을 충족 하는 Pipelines 재현 가능한 결과를 생성할 수 있습니다. |

Azure에는 다른 유형의 파이프라인이 있습니다. Azure Data Factory 파이프라인은 데이터-데이터 파이프라인을 강력 하 게 지원 하지만 Azure Pipelines는 CI/CD 자동화에 대 한 최상의 선택입니다. [이러한 다른 파이프라인과 Machine Learning 파이프라인을 비교](concept-ml-pipelines.md#which-azure-pipeline-technology-should-i-use)합니다.

## <a name="create-your-first-pipeline"></a>첫 번째 파이프라인 만들기

`cli/jobs/pipelines-with-components/basics` [ `azureml-examples` 리포지토리의](https://github.com/Azure/azureml-examples)디렉터리에서 `3a_basic_pipeline` 하위 디렉터리로 이동 합니다. 해당 디렉터리의 이전 예제에서는 구성 요소가 아닌 파이프라인을 표시 합니다. 다음 명령을 사용 하 여 사용 가능한 계산 리소스를 나열 합니다. 

```azurecli
az ml compute list
```

없는 경우 `cpu-cluster` 다음을 실행 하 여 이라는 클러스터를 만듭니다.

```azurecli
az ml compute create -n cpu-cluster --type amlcompute --min-instances 0 --max-instances 10
```

이제 다음 명령을 사용 하 여 파이프라인 작업을 만듭니다.

```azurecli
az ml job create --file pipeline.yml
```

파이프라인 작업에 대 한 정보를 포함 하는 다음과 같은 JSON 사전을 받게 됩니다.

| 키 | Description | 
| --- | --- | 
| `name` | 작업의 GUID 기반 이름입니다. | 
| `experiment_name` | Studio에서 작업을 구성 하는 데 사용할 이름입니다. | 
| `services.Studio.endpoint` | 파이프라인 작업을 모니터링 하 고 검토 하는 URL입니다. | 
| `status` | 작업의 상태. 이 `Preparing` 시점에 있을 것입니다. |

### <a name="review-a-component"></a>구성 요소 검토 

 , 및에서 Python 소스 코드를 빠르게 살펴보세요 `componentA_src` `componentB_src` `componentC_src` . 여기에서 볼 수 있듯이 이러한 각 디렉터리에는 Python의 약간 다른 "헬로 월드" 프로그램이 포함 되어 있습니다. 

`ComponentA.yaml`를 열고 첫 번째 구성 요소가 정의 된 방식을 확인 합니다. 

:::code language="yaml" source="~/azureml-examples-main/cli/jobs/pipelines-with-components/basics/3a_basic_pipeline/componentA.yml":::

현재 미리 보기에서는 형식의 구성 요소만 `command` 지원 됩니다. 는 `name` 고유 식별자 이며 Studio에서 구성 요소를 설명 하는 데 사용 되며 `display_name` 이름을 제공 하는 데 사용 됩니다. `version`키-값 쌍을 사용 하면 이전 버전과 재현 가능성를 유지 하면서 파이프라인 구성 요소를 개선할 수 있습니다. 

이 값의 모든 파일 `code.local_path` 은 처리를 위해 Azure에 업로드 됩니다. 

`environment`섹션에서는 구성 요소가 실행 되는 소프트웨어 환경을 지정할 수 있습니다. 이 경우 구성 요소는에 지정 된 대로 기본 Docker 이미지를 사용 `environment.image` 합니다. 자세한 내용은 [Azure Machine Learning에서 소프트웨어 환경 만들기 & 사용](how-to-use-environments.md)을 참조 하세요. 

마지막으로 `command` 키를 사용 하 여 실행할 명령을 지정 합니다.

> [!NOTE]
> `command`시작 값으로, `>-` yaml "chomping를 사용 하는 접기 스타일"입니다. 이렇게 하면 여러 줄의 텍스트에 대해 쉽게 구별할 수 있도록 명령을 작성할 수 있습니다.

구성 요소 및 해당 사양에 대 한 자세한 내용은 [Azure Machine Learning 구성 요소인?](concept-component.md)을 참조 하세요.

### <a name="review-the-pipeline-specification"></a>파이프라인 사양을 검토 합니다.

예제 디렉터리에서 `pipeline.yaml` 파일은 다음 코드와 같습니다.

:::code language="yaml" source="~/azureml-examples-main/cli/jobs/pipelines-with-components/basics/3a_basic_pipeline/pipeline.yml":::

Studio에서 작업 URL ( `services.Studio.endpoint` `job create` 작업을 만들 때 또는 작업을 만들 때 명령의 값 `job show` )을 열면 파이프라인의 그래프 표현이 표시 됩니다.

:::image type="content" source="media/how-to-create-component-pipelines-cli/pipeline-graph.png" lightbox="media/how-to-create-component-pipelines-cli/pipeline-graph.png" alt-text="스튜디오의 파이프라인 그래프 표현":::

이 파이프라인의 구성 요소 간에는 종속성이 없습니다. 일반적으로 파이프라인은 종속성을 가지 며이 페이지는 시각적으로 표시 됩니다. 이러한 구성 요소는 서로 종속 되지 않으며에 노드가 충분 하기 때문에 `cpu-cluster` 동시에 실행 됩니다. 

파이프라인 그래프에서 구성 요소를 두 번 클릭 하면 구성 요소의 자식 실행에 대 한 세부 정보를 볼 수 있습니다. 

:::image type="content" source="media/how-to-create-component-pipelines-cli/component-details.png" alt-text="구성 요소의 자식 실행에 대 한 세부 정보 및 출력을 보여 주는 스크린샷" lightbox="media/how-to-create-component-pipelines-cli/component-details.png"::: 

## <a name="upload-and-use-data"></a>데이터 업로드 및 사용

이 예제에서는 `3b_pipeline_with_data` 파이프라인에서 입력 및 출력 데이터 흐름과 저장소를 정의 하는 방법을 보여 줍니다. 

경로를 사용 하 여 파이프라인 YAML 파일에서 파이프라인에 대 한 입력 데이터 디렉터리를 정의 `inputs` 합니다. 경로를 사용 하 여 출력 및 중간 데이터 디렉터리를 정의 `outputs` 합니다. `jobs.<JOB_NAME>.inputs` `jobs.<JOB_NAME>.outputs` 다음 그림에 표시 된 것 처럼 및 경로에 이러한 정의를 사용 합니다.

:::image type="content" source="media/how-to-create-component-pipelines-cli/inputs-and-outputs.png" alt-text="입력 및 출력 경로가 작업 입력 및 출력 경로에 매핑되는 방법을 보여 주는 이미지" lightbox="media/how-to-create-component-pipelines-cli/inputs-and-outputs.png":::

1. `inputs.pipeline_sample_input_data`경로는 키 식별자를 만들고 디렉터리에서 입력 데이터를 업로드 합니다 `local_path` . 이 식별자는 `${{inputs.pipeline_sample_input_data}}` 키의 값으로 사용 됩니다 `jobs.componentA_job.inputs.componentA_input` . 
1. `jobs.componentA_job.outputs.componentA_output` `${{jobs.componentA_job.outputs.componentA_output` 다음 단계의 키에 대 한 값으로 사용 되는 식별자 (}})의 경로 `jobs.componentB_job.inputs.componentB_input` 입니다. 
1. 구성 요소 A와 마찬가지로 구성 요소 B의 출력은 구성 요소 C에 대 한 입력으로 사용 됩니다.
1. 파이프라인의 `outputs.final_pipeline_output` 키는 키에 대 한 값으로 사용 되는 식별자의 소스입니다 `jobs.componentC_job.outputs.componentC_output` . 즉, 구성 요소 C의 출력은 파이프라인의 최종 출력입니다.

이 파이프라인의 스튜디오 시각화는 다음과 같습니다. 

:::image type="content" source="media/how-to-create-component-pipelines-cli/pipeline-graph-dependencies.png" alt-text="데이터 종속성이 포함 된 파이프라인의 스튜디오 그래프 뷰를 보여 주는 스크린샷" lightbox="media/how-to-create-component-pipelines-cli/pipeline-graph-dependencies.png":::

가로 표시 되는 것을 볼 수 있습니다 `inputs.pipeline_sample_input_data` `Dataset` . `jobs.<COMPONENT_NAME>.inputs`및 `outputs` 경로의 키는 파이프라인 구성 요소 간의 데이터 흐름으로 표시 됩니다.

샘플 리포지토리의 하위 디렉터리로 전환 하 고를 실행 하 여이 예제를 실행할 수 있습니다 `3b_pipeline_with_data` .

`az ml job create --file pipeline.yaml`

### <a name="access-data-in-your-script"></a>스크립트의 데이터에 액세스

구성 요소에 대 한 입력 및 출력 디렉터리 경로는 인수로 스크립트에 전달 됩니다. 인수 이름은 또는 경로에서 YAML 파일에 지정한 키가 됩니다 `inputs` `outputs` . 예를 들면 다음과 같습니다.

```python
import argparse

parser = argparse.ArgumentParser()
parser.add_argument("--componentA_input", type=str)
parser.add_argument("--componentA_output", type=str)

args = parser.parse_args()

print("componentA_input path: %s" % args.componentA_input)
print("componentA_output path: %s" % args.componentA_output)

```

입력의 경우 파이프라인 오 케 스트레이 터는 클라우드 저장소에서 데이터를 다운로드 하거나 탑재 하 고 각 작업에서 실행 되는 스크립트를 읽을 로컬 폴더로 사용할 수 있도록 합니다. 이 동작은 스크립트에서 로컬로 실행 되 고 클라우드 계산에서 실행 되는를 수정할 필요가 없음을 의미 합니다. 마찬가지로 출력의 경우 스크립트는 클라우드 저장소에 탑재 되 고 동기화 된 로컬 폴더에 쓰고 스크립트가 완료 된 후 업로드 됩니다. 키워드를 사용 `mode` 하 여 입력을 위한 vs mount 다운로드를 지정 하 고 출력에 대해 vs mount를 업로드할 수 있습니다. 

## <a name="create-a-preparation-train-evaluate-pipeline"></a>준비-학습-평가 파이프라인 만들기

기계 학습 파이프라인에 대 한 일반적인 시나리오 중 하나에는 세 가지 주요 단계가 있습니다.

1. 데이터 준비
1. 학습
1. 모델 평가

이러한 각 단계에는 여러 구성 요소가 있을 수 있습니다. 예를 들어 데이터 준비 단계에 학습 데이터를 로드 하 고 변환 하기 위한 별도의 단계가 있을 수 있습니다. 예제 리포지토리에는 디렉터리의 종단 간 예제 파이프라인이 있습니다 `cli/jobs/pipelines-with-components/nyc_taxi_data_regression` . 

는 `job.yml` 필수 `type: pipeline` 키-값 쌍으로 시작 합니다. 그런 다음 입력 및 출력을 다음과 같이 정의 합니다.

:::code language="yaml" source="~/azureml-examples-main/cli/jobs/pipelines-with-components/nyc_taxi_data_regression/job.yml" range="5-22":::

앞에서 설명한 대로 이러한 항목은 파이프라인에 대 한 입력 데이터를 지정 합니다 .이 경우에는의 데이터 집합과 `./data` 별도의 경로에 저장 되는 파이프라인의 중간 및 최종 출력을 지정 합니다. 이러한 입력 및 출력 항목 내의 이름은 `inputs` 개별 작업의 및 항목에 포함 된 값이 됩니다 `outputs` . 

:::code language="yaml" source="~/azureml-examples-main/cli/jobs/pipelines-with-components/nyc_taxi_data_regression/job.yml" range="26-72":::

`jobs.train_job.outputs.model_output`는 다음 다이어그램에 표시 된 것 처럼 예측 작업 및 점수 매기기 작업 모두에 대 한 입력으로 사용 됩니다. 

:::image type="content" source="media/how-to-create-component-pipelines-cli/regression-graph.png" alt-text="NYC taxi-요금 예측 작업의 파이프라인 그래프" lightbox="media/how-to-create-component-pipelines-cli/regression-graph.png":::

## <a name="caching--reuse"></a>캐싱 및 재사용  

기본적으로 해당 입력이 변경 된 구성 요소만 다시 실행 됩니다. `is_deterministic`구성 요소 사양 YAML의 키를로 설정 하 여이 동작을 변경할 수 있습니다 `False` . 이에 대 한 일반적인 요구는 고정 된 위치나 URL에서 업데이트 된 데이터를 로드 하는 구성 요소입니다. 

## <a name="faq"></a>FAQ

### <a name="how-do-i-change-the-location-of-the-outputs-generated-by-the-pipeline"></a>파이프라인에서 생성 되는 출력의 위치를 변경 어떻게 할까요??
`settings`파이프라인 작업에서 섹션을 사용 하 여 파이프라인의 모든 작업에 대해 다른 데이터 저장소를 지정할 수 있습니다 ( [이 예제](https://github.com/Azure/azureml-examples/blob/main/cli/jobs/pipelines-with-components/basics/1a_e2e_local_components/pipeline.yml)에서는 줄 25-26 참조). 특정 작업 또는 특정 출력에 대해 다른 데이터 저장소를 지정 하는 것은 현재 지원 되지 않습니다. 또한 출력이 데이터 저장소에 저장 되는 경로를 지정 하는 것은 현재 지원 되지 않습니다.

### <a name="how-do-i-specify-a-compute-that-can-be-used-by-all-jobs"></a>모든 작업에서 사용할 수 있는 계산을 지정 어떻게 할까요??
계산을 명시적으로 언급 하지 않는 작업에서 사용 되는 파이프라인 작업 수준에서 계산을 지정할 수 있습니다. ( [이 예제](https://github.com/Azure/azureml-examples/blob/main/cli/jobs/pipelines-with-components/basics/1a_e2e_local_components/pipeline.yml)에서는 줄 28 참조)

### <a name="what-job-types-are-supported-in-the-pipeline-job"></a>파이프라인 작업에서 지원 되는 작업 유형은 무엇입니까?
현재 릴리스는 명령, 구성 요소 및 스윕 작업 유형을 지원 합니다.

### <a name="what-are-the-different-modes-that-i-use-with-inputs-or-outputs"></a>입/출력에 사용 하는 다양 한 모드는 무엇 인가요?
| 범주 | 허용 모드 | 기본값 |
| --- | --- | --- |
| 데이터 집합 입력 | `ro_mount` 및 `download` | `ro_mount` |
| URI 입력 | `ro_mount`, `rw_mount`, `download` | `ro_mount` | 
| 출력 | `rw_mount`, `upload` | `rw_mount` | 

### <a name="when-do-i-use-command-jobs-vs-component-jobs"></a>명령 작업 및 구성 요소 작업을 언제 사용 하나요?
명령 작업을 빠르게 반복 하 여 파이프라인에 연결할 수 있습니다. 그러나 이렇게 하면 파이프라인이 모놀리식 됩니다. 다른 파이프라인에서 파이프라인의 단계 중 하나를 사용 해야 하는 경우 작업 정의, 스크립트, 환경 등을 복사 해야 합니다. 파이프라인에서 개별 단계를 재사용 하 고 팀의 다른 사용자를 위해 쉽게 이해 하 고 사용할 수 있도록 하려는 경우 추가 단계를 만들고 등록 하는 것이 좋습니다. 구성 요소를 사용 하 여 고려해 야 할 다른 이유는 끌어서 놓기 디자이너 UI를 사용 하 여 Pipelines를 빌드하는 것입니다. 작업은 작업 영역에 등록 되지 않으므로 디자이너 캔버스에서 끌어서 놓을 수 없습니다.

### <a name="im-doing-distributed-training-in-my-component-the-component-which-is-registered-specifies-distributed-training-settings-including-node-count-how-can-i-change-the-number-of-nodes-used-during-runtime-the-optimal-number-of-nodes-is-best-determined-at-runtime-so-i-dont-want-to-update-the-component-and-register-a-new-version"></a>내 구성 요소에서 분산 교육을 수행 합니다. 등록 된 구성 요소는 노드 수를 포함 하 여 분산 학습 설정을 지정 합니다. 런타임 중에 사용 되는 노드 수를 변경 하려면 어떻게 해야 하나요? 최적의 노드 수는 런타임에 결정 하는 것이 좋지만 구성 요소를 업데이트 하 고 새 버전을 등록 하지 않으려고 합니다.

구성 요소 작업의 재정의 섹션을 사용 하 여 리소스 및 배포 설정을 변경할 수 있습니다. [이 예제에서는 TensorFlow를 사용](https://github.com/Azure/azureml-examples/tree/main/cli/jobs/pipelines-with-components/basics/6a_tf_hello_world) 하거나 [PyTorch을 사용 하는 예제](https://github.com/Azure/azureml-examples/tree/main/cli/jobs/pipelines-with-components/basics/6c_pytorch_hello_world)를 참조 하세요.  

### <a name="how-can-i-define-an-environment-with-conda-dependencies-inside-a-component"></a>구성 요소 내에서 conda 종속성이 있는 환경을 정의 하려면 어떻게 해야 하나요?
[이 예제](https://github.com/Azure/azureml-examples/tree/main/cli/jobs/pipelines-with-components/basics/5c_env_conda_file)를 참조하세요.
 

## <a name="next-steps"></a>다음 단계

- 동료 또는 고객과 파이프라인을 공유하려면 [기계 학습 파이프라인 게시](how-to-deploy-pipelines.md)를 참조하세요.
- [GitHub의 Jupyter Notebook](https://aka.ms/aml-pipeline-readme)을 사용하여 기계 학습 파이프라인을 추가로 살펴보기
- [azureml-pipelines-core](/python/api/azureml-pipeline-core/) 패키지 및 [azureml-pipelines-steps](/python/api/azureml-pipeline-steps/) 패키지에 대한 SDK 참조 도움말을 확인하세요.
- 파이프라인 디버깅 및 문제 해결에 대 한 팁은 [machine learning 파이프라인 문제 해결](how-to-debug-pipelines.md) 을 참조 하세요.
- [Jupyter 노트북을 사용하여 이 서비스 검색](samples-notebooks.md) 문서를 따라 노트북을 실행하는 방법을 알아봅니다.
