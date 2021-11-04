---
title: CLI(v2) Azure Data Lake Gen1 데이터 저장소 YAML 스키마
titleSuffix: Azure Machine Learning
description: CLI(v2) Azure Data Lake Gen1 데이터 저장소 YAML 스키마에 대한 참조 설명서입니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: ynpandey
ms.author: yogipandey
ms.date: 10/21/2021
ms.reviewer: laobri
ms.openlocfilehash: 3a0048e0bba1e4772345845dbe1505cc636aba5b
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/04/2021
ms.locfileid: "131556125"
---
# <a name="cli-v2-azure-data-lake-gen1-yaml-schema"></a>CLI(v2) Azure Data Lake Gen1 YAML 스키마

원본 JSON 스키마는 https://azuremlschemas.azureedge.net/latest/azureDataLakeGen1.schema.json 에서 찾을 수 있습니다.

[!INCLUDE [preview disclaimer](../../includes/machine-learning-preview-generic-disclaimer.md)]

## <a name="yaml-syntax"></a>YAML 구문

| 키 | 형식 | Description | 허용되는 값 | 기본값 |
| --- | ---- | ----------- | -------------- | ------- |
| `$schema` | 문자열 | YAML 스키마입니다. 파일의 맨 위에 있는 를 포함하여 Azure Machine Learning VS Code 확장명을 사용하여 YAML 파일을 작성하면 `$schema` 스키마 및 리소스 완성을 호출할 수 있습니다. | | |
| `type` | 문자열 | **필수 사항입니다.** 데이터 저장소의 형식입니다. | `azure_data_lake_gen1` | |
| `name` | 문자열 | **필수 사항입니다.** 데이터 저장소의 이름입니다. | | |
| `description` | 문자열 | 데이터 저장소에 대한 설명입니다. | | |
| `tags` | 개체 | 데이터 저장소에 대한 태그의 사전입니다. | | |
| `store_name` | 문자열 | **필수 사항입니다.** Azure Data Lake Storage Gen1 계정의 이름입니다. | | |
| `credentials` | 개체 | Azure Storage 계정에 연결하기 위한 서비스 주체 자격 증명입니다. 자격 증명 비밀은 작업 영역 키 자격 증명 모음에 저장됩니다. | | |
| `credentials.tenant_id` | 문자열 | 서비스 주체의 테넌트 ID입니다. **가 지정된 경우 `credentials` 필수입니다.** | | |
| `credentials.client_id` | 문자열 | 서비스 주체의 클라이언트 ID입니다. **가 지정된 경우 `credentials` 필수입니다.** | | |
| `credentials.client_secret` | 문자열 | 서비스 주체의 클라이언트 암호입니다. **가 지정된 경우 `credentials` 필수입니다.** | | |
| `credentials.resource_url` | 문자열 | Azure Data Lake Storage Gen1 계정에서 수행할 작업을 결정하는 리소스 URL입니다. | | `https://datalake.azure.net/` |
| `credentials.authority_url` | 문자열 | 사용자를 인증하는 데 사용되는 기관 URL입니다. | | `https://login.microsoftonline.com` |

## <a name="remarks"></a>설명

`az ml datastore` 명령은 Azure Machine Learning 데이터 저장소를 관리하는 데 사용할 수 있습니다.

## <a name="examples"></a>예제

예제는 [GitHub 리포지토리 예제에서](https://github.com/Azure/azureml-examples/tree/main/cli/resources/datastore)사용할 수 있습니다. 아래에는 몇 가지가 있습니다.

## <a name="yaml-identity-based-access"></a>YAML: ID 기반 액세스

:::code language="yaml" source="~/azureml-examples-cli-preview/cli/resources/datastore/adls-gen1-credless.yml":::

## <a name="yaml-tenant-id-client-id-client-secret"></a>YAML: 테넌트 ID, 클라이언트 ID, 클라이언트 암호

:::code language="yaml" source="~/azureml-examples-cli-preview/cli/resources/datastore/adls-gen1.yml":::

## <a name="next-steps"></a>다음 단계

- [CLI(v2) 설치 및 사용](how-to-configure-cli.md)
