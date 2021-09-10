---
title: 하이퍼스케일 보조 복제본
description: 이 문서에서는 하이퍼스케일 서비스 계층에서 사용할 수 있는 다양한 유형의 보조 복제본에 대해 설명합니다.
services: sql-database
ms.service: sql-database
ms.subservice: service-overview
ms.topic: overview
author: yorek
ms.author: damauri
ms.reviewer: ''
ms.date: 7/27/2021
ms.openlocfilehash: 9fa0a964033439a4b1c5276bf4310528dd345ff8
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "121722901"
---
# <a name="hyperscale-secondary-replicas"></a>하이퍼스케일 보조 복제본
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

[분산 기능 아키텍처](service-tier-hyperscale.md)에 설명된 대로 Azure SQL Database 하이퍼스케일에는 두 가지 유형의 컴퓨팅 노드(복제본이라고도 함)가 있습니다.

- 주: 읽기 및 쓰기 작업을 제공합니다.
- 보조: 읽기 확장, 고가용성, 지역 복제를 제공합니다.

보조 복제본은 항상 읽기 전용이며 다음 세 가지 유형일 수 있습니다.

- 고가용성 복제본
- 명명된 복제본(미리 보기)
- 지역 복제본(미리 보기)

아키텍처, 기능 세트, 목적, 비용은 유형마다 다릅니다. 필요한 기능에 따라 세 유형 중 하나만 사용하거나 모든 유형을 함께 사용할 수도 있습니다.

## <a name="high-availability-replica"></a>고가용성 복제본

