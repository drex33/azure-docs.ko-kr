---
title: Azure Cosmos DB의 가격 책정 모델
description: 이 문서에서는 Azure Cosmos DB의 가격 책정 모델과 이 모델로 비용 관리 및 비용 계획을 간소화하는 방법을 설명합니다.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 08/26/2021
ms.openlocfilehash: 407e5935771b6b6c0b351d75d0a0d7c734eaa556
ms.sourcegitcommit: 03f0db2e8d91219cf88852c1e500ae86552d8249
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2021
ms.locfileid: "123038028"
---
# <a name="pricing-model-in-azure-cosmos-db"></a>Azure Cosmos DB의 가격 책정 모델
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Azure Cosmos DB의 가격 책정 모델은 비용 관리 및 계획을 간소화합니다. Azure Cosmos DB를 사용하면 데이터베이스를 대상으로 수행한 작업과 데이터에서 사용하는 스토리지에 대한 비용만 지불하게 됩니다.

- **데이터베이스 작업**: 데이터베이스 작업에 대해 부과되는 방법은 사용하는 Azure Cosmos 계정 유형에 따라 달라집니다.

  - **프로비전된 처리량**: [프로비전된 처리량](set-throughput.md)(예약된 처리량이라고도 함)은 어떤 규모에서도 뛰어난 성능을 보장합니다. 초당 [요청 단위](request-units.md)(RU/s)에 필요한 처리량을 지정하면 Azure Cosmos DB에서 구성된 처리량을 제공하는 데 필요한 리소스를 전담 투입합니다. [데이터베이스 또는 컨테이너에 대해 처리량을 프로비전](set-throughput.md)할 수 있습니다. 워크로드 필요에 따라 언제든 처리량을 높이거나 낮출 수 있으며 [자동 크기 조정](provision-throughput-autoscale.md)을 사용할 수도 있습니다(하지만 SLA를 보장하기 위해 데이터베이스 또는 컨테이너에 필요한 최소 처리량이 지정되어 있습니다). 지정된 시간의 최대 프로비전된 처리량을 기준으로 시간별로 요금이 청구됩니다.

   > [!NOTE]
   > 프로비전된 처리량 모델은 컨테이너 또는 데이터베이스에 리소스를 전담으로 투입하므로 워크로드를 실행하지 않더라도 프로비전된 처리량에 대해 비용이 부과됩니다.

  - **서버리스**: [서버리스](serverless.md) 모드에서는 Azure Cosmos 계정에 리소스를 만들 때 처리량을 프로비전할 필요가 없습니다. 청구 기간이 종료되면 데이터베이스 작업에 사용된 요청 단위 수에 대한 요금이 청구됩니다.

- **스토리지**: 지정된 시간 동안 데이터 및 인덱스에 사용된 총 스토리지 양(GB)에 대해 정액 요금이 부과됩니다. 스토리지는 소비량 단위로 청구되므로 스토리지를 미리 예약하지 않아도 됩니다. 사용한 스토리지에 대해서만 요금이 청구됩니다.

