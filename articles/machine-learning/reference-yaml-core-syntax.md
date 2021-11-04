---
title: CLI(v2) 핵심 YAML 구문
titleSuffix: Azure Machine Learning
description: 개요 CLI(v2) 핵심 YAML 구문입니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: mx-iao
ms.author: minxia
ms.date: 10/21/2021
ms.reviewer: laobri
ms.openlocfilehash: 943de3f7327c02e4bdb20bb364f530685bce2965
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/04/2021
ms.locfileid: "131570097"
---
# <a name="cli-v2-core-yaml-syntax"></a>CLI(v2) 핵심 YAML 구문

모든 Azure Machine Learning 엔터티에는 스키마화된 YAML 표현이 있습니다. 또는 확장자를 사용하여 YAML 구성 파일에서 새 엔터티를 만들 수 `.yml` `.yaml` 있습니다.

이 문서에서는 이러한 YAML 파일을 구성하는 동안 발생하는 핵심 구문 개념에 대한 개요를 제공합니다.

[!INCLUDE [preview disclaimer](../../includes/machine-learning-preview-generic-disclaimer.md)]

## <a name="referencing-an-azure-ml-entity"></a>Azure ML 엔터티 참조

Azure ML YAML 파일을 구성할 때 기존 Azure ML 엔터티를 참조하기 위한 참조 구문(약식 및 긴 형식으로 구성)을 제공합니다. 예를 들어 작업 영역에서 기존에 등록된 환경을 참조하여 작업 환경에서 사용할 수 있습니다.

### <a name="shorthand"></a>약식

약식 구문은 다음으로 구성됩니다.

* 자산의 경우: `azureml:<asset-name>:<asset-version>`
* 리소스의 경우: `azureml:<resource-name>`

Azure ML 작업 영역에서 지정된 자산 또는 리소스에 대한 이 참조를 확인합니다.

### <a name="longhand"></a>Longhand

긴 구문은 `azureml:` 접두사 및 엔터티의 ARM 리소스 ID로 구성됩니다.

```
azureml:/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.MachineLearningServices/workspaces/<workspace-name>/environments/<environment-name>/versions/<environment-version>
```

## <a name="azure-ml-data-reference-uri"></a>Azure ML 데이터 참조 URI

Azure ML Azure Storage 서비스의 데이터를 가리키는 편리한 데이터 참조 URI 형식을 제공합니다. 이는 YAML 파일에서 클라우드 스토리지 위치를 지정해야 하는 시나리오(예: 스토리지의 파일에서 Azure ML 모델을 만들거나 작업에 입력으로 전달할 데이터를 가리키는 경우)에 사용할 수 있습니다.

이 데이터 URI 형식을 사용하려면 참조하려는 스토리지 서비스를 먼저 작업 영역에서 데이터 저장소로 등록해야 합니다. Azure ML 데이터 저장소를 만드는 동안 제공한 자격 증명을 사용하여 데이터 액세스를 처리합니다.

형식은 현재 작업 영역의 데이터 저장소와 가리키려는 파일 또는 폴더에 대한 데이터 저장소의 경로로 구성됩니다.

```
azureml://datastores/<datastore-name>/paths/<path-on-datastore>/
```

예를 들면 다음과 같습니다.

* `azureml://datastores/workspaceblobstore/paths/example-data/`
* `azureml://datastores/workspaceblobstore/paths/example-data/iris.csv`

Azure ML 데이터 참조 URI 외에도 Azure ML 공용 및 URI뿐만 아니라 , , 및 직접 스토리지 `https` `wasbs` `abfss` `adl` URI 프로토콜도 `http` `https` 지원합니다.

## <a name="expression-syntax-for-configuring-azure-ml-jobs-and-components"></a>Azure ML 작업 및 구성 요소를 구성하기 위한 식 구문

v2 작업 및 구성 요소 YAML 파일을 사용하면 식을 사용하여 다양한 시나리오의 컨텍스트에 바인딩할 수 있습니다. 필수 사용 사례는 구성을 작성할 때 알려지지 않았지만 런타임에 해결해야 하는 값에 식을 사용하는 것입니다.

다음 구문을 사용하여 식을 문자열로 처리하는 대신 평가하도록 Azure ML 지시합니다.

`${{ <expression> }}`

지원되는 시나리오는 아래에서 다룹니다.

### <a name="parameterizing-the-command-with-the-inputs-and-outputs-contexts-of-a-job"></a>`command` `inputs` 작업의 및 `outputs` 컨텍스트를 통해 매개 변수화

