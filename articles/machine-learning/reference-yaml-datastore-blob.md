---
title: CLI(v2) Azure Blob 데이터 저장소 YAML 스키마
titleSuffix: Azure Machine Learning
description: CLI(v2) Azure Blob 데이터 저장소 YAML 스키마에 대한 참조 문서입니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: ynpandey
ms.author: yogipandey
ms.date: 10/21/2021
ms.reviewer: laobri
ms.openlocfilehash: dc2455212e2ab67d70dba5cce72a343f05fe2b81
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/04/2021
ms.locfileid: "131557968"
---
# <a name="cli-v2-azure-blob-datastore-yaml-schema"></a>CLI(v2) Azure Blob 데이터 저장소 YAML 스키마

원본 JSON 스키마는 https://azuremlschemas.azureedge.net/latest/azureBlob.schema.json 에서 찾을 수 있습니다.

[!INCLUDE [preview disclaimer](../../includes/machine-learning-preview-generic-disclaimer.md)]

## <a name="yaml-syntax"></a>YAML 구문

| 키 | 형식 | Description | 허용되는 값 | 기본값 |
| --- | ---- | ----------- | -------------- | ------- |
| `$schema` | 문자열 | YAML 스키마입니다. Azure Machine Learning VS Code 확장명을 사용 하 여 yaml 파일을 작성 하는 경우 `$schema` 파일의 맨 위를 비롯 하 여 스키마 및 리소스 완성을 호출할 수 있습니다. | | |
| `type` | 문자열 | **필수 사항입니다.** 데이터 저장소의 형식입니다. | `azure_blob` | |
| `name` | 문자열 | **필수 사항입니다.** 데이터 저장소의 이름입니다. | | |
| `description` | 문자열 | 데이터 저장소에 대 한 설명입니다. | | |
| `tags` | 개체 | 데이터 저장소에 대 한 태그 사전입니다. | | |
| `account_name` | 문자열 | **필수 사항입니다.** Azure storage 계정의 이름입니다. | | |
| `container_name` | 문자열 | **필수 사항입니다.** 컨테이너의 이름입니다. | | |
| `endpoint` | 문자열 | 저장소 계정 이름과을 결합 하 여 저장소 계정 끝점 URL을 만드는 데 사용 되는 저장소 서비스의 끝점 접미사입니다 `endpoint` . 저장소 계정 URL 예: `https://<storage-account-name>.blob.core.windows.net` . | | `core.windows.net` |
| `protocol` | 문자열 | 컨테이너에 연결 하는 데 사용할 프로토콜입니다. | `https`, `wasbs` | `https` |
| `credentials` | 개체 | Azure 저장소 계정에 연결 하기 위한 자격 증명 기반 인증 자격 증명입니다. 계정 키 또는 SAS (공유 액세스 서명) 토큰을 제공할 수 있습니다. 자격 증명 암호는 작업 영역 키 자격 증명 모음에 저장 됩니다. | | |
| `credentials.account_key` | 문자열 | 저장소 계정에 액세스 하기 위한 계정 키입니다. **`credentials.account_key` `credentials.sas_token` 가 지정 된 경우 또는 중 하나가 필요 `credentials` 합니다.** | | |
| `credentials.sas_token` | 문자열 | 저장소 계정에 액세스 하기 위한 SAS 토큰입니다. **`credentials.account_key` `credentials.sas_token` 가 지정 된 경우 또는 중 하나가 필요 `credentials` 합니다.** | | |

## <a name="remarks"></a>설명

`az ml datastore` 명령은 Azure Machine Learning 데이터 저장소를 관리하는 데 사용할 수 있습니다.

## <a name="examples"></a>예제

예제는 [GitHub 리포지토리에서](https://github.com/Azure/azureml-examples/tree/main/cli/resources/datastore)사용할 수 있습니다. 몇 가지는 다음과 같습니다.

## <a name="yaml-identity-based-access"></a>YAML: id 기반 액세스

:::code language="yaml" source="~/azureml-examples-cli-preview/cli/resources/datastore/blob-credless.yml":::

## <a name="yaml-account-key"></a>YAML: 계정 키

:::code language="yaml" source="~/azureml-examples-cli-preview/cli/resources/datastore/blob.yml":::

## <a name="yaml-wasbs-protocol"></a>YAML: wasbs 프로토콜

:::code language="yaml" source="~/azureml-examples-cli-preview/cli/resources/datastore/blob-protocol.yml":::

## <a name="yaml-sas-token"></a>YAML: sas 토큰

:::code language="yaml" source="~/azureml-examples-cli-preview/cli/resources/datastore/blob-sas.yml":::

## <a name="next-steps"></a>다음 단계

- [CLI(v2) 설치 및 사용](how-to-configure-cli.md)
