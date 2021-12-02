---
title: Azure Data Share에 대한 재해 복구
description: Azure Data Share에 대한 재해 복구
author: jifems
ms.author: jife
ms.service: data-share
ms.topic: how-to
ms.date: 07/30/2020
ms.openlocfilehash: baf968c02043d84d89c1c0affe70a6adf29ad99c
ms.sourcegitcommit: 93c7420c00141af83ed3294923b4826dd4dc6ff2
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/02/2021
ms.locfileid: "133440337"
---
# <a name="disaster-recovery-for-azure-data-share"></a>Azure Data Share에 대한 재해 복구

이 문서에서는 Azure Data Share에 대한 재해 복구 환경을 구성하는 방법을 설명합니다. Azure 데이터 센터 중단은 거의 발생하지 않지만 몇 분에서 몇 시간까지 지속될 수 있습니다. 데이터 센터가 중단되면 데이터 공급자가 공유 중인 데이터를 사용하는 환경이 중단될 수 있습니다. 이 문서에서 설명하는 단계에 따라 데이터 공급자는 데이터 센터 중단 시 데이터 공유를 호스트하는 주 지역의 데이터 소비자와 계속해서 데이터를 공유할 수 있습니다. 

## <a name="achieving-business-continuity-for-azure-data-share"></a>Azure Data Share를 위한 비즈니스 연속성 달성

데이터 센터 중단에 대비하여 데이터 공급자는 보조 지역에 프로비저닝된 데이터 공유 환경을 구축할 수 있습니다. 데이터 센터가 중단되면 원활한 장애 조치(failover)를 위해 측정값을 사용할 수 있습니다. 

데이터 공급자는 보조 Azure Data Share 리소스를 추가 지역에 프로비저닝할 수 있습니다. Data Share 리소스는 기본 Azure Data Share 리소스에 있는 공유 및 데이터 세트를 포함하도록 구성할 수 있습니다. 데이터 공급자는 DR 환경을 구성할 때 또는 나중에(즉, 수동 장애 조치(failover) 단계에서) 보조 공유로 데이터 소비자를 초대할 수 있습니다.

데이터 소비자가 DR을 위해 프로비저닝된 보조 환경에서 활성 공유 구독을 보유하는 경우 장애 조치(failover)의 일부로 스냅샷 일정을 사용하도록 설정할 수 있습니다. DR을 위한 보조 지역을 구독하길 원치 않는 데이터 소비자는 나중에 보조 공유에 초대될 수 있습니다. 

데이터 소비자는 DR을 위해 현재 유휴 상태인 활성 공유 구독을 보유하거나 데이터 공급자가 수동 장애 조치(failover) 절차의 일부로 나중에 데이터 소비자를 초대할 수 있습니다. 

## <a name="related-information"></a>관련 정보

- [비즈니스 연속성 및 재해 복구](../availability-zones/cross-region-replication-azure.md)
- [BCDR 전략에 고가용성 구축](/azure/architecture/solution-ideas/articles/build-high-availability-into-your-bcdr-strategy)

## <a name="next-steps"></a>다음 단계

데이터 공유를 시작하는 방법을 알아보려면 [데이터 공유](share-your-data.md) 자습서로 계속 진행하세요.