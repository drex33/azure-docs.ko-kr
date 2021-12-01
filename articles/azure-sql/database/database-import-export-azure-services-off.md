---
title: Azure 서비스가 서버에 액세스할 수 있도록 허용하지 않고 Azure SQL Database를 가져오거나 내보냅니다.
description: Azure 서비스가 서버에 액세스할 수 있도록 허용하지 않고 Azure SQL Database를 가져오거나 내보냅니다.
services: sql-database
ms.service: sql-database
ms.subservice: migration
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: how-to
author: LitKnd
ms.author: kendralittle
ms.reviewer: mathoma
ms.date: 01/08/2020
ms.openlocfilehash: 1b3527fe7b1d8943f4484c9bf70bfc652aad4828
ms.sourcegitcommit: 66b6e640e2a294a7fbbdb3309b4829df526d863d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/01/2021
ms.locfileid: "133355359"
---
# <a name="import-or-export-an-azure-sql-database-without-allowing-azure-services-to-access-the-server"></a>Azure 서비스가 서버에 액세스할 수 있도록 허용하지 않고 Azure SQL Database 가져오기 또는 내보내기
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

이 문서에서는 서버에서 ‘Azure 서비스 허용’이 ‘끄기’로 설정된 경우 Azure SQL Database를 가져오거나 내보내는 방법을 보여 줍니다.  워크플로는 Azure 가상 머신을 사용하여 SqlPackage를 실행함으로써 가져오기 또는 내보내기 작업을 수행합니다.

## <a name="sign-in-to-the-azure-portal"></a>Azure Portal에 로그인