Azure Cosmos DB의 가격 책정 모델은 모든 API에서 일치합니다. 자세한 내용은 [Azure Cosmos DB 가격 책정 페이지](https://azure.microsoft.com/pricing/details/cosmos-db/), [Azure Cosmos DB 청구서를 이해](understand-your-bill.md)와 [Azure Cosmos DB 가격 책정 모델이 고객에게 경제적인 이유](total-cost-ownership.md)를 참조하세요.

Azure Cosmos DB 계정을 미국의 비정부 지역에 배포하는 경우 프로비전된 처리량 모드의 데이터베이스 및 컨테이너 기반 처리량에 대한 최소 가격이 존재합니다. 서버리스 모드에서는 최소 가격이 존재하지 않습니다. 가격은 사용 중인 지역에 따라 다릅니다. 최신 가격 책정 정보는 [Azure Cosmos DB 가격 책정 페이지](https://azure.microsoft.com/pricing/details/cosmos-db/)를 참조하세요.

## <a name="try-azure-cosmos-db-for-free"></a>무료로 Azure Cosmos DB 사용해 보기

Azure Cosmos DB는 개발자를 위해 다양한 옵션을 무료로 제공합니다. 이러한 옵션에는 다음이 포함됩니다.

* **Azure Cosmos DB 체험 계층**: Azure Cosmos DB 체험 계층을 사용하면 쉽게 시작하고, 애플리케이션을 개발 및 테스트하거나, 소규모 프로덕션 워크로드를 무료로 실행할 수도 있습니다. 계정에서 무료 계층을 사용하는 경우 계정의 수명이 유지되는 동안 계정에서 첫 1000RU/s 및 25GB의 스토리지를 무료로 얻을 수 있습니다. Azure 구독당 최대 1개의 체험 계층 계정을 사용할 수 있으며 계정을 만들 때 옵트인해야 합니다. 자세히 알아보려면 [체험 계층 계정을 생성](free-tier.md)하는 방법 문서를 참조하세요.

* **Azure 무료 계정**: Azure는 처음 30일 동안 200달러의 Azure 크레딧과 12개월 동안 무료 서비스라는 제한된 수량을 제공하는 [무료 계층](https://azure.microsoft.com/free/)을 제공합니다. 자세한 내용은 [Azure 체험 계정](../cost-management-billing/manage/avoid-charges-free-account.md)을 참조하세요. Azure Cosmos DB의 Azure 체험 계정의 일부로 제공됩니다. Azure Cosmos DB와 관련해서 이 체험 계정은 1년 동안 25GB의 스토리지와 400RU/s의 프로비전된 처리량을 제공합니다.

* **Azure Cosmos DB 평가판 사용하기**: Azure Cosmos DB는 체험 계정이 있을 때 Azure Cosmos DB를 사용해 볼 수 있는 시간 제한 체험을 제공합니다. 빠른 시작 및 자습서를 사용하여 Azure Cosmos DB 계정을 만들고, 데이터베이스 및 컬렉션을 만들고, 애플리케이션 예제를 실행할 수 있습니다. Azure 계정에 가입하거나 신용 카드를 사용하지 않고 애플리케이션 예제를 실행할 수 있습니다. [Try Azure Cosmos DB for free](https://azure.microsoft.com/try/cosmosdb/)(Azure Cosmos DB 평가판 사용하기)를 통해 1개월간 횟수 제한 없이 계정을 갱신하면서 Azure Cosmos DB를 체험해 볼 수 있습니다.

* **Azure Cosmos DB 에뮬레이터**: Azure Cosmos DB 에뮬레이터는 개발 목적으로 Azure Cosmos DB 서비스를 에뮬레이트하는 로컬 환경을 제공합니다. 안정적인 에뮬레이터가 무료로 클라우드 서비스에 제공됩니다. Azure Cosmos DB 에뮬레이터를 사용하면 Azure 구독을 구입하거나 비용을 발생시키지 않고도 로컬에서 애플리케이션을 테스트할 수 있습니다. 프로덕션으로 전환하기 전에 로컬로 에뮬레이터를 사용하여 애플리케이션을 개발할 수 있습니다. 에뮬레이터를 사용해본 결과, 애플리케이션의 기능이 만족스러우면 클라우드의 Azure Cosmos DB 계정 사용으로 전환하면 비용을 크게 절약할 수 있습니다. 에뮬레이터에 대한 자세한 내용은 [개발 및 테스트에 Azure Cosmos DB 사용](local-emulator.md)을 참조하세요.

## <a name="pricing-with-reserved-capacity"></a>예약 용량이 있는 가격

Azure Cosmos DB [예약 용량](cosmos-db-reserved-capacity.md)을 사용하면 1년 또는 3년 동안 Azure Cosmos DB 리소스 비용을 선불로 지불하여 프로비전된 처리량을 사용할 때 경비를 절약할 수 있습니다. 1년 또는 3년 간의 사전 약정을 비용을 획기적으로 절감하고 정상 가격에 비해 20~65%의 할인 혜택을 받을 수 있습니다. Azure Cosmos DB 예약 용량을 사용하면 1년 또는 3년 기간 동안 프로비전된 처리량(RU/s) 비용을 선불로 지불하고 프로비전된 처리량에 대해 할인을 받아 비용을 절약할 수 있습니다. 

예약 용량은 청구 할인을 제공하며, Azure Cosmos DB 리소스의 런타임 상태에는 영향을 주지 않습니다. 예약 용량은 MongoDB, Cassandra, SQL, Gremlin, Azure Tables를 포함하는 모든 API와 전 세계 모든 지역에서 일관되게 사용할 수 있습니다. [예약 용량을 통해 Azure Cosmos DB 리소스에 대한 선불 결제](cosmos-db-reserved-capacity.md) 문서에서 예약 용량에 대해 자세히 알아보고 [Azure Portal](https://portal.azure.com/)에서 예약 용량을 구입할 수 있습니다.

## <a name="next-steps"></a>다음 단계

다음 문서에서는 Azure Cosmos DB 리소스에 대한 비용을 최적화하는 방법에 대한 추가 정보를 확인할 수 있습니다.

* Azure Cosmos DB로 마이그레이션하기 위한 용량 계획을 수행하려고 하시나요? 용량 계획을 위해 기존 데이터베이스 클러스터에 대한 정보를 사용할 수 있습니다.
    * 기존 데이터베이스 클러스터의 vCore 및 서버 수만 알고 있는 경우 [vCore 또는 vCPU를 사용하여 요청 단위 예측](convert-vcore-to-request-unit.md)에 대해 읽어보세요. 
    * 현재 데이터베이스 워크로드에 대한 일반적인 요청 비율을 알고 있는 경우 [Azure Cosmos DB 용량 계획 도구를 사용하여 요청 단위 예측](estimate-ru-with-capacity-planner.md)에 대해 읽어보세요.
* [개발 및 테스트용으로 최적화](optimize-dev-test.md)에 대한 자세한 정보
* [Azure Cosmos DB 요금 청구 이해](understand-your-bill.md)에 대한 자세한 정보
* [처리량 비용 최적화](optimize-cost-throughput.md)에 대한 자세한 정보
* [스토리지 비용 최적화](optimize-cost-storage.md)에 대한 자세한 정보
* [읽기 및 쓰기 비용 최적화](optimize-cost-reads-writes.md)에 대한 자세한 정보
* [쿼리 비용 최적화](./optimize-cost-reads-writes.md)에 대한 자세한 정보
* [다중 지역 Cosmos 계정 비용 최적화](optimize-cost-regions.md)에 대한 자세한 정보
* [Azure Cosmos DB 예약 용량](cosmos-db-reserved-capacity.md)에 대한 자세한 정보
* [Azure Cosmos DB 에뮬레이터](local-emulator.md)에 대한 자세한 정보