---
title: 관리형 온라인 엔드포인트(미리 보기) YAML 참조
titleSuffix: Azure Machine Learning
description: 모델을 관리형 온라인 엔드포인트로 배포하는 데 사용되는 YAML 파일에 대해 알아봅니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
author: rsethur
ms.author: seramasu
ms.date: 08/03/2021
ms.reviewer: laobri
ms.openlocfilehash: de4a9c78501fd74fa65a453b593701891e04d345
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131024261"
---
# <a name="cli-v2-managed-online-endpoint-yaml-schema"></a>CLI(v2) 관리형 온라인 엔드포인트 YAML 스키마

[!INCLUDE [preview disclaimer](../../includes/machine-learning-preview-generic-disclaimer.md)]

> [!NOTE]
> 관리형 온라인 엔드포인트에 완전히 지정된 샘플 YAML을 [참조](https://azuremlschemas.azureedge.net/latest/managedOnlineEndpoint.template.yaml)용으로 사용할 수 있습니다.

## <a name="schema"></a>스키마

원본 JSON 스키마는 https://azuremlschemas.azureedge.net/latest/managedOnlineEndpoint.schema.json 에서 찾을 수 있습니다. 스키마는 편의를 위해 JSON 및 YAML 형식으로 아래에 제공됩니다.

# <a name="json"></a>[JSON](#tab/json)

:::code language="json" source="~/azureml-examples-main/cli/.schemas/jsons/latest/managedOnlineEndpoint.schema.json":::

# <a name="yaml"></a>[YAML](#tab/yaml)

:::code language="yaml" source="~/azureml-examples-main/cli/.schemas/yamls/latest/managedOnlineEndpoint.schema.yml":::

---

## <a name="remarks"></a>설명

| 키 | 설명 |
| --- | --- |
| $schema    | \[__선택 사항__\] YAML 스키마입니다. 위 예제의 스키마를 브라우저에서 보면 YAML 파일에서 사용할 수 있는 모든 옵션을 볼 수 있습니다.|
| name       | 엔드포인트의 이름입니다. Azure 지역 수준에서 고유해야 합니다.|
| traffic | 엔드포인트에서 각 배포로 전환되는 트래픽의 비율입니다. 트래픽 값의 합은 100이어야 합니다. |
| auth_mode | 키 기반 인증에는 `key`를 사용하고 Azure Machine Learning 토큰 기반 인증에는 `aml_token`을 사용합니다. `key`는 만료되지 않지만 `aml_token`은 만료됩니다. `az ml endpoint list-keys` 명령을 사용하여 최신 토큰을 가져옵니다. |
| identity | 시스템 할당 및 사용자 할당 관리 ID를 구성하는 데 사용됩니다. |
| app_insights_enabled | `True`: Azure Machine Learning 작업 영역과 연결된 Azure AppInsights와의 통합을 지원합니다. 기본적으로 `False`입니다.
| tags | 엔드포인트와 연결할 Azure 태그의 사전입니다. |
| description | 엔드포인트에 대한 설명입니다. |
| 대상 | 이 키가 정의되어 있은 경우 엔드포인트가 관리형 온라인 엔드포인트로 배포됩니다. AKS를 사용하려면 이 키의 값을 등록된 컴퓨팅 대상의 이름(예: `target:azureml:my-aks`)으로 설정합니다. 
| 배포 | 엔드포인트에서 만들 배포의 목록을 포함합니다. 이 경우 `blue`라는 하나의 배포만 있습니다. |

### <a name="attributes-of-the-deployments-key"></a>`deployments` 키의 특성
 
| Key | 설명 |
| --- | --- |
| name  | 배포의 이름입니다. |
| model | `model: azureml:my-model:1` 형식으로 등록된 모델 버전의 이름입니다. 모델 속성을 인라인(`name`, `version` 및 `local_path`)으로 지정할 수도 있습니다. 모델 파일은 자동으로 업로드되고 등록됩니다. 인라인 사양의 단점은 모델 파일을 업데이트하려는 경우 버전을 수동으로 증분시켜야 한다는 것입니다.|
| code_configuration.code.local_path | 모델 채점을 위한 모든 Python 소스 코드가 포함된 디렉터리입니다. 중첩 디렉터리/패키지가 지원됩니다. |
| code_configuration.scoring_script | 위의 채점 디렉터리에 있는 Python 파일입니다. 이 Python 코드에는 `init()` 함수와 `run()` 함수가 있어야 합니다. `init()` 함수는 모델을 만들거나 업데이트한 후에 호출됩니다(모델을 메모리에 캐시하는 등의 용도로 사용할 수 있음). `run()` 함수는 실제 채점/예측을 수행하도록 엔드포인트를 호출할 때마다 호출됩니다. |
| environment | 모델 및 코드를 호스트할 Azure Machine Learning 환경의 세부 정보를 포함합니다. 프로덕션 환경에서는 모델 및 환경을 별도로 등록하고 YAML에 등록된 이름 및 버전을 지정하는 것이 모범 사례입니다. 예들 들어 `environment: azureml:my-env:1`입니다. |
| instance_type | 배포 인스턴스를 호스트할 VM SKU입니다. 자세한 내용은 [관리형 온라인 엔드포인트 지원 VM SKU](reference-managed-online-endpoints-vm-sku-list.md)를 참조하세요.|
| scale_settings.scale_type | 현재 이 값은 `manual`이 되어야 합니다. 엔드포인트 및 배포를 만든 후 스케일 업하거나 스케일 다운하려면 YAML에서 `instance_count`를 업데이트하고 `az ml endpoint update -n $ENDPOINT_NAME --file <yaml filepath>` 명령을 실행합니다. |
| scale_settings.instance_count | 배포의 인스턴스 수입니다. 예상되는 워크로드 값을 기준으로 합니다. 고가용성을 위해 최소한 `3` 이상으로 설정하는 것이 좋습니다. |
| scale_settings.min_instances | 항상 존재하는 최소 인스턴스 수입니다. |
| scale_settings.max_instances | 배포를 확장할 수 있는 최대 인스턴스 수입니다. 할당량은 max_instances용으로 예약됩니다. |
| request_settings.request_timeout_ms | 채점 시간 제한(밀리초)입니다. 관리형 온라인 엔드포인트의 기본값은 5000입니다. |
| request_settings.max_concurrent_requests_per_instance | 배포당 허용되는 노드당 최대 동시 요청 수입니다. 기본값은 1입니다. __Microsoft 기술 지원 또는 Azure Machine Learning 팀의 멤버가 지시하지 않는 한 이 설정을 기본값 1에서 변경하지 마세요.__ |
| request_settings.max_queue_wait_ms | 요청이 큐에 유지되는 최대 시간(밀리초)입니다. 기본값은 500입니다. |
| liveness_probe | 활동성 프로브는 컨테이너의 상태를 정기적으로 모니터링합니다. |
| liveness_probe.period | 활동성 프로브를 수행하는 빈도(초)입니다. 기본값은 10초입니다. 최솟값은 1입니다. |
| liveness_probe.initial_delay | 컨테이너가 시작된 후 활동성 프로브가 시작되기 전까지의 시간(초)입니다. 기본값은 10입니다. |
| liveness_probe.timeout | 활동성 프로브의 시간이 초과된 후 경과된 시간(초)입니다. 기본값은 2초입니다. 최솟값은 1입니다. |
| liveness_probe.failure_threshold | 시스템은 포기하기 전에 failure_threshold 시간을 시도합니다. 기본값은 30입니다. 최솟값은 1입니다. |
| liveness_probe.success_threshold | 활동성 프로브가 실패한 후 성공한 것으로 간주되는 최소 연속 성공 횟수입니다. 기본값은 1입니다. 최솟값은 1입니다. |
| readiness_probe | 준비 상태 프로브는 컨테이너가 트래픽을 제공할 준비가 되었는지 확인합니다. 속성 및 기본값은 활동성 프로브와 동일합니다. |
| tags | 배포와 연결하려는 Azure 태그의 사전입니다. |
| description | 배포에 대한 설명입니다. |

## <a name="next-steps"></a>다음 단계

- [CLI(v2) 설치 및 사용](how-to-configure-cli.md)
- [관리형 온라인 엔드포인트를 사용하여 모델을 배포](how-to-deploy-managed-online-endpoints.md)하는 방법 알아보기
- [관리형 온라인 엔드포인트 배포 및 채점(미리 보기) 문제 해결](./how-to-troubleshoot-online-endpoints.md)