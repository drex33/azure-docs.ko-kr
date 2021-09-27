---
title: Azure-SSIS Integration Runtime 패키지 저장소를 사용하여 패키지 관리
description: Azure-SSIS Integration Runtime 패키지 저장소를 사용하여 패키지를 관리하는 방법을 알아봅니다.
ms.service: data-factory
ms.subservice: integration-services
ms.topic: conceptual
author: swinarko
ms.author: sawinark
ms.custom: seo-lt-2019
ms.date: 09/29/2020
ms.openlocfilehash: ee29630c0f990a7a6342d0027bbf9f36cbf13b91
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124778289"
---
# <a name="manage-packages-with-azure-ssis-integration-runtime-package-store"></a>Azure-SSIS Integration Runtime 패키지 저장소를 사용하여 패키지 관리

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

온-프레미스 SSIS(SQL Server Integration Services) 워크로드를 클라우드로 리프트 앤 시프트하려면 ADF(Azure Data Factory)에서 Azure-SSIS IR(Integration Runtime)을 프로비저닝할 수 있습니다. 자세한 내용은 [Azure-SSIS IR 프로비저닝](./tutorial-deploy-ssis-packages-azure.md)을 참조하세요. Azure-SSIS IR에서 지원하는 작업은 다음과 같습니다.

- Azure SQL Database 서버/Managed Instance(프로젝트 배포 모델)가 호스트하는 SSIS 카탈로그(SSISDB)에 배포된 패키지 실행
- Azure SQL Managed Instance(패키지 배포 모델)가 호스트하는 파일 시스템, Azure Files 또는 SQL Server 데이터베이스(MSDB)에 배포된 패키지 실행

패키지 배포 모델을 사용하는 경우 패키지 저장소를 사용하여 Azure-SSIS IR을 프로비저닝할지 여부를 선택할 수 있습니다. 이러한 저장소는 파일 시스템, Azure Files, 또는 Azure SQL Managed Instance에서 호스트하는 MSDB 위에 패키지 관리 레이어를 제공합니다. Azure-SSIS IR 패키지 저장소를 사용하면 [레거시 SSIS 패키지 저장소](/sql/integration-services/service/package-management-ssis-service)와 비슷하게 SSMS(SQL Server Management Studio)를 통해 패키지를 가져오고/내보내고/삭제하고/실행하고 실행 중인 패키지를 모니터링/중지할 수 있습니다. 

## <a name="connect-to-azure-ssis-ir"></a>Azure-SSIS IR에 연결

Azure-SSIS IR이 프로비저닝되면 이에 연결하여 SSMS에서 패키지 저장소를 찾아볼 수 있습니다.

:::image type="content" source="media/azure-ssis-integration-runtime-package-store/ssms-package-store-connect.png" alt-text="Azure-SSIS IR에 연결":::

SSMS의 **개체 탐색기** 창에 있는 **연결** 드롭다운 메뉴에서 **Azure-SSIS Integration Runtime** 을 선택합니다. 그런 다음 Azure에 로그인하고 패키지 저장소로 프로비저닝한 관련 구독, ADF 및 Azure-SSIS IR을 선택합니다. Azure-SSIS IR이 표시되고 아래에 **실행 중인 패키지** 및 **저장된 패키지** 노드가 나타납니다. **저장된 패키지** 노드를 확장하여 아래의 패키지 저장소를 확인합니다. 패키지 저장소를 확장하여 아래에 폴더 및 패키지를 확인합니다. SSMS가 자동으로 연결하지 못하는 경우 패키지 저장소에 대한 액세스 자격 증명을 입력하라는 메시지가 표시될 수 있습니다. 예를 들어 MSDB 위에 있는 패키지 저장소를 확장하는 경우 먼저 Azure SQL Managed Instance에 연결하라는 메시지가 표시될 수 있습니다.

:::image type="content" source="media/azure-ssis-integration-runtime-package-store/ssms-package-store-connect2.png" alt-text="Azure SQL Managed Instance에 연결":::

## <a name="manage-folders-and-packages"></a>폴더 및 패키지 관리

