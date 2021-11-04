---
title: CLI(v2) 모델 YAML 스키마
titleSuffix: Azure Machine Learning
description: CLI(v2) 모델 YAML 스키마에 대한 참조 설명서입니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: lostmygithubaccount
ms.author: copeters
ms.date: 10/21/2021
ms.reviewer: laobri
ms.openlocfilehash: a9f3ac8ce87c3cec11c7bb5cb17e1004bce9f953
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/04/2021
ms.locfileid: "131556886"
---
# <a name="cli-v2-model-yaml-schema"></a>CLI(v2) 모델 YAML 스키마

원본 JSON 스키마는 https://azuremlschemas.azureedge.net/latest/model.schema.json 에서 찾을 수 있습니다.

[!INCLUDE [preview disclaimer](../../includes/machine-learning-preview-generic-disclaimer.md)]

## <a name="yaml-syntax"></a>YAML 구문

| 키 | 형식 | Description | 허용되는 값 |
| --- | ---- | ----------- | -------------- |
| `$schema` | 문자열 | YAML 스키마입니다. | |
| `name` | 문자열 | **필수 사항입니다.** 모델의 이름입니다. | |
| `version` | 문자열 | 모델의 버전입니다. 생략하면 Azure ML 버전을 자동으로 생성합니다. | |
| `description` | 문자열 | 모델에 대한 설명입니다. | |
| `tags` | 개체 | 모델에 대한 태그 사전입니다. | |
| `local_path` | 문자열 | 모델 파일의 로컬 경로입니다. 파일 또는 디렉터리를 가리킬 수 있습니다. **또는 중 하나가 `local_path` `model_uri` 필요합니다.** | |
| `model_uri` | 문자열 | 모델 파일의 URI입니다. 파일 또는 디렉터리를 가리킬 수 있습니다. **또는 중 하나가 `local_path` `model_uri` 필요합니다.** | |
| `model_format` | 문자열 | 모델의 형식을 Storage. 코드 없는 배포 시나리오에 적용됩니다. | `custom`, `mlflow`, `triton`, `openai` |
| `flavors` | 개체 | 모델의 종류입니다. 각 모델 스토리지 형식 유형에는 하나 이상의 지원되는 유형이 있을 수 있습니다. 코드 없는 배포 시나리오에 적용됩니다. | |

## <a name="remarks"></a>설명

`az ml model` 명령은 Azure Machine Learning 모델을 관리하는 데 사용할 수 있습니다.

## <a name="examples"></a>예제

예제는 [GitHub 리포지토리 예제에서](https://github.com/Azure/azureml-examples/tree/main/cli/assets/model)사용할 수 있습니다. 아래에는 몇 가지가 있습니다.

## <a name="yaml-local-file"></a>YAML: 로컬 파일

:::code language="yaml" source="~/azureml-examples-cli-preview/cli/assets/model/local-file.yml":::

## <a name="yaml-local-folder-in-mlflow-format"></a>YAML: MLflow 형식의 로컬 폴더

:::code language="yaml" source="~/azureml-examples-cli-preview/cli/assets/model/local-mlflow.yml":::

- [CLI(v2) 설치 및 사용](how-to-configure-cli.md)
