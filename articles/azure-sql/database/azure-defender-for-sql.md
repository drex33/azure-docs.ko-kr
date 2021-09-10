---
title: Azure Defender for SQL
description: Azure SQL Database, Azure SQL Managed Instance 또는 Azure Synapse에서 데이터베이스에 대한 위협을 나타낼 수 있는 비정상적인 활동을 감지하고 데이터베이스 취약성을 관리하는 기능에 대해 알아보세요.
ms.service: sql-db-mi
ms.subservice: security
ms.devlang: ''
ms.custom: sqldbrb=2
ms.topic: conceptual
ms.author: memildin
manager: rkarlin
author: memildin
ms.date: 06/07/2021
ms.openlocfilehash: 12cc4fc1c0aafa148b765f329f0badab127f3644
ms.sourcegitcommit: ff1aa951f5d81381811246ac2380bcddc7e0c2b0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/07/2021
ms.locfileid: "111571004"
---
# <a name="azure-defender-for-sql"></a>Azure Defender for SQL

[!INCLUDE[appliesto-sqldb-sqlmi-asa](../includes/appliesto-sqldb-sqlmi-asa.md)]

Azure Defender for SQL은 고급 SQL 보안 기능의 통합 패키지입니다. Azure Defender는 현재 Azure SQL Database, Azure SQL Managed Instance 및 Azure Synapse Analytics에서 사용할 수 있습니다. 데이터베이스에서 취약해질 수 있는 부분을 파악 및 완화하고, 데이터베이스에 위협이 될 수 있는 비정상적인 활동을 감지하기 위한 기능이 포함되어 있습니다. 이러한 기능을 설정하고 관리하는 단일 준비 위치를 제공합니다.

## <a name="what-are-the-benefits-of-azure-defender-for-sql"></a>Azure Defender for SQL의 이점은?

Azure Defender는 SQL Vulnerability Assessment 및 Advanced Threat Protection을 비롯하여 고급 SQL 보안 기능 집합을 제공합니다.
- [취약성 평가](sql-vulnerability-assessment.md)는 잠재적인 데이터베이스 취약성을 검색, 추적 및 수정할 수 있는 서비스를 간편하게 구성합니다. 보안 상태에 대한 가시성을 제공하고, 보안 문제를 해결하고 데이터베이스 보안을 강화하기 위한 실행 가능한 단계를 포함합니다.
- [Advanced Threat Protection](threat-detection-overview.md)은 비정상적이며 잠재적으로 유해할 수 있는 데이터베이스 액세스 또는 악용 시도를 나타내는 비정상적인 활동을 탐지합니다. 지속적으로 의심스러운 활동에 대한 데이터베이스를 모니터링하고, 잠재적인 취약점, Azure SQL 삽입 공격 및 비정상 데이터베이스 액세스 패턴에 대한 보안 경고를 즉시 제공합니다. Advanced Threat Protection 경고는 의심스러운 활동에 대한 세부 정보를 제공하고 위협을 조사하고 완화하는 방법에 대한 조치를 권장합니다.

