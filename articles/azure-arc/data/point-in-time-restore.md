---
title: 데이터베이스를 지정 시간으로 복원
description: 특정 시점 복원 작업을 수행하는 방법을 설명합니다.
author: dnethi
ms.author: dinethi
ms.reviewer: mikeray
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
ms.date: 07/30/2021
ms.topic: how-to
ms.openlocfilehash: fe4d250035c58bde2ba52e71046ea2f88854ff78
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122536518"
---
#  <a name="perform-a-point-in-time-restore"></a>특정 시점 복원 수행


Azure Arc 지원 SQL Managed Instance는 많은 PaaS와 같은 기능과 함께 기본 제공됩니다. 이러한 기능 중 하나는 미리 구성된 보존 설정 내에서 데이터베이스를 지정 시간으로 복원하는 기능입니다. 이 문서에서는 Azure Arc 지원 SQL Managed Instance에서 데이터베이스의 특정 시점 복원을 수행하는 방법을 설명합니다.

특정 시점 복원은 두 가지 속성(RPO(복구 지점 목표) 및 RT(보존 시간))을 포함하는 인스턴스 수준 설정입니다. 복구 지점 목표 설정은 트랜잭션 로그 백업이 수행되는 빈도를 결정합니다. 이는 데이터 손실이 예상되는 시간이기도 합니다. 보존 시간은 백업(전체, 차등 및 트랜잭션 로그)이 유지되는 기간입니다.  

현재, 특정 시점 복원은 다음과 같이 데이터베이스를 복원할 수 있습니다.

- SQL 인스턴스의 기존 데이터베이스에서
- 동일한 SQL 인스턴스의 새 데이터베이스로

### <a name="limitations"></a>제한 사항

Azure Arc 지원 SQL Managed Instance에 대한 특정 시점 복원에는 다음과 같은 제한 사항이 있습니다.

- 동일한 Azure Arc 지원 SQL Managed Instance로만 복원할 수 있습니다.
- 특정 시점 복원은 yaml 파일을 통해서만 수행할 수 있습니다. 
- 미리 구성된 보존 기간이 지난 오래된 백업 파일은 수동으로 정리해야 합니다.
- 데이터베이스 이름을 바꾸면 새 폴더에서 새 백업 체인이 시작됩니다.
- 같은 이름을 가진 다른 데이터베이스를 삭제하고 만드는 작업은 현재 제대로 처리되지 않습니다.

### <a name="edit-pitr-settings"></a>PITR 설정 편집

##### <a name="enabledisable-automated-backups"></a>자동화 백업 사용/사용 안 함

PITR(특정 시점 복원) 서비스는 기본적으로 다음 설정으로 사용하도록 설정됩니다.

- 1RPO(복구 지점 목표) = 300초 허용되는 값은 0 또는 300~600(초)입니다.

이는 Azure Arc 지원 SQL Managed Instance의 모든 데이터베이스에 대한 로그 백업이 기본적으로 300초 또는 5분마다 수행됨을 의미합니다. 이 값을 0으로 변경하여 백업이 수행되지 않도록 하거나 SQL 인스턴스의 데이터베이스에 필요한 RPO 요구 사항에 따라 더 높은 값(초)으로 변경할 수 있습니다. 

PITR 서비스 자체는 사용할 수 없지만 Azure Arc 지원 SQL Managed Instance의 특정 인스턴스에 대한 자동화된 백업을 사용하지 않도록 설정하거나 기본 설정을 변경할 수 있습니다.

다음과 같이 ```recoveryPointObjectiveInSeconds``` 속성의 값을 변경하여 RPO를 편집할 수 있습니다.

```
kubectl edit sqlmi <sqlinstancename>  -n <namespace> -o yaml
```

그러면 기본 편집기에서 Azure Arc 지원 SQL Managed Instance에 대한 사용자 지정 리소스 사양이 열립니다. ```spec```에서 ```backup``` 설정을 찾습니다.

```
backup:
  recoveryPointObjectiveInSeconds: 300
```

