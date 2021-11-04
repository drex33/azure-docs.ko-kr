---
title: CLI (v2) batch 끝점 YAML 스키마
titleSuffix: Azure Machine Learning
description: CLI (v2) batch 끝점 YAML 스키마에 대 한 참조 설명서입니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: tracychms
ms.author: tracych
ms.date: 10/21/2021
ms.reviewer: laobri
ms.openlocfilehash: cfb475bef0cb308a8e7570a4cbad71e4e9bebb54
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/04/2021
ms.locfileid: "131570089"
---
# <a name="cli-v2-batch-endpoint-yaml-schema"></a>CLI (v2) batch 끝점 YAML 스키마

원본 JSON 스키마는 https://azuremlschemas.azureedge.net/latest/batchEndpoint.schema.json 에서 찾을 수 있습니다.

[!INCLUDE [preview disclaimer](../../includes/machine-learning-preview-generic-disclaimer.md)]

## <a name="yaml-syntax"></a>YAML 구문

| 키 | 형식 | Description | 허용되는 값 | 기본값 |
| --- | ---- | ----------- | -------------- | ------------- |
| `$schema` | 문자열 | YAML 스키마입니다. Azure Machine Learning VS Code 확장명을 사용 하 여 yaml 파일을 작성 하는 경우 `$schema` 파일의 맨 위를 비롯 하 여 스키마 및 리소스 완성을 호출할 수 있습니다. | | |
| `name` | 문자열 | **필수 사항입니다.** 엔드포인트의 이름입니다. Azure 지역 수준에서 고유해야 합니다. | | |
| `description` | 문자열 | 엔드포인트에 대한 설명입니다. | | |
| `tags` | 개체 | 끝점에 대 한 태그의 사전입니다. | | |
| `auth_mode` | 문자열 | 끝점에 대 한 인증 방법입니다. 현재는 Azure Active Directory (Azure AD) 토큰 기반 인증만 지원 됩니다. | `aad_token` | `aad_token` |
| `defaults` | 개체 | 끝점에 대 한 기본 설정입니다. | | |
| `defaults.deployment_name` | 문자열 | 끝점의 기본 배포 역할을 할 배포의 이름입니다. | | |

## <a name="remarks"></a>설명

`az ml batch-endpoint`명령은 Azure Machine Learning 끝점을 관리 하는 데 사용할 수 있습니다.

## <a name="examples"></a>예제

예제는 [GitHub 리포지토리에서](https://github.com/Azure/azureml-examples/tree/main/cli/endpoints/batch)사용할 수 있습니다. 몇 가지는 다음과 같습니다.

## <a name="yaml-basic"></a>YAML: 기본

:::code language="yaml" source="~/azureml-examples-cli-preview/cli/endpoints/batch/batch-endpoint.yml":::

## <a name="next-steps"></a>다음 단계

- [CLI(v2) 설치 및 사용](how-to-configure-cli.md)
