---
title: CLI(v2) 데이터 세트 YAML 스키마
titleSuffix: Azure Machine Learning
description: CLI(v2) 데이터 세트 YAML 스키마에 대한 참조 설명서입니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: ynpandey
ms.author: yogipandey
ms.date: 10/21/2021
ms.reviewer: laobri
ms.openlocfilehash: b92324d61316ba59f964cff97ebbb9869d01dafb
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/04/2021
ms.locfileid: "131557987"
---
# <a name="cli-v2-dataset-yaml-schema"></a>CLI(v2) 데이터 세트 YAML 스키마

원본 JSON 스키마는 https://azuremlschemas.azureedge.net/latest/dataset.schema.json 에서 찾을 수 있습니다.

[!INCLUDE [preview disclaimer](../../includes/machine-learning-preview-generic-disclaimer.md)]

## <a name="yaml-syntax"></a>YAML 구문

| 키 | 형식 | Description | 허용되는 값 |
| --- | ---- | ----------- | -------------- |
| `$schema` | 문자열 | YAML 스키마입니다. 파일의 맨 위에 있는 를 포함하여 Azure Machine Learning VS Code 확장명을 사용하여 YAML 파일을 작성하면 `$schema` 스키마 및 리소스 완성을 호출할 수 있습니다. | |
| `name` | 문자열 | **필수 사항입니다.** 데이터 세트의 이름입니다. | |
| `version` | 문자열 | 데이터 세트의 버전입니다. 생략하면 Azure ML 버전을 자동으로 생성합니다. | |
| `description` | 문자열 | 데이터 세트에 대한 설명입니다. | |
| `tags` | 개체 | 데이터 세트에 대한 태그의 사전입니다. | |
| `local_path` | 문자열 | 데이터 세트가 만들어지는 단일 로컬 파일 또는 폴더의 절대 또는 상대 경로입니다. **또는 중 하나가 `local_path` `paths` 필요합니다.** | |
| `paths` | array | 데이터 세트가 만들어지는 URI 원본의 목록입니다. 목록의 각 항목은 [데이터 세트 원본 경로](#dataset-source-path)에 정의된 스키마를 따라야 합니다. 현재 단일 원본만 지원됩니다.  **또는 중 하나가 `local_path` `paths` 필요합니다.** | |

### <a name="dataset-source-path"></a>데이터 세트 원본 경로

| 키 | 형식 | Description |
| --- | ---- | ----------- |
| `file` | 문자열 | 데이터 세트의 원본으로 사용되는 단일 파일에 대한 URI입니다. 지원되는 URI 형식은 `azureml` , , , 및 `https` `wasbs` `abfss` `adl` 입니다. URI 형식을 사용하는 방법에 대한 자세한 내용은 [Core yaml 구문을](reference-yaml-core-syntax.md) 참조하세요. `azureml://` **또는 중 하나가 `file` `folder` 필요합니다.** |
| `folder` | 문자열 | 데이터 세트의 원본으로 사용되는 폴더에 대한 URI입니다. 지원되는 URI 형식은 `azureml` , , , 및 `https` `wasbs` `abfss` `adl` 입니다. URI 형식을 사용하는 방법에 대한 자세한 내용은 [Core yaml 구문을](reference-yaml-core-syntax.md) 참조하세요. `azureml://` **또는 중 하나가 `file` `folder` 필요합니다.** |

## <a name="remarks"></a>설명

`az ml dataset`명령은 Azure Machine Learning 데이터 세트를 관리하는 데 사용할 수 있습니다.

## <a name="examples"></a>예제

예제는 [GitHub 리포지토리 예제에서](https://github.com/Azure/azureml-examples/tree/main/cli/assets/dataset)사용할 수 있습니다. 아래에는 몇 가지가 있습니다.

## <a name="yaml-datastore-file"></a>YAML: 데이터 저장소 파일

:::code language="yaml" source="~/azureml-examples-cli-preview/cli/assets/dataset/cloud-file.yml":::

## <a name="yaml-datastore-folder"></a>YAML: 데이터 저장소 폴더

:::code language="yaml" source="~/azureml-examples-cli-preview/cli/assets/dataset/cloud-folder.yml":::

## <a name="yaml-https-file"></a>YAML: https 파일

:::code language="yaml" source="~/azureml-examples-cli-preview/cli/assets/dataset/cloud-file-https.yml":::

## <a name="yaml-https-folder"></a>YAML: https 폴더

:::code language="yaml" source="~/azureml-examples-cli-preview/cli/assets/dataset/cloud-folder-https.yml":::

## <a name="yaml-wasbs-file"></a>YAML: wasbs 파일

:::code language="yaml" source="~/azureml-examples-cli-preview/cli/assets/dataset/cloud-file-wasbs.yml":::

## <a name="yaml-wasbs-folder"></a>YAML: wasbs 폴더

:::code language="yaml" source="~/azureml-examples-cli-preview/cli/assets/dataset/cloud-folder-wasbs.yml":::

## <a name="yaml-local-file"></a>YAML: 로컬 파일

:::code language="yaml" source="~/azureml-examples-cli-preview/cli/assets/dataset/local-file.yml":::

## <a name="yaml-local-folder"></a>YAML: 로컬 폴더

:::code language="yaml" source="~/azureml-examples-cli-preview/cli/assets/dataset/local-folder.yml":::

## <a name="next-steps"></a>다음 단계

- [CLI(v2) 설치 및 사용](how-to-configure-cli.md)
