---
title: Bicep을 사용하여 Azure Cosmos DB 만들기 및 관리
description: Bicep을 사용하여 Azure Cosmos DB for Core(SQL) API 만들기 및 구성
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 09/13/2021
ms.author: mjbrown
ms.openlocfilehash: b5a3fcdffe3c93a396bb607595b1ee02775a26f2
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128634236"
---
# <a name="manage-azure-cosmos-db-core-sql-api-resources-with-bicep"></a>Bicep을 SQL(Azure Cosmos DB Core) API 리소스 관리

[!INCLUDE[appliesto-sql-api](../includes/appliesto-sql-api.md)]

이 문서에서는 Bicep을 사용하여 Azure Cosmos DB 계정, 데이터베이스 및 컨테이너를 배포하고 관리하는 방법을 알아봅니다.

이 문서에서는 Core(SQL) API 계정에 대한 Bicep 샘플을 보여줍니다. [Cassandra,](../cassandra/manage-with-bicep.md) [Gremlin,](../graph/manage-with-bicep.md) [MongoDB](../mongodb/manage-with-bicep.md)및 [Table](../table/manage-with-bicep.md) API에 대한 Bicep 샘플을 찾을 수도 있습니다.

> [!IMPORTANT]
>
> * 계정 이름은 44자(모두 소문자)로 제한됩니다.
> * 처리량 값을 변경하려면 업데이트된 RU/s를 사용하여 Bicep 파일을 다시 배포합니다.
> * Azure Cosmos 계정에 위치를 추가하거나 제거하면 다른 속성을 동시에 수정할 수 없습니다. 이러한 작업은 별도로 수행해야 합니다.
> * Azure Resource Manager가 리소스 URI와 함께 작동하는 방식을 위반하므로 Azure Cosmos DB 리소스의 이름을 바꿀 수 없습니다.
> * 데이터베이스 수준에서 처리량을 프로비전하고 모든 컨테이너에서 공유하려면 데이터베이스 옵션 속성에 처리량 값을 적용합니다.

아래 Azure Cosmos DB 리소스를 만들려면 다음 예제를 새 bicep 파일에 복사합니다. 필요에 따라 이름 및 값이 다른 동일한 리소스의 여러 인스턴스를 배포할 때 사용할 매개 변수 파일을 만들 수 있습니다. Azure CLI, Azure PowerShell 및 Cloud Shell 등 [Azure Resource Manager](../../azure-resource-manager/bicep/deploy-cli.md)템플릿을 배포하는 [](../../azure-resource-manager/bicep/deploy-powershell.md) 방법에는 여러 [가지가 있습니다.](../../azure-resource-manager/bicep/deploy-cloud-shell.md)

<a id="create-autoscale"></a>

## <a name="azure-cosmos-account-with-autoscale-throughput"></a>자동 크기 조정 처리량을 사용하는 Azure Cosmos 계정

일관성 및 장애 조치(failover) 옵션을 사용하여 두 지역에 Azure Cosmos 계정을 만들고, 대부분의 정책 옵션을 사용하도록 설정된 자동 크기 조정 처리량에 대해 데이터베이스 및 컨테이너를 구성합니다.

:::code language="bicep" source="~/quickstart-templates/quickstarts/microsoft.documentdb/cosmosdb-sql-autoscale/main.bicep":::

<a id="create-analytical-store"></a>

## <a name="azure-cosmos-account-with-analytical-store"></a>분석 저장소가 있는 Azure Cosmos 계정

분석 TTL이 사용하도록 설정된 컨테이너와 수동 또는 자동 크기 조정 처리량에 대한 옵션을 사용하여 한 지역에 Azure Cosmos 계정을 만듭니다.

:::code language="bicep" source="~/quickstart-templates/quickstarts/microsoft.documentdb/cosmosdb-sql-analytical-store/main.bicep":::

<a id="create-manual"></a>

## <a name="azure-cosmos-account-with-standard-provisioned-throughput"></a>프로비저닝된 표준 처리량을 사용하는 Azure Cosmos 계정

대부분의 정책 옵션을 사용하도록 설정된 표준 처리량에 대해 데이터베이스 및 컨테이너가 구성된 일관성 및 장애 조치(failover) 옵션을 사용하여 두 지역에 Azure Cosmos 계정을 만듭니다.

:::code language="bicep" source="~/quickstart-templates/quickstarts/microsoft.documentdb/cosmosdb-sql/main.bicep":::

<a id="create-sproc"></a>

## <a name="azure-cosmos-db-container-with-server-side-functionality"></a>서버 쪽 기능이 있는 Azure Cosmos DB 컨테이너

저장 프로시저, 트리거 및 사용자 정의 함수를 사용하여 Azure Cosmos 계정, 데이터베이스 및 컨테이너를 만듭니다.

:::code language="bicep" source="~/quickstart-templates/quickstarts/microsoft.documentdb/cosmosdb-sql-container-sprocs/main.bicep":::

<a id="create-rbac"></a>

## <a name="azure-cosmos-db-account-with-azure-ad-and-rbac"></a>Azure AD 및 RBAC를 사용하는 Azure Cosmos DB 계정

Azure Cosmos 계정, 고유하게 유지 관리되는 역할 정의 및 AAD ID에 대해 고유하게 유지 관리되는 역할 할당을 만듭니다.

:::code language="bicep" source="~/quickstart-templates/quickstarts/microsoft.documentdb/cosmosdb-sql-rbac/main.bicep":::

<a id="free-tier"></a>

## <a name="free-tier-azure-cosmos-db-account"></a>체험 계층 Azure Cosmos DB 계정

최대 25개의 컨테이너와 공유할 수 있는 공유 처리량이 있는 무료 계층 Azure Cosmos 계정 및 데이터베이스를 만듭니다.

:::code language="bicep" source="~/quickstart-templates/quickstarts/microsoft.documentdb/cosmosdb-free/main.bicep":::

## <a name="next-steps"></a>다음 단계

다음은 몇 가지 추가 리소스입니다.

* [Bicep 설명서](../../azure-resource-manager/bicep/index.yml)
* [Bicep 도구 설치](../../azure-resource-manager/bicep/install.md)
