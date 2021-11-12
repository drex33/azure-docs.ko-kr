---
title: Advanced Threat Protection 구성
description: Advanced Threat Protection은 Azure SQL Database의 잠재적인 보안 위협을 나타내는 비정상적인 데이터베이스 활동을 검색합니다.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: seo-dt-2019, sqldbrb=1
ms.topic: how-to
author: rmatchoro
ms.author: ronmat
ms.reviewer: vanto
ms.date: 12/01/2020
ms.openlocfilehash: f2402fe795141a00ac488cb163930cbde58141f6
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132325660"
---
# <a name="configure-advanced-threat-protection-for-azure-sql-database"></a>Azure SQL Database의 Advanced Threat Protection 구성
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Azure SQL Database용 [Advanced Threat Protection](threat-detection-overview.md)은 비정상적이며 잠재적으로 유해한 데이터베이스 액세스 또는 악용 시도를 나타내는 비정상 활동을 검색합니다. Advanced Threat Protection은 **잠재적인 SQL 삽입**, **비정상적인 위치 또는 데이터 센터에서 액세스**, **생소한 보안 주체 또는 잠재적으로 위험한 애플리케이션의 액세스** 및 **무차별 SQL 자격 증명 대입 공격** 을 식별할 수 있습니다. 자세한 내용은 [Advanced Threat Protection 경고](threat-detection-overview.md#alerts)를 참조하세요.

[이메일 알림](threat-detection-overview.md#explore-detection-of-a-suspicious-event) 또는 [Azure Portal](threat-detection-overview.md#explore-alerts-in-the-azure-portal)을 통해 탐지된 위협에 대한 알림을 받을 수 있습니다.

[advanced Threat Protection](threat-detection-overview.md) 은 고급 SQL 보안 기능을 위한 통합 패키지인 [SQL 제공 용 Microsoft Defender](azure-defender-for-sql.md) 의 일부입니다. 고급 위협 방지는 SQL 용 중앙 Microsoft Defender 포털을 통해 액세스 하 고 관리할 수 있습니다.

## <a name="set-up-advanced-threat-protection-in-the-azure-portal"></a>Azure Portal에서 Advanced Threat Protection 설정

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
2. 보호할 서버의 구성 페이지로 이동합니다. 보안 설정에서 **클라우드 용 Defender** 를 선택 합니다.
3. **Microsoft Defender for SQL** 구성 페이지에서 다음을 수행 합니다.

   - 서버에서 **SQL에 대해 Microsoft Defender를** 사용 하도록 설정 합니다.
   - **Advanced Threat Detection 설정** 에서 **경고 전송 대상** 텍스트 상자에 비정상적인 데이터베이스 활동 검색 시 보안 경고를 받을 이메일 목록을 입력합니다.
   
   :::image type="content" source="media/azure-defender-for-sql/set-up-advanced-threat-protection.png" alt-text="Advanced Threat Protection 설정":::

## <a name="set-up-advanced-threat-protection-using-powershell"></a>PowerShell을 사용하여 Advanced Threat Protection 설정

스크립트 예제는 [PowerShell을 사용하여 감사 및 Advanced Threat Protection 구성](scripts/auditing-threat-detection-powershell-configure.md)을 참조하세요.

## <a name="next-steps"></a>다음 단계

- [Advanced Threat Protection](threat-detection-overview.md)에 대해 자세히 알아봅니다.
- [SQL Managed Instance의 Advanced Threat Protection](../managed-instance/threat-detection-configure.md)에 대해 자세히 알아봅니다.  
- [SQL에 대 한 Microsoft Defender](azure-defender-for-sql.md)에 대해 자세히 알아보세요.
- [감사](../../azure-sql/database/auditing-overview.md)에 대해 자세히 알아보기
- [클라우드 용 Microsoft Defender](../../security-center/security-center-introduction.md) 에 대 한 자세한 정보
- 가격 책정에 대한 자세한 내용은 [SQL Database 가격 책정 페이지](https://azure.microsoft.com/pricing/details/sql-database/)를 참조하세요.
