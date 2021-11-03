---
title: '자습서: Azure Container Apps 미리 보기를 통해 백그라운드 처리 애플리케이션 배포'
description: Azure Container Apps를 사용하여 백그라운드에서 지속적으로 실행되는 애플리케이션을 만드는 방법을 알아봅니다.
services: app-service
author: jorgearteiro
ms.service: app-service
ms.topic: conceptual
ms.date: 10/21/2021
ms.author: joarteir
ms.custom: ignite-fall-2021
ms.openlocfilehash: 922ac6196332a96bf1e1a4490659ae11dd42458d
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131443246"
---
# <a name="tutorial-deploy-a-background-processing-application-with-azure-container-apps-preview"></a>자습서: Azure Container Apps 미리 보기를 통해 백그라운드 처리 애플리케이션 배포

Azure Container Apps를 사용하면 퍼블릭 엔드포인트를 노출하지 않고도 애플리케이션을 배포할 수 있습니다. 이 자습서에서는 Azure Storage 큐에서 메시지를 읽고 Azure log Analytics 작업 영역에 메시지를 기록하는 샘플 애플리케이션을 배포합니다. Container Apps 크기 조정 규칙을 사용하여 애플리케이션은 Azure Storage 큐 길이에 따라 확장 및 축소할 수 있습니다. 큐에 메시지가 없으면 컨테이너 앱이 0으로 축소됩니다.

다음 방법을 알아봅니다.

> [!div class="checklist"]
> * 컨테이너 앱을 배포하는 Container Apps 환경 만들기
> * 컨테이너 앱에 메시지를 보낼 Azure Storage 큐 만들기
> * 백그라운드 처리 애플리케이션을 컨테이너 앱으로 배포
> * 큐 메시지가 컨테이너 앱에서 처리되는지 확인합니다.

## <a name="prerequisites"></a>사전 요구 사항

이 자습서를 완료하려면 다음 항목이 필요합니다.

* **Azure CLI:** 로컬 컴퓨터에 Azure CLI 버전 2.29.0 이상 버전이 설치되어 있어야 합니다.
  * `az --version`을 실행하여 버전을 찾습니다. 설치 또는 업그레이드가 필요한 경우, [Azure CLI 설치](/cli/azure/install-azure-cli)를 참조하세요.
  
## <a name="setup"></a>설치 프로그램

이 자습서에서는 다음 환경 변수를 사용합니다.

# <a name="bash"></a>[Bash](#tab/bash)

```bash
RESOURCE_GROUP="my-containerapps"
LOCATION="canadacentral"
CONTAINERAPPS_ENVIRONMENT="containerapps-env"
LOG_ANALYTICS_WORKSPACE="containerapps-logs"
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```powershell
$RESOURCE_GROUP="my-containerapps"
$LOCATION="canadacentral"
$CONTAINERAPPS_ENVIRONMENT="containerapps-env"
$LOG_ANALYTICS_WORKSPACE="containerapps-logs"
```

---

스토리지 계정 이름에 대한 변수를 만듭니다.

# <a name="bash"></a>[Bash](#tab/bash)

```bash
STORAGE_ACCOUNT="<MY_STORAGE_ACCOUNT_NAME>"
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```powershell
$STORAGE_ACCOUNT="<storage account name>"
```

---

이 `<storage account name>` 조각을 실행하기 전에 자리 표시자를 사용자 고유의 값으로 대체합니다. Storage 계정 이름은 Azure 내에서 고유해야 하고, 길이가 3자에서 24자 사이여야 하며, 숫자 또는 소문자만 포함할 수 있습니다. 스토리지 계정은 다음 단계에서 만들어집니다.

다음으로 CLI에서 Azure에 로그인합니다.

다음 명령을 실행하고 프롬프트에 따라 인증 프로세스를 완료합니다.

# <a name="bash"></a>[Bash](#tab/bash)

```azurecli
az login
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```powershell
az login
```

---

최신 버전의 CLI를 실행하고 있는지 확인하려면 `upgrade` 명령을 사용합니다.

# <a name="bash"></a>[Bash](#tab/bash)

```azurecli
az upgrade
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```powershell
az upgrade
```

