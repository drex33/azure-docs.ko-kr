---
title: Azure Cosmos DB로 데이터 글로벌 배포
description: 글로벌 분산형 다중 모델 데이터베이스 서비스인 Azure Cosmos DB에서 글로벌 데이터베이스를 사용한 전 세계적 지역 복제, 다중 영역 쓰기, 장애 조치(Failover), 데이터 복구에 대해 알아봅니다.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 01/06/2021
ms.custom: seo-nov-2020
ms.openlocfilehash: 6bdb167990afeb5c1b6c68185f24a8f930287bed
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "102487902"
---
# <a name="distribute-your-data-globally-with-azure-cosmos-db"></a>Azure Cosmos DB를 사용하여 전역적으로 데이터 배포
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

오늘날의 애플리케이션은 응답성이 뛰어나며 항상 온라인을 유지해야 합니다. 짧은 대기 시간 및 고가용성을 이루려면 이러한 애플리케이션의 인스턴스를 해당 사용자에서 가까운 데이터 센터에 배포해야 합니다. 이러한 애플리케이션은 일반적으로 여러 데이터 센터에 배포되며 글로벌 분산형이라고 합니다. 글로벌 분산형 애플리케이션에는 해당 사용자와 가까이 있는 데이터의 복사본에서 해당 애플리케이션이 작동하도록 설정하려면 전 세계 어디서나 데이터를 투명하게 복제할 수 있는 글로벌 분산형 데이터베이스가 있어야 합니다. 

Azure Cosmos DB는 데이터베이스의 로컬 복제본에서 데이터를 읽고 쓸 수 있도록 하는 전역적으로 분산된 데이터베이스 시스템입니다. Azure Cosmos DB는 데이터를 Cosmos 계정과 연결된 모든 지역에 투명하게 복제합니다. Azure Cosmos DB는 전역적으로 분산되는 데이터베이스 서비스로 짧은 대기 시간, 탄력적인 처리량 확장성, 데이터 일관성에 대해 잘 정의된 의미 체계 및 고가용성을 제공하도록 설계되었습니다. 즉, 전 세계 어디서나 응용 프로그램의 빠른 응답 시간, 응용 프로그램을 항상 온라인으로 유지하고 처리량 및 스토리지를 제한 없이 탄력적으로 확장할 수 있는 확장성을 제공하려면 Azure Cosmos DB를 사용하여 응용 프로그램을 빌드하는 것이 좋습니다.

고객은 데이터베이스가 전역적으로 분산되고 모든 Azure 지역에서 사용 가능하도록 구성할 수 있습니다. 대기 시간을 줄이려면 데이터를 사용자와 가깝게 배치합니다. 필수 지역을 선택하는 작업은 애플리케이션의 글로벌 도달률 및 사용자가 있는 위치에 따라 달라집니다. Cosmos DB는 데이터를 Cosmos 계정과 연결된 모든 지역에 투명하게 복제합니다. 애플리케이션이 로컬로 읽고 쓸 수 있는 전역적으로 분산된 Azure Cosmos 데이터베이스 및 컨테이너의 단일 시스템 이미지를 제공합니다.

Azure Cosmos DB를 사용하면 언제든지 계정과 연결된 지역을 추가하거나 제거할 수 있습니다. 애플리케이션이 지역을 추가하거나 제거하기 위해 일시 중지되거나 재배포될 필요가 없습니다.

:::image type="content" source="./media/distribute-data-globally/deployment-topology.png" alt-text="고가용성 배포 토폴로지" border="false":::

## <a name="key-benefits-of-global-distribution"></a>전역 분산의 주요 혜택

**글로벌 활성-활성 앱 빌드** 새로운 다중 영역 쓰기 복제 프로토콜을 사용하면 모든 영역에서 쓰기 및 읽기를 지원합니다. 다중 영역 쓰기 기능을 사용하면 다음을 수행할 수 있습니다.

