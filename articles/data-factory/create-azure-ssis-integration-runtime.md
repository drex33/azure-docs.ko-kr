---
title: Azure Data Factory에서 Azure Integration Runtime 만들기 | Microsoft Docs
description: Azure에 SSIS 패키지를 배포하고 실행할 수 있도록 Azure Data Factory에서 Azure-SSIS 통합 런타임을 만드는 방법을 알아봅니다.
ms.service: data-factory
ms.subservice: integration-services
ms.topic: conceptual
ms.date: 07/19/2021
author: swinarko
ms.author: sawinark
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 012c5a2e32b0da48e5a1e2ad71050ca4c2e08ef8
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122528841"
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

이 문서에서는 Azure Portal, Azure PowerShell 및 Azure Resource Manager 템플릿을 사용하여 Azure-SSIS IR를 프로비전하는 방법을 설명합니다.

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

## <a name="use-the-azure-portal-to-create-an-integration-runtime"></a>Azure Portal을 사용하여 통합 런타임 만들기

이 섹션에서는 Azure Portal, 특히 Data Factory UI(사용자 인터페이스) 또는 앱을 사용하여 Azure-SSIS IR을 만듭니다.

### <a name="create-a-data-factory"></a>데이터 팩터리 만들기

Azure Portal을 통해 데이터 팩터리를 만들려면 [UI를 통해 데이터 팩터리 만들기](./quickstart-create-data-factory-portal.md#create-a-data-factory)의 단계별 지침을 따릅니다. 만든 후에 빠르게 액세스할 수 있도록 하려면 이 작업을 수행하면서 **대시 보드에 고정** 을 선택합니다. 

데이터 팩터리가 만들어지면 Azure Portal에서 개요 페이지를 엽니다. **작성자 및 모니터** 타일을 선택하여 별도의 탭에서 **시작하기** 페이지를 엽니다. 여기서 Azure-SSIS IR을 계속 만들 수 있습니다.   

### <a name="provision-an-azure-ssis-integration-runtime"></a>Azure-SSIS 통합 런타임 프로비전

홈페이지에서 **SSIS 구성** 타일을 선택하여 **통합 런타임 설정** 창을 엽니다.

   ![ADF 홈페이지를 보여 주는 스크린샷](./media/doc-common-process/get-started-page.png)

   **통합 런타임 설치** 창에는 일반, 배포 및 고급 설정을 연속으로 구성하는 세 개의 페이지가 있습니다.

#### <a name="general-settings-page"></a>일반 설정 페이지

**통합 런타임 설치** 창의 **일반 설정** 페이지에서 다음 단계를 완료합니다.

![일반 설정](./media/tutorial-create-azure-ssis-runtime-portal/general-settings.png)

1. **이름** 에는 통합 런타임의 이름을 입력합니다.

2. **설명** 에는 통합 런타임에 대한 설명을 입력합니다.

3. **위치** 에는 통합 런타임의 위치를 선택합니다. 지원되는 위치만 표시됩니다. SSISDB를 호스트하는 데이터베이스의 서버와 동일한 위치를 선택하는 것이 좋습니다.

4. **노드 크기** 는 통합 런타임 클러스터의 노드 크기를 선택합니다. 지원되는 노드 크기만 표시됩니다. 여러 개의 계산 집약적 또는 메모리 집약적 패키지를 실행하려면 큰 노드 크기(강화)를 선택합니다.

   > [!NOTE]
   > [컴퓨팅 격리](../azure-government/azure-secure-isolation-guidance.md#compute-isolation)가 필요한 경우 **Standard_E64i_v3** 노드 크기를 선택하세요. 이 노드 크기는 전체 실제 호스트를 사용하고 미 국방부의 Impact Level 5(IL5) 워크로드 같은 특정 워크로드에 필요한 격리 수준을 제공하는 격리된 가상 머신을 나타냅니다.
   
5. **노드 수** 로는 통합 런타임 클러스터의 노드 수를 선택합니다. 지원되는 노드 수만 표시됩니다. 여러 패키지를 동시에 실행하려면 노드 수가 많은 대형 클러스터(규모 확장)를 선택합니다.

6. **버전/라이선스** 의 경우 통합 런타임에 대한 SQL Server 버전, 즉 Standard 또는 Enterprise를 선택합니다. 통합 런타임에서 고급 기능을 사용하려면 Enterprise를 선택합니다.

7. **비용 절감** 의 경우 통합 런타임에 대한 AHB(Azure 하이브리드 혜택) 옵션에서 **예** 또는 **아니요** 를 선택합니다. Software Assurance를 통해 사용자 고유의 SQL Server 라이선스를 가져와서 하이브리드를 사용함으로써 비용을 절감하려면 **예** 를 선택합니다.

8. **계속** 을 선택합니다.

#### <a name="deployment-settings-page"></a>배포 설정 페이지

**통합 런타임 설치** 창의 **배포 설정** 페이지에서 SSISDB 및 또는 Azure-SSIS IR 패키지 저장소를 만드는 옵션이 있습니다.

##### <a name="creating-ssisdb"></a>SSISDB 생성

**통합 런타임 설정** 창의 **배포 설정** 페이지에서 SSISDB(프로젝트 배포 모델)에 패키지를 배포하려면 **프로젝트/패키지/환경/실행 로그를 저장하기 위해 Azure SQL Database 서버/Managed Instance가 호스트하는 SSIS 카탈로그(SSISDB) 만들기** 확인란을 선택합니다. 또는 Azure SQL Managed Instance(패키지 배포 모델)가 호스트하는 파일 시스템, Azure Files 또는 SQL Server 데이터베이스(MSDB)에 패키지를 배포하려는 경우에는 SSISDB를 만들거나 확인란을 선택할 필요가 없습니다.

배포 모델에 관계없이 Azure SQL Managed Instance가 호스트하는 SQL Server 에이전트를 사용하여 패키지 실행을 오케스트레이션/예약하려는 경우 SSISDB에서 사용하도록 설정되어 있으므로 이 확인란을 선택합니다. 자세한 내용은 [Azure SQL Managed Instance 에이전트를 통해 SSIS 패키지 실행 예약](./how-to-invoke-ssis-package-managed-instance-agent.md)을 참조하세요.
   
확인란을 선택한 경우 다음 단계를 완료하여 사용자를 대신하여 만들고 관리할 SSISDB를 호스트하는 사용자 고유의 데이터베이스 서버를 가져옵니다.

![SSISDB의 배포 설정](./media/tutorial-create-azure-ssis-runtime-portal/deployment-settings.png)
   
1. **구독** 에서는 SSISDB를 호스트하는 데이터베이스 서버가 있는 Azure 구독을 선택합니다. 

1. **위치** 에서는 SSISDB를 호스트하는 데이터베이스 서버의 위치를 선택합니다. 통합 런타임과 동일한 위치를 선택하는 것이 좋습니다.

1. **카탈로그 데이터베이스 서버 엔드포인트** 로는 SSISDB를 호스트하는 데이터베이스 서버의 엔드포인트를 선택합니다. 
   
   선택한 데이터베이스 서버에 따라 사용자를 대신하여 SSISDB 인스턴스를 단일 데이터베이스, 탄력적 풀의 일부 또는 관리형 인스턴스로 만들 수 있습니다. 이는 공용 네트워크에서 액세스하거나 가상 네트워크에 조인하여 액세스할 수 있습니다. SSISDB를 호스트할 데이터베이스 서버의 유형을 선택하는 방법에 대한 지침은 [SQL Database 및 SQL Managed Instance 비교](../data-factory/create-azure-ssis-integration-runtime.md#comparison-of-sql-database-and-sql-managed-instance)를 참조하세요.   

   IP 방화벽 규칙/가상 네트워크 서비스 엔드포인트가 있는 Azure SQL Database 서버 또는 프라이빗 엔드포인트가 있는 관리형 인스턴스를 선택하여 SSISDB를 호스팅하거나 자체 호스팅 IR을 구성하지 않고 온-프레미스 데이터에 액세스해야 하는 경우 Azure-SSIS IR을 가상 네트워크에 조인해야 합니다. 자세한 내용은 [가상 네트워크에 Azure-SSIS IR 조인하기](./join-azure-ssis-integration-runtime-virtual-network.md)를 참조하세요.
      
1. **데이터 팩터리의 시스템 관리 ID로 AAD 인증 사용** 또는 **데이터 팩터리의 사용자 할당 관리 ID로 AAD 인증 사용** 확인란을 선택하여 SSISDB를 호스트하는 데이터베이스에 액세스할 Azure-SSIS IR에 대한 Azure AD 인증 방법을 선택합니다. 대신 SQL 인증 방법을 선택하려면 아무 확인란도 선택하지 마세요.

   이 확인란 중 하나를 선택하면 데이터 팩터리의 지정된 시스템/사용자 할당 관리 ID를 데이터베이스 서버에 대한 액세스 권한이 있는 Azure AD 그룹에 추가해야 합니다. **데이터 팩터리의 사용자 할당 관리 ID로 AAD 인증 사용** 확인란을 선택하면 지정된 사용자 할당 관리 ID를 사용하여 만든 기존 자격 증명을 선택하거나 새 자격 증명을 만들 수 있습니다. 자세한 내용은 [Azure-SSIS IR에 Azure AD 인증 사용](./enable-aad-authentication-azure-ssis-ir.md)을 참조하세요.

1. **관리 사용자 이름** 에 대해 SSISDB를 호스트하는 데이터베이스 서버의 SQL 인증 사용자 이름을 입력합니다. 

1. **관리자 암호** 에 대해 SSISDB를 호스트하는 데이터베이스 서버의 SQL 인증 암호를 입력합니다. 

1. BCDR(비즈니스 연속성 및 재해 복구)을 위해 Azure SQL Database/Managed Instance 장애 조치(failover) 그룹과 동기화되는 이중 대기 Azure SSIS IR 쌍을 구성하려면 **SSISDB 장애 조치(failover)에서 이중 대기 Azure-SSIS 통합 런타임 쌍 사용** 확인란을 선택합니다.
   
   확인란을 선택하는 경우 **이중 대기 쌍 이름** 텍스트 상자에 기본 및 보조 Azure-SSIS IR 쌍을 식별하는 이름을 입력합니다. 기본 및 보조 Azure-SSIS IR을 만들 때 동일한 쌍 이름을 입력해야 합니다.

   자세한 내용은 [BCDR용 Azure-SSIS IR 구성](./configure-bcdr-azure-ssis-integration-runtime.md)을 참조하세요.

1. **카탈로그 데이터베이스 서비스 계층** 에 대해 SSISDB를 호스팅할 데이터베이스 서버의 서비스 계층을 선택합니다. 기본, 표준 또는 프리미엄 계층을 선택하거나 탄력적 풀 이름을 선택합니다.

**연결 테스트** 를 선택하고(해당하는 경우) 테스트가 성공하면 **계속** 을 선택합니다.

> [!NOTE]
> Azure SQL Database 서버를 사용하여 SSISDB를 호스트하는 경우, 데이터는 기본적으로 백업용 지역 중복 저장소에 저장됩니다. 다른 지역에 데이터를 복제하지 않으려는 경우 [PowerShell을 사용하여 백업 스토리지 중복성 구성](../azure-sql/database/automated-backups-overview.md?tabs=single-database#configure-backup-storage-redundancy-by-using-powershell) 지침을 따르세요.
   
##### <a name="creating-azure-ssis-ir-package-stores"></a>Azure-SSIS IR 패키지 저장소 만들기

**통합 런타임 설정** 창의 **배포 설정** 페이지에서 Azure-SSIS IR 패키지 저장소를 통해 MSDB, 파일 시스템 또는 Azure Files(패키지 배포 모델)에 배포된 패키지를 관리하려면 **Azure SQL Managed Instance가 호스트하는 파일 시스템/Azure Files/SQL Server 데이터베이스(MSDB)에 배포되는 패키지를 관리할 패키지 저장소 만들기** 확인란을 선택합니다.
   
Azure-SSIS IR 패키지 저장소를 사용하면 [레거시 SSIS 패키지 저장소](/sql/integration-services/service/package-management-ssis-service)와 비슷하게 SSMS를 통해 패키지를 가져오고/내보내고/삭제하고/실행하고 실행 중인 패키지를 모니터링/중지할 수 있습니다. 자세한 내용은 [Azure-SSIS IR 패키지 저장소를 사용하여 SSIS 패키지 관리](./azure-ssis-integration-runtime-package-store.md)를 참조하세요.
   
이 확인란을 선택하면 **새로 만들기** 를 선택하여 Azure-SSIS IR에 여러 패키지 저장소를 추가할 수 있습니다. 반대로, 패키지 저장소 하나를 여러 Azure SSIS IR이 공유할 수 있습니다.

![MSDB/파일 시스템/Azure Files의 배포 설정](./media/tutorial-create-azure-ssis-runtime-portal/deployment-settings2.png)

**패키지 저장소 추가** 창에서 다음 단계를 완료합니다.
   
   1. **패키지 저장소 이름** 으로 패키지 저장소의 이름을 입력합니다. 

   1. **패키지 저장소 연결된 서비스** 로는 패키지가 배포되는 파일 시스템/Azure Files/Azure SQL Managed Instance 대한 액세스 정보를 저장하는 기존의 연결된 서비스를 선택하거나, **새로 생성** 를 선택하여 새로 생성합니다. **새 연결된 서비스** 창에서 다음 단계를 완료합니다.
   
      > [!NOTE]
      > **Azure File Storage** 또는 **파일 시스템** 연결 서비스를 사용하여 Azure Files에 액세스할 수 있습니다. **Azure File Storage** 연결 서비스를 사용하는 경우 Azure-SSIS IR 패키지 저장소는 현재 **기본**(**계정 키** 또는 **SAS URI** 아님) 인증 방법만 지원합니다.  

      ![연결된 서비스의 배포 설정](./media/tutorial-create-azure-ssis-runtime-portal/deployment-settings-linked-service.png)

      1. **이름** 에는 연결된 서비스의 이름을 입력합니다. 
         
      1. **설명** 에는 연결된 서비스에 대한 설명을 입력합니다. 
         
      1. **형식** 으로는 **Azure File Storage**, **Azure SQL Managed Instance** 또는 **파일 시스템** 을 선택합니다.

      1. 항상 Azure-SSIS IR를 사용하여 패키지 저장소에 대한 액세스 정보를 가져오기 때문에 **통합 런타임을 통해 연결** 을 무시해도 됩니다.

      1. **Azure File Storage** 를 선택하는 경우 **인증 방법** 에 대해 **기본** 을 선택한 후, 다음 단계를 완료합니다. 

         1. **계정 선택 방법** 으로는 **Azure 구독에서 선택** 또는 **수동으로 입력** 을 선택합니다.
         
         1. **Azure 구독에서 선택** 을 선택하는 경우 관련된 **Azure 구독**, **스토리지 계정 이름** 및 **파일 공유** 를 선택합니다.
            
         1. **수동으로 입력** 을 선택하는 경우 **호스트** 로 `\\<storage account name>.file.core.windows.net\<file share name>`을 입력하고, **사용자 이름** 으로 `Azure\<storage account name>`을 입력하고, **암호** 로 `<storage account key>`를 입력하거나 비밀로 저장된 **Azure Key Vault** 를 선택합니다.

      1. **Azure SQL Managed Instance** 를 선택하는 경우 다음 단계를 완료합니다. 

         1. **연결 문자열** 또는 비밀로 저장된 **Azure Key Vault** 를 선택합니다.

         1. **연결 문자열** 을 선택하는 경우 다음 단계를 완료합니다. 
             1. **계정 선택 방법** 에 대해 **Azure 구독에서** 를 선택한 경우 관련 **Azure 구독**, **서버 이름**, **엔드포인트 유형** 및 **데이터베이스 이름** 을 선택합니다. **수동으로 입력** 을 선택한 경우 다음 단계를 완료합니다. 
                1.  **정규화된 도메인 이름** 으로 Azure SQL Managed Instance의 프라이빗 엔드포인트인 `<server name>.<dns prefix>.database.windows.net` 또는 퍼블릭 엔드포인트인 `<server name>.public.<dns prefix>.database.windows.net,3342`를 입력합니다. 프라이빗 엔드포인트를 입력하면 ADF UI가 연결할 수 없기 때문에 **연결 테스트** 를 사용할 수 없습니다.

                1. **데이터베이스 이름** 으로 `msdb`를 입력합니다.

            1. **인증 유형** 에 대해 **SQL 인증**, **관리 ID**, **서비스 주체** 또는 **사용자 할당 관리 ID** 를 선택합니다.

                - **SQL 인증** 을 선택하는 경우 관련 **사용자 이름** 및 **암호** 를 입력하거나 비밀로 저장된 **Azure Key Vault** 를 선택합니다.

                -  **관리 ID** 를 선택하는 경우 Azure SQL Managed Instance에 대한 액세스 권한을 ADF에 대한 시스템 관리 ID에 부여합니다.

                - **서비스 주체** 를 선택하는 경우 관련 **서비스 주체 ID** 및 **서비스 주체 키** 를 입력하거나 비밀로 저장된 **Azure Key Vault** 를 선택합니다.

                -  **사용자 할당 관리 ID** 를 선택하는 경우 Azure SQL Managed Instance에 대한 액세스 권한을 ADF에 대한 지정된 사용자 할당 관리 ID에 부여합니다. 그런 다음, 지정된 사용자 할당 관리 ID를 사용하여 만든 기존 자격 증명을 선택하거나 새 자격 증명을 만들 수 있습니다.

      1. **파일 시스템** 을 선택하는 경우 패키지가 배포되는 폴더의 UNC 경로를 **호스트** 로 입력하고, 관련 **사용자 이름** 및 **암호** 을 입력하거나, 비밀로 저장된 **Azure Key Vault** 를 선택합니다.

      1. **연결 테스트** 를 선택하고(해당하는 경우), 테스트가 성공하면 **만들기** 를 선택합니다.

   1. 추가된 패키지 저장소가 **배포 설정** 페이지에 표시됩니다. 저장소를 제거하려면 해당 확인란을 선택한 다음, **삭제** 를 선택합니다.

**연결 테스트** 를 선택하고(해당하는 경우) 테스트가 성공하면 **계속** 을 선택합니다.

#### <a name="advanced-settings-page"></a>고급 설정 페이지

**통합 런타임 설치** 창의 **고급 설정** 페이지에서 다음 단계를 완료합니다.

   ![고급 설정](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings.png)

   1. **노드당 최대 병렬 실행 수** 에 대해 통합 런타임 클러스터에서 노드당 동시에 실행할 최대 패키지 수를 선택합니다. 지원되는 패키지 수만 표시됩니다. 둘 이상의 코어를 사용하여 계산 집약적이거나 메모리 집약적인 하나의 대형 패키지를 실행하려면 낮은 숫자를 선택합니다. 하나의 코어에서 하나 이상의 소형 패키지를 실행하려면 높은 숫자를 선택합니다.

   1. **Customize your Azure-SSIS Integration Runtime with additional system configurations/component installations**(추가 시스템 구성/구성 요소 설치를 통해 Azure-SSIS Integration Runtime 사용자 지정) 확인란을 선택하여 Azure-SSIS IR에 표준/빠른 사용자 지정 설치를 추가할지 여부를 선택합니다. 자세한 내용은 [Azure-SSIS IR 사용자 지정 설치](./how-to-configure-azure-ssis-ir-custom-setup.md)를 참조하세요.

      확인란을 선택하는 경우 다음 단계를 완료하세요.

      ![사용자 지정 설치가 포함된 고급 설정](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings-custom.png)
   
      1. **사용자 지정 설치 컨테이너 SAS URI** 에는 표준 사용자 지정 설치의 스크립트 및 관련 파일을 저장하는 컨테이너의 SAS URI를 입력합니다.

      1. **기본 사용자 지정 설치** 의 경우, **새로 만들기** 를 선택하여 **기본 사용자 지정 설치** 패널을 추가하고 **기본 사용자 지정 설치 유형** 드롭다운 메뉴(예: **cmdkey 명령 실행**, **환경 변수 추가**, **사용이 허가된 구성 요소 설치** 등)에서 유형을 선택합니다.

         **사용이 허가된 구성 요소** 유형을 선택하는 경우 **구성 요소 이름** 드롭다운 메뉴에서 ISV 파트너에서 통합된 구성 요소를 선택하고, 필요한 경우 제품 라이선스 키를 입력하고 해당 제품에서 구입한 제품 라이선스 파일을 **라이선스 키**/**라이선스 파일** 상자에 업로드 합니다.
  
         추가된 기본 사용자 지정 설치가 **고급 설정** 페이지에 표시됩니다. 이를 제거하려면 해당 확인란을 선택한 다음, **삭제** 를 선택합니다.

   1. **조인할 Azure-SSIS Integration Runtime의 VNet을 선택하고 ADF가 특정 네트워크 리소스를 만든 다음, 선택적으로 자체 고정 공용 IP 주소를 가져오도록 허용** 확인란을 선택하여 통합 런타임을 가상 네트워크에 조인할지 여부를 선택합니다. 

      IP 방화벽 규칙/가상 네트워크 서비스 엔드포인트가 있는 Azure SQL Database 서버를 사용하거나 SSISDB를 호스팅하기 위해 프라이빗 엔드포인트가 있는 관리형 인스턴스를 사용하는 경우, 또는 자체 호스팅 IR을 구성하지 않고 온-프레미스 데이터(즉, 온프레미스 데이터 원본 또는 SSIS 패키지 내 대상이 있음)에 액세스해야 하는 경우 선택합니다. 자세한 내용은 [가상 네트워크에 Azure-SSIS IR 조인하기](./join-azure-ssis-integration-runtime-virtual-network.md)를 참조하세요. 

      확인란을 선택하는 경우 다음 단계를 완료하세요.

      ![가상 네트워크 관련 고급 설정](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings-vnet.png)

      1. **구독** 에 대해서는 가상 네트워크가 있는 Azure 구독을 선택합니다.

      1. **위치** 에는 통합 런타임의 동일한 위치가 선택됩니다.

      1. **유형** 에서는 클래식 가상 네트워크 또는 Azure Resource Manager 가상 네트워크 유형을 선택합니다. 클래식 가상 네트워크는 곧 사용되지 않으므로 Azure Resource Manager 가상 네트워크를 선택하는 것이 좋습니다.

      1. **VNet 이름** 에서 가상 네트워크의 이름을 선택합니다. 가상 네트워크 서비스 엔드포인트가 있는 Azure SQL Database 서버나 SSISDB를 호스트할 프라이빗 엔드포인트가 있는 관리형 인스턴스에 사용되는 것과 동일해야 합니다. 또는 온-프레미스 네트워크에 연결된 것과 동일해야 합니다. 그렇지 않으면 Azure-SSIS IR에 자체 정적 공용 IP를 가져오는 임의의 가상 네트워크일 수 있습니다.

      1. **서브넷 이름** 에서 가상 네트워크의 서브넷 이름을 선택합니다. 가상 네트워크 서비스 엔드포인트를 사용하여 SSISDB를 호스트하는 Azure SQL Database 서버에 사용되는 것과 동일해야 합니다. 또는 관리형 인스턴스에 사용된 것과 다른 서브넷이어야 합니다. 이 서브넷은 SSISDB를 호스트하기 위해 프라이빗 엔드포인트를 사용합니다. 그렇지 않은 경우에는 Azure-SSIS IR에 대해 본인의 정적 공용 IP를 가져오는 임의의 서브넷이 될 수 있습니다.

      1. **Azure-SSIS Integration Runtime용 고정 공용 IP 가져오기** 확인란을 선택하여 Azure-SSIS IR용 고정 공용 IP 주소를 만들지 여부를 선택합니다. 그래야만 데이터 원본용 방화벽에서 허용할 수 있습니다.

         확인란을 선택하는 경우 다음 단계를 완료하세요.

         1. **첫 번째 고정 공용 IP 주소** 는 Azure-SSIS IR 요구 사항을 충족하는 첫 번째 고정 공용 IP를 선택합니다. 주소가 없으면 **새 링크 만들기** 를 클릭하여 Azure Portal에서 고정 공용 IP를 만든 다음 새로 고침 단추를 클릭하여 선택할 수 있게 합니다.
      
         1. **두 번째 고정 공용 IP** 는 Azure-SSIS IR 요구 사항을 충족하는 두 번째 고정 공용 IP 주소를 선택합니다. 주소가 없으면 **새 링크 만들기** 를 클릭하여 Azure Portal에서 고정 공용 IP를 만든 다음 새로 고침 단추를 클릭하여 선택할 수 있게 합니다.

   1. **Set up Self-Hosted Integration Runtime as a proxy for your Azure-SSIS Integration Runtime**(자체 호스팅 통합 런타임을 Azure-SSIS Integration Runtime의 프록시로 설정) 확인란을 선택하여 자체 호스팅 IR을 Azure-SSIS IR의 프록시로 구성할지 여부를 선택합니다. 자세한 내용은 [자체 호스팅 IR을 프록시로 설정](./self-hosted-integration-runtime-proxy-ssis.md)을 참조하세요. 

      확인란을 선택하는 경우 다음 단계를 완료하세요.

      ![자체 호스팅 IR을 사용하는 고급 설정](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings-shir.png)

      1. **자체 호스팅 통합 런타임** 의 경우, Azure-SSIS IR의 프록시로 기존 자체 호스팅 IR을 선택합니다.

      1. **스테이징 저장소 연결 서비스** 의 경우, 기존 Azure Blob 저장소 연결 서비스를 선택하거나 스테이징용 서비스를 새로 만듭니다.

      1. **준비 경로** 의 경우, 선택한 Azure Blob 스토리지 계정에 blob 컨테이너를 지정하거나 스테이징에 기본 항목을 사용하도록 비워 둡니다.

   1. **VNet 유효성 검사** > **계속** 을 선택합니다. 

**요약** 섹션에서 모든 프로비저닝 설정을 검토하고, 추천 설명서 링크를 책갈피로 설정하고, **마침** 을 선택하여 통합 런타임을 만들기 시작합니다.

> [!NOTE]
> 이 프로세스는 사용자 지정 설치 시간을 제외하고 5분 이내에 완료됩니다. 그러나 Azure-SSIS IR이 가상 네트워크에 조인하는 데는 20~30분 정도 걸릴 수 있습니다.
>
> SSISDB를 사용하는 경우 Data Factory 서비스에서 데이터베이스 서버에 연결하여 SSISDB를 준비합니다. 또한 이 서비스는 가상 네트워크에 대한 권한 및 설정을 구성하며, 지정된 경우 Azure-SSIS IR을 가상 네트워크에 조인합니다.
>
> Azure-SSIS IR을 프로비저닝하는 경우 Access 재배포 가능 패키지 및 Azure Feature Pack for SSIS도 설치됩니다. 이러한 구성 요소는 기본 제공 구성 요소에서 이미 지원하는 데이터 원본 외에도 Excel 파일, Access 파일 및 다양한 Azure 데이터 원본에 대한 연결을 제공합니다. 기본 제공/미리 설치된 구성 요소에 대한 자세한 내용은 [Azure-SSIS IR의 기본 제공/미리 설치된 구성 요소](./built-in-preinstalled-components-ssis-integration-runtime.md)를 참조하세요. 설치할 수 있는 추가 구성 요소에 대한 자세한 내용은 [Azure-SSIS IR 사용자 지정 설치](./how-to-configure-azure-ssis-ir-custom-setup.md)를 참조하세요.

#### <a name="connections-pane"></a>[연결] 창

**관리** 허브의 **연결** 창에서 **통합 런타임** 페이지로 전환하여 **새로 고침** 을 선택합니다. 

   ![[연결] 창](./media/tutorial-create-azure-ssis-runtime-portal/connections-pane.png)

   Azure-SSIS IR의 이름을 선택하여 편집/재구성할 수 있습니다. 또한 관련된 단추를 선택하여 Azure-SSIS IR을 모니터링/시작/중지/삭제하고, SSIS 패키지 실행 작업을 사용하여 Azure-SSIS IR에서 실행할 ADF 파이프라인을 자동으로 생성하고, Azure-SSIS IR의 JSON 코드/페이로드를 볼 수 있습니다.  Azure-SSIS IR이 중지된 상태에서만 편집/삭제할 수 있습니다.

### <a name="azure-ssis-integration-runtimes-in-the-portal"></a>포털의 Azure SSIS 통합 런타임

1. Azure Data Factory UI에서 **관리** 탭으로 전환한 다음, **연결** 에서 **통합 런타임** 탭으로 전환하여 데이터 팩터리에서 기존 통합 런타임을 확인합니다.

   ![기존 IR 보기](./media/tutorial-create-azure-ssis-runtime-portal/view-azure-ssis-integration-runtimes.png)

1. **새로 만들기** 를 선택하여 새 Azure-SSIS IR을 만들고, **통합 런타임 설치** 창을 엽니다.

   ![메뉴를 통한 통합 런타임](./media/tutorial-create-azure-ssis-runtime-portal/edit-connections-new-integration-runtime-button.png)

1. **통합 런타임 설정** 창에서 **Azure에서 실행할 기존 SSIS 패키지 리프트 앤 시프트** 타일을 선택한 다음, **계속** 을 선택합니다.

   ![통합 런타임 유형 지정](./media/tutorial-create-azure-ssis-runtime-portal/integration-runtime-setup-options.png)

1. 나머지 Azure-SSIS IR 설치 단계는 [Azure SSIS 통합 런타임 프로비전](#provision-an-azure-ssis-integration-runtime) 섹션을 참조하세요.

## <a name="use-azure-powershell-to-create-an-integration-runtime"></a>Azure PowerShell을 사용하여 통합 런타임 만들기

이 섹션에서는 Azure PowerShell을 사용하여 Azure-SSIS IR을 만듭니다.

### <a name="create-variables"></a>변수 만들기

다음 스크립트를 복사하여 붙여넣습니다. 변수에 대한 값을 지정합니다. 

```powershell
### Azure Data Factory info
# If your input contains a PSH special character like "$", precede it with the escape character "`" - for example, "`$"
$SubscriptionName = "[your Azure subscription name]"
$ResourceGroupName = "[your Azure resource group name]"
# Data factory name - must be globally unique
$DataFactoryName = "[your data factory name]"
# For supported regions, see https://azure.microsoft.com/global-infrastructure/services/?products=data-factory&regions=all
$DataFactoryLocation = "EastUS"

### Azure-SSIS integration runtime info - This is a Data Factory compute resource for running SSIS packages.
$AzureSSISName = "[your Azure-SSIS IR name]"
$AzureSSISDescription = "[your Azure-SSIS IR description]"
# For supported regions, see https://azure.microsoft.com/global-infrastructure/services/?products=data-factory&regions=all
$AzureSSISLocation = "EastUS"
# For supported node sizes, see https://azure.microsoft.com/pricing/details/data-factory/ssis/
$AzureSSISNodeSize = "Standard_D8_v3"
# 1-10 nodes are currently supported
$AzureSSISNodeNumber = 2
# Azure-SSIS IR edition/license info: Standard or Enterprise
$AzureSSISEdition = "Standard" # Standard by default, whereas Enterprise lets you use advanced features on your Azure-SSIS IR
# Azure-SSIS IR hybrid usage info: LicenseIncluded or BasePrice
$AzureSSISLicenseType = "LicenseIncluded" # LicenseIncluded by default, whereas BasePrice lets you bring your own on-premises SQL Server license with Software Assurance to earn cost savings from Azure Hybrid Benefit option
# For a Standard_D1_v2 node, up to 4 parallel executions per node are supported. For other nodes, up to (2 x number of cores) are currently supported.
$AzureSSISMaxParallelExecutionsPerNode = 8
# Custom setup info: Standard/express custom setups
$SetupScriptContainerSasUri = "" # OPTIONAL to provide a SAS URI of blob container for standard custom setup where your script and its associated files are stored
$ExpressCustomSetup = "[RunCmdkey|SetEnvironmentVariable|InstallAzurePowerShell|SentryOne.TaskFactory|oh22is.SQLPhonetics.NET|oh22is.HEDDA.IO|KingswaySoft.IntegrationToolkit|KingswaySoft.ProductivityPack|Theobald.XtractIS|AecorSoft.IntegrationService|CData.Standard|CData.Extended or leave it empty]" # OPTIONAL to configure an express custom setup without script
# Virtual network info: Classic or Azure Resource Manager
$VnetId = "[your virtual network resource ID or leave it empty]" # REQUIRED if you use an Azure SQL Database server with IP firewall rules/virtual network service endpoints or a managed instance with private endpoint to host SSISDB, or if you require access to on-premises data without configuring a self-hosted IR. We recommend an Azure Resource Manager virtual network, because classic virtual networks will be deprecated soon.
$SubnetName = "[your subnet name or leave it empty]" # WARNING: Use the same subnet as the one used for your Azure SQL Database server with virtual network service endpoints, or a different subnet from the one used for your managed instance with a private endpoint
# Public IP address info: OPTIONAL to provide two standard static public IP addresses with DNS name under the same subscription and in the same region as your virtual network
$FirstPublicIP = "[your first public IP address resource ID or leave it empty]"
$SecondPublicIP = "[your second public IP address resource ID or leave it empty]"

### SSISDB info
$SSISDBServerEndpoint = "[your Azure SQL Database server name.database.windows.net or managed instance name.DNS prefix.database.windows.net or managed instance name.public.DNS prefix.database.windows.net,3342 or leave it empty if you do not use SSISDB]" # WARNING: If you use SSISDB, ensure that there's no existing SSISDB on your database server, so we can prepare and manage one on your behalf
# Authentication info: SQL or Azure AD
$SSISDBServerAdminUserName = "[your server admin username for SQL authentication or leave it empty for Azure AD authentication]"
$SSISDBServerAdminPassword = "[your server admin password for SQL authentication or leave it empty for Azure AD authentication]"
# For the basic pricing tier, specify "Basic," not "B." For standard, premium, and elastic pool tiers, specify "S0," "S1," "S2," "S3," etc. See https://docs.microsoft.com/azure/sql-database/sql-database-resource-limits-database-server.
$SSISDBPricingTier = "[Basic|S0|S1|S2|S3|S4|S6|S7|S9|S12|P1|P2|P4|P6|P11|P15|…|ELASTIC_POOL(name = <elastic_pool_name>) for Azure SQL Database server or leave it empty for managed instance]"

### Self-hosted integration runtime info - This can be configured as a proxy for on-premises data access 
$DataProxyIntegrationRuntimeName = "" # OPTIONAL to configure a proxy for on-premises data access 
$DataProxyStagingLinkedServiceName = "" # OPTIONAL to configure a proxy for on-premises data access 
$DataProxyStagingPath = "" # OPTIONAL to configure a proxy for on-premises data access 
```

### <a name="sign-in-and-select-a-subscription"></a>로그인하고 구독 선택

다음 스크립트를 추가하여 로그인하고 Azure 구독을 선택합니다.

```powershell
Connect-AzAccount
Select-AzSubscription -SubscriptionName $SubscriptionName
```

### <a name="validate-the-connection-to-database-server"></a>데이터베이스 서버에 대한 연결 유효성 검사

Azure SQL Database 서버 또는 관리형 인스턴스의 유효성을 검사하려면 다음 스크립트를 추가합니다.

```powershell
# Validate only if you use SSISDB and you don't use virtual network or Azure AD authentication
if(![string]::IsNullOrEmpty($SSISDBServerEndpoint))
{
    if([string]::IsNullOrEmpty($VnetId) -and [string]::IsNullOrEmpty($SubnetName))
    {
        if(![string]::IsNullOrEmpty($SSISDBServerAdminUserName) -and ![string]::IsNullOrEmpty($SSISDBServerAdminPassword))
        {
            $SSISDBConnectionString = "Data Source=" + $SSISDBServerEndpoint + ";User ID=" + $SSISDBServerAdminUserName + ";Password=" + $SSISDBServerAdminPassword
            $sqlConnection = New-Object System.Data.SqlClient.SqlConnection $SSISDBConnectionString;
            Try
            {
                $sqlConnection.Open();
            }
            Catch [System.Data.SqlClient.SqlException]
            {
                Write-Warning "Cannot connect to your Azure SQL Database server, exception: $_";
                Write-Warning "Please make sure the server you specified has already been created. Do you want to proceed? [Y/N]"
                $yn = Read-Host
                if(!($yn -ieq "Y"))
                {
                    Return;
                }
            }
        }
    }
}
```

### <a name="configure-the-virtual-network"></a>가상 네트워크 구성

다음 스크립트를 추가하여 조인할 Azure-SSIS Integration Runtime에 대한 가상 네트워크 권한 및 설정을 자동으로 구성합니다.

```powershell
# Make sure to run this script against the subscription to which the virtual network belongs
if(![string]::IsNullOrEmpty($VnetId) -and ![string]::IsNullOrEmpty($SubnetName))
{
    # Register to the Azure Batch resource provider
    $BatchApplicationId = "ddbf3205-c6bd-46ae-8127-60eb93363864"
    $BatchObjectId = (Get-AzADServicePrincipal -ServicePrincipalName $BatchApplicationId).Id
    Register-AzResourceProvider -ProviderNamespace Microsoft.Batch
    while(!(Get-AzResourceProvider -ProviderNamespace "Microsoft.Batch").RegistrationState.Contains("Registered"))
    {
    Start-Sleep -s 10
    }
    if($VnetId -match "/providers/Microsoft.ClassicNetwork/")
    {
        # Assign the VM contributor role to Microsoft.Batch
        New-AzRoleAssignment -ObjectId $BatchObjectId -RoleDefinitionName "Classic Virtual Machine Contributor" -Scope $VnetId
    }
}
```

### <a name="create-a-resource-group"></a>리소스 그룹 만들기

[New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) 명령을 사용하여 [Azure 리소스 그룹](../azure-resource-manager/management/overview.md)을 만듭니다. 리소스 그룹은 Azure 리소스가 그룹으로 배포되고 관리되는 논리 컨테이너입니다.

리소스 그룹이 이미 있는 경우 스크립트에 이 코드를 복사하지 마세요. 

```powershell
New-AzResourceGroup -Location $DataFactoryLocation -Name $ResourceGroupName
```

### <a name="create-a-data-factory"></a>데이터 팩터리 만들기

다음 명령을 실행하여 데이터 팩터리를 만듭니다.

```powershell
Set-AzDataFactoryV2 -ResourceGroupName $ResourceGroupName `
    -Location $DataFactoryLocation `
    -Name $DataFactoryName
```

### <a name="create-an-integration-runtime"></a>Integration Runtime 만들기

다음 명령을 실행하여 Azure에서 SSIS 패키지를 실행하는 Azure-SSIS 통합 런타임을 만듭니다.

SSISDB를 사용하지 않는 경우에는 `CatalogServerEndpoint`, `CatalogPricingTier` 및 `CatalogAdminCredential` 매개변수를 생략해도 됩니다.

IP 방화벽 규칙/가상 네트워크 서비스 엔드포인트가 있는 Azure SQL Database 서버 또는 SSISDB를 호스팅하기 위해 프라이빗 엔드포인트가 있는 관리형 인스턴스를 사용하지 않는 경우에는 `VNetId` 및 `Subnet` 매개변수를 생략하거나 그에 대한 빈 값을 전달할 수 있습니다. 온-프레미스 데이터에 액세스하는 Azure-SSIS IR의 프록시로 자체 호스팅 IR을 구성하는 경우에도 이를 생략할 수 있습니다. 아닌 경우에는 이를 생략할 수 없으며 가상 네트워크 구성에서 유효한 값을 전달해야 합니다. 자세한 내용은 [가상 네트워크에 Azure-SSIS IR 조인하기](./join-azure-ssis-integration-runtime-virtual-network.md)를 참조하세요.

관리형 인스턴스를 사용하여 SSISDB를 호스팅하는 경우 `CatalogPricingTier` 매개변수를 생략하거나 빈 값을 전달할 수 있습니다. 아닌 경우에는 이를 생략할 수 없으며 Azure SQL Database에 지원되는 가격 책정 계층 목록에서 유효한 값을 전달해야 합니다. 자세한 내용은 [SQL Database 리소스 한도](../azure-sql/database/resource-limits-logical-server.md)를 참조하세요.

데이터 팩터리의 지정한 시스템/사용자가 할당한 관리 ID로 Azure AD 인증을 사용하여 데이터베이스 서버에 연결하는 경우에는 `CatalogAdminCredential` 매개 변수를 생략할 수 있습니다. 그러나 데이터 팩터리의 지정한 시스템/사용자가 할당한 관리 ID를 데이터베이스 서버에 대한 액세스 권한이 있는 Azure AD 그룹에 추가해야 합니다. 자세한 내용은 [Azure-SSIS IR에 Azure AD 인증 사용](./enable-aad-authentication-azure-ssis-ir.md)을 참조하세요. 아닌 경우에는 생략할 수 없으며, SQL 인증을 위해 서버 관리 사용자 이름과 암호로 구성된 유효한 개체를 전달해야 합니다.

```powershell
Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
    -DataFactoryName $DataFactoryName `
    -Name $AzureSSISName `
    -Description $AzureSSISDescription `
    -Type Managed `
    -Location $AzureSSISLocation `
    -NodeSize $AzureSSISNodeSize `
    -NodeCount $AzureSSISNodeNumber `
    -Edition $AzureSSISEdition `
    -LicenseType $AzureSSISLicenseType `
    -MaxParallelExecutionsPerNode $AzureSSISMaxParallelExecutionsPerNode `
    -VnetId $VnetId `
    -Subnet $SubnetName
       
# Add the CatalogServerEndpoint, CatalogPricingTier, and CatalogAdminCredential parameters if you use SSISDB
if(![string]::IsNullOrEmpty($SSISDBServerEndpoint))
{
    Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
        -DataFactoryName $DataFactoryName `
        -Name $AzureSSISName `
        -CatalogServerEndpoint $SSISDBServerEndpoint `
        -CatalogPricingTier $SSISDBPricingTier

    if(![string]::IsNullOrEmpty($SSISDBServerAdminUserName) –and ![string]::IsNullOrEmpty($SSISDBServerAdminPassword)) # Add the CatalogAdminCredential parameter if you don't use Azure AD authentication
    {
        $secpasswd = ConvertTo-SecureString $SSISDBServerAdminPassword -AsPlainText -Force
        $serverCreds = New-Object System.Management.Automation.PSCredential($SSISDBServerAdminUserName, $secpasswd)

        Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
            -DataFactoryName $DataFactoryName `
            -Name $AzureSSISName `
            -CatalogAdminCredential $serverCreds
    }
}

# Add custom setup parameters if you use standard/express custom setups
if(![string]::IsNullOrEmpty($SetupScriptContainerSasUri))
{
    Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
        -DataFactoryName $DataFactoryName `
        -Name $AzureSSISName `
        -SetupScriptContainerSasUri $SetupScriptContainerSasUri
}
if(![string]::IsNullOrEmpty($ExpressCustomSetup))
{
    if($ExpressCustomSetup -eq "RunCmdkey")
    {
        $addCmdkeyArgument = "YourFileShareServerName or YourAzureStorageAccountName.file.core.windows.net"
        $userCmdkeyArgument = "YourDomainName\YourUsername or azure\YourAzureStorageAccountName"
        $passCmdkeyArgument = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString("YourPassword or YourAccessKey")
        $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.CmdkeySetup($addCmdkeyArgument, $userCmdkeyArgument, $passCmdkeyArgument)
    }
    if($ExpressCustomSetup -eq "SetEnvironmentVariable")
    {
        $variableName = "YourVariableName"
        $variableValue = "YourVariableValue"
        $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.EnvironmentVariableSetup($variableName, $variableValue)
    }
    if($ExpressCustomSetup -eq "InstallAzurePowerShell")
    {
        $moduleVersion = "YourAzModuleVersion"
        $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.AzPowerShellSetup($moduleVersion)
    }
    if($ExpressCustomSetup -eq "SentryOne.TaskFactory")
    {
        $licenseKey = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString("YourLicenseKey")
        $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup, $licenseKey)
    }
    if($ExpressCustomSetup -eq "oh22is.SQLPhonetics.NET")
    {
        $licenseKey = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString("YourLicenseKey")
        $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup, $licenseKey)
    }
    if($ExpressCustomSetup -eq "oh22is.HEDDA.IO")
    {
        $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup)
    }
    if($ExpressCustomSetup -eq "KingswaySoft.IntegrationToolkit")
    {
        $licenseKey = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString("YourLicenseKey")
        $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup, $licenseKey)
    }
    if($ExpressCustomSetup -eq "KingswaySoft.ProductivityPack")
    {
        $licenseKey = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString("YourLicenseKey")
        $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup, $licenseKey)
    }    
    if($ExpressCustomSetup -eq "Theobald.XtractIS")
    {
        $jsonData = Get-Content -Raw -Path YourLicenseFile.json
        $jsonData = $jsonData -replace '\s',''
        $jsonData = $jsonData.replace('"','\"')
        $licenseKey = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString($jsonData)
        $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup, $licenseKey)
    }
    if($ExpressCustomSetup -eq "AecorSoft.IntegrationService")
    {
        $licenseKey = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString("YourLicenseKey")
        $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup, $licenseKey)
    }
    if($ExpressCustomSetup -eq "CData.Standard")
    {
        $licenseKey = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString("YourLicenseKey")
        $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup, $licenseKey)
    }
    if($ExpressCustomSetup -eq "CData.Extended")
    {
        $licenseKey = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString("YourLicenseKey")
        $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup, $licenseKey)
    }    
    # Create an array of one or more express custom setups
    $setups = New-Object System.Collections.ArrayList
    $setups.Add($setup)

    Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
        -DataFactoryName $DataFactoryName `
        -Name $AzureSSISName `
        -ExpressCustomSetup $setups
}

# Add self-hosted integration runtime parameters if you configure a proxy for on-premises data access
if(![string]::IsNullOrEmpty($DataProxyIntegrationRuntimeName) -and ![string]::IsNullOrEmpty($DataProxyStagingLinkedServiceName))
{
    Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
        -DataFactoryName $DataFactoryName `
        -Name $AzureSSISName `
        -DataProxyIntegrationRuntimeName $DataProxyIntegrationRuntimeName `
        -DataProxyStagingLinkedServiceName $DataProxyStagingLinkedServiceName

    if(![string]::IsNullOrEmpty($DataProxyStagingPath))
    {
        Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
            -DataFactoryName $DataFactoryName `
            -Name $AzureSSISName `
            -DataProxyStagingPath $DataProxyStagingPath
    }
}

# Add public IP address parameters if you bring your own static public IP addresses
if(![string]::IsNullOrEmpty($FirstPublicIP) -and ![string]::IsNullOrEmpty($SecondPublicIP))
{
    $publicIPs = @($FirstPublicIP, $SecondPublicIP)
    Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
        -DataFactoryName $DataFactoryName `
        -Name $AzureSSISName `
        -PublicIPs $publicIPs
}
```

### <a name="start-the-integration-runtime"></a>통합 런타임 시작

다음 명령을 실행하여 Azure-SSIS Integration Runtime을 시작합니다.

```powershell
write-host("##### Starting #####")
Start-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
    -DataFactoryName $DataFactoryName `
    -Name $AzureSSISName `
    -Force

write-host("##### Completed #####")
write-host("If any cmdlet is unsuccessful, please consider using -Debug option for diagnostics.")
```

> [!NOTE]
> 이 프로세스는 사용자 지정 설치 시간을 제외하고 5분 이내에 완료됩니다. 그러나 Azure-SSIS IR이 가상 네트워크에 조인하는 데는 20~30분 정도 걸릴 수 있습니다.
>
> SSISDB를 사용하는 경우 Data Factory 서비스에서 데이터베이스 서버에 연결하여 SSISDB를 준비합니다. 또한 이 서비스는 가상 네트워크에 대한 권한 및 설정을 구성하며, 지정된 경우 Azure-SSIS IR을 가상 네트워크에 조인합니다.
> 
> Azure-SSIS IR을 프로비저닝하는 경우 Access 재배포 가능 패키지 및 Azure Feature Pack for SSIS도 설치됩니다. 이러한 구성 요소는 기본 제공 구성 요소에서 이미 지원하는 데이터 원본 외에도 Excel 파일, Access 파일 및 다양한 Azure 데이터 원본에 대한 연결을 제공합니다. 기본 제공/미리 설치된 구성 요소에 대한 자세한 내용은 [Azure-SSIS IR의 기본 제공/미리 설치된 구성 요소](./built-in-preinstalled-components-ssis-integration-runtime.md)를 참조하세요. 설치할 수 있는 추가 구성 요소에 대한 자세한 내용은 [Azure-SSIS IR 사용자 지정 설치](./how-to-configure-azure-ssis-ir-custom-setup.md)를 참조하세요.

### <a name="full-script"></a>전체 스크립트

다음은 Azure-SSIS 통합 런타임을 만드는 전체 스크립트입니다.

```powershell
### Azure Data Factory info
# If your input contains a PSH special character like "$", precede it with the escape character "`" - for example, "`$"
$SubscriptionName = "[your Azure subscription name]"
$ResourceGroupName = "[your Azure resource group name]"
# Data factory name - must be globally unique
$DataFactoryName = "[your data factory name]"
# For supported regions, see https://azure.microsoft.com/global-infrastructure/services/?products=data-factory&regions=all
$DataFactoryLocation = "EastUS"

### Azure-SSIS integration runtime info - This is a Data Factory compute resource for running SSIS packages.
$AzureSSISName = "[your Azure-SSIS IR name]"
$AzureSSISDescription = "[your Azure-SSIS IR description]"
# For supported regions, see https://azure.microsoft.com/global-infrastructure/services/?products=data-factory&regions=all
$AzureSSISLocation = "EastUS"
# For supported node sizes, see https://azure.microsoft.com/pricing/details/data-factory/ssis/
$AzureSSISNodeSize = "Standard_D8_v3"
# 1-10 nodes are currently supported
$AzureSSISNodeNumber = 2
# Azure-SSIS IR edition/license info: Standard or Enterprise
$AzureSSISEdition = "Standard" # Standard by default, whereas Enterprise lets you use advanced features on your Azure-SSIS IR
# Azure-SSIS IR hybrid usage info: LicenseIncluded or BasePrice
$AzureSSISLicenseType = "LicenseIncluded" # LicenseIncluded by default, whereas BasePrice lets you bring your own on-premises SQL Server license with Software Assurance to earn cost savings from the Azure Hybrid Benefit option
# For a Standard_D1_v2 node, up to four parallel executions per node are supported. For other nodes, up to (2 x number of cores) are currently supported.
$AzureSSISMaxParallelExecutionsPerNode = 8
# Custom setup info: Standard/express custom setups
$SetupScriptContainerSasUri = "" # OPTIONAL to provide a SAS URI of blob container for standard custom setup where your script and its associated files are stored
$ExpressCustomSetup = "[RunCmdkey|SetEnvironmentVariable|InstallAzurePowerShell|SentryOne.TaskFactory|oh22is.SQLPhonetics.NET|oh22is.HEDDA.IO|KingswaySoft.IntegrationToolkit|KingswaySoft.ProductivityPack|Theobald.XtractIS|AecorSoft.IntegrationService|CData.Standard|CData.Extended or leave it empty]" # OPTIONAL to configure an express custom setup without script
# Virtual network info: Classic or Azure Resource Manager
$VnetId = "[your virtual network resource ID or leave it empty]" # REQUIRED if you use an Azure SQL Database server with IP firewall rules/virtual network service endpoints or a managed instance with private endpoint to host SSISDB, or if you require access to on-premises data without configuring a self-hosted IR. We recommend an Azure Resource Manager virtual network, because classic virtual networks will be deprecated soon.
$SubnetName = "[your subnet name or leave it empty]" # WARNING: Use the same subnet as the one used for your Azure SQL Database server with virtual network service endpoints, or a different subnet from the one used for your managed instance with a private endpoint
# Public IP address info: OPTIONAL to provide two standard static public IP addresses with DNS name under the same subscription and in the same region as your virtual network
$FirstPublicIP = "[your first public IP address resource ID or leave it empty]"
$SecondPublicIP = "[your second public IP address resource ID or leave it empty]"

### SSISDB info
$SSISDBServerEndpoint = "[your Azure SQL Database server name.database.windows.net or managed instance name.DNS prefix.database.windows.net or managed instance name.public.DNS prefix.database.windows.net,3342 or leave it empty if you do not use SSISDB]" # WARNING: If you use SSISDB, ensure that there's no existing SSISDB on your database server, so we can prepare and manage one on your behalf
# Authentication info: SQL or Azure AD
$SSISDBServerAdminUserName = "[your server admin username for SQL authentication or leave it empty for Azure AD authentication]"
$SSISDBServerAdminPassword = "[your server admin password for SQL authentication or leave it empty for Azure AD authentication]"
# For the basic pricing tier, specify "Basic," not "B." For standard, premium, and elastic pool tiers, specify "S0," "S1," "S2," "S3," etc. See https://docs.microsoft.com/azure/sql-database/sql-database-resource-limits-database-server.
$SSISDBPricingTier = "[Basic|S0|S1|S2|S3|S4|S6|S7|S9|S12|P1|P2|P4|P6|P11|P15|…|ELASTIC_POOL(name = <elastic_pool_name>) for Azure SQL Database server or leave it empty for managed instance]"

### Self-hosted integration runtime info - This can be configured as a proxy for on-premises data access 
$DataProxyIntegrationRuntimeName = "" # OPTIONAL to configure a proxy for on-premises data access 
$DataProxyStagingLinkedServiceName = "" # OPTIONAL to configure a proxy for on-premises data access 
$DataProxyStagingPath = "" # OPTIONAL to configure a proxy for on-premises data access 

### Sign in and select a subscription
Connect-AzAccount
Select-AzSubscription -SubscriptionName $SubscriptionName

### Validate the connection to the database server
# Validate only if you use SSISDB and don't use a virtual network or Azure AD authentication
if(![string]::IsNullOrEmpty($SSISDBServerEndpoint))
{
    if([string]::IsNullOrEmpty($VnetId) -and [string]::IsNullOrEmpty($SubnetName))
    {
        if(![string]::IsNullOrEmpty($SSISDBServerAdminUserName) -and ![string]::IsNullOrEmpty($SSISDBServerAdminPassword))
        {
            $SSISDBConnectionString = "Data Source=" + $SSISDBServerEndpoint + ";User ID=" + $SSISDBServerAdminUserName + ";Password=" + $SSISDBServerAdminPassword
            $sqlConnection = New-Object System.Data.SqlClient.SqlConnection $SSISDBConnectionString;
            Try
            {
                $sqlConnection.Open();
            }
            Catch [System.Data.SqlClient.SqlException]
            {
                Write-Warning "Cannot connect to your Azure SQL Database server, exception: $_";
                Write-Warning "Please make sure the server you specified has already been created. Do you want to proceed? [Y/N]"
                $yn = Read-Host
                if(!($yn -ieq "Y"))
                {
                    Return;
                }
            }
        }
    }
}

### Configure a virtual network
# Make sure to run this script against the subscription to which the virtual network belongs
if(![string]::IsNullOrEmpty($VnetId) -and ![string]::IsNullOrEmpty($SubnetName))
{
    # Register to the Azure Batch resource provider
    $BatchApplicationId = "ddbf3205-c6bd-46ae-8127-60eb93363864"
    $BatchObjectId = (Get-AzADServicePrincipal -ServicePrincipalName $BatchApplicationId).Id
    Register-AzResourceProvider -ProviderNamespace Microsoft.Batch
    while(!(Get-AzResourceProvider -ProviderNamespace "Microsoft.Batch").RegistrationState.Contains("Registered"))
    {
    Start-Sleep -s 10
    }
    if($VnetId -match "/providers/Microsoft.ClassicNetwork/")
    {
        # Assign the VM contributor role to Microsoft.Batch
        New-AzRoleAssignment -ObjectId $BatchObjectId -RoleDefinitionName "Classic Virtual Machine Contributor" -Scope $VnetId
    }
}

### Create a data factory
Set-AzDataFactoryV2 -ResourceGroupName $ResourceGroupName `
    -Location $DataFactoryLocation `
    -Name $DataFactoryName

### Create an integration runtime
Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
    -DataFactoryName $DataFactoryName `
    -Name $AzureSSISName `
    -Description $AzureSSISDescription `
    -Type Managed `
    -Location $AzureSSISLocation `
    -NodeSize $AzureSSISNodeSize `
    -NodeCount $AzureSSISNodeNumber `
    -Edition $AzureSSISEdition `
    -LicenseType $AzureSSISLicenseType `
    -MaxParallelExecutionsPerNode $AzureSSISMaxParallelExecutionsPerNode `
    -VnetId $VnetId `
    -Subnet $SubnetName
       
# Add CatalogServerEndpoint, CatalogPricingTier, and CatalogAdminCredential parameters if you use SSISDB
if(![string]::IsNullOrEmpty($SSISDBServerEndpoint))
{
    Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
        -DataFactoryName $DataFactoryName `
        -Name $AzureSSISName `
        -CatalogServerEndpoint $SSISDBServerEndpoint `
        -CatalogPricingTier $SSISDBPricingTier

    if(![string]::IsNullOrEmpty($SSISDBServerAdminUserName) –and ![string]::IsNullOrEmpty($SSISDBServerAdminPassword)) # Add the CatalogAdminCredential parameter if you don't use Azure AD authentication
    {
        $secpasswd = ConvertTo-SecureString $SSISDBServerAdminPassword -AsPlainText -Force
        $serverCreds = New-Object System.Management.Automation.PSCredential($SSISDBServerAdminUserName, $secpasswd)

        Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
            -DataFactoryName $DataFactoryName `
            -Name $AzureSSISName `
            -CatalogAdminCredential $serverCreds
    }
}

# Add custom setup parameters if you use standard/express custom setups
if(![string]::IsNullOrEmpty($SetupScriptContainerSasUri))
{
    Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
        -DataFactoryName $DataFactoryName `
        -Name $AzureSSISName `
        -SetupScriptContainerSasUri $SetupScriptContainerSasUri
}
if(![string]::IsNullOrEmpty($ExpressCustomSetup))
{
    if($ExpressCustomSetup -eq "RunCmdkey")
    {
        $addCmdkeyArgument = "YourFileShareServerName or YourAzureStorageAccountName.file.core.windows.net"
        $userCmdkeyArgument = "YourDomainName\YourUsername or azure\YourAzureStorageAccountName"
        $passCmdkeyArgument = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString("YourPassword or YourAccessKey")
        $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.CmdkeySetup($addCmdkeyArgument, $userCmdkeyArgument, $passCmdkeyArgument)
    }
    if($ExpressCustomSetup -eq "SetEnvironmentVariable")
    {
        $variableName = "YourVariableName"
        $variableValue = "YourVariableValue"
        $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.EnvironmentVariableSetup($variableName, $variableValue)
    }
    if($ExpressCustomSetup -eq "InstallAzurePowerShell")
    {
        $moduleVersion = "YourAzModuleVersion"
        $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.AzPowerShellSetup($moduleVersion)
    }
    if($ExpressCustomSetup -eq "SentryOne.TaskFactory")
    {
        $licenseKey = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString("YourLicenseKey")
        $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup, $licenseKey)
    }
    if($ExpressCustomSetup -eq "oh22is.SQLPhonetics.NET")
    {
        $licenseKey = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString("YourLicenseKey")
        $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup, $licenseKey)
    }
    if($ExpressCustomSetup -eq "oh22is.HEDDA.IO")
    {
        $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup)
    }
    if($ExpressCustomSetup -eq "KingswaySoft.IntegrationToolkit")
    {
        $licenseKey = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString("YourLicenseKey")
        $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup, $licenseKey)
    }
    if($ExpressCustomSetup -eq "KingswaySoft.ProductivityPack")
    {
        $licenseKey = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString("YourLicenseKey")
        $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup, $licenseKey)
    }    
    if($ExpressCustomSetup -eq "Theobald.XtractIS")
    {
        $jsonData = Get-Content -Raw -Path YourLicenseFile.json
        $jsonData = $jsonData -replace '\s',''
        $jsonData = $jsonData.replace('"','\"')
        $licenseKey = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString($jsonData)
        $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup, $licenseKey)
    }
    if($ExpressCustomSetup -eq "AecorSoft.IntegrationService")
    {
        $licenseKey = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString("YourLicenseKey")
        $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup, $licenseKey)
    }
    if($ExpressCustomSetup -eq "CData.Standard")
    {
        $licenseKey = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString("YourLicenseKey")
        $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup, $licenseKey)
    }
    if($ExpressCustomSetup -eq "CData.Extended")
    {
        $licenseKey = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString("YourLicenseKey")
        $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup, $licenseKey)
    }
    # Create an array of one or more express custom setups
    $setups = New-Object System.Collections.ArrayList
    $setups.Add($setup)

    Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
        -DataFactoryName $DataFactoryName `
        -Name $AzureSSISName `
        -ExpressCustomSetup $setups
}

# Add self-hosted integration runtime parameters if you configure a proxy for on-premises data accesss
if(![string]::IsNullOrEmpty($DataProxyIntegrationRuntimeName) -and ![string]::IsNullOrEmpty($DataProxyStagingLinkedServiceName))
{
    Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
        -DataFactoryName $DataFactoryName `
        -Name $AzureSSISName `
        -DataProxyIntegrationRuntimeName $DataProxyIntegrationRuntimeName `
        -DataProxyStagingLinkedServiceName $DataProxyStagingLinkedServiceName

    if(![string]::IsNullOrEmpty($DataProxyStagingPath))
    {
        Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
            -DataFactoryName $DataFactoryName `
            -Name $AzureSSISName `
            -DataProxyStagingPath $DataProxyStagingPath
    }
}

# Add public IP address parameters if you bring your own static public IP addresses
if(![string]::IsNullOrEmpty($FirstPublicIP) -and ![string]::IsNullOrEmpty($SecondPublicIP))
{
    $publicIPs = @($FirstPublicIP, $SecondPublicIP)
    Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
        -DataFactoryName $DataFactoryName `
        -Name $AzureSSISName `
        -PublicIPs $publicIPs
}

### Start the integration runtime
write-host("##### Starting #####")
Start-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
    -DataFactoryName $DataFactoryName `
    -Name $AzureSSISName `
    -Force

write-host("##### Completed #####")
write-host("If any cmdlet is unsuccessful, please consider using -Debug option for diagnostics.")
```

## <a name="use-an-azure-resource-manager-template-to-create-an-integration-runtime"></a>Azure Resource Manager 템플릿을 사용하여 통합 런타임을 만들기

이 섹션에서는 Azure Resource Manager 템플릿을 사용하여 Azure-SSIS 통합 런타임을 만듭니다. 다음은 샘플 연습입니다.

1. 다음 Azure Resource Manager 템플릿을 사용하여 JSON 파일을 만듭니다. 대괄호(자리 표시자)의 값을 사용자 고유 값으로 바꿉니다.

    ```json
    {
        "contentVersion": "1.0.0.0",
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "parameters": {},
        "variables": {},
        "resources": [{
            "name": "<Specify a name for your data factory>",
            "apiVersion": "2018-06-01",
            "type": "Microsoft.DataFactory/factories",
            "location": "East US",
            "properties": {},
            "resources": [{
                "type": "integrationruntimes",
                "name": "<Specify a name for your Azure-SSIS IR>",
                "dependsOn": [ "<The name of the data factory you specified at the beginning>" ],
                "apiVersion": "2018-06-01",
                "properties": {
                    "type": "Managed",
                    "typeProperties": {
                        "computeProperties": {
                            "location": "East US",
                            "nodeSize": "Standard_D8_v3",
                            "numberOfNodes": 1,
                            "maxParallelExecutionsPerNode": 8
                        },
                        "ssisProperties": {
                            "catalogInfo": {
                                "catalogServerEndpoint": "<Azure SQL Database server name>.database.windows.net",
                                "catalogAdminUserName": "<Azure SQL Database server admin username>",
                                "catalogAdminPassword": {
                                    "type": "SecureString",
                                    "value": "<Azure SQL Database server admin password>"
                                },
                                "catalogPricingTier": "Basic"
                            }
                        }
                    }
                }
            }]
        }]
    }
    ```

2. Azure Resource Manager 템플릿을 배포하려면 다음 예제와 같이 `New-AzResourceGroupDeployment` 명령을 실행합니다. 이 예제에서 리소스 그룹의 이름은 `ADFTutorialResourceGroup`입니다. `ADFTutorialARM.json`은 데이터 팩터리 및 Azure-SSIS IR에 대한 JSON 정의를 포함하고 있는 파일입니다.

    ```powershell
    New-AzResourceGroupDeployment -Name MyARMDeployment -ResourceGroupName ADFTutorialResourceGroup -TemplateFile ADFTutorialARM.json
    ```

    이 명령은 데이터 팩터리를 만들고 그 안에 Azure-SSIS IR을 만들지만 IR을 시작하지는 않습니다.

3. Azure-SSIS IR을 시작하려면 `Start-AzDataFactoryV2IntegrationRuntime` 명령을 실행합니다.

    ```powershell
    Start-AzDataFactoryV2IntegrationRuntime -ResourceGroupName "<Resource Group Name>" `
        -DataFactoryName "<Data Factory Name>" `
        -Name "<Azure SSIS IR Name>" `
        -Force
    ```

> [!NOTE]
> 이 프로세스는 사용자 지정 설치 시간을 제외하고 5분 이내에 완료됩니다. 그러나 Azure-SSIS IR이 가상 네트워크에 조인하는 데는 20~30분 정도 걸릴 수 있습니다.
>
> SSISDB를 사용하는 경우 Data Factory 서비스에서 데이터베이스 서버에 연결하여 SSISDB를 준비합니다. 또한 이 서비스는 가상 네트워크에 대한 권한 및 설정을 구성하며, 지정된 경우 Azure-SSIS IR을 가상 네트워크에 조인합니다.
> 
> Azure-SSIS IR을 프로비저닝하는 경우 Access 재배포 가능 패키지 및 Azure Feature Pack for SSIS도 설치됩니다. 이러한 구성 요소는 기본 제공 구성 요소에서 이미 지원하는 데이터 원본 외에도 Excel 파일, Access 파일 및 다양한 Azure 데이터 원본에 대한 연결을 제공합니다. 기본 제공/미리 설치된 구성 요소에 대한 자세한 내용은 [Azure-SSIS IR의 기본 제공/미리 설치된 구성 요소](./built-in-preinstalled-components-ssis-integration-runtime.md)를 참조하세요. 설치할 수 있는 추가 구성 요소에 대한 자세한 내용은 [Azure-SSIS IR 사용자 지정 설치](./how-to-configure-azure-ssis-ir-custom-setup.md)를 참조하세요.

## <a name="deploy-ssis-packages"></a>SSIS 패키지 배포

SSISDB를 사용하는 경우 Azure 지원 SSDT 또는 SSMS 도구를 사용하여 SSISDB에 패키지를 배포하고 Azure-SSIS IR에서 실행할 수 있습니다. 이러한 도구는 다음과 같은 서버 엔드포인트를 통해 데이터베이스 서버에 연결됩니다. 

- Azure SQL Database 서버의 경우 서버 엔드포인트 형식은 `<server name>.database.windows.net`입니다.
- 프라이빗 엔드포인트가 있는 관리형 인스턴스의 경우 서버 엔드포인트 형식은 `<server name>.<dns prefix>.database.windows.net`입니다.
- 퍼블릭 엔드포인트가 있는 관리형 인스턴스의 경우 서버 엔드포인트 형식은 `<server name>.public.<dns prefix>.database.windows.net,3342`입니다. 

SSISDB를 사용하지 않는 경우 [dtutil](/sql/integration-services/dtutil-utility) 및 [AzureDTExec](./how-to-invoke-ssis-package-azure-enabled-dtexec.md) 명령줄 유틸리티를 사용하여 Azure SQL Managed Instance가 호스트하는 파일 시스템, Azure Files 또는 MSDB에 패키지를 배포하고 Azure-SSIS IR에서 실행할 수 있습니다. 

자세한 내용은 [SSIS 프로젝트/패키지 배포](/sql/integration-services/packages/deploy-integration-services-ssis-projects-and-packages)를 참조하세요.

또한 두 경우 모두 Data Factory 파이프라인에서 SSIS 패키지 실행 활동을 사용하여 배포된 패키지를 Azure-SSIS IR에서 실행할 수도 있습니다. 자세한 내용은 [SSIS 패키지 실행을 Data Factory 첫 번째 클래스 활동으로 호출](./how-to-invoke-ssis-package-ssis-activity.md)을 참조하세요.

## <a name="next-steps"></a>다음 단계

이 설명서의 다른 Azure-SSIS IR 항목을 참조하세요.

- [Azure-SSIS 통합 런타임](concepts-integration-runtime.md#azure-ssis-integration-runtime). 이 문서에서는 Azure-SSIS IR을 비롯한 일반적인 통합 런타임에 관한 정보를 제공합니다.
- [Azure-SSIS IR 모니터링](monitor-integration-runtime.md#azure-ssis-integration-runtime). 이 문서에서는 Azure SSIS IR에 대한 정보를 가져와 파악하는 방법을 설명합니다.
- [Azure-SSIS IR 관리](manage-azure-ssis-integration-runtime.md). 이 문서에서는 Azure-SSIS IR을 중지하거나, 시작하거나, 삭제하는 방법을 설명합니다. 또한 노드를 추가하여 Azure-SSIS IR 규모를 확장하는 방법도 설명합니다.
- [Azure에서 SSIS 패키지 배포, 실행 및 모니터링](/sql/integration-services/lift-shift/ssis-azure-deploy-run-monitor-tutorial)   
- [Azure에서 SSISDB에 연결](/sql/integration-services/lift-shift/ssis-azure-connect-to-catalog-database)
- [Windows 인증을 사용하는 온-프레미스 데이터 원본에 연결](/sql/integration-services/lift-shift/ssis-azure-connect-with-windows-auth) 
- [Azure에서 패키지 실행 예약](/sql/integration-services/lift-shift/ssis-azure-schedule-packages)
