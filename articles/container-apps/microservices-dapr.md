---
title: '자습서: Azure CLI 사용하여 Azure Container Apps에 Dapr 애플리케이션 배포'
description: Azure CLI 사용하여 Azure Container Apps에 Dapr 애플리케이션을 배포합니다.
services: app-service
author: asw101
ms.service: app-service
ms.topic: conceptual
ms.date: 11/02/2021
ms.author: aawislan
ms.custom: ignite-fall-2021
ms.openlocfilehash: 194368627e18899521d45607d83dfaaad195ad8b
ms.sourcegitcommit: 591ffa464618b8bb3c6caec49a0aa9c91aa5e882
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/06/2021
ms.locfileid: "131893503"
---
# <a name="tutorial-deploy-a-dapr-application-to-azure-container-apps-using-the-azure-cli"></a>자습서: Azure CLI 사용하여 Azure Container Apps에 Dapr 애플리케이션 배포

[Dapr(분산](https://dapr.io/) Application Runtime)은 복원력 있는 상태 비 상태 비지정 마이크로서비스를 빌드하는 데 도움이 되는 런타임입니다. 이 자습서에서는 샘플 Dapr 애플리케이션이 Azure Container Apps에 배포됩니다.

다음 방법을 알아봅니다.

> [!div class="checklist"]
> * 컨테이너 앱에 대한 Container Apps 환경 만들기
> * 컨테이너 앱에 대한 Azure Blob Storage 상태 저장소 만들기
> * 가 메시지를 생성하고 사용하는 두 개의 앱을 배포하고 상태 저장소를 사용하여 유지합니다.
> * 두 마이크로 서비스 간의 상호 작용을 확인합니다.

Azure Container Apps는 마이크로서비스를 빌드할 때 완전히 관리되는 버전의 Dapr API를 제공합니다. Azure Container Apps에서 Dapr을 사용하는 경우 다양한 기능을 제공하는 마이크로 서비스 옆에 사이드카를 실행할 수 있습니다. 사용 가능한 Dapr API에는 [서비스 대 서비스 호출,](https://docs.dapr.io/developing-applications/building-blocks/service-invocation/) [Pub/Sub,](https://docs.dapr.io/developing-applications/building-blocks/pubsub/) [이벤트 바인딩,](https://docs.dapr.io/developing-applications/building-blocks/bindings/) [상태 저장소](https://docs.dapr.io/developing-applications/building-blocks/state-management/)및 행위자 가 [포함됩니다.](https://docs.dapr.io/developing-applications/building-blocks/actors/)

이 자습서에서는 메시지를 생성하는 클라이언트(Python) 앱과 구성된 상태 저장소에서 해당 메시지를 사용하고 유지하는 서비스(노드) 앱으로 구성된 Dapr [Hello World](https://github.com/dapr/quickstarts/tree/master/hello-kubernetes) 빠른 시작에서 동일한 애플리케이션을 배포합니다. 다음 아키텍처 다이어그램에서는 이 자습서를 구성하는 구성 요소를 보여 줍니다.

:::image type="content" source="media/microservices-dapr/azure-container-apps-microservices-dapr.png" alt-text="Azure Container Apps의 Dapr Hello World 마이크로서비스에 대한 아키텍처 다이어그램":::

## <a name="prerequisites"></a>필수 구성 요소

* [Azure CLI](/cli/azure/install-azure-cli)

## <a name="before-you-begin"></a>시작하기 전에

이 가이드에서는 다음 환경 변수를 사용합니다.

# <a name="bash"></a>[Bash](#tab/bash)

```bash
RESOURCE_GROUP="my-containerapps"
LOCATION="canadacentral"
CONTAINERAPPS_ENVIRONMENT="containerapps-env"
LOG_ANALYTICS_WORKSPACE="containerapps-logs"
STORAGE_ACCOUNT_CONTAINER="mycontainer"
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```powershell
$RESOURCE_GROUP="my-containerapps"
$LOCATION="canadacentral"
$CONTAINERAPPS_ENVIRONMENT="containerapps-env"
$LOG_ANALYTICS_WORKSPACE="containerapps-logs"
$STORAGE_ACCOUNT_CONTAINER="mycontainer"
```

---

위의 조각은 bash, zsh 또는 PowerShell을 사용하여 환경 변수를 설정하는 데 사용할 수 있습니다.

# <a name="bash"></a>[Bash](#tab/bash)

```bash
STORAGE_ACCOUNT="<storage account name>"
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```powershell
$STORAGE_ACCOUNT="<storage account name>"
```

---

`STORAGE_ACCOUNT`에 대한 이름을 선택합니다. 다음 단계에서 만들어집니다. Storage 계정 이름은 Azure *내에서 고유해야* 하며 길이가 3~24자 사이여야 하며 숫자와 소문자만 포함할 수 있습니다.

## <a name="setup"></a>설치 프로그램

CLI에서 Azure에 로그인하여 시작합니다.

다음 명령을 실행하고 프롬프트에 따라 인증 프로세스를 완료합니다.

# <a name="bash"></a>[Bash](#tab/bash)

```azurecli
az login
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```azurecli
az login
```

---

업그레이드 명령을 통해 최신 버전의 CLI를 실행하고 있는지 확인합니다.

# <a name="bash"></a>[Bash](#tab/bash)

```azurecli
az upgrade
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```azurecli
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

```azurecli
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

```azurecli
az provider register --namespace Microsoft.Web
```

---

리소스 그룹을 만들어 새 컨테이너 앱과 관련된 서비스를 구성합니다.

# <a name="bash"></a>[Bash](#tab/bash)

```azurecli
az group create \
  --name $RESOURCE_GROUP \
  --location "$LOCATION"
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```azurecli
az group create `
  --name $RESOURCE_GROUP `
  --location "$LOCATION"
```

---

CLI가 업그레이드되고 새 리소스 그룹이 사용 가능해지면 컨테이너 앱 환경을 만들고 컨테이너 앱을 배포할 수 있습니다.

## <a name="create-an-environment"></a>환경 만들기

Azure Container Apps 환경은 컨테이너 앱 그룹 간의 격리 경계 역할을 합니다. 동일한 환경에 배포된 컨테이너 앱은 동일한 가상 네트워크에 배포되고 동일한 Log Analytics 작업 영역에 로그를 씁니다.

Azure Log Analytics는 컨테이너 앱을 모니터링하는 데 사용되며 Container Apps 환경을 만들 때 필요합니다.

다음 명령을 사용하여 새 Log Analytics 작업 영역을 만듭니다.

# <a name="bash"></a>[Bash](#tab/bash)

```azurecli
az monitor log-analytics workspace create \
  --resource-group $RESOURCE_GROUP \
  --workspace-name $LOG_ANALYTICS_WORKSPACE
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```azurecli
az monitor log-analytics workspace create `
  --resource-group $RESOURCE_GROUP `
  --workspace-name $LOG_ANALYTICS_WORKSPACE
```

---

다음으로 Log Analytics 클라이언트 ID 및 클라이언트 암호를 검색합니다.

# <a name="bash"></a>[Bash](#tab/bash)

요청이 완료될 수 있도록 충분한 시간을 주기 위해 각 쿼리를 별도로 실행해야 합니다.

```bash
LOG_ANALYTICS_WORKSPACE_CLIENT_ID=`az monitor log-analytics workspace show --query customerId -g $RESOURCE_GROUP -n $LOG_ANALYTICS_WORKSPACE --out tsv`
```

```bash
LOG_ANALYTICS_WORKSPACE_CLIENT_SECRET=`az monitor log-analytics workspace get-shared-keys --query primarySharedKey -g $RESOURCE_GROUP -n $LOG_ANALYTICS_WORKSPACE --out tsv`
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

요청이 완료될 수 있도록 충분한 시간을 주기 위해 각 쿼리를 별도로 실행해야 합니다.

```powershell
$LOG_ANALYTICS_WORKSPACE_CLIENT_ID=(az monitor log-analytics workspace show --query customerId -g $RESOURCE_GROUP -n $LOG_ANALYTICS_WORKSPACE --out tsv)
```

```powershell
$LOG_ANALYTICS_WORKSPACE_CLIENT_SECRET=(az monitor log-analytics workspace get-shared-keys --query primarySharedKey -g $RESOURCE_GROUP -n $LOG_ANALYTICS_WORKSPACE --out tsv)
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

```azurecli
az containerapp env create `
  --name $CONTAINERAPPS_ENVIRONMENT `
  --resource-group $RESOURCE_GROUP `
  --logs-workspace-id $LOG_ANALYTICS_WORKSPACE_CLIENT_ID `
  --logs-workspace-key $LOG_ANALYTICS_WORKSPACE_CLIENT_SECRET `
  --location "$LOCATION"
```

---

## <a name="set-up-a-state-store"></a>상태 저장소 설정

### <a name="create-an-azure-blob-storage-account"></a>Azure Blob Storage 계정 만들기

다음 명령을 사용하여 새 Azure Storage 계정을 만듭니다.

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

```azurecli
az storage account create `
  --name $STORAGE_ACCOUNT `
  --resource-group $RESOURCE_GROUP `
  --location "$LOCATION" `
  --sku Standard_RAGRS `
  --kind StorageV2
```

---

Azure Blob Storage 계정이 만들어지면 이 자습서의 후속 단계에 다음 값이 필요합니다.

* `storage_account_name` 는 `STORAGE_ACCOUNT` 위에서 선택한 변수의 값입니다.

* `storage_container_name` 는 위에서 `STORAGE_ACCOUNT_CONTAINER` 정의한 값입니다(예: `mycontainer` ). dapr은 Azure Storage 계정에 컨테이너가 없는 경우 이 이름으로 컨테이너를 만듭니다.

다음 명령을 통해 스토리지 계정 키를 얻습니다.

# <a name="bash"></a>[Bash](#tab/bash)

```bash
STORAGE_ACCOUNT_KEY=`az storage account keys list --resource-group $RESOURCE_GROUP --account-name $STORAGE_ACCOUNT --query '[0].value' --out tsv`
```

```bash
echo $STORAGE_ACCOUNT_KEY
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```powershell
$STORAGE_ACCOUNT_KEY=(az storage account keys list --resource-group $RESOURCE_GROUP --account-name $STORAGE_ACCOUNT --query '[0].value' --out tsv)
```

```powershell
echo $STORAGE_ACCOUNT_KEY
```

---

### <a name="configure-the-state-store-component"></a>상태 저장소 구성 요소 구성

위의 단계에서 원본으로 만든 속성을 사용하여 *components.yaml이라는* 구성 파일을 만듭니다. 이 파일을 사용하면 Dapr 앱이 상태 저장소에 액세스할 수 있습니다. 다음 예제에서는 Azure Blob Storage 계정에 대해 구성할 때 *components.yaml* 파일이 어떻게 표시되는지 보여 줍니다.

```yaml
# components.yaml for Azure Blob storage component
- name: statestore
  type: state.azure.blobstorage
  version: v1
  metadata:
  # Note that in a production scenario, account keys and secrets 
  # should be securely stored. For more information, see
  # https://docs.dapr.io/operations/components/component-secrets
  - name: accountName
    value: <YOUR_STORAGE_ACCOUNT_NAME>
  - name: accountKey
    value: <YOUR_STORAGE_ACCOUNT_KEY>
  - name: containerName
    value: <YOUR_STORAGE_CONTAINER_NAME>
```

이 파일을 사용하려면 대괄호 사이의 자리 표시자 값을 `<>` 사용자 고유의 값으로 바꿔야 합니다.

> [!NOTE]
> Container Apps는 현재 네이티브 [Dapr 구성 요소 스키마 를](https://docs.dapr.io/operations/components/component-schema/)지원하지 않습니다. 위의 예제에서는 지원되는 스키마를 사용합니다.
>
> 프로덕션급 애플리케이션에서 비밀 [관리](https://docs.dapr.io/operations/components/component-secrets) 지침에 따라 비밀을 안전하게 관리합니다.


## <a name="deploy-the-service-application-http-web-server"></a>서비스 애플리케이션 배포(HTTP 웹 서버)

*components.yaml* 파일을 저장한 디렉터리로 이동하고 아래 명령을 실행하여 서비스 컨테이너 앱을 배포합니다.

# <a name="bash"></a>[Bash](#tab/bash)

```azurecli
az containerapp create \
  --name nodeapp \
  --resource-group $RESOURCE_GROUP \
  --environment $CONTAINERAPPS_ENVIRONMENT \
  --image dapriosamples/hello-k8s-node:latest \
  --target-port 3000 \
  --ingress 'external' \
  --min-replicas 1 \
  --max-replicas 1 \
  --enable-dapr \
  --dapr-app-port 3000 \
  --dapr-app-id nodeapp \
  --dapr-components ./components.yaml
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```azurecli
az containerapp create `
  --name nodeapp `
  --resource-group $RESOURCE_GROUP `
  --environment $CONTAINERAPPS_ENVIRONMENT `
  --image dapriosamples/hello-k8s-node:latest `
  --target-port 3000 `
  --ingress 'external' `
  --min-replicas 1 `
  --max-replicas 1 `
  --enable-dapr `
  --dapr-app-port 3000 `
  --dapr-app-id nodeapp `
  --dapr-components ./components.yaml
```

---

이 명령은 서비스 검색 `--target-port 3000` 및 호출을 위해 및 로 구성된 함께 제공되는 Dapr 사이드카와 함께 서비스(노드) 앱 서버를 `--dapr-app-id nodeapp` (앱의 포트)에 `--dapr-app-port 3000` 배포합니다. 상태 저장소는 사이드카가 상태를 유지할 수 있도록 하는 를 사용하여 `--dapr-components ./components.yaml` 구성됩니다.


## <a name="deploy-the-client-application-headless-client"></a>클라이언트 애플리케이션(헤드리스 클라이언트) 배포

아래 명령을 실행하여 클라이언트 컨테이너 앱을 배포합니다.

# <a name="bash"></a>[Bash](#tab/bash)

```azurecli
az containerapp create \
  --name pythonapp \
  --resource-group $RESOURCE_GROUP \
  --environment $CONTAINERAPPS_ENVIRONMENT \
  --image dapriosamples/hello-k8s-python:latest \
  --min-replicas 1 \
  --max-replicas 1 \
  --enable-dapr \
  --dapr-app-id pythonapp
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```azurecli
az containerapp create `
  --name pythonapp `
  --resource-group $RESOURCE_GROUP `
  --environment $CONTAINERAPPS_ENVIRONMENT `
  --image dapriosamples/hello-k8s-python:latest `
  --min-replicas 1 `
  --max-replicas 1 `
  --enable-dapr `
  --dapr-app-id pythonapp
```

---

이 명령은 `pythonapp` 에 대한 Dapr 사이드카를 조회하고 안전하게 호출하는 데 사용되는 Dapr 사이드카와 함께 실행되는 를 `nodeapp` 배포합니다. 이 앱은 헤드리스이기 때문에 서버를 시작할 필요가 `--target-port` 없으며 수신을 사용하도록 설정할 필요도 없습니다.

## <a name="verify-the-result"></a>결과 확인

### <a name="confirm-successful-state-persistence"></a>성공적인 상태 지속성 확인

Azure Storage 계정에서 데이터를 확인하여 서비스가 제대로 작동하는지 확인할 수 있습니다.

1. 브라우저에서 [Azure Portal](https://portal.azure.com) 열고 스토리지 계정으로 이동합니다.

1. 왼쪽에서 **컨테이너를** 선택합니다.

1. **mycontainer** 를 선택합니다.

1. 컨테이너에 라는 파일이 `order` 표시되는지 확인합니다.

1. 파일을 클릭합니다.

1. **편집** 탭을 클릭합니다.

1. 새로 **고침** 단추를 클릭하여 데이터가 자동으로 업데이트하는 방법을 관찰합니다.

### <a name="view-logs"></a>로그 보기

컨테이너 앱을 통해 기록된 데이터는 `ContainerAppConsoleLogs_CL` Log Analytics 작업 영역의 사용자 지정 테이블에 저장됩니다. Azure Portal 또는 CLI를 통해 로그를 볼 수 있습니다. 기록된 데이터를 쿼리하기 전에 분석이 처음으로 도착할 때까지 몇 분 정도 기다려야 할 수 있습니다.

다음 CLI 명령을 사용하여 명령줄에서 로그를 봅니다.

# <a name="bash"></a>[Bash](#tab/bash)

```azurecli
az monitor log-analytics query \
  --workspace $LOG_ANALYTICS_WORKSPACE_CLIENT_ID \
  --analytics-query "ContainerAppConsoleLogs_CL | where ContainerAppName_s == 'nodeapp' and (Log_s contains 'persisted' or Log_s contains 'order') | project ContainerAppName_s, Log_s, TimeGenerated | take 5" \
  --out table
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```azurecli
az monitor log-analytics query `
  --workspace $LOG_ANALYTICS_WORKSPACE_CLIENT_ID `
  --analytics-query "ContainerAppConsoleLogs_CL | where ContainerAppName_s == 'nodeapp' and (Log_s contains 'persisted' or Log_s contains 'order') | project ContainerAppName_s, Log_s, TimeGenerated | take 5" `
  --out table
```

---

다음 출력에서는 CLI 명령에서 예상할 응답 형식을 보여 주는 출력입니다.

```console
ContainerAppName_s    Log_s                            TableName      TimeGenerated
--------------------  -------------------------------  -------------  ------------------------
nodeapp               Got a new order! Order ID: 61    PrimaryResult  2021-10-22T21:31:46.184Z
nodeapp               Successfully persisted state.    PrimaryResult  2021-10-22T21:31:46.184Z
nodeapp               Got a new order! Order ID: 62    PrimaryResult  2021-10-22T22:01:57.174Z
nodeapp               Successfully persisted state.    PrimaryResult  2021-10-22T22:01:57.174Z
nodeapp               Got a new order! Order ID: 63    PrimaryResult  2021-10-22T22:45:44.618Z
```

> [!TIP]
> 문제가 있나요? [Azure Container Apps 리포지토리](https://github.com/microsoft/azure-container-apps)에서 문제를 열어 GitHub에 알려주세요.

## <a name="clean-up-resources"></a>리소스 정리

완료되면 다음 명령을 실행하여 컨테이너 앱 리소스를 정리하여 리소스 그룹을 삭제합니다.

# <a name="bash"></a>[Bash](#tab/bash)

```azurecli
az group delete \
    --resource-group $RESOURCE_GROUP
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```azurecli
az group delete `
    --resource-group $RESOURCE_GROUP
```

---

이 명령은 컨테이너 앱, 스토리지 계정, 컨테이너 앱 환경 및 리소스 그룹의 다른 리소스를 모두 삭제합니다.

> [!NOTE]
> `pythonapp`구성된 상태 저장소에 유지되는 메시지를 사용하여 를 지속적으로 호출하기 때문에 `nodeapp` 지속적인 청구 가능 작업을 방지하려면 이러한 정리 단계를 완료하는 것이 중요합니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [애플리케이션 수명 주기 관리](application-lifecycle-management.md)
