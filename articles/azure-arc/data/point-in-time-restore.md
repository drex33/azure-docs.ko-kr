---
title: Azure Arc 사용 SQL Managed Instance 데이터베이스를 이전 시점으로 복원
description: Azure Arc 사용 가능한 SQL Managed Instance 특정 시점으로 데이터베이스를 복원하는 방법을 설명합니다.
author: dnethi
ms.author: dinethi
ms.reviewer: mikeray
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
ms.date: 11/03/2021
ms.topic: how-to
ms.openlocfilehash: 51235a67d6f00410ee2b3db753e892cf05992baa
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/04/2021
ms.locfileid: "131563267"
---
#  <a name="perform-a-point-in-time-restore"></a>시점 복원 수행

PITR(시점 복원)을 사용하여 보존 기간 내에 있는 과거의 다른 데이터베이스 복사본으로 데이터베이스를 만듭니다. 이 문서에서는 Azure Arc 지원 SQL Managed Instance에서 데이터베이스의 특정 시점 복원을 수행하는 방법을 설명합니다.

특정 시점 복원은 다음과 같이 데이터베이스를 복원할 수 있습니다.

- 기존 데이터베이스에서
- 동일한 Azure Arc 지원 SQL 관리되는 인스턴스의 새 데이터베이스로

미리 구성된 보존 설정 내에서 데이터베이스를 지정 시간으로 복원할 수 있습니다.
다음과 같이 Azure Arc 사용 가능한 SQL 관리형 인스턴스에 대한 보존 설정을 확인할 수 있습니다.

**직접** 연결 모드의 경우:
```
az sql mi-arc show --name <SQL instance name> --resource-group <resource-group>
#Example
az sql mi-arc show --name sqlmi --resource-group myresourcegroup
```
**간접** 연결 모드의 경우:
```
az sql mi-arc show --name <SQL instance name> --k8s-namespace <SQL MI namespace> --use-k8s
#Example
az sql mi-arc show --name sqlmi --k8s-namespace arc --use-k8s
```

현재, 시점 복원은 데이터베이스를 복원할 수 있습니다.

- 인스턴스의 기존 데이터베이스에서
- 동일한 인스턴스의 새 데이터베이스로

## <a name="automatic-backups"></a>자동 백업

Azure Arc 지원 SQL 관리되는 인스턴스에는 기본 제공 자동 백업 기능이 사용하도록 설정되어 있습니다. 새 데이터베이스를 만들거나 복원할 때마다 Azure Arc 사용 가능한 SQL 관리되는 인스턴스는 즉시 전체 백업을 시작하고 차등 및 트랜잭션 로그 백업을 자동으로 예약합니다. SQL 관리되는 인스턴스는 배포 중에 지정된 스토리지 클래스에 이러한 백업을 저장합니다. 

지정 시간 복원을 사용하면 데이터베이스를 보존 기간 내의 특정 시점으로 복원할 수 있습니다. 특정 시점으로 데이터베이스를 복원하기 위해 Azure Arc 지원 데이터 서비스는 백업 파일을 특정 순서로 적용합니다. 예를 들어:

1. 전체 백업
2. 차등 백업 
3. 하나 이상의 트랜잭션 로그 백업

:::image type="content" source="media/point-in-time-restore/point-in-time-restore.png" alt-text="특정 시점 복원":::

현재 전체 백업은 일주일에 한 번 수행되며, 차등 백업은 12시간마다 수행되고 트랜잭션 로그 백업은 5분마다 수행됩니다.

## <a name="retention-period"></a>재방문 주기 기간

새 Azure Arc 지원 SQL 관리되는 인스턴스의 기본 보존 기간은 7일이며 0 또는 1-35일 값으로 조정할 수 있습니다. 보존 기간은 속성을 지정하여 SQL 관리되는 인스턴스를 배포하는 동안 설정할 수 `--retention-days` 있습니다. 구성된 보존 기간보다 오래된 백업 파일은 자동으로 삭제됩니다.


## <a name="create-a-database-from-a-point-in-time-using-az"></a>az를 사용하여 시점부터 데이터베이스 만들기

