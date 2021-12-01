---
title: Azure Automation을 사용하여 데이터베이스 관리
description: Azure Automation 서비스를 사용하여 대규모 Azure SQL Database를 관리하는 방법을 알아봅니다.
services: sql-database
ms.service: sql-db-mi
ms.subservice: deployment-configuration
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: LitKnd
ms.author: kendralittle
ms.date: 03/12/2019
ms.openlocfilehash: f824ce7c28108ec7bc8d3e044da900cf010ecaf8
ms.sourcegitcommit: 66b6e640e2a294a7fbbdb3309b4829df526d863d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/01/2021
ms.locfileid: "133386801"
---
# <a name="manage-databases-in-azure-sql-database-by-using-azure-automation"></a>Azure Automation을 사용하여 Azure SQL Database의 데이터베이스 관리

[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

이 가이드에서는 Azure Automation 서비스 및 이 서비스를 사용하여 Azure SQL Database의 데이터베이스 관리를 간소화하는 방법을 소개합니다.

## <a name="about-azure-automation"></a>Azure Automation 소개

[Azure Automation](https://azure.microsoft.com/services/automation/)은 프로세스 자동화를 통해 클라우드 관리를 간소화하기 위한 Azure 서비스입니다. Azure Automation을 통해 장기 실행 작업, 수동 작업, 오류가 발생하기 쉬운 작업 및 빈번하게 반복되는 작업을 자동화하여 조직의 안정성, 효율성 및 가치 창출 시간을 향상시킬 수 있습니다. 시작하는 방법에 대한 자세한 내용은 [Azure Automation 소개](../../automation/automation-intro.md)를 참조하세요.

Azure Automation은 조직이 성장함에 따라 요구를 충족하기 위해 크기가 조정되는 안정성과 가용성 높은 워크플로 실행 엔진을 제공합니다. Azure Automation에서는 작업이 정확히 필요한 시간에 수행되도록 수동, 타사 시스템 또는 예약된 간격에 의해 프로세스를 시작할 수 있습니다.

Azure Automation에 의해 자동으로 실행되도록 클라우드 관리 작업을 이동하여 작업 오버헤드를 줄이고 IT/DevOps 직원들이 비즈니스 가치를 추가하는 작업에 집중할 수 있게 합니다.

## <a name="how-azure-automation-can-help-manage-your-databases"></a>Azure Automation을 통해 데이터베이스를 관리할 수 있는 방법

Azure Automation을 사용하면 [Azure PowerShell 도구](/powershell/azure/)에서 제공되는 [PowerShell cmdlet](/powershell/module/servicemanagement/azure.service/#sql)을 통해 Azure SQL Database의 데이터베이스를 관리할 수 있습니다. Azure Automation에는 Azure SQL Database PowerShell cmdlet이 기본 제공되므로 서비스 내에서 SQL Database 관리 작업을 모두 수행할 수 있습니다. Azure Automation에서 이러한 cmdlet을 다른 Azure 서비스용 cmdlet과 연결하여 Azure 서비스와 타사 시스템 간의 복잡한 작업을 자동화할 수도 있습니다.

또한 Azure Automation에서 PowerShell을 통해 SQL 명령을 실행하여 SQL 서버와 직접 통신할 수 있습니다.

[Azure Automation](../../automation/automation-runbook-gallery.md)에 대한 Runbook 및 모듈 갤러리는 Azure Automation으로 가져올 수 있는 Microsoft 및 커뮤니티의 다양한 Runbook을 제공합니다. Runbook을 사용하려면 갤러리에서 Runbook을 다운로드하거나, Azure Portal을 사용하여 갤러리에서 또는 Automation 계정에서 Runbook을 직접 가져올 수 있습니다.

## <a name="next-steps"></a>다음 단계

Azure Automation의 기본 사항과 Azure Automation을 사용하여 Azure SQL Database를 관리하는 방법을 배웠으므로 이제 다음 링크에 따라 Azure Automation에 대해 자세히 알아봅니다.

- [Azure Automation 개요](../../automation/automation-intro.md)
- [내 첫 번째 runbook](../../automation/learn/powershell-runbook-managed-identity.md)
