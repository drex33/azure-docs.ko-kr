---
title: CLI(v2) 스윕 작업 YAML 스키마
titleSuffix: Azure Machine Learning
description: CLI(v2) 스윕 작업 YAML 스키마에 대한 참조 문서입니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: mlops
ms.topic: reference
author: mx-iao
ms.author: minxia
ms.date: 10/21/2021
ms.reviewer: laobri
ms.openlocfilehash: 90a69fceb96067ad92cc4d68f8b3e0929d2622a6
ms.sourcegitcommit: 838413a8fc8cd53581973472b7832d87c58e3d5f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/10/2021
ms.locfileid: "132135109"
---
# <a name="cli-v2-sweep-job-yaml-schema"></a>CLI(v2) 스윕 작업 YAML 스키마

원본 JSON 스키마는 https://azuremlschemas.azureedge.net/latest/sweepJob.schema.json 에서 찾을 수 있습니다.

[!INCLUDE [preview disclaimer](../../includes/machine-learning-preview-generic-disclaimer.md)]

## <a name="yaml-syntax"></a>YAML 구문

| 키 | 형식 | Description | 허용되는 값 | 기본값 |
| --- | ---- | ----------- | -------------- | ------------- |
| `$schema` | 문자열 | YAML 스키마입니다. Azure Machine Learning VS Code 확장명을 사용하여 파일 맨 위에 있는 를 포함하여 YAML 파일을 작성하면 `$schema` 스키마 및 리소스 완성을 호출할 수 있습니다. | | |
| `type` | const | **필수 사항입니다.** 작업의 유형입니다. | `sweep` | `sweep` |
| `name` | 문자열 | 작업의 이름입니다. 작업 영역의 모든 작업에서 고유해야 합니다. 생략하면 Azure ML 이름에 대한 GUID를 자동으로 생성합니다. | | |
| `display_name` | 문자열 | 스튜디오 UI에서 작업의 표시 이름입니다. 작업 영역 내에서 고유하지 않을 수 있습니다. 생략하면 Azure ML 표시 이름에 대해 사람이 읽을 수 있는 형용사-명사 식별자를 자동으로 생성합니다. | | |
| `experiment_name` | 문자열 | 작업을 구성할 실험 이름입니다. 각 작업의 실행 레코드는 스튜디오의 "실험" 탭에 있는 해당 실험 아래에 구성됩니다. 생략하면 Azure ML 작업을 만든 작업 디렉터리 이름으로 기본 설정됩니다. | | |
| `description` | 문자열 | 작업에 대한 설명입니다. | | |
| `tags` | 개체 | 작업에 대한 태그 사전입니다. | | |
| `sampling_algorithm` | 문자열 | **필수 사항입니다.** 에 사용할 하이퍼 부모 샘플링 `search_space` 알고리즘입니다. | `random`, `grid`, `bayesian` | |
| `search_space` | 개체 | **필수 사항입니다.** 하이퍼매니터 검색 공간의 사전입니다. 키는 하이퍼 매개 변수의 이름이고 값은 매개 변수 식입니다. <br><br> 식을 사용하여 에서 하이퍼 부모 를 참조할 수 `trial.command` `${{ search_space.<hyperparameter> }}` 있습니다. | | |
| `search_space.<hyperparameter>` | 개체 | 사용할 수 있는 식 집합은 [매개 변수](#parameter-expressions) 식을 참조하세요. | | |
| `objective.primary_metric` | 문자열 | **필수 사항입니다.** 각 평가판 작업에서 보고하는 기본 메트릭의 이름입니다. 메트릭은 동일한 메트릭 이름으로 를 사용하여 사용자의 학습 스크립트에 기록되어야 `mlflow.log_metric()` 합니다. | | |
| `objective.goal` | 문자열 | **필수 사항입니다.** 의 최적화 `objective.primary_metric` 목표입니다. | `maximize`, `minimize` | |
| `early_termination` | 개체 | 사용할 초기 종료 정책입니다. 지정된 정책의 조건이 충족되면 평가판 작업이 취소됩니다. 생략하면 조기 종료 정책이 적용되지 않습니다. [BanditPolicy](#banditpolicy), [MedianStoppingPolicy](#medianstoppingpolicy)또는 [TruncationSelectionPolicy](#truncationselectionpolicy)중 하나입니다. | | |
| `limits` | 개체 | 스윕 작업에 대한 제한입니다. [ `limits` 키의 특성을](#attributes-of-the-limits-key)참조하세요. | | |
| `compute` | 문자열 | **필수 사항입니다.** 구문을 사용하여 작업을 실행할 컴퓨팅 `azureml:<compute_name>` 대상의 이름입니다. | | |
| `trial` | 개체 | **필수 사항입니다.** 각 평가판에 대한 작업 템플릿입니다. 각 평가판 작업에는 시스템에서 에서 샘플링하는 하이퍼매니터 값의 다양한 조합이 `search_space` 제공됩니다. [ `trial` 키의 특성을](#attributes-of-the-trial-key)참조하세요. | | |
| `inputs` | 개체 | 작업에 대한 입력의 사전입니다. 키는 작업의 컨텍스트 내에서 입력에 대한 이름이고 값은 입력 값입니다. <br><br> 입력은 식을 사용하여 에서 참조할 수 `trial.command` `${{ inputs.<input_name> }}` 있습니다. | | |
| `inputs.<input_name>` | number, integer, boolean, string 또는 object | 리터럴 값(number, integer, boolean 또는 string 형식), [JobInputUri](#jobinputuri)또는 [JobInputDataset](#jobinputdataset)중 하나입니다. | | |
| `outputs` | 개체 | 작업의 출력 구성 사전입니다. 키는 작업의 컨텍스트 내에서 출력에 대한 이름이고 값은 출력 구성입니다. <br><br> 식을 사용하여 에서 출력을 참조할 수 `trial.command` `${{ outputs.<output_name> }}` 있습니다. | |
| `outputs.<output_name>` | 개체 | 선택적 을 `mode` 지정하거나 개체를 비워 둘 수 있습니다. 사전에 지정된 명명된 각 출력에 대해 `outputs` Azure ML 출력 위치를 기반으로 자동 생성합니다. | |
| `outputs.<output_name>.mode` | 문자열 | 출력 파일을 대상 스토리지에 배달하는 방법의 모드입니다. 읽기/쓰기 탑재 모드의 경우 출력 디렉터리에 탑재된 디렉터리입니다. 업로드 모드의 경우 출력 디렉터리에 기록된 파일은 작업이 끝날 때 업로드됩니다. | `rw_mount`, `upload` | `rw_mount` |

### <a name="early-termination-policies"></a>조기 종료 정책

#### <a name="banditpolicy"></a>BanditPolicy

| 키 | 형식 | Description | 허용되는 값 | 기본값 |
| --- | ---- | ----------- | -------------- | ------------- |
| `type` | const | **필수 사항입니다.** 정책의 형식입니다. | `bandit` | |
| `slack_factor` | number | 가장 성능이 좋은 평가판에서 허용되는 거리를 계산하는 데 사용되는 비율입니다. **또는 중 하나가 `slack_factor` `slack_amount` 필요합니다.** | | |
| `slack_amount` | number | 최상의 평가판에서 허용되는 절대 거리입니다. **또는 중 하나가 `slack_factor` `slack_amount` 필요합니다.** | | |
| `evaluation_interval` | 정수 | 정책을 적용하는 빈도입니다. | | `1` |
| `delay_evaluation` | 정수 | 첫 번째 정책 평가를 지연할 간격의 수입니다. 지정 된 경우 정책은 보다 크거나 같은의 모든에 적용 됩니다 `evaluation_interval` `delay_evaluation` . | | `0` |

#### <a name="medianstoppingpolicy"></a>Medianstop... 정책

| 키 | 형식 | Description | 허용되는 값 | 기본값 |
| --- | ---- | ----------- | -------------- | ------------- |
| `type` | const | **필수 사항입니다.** 정책의 형식입니다. | `median_stopping` | |
| `evaluation_interval` | 정수 | 정책을 적용 하는 빈도입니다. | | `1` |
| `delay_evaluation` | 정수 | 첫 번째 정책 평가를 지연할 간격의 수입니다. 지정 된 경우 정책은 보다 크거나 같은의 모든에 적용 됩니다 `evaluation_interval` `delay_evaluation` . | | `0` |

#### <a name="truncationselectionpolicy"></a>TruncationSelectionPolicy

| 키 | 형식 | Description | 허용되는 값 | 기본값 |
| --- | ---- | ----------- | -------------- | ------------- |
| `type` | const | **필수 사항입니다.** 정책의 형식입니다. | `truncation_selection` | |
| `truncation_percentage` | 정수 | **필수 사항입니다.** 각 평가 간격에서 취소할 평가 작업의 백분율입니다. | | |
| `evaluation_interval` | 정수 | 정책을 적용 하는 빈도입니다. | | `1` |
| `delay_evaluation` | 정수 | 첫 번째 정책 평가를 지연할 간격의 수입니다. 지정 된 경우 정책은 보다 크거나 같은의 모든에 적용 됩니다 `evaluation_interval` `delay_evaluation` . | | `0` |

### <a name="parameter-expressions"></a>매개 변수 식

#### <a name="choice"></a>choice

| 키 | 형식 | Description | 허용되는 값 |
| --- | ---- | ----------- | -------------- |
| `type` | const | **필수 사항입니다.** 식의 유형입니다. | `choice` |
| `values` | array | **필수 사항입니다.** 선택할 불연속 값의 목록입니다. | |

#### <a name="randint"></a>randint

| 키 | 형식 | Description | 허용되는 값 |
| --- | ---- | ----------- | -------------- |
| `type` | const | **필수 사항입니다.** 식의 유형입니다. | `randint` |
| `upper` | 정수 | **필수 사항입니다.** 정수 범위에 대 한 상한 (제외)입니다. | |

#### <a name="qlognormal-qnormal"></a>qlognormal, qlognormal

| 키 | 형식 | Description | 허용되는 값 |
| --- | ---- | ----------- | -------------- |
| `type` | const | **필수 사항입니다.** 식의 유형입니다. | `qlognormal`, `qnormal` |
| `mu` | number | **필수 사항입니다.** 정규 분포의 평균입니다. | |
| `sigma` | number | **필수 사항입니다.** 정규 분포의 표준 편차입니다. | |
| `q` | 정수 | **필수 사항입니다.** 다듬기 요소입니다. | |

#### <a name="qloguniform-quniform"></a>qloguniform, quniform

| 키 | 형식 | Description | 허용되는 값 |
| --- | ---- | ----------- | -------------- |
| `type` | const | **필수 사항입니다.** 식의 유형입니다. | `qloguniform`, `quniform` |
| `min_value` | number | **필수 사항입니다.** 범위 (포함)의 최소값입니다. | |
| `max_value` | number | **필수 사항입니다.** 범위 (포함)의 최대값입니다. | |
| `q` | 정수 | **필수 사항입니다.** 다듬기 요소입니다. | |

#### <a name="lognormal-normal"></a>로그 정규, 보통

| 키 | 형식 | Description | 허용되는 값 |
| --- | ---- | ----------- | -------------- |
| `type` | const | **필수 사항입니다.** 식의 유형입니다. | `lognormal`, `normal` |
| `mu` | number | **필수 사항입니다.** 정규 분포의 평균입니다. | |
| `sigma` | number | **필수 사항입니다.** 정규 분포의 표준 편차입니다. | |

#### <a name="loguniform"></a>loguniform

| 키 | 형식 | Description | 허용되는 값 |
| --- | ---- | ----------- | -------------- |
| `type` | const | **필수 사항입니다.** 식의 유형입니다. | `loguniform` |
| `min_value` | number | **필수 사항입니다.** 범위의 최소값은 `exp(min_value)` (포함)입니다. | |
| `max_value` | number | **필수 사항입니다.** 범위의 최 댓 값은 `exp(max_value)` (포함)입니다. | |

#### <a name="uniform"></a>uniform

| 키 | 형식 | Description | 허용되는 값 |
| --- | ---- | ----------- | -------------- |
| `type` | const | **필수 사항입니다.** 식의 유형입니다. | `uniform` |
| `min_value` | number | **필수 사항입니다.** 범위 (포함)의 최소값입니다. | |
| `max_value` | number | **필수 사항입니다.** 범위 (포함)의 최대값입니다. | |

### <a name="attributes-of-the-limits-key"></a>`limits` 키의 특성

| 키 | 형식 | Description | 기본값 |
| --- | ---- | ----------- | ------------- |
| `max_total_trials` | 정수 | 작업을 실행할 수 있는 최대 시간 (초)입니다. 이 한도에 도달 하면 시스템은 작업을 취소 합니다. | `1000` |
| `max_concurrent_trials` | 정수 | | 기본값은 `max_total_trials`입니다. |
| `timeout` | 정수 | 전체 스윕 작업을 실행할 수 있는 최대 시간 (분)입니다. 이 한도에 도달 하면 시스템은 모든 시험을 포함 하 여 스윕 작업을 취소 합니다. | `10080` |
| `trial_timeout` | 정수 | 각 평가판 작업을 실행할 수 있는 최대 시간 (초)입니다. 이 한도에 도달 하면 시스템에서 평가판을 취소 합니다. | |

### <a name="attributes-of-the-trial-key"></a>`trial` 키의 특성

| 키 | 형식 | Description | 기본값 |
| --- | ---- | ----------- | ------------- |
| `command` | 문자열 | **필수 사항입니다.** 실행할 명령입니다. | |
| `code.local_path` | 문자열 | 업로드 하 여 작업에 사용할 소스 코드 디렉터리의 로컬 경로입니다. | |
| `environment` | 문자열 또는 개체 | **필수 사항입니다.** 작업에 사용할 환경입니다. 작업 영역이 나 인라인 환경 사양의 기존 버전 관리 환경에 대 한 참조일 수 있습니다. <br> <br> 기존 환경을 참조 하려면 `azureml:<environment-name>:<environment-version>` 구문을 사용 합니다. <br><br> 환경 인라인을 정의 하려면 [환경 스키마](reference-yaml-environment.md#yaml-syntax)를 따르세요. `name` `version` 인라인 환경에서 지원 되지 않으므로 및 속성을 제외 합니다. | |
| `environment_variables` | 개체 | 명령이 실행 되는 프로세스에 설정할 환경 변수 이름-값 쌍의 사전입니다. | |
| `distribution` | 개체 | 분산 된 학습 시나리오에 대 한 배포 구성입니다. [MpiConfiguration](#mpiconfiguration), [PyTorchConfiguration](#pytorchconfiguration)또는 [TensorFlowConfiguration](#tensorflowconfiguration)중 하나입니다. | |
| `resources.instance_count` | 정수 | 작업에 사용할 노드 수입니다. | `1` |

#### <a name="distribution-configurations"></a>배포 구성

##### <a name="mpiconfiguration"></a>MpiConfiguration

| 키 | 형식 | Description | 허용되는 값 |
| --- | ---- | ----------- | -------------- |
| `type` | const | **필수 사항입니다.** 배포 유형입니다.  | `mpi` |
| `process_count_per_instance` | 정수 | **필수 사항입니다.** 작업에 대해 시작할 노드당 프로세스의 수입니다.  | |

##### <a name="pytorchconfiguration"></a>PyTorchConfiguration

| 키 | 형식 | Description | 허용되는 값 | 기본값 |
| --- | ---- | ----------- | -------------- | ------------- |
| `type` | const | **필수 사항입니다.** 배포 유형입니다.  | `pytorch` | |
| `process_count_per_instance` | 정수 | 작업에 대해 시작할 노드당 프로세스의 수입니다. | |  `1` |

##### <a name="tensorflowconfiguration"></a>TensorFlowConfiguration

| 키 | 형식 | Description | 허용되는 값 | 기본값 |
| --- | ---- | ----------- | -------------- | ------------- |
| `type` | const | **필수 사항입니다.** 배포 유형입니다.  | `tensorflow` |
| `worker_count` | 정수 | 작업에 대해 실행할 작업자 수입니다. | | 기본값은 `resources.instance_count`입니다. |
| `parameter_server_count` | 정수 | 작업에 대해 시작할 매개 변수 서버의 수입니다. | | `0` |

### <a name="job-inputs"></a>작업 입력

#### <a name="jobinputuri"></a>JobInputUri

| 키 | 형식 | Description | 허용되는 값 | 기본값 |
| --- | ---- | ----------- | -------------- | ------------- |
| `file` | 문자열 | 입력으로 사용할 단일 파일에 대 한 URI입니다. 지원 되는 URI 형식은 `azureml` ,, `https` `wasbs` , `abfss` , `adl` 입니다. URI 형식을 사용 하는 방법에 대 한 자세한 내용은 [Core yaml 구문](reference-yaml-core-syntax.md) 을 참조 하세요 `azureml://` . **또는 중 `file` 하나가 `folder` 필요 합니다.**  | | |
| `folder` | 문자열 | 입력으로 사용할 폴더에 대 한 URI입니다. 지원 되는 URI 형식은 `azureml` ,, `wasbs` `abfss` , `adl` 입니다. URI 형식을 사용 하는 방법에 대 한 자세한 내용은 [Core yaml 구문](reference-yaml-core-syntax.md) 을 참조 하세요 `azureml://` . **또는 중 `file` 하나가 `folder` 필요 합니다.**   | | |
| `mode` | 문자열 | 데이터를 계산 대상으로 배달 하는 방법의 모드입니다. 읽기 전용 탑재 및 읽기/쓰기 탑재의 경우 데이터는 탑재 경로로 사용 됩니다. 폴더는 폴더로 탑재 되 고 파일은 파일로 탑재 됩니다. 다운로드 모드의 경우 데이터가 다운로드 된 경로로 사용 됩니다. | `ro_mount`, `rw_mount`, `download` | `ro_mount` |

#### <a name="jobinputdataset"></a>JobInputDataset

| 키 | 형식 | Description | 허용되는 값 | 기본값 |
| --- | ---- | ----------- | -------------- | ------------- |
| `dataset` | 문자열 또는 개체 | **필수 사항입니다.** 입력으로 사용할 데이터 집합입니다. 작업 영역에서 기존 버전의 데이터 집합에 대 한 참조 또는 인라인 데이터 집합 사양이 될 수 있습니다. <br><br> 기존 데이터 집합을 참조 하려면 `azureml:<dataset_name>:<dataset_version>` 구문을 사용 합니다. <br><br> 데이터 집합 인라인을 정의 하려면 [데이터 집합 스키마](reference-yaml-dataset.md#yaml-syntax)를 따르세요. `name` `version` 인라인 데이터 집합에 대해 지원 되지 않으므로 및 속성을 제외 합니다. | | |
| `mode` | 문자열 | 데이터 집합을 계산 대상으로 전달 하는 방법의 모드입니다. 읽기 전용 탑재의 경우 데이터 집합이 탑재 경로로 사용 됩니다. 폴더는 폴더로 탑재 되 고 파일은 부모 폴더로 탑재 됩니다. 다운로드 모드의 경우 데이터 집합이 다운로드 된 경로로 사용 됩니다. | `ro_mount`, `download` | `ro_mount` |

## <a name="remarks"></a>설명

`az ml job` 명령은 Azure Machine Learning 작업을 관리하는 데 사용할 수 있습니다.

## <a name="examples"></a>예제

예제는 [GitHub 리포지토리에서](https://github.com/Azure/azureml-examples/tree/main/cli/jobs)사용할 수 있습니다. 몇 가지는 다음과 같습니다.

## <a name="yaml-hello-sweep"></a>YAML: hello 스윕

:::code language="yaml" source="~/azureml-examples-main/cli/jobs/basics/hello-sweep.yml":::

## <a name="yaml-basic-python-model-hyperparameter-tuning"></a>YAML: 기본 Python 모델 하이퍼 매개 변수 튜닝

:::code language="yaml" source="~/azureml-examples-main/cli/jobs/single-step/scikit-learn/iris/job-sweep.yml":::

## <a name="next-steps"></a>다음 단계

- [CLI(v2) 설치 및 사용](how-to-configure-cli.md)
