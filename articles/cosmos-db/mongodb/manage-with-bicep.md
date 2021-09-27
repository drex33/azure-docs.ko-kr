---
title: Bicep를 사용 하 여 Azure Cosmos DB에 대 한 MongoDB API 만들기 및 관리
description: Bicep를 사용 하 여 MongoDB api Azure Cosmos DB api를 만들고 구성 합니다.
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: how-to
ms.date: 09/13/2021
ms.author: mjbrown
ms.openlocfilehash: e6ded17c15b9a126a996f4e6f368a5ab5c735ed9
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128705699"
---
# <a name="manage-azure-cosmos-db-mongodb-api-resources-using-bicep"></a>Bicep를 사용 하 여 Azure Cosmos DB MongoDB API 리소스 관리

[!INCLUDE[appliesto-mongodb-api](../includes/appliesto-mongodb-api.md)]

이 문서에서는 Bicep를 사용 하 여 MongoDB API, 데이터베이스 및 컬렉션에 대 한 Azure Cosmos DB 계정을 배포 하 고 관리 하는 방법에 대해 알아봅니다.

이 문서에서는 Gremlin API 계정에 대 한 Bicep 샘플을 보여 줍니다. [SQL](../sql/manage-with-bicep.md), [Cassandra](../cassandra/manage-with-bicep.md), [Gremlin](../graph/manage-with-bicep.md)및 [Table](../table/manage-with-bicep.md) api에 대 한 Bicep 샘플도 찾을 수 있습니다.

> [!IMPORTANT]
>
> * 계정 이름은 44자(모두 소문자)로 제한됩니다.
> * 처리량 값을 변경하려면 업데이트된 RU/s로 템플릿을 다시 배포합니다.
> * Azure Cosmos 계정에 위치를 추가하거나 제거하면 다른 속성을 동시에 수정할 수 없습니다. 이러한 작업은 별도로 수행해야 합니다.

아래 Azure Cosmos DB 리소스를 만들려면 다음 예제를 새 bicep 파일에 복사 합니다. 필요에 따라 다른 이름 및 값을 사용 하 여 동일한 리소스의 여러 인스턴스를 배포할 때 사용할 매개 변수 파일을 만들 수 있습니다. , [Azure CLI](../../azure-resource-manager/bicep/deploy-cli.md), [Azure PowerShell](../../azure-resource-manager/bicep/deploy-powershell.md) 및 [Cloud Shell](../../azure-resource-manager/bicep/deploy-cloud-shell.md)를 비롯 한 Azure Resource Manager 템플릿을 배포 하는 방법에는 여러 가지가 있습니다.

<a id="create-autoscale"></a>

## <a name="mongodb-api-with-autoscale-provisioned-throughput"></a>자동 크기 조정 프로 비전 된 처리량이 있는 MongoDB API

이 템플릿은 데이터베이스 수준에서 자동 크기 조정 처리량을 공유 하는 두 개의 컬렉션을 사용 하 여 MongoDB API에 대 한 Azure Cosmos 계정 (3.2, 3.6 또는 4.0)을 만듭니다.

:::code language="bicep" source="~/quickstart-templates/quickstarts/microsoft.documentdb/cosmosdb-mongodb-autoscale/main.bicep":::

<a id="create-manual"></a>

## <a name="mongodb-api-with-standard-provisioned-throughput"></a>표준 프로 비전 된 처리량이 있는 MongoDB API

데이터베이스 수준에서 400 r u/s 표준 (수동) 처리량을 공유 하는 두 개의 컬렉션을 사용 하 여 MongoDB API에 대 한 Azure Cosmos 계정 (3.2, 3.6 또는 4.0)을 만듭니다.

:::code language="bicep" source="~/quickstart-templates/quickstarts/microsoft.documentdb/cosmosdb-mongodb/main.bicep":::

## <a name="next-steps"></a>다음 단계

다음은 몇 가지 추가 리소스입니다.

* [Bicep 설명서](../../azure-resource-manager/bicep/index.yml)
* [Bicep 도구 설치](../../azure-resource-manager/bicep/install.md)
* Azure Cosmos DB로 마이그레이션하기 위한 용량 계획을 수행하려고 하나요? 용량 계획을 위해 기존 데이터베이스 클러스터에 대한 정보를 사용할 수 있습니다.
  * 기존 데이터베이스 클러스터의 vCore 및 서버 수만 알고 있는 경우 [vCore 또는 vCPU를 사용하여 요청 단위 예측](../convert-vcore-to-request-unit.md)에 대해 읽어보세요.
  * 현재 데이터베이스 워크로드에 대한 일반적인 요청 비율을 알고 있는 경우 [Azure Cosmos DB 용량 플래너를 사용하여 요청 단위 예측](estimate-ru-capacity-planner.md)에 대해 읽어보세요.