---

그런 다음 CLI에 Azure Container Apps 확장을 설치합니다.

# <a name="bash"></a>[Bash](#tab/bash)

```azurecli
az extension add \
  --source https://workerappscliextension.blob.core.windows.net/azure-cli-extension/containerapp-0.2.0-py2.py3-none-any.whl
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```powershell
az extension add `
  --source https://workerappscliextension.blob.core.windows.net/azure-cli-extension/containerapp-0.2.0-py2.py3-none-any.whl
```

---

이제 확장이 설치되어 `Microsoft.Web` 네임스페이스를 등록합니다.

# <a name="bash"></a>[Bash](#tab/bash)

```azurecli
az provider register --namespace Microsoft.Web
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```powershell
az provider register --namespace Microsoft.Web
```

---

리소스 그룹을 사용하여 새 컨테이너 앱과 관련된 서비스를 구성합니다. 다음 명령을 사용하여 그룹을 만듭니다.

# <a name="bash"></a>[Bash](#tab/bash)

```azurecli
az group create \
  --name $RESOURCE_GROUP \
  --location "$LOCATION"
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```powershell
az group create `
  --name $RESOURCE_GROUP `
  --location $LOCATION
```

---

CLI가 업그레이드되고 새 리소스 그룹이 사용 가능해지면 컨테이너 앱 환경을 만들고 컨테이너 앱을 배포할 수 있습니다.

## <a name="create-an-environment"></a>환경 만들기

Azure Container Apps 환경은 컨테이너 앱 그룹을 중심으로 보안 경계 역할을 합니다. 동일한 환경의 여러 컨테이너 앱이 동일한 가상 네트워크에 배포되고 로그를 동일한 Log Analytics 작업 영역에 기록합니다.

Azure Log Analytics는 컨테이너 앱 환경을 만들 때 필요한 컨테이너 앱을 모니터링하는 데 사용됩니다.

다음 명령을 사용하여 새 Log Analytics 작업 영역을 만듭니다.

# <a name="bash"></a>[Bash](#tab/bash)

```azurecli
az monitor log-analytics workspace create \
  --resource-group $RESOURCE_GROUP \
  --workspace-name $LOG_ANALYTICS_WORKSPACE
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```powershell
az monitor log-analytics workspace create `
  --resource-group $RESOURCE_GROUP `
  --workspace-name $LOG_ANALYTICS_WORKSPACE
```

---

다음으로 Log Analytics 클라이언트 ID 및 클라이언트 암호를 검색합니다. 요청이 완료될 수 있도록 충분한 시간을 주기 위해 각 쿼리를 별도로 실행해야 합니다.

# <a name="bash"></a>[Bash](#tab/bash)

```azurecli
LOG_ANALYTICS_WORKSPACE_CLIENT_ID=`az monitor log-analytics workspace show --query customerId -g $RESOURCE_GROUP -n $LOG_ANALYTICS_WORKSPACE --out json | tr -d '"'`
```

```azurecli
LOG_ANALYTICS_WORKSPACE_CLIENT_SECRET=`az monitor log-analytics workspace get-shared-keys --query primarySharedKey -g $RESOURCE_GROUP -n $LOG_ANALYTICS_WORKSPACE --out json | tr -d '"'`
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```powershell
$LOG_ANALYTICS_WORKSPACE_CLIENT_ID=(az monitor log-analytics workspace show --query customerId -g $RESOURCE_GROUP -n $LOG_ANALYTICS_WORKSPACE --out json | tr -d '"')
```

```powershell
$LOG_ANALYTICS_WORKSPACE_CLIENT_SECRET=`az monitor log-analytics workspace get-shared-keys --query primarySharedKey -g $RESOURCE_GROUP -n $LOG_ANALYTICS_WORKSPACE --out json | tr -d '"'`
```

---

개별 컨테이너 앱은 Azure Container Apps 환경에 배포됩니다. 환경을 만들려면 다음 명령을 실행합니다.

