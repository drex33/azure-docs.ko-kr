---
title: 공유 데이터베이스
description: Azure Synapse Analytics는 서버리스 Apache Spark 풀에서 데이터베이스를 만들면 서버리스 SQL 풀 및 SQL 풀 엔진에서 액세스할 수 있는 공유 메타데이터 모델을 제공합니다.
services: synapse-analytics
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: metadata
ms.date: 10/05/2021
author: ma77b
ms.author: maburd
ms.reviewer: wiassaf
ms.custom: devx-track-csharp
ms.openlocfilehash: 6144afdca350e8e7ff609eec273f84a39b84d2a3
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130228490"
---
# <a name="azure-synapse-analytics-shared-database"></a>Azure Synapse Analytics 공유 데이터베이스

Azure Synapse Analytics를 사용하면 여러 컴퓨팅 작업 영역 엔진이 데이터베이스와 테이블을 공유할 수 있습니다. 현재 Apache Spark 풀에 만들어진 데이터베이스와 테이블(Parquet 또는 CSV 지원)은 서버리스 SQL 풀 엔진과 자동으로 공유됩니다.

Spark 작업을 사용하여 만든 데이터베이스는 서버리스 SQL 풀 엔진을 포함하여 작업 영역에 있는 모든 현재 및 미래의 Spark 풀에 동일한 이름으로 표시됩니다. 서버리스 SQL 풀을 사용하여 이 동기화된 데이터베이스에 직접 사용자 지정 개체(외부 테이블, 보기, 프로시저)를 추가할 수 없습니다.

`default`라는 Spark 기본 데이터베이스는 서버리스 SQL 풀 컨텍스트에서 `default`라는 데이터베이스로도 표시됩니다. Spark에서 데이터베이스를 만든 다음, 서버리스 SQL 풀에서 같은 이름으로 다른 데이터베이스를 만들 수 없습니다.

데이터베이스는 서버리스 SQL 풀에 비동기적으로 동기화되므로 표시될 때까지 지연됩니다.

## <a name="manage-a-spark-created-database"></a>Spark에서 만든 데이터베이스 관리

Spark에서 만든 데이터베이스를 관리하려면 Apache Spark 풀을 사용해야 합니다. 예를 들어 Spark 풀 작업을 통해 만들거나 삭제합니다.

동기화된 데이터베이스의 개체는 서버리스 SQL 풀에서 수정할 수 없습니다.

>[!NOTE]
>다른 풀에서 같은 이름으로 여러 데이터베이스를 만들 수 없습니다. 서버리스 SQL 풀 데이터베이스가 만들어지면 동일한 이름으로 Spark 데이터베이스를 만들 수 없습니다. 각각 Spark에서 데이터베이스가 만들어지면 동일한 이름으로 서버리스 SQL 풀 데이터베이스를 만들 수 없습니다.

## <a name="security-model"></a>보안 모델

Spark 데이터베이스와 테이블은 SQL 엔진에서 동기화되는 해당 표현과 함께 기본 스토리지 수준에서 보호됩니다.

데이터베이스를 만드는 보안 주체는 해당 데이터베이스의 소유자로 간주되며, 데이터베이스와 해당 개체에 대한 모든 권한을 갖습니다. Synapse 관리자 및 Synapse SQL 관리자는 기본적으로 서버리스 SQL 풀의 동기화된 개체에 대한 모든 권한을 갖습니다. 동기화된 SQL 데이터베이스에서 사용자 지정 개체(사용자 포함)를 만들 수 없습니다. 

사용자, Azure AD 앱 또는 보안 그룹과 같은 보안 주체가 외부 테이블에 사용되는 기본 데이터에 액세스할 수 있도록 하려면 파일(예: 테이블의 기본 데이터 파일)의 `read (R)` 사용 권한과 파일이 루트까지 모든 상위 폴더에 저장되어 있는 폴더의 `execute (X)`에 사용 권한을 부여해야 합니다. [ACL(액세스 제어 목록)](../../storage/blobs/data-lake-storage-access-control.md) 페이지에서 이러한 권한에 대해 자세히 알아볼 수 있습니다. 

예를 들어, `https://<storage-name>.dfs.core.windows.net/<fs>/synapse/workspaces/<synapse_ws>/warehouse/mytestdb.db/myparquettable/`의 경우, 데이터베이스의 테이블을 읽을 수 있으려면 보안 주체가 `<fs>`에서 `myparquettable` 및 `R` 권한으로 시작하는 모든 폴더와 해당 폴더 내의 `myparquettable` 및 파일에 대해 `X` 권한을 가지고 있어야 합니다.

보안 주체가 데이터베이스에 개체를 만들거나 데이터베이스의 개체를 삭제할 수 있는 기능이 필요한 경우 `warehouse` 폴더의 폴더와 파일에 대한 추가 `W` 권한이 필요합니다. 데이터베이스의 개체는 서버리스 SQL 풀에서는 수정할 수 없으며 Spark에서만 수정할 수 있습니다.

## <a name="examples"></a>예

### <a name="create-and-connect-to-spark-database-with-serverless-sql-pool"></a>서버리스 SQL 풀을 사용하여 Spark 데이터베이스 만들기 및 연결

먼저 작업 영역에서 이미 만든 Spark 클러스터를 사용하여 `mytestdb`라는 새 Spark 데이터베이스를 만듭니다. 예를 들어 Spark C# Notebook을 사용하는 경우 다음 .NET for Spark 문을 사용하면 됩니다.

```csharp
spark.Sql("CREATE DATABASE mytestdb")
```

잠시 후 서버리스 SQL 풀에서 데이터베이스를 볼 수 있습니다. 예를 들어 서버리스 SQL 풀에서 다음 명령문을 실행합니다.

```sql
SELECT * FROM sys.databases;
```

결과에 `mytestdb`가 포함되어 있는지 확인합니다.

## <a name="next-steps"></a>다음 단계

- [Azure Synapse Analytics의 공유 메타데이터에 대한 자세한 정보](overview.md)
- [Azure Synapse Analytics의 공유 메타데이터 테이블에 대한 자세한 정보](table.md)