SSMS의 Azure-SSIS IR에 연결한 후 패키지 저장소, 폴더 또는 패키지를 마우스 오른쪽 단추로 클릭하여 메뉴를 표시하고 **새 폴더**, **패키지 가져오기**, **패키지 내보내기**, **삭제** 또는 **새로 고침** 을 선택할 수 있습니다.

   :::image type="content" source="media/azure-ssis-integration-runtime-package-store/ssms-package-store-manage.png" alt-text="폴더 및 패키지 관리":::

   *  **새 폴더** 를 선택하여 가져온 패키지에 대한 새 폴더를 만듭니다.

   *  **패키지 가져오기** 를 선택하여 **파일 시스템**, **SQL Server**(MSDB) 또는 레거시 **SSIS 패키지 저장소** 에서 패키지 저장소로 패키지를 가져옵니다.

      :::image type="content" source="media/azure-ssis-integration-runtime-package-store/ssms-package-store-import.png" alt-text="패키지 가져오기":::

      가져올 **패키지 위치** 에 따라 관련 **서버**/**인증 유형** 을 선택하고 필요한 경우 액세스 자격 증명을 입력한 다음, **패키지 경로** 를 선택하고 새 **패키지 이름** 을 입력합니다. 패키지를 가져올 때 해당 보호 수준을 변경할 수 없습니다. 이를 변경하려면 SSDT(SQL Server Data Tools) 또는 `dtutil` 명령줄 유틸리티를 사용합니다.

      > [!NOTE]
      > Azure-SSIS IR 패키지 저장소에 SSIS 패키지를 가져오는 것은 한 번만 가능하며, 단순히 SQL Server/SSIS 버전을 유지하면서 기본 MSDB/파일 시스템/Azure Files에만 복사합니다. 
      >
      > Azure-SSIS IR은 현재 **SQL Server 2017** 을 기반으로 하기 때문에 하위 버전 패키지를 실행하면 런타임에 SSIS 2017 패키지로 업그레이드됩니다. 더 높은 버전의 패키지 실행은 지원되지 않습니다.
      >
      > 또한 레거시 SSIS 패키지 저장소는 특정 SQL Server 버전에 바인딩되고 해당 버전의 SSMS에서만 액세스할 수 있으므로, 이전 SSIS 패키지 저장소의 하위 버전 패키지를 SSMS 2019 이상 버전을 사용하는 Azure-SSIS IR 패키지 저장소로 가져오려면 먼저 지정된 SSMS 버전을 사용하여 파일 시스템으로 내보내야 합니다.
      >
      > 또는 보호 수준을 변경하면서 여러 SSIS 패키지를 Azure-SSIS IR 패키지 저장소로 가져오려면 [dtutil](/sql/integration-services/dtutil-utility) 명령줄 유틸리티를 사용합니다. [dtutil을 사용하여 여러 패키지 배포](#deploying-multiple-packages-with-dtutil)를 참조하세요.

   *  **패키지 내보내기** 를 선택하여 패키지 저장소에서 **파일 시스템**, **SQL Server**(MSDB) 또는 레거시 **SSIS 패키지 저장소** 로 패키지를 내보냅니다.

      :::image type="content" source="media/azure-ssis-integration-runtime-package-store/ssms-package-store-export.png" alt-text="패키지 내보내기":::

      내보낼 **패키지 위치** 에 따라 관련 **서버**/**인증 유형** 을 선택하고 필요한 경우 액세스 자격 증명을 입력한 다음, **패키지 경로** 를 선택합니다. 패키지를 내보낼 때 암호화된 경우 먼저 암호를 입력하여 암호를 해독한 다음, 예를 들어 중요한 데이터가 저장되지 않도록 하거나 사용자 키 또는 암호를 사용하여 중요한 데이터 또는 모든 데이터를 암호화하는 등의 보호 수준을 변경할 수 있습니다.

      > [!NOTE]
      > Azure-SSIS IR 패키지 저장소에서 SSIS 패키지를 내보내는 것은 한 번만 가능하며, 보호 수준을 변경하지 않고 내보내면 단순히 SQL Server/SSIS 버전을 유지하면서 복사됩니다. 그렇지 않으면 SSIS 2019 이상 버전 패키지로 업그레이드됩니다.
      >
      > Azure-SSIS IR은 현재 **SQL Server 2017** 을 기반으로 하기 때문에 하위 버전 패키지를 실행하면 런타임에 SSIS 2017 패키지로 업그레이드됩니다. 더 높은 버전의 패키지 실행은 지원되지 않습니다.
      >
      > 또는 보호 수준을 변경하면서 여러 SSIS 패키지를 Azure-SSIS IR 패키지 저장소에서 내보내려면 [dtutil](/sql/integration-services/dtutil-utility) 명령줄 유틸리티를 사용합니다. [dtutil을 사용하여 여러 패키지 배포](#deploying-multiple-packages-with-dtutil)를 참조하세요.

   *  **삭제** 를 선택하여 패키지 저장소에서 기존 폴더/패키지를 삭제합니다.

   *  **새로 고침** 을 선택하여 패키지 저장소에 새로 추가된 폴더/패키지를 표시합니다.

## <a name="execute-packages"></a>패키지 실행

SSMS의 Azure-SSIS IR에 연결한 후 저장된 패키지를 마우스 오른쪽 단추로 클릭하여 메뉴를 표시하고 **패키지 실행** 을 선택할 수 있습니다.  이렇게 하면 ADF 파이프라인에서 SSIS 패키지 실행 작업으로 Azure-SSIS IR 패키지 실행을 구성할 수 있는 **패키지 실행 유틸리티** 대화 상자가 열립니다.

:::image type="content" source="media/azure-ssis-integration-runtime-package-store/ssms-package-store-execute.png" alt-text="패키지 실행 유틸리티 페이지 1 & 2":::

:::image type="content" source="media/azure-ssis-integration-runtime-package-store/ssms-package-store-execute2.png" alt-text="패키지 실행 유틸리티 페이지 3 & 4":::

**패키지 실행 유틸리티** 대화 상자의 **일반**, **구성**, **실행 옵션** 및 **로깅** 페이지는 SSIS 패키지 실행 작업의 **설정** 탭에 해당합니다. 이러한 페이지에서 패키지 암호화 암호와 패키지 구성 파일 액세스 정보를 입력할 수 있습니다. 또한 패키지 실행 자격 증명 및 속성과 로그 폴더에 대한 액세스 정보도 입력할 수 있습니다.  **패키지 실행 유틸리티** 대화 상자의 **값 설정** 페이지는 SSIS 패키지 실행 작업의 **속성 재정의** 탭에 해당하며 여기에 재정의할 기존 패키지 속성을 입력할 수 있습니다. 자세한 내용은 [ADF 파이프라인에서 SSIS 패키지 실행 작업으로 SSIS 패키지 실행](./how-to-invoke-ssis-package-ssis-activity.md)을 참조하세요.

**실행** 단추를 선택하면 SSIS 패키지 실행 작업을 사용하는 새 ADF 파이프라인이 자동으로 생성되고 트리거됩니다. 설정이 동일한 ADF 파이프라인이 이미 있으면 새 파이프라인이 생성되지 않고 기존 파이프라인이 다시 실행됩니다. ADF 파이프라인과 SSIS 패키지 실행 작업은 각각 `Pipeline_SSMS_YourPackageName_HashString` 및 `Activity_SSMS_YourPackageName`으로 명명됩니다.

:::image type="content" source="media/azure-ssis-integration-runtime-package-store/ssms-package-store-execute3.png" alt-text="패키지 실행 유틸리티 단추":::

:::image type="content" source="media/azure-ssis-integration-runtime-package-store/ssis-activity-package-store.png" alt-text="SSIS 패키지 작업 실행":::

## <a name="monitor-and-stop-running-packages"></a>실행 중인 패키지 모니터링 및 중지

SSMS의 Azure-SSIS IR에 연결한 후 **실행 중인 패키지** 노드를 확장하여 아래에서 현재 실행 중인 패키지를 확인할 수 있습니다.  그 중 하나를 마우스 오른쪽 단추로 클릭하여 메뉴를 표시하고 **중지** 또는 **새로 고침** 을 선택합니다.

   :::image type="content" source="media/azure-ssis-integration-runtime-package-store/ssms-package-store-monitor.png" alt-text="실행 중인 패키지 모니터링 및 중지":::

   *  **중지** 를 선택하여 SSIS 패키지 실행 작업으로 패키지를 실행하는 현재 실행 중인 ADF 파이프라인을 취소합니다.

   *  **새로 고침** 을 선택하여 패키지 저장소에서 새로 실행되는 패키지를 표시합니다.

## <a name="monitor-azure-ssis-ir-and-edit-package-stores"></a>Azure-SSIS IR 모니터링 및 패키지 저장소 편집

SSMS의 Azure-SSIS IR에 연결한 후 마우스 오른쪽 단추로 클릭하여 메뉴를 표시하고 **Azure Data Factory 포털로 이동** 또는 **새로 고침** 을 선택할 수 있습니다.

   :::image type="content" source="media/azure-ssis-integration-runtime-package-store/ssms-package-store-monitor2.png" alt-text="ADF 포털로 이동":::

   *  **Azure Data Factory 포털로 이동** 을 선택하여 ADF 모니터링 허브의 **통합 런타임** 페이지를 엽니다. 여기서 Azure-SSIS IR을 모니터링할 수 있습니다. **패키지 저장소** 타일에서 Azure-SSIS IR에 연결된 패키지 저장소 수를 확인할 수 있습니다.  이 숫자를 선택하면 패키지 저장소에 대한 액세스 정보를 저장하는 ADF 연결된 서비스를 편집할 수 있는 창이 표시됩니다.

      :::image type="content" source="media/azure-ssis-integration-runtime-package-store/ssms-package-store-monitor3.png" alt-text="패키지 저장소 편집":::

   *  **새로 고침** 을 선택하여 패키지 저장소에 새로 추가된 폴더/패키지와 패키지 저장소에서 실행 중인 패키지를 표시합니다.

## <a name="deploying-multiple-packages-with-dtutil"></a>dtutil을 사용하여 여러 패키지 배포

레거시 패키지 배포 모델을 유지하면서 온-프레미스 SSIS 워크로드를 ADF의 SSIS로 리프트 앤 시프트하려면 패키지를 파일 시스템, SQL Server에서 호스트하는 MSDB 또는 레거시 SSIS 패키지 저장소에서 Azure Files, Azure SQL Managed Instance에서 호스트하는 MSDB 또는 Azure-SSIS IR 패키지 저장소에 배포해야 합니다. 이와 동시에, 아직 수행하지 않은 경우 보호 수준을 사용자 키로 암호화에서 암호화되지 않음 또는 암호로 암호화로 전환해야 합니다.

SQL Server/SSIS 설치와 함께 제공되는 [dtutil](/sql/integration-services/dtutil-utility) 명령줄 유틸리티를 사용하여 여러 패키지를 일괄 처리로 배포할 수 있습니다. 특정 SSIS 버전에 바인딩되어 있으므로 이 유틸리티를 사용하여 보호 수준을 변경하지 않고 하위 버전의 패키지를 배포하는 경우에는 SSIS 버전을 유지하면서 복사하기만 하면 됩니다. 이 유틸리티를 사용하여 패키지를 배포하고 동시에 보호 수준을 변경하는 경우 패키지를 SSIS 버전으로 업그레이드합니다.

 Azure-SSIS IR은 현재 **SQL Server 2017** 을 기반으로 하기 때문에 하위 버전 패키지를 실행하면 런타임에 SSIS 2017 패키지로 업그레이드됩니다. 더 높은 버전의 패키지 실행은 지원되지 않습니다.

따라서 런타임 업그레이드를 방지하려면 패키지 배포 모델에서 Azure-SSIS IR에 실행되는 패키지를 배포할 때 SQL Server/SSIS 2017 설치와 함께 제공되는 dtutil 2017을 사용해야 합니다. 이 목적을 위해 무료 [SQL Server/SSIS 2017 Developer Edition](https://go.microsoft.com/fwlink/?linkid=853016)을 다운로드하여 설치할 수 있습니다. 이 버전이 설치되면 `YourLocalDrive:\Program Files\Microsoft SQL Server\140\DTS\Binn` 폴더에서 dtutil 2017을 찾을 수 있습니다.

### <a name="deploying-multiple-packages-from-file-system-on-premises-into-azure-files-with-dtutil"></a>dtutil을 사용하여 온-프레미스 파일 시스템에서 Azure Files로 여러 패키지 배포

 파일 시스템에서 Azure Files로 여러 패키지를 배포하고 동시에 해당 보호 수준을 전환하려면 명령 프롬프트에서 다음 명령을 실행합니다. 해당 사례와 관련된 모든 문자열을 바꾸세요.
  
```dos
REM Persist the access credentials for Azure Files on your local machine
cmdkey /ADD:YourStorageAccountName.file.core.windows.net /USER:azure\YourStorageAccountName /PASS:YourStorageAccountKey

REM Connect Azure Files to a drive on your local machine
net use Z: \\YourStorageAccountName.file.core.windows.net\YourFileShare /PERSISTENT:Yes

REM Go to a local folder where you store your packages
cd YourLocalDrive:\...\YourPackageFolder

REM Run dtutil in a loop to deploy your packages from the local folder into Azure Files while switching their protection level
for %f in (*.dtsx) do dtutil.exe /FILE %f /ENCRYPT FILE;Z:\%f;2;YourEncryptionPassword
```

위의 명령을 배치 파일에서 실행하려면 `%f`를 `%%f`로 바꿉니다.

파일 시스템 위의 레거시 SSIS 패키지 저장소에서 Azure Files로 여러 패키지를 배포하고 동시에 해당 보호 수준을 전환하려면 동일한 명령을 사용할 수 있지만 `YourLocalDrive:\...\YourPackageFolder`를 레거시 SSIS 패키지 저장소에 사용되는 로컬 폴더 `YourLocalDrive:\Program Files\Microsoft SQL Server\YourSQLServerDefaultCompatibilityLevel\DTS\Packages\YourPackageFolder`로 바꿉니다. 예를 들어 레거시 SSIS 패키지 저장소가 SQL Server 2016에 바인딩되어 있는 경우 `YourLocalDrive:\Program Files\Microsoft SQL Server\130\DTS\Packages\YourPackageFolder`로 이동합니다.  [SQL Server 기본 호환성 수준 목록](/sql/t-sql/statements/alter-database-transact-sql-compatibility-level#arguments)에서 `YourSQLServerDefaultCompatibilityLevel` 값을 찾을 수 있습니다.

Azure Files 위에 Azure-SSIS IR 패키지 저장소를 구성한 경우 SSMS 2019 이상 버전에서 Azure-SSIS IR에 연결할 때 배포된 패키지가 표시됩니다.

### <a name="deploying-multiple-packages-from-msdb-on-premises-into-msdb-in-azure-with-dtutil"></a>dtutil을 사용하여 온-프레미스 MSDB에서 Azure의 MSDB로 여러 패키지 배포

 SQL Server에서 호스트하는 MSDB 또는 MSDB 위의 레거시 SSIS 패키지 저장소에서 Azure SQL Managed Instance에서 호스트하는 MSDB로 여러 패키지를 배포하고 동시에 해당 보호 수준을 변경하려면 SSMS의 SQL Server에 연결하고 SSMS **개체 탐색기** 에서 `Databases->System Databases->msdb` 노드를 마우스 오른쪽 단추로 클릭하여 **새 쿼리** 창을 열고 다음 T-SQL 스크립트를 실행합니다. 해당 사례와 관련된 모든 문자열을 바꾸세요.  
  
```sql
BEGIN
  SELECT 'dtutil /SQL '+f.foldername+'\'+NAME+' /ENCRYPT SQL;'+f.foldername+'\'+NAME+';2;YourEncryptionPassword /DestServer YourSQLManagedInstanceEndpoint /DestUser YourSQLAuthUsername /DestPassword YourSQLAuthPassword'
  FROM msdb.dbo.sysssispackages p
  inner join msdb.dbo.sysssispackagefolders f
  ON p.folderid = f.folderid
END
```

Azure SQL Managed Instance의 프라이빗/퍼블릭 엔드포인트를 사용하려면 `YourSQLManagedInstanceEndpoint`를 각각 `YourSQLMIName.YourDNSPrefix.database.windows.net`/`YourSQLMIName.public.YourDNSPrefix.database.windows.net,3342`로 바꿉니다.

이 스크립트는 MSDB의 모든 패키지에 대한 dtutil 명령줄을 생성합니다. 그러면 명령 프롬프트에서 패키지를 다중 선택하고, 복사하여 붙여넣고, 실행할 수 있습니다.

:::image type="content" source="media/azure-ssis-integration-runtime-package-store/sql-server-msdb-to-sql-mi-msdb.png" alt-text="dtutil 명령줄 생성":::

```dos
dtutil /SQL YourFolder\YourPackage1 /ENCRYPT SQL;YourFolder\YourPackage1;2;YourEncryptionPassword /DestServer YourSQLManagedInstanceEndpoint /DestUser YourUserName /DestPassword YourPassword
dtutil /SQL YourFolder\YourPackage2 /ENCRYPT SQL;YourFolder\YourPackage2;2;YourEncryptionPassword /DestServer YourSQLManagedInstanceEndpoint /DestUser YourUserName /DestPassword YourPassword
dtutil /SQL YourFolder\YourPackage3 /ENCRYPT SQL;YourFolder\YourPackage3;2;YourEncryptionPassword /DestServer YourSQLManagedInstanceEndpoint /DestUser YourUserName /DestPassword YourPassword
```

MSDB 위에 Azure-SSIS IR 패키지 저장소를 구성한 경우 SSMS 2019 이상 버전에서 Azure-SSIS IR에 연결할 때 배포된 패키지가 표시됩니다.

### <a name="deploying-multiple-packages-from-msdb-on-premises-into-azure-files-with-dtutil"></a>dtutil을 사용하여 온-프레미스 MSDB에서 Azure Files로 여러 패키지 배포

 SQL Server에서 호스트하는 MSDB 또는 MSDB 위의 레거시 SSIS 패키지 저장소에서 Azure Files로 여러 패키지를 배포하고 동시에 해당 보호 수준을 변경하려면 SSMS의 SQL Server에 연결하고 SSMS **개체 탐색기** 에서 `Databases->System Databases->msdb` 노드를 마우스 오른쪽 단추로 클릭하여 **새 쿼리** 창을 열고 다음 T-SQL 스크립트를 실행합니다. 해당 사례와 관련된 모든 문자열을 바꾸세요.  
  
```sql
BEGIN
  SELECT 'dtutil /SQL '+f.foldername+'\'+NAME+' /ENCRYPT FILE;Z:\'+f.foldername+'\'+NAME+'.dtsx;2;YourEncryptionPassword' 
  FROM msdb.dbo.sysssispackages p
  inner join msdb.dbo.sysssispackagefolders f
  ON p.folderid = f.folderid
END
```

이 스크립트는 MSDB의 모든 패키지에 대한 dtutil 명령줄을 생성합니다. 그러면 명령 프롬프트에서 패키지를 다중 선택하고, 복사하여 붙여넣고, 실행할 수 있습니다.

```dos
REM Persist the access credentials for Azure Files on your local machine
cmdkey /ADD:YourStorageAccountName.file.core.windows.net /USER:azure\YourStorageAccountName /PASS:YourStorageAccountKey

REM Connect Azure Files to a drive on your local machine
net use Z: \\YourStorageAccountName.file.core.windows.net\YourFileShare /PERSISTENT:Yes

REM Multiselect, copy & paste, and run the T-SQL-generated dtutil command lines to deploy your packages from MSDB on premises into Azure Files while switching their protection level
dtutil /SQL YourFolder\YourPackage1 /ENCRYPT FILE;Z:\YourFolder\YourPackage1.dtsx;2;YourEncryptionPassword
dtutil /SQL YourFolder\YourPackage2 /ENCRYPT FILE;Z:\YourFolder\YourPackage2.dtsx;2;YourEncryptionPassword
dtutil /SQL YourFolder\YourPackage3 /ENCRYPT FILE;Z:\YourFolder\YourPackage3.dtsx;2;YourEncryptionPassword
```

Azure Files 위에 Azure-SSIS IR 패키지 저장소를 구성한 경우 SSMS 2019 이상 버전에서 Azure-SSIS IR에 연결할 때 배포된 패키지가 표시됩니다.

## <a name="next-steps"></a>다음 단계

SSIS 패키지 실행 작업을 사용하는 자동 생성된 ADF 파이프라인은 다시 실행/편집하거나 ADF 포털에서 새로 만들 수 있습니다. 자세한 내용은 [ADF 파이프라인에서 SSIS 패키지 실행 작업으로 SSIS 패키지 실행](./how-to-invoke-ssis-package-ssis-activity.md)을 참조하세요.