```azurecli
az sql midb-arc restore --managed-instance <SQL managed instance> --name <source DB name> --dest-name <Name for new db> --k8s-namespace <namespace of managed instance> --time "YYYY-MM-DDTHH:MM:SSZ" --use-k8s
#Example
az sql midb-arc restore --managed-instance sqlmi1 --name Testdb1 --dest-name mynewdb --k8s-namespace arc --time "2021-10-29T01:42:14.00Z" --use-k8s
```

옵션을 사용하여 `--dry-run` 데이터베이스를 실제로 복원하지 않고 복원 작업의 유효성을 검사할 수도 있습니다. 

```azurecli
az sql midb-arc restore --managed-instance <SQL managed instance> --name <source DB name> --dest-name <Name for new db> --k8s-namespace <namespace of managed instance> --time "YYYY-MM-DDTHH:MM:SSZ" --use-k8s --dry-run
#Example
az sql midb-arc restore --managed-instance sqlmi1 --name Testdb1 --dest-name mynewdb --k8s-namespace arc --time "2021-10-29T01:42:14.00Z" --use-k8s --dry-run
```


## <a name="create-a-database-from-a-point-in-time-using-azure-data-studio"></a>Azure Data Studio 사용하여 시점부터 데이터베이스 만들기

다음과 같이 데이터베이스를 Azure Data Studio 시점으로 복원할 수도 있습니다.
1. Azure Data Studio 시작
2. 도구 에 설명된 대로 필요한 Arc 확장이 있는지 [확인합니다.](install-client-tools.md)
3. Azure Arc 데이터 컨트롤러에 연결
4. 데이터 컨트롤러 노드를 확장하고 Azure Arc 지원 SQL 관리형 인스턴스를 마우스 오른쪽 단추로 클릭하고 "관리"를 선택합니다. Azure Data Studio SQL 관리되는 인스턴스 대시보드를 시작합니다.
5. 대시보드에서 **백업** 탭을 클릭합니다.
6. SQL 관리되는 인스턴스의 데이터베이스 목록과 해당 가장 빠른 및 최신 복원 시간 창과 **복원을** 시작하는 아이콘이 표시됩니다.
7. 복원하려는 데이터베이스의 아이콘을 클릭합니다. Azure Data Studio 오른쪽에 블레이드가 시작됩니다.
8. 블레이드에 필요한 입력을 제공하고 **복원을** 클릭합니다.

### <a name="monitor-progress"></a>진행률 모니터링

복원이 시작되면 kubernetes 클러스터에 전체, 차등 및 로그 백업의 실제 복원 작업을 실행하는 작업이 만들어집니다. 이 활동의 진행 상황을 다음과 같이 kubernetes 클러스터에서 모니터링할 수 있습니다.

```console
kubectl get sqlmirestoretask -n <namespace>
#Example
kubectl get sqlmirestoretask -n arc
```

작업에서 를 실행하여 작업에 대한 자세한 정보를 얻을 수 `kubectl describe` 있습니다. 예를 들면 다음과 같습니다.

```console
kubectl describe sqlmirestoretask <nameoftask> -n <namespace>
```

## <a name="configure-retention-period"></a>보존 기간 구성

Azure Arc 사용 가능한 SQL 관리형 인스턴스의 보존 기간은 다음과 같이 원래 설정에서 다시 구성할 수 있습니다.

> [!WARNING] 
> 현재 보존 기간을 줄이면 새 보존 기간보다 오래된 시점으로 복원할 수 없게 됩니다. 새 보존 기간 내에 PITR을 제공하는 데 더 이상 필요 없는 백업은 삭제됩니다. 현재 보존 기간을 늘려도 새 보존 기간의 더 오래된 시점으로 복원하는 기능이 즉시 제공되지는 않습니다. 시간이 지나 시스템이 백업을 장기간 보존하기 시작하면 이 기능을 사용할 수 있습니다.

### <a name="change-retention-period-for-direct-connected-sql-managed-instance"></a>**직접** 연결된 SQL 관리되는 인스턴스의 보존 기간 변경

```azurecli
az sql mi-arc edit  --name <SQLMI name> --custom-location dn-octbb-cl --resource-group dn-testdc --location eastus --retention-days 10
#Example
az sql mi-arc edit  --name sqlmi --custom-location dn-octbb-cl --resource-group dn-testdc --location eastus --retention-days 10
```

