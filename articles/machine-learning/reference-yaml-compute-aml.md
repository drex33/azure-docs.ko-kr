---
title: CLI(v2) 컴퓨팅 클러스터(AmlCompute) YAML 스키마
titleSuffix: Azure Machine Learning
description: CLI(v2) 컴퓨팅 클러스터(AmlCompute) YAML 스키마에 대한 참조 설명서입니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: lostmygithubaccount
ms.author: copeters
ms.date: 10/21/2021
ms.reviewer: laobri
ms.openlocfilehash: ba8cdc3d4c74398f088c19ea80688e308ed4c551
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/04/2021
ms.locfileid: "131570098"
---
# <a name="cli-v2-compute-cluster-amlcompute-yaml-schema"></a>CLI(v2) 컴퓨팅 클러스터(AmlCompute) YAML 스키마

원본 JSON 스키마는 https://azuremlschemas.azureedge.net/latest/amlCompute.schema.json 에서 찾을 수 있습니다.

[!INCLUDE [preview disclaimer](../../includes/machine-learning-preview-generic-disclaimer.md)]

## <a name="yaml-syntax"></a>YAML 구문

| 키 | 형식 | Description | 허용되는 값 | 기본값 |
| --- | ---- | ----------- | -------------- | ------- |
| `$schema` | 문자열 | YAML 스키마입니다. 파일의 맨 위에 있는 를 포함하여 Azure Machine Learning VS Code 확장명을 사용하여 YAML 파일을 작성하면 `$schema` 스키마 및 리소스 완성을 호출할 수 있습니다. | | |
| `type` | 문자열 | **필수 사항입니다.** 컴퓨팅 유형입니다. | `amlcompute` | |
| `name` | 문자열 | **필수 사항입니다.** 컴퓨팅의 이름입니다. | | |
| `description` | 문자열 | 컴퓨팅에 대한 설명입니다. | | |
| `location` | 문자열 | 컴퓨팅의 위치입니다. 생략하면 기본값은 작업 영역 위치입니다. | | |
| `size` | 문자열 | 클러스터에 사용할 VM 크기입니다. 자세한 내용은 [지원되는 VM 시리즈 및 크기를 참조하세요.](concept-compute-target.md#supported-vm-series-and-sizes) 모든 지역에서 모든 크기를 사용할 수 있는 것은 아닙니다. | 지정된 지역에서 지원되는 크기 목록은 을 `az ml compute list-sizes` 사용하세요.  | `Standard_DS3_v2` |
| `tier` | 문자열 | 클러스터에 사용할 VM 우선 순위 계층입니다. 우선 순위가 낮은 VM은 선점할 수 있지만 전용 VM에 비해 비용이 절감됩니다. | `dedicated`, `low_priority` | `dedicated` |
| `min_instances` | 정수 | 클러스터에서 사용할 최소 노드 수입니다. 최소 노드 수를 로 `0` 설정하면 Azure ML 사용하지 않을 때 클러스터를 0개 노드로 자동 스케일 다운할 수 있습니다. 보다 큰 `0` 값은 클러스터가 사용되지 않는 경우에도 해당 노드 수를 계속 실행합니다. | | `0` |
| `max_instances` | 정수 | 클러스터에서 사용할 최대 노드 수입니다. | | `1` |
| `idle_time_before_scale_down` | 정수 | 클러스터를 축소하기 전의 노드 유휴 시간(초)입니다. | | `120` |
| `ssh_public_access_enabled` | boolean | 클러스터의 노드에서 공용 SSH 액세스를 사용하도록 설정할지 여부입니다. | | `false` |
| `ssh_settings` | 개체 | 클러스터에 연결하기 위한 SSH 설정입니다. | | |
| `ssh_settings.admin_username` | 문자열 | 노드에 SSH하는 데 사용할 수 있는 관리자 사용자 계정의 이름입니다. | | |
| `ssh_settings.admin_password` | 문자열 | 관리자 사용자 계정의 암호입니다. **또는 중 하나가 `admin_password` `ssh_key_value` 필요합니다.** | | |
| `ssh_settings.ssh_key_value` | 문자열 | 관리자 사용자 계정의 SSH 공개 키입니다. **또는 중 하나가 `admin_password` `ssh_key_value` 필요합니다.** | | |
| `network_settings` | 개체 | 네트워크 보안 설정. | | |
| `network_settings.vnet_name` | 문자열 | 새 가상 네트워크를 만들거나 기존 가상 네트워크를 참조할 때 VNet(가상 네트워크)의 이름입니다. | | |
| `network_settings.subnet` | 문자열 | 새 VNet을 만들거나 기존 VNet을 참조할 때 서브넷의 이름 또는 기존 VNet에 있는 서브넷의 정규화된 리소스 ID입니다. `network_settings.vnet_name`서브넷 ID가 지정되었는지 지정하지 마십시오. 서브넷 ID는 다른 리소스 그룹의 VNet/서브넷을 참조할 수 있습니다. | | |
| `identity` | 개체 | 컴퓨팅에 할당할 관리 ID 구성입니다. AmlCompute 클러스터는 동시에 하나의 시스템 할당 ID 또는 여러 사용자 할당 ID만 지원합니다. | | |
| `identity.type` | 문자열 | 컴퓨팅에 할당할 관리 ID의 형식입니다. 형식이 `user_assigned` 이면 `identity.user_assigned_identities` 속성도 지정해야 합니다. | `system_assigned`, `user_assigned` | |
| `identity.user_assigned_identities` | array | 사용자 할당 ID의 정규화된 리소스 ID 목록입니다. | | |

## <a name="remarks"></a>설명

`az ml compute`명령은 Azure Machine Learning 컴퓨팅 클러스터(AmlCompute)를 관리하는 데 사용할 수 있습니다.

## <a name="examples"></a>예제

예제는 [GitHub 리포지토리 예제에서](https://github.com/Azure/azureml-examples/tree/main/cli/resources/compute)사용할 수 있습니다. 아래에는 몇 가지가 있습니다.

## <a name="yaml-minimal"></a>YAML: 최소

:::code language="yaml" source="~/azureml-examples-cli-preview/cli/resources/compute/cluster-minimal.yml":::

## <a name="yaml-basic"></a>YAML: basic

:::code language="yaml" source="~/azureml-examples-cli-preview/cli/resources/compute/cluster-basic.yml":::

## <a name="yaml-custom-location"></a>YAML: 사용자 지정 위치

:::code language="yaml" source="~/azureml-examples-cli-preview/cli/resources/compute/cluster-location.yml":::

## <a name="yaml-low-priority"></a>YAML: 낮은 우선 순위

:::code language="yaml" source="~/azureml-examples-cli-preview/cli/resources/compute/cluster-low-priority.yml":::

## <a name="yaml-ssh-username-and-password"></a>YAML: SSH 사용자 이름 및 암호

:::code language="yaml" source="~/azureml-examples-cli-preview/cli/resources/compute/cluster-ssh-password.yml":::

## <a name="next-steps"></a>다음 단계

- [CLI(v2) 설치 및 사용](how-to-configure-cli.md)
