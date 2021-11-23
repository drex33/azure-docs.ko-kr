---
title: SQL Server에서 Azure Arc 지원 SQL Managed Instance로 데이터베이스 마이그레이션
description: SQL Server에서 Azure Arc 지원 SQL Managed Instance로 데이터베이스 마이그레이션
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: dnethi
ms.author: dinethi
ms.reviewer: mikeray
ms.date: 07/30/2021
ms.topic: how-to
ms.openlocfilehash: cb99653c9e105f44090a27de352c7a8cbc5fa344
ms.sourcegitcommit: 01b678462a4a390c30463c525432ffbbbe0195cf
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/23/2021
ms.locfileid: "132953732"
---
# <a name="migrate-sql-server-to-azure-arc-enabled-sql-managed-instance"></a>마이그레이션: SQL Server에서 Azure Arc 지원 SQL Managed Instance로 마이그레이션

이 시나리오에서는 두 가지 백업 및 복원 방법을 통해 SQL Server 인스턴스에서 Azure Arc의 Azure SQL 관리형 인스턴스로 데이터베이스를 마이그레이션하는 단계를 안내합니다.


## <a name="use-azure-blob-storage"></a>Azure Blob Storage 사용

Azure Arc 지원 SQL Managed Instance로 마이그레이션하려면 Azure Blob Storage를 사용합니다.

이 방법은 백업한 후 복원할 수 있는 임시 스토리지 위치로 Azure Blob Storage를 사용합니다.

### <a name="prerequisites"></a>사전 요구 사항

- [Azure Data Studio 설치](install-client-tools.md)

   [!INCLUDE [use-insider-azure-data-studio](includes/use-insider-azure-data-studio.md)]

