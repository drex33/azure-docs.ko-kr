---
title: CLI(v2) 작업 영역 YAML 스키마
titleSuffix: Azure Machine Learning
description: CLI(v2) 작업 영역 YAML 스키마에 대한 참조 문서입니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: lostmygithubaccount
ms.author: copeters
ms.date: 10/21/2021
ms.reviewer: laobri
ms.openlocfilehash: e14e46d2301f8a87ca59ab09f4d4deb29d8c16a6
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/04/2021
ms.locfileid: "131561331"
---
# <a name="cli-v2-workspace-yaml-schema"></a>CLI(v2) 작업 영역 YAML 스키마

원본 JSON 스키마는 https://azuremlschemas.azureedge.net/latest/workspace.schema.json 에서 찾을 수 있습니다.

[!INCLUDE [preview disclaimer](../../includes/machine-learning-preview-generic-disclaimer.md)]

## <a name="yaml-syntax"></a>YAML 구문

| 키 | 형식 | Description | 허용되는 값 | 기본값 |
| --- | ---- | ----------- | -------------- | ------- |
| `$schema` | 문자열 | YAML 스키마입니다. Azure Machine Learning VS Code 확장명을 사용 하 여 yaml 파일을 작성 하는 경우 `$schema` 파일의 맨 위를 비롯 하 여 스키마 및 리소스 완성을 호출할 수 있습니다. | | |
| `name` | 문자열 | **필수 사항입니다.** 작업 영역의 이름입니다. | | |
| `display_name` | 문자열 | 스튜디오 UI의 작업 영역 표시 이름입니다. 는 리소스 그룹 내에서 고유 하지 않을 수 있습니다. | | |
| `description` | 문자열 | 작업 영역에 대 한 설명입니다. | | |
| `tags` | 개체 | 작업 영역에 대 한 태그 사전입니다. | | |
| `location` | 문자열 | 작업 영역의 위치입니다. 생략 하면 기본적으로 리소스 그룹 위치로 설정 됩니다. | | |
| `resource_group` | 문자열 | **필수 사항입니다.** 작업 영역을 포함 하는 리소스 그룹입니다. 리소스 그룹이 없는 경우 새 리소스 그룹이 생성 됩니다. | | |
| `hbi_workspace` | boolean | 고객 데이터가 중요 한 비즈니스 정보를 포함 하는 높은 비즈니스 영향 (HBI) 인지 여부 자세한 내용은 [미사용 데이터 암호화](concept-data-encryption.md#encryption-at-rest)를 참조 하세요. | | `false` |
| `storage_account` | 문자열 | 작업 영역에 대 한 기본 저장소 계정으로 사용할 기존 Azure storage 계정의 정규화 된 리소스 ID입니다. Premium storage 또는 계층적 네임 스페이스를 포함 하는 저장소 계정은 기본 저장소 계정으로 사용할 수 없습니다. 생략 하는 경우 새 저장소 계정이 생성 됩니다. | | |
| `container_registry` | 문자열 | 작업 영역에 대 한 기본 컨테이너 레지스트리로 사용할 기존 Azure container registry의 정규화 된 리소스 ID입니다. Azure ML는 Azure Container Registry (ACR)를 사용 하 여 학습 및 배포에 사용 되는 컨테이너 이미지를 관리 합니다. 생략 하면 새 컨테이너 레지스트리가 생성 됩니다. 만들기가 지연 로드 되었으므로 학습 또는 배포 중 하나에 대 한 작업에 처음으로 필요할 때 컨테이너 레지스트리가 생성 됩니다. | | |
| `key_vault` | 문자열 | 작업 영역에 대 한 기본 키 자격 증명 모음으로 사용할 기존 Azure 주요 자격 증명 모음의 정규화 된 리소스 ID입니다. 생략 하는 경우 새 키 자격 증명 모음이 생성 됩니다. | | |
| `application_insights` | 문자열 | 작업 영역에 대 한 기본 application insights로 사용할 기존 Azure application insights의 정규화 된 리소스 ID입니다. 생략 하는 경우 새 application insights가 만들어집니다. | | |
| `customer_managed_key` | 개체 | Azure Machine Learning은 Azure Cosmos DB 인스턴스에 메타데이터를 저장합니다. 기본적으로 데이터는 Microsoft 관리 키를 사용 하 여 미사용에 암호화 됩니다. 암호화에 고유한 고객 관리 키를 사용 하려면이 섹션에서 고객 관리 키 정보를 지정 합니다. 자세한 내용은 [Azure Cosmos DB에 대 한 데이터 암호화](concept-data-encryption.md#azure-cosmos-db)를 참조 하세요. | | |
| `customer_managed_key.key_vault` | 문자열 | 고객이 관리 하는 키를 포함 하는 key vault의 정규화 된 리소스 ID입니다. 이 키 자격 증명 모음은에 지정 된 기본 작업 영역 키 자격 증명 모음과 다를 수 있습니다 `key_vault` .| | |
| `customer_managed_key.key_uri` | 문자열 | 미사용 데이터를 암호화 하기 위해 고객이 관리 하는 키의 키 URI입니다. URI 형식은 `https://<keyvault-dns-name>/keys/<key-name>/<key-version>` 입니다. | | |
| `image_build_compute` | 문자열 | 컨테이너 레지스트리가 VNet 뒤에 있을 때 환경 Docker 이미지를 작성 하는 데 사용할 계산 대상의 이름입니다. 자세한 내용은 [vnet 뒤에 작업 영역 리소스 보안](how-to-secure-workspace-vnet.md#enable-azure-container-registry-acr)을 참조 하세요. | | |
| `public_network_access` | 문자열 | 작업 영역에서 개인 링크를 사용 하는 경우 공용 끝점 액세스를 허용할지 여부를 지정 합니다. 자세한 내용은 [vnet 뒤에 있을 때 공용 액세스 사용](how-to-configure-private-link.md#enable-public-access)을 참조 하세요. | `enabled`, `disabled` | `disabled` |

## <a name="remarks"></a>설명

`az ml workspace` 명령은 Azure Machine Learning 작업 영역을 관리하는 데 사용할 수 있습니다.

## <a name="examples"></a>예제

예제는 [GitHub 리포지토리에서](https://github.com/Azure/azureml-examples/tree/main/cli/resources/workspace)사용할 수 있습니다. 몇 가지는 다음과 같습니다.

## <a name="yaml-basic"></a>YAML: 기본

:::code language="yaml" source="~/azureml-examples-cli-preview/cli/resources/workspace/basic.yml":::

## <a name="yaml-with-existing-resources"></a>YAML: 기존 리소스 사용

:::code language="yaml" source="~/azureml-examples-cli-preview/cli/resources/workspace/with-existing-resources.yml":::

## <a name="yaml-customer-managed-key"></a>YAML: 고객 관리 키

:::code language="yaml" source="~/azureml-examples-cli-preview/cli/resources/workspace/cmk.yml":::

## <a name="yaml-private-link"></a>YAML: 개인 링크

:::code language="yaml" source="~/azureml-examples-cli-preview/cli/resources/workspace/privatelink.yml":::

## <a name="yaml-high-business-impact"></a>YAML: 높은 비즈니스 영향

:::code language="yaml" source="~/azureml-examples-cli-preview/cli/resources/workspace/hbi.yml":::

## <a name="next-steps"></a>다음 단계

- [CLI(v2) 설치 및 사용](how-to-configure-cli.md)
