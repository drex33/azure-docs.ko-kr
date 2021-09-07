---
title: Azure Database for PostgreSQL 하이퍼스케일 서버 그룹에 대한 백업 및 복원
description: Azure Database for PostgreSQL 하이퍼스케일 서버 그룹에 대한 백업 및 복원
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: TheJY
ms.author: jeanyd
ms.reviewer: mikeray
ms.date: 07/30/2021
ms.topic: how-to
ms.openlocfilehash: de908a8f29d85aea1954415ba5c9793349837c5f
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122528595"
---
# <a name="back-up-and-restore-azure-arc-enabled-postgresql-hyperscale-server-groups"></a>Azure Arc 지원 PostgreSQL 하이퍼스케일 서버 그룹 백업 및 복원

> [!IMPORTANT]
> Azure Arc 지원 PostgreSQL 하이퍼스케일 서버의 백업 및 복원은 현재 미리 보기 릴리스에서 지원되지 않습니다.

[!INCLUDE [azure-arc-common-prerequisites](../../../includes/azure-arc-common-prerequisites.md)]

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

Azure Arc 지원 PostgreSQL 하이퍼스케일 서버 그룹을 백업하거나 복원하면 서버 그룹의 모든 PostgreSQL 노드에 있는 전체 데이터베이스 세트가 백업 및/또는 복원됩니다.

## <a name="take-a-manual-full-backup"></a>수동 전체 백업 수행

서버 그룹에 있는 전체 데이터 및 로그 폴더의 전체 백업을 수행하려면 다음 명령을 실행합니다.
```console
azdata arc postgres backup create [--name <backup name>] --server-name <server group name> [--no-wait] 
```
위치:
- __name__ 은 백업 이름을 나타냅니다.
- __server-name__ 은 서버 그룹을 나타냅니다.
- __no-wait__ 는 이 명령줄 창을 계속 사용할 수 있도록 백업이 완료될 때까지 명령줄이 대기하지 않음을 나타냅니다.

이 명령은 Azure Arc 지원 PostgreSQL 하이퍼스케일 서버 그룹을 구성하는 모든 노드에서 분산된 전체 백업을 조정합니다. 즉, 코디네이터 및 작업자 노드의 모든 데이터를 백업합니다.

예를 들어 다음과 같은 가치를 제공해야 합니다.

```console
azdata arc postgres backup create --name backup12082020-0250pm --server-name postgres01
```

백업이 완료되면 백업의 ID, 이름, 크기, 상태, 타임스탬프가 반환됩니다. 예를 들어 다음과 같은 가치를 제공해야 합니다.
```console
{
  "ID": "8085723fcbae4aafb24798c1458f4bb7",
  "name": "backup12082020-0250pm",
  "size": "9.04 MiB",
  "state": "Done",
  "timestamp": "2020-12-08 22:50:22+00:00"
}
```
`+xx:yy`는 백업이 수행된 시간의 표준 시간대를 나타냅니다. 이 예제에서 “+ 00:00”은 UTC 시간(UTC + 00시간 00분)을 의미합니다.

> [!NOTE]
> 아직 다음 작업을 수행할 수 없습니다.
> - 자동 백업 예약
> - 수행 중인 백업 진행률 표시

## <a name="list-backups"></a>백업 나열

복원에 사용할 수 있는 백업을 나열하려면 다음 명령을 실행합니다.

```console
azdata arc postgres backup list --server-name <servergroup name>
```

예를 들어 다음과 같은 가치를 제공해야 합니다.

```console
azdata arc postgres backup list --server-name postgres01
```

다음과 같은 출력을 반환합니다.

```output
ID                                Name                   Size       State    Timestamp
--------------------------------  ---------------------  ---------  -------  -------------------------
d744303b1b224ef48be9cba4f58c7cb9  backup12072020-0731pm  13.83 MiB  Done     2020-12-08 03:32:09+00:00
c4f964d28da34318a420e6d14374bd36  backup12072020-0819pm  9.04 MiB   Done     2020-12-08 04:19:49+00:00
a304c6ef99694645a2a90ce339e94714  backup12072020-0822pm  9.1 MiB    Done     2020-12-08 04:22:26+00:00
47d1f57ec9014328abb0d8fe56020760  backup12072020-0827pm  9.06 MiB   Done     2020-12-08 04:27:22+00:00
8085723fcbae4aafb24798c1458f4bb7  backup12082020-0250pm  9.04 MiB   Done     2020-12-08 22:50:22+00:00
```

