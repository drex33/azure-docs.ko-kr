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
ms.openlocfilehash: 51db45ecaac8be0679832dde29685a6ba2909f75
ms.sourcegitcommit: 61f87d27e05547f3c22044c6aa42be8f23673256
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/09/2021
ms.locfileid: "132055760"
---
# <a name="cli-v2-model-yaml-schema"></a>CLI(v2) 모델 YAML 스키마

원본 JSON 스키마는 https://azuremlschemas.azureedge.net/latest/model.schema.json 에서 찾을 수 있습니다.

[!INCLUDE [preview disclaimer](../../includes/machine-learning-preview-generic-disclaimer.md)]

## <a name="yaml-syntax"></a>YAML 구문

| 키 | 형식 | Description | 허용되는 값 |
| --- | ---- | ----------- | -------------- |
| `$schema` | 문자열 | YAML 스키마입니다. | |
| `name` | 문자열 | **필수 사항입니다.** 모델의 이름입니다. | |
| `version` | 문자열 | 모델의 버전입니다. 생략 하면 Azure ML에서 버전을 자동으로 생성 합니다. | |
| `description` | 문자열 | 모델에 대 한 설명입니다. | |
| `tags` | 개체 | 모델에 대 한 태그 사전입니다. | |
| `local_path` | 문자열 | 모델 파일의 로컬 경로입니다. 파일이 나 디렉터리를 가리킬 수 있습니다. **또는 중 `local_path` 하나가 `model_uri` 필요 합니다.** | |
| `model_uri` | 문자열 | 모델 파일의 URI입니다. 파일이 나 디렉터리를 가리킬 수 있습니다. **또는 중 `local_path` 하나가 `model_uri` 필요 합니다.** | |
| `model_format` | 문자열 | 모델의 Storage 형식입니다. 코드 없는 배포 시나리오에 적용할 수 있습니다. | `custom`, `mlflow`, `triton`, `openai` |
| `flavors` | 개체 | 모델의 특성입니다. 각 모델 저장소 형식 유형에는 하나 이상의 지원 되는 종류가 있을 수 있습니다. 코드 없는 배포 시나리오에 적용할 수 있습니다. | |

## <a name="remarks"></a>설명

`az ml model` 명령은 Azure Machine Learning 모델을 관리하는 데 사용할 수 있습니다.

## <a name="examples"></a>예

예제는 [GitHub 리포지토리에서](https://github.com/Azure/azureml-examples/tree/main/cli/assets/model)사용할 수 있습니다. 몇 가지는 다음과 같습니다.

## <a name="yaml-local-file"></a>YAML: 로컬 파일

:::code language="yaml" source="~/azureml-examples-main/cli/assets/model/local-file.yml":::

## <a name="yaml-local-folder-in-mlflow-format"></a>YAML: MLflow 형식의 로컬 폴더

:::code language="yaml" source="~/azureml-examples-main/cli/assets/model/local-mlflow.yml":::

- [CLI(v2) 설치 및 사용](how-to-configure-cli.md)
