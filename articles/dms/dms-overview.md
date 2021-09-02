---
title: Azure Database Migration Service란 무엇인가요?
description: 많은 데이터베이스 소스에서 Azure 데이터 플랫폼으로 원활하게 마이그레이션을 제공하는 Azure Database Migration Service 개요입니다.
services: database-migration
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.topic: overview
ms.date: 09/01/2021
ms.openlocfilehash: 68d462a93d891c25602bb305417ddeb64f5f02a1
ms.sourcegitcommit: 851b75d0936bc7c2f8ada72834cb2d15779aeb69
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/31/2021
ms.locfileid: "123317286"
---
# <a name="what-is-azure-database-migration-service"></a>Azure Database Migration Service란 무엇인가요?

Azure Database Migration Service는 가동 중지 시간을 최소화하면서 여러 데이터베이스 소스에서 Azure 데이터 플랫폼으로 원활하게 마이그레이션(온라인 마이그레이션)할 수 있도록 설계된 완전 관리형 서비스입니다.

[!INCLUDE [database-migration-services-sql-mi-sql-vm](../../includes/database-migration-services-sql-mi-sql-vm.md)]

## <a name="migrate-databases-to-azure-with-familiar-tools"></a>익숙한 도구로 Azure에 데이터베이스 마이그레이션

Azure Database Migration Service는 기존 도구 및 서비스의 일부 기능을 통합합니다. 고객에게 포괄적이고 항상 사용 가능한 솔루션을 제공합니다. 서비스는 [Data Migration Assistant](/sql/dma/dma-overview)를 사용하여 마이그레이션을 수행하기 전에 필요한 변경 사항을 안내하는 권장 사항을 제공하는 평가 보고서를 생성합니다. 필요한 모든 수정 작업을 수행하는 것은 사용자의 몫입니다. 마이그레이션 프로세스를 시작할 준비가 되면 Azure Database Migration Service는 필요한 단계를 모두 수행합니다. 프로세스가 Microsoft에서 확인한 모범 사례를 활용한다는 사실에 안심하고 마이그레이션 프로젝트를 시작하고 잊어버릴 수 있습니다. 

> [!NOTE]
> Azure Database Migration Service를 사용하여 온라인 마이그레이션을 수행하려면 프리미엄 가격 책정 계층에 따라 인스턴스를 만들어야 합니다.

## <a name="regional-availability"></a>국가별 가용성

Azure Database Migration Service의 지역별 가용성에 대한 최신 정보는 [지역별로 사용 가능한 제품](https://azure.microsoft.com/global-infrastructure/services/?products=database-migration)을 참조하세요.

## <a name="pricing"></a>가격 책정

Azure Database Migration Service 가격 책정에 대한 최신 정보는 [Azure Database Migration Service 가격 책정](https://azure.microsoft.com/pricing/details/database-migration/)을 참조하세요.

## <a name="next-steps"></a>다음 단계

* [Azure Database Migration Service에서 지원하는 마이그레이션 시나리오 상태](resource-scenario-status.md).
* [Azure Portal을 사용하여 Azure Database Migration Service 인스턴스를 만듭니다](quickstart-create-data-migration-service-portal.md).
* [SQL Server를 Azure SQL Database로 마이그레이션](tutorial-sql-server-to-azure-sql.md).
* [Azure Database Migration Service 사용을 위한 필수 구성 요소 개요](pre-reqs.md).
* [Azure Database Migration Service 사용에 대한 FAQ](faq.yml).
* [데이터 마이그레이션 시나리오에 사용할 수 있는 서비스 및 도구](dms-tools-matrix.md).