Timestamp 열은 백업이 수행된 특정 시점 UTC를 나타냅니다.

## <a name="restore-a-backup"></a>백업 복원
이 섹션에서는 전체 복원 또는 특정 시점 복원을 수행하는 방법을 보여 줍니다. 전체 백업을 복원하는 경우 백업의 전체 콘텐츠를 복원합니다. 특정 시점 복원을 수행하는 경우 지정한 특정 시점까지 복원합니다. 이 시점 이후에 수행된 모든 트랜잭션은 복원되지 않습니다.

> [!CAUTION]
> 백업을 수행했을 때 있던 동일한 수의 작업자 노드가 있는 서버 그룹으로만 복원할 수 있습니다. 백업이 수행된 이후 작업자 노드 수를 늘리거나 줄인 경우, 복원하기 전에 작업자 노드 수를 늘리거나 줄여야 합니다. 또는 백업 내용과 일치하도록 새 서버 그룹을 만들어야 합니다. 작업자 노드 수가 일치하지 않으면 복원이 실패합니다.

### <a name="restore-a-full-backup"></a>전체 백업 복원
백업의 전체 콘텐츠를 복원하려면 다음 명령을 실행합니다.
```console
azdata arc postgres backup restore --server-name <target server group name> [--source-server-name <source server group name> --backup-id <backup id>]
or
azdata arc postgres backup restore -sn <target server group name> [-ssn <source server group name> --backup-id <backup id>]
```
<!--To read the general format of restore command, run: azdata arc postgres backup restore --help -->

위치:
- __backup-id__ 는 위에 표시된 list backup 명령에 표시된 백업의 ID입니다.
이 항목은 Azure Arc 지원 PostgreSQL 하이퍼스케일 서버 그룹을 구성하는 모든 노드에서 분산된 전체 복원을 조정합니다. 즉, 코디네이터 및 작업자 노드의 모든 데이터를 복원합니다.

#### <a name="examples"></a>예:

__서버 그룹 postgres01을 자체 서버 그룹으로 복원:__

```console
azdata arc postgres backup restore -sn postgres01 --backup-id d134f51aa87f4044b5fb07cf95cf797f
```

이 작업은 PostgreSQL 버전 12 이상에서만 지원됩니다.

__서버 그룹 postgres01을 다른 서버 그룹 postgres02로 복원:__

```console
azdata arc postgres backup restore -sn postgres02 -ssn postgres01 --backup-id d134f51aa87f4044b5fb07cf95cf797f
```
이 작업은 버전 11부터 PostgreSQL의 모든 버전에서 지원됩니다. 대상 서버 그룹은 복원 작업 전에 만들어야 하고, 구성이 동일해야 하며, 원본 서버 그룹과 동일한 백업 PVC를 사용해야 합니다.

복원 작업이 완료되면 다음과 같은 출력이 명령줄에 반환됩니다.

```json
{
  "ID": "d134f51aa87f4044b5fb07cf95cf797f",
  "state": "Done"
}
```

> [!NOTE]
> 아직 다음 작업을 수행할 수 없습니다.
> - 이름을 지정하여 백업 복원
> - 복원 작업의 진행률 표시


### <a name="do-a-point-in-time-restore"></a>특정 시점 복원 수행

서버 그룹을 특정 시점까지 복원하려면 다음 명령을 실행합니다.
```console
azdata arc postgres backup restore --server-name <target server group name> --source-server-name <source server group name> --time <point in time to restore to>
or
azdata arc postgres backup restore -sn <target server group name> -ssn <source server group name> -t <point in time to restore to>
```

restore 명령의 일반 형식을 읽으려면 `azdata arc postgres backup restore --help`를 실행합니다.

여기서 `time`은 복원할 특정 시점입니다. 타임스탬프 또는 숫자와 접미사(분의 경우 `m`, 시간의 경우 `h`, 일의 경우 `d` 또는 주의 경우 `w`)를 제공합니다. 예를 들어, `1.5h`는 90분 전으로 돌아갑니다.

