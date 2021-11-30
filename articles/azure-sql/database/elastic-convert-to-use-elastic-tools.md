---
title: 확장하기 위해 기존 데이터베이스 마이그레이션
description: 분할된 데이터베이스 맵 관리자를 만들어 탄력적 데이터베이스 도구를 사용하기 위해 분할된 데이터베이스 변환
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: how-to
author: scoriani
ms.author: scoriani
ms.reviewer: mathoma
ms.date: 01/25/2019
ms.openlocfilehash: a8569f321165d6f040171c911794dd510977d016
ms.sourcegitcommit: 991268c548dd47e5f7487cd025c7501b9315e477
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/30/2021
ms.locfileid: "133287140"
---
# <a name="migrate-existing-databases-to-scale-out"></a>확장하기 위해 기존 데이터베이스 마이그레이션
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

도구(예: [탄력적 데이터베이스 클라이언트 라이브러리](elastic-database-client-library.md))를 사용하여 기존의 확장된 분할된 데이터베이스를 쉽게 관리합니다. 기존의 데이터베이스 집합을 먼저 변환하여 [분할된 데이터베이스 맵 관리자](elastic-scale-shard-map-management.md)를 사용합니다.

## <a name="overview"></a>개요

기존의 분할된 데이터베이스를 마이그레이션하려면:

1. [분할된 데이터베이스 맵 관리자 데이터베이스](elastic-scale-shard-map-management.md)를 준비합니다.
2. 분할된 데이터베이스 맵을 만듭니다.
3. 개별 분할된 데이터베이스를 준비합니다.  
4. 분할된 데이터베이스 맵에 매핑을 추가합니다.

