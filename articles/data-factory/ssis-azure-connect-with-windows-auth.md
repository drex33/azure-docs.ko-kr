---
title: Windows 인증으로 데이터 저장소 및 파일 공유 액세스
description: Windows 인증을 사용하여 데이터 저장소과 파일 공유에 액세스하는 패키지를 실행하도록 Azure SQL Database와 Azure Data Factory의 Azure-SSIS Integration Runtime에 SSIS 카탈로그를 구성하는 방법을 알아봅니다.
ms.date: 10/22/2021
ms.topic: conceptual
ms.service: data-factory
ms.subservice: integration-services
author: swinarko
ms.author: sawinark
ms.reviewer: jburchel
ms.openlocfilehash: 5d1ced9c1c867a45a56b684515db53aaf9a73848
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/05/2021
ms.locfileid: "131850359"
---
# <a name="access-data-stores-and-file-shares-with-windows-authentication-from-ssis-packages-in-azure"></a>Azure의 SSIS 패키지에서 Windows 인증으로 데이터 저장소 및 파일 공유 액세스

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Windows 인증을 사용하여 Azure Data Factory(ADF)의 Azure-SSIS Integration Runtime(IR)에서 실행되는 SSIS 패키지에서 SQL Server, 파일 공유, Azure Files 등과 같은 데이터 저장소에 액세스할 수 있습니다. 데이터 저장소는 온-프레미스 상태이거나, Azure VM(가상 머신)에서 호스트되거나 Azure에서 관리형 서비스로서 실행 중일 수 있습니다. 온-프레미스일 경우 온-프레미스 네트워크에 연결된 가상 네트워크(Microsoft Azure Virtual Network)에 Azure-SSIS IR을 조인해야 합니다. [Azure-SSIS IR을 Microsoft Azure Virtual Network에 조인](./join-azure-ssis-integration-runtime-virtual-network.md)을 참조하세요. Azure SSIS IR을 실행하는 SSIS 패키지에서 Windows 인증을 사용하여 데이터 저장소에 액세스하는 방법에는 네 가지가 있습니다.