# <a name="bash"></a>[Bash](#tab/bash)

```azurecli
az containerapp env create \
  --name $CONTAINERAPPS_ENVIRONMENT \
  --resource-group $RESOURCE_GROUP \
  --logs-workspace-id $LOG_ANALYTICS_WORKSPACE_CLIENT_ID \
  --logs-workspace-key $LOG_ANALYTICS_WORKSPACE_CLIENT_SECRET \
  --location "$LOCATION"
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```powershell
az containerapp env create `
  --name $CONTAINERAPPS_ENVIRONMENT `
  --resource-group $RESOURCE_GROUP `
  --logs-workspace-id $LOG_ANALYTICS_WORKSPACE_CLIENT_ID `
  --logs-workspace-key $LOG_ANALYTICS_WORKSPACE_CLIENT_SECRET `
  --location $LOCATION
```

---

## <a name="set-up-a-storage-queue"></a>스토리지 큐 설정

Azure Storage 계정 만들기

# <a name="bash"></a>[Bash](#tab/bash)

```azurecli
az storage account create \
  --name $STORAGE_ACCOUNT \
  --resource-group $RESOURCE_GROUP \
  --location "$LOCATION" \
  --sku Standard_RAGRS \
  --kind StorageV2
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```powershell
az storage account create `
  --name $STORAGE_ACCOUNT `
  --resource-group $RESOURCE_GROUP `
  --location $LOCATION `
  --sku Standard_RAGRS `
  --kind StorageV2
```

---

다음으로, 큐의 연결 문자열을 가져옵니다.

# <a name="bash"></a>[Bash](#tab/bash)

```azurecli
QUEUE_CONNECTION_STRING=`az storage account show-connection-string -g $RESOURCE_GROUP --name $STORAGE_ACCOUNT --query connectionString --out json | tr -d '"'`
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```powershell
$QUEUE_CONNECTION_STRING=(az storage account show-connection-string -g $RESOURCE_GROUP --name $STORAGE_ACCOUNT --query connectionString --out json | tr -d '"')
```

---

이제 메시지 큐를 만들 수 있습니다.

# <a name="bash"></a>[Bash](#tab/bash)

```azurecli
az storage queue create \
  --name "myqueue" \
  --account-name $STORAGE_ACCOUNT \
  --connection-string $QUEUE_CONNECTION_STRING
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```powershell
az storage queue create `
  --name "myqueue" `
  --account-name $STORAGE_ACCOUNT `
  --connection-string $QUEUE_CONNECTION_STRING
```

---

마지막으로 큐에 메시지를 보낼 수 있습니다.

# <a name="bash"></a>[Bash](#tab/bash)

```azurecli
az storage message put \
  --content "Hello Queue Reader App" \
  --queue-name "myqueue" \
  --connection-string $QUEUE_CONNECTION_STRING
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```powershell
az storage message put `
  --content "Hello Queue Reader App" `
  --queue-name "myqueue" `
  --connection-string $QUEUE_CONNECTION_STRING
```

---

## <a name="deploy-the-background-application"></a>백그라운드 애플리케이션 배포

*queue.json이라는* 파일을 만들고 다음 구성 코드를 파일에 붙여넣습니다.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-08-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "location": {
            "defaultValue": "canadacentral",
            "type": "String"
        },
        "environment_name": {
            "defaultValue": "",
            "type": "String"
        },
        "queueconnection": {
            "defaultValue": "",
            "type": "String"
        }
    },
    "variables": {},
    "resources": [
    {
        "name": "queuereader",
        "type": "Microsoft.Web/containerApps",
        "apiVersion": "2021-03-01",
        "kind": "containerapp",
        "location": "[parameters('location')]",
        "properties": {
            "kubeEnvironmentId": "[resourceId('Microsoft.Web/kubeEnvironments', parameters('environment_name'))]",
            "configuration": {
                "activeRevisionsMode": "single",
                "secrets": [
                {
                    "name": "queueconnection",
                    "value": "[parameters('queueconnection')]"
                }]
            },
            "template": {
                "containers": [
                    {
                        "image": "mcr.microsoft.com/azuredocs/containerapps-queuereader",
                        "name": "queuereader",
                        "env": [
                            {
                                "name": "QueueName",
                                "value": "myqueue"
                            },
                            {
                                "name": "QueueConnectionString",
                                "secretref": "queueconnection"
                            }
                        ]
                    }
                ],
                "scale": {
                    "minReplicas": 1,
                    "maxReplicas": 10,
                    "rules": [
                        {
                            "name": "myqueuerule",
                            "azureQueue": {
                                "queueName": "myqueue",
                                "queueLength": 100,
                                "auth": [
                                    {
                                        "secretRef": "queueconnection",
                                        "triggerParameter": "connection"
                                    }
                                ]
                            }
                        }
                    ]
                }
            }
        }
    }]
}

```

