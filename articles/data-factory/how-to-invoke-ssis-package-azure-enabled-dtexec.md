---
title: Azure 지원 dtexec 유틸리티를 사용하여 SQL Server Integration Services(SSIS) 패키지 실행하기
description: Azure 지원 dtexec 유틸리티를 사용한 SQL Server Integration Services(SSIS) 패키지를 실행 방법 알아보기.
ms.service: data-factory
ms.subservice: integration-services
ms.topic: conceptual
ms.date: 10/22/2021
author: swinarko
ms.author: sawinark
ms.openlocfilehash: 633c7f52bb015769ab2e15c5fbde23d5d4128ee9
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/05/2021
ms.locfileid: "131843559"
---
# <a name="run-sql-server-integration-services-packages-with-the-azure-enabled-dtexec-utility"></a>Azure 지원 dtexec 유틸리티를 사용하여 SQL Server Integration Services 패키지 실행

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

이 문서에서는 Azure 지원 dtexec(AzureDTExec) 명령 프롬프트 유틸리티에 대해 설명합니다. Azure Data Factory에서 Azure-SSIS Integration Runtime(IR)에 SQL Server Integration Services(SSIS) 패키지를 실행하는 데 사용됩니다.

기존 dtexec 유틸리티는 SQL Server와 함께 제공됩니다. 자세한 내용은 [dtexec 유틸리티](/sql/integration-services/packages/dtexec-utility)를 참조하세요. 온-프레미스 SSIS 패키지 실행 시 ActiveBatch, Control-M 등의 타사 오케스트레이터 또는 스케줄러에 의해 호출되는 경우가 많습니다. 

최신 AzureDTExec 유틸리티는 SQL Server Management Studio(SSMS) 도구와 함께 제공됩니다. Azure에서 SSIS 패키지를 실행하기 위해 타사 오케스트레이터 또는 스케줄러에 의해 호출될 수도 있습니다. SSIS 패키지를 클라우드로 전환하거나 마이그레이션하기 용이합니다. 마이그레이션 후 일상 작업에서 타사 오케스트레이터 또는 스케줄러를 계속 사용하려는 경우, 이제 dtexec 대신 AzureDTExec를 호출할 수 있습니다.

AzureDTExec는 Data Factory 파이프라인에서 SSIS 패키지 실행으로 패키지를 실행합니다. 자세한 내용은 [Azure Data Factory 작업으로서의 SSIS 패키지 실행](./how-to-invoke-ssis-package-ssis-activity.md)을 참조하세요. 

SSMS를 통해 AzureDTExec를 구성하여 데이터 팩터리에서 파이프라인을 생성하는 Azure AD(Azure Active Directory) 애플리케이션을 사용할 수 있습니다. 또한 패키지를 저장하는 파일 시스템, 파일 공유, Azure Files에 액세스하도록 구성할 수 있습니다. 사용자가 호출 옵션에 대해 지정한 값에 따라 AzureDTExec는 Execute SSIS Package 작업을 동해 고유한 Data Factory 파이프라인을 생성하고 실행합니다. 해당 옵션에 대해 동일한 값을 사용하여 AzureDTExec를 호출하면 기존 파이프라인이 다시 실행됩니다.

## <a name="prerequisites"></a>필수 조건
AzureDTExec를 사용하려면 최신 버전의 SSMS(버전 18.3 이상)를 다운로드하여 설치합니다. [이 웹 사이트](/sql/ssms/download-sql-server-management-studio-ssms)에서 다운로드합니다.

## <a name="configure-the-azuredtexec-utility"></a>AzureDTExec 유틸리티 구성하기
로컬 머신에 SSMS 설치 시 AzureDTExec도 함께 설치됩니다. 설정을 구성하려면 **관리자 권한으로 실행** 옵션을 사용하여 SSMS를 시작합니다. 그런 다음 **도구** > **Azure로 마이그레이션**  >  **Azure 지원 DTExec 구성** 을 선택합니다.

:::image type="content" source="media/how-to-invoke-ssis-package-azure-enabled-dtexec/ssms-azure-enabled-dtexec-menu.png" alt-text="Azure 지원 dtexec 메뉴 구성":::

이 작업을 수행할 경우, *AzureDTExec.settings* 파일에 쓰기 위해 관리자 권한으로 열어야 하는 **AzureDTExecConfig** 창이 열립니다. SSMS를 관리자로 실행하지 않은 경우, UAC(사용자 계정 컨트롤) 창이 열립니다. 관리자 암호를 입력하여 권한을 승급합니다.

:::image type="content" source="media/how-to-invoke-ssis-package-azure-enabled-dtexec/ssms-azure-enabled-dtexec-settings.png" alt-text="Azure 지원 dtexec 설정 구성":::

**AzureDTExecConfig** 창에서 다음과 같이 구성 설정을 입력합니다.

