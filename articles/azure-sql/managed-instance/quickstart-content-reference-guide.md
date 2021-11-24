---
title: 시작 콘텐츠 참조
titleSuffix: Azure SQL Managed Instance
description: 'Azure SQL Managed Instance를 시작하는 데 도움이 되는 콘텐츠에 대한 참조입니다. '
services: sql-database
ms.service: sql-managed-instance
ms.subservice: deployment-configuration
ms.custom: sqldbrb=1, mode-other
ms.devlang: ''
ms.topic: quickstart
author: davidtrigano
ms.author: datrigan
ms.reviewer: vanto, mathoma
ms.date: 07/11/2019
ms.openlocfilehash: 4bc5d6558a6c4eddb1d5c3157e05dcf8d54ee70d
ms.sourcegitcommit: 56235f8694cc5f88db3afcc8c27ce769ecf455b0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/24/2021
ms.locfileid: "133054250"
---
# <a name="getting-started-with-azure-sql-managed-instance"></a>Azure SQL Managed Instance 시작
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

[Azure SQL Managed Instance](sql-managed-instance-paas-overview.md)는 최신 SQL Server(Enterprise Edition) 데이터베이스 엔진과 거의 100% 호환되는 데이터베이스를 만들며, 일반적인 보안 문제를 해결하는 원시 [VNet(가상 네트워크)](../../virtual-network/virtual-networks-overview.md) 구현과 기존 SQL Server 고객에게 친숙한 [비즈니스 모델](https://azure.microsoft.com/pricing/details/sql-database/)을 제공합니다.

이 문서에서는 SQL Managed Instance를 신속하게 구성 및 생성하고 데이터베이스를 마이그레이션하는 방법을 알려 주는 콘텐츠에 대한 참조를 찾을 수 있습니다.

## <a name="quickstart-overview"></a>빠른 시작 개요

다음 빠른 시작에서는 SQL Managed Instance를 빠르게 만들고, 클라이언트 애플리케이션에 가상 머신 또는 지점과 사이트 간 VPN 연결을 구성하고, `.bak` 파일을 사용하여 새로운 SQL Managed Instance로 데이터베이스를 복원하도록 할 수 있습니다.

### <a name="configure-environment"></a>환경 구성

첫 번째 단계에서는 첫 번째 SQL Managed Instance가 배치될 네트워크 환경에서 해당 인스턴스를 만들어야 하며, SQL Managed Instance에 대한 쿼리를 실행하는 컴퓨터 또는 가상 머신에서 연결을 사용하도록 설정해야 합니다. 다음 지침을 사용할 수 있습니다.

- [Azure Portal을 사용하여 SQL Managed Instance 만들기](instance-create-quickstart.md). Azure Portal에서 필요한 매개 변수(사용자 이름/암호, 코어 수, 최대 스토리지 용량)를 구성하고, 네트워킹 세부 정보 및 인프라 요구 사항에 대해 알아볼 필요 없이 Azure 네트워크 환경을 자동으로 만들 수 있습니다. 현재 SQL Managed Instance를 만들도록 허용된 [구독 유형](resource-limits.md#supported-subscription-types)이 있는지 확인합니다. 사용하려는 고유한 네트워크가 있거나 네트워크를 사용자 지정하려는 경우 [Azure SQL Managed Instance의 기존 가상 네트워크 구성](vnet-existing-add-subnet.md) 또는 [Azure SQL Managed Instance의 가상 네트워크 만들기](virtual-network-subnet-create-arm-template.md)를 참조하세요.
- SQL Managed Instance는 퍼블릭 엔드포인트를 사용하지 않고 자체 VNet에 생성됩니다. 클라이언트 애플리케이션 액세스의 경우 **동일한 VNet(여러 서브넷)에 VM을 만들거나** 다음과 같은 빠른 시작 중 하나를 사용하여 **클라이언트 컴퓨터에서 VNet에 대한 지점과 사이트 간 VPN 연결** 을 만들 수 있습니다.
  - 사용자 환경에서 직접 데이터에 액세스하기 위해 SQL Managed Instance에 [퍼블릭 엔드포인트](public-endpoint-configure.md)를 사용하도록 설정합니다.
  - 클라이언트 애플리케이션 연결에 사용할 [SQL Managed Instance VNet의 Azure Virtual Machine](connect-vm-instance-configure.md)(SQL Server Management Studio 포함)을 만듭니다.
  - SQL Server Management Studio 및 기타 클라이언트 연결 애플리케이션이 있는 클라이언트 컴퓨터에서 [SQL Managed Instance에 대한 지점과 사이트 간 VPN 연결](point-to-site-p2s-configure.md)을 설정합니다. 이는 SQL Managed Instance 및 VNet 연결에 대한 두 가지 옵션 중 하나입니다.

  > [!NOTE]
  > - 또한 로컬 네트워크에서 사이트 간 연결 또는 Express 경로를 사용할 수 있지만 이러한 방법은 이 빠른 시작의 범위를 벗어납니다.
  > - 보존 기간을 0(무제한 보존)에서 다른 값으로 변경하는 경우 보존 값이 변경된 후에 작성된 로그에만 보존이 적용됩니다(보존이 무제한으로 설정된 기간 동안 작성된 로그는 보존이 활성화된 후에도 보존됨).

SQL Managed Instance를 수동으로 만드는 대신, [PowerShell](scripts/create-configure-managed-instance-powershell.md), [Resource Manager 템플릿이 있는 PowerShell](./create-template-quickstart.md) 또는 [Azure CLI](/cli/azure/sql/mi#az_sql_mi_create)를 사용하여 이 프로세스를 스크립트로 작성하고 자동화할 수 있습니다.

### <a name="migrate-your-databases"></a>데이터베이스 마이그레이션

SQL Managed Instance를 만들고 액세스를 구성한 후 SQL Server 데이터베이스 마이그레이션을 시작할 수 있습니다. 마이그레이션할 원본 데이터베이스에 지원되지 않는 기능이 있으면 마이그레이션이 실패할 수 있습니다. 실패를 방지하고 호환성을 확인하려면 [DMA(Data Migration Assistant)](https://www.microsoft.com/download/details.aspx?id=53595)를 사용하여 SQL Server의 데이터베이스를 분석하고 SQL Managed Instance로의 마이그레이션을 차단할 수 있는 문제(예: 여러 로그 파일 또는 [FileStream](/sql/relational-databases/blob/filestream-sql-server) 존재)를 찾을 수 있습니다. 이러한 문제를 해결하면 데이터베이스를 SQL Managed Instance로 마이그레이션할 수 있습니다. [데이터베이스 실험 도우미](/sql/dea/database-experimentation-assistant-overview)는 또 다른 유용한 도구입니다. SQL Server에 워크로드를 기록하고 SQL Managed Instance에서 재생할 수 있기 때문에 SQL Managed Instance로 마이그레이션할 때 성능 문제가 있는지 확인할 수 있습니다.

데이터베이스를 SQL Managed Instance로 마이그레이션할 수 있다는 확신이 들면 원시 SQL Server 복원 기능을 사용하여 `.bak` 파일에서 SQL Managed Instance로 데이터베이스를 복원할 수 있습니다. 온-프레미스 또는 Azure Virtual Machines에 설치된 SQL Server 데이터베이스 엔진에서 데이터베이스를 마이그레이션하려면 이 메서드를 사용할 수 있습니다. 빠른 시작의 경우 [백업에서 SQL Managed Instance로 복원](restore-sample-database-quickstart.md)을 참조하세요. 이 빠른 시작에서는 `RESTORE` Transact-SQL 명령을 사용하여 Azure Blob Storage에 저장된 `.bak` 파일로 복원합니다.

> [!TIP]
> `BACKUP` Transact-SQL 명령을 사용하여 Azure Blob Storage에 데이터베이스 백업을 만들려면 [URL에 SQL Server 백업](/sql/relational-databases/backup-restore/sql-server-backup-to-url)을 참조하세요.

이 빠른 시작을 사용하면 데이터베이스 백업을 신속하게 만들고, 구성하고, SQL Managed Instance로 복원할 수 있습니다. 일부 시나리오에서는 SQL Managed Instance 배포와 필요한 네트워킹 환경을 사용자 지정하거나 자동화해야 합니다. 이러한 시나리오는 아래 설명되어 있습니다.

## <a name="customize-network-environment"></a>네트워크 환경 사용자 지정

[Azure Portal](instance-create-quickstart.md)을 사용하여 인스턴스를 만드는 경우 VNet/서브넷을 자동으로 구성할 수 있지만, VNet과 서브넷의 매개 변수를 구성할 수 있기 때문에 SQL Managed Instance의 인스턴스를 만들기 전에 인스턴스를 만드는 것이 좋습니다. 네트워크 환경을 만들고 구성하는 가장 쉬운 방법은 인스턴스가 배치되는 곳에 네트워크와 서브넷을 만들고 구성하는 [Azure Resource 배포](virtual-network-subnet-create-arm-template.md) 템플릿을 사용하는 것입니다. Azure Resource Manager 배포 단추를 누르고 양식에 매개 변수를 입력하기만 하면 됩니다.

또는 [PowerShell 스크립트](https://www.powershellmagazine.com/2018/07/23/configuring-azure-environment-to-set-up-azure-sql-database-managed-instance-preview/)를 사용하여 네트워크 만들기를 자동화할 수도 있습니다.

SQL Managed Instance를 배포하려는 곳에 VNet과 서브넷이 이미 있는 경우에는 VNet과 서브넷이 [네트워킹 요구 사항](connectivity-architecture-overview.md#network-requirements)을 충족하는지 확인해야 합니다. 이 [PowerShell 스크립트를 사용하여 서브넷이 제대로 구성되어 있는지 확인](vnet-existing-add-subnet.md)하세요. 이 스크립트는 네트워크 유효성을 검사하여 문제를 보고할 뿐만 아니라 변경해야 하는 항목을 알려주고 VNet/서브넷에서 필요한 사항을 변경하도록 제안합니다. VNet/서브넷을 수동으로 구성하지 않으려는 경우 이 스크립트를 실행하세요. 또한 네트워크 인프라의 모든 주요 재구성 후 실행할 수도 있습니다. 자체 네트워크를 만들고 구성하려면 [연결 아키텍처](connectivity-architecture-overview.md) 및 [SQL Managed Instance 환경 생성 및 구성을 위한 최고의 가이드](https://medium.com/azure-sqldb-managed-instance/the-ultimate-guide-for-creating-and-configuring-azure-sql-managed-instance-environment-91ff58c0be01)를 읽어 보세요.

## <a name="migrate-to-a-sql-managed-instance"></a>SQL Managed Instance로 마이그레이션

이전에 언급한 빠른 시작을 참고하여 SQL Managed Instance를 신속하게 설정하고 원시 `RESTORE` 기능을 사용하여 데이터베이스를 이동할 수 있습니다. 빠른 개념 증명을 완료하고 솔루션이 관리되는 인스턴스에서 작동할 수 있는지 확인하려는 경우 이 방법부터 시작하면 좋습니다.

그러나 일부 성능 테스트에 사용하려는 프로덕션 데이터베이스 또는 개발/테스트 데이터베이스를 마이그레이션하려면 다음과 같은 몇 가지 추가 기술을 사용하는 것을 고려해야 합니다.

- 성능 테스트 - 원본 SQL Server 인스턴스에서 기준 성능 메트릭을 측정하고 데이터베이스가 마이그레이션된 대상인 SQL Managed Instance의 성능 메트릭과 비교해야 합니다. [성능 비교 모범 사례](https://techcommunity.microsoft.com/t5/Azure-SQL-Database/The-best-practices-for-performance-comparison-between-Azure-SQL/ba-p/683210)에 대해 자세히 알아봅니다.
- 온라인 마이그레이션 - 이 문서에 설명된 네이티브 `RESTORE`를 사용할 경우 데이터베이스가 복원될 때까지(그리고 Azure Blob Storage에 저장되지 않은 경우에는 복사될 때까지) 기다려야 합니다. 이렇게 하면 특히 대규모 데이터베이스에서 애플리케이션의 가동 중지 시간이 발생합니다. 프로덕션 데이터베이스를 이동하려면 [DMS(Data Migration Service)](../../dms/tutorial-sql-server-to-managed-instance.md?toc=%2fazure%2fsql-database%2ftoc.json)를 사용하여 최소 가동 중지 시간으로 데이터베이스를 마이그레이션하세요. DMS는 복원 중인 SQL Managed Instance 데이터베이스에 원본 데이터베이스의 변경 사항을 점진적으로 보냄으로써 데이터베이스를 이동합니다. 이 방식을 사용하면 가동 중지 시간을 최소화하면서 원본에서 대상 데이터베이스로 애플리케이션을 신속하게 전환할 수 있습니다.

[권장 마이그레이션 프로세스](../migration-guides/managed-instance/sql-server-to-managed-instance-guide.md)에 대해 자세히 알아봅니다.

## <a name="next-steps"></a>다음 단계

- [SQL Managed Instance에서 지원되는 기능에 대한 개략적인 목록](../database/features-comparison.md)과 [세부 정보 및 알려진 문제](transact-sql-tsql-differences-sql-server.md)를 확인합니다.
- [SQL Managed Instance의 기술적인 특성](resource-limits.md#service-tier-characteristics)에 대해 알아봅니다.
- [SQL Managed Instance를 사용하는 방법](how-to-content-reference-guide.md)에서 더욱 자세한 작업 가이드를 확인합니다.
- [온-프레미스 데이터베이스에 적합한 Azure SQL Managed Instance SKU를 파악합니다](/sql/dma/dma-sku-recommend-sql-db/).