| 연결 방법 | 유효 범위 | 설정 단계 | 패키지의 액세스 방법 | 자격 증명 집합 및 연결된 리소스 수 | 연결된 리소스 유형 | 
|---|---|---|---|---|---|
| 작업 수준 실행 컨텍스트 설정 | SSIS 패키지 실행 작업별 | ADF 파이프라인에서 SSIS 패키지 작업 실행으로 SSIS 패키지를 실행할 때 "실행 방법/다음 계정으로 실행" 컨텍스트를 설정하도록 **Windows 인증** 속성을 구성합니다.<br/><br/> 자세한 내용은 [SSIS 패키지 실행 작업 구성](./how-to-invoke-ssis-package-ssis-activity.md)을 참조하세요. | 패키지에서 직접 리소스에 액세스(예를 들어 UNC 경로를 통해 파일 공유 또는 Azure Files에 액세스하는 경우 `\\YourFileShareServerName\YourFolderName` 또는 `\\YourAzureStorageAccountName.file.core.windows.net\YourFolderName`) | 모든 연결된 리소스에 하나의 자격 증명 집합만 지원 | - 온-프레미스/Azure VM의 파일 공유<br/><br/> - Azure Files, [Azure 파일 공유 사용](../storage/files/storage-how-to-use-files-windows.md) 참조<br/><br/> - Windows 인증을 사용하는 온프레미스SQL Server/Azure VM<br/><br/> - Windows 인증을 사용하는 기타 리소스 |
| 카탈로그 수준 실행 컨텍스트 설정 | Azure-SSIS IR당. 하지만 작업 수준 실행 컨텍스트를 설정할 때 재정의됨(위 참조) | SSISDB `catalog.set_execution_credential` 저장 프로시저를 실행하여 “실행 방법/다음 계정으로 실행” 컨텍스트를 설정합니다.<br/><br/> 자세한 내용은 아래에서 이 문서의 나머지 부분을 참조하세요. | 패키지에서 직접 리소스에 액세스(예를 들어 UNC 경로를 통해 파일 공유 또는 Azure Files에 액세스하는 경우 `\\YourFileShareServerName\YourFolderName` 또는 `\\YourAzureStorageAccountName.file.core.windows.net\YourFolderName`) | 모든 연결된 리소스에 하나의 자격 증명 집합만 지원 | - 온-프레미스/Azure VM의 파일 공유<br/><br/> - Azure Files, [Azure 파일 공유 사용](../storage/files/storage-how-to-use-files-windows.md) 참조<br/><br/> - Windows 인증을 사용하는 온프레미스SQL Server/Azure VM<br/><br/> - Windows 인증을 사용하는 기타 리소스 |
| `cmdkey` 명령을 통해 자격 증명 유지 | Azure-SSIS IR당. 하지만 작업/카탈로그 수준 실행 컨텍스트를 설정할 때 재정의됨(위 참조) | Azure-SSIS IR을 프로비전할 때 사용자 지정 설정 스크립트(`main.cmd`)에서 `cmdkey` 명령을 실행합니다(예를 들어 파일 공유, Azure Files, SQL Server를 사용하는 경우).<br/><br/> `cmdkey /add:YourFileShareServerName /user:YourDomainName\YourUsername /pass:YourPassword`,<br/><br/> `cmdkey /add:YourAzureStorageAccountName.file.core.windows.net /user:azure\YourAzureStorageAccountName /pass:YourAccessKey` 또는<br/><br/> `cmdkey /add:YourSQLServerFullyQualifiedDomainNameOrIPAddress:YorSQLServerPort /user:YourDomainName\YourUsername /pass:YourPassword`.<br/><br/> 자세한 내용은 [Azure-SSIS IR 설정 사용자 지정](./how-to-configure-azure-ssis-ir-custom-setup.md)을 참조하세요. | 패키지에서 직접 리소스에 액세스(예를 들어 UNC 경로를 통해 파일 공유 또는 Azure Files에 액세스하는 경우 `\\YourFileShareServerName\YourFolderName` 또는 `\\YourAzureStorageAccountName.file.core.windows.net\YourFolderName`) | 다양한 연결된 리소스에 여러 자격 증명 집합 지원 | - 온-프레미스/Azure VM의 파일 공유<br/><br/> - Azure Files, [Azure 파일 공유 사용](../storage/files/storage-how-to-use-files-windows.md) 참조<br/><br/> - Windows 인증을 사용하는 온프레미스SQL Server/Azure VM<br/><br/> - Windows 인증을 사용하는 기타 리소스 |
| 패키지 실행 시간에 드라이브 탑재(비지속적) | 패키지별 | 패키지의 제어 흐름 시작 부분에 추가되는 프로세스 실행 태스크로 `net use` 명령을 실행합니다(예: `net use D: \\YourFileShareServerName\YourFolderName`). | 매핑된 드라이브를 통해 파일 공유에 액세스 | 다양한 파일 공유에 여러 드라이브 지원 | - 온-프레미스/Azure VM의 파일 공유<br/><br/> - Azure Files, [Azure 파일 공유 사용](../storage/files/storage-how-to-use-files-windows.md) 참조 |
|||||||

> [!WARNING]
> Windows 인증으로 데이터 저장소에 액세스하는 데 위의 방법을 사용하지 않으면 Windows 인증을 사용하는 패키지는 해당 저장소에 액세스할 수 없고 런타임에서 실패합니다. 

이 문서의 나머지 부분에서는 Windows 인증을 사용하여 데이터 저장소에 액세스하는 패키지를 Azure-SSIS IR에서 실행하도록 SQL Database/SQL Managed Instance에서 호스트되는 SSIS 카탈로그(SSISDB)를 구성하는 방법을 설명합니다. 

## <a name="you-can-only-use-one-set-of-credentials"></a>자격 증명 집합을 하나만 사용 가능

SSIS 패키지에서 Windows 인증을 사용하는 경우 한 집합의 자격 증명만 사용할 수 있습니다. 이 문서의 단계를 수행할 때 제공하는 도메인 자격 증명은 변경되거나 제거되기 전까지 Azure-SSIS IR에서 모든 패키지 실행(대화형 또는 예약된 실행)에 적용됩니다. 여러 자격 증명 집합을 사용하여 패키지를 여러 데이터 저장소에 연결해야 하는 경우 위의 대체 방법을 고려해야 합니다.

## <a name="provide-domain-credentials-for-windows-authentication"></a>Windows 인증에 대한 도메인 자격 증명 제공

