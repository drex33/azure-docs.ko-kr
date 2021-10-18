---
title: Azure Defender for SQL - 이점 및 특징
description: Azure Defender for SQL의 이점 및 특징에 대해 알아봅니다.
author: memildin
ms.author: memildin
ms.date: 05/27/2021
ms.topic: overview
ms.service: security-center
ms.custom: references_regions
manager: rkarlin
ms.openlocfilehash: 7f5427edb2b48065ea67e589602be2278c7410c9
ms.sourcegitcommit: 860f6821bff59caefc71b50810949ceed1431510
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2021
ms.locfileid: "129711727"
---
# <a name="introduction-to-azure-defender-for-sql"></a>Azure Defender for SQL 소개

SQL용 Azure Defender에는 Azure Security Center의 [데이터 보안 패키지](../azure-sql/database/azure-defender-for-sql.md)를 확장하여 데이터베이스와 해당 데이터를 어디서나 안전하게 보호하는 두 개의 Azure Defender 요금제가 포함되어 있습니다. Azure Defender for SQL에는 잠재적인 데이터베이스 취약성을 검색하고 완화하며 데이터베이스에 대한 위협을 나타낼 수 있는 비정상 활동을 탐지하는 기능이 포함되어 있습니다.

## <a name="availability"></a>가용성

