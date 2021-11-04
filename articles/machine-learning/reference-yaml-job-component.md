---
title: CLI(v2) 구성 요소 작업 YAML 스키마
titleSuffix: Azure Machine Learning
description: CLI(v2) 구성 요소 작업 YAML 스키마에 대한 참조 설명서입니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: mx-iao
ms.author: minxia
ms.date: 10/21/2021
ms.reviewer: laobri
ms.openlocfilehash: fc1f9580b44132b172c7f58a93d75943dea63efd
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/04/2021
ms.locfileid: "131570103"
---
# <a name="cli-v2-component-job-yaml-schema"></a>CLI(v2) 구성 요소 작업 YAML 스키마

원본 JSON 스키마는 https://azuremlschemas.azureedge.net/latest/commandJob.schema.json 에서 찾을 수 있습니다.

[!INCLUDE [preview disclaimer](../../includes/machine-learning-preview-generic-disclaimer.md)]

## <a name="yaml-syntax"></a>YAML 구문

| 키 | 형식 | Description | 허용되는 값 | 기본값 |
| --- | ---- | ----------- | -------------- | ------------- |
| `$schema` | 문자열 | YAML 스키마입니다. Azure Machine Learning VS Code 확장명을 사용하여 파일 맨 위에 있는 를 포함하여 YAML 파일을 작성하면 `$schema` 스키마 및 리소스 완성을 호출할 수 있습니다. | | |
| `type` | const | 작업의 유형입니다. | `component` | |
| `component` | 개체 | **필수 사항입니다.** 작업에서 호출하고 실행할 구성 요소입니다. 이 값은 작업 영역의 기존 버전이 적용된 구성 요소에 대한 참조, 인라인 구성 요소 사양 또는 별도의 구성 요소 YAML 사양 파일에 대한 로컬 경로일 수 있습니다. <br><br> 기존 구성 요소를 참조하려면 `azureml:<component-name>:<component-version>` 구문을 사용합니다. <br><br> 구성 요소를 인라인 또는 별도의 YAML 파일에 정의하려면 [명령 구성 요소 스키마](reference-yaml-component-command.md#yaml-syntax)를 따릅니다. 및 `name` `version` 속성은 인라인 구성 요소 사양에 적용되지 않기 때문에 제외합니다. | | |
| `compute` | 문자열 | 작업을 실행할 컴퓨팅 대상의 이름입니다. 이 값은 구문을 사용하여 작업 영역의 기존 컴퓨팅에 대한 참조여야 `azureml:<compute-name>` 합니다. 생략하면 Azure ML 파이프라인 작업의 속성에 정의된 컴퓨팅을 `compute` 사용합니다. | | |
| `inputs` | 개체 | 작업에 대한 입력의 사전입니다. 키는 구성 요소 입력 중 하나의 이름에 해당하며 값은 런타임 입력 값입니다. <br><br> 입력은 식을 사용하여 에서 참조할 수 `command` `${{ inputs.<input_name> }}` 있습니다. | | |
| `inputs.<input_name>` | number, integer, boolean, string 또는 object | 리터럴 값(number, integer, boolean 또는 string 형식), [JobInputUri](#jobinputuri)또는 [JobInputDataset](#jobinputdataset)중 하나입니다. 를 통해 동일한 파이프라인에 있는 다른 작업의 출력을 참조할 수도 있습니다. `jobs.<COMPONENT_NAME>.outputs.<OUTPUT_NAME>` | | |
| `outputs` | 개체 | 작업의 출력 구성 사전입니다. 키는 구성 요소 출력 중 하나의 이름에 해당하는 이름에 해당하며 값은 런타임 출력 구성입니다. <br><br> 식을 사용하여 에서 출력을 참조할 수 `command` `${{ outputs.<output_name> }}` 있습니다. | |
| `outputs.<output_name>` | 개체 | 선택적 을 `mode` 지정하거나 개체를 비워 둘 수 있습니다. 사전에 지정된 명명된 각 출력에 대해 `outputs` Azure ML 템플릿화된 경로를 기반으로 출력 위치를 자동으로 생성합니다. `{default-datastore}/azureml/{job-name}/{output-name}/` 사용자는 이후 릴리스에서 사용자 지정 위치를 제공할 수 있습니다. | |
| `outputs.<output_name>.mode` | 문자열 | 출력 파일을 대상 스토리지에 배달하는 방법의 모드입니다. 읽기/쓰기 탑재 모드의 경우 출력 디렉터리에 탑재된 디렉터리입니다. 업로드 모드의 경우 출력 디렉터리에 기록된 파일은 작업이 끝날 때 업로드됩니다. | `rw_mount`, `upload` | `rw_mount` |
| `overrides` | 개체 | 구성 요소가 작업에서 실행될 때 구성 요소의 특정 설정을 다른 런타임 설정으로 재정의할 수 있습니다. 명령 구성 요소의 경우 및 속성을 및 를 `resources` `distribution` 통해 재정의할 수 `overrides.resources` `overrides.distribution` 있습니다. | | |

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
| `dataset` | 문자열 또는 개체 | **필수 사항입니다.** 입력으로 사용할 데이터 세트입니다. 이 값은 작업 영역의 기존 버전 데이터 세트에 대한 참조이거나 인라인 데이터 세트 사양일 수 있습니다. <br><br> 기존 데이터 세트를 참조하려면 `azureml:<dataset-name>:<dataset-version>` 구문을 사용합니다. <br><br> 데이터 세트를 인라인으로 정의하려면 [데이터 세트 스키마](reference-yaml-dataset.md#yaml-syntax)를 따릅니다. 및 `name` `version` 속성은 인라인 데이터 세트에 대해 지원되지 않기 때문에 제외합니다. | | |
| `mode` | 문자열 | 데이터 세트를 컴퓨팅 대상에 배달하는 방법의 모드입니다. 읽기 전용 탑재의 경우 데이터 세트가 탑재 경로로 소비됩니다. 폴더는 폴더로 탑재되고 파일은 부모 폴더로 탑재됩니다. 다운로드 모드의 경우 데이터 세트가 다운로드된 경로로 소비됩니다. | `ro_mount`, `download` | `ro_mount` |

## <a name="remarks"></a>설명

구성 요소 작업은 파이프라인 작업 내에서 실행할 수 있습니다. `az ml job`명령은 Azure Machine Learning 파이프라인 작업을 관리하는 데 사용할 수 있습니다.

구성 요소 작업은 현재 독립 실행형 작업으로 실행할 수 없으며 파이프라인 내에서만 실행할 수 있습니다.

## <a name="next-steps"></a>다음 단계

- [CLI(v2) 설치 및 사용](how-to-configure-cli.md)
