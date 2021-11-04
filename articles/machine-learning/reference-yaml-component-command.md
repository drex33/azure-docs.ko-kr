---
title: CLI (v2) 명령 구성 요소 YAML 스키마
titleSuffix: Azure Machine Learning
description: CLI (v2) 명령 구성 요소 YAML 스키마에 대 한 참조 설명서입니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: lostmygithubaccount
ms.author: copeters
ms.date: 10/21/2021
ms.reviewer: laobri
ms.openlocfilehash: ae71e4423011d5a69945b6bcce8ed73e0f093fc1
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/04/2021
ms.locfileid: "131570093"
---
# <a name="cli-v2-command-component-yaml-schema"></a>CLI (v2) 명령 구성 요소 YAML 스키마

원본 JSON 스키마는 https://azuremlschemas.azureedge.net/latest/commandComponent.schema.json 에서 찾을 수 있습니다.

[!INCLUDE [preview disclaimer](../../includes/machine-learning-preview-generic-disclaimer.md)]

## <a name="yaml-syntax"></a>YAML 구문

| 키 | 형식 | Description | 허용되는 값 | 기본값 |
| --- | ---- | ----------- | -------------- | ------------- |
| `$schema` | 문자열 | YAML 스키마입니다. Azure Machine Learning VS Code 확장명을 사용 하 여 yaml 파일을 작성 하는 경우 `$schema` 파일의 맨 위를 비롯 하 여 스키마 및 리소스 완성을 호출할 수 있습니다. | | |
| `type` | const | 구성 요소의 유형입니다. | `command` | `command` |
| `name` | 문자열 | **필수 사항입니다.** 구성 요소의 이름입니다. | | |
| `version` | 문자열 | 구성 요소 버전입니다. 생략 하면 Azure ML에서 버전을 자동으로 생성 합니다. | | |
| `display_name` | 문자열 | 스튜디오 UI의 구성 요소 표시 이름입니다. 작업 영역 내에서 고유 하지 않을 수 있습니다. | | |
| `description` | 문자열 | 구성 요소에 대 한 설명입니다. | | |
| `tags` | 개체 | 구성 요소에 대 한 태그 사전입니다. | | |
| `command` | 문자열 | **필수 사항입니다.** 실행할 명령입니다. | | |
| `code.local_path` | 문자열 | 업로드 하 여 구성 요소에 사용할 소스 코드 디렉터리의 로컬 경로입니다. | | |
| `environment` | 문자열 또는 개체 | **필수 사항입니다.** 구성 요소에 사용할 환경입니다. 이 값은 작업 영역에서 기존 버전의 환경에 대 한 참조 또는 인라인 환경 사양의 참조일 수 있습니다. <br><br> 기존 환경을 참조 하려면 `azureml:<environment-name>:<environment-version>` 구문을 사용 합니다. <br><br> 환경을 인라인으로 정의 하려면 [환경 스키마](reference-yaml-environment.md#yaml-syntax)를 따릅니다. `name` `version` 인라인 환경에서 지원 되지 않으므로 및 속성을 제외 합니다. | | |
| `distribution` | 개체 | 분산 된 학습 시나리오에 대 한 배포 구성입니다. [MpiConfiguration](#mpiconfiguration), [PyTorchConfiguration](#pytorchconfiguration)또는 [TensorFlowConfiguration](#tensorflowconfiguration)중 하나입니다. | | |
| `resources.instance_count` | 정수 | 작업에 사용할 노드 수입니다. | | `1` |
| `inputs` | 개체 | 구성 요소 입력의 사전입니다. 키는 구성 요소의 컨텍스트 내에 있는 입력의 이름이 며 값은 구성 요소 입력 정의입니다. <br><br> 식을 사용 하 여에서 입력을 참조할 수 있습니다 `command` `${{ inputs.<input_name> }}` . | | |
| `inputs.<input_name>` | 개체 | 구성 요소 입력 정의입니다. 구성 가능한 속성 집합의 [구성 요소 입력](#component-input) 을 참조 하세요. | | |
| `outputs` | 개체 | 구성 요소 출력의 사전입니다. 키는 구성 요소의 컨텍스트 내에서 출력에 대 한 이름이 고, 값은 구성 요소 출력 정의입니다. <br><br> 식을 사용 하 여에서 출력을 참조할 수 있습니다 `command` `${{ outputs.<output_name> }}` . | |
| `outputs.<output_name>` | 개체 | 구성 요소 출력 정의입니다. 구성 가능한 속성 집합은 [구성 요소 출력](#component-output) 을 참조 하세요. | |

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
| `worker_count` | 정수 | 작업에 대해 실행할 작업자 수입니다. | | 기본값은 `resources.instance_count`입니다. |
| `parameter_server_count` | 정수 | 작업에 대해 시작할 매개 변수 서버의 수입니다. | | `0` |

### <a name="component-input"></a>구성 요소 입력

| 키 | 형식 | Description | 허용되는 값 | 기본값 |
| --- | ---- | ----------- | -------------- | ------------- |
| `type` | 문자열 | **필수 사항입니다.** 구성 요소 입력의 형식입니다. <br><br> `type: path`구성 요소가 실행 될 때 런타임 작업 입력 값이 데이터 URI 또는 Azure ML 데이터 집합이 되도록 하려면를 사용 합니다. | `number`, `integer`, `boolean`, `string`, `path` | |
| `description` | 문자열 | 입력에 대 한 설명입니다. | | |
| `default` | 숫자, 정수, 부울 또는 문자열 | 입력의 기본값입니다. | | |
| `optional` | boolean | 입력이 필요한 지 여부를 나타냅니다. | | `false` |
| `min` | 정수 또는 숫자 | 입력에 대해 허용 되는 최소 값입니다. 이 필드 `type` 는 필드가 또는 인 경우에만 지정할 수 있습니다 `number` `integer` . | |
| `max` | 정수 또는 숫자 | 입력에 대해 허용 되는 최대값입니다. 이 필드 `type` 는 필드가 또는 인 경우에만 지정할 수 있습니다 `number` `integer` . | |
| `enum` | array | 입력에 대해 허용 된 값 목록입니다. 필드가 인 경우 적용할 수 없습니다 `type` `boolean` .  | |

### <a name="component-output"></a>구성 요소 출력

| 키 | 형식 | Description | 허용되는 값 | 기본값 |
| --- | ---- | ----------- | -------------- | ------------- |
| `type` | 문자열 | **필수 사항입니다.** 구성 요소 출력의 유형입니다. | `path` | |
| `description` | 문자열 | 출력에 대 한 설명입니다. | | |

## <a name="remarks"></a>설명

`az ml component`명령은 Azure Machine Learning 구성 요소를 관리 하는 데 사용할 수 있습니다.

## <a name="examples"></a>예제

명령 구성 요소 예제는 GitHub 리포지토리에서 사용할 수 있습니다. 에 대 한 예는 아래와 같습니다.

예제는 [GitHub 리포지토리에서](https://github.com/Azure/azureml-examples/tree/main/cli/jobs/pipeline-with-components)사용할 수 있습니다. 몇 가지는 다음과 같습니다.

## <a name="hello-world-command-component"></a>Hello 세계 명령 구성 요소

:::code language="yaml" source="~/azureml-examples-cli-preview/cli/jobs/pipelines-with-components/basics/2a_basic_component/component.yml":::

## <a name="next-steps"></a>다음 단계

- [CLI(v2) 설치 및 사용](how-to-configure-cli.md)
