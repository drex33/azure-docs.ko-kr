---
title: Azure Cosmos DB Cassandra API에 대한 Azure CLI 샘플
description: Azure Cosmos DB Cassandra API에 대한 Azure CLI 샘플
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: sample
ms.date: 11/15/2021
ms.author: mjbrown
ms.custom: devx-track-azurecli
ms.openlocfilehash: c01b0a61aa8af8332780b352f0a5543754db4497
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/17/2021
ms.locfileid: "132708583"
---
# <a name="azure-cli-samples-for-azure-cosmos-db-cassandra-api"></a>Azure Cosmos DB Cassandra API용 Azure CLI 샘플
[!INCLUDE[appliesto-cassandra-api](../includes/appliesto-cassandra-api.md)]

다음 표는 Azure Cosmos DB의 Azure CLI 샘플 스크립트에 대한 링크를 포함합니다. 오른쪽의 링크를 사용하여 API 관련 샘플로 이동합니다. 일반적인 샘플은 모든 API에서 동일합니다. 모든 Azure Cosmos DB CLI 명령에 대한 참조 페이지는 [Azure CLI 참조](/cli/azure/cosmosdb)에서 제공됩니다. Azure Cosmos DB CLI 스크립트 샘플은 [Azure Cosmos DB CLI GitHub 리포지토리](https://github.com/Azure-Samples/azure-cli-samples/tree/master/cosmosdb)에서도 찾을 수 있습니다.

이러한 샘플에는 Azure CLI 버전 2.12.1 이상이 필요합니다. `az --version`을 실행하여 버전을 찾습니다. 설치 또는 업그레이드해야 하는 경우 [Azure CLI 설치](/cli/azure/install-azure-cli)를 참조하세요.

## <a name="common-samples"></a>일반적인 샘플

이러한 샘플은 모든 Azure Cosmos DB API에 적용됩니다.

|Task | 설명 |
|---|---|
| [영역 추가 또는 장애 조치(failover)](../scripts/cli/common/regions.md?toc=%2fcli%2fazure%2ftoc.json) | 영역을 추가하고, 장애 조치(failover) 우선순위를 변경하고, 수동 장애 조치(failover)를 트리거합니다.|
| [계정 키 및 연결 문자열](../scripts/cli/common/keys.md?toc=%2fcli%2fazure%2ftoc.json) | 계정 키, 읽기 전용 키를 나열하고, 키를 다시 생성하고, 연결 문자열을 나열합니다.|
| [IP 방화벽으로 보안](../scripts/cli/common/ipfirewall.md?toc=%2fcli%2fazure%2ftoc.json)| IP 방화벽이 구성된 Cosmos 계정을 만듭니다.|
| [서비스 엔드포인트로 새 계정 보호](../scripts/cli/common/service-endpoints.md?toc=%2fcli%2fazure%2ftoc.json)| Cosmos 계정을 만들고 서비스 엔드포인트로 보호합니다.|
| [서비스 엔드포인트로 기존 계정 보호](../scripts/cli/common/service-endpoints-ignore-missing-vnet.md?toc=%2fcli%2fazure%2ftoc.json)| 서브넷이 최종적으로 구성될 때 서비스 엔드포인트로 보호할 Cosmos 계정을 업데이트합니다.|
|||

## <a name="cassandra-api-samples"></a>Cassandra API 샘플

|Task | 설명 |
|---|---|
| [Azure Cosmos 계정, 키스페이스 및 테이블 만들기](../scripts/cli/cassandra/create.md?toc=%2fcli%2fazure%2ftoc.json)| Cassandra API에 대한 Azure Cosmos DB 계정, 키스페이스 및 테이블을 만듭니다. |
| [Cassandra API, 키스페이스 및 테이블용 서버리스 Azure Cosmos 계정 만들기](../scripts/cli/cassandra/create.md?toc=%2fcli%2fazure%2ftoc.json)| Cassandra API에 대한 서버리스 Azure Cosmos DB 계정, 키스페이스 및 테이블을 만듭니다. |
| [자동 스케일링되는 Azure Cosmos 계정, 키스페이스 및 테이블 만들기](../scripts/cli/cassandra/autoscale.md?toc=%2fcli%2fazure%2ftoc.json)| 자동 스케일링되는 Cassandra API에 대한 Azure Cosmos DB 계정, 키스페이스 및 테이블을 만듭니다. |
| [처리량 작업](../scripts/cli/cassandra/throughput.md?toc=%2fcli%2fazure%2ftoc.json) | 키스페이스 및 테이블에서 자동 크기 조정과 표준 처리량 간에 읽기, 업데이트 및 마이그레이션합니다.|
| [삭제에서 리소스 잠그기](../scripts/cli/cassandra/lock.md?toc=%2fcli%2fazure%2ftoc.json)| 리소스 잠금을 사용하여 리소스를 삭제하지 않습니다.|
|||