이러한 기술은 [.NET Framework 클라이언트 라이브러리](https://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Client/) 또는 [Azure SQL Database - 탄력적 데이터베이스 도구 스크립트](https://github.com/Azure/elastic-db-tools/tree/master/Samples/PowerShell)에 있는 PowerShell 스크립트 중 하나를 사용하여 구현할 수 있습니다. 여기에 있는 예제에서는 PowerShell 스크립트를 사용합니다.

ShardMapManager에 대한 자세한 내용은 [분할된 데이터베이스 맵 관리](elastic-scale-shard-map-management.md)를 참조하세요. 탄력적 데이터베이스 도구에 대한 개요는 [ 기능 개요](elastic-scale-introduction.md)를 참조하세요.

## <a name="prepare-the-shard-map-manager-database"></a>분할된 데이터베이스 맵 관리자 데이터베이스를 준비합니다.

분할된 데이터베이스 맵 관리자는 확장된 데이터베이스를 관리하는 데이터를 포함하는 특별한 데이터베이스입니다. 기존 데이터베이스를 사용하거나 새 데이터베이스를 만들 수 있습니다. 분할된 데이터베이스 맵 관리자의 역할을 하는 데이터베이스는 분할된 데이터베이스와 같은 데이터베이스가 아니어야 합니다. PowerShell 스크립트는 데이터베이스를 만들지 않습니다.

## <a name="step-1-create-a-shard-map-manager"></a>1단계: 분할된 데이터베이스 맵 관리자 만들기

```powershell
# Create a shard map manager
New-ShardMapManager -UserName '<user_name>' -Password '<password>' -SqlServerName '<server_name>' -SqlDatabaseName '<smm_db_name>'
#<server_name> and <smm_db_name> are the server name and database name
# for the new or existing database that should be used for storing
# tenant-database mapping information.
```

### <a name="to-retrieve-the-shard-map-manager"></a>분할된 데이터베이스 맵 관리자를 검색하려면

만든 후 이 cmdlet을 사용하여 분할된 데이터베이스 맵 관리자를 검색할 수 있습니다. 이 단계는 ShardMapManager 개체를 사용해야 할 때마다 필요합니다.

```powershell
# Try to get a reference to the Shard Map Manager  
$ShardMapManager = Get-ShardMapManager -UserName '<user_name>' -Password '<password>' -SqlServerName '<server_name>' -SqlDatabaseName '<smm_db_name>'
```

## <a name="step-2-create-the-shard-map"></a>2단계: 분할된 데이터베이스 맵 만들기

만들 분할된 데이터베이스 맵의 종류를 선택합니다. 데이터베이스 아키텍처에 따라 선택합니다.

1. 데이터베이스당 단일 테넌트(용어는 [용어집](elastic-scale-glossary.md)참조.)
2. 데이터베이스당 다중 테넌트(두 가지 형식):
   1. 목록 매핑
   2. 범위 매핑

단일 테넌트 모델은 **목록 매핑** 분할된 데이터베이스 맵을 만듭니다. 단일 테넌트 모델은 테넌트당 하나의 데이터베이스를 할당합니다. 관리를 단순화하므로 SaaS 개발자에게 효과적인 모델입니다.

![목록 매핑][1]

다중 테넌트 모델은 개별 데이터베이스에 여러 테넌트를 할당합니다. 여러 데이터베이스에 테넌트 그룹을 분산시킬 수도 있습니다. 각 테넌트에 데이터 요구가 적다고 예상되는 경우 이 모델을 사용합니다. 이 모델에서 **범위 매핑** 을 사용하여 데이터베이스에 테넌트의 범위를 할당합니다.

![범위 매핑][2]

여러 테넌트를 개별 데이터베이스에 할당하는 *목록 매핑* 을 사용하여 다중 테넌트 데이터베이스 모델을 구현할 수도 있습니다. 예를 들어 DB1은 테넌트 ID 1과 5에 대한 정보를 저장하는 데 사용하고 DB2는 테넌트 7과 테넌트 10의 데이터를 저장합니다.

![단일 DB의 다중 테넌트][3]

**다음 옵션 중 하나를 선택합니다.**

### <a name="option-1-create-a-shard-map-for-a-list-mapping"></a>옵션1: 목록 매핑에 대한 분할된 데이터베이스 맵 만들기

ShardMapManager 개체를 사용하여 분할된 데이터베이스 맵을 만듭니다.

```powershell
# $ShardMapManager is the shard map manager object
$ShardMap = New-ListShardMap -KeyType $([int]) -ListShardMapName 'ListShardMap' -ShardMapManager $ShardMapManager
```

### <a name="option-2-create-a-shard-map-for-a-range-mapping"></a>옵션 2: 범위 매핑에 대한 분할된 데이터베이스 맵 만들기

이 매핑 패턴을 활용하려면 테넌트 ID 값이 연속 범위여야 합니다. 또한 데이터베이스를 만들 때 범위를 건너뛰어 범위에 갭이 있을 수 있습니다.

```powershell
# $ShardMapManager is the shard map manager object
# 'RangeShardMap' is the unique identifier for the range shard map.  
$ShardMap = New-RangeShardMap -KeyType $([int]) -RangeShardMapName 'RangeShardMap' -ShardMapManager $ShardMapManager
```

### <a name="option-3-list-mappings-on-an-individual-database"></a>옵션 3: 개별 데이터베이스의 매핑 나열

2단계 옵션 1과 같이 이 패턴을 설정할 때도 목록 맵을 만들어야 합니다.

## <a name="step-3-prepare-individual-shards"></a>3단계: 개별 분할된 데이터베이스 준비

각각의 분할된 데이터베이스를 분할된 데이터베이스 맵 관리자에 추가합니다. 이는 매핑 정보를 저장하기 위한 개별 데이터베이스를 준비합니다. 각각의 분할된 데이터베이스에서 이 메서드를 실행합니다.

```powershell
Add-Shard -ShardMap $ShardMap -SqlServerName '<shard_server_name>' -SqlDatabaseName '<shard_database_name>'
# The $ShardMap is the shard map created in step 2.
```

## <a name="step-4-add-mappings"></a>4단계: 매핑 추가

매핑 추가는 만든 분할된 데이터베이스 맵의 종류에 따라 달라집니다. 목록 맵을 만든 경우 목록 매핑을 추가합니다. 범위 맵을 만든 경우 범위 매핑을 추가합니다.

### <a name="option-1-map-the-data-for-a-list-mapping"></a>옵션 1: 목록 매핑을 위한 데이터 매핑

각 테넌트에 대한 목록 매핑을 추가하여 데이터를 매핑합니다.  

```powershell
# Create the mappings and associate it with the new shards
Add-ListMapping -KeyType $([int]) -ListPoint '<tenant_id>' -ListShardMap $ShardMap -SqlServerName '<shard_server_name>' -SqlDatabaseName '<shard_database_name>'
```

### <a name="option-2-map-the-data-for-a-range-mapping"></a>옵션 2: 범위 매핑을 위한 데이터 매핑

모든 테넌트 ID 범위에 대한 범위 매핑 추가 - 데이터베이스 연결:

```powershell
# Create the mappings and associate it with the new shards
Add-RangeMapping -KeyType $([int]) -RangeHigh '5' -RangeLow '1' -RangeShardMap $ShardMap -SqlServerName '<shard_server_name>' -SqlDatabaseName '<shard_database_name>'
```

### <a name="step-4-option-3-map-the-data-for-multiple-tenants-on-an-individual-database"></a>4단계 옵션 3: 개별 데이터베이스의 여러 테넌트에 대한 데이터 매핑

각 테넌트에 대해 ListMapping 추가를 실행합니다(옵션 1).

## <a name="checking-the-mappings"></a>매핑 확인

기존의 분할된 데이터베이스와 이와 연결된 매핑에 대한 정보는 다음 명령을 사용하여 쿼리할 수 있습니다.  

```powershell
# List the shards and mappings
Get-Shards -ShardMap $ShardMap
Get-Mappings -ShardMap $ShardMap
```

## <a name="summary"></a>요약

설치를 완료한 후에는 Elastic Database 클라이언트 라이브러리를 사용하기 시작할 수 있습니다. 또한 [데이터 종속 라우팅](elastic-scale-data-dependent-routing.md) 및 [다중 분할된 데이터베이스 쿼리](elastic-scale-multishard-querying.md)를 사용할 수도 있습니다.

## <a name="next-steps"></a>다음 단계

Azure 탄력적 데이터베이스 도구 스크립트에서 PowerShell [스크립트를 얻습니다.](https://github.com/Azure/elastic-db-tools/tree/master/Samples/PowerShell)

탄력적 데이터베이스 도구 클라이언트 라이브러리는 [azure/elastic-db-tools](https://github.com/Azure/elastic-db-tools)GitHub 사용할 수 있습니다.

분할/병합 도구를 사용하여 데이터를 다중 테넌트 모델에서 단일 테넌트 모델로 또는 반대로 이동합니다. [분할 병합 도구](elastic-scale-configure-deploy-split-and-merge.md)를 참조하세요.

## <a name="additional-resources"></a>추가 리소스

다중 테넌트 SaaS(software-as-a-service) 데이터베이스 애플리케이션의 일반적인 데이터 아키텍처 패턴에 대한 정보는 [Azure SQL Database를 사용한 다중 테넌트 SaaS 애플리케이션의 설계 패턴](saas-tenancy-app-design-patterns.md)을 참조하세요.

## <a name="questions-and-feature-requests"></a>질문 및 기능 요청

질문이 있으면 [SQL Database에 대한 Microsoft Q&A 질문 페이지](/answers/topics/azure-sql-database.html)를 사용하고, 기능 요청이 있는 경우 해당 기능을 [SQL Database 피드백 포럼](https://feedback.azure.com/d365community/forum/04fe6ee0-3b25-ec11-b6e6-000d3a4f0da0)에 추가하세요.

<!--Image references-->
[1]: ./media/elastic-convert-to-use-elastic-tools/listmapping.png
[2]: ./media/elastic-convert-to-use-elastic-tools/rangemapping.png
[3]: ./media/elastic-convert-to-use-elastic-tools/multipleonsingledb.png