포함된 모든 기능을 사용하도록 설정하려면 SQL용 Azure Defender를 한 번 사용하도록 설정합니다. 한 번 클릭으로 Azure 또는 SQL Managed Instance의 [서버](logical-servers.md)에 있는 모든 데이터베이스에 대해 Azure Defender를 사용하도록 설정할 수 있습니다. Azure Defender 설정을 사용하거나 관리하려면 [SQL 보안 관리자](../../role-based-access-control/built-in-roles.md#sql-security-manager) 역할, 데이터베이스 또는 SQL Server 관리자 역할에 속해야 합니다.

Azure Defender for SQL 가격 책정에 대한 자세한 내용은 [Azure Security Center 가격 책정 페이지](https://azure.microsoft.com/pricing/details/security-center/)를 참조하세요.

## <a name="enable-azure-defender"></a>Azure Defender 사용 
Azure Defender 계획을 사용하도록 설정하는 방법에는 여러 가지가 있습니다. 다음의 구독 수준(**권장**)에서 활성화할 수 있습니다.

- [Azure Security Center](#enable-azure-defender-for-azure-sql-database-at-the-subscription-level-from-azure-security-center)
- [REST API, Azure CLI, PowerShell 또는 Azure Policy를 사용하여 프로그래밍](#enable-azure-defender-plans-programatically)

또는 [리소스 수준에서 Azure Defender for Azure SQL Database 사용](#enable-azure-defender-for-azure-sql-database-at-the-resource-level)에서 설명한 대로 리소스 수준에서 사용하도록 설정할 수 있습니다.

### <a name="enable-azure-defender-for-azure-sql-database-at-the-subscription-level-from-azure-security-center"></a>Azure Security Center의 구독 수준에서 Azure SQL Database용 Azure Defender 사용
Azure Security Center 내의 구독 수준에서 Azure SQL Database용 Azure Defender를 사용하려면 다음을 수행합니다.

1. [Azure Portal](https://portal.azure.com)에서 **Security Center** 를 엽니다.
1. Security Center 메뉴에서 **가격 책정 및 설정** 을 선택합니다.
1. 관련 구독을 선택합니다.
1. 계획 설정을 **켜기** 로 변경합니다.

    :::image type="content" source="media/azure-defender-for-sql/enable-azure-defender-sql-subscription-level.png" alt-text="구독 수준에서 Azure SQL Database용 Azure Defender를 사용하도록 설정합니다.":::

1. **저장** 을 선택합니다.


### <a name="enable-azure-defender-plans-programatically"></a>프로그래밍 방식으로 Azure Defender 계획 사용 

Azure의 유연성을 통해 Azure Defender 계획을 사용하기 위한 여러 프로그래밍 방식을 사용할 수 있습니다. 

다음 도구 중 하나를 사용하여 Azure Defender를 구독에 사용하도록 설정할 수 있습니다. 

| 메서드       | 지침                                                                                                                                       |
|--------------|----------------------------------------------------------------------------------------------------------------------------------------------------|
| REST API     | [가격 책정 API](/rest/api/securitycenter/pricings)                                                                                                  |
| Azure CLI    | [az security pricing](/cli/azure/security/pricing)                                                                                                 |
| PowerShell   | [Set-AzSecurityPricing](/powershell/module/az.security/set-azsecuritypricing)                                                                      |
| Azure Policy | [가격 책정 번들](https://github.com/Azure/Azure-Security-Center/blob/master/Pricing%20%26%20Settings/ARM%20Templates/Set-ASC-Bundle-Pricing.json) |
|              |                                                                                                                                                    |

### <a name="enable-azure-defender-for-azure-sql-database-at-the-resource-level"></a>리소스 수준에서 Azure Defender for Azure SQL Database 사용

구독 수준에서 Azure Defender 계획을 활성화하는 것이 좋습니다. 그러면 보호되지 않는 리소스를 만드는 데 도움이 될 수 있습니다. 그러나 서버 수준에서 Azure Defender를 사용하도록 설정해야 하는 조직의 이유가 있는 경우 다음 단계를 사용합니다.

1. [Azure Portal](https://portal.azure.com)에서 서버 또는 관리되는 인스턴스를 엽니다.
1. **보안** 제목 아래에서 **Security Center** 를 선택합니다.
1. **Azure Defender for SQL 사용** 을 선택합니다.

    :::image type="content" source="media/azure-defender-for-sql/enable-azure-defender.png" alt-text="Azure SQL 데이터베이스 내에서 Azure Defender for SQL을 사용하도록 설정합니다.":::

> [!NOTE]
> **취약성 평가** 검사 결과를 저장하기 위해 스토리지 계정이 자동으로 생성 및 구성됩니다. 동일한 리소스 그룹 및 지역의 다른 서버에 대해 Azure Defender를 이미 사용하도록 설정한 경우 기존 스토리지 계정이 사용됩니다.
>
> Azure Defender의 비용은 노드당 Azure Security Center 표준 계층 가격 책정에 부합합니다. 여기서 노드는 전체 서버 또는 관리되는 인스턴스입니다. 따라서 Azure Defender를 사용하여 서버 또는 관리되는 인스턴스의 모든 데이터베이스를 보호하기 위해 한 번만 결제합니다. Azure Defender를 처음에 평가판으로 사용해볼 수 있습니다.


## <a name="manage-azure-defender-settings"></a>Azure Defender 설정 관리

Azure Defender 설정을 보고 관리하려면 다음을 수행합니다.

1. 서버 또는 관리되는 인스턴스의 **보안** 영역에서 **Security Center** 를 선택합니다.

    이 페이지에는 Azure Defender for SQL 상태가 표시됩니다.

    :::image type="content" source="media/azure-defender-for-sql/status-of-defender-for-sql.png" alt-text="Azure SQL Database 내에서 Azure Defender for SQL의 상태를 확인하는 중입니다.":::

1. Azure Defender for SQL을 사용하는 경우 이전 그래픽에 나온 것처럼 **구성** 링크가 표시됩니다. Azure Defender for SQL에 대한 설정을 편집하려면 **구성** 을 선택합니다.

    :::image type="content" source="media/azure-defender-for-sql/security-server-settings.png" alt-text="Azure Defender for SQL에 대한 설정입니다.":::

1. 필요한 부분을 변경하고 **저장** 을 선택합니다.


## <a name="next-steps"></a>다음 단계

- [취약성 평가](sql-vulnerability-assessment.md)에 대해 자세히 알아보기
- [Advanced Threat Protection](threat-detection-configure.md)에 대해 자세히 알아보기
- [Azure Security Center](../../security-center/security-center-introduction.md)에 대한 자세한 정보
