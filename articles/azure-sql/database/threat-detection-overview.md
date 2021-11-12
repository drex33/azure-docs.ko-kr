---
title: Advanced Threat Protection
titleSuffix: Azure SQL Database, SQL Managed Instance, & Azure Synapse Analytics
description: Advanced Threat Protection은 Azure SQL Database, Azure SQL Managed Instance, Azure Synapse Analytics에서 잠재적 보안 위협을 나타내는 비정상적인 데이터베이스 활동을 탐지합니다.
services: sql-database
ms.service: sql-db-mi
ms.subservice: security
ms.custom: sqldbrb=2
ms.topic: conceptual
author: davidtrigano
ms.author: datrigan
ms.reviewer: vanto, sstein
ms.date: 06/09/2021
tags: azure-synapse
ms.openlocfilehash: 5377af3720f8666f23bfc956c86fdc5df667b432
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132301858"
---
# <a name="sql-advanced-threat-protection"></a>SQL Advanced Threat Protection
[!INCLUDE[appliesto-sqldb-sqlmi-asa](../includes/appliesto-sqldb-sqlmi-asa.md)]:::image type="icon" source="../media/applies-to/yes.png" border="false":::Azure VM :::image type="icon" source="../media/applies-to/yes.png" border="false"::: 에서 SQL Server Azure Arc-사용 SQL Server

[azure Virtual Machines](../virtual-machines/windows/sql-server-on-azure-vm-iaas-what-is-overview.md) 및 azure [Arc 사용 SQL Server](/sql/sql-server/azure-arc/overview) 에 대 한 [Azure SQL Database](sql-database-paas-overview.md), [azure SQL Managed Instance](../managed-instance/sql-managed-instance-paas-overview.md), [azure Synapse Analytics](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md), SQL Server에 대 한 Advanced Threat Protection은 비정상적인 활동을 감지 하 여 데이터베이스에 액세스 하거나 악용 하려는 비정상적인 시도를 감지 합니다.

advanced Threat Protection은 고급 SQL 보안 기능을 위한 통합 패키지인 [SQL 제공 용 Microsoft Defender](../../security-center/defender-for-sql-introduction.md) 의 일부입니다. 고급 위협 방지는 SQL 용 중앙 Microsoft Defender 포털을 통해 액세스 하 고 관리할 수 있습니다.

## <a name="overview"></a>개요

