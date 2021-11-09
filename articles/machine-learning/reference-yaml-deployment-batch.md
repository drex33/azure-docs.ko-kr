---
title: CLI (v2) batch 배포 YAML 스키마
titleSuffix: Azure Machine Learning
description: CLI (v2) batch 배포 YAML 스키마에 대 한 참조 설명서입니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: tracychms
ms.author: tracych
ms.date: 10/21/2021
ms.reviewer: laobri
ms.openlocfilehash: 53e83f76e61038bc1fb95bfba653b18f63febede
ms.sourcegitcommit: 61f87d27e05547f3c22044c6aa42be8f23673256
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/09/2021
ms.locfileid: "132054508"
---
# <a name="cli-v2-batch-deployment-yaml-schema"></a>CLI (v2) batch 배포 YAML 스키마

원본 JSON 스키마는 https://azuremlschemas.azureedge.net/latest/batchDeployment.schema.json 에서 찾을 수 있습니다.

[!INCLUDE [preview disclaimer](../../includes/machine-learning-preview-generic-disclaimer.md)]

## <a name="yaml-syntax"></a>YAML 구문

| 키 | 형식 | Description | 허용되는 값 | 기본값 |
| --- | ---- | ----------- | -------------- | ------------- |
| `$schema` | 문자열 | YAML 스키마입니다. Azure Machine Learning VS Code 확장명을 사용 하 여 yaml 파일을 작성 하는 경우 `$schema` 파일의 맨 위를 비롯 하 여 스키마 및 리소스 완성을 호출할 수 있습니다. | | |
| `name` | 문자열 | **필수 사항입니다.** 배포의 이름입니다. | | |
| `description` | 문자열 | 배포에 대 한 설명입니다. | | |
| `tags` | 개체 | 배포에 대 한 태그 사전입니다. | | |
| `endpoint_name` | 문자열 | **필수 사항입니다.** 배포를 만들 끝점의 이름입니다. | | |
| `model` | 문자열 또는 개체 | **필수 사항입니다.** 배포에 사용할 모델입니다. 이 값은 작업 영역에 있는 기존 버전의 모델에 대 한 참조 또는 인라인 모델 사양을 사용할 수 있습니다. <br><br> 기존 모델을 참조 하려면 `azureml:<model-name>:<model-version>` 구문을 사용 합니다. <br><br> 모델 인라인을 정의 하려면 [모델 스키마](reference-yaml-model.md#yaml-syntax)를 따릅니다. <br><br> 프로덕션 시나리오에 대 한 모범 사례로 모델을 별도로 만들고 여기에서 참조 해야 합니다. | | |
| `code_configuration` | 개체 | 점수 매기기 코드 논리에 대 한 구성입니다. <br><br> 모델이 MLflow 형식이 면이 속성은 필요 하지 않습니다. | | |
| `code_configuration.code.local_path` | 문자열 | 모델의 점수를 매기는 소스 코드 디렉터리의 로컬 경로입니다. | | |
| `code_configuration.scoring_script` | 문자열 | 소스 코드 디렉터리에 있는 점수 매기기 파일의 상대 경로입니다. | | |
| `environment` | 문자열 또는 개체 | 배포에 사용할 환경입니다. 이 값은 작업 영역에서 기존 버전의 환경에 대 한 참조 또는 인라인 환경 사양의 참조일 수 있습니다. <br><br> 모델이 MLflow 형식이 면이 속성은 필요 하지 않습니다. <br><br> 기존 환경을 참조 하려면 `azureml:<environment-name>:<environment-version>` 구문을 사용 합니다. <br><br> 환경을 인라인으로 정의 하려면 [환경 스키마](reference-yaml-environment.md#yaml-syntax)를 따릅니다. <br><br> 프로덕션 시나리오에 대 한 모범 사례로, 환경을 별도로 만들고 여기에서 참조 해야 합니다. | | |
| `compute` | 문자열 | **필수 사항입니다.** 일괄 처리 점수 매기기 작업을 실행할 계산 대상의 이름입니다. 이 값은 구문을 사용 하 여 작업 영역의 기존 계산에 대 한 참조 여야 합니다 `azureml:<compute-name>` . | | |
| `resources.instance_count` | 정수 | 각 일괄 처리 점수 매기기 작업에 사용할 노드 수입니다. | | `1` |
| `max_concurrency_per_instance` | 정수 | 인스턴스당 최대 병렬 실행 수 `scoring_script` 입니다. | | `1` |
| `error_threshold` | 정수 | 무시할 파일 오류 수입니다. 전체 입력의 오류 수가이 값을 초과 하면 일괄 처리 점수 매기기 작업이 종료 됩니다. `error_threshold` 는 개별 미니 일괄 처리에 대해서가 아니라 전체 입력을 위한 것입니다. 생략 하는 경우 작업을 종료 하지 않고 파일 오류가 발생할 수 있습니다.  | | `-1` |
| `logging_level` | 문자열 | 로그의 자세한 정도입니다. | `warning`, `info`, `debug` | `info` |
| `mini_batch_size` | 정수 | 가 `code_configuration.scoring_script` 한 번의 호출로 처리할 수 있는 파일 수입니다 `run()` . | | `10` |
| `retry_settings` | 개체 | 각 미니 일괄 처리의 점수를 매기는 설정을 다시 시도 합니다. | | |
| `retry_settings.max_retries` | 정수 | 실패 또는 시간 제한 된 미니 일괄 처리에 대 한 최대 다시 시도 횟수입니다. | | `3` |
| `retry_settings.timeout` | 정수 | 미니 배치의 점수를 매기는 제한 시간 (초)입니다. | | `30` |
| `output_action` | 문자열 | 출력 파일에서 출력을 구성 하는 방법을 나타냅니다. | `append_row`, `summary_only` | `append_row` |
| `output_file_name` | 문자열 | 일괄 처리 점수 매기기 출력 파일의 이름입니다. | | `predictions.csv` |
| `environment_variables` | 개체 | 각 일괄 처리 점수 매기기 작업에 대해 설정할 환경 변수 이름-값 쌍의 사전입니다. | | |

## <a name="remarks"></a>설명

`az ml batch-deployment`명령은 일괄 배포 Azure Machine Learning 관리 하는 데 사용할 수 있습니다.

## <a name="examples"></a>예

예제는 [GitHub 리포지토리에서](https://github.com/Azure/azureml-examples/tree/main/cli/endpoints/batch)사용할 수 있습니다. 몇 가지는 다음과 같습니다.

## <a name="yaml-basic-mlflow"></a>YAML: 기본 (MLflow)

:::code language="yaml" source="~/azureml-examples-main/cli/endpoints/batch/mlflow-deployment.yml":::

## <a name="yaml-custom-model-and-scoring-code"></a>YAML: 사용자 지정 모델 및 점수 매기기 코드

:::code language="yaml" source="~/azureml-examples-main/cli/endpoints/batch/nonmlflow-deployment.yml":::

## <a name="next-steps"></a>다음 단계

- [CLI(v2) 설치 및 사용](how-to-configure-cli.md)
