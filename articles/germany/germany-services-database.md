---
title: Azure 독일 데이터베이스 서비스 | Microsoft Docs
description: 이 문서에서는 Azure 독일에 대한 SQL 데이터베이스 서비스 비교를 제공합니다.
ms.topic: article
ms.date: 10/16/2020
author: gitralf
ms.author: ralfwi
ms.service: germany
ms.custom: bfdocs
ms.openlocfilehash: 6316c381a601d1a28c3f6ecf529b3d31526bfd45
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/21/2020
ms.locfileid: "122535438"
---
# <a name="azure-germany-database-services"></a>Azure 독일 데이터베이스 서비스

[!INCLUDE [closureinfo](../../includes/germany-closure-info.md)]

## <a name="sql-database"></a>SQL Database
Azure SQL Database 및 Azure SQL Managed Instance V12는 일반적으로 Azure 독일에서 제공됩니다. 메타데이터 표시 여부 구성에 대한 추가 지침 및 보호 모범 사례는 [SQL Database 엔진에 대한 Microsoft Security Center](/sql/relational-databases/security/security-center-for-sql-server-database-engine-and-azure-sql-database)와 [SQL Database 전체 설명서](../azure-sql/database/index.yml) 및 [SQL Managed Instance 전체 설명서](../azure-sql/managed-instance/index.yml)를 참조하세요.

### <a name="variations"></a>변형
Azure 독일의 SQL Database 주소는 글로벌 Azure의 주소와 다릅니다.

| 서비스 유형 | 글로벌 Azure | Azure 독일 |
| --- | --- | --- |
| SQL Database | *.database.windows.net | *.database.cloudapi.de |


## <a name="azure-cache-for-redis"></a>Azure Cache for Redis
Azure Cache for Redis 및 사용 방법에 대한 자세한 내용은 [Azure Cache for Redis 전체 설명서](../azure-cache-for-redis/index.yml)를 참조하세요.

### <a name="variations"></a>변형
Azure 독일의 Azure Cache for Redis에 액세스하고 관리하기 위한 URL은 글로벌 Azure의 URL과 다릅니다.

| 서비스 유형 | 글로벌 Azure | Azure 독일 |
| --- | --- | --- |
| 캐시 엔드포인트 | *.redis.cache.windows.net | *.redis.cache.cloudapi.de |
| Azure portal | https://portal.azure.com | https://portal.microsoftazure.de |

> [!NOTE]
> 모든 스크립트와 코드는 적절한 엔드포인트와 환경을 고려해야 합니다. 자세한 내용은 [Azure PowerShell을 사용하여 Azure Cache for Redis 관리](../azure-cache-for-redis/cache-how-to-manage-redis-cache-powershell.md)에서 "Microsoft Azure 독일에 연결하려면"을 참조하세요.
>
>


## <a name="next-steps"></a>다음 단계
추가 정보 및 업데이트를 보려면 [Azure 독일 블로그](/archive/blogs/azuregermany/)를 구독하세요.