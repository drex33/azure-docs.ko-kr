---
title: Azure Monitor에 로그 업로드
description: Azure Monitor에 Azure Arc 지원 데이터 서비스 로그 업로드
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
ms.date: 07/30/2021
ms.topic: how-to
zone_pivot_groups: client-operating-system-macos-and-linux-windows-powershell
ms.openlocfilehash: 812b6add11ac032eb6dffea7de54111574393cf9
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122528585"
---
# <a name="upload-logs-to-azure-monitor"></a>Azure Monitor에 로그 업로드

정기적으로 로그를 내보낸 후 Azure에 업로드할 수 있습니다. 로그를 내보내 업로드하면 Azure에서 데이터 컨트롤러, SQL Managed Instance, PostgreSQL 하이퍼스케일 서버 그룹 리소스가 생성되고 업데이트됩니다.

## <a name="before-you-begin"></a>시작하기 전에

로그를 업로드하려면 먼저 다음을 수행해야 합니다. 

1. [Log Analytics 작업 영역 만들기](#create-a-log-analytics-workspace)
1. [환경 변수에 ID 및 공유 키 할당](#assign-id-and-shared-key-to-environment-variables)

## <a name="create-a-log-analytics-workspace"></a>Log Analytics 작업 영역 만들기

Log analytics 작업 영역을 만들려면 다음 명령을 실행하여 Log Analytics 작업 영역을 만들고 액세스 정보를 환경 변수로 설정합니다.

> [!NOTE]
> 작업 영역이 이미 있는 경우 이 단계를 건너뛸 수 있습니다.

```azurecli
az monitor log-analytics workspace create --resource-group <resource group name> --workspace-name <some name you choose>
```

예제 출력:

```output
{
  "customerId": "d6abb435-2626-4df1-b887-445fe44a4123",
  "eTag": null,
  "id": "/subscriptions/<Subscription ID>/resourcegroups/user-arc-demo/providers/microsoft.operationalinsights/workspaces/user-logworkspace",
  "location": "eastus",
  "name": "user-logworkspace",
  "portalUrl": null,
  "provisioningState": "Succeeded",
  "resourceGroup": "user-arc-demo",
  "retentionInDays": 30,
  "sku": {
    "lastSkuUpdate": "Thu, 30 Jul 2020 22:37:53 GMT",
    "maxCapacityReservationLevel": 3000,
    "name": "pergb2018"
  },
  "source": "Azure",
  "tags": null,
  "type": "Microsoft.OperationalInsights/workspaces"
}
```

## <a name="assign-id-and-shared-key-to-environment-variables"></a>환경 변수에 ID 및 공유 키 할당

나중에 사용하기 위해 로그 작업 영역 분석 `customerId`를 환경 변수로 저장합니다.

::: zone pivot="client-operating-system-windows-command"

```console
SET WORKSPACE_ID=<customerId>
```

::: zone-end

::: zone pivot="client-operating-system-powershell"

```PowerShell
$Env:WORKSPACE_ID='<customerId>'
```
::: zone-end

::: zone pivot="client-operating-system-macos-and-linux"

```console
export WORKSPACE_ID='<customerId>'
```
::: zone-end

이 명령은 Log Analytics 작업 영역에 연결하는 데 필요한 액세스 키를 반환합니다.

```azurecli
az monitor log-analytics workspace get-shared-keys --resource-group MyResourceGroup --workspace-name MyLogsWorkpace
```

예제 출력:

```output
{
  "primarySharedKey": "JXzQp1RcGgjXFCDS3v0sXoxPvbgCoGaIv35lf11Km2WbdGFvLXqaydpaj1ByWGvKoCghL8hL4BRoypXxkLr123==",
  "secondarySharedKey": "p2XHSxLJ4o9IAqm2zINcEmx0UWU5Z5EZz8PQC0OHpFjdpuVaI0zsPbTv5VyPFgaCUlCZb2yEbkiR4eTuTSF123=="
}
```

나중에 사용하기 위해 기본 키를 환경 변수에 저장합니다.

::: zone pivot="client-operating-system-windows-command"

```console
SET WORKSPACE_SHARED_KEY=<primarySharedKey>
```

::: zone-end

::: zone pivot="client-operating-system-powershell"

```console
$Env:WORKSPACE_SHARED_KEY='<primarySharedKey>'
```
::: zone-end


::: zone pivot="client-operating-system-macos-and-linux"

```console
export WORKSPACE_SHARED_KEY='<primarySharedKey>'
```

::: zone-end

## <a name="set-final-environment-variables-and-confirm"></a>최종 환경 변수 설정 및 확인

환경 변수에 SPN 기관 URL을 설정합니다.

::: zone pivot="client-operating-system-windows-command"

```console
SET SPN_AUTHORITY=https://login.microsoftonline.com
```

::: zone-end

::: zone pivot="client-operating-system-powershell"

```console
$Env:SPN_AUTHORITY='https://login.microsoftonline.com'
```

::: zone-end

::: zone pivot="client-operating-system-macos-and-linux"

```console
export SPN_AUTHORITY='https://login.microsoftonline.com'
```

::: zone-end


## <a name="verify-environment-variables"></a>환경 변수 확인

원하는 경우 다음과 같이 필요한 모든 환경 변수를 설정했는지 확인합니다.


::: zone pivot="client-operating-system-windows-command"

```console
echo %WORKSPACE_ID%
echo %WORKSPACE_SHARED_KEY%
echo %SPN_TENANT_ID%
echo %SPN_CLIENT_ID%
echo %SPN_CLIENT_SECRET%
echo %SPN_AUTHORITY%
```

::: zone-end

::: zone pivot="client-operating-system-powershell"

```PowerShell
$Env:WORKSPACE_ID
$Env:WORKSPACE_SHARED_KEY
$Env:SPN_TENANT_ID
$Env:SPN_CLIENT_ID
$Env:SPN_CLIENT_SECRET
$Env:SPN_AUTHORITY
```


::: zone-end

::: zone pivot="client-operating-system-macos-and-linux"

```console
echo $WORKSPACE_ID
echo $WORKSPACE_SHARED_KEY
echo $SPN_TENANT_ID
echo $SPN_CLIENT_ID
echo $SPN_CLIENT_SECRET
echo $SPN_AUTHORITY
```

::: zone-end

환경 변수를 설정하면 로그를 로그 작업 영역으로 업로드할 수 있습니다. 

## <a name="upload-logs-to-azure-monitor"></a>Azure Monitor에 로그 업로드

 Azure Arc 지원 SQL Managed Instance와 Azure Arc 지원 PostgreSQL 하이퍼스케일 서버 그룹에 대한 로그를 업로드하려면 다음 CLI 명령을 실행합니다.

1. `arcdata` 확장명이 있는 Azure(`az`) CLI를 사용하여 Azure Arc 데이터 컨트롤러에 로그인합니다.

   ```azurecli
   az arcdata login
   ```

   프롬프트에 따라 네임스페이스, 관리자 사용자 이름, 암호를 설정합니다. 

1. 모든 로그를 지정된 파일로 내보냅니다.

> [!NOTE]
> `az arcdata dc export` 명령을 사용하여 사용량/결제 정보, 메트릭 및 로그를 내보내려면 현재로서는 SSL 확인을 바이패스해야 합니다.  SSL 확인을 바이패스하라는 메시지가 표시되거나 메시지가 표시되지 않도록 `AZDATA_VERIFY_SSL=no` 환경 변수를 설정할 수 있습니다.  현재 데이터 컨트롤러 내보내기 API에 대한 SSL 인증서를 구성할 수 있는 방법이 없습니다.

   ```azurecli
   az arcdata dc export --type logs --path logs.json
   ```

2. Azure Monitor Log Analytics 작업 영역에 로그를 업로드합니다.

   ```azurecli
   az arcdata dc upload --path logs.json
   ```

## <a name="view-your-logs-in-azure-portal"></a>Azure Portal에서 로그 보기

로그가 업로드되면 다음과 같이 로그 쿼리 탐색기를 사용하여 로그를 쿼리할 수 있습니다.

1. Azure Portal을 열고 위쪽의 검색 표시줄에서 이름으로 작업 영역을 검색한 다음 선택합니다.
2. 왼쪽 패널에서 로그를 선택합니다.
3. 시작하기를 선택합니다. 처음 사용하는 경우 시작 페이지의 링크를 선택하여 Log Analytics에 대한 자세한 내용을 알아보세요.
4. Log Analytics를 처음 사용하는 경우 Log Analytics에 대해 자세히 알아보려면 자습서를 따릅니다.
5. 테이블 목록의 맨 아래에 있는 사용자 지정 로그를 확장하면 'sql_instance_logs_CL'이라는 테이블이 표시됩니다.
6. 테이블 이름 옆의 '눈' 아이콘을 선택합니다.
7. '쿼리 편집기에서 보기' 단추를 선택합니다.
8. 이제 쿼리 편집기에 쿼리를 포함하여 로그에 최근 10개의 이벤트를 표시합니다.
9. 여기에서 쿼리 편집기를 사용하여 로그 쿼리, 경고 설정 등을 시험해 볼 수 있습니다.

## <a name="automating-uploads-optional"></a>업로드 자동화(선택 사항)

일정에 따라 메트릭과 로그를 업로드하려는 경우 스크립트를 만들어 몇 분마다 타이머에서 실행되도록 할 수 있습니다. 다음은 Linux 셸 스크립트를 사용하여 업로드를 자동화하는 예제입니다.

자주 사용하는 텍스트/코드 편집기에서 다음 스크립트를 파일에 추가하고 as .sh(Linux/Mac) 또는 .cmd, .bat, .ps1과 같은 스크립트 실행 파일로 저장합니다.

```azurecli
az arcdata dc export --type logs --path logs.json --force
az arcdata dc upload --path logs.json
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

## <a name="next-steps"></a>다음 단계

[Azure Monitor에 메트릭 및 로그 업로드](upload-metrics.md)

[Azure Monitor에 사용량 데이터, 메트릭 및 로그 업로드](upload-usage-data.md)

[청구 데이터를 Azure에 업로드하고 Azure Portal에서 보기](view-billing-data-in-azure.md)

[Azure Portal에서 Azure Arc 데이터 컨트롤러 리소스 보기](view-data-controller-in-azure-portal.md)
