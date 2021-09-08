---
title: Azure Cosmos DB에서 개발 및 테스트 최적화
description: 이 문서에서는 Azure Cosmos DB에서 서비스의 개발 및 테스트를 위해 여러 가지 옵션을 무료로 제공하는 방법을 설명합니다.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 08/26/2021
ms.openlocfilehash: 60f586e3f2ead0bde743f75d7810bf2d43c737af
ms.sourcegitcommit: 03f0db2e8d91219cf88852c1e500ae86552d8249
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2021
ms.locfileid: "123028618"
---
# <a name="optimize-development-and-testing-cost-in-azure-cosmos-db"></a>Azure Cosmos DB에서 개발 및 테스트 비용 최적화
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

이 문서에서는 무료 개발 및 테스트를 위해 Azure Cosmos DB를 사용하는 다양한 옵션과 개발 또는 테스트 계정의 비용을 최적화하는 기술에 대해 설명합니다.

## <a name="azure-cosmos-db-emulator-locally-downloadable-version"></a>Azure Cosmos DB 에뮬레이터(로컬로 다운로드 가능한 버전)

[Azure Cosmos DB 에뮬레이터](local-emulator.md)는 Azure Cosmos DB 클라우드 서비스를 모방하는, 로컬로 다운로드 가능한 버전입니다. 네트워크 연결이 없어도 비용 발생 없이 Azure Cosmos DB API를 사용하는 코드를 작성하고 테스트할 수 있습니다. Azure Cosmos DB 에뮬레이터는 클라우드 서비스와 유사한, 개발 목적의 로컬 환경을 제공합니다. Azure 구독을 만들지 않고 로컬에서 애플리케이션을 개발하고 테스트할 수 있습니다. 애플리케이션을 클라우드에 배포할 준비가 되면 클라우드의 Azure Cosmos DB 엔드포인트에 연결하도록 연결 문자열을 업데이트하면 됩니다. 다른 수정은 필요하지 않습니다. 테스트 실행을 위해 Azure DevOps에서 [Azure Cosmos DB 에뮬레이터 빌드 작업을 사용하여 CI/CD 파이프라인을 설정](tutorial-setup-ci-cd.md)할 수도 있습니다. [Azure Cosmos DB 에뮬레이터](local-emulator.md) 문서를 방문하여 시작할 수 있습니다.

## <a name="azure-cosmos-db-free-tier"></a>Azure Cosmos DB 체험 계층

Azure Cosmos DB 체험 계층을 사용하면 쉽게 시작하고, 애플리케이션을 개발 및 테스트하거나, 소규모 프로덕션 워크로드를 무료로 실행할 수도 있습니다. 계정에서 무료 계층을 사용하는 경우 계정의 첫 1000RU/s 및 25GB의 스토리지를 무료로 얻을 수 있습니다.

