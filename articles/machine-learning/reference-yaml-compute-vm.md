---
title: CLI(v2) 연결된 Virtual Machine YAML 스키마
titleSuffix: Azure Machine Learning
description: CLI(v2) 연결 Virtual Machine 스키마에 대한 참조 설명서입니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: lostmygithubaccount
ms.author: copeters
ms.date: 10/21/2021
ms.reviewer: laobri
ms.openlocfilehash: 25eb702d61b98d9c418e20763e95a948cc2cbf05
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/04/2021
ms.locfileid: "131570095"
---
# <a name="cli-v2-attached-virtual-machine-yaml-schema"></a>CLI(v2) 연결된 Virtual Machine YAML 스키마

원본 JSON 스키마는 https://azuremlschemas.azureedge.net/latest/vmCompute.schema.json 에서 찾을 수 있습니다.

[!INCLUDE [preview disclaimer](../../includes/machine-learning-preview-generic-disclaimer.md)]

## <a name="yaml-syntax"></a>YAML 구문

| 키 | 형식 | Description | 허용되는 값 | 기본값 |
| --- | ---- | ----------- | -------------- | ------- |
| `$schema` | 문자열 | YAML 스키마입니다. 파일의 맨 위에 있는 를 포함하여 Azure Machine Learning VS Code 확장명을 사용하여 YAML 파일을 작성하면 `$schema` 스키마 및 리소스 완성을 호출할 수 있습니다. | | |
| `type` | 문자열 | **필수 사항입니다.** 컴퓨팅 유형입니다. | `virtualmachine` | |
| `name` | 문자열 | **필수 사항입니다.** 컴퓨팅의 이름입니다. | | |
| `description` | 문자열 | 컴퓨팅에 대한 설명입니다. | | |
| `resource_id` | 문자열 | **필수 사항입니다.** 컴퓨팅 대상으로 작업 영역에 연결할 Azure Virtual Machine의 정규화된 리소스 ID입니다. | | |
| `ssh_settings` | 개체 | 가상 머신에 연결하기 위한 SSH 설정입니다. | | |
| `ssh_settings.admin_username` | 문자열 | 가상 머신에 SSH하는 데 사용할 수 있는 관리자 사용자 계정의 이름입니다. | | |
| `ssh_settings.admin_password` | 문자열 | 관리자 사용자 계정의 암호입니다. **또는 중 하나가 `admin_password` `ssh_private_key_file` 필요합니다.** | | |
| `ssh_settings.ssh_private_key_file` | 문자열 | 관리자 사용자 계정의 SSH 프라이빗 키 파일에 대한 로컬 경로입니다. **또는 중 하나가 `admin_password` `ssh_private_key_file` 필요합니다.** | | |
| `ssh_settings.ssh_port` | 정수 | 가상 머신의 SSH 포트입니다. | | `22` |

## <a name="remarks"></a>설명

`az ml compute`명령은 Azure Machine Learning 작업 영역에 연결된 VM(Virtual Machines)을 관리하는 데 사용할 수 있습니다.

## <a name="examples"></a>예제

예제는 [GitHub 리포지토리 예제에서](https://github.com/Azure/azureml-examples/tree/main/cli/resources/compute)사용할 수 있습니다. 아래에는 몇 가지가 있습니다.

## <a name="yaml-basic"></a>YAML: basic

:::code language="yaml" source="~/azureml-examples-cli-preview/cli/resources/compute/vm-attach.yml":::

## <a name="next-steps"></a>다음 단계

- [CLI(v2) 설치 및 사용](how-to-configure-cli.md)
