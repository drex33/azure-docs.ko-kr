---
title: Azure Purview 재해 복구
description: Azure Purview의 재해 복구 환경을 구성하는 방법을 알아봅니다.
author: sudheerreddykoppula
ms.author: sukoppul
ms.service: purview
ms.topic: how-to
ms.date: 04/23/2021
ms.openlocfilehash: b77d4594b0376c0525ef8a1ec15189d41a3b5193
ms.sourcegitcommit: 93c7420c00141af83ed3294923b4826dd4dc6ff2
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/02/2021
ms.locfileid: "133440223"
---
# <a name="disaster-recovery-for-purview"></a>Purview 재해 복구

이 문서에서는 Azure Purview의 재해 복구 환경을 구성하는 방법을 설명합니다. Azure 데이터 센터 중단은 거의 발생하지 않지만 몇 분에서 몇 시간까지 지속될 수 있습니다. 데이터 센터가 중단되면 데이터 거버넌스에 의존하는 환경이 중단될 수 있습니다. 이 문서에서 자세히 설명하는 단계를 수행하면 Purview 계정의 주 지역에서 데이터 센터가 중단되어도 데이터를 계속 제어할 수 있습니다.

## <a name="achieve-business-continuity-for-azure-purview"></a>Azure Purview의 비즈니스 연속성 달성

Azure Purview 인스턴스의 BCDR(비즈니스 연속성 및 재해 복구)은 기업이 데이터 손실을 보호하고 특히 검사, 카탈로그 및 인사이트 계층에 의한 중단 시 계속 작업할 수 있게 해주는 메커니즘, 정책 및 프로시저를 의미합니다. 이 페이지에서는 Azure Purview의 재해 복구 환경을 구성하는 방법을 설명합니다.

현재 Azure Purview는 자동화된 BCDR을 지원하지 않습니다. 지원이 추가될 때까지 사용자가 백업 및 복원 활동을 수행해야 합니다. 다른 지역에 수동으로 보조 Purview 계정을 웜 대기 인스턴스로 만들 수 있습니다.

다음 단계에서는 수동으로 재해 복구를 수행할 수 있는 방법을 보여줍니다.

1. 특정 지역에 기본 Purview 계정을 만들면 Azure Portal에서 별도의 지역에 보조 Purview 계정을 하나 이상 프로비전해야 합니다. 

2. 기본 Purview 계정에서 수행한 모든 활동을 보조 Purview 계정에서도 수행해야 합니다. 다음 내용이 포함됩니다. 

    - 계정 정보를 유지 관리합니다.
    - 사용자 지정 검사 규칙 세트, 분류 및 분류 규칙을 만들고 및 유지 관리합니다.
    - 원본을 등록하고 검사합니다.
    - 컬렉션과 연결된 원본과 함께 컬렉션을 만들고 유지 관리합니다.
    - 검사하는 동안 자격 증명을 만들고 유지 관리합니다.
    - 데이터 자산을 큐레이팅합니다.
    - 용어집 용어를 만들고 유지 관리합니다.


수동 BCDR 계획을 계획하는 경우 다음 사항에 유의하세요. 

- 기본 및 보조 Purview 계정에 요금이 청구됩니다. 

- 기본 및 보조 Purview 계정을 동일한 Azure Data Factory, Azure Data Share 및 Synapse Analytics 계정(해당되는 경우)에 구성할 수 없습니다.  따라서 보조 Purview 계정에서 Azure Data Factory 및 Azure Data Share의 계보를 확인할 수 없습니다. 기본 Purview 계정과 연결된 Synapse Analytics 작업 영역을 보조 Purview 계정과 연결할 수 없습니다. 이는 현재 제한 사항이며 자동화된 BCDR이 지원될 때 해결될 예정입니다. 

- 통합 런타임은 Purview 계정과 관련됩니다. 따라서 기본 및 보조 Purview 계정에서 검사를 동시에 실행해야 하는 경우 자체 호스팅된 통합 런타임을 여러 개 유지 관리해야 합니다. 이 제한 사항도 자동화된 BCDR이 지원될 때 해결될 예정입니다. 

- 동일한 원본의 기본 및 보조 Purview 계정 모두에서 검사를 동시에 실행하면 원본 성능이 영향을 받을 수 있습니다. 이로 인해 Purview 계정에서 검사 기간이 달라질 수 있습니다.   

## <a name="related-information"></a>관련 정보

- [비즈니스 연속성 및 재해 복구](../availability-zones/cross-region-replication-azure.md)
- [BCDR 전략에 고가용성 구축](/azure/architecture/solution-ideas/articles/build-high-availability-into-your-bcdr-strategy)
- [Azure 상태](https://status.azure.com/status)

## <a name="next-steps"></a>다음 단계

Azure Purview를 시작하려면 [Azure Purview 계정 만들기](create-catalog-portal.md)를 참조하세요.
