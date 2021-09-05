---
title: 할당량 증가 요청
description: 이 페이지에서는 Azure SQL Database 및 Azure SQL Managed Instance에 대한 할당량을 늘리는 지원 요청을 만드는 방법을 설명합니다.
services: sql-database
ms.service: sql-db-mi
ms.subservice: deployment-configuration
ms.topic: how-to
author: sachinpMSFT
ms.author: sachinp
ms.reviewer: mathoma
ms.date: 06/04/2020
ms.openlocfilehash: a5530becdcbe150ddc7ab2608bdf964ac269ed99
ms.sourcegitcommit: c385af80989f6555ef3dadc17117a78764f83963
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/04/2021
ms.locfileid: "111409022"
---
# <a name="request-quota-increases-for-azure-sql-database-and-sql-managed-instance"></a>Azure SQL Database 및 SQL Managed Instance에 대한 할당량 증가 요청
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

이 문서에서는 Azure SQL Database 및 Azure SQL Managed Instance에 대한 할당량 증가를 요청하는 방법을 설명합니다. 또한 지역에 대한 구독 액세스를 사용하는 방법과 지역에서 특정 하드웨어를 사용하도록 요청하는 방법에 대해서도 설명합니다.

## <a name="create-a-new-support-request"></a><a id="newquota"></a> 새 지원 요청 만들기

Azure Portal에서 SQL Database에 대한 새 지원 요청을 만들려면 다음 단계를 수행합니다.