- 무제한 탄력적 쓰기 및 읽기 확장성.
- 전 세계의 99.999% 읽기 및 쓰기 가용성
- 99번째 백분위에서 10밀리초 미만으로 제공되는 보장된 읽기 및 쓰기

Azure Cosmos 계정에서 지역을 추가하거나 제거할 때, 응용 프로그램을 다시 배포하거나 중지할 필요가 없으며, 높은 가용성을 계속 유지할 수 있습니다.

**응답성이 뛰어난 앱 빌드** 응용 프로그램은 데이테베이스에 선택한 모든 영역에 대해 근 실시간 읽기 및 쓰기를 수행할 수 있습니다. Azure Cosmos DB은 사용자가 선택한 수준에 대해 일관성 수준을 보장하는 영역 간의 데이터 복제를 내부적으로 처리합니다.

**고가용성 앱 빌드** 다중 영역에서 데이터베이스를 실행하면 데이터베이스 가용성이 증가 됩니다. 한 지역을 사용할 수 없게 되면 다른 지역에서 애플리케이션 요청을 자동으로 처리합니다. Azure Cosmos DB는 다중 지역 데이터베이스에 대해 99.999% 읽기 및 쓰기 가용성을 제공합니다.

**지역 가동 중단 동안 비즈니스 연속성 유지** Azure Cosmos DB는 지역 가동 중단 시 [자동 장애 조치(failover)](how-to-manage-database-account.md#automatic-failover)를 지원합니다. 지역 가동 중단 시 Azure Cosmos DB는 해당 대기 시간, 가용성, 일관성 및 처리량 SLA를 계속 유지합니다. 전체 응용 프로그램을 항상 사용할 수 있도록 하기 위해 Cosmos DB는 영역 가동 중단을 시뮬레이션하는 수동 장애 조치(failover) API를 제공합니다. 이 API를 사용하여 정기적인 비즈니스 연속성 훈련을 수행할 수 있습니다.

**전역적으로 읽기 및 쓰기 처리량 조정** 모든 영역을 쓰기 가능하도록 설정하고 전 세계에서 탄력적으로 읽기 및 쓰기를 스케일링할 수 있습니다. 응용 프로그램에서 Azure Cosmos 데이터베이스 또는 컨테이너에 대해 구성하는 처리량은 Azure Cosmos 계정과 연결된 모든 영역에서 프로비전됩니다. 프로비전된 처리량은 [재정적으로 지원되는 SLA](https://azure.microsoft.com/support/legal/sla/cosmos-db/v1_3/)에 의해 보장됩니다.

**잘 정의된 다중 일관성 모델에서 선택** Azure Cosmos DB 복제 프로토콜은 5개의 잘 정의되고, 실용적이며, 직관적인 일관성 모델을 제공합니다. 각 모델에서는 일관성과 성능이 절충되었습니다. 이러한 일관성 모델을 사용하여 전역적으로 분산된 애플리케이션을 쉽게 빌드합니다.

## <a name="next-steps"></a><a id="Next Steps"></a>다음 단계

다음 문서에서 전역 분산에 대해 자세히 알아보세요.

* [글로벌 배포 - 내부 살펴보기](global-dist-under-the-hood.md)
* [응용 프로그램에서 다중 영역 쓰기를 구성하는 방법](how-to-multi-master.md)
* [클라이언트 멀티 호밍 구성](how-to-manage-database-account.md#configure-multiple-write-regions)
* [Azure Cosmos DB 계정에서 지역 추가 또는 제거](how-to-manage-database-account.md#addremove-regions-from-your-database-account)
* [SQL API 계정에 대한 사용자 지정 충돌 해결 정책 만들기](how-to-manage-conflicts.md#create-a-custom-conflict-resolution-policy)
* [Cosmos DB의 프로그래밍 가능 일관성 모델](consistency-levels.md)
* [애플리케이션에 대한 올바른 일관성 수준 선택](./consistency-levels.md)
* [Azure Cosmos DB API 간 일관성 수준](./consistency-levels.md)
* [다양한 일관성 수준의 가용성 및 성능 절충](./consistency-levels.md)