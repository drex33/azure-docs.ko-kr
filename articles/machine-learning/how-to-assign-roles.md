---
title: 작업 영역에서 역할 관리
titleSuffix: Azure Machine Learning
description: Azure RBAC(역할 기반 액세스 제어)를 사용하여 Azure Machine Learning 작업 영역에 액세스하는 방법에 대해 알아봅니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
ms.reviewer: Blackmist
ms.author: johwu
author: johnwu0604
ms.date: 03/26/2021
ms.custom: how-to, seodec18, devx-track-azurecli, contperf-fy21q2
ms.openlocfilehash: 51846db6f54baaec89a6a9a1164e2d5001c9dbb9
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128679636"
---
# <a name="manage-access-to-an-azure-machine-learning-workspace"></a>Azure Machine Learning 작업 영역에 대한 액세스 관리

이 문서에서는 Azure Machine Learning 작업 영역에 대한 액세스(권한 부여)를 관리하는 방법에 대해 알아봅니다. [Azure RBAC(Azure 역할 기반 액세스 제어)](../role-based-access-control/overview.md)는 새 리소스를 만들거나 기존 리소스를 사용하는 기능과 같은 Azure 리소스에 대한 액세스를 관리하는 데 사용됩니다. Azure AD(Azure Active Directory)의 사용자에게는 리소스에 대한 액세스 권한을 부여하는 특정 역할이 할당됩니다. Azure는 기본 제공 역할과 사용자 지정 역할을 만드는 기능을 모두 제공합니다.

> [!TIP]
> 이 문서에서는 Azure Machine Learning을 집중적으로 다루지만 Azure ML에서 사용하는 개별 서비스는 자체 RBAC 설정을 제공합니다. 예를 들어 이 문서의 정보를 사용하여 누가 Azure Kubernetes Service의 웹 서비스로 배포된 모델에 채점 요청을 제출할 수 있는지 구성할 수 있습니다. 그러나 Azure Kubernetes Service는 자체 Azure 역할 세트를 제공합니다. Azure Machine Learning에 유용할 수 있는 서비스별 RBAC 정보는 다음 링크를 참조하세요.
>
> * [Azure Kubernetes 클러스터 리소스에 대한 액세스 제어](../aks/azure-ad-rbac.md)
> * [Kubernetes 권한 부여에 Azure RBAC 사용](../aks/manage-azure-rbac.md)
> * [Blob 데이터에 액세스하기 위해 Azure RBAC 사용](../storage/blobs/assign-azure-role-data-access.md)

> [!WARNING]
> 일부 역할을 적용하면 다른 사용자에 대해 Azure Machine Learning 스튜디오의 UI 기능이 제한될 수 있습니다. 예를 들어 사용자의 역할에 컴퓨팅 인스턴스를 만들 수 없는 경우 스튜디오에서 컴퓨팅 인스턴스를 생성하는 옵션을 사용할 수 없습니다. 이 동작은 예상된 것이며, 사용자가 액세스 거부 오류를 반환하는 작업을 시도하지 못하게 합니다.

## <a name="default-roles"></a>기본 역할

Azure Machine Learning 작업 영역에는 기본적으로 사용할 수 있는 네 가지 기본 제공 역할이 있습니다. 작업 영역에 사용자를 추가할 때 아래에 설명된 기본 제공 역할 중 하나를 할당할 수 있습니다.

| 역할 | 액세스 수준 |
| --- | --- |
| **AzureML 데이터 과학자** | 컴퓨팅 리소스를 만들거나 삭제하고 작업 영역 자체를 수정하는 것을 제외하고 Azure Machine Learning 작업 영역에서 모든 작업을 수행할 수 있습니다. |
| **판독기** | 작업 영역의 읽기 전용 작업입니다. 읽기 권한자는 작업 영역에서 [데이터 저장소](how-to-access-data.md) 자격 증명을 포함한 자산을 나열하고 볼 수 있습니다. 읽기 권한자는 이러한 자산을 만들거나 업데이트할 수 없습니다. |
| **기여자** | 작업 영역에서 해당하는 자산을 보거나, 만들거나, 편집하거나, 삭제합니다. 예를 들어 기여자는 실험을 만들고, 컴퓨팅 클러스터를 만들거나 연결하고, 실행을 제출하고, 웹 서비스를 배포할 수 있습니다. |
| **소유자** | 작업 영역에 대한 모든 액세스 권한(작업 영역에서 해당하는 자산을 보거나, 만들거나, 편집하거나, 삭제하는 기능 포함)입니다. 또한 역할 할당을 변경할 수 있습니다. |