1. [Azure Portal](https://portal.azure.com) 메뉴에서 **도움말 + 지원** 을 선택합니다.

   ![도움말 + 지원 링크](./media/quota-increase-request/help-plus-support.png)

1. **도움말 + 지원** 에서 **새 지원 요청** 을 선택합니다.

    ![새 지원 요청 만들기](./media/quota-increase-request/new-support-request.png)

1. **문제 유형** 의 경우 **서비스 및 구독 제한(할당량)** 를 선택합니다.

   ![문제 유형 선택](./media/quota-increase-request/select-quota-issue-type.png)

1. **구독** 의 경우 할당량을 늘릴 구독을 선택합니다.

   ![할당량을 늘릴 구독을 선택](./media/quota-increase-request/select-subscription-support-request.png)

1. **할당량 유형** 의 경우 할당량 유형 중 하나를 선택합니다.

   - 단일 데이터베이스 및 탄력적 풀 할당량에 대한 **SQL Database**.
   - 관리형 인스턴스에 대한 **SQL Database Managed Instance**.

   그런 다음, **Next: 솔루션 >>** 을 선택합니다.

   ![할당량 유형 선택](./media/quota-increase-request/select-quota-type.png)

1. **세부 정보** 창에서 **세부 정보 입력** 을 선택하여 추가 정보를 입력합니다.

   ![세부 정보 입력 링크](./media/quota-increase-request/provide-details-link.png)

**세부 정보 입력** 을 선택하면 정보를 추가할 수 있는 **할당량 세부 정보** 창이 표시됩니다. 다음 섹션에서는 **SQL Database** 및 **SQL Managed Instance** 할당량 유형의 다양한 옵션을 설명합니다.

## <a name="sql-database-quota-types"></a><a id="sqldbquota"></a>SQL Database 할당량 유형

다음 섹션에서는 **SQL Database** 할당량 유형의 할당량 증가 옵션에 대해 설명합니다.

- 서버당 DTU(데이터베이스 트랜잭션 단위)
- 구독당 서버
- 구독 또는 특정 하드웨어에 대한 지역 액세스

### <a name="database-transaction-units-dtus-per-server"></a>서버당 DTU(데이터베이스 트랜잭션 단위)

서버당 DTU의 증가를 요청하려면 다음 단계를 수행합니다.

1. **서버당 DTU(데이터베이스 트랜잭션 단위)** 할당량 유형을 선택합니다.

1. **리소스** 목록에서 대상으로 할 리소스를 선택합니다.

1. **새로운 할당량** 필드에 요청하는 새 DTU 한도를 입력합니다.

   ![DTU 할당량 세부 정보](./media/quota-increase-request/quota-details-dtus.png)

자세한 내용은 [DTU 구매 모델을 사용하는 단일 데이터베이스에 대한 리소스 한도](resource-limits-dtu-single-databases.md)와 [DTU 구매 모델을 사용하는 탄력적 풀에 대한 리소스 한도](resource-limits-dtu-elastic-pools.md)를 참조하세요.

### <a name="servers-per-subscription"></a>구독당 서버

구독당 서버 수의 증가를 요청하려면 다음 단계를 사용합니다.

1. **구독당 서버** 할당량 유형을 선택합니다.

1. **위치** 목록에서 사용할 Azure 지역을 선택합니다. 할당량은 각 지역의 구독당입니다.

1. **새 할당량** 필드에 해당 지역의 최대 서버 수에 대한 요청을 입력합니다.

   ![서버 할당량 세부 정보](./media/quota-increase-request/quota-details-servers.png)

자세한 내용은 [SQL Database 리소스 한도 및 리소스 거버넌스](resource-limits-logical-server.md)를 참조하세요.

### <a name="enable-subscription-access-to-a-region"></a><a id="region"></a>지역에 대한 구독 액세스 사용

일부 제품 유형은 모든 지역에서 사용할 수 없습니다. 다음과 같은 오류가 표시될 수 있습니다.

`Your subscription does not have access to create a server in the selected region. For the latest information about region availability for your subscription, go to aka.ms/sqlcapacity. Please try another region or create a support ticket to request access.`

구독이 특정 지역에서 액세스해야 하는 경우 **지역 액세스** 옵션을 선택합니다. 요청에서 지역에 대해 사용하도록 설정할 제품 및 SKU 세부 정보를 지정합니다. 제품 및 SKU 옵션을 탐색하려면 [Azure SQL Database 가격 책정](https://azure.microsoft.com/pricing/details/sql-database/single/)을 참조하세요.

1. **지역 액세스** 할당량 유형을 선택합니다.

1. **위치 선택하기** 목록에서 사용할 Azure 지역을 선택합니다. 할당량은 각 지역의 구독당입니다.

1. **구매 모델** 과 **예상 소비량** 세부 정보를 입력합니다.

   ![지역 액세스 요청](./media/quota-increase-request/quota-request.png)

### <a name="request-enabling-specific-hardware-in-a-region"></a>지역에서 특정 하드웨어를 사용하도록 요청

사용하려는 하드웨어 세대를 해당 지역에서 사용할 수 없는 경우 다음 단계를 수행하여 요청할 수 있습니다. 하드웨어 세대 및 지역별 가용성에 대한 자세한 내용은 [SQL Database용 하드웨어 세대](./service-tiers-sql-database-vcore.md#hardware-generations) 또는 [SQL Managed Instance용 하드웨어 세대](../managed-instance/service-tiers-managed-instance-vcore.md#hardware-generations)를 참조하세요.

1. **다른 할당량 요청** 할당량 유형을 선택합니다.

1. **설명** 필드에 하드웨어 생성의 이름과 하드웨어가 필요한 지역의 이름을 포함하여 요청을 기술합니다.

   ![새 지역에 하드웨어 요청](./media/quota-increase-request/hardware-in-new-region.png)

## <a name="submit-your-request"></a>요청 제출

마지막 단계는 SQL Database 할당량 요청에 대한 나머지 세부 정보를 입력하는 것입니다. 그런 다음, **Next: 검토 + 만들기>>** 를 선택하고 요청 세부 정보를 검토한 후 **만들기** 를 클릭하여 요청을 제출합니다.

## <a name="next-steps"></a>다음 단계

요청을 제출하면 검토됩니다. 양식에 제공한 정보를 바탕으로 답변을 받게 됩니다.

Azure 한도에 대한 자세한 내용은 [Azure 구독 및 서비스 한도, 할당량, 제약 조건](../../azure-resource-manager/management/azure-subscription-service-limits.md)을 참조하세요.
