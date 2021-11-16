---
title: '빠른 시작: 첫 번째 컨테이너 앱 배포'
description: Azure Container Apps Preview에 첫 번째 애플리케이션을 배포합니다.
services: container-apps
author: craigshoemaker
ms.service: container-apps
ms.topic: quickstart
ms.date: 11/02/2021
ms.author: cshoe
ms.custom: ignite-fall-2021
ms.openlocfilehash: 68d446ca120ea9378c998cb81c2bf06579ffde7d
ms.sourcegitcommit: 4cd97e7c960f34cb3f248a0f384956174cdaf19f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/08/2021
ms.locfileid: "132028518"
---
# <a name="quickstart-deploy-your-first-container-app"></a>빠른 시작: 첫 번째 컨테이너 앱 배포

Azure Container Apps Preview를 사용하면 서버리스 플랫폼에서 마이크로서비스 및 컨테이너화된 애플리케이션을 실행할 수 있습니다. Container Apps를 사용하면 클라우드 인프라와 복잡한 컨테이너 오케스트레이터를 수동으로 구성해야 하는 걱정을 버리고 컨테이너를 실행하는 이점을 누릴 수 있습니다.

이 빠른 시작에서는 보안 Container Apps 환경을 만들고 첫 번째 컨테이너 앱을 배포합니다.

## <a name="prerequisites"></a>사전 요구 사항

- [Azure CLI](/cli/azure/install-azure-cli)를 설치합니다.

## <a name="setup"></a>설치 프로그램

CLI에서 Azure에 로그인하여 시작합니다. 다음 명령을 실행하고 프롬프트에 따라 인증 프로세스를 완료합니다.

# <a name="bash"></a>[Bash](#tab/bash)

```azurecli
az login
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```azurecli
az login
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

확장이 설치되었으므로 `Microsoft.Web` 네임스페이스를 등록합니다.

# <a name="bash"></a>[Bash](#tab/bash)

```azurecli
az provider register --namespace Microsoft.Web
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```azurecli
az provider register --namespace Microsoft.Web
```

---

다음으로, 다음 환경 변수를 설정합니다.

# <a name="bash"></a>[Bash](#tab/bash)

```azurecli
RESOURCE_GROUP="my-containerapps"
LOCATION="canadacentral"
LOG_ANALYTICS_WORKSPACE="containerapps-logs"
CONTAINERAPPS_ENVIRONMENT="containerapps-env"
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```powershell
$RESOURCE_GROUP="my-containerapps"
$LOCATION="canadacentral"
$LOG_ANALYTICS_WORKSPACE="containerapps-logs"
$CONTAINERAPPS_ENVIRONMENT="containerapps-env"
```

---

이러한 변수를 정의하면 리소스 그룹을 만들어 새 컨테이너 앱과 관련된 서비스를 구성할 수 있습니다.

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

Azure Container Apps의 환경은 컨테이너 앱 그룹 주위에 보안 경계를 만듭니다. 동일한 환경에 배포된 컨테이너 앱은 동일한 가상 네트워크에 배포되고 동일한 Log Analytics 작업 영역에 로그를 씁니다.

Azure Log Analytics는 컨테이너 앱 환경을 만들 때 필요한 컨테이너 앱을 모니터링하는 데 사용됩니다.

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

## <a name="create-a-container-app"></a>컨테이너 앱 만들기

이제 환경이 만들어졌으므로 첫 번째 컨테이너 앱을 배포할 수 있습니다. `containerapp create` 명령을 사용하여 컨테이너 이미지를 Azure Container Apps에 배포합니다.

# <a name="bash"></a>[Bash](#tab/bash)

```azurecli
az containerapp create \
  --name my-container-app \
  --resource-group $RESOURCE_GROUP \
  --environment $CONTAINERAPPS_ENVIRONMENT \
  --image mcr.microsoft.com/azuredocs/containerapps-helloworld:latest \
  --target-port 80 \
  --ingress 'external' \
  --query configuration.ingress.fqdn
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```azurecli
az containerapp create `
  --name my-container-app `
  --resource-group $RESOURCE_GROUP `
  --environment $CONTAINERAPPS_ENVIRONMENT `
  --image mcr.microsoft.com/azuredocs/containerapps-helloworld:latest `
  --target-port 80 `
  --ingress 'external' `
  --query configuration.ingress.fqdn
```

---

`--ingress`를 `external`로 설정하면 컨테이너 앱에서 퍼블릭 요청을 사용할 수 있습니다.

여기서 `create` 명령은 컨테이너 앱의 정규화된 도메인 이름을 반환합니다. 이 위치를 웹 브라우저에 복사하면 다음 메시지가 표시됩니다.

:::image type="content" source="media/get-started/azure-container-apps-quickstart.png" alt-text="첫 Azure Container Apps 배포.":::

## <a name="clean-up-resources"></a>리소스 정리

이 애플리케이션을 계속 사용하지 않으려면 리소스 그룹을 제거하여 Azure Container Apps 인스턴스 및 모든 관련 서비스를 삭제할 수 있습니다.

# <a name="bash"></a>[Bash](#tab/bash)

```azurecli
az group delete \
  --name $RESOURCE_GROUP
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```azurecli
az group delete `
  --name $RESOURCE_GROUP
```

---

> [!TIP]
> 문제가 있나요? [Azure Container Apps 리포지토리](https://github.com/microsoft/azure-container-apps)에서 문제를 열어 GitHub에 알려주세요.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [Azure Container Apps의 환경](environment.md)
