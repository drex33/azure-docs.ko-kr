---
title: Advanced Threat Protection 구성
titleSuffix: Azure SQL Managed Instance
description: Advanced Threat Protection은 Azure SQL Managed Instance의 잠재적인 보안 위협을 표시하는 비정상적인 데이터베이스 활동을 검색합니다.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: security
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: how-to
author: rmatchoro
ms.author: ronmat
ms.reviewer: vanto
ms.date: 12/01/2020
ms.openlocfilehash: b6f03129076ea4c87b7cbd176681cd1323d9d453
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132345722"
---
# <a name="configure-advanced-threat-protection-in-azure-sql-managed-instance"></a>Azure SQL Managed Instance에서 Advanced Threat Protection 구성
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

[Advanced Threat Protection](../database/threat-detection-overview.md)은 [Azure SQL Managed Instance](sql-managed-instance-paas-overview.md)에서 비정상적이며 잠재적으로 유해한 데이터베이스 액세스 또는 악용 시도를 표시하는 비정상 활동을 검색합니다. Advanced Threat Protection은 **잠재적인 SQL 삽입**, **비정상적인 위치 또는 데이터 센터에서 액세스**, **생소한 보안 주체 또는 잠재적으로 위험한 애플리케이션의 액세스**, **무차별 SQL 자격 증명 대입 공격** 을 식별할 수 있습니다. 세부 정보는 [Advanced Threat Protection 경고](../database/threat-detection-overview.md#alerts)를 참조하세요.

[이메일 알림](../database/threat-detection-overview.md#explore-detection-of-a-suspicious-event) 또는 [Azure Portal](../database/threat-detection-overview.md#explore-alerts-in-the-azure-portal)을 통해 탐지된 위협에 대한 알림을 받을 수 있습니다.

[advanced Threat Protection](../database/threat-detection-overview.md) 은 고급 SQL 보안 기능을 위한 통합 패키지인 [SQL 제공 용 Microsoft Defender](../database/azure-defender-for-sql.md) 의 일부입니다. 고급 위협 방지는 SQL 용 중앙 Microsoft Defender 포털을 통해 액세스 하 고 관리할 수 있습니다.

##  <a name="azure-portal"></a>Azure portal

1. [Azure Portal](https://portal.azure.com)에 로그인합니다. 
2. 보호할 SQL Managed Instance의 인스턴스 구성 페이지로 이동합니다. **보안** 에서 **SQL에 대해 Defender** 를 선택 합니다.
3. Microsoft Defender for SQL 구성 페이지에서 다음을 수행 합니다.
   - SQL에 대해 Microsoft Defender **를** 켭니다.
   - 비정상적인 데이터베이스 활동 감지 시 보안 경고를 수신하도록 이메일 주소로 **알림 보내기** 를 구성합니다.
   - 비정상적인 위협 감사 레코드가 저장되는 **Azure Storage 계정** 을 선택합니다.
   - 구성할 **Advanced Threat Protection 형식** 을 선택합니다. [Advanced Threat Protection 경고](../database/threat-detection-overview.md)에 대해 자세히 알아봅니다.
4. **저장** 을 클릭 하 SQL 정책에 대 한 새 또는 업데이트 된 Microsoft Defender를 저장 합니다.

   :::image type="content" source="../database/media/azure-defender-for-sql/set-up-advanced-threat-protection-mi.png" alt-text="Advanced Threat Protection 설정":::

## <a name="next-steps"></a>다음 단계

- [Advanced Threat Protection](../database/threat-detection-overview.md)에 대해 자세히 알아봅니다.
- 관리되는 인스턴스에 대한 자세한 내용은 [Azure SQL Managed Instance 정의](sql-managed-instance-paas-overview.md)를 참조하세요.
- [Azure SQL Database에 대한 Advanced Threat Protection](../database/threat-detection-configure.md)에 대해 자세히 알아보세요.
- [SQL Managed Instance 감사](./auditing-configure.md)에 대해 자세히 알아봅니다.
- [클라우드에 대 한 Microsoft Defender](../../security-center/security-center-introduction.md)에 대해 자세히 알아보세요.
