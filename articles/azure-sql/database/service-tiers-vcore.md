---
title: vCore 구매 모델
titleSuffix: Azure SQL Database & SQL Managed Instance
description: vCore 구매 모델을 사용하면 컴퓨팅 및 스토리지 리소스를 독립적으로 스케일링하고, 온-프레미스 성능과 일치시키며, Azure SQL Database 및 Azure SQL Managed Instance 가격을 최적화할 수 있습니다.
services: sql-database
ms.service: sql-db-mi
ms.subservice: service-overview
ms.topic: conceptual
author: dimitri-furman
ms.author: dfurman
ms.reviewer: mathoma
ms.date: 05/18/2021
ROBOTS: NOINDEX
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 67e79d8b307de6327f3bacf2bfd57f95080b50d1
ms.sourcegitcommit: 05c8e50a5df87707b6c687c6d4a2133dc1af6583
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/16/2021
ms.locfileid: "132553195"
---
# <a name="vcore-model-overview---azure-sql-database-and-azure-sql-managed-instance"></a>vCore 모델 개요 - Azure SQL Database 및 Azure SQL Managed Instance 
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

Azure SQL Database 및 Azure SQL Managed Instance에서 사용하는 가상 코어(vCore) 구매 모델은 다음과 같은 몇 가지 이점을 제공합니다.

- 워크로드에 대한 컴퓨팅 및 메모리 요구 사항을 더 잘 일치시키기 위한 하드웨어 세대 제어.
- [AHB(Azure 하이브리드 혜택)](../azure-hybrid-benefit.md) 및 [RI(예약 인스턴스)](reserved-capacity-overview.md)의 가격 할인.
- 컴퓨팅을 지원하는 하드웨어 세부 정보의 투명도를 향상시킴으로써 온-프레미스 배포에서의 마이그레이션 계획을 용이하게 함.
- Azure SQL Database 경우 vCore 구매 모델은 DTU 모델보다 더 높은 컴퓨팅, 메모리, I/O 및 저장소 제한을 제공합니다.

vCore 및 DTU 구매 모델 중 선택하는 방법에 대한 자세한 내용은 [vCore 및 DTU 구매 모델 중에서 선택](purchasing-models.md)을 참조하세요.

> [!div class="nextstepaction"]
> [Azure SQL 개선을 위한 설문 조사](https://aka.ms/AzureSQLSurveyNov2021)

## <a name="service-tiers"></a>서비스 계층

다음 문서에서는 각 제품의 vCore 구매 모델에 대한 특정 정보를 제공합니다.

- vCore 모델의 Azure SQL Database 서비스 계층에 대한 자세한 내용은 [vCore 모델 개요 - Azure SQL Database](service-tiers-sql-database-vcore.md)를 참조하세요.
- vCore 모델의 Azure SQL Managed Instance 서비스 계층에 대한 자세한 내용은 [vCore 모델 개요 - Azure SQL Managed Instance](../managed-instance/service-tiers-managed-instance-vcore.md)를 참조하세요.

## <a name="next-steps"></a>다음 단계

시작하려면 다음을 참조하십시오. 
- [Azure Portal을 사용하여 SQL Database 만들기](single-database-create-quickstart.md)
- [Azure Portal을 사용하여 SQL Managed Instance 만들기](../managed-instance/instance-create-quickstart.md)

- 가격 정보는 다음을 참조하세요. 
    - [Azure SQL Database 가격 책정 페이지](https://azure.microsoft.com/pricing/details/sql-database/single/)
    - [Azure SQL Managed Instance 단일 인스턴스 가격 책정 페이지](https://azure.microsoft.com/pricing/details/azure-sql-managed-instance/single/)
    - [Azure SQL Managed Instance 풀 가격 책정 페이지](https://azure.microsoft.com/pricing/details/azure-sql-managed-instance/pools/)
    
범용 및 중요 비즈니스용 서비스 계층에서 사용할 수 있는 특정 컴퓨팅 및 스토리지 크기에 대한 자세한 내용은 다음을 참조하세요.

- [Azure SQL Database를 위한 vCore 기반 리소스 제한](resource-limits-vcore-single-databases.md)
- [풀링된 Azure SQL Database를 위한 vCore 기반 리소스 제한](resource-limits-vcore-elastic-pools.md)
- [Azure SQL Managed Instance를 위한 vCore 기반 리소스 제한](../managed-instance/resource-limits.md)
