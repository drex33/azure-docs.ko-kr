---
title: REST를 사용하여 ML 리소스 관리
titleSuffix: Azure Machine Learning
description: REST API를 사용하여 작업 영역 등의 Azure Machine Learning 리소스를 만들고, 실행하고, 삭제하거나 모델을 등록하는 방법을 설명합니다.
author: lobrien
ms.author: laobri
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.date: 08/10/2020
ms.topic: how-to
ms.custom: devx-track-python
ms.openlocfilehash: 9465a8c45dd44eca4fcd67a8603e60a2061f2609
ms.sourcegitcommit: 2ed2d9d6227cf5e7ba9ecf52bf518dff63457a59
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/16/2021
ms.locfileid: "132518188"
---
# <a name="create-run-and-delete-azure-ml-resources-using-rest"></a>REST를 사용하여 Azure ML 리소스 만들기, 실행 및 삭제



몇 가지 방법으로 Azure ML 리소스를 관리할 수 있습니다. [포털](https://portal.azure.com/), [명령줄 인터페이스](/cli/azure) 또는 [Python SDK](/python/api/overview/azure/ml/intro)를 사용할 수 있습니다. 또는 REST API를 선택할 수 있습니다. REST API는 표준 방식으로 HTTP 동사를 사용하여 리소스를 만들고, 검색하고, 업데이트하고, 삭제합니다. REST API는 HTTP 요청을 만들 수 있는 모든 언어 또는 도구에서 작동합니다. REST는 구조가 간단하기 때문에 스크립팅 환경 및 MLOps 자동화에 적합합니다. 

이 문서에서는 다음 방법을 설명합니다.

> [!div class="checklist"]
> * 권한 부여 토큰 검색
> * 서비스 주체 인증을 사용하여 올바른 형식의 REST 요청 만들기
> * GET 요청을 사용하여 Azure ML의 계층 구조 리소스에 대한 정보 검색
> * PUT 및 POST 요청을 사용하여 리소스 만들기 및 수정
> * PUT 요청을 사용 하 여 Azure ML 작업 영역 만들기
> * DELETE 요청을 사용하여 리소스 정리 

## <a name="prerequisites"></a>사전 요구 사항

- 본인에게 관리 권한이 있는 **Azure 구독** 이러한 구독이 없는 경우 [무료 또는 유료 개인 구독](https://azure.microsoft.com/free/)을 사용해 보세요.
- [Azure Machine Learning 작업 영역](./how-to-manage-workspace.md)
- 관리 REST 요청은 서비스 사용자 인증을 사용합니다. [Azure Machine Learning 리소스 및 워크플로에 대한 인증 설정](./how-to-setup-authentication.md#service-principal-authentication)의 단계에 따라 작업 영역에서 서비스 주체를 만듭니다.
- **curl** 유틸리티. **curl** 프로그램은 [Linux용 Windows 하위 시스템](/windows/wsl/install-win10) 또는 모든 UNIX에서 사용할 수 있습니다. PowerShell에서 **curl** 은 **Invoke-WebRequest** 의 별칭으로, `curl -d "key=val" -X POST uri`는 `Invoke-WebRequest -Body "key=val" -Method POST -Uri uri`가 됩니다. 

## <a name="retrieve-a-service-principal-authentication-token"></a>서비스 사용자 인증 토큰 검색

관리 REST 요청은 OAuth2 암시적 흐름으로 인증됩니다. 이 인증 흐름은 구독의 서비스 사용자가 제공하는 토큰을 사용합니다. 이 토큰을 검색하려면 다음이 필요합니다.

- 사용자의 테넌트 ID(구독이 속한 조직 식별)
- 클라이언트 ID(생성된 토큰과 연결됨)
- 클라이언트 암호(보호해야 함)

서비스 사용자 생성에 대한 응답에 이 값이 있어야 합니다. 이러한 값을 가져오는 방법은 [Azure Machine Learning 리소스 및 워크플로에 대한 인증 설정](./how-to-setup-authentication.md#service-principal-authentication)에서 설명합니다. 회사 구독을 사용할 경우 서비스 사용자를 만들 권한이 없을 수 있습니다. 이 경우 [무료 또는 유료 개인 구독](https://azure.microsoft.com/free/) 중 하나를 사용해야 합니다.

토큰을 검색하려면:

1. 터미널 창 열기
1. 명령줄에서 다음 코드를 입력합니다.
1. `<YOUR-TENANT-ID>`, `<YOUR-CLIENT-ID>` 및 `<YOUR-CLIENT-SECRET>`에 대한 고유한 값을 대체합니다. 이 문서 전체에서 꺾쇠괄호로 묶인 문자열은 적절한 고유의 값으로 바꿔야 하는 변수입니다.
1. 명령 실행

```bash
curl -X POST https://login.microsoftonline.com/<YOUR-TENANT-ID>/oauth2/token \
-d "grant_type=client_credentials&resource=https%3A%2F%2Fmanagement.azure.com%2F&client_id=<YOUR-CLIENT-ID>&client_secret=<YOUR-CLIENT-SECRET>" \
```

응답은 1시간 동안 유효한 액세스 토큰을 제공할 것입니다.

```json
{
    "token_type": "Bearer",
    "expires_in": "3599",
    "ext_expires_in": "3599",
    "expires_on": "1578523094",
    "not_before": "1578519194",
    "resource": "https://management.azure.com/",
    "access_token": "YOUR-ACCESS-TOKEN"
}
```

이 토큰은 모든 추가 관리 요청을 인증하는 데 사용되므로 기록해 둡니다. 모든 요청에서 권한 부여 헤더를 설정하여 이 작업을 수행하게 됩니다.

```bash
curl -h "Authorization:Bearer <YOUR-ACCESS-TOKEN>" ...more args...
```

> [!NOTE]
> 값은 토큰을 추가하기 전에 단일 공백을 포함하여 문자열 “Bearer”로 시작합니다.

## <a name="get-a-list-of-resource-groups-associated-with-your-subscription"></a>구독과 연결된 리소스 그룹 목록 가져오기

구독과 연결된 리소스 그룹 목록을 가져오려면 다음을 실행합니다.

```bash
curl https://management.azure.com/subscriptions/<YOUR-SUBSCRIPTION-ID>/resourceGroups?api-version=2021-03-01-preview -H "Authorization:Bearer <YOUR-ACCESS-TOKEN>"
```

Azure 전체에서 많은 REST API가 게시됩니다. 각 서비스 공급자는 자체 케이던스에 대해 API를 업데이트하지만 기존 프로그램을 중단하지는 않습니다. 서비스 공급자는 `api-version` 인수를 사용하여 호환성을 보장합니다. `api-version` 인수는 서비스마다 다릅니다. 예를 들어 Machine Learning Service의 경우 최신 API 버전은 `2021-03-01-preview`입니다. 스토리지 계정의 경우에는 `2019-08-01`입니다. 키 자격 증명 모음의 경우 `2019-09-01`이 됩니다. 모든 REST 호출은 `api-version` 인수를 기대값으로 설정해야 합니다. API가 계속 진화하더라도 지정된 버전의 구문 및 의미 체계를 사용할 수 있습니다. `api-version` 인수 없이 공급자에게 요청을 보내는 경우 응답에는 지원되는 값의 사람이 읽을 수 있는 목록이 포함됩니다. 

위 호출의 결과는 다음과 같은 양식의 압축된 JSON 응답입니다. 

```json
{
    "value": [
        {
            "id": "/subscriptions/12345abc-abbc-1b2b-1234-57ab575a5a5a/resourceGroups/RG1",
            "name": "RG1",
            "type": "Microsoft.Resources/resourceGroups",
            "location": "westus2",
            "properties": {
                "provisioningState": "Succeeded"
            }
        },
        {
            "id": "/subscriptions/12345abc-abbc-1b2b-1234-57ab575a5a5a/resourceGroups/RG2",
            "name": "RG2",
            "type": "Microsoft.Resources/resourceGroups",
            "location": "eastus",
            "properties": {
                "provisioningState": "Succeeded"
            }
        }
    ]
}
```


## <a name="drill-down-into-workspaces-and-their-resources"></a>작업 영역 및 해당 리소스 드릴다운

리소스 그룹에서 작업 영역 세트를 검색하려면 `<YOUR-SUBSCRIPTION-ID>`, `<YOUR-RESOURCE-GROUP>`, `<YOUR-ACCESS-TOKEN>`을 대체하여 다음을 실행합니다. 

```
curl https://management.azure.com/subscriptions/<YOUR-SUBSCRIPTION-ID>/resourceGroups/<YOUR-RESOURCE-GROUP>/providers/Microsoft.MachineLearningServices/workspaces/?api-version=2021-03-01-preview \
-H "Authorization:Bearer <YOUR-ACCESS-TOKEN>"
```

여기서도 JSON 목록을 받게 되지만 이번에는 각 항목이 작업 영역을 상세히 설명하는 목록이 포함됩니다.

```json
{
    "id": "/subscriptions/12345abc-abbc-1b2b-1234-57ab575a5a5a/resourceGroups/DeepLearningResourceGroup/providers/Microsoft.MachineLearningServices/workspaces/my-workspace",
    "name": "my-workspace",
    "type": "Microsoft.MachineLearningServices/workspaces",
    "location": "centralus",
    "tags": {},
    "etag": null,
    "properties": {
        "friendlyName": "",
        "description": "",
        "creationTime": "2020-01-03T19:56:09.7588299+00:00",
        "storageAccount": "/subscriptions/12345abc-abbc-1b2b-1234-57ab575a5a5a/resourcegroups/DeepLearningResourceGroup/providers/microsoft.storage/storageaccounts/myworkspace0275623111",
        "containerRegistry": null,
        "keyVault": "/subscriptions/12345abc-abbc-1b2b-1234-57ab575a5a5a/resourcegroups/DeepLearningResourceGroup/providers/microsoft.keyvault/vaults/myworkspace2525649324",
        "applicationInsights": "/subscriptions/12345abc-abbc-1b2b-1234-57ab575a5a5a/resourcegroups/DeepLearningResourceGroup/providers/microsoft.insights/components/myworkspace2053523719",
        "hbiWorkspace": false,
        "workspaceId": "cba12345-abab-abab-abab-ababab123456",
        "subscriptionState": null,
        "subscriptionStatusChangeTimeStampUtc": null,
        "discoveryUrl": "https://centralus.experiments.azureml.net/discovery"
    },
    "identity": {
        "type": "SystemAssigned",
        "principalId": "abcdef1-abab-1234-1234-abababab123456",
        "tenantId": "1fedcba-abab-1234-1234-abababab123456"
    },
    "sku": {
        "name": "Basic",
        "tier": "Basic"
    }
}
```

작업 영역 안에서 리소스를 작업하려면 일반 **management.azure.com** 서버에서 작업 영역의 위치와 관련된 REST API 서버로 전환합니다. 위의 JSON 응답에서 `discoveryUrl` 키 값을 확인합니다. 해당 URL을 GET하면 다음과 같은 응답을 받게 됩니다.

```json
{
  "api": "https://centralus.api.azureml.ms",
  "catalog": "https://catalog.cortanaanalytics.com",
  "experimentation": "https://centralus.experiments.azureml.net",
  "gallery": "https://gallery.cortanaintelligence.com/project",
  "history": "https://centralus.experiments.azureml.net",
  "hyperdrive": "https://centralus.experiments.azureml.net",
  "labeling": "https://centralus.experiments.azureml.net",
  "modelmanagement": "https://centralus.modelmanagement.azureml.net",
  "pipelines": "https://centralus.aether.ms",
  "studiocoreservices": "https://centralus.studioservice.azureml.com"
}
```

`api` 응답의 값은 추가 요청에 사용할 서버의 URL입니다. 예를 들어 실험을 나열하려면 다음 명령을 보냅니다. `api` 응답의 값으로 `REGIONAL-API-SERVER`를 바꿉니다(예: `centralus.api.azureml.ms`). 평소와 같이 `YOUR-SUBSCRIPTION-ID`, `YOUR-RESOURCE-GROUP`, `YOUR-WORKSPACE-NAME` 및 `YOUR-ACCESS-TOKEN`도 바꿉니다.

```bash
curl https://<REGIONAL-API-SERVER>/history/v1.0/subscriptions/<YOUR-SUBSCRIPTION-ID>/resourceGroups/<YOUR-RESOURCE-GROUP>/\
providers/Microsoft.MachineLearningServices/workspaces/<YOUR-WORKSPACE-NAME>/experiments?api-version=2021-03-01-preview \
-H "Authorization:Bearer <YOUR-ACCESS-TOKEN>"
```

마찬가지로 작업 영역에서 등록된 모델을 검색하려면 다음을 보냅니다.

```bash
curl https://<REGIONAL-API-SERVER>/modelmanagement/v1.0/subscriptions/<YOUR-SUBSCRIPTION-ID>/resourceGroups/<YOUR-RESOURCE-GROUP>/\
providers/Microsoft.MachineLearningServices/workspaces/<YOUR-WORKSPACE-NAME>/models?api-version=2021-03-01-preview \
-H "Authorization:Bearer <YOUR-ACCESS-TOKEN>"
```

실험을 나열하려면 경로가 `history/v1.0`으로 시작하지만 모델을 나열할 때는 `modelmanagement/v1.0`으로 시작합니다. REST API는 각각 고유한 경로를 가진 여러 작업 그룹으로 구분됩니다. 

|영역|경로|
|-|-|
|Artifacts|/rest/api/azureml|
|데이터 저장소|/azure/machine-learning/how-to-access-data|
|하이퍼 매개 변수 조정|hyperdrive/v1.0/|
|모델|modelmanagement/v1.0/|
|실행 기록|execution/v1.0/ 및 history/v1.0/|

다음의 일반적인 패턴을 사용하여 REST API를 검색할 수 있습니다.

|URL 구성 요소|예제|
|-|-|
| https://| |
| REGIONAL-API-SERVER/ | centralus.api.azureml.ms/ |
| operations-path/ | history/v1.0/ |
| subscriptions/YOUR-SUBSCRIPTION-ID/ | subscriptions/abcde123-abab-abab-1234-0123456789abc/ |
| resourceGroups/YOUR-RESOURCE-GROUP/ | resourceGroups/MyResourceGroup/ |
| providers/operation-provider/ | providers/Microsoft.MachineLearningServices/ |
| provider-resource-path/ | workspaces/MLWorkspace/MyWorkspace/FirstExperiment/runs/1/ |
| operations-endpoint/ | artifacts/metadata/ |


## <a name="create-and-modify-resources-using-put-and-post-requests"></a>PUT 및 POST 요청을 사용하여 리소스 만들기 및 수정

REST API는 GET 동사를 통한 리소스 검색 외에도 ML 솔루션의 교육, 배포, 모니터링에 필요한 모든 리소스의 생성을 지원합니다. 

ML 모델을 학습 및 실행하려면 컴퓨팅 리소스가 필요합니다. 다음을 통해 작업 영역의 컴퓨팅 리소스를 나열할 수 있습니다. 

```bash
curl https://management.azure.com/subscriptions/<YOUR-SUBSCRIPTION-ID>/resourceGroups/<YOUR-RESOURCE-GROUP>/\
providers/Microsoft.MachineLearningServices/workspaces/<YOUR-WORKSPACE-NAME>/computes?api-version=2021-03-01-preview \
-H "Authorization:Bearer <YOUR-ACCESS-TOKEN>"
```

명명된 컴퓨팅 리소스를 만들거나 덮어쓰려면 PUT 요청을 사용합니다. 다음에서 익숙한 `YOUR-SUBSCRIPTION-ID`, `YOUR-RESOURCE-GROUP`, `YOUR-WORKSPACE-NAME`, `YOUR-ACCESS-TOKEN`을 대체하고 `YOUR-COMPUTE-NAME`과 `location`, `vmSize`, `vmPriority`, `scaleSettings`, `adminUserName` 및 `adminUserPassword`에 대한 값도 대체합니다. [Machine Learning 컴퓨팅 - SDK 참조 만들기 또는 업데이트](/rest/api/azureml/workspaces/createorupdate)의 참조에서 명시한 것처럼 다음 명령은 30분 후 규모가 축소되는 전용 단일 노드 Standard_D1(기본 CPU 컴퓨팅 리소스)을 만듭니다.

```bash
curl -X PUT \
  'https://management.azure.com/subscriptions/<YOUR-SUBSCRIPTION-ID>/resourceGroups/<YOUR-RESOURCE-GROUP>/providers/Microsoft.MachineLearningServices/workspaces/<YOUR-WORKSPACE-NAME>/computes/<YOUR-COMPUTE-NAME>?api-version=2021-03-01-preview' \
  -H 'Authorization:Bearer <YOUR-ACCESS-TOKEN>' \
  -H 'Content-Type: application/json' \
  -d '{
    "location": "eastus",
    "properties": {
        "computeType": "AmlCompute",
        "properties": {
            "vmSize": "Standard_D1",
            "vmPriority": "Dedicated",
            "scaleSettings": {
                "maxNodeCount": 1,
                "minNodeCount": 0,
                "nodeIdleTimeBeforeScaleDown": "PT30M"
            }
        }
    },
    "userAccountCredentials": {
        "adminUserName": "<ADMIN_USERNAME>",
        "adminUserPassword": "<ADMIN_PASSWORD>"
    }
}'
```

> [!Note]
> Windows 터미널에서는 JSON 데이터를 보낼 때 큰따옴표 기호를 이스케이프해야 할 수 있습니다. 즉, `"location"` 같은 텍스트는 `\"location\"`가 됩니다. 

요청에 성공하면 `201 Created` 응답을 받지만 이 응답은 단순히 프로비저닝 프로세스가 시작되었음을 의미합니다. 성공적으로 완료되었는지 확인하려면 폴링을 수행하거나 포털을 사용해야 합니다.

## <a name="create-a-workspace-using-rest"></a>REST를 사용하여 작업 영역 만들기 

모든 azure ML 작업 영역에는 Azure Container Registry 리소스, Azure Key Vault, Azure 애플리케이션 Insights, Azure Storage 계정 이라는 4 개의 다른 azure 리소스에 대 한 종속성이 있습니다. 작업 영역을 만들려면 이러한 리소스가 있어야 합니다. 각각의 리소스를 만드는 방법에 대한 자세한 내용은 REST API 참조에서 확인하세요.

작업 영역을 만들려면 `management.azure.com`에 대해 다음과 유사한 호출을 PUT 합니다. 이 호출에서는 대규모 변수를 설정해야 하지만 이 문서에서 논의하는 다른 호출과 구조적으로는 동일합니다. 

```bash
curl -X PUT \
  'https://management.azure.com/subscriptions/<YOUR-SUBSCRIPTION-ID>/resourceGroups/<YOUR-RESOURCE-GROUP>\
/providers/Microsoft.MachineLearningServices/workspaces/<YOUR-NEW-WORKSPACE-NAME>?api-version=2021-03-01-preview' \
  -H 'Authorization: Bearer <YOUR-ACCESS-TOKEN>' \
  -H 'Content-Type: application/json' \
  -d '{
    "location": "AZURE-LOCATION>",
    "identity" : {
        "type" : "systemAssigned"
    },
    "properties": {
        "friendlyName" : "<YOUR-WORKSPACE-FRIENDLY-NAME>",
        "description" : "<YOUR-WORKSPACE-DESCRIPTION>",
        "containerRegistry" : "/subscriptions/<YOUR-SUBSCRIPTION-ID>/resourceGroups/<YOUR-RESOURCE-GROUP>/\
providers/Microsoft.ContainerRegistry/registries/<YOUR-REGISTRY-NAME>",
        keyVault" : "/subscriptions/<YOUR-SUBSCRIPTION-ID>/resourceGroups/<YOUR-RESOURCE-GROUP>\
/providers/Microsoft.Keyvault/vaults/<YOUR-KEYVAULT-NAME>",
        "applicationInsights" : "subscriptions/<YOUR-SUBSCRIPTION-ID>/resourceGroups/<YOUR-RESOURCE-GROUP>/\
providers/Microsoft.insights/components/<YOUR-APPLICATION-INSIGHTS-NAME>",
        "storageAccount" : "/subscriptions/<YOUR-SUBSCRIPTION-ID>/resourceGroups/<YOUR-RESOURCE-GROUP>/\
providers/Microsoft.Storage/storageAccounts/<YOUR-STORAGE-ACCOUNT-NAME>"
    }
}'
```

`202 Accepted` 응답을 수신하고, 반환되는 헤더에 `Location` URI가 있어야 합니다. 종속 리소스 중 하나에 문제가 있는 경우(예: 컨테이너 레지스트리에서 관리자 액세스를 사용하도록 설정하지 않은 경우) 유용한 디버깅 정보를 포함하여 배포 관련 정보에 대해 이 URI를 GET할 수 있습니다. 

## <a name="create-a-workspace-using-a-user-assigned-managed-identity"></a>사용자 할당 관리 id를 사용 하 여 작업 영역 만들기 

작업 영역을 만들 때 ACR, KeyVault, Storage 및 App Insights와 같은 연결된 리소스에 액세스하는 데 사용되는 사용자가 할당한 관리 ID를 지정할 수 있습니다. 사용자 할당 관리 id를 사용 하 여 작업 영역을 만들려면 아래 요청 본문을 사용 합니다. 

```bash
curl -X PUT \
  'https://management.azure.com/subscriptions/<YOUR-SUBSCRIPTION-ID>/resourceGroups/<YOUR-RESOURCE-GROUP>\
/providers/Microsoft.MachineLearningServices/workspaces/<YOUR-NEW-WORKSPACE-NAME>?api-version=2021-03-01-preview' \
  -H 'Authorization: Bearer <YOUR-ACCESS-TOKEN>' \
  -H 'Content-Type: application/json' \
  -d '{
    "location": "AZURE-LOCATION>",
    "identity": {
      "type": "SystemAssigned,UserAssigned",
      "userAssignedIdentities": {
        "/subscriptions/<YOUR-SUBSCRIPTION-ID>/resourceGroups/<YOUR-RESOURCE-GROUP>/\
providers/Microsoft.ManagedIdentity/userAssignedIdentities/<YOUR-MANAGED-IDENTITY>": {}
      }
    },
    "properties": {
        "friendlyName" : "<YOUR-WORKSPACE-FRIENDLY-NAME>",
        "description" : "<YOUR-WORKSPACE-DESCRIPTION>",
        "containerRegistry" : "/subscriptions/<YOUR-SUBSCRIPTION-ID>/resourceGroups/<YOUR-RESOURCE-GROUP>/\
providers/Microsoft.ContainerRegistry/registries/<YOUR-REGISTRY-NAME>",
        keyVault" : "/subscriptions/<YOUR-SUBSCRIPTION-ID>/resourceGroups/<YOUR-RESOURCE-GROUP>\
/providers/Microsoft.Keyvault/vaults/<YOUR-KEYVAULT-NAME>",
        "applicationInsights" : "subscriptions/<YOUR-SUBSCRIPTION-ID>/resourceGroups/<YOUR-RESOURCE-GROUP>/\
providers/Microsoft.insights/components/<YOUR-APPLICATION-INSIGHTS-NAME>",
        "storageAccount" : "/subscriptions/<YOUR-SUBSCRIPTION-ID>/resourceGroups/<YOUR-RESOURCE-GROUP>/\
providers/Microsoft.Storage/storageAccounts/<YOUR-STORAGE-ACCOUNT-NAME>"
    }
}'
```

## <a name="create-a-workspace-using-customer-managed-encryption-keys"></a>고객이 관리 하는 암호화 키를 사용 하 여 작업 영역 만들기

기본적으로 작업 영역에 대한 메타데이터는 Microsoft에서 유지 관리하는 Azure Cosmos DB 인스턴스에 저장됩니다. 이 데이터는 Microsoft 관리형 키를 사용하여 암호화됩니다. Microsoft 관리형 키를 사용하는 대신 사용자 고유 키를 사용할 수도 있습니다. 이렇게 하면 데이터를 저장 하기 위해 Azure 구독에 [추가 리소스 집합이](/azure/machine-learning/concept-data-encryption#azure-cosmos-db) 생성 됩니다.

암호화를 위해 키를 사용 하는 작업 영역을 만들려면 다음 필수 구성 요소를 충족 해야 합니다.

* Azure Machine Learning 서비스 주체에 게 Azure 구독에 대 한 참가자 액세스 권한이 있어야 합니다.
* 암호화 키를 포함하는 기존 Azure Key Vault가 있어야 합니다.
* Azure Key Vault은 Azure Machine Learning 작업 영역을 만들 동일한 Azure 지역에 있어야 합니다.
* 실수로 삭제 한 경우 데이터 손실을 방지 하려면 일시 삭제 및 제거 보호를 사용 하도록 설정 해야 합니다. Azure Key Vault
* Azure Key Vault에는 Azure Cosmos DB 응용 프로그램에 대 한 가져오기, 래핑 및 래핑 해제 액세스 권한을 부여 하는 액세스 정책이 있어야 합니다.

사용자 할당 관리 id 및 암호화에 대 한 고객 관리 키를 사용 하는 작업 영역을 만들려면 아래 요청 본문을 사용 합니다. 작업 영역에 사용자 할당 관리 id를 사용 하는 경우 `userAssignedIdentity` 속성을 관리 id의 리소스 ID로 설정 합니다.

```bash
curl -X PUT \
  'https://management.azure.com/subscriptions/<YOUR-SUBSCRIPTION-ID>/resourceGroups/<YOUR-RESOURCE-GROUP>\
/providers/Microsoft.MachineLearningServices/workspaces/<YOUR-NEW-WORKSPACE-NAME>?api-version=2021-03-01-preview' \
  -H 'Authorization: Bearer <YOUR-ACCESS-TOKEN>' \
  -H 'Content-Type: application/json' \
  -d '{
    "location": "eastus2euap",
    "identity": {
      "type": "SystemAssigned"
    },
    "properties": {
      "friendlyName": "<YOUR-WORKSPACE-FRIENDLY-NAME>",
      "description": "<YOUR-WORKSPACE-DESCRIPTION>",
      "containerRegistry" : "/subscriptions/<YOUR-SUBSCRIPTION-ID>/resourceGroups/<YOUR-RESOURCE-GROUP>/\
providers/Microsoft.ContainerRegistry/registries/<YOUR-REGISTRY-NAME>",
      "keyVault" : "/subscriptions/<YOUR-SUBSCRIPTION-ID>/resourceGroups/<YOUR-RESOURCE-GROUP>\
/providers/Microsoft.Keyvault/vaults/<YOUR-KEYVAULT-NAME>",
      "applicationInsights" : "subscriptions/<YOUR-SUBSCRIPTION-ID>/resourceGroups/<YOUR-RESOURCE-GROUP>/\
providers/Microsoft.insights/components/<YOUR-APPLICATION-INSIGHTS-NAME>",
      "storageAccount" : "/subscriptions/<YOUR-SUBSCRIPTION-ID>/resourceGroups/<YOUR-RESOURCE-GROUP>/\
providers/Microsoft.Storage/storageAccounts/<YOUR-STORAGE-ACCOUNT-NAME>",
      "encryption": {
        "status": "Enabled",
        "identity": {
          "userAssignedIdentity": null
        },      
        "keyVaultProperties": {
           "keyVaultArmId": "/subscriptions/<YOUR-SUBSCRIPTION-ID>/resourceGroups/<YOUR-RESOURCE-GROUP>/\
providers/Microsoft.KeyVault/vaults/<YOUR-VAULT>",
           "keyIdentifier": "https://<YOUR-VAULT>.vault.azure.net/keys/<YOUR-KEY>/<YOUR-KEY-VERSION>",
           "identityClientId": ""
        }
      },
      "hbiWorkspace": false
    }
}'
```

### <a name="delete-resources-you-no-longer-need"></a>더 이상 필요하지 않은 리소스 삭제

전체는 아니지만 일부 리소스는 DELETE 동사를 지원합니다. 삭제 사용 사례에 대해 REST API를 커밋하기 전에 [API 참조](/rest/api/azureml/)를 확인하세요. 예를 들어 모델을 삭제하려면 다음을 사용할 수 있습니다.

```bash
curl
  -X DELETE \
'https://<REGIONAL-API-SERVER>/modelmanagement/v1.0/subscriptions/<YOUR-SUBSCRIPTION-ID>/resourceGroups/<YOUR-RESOURCE-GROUP>/providers/Microsoft.MachineLearningServices/workspaces/<YOUR-WORKSPACE-NAME>/models/<YOUR-MODEL-ID>?api-version=2021-03-01-preview' \
  -H 'Authorization:Bearer <YOUR-ACCESS-TOKEN>' 
```

## <a name="troubleshooting"></a>문제 해결

### <a name="resource-provider-errors"></a>리소스 공급자 오류

[!INCLUDE [machine-learning-resource-provider](../../includes/machine-learning-resource-provider.md)]

### <a name="moving-the-workspace"></a>작업 영역 이동

> [!WARNING]
> Azure Machine Learning 작업 영역을 다른 구독으로 이동하거나 소유하는 구독을 새 테넌트로 이동하는 것은 지원되지 않습니다. 이렇게 하면 오류가 발생할 수 있습니다.

### <a name="deleting-the-azure-container-registry"></a>Azure Container Registry 삭제

Azure Machine Learning 작업 영역에서는 일부 작업에 ACR(Azure Container Registry)을 사용합니다. 먼저 필요한 경우 ACR 인스턴스를 자동으로 만듭니다.

[!INCLUDE [machine-learning-delete-acr](../../includes/machine-learning-delete-acr.md)]

## <a name="next-steps"></a>다음 단계

- 전체 [AzureML REST API 참조](/rest/api/azureml/)를 살펴봅니다.
- 디자이너를 사용하여 [디자이너로 자동차 가격을 예측](./tutorial-designer-automobile-price-train-score.md)하는 방법을 알아봅니다.
- [Jupyter Notebook을 사용하여 Azure Machine Learning](..//machine-learning/samples-notebooks.md)을 살펴봅니다.
