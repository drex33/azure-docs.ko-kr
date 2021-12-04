---
title: Azure Monitor에 메트릭 업로드
description: Azure Monitor에 Azure Arc 지원 데이터 서비스 메트릭을 업로드합니다.
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
ms.date: 11/03/2021
ms.topic: how-to
ms.openlocfilehash: d2a3a24ecd560b5040252e57df8eb9881652bad4
ms.sourcegitcommit: 1e9139680ca51f55ac965c4dd6dd82bf2fd43675
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/04/2021
ms.locfileid: "133540014"
---
# <a name="upload-metrics-to-azure-monitor"></a>Azure Monitor에 메트릭 업로드

정기적으로 모니터링 메트릭을 내보낸 다음 Azure에 업로드할 수 있습니다. 데이터를 내보내고 업데이트하면 Azure에서 데이터 컨트롤러, SQL Managed Instance 및 PostgreSQL 하이퍼스케일 서버 그룹 리소스가 만들어지고 업데이트됩니다.

Azure Arc 데이터 서비스를 사용하면 필요에 따라 메트릭을 Azure Monitor에 업로드하여 메트릭을 분석 및 집계하거나, 경고를 생성하거나, 알림을 보내거나, 자동화된 작업을 트리거할 수 있습니다. 

또한 데이터를 Azure Monitor로 보내면 메트릭 데이터를 오프사이트 및 대규모로 저장할 수 있으며, 고급 분석을 위해 데이터를 장기적으로 저장할 수 있습니다.

Azure Arc 데이터 서비스를 사용하는 여러 사이트가 있는 경우 Azure Monitor를 중앙 위치로 사용하여 사이트 전체에서 모든 로그와 메트릭을 수집할 수 있습니다.

## <a name="upload-metrics-for-azure-arc-data-controller-in-direct-mode"></a>직접 모드에서 Azure Arc 데이터 컨트롤러에 대한 메트릭 **업로드**

**직접** 연결 모드에서 메트릭 업로드는 **자동** 모드에서만 설정할 수 있습니다. 이 메트릭 자동 업로드는 Azure Arc 데이터 컨트롤러를 배포하는 동안 또는 배포 후 설정할 수 있습니다.
Arc 데이터 서비스 확장 관리 ID는 메트릭을 업로드하는 데 사용됩니다. 관리 ID에는 **모니터링 메트릭 Publisher** 역할이 할당되어 있어야 합니다. 

> [!NOTE]
> Azure Arc 데이터 컨트롤러 배포 중에 메트릭의 자동 업로드를 사용하지 않도록 설정한 경우 자동 업로드를 사용하도록 설정하기 전에 먼저 Arc 데이터 컨트롤러 확장의 관리 ID를 검색하고 **모니터링 메트릭 Publisher** 역할을 부여해야 합니다. 아래 단계에 따라 관리 ID를 검색하고 필요한 역할을 부여합니다.   

[!INCLUDE [azure-arc-angle-bracket-example](../../../includes/azure-arc-angle-bracket-example.md)]

### <a name="1-retrieve-managed-identity-of-the-arc-data-controller-extension"></a>(1) Arc 데이터 컨트롤러 확장의 관리 ID 검색

# <a name="powershell"></a>[PowerShell](#tab/powershell)
```powershell
$Env:MSI_OBJECT_ID = (az k8s-extension show --resource-group <resource group>  --cluster-name <connectedclustername> --cluster-type connectedClusters --name <name of extension> | convertFrom-json).identity.principalId
#Example
$Env:MSI_OBJECT_ID = (az k8s-extension show --resource-group myresourcegroup  --cluster-name myconnectedcluster --cluster-type connectedClusters --name ads-extension | convertFrom-json).identity.principalId
```

