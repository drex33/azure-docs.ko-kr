---
title: '자습서: SQL Server를 SQL Managed Instance로 마이그레이션'
titleSuffix: Azure Database Migration Service
description: Azure Database Migration Service를 사용하여 SQL Server에서 Azure SQL Managed Instance로 마이그레이션하는 방법을 알아봅니다.
services: dms
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019,fasttrack-edit
ms.topic: tutorial
ms.date: 08/16/2021
ms.openlocfilehash: 0ff725703d96b524b9c6a45c84e95ae45af33cf8
ms.sourcegitcommit: f53f0b98031cd936b2cd509e2322b9ee1acba5d6
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/30/2021
ms.locfileid: "123214822"
---
# <a name="tutorial-migrate-sql-server-to-an-azure-sql-managed-instance-offline-using-dms"></a>자습서: DMS를 사용하여 SQL Server를 Azure SQL Managed Instance로 오프라인 마이그레이션

Azure Database Migration Service를 사용하여 SQL Server 인스턴스에서 [Azure SQL Managed Instance](../azure-sql/managed-instance/sql-managed-instance-paas-overview.md)로 데이터를 마이그레이션할 수 있습니다. 수동 작업이 필요한 추가적인 방법은 [SQL Server를 Azure SQL Managed Instance로](../azure-sql/migration-guides/managed-instance/sql-server-to-managed-instance-guide.md) 문서를 참조하세요.