이제 컨테이너 앱을 만들고 배포할 수 있습니다.

# <a name="bash"></a>[Bash](#tab/bash)

```azurecli
az deployment group create --resource-group "$RESOURCE_GROUP" \
  --template-file ./queue.json \
  --parameters \
    environment_name="$CONTAINERAPPS_ENVIRONMENT" \
    queueconnection="$QUEUE_CONNECTION_STRING" \
    location="$LOCATION"
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```powershell
az deployment group create --resource-group "$RESOURCE_GROUP" `
  --template-file ./queue.json `
  --parameters `
    environment_name="$CONTAINERAPPS_ENVIRONMENT" `
    queueconnection="$QUEUE_CONNECTION_STRING" `
    location="$LOCATION"
```

---

이 명령은 이라는 공용 컨테이너 이미지에서 데모 애플리케이션을 `mcr.microsoft.com/azuredocs/containerapps-queuereader` 배포하고 애플리케이션에서 사용하는 비밀 및 환경 변수를 설정합니다.

애플리케이션은 ARM 템플릿의 섹션에 정의된 큐 길이에 따라 최대 10개의 `scale` 복제본을 확장합니다.

## <a name="verify-the-result"></a>결과 확인

백그라운드 프로세스로 실행되는 컨테이너 앱은 메시지가 Azure Storage 큐에서 도착할 때 Log Analytics에 로그 항목을 만듭니다. 기록된 데이터를 쿼리하기 전에 분석이 처음으로 도착할 때까지 몇 분 정도 기다려야 할 수 있습니다.

다음 명령을 실행하여 기록된 메시지를 확인합니다. 이 명령에는 Log Analytics 확장이 필요하므로 요청 시 확장을 설치하라는 메시지를 수락합니다.

# <a name="bash"></a>[Bash](#tab/bash)

```azurecli
az monitor log-analytics query \
  --workspace $LOG_ANALYTICS_WORKSPACE_CLIENT_ID \
  --analytics-query "ContainerAppConsoleLogs_CL | where ContainerAppName_s == 'queuereader' and Log_s contains 'Message ID'" \
  --out table
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```azurecli
az monitor log-analytics query `
  --workspace $LOG_ANALYTICS_WORKSPACE_CLIENT_ID `
  --analytics-query "ContainerAppConsoleLogs_CL | where ContainerAppName_s == 'queuereader' and Log_s contains 'Message ID'" `
  --out table
```

---

> [!TIP]
> 문제가 있나요? [Azure Container Apps 리포지토리](https://github.com/microsoft/azure-container-apps)에서 문제를 열어 GitHub에 알려주세요.

## <a name="clean-up-resources"></a>리소스 정리

완료되면 다음 명령을 실행하여 Container Apps 리소스를 정리하여 리소스 그룹을 삭제합니다.

# <a name="bash"></a>[Bash](#tab/bash)

```azurecli
az group delete \
  --resource-group $RESOURCE_GROUP
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```powershell
az group delete `
  --resource-group $RESOURCE_GROUP
```

---

이 명령은 Container Apps 인스턴스, 스토리지 계정, Log Analytics 작업 영역 및 리소스 그룹의 다른 리소스를 포함하여 전체 리소스 그룹을 삭제합니다.
