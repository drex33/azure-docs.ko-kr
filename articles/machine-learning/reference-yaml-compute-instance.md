---
title: CLI (v2) 계산 인스턴스 YAML 스키마
titleSuffix: Azure Machine Learning
description: CLI (v2) 계산 인스턴스 YAML 스키마에 대 한 참조 설명서입니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: lostmygithubaccount
ms.author: copeters
ms.date: 10/21/2021
ms.reviewer: laobri
ms.openlocfilehash: ff13936785dc48fc8fb0c4b26a55651f0e7f827c
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/04/2021
ms.locfileid: "131570096"
---
# <a name="cli-v2-compute-instance-yaml-schema"></a>CLI (v2) 계산 인스턴스 YAML 스키마

원본 JSON 스키마는 https://azuremlschemas.azureedge.net/latest/computeInstance.schema.json 에서 찾을 수 있습니다.

[!INCLUDE [preview disclaimer](../../includes/machine-learning-preview-generic-disclaimer.md)]

## <a name="yaml-syntax"></a>YAML 구문

| 키 | 형식 | Description | 허용되는 값 | 기본값 |
| --- | ---- | ----------- | -------------- | ------- |
| `$schema` | 문자열 | YAML 스키마입니다. Azure Machine Learning VS Code 확장명을 사용 하 여 yaml 파일을 작성 하는 경우 `$schema` 파일의 맨 위를 비롯 하 여 스키마 및 리소스 완성을 호출할 수 있습니다. | | |
| `type` | 문자열 | **필수 사항입니다.** 계산의 유형입니다. | `computeinstance` | |
| `name` | 문자열 | **필수 사항입니다.** 계산의 이름입니다. | | |
| `description` | 문자열 | 계산에 대 한 설명입니다. | | |
| `size` | 문자열 | 계산 인스턴스에 사용할 VM 크기입니다. 자세한 내용은 [지원 되는 VM 시리즈 및 크기](concept-compute-target.md#supported-vm-series-and-sizes)를 참조 하세요. 모든 지역에서 모든 크기를 사용할 수 있는 것은 아닙니다. | 지정 된 영역에서 지원 되는 크기 목록을 보려면 명령을 사용 하세요 `az ml compute list-sizes` .  | `Standard_DS3_v2` |
| `create_on_behalf_of` | 개체 | 다른 사용자를 대신 하 여 계산 인스턴스를 만들 설정. 할당 된 사용자에 게 올바른 RBAC 권한이 있는지 확인 하세요. |  |  |
| `create_on_behalf_of.user_tenant_id` | 문자열 | 할당 된 사용자의 AAD 테 넌 트 ID입니다. |  |  |
| `create_on_behalf_of.user_object_id` | 문자열 | 할당 된 사용자의 AAD 개체 ID입니다. |  |  |
| `ssh_public_access_enabled` | boolean | 계산 인스턴스에서 공용 SSH 액세스를 사용 하도록 설정할지 여부입니다. | | `false` |
| `ssh_settings` | 개체 | 계산 인스턴스에 연결 하기 위한 SSH 설정입니다. | | |
| `ssh_settings.ssh_key_value` | 문자열 | 관리자 사용자 계정의 SSH 공개 키입니다. | | |
| `network_settings` | 개체 | 네트워크 보안 설정. | | |
| `network_settings.vnet_name` | 문자열 | 새 가상 네트워크 (VNet)를 만들거나 기존 가상 네트워크를 참조 하는 경우의 이름입니다. | | |
| `network_settings.subnet` | 문자열 | 새 VNet을 만들 때 또는 기존 VNet에서 서브넷의 정규화 된 리소스 ID를 참조할 때 서브넷의 이름입니다. `network_settings.vnet_name`서브넷 ID가 지정 되었는지 지정 하지 마십시오. 서브넷 ID는 다른 리소스 그룹의 VNet/subnet을 참조할 수 있습니다. | | |

## <a name="remarks"></a>설명

이 `az ml compute` 명령은 Azure Machine Learning 계산 인스턴스를 관리 하는 데 사용할 수 있습니다.

## <a name="yaml-minimal"></a>YAML: 최소

:::code language="yaml" source="~/azureml-examples-cli-preview/cli/resources/compute/instance-minimal.yml":::

## <a name="yaml-basic"></a>YAML: 기본

:::code language="yaml" source="~/azureml-examples-cli-preview/cli/resources/compute/instance-basic.yml":::

## <a name="next-steps"></a>다음 단계

- [CLI(v2) 설치 및 사용](how-to-configure-cli.md)
