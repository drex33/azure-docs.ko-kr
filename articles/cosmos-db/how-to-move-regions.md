---
title: Azure Cosmos DB 계정을 다른 영역으로 이동
description: Azure Cosmos DB 계정을 다른 영역으로 이동하는 방법을 알아봅니다.
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.custom: subject-moving-resources
ms.date: 05/13/2021
ms.author: mjbrown
ms.openlocfilehash: abe4c084243025cb5570d8913911a45554f067f5
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131454526"
---
# <a name="move-an-azure-cosmos-db-account-to-another-region"></a>Azure Cosmos DB 계정을 다른 영역으로 이동
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

이 문서에서는 다음 중 하나를 수행하는 방법을 설명합니다.

- Azure Cosmos DB에서 데이터가 복제되는 영역을 이동합니다.
- 한 영역에서 다른 영역으로 계정(Azure Resource Manager) 메타데이터 및 데이터를 마이그레이션합니다.

## <a name="move-data-from-one-region-to-another"></a>한 영역에서 다른 영역으로 데이터 이동

Azure Cosmos DB은 데이터 복제를 기본적으로 지원하므로 한 영역에서 다른 영역으로 데이터를 이동하는 것이 간단합니다. Azure Portal, Azure PowerShell 또는 Azure CLI를 사용하여 이 작업을 수행할 수 있습니다. 다음 단계를 포함합니다.

1. 계정에 새 영역을 추가합니다.

    Azure Cosmos DB 계정에 새 영역을 추가하려면 [Azure Cosmos DB 계정에 지역 추가/제거](how-to-manage-database-account.md#addremove-regions-from-your-database-account)를 참조하세요.

1. 새 지역으로 수동 장애 조치(failover)를 수행합니다.

    제거할 영역이 현재 계정의 쓰기 영역인 경우 이전 단계에서 추가된 새 영역으로 장애 조치(failover)를 시작해야 합니다. 가동 중지 시간이 없는 작업입니다. 여러 영역 계정에서 읽기 영역을 이동하는 경우 이 단계를 건너뛸 수 있습니다. 
    
    장애 조치(failover)를 시작하려면 [Azure Cosmos 계정에서 수동 장애 조치(failover) 수행](how-to-manage-database-account.md#manual-failover)을 참조하세요.

1. 원래 영역을 제거합니다.

    Azure Cosmos DB 계정에서 영역을 제거하려면 [Azure Cosmos DB 계정에서 영역 추가/제거](how-to-manage-database-account.md#addremove-regions-from-your-database-account)를 참조하세요.

> [!NOTE]
> [비동기 처리량 크기 조정 작업이](scaling-provisioned-throughput-best-practices.md#background-on-scaling-rus) 진행 중인 동안 장애 조치 (failover) 작업을 수행 하거나 새 지역을 추가/제거 하는 경우 처리량 확장 작업이 일시 중지 됩니다. 장애 조치 (failover) 또는 지역 추가/제거 작업이 완료 되 면 자동으로 다시 시작 됩니다. 

## <a name="migrate-azure-cosmos-db-account-metadata"></a>Azure Cosmos DB 계정 메타데이터 마이그레이션

Azure Cosmos DB은 기본적으로 한 영역에서 다른 영역으로 계정 메타데이터 마이그레이션을 지원하지 않습니다. 계정 메타데이터와 고객 데이터를 모두 한 영역에서 다른 영역으로 마이그레이션하려면 원하는 영역에서 새 계정을 만든 다음 데이터를 수동으로 복사해야 합니다. 

SQL API에 대한 가동 중지 시간이 0에 가까운 마이그레이션에는 [변경 피드](change-feed.md) 또는 이를 사용하는 도구를 사용해야 합니다. MongoDB API, Cassandra API 또는 다른 API를 마이그레이션하거나 계정 간에 데이터를 마이그레이션하는 옵션에 대해 자세히 알아보려면 [온-프레미스 또는 클라우드 데이터를 Azure Cosmos DB로 마이그레이션하는 옵션](cosmosdb-migrationchoices.md)을 참조하세요. 

다음 단계에서는 SQL API 및 해당 데이터에 대해 Azure Cosmos DB 계정을 한 영역에서 다른 영역으로 마이그레이션하는 방법을 보여 줍니다.

1. 원하는 영역에 새 Azure Cosmos DB 계정을 만듭니다.

    Azure Portal, PowerShell 또는 Azure CLI를 통해 새 계정을 만들려면 [Azure Cosmos DB 계정 만들기](how-to-manage-database-account.md#create-an-account)를 참조하세요.

1. 데이터베이스 및 컨테이너를 만듭니다.

    새 데이터베이스 및 컨테이너를 만들려면 [Azure Cosmos 컨테이너 만들기](how-to-create-container.md)를 참조하세요.

1. Azure Cosmos DB Live Data Migrator 도구를 사용하여 데이터를 마이그레이션합니다.

    가동 중지 시간이 거의 없이 데이터를 마이그레이션하려면 [Azure Cosmos DB Live Data Migrator 도구](https://github.com/Azure-Samples/azure-cosmosdb-live-data-migrator)를 참조하세요.

1. 애플리케이션 연결 문자열을 업데이트합니다.

    Live Data Migrator 도구가 계속 실행하여 애플리케이션의 새 배포에서 연결 정보를 업데이트합니다. Azure Portal에서 애플리케이션에 대한 엔드포인트 및 키를 검색할 수 있습니다.

    :::image type="content" source="./media/secure-access-to-data/nosql-database-security-master-key-portal.png" alt-text="NoSQL 데이터베이스 보안을 설명하는 Azure Portal의 액세스 제어.":::

1. 요청을 새 애플리케이션으로 리디렉션합니다.

    새 애플리케이션이 Azure Cosmos DB에 연결된 후 클라이언트 요청을 새 배포로 리디렉션할 수 있습니다.

1. 더 이상 필요하지 않은 리소스를 삭제합니다.

    이제 요청이 새 인스턴스로 완전히 리디렉션되면 이전 Azure Cosmos DB 계정 및 Live Data Migrator 도구를 삭제할 수 있습니다.

## <a name="next-steps"></a>다음 단계

Azure Cosmos 계정과 데이터베이스 및 컨테이너를 관리하는 방법에 대한 자세한 내용 및 예제는 다음 문서를 읽어보세요.

* [Azure Cosmos 계정 관리](how-to-manage-database-account.md)
* [Azure Cosmos DB의 변경 피드](change-feed.md)