# <a name="macos--linux"></a>[macOS & Linux](#tab/linux)
```console
export MSI_OBJECT_ID=`az k8s-extension show --resource-group <resource group>  --cluster-name <connectedclustername>  --cluster-type connectedClusters --name <name of extension> | jq '.identity.principalId' | tr -d \"`
#Example
export MSI_OBJECT_ID=`az k8s-extension show --resource-group myresourcegroup  --cluster-name myconnectedcluster --cluster-type connectedClusters --name ads-extension | jq '.identity.principalId' | tr -d \"`
```

# <a name="windows"></a>[Windows](#tab/windows)

해당 없음

---

### <a name="2-assign-role-to-the-managed-identity"></a>(2) 관리 ID에 역할 할당

아래 명령을 실행하여 **모니터링 메트릭 게시자** 역할을 할당합니다.
# <a name="powershell"></a>[PowerShell](#tab/powershell)
```powershell
az role assignment create --assignee $Env:MSI_OBJECT_ID --role 'Monitoring Metrics Publisher' --scope "/subscriptions/$SUBSCRIPTION_ID/resourceGroups/$RESOURCE_GROUP_NAME"
```

# <a name="macos--linux"></a>[macOS & Linux](#tab/linux)
```console
az role assignment create --assignee ${MSI_OBJECT_ID} --role 'Monitoring Metrics Publisher' --scope "/subscriptions/${subscription}/resourceGroups/${resourceGroup}"
```

# <a name="windows"></a>[Windows](#tab/windows)

해당 없음

---

### <a name="automatic-upload-of-metrics-can-be-enabled-as-follows"></a>메트릭의 자동 업로드는 다음과 같이 사용하도록 설정할 수 있습니다.
```
az arcdata dc update --name <name of datacontroller> --resource-group <resource group> --auto-upload-metrics true
#Example
az arcdata dc update --name arcdc --resource-group <myresourcegroup> --auto-upload-metrics true
```

Azure Monitor 메트릭 자동 업로드를 사용하지 않도록 설정하려면 다음 명령을 실행합니다.
```
az arcdata dc update --name <name of datacontroller> --resource-group <resource group> --auto-upload-metrics false
#Example
az arcdata dc update --name arcdc --resource-group <myresourcegroup> --auto-upload-metrics false
```

## <a name="upload-metrics-for-azure-arc-data-controller-in-indirect-mode"></a>**간접** 모드에서 Azure Arc 데이터 컨트롤러에 대한 업로드 메트릭

**간접** 연결 모드에서는 서비스 주체가 메트릭을 업로드하는 데 사용됩니다.

### <a name="prerequisites"></a>사전 요구 사항

계속 진행하기 전에 필요한 서비스 주체를 만들고 적절한 역할에 할당했는지 확인합니다. 자세한 내용은 다음을 참조하세요.
* [서비스 주체 만들기](upload-metrics-and-logs-to-azure-monitor.md#create-service-principal).
* [서비스 주체에게 역할 할당](upload-metrics-and-logs-to-azure-monitor.md#assign-roles-to-the-service-principal)

### <a name="set-environment-variables-and-confirm"></a>환경 변수 설정 및 확인

환경 변수에 SPN 기관 URL을 설정합니다.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```PowerShell
$Env:SPN_AUTHORITY='https://login.microsoftonline.com'
```

# <a name="macos--linux"></a>[macOS & Linux](#tab/linux)

```console
export SPN_AUTHORITY='https://login.microsoftonline.com'
```

# <a name="windows"></a>[Windows](#tab/windows)

```console
SET SPN_AUTHORITY=https://login.microsoftonline.com
```

---

원하는 경우 다음과 같이 필요한 모든 환경 변수를 설정했는지 확인합니다.


# <a name="powershell"></a>[PowerShell](#tab/powershell)

```PowerShell
$Env:SPN_TENANT_ID
$Env:SPN_CLIENT_ID
$Env:SPN_CLIENT_SECRET
$Env:SPN_AUTHORITY
```


# <a name="macos--linux"></a>[macOS & Linux](#tab/linux)

```console
echo $SPN_TENANT_ID
echo $SPN_CLIENT_ID
echo $SPN_CLIENT_SECRET
echo $SPN_AUTHORITY
```

# <a name="windows"></a>[Windows](#tab/windows)

```console
echo %SPN_TENANT_ID%
echo %SPN_CLIENT_ID%
echo %SPN_CLIENT_SECRET%
echo %SPN_AUTHORITY%
```

---

### <a name="upload-metrics-to-azure-monitor"></a>Azure Monitor에 메트릭 업로드

Azure Arc 지원 SQL Managed Instance와 Azure Arc 지원 PostgreSQL 하이퍼스케일 서버 그룹에 대한 메트릭을 업로드하려면 다음 CLI 명령을 실행합니다.

 
1. 모든 메트릭을 지정된 파일로 내보냅니다.

> [!NOTE]
> `az arcdata dc export` 명령을 사용하여 사용량/결제 정보, 메트릭 및 로그를 내보내려면 현재로서는 SSL 확인을 바이패스해야 합니다.  SSL 확인을 바이패스하라는 메시지가 표시되거나 메시지가 표시되지 않도록 `AZDATA_VERIFY_SSL=no` 환경 변수를 설정할 수 있습니다.  현재 데이터 컨트롤러 내보내기 API에 대한 SSL 인증서를 구성할 수 있는 방법이 없습니다.

   ```azurecli
   az arcdata dc export --type metrics --path metrics.json --k8s-namespace arc
   ```

2. Azure Monitor에 메트릭을 업로드합니다.

   ```azurecli
   az arcdata dc upload --path metrics.json
   ```

   >[!NOTE]
   >첫 번째 업로드에 대해 Azure Arc 지원 데이터 인스턴스를 만든 후 30분 이상 기다립니다.
   >
   >Azure Monitor는 최근 30분 동안의 메트릭만 허용하므로 `export` 직후에 메트릭을 `upload`해야 합니다. [자세히 알아봅니다](../../azure-monitor/essentials/metrics-store-custom-rest-api.md#troubleshooting).


내보내기 중에 '메트릭 가져오기 실패'를 나타내는 오류가 표시되는 경우 다음 명령을 실행하여 데이터 수집이 `true`로 설정되었는지 확인합니다.

```azurecli
az arcdata dc config show  --k8s-namespace arc --use-k8s
```

'보안 섹션' 아래를 살펴봅니다.

```output
 "security": {
      "allowDumps": true,
      "allowNodeMetricsCollection": true,
      "allowPodMetricsCollection": true,
    },