Advanced Threat Protection은 비정상적인 활동에 대한 보안 경고를 제공하여 잠재적인 위협이 발생하면 고객이 이를 감지하고 대응할 수 있도록 하는 새로운 차원의 보안을 제공합니다. 사용자는 의심스러운 데이터베이스 활동, 잠재적 취약성 및 SQL 삽입 공격뿐만 아니라 비정상적인 데이터베이스 액세스 및 쿼리 패턴에 대한 경고도 받습니다. Advanced Threat Protection은 의심 스러운 활동의 세부 정보를 포함 하 고 위협을 조사 하 고 완화 하는 방법에 대 한 권장 조치를 포함 하는 [클라우드 용 Microsoft Defender](https://azure.microsoft.com/services/security-center/)와 경고를 통합 합니다. Advanced Threat Protection은 보안 전문가가 되거나 고급 보안 모니터링 시스템을 관리할 필요 없이 데이터베이스에 대한 잠재적인 위협에 간단하게 대처할 수 있도록 합니다.

조사 환경을 완전하게 하려면 Azure Storage 계정의 감사 로그에 데이터베이스 이벤트를 쓰는 감사를 사용하도록 설정하는 것이 좋습니다.  감사를 사용하려면 [Azure SQL Database 및 Azure Synapse의 감사](../../azure-sql/database/auditing-overview.md) 또는 [Azure SQL Managed Instance의 감사](../managed-instance/auditing-configure.md)를 참조하세요.

## <a name="alerts"></a>경고

Advanced Threat Protection은 비정상적이며 잠재적으로 유해할 수 있는 데이터베이스 액세스 또는 악용 시도를 나타내는 비정상적인 활동을 검색합니다. 경고 목록은 [Microsoft Defender for Cloud의 SQL Database 및 Azure Synapse Analytics에 대 한 경고](../../security-center/alerts-reference.md#alerts-sql-db-and-warehouse)를 참조 하세요.

## <a name="explore-detection-of-a-suspicious-event"></a>의심스러운 이벤트 탐지 살펴보기

비정상적인 데이터베이스 활동이 감지되면 메일 알림을 받습니다. 메일에는 비정상적인 활동의 특징, 데이터베이스 이름, 서버 이름, 애플리케이션 이름, 이벤트 시간을 비롯한 의심스러운 보안 이벤트에 대한 정보가 제공됩니다. 또한 전자 메일에는 가능한 원인에 대한 정보 및 데이터베이스에 대한 잠재적인 위협을 조사하고 완화시키기 위해 권장되는 조치가 제공됩니다.

![비정상적인 활동 보고서](./media/threat-detection-overview/anomalous_activity_report.png)

1. 전자 메일의 **최근 SQL 경고 보기** 링크를 클릭 하 여 Azure Portal를 시작 하 고, 데이터베이스에서 감지 된 활성 위협에 대 한 개요를 제공 하는 클라우드 경고 용 Microsoft Defender 페이지를 표시 합니다.

   ![활동 위협](./media/threat-detection-overview/active_threats.png)

1. 특정 경고를 클릭하여 이 위협을 조사하고 향후 위협을 수정하기 위한 추가 세부 정보 및 조치를 확인합니다.

   예를 들어 SQL 삽입은 데이터 기반 애플리케이션 공격에 사용되는 인터넷 상의 가장 대표적인 웹 애플리케이션 보안 문제 중 하나입니다. 공격자는 애플리케이션의 취약성을 이용하여 악의적인 SQL 문을 애플리케이션 항목 필드에 삽입하고 데이터베이스의 데이터를 침범하거나 수정합니다. SQL 삽입 경고의 경우, 경고 세부 정보에 악용된 취약한 SQL 문이 포함되어 있습니다.

   ![특정 경고](./media/threat-detection-overview/specific_alert.png)

## <a name="explore-alerts-in-the-azure-portal"></a>Azure Portal에서 경고 살펴보기

Advanced Threat Protection은 경고를 [클라우드 용 Microsoft Defender](https://azure.microsoft.com/services/security-center/)와 통합 합니다. 데이터베이스 내의 라이브 SQL Advanced Threat Protection 타일과 Azure Portal의 클라우드 블레이드에 대 한 Microsoft Defender SQL 활성 위협의 상태를 추적 합니다.

**Advanced Threat Protection 경고** 를 클릭 하 여 클라우드 경고 용 Microsoft Defender 페이지를 시작 하 고 데이터베이스에서 감지 된 활성 SQL 위협에 대 한 개요를 가져옵니다.

:::image type="content" source="media/azure-defender-for-sql/advanced-threat-protection-alerts.png" alt-text="데이터베이스 개요의 Advanced Threat Protection 경고":::

:::image type="content" source="media/azure-defender-for-sql/advanced-threat-protection.png" alt-text="SQL에 대 한 Defender의 advanced threat protection":::

## <a name="next-steps"></a>다음 단계

- [Azure SQL Database 및 Azure Synapse의 Advanced Threat Protection](threat-detection-configure.md)에 관해 자세히 알아봅니다.
- [Azure SQL Managed Instance의 Advanced Threat Protection](../managed-instance/threat-detection-configure.md)에 관해 자세히 알아봅니다.
- [SQL에 대 한 Microsoft Defender](azure-defender-for-sql.md)에 대해 자세히 알아보세요.
- [Azure SQL Database 감사](../../azure-sql/database/auditing-overview.md)에 대한 자세한 정보
- [Microsoft Defender for Cloud](../../security-center/security-center-introduction.md) 에 대 한 자세한 내용은 가격 책정에 대 한 자세한 내용은 [가격 책정 페이지를 Azure SQL Database](https://azure.microsoft.com/pricing/details/sql-database/) 참조 하세요.