|양상|세부 정보|
|----|:----|
|릴리스 상태:|**Azure SQL 데이터베이스 서버용 Azure Defender** - 일반 공급(GA)<br>**머신의 SQL 서버용 Azure Defender** - 일반 공급(GA) |
|가격 책정:|**Azure Defender for SQL** 을 구성하는 두 요금제는 [Security Center 가격 책정](https://azure.microsoft.com/pricing/details/security-center/)에 표시된 대로 청구됩니다.|
|보호되는 SQL 버전:|[Azure 가상 머신의 SQL](../azure-sql/virtual-machines/windows/sql-server-on-azure-vm-iaas-what-is-overview.md)<br>[Azure Arc 지원 SQL 서버](/sql/sql-server/azure-arc/overview)<br>Azure Arc를 사용하지 않는 Windows 머신의 온-프레미스 SQL Server<br>Azure SQL [단일 데이터베이스](../azure-sql/database/single-database-overview.md) 및 [탄력적 풀](../azure-sql/database/elastic-pool-overview.md)<br>[Azure SQL Managed Instance](../azure-sql/managed-instance/sql-managed-instance-paas-overview.md)<br>[Azure Synapse Analytics(이전의 SQL DW) 전용 SQL 풀](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md)|
|클라우드:|:::image type="icon" source="./media/icons/yes-icon.png"::: 상용 클라우드<br>:::image type="icon" source="./media/icons/yes-icon.png"::: Azure Government<br>:::image type="icon" source="./media/icons/yes-icon.png"::: Azure 중국 21Vianet(**부분**: SQL 서버에 대한 경고 및 취약성 평가의 하위 집합입니다. 동작 위협 보호를 사용할 수 없습니다.)|
|||

## <a name="what-does-azure-defender-for-sql-protect"></a>Azure Defender for SQL은 무엇을 보호하나요?

**Azure Defender for SQL** 은 두 개의 개별 Azure Defender 요금제로 구성됩니다.

- **Azure SQL 데이터베이스 서버용 Azure Defender** 는 다음을 보호합니다.
    - [Azure SQL Database](../azure-sql/database/sql-database-paas-overview.md)
    - [Azure SQL Managed Instance](../azure-sql/managed-instance/sql-managed-instance-paas-overview.md)
    - [Azure Synapse의 전용 SQL 풀](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md)

- **머신의 SQL 서버용 Azure Defender** 는 하이브리드 환경을 완벽하게 지원하고 Azure, 기타 클라우드 환경 및 온-프레미스 머신에서 호스팅되는 SQL 서버(지원되는 모든 버전)를 보호하도록 Azure 네이티브 SQL Server에 대한 보호를 확장합니다.
    - [Virtual Machines의 SQL Server](https://azure.microsoft.com/services/virtual-machines/sql-server/)
    - 온-프레미스 SQL Server:
        - [Azure Arc 지원 SQL Server(미리 보기)](/sql/sql-server/azure-arc/overview)
        - [Azure Arc를 사용하지 않는 Windows 머신에서 실행되는 SQL Server](../azure-monitor/agents/agent-windows.md)


## <a name="what-are-the-benefits-of-azure-defender-for-sql"></a>Azure Defender for SQL의 이점은?

이러한 두 요금제에는 잠재적인 데이터베이스 취약성을 식별하고 완화하며, 데이터베이스에 대한 위협을 나타낼 수 있는 비정상 활동을 탐지하는 기능이 포함됩니다.

취약성 평가 서비스는 잠재적인 데이터베이스 취약성을 검색하고, 추적하고, 수정하는 데 도움이 됩니다. 평가 검사는 SQL 머신의 보안 상태에 대한 개요와 보안 결과에 대한 세부 정보를 제공합니다.

- [Azure SQL Database 취약성 평가](../azure-sql/database/sql-vulnerability-assessment.md)에 대해 자세히 알아봅니다.
- [컴퓨터의 Azure SQL 서버 취약성 평가](defender-for-sql-on-machines-vulnerability-assessment.md)에 대해 자세히 알아봅니다.

Advanced Threat Protection 서비스는 SQL 서버에서 SQL 삽입, 무차별 암호 대입 공격 및 권한 남용과 같은 위협을 지속적으로 모니터링합니다. 이 서비스는 Azure Security Center에서 의심스러운 활동에 대한 세부 정보, 위협을 완화하는 방법에 대한 지침, Azure Sentinel에서 조사를 계속하는 옵션이 포함된 작업 지향 보안 경고를 제공합니다. [Advanced Threat Protection](../azure-sql/database/threat-detection-overview.md)에 대해 자세히 알아봅니다.

 > [!TIP]
 > [경고 참조 페이지](alerts-reference.md#alerts-sql-db-and-warehouse)에서 SQL 서버에 대한 보안 경고 목록을 확인하세요.


## <a name="is-there-a-performance-impact-from-deploying-azure-defender-for-sql-on-machines"></a>Azure Defender for SQL을 컴퓨터에 배포하면 성능에 영향을 미치나요?

**컴퓨터에 있는 Azure Defender for SQL** 은 분명히 보안에 중점을 둡니다. 그러나 비즈니스에도 관심이 있으므로 SQL 서버에 미치는 영향을 최소화하기 위해 성능에 대한 우선 순위가 지정되었습니다. 

이 서비스에는 데이터 업로드 및 속도와 성능 간의 균형을 유지할 수 있는 분할 아키텍처가 있습니다. 

- 일부 탐지기는 실시간 속도 이점을 위해 컴퓨터에서 실행됩니다.
- 다른 항목은 클라우드에서 실행되어 과도한 계산 부하로부터 컴퓨터를 절약합니다.

솔루션에 대한 랩을 벤치마크 부하와 비교하여 테스트한 결과에서 최고 조각의 CPU 사용량이 평균 3%인 것으로 보여 주었습니다. 현재 사용자에 대한 원격 분석에서 CPU 및 메모리 사용량에 미치는 영향은 무시할 수 있는 수준입니다.

물론 성능은 항상 환경, 컴퓨터 및 부하에 따라 달라집니다. 위의 설명과 숫자는 개별 배포를 보장하는 것이 아니라 일반적인 지침으로 제공되는 것입니다.


## <a name="what-kind-of-alerts-does-azure-defender-for-sql-provide"></a>Azure Defender for SQL은 어떤 종류의 경고를 제공하나요?

다음과 같은 경우 위협 인텔리전스 강화 보안 경고가 트리거됩니다.

- **잠재적 SQL 삽입 공격** - 애플리케이션이 데이터베이스에서 잘못된 SQL 문을 생성할 때 검색되는 취약성 포함
- **비정상적인 데이터베이스 액세스 및 쿼리 패턴** - 예: 여러 자격 증명을 사용한 로그인 시도 실패 횟수가 비정상적으로 많은 경우(무차별 암호 대입 시도)
- **의심스러운 데이터베이스 활동** - 예를 들어, 암호화 마이닝 C&C 서버와 통신하여 보안이 뚫린 컴퓨터에서 SQL Server에 액세스하는 합법적인 사용자

경고에는 경고를 트리거한 인시던트의 세부 정보와 위협을 조사하고 완화하는 방법에 대한 권장 사항이 포함됩니다.



## <a name="next-steps"></a>다음 단계

이 문서에서는 Azure Defender for SQL에 대해 알아보았습니다. 설명된 서비스를 사용하려면 다음을 수행합니다.

- 머신에서 SQL 서버용 Azure Defender를 사용하여 [SQL 서버에서 취약성을 검색](defender-for-sql-usage.md)합니다.
- Azure Defender for SQL에 대한 프레젠테이션은 [Azure Defender for SQL를 통해 어디서나 SQL 서버를 보호하는 방법](https://www.youtube.com/watch?v=V7RdB6RSVpc)을 참조하세요.
