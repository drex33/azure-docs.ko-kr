---
title: Azure Arc 지원 SQL Managed Instance 데이터베이스를 이전 시점으로 복원
description: Azure Arc 사용 가능한 SQL Managed Instance 특정 시점으로 데이터베이스를 복원하는 방법을 설명합니다.
author: dnethi
ms.author: dinethi
ms.reviewer: mikeray
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
ms.date: 07/30/2021
ms.topic: how-to
ms.openlocfilehash: 0d091e9c90217ff9df6fba45d308296a80970053
ms.sourcegitcommit: 03e84c3112b03bf7a2bc14525ddbc4f5adc99b85
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/03/2021
ms.locfileid: "129401104"
---
#  <a name="perform-a-point-in-time-restore"></a>시점 복원 수행

PITR(Point-In-Time Restore)을 사용하여 과거의 다른 데이터베이스 복사본으로 데이터베이스를 만듭니다. 이 문서에서는 Azure Arc 사용하도록 설정된 SQL 관리되는 인스턴스에서 데이터베이스의 시점 복원을 수행하는 방법을 설명합니다.

특정 시점 복원은 다음과 같이 데이터베이스를 복원할 수 있습니다.

- 기존 데이터베이스에서
- 관리되는 인스턴스와 동일한 Azure Arc 사용하도록 설정된 SQL 새 데이터베이스로

미리 구성된 보존 설정 내에서 데이터베이스를 지정 시간으로 복원할 수 있습니다.

시점 복원은 RPO(복구 지점 목표) 및 보존 기간이라는 두 가지 속성을 가진 인스턴스 수준 설정입니다. RPO는 트랜잭션 로그 백업이 수행되는 빈도를 결정합니다. RPO는 데이터 손실이 예상되는 시간입니다. RPO는 분 안에 지정됩니다. 보존 기간은 Azure Arc 지원 데이터 서비스에서 데이터베이스 백업을 저장할 기간(일)을 결정합니다. 보존 기간은 전체, 차등 및 트랜잭션 로그 백업 파일에 적용됩니다.  

현재, 시점 복원은 데이터베이스를 복원할 수 있습니다.

- 인스턴스의 기존 데이터베이스에서
- 동일한 인스턴스의 새 데이터베이스로

## <a name="limitations"></a>제한 사항

Azure Arc 사용하도록 설정된 SQL Managed Instance 대한 시점 복원에는 다음과 같은 제한이 있습니다.

- 전체 Azure Arc 활성화된 SQL Managed Instance 시점 복원은 불가능합니다. 
- 고가용성(미리 보기)을 사용하여 배포된 Azure Arc 지원 SQL 관리되는 인스턴스는 현재 시점 복원을 지원하지 않습니다.
- 동일한 Azure Arc 지원 SQL 관리되는 인스턴스로만 복원할 수 있습니다.
- 시점 복원은 yaml 파일 를 통해서만 수행할 수 있습니다.
- 미리 구성된 보존 기간을 초과하는 이전 백업 파일은 수동으로 정리해야 합니다.
- 데이터베이스 이름을 변경하여 새 폴더에서 새 백업 체인을 시작합니다.
- 이름이 같은 다른 데이터베이스를 삭제하고 만드는 것은 현재 제대로 처리되지 않습니다.

## <a name="description"></a>Description

지정 시간 복원은 데이터베이스를 특정 시점으로 복원합니다. 데이터베이스를 특정 시점으로 복원하기 위해 Azure Arc 지원 데이터 서비스는 백업 파일을 특정 순서로 적용합니다. 예:

1. 전체 백업
2. 차등 백업 
3. 하나 이상의 트랜잭션 로그 백업

:::image type="content" source="media/point-in-time-restore/point-in-time-restore.png" alt-text="특정 시점 복원":::

Azure Arc 사용하도록 설정된 SQL Managed Instance 지점 복원을 수행하는 기본 제공 기능이 제공됩니다. 새 데이터베이스를 만들거나 Azure Arc 사용하도록 설정된 SQL Managed Instance 복원할 때마다 백업이 자동으로 수행됩니다. 

시점 복원 기능에 영향을 주는 두 가지 매개 변수가 있습니다.

- 복구 지점 목표 
- 보존 기간

## <a name="create-a-database-from-a-point-in-time"></a>시점에서 데이터베이스 만들기

다음은 를 사용하여 동일한 Azure Ar 지원 SQL Managed Instance 데이터베이스를 복원하는 단계입니다. `kubectl`

1. 복원 작업에 대한 작업을 만듭니다. 복원 매개 변수를 사용하여 .yaml 파일을 만듭니다.

   예:

   ```json
   apiVersion: tasks.sql.arcdata.microsoft.com/v1beta1
      kind: SqlManagedInstanceRestoreTask
   metadata:
     name: sql01-restore-20210909
   namespace: arc
   spec:
     source:
       name: sql01
       database: db01
     restorePoint: "2021-09-09T02:00:00Z"
     destination:
       name: sql01
       database: db02
   ```

   위의 yaml 파일을 편집합니다.

   - `metadata` > `name` - CR(작업 사용자 지정 리소스)의 이름
   - `metadata` > `namespace`- Azure Arc 사용하도록 설정된 SQL Managed Instance 네임스페이스
   - `source` > `name`- 사용하도록 설정된 Azure Arc 이름 SQL Managed Instance
   - `source` > `database`- 복원할 Azure Arc SQL Managed Instance **원본** 데이터베이스의 이름
   - `restorePoint` - 복원할 시점("UTC" 날짜/시간)입니다.
   - `destination` > `name`- 사용하도록 설정된 Azure Arc 이름 SQL Managed Instance
   - `destination` > `database`- Azure Arc 사용하도록 설정된 SQL Managed Instance **대상** 데이터베이스의 이름


   > [!NOTE] 
   > 관리되는 인스턴스를 SQL 원본 및 대상 Azure Arc 이름은 동일해야 합니다.