> [!IMPORTANT]
> Azure에서 여러 수준으로 역할 액세스의 범위를 지정할 수 있습니다. 예를 들어 작업 영역에 대한 소유자 액세스 권한이 있는 사용자에게 작업 영역을 포함하는 리소스 그룹에 대한 소유자 액세스 권한이 없을 수 있습니다. 자세한 내용은 [Azure RBAC 작동 방식](../role-based-access-control/overview.md#how-azure-rbac-works)을 참조하세요.


## <a name="manage-workspace-access"></a>작업 영역 액세스 관리

작업 영역의 소유자인 경우 작업 영역에 대한 역할을 추가하고 제거할 수 있습니다. 또한 사용자에게 역할을 할당할 수 있습니다. 다음 링크를 사용하여 액세스를 관리하는 방법을 알아봅니다.
- [Azure Portal UI](../role-based-access-control/role-assignments-portal.md)
- [PowerShell](../role-based-access-control/role-assignments-powershell.md)
- [Azure CLI](../role-based-access-control/role-assignments-cli.md)
- [REST API](../role-based-access-control/role-assignments-rest.md)
- [Azure 리소스 관리자 템플릿](../role-based-access-control/role-assignments-template.md)

## <a name="create-custom-role"></a>사용자 지정 역할 만들기

기본 제공 역할이 충분하지 않은 경우 사용자 지정 역할을 만들 수 있습니다. 사용자 지정 역할에는 해당 작업 영역에서 읽기, 쓰기, 삭제 및 컴퓨팅 리소스 권한이 있을 수 있습니다. 특정 작업 영역 수준, 특정 리소스 그룹 수준 또는 특정 구독 수준에서 역할을 사용하도록 설정할 수 있습니다.

> [!NOTE]
> 해당 리소스 내에서 사용자 지정 역할을 만들려면 해당 수준의 리소스 소유자여야 합니다.

사용자 지정 역할을 만들려면 먼저 역할에 대한 권한과 범위를 지정하는 역할 정의 JSON 파일을 생성합니다. 다음 예제에서는 특정 작업 영역 수준에서 범위가 지정된 "데이터 과학자 사용자 지정"이라는 사용자 지정 역할을 정의합니다.

`data_scientist_custom_role.json` :
```json
{
    "Name": "Data Scientist Custom",
    "IsCustom": true,
    "Description": "Can run experiment but can't create or delete compute.",
    "Actions": ["*"],
    "NotActions": [
        "Microsoft.MachineLearningServices/workspaces/*/delete",
        "Microsoft.MachineLearningServices/workspaces/write",
        "Microsoft.MachineLearningServices/workspaces/computes/*/write",
        "Microsoft.MachineLearningServices/workspaces/computes/*/delete", 
        "Microsoft.Authorization/*/write"
    ],
    "AssignableScopes": [
        "/subscriptions/<subscription_id>/resourceGroups/<resource_group_name>/providers/Microsoft.MachineLearningServices/workspaces/<workspace_name>"
    ]
}
```

> [!TIP]
> `AssignableScopes` 필드를 변경하여 구독 수준, 리소스 그룹 수준 또는 특정 작업 영역 수준에서 이 사용자 지정 역할의 범위를 설정할 수 있습니다.
> 위의 사용자 지정 역할은 예일 뿐입니다. [Azure Machine Learning Service에 대해 제안된 몇 가지 사용자 지정 역할](#customroles)을 참조하세요.

이 사용자 지정 역할은 다음 작업을 제외하고 작업 영역에서 모든 작업을 수행할 수 있습니다.

- 컴퓨팅 리소스를 만들거나 업데이트할 수 없습니다.
- 컴퓨팅 리소스를 삭제할 수 없습니다.
- 역할 할당을 추가, 삭제 또는 변경할 수 없습니다.
- 작업 영역을 삭제할 수 없습니다.

이 사용자 지정 역할을 배포하려면 다음 Azure CLI 명령을 사용합니다.

```azurecli-interactive 
az role definition create --role-definition data_scientist_role.json
```

배포 후 지정된 작업 영역에서 이 역할을 사용할 수 있게 됩니다. 이제 Azure Portal에서 이 역할을 추가하고 할당할 수 있습니다.

사용자 지정 역할에 대한 자세한 내용은 [Azure 사용자 지정 역할](../role-based-access-control/custom-roles.md)을 참조하세요. 

### <a name="azure-machine-learning-operations"></a>Azure Machine Learning 작업

사용자 지정 역할과 함께 사용할 수 있는 작업(작업(Actions) 및 작업 안 함(NotActions))에 대한 자세한 내용은 [리소스 공급자 작업](../role-based-access-control/resource-provider-operations.md#microsoftmachinelearningservices)을 참조하세요. 다음 Azure CLI 명령을 사용하여 작업을 나열할 수도 있습니다.

```azurecli-interactive
az provider operation show –n Microsoft.MachineLearningServices
```

## <a name="list-custom-roles"></a>사용자 지정 역할 나열

Azure CLI에서 다음 명령을 실행합니다.

```azurecli-interactive
az role definition list --subscription <sub-id> --custom-role-only true
```

특정 사용자 지정 역할에 대한 역할 정의를 보려면 다음 Azure CLI 명령을 사용합니다. `<role-name>`은 위의 명령에서 반환된 것과 동일한 형식이어야 합니다.

```azurecli-interactive
az role definition list -n <role-name> --subscription <sub-id>
```

## <a name="update-a-custom-role"></a>사용자 지정 역할 업데이트

Azure CLI에서 다음 명령을 실행합니다.

```azurecli-interactive
az role definition update --role-definition update_def.json --subscription <sub-id>
```

새 역할 정의의 전체 범위에 대한 권한이 있어야 합니다. 예를 들어 3개의 구독이 이 새 역할의 범위에 해당하는 경우 3개의 구독 모두에 대한 권한이 있어야 합니다. 

> [!NOTE]
> 역할 업데이트는 해당 범위의 모든 역할 할당에 적용되는 데 15분~1시간 정도 걸릴 수 있습니다.

## <a name="use-azure-resource-manager-templates-for-repeatability"></a>반복성을 위해 Azure Resource Manager 템플릿 사용

복잡한 역할 할당을 다시 만들어야 할 것으로 예상되는 경우 Azure Resource Manager 템플릿이 큰 도움이 될 수 있습니다. [machine-learning-dependencies-role-assignment 템플릿](https://github.com/Azure/azure-quickstart-templates/tree/master//quickstarts/microsoft.machinelearningservices/machine-learning-dependencies-role-assignment)은 재사용을 위해 소스 코드에서 역할 할당을 지정하는 방법을 보여줍니다. 

## <a name="common-scenarios"></a>일반적인 시나리오

다음 표에는 Azure Machine Learning 작업과 최소 범위에서 이를 수행하는 데 필요한 권한이 요약되어 있습니다. 예를 들어 작업 영역 범위(열 4)로 작업을 수행할 수 있는 경우 해당 권한이 있는 모든 상위 범위도 자동으로 작동합니다.

> [!IMPORTANT]
> 이 표에서 `/`로 시작하는 모든 경로는 `Microsoft.MachineLearningServices/`에 대한 **상대 경로** 입니다.

| 활동 | 구독 수준 범위 | 리소스 그룹 수준 범위 | 작업 영역 수준 범위 |
| ----- | ----- | ----- | ----- |
| 새 작업 영역 만들기 | 필요 없음 | 소유자 또는 기여자 | 해당 없음(소유자가 되거나 생성 후 더 높은 범위 역할을 상속함) |
| 구독 수준 Amlcompute 할당량 요청 또는 작업 영역 수준 할당량 설정 | 소유자, 기여자 또는 사용자 지정 역할 </br>구독 범위에서 `/locations/updateQuotas/action`</br> 허용 | 권한 없음 | 권한 없음 |
| 새 컴퓨팅 클러스터 만들기 | 필요 없음 | 필요 없음 | 다음을 허용하는 소유자, 기여자 또는 사용자 지정 역할: `/workspaces/computes/write` |
| 새 컴퓨팅 인스턴스 만들기 | 필요 없음 | 필요 없음 | 다음을 허용하는 소유자, 기여자 또는 사용자 지정 역할: `/workspaces/computes/write` |
| 모든 형식의 실행 제출 | 필요 없음 | 필요 없음 | 다음을 허용하는 소유자, 기여자 또는 사용자 지정 역할: `"/workspaces/*/read", "/workspaces/environments/write", "/workspaces/experiments/runs/write", "/workspaces/metadata/artifacts/write", "/workspaces/metadata/snapshots/write", "/workspaces/environments/build/action", "/workspaces/experiments/runs/submit/action", "/workspaces/environments/readSecrets/action"` |
| 파이프라인 및 엔드포인트 게시 | 필요 없음 | 필요 없음 | 다음을 허용하는 소유자, 기여자 또는 사용자 지정 역할: `"/workspaces/endpoints/pipelines/*", "/workspaces/pipelinedrafts/*", "/workspaces/modules/*"` |
| AKS/ACI 리소스에 등록된 모델 배포 | 필요 없음 | 필요 없음 | 다음을 허용하는 소유자, 기여자 또는 사용자 지정 역할: `"/workspaces/services/aks/write", "/workspaces/services/aci/write"` |
| 배포된 AKS 엔드포인트에 대한 점수 매기기 | 필요 없음 | 필요 없음 | 다음을 허용하는 소유자, 기여자 또는 사용자 지정 역할: `"/workspaces/services/aks/score/action", "/workspaces/services/aks/listkeys/action"`(Azure Active Directory 인증을 사용하지 않는 경우) 또는 `"/workspaces/read"`(토큰 인증을 사용하는 경우) |
| 대화형 Notebook을 사용하여 스토리지에 액세스 | 필요 없음 | 필요 없음 | 다음을 허용하는 소유자, 기여자 또는 사용자 지정 역할: `"/workspaces/computes/read", "/workspaces/notebooks/samples/read", "/workspaces/notebooks/storage/*", "/workspaces/listStorageAccountKeys/action"` |
| 새 사용자 지정 역할 만들기 | 다음을 허용하는 소유자, 기여자 또는 사용자 지정 역할: `Microsoft.Authorization/roleDefinitions/write` | 필요 없음 | 다음을 허용하는 소유자, 기여자 또는 사용자 지정 역할: `/workspaces/computes/write` |

> [!TIP]
> 처음 작업 영역 만들기를 시도할 때 오류가 발생하면 역할이 `Microsoft.MachineLearningServices/register/action`을 허용하는지 확인합니다. 이 작업을 통해 Azure 구독에 Azure Machine Learning 리소스 공급자를 등록할 수 있습니다.

### <a name="user-assigned-managed-identity-with-azure-ml-compute-cluster"></a>Azure ML 컴퓨팅 클러스터를 통해 사용자가 할당한 관리 ID

사용자 할당 ID를 Azure Machine Learning 컴퓨팅 클러스터에 할당하려면 컴퓨팅 및 [관리 ID 운영자 역할](../role-based-access-control/built-in-roles.md#managed-identity-operator)을 만들기 위한 쓰기 권한이 필요합니다. 관리 ID가 있는 Azure RBAC에 대한 자세한 내용은 [사용자 할당 ID를 관리하는 방법](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md)을 참조하세요.

### <a name="mlflow-operations"></a>MLflow 작업

Azure Machine Learning 작업 영역에서 MLflow 작업을 수행하려면 사용자 지정 역할의 다음 범위를 사용합니다.

| MLflow 작업 | Scope |
| --- | --- |
| 작업 영역 추적 저장소의 모든 실험 나열, ID로 실험 가져오기, 이름으로 실험 가져오기 | `Microsoft.MachineLearningServices/workspaces/experiments/read` |
| 이름으로 실험을 만들기, 실험에서 태그 설정, 삭제하도록 표시된 실험 복원| `Microsoft.MachineLearningServices/workspaces/experiments/write` | 
| 실험 삭제 | `Microsoft.MachineLearningServices/workspaces/experiments/delete` |
| 실행과 관련 데이터 및 메타데이터 가져오기, 지정한 실행에 대해 지정된 메트릭의 모든 값 목록 가져오기, 실행에 대한 아티팩트 나열 | `Microsoft.MachineLearningServices/workspaces/experiments/runs/read` |
| 실험 내에서 새 실행 만들기, 실행 삭제, 삭제된 실행 복원, 현재 실행에서 메트릭 로깅, 실행에 대한 태그 설정, 실행에 대한 태그 삭제, 실행에 사용된 매개 변수(키-값 쌍) 기록, 실행에 대한 메트릭, 매개 변수 및 태그 배치 기록, 실행 상태 업데이트 | `Microsoft.MachineLearningServices/workspaces/experiments/runs/write` |
| 이름으로 등록된 모델 가져오기, 레지스트리에 등록된 모든 모델 목록 가져오기, 등록된 모델 검색, 각 요청 단계에 대한 최신 버전 모델, 등록된 모델 버전 가져오기, 모델 버전 검색, 모델 버전의 아티팩트가 저장되는 URI 가져오기, 실험 ID로 실행 검색 | `Microsoft.MachineLearningServices/workspaces/models/read` |
| 등록된 새 모델 만들기, 등록된 모델의 이름/설명 업데이트, 등록된 기존 모델 이름 바꾸기, 모델의 새 버전 만들기, 모델 버전의 설명 업데이트, 단계 중 하나로 등록된 모델 전환 | `Microsoft.MachineLearningServices/workspaces/models/write` |
| 모든 버전과 함께 등록된 모델 삭제, 등록된 모델의 특정 버전 삭제 | `Microsoft.MachineLearningServices/workspaces/models/delete` |

<a id="customroles"></a>

## <a name="example-custom-roles"></a>사용자 지정 역할 예제

### <a name="data-scientist"></a>데이터 과학자

데이터 과학자가 작업 영역 내에서 다음을 **제외한** 모든 작업을 수행할 수 있습니다.

* 컴퓨팅 만들기
* 프로덕션 AKS 클러스터에 모델 배포
* 프로덕션에서 파이프라인 엔드포인트 배포

`data_scientist_custom_role.json` :
```json
{
    "Name": "Data Scientist Custom",
    "IsCustom": true,
    "Description": "Can run experiment but can't create or delete compute or deploy production endpoints.",
    "Actions": [
        "Microsoft.MachineLearningServices/workspaces/*/read",
        "Microsoft.MachineLearningServices/workspaces/*/action",
        "Microsoft.MachineLearningServices/workspaces/*/delete",
        "Microsoft.MachineLearningServices/workspaces/*/write"
    ],
    "NotActions": [
        "Microsoft.MachineLearningServices/workspaces/delete",
        "Microsoft.MachineLearningServices/workspaces/write",
        "Microsoft.MachineLearningServices/workspaces/computes/*/write",
        "Microsoft.MachineLearningServices/workspaces/computes/*/delete", 
        "Microsoft.Authorization/*",
        "Microsoft.MachineLearningServices/workspaces/computes/listKeys/action",
        "Microsoft.MachineLearningServices/workspaces/listKeys/action",
        "Microsoft.MachineLearningServices/workspaces/services/aks/write",
        "Microsoft.MachineLearningServices/workspaces/services/aks/delete",
        "Microsoft.MachineLearningServices/workspaces/endpoints/pipelines/write"
    ],
    "AssignableScopes": [
        "/subscriptions/<subscription_id>"
    ]
}
```

### <a name="data-scientist-restricted"></a>데이터 과학자 제한됨

허용된 작업에 와일드카드가 없는 보다 제한된 역할 정의. 작업 영역 내에서 다음을 **제외한** 모든 작업을 수행할 수 있습니다.

* 컴퓨팅 만들기
* 프로덕션 AKS 클러스터에 모델 배포
* 프로덕션에서 파이프라인 엔드포인트 배포

`data_scientist_restricted_custom_role.json` :
```json
{
    "Name": "Data Scientist Restricted Custom",
    "IsCustom": true,
    "Description": "Can run experiment but can't create or delete compute or deploy production endpoints",
    "Actions": [
        "Microsoft.MachineLearningServices/workspaces/*/read",
        "Microsoft.MachineLearningServices/workspaces/computes/start/action",
        "Microsoft.MachineLearningServices/workspaces/computes/stop/action",
        "Microsoft.MachineLearningServices/workspaces/computes/restart/action",
        "Microsoft.MachineLearningServices/workspaces/computes/applicationaccess/action",
        "Microsoft.MachineLearningServices/workspaces/notebooks/storage/read",
        "Microsoft.MachineLearningServices/workspaces/notebooks/storage/write",
        "Microsoft.MachineLearningServices/workspaces/notebooks/storage/delete",
        "Microsoft.MachineLearningServices/workspaces/notebooks/samples/read",
        "Microsoft.MachineLearningServices/workspaces/experiments/runs/write",
        "Microsoft.MachineLearningServices/workspaces/experiments/write",
        "Microsoft.MachineLearningServices/workspaces/experiments/runs/submit/action",
        "Microsoft.MachineLearningServices/workspaces/pipelinedrafts/write",
        "Microsoft.MachineLearningServices/workspaces/metadata/snapshots/write",
        "Microsoft.MachineLearningServices/workspaces/metadata/artifacts/write",
        "Microsoft.MachineLearningServices/workspaces/environments/write",
        "Microsoft.MachineLearningServices/workspaces/models/write",
        "Microsoft.MachineLearningServices/workspaces/modules/write",
        "Microsoft.MachineLearningServices/workspaces/datasets/registered/write", 
        "Microsoft.MachineLearningServices/workspaces/datasets/registered/delete",
        "Microsoft.MachineLearningServices/workspaces/datasets/unregistered/write",
        "Microsoft.MachineLearningServices/workspaces/datasets/unregistered/delete",
        "Microsoft.MachineLearningServices/workspaces/computes/listNodes/action",
        "Microsoft.MachineLearningServices/workspaces/environments/build/action"
    ],
    "NotActions": [
        "Microsoft.MachineLearningServices/workspaces/computes/write",
        "Microsoft.MachineLearningServices/workspaces/write",
        "Microsoft.MachineLearningServices/workspaces/computes/delete",
        "Microsoft.MachineLearningServices/workspaces/delete",
        "Microsoft.MachineLearningServices/workspaces/computes/listKeys/action",
        "Microsoft.MachineLearningServices/workspaces/listKeys/action",
        "Microsoft.Authorization/*",
        "Microsoft.MachineLearningServices/workspaces/datasets/registered/profile/read",
        "Microsoft.MachineLearningServices/workspaces/datasets/registered/preview/read",
        "Microsoft.MachineLearningServices/workspaces/datasets/unregistered/profile/read",
        "Microsoft.MachineLearningServices/workspaces/datasets/unregistered/preview/read",
        "Microsoft.MachineLearningServices/workspaces/datasets/registered/schema/read",    
        "Microsoft.MachineLearningServices/workspaces/datasets/unregistered/schema/read",
        "Microsoft.MachineLearningServices/workspaces/datastores/write",
        "Microsoft.MachineLearningServices/workspaces/datastores/delete"
    ],
    "AssignableScopes": [
        "/subscriptions/<subscription_id>"
    ]
}
```
     
### <a name="mlflow-data-scientist"></a>MLflow 데이터 과학자

데이터 과학자가 다음을 **제외한** 모든 MLflow AzureML 지원 작업을 수행할 수 있습니다.

* 컴퓨팅 만들기
* 프로덕션 AKS 클러스터에 모델 배포
* 프로덕션에서 파이프라인 엔드포인트 배포

`mlflow_data_scientist_custom_role.json` :
```json
{
    "Name": "MLFlow Data Scientist Custom",
    "IsCustom": true,
    "Description": "Can perform azureml mlflow integrated functionalities that includes mlflow tracking, projects, model registry",
    "Actions": [
        "Microsoft.MachineLearningServices/workspaces/experiments/read",
        "Microsoft.MachineLearningServices/workspaces/experiments/write",
        "Microsoft.MachineLearningServices/workspaces/experiments/delete",
        "Microsoft.MachineLearningServices/workspaces/experiments/runs/read",
        "Microsoft.MachineLearningServices/workspaces/experiments/runs/write",
        "Microsoft.MachineLearningServices/workspaces/models/read",
        "Microsoft.MachineLearningServices/workspaces/models/write",
        "Microsoft.MachineLearningServices/workspaces/models/delete"
    ],
    "NotActions": [
        "Microsoft.MachineLearningServices/workspaces/delete",
        "Microsoft.MachineLearningServices/workspaces/write",
        "Microsoft.MachineLearningServices/workspaces/computes/*/write",
        "Microsoft.MachineLearningServices/workspaces/computes/*/delete", 
        "Microsoft.Authorization/*",
        "Microsoft.MachineLearningServices/workspaces/computes/listKeys/action",
        "Microsoft.MachineLearningServices/workspaces/listKeys/action",
        "Microsoft.MachineLearningServices/workspaces/services/aks/write",
        "Microsoft.MachineLearningServices/workspaces/services/aks/delete",
        "Microsoft.MachineLearningServices/workspaces/endpoints/pipelines/write"
    ],
    "AssignableScopes": [
        "/subscriptions/<subscription_id>"
    ]
}
```   

### <a name="mlops"></a>MLOps

서비스 주체에 역할을 할당하고 이를 사용하여 MLOps 파이프라인을 자동화할 수 있습니다. 이미 게시된 파이프라인에 대해 실행을 제출하려는 경우를 예로 들 수 있습니다.

`mlops_custom_role.json` :
```json
{
    "Name": "MLOps Custom",
    "IsCustom": true,
    "Description": "Can run pipelines against a published pipeline endpoint",
    "Actions": [
        "Microsoft.MachineLearningServices/workspaces/read",
        "Microsoft.MachineLearningServices/workspaces/endpoints/pipelines/read",
        "Microsoft.MachineLearningServices/workspaces/metadata/artifacts/read",
        "Microsoft.MachineLearningServices/workspaces/metadata/snapshots/read",
        "Microsoft.MachineLearningServices/workspaces/environments/read",    
        "Microsoft.MachineLearningServices/workspaces/metadata/secrets/read",
        "Microsoft.MachineLearningServices/workspaces/modules/read",
        "Microsoft.MachineLearningServices/workspaces/experiments/runs/read",
        "Microsoft.MachineLearningServices/workspaces/datasets/registered/read",
        "Microsoft.MachineLearningServices/workspaces/datastores/read",
        "Microsoft.MachineLearningServices/workspaces/environments/write",
        "Microsoft.MachineLearningServices/workspaces/experiments/runs/write",
        "Microsoft.MachineLearningServices/workspaces/metadata/artifacts/write",
        "Microsoft.MachineLearningServices/workspaces/metadata/snapshots/write",
        "Microsoft.MachineLearningServices/workspaces/environments/build/action",
        "Microsoft.MachineLearningServices/workspaces/experiments/runs/submit/action"
    ],
    "NotActions": [
        "Microsoft.MachineLearningServices/workspaces/computes/write",
        "Microsoft.MachineLearningServices/workspaces/write",
        "Microsoft.MachineLearningServices/workspaces/computes/delete",
        "Microsoft.MachineLearningServices/workspaces/delete",
        "Microsoft.MachineLearningServices/workspaces/computes/listKeys/action",
        "Microsoft.MachineLearningServices/workspaces/listKeys/action",
        "Microsoft.Authorization/*"
    ],
    "AssignableScopes": [
        "/subscriptions/<subscription_id>"
    ]
}
```

### <a name="workspace-admin"></a>작업 영역 관리자

작업 영역 범위 내에서 다음을 **제외한** 모든 작업을 수행할 수 있습니다.

* 새 작업 영역 만들기
* 구독 또는 작업 영역 수준 할당량 할당

또한 작업 영역 관리자는 새 역할을 만들 수 없습니다. 작업 영역 범위 내에서 기존 기본 제공 또는 사용자 지정 역할만 할당할 수 있습니다.

`workspace_admin_custom_role.json` :
```json
{
    "Name": "Workspace Admin Custom",
    "IsCustom": true,
    "Description": "Can perform all operations except quota management and upgrades",
    "Actions": [
        "Microsoft.MachineLearningServices/workspaces/*/read",
        "Microsoft.MachineLearningServices/workspaces/*/action",
        "Microsoft.MachineLearningServices/workspaces/*/write",
        "Microsoft.MachineLearningServices/workspaces/*/delete",
        "Microsoft.Authorization/roleAssignments/*"
    ],
    "NotActions": [
        "Microsoft.MachineLearningServices/workspaces/write"
    ],
    "AssignableScopes": [
        "/subscriptions/<subscription_id>"
    ]
}
```

<a name="labeler"></a>
### <a name="data-labeler"></a>데이터 레이블 지정자

데이터 레이블 지정으로만 범위가 지정된 역할을 정의할 수 있습니다.

`labeler_custom_role.json` :
```json
{
    "Name": "Labeler Custom",
    "IsCustom": true,
    "Description": "Can label data for Labeling",
    "Actions": [
        "Microsoft.MachineLearningServices/workspaces/read",
        "Microsoft.MachineLearningServices/workspaces/labeling/projects/read",
        "Microsoft.MachineLearningServices/workspaces/labeling/labels/write"
    ],
    "NotActions": [
        "Microsoft.MachineLearningServices/workspaces/labeling/projects/summary/read"
    ],
    "AssignableScopes": [
        "/subscriptions/<subscription_id>"
    ]
}
```

### <a name="labeling-team-lead"></a>팀 잠재 고객 레이블 지정

레이블이 지정된 데이터 세트를 검토 및 거부하고 레이블 지정 사이트를 볼 수 있습니다. 그 밖에 이 역할을 통해 레이블 지정자의 역할을 수행할 수도 있습니다.

`labeling_team_lead_custom_role.json` :
```json
{
    "properties": {
        "roleName": "Labeling Team Lead",
        "description": "Team lead for Labeling Projects",
        "assignableScopes": [
            "/subscriptions/<subscription_id>"
        ],
        "permissions": [
            {
                "actions": [
                    "Microsoft.MachineLearningServices/workspaces/read",
                    "Microsoft.MachineLearningServices/workspaces/labeling/labels/read",
                    "Microsoft.MachineLearningServices/workspaces/labeling/labels/write",
                    "Microsoft.MachineLearningServices/workspaces/labeling/labels/reject/action",
                    "Microsoft.MachineLearningServices/workspaces/labeling/projects/read",
                    "Microsoft.MachineLearningServices/workspaces/labeling/projects/summary/read"
                ],
                "notActions": [
                    "Microsoft.MachineLearningServices/workspaces/labeling/projects/write",
                    "Microsoft.MachineLearningServices/workspaces/labeling/projects/delete",
                    "Microsoft.MachineLearningServices/workspaces/labeling/export/action"
                ],
                "dataActions": [],
                "notDataActions": []
            }
        ]
    }
}
```

## <a name="troubleshooting"></a>문제 해결

다음은 Azure RBAC(Azure 역할 기반 액세스 제어)를 사용하는 동안 유의해야 할 사항입니다.

- Azure에서 작업 영역과 같은 리소스를 만드는 경우 리소스의 직접 소유자가 아닙니다. 역할은 해당 구독에서 권한이 부여된 가장 높은 범위의 역할에서 상속됩니다. 예를 들어 네트워크 관리자이고 Machine Learning 작업 영역을 만들 수 있는 권한이 있는 경우 소유자 역할이 아닌 해당 작업 영역에 대한 네트워크 관리자 역할이 할당됩니다.

- 작업 영역에서 할당량 작업을 수행하려면 구독 수준 권한이 필요합니다. 즉, 관리 컴퓨팅 리소스에 대한 구독 수준 할당량 또는 작업 영역 수준 할당량 설정은 구독 범위에서 쓰기 권한이 있는 경우에만 발생할 수 있습니다.

- Actions/NotActions 섹션이 충돌하는 동일한 Azure Active Directory 사용자에 대한 2개의 역할 할당이 있으면 한 역할의 NotActions에 나열된 작업이 다른 역할의 Actions로도 나열되는 경우 해당 작업은 유효하지 않을 수 있습니다. Azure에서 역할 할당을 구문 분석하는 방법에 대해 자세히 알아보려면 [Azure RBAC에서 사용자가 리소스에 액세스할 수 있는지 확인하는 방법](../role-based-access-control/overview.md#how-azure-rbac-determines-if-a-user-has-access-to-a-resource)을 참조하세요.

- VNet 내에서 컴퓨팅 리소스를 배포하려면 다음 작업에 대한 권한이 명시적으로 있어야 합니다.
    - VNet 리소스의 `Microsoft.Network/virtualNetworks/*/read`
    - 서브넷 리소스의 `Microsoft.Network/virtualNetworks/subnets/join/action`
    
    네트워킹과 Azure RBAC에 관한 자세한 내용은 [네트워킹 기본 제공 역할](../role-based-access-control/built-in-roles.md#networking)을 참조하세요.

- 경우에 따라 새 역할 할당이 스택 전체의 캐시된 권한에 적용되는 데 최대 1시간까지 걸릴 수 있습니다.
- 현재 Azure Machine Learning에서는 [조건부 액세스](../role-based-access-control/conditional-access-azure-management.md)가 지원되지 않습니다.

## <a name="next-steps"></a>다음 단계

- [엔터프라이즈 보안 개요](concept-enterprise-security.md)
- [가상 네트워크 격리 및 개인 정보 개요](how-to-network-security-overview.md)
- [자습서: 모델 교육](tutorial-train-models-with-aml.md)
- [리소스 공급자 작업](../role-based-access-control/resource-provider-operations.md#microsoftmachinelearningservices)
