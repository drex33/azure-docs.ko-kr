---
title: AdventureWorks 샘플 데이터베이스를 SQL Managed Instance로 복원
description: AdventureWorks 샘플 데이터베이스를 SQL Managed Instance로 복원
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
ms.date: 07/30/2021
ms.topic: how-to
ms.openlocfilehash: 5c3b8e3fc476c8ab55d4c217c8cff83c756f0533
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131450878"
---
# <a name="restore-the-adventureworks-sample-database-into-sql-managed-instance---azure-arc"></a>AdventureWorks 샘플 데이터베이스를 SQL Managed Instance로 복원 - Azure Arc

[AdventureWorks](/sql/samples/adventureworks-install-configure)는 자습서 및 예제에서 자주 사용되는 OLTP 데이터베이스를 포함하는 샘플 데이터베이스입니다. [SQL Server 샘플 GitHub 리포지토리](https://github.com/microsoft/sql-server-samples/tree/master/samples/databases)의 일부로 Microsoft에서 제공하고 유지합니다.

이 문서에서는 SQL Managed Instance - Azure Arc로 복원된 AdventureWorks 샘플 데이터베이스를 가져오는 간단한 프로세스에 대해 설명합니다.


## <a name="download-the-adventureworks-backup-file"></a>AdventureWorks 백업 파일 다운로드

AdventureWorks 백업(.bak) 파일을 SQL Managed Instance 컨테이너에 다운로드합니다. 이 예제에서는 `kubectl exec` 명령을 사용하여 SQL Managed Instance 컨테이너 내부에서 원격으로 명령을 실행하여 .bak 파일을 컨테이너에 다운로드합니다. SQL Managed Instance 컨테이너 내에 가져오려는 다른 데이터베이스 백업 파일이 있는 경우 `wget`에서 액세스할 수 있는 위치에 이 파일을 다운로드합니다. SQL Managed Instance 컨테이너 내부에 있으면 표준 `RESTORE DATABASE` T-SQL을 사용하여 쉽게 복원할 수 있습니다.

이와 같은 명령을 실행하여 실행 전에 pod 이름 및 네임스페이스 이름의 값을 대체하는 .bak 파일을 다운로드합니다.
> [!NOTE]
>  GitHub에서 파일을 다운로드하려면 컨테이너가 443을 통해 인터넷에 연결되어 있어야 합니다.

```console
kubectl exec <SQL pod name> -n <namespace name> -c arc-sqlmi -- wget https://github.com/Microsoft/sql-server-samples/releases/download/adventureworks/AdventureWorks2019.bak -O /var/opt/mssql/data/AdventureWorks2019.bak
```

예제

```console
kubectl exec sqltest1-0 -n arc -c arc-sqlmi -- wget https://github.com/Microsoft/sql-server-samples/releases/download/adventureworks/AdventureWorks2019.bak -O /var/opt/mssql/data/AdventureWorks2019.bak
```

## <a name="restore-the-adventureworks-database"></a>AdventureWorks 데이터베이스 복원

마찬가지로 SQL Managed Instance 컨테이너에 포함된 `sqlcmd` CLI 도구를 사용하는 `kubectl` exec 명령을 실행하여 데이터베이스를 복원하는 T-SQL 명령을 실행할 수 있습니다.

이와 같은 명령을 실행하여 데이터베이스를 복원합니다. 실행하기 전에 pod 이름, 암호 및 네임 스페이스 이름의 값을 바꿉니다.

```console
kubectl exec <SQL pod name> -n <namespace name> -c arc-sqlmi -- /opt/mssql-tools/bin/sqlcmd -S localhost -U sa -P <password> -Q "RESTORE DATABASE AdventureWorks2019 FROM  DISK = N'/var/opt/mssql/data/AdventureWorks2019.bak' WITH MOVE 'AdventureWorks2017' TO '/var/opt/mssql/data/AdventureWorks2019.mdf', MOVE 'AdventureWorks2017_Log' TO '/var/opt/mssql/data/AdventureWorks2019_Log.ldf'"
```
예제

```console
kubectl exec sqltest1-0 -n arc -c arc-sqlmi -- /opt/mssql-tools/bin/sqlcmd -S localhost -U sa -P MyPassword! -Q "RESTORE DATABASE AdventureWorks2019 FROM DISK = N'/var/opt/mssql/data/AdventureWorks2019.bak' WITH MOVE 'AdventureWorks2017' TO '/var/opt/mssql/data/AdventureWorks2019.mdf', MOVE 'AdventureWorks2017_Log' TO '/var/opt/mssql/data/AdventureWorks2019_Log.ldf'"
```