HA(고가용성) 복제본은 주 복제본과 동일한 페이지 서버를 사용하므로 HA 복제본을 추가하기 위해 데이터를 복사할 필요가 없습니다. HA 복제본은 장애 조치(failover) 목적으로 상시 대기로 작동하기 때문에 주로 데이터베이스 가용성을 제공하는 데 사용됩니다. 주 복제본을 사용할 수 없게 되면 기존 HA 복제본 중 하나로 장애 조치(failover)가 자동으로 빠르게 수행됩니다. 연결 문자열은 변경할 필요가 없습니다. 장애 조치(failover) 중 활성 연결이 끊어지기 때문에 애플리케이션에서 최소한의 가동 중지 시간이 발생할 수 있습니다. 일반적으로 이 시나리오에서는 적절한 재시도 논리를 사용하는 것이 좋습니다. 일부 드라이버는 이미 일정 수준의 자동 재시도 논리를 제공합니다. .NET을 사용하는 경우 [최신 Microsoft.Data.SqlClient](https://devblogs.microsoft.com/azure-sql/configurable-retry-logic-for-microsoft-data-sqlclient/) 라이브러리에서 기본적으로 구성 가능한 자동 재시도 논리를 완전히 지원합니다.

HA 복제본은 주 복제본과 동일한 서버 및 데이터베이스 이름을 사용합니다. 서비스 수준 목표도 주 복제본과 항상 같습니다. HA 복제본은 포털이나 API에서 독립 실행형 리소스로 표시되거나 관리할 수 없습니다. 

0~4개의 HA 복제본을 사용할 수 있습니다. 데이터베이스를 만드는 동안 또는 데이터베이스를 만든 후 일반적인 관리 엔드포인트와 도구(예: PowerShell, AZ CLI, Portal, REST API)를 통해 해당 번호를 변경할 수 있습니다. HA 복제본을 만들거나 제거해도 주 복제본의 활성 연결에는 영향을 주지 않습니다.

### <a name="connecting-to-an-ha-replica"></a>HA 복제본에 연결

하이퍼스케일 데이터베이스에서 클라이언트가 사용하는 연결 문자열의 `ApplicationIntent` 인수는 연결이 읽기/쓰기 주 복제본으로 라우팅되는지, 아니면 읽기 전용 HA 복제본으로 라우팅되는지를 나타냅니다. `ApplicationIntent`가 `ReadOnly`로 설정되고 데이터베이스에 보조 복제본이 없는 경우 연결은 주 복제본으로 라우팅되고 기본값은 `ReadWrite` 동작으로 설정됩니다.

```csharp
-- Connection string with application intent
Server=tcp:<myserver>.database.windows.net;Database=<mydatabase>;ApplicationIntent=ReadOnly;User ID=<myLogin>;Password=<myPassword>;Trusted_Connection=False; Encrypt=True;
```

모든 HA 복제본의 리소스 용량은 동일합니다. 둘 이상의 HA 복제본이 있는 경우 읽기 의도 워크로드가 사용 가능한 모든 복제본에 임의로 분산됩니다. HA 복제본이 여러 개 있는 경우 주 복제본의 데이터 변경 내용에 따라 각 HA 복제본의 데이터 대기 시간은 서로 다를 수 있습니다. 각 HA 복제본은 동일한 페이지 서버 세트의 주 복제본과 동일한 데이터를 사용합니다. 하지만 각 HA 복제본의 로컬 데이터 캐시는 주 복제본에서 HA 복제본으로 로그 레코드를 전달하는 트랜잭션 로그 서비스를 통해 주 복제본의 변경 내용을 반영합니다. 따라서 HA 복제본에서 처리되는 워크로드에 따라 로그 레코드가 다른 속도로 적용될 수 있으므로 각 복제본의 데이터 대기 시간은 주 복제본을 기준으로 다를 수 있습니다.

## <a name="named-replica-in-preview"></a>명명된 복제본(미리 보기)

HA 복제본과 마찬가지로 명명된 복제본은 주 복제본과 동일한 페이지 서버를 사용합니다. HA 복제본과 유사하게, 명명된 복제본을 추가하는 데 데이터 복사는 필요하지 않습니다. 

HA 복제본과 명명된 복제본의 차이점은 다음과 같습니다. 

- 명명된 복제본은 포털과 API(AZ CLI, PowerShell, T-SQL) 호출에서 일반적인(읽기 전용) Azure SQL 데이터베이스로 표시됩니다.
- 명명된 복제본은 주 복제본과 다른 데이터베이스 이름을 사용할 수 있으며, 필요에 따라 다른 논리 서버에 있을 수 있습니다(주 복제본과 동일한 지역에 있는 경우).
- 명명된 복제본에는 주 복제본과 독립적으로 설정하고 변경할 수 있는 자체 서비스 수준 목표가 있습니다.
- 명명된 복제본은 주 복제본마다 최대 30개의 명명된 복제본을 지원합니다.
- 명명된 복제본을 호스트하는 논리 서버에서 서로 다른 로그인을 만들어 명명된 복제본마다 다른 인증을 지원할 수 있습니다.

명명된 복제본의 주요 목표는 대규모 OLTP 읽기 확장 시나리오를 지원하고 HTAP(하이브리드 트랜잭션 및 분석 처리) 워크로드를 개선하는 것입니다. 해당 솔루션을 만드는 방법의 예는 아래에서 확인할 수 있습니다.

- [OLTP 스케일 아웃 샘플](https://github.com/Azure-Samples/azure-sql-db-named-replica-oltp-scaleout)
- [HTAP 스케일 아웃 샘플](https://github.com/Azure-Samples/azure-sql-db-named-replica-htap)

위에 나열된 주요 시나리오 외에도 명명된 복제본은 유연성과 탄력성을 제공하여 다른 많은 사용 사례를 충족합니다.
- [액세스 격리](hyperscale-named-replica-security-configure.md): 명명된 특정 복제본에 액세스 권한을 부여할 수 있지만 주 복제본 또는 다른 명명된 복제본에는 부여할 수 없습니다.
- 워크로드 종속 서비스 목표: 명명된 복제본은 자체 서비스 수준 목표를 가질 수 있기 때문에 워크로드와 사용 사례마다 서로 다른 명명된 복제본을 사용할 수 있습니다. 예를 들어 하나의 명명된 복제본을 사용하여 Power BI 요청을 처리하고, 다른 명명된 복제본을 사용하여 데이터 과학 작업 데이터를 Apache Spark에 제공할 수 있습니다. 각 명명된 복제본은 독립적인 서비스 수준 목표를 가지고 독립적으로 스케일링될 수 있습니다.
- 워크로드 종속 라우팅: 최대 30개의 명명된 복제본까지 명명된 복제본을 그룹으로 사용하여 애플리케이션을 서로 격리할 수 있습니다. 예를 들어 네 개의 명명된 복제본 그룹을 사용하여 모바일 애플리케이션에서 들어오는 요청을 처리하고, 다른 두 개의 명명된 복제본 그룹을 사용하여 웹 애플리케이션에서 들어오는 요청을 처리할 수 있습니다. 이 방법을 사용하면 각 그룹의 성능과 비용을 세부적으로 튜닝할 수 있습니다.

다음 예에서는 `WideWorldImporters` 데이터베이스의 복제본 `WideWorldImporters_NR`을 만듭니다. 주 복제본은 서비스 수준 목표 HS_Gen5_4를 사용하고, 명명된 복제본은 HS_Gen5_2를 사용합니다. 둘 다 동일한 논리 서버 `MyServer`를 사용합니다. REST API를 직접 사용하려는 경우 [데이터베이스 - 명명된 보조 복제본으로 데이터베이스 만들기](/rest/api/sql/2020-11-01-preview/databases/createorupdate#creates-a-database-as-named-replica-secondary) 옵션도 사용할 수 있습니다.

# <a name="t-sql"></a>[T-SQL](#tab/tsql)
```sql
ALTER DATABASE [WideWorldImporters]
ADD SECONDARY ON SERVER [MyServer] 
WITH (SERVICE_OBJECTIVE = 'HS_Gen5_2', SECONDARY_TYPE = Named, DATABASE_NAME = [WideWorldImporters_NR]);
```
# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
```azurepowershell
New-AzSqlDatabaseSecondary -ResourceGroupName "MyResourceGroup" -ServerName "MyServer" -DatabaseName "WideWorldImporters" -PartnerResourceGroupName "MyResourceGroup" -PartnerServerName "MyServer" -PartnerDatabaseName "WideWorldImporters_NR" -SecondaryServiceObjectiveName HS_Gen5_2
```
# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
```azurecli
az sql db replica create -g MyResourceGroup -n WideWorldImporters -s MyServer --secondary-type named --partner-database WideWorldImporters_NR --partner-server MyServer --service-objective HS_Gen5_2
```

---

데이터 이동이 없기 때문에 대부분의 경우 명명된 복제본은 약 1분 이내에 생성됩니다. 명명된 복제본을 사용할 수 있게 되면 포털이나 AZ CLI 또는 PowerShell과 같은 명령줄 도구에서 볼 수 있습니다. 명명된 복제본은 일반 읽기 전용 데이터베이스로 사용할 수 있습니다. 

> [!NOTE]
> 하이퍼스케일 명명된 복제본에 대한 자주 묻는 질문은 [Azure SQL Database 하이퍼스케일 명명된 복제본 FAQ](service-tier-hyperscale-named-replicas-faq.yml)를 참조하세요.

### <a name="connecting-to-a-named-replica"></a>명명된 복제본에 연결

명명된 복제본에 연결하려면 명명된 복제본의 연결 문자열을 사용하여 서버 이름과 데이터베이스 이름을 참조해야 합니다. 명명된 복제본은 항상 읽기 전용이기 때문에 “ApplicationIntent=ReadOnly” 옵션을 지정할 필요가 없습니다.

HA 복제본과 마찬가지로 주 복제본, HA 복제본, 명명된 복제본이 동일한 페이지 서버 세트의 동일한 데이터를 공유하더라도 각 명명된 복제본의 데이터 캐시는 주 복제본에서 명명된 복제본으로 로그 레코드를 전달하는 트랜잭션 로그 서비스를 통해 주 복제본과 동기화 상태를 유지합니다. 따라서 명명된 복제본에서 처리되는 워크로드에 따라 로그 레코드가 다른 속도로 적용될 수 있으므로 각 복제본의 데이터 대기 시간은 주 복제본을 기준으로 다를 수 있습니다.

### <a name="modifying-a-named-replica"></a>명명된 복제본 수정

명명된 복제본을 만들 때 `ALTER DATABASE` 명령이나 지원되는 다른 방법(AZ CLI, PowerShell, REST API)을 통해 서비스 수준 목표를 정의할 수 있습니다. 명명된 복제본을 만든 후 서비스 수준 목표를 변경해야 하는 경우 명명된 복제본 자체에서 `ALTER DATABASE ... MODIFY` 명령을 사용하면 됩니다. 예를 들어 `WideWorldImporters_NR`이 `WideWorldImporters` 데이터베이스의 명명된 복제본인 경우 아래와 같이 수행하면 됩니다. 

# <a name="t-sql"></a>[T-SQL](#tab/tsql)
```sql
ALTER DATABASE [WideWorldImporters_NR] MODIFY (SERVICE_OBJECTIVE = 'HS_Gen5_4')
```
# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
```azurepowershell
Set-AzSqlDatabase -ResourceGroup "MyResourceGroup" -ServerName "MyServer" -DatabaseName "WideWorldImporters_NR" -RequestedServiceObjectiveName "HS_Gen5_4"
```
# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
```azurecli
az sql db update -g MyResourceGroup -s MyServer -n WideWorldImporters_NR --service-objective HS_Gen5_4
```

---

### <a name="removing-a-named-replica"></a>명명된 복제본 제거

명명된 복제본을 제거하려면 일반 데이터베이스를 삭제할 때처럼 삭제하면 됩니다. 삭제하려는 명명된 복제본이 있는 서버의 `master` 데이터베이스에 연결한 후 다음 명령을 사용합니다.

# <a name="t-sql"></a>[T-SQL](#tab/tsql)
```sql
DROP DATABASE [WideWorldImporters_NR];
```
# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
```azurepowershell
Remove-AzSqlDatabase -ResourceGroupName "MyResourceGroup" -ServerName "MyServer" -DatabaseName "WideWorldImporters_NR"
```
# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
```azurecli
az sql db delete -g MyResourceGroup -s MyServer -n WideWorldImporters_NR
```
---

> [!IMPORTANT]
> 명명된 복제본을 만들 때 사용된 주 복제본을 삭제하면 명명된 복제본이 자동으로 제거됩니다.

### <a name="known-issues"></a>알려진 문제

#### <a name="partially-incorrect-data-returned-from-sysdatabases"></a>sys.databases에서 부분적으로 잘못된 데이터가 반환됨
퍼블릭 미리 보기 중에 `sys.databases`에서 명명된 복제본에 대해 `name` 및 `database_id` 이외의 열에 반환된 행 값은 일관되지 않고 올바르지 않을 수 있습니다. 예를 들어 명명된 복제본을 만들 때 사용된 주 데이터베이스가 150으로 설정된 경우에도 명명된 복제본의 `compatibility_level` 열에 140으로 보고될 수 있습니다. 해결 방법은 가능한 경우 올바른 데이터를 반환하는 `DATABASEPROPERTYEX()` 함수를 사용하여 동일한 데이터를 가져오는 것입니다.

## <a name="geo-replica-in-preview"></a>지역 복제본(미리 보기)

[활성 지역 복제](active-geo-replication-overview.md)를 사용하면 동일하거나 다른 Azure 지역에 주 하이퍼스케일 데이터베이스의 읽기 가능한 보조 복제본을 만들 수 있습니다. 지역 복제본은 다른 논리 서버에 만들어야 합니다. 지역 복제본의 데이터베이스 이름은 주 복제본의 데이터베이스 이름과 항상 일치합니다.

지역 복제본을 만드는 경우 모든 데이터가 주 복제본에서 다른 페이지 서버 세트로 복사됩니다. 지역 복제본은 동일한 지역에 있더라도 주 복제본과 페이지 서버를 공유하지 않습니다. 이 아키텍처는 지역 장애 조치에 필요한 중복성을 제공합니다.

지역 복제본은 비동기 복제를 통해 트랜잭션 측면에서 일관된 데이터베이스 사본을 유지하는 데 사용됩니다. 지역 복제본이 다른 Azure 지역에 있는 경우 주 지역에서 재해 또는 중단이 발생하면 재해 복구에 사용될 수 있습니다. 지역 복제본은 지리적 읽기 확장 시나리오에서도 사용할 수 있습니다.

하이퍼스케일에서는 지역 장애 조치(failover)를 수동으로 시작해야 합니다. 장애 조치 후 새로운 주 복제본에 해당 복제본을 호스트하는 논리 서버 이름을 참조하는 다른 연결 엔드포인트가 생성됩니다. 자세한 내용은 [활성 지역 복제](active-geo-replication-overview.md)를 참조하세요.

하이퍼스케일 데이터베이스에 대한 지역 복제는 현재 미리 보기로 제공되며 다음과 같은 제한 사항이 있습니다.
- 동일하거나 다른 지역에 하나의 지역 복제본만 만들 수 있습니다.
- 장애 조치 그룹은 지원되지 않습니다. 
- 계획된 장애 조치는 지원되지 않습니다.
- 지역 복제본의 특정 시점 복원은 지원되지 않습니다.
- 지역 복제본의 데이터베이스 복사본을 만들 수 없습니다. 
- 보조 복제본의 보조 복제본(“지역 복제본 체인”이라고도 함)은 지원되지 않습니다. 

## <a name="next-steps"></a>다음 단계

- [하이퍼스케일 서비스 계층](service-tier-hyperscale.md)
- [활성 지역 복제](active-geo-replication-overview.md)
- [Azure SQL Database의 하이퍼스케일 명명된 복제본에 대한 격리된 액세스를 허용하도록 보안 구성](hyperscale-named-replica-security-configure.md)
- [Azure SQL Database 하이퍼스케일 명명된 복제본 FAQ](service-tier-hyperscale-named-replicas-faq.yml)
