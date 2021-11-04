---
title: Azure Container Apps 미리 보기에서 앱 모니터링
description: Azure Container Apps에서 애플리케이션을 모니터링하고 기록하는 방법을 알아봅니다.
services: app-service
author: craigshoemaker
ms.service: app-service
ms.topic: conceptual
ms.date: 11/02/2021
ms.author: cshoe
ms.custom: ignite-fall-2021
ms.openlocfilehash: 075e52647137841539e8db72a4c9fdae90bd8447
ms.sourcegitcommit: 96deccc7988fca3218378a92b3ab685a5123fb73
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/04/2021
ms.locfileid: "131577026"
---
# <a name="monitor-an-app-in-azure-container-apps-preview"></a>Azure Container Apps 미리 보기에서 앱 모니터링

Azure Container Apps는 컨테이너 앱에 대한 광범위한 데이터 집합을 수집하고 Log Analytics 를 사용하여 [저장합니다.](../azure-monitor/logs/log-analytics-tutorial.md) 이 문서에서는 사용 가능한 로그 및 로그를 작성하고 보는 방법을 설명합니다.

## <a name="writing-to-a-log"></a>로그에 쓰기

[표준 출력(stdout) 또는 표준 오류(stderr) 스트림에](https://wikipedia.org/wiki/Standard_streams)쓸 때 Container Apps 로깅 에이전트는 각 메시지에 대한 로그를 작성합니다.

메시지가 기록되면 다음 정보가 로그 테이블에 수집됩니다.

| 속성 | 설명 |
|---|---|
| `RevisionName` | |
| `ContainerAppName` | |
| `ContainerGroupID` | |
| `ContainerGroupName` | |
| `ContainerImage` | |
| `ContainerID` | 컨테이너의 고유 식별자입니다. 이 값을 사용하여 컨테이너 작동 중단을 식별할 수 있습니다. |
| `Stream` | `stdout`또는 가 `stderr` 로깅에 사용되는지 여부를 표시합니다. |
| `EnvironmentName` | |

### <a name="simple-text-vs-structured-data"></a>단순 텍스트와 구조적 데이터

직렬화된 JSON 데이터의 단일 텍스트 문자열 또는 줄을 기록할 수 있습니다. 정보는 기록하는 데이터의 유형에 따라 다르게 표시됩니다.

| 데이터 형식 | Description |
|---|---|
| 한 줄의 텍스트 | 열에 텍스트가 `Log_s` 나타납니다. |
| 직렬화된 JSON | 데이터는 로깅 에이전트에 의해 구문 분석되고 JSON 개체 속성 이름과 일치하는 열에 표시됩니다. |

## <a name="viewing-logs"></a>로그 보기

컨테이너 앱을 통해 기록된 데이터는 `ContainerAppConsoleLogs_CL` Log Analytics 작업 영역의 사용자 지정 테이블에 저장됩니다. Azure Portal 또는 CLI를 통해 로그를 볼 수 있습니다.

리소스 그룹 및 Log Analytics 작업 영역의 이름을 설정한 후 `LOG_ANALYTICS_WORKSPACE_CLIENT_ID` 다음 명령을 사용하여 를 검색합니다.

# <a name="bash"></a>[Bash](#tab/bash)

```bash
RESOURCE_GROUP="my-containerapps"
LOG_ANALYTICS_WORKSPACE="containerapps-logs"

LOG_ANALYTICS_WORKSPACE_CLIENT_ID=`az monitor log-analytics workspace show --query customerId -g $RESOURCE_GROUP -n $LOG_ANALYTICS_WORKSPACE --out tsv`
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```powershell
$RESOURCE_GROUP="my-containerapps"
$LOG_ANALYTICS_WORKSPACE="containerapps-logs"

$LOG_ANALYTICS_WORKSPACE_CLIENT_ID=az monitor log-analytics workspace show --query customerId -g $RESOURCE_GROUP -n $LOG_ANALYTICS_WORKSPACE --out tsv
```

---

다음 CLI 명령을 사용하여 명령줄에서 로그를 봅니다.

# <a name="bash"></a>[Bash](#tab/bash)

```azurecli
az monitor log-analytics query \
  --workspace $LOG_ANALYTICS_WORKSPACE_CLIENT_ID \
  --analytics-query "ContainerAppConsoleLogs_CL | where ContainerAppName_s == 'my-container-app' | project ContainerAppName_s, Log_s, TimeGenerated | take 3" \
  --out table
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```powershell
az monitor log-analytics query `
  --workspace $LOG_ANALYTICS_WORKSPACE_CLIENT_ID `
  --analytics-query "ContainerAppConsoleLogs_CL | where ContainerAppName_s == 'my-container-app' | project ContainerAppName_s, Log_s, TimeGenerated | take 3" `
  --out table
```

---

다음 출력에서는 CLI 명령에서 예상할 응답 형식을 보여 주는 출력입니다.

```console
ContainerAppName_s    Log_s                 TableName      TimeGenerated
--------------------  --------------------  -------------  ------------------------
my-container-app      listening on port 80  PrimaryResult  2021-10-23T02:09:00.168Z
my-container-app      listening on port 80  PrimaryResult  2021-10-23T02:11:36.197Z
my-container-app      listening on port 80  PrimaryResult  2021-10-23T02:11:43.171Z
```

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [컨테이너 앱 보안](secure-app.md)