리터럴 값, URI 경로 및 Azure ML 데이터 세트를 작업에 대한 입력으로 지정할 수 있습니다. `command`그런 다음 구문을 사용하여 해당 입력에 대한 참조를 사용하여 를 매개 변수화할 수 `${{inputs.<input-name>}}` 있습니다. 리터럴 입력에 대한 참조는 런타임에 리터럴 값으로 확인되는 반면, 데이터 URI 또는 Azure ML 데이터 세트 입력에 대한 참조는 지정된 에 따라 다운로드 경로 또는 탑재 경로로 `mode` 확인됩니다.

마찬가지로 작업에 대한 출력도 에서 참조할 수 `command` 있습니다. 사전에 지정된 명명된 각 출력에 대해 `outputs` Azure ML 파일을 쓸 수 있는 기본 데이터 저장소의 출력 위치를 자동으로 생성합니다. 명명된 각 출력의 출력 위치는 템플릿화된 경로를 기반으로 `<default-datastore>/azureml/<job-name>/<output-name>/` 합니다. 구문을 사용하여 를 매개 `command` 변수화하면 `${{outputs.<output-name>}}` 스크립트가 작업에서 해당 위치에 파일을 쓸 수 있도록 자동 생성 경로에 대한 참조가 확인됩니다.

명령 작업 YAML 파일에 대한 아래 예제에서 `command` 는 두 개의 입력, 즉 리터럴 입력과 URI 입력 및 하나의 출력으로 매개 변수화됩니다. 런타임에 `${{inputs.learning_rate}}` 식이 로 `0.01` 확인되고 `${{inputs.iris}}` 식이 파일의 다운로드 경로로 `iris.csv` 확인됩니다. `${{outputs.model_dir}}` 는 자동 생성 출력 위치의 탑재 경로로 확인됩니다.

```yaml
$schema: https://azuremlschemas.azureedge.net/latest/commandJob.schema.json
code:
  local_path: ./src
command: python train.py --lr ${{inputs.learning_rate}} --training-data ${{inputs.iris}} --model-dir ${{outputs.model_dir}}
environment: azureml:AzureML-Minimal:1
compute: azureml:cpu-cluster
inputs:
  learning_rate: 0.01
  iris:
    file: https://azuremlexamples.blob.core.windows.net/datasets/iris.csv
    mode: download
outputs:
  model_dir:
```

### <a name="parameterizing-the-command-with-the-search_space-context-of-a-sweep-job"></a>`command`스윕 작업의 컨텍스트를 통해 매개 변수화 `search_space`

또한 작업 작성 시간 동안 하이퍼매개수의 실제 값을 알 수 없으므로 스윕 작업을 통해 하이퍼매개수 튜닝을 수행할 때도 이 식 구문을 사용합니다. 스윕 작업을 실행하면 Azure ML 에 따라 각 평가판의 하이퍼매개 값이 `search_space` 선택됩니다. 학습 스크립트에서 이러한 값에 액세스하려면 스크립트의 명령줄 인수를 통해 전달해야 합니다. 이렇게 하려면 의 `${{search_space.<hyperparameter>}}` 구문을 `trial.command` 사용합니다.

스윕 작업 YAML 파일에 대한 아래 예제에서 `${{search_space.learning_rate}}` 의 및 `${{search_space.boosting}}` `trial.command` 참조는 평가판 작업이 실행을 위해 제출될 때 각 평가판에 대해 선택된 실제 하이퍼매개수 값으로 확인됩니다.

```yaml
$schema: https://azuremlschemas.azureedge.net/latest/sweepJob.schema.json
type: sweep
sampling_algorithm: random
search_space:
  learning_rate:
    type: uniform
    min_value: 0.01
    max_value: 0.9
  boosting:
    type: choice
    values: ["gbdt", "dart"]
objective:
  goal: minimize
  primary_metric: test-multi_logloss
trial:
  code: 
    local_path: src 
  command: >-
    python train.py 
    --training-data ${{inputs.iris}}
    --lr ${{search_space.learning_rate}}
    --boosting ${{search_space.boosting}}
  environment: azureml:AzureML-Minimal:1
inputs:
  iris:
    file: https://azuremlexamples.blob.core.windows.net/datasets/iris.csv
    mode: download
compute: azureml:cpu-cluster
```

### <a name="binding-inputs-and-outputs-between-steps-in-a-pipeline-job"></a>파이프라인 작업의 단계 간에 입력 및 출력 바인딩

식은 파이프라인 작업의 단계 간에 입력 및 출력을 바인딩하는 데도 사용됩니다. 예를 들어 파이프라인에서 한 작업(job #2)의 입력을 다른 작업의 출력(job #1)에 바인딩할 수 있습니다. 이 사용량은 파이프라인 그래프의 종속성 흐름을 ML Azure에 신호를 표시하고, job #1의 출력이 작업 #2의 입력으로 필요하기 때문에 job #2는 job #1 이후에 실행됩니다.

파이프라인 작업 YAML 파일의 경우 `inputs` 각 자식 작업의 및 `outputs` 섹션이 부모 컨텍스트(최상위 파이프라인 작업) 내에서 평가됩니다. 반면에 는 `command` 현재 컨텍스트(자식 작업)로 확인됩니다.

파이프라인 작업에서 입력 및 출력을 바인딩하는 방법에는 두 가지가 있습니다.

**1) 파이프라인 작업의 최상위 입력 및 출력에 바인딩**