- **ApplicationId**: 데이터 팩터리에 파이프라인을 생성하기 위해 올바른 권한을 사용하여 만든 Azure AD 앱의 고유 식별자를 입력합니다. 자세한 내용은 [Azure Portal로 Azure AD 앱 및 서비스 주체 만들기](../active-directory/develop/howto-create-service-principal-portal.md)를 참조하세요.
- **AuthenticationKey**: Azure AD 앱에 인증 키를 입력합니다.
- **TenantId**: Azure AD 앱이 생성된 Azure AD 테넌트의 고유 식별자를 입력합니다.
- **DataFactory**: AzureDTExec를 호출할 때 제공되는 옵션 값에 따라 생성되는 SSIS 패키지 작업 실행이 포함된 고유 파이프라인에 있는 데이터 팩터리의 이름을 입력합니다.
- **IRName**: AzureDTExec를 호출할 때 UNC(범용 명명 규칙) 경로에서 지정된 패키지가 실행되는 데이터 팩터리의 Azure-SSIS IR 이름을 입력합니다.
- **PipelineNameHashStrLen**: AzureDTExec를 호출할 때 제공하는 옵션 값에서 생성할 해시 문자열의 길이를 입력합니다. 문자열은 Azure-SSIS IR에서 패키지를 실행하는 Data Factory 파이프라인에 대한 고유 이름을 구성하는 데 사용됩니다. 일반적으로 32글자면 충분합니다.
- **ResourceGroup**: 데이터 팩터리가 생성된 Azure 리소스 그룹의 이름을 입력합니다.
- **SubscriptionId**: 데이터 팩터리가 생성된 Azure 구독의 고유 식별자를 입력합니다.
- **LogAccessDomain**: 로그 파일을 작성할 때 해당 UNC 경로의 로그 폴더에 액세스하려면 도메인 자격 증명을 입력합니다(**LogPath** 가 지정되고 **LogLevel** 이 **Null** 이 아닌 경우 필수).
- **LogAccessPassword**: 로그 파일을 작성할 때 해당 UNC 경로의 로그 폴더에 액세스하려면 암호 자격 증명을 입력합니다(**LogPath** 가 지정되고 **LogLevel** 이 **Null** 이 아닌 경우 필수).
- **LogAccessUserName**: 로그 파일을 작성할 때 해당 UNC 경로의 로그 폴더에 액세스하려면 사용자 이름 자격 증명을 입력합니다(**LogPath** 가 지정되고 **LogLevel** 이 **Null** 이 아닌 경우 필수).
- **LogLevel**: Azure-SSIS IR의 패키지 실행에 대해 사전 정의된 **Null**, **Basic**, **Verbose**, **Performance** 옵션 중 로깅 범위를 선택하여 입력합니다.
- **LogPath**: Azure-SSIS IR의 패키지 실행에서 로그 파일이 기록되는 로그 폴더의 UNC 경로를 입력합니다.
- **PackageAccessDomain**: AzureDTExec를 호출할 때 지정된 UNC 경로의 패키지에 액세스하려면 도메인 자격 증명을 입력합니다.
- **PackageAccessPassword**: AzureDTExec를 호출할 때 지정된 UNC 경로의 패키지에 액세스하려면 암호 자격 증명을 입력합니다.
- **PackageAccessUserName**: AzureDTExec를 호출할 때 지정된 UNC 경로의 패키지에 액세스하려면 사용자 이름 자격 증명을 입력합니다.

패키지 및 로그 파일을 파일 시스템 또는 온-프레미스 파일 공유에 저장하기 위해서는 패키지를 가져와 로그 파일을 쓸 수 있도록 온-프레미스 네트워크에 연결된 가상 네트워크에 Azure-SSIS IR를 조인합니다. 자세한 내용은 [가상 네트워크에서 Azure-SSIS IR 조인하기](./join-azure-ssis-integration-runtime-virtual-network.md)를 참조하세요.

일반 텍스트에 *AzureDTExec.settings* 파일에 기록된 중요한 값이 표시되지 않도록 하려면 Base64 인코딩 문자열로 인코딩합니다. AzureDTExec를 호출하면 모든 Base64 인코딩 문자열이 원래 값으로 다시 디코딩됩니다. 액세스할 수 있는 계정을 제한하여 *AzureDTExec.settings* 파일의 보안을 강화할 수 있습니다.

## <a name="invoke-the-azuredtexec-utility"></a>AzureDTExec 유틸리티 호출
명령줄 프롬프트에서 AzureDTExec를 호출하고 사용 사례 시나리오에서 특정 옵션에 대한 관련 값을 제공할 수 있습니다.

유틸리티는 `{SSMS Folder}\Common7\IDE\CommonExtensions\Microsoft\SSIS\150\Binn`에 설치됩니다. 'PATH' 환경 변수에 해당 경로를 추가하여 어디에서나 호출할 수 있습니다.