### <a name="change-retention-period-for-indirect-connected-sql-managed-instance"></a>**간접** 연결 SQL 관리되는 인스턴스에 대한 보존 기간 변경

```azurecli
az sql mi-arc edit  --name <SQLMI name> --k8s-namespace <namespace>  --use-k8s --retention-days <retentiondays>
#Example
az sql mi-arc edit  --name sqlmi --k8s-namespace arc  --use-k8s --retention-days 10
```

## <a name="disable-automatic-backups"></a>자동 백업 사용 안 함

다음과 같이 속성을 0으로 설정하여 Azure Arc 지원 SQL 관리형 인스턴스의 특정 인스턴스에 대해 자동화된 백업을 사용하지 않도록 설정할 수 `--retention-days` 있습니다.

> [!WARNING]
> Azure Arc 사용 가능한 SQL 관리되는 인스턴스에 대해 자동 백업을 사용하지 않도록 설정하면 구성된 모든 자동 백업이 삭제되고 지정 시간 복원을 수행할 수 없게 됩니다. 필요한 경우 `retention-days` 속성을 변경하여 자동 백업을 다시 시작할 수 있습니다.

### <a name="disable-automatic-backups-for-direct-connected-sql-managed-instance"></a>**직접** 연결된 SQL 관리되는 인스턴스에 대한 자동 백업 사용 안 함

```azurecli
az sql mi-arc edit  --name <SQLMI name> --custom-location dn-octbb-cl --resource-group dn-testdc --location eastus --retention-days 0
#Example
az sql mi-arc edit  --name sqlmi --custom-location dn-octbb-cl --resource-group dn-testdc --location eastus --retention-days 0
```

### <a name="disable-automatic-backups-for-indirect-connected-sql-managed-instance"></a>**간접** 연결 SQL 관리형 인스턴스에 대한 자동 백업 사용 안 함

```azurecli
az sql mi-arc edit  --name <SQLMI name> --k8s-namespace <namespace>  --use-k8s --retention-days 0
#Example
az sql mi-arc edit  --name sqlmi --k8s-namespace arc  --use-k8s --retention-days 0
```

## <a name="monitor-backups"></a>백업 모니터링

백업은 `/var/opt/mssql/backups/archived/<dbname>/<datetime>` 폴더에 저장됩니다. 여기서 `<dbname>`은 데이터베이스 이름이고 `<datetime>`은 각 전체 백업 시작에 대한 UTC 형식의 타임스탬프입니다. 전체 백업이 시작될 때마다 해당 폴더 내에 전체 백업과 모든 후속 차등 및 트랜잭션 로그 백업이 포함된 새 폴더가 생성됩니다. 최신 전체 백업과 후속 차등 및 트랜잭션 로그 백업은 `/var/opt/mssql/backups/current/<dbname><datetime>` 폴더에 저장됩니다.

## <a name="limitations"></a>제한 사항

Azure Arc 지원 SQL Managed Instance에 대한 특정 시점 복원에는 다음과 같은 제한 사항이 있습니다.

- 전체 Azure Arc 사용 SQL Managed Instance 시점 복원은 불가능합니다. 
- 고가용성(미리 보기)을 사용하여 배포된 Azure Arc 지원 SQL 관리되는 인스턴스는 현재 시점 복원을 지원하지 않습니다.
- 동일한 Azure Arc 지원 SQL 관리되는 인스턴스로만 복원할 수 있습니다.
- 이름이 같은 다른 데이터베이스를 삭제하고 만드는 것은 현재 제대로 처리되지 않습니다.
- 를 사용하여 복원 작업을 실행할 때 이후 날짜를 ```--dry-run``` 제공하면 오류가 발생합니다.




## <a name="next-steps"></a>다음 단계

[Azure Arc 지원 SQL Managed Instance의 특징 및 기능에 대해 자세히 알아보기](managed-instance-features.md)

[데이터 컨트롤러를 생성하여 시작](create-data-controller.md)

[데이터 컨트롤러가 이미 만들어져 있나요? Azure Arc 지원 SQL Managed Instance 만들기](create-sql-managed-instance.md)