패키지에서 Windows 인증을 사용하여 온-프레미스 데이터 저장소에 액세스할 수 있는 도메인 자격 증명을 제공하려면 다음을 수행합니다.

1. SSMS(SQL Server Management Studio) 또는 다른 도구를 사용하여 SSISDB를 호스트하는 SQL Database/SQL Managed Instance에 연결합니다. 자세한 내용은 [Azure에서 SSISDB에 연결](/sql/integration-services/lift-shift/ssis-azure-connect-to-catalog-database)을 참조하세요.

2. SSISDB를 현재 데이터베이스로 사용하여 쿼리 창을 엽니다.

3. 다음 저장 프로시저를 실행하고 적절한 도메인 자격 증명을 제공합니다.

   ```sql
   catalog.set_execution_credential @user='<your user name>', @domain='<your domain name>', @password='<your password>'
   ```

4. SSIS 패키지를 실행합니다. 패키지는 Windows 인증을 사용하여 온-프레미스 데이터 저장소에 액세스하기 위해 제공한 자격 증명을 사용합니다.

### <a name="view-domain-credentials"></a>도메인 자격 증명 보기

활성 도메인 자격 증명을 보려면 다음을 수행합니다.

1. SSMS나 다른 도구를 사용하여 SSISDB를 호스트하는 SQL Database/SQL Managed Instance에 연결합니다. 자세한 내용은 [Azure에서 SSISDB에 연결](/sql/integration-services/lift-shift/ssis-azure-connect-to-catalog-database)을 참조하세요.

2. SSISDB를 현재 데이터베이스로 사용하여 쿼리 창을 엽니다.

3. 다음 저장 프로시저를 실행하고 출력을 확인합니다.

   ```sql
   SELECT * 
   FROM catalog.master_properties
   WHERE property_name = 'EXECUTION_DOMAIN' OR property_name = 'EXECUTION_USER'
   ```

### <a name="clear-domain-credentials"></a>도메인 자격 증명 지우기
이 문서의 설명에 따라 제공한 자격 증명을 지우고 제거하려면 다음을 수행합니다.

1. SSMS나 다른 도구를 사용하여 SSISDB를 호스트하는 SQL Database/SQL Managed Instance에 연결합니다. 자세한 내용은 [Azure에서 SSISDB에 연결](/sql/integration-services/lift-shift/ssis-azure-connect-to-catalog-database)을 참조하세요.

2. SSISDB를 현재 데이터베이스로 사용하여 쿼리 창을 엽니다.

3. 다음 저장 프로시저를 실행합니다.

   ```sql
   catalog.set_execution_credential @user='', @domain='', @password=''
   ```

## <a name="connect-to-a-sql-server-on-premises"></a>온-프레미스 SQL Server에 연결

온-프레미스 SQL Server에 연결할 수 있는지 확인하려면 다음을 수행합니다.

1. 이 테스트를 실행하려면 도메인에 가입되지 않은 컴퓨터를 찾습니다.

2. 도메인에 가입하지 않은 컴퓨터에서 다음 명령을 실행하여, 사용할 도메인 자격 증명으로 SSMS를 시작합니다.

   ```cmd
   runas.exe /netonly /user:<domain>\<username> SSMS.exe
   ```

3. SSMS에서 온-프레미스 SQL Server에 연결할 수 있는지 확인합니다.

### <a name="prerequisites"></a>필수 구성 요소

Azure에서 실행 중인 패키지에서 온-프레미스 SQL Server에 액세스하려면 다음을 수행합니다.

1.  SQL Server 구성 관리자에서 TCP/IP 프로토콜을 설정합니다.

2. Windows 방화벽을 통한 액세스를 허용합니다. 자세한 내용은 [Configure Windows firewall to access SQL Server](/sql/sql-server/install/configure-the-windows-firewall-to-allow-sql-server-access)(SQL Server에 액세스하도록 Windows 방화벽 구성)를 참조하세요.

3. 온-프레미스 SQL Server에 연결된 Microsoft Azure Virtual Network에 Azure-SSIS IR을 조인합니다.  자세한 내용은 [Azure-SSIS IR을 Microsoft Azure Virtual Network에 조인](./join-azure-ssis-integration-runtime-virtual-network.md)을 참조하세요.

