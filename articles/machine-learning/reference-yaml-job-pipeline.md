---
title: CLI(v2) 파이프라인 작업 YAML 스키마
titleSuffix: Azure Machine Learning
description: CLI(v2) 파이프라인 작업 YAML 스키마에 대한 참조 설명서입니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: lostmygithubaccount
ms.author: copeters
ms.date: 10/21/2021
ms.reviewer: laobri
ms.openlocfilehash: 73d5c79ac3e425039820101102ac91b3e3362d8e
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/04/2021
ms.locfileid: "131570083"
---
# <a name="cli-v2-pipeline-job-yaml-schema"></a>CLI(v2) 파이프라인 작업 YAML 스키마

원본 JSON 스키마는 https://azuremlschemas.azureedge.net/latest/pipelineJob.schema.json 에서 찾을 수 있습니다.

[!INCLUDE [preview disclaimer](../../includes/machine-learning-preview-generic-disclaimer.md)]

## <a name="yaml-syntax"></a>YAML 구문

| 키 | 형식 | Description | 허용되는 값 | 기본값 |
| --- | ---- | ----------- | -------------- | ------------- |
| `$schema` | 문자열 | YAML 스키마입니다. 파일의 맨 위에 있는 를 포함하여 Azure Machine Learning VS Code 확장명을 사용하여 YAML 파일을 작성하면 `$schema` 스키마 및 리소스 완성을 호출할 수 있습니다. | | |
| `type` | const | **필수 사항입니다.** 작업의 유형입니다. | `pipeline` | |
| `name` | 문자열 | 작업의 이름입니다. 작업 영역의 모든 작업에서 고유해야 합니다. 생략하면 Azure ML 이름에 대한 GUID를 자동으로 생성합니다. | | |
| `display_name` | 문자열 | 스튜디오 UI에서 작업의 표시 이름입니다. 작업 영역 내에서 고유하지 않을 수 있습니다. 생략하면 Azure ML 표시 이름에 대해 사람이 읽을 수 있는 형용사-명사 식별자를 자동으로 생성합니다. | | |
| `experiment_name` | 문자열 | 작업을 구성할 실험 이름입니다. 각 작업의 실행 레코드는 스튜디오의 "실험" 탭에 있는 해당 실험 아래에 구성됩니다. 생략하면 Azure ML 작업을 만든 작업 디렉터리 이름으로 기본 설정됩니다. | | |
| `description` | 문자열 | 작업에 대한 설명입니다. | | |
| `tags` | 개체 | 작업에 대한 태그 사전입니다. | | |
| `settings` | 개체 | 파이프라인 작업에 대한 기본 설정입니다. 구성 가능한 속성 [집합은 `settings` 키의 특성을](#attributes-of-the-settings-key) 참조하세요. | | |
| `jobs` | 개체 | **필수 사항입니다.** 파이프라인 내에서 단계로 실행할 개별 작업 집합의 사전입니다. 이러한 작업은 부모 파이프라인 작업의 자식 작업으로 간주됩니다. <br><br> 키는 파이프라인 작업의 컨텍스트 내에서 단계의 이름입니다. 이 이름은 자식 작업의 고유한 작업 이름과 다릅니다. 값은 [명령 작업 스키마](reference-yaml-job-command.md#yaml-syntax) 또는 구성 요소 작업 [스키마](reference-yaml-job-component.md#yaml-syntax)를 따를 수 있는 작업 사양입니다. 현재 명령 구성 요소를 실행하는 명령 작업 및 구성 요소 작업만 파이프라인에서 실행할 수 있습니다. 이후 릴리스는 다른 작업 유형을 지원합니다. | | |
| `inputs` | 개체 | 파이프라인 작업에 대한 입력의 사전입니다. 키는 작업의 컨텍스트 내에서 입력에 대한 이름이고 값은 입력 값입니다. <br><br> 이러한 파이프라인 입력은 식을 사용하여 파이프라인에서 개별 단계 작업의 입력으로 참조할 수 `${{ inputs.<input_name> }}` 있습니다. | | |
| `inputs.<input_name>` | number, integer, boolean, string 또는 object | 리터럴 값(number, integer, boolean 또는 string 형식), [JobInputUri](#jobinputuri)또는 [JobInputDataset](#jobinputdataset)중 하나입니다. | | |
| `outputs` | 개체 | 파이프라인 작업의 출력 구성 사전입니다. 키는 작업의 컨텍스트 내에서 출력에 대한 이름이고 값은 출력 구성입니다. <br><br> 이러한 파이프라인 출력은 식을 사용하여 파이프라인의 개별 단계 작업의 출력에서 참조할 수 `${{ outputs.<output_name> }}` 있습니다. | |
| `outputs.<output_name>` | 개체 | 선택적 을 `mode` 지정하거나 개체를 비워 둘 수 있습니다. 사전에 지정된 명명된 각 출력에 대해 `outputs` Azure ML 템플릿화된 경로를 기반으로 출력 위치를 자동으로 생성합니다. `{default-datastore}/azureml/{job-name}/{output-name}/` 사용자는 이후 릴리스에서 사용자 지정 위치를 제공할 수 있습니다. | |
| `outputs.<output_name>.mode` | 문자열 | 출력 파일을 대상 스토리지에 배달하는 방법의 모드입니다. 읽기/쓰기 탑재 모드의 경우 출력 디렉터리에 탑재된 디렉터리입니다. 업로드 모드의 경우 출력 디렉터리에 기록된 파일은 작업이 끝날 때 업로드됩니다. | `rw_mount`, `upload` | `rw_mount` |
| `compute` | 문자열 | 개별 단계에 컴퓨팅이 지정되지 않은 경우 단계 작업을 실행할 기본 컴퓨팅 대상의 이름입니다. 이 값은 구문을 사용하여 작업 영역의 기존 컴퓨팅에 대한 참조여야 `azureml:<compute-name>` 합니다. |

### <a name="attributes-of-the-settings-key"></a>`settings` 키의 특성

| 키 | 형식 | Description |
| --- | ---- | ----------- |
| `datastore` | 문자열 | 파이프라인 작업의 기본 데이터 저장소로 사용할 데이터 저장소의 이름입니다. 이 값은 구문을 사용하여 작업 영역의 기존 데이터 저장소에 대한 참조여야 `azureml:<datastore-name>` 합니다. 부모 파이프라인 작업 또는 자식 단계 작업의 속성에 정의된 모든 `outputs` 출력은 이 데이터 저장소에 저장됩니다. 생략하면 출력이 작업 영역 Blob 데이터 저장소에 저장됩니다. |

### <a name="job-inputs"></a>작업 입력

#### <a name="jobinputuri"></a>JobInputUri

| 키 | 형식 | Description | 허용되는 값 | 기본값 |
| --- | ---- | ----------- | -------------- | ------------- |
| `file` | 문자열 | 입력으로 사용할 단일 파일에 대한 URI입니다. 지원되는 URI 형식은 `azureml` , , , , `https` `wasbs` `abfss` `adl` 입니다. URI 형식을 사용하는 방법에 대한 자세한 내용은 `azureml` [Core yaml 구문 를 참조하세요.](reference-yaml-core-syntax.md) **또는 중 하나가 `file` `folder` 필요합니다.**  | | |
| `folder` | 문자열 | 입력으로 사용할 폴더에 대한 URI입니다. 지원되는 URI 형식은 `azureml` , `wasbs` , , `abfss` `adl` 입니다. URI 형식을 사용하는 방법에 대한 자세한 내용은 `azureml` [Core yaml 구문 를 참조하세요.](reference-yaml-core-syntax.md) **또는 중 하나가 `file` `folder` 필요합니다.**   | | |
| `mode` | 문자열 | 컴퓨팅 대상에 데이터를 배달하는 방법의 모드입니다. 읽기 전용 탑재 및 읽기/쓰기 탑재의 경우 데이터는 탑재 경로로 소비됩니다. 폴더는 폴더로 탑재되고 파일은 파일로 탑재됩니다. 다운로드 모드의 경우 데이터는 다운로드된 경로로 소비됩니다. | `ro_mount`, `rw_mount`, `download` | `ro_mount` |

#### <a name="jobinputdataset"></a>JobInputDataset

| 키 | 형식 | Description | 허용되는 값 | 기본값 |
| --- | ---- | ----------- | -------------- | ------------- |
| `dataset` | 문자열 또는 개체 | **필수 사항입니다.** 입력으로 사용할 데이터 집합입니다. 이 값은 작업 영역에서 기존 버전 데이터 집합에 대 한 참조 또는 인라인 데이터 집합 사양이 될 수 있습니다. <br><br> 기존 데이터 집합을 참조 하려면 구문을 사용 `azureml:<dataset-name>:<dataset-version>` 합니다. <br><br> 인라인으로 데이터 집합을 정의 하려면 [데이터 집합 스키마](reference-yaml-dataset.md#yaml-syntax)를 따릅니다. `name` `version` 인라인 데이터 집합에 대해 지원 되지 않으므로 및 속성을 제외 합니다. | | |
| `mode` | 문자열 | 데이터 집합을 계산 대상으로 전달 하는 방법의 모드입니다. 읽기 전용 탑재의 경우 데이터 집합이 탑재 경로로 사용 됩니다. 폴더는 폴더로 탑재 되 고 파일은 부모 폴더로 탑재 됩니다. 다운로드 모드의 경우 데이터 집합이 다운로드 된 경로로 사용 됩니다. | `ro_mount`, `download` | `ro_mount` |

## <a name="remarks"></a>설명

`az ml job`명령은 Azure Machine Learning 파이프라인 작업을 관리 하는 데 사용할 수 있습니다.

## <a name="examples"></a>예제

예제는 [GitHub 리포지토리에서](https://github.com/Azure/azureml-examples/tree/main/cli/jobs)사용할 수 있습니다. 몇 가지는 다음과 같습니다.

## <a name="yaml-hello-pipeline"></a>YAML: hello 파이프라인

:::code language="yaml" source="~/azureml-examples-cli-preview/cli/jobs/basics/hello-pipeline.yml":::

## <a name="yaml-inputoutput-dependency"></a>YAML: 입/출력 종속성

:::code language="yaml" source="~/azureml-examples-cli-preview/cli/jobs/basics/hello-pipeline-io.yml":::

## <a name="yaml-common-pipeline-job-settings"></a>YAML: 일반 파이프라인 작업 설정

:::code language="yaml" source="~/azureml-examples-cli-preview/cli/jobs/basics/hello-pipeline-settings.yml":::

## <a name="yaml-top-level-input-and-overriding-common-pipeline-job-settings"></a>YAML: 최상위 입력 및 공통 파이프라인 작업 설정 재정의

:::code language="yaml" source="~/azureml-examples-cli-preview/cli/jobs/basics/hello-pipeline-abc.yml":::

## <a name="yaml-model-training-pipeline"></a>YAML: 모델 학습 파이프라인

:::code language="yaml" source="~/azureml-examples-cli-preview/cli/jobs/pipelines/cifar-10/job.yml":::

## <a name="next-steps"></a>다음 단계

- [CLI(v2) 설치 및 사용](how-to-configure-cli.md)
