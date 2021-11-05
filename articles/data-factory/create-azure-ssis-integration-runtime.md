---
title: Azure Data Factory에서 Azure Integration Runtime 만들기 | Microsoft Docs
description: Azure에 SSIS 패키지를 배포하고 실행할 수 있도록 Azure Data Factory에서 Azure-SSIS 통합 런타임을 만드는 방법을 알아봅니다.
ms.service: data-factory
ms.subservice: integration-services
ms.topic: conceptual
ms.date: 10/22/2021
author: swinarko
ms.author: sawinark
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 70e745d18699c6a1acb707528ec38641a1fe7a02
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/05/2021
ms.locfileid: "131842894"
---
# <a name="create-an-azure-ssis-integration-runtime-in-azure-data-factory"></a>Azure Data Factory에서 Azure Integration Runtime 만들기 | Microsoft Docs

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

이 문서에서는 ADF(Azure Data Factory)에서 Azure-SSIS(SQL Server Integration Services) IR(Integration Runtime)을 프로비저닝하는 절차를 설명합니다. Azure-SSIS IR에서 지원하는 작업은 다음과 같습니다.

- Azure SQL Database 서버/Managed Instance(프로젝트 배포 모델)가 호스트하는 SSIS 카탈로그(SSISDB)에 배포된 패키지 실행
- Azure SQL Managed Instance(패키지 배포 모델)가 호스트하는 파일 시스템, Azure Files 또는 SQL Server 데이터베이스(MSDB)에 배포된 패키지 실행

Azure-SSIS IR이 프로비저닝되면 익숙한 도구를 사용하여 Azure에서 패키지를 배포하고 실행할 수 있습니다. 이러한 도구는 이미 Azure를 사용하며 SSDT(SQL Server Data Tools), SSMS(SQL Server Management Studio) 및 명령줄 유틸리티(예: [dtutil](/sql/integration-services/dtutil-utility) 및 [AzureDTExec](./how-to-invoke-ssis-package-azure-enabled-dtexec.md))를 포함하고 있습니다.

[Azure-SSIS IR 프로비저닝](./tutorial-deploy-ssis-packages-azure.md) 자습서에서는 Azure Portal 또는 Data Factory 앱을 통해 Azure-SSIS IR을 만드는 방법을 설명합니다. 또한 선택에 따라 Azure SQL Database 서버 또는 관리되는 인스턴스를 사용하여 SSISDB를 호스트하는 방법도 설명합니다. 이 문서는 자습서에서 다루는 것에 더해 다음과 같은 선택적 작업을 수행하는 방법도 설명합니다.

- IP 방화벽 규칙/가상 네트워크 서비스 엔드포인트가 있는 Azure SQL Database 서버 또는 프라이빗 엔드포인트가 있는 관리형 인스턴스를 사용하여 SSISDB를 호스트합니다. 필수 조건으로 Azure-SSIS IR의 가상 네트워크 사용 권한과 설정을 가상 네트워크에 조인하도록 구성해야 합니다.

- 데이터 팩터리의 지정한 시스템/사용자가 할당한 관리 ID로 Azure AD(Azure Active Directory) 인증을 사용하여 Azure SQL Database 서버 또는 관리형 인스턴스에 연결합니다. 필수 조건으로, SSISDB 인스턴스를 만들 수 있는 데이터베이스 사용자로서 데이터 팩터리의 지정한 시스템/사용자가 할당한 관리 ID를 추가해야 합니다.

- Azure-SSIS IR을 가상 네트워크에 조인하거나, Azure-SSIS IR에서 온-프레미스 데이터에 액세스하도록 자체 호스팅 IR을 프록시로 구성합니다.

이러한 문서에서는 [Azure Portal](create-azure-ssis-integration-runtime-portal.md), [Azure PowerShell](create-azure-ssis-integration-runtime-powershell.md)및 [Azure Resource Manager 템플릿을](create-azure-ssis-integration-runtime-resource-manager-template.md)사용 하 여 Azure-SSIS IR를 프로 비전 하는 방법을 보여 줍니다.