- [Azure Storage Explorer 설치](https://azure.microsoft.com/features/storage-explorer/)
- Azure 구독



### <a name="step-1-provision-azure-blob-storage"></a>1단계: Azure Blob Storage 프로비저닝

1. [Azure Blob Storage 계정 만들기](../../storage/common/storage-account-create.md?tabs=azure-portal)에 설명된 단계를 수행합니다.
1. Azure Storage Explorer 시작
1. [Azure에 로그인](../../vs-azure-tools-storage-manage-with-storage-explorer.md?tabs=windows#sign-in-to-azure)하여 이전 단계에서 만든 Blob Storage에 액세스합니다.
1. Blob Storage 계정을 마우스 오른쪽 단추로 클릭하고 **Blob 컨테이너 만들기** 를 선택하여 백업 파일이 저장될 새 컨테이너를 만듭니다.

### <a name="step-2-get-storage-blob-credentials"></a>2단계: Blob Storage 자격 증명 가져오기

1. Azure Storage Explorer에서 방금 만든 Blob 컨테이너를 마우스 오른쪽 단추로 클릭하고 **공유 액세스 서명 가져오기** 를 선택합니다.

1. **읽기**, **쓰기**, **나열** 을 선택합니다.

1. **만들기** 를 선택합니다.

   이 화면의 URI 및 쿼리 문자열을 적어 둡니다. 해당 정보는 이후 단계에서 필요합니다. **복사** 단추를 클릭하여 메모장/OneNote 등에 저장합니다.

1. **공유 액세스 서명** 창을 닫습니다.

### <a name="step-3-backup-database-file-to-azure-blob-storage"></a>3단계: Azure Blob Storage에 데이터베이스 파일 백업

이 단계에서는 원본 SQL Server에 연결하고 SQL Managed Instance - Azure Arc로 마이그레이션할 데이터베이스의 백업 파일을 만듭니다.

1. Azure Data Studio 시작
1. SQL Managed Instance - Azure Arc로 마이그레이션할 데이터베이스가 있는 SQL Server 인스턴스에 연결합니다.
1. 데이터베이스를 마우스 오른쪽 단추로 클릭하고 **새 쿼리** 를 선택합니다.
1. `<...>`에 표시된 자리 표시자를 이전 단계의 공유 액세스 서명 정보로 바꾸어 다음 형식으로 쿼리를 준비합니다.  값을 대체한 후 쿼리를 실행합니다.

   ```sql
   IF NOT EXISTS  
   (SELECT * FROM sys.credentials
   WHERE name = 'https://<mystorageaccountname>.blob.core.windows.net/<mystorageaccountcontainername>')  
   CREATE CREDENTIAL [https://<mystorageaccountname>.blob.core.windows.net/<mystorageaccountcontainername>]
     WITH IDENTITY = 'SHARED ACCESS SIGNATURE',  
      SECRET = '<SAS_TOKEN>';  
   ```

1. 마찬가지로, **BACKUP DATABASE** 명령을 다음과 같이 준비하여 Blob 컨테이너에 대한 백업 파일을 만듭니다.  값을 대체한 후 쿼리를 실행합니다.

   ```sql
   BACKUP DATABASE <database name> TO URL = 'https://<mystorageaccountname>.blob.core.windows.net/<mystorageaccountcontainername>'
   ```

1. Azure Storage Explorer를 열고 이전 단계에서 만든 백업 파일이 Blob 컨테이너에 표시되는지 확인합니다.

### <a name="step-4-restore-the-database-from-azure-blob-storage-to-sql-managed-instance---azure-arc"></a>4단계: Azure Blob Storage에서 SQL Managed Instance - Azure Arc로 데이터베이스 복원

1. Azure Data Studio에서 로그인하고 SQL Managed Instance - Azure Arc에 연결합니다.
1. **시스템 데이터베이스** 를 펼치고 **master** 데이터베이스를 마우스 오른쪽 단추로 클릭한 다음, **새 쿼리** 를 선택합니다.
1. 쿼리 편집기 창에서 이전 단계와 동일한 쿼리를 준비하고 실행하여 자격 증명을 만듭니다.

   ```sql
   IF NOT EXISTS  
   (SELECT * FROM sys.credentials
   WHERE name = 'https://<mystorageaccountname>.blob.core.windows.net/<mystorageaccountcontainername>')  
   CREATE CREDENTIAL [https://<mystorageaccountname>.blob.core.windows.net/<mystorageaccountcontainername>]
     WITH IDENTITY = 'SHARED ACCESS SIGNATURE',  
     SECRET = '<SAS_TOKEN>';  
   ```

1. 아래 명령을 준비하고 실행하여 백업 파일을 읽을 수 있고 그대로 유지되는지 확인합니다.

   ```console
   RESTORE FILELISTONLY FROM URL = 'https://<mystorageaccountname>.blob.core.windows.net/<mystorageaccountcontainername>/<file name>.bak'
   ```

1. **RESTORE DATABASE** 명령을 다음과 같이 준비하고 실행하여 SQL Managed Instance - Azure Arc의 데이터베이스로 백업 파일을 복원합니다.

   ```sql
   RESTORE DATABASE <database name> FROM URL = 'https://<mystorageaccountname>.blob.core.windows.net/<mystorageaccountcontainername>/<file name>'
   WITH MOVE 'Test' to '/var/opt/mssql/data/<file name>.mdf'
   ,MOVE 'Test_log' to '/var/opt/mssql/data/<file name>.ldf'
   ,RECOVERY  
   ,REPLACE  
   ,STATS = 5;  
   GO
   ```

URL에 백업에 대한 자세한 내용은 아래를 참조하세요.

[URL에 백업 문서](/sql/relational-databases/backup-restore/sql-server-backup-to-url)

[SSMS(SQL Server Management Studio)를 사용하여 URL에 백업](/sql/relational-databases/tutorial-sql-server-backup-and-restore-to-azure-blob-storage-service)

-------

## <a name="method-2-copy-the-backup-file-into-an-azure-sql-managed-instance---azure-arc-pod-using-kubectl"></a>방법 2: kubectl을 사용하여 Azure SQL Managed Instance - Azure Arc Pod에 백업 파일 복사

이 방법은 모든 방법으로 만든 백업 파일을 사용하고 Azure SQL 관리형 인스턴스 Pod의 로컬 스토리지에 복사하여 Windows 또는 Linux의 일반적인 파일 시스템에 있는 것처럼 복원할 수 있도록 하는 방법을 보여 줍니다. 이 시나리오에서는 `kubectl cp` 명령을 사용하여 한 위치의 파일을 Pod의 파일 시스템에 복사합니다.

### <a name="prerequisites"></a>사전 요구 사항

- kubectl을 설치하고 Azure Arc 데이터 서비스가 배포된 Kubernetes 클러스터를 가리키도록 구성합니다.
- Azure Data Studio 또는 SQL Server Management Server와 같은 도구를 설치하고 백업 파일을 만들려고 하거나 로컬 파일 시스템에 이미 생성된 기존 .bak 파일이 있는 SQL Server에 연결합니다.

### <a name="step-1-backup-the-database-if-you-havent-already"></a>1단계: 아직 백업하지 않은 경우 데이터베이스 백업

디스크에 대한 일반적인 SQL Server 백업처럼 SQL Server 데이터베이스를 로컬 파일 경로에 백업합니다.

```sql
BACKUP DATABASE Test
TO DISK = 'c:\tmp\test.bak'
WITH FORMAT, MEDIANAME = 'Test' ;
GO
```

### <a name="step-2-copy-the-backup-file-into-the-pods-file-system"></a>2단계: Pod의 파일 시스템에 백업 파일 복사

SQL 인스턴스가 배포된 Pod의 이름을 찾습니다. 일반적으로 `pod/<sqlinstancename>-0`과 비슷하게 표시됩니다.

다음을 실행하여 모든 Pod 목록을 가져옵니다.

```console
kubectl get pods -n <namespace of data controller>
```

예제:

로컬 스토리지에서 클러스터의 SQL Pod로 백업 파일을 복사합니다.

```console
kubectl cp <source file location> <pod name>:var/opt/mssql/data/<file name> -n <namespace name>

#Example:
kubectl cp C:\Backupfiles\test.bak sqlinstance1-0:var/opt/mssql/data/test.bak -n arc
```

### <a name="step-3-restore-the-database"></a>3단계: 데이터베이스 복원

RESTORE 명령을 준비하고 실행하여 Azure SQL 관리형 인스턴스 - Azure Arc로 백업 파일을 복원합니다.

```sql
RESTORE DATABASE test FROM DISK = '/var/opt/mssql/data/<file name>.bak'
WITH MOVE '<database name>' to '/var/opt/mssql/data/<file name>.mdf'  
,MOVE '<database name>' to '/var/opt/mssql/data/<file name>_log.ldf'  
,RECOVERY  
,REPLACE  
,STATS = 5;  
GO
```

예제:

```sql
RESTORE DATABASE test FROM DISK = '/var/opt/mssql/data/test.bak'
WITH MOVE 'test' to '/var/opt/mssql/data/test.mdf'  
,MOVE 'test' to '/var/opt/mssql/data/test_log.ldf'  
,RECOVERY  
,REPLACE  
,STATS = 5;  
GO
```

## <a name="next-steps"></a>다음 단계

[Azure Arc 지원 SQL Managed Instance의 특징 및 기능에 대해 자세히 알아보기](managed-instance-features.md)

[데이터 컨트롤러를 생성하여 시작](create-data-controller-indirect-cli.md)

[데이터 컨트롤러가 이미 만들어져 있나요? Azure Arc 지원 SQL Managed Instance 만들기](create-sql-managed-instance.md)
