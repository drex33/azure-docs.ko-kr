---
title: 투명한 데이터 암호화
titleSuffix: Azure SQL Database & SQL Managed Instance & Azure Synapse Analytics
description: Azure SQL Database, Azure SQL Managed Instance, Azure Synapse 분석에 대한 투명한 데이터 암호화의 개요입니다. 이 문서에서는 이점 및 서비스 관리 투명한 데이터 암호화 및 Bring Your Own Key를 포함한 구성 옵션을 설명합니다.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: seo-lt-2019 sqldbrb=3
ms.devlang: ''
ms.topic: conceptual
author: shohamMSFT
ms.author: shohamd
ms.reviewer: vanto
ms.date: 06/23/2021
ms.openlocfilehash: 16886e185d27a67cdea64c4214ca6132e714e9d9
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122528906"
---
# <a name="transparent-data-encryption-for-sql-database-sql-managed-instance-and-azure-synapse-analytics"></a>SQL Database, SQL Managed Instance, Azure Synapse Analytics에 대한 투명한 데이터 암호화
[!INCLUDE[appliesto-sqldb-sqlmi-asa](../includes/appliesto-sqldb-sqlmi-asa.md)]

[TDE(투명한 데이터 암호화)](/sql/relational-databases/security/encryption/transparent-data-encryption)는 미사용 데이터를 암호화하여 악의적인 오프라인 작업의 위협으로부터 Azure SQL Database, Azure SQL Managed Instance, Azure Synapse Analytics를 보호하는 데 도움이 됩니다. 애플리케이션에 대한 변경 없이 미사용 데이터베이스, 연결된 백업 및 트랜잭션 로그 파일의 실시간 암호화 및 암호 해독을 수행합니다. 기본적으로 TDE는 새로 배포된 모든 Azure SQL 데이터베이스에 대해 사용하도록 설정되며, Azure SQL Database의 이전 데이터베이스에 대해서는 수동으로 사용하도록 설정해야 합니다. Azure SQL Managed Instance의 경우 TDE는 인스턴스 수준 및 새로 만들어진 데이터베이스에서 사용하도록 설정됩니다. Azure Synapse Analytics에 대해 TDE를 수동으로 사용하도록 설정해야 합니다. 

> [!NOTE]
> 이 문서는 Azure SQL Database, Azure SQL Managed Instance 및 Azure Synapse Analytics(전용 SQL 풀(이전의 SQL DW))에 적용됩니다. Synapse 작업 영역 내의 전용 SQL 풀을 위한 투명한 데이터 암호화에 대한 문서는 [Azure Synapse Analytics 암호화](../../synapse-analytics/security/workspaces-encryption.md)를 참조하세요.

TDE를 통해 페이지 수준에서 데이터의 실시간 I/O 암호화 및 암호 해독을 수행합니다. 각 페이지는 메모리로 읽을 때 해독된 다음, 디스크에 쓰기 전에 암호화됩니다. TDE는 DEK(데이터베이스 암호화 키)라는 대칭 키를 사용하여 전체 데이터베이스의 스토리지를 암호화합니다. 데이터베이스 시작 시 암호화된 DEK는 암호 해독된 후 SQL Server 데이터베이스 엔진 프로세스의 데이터베이스 파일을 암호 해독 및 재암호화하는 데 사용됩니다. DEK는 TDE 보호기에 의해 보호됩니다. TDE 보호기는 서비스 관리 인증서(서비스 관리 투명한 데이터 암호화) 또는 [Azure Key Vault](../../key-vault/general/security-features.md) 고객 관리형 투명한 데이터 암호화에 저장된 비대칭 키(Bring Your Own Key)입니다.

Azure SQL Database와 Azure Synapse의 경우 TDE 보호기는 [서버](logical-servers.md) 수준에서 설정되며 해당 서버와 연결된 모든 데이터베이스에서 상속됩니다. Azure SQL Managed Instance의 경우 TDE 보호기는 인스턴스 수준에서 설정되며 해당 인스턴스의 모든 암호화된 데이터베이스에 의해 상속됩니다. *서버* 라는 용어는 달리 언급하지 않는 한, 이 문서 전체에서 서버와 인스턴스를 모두 나타냅니다.