2. 시점 복원 작업을 시작하는 작업 만들기

   ```console
   kubectl apply -f sql-restore-task.yaml
   ```

3. 복원 상태 확인

   다음 명령을 실행하여 복원 작업의 상태를 확인합니다.

   ```console
   kubectl get sqlmirestoretask -n <namespace>
   ```

   `<namespace>`를 SQL 인스턴스를 호스팅하는 네임스페이스로 대체합니다.

복원 작업의 상태가 **완료로** 표시되면 새 데이터베이스를 사용할 수 있어야 합니다. 

## <a name="troubleshoot-failed-restore-operations"></a>실패한 복원 작업 문제 해결

복원 작업 상태에 **실패가** 표시되면 다음 명령을 실행하여 이벤트의 근본 원인을 찾습니다.

```console
kubectl describe sqlmirestoretask <taskname> -n <namespace>
```

예:
```console
kubectl describe sqlmirestoretask sql01-restore-20210909 -n arc
```

## <a name="enabledisable-automated-backups"></a>자동화 백업 사용/사용 안 함

PITR(지정 시간 복원) 서비스는 기본적으로 다음 설정을 사용하여 사용하도록 설정됩니다.

- 1RPO(복구 지점 목표) = 300초 허용되는 값은 0 또는 300~600(초)입니다.

RPO는 기본적으로 300초 또는 5분마다 Azure Arc 지원 SQL 관리형 인스턴스의 모든 데이터베이스에 대해 로그 백업을 사용하도록 서비스를 설정합니다. 이 값을 0으로 변경하여 백업이 수행되지 않도록 하거나 SQL 인스턴스의 데이터베이스에 필요한 RPO 요구 사항에 따라 더 높은 값(초)으로 변경할 수 있습니다. 

Azure Arc 지원 SQL 관리되는 인스턴스의 특정 인스턴스에 대해 자동화된 백업을 사용하지 않도록 설정하거나 기본 설정을 변경할 수 있습니다. PITR 서비스 자체는 사용하지 않도록 설정될 수 없습니다.

다음과 같이 속성의 값을 변경하여 RPO를 편집할 수 `recoveryPointObjectiveInSeconds` 있습니다.

```console
kubectl edit sqlmi <sqlinstancename>  -n <namespace> -o yaml
```

이 명령은 기본 편집기에서 Azure Arc 사용 SQL 관리형 인스턴스에 대한 사용자 지정 리소스 사양을 엽니다. `backup`에서 설정을 찾습니다. `spec`

```json
backup:
  recoveryPointObjectiveInSeconds: 300
```

편집기에서 `recoveryPointObjectiveInSeconds`의 값을 편집하고 새 설정이 적용되도록 변경 내용을 저장합니다. 

> [!NOTE]
> RPO 설정을 편집하면 Azure Arc 지원 SQL Managed Instance가 포함된 Pod가 다시 부팅됩니다. 

## <a name="monitor-backups"></a>백업 모니터링

백업은 `/var/opt/mssql/backups/archived/<dbname>/<datetime>` 폴더에 저장됩니다. 여기서 `<dbname>`은 데이터베이스 이름이고 `<datetime>`은 각 전체 백업 시작에 대한 UTC 형식의 타임스탬프입니다. 전체 백업이 시작될 때마다 해당 폴더 내에 전체 백업과 모든 후속 차등 및 트랜잭션 로그 백업이 포함된 새 폴더가 생성됩니다. 최신 전체 백업과 후속 차등 및 트랜잭션 로그 백업은 `/var/opt/mssql/backups/current/<dbname><datetime>` 폴더에 저장됩니다.

### <a name="clean-up"></a>정리 

이전 백업을 삭제하여 공간을 확보해야 하거나 공간이 더 이상 필요하지 않은 경우 `/var/opt/mssql/backups/archived/` 폴더 아래의 폴더를 제거할 수 있습니다. 타임라인 중간에 폴더를 제거하면 해당 기간 동안 특정 시점으로 복원하는 기능에 영향을 줄 수 있습니다. 복원 가능성의 연속 타임라인을 허용하려면 가장 오래된 폴더를 먼저 삭제합니다. 

## <a name="next-steps"></a>다음 단계

[Azure Arc 지원 SQL Managed Instance의 특징 및 기능에 대한 자세한 정보](managed-instance-features.md)

[데이터 컨트롤러를 생성하여 시작](create-data-controller.md)

[데이터 컨트롤러가 이미 만들어져 있나요? Azure Arc 지원 SQL Managed Instance 만들기](create-sql-managed-instance.md)