편집기에서 ```recoveryPointObjectiveInSeconds```의 값을 편집하고 새 설정이 적용되도록 변경 내용을 저장합니다. 

> [!NOTE]
> RPO 설정을 편집하면 Azure Arc 지원 SQL Managed Instance가 포함된 Pod가 다시 부팅됩니다. 

### <a name="restore-a-database-to-a-point-in-time"></a>특정 시점으로 데이터베이스 복원

Azure Arc 지원 SQL Managed Instance에서 복원 작업을 수행하여 원본 데이터베이스에서 보존 기간 내의 특정 시점으로 복원할 수 있습니다. 
**(1) 편집기에서 아래와 같이 yaml 파일을 만듭니다.**

```
apiVersion: tasks.sql.arcdata.microsoft.com/v1beta1
kind: SqlManagedInstanceRestoreTask
metadata:
  name: sql01-restore-20210707
  namespace: arc
spec:
  source:
    name: sql01
    database: db01
  restorePoint: "2021-07-01T02:00:00Z"
  destination:
    name: sql01
    database: db02
```

- name - kubernetes의 요구 사항인 각 사용자 지정 리소스에 대한 고유 문자열입니다.
- namespace - Azure Arc 지원 SQL Managed Instance가 실행되는 네임스페이스입니다.
- source > name - Azure Arc 지원 SQL Managed Instance의 이름입니다.
- source > database - Azure Arc 지원 SQL Managed Instance의 원본 데이터베이스 이름입니다.
- restorePoint - "UTC" 날짜 시간의 복원 작업을 위한 특정 시점입니다.
- destination > name - 복원할 대상 Azure Arc 지원 SQL Managed Instance의 이름입니다. 현재는 동일한 인스턴스로의 복원만 지원됩니다.
- destination > database - 복원이 적용될 새 데이터베이스의 이름입니다.

**(2) yaml 파일을 적용하여 복원 작업을 시작하는 작업을 만듭니다.**

다음과 같이 명령을 실행하여 복원 작업을 시작합니다.

```
kubectl apply -f sql01-restore-task.yaml
```

> [!NOTE]
> 사용자 지정 리소스 내의 작업 이름과 파일 이름이 같을 필요는 없습니다.


**복원의 상태 확인**

- 복원 작업 상태는 약 10초마다 업데이트되고 상태는 "대기 중" --> "복원 중" --> "완료됨"/"실패"로 변경됩니다. 
- 데이터베이스가 복원되는 동안 상태에는 "복원 중"이 반영됩니다.

다음과 같이 작업 상태를 검색할 수 있습니다.

```
kubectl get sqlmirestoretask -n arc
``` 

### <a name="monitor-your-backups"></a>백업 모니터링

백업은 ```/var/opt/mssql/backups/archived/<dbname>/<datetime>``` 폴더에 저장됩니다. 여기서 ```<dbname>```은 데이터베이스 이름이고 ```<datetime>```은 각 전체 백업 시작에 대한 UTC 형식의 타임스탬프입니다. 전체 백업이 시작될 때마다 해당 폴더 내에 전체 백업과 모든 후속 차등 및 트랜잭션 로그 백업이 포함된 새 폴더가 생성됩니다. 최신 전체 백업과 후속 차등 및 트랜잭션 로그 백업은 ```/var/opt/mssql/backups/current/<dbname><datetime>``` 폴더에 저장됩니다.


### <a name="clean-up"></a>정리 

이전 백업을 삭제하여 공간을 확보해야 하거나 공간이 더 이상 필요하지 않은 경우 ```/var/opt/mssql/backups/archived/``` 폴더 아래의 폴더를 제거할 수 있습니다. 타임라인 중간에 폴더를 제거하면 해당 기간 동안 특정 시점으로 복원하는 기능에 영향을 줄 수 있습니다. 지속적인 복원 가능성을 위해 가장 오래된 폴더를 먼저 삭제하는 것이 좋습니다. 