이 자습서에서는 Azure Database Migration Service를 사용하여 [AdventureWorks2016](/sql/samples/adventureworks-install-configure#download-backup-files) 데이터베이스를 SQL Server의 온-프레미스 인스턴스에서 SQL Managed Instance로 마이그레이션합니다.

다음 방법을 알게 됩니다.
> [!div class="checklist"]
>
> - Azure DataMigration 리소스 공급자를 등록합니다.
> - Azure Database Migration Service 인스턴스를 만듭니다.
> - Azure Database Migration Service를 사용하여 마이그레이션 프로젝트를 만듭니다.
> - 마이그레이션을 실행합니다.
> - 마이그레이션을 모니터링합니다.

> [!IMPORTANT]
> SQL Server에서 SQL Managed Instance로 오프라인 마이그레이션의 경우 Azure Database Migration Service에서 백업 파일을 만들 수 있습니다. 또는 서비스에서 데이터베이스를 마이그레이션하는 데 사용할 SMB 네트워크 공유에 최신 전체 데이터베이스 백업을 제공할 수 있습니다. 각 백업은 별도의 백업 파일 또는 여러 백업 파일에 쓸 수 있습니다. 그러나 단일 백업 미디어에 여러 백업을 추가하는 것은 지원되지 않습니다. 압축된 백업도 사용하여 대량 백업 마이그레이션과 관련된 잠재적인 문제 발생 가능성을 줄일 수 있습니다.

[!INCLUDE [online-offline](../../includes/database-migration-service-offline-online.md)]

이 문서에서는 SQL Server에서 SQL Managed Instance로 오프라인 마이그레이션을 설명합니다. 온라인 마이그레이션의 경우 [DMS를 사용하여 SQL Server를 SQL Managed Instance로 온라인 마이그레이션](tutorial-sql-server-managed-instance-online.md)을 참조하세요.

## <a name="prerequisites"></a>사전 요구 사항

이 자습서를 완료하려면 다음이 필요합니다.

- [SQL Server 2016 이상](https://www.microsoft.com/sql-server/sql-server-downloads)을 다운로드하여 설치합니다.
- 문서 [서버 네트워크 프로토콜 사용 또는 사용 안 함](/sql/database-engine/configure-windows/enable-or-disable-a-server-network-protocol#SSMSProcedure)의 지침을 수행하여 SQL Server Express를 설치하는 동안 기본적으로 사용 안 함으로 설정되어 있는 TCP/IP 프로토콜을 사용하도록 설정합니다.
- [AdventureWorks2016 데이터베이스를 SQL Server 인스턴스로 복원합니다.](/sql/samples/adventureworks-install-configure#restore-to-sql-server)
- Azure Resource Manager 배포 모델을 사용하여 Azure Database Migration Service용 Microsoft Azure Virtual Network를 만듭니다. 그러면 [ExpressRoute](../expressroute/expressroute-introduction.md) 또는 [VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md)을 사용하여 온-프레미스 원본 서버에 사이트 간 연결이 제공됩니다. [Azure Database Migration Service를 사용한 SQL Managed Instance 마이그레이션에 대한 네트워크 토폴로지를 알아봅니다](./resource-network-topologies.md). 가상 네트워크를 만드는 방법에 대한 자세한 내용은 [Virtual Network 설명서](../virtual-network/index.yml)를 참조하세요. 특히 단계별 세부 정보를 제공하는 빠른 시작 문서를 참조하세요.

    > [!NOTE]
    > 가상 네트워크 설정 중에 Microsoft에 대한 네트워크 피어링에서 ExpressRoute를 사용하는 경우 서비스가 프로비저닝되는 서브넷에 다음 서비스 [엔드포인트](../virtual-network/virtual-network-service-endpoints-overview.md)를 추가합니다.
    > - 대상 데이터베이스 엔드포인트(예: SQL 엔드포인트, Cosmos DB 엔드포인트 등)
    > - 스토리지 엔드포인트
    > - Service Bus 엔드포인트
    >
    > Azure Database Migration Service에는 인터넷 연결이 없으므로 이 구성이 필요합니다.

- 가상 네트워크 Network Security Group 규칙이 ServiceBus, Storage 및 AzureMonitor용 ServiceTag의 아웃바운드 포트 443을 차단하지 않는지 확인합니다. 가상 네트워크 NSG 트래픽 필터링에 대한 자세한 내용은 [네트워크 보안 그룹을 사용하여 네트워크 트래픽 필터링](../virtual-network/virtual-network-vnet-plan-design-arm.md) 문서를 참조하세요.
- [소스 데이터베이스 엔진 액세스를 위한 Windows 방화벽](/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access)을 구성합니다.
- Azure Database Migration Service가 원본 SQL Server(기본적으로 1433 TCP 포트)에 액세스할 수 있도록 Windows 방화벽을 엽니다. 기본 인스턴스가 일부 다른 포트에서 수신 중인 경우 이를 방화벽에 추가합니다.
- 동적 포트를 사용하여 명명된 여러 SQL Server 인스턴스를 실행하는 경우 SQL Browser 서비스를 사용하도록 설정하고, 방화벽을 통해 1434 UDP 포트에 액세스하도록 허용하여 Azure Database Migration Service가 원본 서버에서 명명된 인스턴스에 연결할 수 있습니다.
- 원본 데이터베이스 앞에 방화벽 어플라이언스를 사용하는 경우, Azure Database Migration Service에서 마이그레이션을 위해 445 SMB 포트를 통해 파일뿐만 아니라 원본 데이터베이스에 액세스할 수 있도록 허용하는 방화벽 규칙을 추가해야 합니다.
- [Azure Portal에서 SQL Managed Instance 만들기](../azure-sql/managed-instance/instance-create-quickstart.md) 문서의 세부 정보에 따라 SQL Managed Instance를 만듭니다.
- 원본 SQL Server와 대상 SQL Managed Instance를 연결하는 데 사용되는 로그인이 sysadmin 서버 역할의 멤버인지 확인합니다.

    >[!NOTE]
    >기본적으로 Azure Database Migration Service는 SQL 로그인 마이그레이션만 지원합니다. 하지만 다음과 같은 방법으로 Windows 로그인을 마이그레이션하는 기능을 사용하도록 설정할 수 있습니다.
    >
    >- 대상 SQL Managed Instance에 AAD 읽기 액세스 권한이 있는지 확인합니다. 이 권한은 **전역 관리자** 역할이 있는 사용자가 Azure Portal을 통해 구성할 수 있습니다.
    >- Windows 사용자/그룹 로그인 마이그레이션이 가능하도록 Azure Database Migration Service 인스턴스를 구성합니다. 이 구성은 Azure Portal을 통해 구성 페이지에서 설정할 수 있습니다. 이 설정을 사용하도록 설정한 후 서비스를 다시 시작하면 변경 사항이 적용됩니다.
    >
    > 서비스를 다시 시작하면 마이그레이션에 사용할 수 있는 로그인 목록에 Windows 사용자/그룹 로그인이 나타납니다. 마이그레이션하는 Windows 사용자/그룹 로그인에 대해서는 연결된 도메인 이름을 입력하라는 메시지가 표시됩니다. 서비스 사용자 계정(도메인 이름이 NT AUTHORITY인 계정) 및 가상 사용자 계정(도메인 이름이 NT SERVICE인 계정 이름)은 지원 되지 않습니다.

- Azure Database Migration Service가 원본 데이터베이스를 백업하는 데 사용할 수 있는 네트워크 공유를 만듭니다.
- 원본 SQL Server 인스턴스를 실행 중인 서비스 계정에 본인이 만든 네트워크 공유에 대한 쓰기 권한이 있고, 원본 서버의 컴퓨터 계정에 동일한 공유에 대한 읽기/쓰기 액세스 권한이 있는지 확인합니다.
- 이전에 만든 네트워크 공유에 대한 전체 제어 권한을 갖고 있는 Windows 사용자(및 암호)를 메모해 둡니다. Azure Database Migration Service는 사용자 자격 증명을 가장하여 복원 작업을 위해 백업 파일을 Azure Storage 컨테이너에 업로드합니다.
- [Storage Explorer를 사용하여 Azure Blob Storage 리소스 관리](../vs-azure-tools-storage-explorer-blobs.md#get-the-sas-for-a-blob-container) 문서의 단계를 사용하여 Blob 컨테이너를 만들고 해당 SAS URI를 검색합니다. SAS URI를 만드는 동안 정책 창에서 모든 권한(읽기, 쓰기, 삭제, 나열)을 선택해야 합니다. 이 세부 정보는 SQL Managed Instance로 데이터베이스를 마이그레이션하는 데 사용되는 백업 파일을 업로드할 수 있도록 스토리지 계정 컨테이너에 대한 액세스 권한을 Azure Database Migration Service에 제공합니다.

    > [!NOTE]
    > Azure Database Migration Service는 [마이그레이션 설정 구성](#configure-migration-settings) 단계 중에 스토리지 계정 설정을 구성할 때 계정 수준 SAS 토큰을 사용하도록 지원하지 않습니다.
    
[!INCLUDE [resource-provider-register](../../includes/database-migration-service-resource-provider-register.md)]

## <a name="create-an-azure-database-migration-service-instance"></a>Azure Database Migration Service 인스턴스 만들기

1. Azure Portal 메뉴 또는 **홈** 페이지에서 **리소스 만들기** 를 선택합니다. **Azure Database Migration Service** 를 검색하여 선택합니다

    ![Azure Marketplace](media/tutorial-sql-server-to-managed-instance/portal-marketplace.png)

2. **Azure Database Migration Service** 화면에서 **만들기** 를 선택합니다.

    ![Azure Database Migration Service 인스턴스 만들기](media/tutorial-sql-server-to-managed-instance/dms-create-service-1.png)

3. **마이그레이션 서비스 만들기** 기본 사항 화면에서:

     - 구독을 선택합니다.
     - 새로운 리소스 그룹을 지정하거나 기존 항목을 만듭니다.
     - Azure Database Migration Service의 인스턴스 이름를 지정합니다.
     - Azure Database Migration Service 인스턴스를 만들 위치를 선택합니다.
     - **Azure** 를 서비스 모드로 선택합니다.
     - 가격 책정 계층을 선택합니다. 비용 및 가격 책정 계층에 대한 자세한 내용은 [가격 책정 페이지](https://aka.ms/dms-pricing)를 참조하세요.

    ![Azure Database Migration Service 인스턴스 기본 사항 설정 구성](media/tutorial-sql-server-to-managed-instance/dms-create-service-2.png)

     - **다음: 네트워킹** 을 선택합니다.

4. **마이그레이션 서비스 만들기** 네트워킹 화면에서:

    - 기존 가상 네트워크를 선택하거나 새로 만듭니다. 가상 네트워크는 원본 SQL Server와 대상 Azure SQL Managed Instance에 대한 액세스 권한이 있는 Azure Database Migration Service를 제공합니다.
     
    - Azure Portal에서 가상 네트워크를 만드는 방법에 대한 자세한 내용은 [Azure Portal을 사용하여 가상 네트워크 만들기](../virtual-network/quick-create-portal.md) 문서를 참조하세요.
    
    - 자세한 내용은 [Azure Database Migration Service를 사용한 Azure SQL Managed Instance 마이그레이션에 대한 네트워크 토폴로지](./resource-network-topologies.md) 문서를 참조하세요.

    ![Azure Database Migration Service 인스턴스 네트워킹 설정 구성](media/tutorial-sql-server-to-managed-instance/dms-create-service-3.png)

    - **검토 + 만들기** 를 선택하여 세부 정보를 검토한 다음 **만들기** 를 선택하여 서비스를 만듭니다.

## <a name="create-a-migration-project"></a>마이그레이션 프로젝트 만들기

서비스 인스턴스가 생성된 후 Azure Portal에서 서비스를 찾아 연 다음, 새로운 마이그레이션 프로젝트를 만듭니다.

1. Azure Portal 메뉴에서 **모든 서비스** 를 선택합니다. **Azure Database Migration Services** 를 검색하여 선택합니다

    ![Azure Database Migration Service의 모든 인스턴스 찾기](media/tutorial-sql-server-to-managed-instance/dms-search.png)

2. **Azure Database Migration Services** 화면에서 직접 만든 Azure Database Migration Service 인스턴스를 선택합니다.

3. **새 마이그레이션 프로젝트** 를 선택합니다.

     ![Azure Database Migration Service 인스턴스 찾기](media/tutorial-sql-server-to-managed-instance/dms-create-project-1.png)

4. **새 마이그레이션 프로젝트** 화면에서 프로젝트의 이름을 지정하고, **원본 서버 형식** 텍스트 상자에서 **SQL Server** 를 선택하고, **대상 서버 형식** 텍스트 상자에서 **Azure SQL Database Managed Instance** 를 선택한 다음, **작업 형식 선택** 에서 **오프라인 데이터 마이그레이션** 을 선택합니다.

   ![Database Migration Service 프로젝트 만들기](media/tutorial-sql-server-to-managed-instance/dms-create-project-2.png)

5. **작업 만들기 및 실행** 을 선택하여 프로젝트를 만들고 마이그레이션 작업을 실행합니다.

## <a name="specify-source-details"></a>원본 세부 정보 지정

1. **원본 선택** 화면에서 원본 SQL Server 인스턴스에 대한 연결 세부 정보를 지정합니다.

    원본 SQL Server 인스턴스 이름에 정규화된 도메인 이름(FQDN)을 사용하도록 합니다. 또한 DNS 이름을 확인할 수 없는 경우에는 IP 주소를 사용할 수도 있습니다.

2. 서버에 신뢰할 수 있는 인증서가 설치되지 않은 경우 **서버 인증서 신뢰** 확인란을 선택합니다.

    신뢰할 수 있는 인증서가 설치되어 있지 않으면 인스턴스가 시작될 때 SQL Server에서 자체 서명 인증서를 생성합니다. 이 인증서는 클라이언트 연결에 대한 자격 증명을 암호화하는 데 사용됩니다.

    > [!CAUTION]
    > 자체 서명 인증서를 사용하여 암호화된 TLS 연결은 강력한 보안을 제공하지 않습니다. 중간자 공격(man-in-the-middle)을 받기 쉽습니다. 프로덕션 환경이나 인터넷에 연결된 서버에서는 자체 서명된 인증서를 사용한 TLS에 의존해서는 안 됩니다.

   ![원본 세부 정보](media/tutorial-sql-server-to-managed-instance/dms-source-details.png)

3. **다음: 대상 선택** 선택

## <a name="specify-target-details"></a>대상 세부 정보 지정

1. **대상 선택** 화면에서 대상에 대한 연결 세부 정보를 지정합니다. 이 대상은 **AdventureWorks2016** 데이터베이스를 마이그레이션할 미리 프로비저닝된 SQL Managed Instance입니다.

    SQL Managed Instance를 아직 프로비저닝하지 않은 경우 인스턴스를 프로비저닝하는 데 도움이 되는 [링크](../azure-sql/managed-instance/instance-create-quickstart.md)를 선택합니다. 프로젝트 생성은 계속 진행할 수 있으며, SQL Managed Instance가 준비되면 이 특정 프로젝트로 돌아가서 마이그레이션을 실행합니다.

    ![대상 선택](media/tutorial-sql-server-to-managed-instance/dms-target-details.png)

2. **다음: 데이터베이스 선택** 을 선택합니다. **데이터베이스 선택** 화면에서 마이그레이션할 **AdventureWorks2016** 데이터베이스를 선택합니다.

   ![원본 데이터베이스 선택](media/tutorial-sql-server-to-managed-instance/dms-source-database.png)

    > [!IMPORTANT]
    > SSIS(SQL Server Integration Services)를 사용하는 경우 DMS는 현재 SSIS 프로젝트/패키지의 카탈로그 데이터베이스(SSISDB)를 SQL Server에서 SQL Managed Instance로 마이그레이션하는 기능을 지원하지 않습니다. 하지만 ADF(Azure Data Factory)에 SSIS를 프로비저닝하고 SQL Managed Instance에서 호스팅하는 대상 SSISDB에 SSIS 프로젝트/패키지를 재배포할 수 있습니다. SSIS 패키지 마이그레이션에 대한 자세한 내용은 [SQL Server Integration Services 패키지를 Azure로 마이그레이션](./how-to-migrate-ssis-packages.md) 문서를 참조하세요.

3. **다음: 로그인 선택** 을 선택합니다.

## <a name="select-logins"></a>로그인 선택

1. **로그인 선택** 화면에서 마이그레이션할 로그인을 선택합니다.

    >[!NOTE]
    >기본적으로 Azure Database Migration Service는 SQL 로그인 마이그레이션만 지원합니다. Windows 로그인 마이그레이션을 지원할 수 있도록 하려면 이 자습서의 **사전 요구 사항** 섹션을 참조하세요.

    ![로그인 선택](media/tutorial-sql-server-to-managed-instance/dms-select-logins.png)

2. **다음: 마이그레이션 설정 구성** 을 선택합니다.

## <a name="configure-migration-settings"></a>마이그레이션 설정 구성

1. **마이그레이션 설정 구성** 화면에서 다음 세부 정보를 입력합니다.

    | 매개 변수 | Description |
    |--------|---------|
    |**원본 백업 옵션 선택** | DMS가 데이터베이스 마이그레이션에 사용할 수 있는 전체 백업 파일이 이미 있으면 **최신 백업 파일을 제공합니다** 옵션을 선택합니다. DMS에서 원본 데이터베이스 전체 백업을 처음으로 수행하고 마이그레이션에 이 백업을 사용하도록 하려면 **Azure Database Migration Service를 통해 백업 파일을 만들려고 합니다** 옵션을 선택합니다. |
    |**네트워크 위치 공유** | Azure Database Migration Service가 원본 데이터베이스를 백업하는 데 사용할 수 있는 로컬 SMB 네트워크 공유입니다. 원본 SQL Server 인스턴스를 실행하는 서비스 계정에는 이 네트워크 공유에 대한 쓰기 권한이 있어야 합니다. 네트워크 공유에 있는 서버의 FQDN 또는 IP 주소를 입력합니다(예: '\\\servername.domainname.com\backupfolder' 또는 '\\\IP address\backupfolder').|
    |**사용자 이름** | Windows 사용자가 위에서 입력한 네트워크 공유에 대한 전체 제어 권한을 갖고 있는지 확인합니다. Azure Database Migration Service는 사용자 자격 증명을 가장하여 복원 작업을 위한 Azure Storage 컨테이너에 백업 파일을 업로드합니다. TDE 가능 데이터베이스를 마이그레이션하기로 선택하는 경우 인증서 파일을 업로드하고 삭제할 수 있도록 위의 Windows 사용자가 기본 제공 관리자 계정이어야 하고, Azure Database Migration Service에 [사용자 계정 컨트롤](/windows/security/identity-protection/user-account-control/user-account-control-overview)을 사용하지 않도록 설정해야 합니다. |
    |**암호** | 사용자에 대한 암호입니다. |
    |**스토리지 계정 설정** | SQL Managed Instance로 데이터베이스를 마이그레이션하는 데 사용되며 백업 파일을 업로드하는 스토리지 계정 컨테이너에 대한 액세스 권한이 있는 Azure Database Migration Service를 제공하는 SAS URI입니다. [Blob 컨테이너에 대한 SAS URI를 가져오는 방법을 알아봅니다](../vs-azure-tools-storage-explorer-blobs.md#get-the-sas-for-a-blob-container). 이 SAS URI는 스토리지 계정이 아닌 Blob 컨테이너용이어야 합니다.|
    |**TDE 설정** | TDE(투명한 데이터 암호화)가 설정된 원본 데이터베이스를 마이그레이션하려면 대상 SQL Managed Instance에 대한 쓰기 권한이 필요합니다.  드롭다운 메뉴에서 SQL Managed Instance가 프로비저닝되는 구독을 선택합니다.  드롭다운 메뉴에서 대상 **Azure SQL Database Managed Instance** 를 선택합니다. |

    ![마이그레이션 설정 구성](media/tutorial-sql-server-to-managed-instance/dms-configure-migration-settings.png)

2. 완료되면 **다음: 요약** 을 선택합니다.

## <a name="review-the-migration-summary"></a>마이그레이션 요약 검토

1. **요약** 화면의 **작업 이름** 텍스트 상자에서 마이그레이션 작업의 이름을 지정합니다.

2. 마이그레이션 프로젝트와 연결된 세부 정보를 검토하고 확인합니다.

    ![마이그레이션 프로젝트 요약](media/tutorial-sql-server-to-managed-instance/dms-project-summary.png)

## <a name="run-the-migration"></a>마이그레이션 실행

- **마이그레이션 시작** 을 선택합니다.

  데이터베이스 및 로그인의 현재 마이그레이션 상태를 표시하는 마이그레이션 작업 창이 나타납니다.

## <a name="monitor-the-migration"></a>마이그레이션 모니터링

1. 마이그레이션 작업 화면에서 **새로 고침** 을 선택하여 화면을 업데이트합니다.

   ![마이그레이션 작업 화면과 새로 고침 단추를 보여 주는 스크린샷](media/tutorial-sql-server-to-managed-instance/dms-monitor-migration.png)

2. 데이터베이스 및 로그인 범주를 더 확장하여 각 서버 개체의 마이그레이션 상태를 모니터링할 수 있습니다.

   ![진행 중인 마이그레이션 작업](media/tutorial-sql-server-to-managed-instance/dms-monitor-migration-extend.png)

3. 마이그레이션이 완료된 후 SQL Managed Instance 환경에서 대상 데이터베이스를 확인합니다.

## <a name="additional-resources"></a>추가 리소스

- T-SQL RESTORE 명령을 사용하여 SQL Managed Instance로 데이터베이스를 마이그레이션하는 방법을 보여주는 자습서는 [복원 명령을 사용하여 백업을 SQL Managed Instance에 복원](../azure-sql/managed-instance/restore-sample-database-quickstart.md)을 참조하세요.
- SQL Managed Instance에 대한 자세한 내용은 [SQL Managed Instance란?](../azure-sql/managed-instance/sql-managed-instance-paas-overview.md)을 참조하세요.
- SQL Managed Instance에 앱을 연결하는 방법에 대한 자세한 내용은 [애플리케이션 연결](../azure-sql/managed-instance/connect-application-instance.md)을 참조하세요.
