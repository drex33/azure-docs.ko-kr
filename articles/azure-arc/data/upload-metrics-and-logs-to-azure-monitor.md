---
title: Azure에 사용량 데이터, 메트릭 및 로그 업로드
description: Azure에 리소스 인벤토리, 사용량 데이터, 메트릭 및 로그 업로드
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
ms.date: 07/30/2021
ms.topic: how-to
zone_pivot_groups: client-operating-system-macos-and-linux-windows-powershell
ms.openlocfilehash: 6f314b8918b415c0449722d1229c6de47af1cac5
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122536402"
---
# <a name="upload-usage-data-metrics-and-logs-to-azure"></a>Azure에 사용량 데이터, 메트릭 및 로그 업로드

정기적으로 청구 목적의 사용량 정보, 모니터링 메트릭 및 로그를 내보낸 후 Azure에 업로드할 수 있습니다. 이러한 세 유형의 데이터를 내보내고 업데이트하면 Azure에서 데이터 컨트롤러, SQL Managed Instance 및 PostgreSQL 하이퍼스케일 서버 그룹 리소스가 만들어지고 업데이트됩니다.

사용량 데이터, 메트릭 또는 로그를 업로드하려면 다음 작업을 수행해야 합니다.

* 도구 설치 
* [`Microsoft.AzureArcData` 리소스 공급자 등록](#register-the-resource-provider) 
* [서비스 주체 만들기](#create-service-principal)

## <a name="install-tools"></a>도구 설치

필요한 도구는 다음과 같습니다. 
* Azure CLI(az) 
* `arcdata` 확장 

[도구 설치](./install-client-tools.md)를 참조하세요.

## <a name="register-the-resource-provider"></a>리소스 공급자 등록

메트릭 또는 사용자 데이터를 Azure에 업로드하기 전에 Azure 구독에 `Microsoft.AzureArcData` 리소스 공급자가 등록되어 있는지 확인해야 합니다.

리소스 공급자를 확인하려면 다음 명령을 실행합니다.

```azurecli
az provider show -n Microsoft.AzureArcData -o table
```

리소스 공급자가 구독에 등록되지 않은 경우 등록할 수 있습니다. 등록하려면 다음 명령을 실행합니다.  이 명령을 완료하는 데 1~2분 정도 걸릴 수 있습니다.

```azurecli
az provider register -n Microsoft.AzureArcData --wait
```

## <a name="create-service-principal"></a>서비스 주체 만들기

서비스 주체는 사용량 및 메트릭 데이터를 업로드하는 데 사용됩니다.

다음 명령을 실행하여 메트릭 업로드 서비스 주체를 만듭니다.

> [!NOTE]
> 서비스 주체를 만들려면 [Azure의 특정 권한](../../active-directory/develop/howto-create-service-principal-portal.md#permissions-required-for-registering-an-app)이 필요합니다.

서비스 주체를 만들려면 다음 예제를 업데이트합니다. `<ServicePrincipalName>`, `SubscriptionId` 및 `resourcegroup`을 사용자의 값으로 바꾸고 명령을 실행합니다.

```azurecli
az ad sp create-for-rbac --name <ServicePrincipalName> --role Contributor --scopes /subscriptions/<SubscriptionId>/resourceGroups/<resourcegroup>
```

이전에 서비스 주체를 만들었고 현재 자격 증명만 가져와야 하는 경우 다음 명령을 실행하여 자격 증명을 다시 설정합니다.

```azurecli
az ad sp credential reset --name <ServicePrincipalName>
```

예를 들어 `azure-arc-metrics`라는 서비스 주체를 만들려면 다음 명령을 실행합니다.

```azurecli
az ad sp create-for-rbac --name azure-arc-metrics --role Contributor --scopes /subscriptions/a345c178a-845a-6a5g-56a9-ff1b456123z2/resourceGroups/myresourcegroup
```

예제 출력:

```output
"appId": "2e72adbf-de57-4c25-b90d-2f73f126e123",
"displayName": "azure-arc-metrics",
"name": "http://azure-arc-metrics",
"password": "5039d676-23f9-416c-9534-3bd6afc78123",
"tenant": "72f988bf-85f1-41af-91ab-2d7cd01ad1234"
```

나중에 사용하기 위해 `appId`, `password` 및 `tenant` 값을 환경 변수에 저장합니다. 

::: zone pivot="client-operating-system-windows-command"

```console
SET SPN_CLIENT_ID=<appId>
SET SPN_CLIENT_SECRET=<password>
SET SPN_TENANT_ID=<tenant>
```

::: zone-end

::: zone pivot="client-operating-system-macos-and-linux"

```console
export SPN_CLIENT_ID='<appId>'
export SPN_CLIENT_SECRET='<password>'
export SPN_TENANT_ID='<tenant>'
```

::: zone-end

::: zone pivot="client-operating-system-powershell"

```console
$Env:SPN_CLIENT_ID="<appId>"
$Env:SPN_CLIENT_SECRET="<password>"
$Env:SPN_TENANT_ID="<tenant>"
```

::: zone-end

서비스 주체를 만든 후에는 해당 역할에 서비스 주체를 할당합니다. 

## <a name="assign-roles-to-the-service-principal"></a>서비스 주체에 역할 할당

다음 명령을 실행하여 데이터베이스 인스턴스 리소스가 있는 구독의 `Monitoring Metrics Publisher` 역할에 서비스 주체를 할당합니다.

::: zone pivot="client-operating-system-windows-command"

> [!NOTE]
> Windows 환경에서 실행할 때 역할 이름에 큰따옴표를 사용해야 합니다.

```azurecli
az role assignment create --assignee <appId> --role "Monitoring Metrics Publisher" --scope subscriptions/<SubscriptionID>/resourceGroups/<resourcegroup>

```
::: zone-end

::: zone pivot="client-operating-system-macos-and-linux"

```azurecli
az role assignment create --assignee <appId> --role 'Monitoring Metrics Publisher' --scope subscriptions/<SubscriptionID>/resourceGroups/<resourcegroup>
```

::: zone-end

::: zone pivot="client-operating-system-powershell"

```powershell
az role assignment create --assignee <appId> --role 'Monitoring Metrics Publisher' --scope subscriptions/<SubscriptionID>/resourceGroups/<resourcegroup>
```

::: zone-end

예제 출력:

```output
{
  "canDelegate": null,
  "id": "/subscriptions/<Subscription ID>/providers/Microsoft.Authorization/roleAssignments/f82b7dc6-17bd-4e78-93a1-3fb733b912d",
  "name": "f82b7dc6-17bd-4e78-93a1-3fb733b9d123",
  "principalId": "5901025f-0353-4e33-aeb1-d814dbc5d123",
  "principalType": "ServicePrincipal",
  "roleDefinitionId": "/subscriptions/<Subscription ID>/providers/Microsoft.Authorization/roleDefinitions/3913510d-42f4-4e42-8a64-420c39005123",
  "scope": "/subscriptions/<Subscription ID>",
  "type": "Microsoft.Authorization/roleAssignments"
}
```

## <a name="verify-service-principal-role"></a>서비스 주체 역할 확인

```azurecli
az role assignment list -o table
```

적절한 역할에 할당된 서비스 주체를 사용하여 계속해서 메트릭 또는 사용자 데이터를 업로드할 수 있습니다. 



## <a name="upload-logs-metrics-or-usage-data"></a>로그, 메트릭 또는 사용량 데이터 업로드

로그, 메트릭 또는 사용량 데이터를 업로드하는 구체적인 단계는 업로드하는 정보의 유형에 따라 달라집니다. 

[Azure Monitor에 로그 업로드](upload-logs.md)

[Azure Monitor에 메트릭 업로드](upload-metrics.md)

[Azure에 사용량 데이터 업로드](upload-usage-data.md)

## <a name="general-guidance-on-exporting-and-uploading-usage-and-metrics"></a>사용량과 메트릭 내보내기 및 업로드에 대한 일반 지침

Azure Arc 지원 데이터 서비스에 대한 CRUD(만들기, 읽기, 업데이트 및 삭제) 작업은 청구 및 모니터링을 위해 기록됩니다. 이러한 CRUD 작업을 모니터링하고 소비량을 적절하게 계산하는 백그라운드 서비스가 있습니다. 사용량 또는 소비량의 실제 계산은 일정에 따라 수행되며 백그라운드에서 실행됩니다. 

사용량을 하루에 한 번만 업로드합니다. 사용량 정보를 동일한 24시간 내에 여러 번 내보내고 업로드하면 Azure Portal에서 리소스 인벤토리가 업데이트되지만 리소스 사용량은 업데이트되지 않습니다.

메트릭 업로드의 경우 Azure Monitor는 최근 30분 동안의 데이터만 허용합니다([자세한 정보](../../azure-monitor/essentials/metrics-store-custom-rest-api.md#troubleshooting)). 메트릭을 업로드하기 위한 지침은 내보내기 파일을 만든 후 즉시 메트릭을 업로드하여 Azure Portal에서 전체 데이터 세트를 볼 수 있도록 하는 것입니다. 예를 들어, 메트릭을 오후 2:00에 내보내고 오후 2:50에 업로드 명령을 실행한 경우 Azure Monitor는 최근 30분 동안의 데이터만 허용하므로 포털에 데이터가 표시되지 않을 수 있습니다. 

## <a name="next-steps"></a>다음 단계

[서비스 주체에 대해 자세히 알아보기](/powershell/azure/azurerm/create-azure-service-principal-azureps#what-is-a-service-principal)

[청구 데이터를 Azure에 업로드하고 Azure Portal에서 보기](view-billing-data-in-azure.md)

[Azure Portal에서 Azure Arc 데이터 컨트롤러 리소스 보기](view-data-controller-in-azure-portal.md)