또는 구문을 사용하여 자식 작업의 입력 또는 출력을 최상위 부모 파이프라인 작업의 입/출력에 바인딩할 수 `${{inputs.<input-name>}}` `${{outputs.<output-name>}}` 있습니다. 이 참조는 부모 컨텍스트로 확인됩니다. 따라서 최상위 입력/출력입니다. 

아래 예제에서 최종 단계의 출력( `model_dir` `train` )은 를 통해 최상위 파이프라인 작업 출력에 바인딩됩니다. `${{outputs.trained_model}}`

**2) 다른 자식 작업의 입력 및 출력에 바인딩(단계)**

한 단계의 입/출력을 다른 단계의 입/출력에 바인딩하려면 또는 구문을 `${{jobs.<step-name>.inputs.<input-name>}}` `${{jobs.<step-name>.outputs.<outputs-name>}}` 사용합니다. 다시 이 참조는 부모 컨텍스트로 확인되므로 컨텍스트는 로 `jobs.<step-name>` 시작합니다.

아래 예제에서 단계의 입력( `clean_data` `train` )은 를 `prep_data` 통해 단계의 출력()에 `prep` 바인딩됩니다. `${{jobs.prep.outputs.prep_data}}` 단계의 준비된 데이터는 `prep` 단계의 학습 데이터로 `train` 사용됩니다.

반면에 속성 내의 컨텍스트 `command` 참조는 현재 컨텍스트로 확인됩니다. 예를 들어 `${{inputs.raw_data}}` 단계의 참조는 `prep` `command` 자식 작업인 현재 컨텍스트의 입력으로 `prep` 확인됩니다. 조회는 에서 `prep.inputs` 수행되므로 이라는 입력을 `raw_data` 정의해야 합니다.

```yaml
$schema: https://azuremlschemas.azureedge.net/latest/pipelineJob.schema.json
type: pipeline
inputs:
outputs:
  trained_model:
jobs:
  prep:
    type: command
    inputs:
      raw_data:
        folder:
        mode: rw_mount
    outputs:
      prep_data: 
        mode: upload
    code:
      local_path: src/prep
    environment: azureml:AzureML-Minimal:1
    command: >-
      python prep.py 
      --raw-data ${{inputs.raw_data}} 
      --prep-data ${{outputs.prep_data}}
    compute: azureml:cpu-cluster
  train:
    type: command
    inputs: 
      clean_data: ${{jobs.prep.outputs.prep_data}}
    outputs:
      model_dir: $${{outputs.trained_model}}
    code: 
      local_path: src/train
    environment: azureml:AzureML-Minimal:1
    compute: azureml:gpu-cluster
    command: >-
      python train.py 
      --training-data ${{inputs.clean_data}} 
      --model-output ${{outputs.model_dir}}
```

### <a name="parameterizing-the-command-with-the-inputs-and-outputs-contexts-of-a-component"></a>`command` `inputs` 구성 요소의 및 `outputs` 컨텍스트를 통해 매개 변수화

작업에 대한 와 마찬가지로 `command` 구성 요소의 은 `command` 및 컨텍스트에 대한 참조를 사용하여 매개 변수화할 수도 `inputs` `outputs` 있습니다. 이 경우 참조는 구성 요소의 입력 및 출력에 대한 것입니다. 구성 요소가 작업에서 실행되면 Azure ML 해당 구성 요소 입력 및 출력에 대해 지정된 작업 런타임 입력 및 출력 값에 대한 참조를 확인합니다. 다음은 명령 구성 요소 YAML 사양에 대한 컨텍스트 구문을 사용하는 예제입니다.

```yaml
$schema: https://azuremlschemas.azureedge.net/latest/commandComponent.schema.json
type: command
code:
  local_path: ./src
command: python train.py --lr ${{inputs.learning_rate}} --training-data ${{inputs.iris}} --model-dir ${{outputs.model_dir}}
environment: azureml:AzureML-Minimal:1
inputs:
  learning_rate:
    type: number
    default: 0.01
    optional: true
  iris:
    type: path
outputs:
  model_dir:
    type: path
```

## <a name="next-steps"></a>다음 단계

* [CLI(v2) 설치 및 사용](how-to-configure-cli.md)
* [CLI를 통해 모델 학습(v2)](how-to-train-cli.md)
* [CLI(v2) YAML 스키마](reference-yaml-overview.md)
