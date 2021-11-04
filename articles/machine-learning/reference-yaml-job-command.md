---
title: CLI(v2) 명령 작업 YAML 스키마
titleSuffix: Azure Machine Learning
description: CLI(v2) 명령 작업 YAML 스키마에 대한 참조 문서입니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: mx-iao
ms.author: minxia
ms.date: 10/21/2021
ms.reviewer: laobri
ms.openlocfilehash: ff49606e2f1a3877b6949d9ec0ae8a125c533484
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/04/2021
ms.locfileid: "131557930"
---
# <a name="cli-v2-command-job-yaml-schema"></a>CLI(v2) 명령 작업 YAML 스키마

원본 JSON 스키마는 https://azuremlschemas.azureedge.net/latest/commandJob.schema.json 에서 찾을 수 있습니다.

[!INCLUDE [preview disclaimer](../../includes/machine-learning-preview-generic-disclaimer.md)]

## <a name="yaml-syntax"></a>YAML 구문

| 키 | 형식 | Description | 허용되는 값 | 기본값 |
| --- | ---- | ----------- | -------------- | ------------- |
| `$schema` | 문자열 | YAML 스키마입니다. Azure Machine Learning VS Code 확장명을 사용 하 여 yaml 파일을 작성 하는 경우 `$schema` 파일의 맨 위를 비롯 하 여 스키마 및 리소스 완성을 호출할 수 있습니다. | | |
| `type` | const | 작업의 유형입니다. | `command` | `command` |
| `name` | 문자열 | 작업의 이름입니다. 작업 영역의 모든 작업에서 고유 해야 합니다. 생략 하는 경우 Azure ML는 이름에 대해 GUID를 자동으로 생성 합니다. | | |
| `display_name` | 문자열 | 스튜디오 UI에 있는 작업의 표시 이름입니다. 작업 영역 내에서 고유 하지 않을 수 있습니다. 생략 하는 경우 Azure ML는 표시 이름에 대해 사람이 읽을 수 있는 형용사 명사 식별자를 자동으로 생성 합니다. | | |
| `experiment_name` | 문자열 | 작업을 구성 하는 실험 이름입니다. 각 작업의 실행 레코드는 해당 실험의 스튜디오 "실험" 탭에서 구성 됩니다. 생략 하는 경우 Azure ML은 작업이 만들어진 작업 디렉터리의 이름으로 기본값을 지정 합니다. | | |
| `description` | 문자열 | 작업에 대 한 설명입니다. | | |
| `tags` | 개체 | 작업에 대 한 태그 사전입니다. | | |
| `command` | 문자열 | **필수 사항입니다.** 실행할 명령입니다. | | |
| `code.local_path` | 문자열 | 업로드 하 여 작업에 사용할 소스 코드 디렉터리의 로컬 경로입니다. | | |
| `environment` | 문자열 또는 개체 | **필수 사항입니다.** 작업에 사용할 환경입니다. 작업 영역이 나 인라인 환경 사양의 기존 버전 관리 환경에 대 한 참조일 수 있습니다. <br><br> 기존 환경을 참조 하려면 `azureml:<environment_name>:<environment_version>` 구문을 사용 합니다. <br><br> 환경 인라인을 정의 하려면 [환경 스키마](reference-yaml-environment.md#yaml-syntax)를 따르세요. `name` `version` 인라인 환경에서 지원 되지 않으므로 및 속성을 제외 합니다. | | |
| `environment_variables` | 개체 | 명령이 실행 되는 프로세스에 설정할 환경 변수 이름-값 쌍의 사전입니다. | | |
| `distribution` | 개체 | 분산 된 학습 시나리오에 대 한 배포 구성입니다. [MpiConfiguration](#mpiconfiguration), [PyTorchConfiguration](#pytorchconfiguration)또는 [TensorFlowConfiguration](#tensorflowconfiguration)중 하나입니다. | | |
| `compute` | 문자열 | 작업을 실행할 계산 대상의 이름입니다. 작업 영역의 기존 계산에 대 한 참조 ( `azureml:<compute_name>` 구문 사용) 이거나 `local` 로컬 실행을 지정할 수 있습니다. | | `local` |
| `resources.instance_count` | 정수 | 작업에 사용할 노드 수입니다. | | `1` |
| `limits.timeout` | 정수 | 작업을 실행할 수 있는 최대 시간 (초)입니다. 이 한도에 도달 하면 시스템은 작업을 취소 합니다. | | |
| `inputs` | 개체 | 작업에 대 한 입력의 사전입니다. 키는 작업 컨텍스트 내의 입력 이름 이며 값은 입력 값입니다. <br><br> 식을 사용 하 여에서 입력을 참조할 수 있습니다 `command` `${{ inputs.<input_name> }}` . | | |
| `inputs.<input_name>` | 숫자, 정수, 부울, 문자열 또는 개체 | 리터럴 값 (숫자, 정수, 부울 또는 문자열 형식), [Jobinputuri](#jobinputuri)또는 [jobinputuri](#jobinputdataset)중 하나입니다. | | |
| `outputs` | 개체 | 작업의 출력 구성 사전입니다. 키는 작업 컨텍스트 내의 출력 이름 이며 값은 출력 구성입니다. <br><br> 식을 사용 하 여에서 출력을 참조할 수 있습니다 `command` `${{ outputs.<output_name> }}` . | |
| `outputs.<output_name>` | 개체 | 선택 항목을 지정 `mode` 하거나 개체를 비워 둘 수 있습니다. 사전에 지정 된 각 명명 된 출력에 대해 `outputs` Azure ML는 출력 위치를 자동으로 생성 합니다. | |
| `outputs.<output_name>.mode` | 문자열 | 출력 파일이 대상 저장소에 배달 되는 방법의 모드입니다. 읽기/쓰기 탑재 모드의 경우 출력 디렉터리는 탑재 된 디렉터리가 됩니다. 업로드 모드의 경우 출력 디렉터리에 기록 된 파일은 작업이 끝날 때 업로드 됩니다. | `rw_mount`, `upload` | `rw_mount` |

### <a name="distribution-configurations"></a>배포 구성

#### <a name="mpiconfiguration"></a>MpiConfiguration

| 키 | 형식 | Description | 허용되는 값 |
| --- | ---- | ----------- | -------------- |
| `type` | const | **필수 사항입니다.** 배포 유형입니다.  | `mpi` |
| `process_count_per_instance` | 정수 | **필수 사항입니다.** 작업에 대해 시작할 노드당 프로세스의 수입니다.  | |

#### <a name="pytorchconfiguration"></a>PyTorchConfiguration

| 키 | 형식 | Description | 허용되는 값 | 기본값 |
| --- | ---- | ----------- | -------------- | ------------- |
| `type` | const | **필수 사항입니다.** 배포 유형입니다.  | `pytorch` | |
| `process_count_per_instance` | 정수 | 작업에 대해 시작할 노드당 프로세스의 수입니다. | |  `1` |

#### <a name="tensorflowconfiguration"></a>TensorFlowConfiguration

| 키 | 형식 | Description | 허용되는 값 | 기본값 |
| --- | ---- | ----------- | -------------- | ------------- |
| `type` | const | **필수 사항입니다.** 배포 유형입니다.  | `tensorflow` |
| `worker_count` | 정수 | 작업에 대해 시작할 작업자 수입니다. | | 기본값은 `resources.instance_count`입니다. |
| `parameter_server_count` | 정수 | 작업에 대해 시작할 매개 변수 서버의 수입니다. | | `0` |

### <a name="job-inputs"></a>작업 입력

#### <a name="jobinputuri"></a>JobInputUri

| 키 | 형식 | Description | 허용되는 값 | 기본값 |
| --- | ---- | ----------- | -------------- | ------------- |
| `file` | 문자열 | 입력으로 사용할 단일 파일에 대한 URI입니다. 지원되는 URI 형식은 `azureml` , , , , `https` `wasbs` `abfss` `adl` 입니다. URI 형식을 사용하는 방법에 대한 자세한 내용은 [Core yaml 구문을](reference-yaml-core-syntax.md) 참조하세요. `azureml://` **또는 중 하나가 `file` `folder` 필요합니다.**  | | |
| `folder` | 문자열 | 입력으로 사용할 폴더에 대한 URI입니다. 지원되는 URI 형식은 `azureml` , `wasbs` , , `abfss` `adl` 입니다. URI 형식을 사용하는 방법에 대한 자세한 내용은 [Core yaml 구문을](reference-yaml-core-syntax.md) 참조하세요. `azureml://` **또는 중 하나가 `file` `folder` 필요합니다.**   | | |
| `mode` | 문자열 | 컴퓨팅 대상에 데이터를 배달하는 방법의 모드입니다. 읽기 전용 탑재 및 읽기/쓰기 탑재의 경우 데이터는 탑재 경로로 소비됩니다. 폴더는 폴더로 탑재되고 파일은 파일로 탑재됩니다. 다운로드 모드의 경우 데이터는 다운로드된 경로로 소비됩니다. | `ro_mount`, `rw_mount`, `download` | `ro_mount` |

#### <a name="jobinputdataset"></a>JobInputDataset

| 키 | 형식 | Description | 허용되는 값 | 기본값 |
| --- | ---- | ----------- | -------------- | ------------- |
| `dataset` | 문자열 또는 개체 | **필수 사항입니다.** 입력으로 사용할 데이터 세트입니다. 작업 영역의 기존 버전 데이터 세트에 대한 참조 또는 인라인 데이터 세트 사양일 수 있습니다. <br><br> 기존 데이터 세트를 참조하려면 `azureml:<dataset_name>:<dataset_version>` 구문을 사용합니다. <br><br> 인라인으로 데이터 세트를 정의하려면 [데이터 세트 스키마](reference-yaml-dataset.md#yaml-syntax)를 따르세요. 및 `name` `version` 속성은 인라인 데이터 세트에 대해 지원되지 않기 때문에 제외합니다. | | |
| `mode` | 문자열 | 데이터 세트를 컴퓨팅 대상에 배달하는 방법의 모드입니다. 읽기 전용 탑재의 경우 데이터 세트가 탑재 경로로 소비됩니다. 폴더는 폴더로 탑재되고 파일은 부모 폴더로 탑재됩니다. 다운로드 모드의 경우 데이터 세트가 다운로드된 경로로 소비됩니다. | `ro_mount`, `download` | `ro_mount` |

## <a name="remarks"></a>설명

`az ml job` 명령은 Azure Machine Learning 작업을 관리하는 데 사용할 수 있습니다.

## <a name="examples"></a>예제

예제는 [GitHub 리포지토리 예제에서](https://github.com/Azure/azureml-examples/tree/main/cli/jobs)사용할 수 있습니다. 아래에는 몇 가지가 있습니다.

## <a name="yaml-hello-world"></a>YAML: hello world

:::code language="yaml" source="~/azureml-examples-cli-preview/cli/jobs/basics/hello-world.yml":::

## <a name="yaml-display-name-experiment-name-description-and-tags"></a>YAML: 표시 이름, 실험 이름, 설명 및 태그

:::code language="yaml" source="~/azureml-examples-cli-preview/cli/jobs/basics/hello-world-org.yml":::

## <a name="yaml-environment-variables"></a>YAML: 환경 변수

:::code language="yaml" source="~/azureml-examples-cli-preview/cli/jobs/basics/hello-world-env-var.yml":::

## <a name="yaml-source-code"></a>YAML: 소스 코드

:::code language="yaml" source="~/azureml-examples-cli-preview/cli/jobs/basics/hello-code.yml":::

## <a name="yaml-literal-inputs"></a>YAML: 리터럴 입력

:::code language="yaml" source="~/azureml-examples-cli-preview/cli/jobs/basics/hello-world-input.yml":::

## <a name="yaml-write-to-default-outputs"></a>YAML: 기본 출력에 쓰기

:::code language="yaml" source="~/azureml-examples-cli-preview/cli/jobs/basics/hello-world-output.yml":::

## <a name="yaml-write-to-named-data-output"></a>YAML: 명명된 데이터 출력에 쓰기

:::code language="yaml" source="~/azureml-examples-cli-preview/cli/jobs/basics/hello-world-output-data.yml":::

## <a name="yaml-datastore-uri-file-input"></a>YAML: 데이터 저장소 URI 파일 입력

:::code language="yaml" source="~/azureml-examples-cli-preview/cli/jobs/basics/hello-iris-datastore-file.yml":::

## <a name="yaml-datastore-uri-folder-input"></a>YAML: 데이터 저장소 URI 폴더 입력

:::code language="yaml" source="~/azureml-examples-cli-preview/cli/jobs/basics/hello-iris-datastore-folder.yml":::

## <a name="yaml-uri-file-input"></a>YAML: URI 파일 입력

:::code language="yaml" source="~/azureml-examples-cli-preview/cli/jobs/basics/hello-iris-file.yml":::

## <a name="yaml-uri-folder-input"></a>YAML: URI 폴더 입력

:::code language="yaml" source="~/azureml-examples-cli-preview/cli/jobs/basics/hello-iris-folder.yml":::

## <a name="yaml-notebook-via-papermill"></a>YAML: Papermill을 통한 Notebook

:::code language="yaml" source="~/azureml-examples-cli-preview/cli/jobs/basics/hello-notebook.yml":::

## <a name="yaml-basic-python-model-training"></a>YAML: 기본 Python 모델 학습

:::code language="yaml" source="~/azureml-examples-cli-preview/cli/jobs/single-step/scikit-learn/iris/job.yml":::

## <a name="yaml-basic-r-model-training-with-local-docker-build-context"></a>YAML: 로컬 Docker 빌드 컨텍스트를 통해 기본 R 모델 학습

:::code language="yaml" source="~/azureml-examples-cli-preview/cli/jobs/single-step/r/iris/job.yml":::

## <a name="yaml-distributed-pytorch"></a>YAML: 분산 PyTorch

:::code language="yaml" source="~/azureml-examples-cli-preview/cli/jobs/single-step/pytorch/cifar-distributed/job.yml":::

## <a name="yaml-distributed-tensorflow"></a>YAML: 분산 TensorFlow

:::code language="yaml" source="~/azureml-examples-cli-preview/cli/jobs/single-step/tensorflow/mnist-distributed/job.yml":::

## <a name="yaml-distributed-mpi"></a>YAML: 분산 MPI

:::code language="yaml" source="~/azureml-examples-cli-preview/cli/jobs/single-step/tensorflow/mnist-distributed-horovod/job.yml":::

## <a name="next-steps"></a>다음 단계

- [CLI(v2) 설치 및 사용](how-to-configure-cli.md)
