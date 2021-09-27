---
title: Bicep을 사용하여 Azure Cosmos DB Table API 만들기 및 관리
description: Bicep을 사용하여 Azure Cosmos DB Table API 만들고 구성합니다.
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.topic: how-to
ms.date: 09/13/2021
ms.author: mjbrown
ms.openlocfilehash: fe911a945de6b7b15c49641b7aff3850c3759da2
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128634138"
---
# <a name="manage-azure-cosmos-db-table-api-resources-using-bicep"></a>Bicep을 사용하여 Azure Cosmos DB Table API 리소스 관리

[!INCLUDE[appliesto-table-api](../includes/appliesto-table-api.md)]

이 문서에서는 Bicep을 사용하여 Azure Cosmos DB Table API 계정 및 테이블을 배포하고 관리하는 방법을 알아봅니다.

이 문서에는 Table API 계정에 대한 예제만 있습니다. [Cassandra,](../cassandra/manage-with-bicep.md) [Gremlin,](../graph/manage-with-bicep.md) [MongoDB,](../mongodb/manage-with-bicep.md) [SQL](../sql/manage-with-bicep.md) 문서에 대한 Bicep 샘플을 찾을 수도 있습니다.

> [!IMPORTANT]
>
> * 계정 이름은 44자(모두 소문자)로 제한됩니다.
> * 처리량 값을 변경하려면 업데이트된 RU/s로 템플릿을 다시 배포합니다.
> * Azure Cosmos 계정에 위치를 추가하거나 제거하면 다른 속성을 동시에 수정할 수 없습니다. 이러한 작업은 별도로 수행해야 합니다.

아래 Azure Cosmos DB 리소스를 만들려면 다음 예제를 새 bicep 파일에 복사합니다. 필요에 따라 이름 및 값이 다른 동일한 리소스의 여러 인스턴스를 배포할 때 사용할 매개 변수 파일을 만들 수 있습니다. Azure CLI, Azure PowerShell 및 [Cloud Shell](../../azure-resource-manager/bicep/deploy-cli.md)등 [Azure Resource Manager](../../azure-resource-manager/bicep/deploy-powershell.md) 템플릿을 배포하는 방법에는 여러 [가지가 있습니다.](../../azure-resource-manager/bicep/deploy-cloud-shell.md)

> [!TIP]
> Table API를 사용할 때 공유 처리량을 활성화하려면 Azure Portal에서 계정 수준 처리량을 사용하도록 설정합니다. Bicep을 사용하여 계정 수준 공유 처리량을 설정할 수 없습니다.

<a id="create-autoscale"></a>

## <a name="azure-cosmos-account-for-table-with-autoscale-throughput"></a>자동 크기 조정 처리량을 사용하는 Table용 Azure Cosmos 계정

자동 크기 조정 처리량이 있는 하나의 테이블이 있는 Table API 대한 Azure Cosmos 계정을 만듭니다.

:::code language="bicep" source="~/quickstart-templates/quickstarts/microsoft.documentdb/cosmosdb-table-autoscale/main.bicep":::

<a id="create-manual"></a>

## <a name="azure-cosmos-account-for-table-with-standard-provisioned-throughput"></a>표준 프로비전된 처리량을 사용하는 Table용 Azure Cosmos 계정

표준 처리량이 있는 하나의 테이블이 있는 Table API 대한 Azure Cosmos 계정을 만듭니다.

:::code language="bicep" source="~/quickstart-templates/quickstarts/microsoft.documentdb/cosmosdb-table/main.bicep":::

## <a name="next-steps"></a>다음 단계

다음은 몇 가지 추가 리소스입니다.

* [Bicep 설명서](../../azure-resource-manager/bicep/index.yml)
* [Bicep 도구 설치](../../azure-resource-manager/bicep/install.md)