## <a name="prerequisites"></a>필수 구성 요소

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

- **Azure 구독**. 구독이 없는 경우 [평가판](https://azure.microsoft.com/pricing/free-trial/) 계정을 만들 수 있습니다.

- **Azure SQL Database 서버 또는 SQL 관리형 인스턴스(선택 사항)** . 데이터베이스 서버 또는 관리형 인스턴스가 아직 없는 경우 시작하기 전에 Azure Portal에서 데이터베이스 서버를 만듭니다. 그러면 Data Factory에서 SSISDB 인스턴스를 이 데이터베이스 서버에 만듭니다. 

  통합 런타임과 동일한 Azure 지역에 데이터베이스 서버 또는 관리형 인스턴스를 만드는 것이 좋습니다. 이 구성을 사용하면 통합 런타임에서 Azure 지역을 벗어나지 않고 SSISDB에 실행 로그를 쓸 수 있습니다.

  다음 사항에 유의하세요.

  - 사용자를 대신하여 SSISDB 인스턴스를 단일 데이터베이스, 탄력적 풀의 일부 또는 관리형 인스턴스로 만들 수 있습니다. 이는 공용 네트워크에서 액세스하거나 가상 네트워크에 조인하여 액세스할 수 있습니다. SSISDB를 호스트하기 위해 SQL Database와 SQL Managed Instance 중 무엇을 선택해야 하는지에 대한 지침은 이 문서의 [SQL Database 및 SQL Managed Instance 비교](#comparison-of-sql-database-and-sql-managed-instance) 섹션을 참조하세요. 
  
    IP 방화벽 규칙/가상 네트워크 서비스 엔드포인트가 있는 Azure SQL Database 서버 또는 프라이빗 엔드포인트가 있는 SQL 관리형 인스턴스를 사용하여 SSISDB를 호스트하거나 자체 호스팅 IR을 구성하지 않고 온-프레미스 데이터에 액세스해야 하는 경우, Azure-SSIS IR을 가상 네트워크에 조인해야 합니다. 자세한 내용은 [가상 네트워크에 Azure-SSIS IR 조인하기](./join-azure-ssis-integration-runtime-virtual-network.md)를 참조하세요.

  - 데이터베이스 서버에 대해 **Azure 서비스 방문 허용** 설정을 사용하도록 설정되어 있는지 확인합니다. IP 방화벽 규칙/가상 네트워크 서비스 엔드포인트가 있는 Azure SQL Database 서버 또는 프라이빗 엔드포인트가 있는 SQL 관리형 인스턴스를 사용하여 SSISDB를 호스트하는 경우에는 이 설정이 적용되지 않습니다. 자세한 내용은 [Azure SQL Database 보호](../azure-sql/database/secure-database-tutorial.md#create-firewall-rules)를 참조하세요. PowerShell을 사용하여 이 설정을 사용하려면 [New-AzSqlServerFirewallRule](/powershell/module/az.sql/new-azsqlserverfirewallrule)을 참조하세요.

  - 클라이언트 머신의 IP 주소 또는 이러한 주소가 포함된 IP 주소의 범위를 데이터베이스 서버에 대한 방화벽 설정의 클라이언트 IP 주소 목록에 추가합니다. 자세한 내용은 [Azure SQL Database 서버 수준 및 데이터베이스 수준 방화벽 규칙 구성](../azure-sql/database/firewall-configure.md)을 참조하세요.

  - 서버 관리자 자격 증명으로 SQL 인증을 사용하여 데이터베이스 서버에 연결하거나 데이터 팩터리에 지정한 시스템/사용자가 할당한 관리 ID로 Azure AD 인증을 사용하여 데이터베이스 서버에 연결할 수 있습니다. 후자의 경우 데이터 팩터리에 지정한 시스템/사용자가 할당한 관리 ID를 데이터베이스 서버에 대한 액세스 권한이 있는 Azure AD 그룹에 추가해야 합니다. 자세한 내용은 [Azure-SSIS IR에 Azure AD 인증 사용](./enable-aad-authentication-azure-ssis-ir.md)을 참조하세요.

  - 데이터베이스 서버에 SSISDB 인스턴스가 아직 없는지 확인합니다. Azure-SSIS IR 프로비저닝은 기존 SSISDB 인스턴스 사용을 지원하지 않습니다.

- **Azure Resource Manager 가상 네트워크(선택 사항)**. 다음 조건 중 하나 이상에 해당하는 경우 Azure Resource Manager 가상 네트워크가 있어야 합니다.

  - IP 방화벽 규칙/가상 네트워크 서비스 엔드포인트가 있는 Azure SQL Database 서버 또는 프라이빗 엔드포인트가 있는 관리형 인스턴스에 SSISDB를 호스트합니다.

  - 자체 호스팅 IR을 구성하지 않고 Azure-SSIS IR에서 실행되는 SSIS 패키지에서 온-프레미스 데이터 저장소에 연결해야 합니다.

- **Azure PowerShell(선택 사항)** . PowerShell 스크립트를 실행하여 Azure-SSIS IR을 프로비저닝하려는 경우 [Azure PowerShell 설치 및 구성 방법](/powershell/azure/install-az-ps)의 지침을 따릅니다.

### <a name="regional-support"></a>지역 지원

Data Factory 및 Azure-SSIS IR을 사용할 수 있는 Azure 지역의 목록은 [지역별 사용 가능한 Data Factory 및 SSIS IR](https://azure.microsoft.com/global-infrastructure/services/?products=data-factory&regions=all)을 참조하세요.

### <a name="comparison-of-sql-database-and-sql-managed-instance"></a>SQL Database와 SQL Managed Instance 비교

다음 표에서는 Azure-SSIR IR과 관련된 SQL Database 서버 및 SQL Managed Instance의 특정 기능을 비교합니다.

| 기능 | SQL Database| SQL Managed Instance |
|---------|--------------|------------------|
| **일정 예약** | SQL Server 에이전트를 사용할 수 없습니다.<br/><br/>[Data Factory 파이프라인에서 패키지 실행 예약](/sql/integration-services/lift-shift/ssis-azure-schedule-packages#activity)을 참조하세요.| Managed Instance 에이전트를 사용할 수 있습니다. |
| **인증** | 데이터 팩터리의 관리 ID를 사용하여 Azure AD 그룹을 **db_owner** 역할의 구성원으로 나타내는 포함된 데이터베이스 사용자로 SSISDB를 만들 수 있습니다.<br/><br/>[Azure SQL Database 서버의 SSISDB 생성을 위한 Azure AD 인증 활성화](enable-aad-authentication-azure-ssis-ir.md#enable-azure-ad-authentication-on-azure-sql-database)를 참조하세요. | 데이터 팩터리의 관리 ID를 나타내는 포함된 데이터베이스가 있는 SSISDB 인스턴스를 만들 수 있습니다. <br/><br/>[Azure SQL Managed Instance의 SSISDB 생성을 위한 Azure AD 인증 활성화](enable-aad-authentication-azure-ssis-ir.md#enable-azure-ad-authentication-on-azure-sql-managed-instance)를 참조하세요. |
| **서비스 계층** | Azure SQL Database 서버를 통해 Azure-SSIS IR을 만들 때 SSISDB의 서비스 계층을 선택할 수 있습니다. 여러 서비스 계층이 있습니다. | 관리형 인스턴스로 Azure-SSIS IR을 만들 때는 SSISDB의 서비스 계층을 선택할 수 없습니다. 관리형 인스턴스의 모든 데이터베이스는 해당 인스턴스에 할당된 동일한 리소스를 공유합니다. |
| **가상 네트워크** | IP 방화벽 규칙/가상 네트워크 서비스 엔드포인트에 Azure SQL Database 서버를 사용하는 경우 Azure-SSIS IR이 Azure Resource Manager 가상 네트워크에 조인할 수 있습니다. | 프라이빗 엔드포인트에 관리형 인스턴스를 사용하는 경우 Azure-SSIS IR이 Azure Resource Manager 가상 네트워크에 조인할 수 있습니다. 관리형 인스턴스에 퍼블릭 엔드포인트를 사용하지 않는 경우 가상 네트워크가 필요합니다.<br/><br/>Azure-SSIS IR을 관리형 인스턴스와 동일한 가상 네트워크에 조인하는 경우 Azure-SSIS IR은 관리형 인스턴스와 다른 서브넷에 있어야 합니다. Azure-SSIS IR을 관리형 인스턴스와 다른 가상 네트워크에 조인하는 경우에는 가상 네트워크 피어링 또는 가상 네트워크 간 연결을 사용하는 것이 좋습니다. [애플리케이션을 Azure SQL Database Managed Instance에 연결](../azure-sql/managed-instance/connect-application-instance.md)을 참조하세요. |
| **분산 트랜잭션** | 이 기능은 탄력적 트랜잭션을 통해 지원됩니다. MSDTC(Microsoft Distributed Transaction Coordinator) 트랜잭션은 지원되지 않습니다. SSIS 패키지가 MSDTC를 사용하여 분산형 트랜잭션을 조정하는 경우 Azure SQL Database용 탄력적 트랜잭션으로 마이그레이션하는 것을 고려하세요. 자세한 내용은 [클라우드 데이터베이스의 분산 트랜잭션](../azure-sql/database/elastic-transactions-overview.md)을 참조하세요. | 지원 안 됨 |
| | | |


## <a name="next-steps"></a>다음 단계

- [Azure Portal를 사용 하 여 Azure-SSIS IR를 프로 비전 하는 방법을 알아봅니다](create-azure-ssis-integration-runtime-portal.md).
- [Azure PowerShell를 사용 하 여 Azure-SSIS IR를 프로 비전 하는 방법을 알아봅니다](create-azure-ssis-integration-runtime-powershell.md).
- [Azure Resource Manager 템플릿을 사용 하 여 Azure-SSIS IR를 프로 비전 하는 방법을 알아봅니다](create-azure-ssis-integration-runtime-resource-manager-template.md).
- [Azure Data Factory에서 SSIS 패키지를 배포 하 고 실행](create-azure-ssis-integration-runtime-deploy-packages.md)합니다.

이 설명서의 다른 Azure-SSIS IR 항목을 참조하세요.

- [Azure-SSIS 통합 런타임](concepts-integration-runtime.md#azure-ssis-integration-runtime). 이 문서에서는 Azure-SSIS IR을 비롯한 일반적인 통합 런타임에 관한 정보를 제공합니다.
- [Azure-SSIS IR 모니터링](monitor-integration-runtime.md#azure-ssis-integration-runtime). 이 문서에서는 Azure SSIS IR에 대한 정보를 가져와 파악하는 방법을 설명합니다.
- [Azure-SSIS IR 관리](manage-azure-ssis-integration-runtime.md). 이 문서에서는 Azure-SSIS IR을 중지하거나, 시작하거나, 삭제하는 방법을 설명합니다. 또한 노드를 추가하여 Azure-SSIS IR 규모를 확장하는 방법도 설명합니다.
- [Azure에서 SSIS 패키지 배포, 실행 및 모니터링](/sql/integration-services/lift-shift/ssis-azure-deploy-run-monitor-tutorial)   
- [Azure에서 SSISDB에 연결](/sql/integration-services/lift-shift/ssis-azure-connect-to-catalog-database)
- [Windows 인증을 사용하는 온-프레미스 데이터 원본에 연결](/sql/integration-services/lift-shift/ssis-azure-connect-with-windows-auth) 
- [Azure에서 패키지 실행 예약](/sql/integration-services/lift-shift/ssis-azure-schedule-packages)
