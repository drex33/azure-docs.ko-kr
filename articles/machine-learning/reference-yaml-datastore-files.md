---
title: CLI(v2) Azure Files 데이터 저장소 YAML 스키마
titleSuffix: Azure Machine Learning
description: CLI(v2) Azure Files 데이터 저장소 YAML 스키마에 대한 참조 문서입니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: ynpandey
ms.author: yogipandey
ms.date: 10/21/2021
ms.reviewer: laobri
ms.openlocfilehash: 63d1d2f6be0febea3404431b812e59aea356ac94
ms.sourcegitcommit: 61f87d27e05547f3c22044c6aa42be8f23673256
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/09/2021
ms.locfileid: "132062722"
---
# <a name="cli-v2-azure-files-datastore-yaml-schema"></a>CLI(v2) Azure Files 데이터 저장소 YAML 스키마

원본 JSON 스키마는 https://azuremlschemas.azureedge.net/latest/azureFile.schema.json 에서 찾을 수 있습니다.

[!INCLUDE [preview disclaimer](../../includes/machine-learning-preview-generic-disclaimer.md)]

## <a name="yaml-syntax"></a>YAML 구문

| 키 | 형식 | Description | 허용되는 값 | 기본값 |
| --- | ---- | ----------- | -------------- | ------- |
| `$schema` | 문자열 | YAML 스키마입니다. 파일의 맨 위에 있는 를 포함하여 Azure Machine Learning VS Code 확장명을 사용하여 YAML 파일을 작성하면 `$schema` 스키마 및 리소스 완성을 호출할 수 있습니다. | | |
| `type` | 문자열 | **필수 사항입니다.** 데이터 저장소의 형식입니다. | `azure_file` | |
| `name` | 문자열 | **필수 사항입니다.** 데이터 저장소의 이름입니다. | | |
| `description` | 문자열 | 데이터 저장소에 대한 설명입니다. | | |
| `tags` | 개체 | 데이터 저장소에 대한 태그의 사전입니다. | | |
| `account_name` | 문자열 | **필수 사항입니다.** Azure Storage 계정의 이름입니다. | | |
| `file_share_name` | 문자열 | **필수 사항입니다.** 파일 공유의 이름입니다. | | |
| `endpoint` | 문자열 | 스토리지 계정 이름과 를 결합하여 스토리지 계정 엔드포인트 URL을 만드는 데 사용되는 스토리지 서비스의 엔드포인트 접미사입니다. `endpoint` 스토리지 계정 URL 예: `https://<storage-account-name>.file.core.windows.net` . | | `core.windows.net` |
| `protocol` | 문자열 | 파일 공유에 연결하는 데 사용할 프로토콜입니다. | `https` | `https` |
| `credentials` | 개체 | Azure Storage 계정에 연결하기 위한 자격 증명 기반 인증 자격 증명입니다. 계정 키 또는 SAS(공유 액세스 서명) 토큰을 제공할 수 있습니다. 자격 증명 비밀은 작업 영역 키 자격 증명 모음에 저장됩니다. | | |
| `credentials.account_key` | 문자열 | 스토리지 계정에 액세스하기 위한 계정 키입니다. **가 지정된 경우 또는 중 하나가 `credentials.account_key` `credentials.sas_token` `credentials` 필요합니다.** | | |
| `credentials.sas_token` | 문자열 | 스토리지 계정에 액세스하기 위한 SAS 토큰입니다. **가 지정된 경우 또는 중 하나가 `credentials.account_key` `credentials.sas_token` `credentials` 필요합니다.** | | |

## <a name="remarks"></a>설명

`az ml datastore` 명령은 Azure Machine Learning 데이터 저장소를 관리하는 데 사용할 수 있습니다.

## <a name="examples"></a>예

예제는 [GitHub 리포지토리 예제에서](https://github.com/Azure/azureml-examples/tree/main/cli/resources/datastore)사용할 수 있습니다. 아래에는 몇 가지가 있습니다.

## <a name="yaml-account-key"></a>YAML: 계정 키

:::code language="yaml" source="~/azureml-examples-main/cli/resources/datastore/file.yml":::

## <a name="yaml-sas-token"></a>YAML: sas 토큰

:::code language="yaml" source="~/azureml-examples-main/cli/resources/datastore/file-sas.yml":::

## <a name="next-steps"></a>다음 단계

- [CLI(v2) 설치 및 사용](how-to-configure-cli.md)