```

`allowNodeMetricsCollection` 및 `allowPodMetricsCollection` 속성이 `true`로 설정되었는지 확인합니다.

## <a name="view-the-metrics-in-the-portal"></a>포털에서 메트릭 보기

업로드한 메트릭은 Azure Portal에서 볼 수 있습니다.
> [!NOTE]
> 업로드된 데이터가 처리되어 포털에서 메트릭을 볼 수 있으려면 몇 분 정도 걸릴 수 있습니다.


포털에서 메트릭을 보려면 <https://portal.azure.com> 링크를 사용하여 포털을 엽니다. 그런 다음 검색 창에서 이름으로 데이터베이스 인스턴스를 검색합니다.

개요 페이지에서 CPU 사용률을 볼 수 있으며, 더 자세한 메트릭을 원하는 경우 왼쪽 탐색 패널에서 메트릭을 클릭할 수 있습니다.

메트릭 네임스페이스로 sql server를 선택합니다.

시각화할 메트릭을 선택합니다(여러 항목을 선택할 수 있음).

빈도를 최근 30분으로 변경합니다.

> [!NOTE]
> 최근 30분 동안의 메트릭만 업로드할 수 있습니다. Azure Monitor는 30분보다 오래된 메트릭을 거부합니다.

## <a name="automating-uploads-optional"></a>업로드 자동화(선택 사항)

일정에 따라 메트릭과 로그를 업로드하려는 경우 스크립트를 만들어 몇 분마다 타이머에서 실행되도록 할 수 있습니다. 다음은 Linux 셸 스크립트를 사용하여 업로드를 자동화하는 예제입니다.

자주 사용하는 텍스트/코드 편집기에서 다음 스크립트를 파일에 추가하고 as .sh(Linux/Mac) 또는 .cmd, .bat, .ps1과 같은 스크립트 실행 파일로 저장합니다.

```azurecli
az arcdata dc export --type metrics --path metrics.json --force  --k8s-namespace arc
az arcdata dc upload --path metrics.json
```

스크립트 파일을 실행 가능하도록 설정합니다.

```console
chmod +x myuploadscript.sh
```

20분마다 스크립트를 실행합니다.

```console
watch -n 1200 ./myuploadscript.sh
```

Cron 또는 Windows 작업 스케줄러와 같은 작업 스케줄러를 사용하거나 Ansible, Puppet 또는 Chef와 같은 오케스트레이터를 사용할 수도 있습니다.

## <a name="general-guidance-on-exporting-and-uploading-usage-metrics"></a>사용량, 메트릭 내보내기 및 업로드에 대한 일반 지침

Azure Arc 지원 데이터 서비스에 대한 CRUD(만들기, 읽기, 업데이트 및 삭제) 작업은 청구 및 모니터링을 위해 기록됩니다. 이러한 CRUD 작업을 모니터링하고 소비량을 적절하게 계산하는 백그라운드 서비스가 있습니다. 사용량 또는 소비량의 실제 계산은 일정에 따라 수행되며 백그라운드에서 실행됩니다. 

사용량을 하루에 한 번만 업로드합니다. 사용량 정보를 동일한 24시간 내에 여러 번 내보내고 업로드하면 Azure Portal에서 리소스 인벤토리가 업데이트되지만 리소스 사용량은 업데이트되지 않습니다.

메트릭 업로드의 경우 Azure Monitor는 최근 30분 동안의 데이터만 허용합니다([자세한 정보](../../azure-monitor/essentials/metrics-store-custom-rest-api.md#troubleshooting)). 메트릭을 업로드하기 위한 지침은 내보내기 파일을 만든 후 즉시 메트릭을 업로드하여 Azure Portal에서 전체 데이터 세트를 볼 수 있도록 하는 것입니다. 예를 들어, 메트릭을 오후 2:00에 내보내고 오후 2:50에 업로드 명령을 실행한 경우 Azure Monitor는 최근 30분 동안의 데이터만 허용하므로 포털에 데이터가 표시되지 않을 수 있습니다. 

## <a name="next-steps"></a>다음 단계

[Azure Monitor에 로그 업로드](upload-logs.md)

[Azure Monitor에 사용량 데이터, 메트릭 및 로그 업로드](upload-usage-data.md)

[청구 데이터를 Azure에 업로드하고 Azure Portal에서 보기](view-billing-data-in-azure.md)

[Azure Portal에서 Azure Arc 데이터 컨트롤러 리소스 보기](view-data-controller-in-azure-portal.md)
