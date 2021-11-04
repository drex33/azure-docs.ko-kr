---
title: 온라인 엔드포인트(미리 보기) YAML 참조
titleSuffix: Azure Machine Learning
description: 모델을 온라인 엔드포인트로 배포하는 데 사용되는 YAML 파일에 대해 알아봅니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
author: rsethur
ms.author: seramasu
ms.date: 10/21/2021
ms.reviewer: laobri
ms.openlocfilehash: 5b7637f16885e2eed5281273f1acad866e38e39e
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/04/2021
ms.locfileid: "131555859"
---
# <a name="cli-v2-online-endpoint-yaml-schema"></a>CLI(v2) 온라인 엔드포인트 YAML 스키마

원본 JSON 스키마는 https://azuremlschemas.azureedge.net/latest/managedOnlineEndpoint.schema.json 에서 찾을 수 있습니다.

[!INCLUDE [preview disclaimer](../../includes/machine-learning-preview-generic-disclaimer.md)]

> [!NOTE]
> 관리형 온라인 엔드포인트에 완전히 지정된 샘플 YAML을 [참조](https://azuremlschemas.azureedge.net/latest/managedOnlineEndpoint.template.yaml)용으로 사용할 수 있습니다.

## <a name="yaml-syntax"></a>YAML 구문

| 키 | 형식 | Description | 허용되는 값 | 기본값 |
| --- | ---- | ----------- | -------------- | ------------- |
| `$schema` | 문자열 | YAML 스키마입니다. Azure Machine Learning VS Code 확장명을 사용하여 파일 맨 위에 있는 를 포함하여 YAML 파일을 작성하면 `$schema` 스키마 및 리소스 완성을 호출할 수 있습니다. | | |
| `name` | 문자열 | **필수 사항입니다.** 엔드포인트의 이름입니다. Azure 지역 수준에서 고유해야 합니다. <br><br> 명명 규칙은 [여기에서](how-to-manage-quotas.md#azure-machine-learning-managed-online-endpoints-preview)정의됩니다.| | |
| `description` | 문자열 | 엔드포인트에 대한 설명입니다. | | |
| `tags` | 개체 | 엔드포인트에 대한 태그의 사전입니다. | | |
| `auth_mode` | 문자열 | 엔드포인트에 대한 인증 방법입니다. 키 기반 인증 및 Azure ML 토큰 기반 인증이 지원됩니다. 키 기반 인증은 만료되지 않지만 Azure ML 토큰 기반 인증은 만료됩니다. | `key`, `aml_token` | `key` |
| `allow_public_access` | boolean | Private Link 사용하도록 설정된 경우 공용 액세스를 허용할지 여부입니다. | | `true` |
| `identity` | 개체 | 엔드포인트 프로비저닝 및 유추를 위해 Azure 리소스에 액세스하기 위한 관리 ID 구성입니다. | | |
| `identity.type` | 문자열 | 관리 ID의 형식입니다. 형식이 `user_assigned` 이면 `identity.user_assigned_identities` 속성도 지정해야 합니다. | `system_assigned`, `user_assigned` | |
| `identity.user_assigned_identities` | array | 사용자 할당 ID의 정규화된 리소스 ID 목록입니다. | | |

## <a name="remarks"></a>설명

`az ml online-endpoint`명령은 Azure Machine Learning 온라인 엔드포인트를 관리하는 데 사용할 수 있습니다.

## <a name="examples"></a>예제

예제는 [GitHub 리포지토리 예제에서](https://github.com/Azure/azureml-examples/tree/main/cli/endpoints/batch)사용할 수 있습니다. 아래에는 몇 가지가 있습니다.

## <a name="yaml-basic"></a>YAML: basic

:::code language="yaml" source="~/azureml-examples-cli-preview/cli/endpoints/online/managed/sample/endpoint.yml":::

## <a name="yaml-system-assigned-identity"></a>YAML: 시스템 할당 ID

:::code language="yaml" source="~/azureml-examples-cli-preview/cli/endpoints/online/managed/managed-identities/1-sai-create-endpoint.yml":::

## <a name="yaml-user-assigned-identity"></a>YAML: 사용자 할당 ID

:::code language="yaml" source="~/azureml-examples-cli-preview/cli/endpoints/online/managed/managed-identities/1-uai-create-endpoint.yml":::

## <a name="next-steps"></a>다음 단계

- [CLI(v2) 설치 및 사용](how-to-configure-cli.md)
- [관리형 온라인 엔드포인트를 사용하여 모델을 배포](how-to-deploy-managed-online-endpoints.md)하는 방법 알아보기
- [관리형 온라인 엔드포인트 배포 및 채점(미리 보기) 문제 해결](./how-to-troubleshoot-online-endpoints.md)