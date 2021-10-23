---
title: Azure SQL Managed Instance 풀이란?
titleSuffix: Azure SQL Managed Instance
description: 편리하고 비용 효율적인 방식으로 더 작은 SQL Server 데이터베이스를 대규모로 클라우드로 마이그레이션하고 여러 관리형 인스턴스를 관리할 수 있는 Azure SQL Managed Instance 풀(미리 보기)에 대해 알아봅니다.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: service-overview
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: urosmil
ms.author: urmilano
ms.reviewer: mathoma
ms.date: 10/21/2021
ms.openlocfilehash: 66a19e87368e6d833020f5b0f7d581ec0963e429
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130233311"
---
# <a name="what-is-an-azure-sql-managed-instance-pool-preview"></a>Azure SQL Managed Instance 풀(미리 보기)이란?
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Azure SQL Managed Instance의 인스턴스 풀을 통해 편리하고 비용 효율적인 방식으로 더 작은 SQL Server 인스턴스를 대규모로 클라우드로 마이그레이션할 수 있습니다.

인스턴스 풀을 사용하면 총 마이그레이션 요구 사항에 따라 계산 리소스를 미리 프로비전할 수 있습니다. 그런 다음 미리 프로비전된 계산 수준까지 여러 개의 개별 관리되는 인스턴스를 배포할 수 있습니다. 예를 들어 vCore 8개를 사전 프로비저닝하면 두 개의 2-vCore와 하나의 4-vCore 인스턴스를 배포한 다음, 데이터베이스를 이러한 인스턴스로 마이그레이션할 수 있습니다. 인스턴스 풀을 사용하기 전에 더 작고 덜 컴퓨팅 집약적인 워크로드를 클라우드로 마이그레이션할 때 더 큰, 관리형 인스턴스로 통합해야 하는 경우가 많습니다. 데이터베이스 그룹을 대규모 인스턴스로 마이그레이션하기 위해서는 일반적으로 신중한 용량 계획과 리소스 관리, 부가적인 보안에 대한 고려, 그리고 인스턴스 수준에서의 추가 데이터 통합 작업이 필요합니다.

또한 인스턴스 풀은 네이티브 VNet 통합을 지원하므로 여러 인스턴스 풀과 여러 단일 인스턴스를 같은 서브넷에 배포할 수 있습니다.

## <a name="key-capabilities"></a>주요 기능

인스턴스 풀은 다음과 같은 이점을 제공합니다.

1. 2개의 vCore 인스턴스를 호스트하는 기능 *\*인스턴스 풀의 인스턴스에만 해당합니다*.
2. 예측 가능하고 빠른 인스턴스 배포 시간(최대 5분)
3. 최소한의 IP 주소 할당

다음 다이어그램에서는 가상 네트워크 서브넷 내에 여러 관리형 인스턴스가 배포된 인스턴스 풀을 보여 줍니다.

![여러 인스턴스가 있는 인스턴스 풀](./media/instance-pools-overview/instance-pools1.png)

인스턴스 풀을 사용하면 동일한 가상 머신에 여러 개의 인스턴스를 배포할 수 있으며, 여기서 가상 머신의 컴퓨팅 크기는 해당 풀에 할당된 총 vCore의 수를 기준으로 합니다. 이 아키텍처를 사용하면 가상 머신을 여러 개의 인스턴스로 *분할* 할 수 있으며, 이 경우 크기는 2개 vCore(2-vCore 인스턴스는 풀의 인스턴스에서만 사용할 수 있음)를 포함하여 지원되는 모든 크기가 가능합니다.