4. 이 문서에 설명된 대로 SSISDB `catalog.set_execution_credential` 저장 프로시저를 사용하여 자격 증명을 제공합니다.

## <a name="connect-to-a-file-share-on-premises"></a>온-프레미스 파일 공유에 연결

온-프레미스 파일 공유에 연결할 수 있는지 확인하려면 다음을 수행합니다.

1. 이 테스트를 실행하려면 도메인에 가입되지 않은 컴퓨터를 찾습니다.

2. 도메인에 가입하지 않은 컴퓨터에서 다음 명령을 실행합니다. 이러한 명령은 사용할 도메인 자격 증명이 있는 명령 프롬프트 창을 연 다음 디렉터리 목록을 가져와 온-프레미스 파일 공유에 대한 연결을 테스트합니다.

   ```cmd
   runas.exe /netonly /user:<domain>\<username> cmd.exe
   dir \\fileshare
   ```

3. 온-프레미스 파일 공유에 대한 디렉터리 목록이 반환되는지 확인합니다.

### <a name="prerequisites"></a>필수 구성 요소

Azure에서 실행 중인 패키지에서 온-프레미스 파일 공유에 액세스하려면 다음을 수행합니다.

1. Windows 방화벽을 통한 액세스를 허용합니다.

2. 온-프레미스 파일 공유에 연결된 Microsoft Azure Virtual Network에 Azure-SSIS IR을 조인합니다.  자세한 내용은 [Azure-SSIS IR을 Microsoft Azure Virtual Network에 조인](./join-azure-ssis-integration-runtime-virtual-network.md)을 참조하세요.

3. 이 문서에 설명된 대로 SSISDB `catalog.set_execution_credential` 저장 프로시저를 사용하여 자격 증명을 제공합니다.

## <a name="connect-to-a-file-share-on-azure-vm"></a>Azure VM의 파일 공유에 연결

Azure에서 실행 중인 패키지에서 Azure VM의 파일 공유에 액세스하려면 다음을 수행합니다.

1. SSMS나 다른 도구를 사용하여 SSISDB를 호스트하는 SQL Database/SQL Managed Instance에 연결합니다. 자세한 내용은 [Azure에서 SSISDB에 연결](/sql/integration-services/lift-shift/ssis-azure-connect-to-catalog-database)을 참조하세요.

2. SSISDB를 현재 데이터베이스로 사용하여 쿼리 창을 엽니다.

3. 다음 저장 프로시저를 실행하고 적절한 도메인 자격 증명을 제공합니다.

   ```sql
   catalog.set_execution_credential @domain = N'.', @user = N'username of local account on Azure virtual machine', @password = N'password'
   ```

## <a name="connect-to-a-file-share-in-azure-files"></a>Azure 파일의 파일 공유에 연결

Azure Files에 대한 자세한 내용은 [Azure Files](https://azure.microsoft.com/services/storage/files/)를 참조하세요.

Azure에서 실행 중인 패키지에서 Azure Files의 파일 공유에 액세스하려면 다음을 수행합니다.

1. SSMS나 다른 도구를 사용하여 SSISDB를 호스트하는 SQL Database/SQL Managed Instance에 연결합니다. 자세한 내용은 [Azure에서 SSISDB에 연결](/sql/integration-services/lift-shift/ssis-azure-connect-to-catalog-database)을 참조하세요.

2. SSISDB를 현재 데이터베이스로 사용하여 쿼리 창을 엽니다.

3. 다음 저장 프로시저를 실행하고 적절한 도메인 자격 증명을 제공합니다.

   ```sql
   catalog.set_execution_credential @domain = N'Azure', @user = N'<storage-account-name>', @password = N'<storage-account-key>'
   ```

## <a name="next-steps"></a>다음 단계

- 패키지를 배포합니다. 자세한 내용은 [SSMS를 사용하여 Azure에 SSIS 프로젝트 배포](/sql/integration-services/ssis-quickstart-deploy-ssms)를 참조합니다.
- 패키지를 실행합니다. 자세한 내용은 [SSMS를 사용하여 Azure에서 SSIS 패키지 실행](/sql/integration-services/ssis-quickstart-run-ssms)을 참조합니다.
- 패키지를 예약합니다. 자세한 내용은 [Azure에서 SSIS 패키지 예약](/sql/integration-services/lift-shift/ssis-azure-schedule-packages-ssms)을 참조하세요.
