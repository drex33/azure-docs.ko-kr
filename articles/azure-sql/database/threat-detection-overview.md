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
ms.openlocfilehash: c3b2d31a2351e90be2a30b146dd12d02b90ac16b
ms.sourcegitcommit: f3b930eeacdaebe5a5f25471bc10014a36e52e5e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/16/2021
ms.locfileid: "112236376"
---
# <a name="sql-advanced-threat-protection"></a>SQL Advanced Threat Protection
[!INCLUDE[appliesto-sqldb-sqlmi-asa](../includes/appliesto-sqldb-sqlmi-asa.md)] :::image type="icon" source="../media/applies-to/yes.png" border="false":::Azure VM의 SQL Server :::image type="icon" source="../media/applies-to/yes.png" border="false":::Azure Arc 지원 SQL Server

[Azure SQL Database](sql-database-paas-overview.md), [Azure SQL Managed Instance](../managed-instance/sql-managed-instance-paas-overview.md), [Azure Synapse Analytics](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md), [SQL Server on Azure Virtual Machines](../virtual-machines/windows/sql-server-on-azure-vm-iaas-what-is-overview.md), [Azure Arc 지원 SQL Server](/sql/sql-server/azure-arc/overview)의 Advanced Threat Protection은 데이터베이스를 악용하거나 액세스하려는 비정상적이고 잠재적으로 유해한 시도를 나타내는 비정상적인 활동을 탐지합니다.

Advanced Threat Protection은 고급 SQL 보안 기능용 통합 패키지인 [Azure Defender for SQL](../../security-center/defender-for-sql-introduction.md) 제품에 포함되어 있습니다. Azure Defender for SQL 중앙 포털을 통해 Advanced Threat Protection에 액세스하고 관리할 수 있습니다.

## <a name="overview"></a>개요

Advanced Threat Protection은 비정상적인 활동에 대한 보안 경고를 제공하여 잠재적인 위협이 발생하면 고객이 이를 감지하고 대응할 수 있도록 하는 새로운 차원의 보안을 제공합니다. 사용자는 의심스러운 데이터베이스 활동, 잠재적 취약성 및 SQL 삽입 공격뿐만 아니라 비정상적인 데이터베이스 액세스 및 쿼리 패턴에 대한 경고도 받습니다. Advanced Threat Protection은 의심스러운 활동에 대한 세부 정보와 위협을 조사하고 완화하는 방법에 대한 권장 작업을 포함한 경고를 [Azure Security Center](https://azure.microsoft.com/services/security-center/)와 통합합니다. Advanced Threat Protection은 보안 전문가가 되거나 고급 보안 모니터링 시스템을 관리할 필요 없이 데이터베이스에 대한 잠재적인 위협에 간단하게 대처할 수 있도록 합니다.

조사 환경을 완전하게 하려면 Azure Storage 계정의 감사 로그에 데이터베이스 이벤트를 쓰는 감사를 사용하도록 설정하는 것이 좋습니다.  감사를 사용하려면 [Azure SQL Database 및 Azure Synapse의 감사](../../azure-sql/database/auditing-overview.md) 또는 [Azure SQL Managed Instance의 감사](../managed-instance/auditing-configure.md)를 참조하세요.

## <a name="alerts"></a>경고

Advanced Threat Protection은 비정상적이며 잠재적으로 유해할 수 있는 데이터베이스 액세스 또는 악용 시도를 나타내는 비정상적인 활동을 검색합니다. 경고 목록은 [Azure Security Center의 SQL Database 및 Azure Synapse Analytics에 대한 경고](../../security-center/alerts-reference.md#alerts-sql-db-and-warehouse)를 참조하세요.

## <a name="explore-detection-of-a-suspicious-event"></a>의심스러운 이벤트 탐지 살펴보기

비정상적인 데이터베이스 활동이 감지되면 메일 알림을 받습니다. 메일에는 비정상적인 활동의 특징, 데이터베이스 이름, 서버 이름, 애플리케이션 이름, 이벤트 시간을 비롯한 의심스러운 보안 이벤트에 대한 정보가 제공됩니다. 또한 전자 메일에는 가능한 원인에 대한 정보 및 데이터베이스에 대한 잠재적인 위협을 조사하고 완화시키기 위해 권장되는 조치가 제공됩니다.

![비정상적인 활동 보고서](./media/threat-detection-overview/anomalous_activity_report.png)

1. 메일에서 **최근 SQL 경고 보기** 링크를 클릭하여 Azure Portal을 시작하고, 데이터베이스에서 탐지된 활성 위협을 대략적으로 보여 주는 Azure Security Center 경고 페이지를 표시합니다.

   ![활동 위협](./media/threat-detection-overview/active_threats.png)

1. 특정 경고를 클릭하여 이 위협을 조사하고 향후 위협을 수정하기 위한 추가 세부 정보 및 조치를 확인합니다.

   예를 들어 SQL 삽입은 데이터 기반 애플리케이션 공격에 사용되는 인터넷 상의 가장 대표적인 웹 애플리케이션 보안 문제 중 하나입니다. 공격자는 애플리케이션의 취약성을 이용하여 악의적인 SQL 문을 애플리케이션 항목 필드에 삽입하고 데이터베이스의 데이터를 침범하거나 수정합니다. SQL 삽입 경고의 경우, 경고 세부 정보에 악용된 취약한 SQL 문이 포함되어 있습니다.

   ![특정 경고](./media/threat-detection-overview/specific_alert.png)

## <a name="explore-alerts-in-the-azure-portal"></a>Azure Portal에서 경고 살펴보기

Advanced Threat Protection은 경고를 [Azure 보안 센터](https://azure.microsoft.com/services/security-center/)와 통합합니다. Azure Portal의 데이터베이스와 SQL Azure Defender 블레이드 내에 있는 라이브 SQL Advanced Threat Protection 타일에서는 활성 위협의 상태를 추적합니다.

**Advanced Threat Protection 경고** 를 클릭하여 Azure Security Center 경고 페이지를 시작하고 데이터베이스에서 감지된 활성 SQL 위협에 대한 개요를 가져옵니다.

:::image type="content" source="media/azure-defender-for-sql/advanced-threat-protection-alerts.png" alt-text="데이터베이스 개요의 Advanced Threat Protection 경고":::

:::image type="content" source="media/azure-defender-for-sql/advanced-threat-protection.png" alt-text="Security Center의 Advanced Threat Protection":::

## <a name="next-steps"></a>다음 단계

- [Azure SQL Database 및 Azure Synapse의 Advanced Threat Protection](threat-detection-configure.md)에 관해 자세히 알아봅니다.
- [Azure SQL Managed Instance의 Advanced Threat Protection](../managed-instance/threat-detection-configure.md)에 관해 자세히 알아봅니다.
- [Azure Defender for SQL](azure-defender-for-sql.md)에 대해 자세히 알아봅니다.
- [Azure SQL Database 감사](../../azure-sql/database/auditing-overview.md)에 대한 자세한 정보
- [Azure Security Center](../../security-center/security-center-introduction.md)에 대해 자세히 알아봅니다. 가격 책정에 대한 자세한 내용은 [Azure SQL Database 가격 책정 페이지](https://azure.microsoft.com/pricing/details/sql-database/)를 참조하세요.