#### <a name="examples"></a>예:
__서버 그룹 postgres01을 자체 서버 그룹으로 특정 시점 복원 수행:__

아직 서버 그룹을 자체 서버 그룹으로 특정 시점 복원을 수행할 수는 없습니다.

__특정 타임스탬프에 대해 서버 그룹 postgres01을 다른 서버 그룹 postgres02로 특정 시점 복원 수행:__
```console
azdata arc postgres backup restore -sn postgres02 -ssn postgres01 -t "2020-12-08 04:23:48.751326+00"
``` 

이 예제에서는 서버 그룹 postgres01이 2020년 12월 8일 04:23:48.75 UTC에 있었던 상태를 서버 그룹 postgres02로 복원합니다. “+00”은 지정한 특정 시점의 표준 시간대를 나타냅니다. 표준 시간대를 지정하지 않으면 복원 작업을 실행하는 클라이언트의 표준 시간대가 사용됩니다.

예를 들어 다음과 같은 가치를 제공해야 합니다.
- `2020-12-08 04:23:48.751326+00`은 `2020-12-08 04:23:48.751326` UTC로 해석됩니다.
- 태평양 표준 시간대(PST = UTC+08)에 있는 경우 `2020-12-08 04:23:48.751326`은 `2020-12-08 12:23:48.751326` UTC로 해석됩니다. 이 작업은 버전 11부터 PostgreSQL의 모든 버전에서 지원됩니다. 대상 서버 그룹은 복원 작업 전에 만들어야 하고 원본 서버 그룹과 동일한 백업 PVC를 사용해야 합니다.


__과거 특정 시간 길이에 대해 서버 그룹 postgres01을 다른 서버 그룹 postgres02로 특정 시점 복원 수행:__
```console
azdata arc postgres backup restore -sn postgres02 -ssn postgres01 -t "22m"
```

이 예제에서는 서버 그룹 postgres01이 22분 전에 있었던 상태를 서버 그룹 postgres02로 복원합니다.
이 작업은 버전 11부터 PostgreSQL의 모든 버전에서 지원됩니다. 대상 서버 그룹은 복원 작업 전에 만들어야 하고 원본 서버 그룹과 동일한 백업 PVC를 사용해야 합니다.

> [!NOTE]
> 아직 다음 작업을 수행할 수 없습니다.
> - 복원 작업의 진행률 표시

## <a name="delete-backups"></a>백업 삭제

미리 보기에서는 백업 보존을 설정할 수 없습니다. 그러나 필요하지 않은 백업은 수동으로 삭제할 수 있습니다.
백업을 삭제하는 일반적인 명령은 다음과 같습니다.

```console
azdata arc postgres backup delete  [--server-name, -sn] {[--name, -n], -id}
```

여기서
- `--server-name`은 사용자가 백업을 삭제하려는 서버 그룹의 이름입니다.
- `--name`은 삭제할 백업의 이름입니다.
- `-id`는 삭제할 백업의 ID입니다.

> [!NOTE]
> `--name` 및 `-id`는 함께 사용할 수 없습니다.

예를 들어 다음과 같은 가치를 제공해야 합니다.

```console
azdata arc postgres backup delete -sn postgres01 -n MyBackup091720200110am
{
  "ID": "5b0481dfc1c94b4cac79dd56a1bb21f4",
  "name": "MyBackup091720200110am",
  "state": "Done"
}
```

이전 단락에서 설명한 대로 list backup 명령을 실행하여 백업의 이름 및 ID를 검색할 수 있습니다.

delete 명령에 대한 자세한 내용을 보려면 다음을 실행합니다.

```console
azdata arc postgres backup delete --help
```

## <a name="next-steps"></a>다음 단계
- 서버 그룹 [스케일링 아웃(작업자 노드 추가)](scale-out-in-postgresql-hyperscale-server-group.md)에 관해 읽어보기
- 서버 그룹 [스케일링 아웃 또는 스케일링 다운(메모리/vCore 늘리기/줄이기)](scale-up-down-postgresql-hyperscale-server-group-using-cli.md)에 관해 읽어보기