[Azure Portal](https://portal.azure.com/)에 로그인합니다.

## <a name="create-the-azure-virtual-machine"></a>Azure 가상 머신 만들기

**Azure에 배포** 단추를 선택하여 Azure 가상 머신을 만듭니다.

이 템플릿을 사용하면 최신 패치가 적용된 버전을 사용하여 Windows 버전에 몇 가지 다른 옵션을 사용해 간단한 Windows 가상 머신을 배포할 수 있습니다. 그러면 리소스 그룹 위치에 A2 크기의 VM이 배포되고 VM의 정규화된 도메인 이름이 반환됩니다.
<br><br>

[!["Azure에 배포"라는 레이블이 지정된 단추를 보여주는 이미지](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fquickstarts%2Fmicrosoft.compute%2Fvm-simple-windows%2Fazuredeploy.json)

자세한 내용은 [매우 간단한 Windows VM 배포](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.compute/vm-simple-windows)를 참조하세요.

## <a name="connect-to-the-virtual-machine"></a>가상 머신에 연결

다음 단계에서는 원격 데스크톱 연결을 사용하여 가상 머신에 연결하는 방법을 보여 줍니다.

1. 배포가 완료되면 가상 머신 리소스로 이동합니다.

   ![스크린샷은 연결 단추가 있는 가상 머신 개요 페이지를 보여 줍니다.](./media/database-import-export-azure-services-off/vm.png)

2. **연결** 을 선택합니다.

   가상 머신의 공용 IP 주소 및 포트 번호를 사용하여 원격 데스크톱 프로토콜 파일(.rdp 파일) 양식이 나타납니다.

   ![RDP 양식](./media/database-import-export-azure-services-off/rdp.png)

3. **RDP 파일 다운로드** 를 선택합니다.

   > [!NOTE]
   > SSH를 사용하여 VM에 연결할 수도 있습니다.

4. **가상 머신에 연결** 양식을 닫습니다.
5. VM에 연결하려면 다운로드한 RDP 파일을 엽니다.
6. 메시지가 표시되면 **연결** 을 선택합니다. Mac의 Mac 앱 스토어에서 이 [원격 데스크톱 클라이언트](https://apps.apple.com/app/microsoft-remote-desktop-10/id1295203466?mt=12)와 같은 RDP 클라이언트가 필요합니다.

7. 가상 머신을 만들 때 지정한 사용자 이름 및 암호를 입력하고 **확인** 을 선택합니다.

8. 로그인 프로세스 중에 인증서 경고가 나타날 수 있습니다. **예** 또는 **계속** 을 선택하여 연결을 진행합니다.

## <a name="install-sqlpackage"></a>SqlPackage 설치

[최신 버전의 SqlPackage를 다운로드 및 설치](/sql/tools/sqlpackage-download)합니다.

자세한 내용은 [SqlPackage.exe](/sql/tools/sqlpackage)를 참조하세요.

## <a name="create-a-firewall-rule-to-allow-the-vm-access-to-the-database"></a>데이터베이스에 대한 VM 액세스를 허용하는 방화벽 규칙 만들기

서버의 방화벽에 가상 머신의 공용 IP 주소를 추가합니다.

다음 단계에서는 가상 머신의 공용 IP 주소에 대한 서버 수준 IP 방화벽 규칙을 만들고 가상 머신에서 연결을 사용하도록 설정합니다.

1. 왼쪽 메뉴에서 **SQL 데이터베이스** 를 선택한 다음 **SQL 데이터베이스** 페이지에서 데이터베이스를 선택합니다. 데이터베이스의 개요 페이지가 열리고 정규화된 서버 이름(예: **servername.database.windows.net**)이 표시되고 추가 구성을 위한 옵션이 제공됩니다.

2. 서버 및 해당 데이터베이스에 연결하는 데 사용하기 위해 이 정규화된 서버 이름을 복사합니다.

   ![서버 이름](./media/database-import-export-azure-services-off/server-name.png)

3. 도구 모음에서 **서버 방화벽 설정** 을 선택합니다. 서버에 대한 **방화벽 설정** 페이지가 열립니다.

   ![서버 수준 IP 방화벽 규칙](./media/database-import-export-azure-services-off/server-firewall-rule.png)

4. 도구 모음에서 **클라이언트 IP 추가** 를 선택하여 가상 머신의 공용 IP 주소를 새 서버 수준 IP 방화벽 규칙에 추가합니다. 서버 수준 IP 방화벽 규칙은 단일 IP 주소 또는 IP 주소의 범위에 1433 포트를 열 수 있습니다.

5. **저장** 을 선택합니다. 서버의 1433 포트를 여는 가상 머신의 공용 IP 주소에 서버 수준 IP 방화벽 규칙이 생성됩니다.

6. **방화벽 설정** 페이지를 닫습니다.

## <a name="export-a-database-using-sqlpackage"></a>SqlPackage를 사용하여 데이터베이스 내보내기

[SqlPackage](/sql/tools/sqlpackage) 명령줄 유틸리티를 사용하여 Azure SQL Database를 내보내려면 [매개 변수 및 속성 내보내기](/sql/tools/sqlpackage#export-parameters-and-properties)를 참조하세요. SqlPackage 유틸리티는 최신 버전의 [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) 및 [SQL Server Data Tools](/sql/ssdt/download-sql-server-data-tools-ssdt)가 함께 제공되며, 최신 버전의 [SqlPackage](/sql/tools/sqlpackage-download)를 다운로드할 수 있습니다.

대부분의 프로덕션 환경에서 규모 및 성능에 SqlPackage 유틸리티를 사용하는 것이 좋습니다. BACPAC 파일을 사용한 마이그레이션에 관한 SQL Server 고객 자문 팀 블로그는 [BACPAC 파일을 사용하여 SQL Server에서 Azure SQL Database로 마이그레이션](/archive/blogs/sqlcat/migrating-from-sql-server-to-azure-sql-database-using-bacpac-files)을 참조하세요.

이 예제는 Active Directory 유니버설 인증으로 SqlPackage.exe를 사용하여 데이터베이스를 내보내는 방법을 보여 줍니다. 환경에 필요한 값으로 바꿉니다.

```cmd
SqlPackage.exe /a:Export /tf:testExport.bacpac /scs:"Data Source=<servername>.database.windows.net;Initial Catalog=MyDB;" /ua:True /tid:"apptest.onmicrosoft.com"
```

## <a name="import-a-database-using-sqlpackage"></a>SqlPackage를 사용하여 데이터베이스 가져오기

[SqlPackage](/sql/tools/sqlpackage) 명령줄 유틸리티를 사용하여 SQL Server 데이터베이스를 가져오려면 [매개 변수 및 속성 가져오기](/sql/tools/sqlpackage#import-parameters-and-properties)를 참조하세요. SqlPackage에는 최신 [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) 및 [SQL Server Data Tools](/sql/ssdt/download-sql-server-data-tools-ssdt)가 있습니다. 최신 버전의 [SqlPackage](/sql/tools/sqlpackage-download)를 다운로드할 수도 있습니다.

규모 및 성능과 관련하여 대부분의 프로덕션 환경에서는 Azure Portal보다 SqlPackage를 사용하는 것이 좋습니다. `BACPAC` 파일을 사용하는 마이그레이션에 대한 SQL Server 고객 자문 팀 블로그는 [BACPAC 파일을 사용하여 SQL Server에서 Azure SQL Database로 마이그레이션](/archive/blogs/sqlcat/migrating-from-sql-server-to-azure-sql-database-using-bacpac-files)을 참조하세요.

다음 SqlPackage 명령은 로컬 스토리지에서 Azure SQL Database로 **AdventureWorks2017** 데이터베이스를 가져옵니다. **프리미엄** 서비스 계층과 **P6** 서비스 목표로 **myMigratedDatabase** 라는 새 데이터베이스를 만듭니다. 이러한 값을 사용자 환경에 적절하게 변경합니다.

```cmd
sqlpackage.exe /a:import /tcs:"Data Source=<serverName>.database.windows.net;Initial Catalog=myMigratedDatabase>;User Id=<userId>;Password=<password>" /sf:AdventureWorks2017.bacpac /p:DatabaseEdition=Premium /p:DatabaseServiceObjective=P6
```

> [!IMPORTANT]
> 회사 방화벽 뒤에서 Azure SQL Database에 연결하려면 방화벽에 1433 포트가 열려 있어야 합니다.

이 예제는 Active Directory 유니버설 인증으로 SqlPackage를 사용하여 데이터베이스를 가져오는 방법을 보여줍니다.

```cmd
sqlpackage.exe /a:Import /sf:testExport.bacpac /tdn:NewDacFX /tsn:apptestserver.database.windows.net /ua:True /tid:"apptest.onmicrosoft.com"
```

## <a name="performance-considerations"></a>성능 고려 사항

내보내기 속도는 다양한 요인(예: 데이터 모양)으로 인해 달라질 수 있으므로 속도를 예측할 수 없습니다. SqlPackage는 특히 큰 데이터베이스의 경우 상당한 시간이 걸릴 수 있습니다.

최상의 성능을 얻기 위해 다음 전략을 시도해 볼 수 있습니다.

1. 데이터베이스에서 다른 워크로드가 실행되고 있지 않은지 확인합니다. 내보내기 전에 복사본을 만드는 것이 다른 워크로드의 실행을 방지하는 가장 좋은 해결 방법일 수 있습니다.
2. 데이터베이스 SLO(서비스 수준 목표)를 늘려 내보내기 워크로드(주로 I/O 읽기)를 효율적으로 처리합니다. 데이터베이스가 현재 GP_Gen5_4인 경우 중요 비즈니스용 계층이 읽기 워크로드에 도움이 될 수 있습니다.
3. 특히 큰 테이블의 경우 클러스터형 인덱스가 있는지 확인합니다.
4. 네트워크 제약을 피하기 위해 VM(가상 머신)은 데이터베이스와 동일한 지역에 있어야 합니다.
5. VM에는 Blob 스토리지에 업로드하기 전에 임시 아티팩트를 생성하기에 충분한 크기의 SSD가 있어야 합니다.
6. VM에는 특정 데이터베이스에 대한 적절한 코어 및 메모리 구성이 있어야 합니다.

## <a name="store-the-imported-or-exported-bacpac-file"></a>가져오거나 내보낸 .BACPAC 파일 저장

.BACPAC 파일은 [Azure Blob](../../storage/blobs/storage-blobs-overview.md) 또는 [Azure Files](../../storage/files/storage-files-introduction.md)에 저장할 수 있습니다.

최상의 성능을 얻으려면 Azure Files를 사용합니다. SqlPackage는 Azure Files에 직접 액세스할 수 있도록 파일 시스템을 사용하여 작동합니다.

비용을 절감하려면 프리미엄 Azure 파일 공유보다 비용이 저렴한 Azure Blob을 사용합니다. 단, 가져오기 또는 내보내기 작업을 수행하기 전에 Blob 및 로컬 파일 시스템 간에 [.BACPAC 파일](/sql/relational-databases/data-tier-applications/data-tier-applications#bacpac)을 복사해야 하므로 시간이 더 오래 걸립니다.

.BACPAC 파일을 업로드하거나 다운로드하려면 [AzCopy 및 Blob 스토리지를 사용하여 데이터 전송](../../storage/common/storage-use-azcopy-v10.md#transfer-data) 및 [AzCopy 및 파일 스토리지를 사용하여 데이터 전송](../../storage/common/storage-use-azcopy-files.md)을 참조하세요.

환경에 따라 [Azure Storage 방화벽 및 가상 네트워크를 구성](../../storage/common/storage-network-security.md)해야 할 수도 있습니다.

## <a name="next-steps"></a>다음 단계

- 가져온 SQL Database에 연결하고 쿼리하는 방법을 알아보려면 [빠른 시작: Azure SQL Database: SQL Server Management Studio를 사용하여 데이터에 연결 및 쿼리](connect-query-ssms.md)를 참조하세요.
- BACPAC 파일을 사용한 마이그레이션에 관한 SQL Server 고객 자문 팀 블로그는 [BACPAC 파일을 사용하여 SQL Server에서 Azure SQL Database로 마이그레이션](https://techcommunity.microsoft.com/t5/DataCAT/Migrating-from-SQL-Server-to-Azure-SQL-Database-using-Bacpac/ba-p/305407)을 참조하세요.
- 성능 권장 사항을 비롯한 전체 SQL Server 데이터베이스 마이그레이션 프로세스에 대한 설명은 [Azure SQL Database로 SQL Server 데이터베이스 마이그레이션](migrate-to-database-from-sql-server.md)을 참조하세요.
- 스토리지 키 및 공유 액세스 서명을 안전하게 관리하고 공유하는 방법을 알아보려면 [Azure Storage 보안 가이드](../../storage/blobs/security-recommendations.md)를 참조하세요.