```dos
> cd "C:\Program Files (x86)\Microsoft SQL Server Management Studio 18\Common7\IDE\CommonExtensions\Microsoft\SSIS\150\Binn"
> AzureDTExec.exe  ^
  /F \\MyStorageAccount.file.core.windows.net\MyFileShare\MyPackage.dtsx  ^
  /Conf \\MyStorageAccount.file.core.windows.net\MyFileShare\MyConfig.dtsConfig  ^
  /Conn "MyConnectionManager;Data Source=MyDatabaseServer.database.windows.net;User ID=MyAdminUsername;Password=MyAdminPassword;Initial Catalog=MyDatabase"  ^
  /Set \package.variables[MyVariable].Value;MyValue  ^
  /De MyEncryptionPassword
```

AzureDTExec를 호출하면 dtexec를 호출한 것과 비슷한 옵션이 제공됩니다. 자세한 내용은 [dtexec Utility](/sql/integration-services/packages/dtexec-utility)를 참조하세요. 현재 지원되는 옵션은 다음과 같습니다.

- **/F[ile]** : 파일 시스템, 파일 공유 또는 Azure Files에 저장된 패키지를 로드합니다. 이 옵션의 값으로 파일 시스템, 파일 공유 또는 .dtsx 확장명을 사용하는 Azure Files 패키지 파일에 대한 UNC 경로를 지정할 수 있습니다. 지정된 UNC 경로에 공백이 있으면 전체 경로 주위에 따옴표를 추가합니다.
- **/Conf[igFile]** : 값을 추출할 구성 파일을 지정합니다. 이 옵션을 사용하면 디자인 타임에 지정된 것과 다른 패키지에 대한 런타임 구성을 설정할 수 있습니다. XML 구성 파일에서 다른 설정을 저장한 다음 패키지 실행 전에 로드할 수 있습니다. 자세한 내용은 [SSIS 패키지 구성](/sql/integration-services/packages/package-configurations)을 참조하세요. 이 옵션의 값을 지정하려면 파일 시스템, 파일 공유 또는 dtsConfig 확장명을 사용하는 Azure Files 구성 파일에 대한 UNC 경로를 사용합니다. 지정된 UNC 경로에 공백이 있으면 전체 경로 주위에 따옴표를 추가합니다.
- **/Conn[ection]** : 패키지의 기존 연결 관리자에 대한 연결 문자열을 지정합니다. 이 옵션을 사용하면 디자인 타임에 지정된 것과 다른 패키지의 기존 연결 관리자에 대한 런타임 연결 문자열을 설정할 수 있습니다. 이 옵션의 값을 `connection_manager_name_or_id;connection_string [[;connection_manager_name_or_id;connection_string]...]`과 같이 지정합니다.
- **/Set**: 패키지에서 매개 변수, 변수, 속성, 컨테이너, 로그 공급자, Foreach 반복기 또는 연결의 구성을 재정의합니다. 이 옵션은 여러 번 지정할 수 있습니다. 이 옵션의 값을 `property_path;value`과 같이 지정합니다. 예를 들어, `\package.variables[counter].Value;1`은 `counter` 변수의 값을 1로 재정의합니다. **패키지 구성** 마법사를 사용하여 값을 재정의할 패키지 항목에 대한 `property_path` 값을 찾아서 복사하고 붙여넣을 수 있습니다. 자세한 내용은 [패키지 구성 마법사](/sql/integration-services/packages/legacy-package-deployment-ssis)를 참조하세요.
- **/De[crypt]** : **EncryptAllWithPassword**/**EncryptSensitiveWithPassword** 보호 수준으로 구성된 패키지에 대한 해독용 암호를 설정합니다.

> [!NOTE]
> 해당 옵션에 대한 새로운 값을 사용하여 AzureDTExec를 호출하면 옵션 **/De[cript]** 를 제외한 새 파이프라인이 생성됩니다.

## <a name="next-steps"></a>다음 단계

AzureDTExec를 호출할 때 Execute SSIS Package 작업을 포함한 고유 파이프라인이 생성 및 실행되면, Data Factory 포털에서 모니터링이 가능합니다. 또한 Data Factory를 사용하여 오케스트레이션/예약하려는 경우, Data Factory 트리거를 할당할 수 있습니다. 자세한 내용은 [Data Factory 작업으로 SSIS 패키지 실행](./how-to-invoke-ssis-package-ssis-activity.md)을 참조하세요.

> [!WARNING]
> 생성된 파이프라인은 AzureDTExec에서만 사용해야 합니다. 해당 속성 또는 매개 변수는 추후 변경될 수 있으므로, 다른 용도로 수정 또는 재사용해서는 안 됩니다. 수정하면 AzureDTExec가 중단될 수 있습니다. 문제가 발생하면 파이프라인을 삭제합니다. AzureDTExec는 이후 호출 시 새 파이프라인을 생성합니다.