> [!IMPORTANT]
> 새로 만든 모든 SQL Database의 데이터베이스는 기본적으로 서비스 관리 투명한 데이터 암호화를 사용하여 암호화됩니다. 2017년 5월 이전에 만든 기존 SQL 데이터베이스와 복원, 지리적 복제 및 데이터베이스 복사를 통해 만든 SQL 데이터베이스는 기본적으로 암호화되지 않습니다. 2019년 2월 이전에 만든 기존 SQL Managed Instance 데이터베이스는 기본적으로 암호화되지 않습니다. 복원을 통해 만든 SQL Managed Instance 데이터베이스는 원본에서 암호화 상태를 상속합니다. 기존 TDE로 암호화된 데이터베이스를 복원하려면 먼저 필요한 TDE 인증서를 SQL Managed Instance로 [가져와야 합니다](../managed-instance/tde-certificate-migrate.md). 

> [!NOTE]
> TDE는 Azure SQL Database 및 Azure SQL Managed Instance에서 **master** 데이터베이스와 같은 시스템 데이터베이스를 암호화하는 데 사용할 수 없습니다. **master** 데이터베이스에는 사용자 데이터베이스에서 TDE 작업을 수행하는 데 필요한 개체가 포함되어 있습니다. 중요한 데이터를 시스템 데이터베이스에 저장하지 않는 것이 좋습니다. [인프라 암호화](transparent-data-encryption-byok-overview.md#doubleencryption)는 이제 master를 포함하여 시스템 데이터베이스 암호화를 롤아웃합니다. 


## <a name="service-managed-transparent-data-encryption"></a>서비스 관리 투명한 데이터 암호화

Azure에서 TDE에 대한 기본 설정은 DEK가 기본 제공 서버 인증서로 보호되는 것입니다. 기본 제공 서버 인증서는 각 서버에 대해 고유하며, 사용되는 암호화 알고리즘은 AES 256입니다. 데이터베이스가 지역 복제 관계에 있는 경우 주 데이터베이스와 지역 보조 데이터베이스는 모두 주 데이터베이스의 부모 서버 키로 보호됩니다. 두 개의 데이터베이스가 동일한 서버에 연결되어 있으면 동일한 기본 제공 인증서도 공유합니다. Microsoft는 내부 보안 정책에 따라 이러한 인증서를 자동으로 회전하며, 루트 키는 Microsoft 내부 비밀 저장소를 통해 보호됩니다. 고객은 [Microsoft 보안 센터](https://servicetrust.microsoft.com/)에서 제공하는 독립 타사 감사 보고서에서 내부 보안 정책에 대한 SQL Database 및 SQL Managed Instance 준수를 확인할 수 있습니다.

또한 Microsoft는 지역 복제 및 복원에 필요한 대로 키를 원활하게 이동하고 관리합니다.

## <a name="customer-managed-transparent-data-encryption---bring-your-own-key"></a>고객 관리형 투명한 데이터 암호화 - Bring Your Own Key

고객 관리형 TDE는 TDE를 위한 BYOK(Bring Your Own Key) 지원이라고도 합니다. 이 시나리오에서 DEK를 암호화하는 TDE 보호기는 고객이 소유 및 관리하는 Azure Key Vault(Azure의 클라우드 기반 외부 키 관리 시스템)에 저장되고 키 자격 증명 모음을 벗어날 수 없는 고객 관리형 비대칭 키입니다. TDE 보호기는 [키 자격 증명 모음에 의해 생성되거나 온-프레미스 HSM(하드웨어 보안 모듈) 디바이스에서 키 자격 증명 모음으로 전송](../../key-vault/keys/hsm-protected-keys.md)될 수 있습니다. DEK를 해독하고 암호화하려면 SQL Database, SQL Managed Instance, Azure Synapse에 고객 소유 키 자격 증명 모음에 대한 권한을 부여해야 합니다. 키 자격 증명 모음에 대한 서버의 권한이 철회되면 데이터베이스에 액세스할 수 없게 되고 모든 데이터가 암호화됩니다.

TDE와 Azure Key Vault가 통합되면 사용자는 키 회전, Key Vault 권한, 키 백업을 포함한 키 관리 작업을 제어하고, Azure Key Vault 기능을 사용하여 모든 TDE 보호기에 감사/보고를 사용하도록 설정할 수 있습니다. Key Vault는 중앙 집중식 키 관리를 제공하고, 철저하게 모니터링되는 HSM을 활용하고, 보안 정책 규정을 준수하도록 키 관리와 데이터 관리의 책임을 분리합니다.
Azure SQL Database 및 Azure Synapse의 BYOK에 대한 자세한 내용은 [Azure Key Vault 통합을 통한 투명한 데이터 암호화](transparent-data-encryption-byok-overview.md)를 참조하세요.

Azure Key Vault 통합을 사용하는 TDE 지원 사용을 시작하려면 [Key Vault에서 사용자 고유 키를 사용하여 투명한 데이터 암호화 설정](transparent-data-encryption-byok-configure.md) 방법 가이드를 확인하세요.

## <a name="move-a-transparent-data-encryption-protected-database"></a>투명한 데이터 암호화로 보호된 데이터베이스 이동

Azure 내에서 작업용 데이터베이스를 해독할 필요가 없습니다. 원본 데이터베이스 또는 주 데이터베이스의 TDE 설정은 대상에서 투명하게 상속됩니다. 포함되는 작업은 다음과 같습니다.

- 지역 복원
- 셀프 서비스 특정 시점 복원
- 삭제된 데이터베이스 복원
- 활성 지리적 복제
- 데이터베이스 복사본 생성
- Azure SQL Managed Instance에 백업 파일 복원

> [!IMPORTANT]
> 암호화에 사용되는 인증서에 액세스할 수 없으므로 Azure SQL Managed Instance에서는 서비스 관리 TDE로 암호화된 데이터베이스에 대한 COPY-ONLY(복사 전용) 수동 백업은 지원되지 않습니다. 특정 시점 복원 기능을 사용하여 이러한 유형의 데이터베이스를 다른 SQL Managed Instance로 이동하거나 고객 관리형 키로 전환합니다.

TDE로 보호된 데이터베이스를 내보낼 때 데이터베이스의 내보낸 콘텐츠는 암호화되지 않습니다. 이 내보낸 콘텐츠는 암호화되지 않은 BACPAC 파일에 저장됩니다. 새 데이터베이스 가져오기가 완료되면 BACPAC 파일을 적절하게 보호하고 TDE를 사용하도록 설정해야 합니다.

예를 들어 SQL Server 인스턴스에서 BACPAC 파일을 내보낸 경우 가져온 새 데이터베이스의 콘텐츠가 자동으로 암호화되지 않습니다. 마찬가지로 BACPAC 파일을 SQL Server 인스턴스로 가져온 경우 새 데이터베이스도 자동으로 암호화되지 않습니다.

데이터베이스를 내보내는 한 가지 예외는 SQL Database 간에 내보내는 경우입니다. 새 데이터베이스에서 TDE를 사용하도록 설정되지만 BACPAC 파일 자체는 여전히 암호화되지 않습니다.

## <a name="manage-transparent-data-encryption"></a>투명한 데이터 암호화 관리

# <a name="the-azure-portal"></a>[Azure Portal](#tab/azure-portal)

Azure Portal에서 TDE를 관리합니다.

Azure Portal을 통해 TDE를 구성하려면 Azure 소유자, 참가자 또는 SQL 보안 관리자로 연결되어 있어야 합니다.

데이터베이스 수준에서 TDE를 사용하거나 사용하지 않도록 설정합니다. Azure SQL Managed Instance의 경우 T-SQL(Transact-SQL)을 사용하여 데이터베이스에서 TDE를 설정 및 해제합니다. Azure SQL Database 및 Azure Synapse의 경우 Azure 관리자 또는 참가자 계정으로 로그인한 후 [Azure Portal](https://portal.azure.com)에서 데이터베이스에 대한 TDE를 관리할 수 ​​있습니다. 사용자 데이터베이스에서 TDE 설정을 찾습니다. 기본적으로 서비스 관리 투명한 데이터 암호화를 사용하도록 설정되어 있습니다. TDE 인증서는 데이터베이스가 포함된 서버에 대해 자동으로 생성됩니다.

![서비스 관리 투명한 데이터 암호화](./media/transparent-data-encryption-tde-overview/service-managed-transparent-data-encryption.png)  

서버 또는 인스턴스 수준에서 TDE 보호기라는 TDE 마스터 키를 설정합니다. BYOK 기반 TDE 지원을 사용하고 Key Vault의 키를 사용하여 데이터베이스를 보호하려면 서버의 TDE 설정을 엽니다.

![Bring Your Own Key 지원을 통한 투명한 데이터 암호화](./media/transparent-data-encryption-tde-overview/tde-byok-support.png)

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

PowerShell을 사용하여 TDE를 관리합니다.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]
> [!IMPORTANT]
> PowerShell Azure Resource Manager 모듈은 여전히 지원되지만 향후 모든 개발은 Az.Sql 모듈에 집중됩니다. 이러한 cmdlet은 [AzureRM.Sql](/powershell/module/AzureRM.Sql/)을 참조하세요. Az 모듈 및 AzureRm 모듈의 명령에 대한 인수는 실질적으로 동일합니다.

PowerShell을 통해 TDE를 구성하려면 Azure 소유자, 참가자 또는 SQL 보안 관리자로 연결되어 있어야 합니다.

### <a name="cmdlets-for-azure-sql-database-and-azure-synapse"></a>Azure SQL Database 및 Azure Synapse용 cmdlet

Azure SQL Database 및 Azure Synapse에 대해 다음 cmdlet을 사용합니다.

| Cmdlet | Description |
| --- | --- |
| [Set-AzSqlDatabaseTransparentDataEncryption](/powershell/module/az.sql/set-azsqldatabasetransparentdataencryption) |데이터베이스에 대한 투명한 데이터 암호화를 사용하거나 사용하지 않도록 설정합니다.|
| [Get-AzSqlDatabaseTransparentDataEncryption](/powershell/module/az.sql/get-azsqldatabasetransparentdataencryption) |데이터베이스에 대한 투명한 데이터 암호화 상태를 가져옵니다. |
| [Get-AzSqlDatabaseTransparentDataEncryptionActivity](/powershell/module/az.sql/get-azsqldatabasetransparentdataencryptionactivity) |데이터베이스에 대한 암호화 진행률을 확인합니다. |
| [Add-AzSqlServerKeyVaultKey](/powershell/module/az.sql/add-azsqlserverkeyvaultkey) |서버에 Key Vault 키를 추가합니다. |
| [Get-AzSqlServerKeyVaultKey](/powershell/module/az.sql/get-azsqlserverkeyvaultkey) |서버에 대한 Key Vault 키를 가져옵니다.  |
| [Set-AzSqlServerTransparentDataEncryptionProtector](/powershell/module/az.sql/set-azsqlservertransparentdataencryptionprotector) |서버에 대한 투명한 데이터 암호화 보호기를 설정합니다. |
| [Get-AzSqlServerTransparentDataEncryptionProtector](/powershell/module/az.sql/get-azsqlservertransparentdataencryptionprotector) |투명한 데이터 암호화 보호기를 가져옵니다. |
| [Remove-AzSqlServerKeyVaultKey](/powershell/module/az.sql/remove-azsqlserverkeyvaultkey) |서버에서 Key Vault 키를 제거합니다. |
|  | |

> [!IMPORTANT]
> Azure SQL Managed Instance의 경우 T-SQL [ALTER DATABASE](/sql/t-sql/statements/alter-database-azure-sql-database) 명령을 사용하여 데이터베이스 수준에서 TDE를 설정 및 해제하고 [샘플 PowerShell 스크립트](transparent-data-encryption-byok-configure.md)를 확인하여 인스턴스 수준에서 TDE를 관리합니다.

# <a name="transact-sql"></a>[Transact-SQL](#tab/azure-TransactSQL)

Transact-SQL을 사용하여 TDE를 관리합니다.

master 데이터베이스에서 **dbmanager** 역할의 관리자 또는 구성원으로 로그인하여 데이터베이스에 연결합니다.

| 명령 | Description |
| --- | --- |
| [ALTER DATABASE(Azure SQL Database)](/sql/t-sql/statements/alter-database-azure-sql-database) | SET ENCRYPTION ON/OFF는 데이터베이스를 암호화하거나 해독합니다. |
| [sys.dm_database_encryption_keys](/sql/relational-databases/system-dynamic-management-views/sys-dm-database-encryption-keys-transact-sql) |데이터베이스 및 연결된 해당 데이터베이스 암호화 키의 암호화 상태에 대한 정보를 반환합니다. |
| [sys.dm_pdw_nodes_database_encryption_keys](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-nodes-database-encryption-keys-transact-sql) |각 Azure Synapse 노드 및 연결된 해당 데이터베이스 암호화 키의 암호화 상태에 대한 정보를 반환합니다. |
|  | |

TDE 보호기는 Transact-SQL을 사용하여 Key Vault의 키로 전환할 수 없습니다. 대신 PowerShell 또는 Azure Portal을 사용합니다.

# <a name="rest-api"></a>[REST API](#tab/azure-RESTAPI)

REST API를 사용하여 TDE를 관리합니다.

REST API를 통해 TDE를 구성하려면 Azure 소유자, 참가자 또는 SQL 보안 관리자로 연결되어 있어야 합니다.
Azure SQL Data Warehouse 및 Azure Synapse에 대해 다음 명령 세트를 사용합니다.

| 명령 | Description |
| --- | --- |
|[서버 만들기 또는 업데이트](/rest/api/sql/servers/createorupdate)|서버에 Azure Active Directory ID를 추가합니다. (Key Vault에 대한 액세스 권한을 부여하는 데 사용)|
|[서버 키 만들기 또는 업데이트](/rest/api/sql/serverkeys/createorupdate)|서버에 Key Vault 키를 추가합니다.|
|[서버 키 삭제](/rest/api/sql/serverkeys/delete)|서버에서 Key Vault 키를 제거합니다. |
|[서버 키 가져오기](/rest/api/sql/serverkeys/get)|서버에서 특정 Key Vault 키를 가져옵니다.|
|[서버별 서버 키 나열](/rest/api/sql/serverkeys/listbyserver)|SQL Server 인스턴스에 대한 Key Vault 키를 가져옵니다. |
|[암호화 보호기 만들기 또는 업데이트](/rest/api/sql/encryptionprotectors/createorupdate)|서버에 대한 TDE 보호기를 설정합니다.|
|[암호화 보호기 가져오기](/rest/api/sql/encryptionprotectors/get)|서버에 대한 TDE 보호기를 가져옵니다.|
|[서버별 암호화 보호기 나열](/rest/api/sql/encryptionprotectors/listbyserver)|서버에 대한 TDE 보호기를 가져옵니다. |
|[투명한 데이터 암호화 구성 만들기 또는 업데이트](/rest/api/sql/transparentdataencryptions/createorupdate)|데이터베이스에 대해 TDE를 사용하거나 사용하지 않도록 설정합니다.|
|[투명한 데이터 암호화 구성 가져오기](/rest/api/sql/transparentdataencryptions/get)|데이터베이스에 대한 TDE 구성을 가져옵니다.|
|[투명한 데이터 암호화 구성 결과 나열](/rest/api/sql/transparentdataencryptionactivities/listbyconfiguration)|데이터베이스에 대한 암호화 결과를 가져옵니다.|

## <a name="see-also"></a>참고 항목

- Azure 가상 머신에서 실행되는 SQL Server는 Key Vault의 비대칭 키를 사용할 수도 있습니다. 구성 단계는 SQL Database 및 SQL Managed Instance에서 비대칭 키를 사용하는 것과 다릅니다. 자세한 내용은 [Azure Key Vault(SQL Server)를 사용한 확장 가능 키 관리](/sql/relational-databases/security/encryption/extensible-key-management-using-azure-key-vault-sql-server)를 참조하세요.
- TDE에 대한 일반적인 설명은 [투명한 데이터 암호화](/sql/relational-databases/security/encryption/transparent-data-encryption)를 참조하세요.
- Azure SQL Database, Azure SQL Managed Instance, Azure Synapse에 대한 BYOK 기반 TDE 지원에 대해 자세히 알아보려면 [Bring Your Own Key 지원으로 투명한 데이터 암호화](transparent-data-encryption-byok-overview.md)를 참조하세요.
- Bring Your Own Key 지원을 사용하는 TDE 지원 사용을 시작하려면 [Key Vault에서 사용자 고유 키를 사용하여 투명한 데이터 암호화 설정](transparent-data-encryption-byok-configure.md) 방법 가이드를 확인하세요.
- Key Vault에 대한 자세한 내용은 [키 자격 증명 모음에 대한 보안 액세스](../../key-vault/general/security-features.md)를 참조하세요.
