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
ms.openlocfilehash: ea641b24b43c7ad1d22e497f9d2558ccb163e81b
ms.sourcegitcommit: 61f87d27e05547f3c22044c6aa42be8f23673256
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/09/2021
ms.locfileid: "132060022"
---
# <a name="cli-v2-workspace-yaml-schema"></a>CLI(v2) 작업 영역 YAML 스키마

원본 JSON 스키마는 https://azuremlschemas.azureedge.net/latest/workspace.schema.json 에서 찾을 수 있습니다.

[!INCLUDE [preview disclaimer](../../includes/machine-learning-preview-generic-disclaimer.md)]

## <a name="yaml-syntax"></a>YAML 구문

| 키 | 형식 | Description | 허용되는 값 | 기본값 |
| --- | ---- | ----------- | -------------- | ------- |
| `$schema` | 문자열 | YAML 스키마입니다. Azure Machine Learning VS Code 확장명을 사용하여 파일 맨 위에 있는 를 포함하여 YAML 파일을 작성하면 `$schema` 스키마 및 리소스 완성을 호출할 수 있습니다. | | |
| `name` | 문자열 | **필수 사항입니다.** 작업 영역의 이름입니다. | | |
| `display_name` | 문자열 | 스튜디오 UI에서 작업 영역의 표시 이름입니다. 리소스 그룹 내에서 고유하지 않을 수 있습니다. | | |
| `description` | 문자열 | 작업 영역에 대한 설명입니다. | | |
| `tags` | 개체 | 작업 영역에 대한 태그 사전입니다. | | |
| `location` | 문자열 | 작업 영역의 위치입니다. 생략하면 기본적으로 리소스 그룹 위치로 설정됩니다. | | |
| `resource_group` | 문자열 | **필수 사항입니다.** 작업 영역을 포함하는 리소스 그룹입니다. 리소스 그룹이 없으면 새 리소스 그룹이 만들어집니다. | | |
| `hbi_workspace` | boolean | 고객 데이터가 중요한 비즈니스 정보를 포함하는 HBI(높은 비즈니스 영향)인지 여부입니다. 자세한 내용은 [미사용 데이터 암호화 를 참조하세요.](concept-data-encryption.md#encryption-at-rest) | | `false` |
| `storage_account` | 문자열 | 작업 영역에 대한 기본 스토리지 계정으로 사용할 기존 Azure Storage 계정의 정규화된 리소스 ID입니다. Premium Storage 또는 계층 구조 네임스페이스가 있는 스토리지 계정은 기본 스토리지 계정으로 사용할 수 없습니다. 생략하면 새 스토리지 계정이 만들어집니다. | | |
| `container_registry` | 문자열 | 작업 영역에 대한 기본 컨테이너 레지스트리로 사용할 기존 Azure 컨테이너 레지스트리의 정규화된 리소스 ID입니다. Azure ML ACR(Azure Container Registry)을 사용하여 학습 및 배포에 사용되는 컨테이너 이미지를 관리합니다. 생략하면 새 컨테이너 레지스트리가 만들어집니다. 생성이 지연 로드되므로 컨테이너 레지스트리는 학습 또는 배포 작업에 처음으로 필요할 때 생성됩니다. | | |
| `key_vault` | 문자열 | 작업 영역에 대한 기본 키 자격 증명 모음으로 사용할 기존 Azure Key Vault의 정규화된 리소스 ID입니다. 생략하면 새 키 자격 증명 모음이 만들어집니다. | | |
| `application_insights` | 문자열 | 작업 영역에 대한 기본 애플리케이션 인사이트로 사용할 기존 Azure Application Insights의 정규화된 리소스 ID입니다. 생략하면 새 Application Insights가 만들어집니다. | | |
| `customer_managed_key` | 개체 | Azure Machine Learning은 Azure Cosmos DB 인스턴스에 메타데이터를 저장합니다. 기본적으로 데이터는 Microsoft 관리형 키를 사용하여 암호화됩니다. 암호화에 고유한 고객 관리형 키를 사용하려면 이 섹션에서 고객 관리형 키 정보를 지정합니다. 자세한 내용은 [Azure Cosmos DB에 대한 데이터 암호화를 참조하세요.](concept-data-encryption.md#azure-cosmos-db) | | |
| `customer_managed_key.key_vault` | 문자열 | 고객 관리형 키를 포함하는 키 자격 증명 모음의 정규화된 리소스 ID입니다. 이 키 자격 증명 모음은 에 지정된 기본 작업 영역 키 자격 증명 모음과 다를 수 `key_vault` 있습니다.| | |
| `customer_managed_key.key_uri` | 문자열 | 휴지 데이터를 암호화하는 고객 관리형 키의 키 URI입니다. URI 형식은 `https://<keyvault-dns-name>/keys/<key-name>/<key-version>` 입니다. | | |
| `image_build_compute` | 문자열 | 컨테이너 레지스트리가 VNet 뒤에 있을 때 환경 Docker 이미지를 빌드하는 데 사용할 컴퓨팅 대상의 이름입니다. 자세한 내용은 [VNet 뒤에 있는 작업 영역 리소스 보안을 참조하세요.](how-to-secure-workspace-vnet.md#enable-azure-container-registry-acr) | | |
| `public_network_access` | 문자열 | 작업 영역에서 Private Link 사용하는 경우 공용 엔드포인트 액세스가 허용되는지 여부입니다. 자세한 내용은 VNet 뒤에 있을 [때 공용 액세스 사용을 참조하세요.](how-to-configure-private-link.md#enable-public-access) | `enabled`, `disabled` | `disabled` |

## <a name="remarks"></a>설명

`az ml workspace` 명령은 Azure Machine Learning 작업 영역을 관리하는 데 사용할 수 있습니다.

## <a name="examples"></a>예

예제는 [GitHub 리포지토리 예제에서](https://github.com/Azure/azureml-examples/tree/main/cli/resources/workspace)사용할 수 있습니다. 아래에는 몇 가지가 있습니다.

## <a name="yaml-basic"></a>YAML: basic

:::code language="yaml" source="~/azureml-examples-main/cli/resources/workspace/basic.yml":::

## <a name="yaml-with-existing-resources"></a>YAML: 기존 리소스 사용

:::code language="yaml" source="~/azureml-examples-main/cli/resources/workspace/with-existing-resources.yml":::

## <a name="yaml-customer-managed-key"></a>YAML: 고객 관리형 키

:::code language="yaml" source="~/azureml-examples-main/cli/resources/workspace/cmk.yml":::

## <a name="yaml-private-link"></a>YAML: 프라이빗 링크

:::code language="yaml" source="~/azureml-examples-main/cli/resources/workspace/privatelink.yml":::

## <a name="yaml-high-business-impact"></a>YAML: 높은 비즈니스 영향

:::code language="yaml" source="~/azureml-examples-main/cli/resources/workspace/hbi.yml":::

## <a name="next-steps"></a>다음 단계

- [CLI(v2) 설치 및 사용](how-to-configure-cli.md)
