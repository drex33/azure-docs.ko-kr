---
title: CLI(v2) 환경 YAML 스키마
titleSuffix: Azure Machine Learning
description: CLI(v2) 환경 YAML 스키마에 대한 참조 설명서입니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: lostmygithubaccount
ms.author: copeters
ms.date: 10/21/2021
ms.reviewer: laobri
ms.openlocfilehash: d26f25f2a007d0488b515485be5cc3bf0c9d735d
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/04/2021
ms.locfileid: "131557949"
---
# <a name="cli-v2-environment-yaml-schema"></a>CLI(v2) 환경 YAML 스키마

원본 JSON 스키마는 https://azuremlschemas.azureedge.net/latest/environment.schema.json 에서 찾을 수 있습니다.

[!INCLUDE [preview disclaimer](../../includes/machine-learning-preview-generic-disclaimer.md)]

## <a name="yaml-syntax"></a>YAML 구문

| 키 | 형식 | Description | 허용되는 값 | 기본값 |
| --- | ---- | ----------- | -------------- | ------- |
| `$schema` | 문자열 | YAML 스키마입니다. 파일의 맨 위에 있는 를 포함하여 Azure Machine Learning VS Code 확장명을 사용하여 YAML 파일을 작성하면 `$schema` 스키마 및 리소스 완성을 호출할 수 있습니다. | | |
| `name` | 문자열 | **필수 사항입니다.** 환경의 이름입니다. | | |
| `version` | 문자열 | 환경의 버전입니다. 생략하면 Azure ML 버전을 자동으로 생성합니다. | | |
| `description` | 문자열 | 환경에 대한 설명입니다. | | |
| `tags` | 개체 | 환경에 대한 태그 사전입니다. | | |
| `image` | 문자열 | 환경에 사용할 Docker 이미지입니다. **또는 중 하나가 `image` `build` 필요합니다.** | | |
| `conda_file` | 문자열 또는 개체 | conda 환경에 대한 의존성의 표준 conda YAML 구성 파일입니다. https://conda.io/projects/conda/en/latest/user-guide/tasks/manage-environments.html#creating-an-environment-file-manually을 참조하세요. <br> <br> 지정한 경우 도 `image` 지정해야 합니다. Azure ML 제공된 Docker 이미지 위에 conda 환경을 빌드합니다. | | |
| `build` | 개체 | 환경에 사용할 Docker 빌드 컨텍스트 구성입니다. **또는 중 하나가 `image` `build` 필요합니다.** | | |
| `build.local_path` | 문자열 | 빌드 컨텍스트로 사용할 디렉터리에 대한 로컬 경로입니다. | | |
| `build.dockerfile_path` | 문자열 | 빌드 컨텍스트 내의 Dockerfile에 대한 상대 경로입니다. | | `Dockerfile` |
| `os_type` | 문자열 | 운영 체제 유형입니다. | `linux`, `windows` | `linux` |  
| `inference_config` | 개체 | 유추 컨테이너 구성. 환경이 온라인 배포를 위한 서비스 컨테이너를 빌드하는 데 사용되는 경우에만 적용됩니다. [ `inference_config` 키의 특성을](#attributes-of-the-inference_config-key)참조하세요. | | |

### <a name="attributes-of-the-inference_config-key"></a>`inference_config` 키의 특성

| 키 | 형식 | 설명 |
| --- | ---- | ----------- |
| `liveness_route` | object | 서비스 컨테이너의 활동성 경로입니다. |
| `liveness_route.path` | 문자열 | 라이브성 요청을 라우팅할 경로입니다. |
| `liveness_route.port` | 정수 | 라이브성 요청을 라우팅할 포트입니다. |
| `readiness_route` | 개체 | 서비스 컨테이너에 대한 준비 경로입니다. |
| `readiness_route.path` | 문자열 | 준비 요청을 라우팅할 경로입니다. |
| `readiness_route.port` | 정수 | 준비 요청을 라우팅할 포트입니다. |
| `scoring_route` | 개체 | 서비스 컨테이너에 대한 점수 매기기 경로입니다. |
| `scoring_route.path` | 문자열 | 점수 매기기 요청을 라우팅할 경로입니다. |
| `scoring_route.port` | 정수 | 점수 매기기 요청을 라우팅할 포트입니다. |

## <a name="remarks"></a>설명

`az ml environment` 명령은 Azure Machine Learning 환경을 관리하는 데 사용될 수 있습니다.

## <a name="examples"></a>예제

예제는 [GitHub 리포지토리 예제에서](https://github.com/Azure/azureml-examples/tree/main/cli/assets/environment)사용할 수 있습니다. 아래에는 몇 가지가 있습니다.

## <a name="yaml-local-docker-build-context"></a>YAML: 로컬 Docker 빌드 컨텍스트

:::code language="yaml" source="~/azureml-examples-cli-preview/cli/assets/environment/docker-context.yml":::

## <a name="yaml-docker-image"></a>YAML: Docker 이미지

:::code language="yaml" source="~/azureml-examples-cli-preview/cli/assets/environment/docker-image.yml":::

## <a name="yaml-docker-image-plus-conda-file"></a>YAML: Docker 이미지와 conda 파일

:::code language="yaml" source="~/azureml-examples-cli-preview/cli/assets/environment/docker-image-plus-conda.yml":::

## <a name="next-steps"></a>다음 단계

- [CLI(v2) 설치 및 사용](how-to-configure-cli.md)
