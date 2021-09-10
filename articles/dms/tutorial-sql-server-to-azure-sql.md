---
title: '자습서: SQL Server를 Azure SQL Database로 오프라인 마이그레이션'
titleSuffix: Azure Database Migration Service
description: Azure Database Migration Service를 사용하여 오프라인으로 SQL Server에서 Azure SQL Database로 마이그레이션하는 방법을 알아봅니다.
services: dms
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: tutorial
ms.date: 01/03/2021
ms.openlocfilehash: ab16bfe708589b6f89dd5b9a37512d4f2148992f
ms.sourcegitcommit: f53f0b98031cd936b2cd509e2322b9ee1acba5d6
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/30/2021
ms.locfileid: "123214914"
---
# <a name="tutorial-migrate-sql-server-to-azure-sql-database-using-dms"></a>자습서: DMS를 사용하여 SQL Server를 Azure SQL Database로 마이그레이션

Azure Database Migration Service를 사용하여 SQL Server 인스턴스에서 [Azure SQL Database](/azure/sql-database/)로 데이터베이스를 마이그레이션할 수 있습니다. 이 자습서에서는 Azure Database Migration Service를 사용하여 SQL Server 2016 이상의 온-프레미스 인스턴스에 복원된 [AdventureWorks2016](/sql/samples/adventureworks-install-configure#download-backup-files) 데이터베이스를 Azure SQL Database의 단일 데이터베이스 또는 풀링된 데이터베이스로 마이그레이션합니다.

다음 방법을 알게 됩니다.
> [!div class="checklist"]
>
> - Data Migration Assistant를 사용하여 온-프레미스 데이터베이스에 차단 문제가 있는지 평가합니다.
> - Data Migration Assistant를 사용하여 데이터베이스 샘플 스키마를 마이그레이션합니다. 
> - Azure DataMigration 리소스 공급자를 등록합니다.
> - Azure Database Migration Service 인스턴스를 만듭니다.
> - Azure Database Migration Service를 사용하여 마이그레이션 프로젝트를 만듭니다.
> - 마이그레이션을 실행합니다.
> - 마이그레이션을 모니터링합니다.


## <a name="prerequisites"></a>사전 요구 사항

이 자습서를 완료하려면 다음이 필요합니다.

- [SQL Server 2016 이상](https://www.microsoft.com/sql-server/sql-server-downloads)을 다운로드하여 설치합니다.
- 문서 [서버 네트워크 프로토콜 사용 또는 사용 안 함](/sql/database-engine/configure-windows/enable-or-disable-a-server-network-protocol#SSMSProcedure)의 지침을 수행하여 SQL Server Express를 설치하는 동안 기본적으로 사용 안 함으로 설정되어 있는 TCP/IP 프로토콜을 사용하도록 설정합니다.
- [AdventureWorks2016 데이터베이스를 SQL Server 인스턴스로 복원합니다.](/sql/samples/adventureworks-install-configure#restore-to-sql-server)
- [Azure Portal을 사용하여 Azure SQL Database에서 데이터베이스 만들기](../azure-sql/database/single-database-create-quickstart.md) 문서의 세부 지침에 따라 Azure SQL Database에 데이터베이스를 만듭니다. 이 자습서에서는 Azure SQL Database의 이름은 **AdventureWorksAzure** 로 가정하지만, 원하는 이름으로 지정할 수 있습니다.

    > [!NOTE]
    > SSIS(SQL Server Integration Services)를 사용하고 있고, SSIS 프로젝트/패키지(SSISDB)용 카탈로그 데이터베이스를 SQL Server에서 Azure SQL Database로 마이그레이션하려는 경우 ADF(Azure Data Factory)에서 SSIS를 프로비저닝할 때 대상 SSISDB가 자동으로 생성되고 관리됩니다. SSIS 패키지 마이그레이션에 대한 자세한 내용은 [SQL Server Integration Services 패키지를 Azure로 마이그레이션](./how-to-migrate-ssis-packages.md) 문서를 참조하세요.
  
- [Data Migration Assistant](https://www.microsoft.com/download/details.aspx?id=53595)의 최신 버전을 다운로드하고 설치합니다.
- Azure Resource Manager 배포 모델을 사용하여 Azure Database Migration Service용 Microsoft Azure Virtual Network를 만듭니다. 그러면 [ExpressRoute](../expressroute/expressroute-introduction.md) 또는 [VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md)을 사용하여 온-프레미스 원본 서버에 사이트 간 연결이 제공됩니다. 가상 네트워크를 만드는 방법에 대한 자세한 내용은 [Virtual Network 설명서](../virtual-network/index.yml)를 참조하세요. 특히 단계별 세부 정보를 제공하는 빠른 시작 문서를 참조하세요.

    > [!NOTE]
    > 가상 네트워크 설정 중에 Microsoft에 대한 네트워크 피어링에서 ExpressRoute를 사용하는 경우 서비스가 프로비저닝되는 서브넷에 다음 서비스 [엔드포인트](../virtual-network/virtual-network-service-endpoints-overview.md)를 추가합니다.
    >
    > - 대상 데이터베이스 엔드포인트(예: SQL 엔드포인트, Cosmos DB 엔드포인트 등)
    > - 스토리지 엔드포인트
    > - Service Bus 엔드포인트
    >
    > Azure Database Migration Service에는 인터넷 연결이 없으므로 이 구성이 필요합니다.
    >
    >온-프레미스 네트워크와 Azure 사이에 사이트 간 연결이 없거나 사이트 간 연결 대역폭이 제한된 경우 하이브리드 모드(미리 보기)에서 Azure Database Migration Service를 사용하는 것이 좋습니다. 하이브리드 모드는 클라우드에서 실행 중인 Azure Database Migration Service 인스턴스와 함께 온-프레미스 마이그레이션 작업자를 활용합니다. 하이브리드 모드에서 Azure Database Migration Service의 인스턴스를 만들려면 [Azure Portal을 사용하여 하이브리드 모드에서 Azure Database Migration Service 인스턴스 만들기](./quickstart-create-data-migration-service-hybrid-portal.md) 문서를 참조하세요.

- 가상 네트워크 Network Security Group 아웃바운드 보안 규칙이 ServiceBus, Storage 및 AzureMonitor용 ServiceTag의 아웃바운드 포트 443을 차단하지 않는지 확인합니다. Azure 가상 네트워크 NSG 트래픽 필터링에 대한 자세한 내용은 [네트워크 보안 그룹을 사용하여 네트워크 트래픽 필터링](../virtual-network/virtual-network-vnet-plan-design-arm.md) 문서를 참조하세요.
- [데이터베이스 엔진 액세스를 위한 Windows 방화벽](/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access)을 구성합니다.
- Azure Database Migration Service가 원본 SQL Server(기본적으로 1433 TCP 포트)에 액세스할 수 있도록 Windows 방화벽을 엽니다. 기본 인스턴스가 일부 다른 포트에서 수신 중인 경우 이를 방화벽에 추가합니다.
- 동적 포트를 사용하여 명명된 여러 SQL Server 인스턴스를 실행하는 경우 SQL Browser 서비스를 사용하도록 설정하고, 방화벽을 통해 1434 UDP 포트에 액세스하도록 허용하여 Azure Database Migration Service가 원본 서버에서 명명된 인스턴스에 연결할 수 있습니다.
- 원본 데이터베이스 앞에 방화벽 어플라이언스를 사용하는 경우 Azure Database Migration Service에서 원본 데이터베이스에 액세스하여 마이그레이션할 수 있도록 허용하는 방화벽 규칙을 추가해야 합니다.
- Azure Database Migration Service에서 대상 데이터베이스에 액세스할 수 있도록 Azure SQL Database에 대한 서버 수준 IP [방화벽 규칙](../azure-sql/database/firewall-configure.md)을 만듭니다. Azure Database Migration Service에 사용되는 가상 네트워크의 서브넷 범위를 입력합니다.
- 원본 SQL Server 인스턴스에 연결하는 데 사용되는 자격 증명에는 [CONTROL SERVER](/sql/t-sql/statements/grant-server-permissions-transact-sql) 권한이 있어야 합니다.
- 대상 Azure SQL Database 인스턴스에 연결하는 데 사용되는 자격 증명에는 대상 데이터베이스에 대한 [CONTROL DATABASE](/sql/t-sql/statements/grant-database-permissions-transact-sql) 권한이 있어야 합니다.

    > [!IMPORTANT]
    > Azure Database Migration Service 인스턴스를 만들려면 일반적으로 동일한 리소스 그룹 내에 있지 않은 가상 네트워크 설정에 액세스해야 합니다. 따라서 DMS의 인스턴스를 만드는 사용자는 구독 수준의 권한이 필요합니다. 필요에 따라 할당할 수 있는 필수 역할을 만들려면 다음 스크립트를 실행합니다.
    >
    > ```
    >
    > $readerActions = `
    > "Microsoft.Network/networkInterfaces/ipConfigurations/read", `
    > "Microsoft.DataMigration/*/read", `
    > "Microsoft.Resources/subscriptions/resourceGroups/read"
    >
    > $writerActions = `
    > "Microsoft.DataMigration/services/*/write", `
    > "Microsoft.DataMigration/services/*/delete", `
    > "Microsoft.DataMigration/services/*/action", `
    > "Microsoft.Network/virtualNetworks/subnets/join/action", `
    > "Microsoft.Network/virtualNetworks/write", `
    > "Microsoft.Network/virtualNetworks/read", `
    > "Microsoft.Resources/deployments/validate/action", `
    > "Microsoft.Resources/deployments/*/read", `
    > "Microsoft.Resources/deployments/*/write"
    >
    > $writerActions += $readerActions
    >
    > # TODO: replace with actual subscription IDs
    > $subScopes = ,"/subscriptions/00000000-0000-0000-0000-000000000000/","/subscriptions/11111111-1111-1111-1111-111111111111/"
    >
    > function New-DmsReaderRole() {
    > $aRole = [Microsoft.Azure.Commands.Resources.Models.Authorization.PSRoleDefinition]::new()
    > $aRole.Name = "Azure Database Migration Reader"
    > $aRole.Description = "Lets you perform read only actions on DMS service/project/tasks."
    > $aRole.IsCustom = $true
    > $aRole.Actions = $readerActions
    > $aRole.NotActions = @()
    >
    > $aRole.AssignableScopes = $subScopes
    > #Create the role
    > New-AzRoleDefinition -Role $aRole
    > }
    >
    > function New-DmsContributorRole() {
    > $aRole = [Microsoft.Azure.Commands.Resources.Models.Authorization.PSRoleDefinition]::new()
    > $aRole.Name = "Azure Database Migration Contributor"
    > $aRole.Description = "Lets you perform CRUD actions on DMS service/project/tasks."
    > $aRole.IsCustom = $true
    > $aRole.Actions = $writerActions
    > $aRole.NotActions = @()
    >
    >   $aRole.AssignableScopes = $subScopes
    > #Create the role
    > New-AzRoleDefinition -Role $aRole
    > }
    > 
    > function Update-DmsReaderRole() {
    > $aRole = Get-AzRoleDefinition "Azure Database Migration Reader"
    > $aRole.Actions = $readerActions
    > $aRole.NotActions = @()
    > Set-AzRoleDefinition -Role $aRole
    > }
    >
    > function Update-DmsConributorRole() {
    > $aRole = Get-AzRoleDefinition "Azure Database Migration Contributor"
    > $aRole.Actions = $writerActions
    > $aRole.NotActions = @()
    > Set-AzRoleDefinition -Role $aRole
    > }
    >
    > # Invoke above functions
    > New-DmsReaderRole
    > New-DmsContributorRole
    > Update-DmsReaderRole
    > Update-DmsConributorRole
    > ```

## <a name="assess-your-on-premises-database"></a>온-프레미스 데이터베이스 평가

SQL Server 인스턴스에서 Azure SQL Database의 단일 데이터베이스 또는 풀링된 데이터베이스로 데이터를 마이그레이션하려면 먼저 마이그레이션을 방해할 수 있는 차단 문제가 있는지 SQL Server 데이터베이스를 평가해야 합니다. Data Migration Assistant를 사용하여 [SQL Server 마이그레이션 평가 수행](/sql/dma/dma-assesssqlonprem) 문서에 설명된 단계를 수행하여 온-프레미스 데이터베이스 평가를 완료합니다. 필수 단계의 요약은 다음과 같습니다.

1. Data Migration Assistant에서 새로 만들기(+) 아이콘을 선택하고 **평가** 프로젝트 형식을 선택합니다.
2. 프로젝트 이름을 지정합니다. **평가 유형** 드롭다운 목록에서 **데이터베이스 엔진** 을 선택하고, **원본 서버 유형** 텍스트 상자에서 **SQL Server** 를 선택하고, **대상 서버 유형** 텍스트 상자에서 **Azure SQL Database** 를 선택한 다음, **만들기** 를 선택하여 프로젝트를 만듭니다.

    Azure SQL Database의 단일 데이터베이스 또는 풀링된 데이터베이스로 마이그레이션할 원본 SQL Server 데이터베이스를 평가할 때 다음 평가 보고서 유형 중 하나 또는 둘 다 선택할 수 있습니다.

   - 데이터베이스 호환성 확인
   - 기능 패리티 확인

    두 보고서 유형이 모두 기본적으로 선택됩니다.

3. Data Migration Assistant의 **옵션** 화면에서 **다음** 을 선택합니다.
4. **원본 선택** 화면의 **서버에 연결** 대화 상자에서 SQL Server에 대한 연결 세부 정보를 제공하고 **연결** 을 선택합니다.
5. **원본 추가** 대화 상자에서 **AdventureWorks2016** 을 선택하고 **추가** 를 선택한 후 **평가 시작** 을 선택합니다.

    > [!NOTE]
    > SSIS를 사용하는 경우 DMA가 현재 원본 SSISDB의 평가를 지원하지 않습니다. 그러나 SSIS 프로젝트/패키지는 Azure SQL Database에서 호스팅하는 대상 SSISDB에 재배포되므로 평가/유효성 검사되지 않습니다. SSIS 패키지 마이그레이션에 대한 자세한 내용은 [SQL Server Integration Services 패키지를 Azure로 마이그레이션](./how-to-migrate-ssis-packages.md) 문서를 참조하세요.

    평가가 완료되면 다음 그래픽에 표시된 대로 결과가 표시됩니다.

    ![데이터 마이그레이션 평가](media/tutorial-sql-server-to-azure-sql/dma-assessments.png)

    Azure SQL Database의 데이터베이스에 대한 평가를 통해 단일 데이터베이스 또는 풀링된 데이터베이스에 배포하는 것을 방해하는 기능 패리티 문제 및 마이그레이션 차단 문제를 식별합니다.

    - **SQL Server 기능 패리티** 범주는 전체 권장 사항 집합, Azure에서 사용 가능한 대체 방법 및 마이그레이션 프로젝트에 대한 작업을 계획하는 데 도움이 되는 완화 단계를 제공합니다.
    - **호환성 문제** 범주는 SQL Server 데이터베이스를 Azure SQL Database로 마이그레이션하는 작업을 차단할 수 있는 호환성 문제를 반영하는 기능을 부분적으로 지원하거나 지원하지 않는 기능을 식별합니다. 해당 문제를 해결하는 데 도움이 되는 권장 사항도 제공됩니다.

6. 특정 옵션을 선택하여 마이그레이션 차단 문제와 기능 패리티 문제에 대한 평가 결과를 검토합니다.

## <a name="migrate-the-sample-schema"></a>샘플 스키마 마이그레이션

평가가 만족스럽고 선택한 데이터베이스가 Azure SQL Database의 단일 데이터베이스 또는 풀링된 데이터베이스로 마이그레이션하는 데 적합한 후보라고 판단되면 DMA를 사용하여 스키마를 Azure SQL Database로 마이그레이션합니다.

> [!NOTE]
> Data Migration Assistant에서 마이그레이션 프로젝트를 만들기 전에 사전 요구 사항에 설명된 대로 Azure에서 데이터베이스를 이미 프로비저닝했는지 확인해야 합니다. 

> [!IMPORTANT]
> SSIS를 사용하는 경우 DMA는 현재 원본 SSISDB 마이그레이션을 지원하지 않지만 Azure SQL Database에서 호스팅하는 대상 SSISDB에 SSIS 프로젝트/패키지를 재배포할 수 있습니다. SSIS 패키지 마이그레이션에 대한 자세한 내용은 [SQL Server Integration Services 패키지를 Azure로 마이그레이션](./how-to-migrate-ssis-packages.md) 문서를 참조하세요.

**AdventureWorks2016** 스키마를 단일 데이터베이스 또는 풀링된 데이터베이스 Azure SQL Database로 마이그레이션하려면 다음 단계를 수행합니다.

1. Data Migration Assistant에서 새로 만들기(+) 아이콘을 선택하고 **속성 유형** 에서 **마이그레이션** 을 선택합니다.
2. 프로젝트 이름을 지정하고, **원본 서버 유형** 텍스트 상자에서 **SQL Server** 를 선택한 다음, **대상 서버 유형** 텍스트 상자에서 **Azure SQL Database** 를 선택합니다.
3. **마이그레이션 범위** 에서 **스키마만** 을 선택합니다.

    이전 단계를 수행한 후 다음 그래픽에 표시된 대로 Data Migration Assistant 인터페이스가 표시됩니다.

    ![Data Migration Assistant 프로젝트 만들기](media/tutorial-sql-server-to-azure-sql/dma-create-project.png)

4. **만들기** 를 선택하여 프로젝트를 만듭니다.
5. Data Migration Assistant에서 SQL Server에 대한 원본 연결 세부 정보를 지정하고, **연결** 을 선택하고 나서, **AdventureWorks2016** 데이터베이스를 선택합니다.

    ![Data Migration Assistant 원본 연결 세부 정보](media/tutorial-sql-server-to-azure-sql/dma-source-connect.png)

6. **대상 서버에 연결** 아래에서 **다음** 을 선택하고, Azure SQL Database에 대한 대상 연결 세부 정보를 지정하고 **연결** 을 선택한 다음, Azure SQL Database에서 미리 프로비저닝된 **AdventureWorksAzure** 데이터베이스를 선택합니다.

    ![Data Migration Assistant 대상 연결 세부 정보](media/tutorial-sql-server-to-azure-sql/dma-target-connect.png)

7. **다음** 을 선택하여 **개체 선택** 화면으로 이동합니다. 여기에서 Azure SQL Database에 배포해야 하는 **AdventureWorks2016** 데이터베이스의 스키마 개체를 지정할 수 있습니다.

    기본적으로 모든 개체가 선택됩니다.

    ![SQL 스크립트 생성](media/tutorial-sql-server-to-azure-sql/dma-assessment-source.png)

8. **SQL 스크립트 생성** 을 선택하여 SQL 스크립트를 만들고 스크립트에 오류가 있는지 검토합니다.

    ![스키마 스크립트](media/tutorial-sql-server-to-azure-sql/dma-schema-script.png)

9. **스키마 배포** 를 선택하여 Azure SQL Database에 스키마를 배포하고, 스키마가 배포된 후 대상 서버에 이상이 있는지 확인합니다.

    ![스키마 배포](media/tutorial-sql-server-to-azure-sql/dma-schema-deploy.png)

[!INCLUDE [resource-provider-register](../../includes/database-migration-service-resource-provider-register.md)]   

## <a name="create-an-azure-database-migration-service-instance"></a>Azure Database Migration Service 인스턴스 만들기

1. Azure Portal 메뉴 또는 **홈** 페이지에서 **리소스 만들기** 를 선택합니다. **Azure Database Migration Service** 를 검색하여 선택합니다

    ![Azure Marketplace](media/tutorial-sql-server-to-azure-sql/portal-marketplace.png)

2. **Azure Database Migration Service** 화면에서 **만들기** 를 선택합니다.

    ![Azure Database Migration Service 인스턴스 만들기](media/tutorial-sql-server-to-azure-sql/dms-create-1.png)
  
3. **마이그레이션 서비스 만들기** 기본 사항 화면에서:

     - 구독을 선택합니다.
     - 새로운 리소스 그룹을 지정하거나 기존 항목을 만듭니다.
     - Azure Database Migration Service의 인스턴스 이름를 지정합니다.
     - Azure Database Migration Service 인스턴스를 만들 위치를 선택합니다.
     - **Azure** 를 서비스 모드로 선택합니다.
     - 가격 책정 계층을 선택합니다. 비용 및 가격 책정 계층에 대한 자세한 내용은 [가격 책정 페이지](https://aka.ms/dms-pricing)를 참조하세요.

    ![Azure Database Migration Service 인스턴스 기본 사항 설정 구성](media/tutorial-sql-server-to-azure-sql/dms-settings-2.png)

     - **다음: 네트워킹** 을 선택합니다.

4. **마이그레이션 서비스 만들기** 네트워킹 화면에서:

    - 기존 가상 네트워크를 선택하거나 새로 만듭니다. 가상 네트워크는 원본 SQL Server 및 대상 Azure SQL Database 인스턴스에 대한 액세스 권한이 있는 Azure Database Migration Service를 제공합니다. Azure Portal에서 가상 네트워크를 만드는 방법에 대한 자세한 내용은 [Azure Portal을 사용하여 가상 네트워크 만들기](../virtual-network/quick-create-portal.md) 문서를 참조하세요.

    ![Azure Database Migration Service 인스턴스 네트워킹 설정 구성](media/tutorial-sql-server-to-azure-sql/dms-settings-3.png)

    - **검토 + 만들기** 를 선택하여 세부 정보를 검토한 다음 **만들기** 를 선택하여 서비스를 만듭니다.

## <a name="create-a-migration-project"></a>마이그레이션 프로젝트 만들기

서비스가 생성된 후 Azure Portal에서 서비스를 찾아 연 다음, 새로운 마이그레이션 프로젝트를 만듭니다.

1. Azure Portal 메뉴에서 **모든 서비스** 를 선택합니다. **Azure Database Migration Services** 를 검색하여 선택합니다

     ![Azure Database Migration Service의 모든 인스턴스 찾기](media/tutorial-sql-server-to-azure-sql/dms-search.png)

2. **Azure Database Migration Services** 화면에서 직접 만든 Azure Database Migration Service 인스턴스를 선택합니다.

3. **새 마이그레이션 프로젝트** 를 선택합니다.

     ![Azure Database Migration Service 인스턴스 찾기](media/tutorial-sql-server-to-azure-sql/dms-instance-search.png)

4. **새 마이그레이션 프로젝트** 화면에서 프로젝트의 이름을 지정하고, **원본 서버 형식** 텍스트 상자에서 **SQL Server** 를 선택하고, **대상 서버 형식** 텍스트 상자에서 **Azure SQL Database** 를 선택한 다음, **마이그레이션 작업 형식 선택** 에서 **데이터 마이그레이션** 을 선택합니다.

    ![Database Migration Service 프로젝트 만들기](media/tutorial-sql-server-to-azure-sql/dms-create-project-2.png)

5. **작업 만들기 및 실행** 을 선택하여 프로젝트를 만들고 마이그레이션 작업을 실행합니다.

## <a name="specify-source-details"></a>원본 세부 정보 지정

1. **원본 선택** 화면에서 원본 SQL Server 인스턴스에 대한 연결 세부 정보를 지정합니다.

    원본 SQL Server 인스턴스 이름에 정규화된 도메인 이름(FQDN)을 사용하도록 합니다. 또한 DNS 이름을 확인할 수 없는 경우에는 IP 주소를 사용할 수도 있습니다.

2. 원본 서버에 신뢰할 수 있는 인증서가 설치되지 않은 경우 **서버 인증서 신뢰** 확인란을 선택합니다.

    신뢰할 수 있는 인증서가 설치되지 않은 경우 인스턴스가 시작될 때SQL Server가 자체 서명 인증서를 생성합니다. 이 인증서는 클라이언트 연결에 대한 자격 증명을 암호화하는 데 사용됩니다.

    > [!CAUTION]
    > 자체 서명 인증서를 사용하여 암호화된 TLS 연결은 강력한 보안을 제공하지 않습니다. 중간자 공격(man-in-the-middle)을 받기 쉽습니다. 프로덕션 환경이나 인터넷에 연결된 서버에서는 자체 서명된 인증서를 사용한 TLS에 의존해서는 안 됩니다.

    > [!IMPORTANT]
    > SSIS를 사용하는 경우 DMS는 현재 원본 SSISDB 마이그레이션을 지원하지 않지만 Azure SQL Database에서 호스팅하는 대상 SSISDB에 SSIS 프로젝트/패키지를 재배포할 수 있습니다. SSIS 패키지 마이그레이션에 대한 자세한 내용은 [SQL Server Integration Services 패키지를 Azure로 마이그레이션](./how-to-migrate-ssis-packages.md) 문서를 참조하세요.

   ![원본 세부 정보](media/tutorial-sql-server-to-azure-sql/dms-source-details-2.png)
   
3. **다음: 데이터베이스 선택** 을 선택합니다.

## <a name="select-databases-for-migration"></a>마이그레이션할 데이터베이스 선택

Azure SQL Database로 마이그레이션할 모든 데이터베이스 또는 특정 데이터베이스를 선택합니다. DMS는 선택한 데이터베이스에 대해 예상되는 마이그레이션 시간을 제공합니다. 마이그레이션 가동 중지 시간이 허용되면 마이그레이션을 계속합니다. 마이그레이션 가동 중지 시간이 허용되지 않는 경우에는 [가동 중지 시간이 0에 가까운 SQL Managed Instance](tutorial-sql-server-managed-instance-online.md)로 마이그레이션하거나, 다른 옵션을 위해 [DMS 팀](mailto:DMSFeedback@microsoft.com) 에 문의하는 것이 좋습니다. 

1. 사용 가능한 데이터베이스 목록에서 마이그레이션하려는 데이터베이스를 선택합니다. 
1. 예상 가동 중지 시간을 검토합니다. 해당 시간이 허용되면 **다음: 대상 선택 >>** 을 선택합니다.

   ![원본 데이터베이스](media/tutorial-sql-server-to-azure-sql/select-database.png)



## <a name="specify-target-details"></a>대상 세부 정보 지정

1. **대상 선택** 화면에서 Azure SQL Database에 대한 인증 설정을 제공합니다. 

   ![대상 선택](media/tutorial-sql-server-to-azure-sql/select-target.png)
   
   > [!NOTE]
   > 현재 유일하게 지원되는 인증 유형은 SQL 인증입니다.

1. **다음: 대상 데이터베이스에 매핑** 화면을 선택하고 마이그레이션하기 위해 원본 및 대상 데이터베이스를 매핑합니다.

    대상 데이터베이스의 이름이 원본 데이터베이스와 동일하면 Azure Database Migration Service는 기본적으로 이 대상 데이터베이스를 선택합니다.

    ![대상 데이터베이스에 매핑](media/tutorial-sql-server-to-azure-sql/dms-map-targets-activity-2.png)

1. **다음: 구성 마이그레이션 설정** 을 선택하고 테이블 목록을 확장한 다음, 영향을 받는 필드의 목록을 검토합니다.

    Azure Database Migration Service는 대상 Azure SQL Database 인스턴스에 있는 모든 빈 원본 테이블을 자동으로 선택합니다. 이미 데이터를 포함하는 테이블을 다시 마이그레이션하려면 이 블레이드에서 테이블을 명시적으로 선택해야 합니다.

    ![테이블 선택](media/tutorial-sql-server-to-azure-sql/dms-configure-setting-activity-2.png)

1. **다음: 요약** 을 선택하고, 마이그레이션 구성을 검토하고, **작업 이름** 텍스트 상자에서 마이그레이션 작업의 이름을 지정합니다.

    ![유효성 검사 옵션 선택](media/tutorial-sql-server-to-azure-sql/dms-configuration-2.png)

## <a name="run-the-migration"></a>마이그레이션 실행

- **마이그레이션 시작** 을 선택합니다.

    마이그레이션 작업 창이 나타나고, 작업 **상태** 는 **보류 중** 입니다.

    ![활동 상태](media/tutorial-sql-server-to-azure-sql/dms-activity-status-1.png)

## <a name="monitor-the-migration"></a>마이그레이션 모니터링

1. 마이그레이션 작업 화면에서 **새로 고침** 을 선택하여 마이그레이션 **상태** 가 **완료됨** 으로 표시될 때까지 디스플레이를 업데이트합니다.

    ![작업 상태 완료됨](media/tutorial-sql-server-to-azure-sql/dms-completed-activity-1.png)

2. 대상 **Azure SQL Database** 에서 대상 데이터베이스를 확인합니다.

### <a name="additional-resources"></a>추가 자료

- Azure Database Migration Service에 대한 자세한 내용은 [Azure Database Migration Service란?](./dms-overview.md) 문서를 참조하세요.
- Azure SQL Database에 대한 자세한 내용은 [Azure SQL Database 서비스란?](../azure-sql/database/sql-database-paas-overview.md) 문서를 참조하세요.
