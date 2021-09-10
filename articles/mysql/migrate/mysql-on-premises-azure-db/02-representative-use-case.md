---
title: 'MySQL 온-프레미스에서 Azure Database for MySQL로 마이그레이션: 대표 사용 사례'
description: 다음 사용 사례는 MySQL 워크로드를 Azure Database for MySQL로 마이그레이션한 기업의 실제 고객 시나리오를 기반으로 합니다.
ms.service: mysql
ms.subservice: migration-guide
ms.topic: how-to
author: arunkumarthiags
ms.author: arthiaga
ms.reviewer: maghan
ms.custom: ''
ms.date: 06/21/2021
ms.openlocfilehash: 508e27006c96003bd4c2825c9987761f83358f0d
ms.sourcegitcommit: 8b38eff08c8743a095635a1765c9c44358340aa8
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/30/2021
ms.locfileid: "113085077"
---
# <a name="migrate-mysql-on-premises-to-azure-database-for-mysql-representative-use-case"></a>MySQL 온-프레미스에서 Azure Database for MySQL로 마이그레이션: 대표 사용 사례

[!INCLUDE[applies-to-mysql-single-flexible-server](../../includes/applies-to-mysql-single-flexible-server.md)]

## <a name="prerequisites"></a>필수 구성 요소

[소개](01-mysql-migration-guide-intro.md)
## <a name="overview"></a>개요

다음 사용 사례는 MySQL 워크로드를 Azure Database for MySQL로 마이그레이션한 기업의 실제 고객 시나리오를 기반으로 합니다.

WWI(World-Wide Importers) 회사는 캘리포니아 주 샌프란시스코에 기반을 둔, 참신한 상품의 제조업체이자 도매 유통업체입니다. 2002년에 운영을 시작했고 효과적인 B2B(Business to Business) 모델을 개발했으며 만든 제품을 미국 전역의 소매 고객에게 직접 판매했습니다. 고객에는 전문 매장, 슈퍼마켓, 컴퓨터 매장, 관광 명소 상점, 일부 개인이 포함됩니다. 이 B2B 모델을 통해 제품의 유통 시스템이 간소화되므로, 비용을 절감하고 제조 품목에 대해 좀 더 경쟁력 있는 가격을 제공할 수 있습니다. 이들은 WWI를 대신하여 제품을 홍보하는 에이전트 네트워크를 통해 다른 도매업자에게도 판매합니다.

새로운 영역에 진출하기 전에 WWI는 IT 인프라가 성장 예상치를 처리할 수 있는지 확인하고자 합니다. WWI는 현재 본사에서 모든 IT 인프라를 온-프레미스로 호스트하는데, 이러한 리소스를 클라우드로 이전하면 미래 성장이 가능할 것이라고 믿고 있습니다. 결과적으로 그들은 CIO에게 고객 포털 및 관련 데이터 워크로드를 클라우드로 마이그레이션하는 업무를 감독하도록 맡겼습니다.

WWI는 클라우드에서 제공하는 많은 고급 기능을 계속 활용하고자 하며, 데이터베이스 및 관련 워크로드를 Azure로 마이그레이션하는 데 관심이 있습니다. 애플리케이션이나 데이터베이스를 변경할 필요 없이 신속하게 이 작업을 수행하고자 합니다. 처음에는 Java 기반 고객 포털 웹 애플리케이션 및 관련된 MySQL 데이터베이스와 워크로드를 클라우드로 마이그레이션할 계획입니다.

### <a name="migration-goals"></a>마이그레이션 목표

데이터베이스 및 관련된 SQL 워크로드를 클라우드로 마이그레이션하는 주된 목표는 다음과 같습니다.

  - 미사용 데이터 및 전송 중인 데이터에서 전반적인 보안 상태를 개선합니다.

  - HA/DR(고가용성 및 재해 복구) 기능을 개선합니다.

  - 특정 시점 복원 같은 클라우드 기본 기능과 기술을 사용하도록 조직을 포지셔닝합니다.

  - Azure Database for MySQL의 관리 및 성능 최적화 기능을 활용합니다.

  - 비즈니스를 더 많은 지역으로 확장하는 데 사용할 수 있는 스케일링 가능한 플랫폼을 만듭니다.

  - 개인 정보 저장과 관련된 다양한 법적 요구 사항을 더 잘 준수합니다.

WWI는 클라우드 마이그레이션에 대한 모범 사례 지침을 따르도록 팀을 교육하기 위해 [CAF(클라우드 채택 프레임워크)](/azure/cloud-adoption-framework/)를 사용했습니다. 그런 다음, CAF를 상위 수준 마이그레이션 가이드로 사용하여 WWI는 마이그레이션을 세 가지 주요 단계로 사용자 지정했습니다. 마지막으로 성공적인 리프트 앤 시프트 클라우드 마이그레이션을 보장하기 위해 각 단계 내에서 해결해야 할 활동을 정의했습니다.

이러한 단계에는 다음이 포함됩니다.

| 단계 | 속성 | 활동 |
|-------|------|------------|
| 1 | 사전 마이그레이션 | 평가, 계획, 마이그레이션 방법 평가, 애플리케이션 영향, Test Plans, 성능 기준 |
| 2 | 마이그레이션     | 마이그레이션 실행, Test Plans 실행                                                                          |
| 3 | 마이그레이션 후 작업| 비즈니스 연속성, 재해 복구, 관리, 보안, 성능 최적화, 플랫폼 현대화 |

WWI는 다양한 버전(5.5~5.7)으로 MySQL의 여러 인스턴스를 실행하고 있습니다. 최대한 빨리 인스턴스를 최신 버전으로 이동하려고 하지만 최신 버전으로 전환하더라도 애플리케이션의 지속적인 작동을 보장하고자 합니다. 클라우드에서 동일한 버전으로 이동하고 나중에 업그레이드하는 게 편하지만, 두 작업을 한 번에 수행할 수 있다면 그 경로를 선택하려고 합니다.

또한 장애가 발생하는 경우 데이터 워크로드가 안전하고 여러 지역에서 사용 가능하도록 보장하고 사용 가능한 구성 옵션을 살펴보고자 합니다.

WWI는 첫 번째 마이그레이션에서는 간단한 애플리케이션으로 시작한 다음, 이후 단계에서 더 중요 업무용 애플리케이션으로 이동하려고 합니다. 이를 통해 팀은 향후 마이그레이션을 준비하고 계획하는 데 필요한 지식과 경험을 얻게 될 것입니다.  

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [평가](./03-assessment.md)