초기 배포 후에는 풀의 인스턴스에 대한 관리 작업이 훨씬 빨라집니다. [가상 클러스터](connectivity-architecture-overview.md#high-level-connectivity-architecture)(전용 가상 머신 집합)의 배포 또는 확장이 관리형 인스턴스 프로비저닝에 속하지 않기 때문입니다.

풀의 모든 인스턴스가 동일한 가상 머신을 공유하므로 총 IP 할당은 배포된 인스턴스의 수에 의존하지 않으며, 이는 IP 범위가 좁은 서브넷에 배포할 때 편리합니다.

각 풀에는 단 9개의 IP 주소에 대한 고정 IP 할당이 있습니다(자체 필요에 따라 예약된 서브넷의 IP 주소 5개는 포함되지 않음). 자세한 내용은 [단일 인스턴스에 대한 서브넷 크기 요구 사항](vnet-subnet-determine-size.md)을 참조하세요.

## <a name="application-scenarios"></a>애플리케이션 시나리오

다음 목록은 인스턴스 풀을 고려해야 하는 주요 사용 사례입니다.

- 대부분 크기가 작은(예를 들어 2개 또는 4개의 vCore) ‘SQL Server 인스턴스 그룹’을 동시에 마이그레이션하는 경우
- 예측 가능하고 짧은 인스턴스 만들기 또는 크기 조정이 중요한 시나리오. 예를 들어 인스턴스 수준 기능이 필요한 다중 테넌트 SaaS 애플리케이션 환경에서 새 테넌트 배포.
- 고정 비용 또는 지출 한도가 중요한 시나리오.  예를 들어 필요에 따라 관리형 인스턴스를 정기적으로 배포하는 고정된(또는 자주 변경되지 않는) 크기의 공유 개발-테스트 또는 데모 환경 실행.
- VNet 서브넷의 최소 IP 주소 할당이 중요한 시나리오. 풀의 모든 인스턴스가 가상 머신을 공유하므로 할당된 IP 주소의 수는 단일 인스턴스의 경우보다 적습니다.

## <a name="architecture"></a>Architecture

인스턴스 풀의 아키텍처는 일반적인 (*단일*) 관리형 인스턴스와 비슷합니다. [Azure 가상 네트워크 내에서의 배포](../../virtual-network/virtual-network-for-azure-services.md)를 지원하고 고객에 대한 격리 및 보안을 제공하기 위해 인스턴스 풀은 [가상 클러스터](connectivity-architecture-overview.md#high-level-connectivity-architecture)에도 의존합니다. 가상 클러스터는 고객의 가상 네트워크 서브넷 내에 배포된 격리된 전용 가상 머신 집합을 나타냅니다.

두 배포 모델의 주요 차이점은 인스턴스 풀은 동일한 가상 머신 노드에서 여러 번의 SQL Server 프로세스 배포([Windows 작업 개체](/windows/desktop/ProcThread/job-objects)를 사용하여 관리되는 리소스)를 허용하는 반면, 단일 인스턴스는 가상 머신 노드에서 항상 단독이라는 점입니다.

다음 다이어그램에서는 인스턴스 풀과 동일한 서브넷에 배포된 두 개의 개별 인스턴스를 보여 주고, 두 배포 모델에 대한 주요 아키텍처 세부 정보를 보여 줍니다.

![인스턴스 풀 및 두 개의 개별 인스턴스](./media/instance-pools-overview/instance-pools2.png)

모든 인스턴스 풀은 아래에 별도의 가상 클러스터를 만듭니다. 풀 내의 인스턴스와 동일한 서브넷에 배포된 단일 인스턴스는 SQL Server 프로세스 및 게이트웨이 구성 요소에 할당된 컴퓨팅 리소스를 공유하지 않으며, 이는 성능 예측 가능성을 보장합니다.

## <a name="resource-limitations"></a>리소스 제한

인스턴스 풀 및 풀 내의 인스턴스에 대한 몇 가지 리소스 제한이 있습니다.

- 인스턴스 풀은 Gen5 하드웨어에서만 사용할 수 있습니다.
- 풀 내의 관리형 인스턴스에는 전용 CPU와 RAM이 있으므로 모든 인스턴스에서 집계된 vCore 수가 풀에 할당된 vCore의 수보다 작거나 같아야 합니다.
- 모든 [인스턴스 수준 제한](resource-limits.md#service-tier-characteristics)은 풀 내에 만들어진 인스턴스에 적용됩니다.
- 인스턴스 수준 제한 외에도 *인스턴스 풀 수준에* 적용되는 두 가지 제한이 있습니다.
  - 풀당 총 스토리지 크기(8TB).
  - 풀당 총 사용자 데이터베이스 수. 이 한도는 풀 vCores 값에 따라 달라집니다.
    - vCore 풀 8개는 최대 200개의 데이터베이스를 지원합니다.
    - vCores 풀 16개는 최대 400개의 데이터베이스를 지원합니다.
    - 24개 이상의 vCores 풀은 최대 500개의 데이터베이스를 지원합니다.
- 플래그를 사용하여 관리되는 인스턴스를 만들거나 설정하면 Azure AD 인증을 사용할 수 `-AssignIdentity` 있습니다. 자세한 내용은 [New-AzSqlInstance](/powershell/module/az.sql/new-azsqlinstance) 및 [Set-AzSqlInstance 를 참조하세요.](/powershell/module/az.sql/set-azsqlinstance) 그런 다음, 사용자는 Azure AD 관리자 [프로비전(SQL Managed Instance)](/azure/azure-sql/database/authentication-aad-configure#provision-azure-ad-admin-sql-managed-instance)에 따라 인스턴스에 대한 Azure AD 관리자를 설정할 수 있습니다.

모든 인스턴스의 전체 스토리지 할당과 데이터베이스의 수는 인스턴스 풀에서 노출하는 제한과 같거나 작아야 합니다.

- 인스턴스 풀은 8, 16, 24, 32, 40, 64, 80개의 vCore를 지원합니다.
- 풀 내의 관리형 인스턴스는 2, 4, 8, 16, 24, 32, 40, 64, 80개의 vCore를 지원합니다.
- 풀 내의 관리형 인스턴스는 다음을 제외하고 32GB ~ 8TB의 스토리지 크기를 지원합니다.
  - 2 vCore 인스턴스는 32GB~640GB의 크기를 지원합니다.
  - 4 vCore 인스턴스는 32GB~2TB의 크기를 지원합니다.
- 풀 내의 관리형 인스턴스는 인스턴스당 최대 50개의 사용자 데이터베이스를 지원하는 vCore 인스턴스 2개를 제외하고 인스턴스당 최대 100개의 사용자 데이터베이스로 제한됩니다.

[서비스 계층 속성](resource-limits.md#service-tier-characteristics)은 인스턴스 풀 리소스와 연결되므로 풀의 모든 인스턴스는 풀의 서비스 계층과 동일한 서비스 계층이어야 합니다. 현재는 범용 서비스 계층만 사용할 수 있습니다(현재 미리 보기의 제한 사항에 대해서는 다음 섹션 참조).

### <a name="public-preview-limitations"></a>공용 미리 보기 제한 사항

공용 미리 보기에는 다음과 같은 제한 사항이 적용됩니다.

- 현재는 범용 서비스 계층만 사용할 수 있습니다.
- 퍼블릭 미리 보기 중에는 인스턴스 풀을 확장할 수 없으므로 배포하기 전에 신중하게 용량을 계획하는 것이 중요합니다.
- 인스턴스 풀 만들기 및 구성에 대한 Azure Portal 지원은 아직 제공되지 않습니다. 인스턴스 풀의 모든 작업은 PowerShell을 통해서만 지원됩니다. 미리 만든 풀의 초기 인스턴스 배포도 PowerShell을 통해서만 지원됩니다. 풀에 배포된 후에는 Azure Portal을 사용하여 관리형 인스턴스를 업데이트할 수 있습니다.
- 풀 외부에서 만들어진 관리형 인스턴스는 기존 풀로 이동할 수 없으며 풀 내부에서 만들어진 인스턴스는 단일 인스턴스나 다른 풀 외부로 이동할 수 없습니다.
- [예약 용량](../database/reserved-capacity-overview.md) 인스턴스 가격 책정 기능은 제공되지 않습니다.

## <a name="sql-features-supported"></a>지원되는 SQL 기능

풀에서 만들어진 관리형 인스턴스는 단일 관리형 인스턴스에서 지원되는 것과 동일한 [호환성 수준 및 기능을 지원합니다](sql-managed-instance-paas-overview.md#sql-features-supported).

풀에 배포된 모든 관리형 인스턴스에는 SQL 에이전트의 개별 인스턴스가 있습니다.

특정 값을 선택해야 하는 선택적 기능(예: 인스턴스 수준 데이터 정렬, 표준 시간대, 데이터 트래픽에 대한 퍼블릭 엔드포인트, 장애 조치(failover) 그룹)은 인스턴스 수준에서 구성되며 풀의 각 인스턴스마다 다를 수 있습니다.

## <a name="performance-considerations"></a>성능 고려 사항

풀 내의 관리형 인스턴스에는 전용 vCore 및 RAM이 있지만 로컬 디스크(tempdb 사용) 및 네트워크 리소스를 공유합니다. 드물지만 풀의 여러 인스턴스가 동시에 많은 리소스를 소비하는 경우 *시끄러운 이웃* 효과가 나타날 수 있습니다. 이 동작이 보인다면 해당 인스턴스를 더 큰 풀이나 단일 인스턴스로 배포하는 것이 좋습니다.

## <a name="security-considerations"></a>보안 고려 사항

풀에 배포된 인스턴스는 동일한 가상 머신을 공유하므로 더 높은 보안 위험을 야기하는 기능을 사용하지 않도록 설정하거나 이러한 기능에 대한 액세스 권한을 확실하게 제어하는 것이 좋습니다. 예를 들어 CLR 통합, 네이티브 백업 및 복원, 데이터베이스 이메일 등이 있습니다.

## <a name="instance-pool-support-requests"></a>인스턴스 풀 지원 요청

[Azure Portal](https://portal.azure.com)에서 인스턴스 풀에 대한 지원 요청을 만들고 관리합니다.

인스턴스 풀 배포(만들기 또는 삭제)와 관련된 문제가 발생하는 경우 **문제 하위 유형** 필드에 **인스턴스 풀** 을 지정해야 합니다.

![인스턴스 풀 지원 요청](./media/instance-pools-overview/support-request.png)

풀 내의 단일 관리형 인스턴스 또는 데이터베이스와 관련된 문제가 발생하는 경우 Azure SQL Managed Instance에 대한 일반 지원 티켓을 만들어야 합니다.

인스턴스 풀을 사용하거나 사용하지 않고 더 큰 SQL Managed Instance 배포를 만들려면 더 큰 지역 할당량을 얻어야 할 수 있습니다. 자세한 내용은 [Azure SQL Database에 대한 할당량 증가 요청](../database/quota-increase-request.md)을 참조하세요. 인스턴스 풀에 대한 배포 논리는 *풀 수준* 에서 총 vCore 사용량을 할당량에 비교하여 할당량을 추가로 늘리지 않고 새 리소스를 만들 수 있는지 여부를 확인합니다.

## <a name="instance-pool-billing"></a>인스턴스 풀 청구

인스턴스 풀을 사용하면 컴퓨팅과 스토리지를 독립적으로 확장할 수 있습니다. 고객은 vCore로 측정되는 풀 리소스와 연결된 컴퓨팅, 그리고 기가바이트로 측정되는 모든 인스턴스와 연결된 스토리지에 대한 요금을 지불합니다(처음 32GB는 모든 인스턴스에 대해 무료로 제공됨).

풀에 대한 vCore 가격은 해당 풀에 배포되는 인스턴스 수에 관계없이 청구됩니다.

컴퓨팅 가격(vCore로 측정)의 경우 두 가지 가격 책정 옵션을 사용할 수 있습니다.

  1. *라이선스 포함*: SQL Server 라이선스 가격이 포함됩니다. Software Assurance가 있는 기존 SQL Server 라이선스를 적용하지 않도록 선택하는 고객을 위한 것입니다.
  2. *Azure 하이브리드 혜택*: SQL Server에 대한 Azure 하이브리드 혜택이 포함된 할인 가격입니다. 고객은 Software Assurance가 있는 기존 SQL Server 라이선스를 사용하여 이 가격을 옵트인할 수 있습니다. 자격 및 기타 세부 정보는 [Azure 하이브리드 혜택](https://azure.microsoft.com/pricing/hybrid-benefit/)을 참조 하세요.

풀의 개별 인스턴스에 대해 다른 가격 책정 옵션을 설정할 수 없습니다. 부모 풀의 모든 인스턴스는 라이선스 포함 가격 또는 Azure 하이브리드 혜택 가격 중 하나여야 합니다. 풀이 만들어진 후에 풀의 라이선스 모델을 변경할 수 있습니다.

> [!IMPORTANT]
> 풀에서와 다른 인스턴스에 대한 라이선스 모델을 지정하는 경우 풀 가격이 사용되며 인스턴스 수준 값은 무시됩니다.

[개발-테스트 혜택을 받을 자격이 있는 구독](https://azure.microsoft.com/pricing/dev-test/)에서 인스턴스 풀을 만드는 경우 Azure SQL Managed Instance에 대해 최대 55%의 할인된 요금이 자동으로 수신됩니다.

인스턴스 풀 가격 책정에 대한 자세한 내용은 [SQL Managed Instance 가격 책정](https://azure.microsoft.com/pricing/details/sql-database/managed/) 페이지의 *인스턴스 풀* 섹션을 참조하세요.

## <a name="next-steps"></a>다음 단계

- 인스턴스 풀을 시작하려면 [SQL Managed Instance 풀 방법 가이드](instance-pools-configure.md)를 참조하세요.
- 첫 번째 Managed Instance를 만드는 방법을 알아보려면 [빠른 시작 가이드](instance-create-quickstart.md)를 참조하세요.
- 기능 및 비교 목록은 [Azure SQL 일반 기능](../database/features-comparison.md)을 참조하세요.
- VNet 구성에 대한 자세한 내용은 [SQL Managed Instance VNet 구성](connectivity-architecture-overview.md)을 참조하세요.
- 백업 파일에서 관리형 인스턴스를 만들고 데이터베이스를 복원하는 방법에 대한 빠른 시작은 [관리형 인스턴스 만들기](instance-create-quickstart.md)를 참조하세요.
- Azure Database Migration Service를 사용하여 마이그레이션하는 방법에 대한 자습서는 [Database Migration Service를 사용한 SQL Managed Instance 마이그레이션](../../dms/tutorial-sql-server-to-managed-instance.md)을 참조하세요.
- 기본 제공 문제 해결 인텔리전스를 사용하는 SQL Managed Instance 데이터베이스의 고급 성능 모니터링에 대해 자세히 알아보려면 [Azure SQL 분석을 사용하여 Azure SQL Managed Instance 모니터링](../../azure-monitor/insights/azure-sql.md)을 참조하세요.
- 가격 정보는 [SQL Managed Instance 가격](https://azure.microsoft.com/pricing/details/sql-database/managed/)을 참조하세요.