무료 계층은 계정 수명 동안 무기한으로 지속되며 무제한 스토리지 및 처리량(RU/s), SLA, 고가용성, 모든 Azure 지역의 턴키 글로벌 배포 등을 포함하여 일반 Azure Cosmos DB 계정의 모든 [이점과 기능](introduction.md#key-benefits)을 제공합니다. Azure Portal, CLI, PowerShell 및 Resource Manager 템플릿을 사용하여 무료 계층 계정을 만들 수 있습니다. 자세히 알아보려면 [무료 계층 계정 생성](free-tier.md) 방법 문서 및 [가격 책정 페이지](https://azure.microsoft.com/pricing/details/cosmos-db/)를 참조하세요.

## <a name="try-azure-cosmos-db-for-free"></a>무료로 Azure Cosmos DB 사용해 보기

[Azure Cosmos DB 체험해 보기](https://azure.microsoft.com/try/cosmosdb/)는 Azure 계정에 등록하거나 신용 카드를 사용하지 않고도 클라우드에서 Azure Cosmos DB를 사용해 볼 수 있는 무료 환경입니다. Azure Cosmos DB 체험 계정은 제한된 시간(현재 30일) 동안 사용할 수 있습니다. 언제든지 계정을 갱신할 수 있습니다. Azure Cosmos DB 계정을 사용하면 쉽게 Azure Cosmos DB를 평가하고, 애플리케이션을 빌드 및 테스트하거나 빠른 시작 또는 자습서를 사용할 수 있습니다. 데모를 만들고 유닛 테스트를 수행하거나 여러 지역 계정을 만들고 비용을 들이지 않고 앱을 실행할 수도 있습니다. Azure Cosmos DB 체험 계정에는 최대 25개의 컨테이너와 20,000RU/s의 처리량을 포함하는 하나의 공유 처리량 데이터베이스 또는 최대 5,000RU/s를 포함하는 하나의 컨테이너가 있을 수 있습니다. 시작하려면 [Azure Cosmos DB 체험해 보기](https://azure.microsoft.com/try/cosmosdb/)를 참조하세요.

## <a name="azure-free-account"></a>Azure 무료 체험 계정

Azure Cosmos DB는 Azure 크레딧과 리소스를 일정 기간 동안 무료로 제공하는 [Azure 체험 계정](https://azure.microsoft.com/free)에 포함됩니다. Azure Cosmos DB와 관련해서 이 체험 계정은 1년 동안 25GB의 스토리지와 400RU의 프로비전된 처리량을 제공합니다. 이 환경을 통해 개발자는 비용 없이 Azure Cosmos DB의 기능을 쉽게 테스트하거나 다른 Azure 서비스와 통합할 수 있습니다. Azure 체험 계정과 함께 처음 30일 동안 사용할 수 있는 200달러 크레딧을 받게 됩니다. 서비스 사용이 시작된 후에도 업그레이드를 선택하기 전에는 요금이 청구되지 않습니다. 시작하려면 [Azure 체험 계정](https://azure.microsoft.com/free) 페이지를 방문하세요.

## <a name="azure-cosmos-db-serverless"></a>Azure Cosmos DB 서버리스

[Azure Cosmos DB 서버리스](serverless.md)를 통해 데이터베이스 작업에서 사용한 요청 단위 및 데이터에서 사용하는 스토리지에 대해서만 요금이 부과되는 사용량 기반 방식으로 Azure Cosmos 계정을 사용할 수 있습니다. 서버리스 모드로 Azure Cosmos DB를 사용하는 경우에는 부과되는 최소 요금이 없습니다. 따라서 프로비전된 용량의 개념이 제거되기 때문에 특히 데이터베이스가 대부분의 시간 동안 유휴 상태인 경우 개발 또는 테스트 작업에 가장 적합합니다.

## <a name="use-shared-throughput-databases"></a>공유 처리량 데이터베이스 사용

[공유 처리량 데이터베이스](set-throughput.md#set-throughput-on-a-database)에서 데이터베이스 내의 모든 컨테이너는 데이터베이스의 프로비전된 처리량(RU/s)을 공유합니다. 예를 들어 400RU/s로 데이터베이스를 프로비전하고 4개의 컨테이너를 포함하는 경우 4개의 컨테이너 모두 400RU/s를 공유합니다. 각 컨테이너에 자주 액세스하지 않을 수 있으므로 최소 400RU/s보다 낮은 값이 필요한 개발 또는 테스트 환경에서 컨테이너를 공유 처리량 데이터베이스에 배치하면 비용을 최적화할 수 있습니다.

예를 들어 개발 또는 테스트 계정에 4개의 컨테이너가 있다고 가정합니다. 전용 처리량(최소 400RU/s)을 가진 4개의 컨테이너를 만드는 경우 총 RU/s는 1600RU/s입니다. 반면 공유 처리량 데이터베이스(최소 400RU/s)를 만들고 컨테이너를 배치하면 총 RU/s는 단 400RU/s입니다. 일반적으로 공유 처리량 데이터베이스는 개별 컨테이너에 대한 보장된 처리량이 필요하지 않은 시나리오에 적합합니다.  [공유 처리량 데이터베이스](set-throughput.md#set-throughput-on-a-database)에 대해 자세히 알아보세요.

## <a name="next-steps"></a>다음 단계

다음 문서를 사용하여 에뮬레이터 또는 Azure Cosmos DB 체험 계정 사용을 시작할 수 있습니다.

* [Azure Cosmos DB 요금 청구 이해](understand-your-bill.md)에 대한 자세한 정보
* [Azure Cosmos DB 서버리스](serverless.md)에 대한 자세한 정보
* [처리량 비용 최적화](optimize-cost-throughput.md)에 대한 자세한 정보
* [스토리지 비용 최적화](optimize-cost-storage.md)에 대한 자세한 정보
* [읽기 및 쓰기 비용 최적화](optimize-cost-reads-writes.md)에 대한 자세한 정보
* [쿼리 비용 최적화](./optimize-cost-reads-writes.md)에 대한 자세한 정보
* [다중 지역 Azure Cosmos 계정 비용 최적화](optimize-cost-regions.md)에 대한 자세한 정보
* Azure Cosmos DB로 마이그레이션하기 위한 용량 계획을 수행하려고 하시나요? 용량 계획을 위해 기존 데이터베이스 클러스터에 대한 정보를 사용할 수 있습니다.
    * 기존 데이터베이스 클러스터의 vCore 및 서버 수만 알고 있는 경우 [vCore 또는 vCPU를 사용하여 요청 단위 예측](convert-vcore-to-request-unit.md)에 대해 읽어보세요. 
    * 현재 데이터베이스 워크로드에 대한 일반적인 요청 비율을 알고 있는 경우 [Azure Cosmos DB 용량 계획 도구를 사용하여 요청 단위 예측](estimate-ru-with-capacity-planner.md)에 대해 읽어보세요.