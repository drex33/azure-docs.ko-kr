---
title: CLI(v2) 관리형 온라인 배포 YAML 스키마
titleSuffix: Azure Machine Learning
description: CLI(v2) 관리형 온라인 배포 YAML 스키마에 대한 참조 설명서입니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: lostmygithubaccount
ms.author: copeters
ms.date: 10/21/2021
ms.reviewer: laobri
ms.openlocfilehash: e356840d54ed156562d93b76bade6531a0b017d7
ms.sourcegitcommit: 61f87d27e05547f3c22044c6aa42be8f23673256
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/09/2021
ms.locfileid: "132060533"
---
# <a name="cli-v2-managed-online-deployment-yaml-schema"></a>CLI(v2) 관리형 온라인 배포 YAML 스키마

원본 JSON 스키마는 https://azuremlschemas.azureedge.net/latest/managedOnlineDeployment.schema.json 에서 찾을 수 있습니다.

[!INCLUDE [preview disclaimer](../../includes/machine-learning-preview-generic-disclaimer.md)]

## <a name="yaml-syntax"></a>YAML 구문

| 키 | 형식 | Description | 허용되는 값 | 기본값 |
| --- | ---- | ----------- | -------------- | ------------- |
| `$schema` | 문자열 | YAML 스키마입니다. Azure Machine Learning VS Code 확장명을 사용 하 여 yaml 파일을 작성 하는 경우 `$schema` 파일의 맨 위를 비롯 하 여 스키마 및 리소스 완성을 호출할 수 있습니다. | | |
| `name` | 문자열 | **필수 사항입니다.** 배포의 이름입니다. <br><br> 명명 규칙은 [여기](how-to-manage-quotas.md#azure-machine-learning-managed-online-endpoints-preview)에서 정의 됩니다.| | |
| `description` | 문자열 | 배포에 대 한 설명입니다. | | |
| `tags` | 개체 | 배포에 대 한 태그 사전입니다. | | |
| `endpoint_name` | 문자열 | **필수 사항입니다.** 배포를 만들 끝점의 이름입니다. | | |
| `model` | 문자열 또는 개체 | 배포에 사용할 모델입니다. 이 값은 작업 영역에 있는 기존 버전의 모델에 대 한 참조 또는 인라인 모델 사양을 사용할 수 있습니다. <br><br> 기존 모델을 참조 하려면 `azureml:<model-name>:<model-version>` 구문을 사용 합니다. <br><br> 모델 인라인을 정의 하려면 [모델 스키마](reference-yaml-model.md#yaml-syntax)를 따릅니다. <br><br> 프로덕션 시나리오에 대 한 모범 사례로 모델을 별도로 만들고 여기에서 참조 해야 합니다. <br><br> [사용자 지정 컨테이너 배포](how-to-deploy-custom-container.md) 시나리오의 경우이 필드는 선택 사항입니다.| | |
| `model_mount_path` | 문자열 | 사용자 지정 컨테이너에 모델을 탑재 하는 경로입니다. [사용자 지정 컨테이너 배포](how-to-deploy-custom-container.md) 시나리오에만 적용 됩니다. `model`필드가 지정 된 경우 컨테이너의이 경로에 탑재 됩니다. | | |
| `code_configuration` | 개체 | 점수 매기기 코드 논리에 대 한 구성입니다. <br><br> [사용자 지정 컨테이너 배포](how-to-deploy-custom-container.md) 시나리오의 경우이 필드는 선택 사항입니다. | | |
| `code_configuration.code.local_path` | 문자열 | 모델의 점수를 매기는 소스 코드 디렉터리의 로컬 경로입니다. | | |
| `code_configuration.scoring_script` | 문자열 | 소스 코드 디렉터리에 있는 점수 매기기 파일의 상대 경로입니다. | | |
| `environment` | 문자열 또는 개체 | **필수 사항입니다.** 배포에 사용할 환경입니다. 이 값은 작업 영역에서 기존 버전의 환경에 대 한 참조 또는 인라인 환경 사양의 참조일 수 있습니다. <br><br> 기존 환경을 참조 하려면 `azureml:<environment-name>:<environment-version>` 구문을 사용 합니다. <br><br> 환경을 인라인으로 정의 하려면 [환경 스키마](reference-yaml-environment.md#yaml-syntax)를 따릅니다. <br><br> 프로덕션 시나리오에 대 한 모범 사례로, 환경을 별도로 만들고 여기에서 참조 해야 합니다. | | |
| `instance_type` | 문자열 | **필수 사항입니다.** 배포에 사용할 VM 크기입니다. 지원 되는 크기 목록은 [관리 되는 온라인 끝점 SKU 목록](reference-managed-online-endpoints-vm-sku-list.md)을 참조 하세요. | | |
| `instance_count` | 정수 | **필수 사항입니다.** 배포에 사용할 인스턴스 수입니다. 원하는 워크 로드에 따라 값을 지정 합니다. 고가용성을 위해 최소한 `3` 이상으로 설정하는 것이 좋습니다. <br><br> `instance_count` 명령을 사용 하 여 배포를 만든 후에 업데이트할 수 있습니다 `az ml online-deployment update` . | | |
| `app_insights_enabled` | boolean | 작업 영역과 연결 된 Azure 애플리케이션 Insights 인스턴스와 통합을 사용할지 여부를 지정 합니다. | | `false` |
| `scale_settings` | 개체 | 배포에 대 한 크기 조정 설정입니다. 현재 `default` 크기 조정 유형만 지원 되므로이 속성을 지정할 필요가 없습니다. <br><br> 이 `default` 크기 조정 유형을 사용 하면 1) 속성을 업데이트 하 여 배포를 만든 후 수동으로 인스턴스 수를 확장 및 축소할 수 있습니다 `instance_count` . 또는 2) 자동 크기 조정 [정책을]()만듭니다. | | |
| `scale_settings.type` | 문자열 | 크기 조정 유형입니다. | `default` | `default` |
| `request_settings` | 개체 | 배포에 대 한 점수 매기기 요청 설정 구성 가능한 속성 집합에 대 한 [Requestsettings](#requestsettings) 를 참조 하세요. | | |
| `liveness_probe` | 개체 | 컨테이너의 상태를 정기적으로 모니터링 하기 위한 프로브 설정이 선거의. 구성 가능한 속성 집합은 [ProbeSettings](#probesettings) 를 참조 하세요. | | |
| `readiness_probe` | 개체 | 컨테이너가 트래픽을 처리할 준비가 되었는지 여부를 확인 하기 위한 준비 프로브 설정입니다. 구성 가능한 속성 집합은 [ProbeSettings](#probesettings) 를 참조 하세요. | | |

### <a name="requestsettings"></a>RequestSettings

| 키 | 형식 | Description | 기본값 |
| --- | ---- | ----------- | ------------- |
| `request_timeout_ms` | 정수 | 채점 시간 제한(밀리초)입니다. | `5000` |
| `max_concurrent_requests_per_instance` | 정수 | 배포에 허용 된 인스턴스당 최대 동시 요청 수입니다. <br><br> **Microsoft 기술 지원 또는 Azure ML 팀의 구성원이 지시 하지 않는 한이 설정을 기본값에서 변경 하지 마십시오.** | `1` |
| `max_queue_wait_ms` | 정수 | 요청을 큐에 유지 하는 최대 시간 (밀리초)입니다. | `500` |

### <a name="probesettings"></a>ProbeSettings

| 키 | 형식 | Description | 기본값 |
| --- | ---- | ----------- | ------------- |
| `period` | 정수 | 프로브를 수행 하는 빈도 (초)입니다. | `10` |
| `initial_delay` | 정수 | 프로브를 시작 하기 전에 컨테이너가 시작 된 이후의 시간 (초)입니다. 최 솟 값은 `1` 입니다. | `10` |
| `timeout` | 정수 | 프로브 시간이 초과 되는 시간 (초)입니다. 최 솟 값은 `1` 입니다. | `2` |
| `success_threshold` | 정수 | 실패 한 후 성공으로 간주 되는 프로브의 최소 연속 성공입니다. 최 솟 값은 `1` 입니다. | `1` |
| `failure_threshold` | 정수 | 프로브가 실패 하면 시스템은 `failure_threshold` 포기 하기 전에 시간을 시도 합니다. 선거의 프로브의 경우에는 컨테이너를 다시 시작 하는 것을 의미 합니다. 준비 프로브의 경우 컨테이너는 준비 되지 않음으로 표시 됩니다. 최 솟 값은 `1` 입니다. | `30` |

## <a name="remarks"></a>설명

이 `az ml online-deployment` 명령은 Azure Machine Learning 관리 되는 온라인 배포를 관리 하는 데 사용할 수 있습니다.

## <a name="examples"></a>예

예제는 [GitHub 리포지토리에서](https://github.com/Azure/azureml-examples/tree/main/cli/endpoints/online)사용할 수 있습니다. 몇 가지는 다음과 같습니다.

## <a name="yaml-basic"></a>YAML: 기본

:::code language="yaml" source="~/azureml-examples-main/cli/endpoints/online/managed/sample/blue-deployment.yml":::

:::code language="yaml" source="~/azureml-examples-main/cli/endpoints/online/managed/sample/green-deployment.yml":::

## <a name="yaml-system-assigned-identity"></a>YAML: 시스템 할당 id

:::code language="yaml" source="~/azureml-examples-main/cli/endpoints/online/managed/managed-identities/2-sai-deployment.yml":::

## <a name="yaml-user-assigned-identity"></a>YAML: 사용자 할당 id

:::code language="yaml" source="~/azureml-examples-main/cli/endpoints/online/managed/managed-identities/2-uai-deployment.yml":::

## <a name="next-steps"></a>다음 단계

- [CLI(v2) 설치 및 사용](how-to-configure-cli.md)
