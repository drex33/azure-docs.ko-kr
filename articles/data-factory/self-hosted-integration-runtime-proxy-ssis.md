---
title: 자체 호스팅 통합 런타임을 SSIS의 프록시로 구성
description: 자체 호스팅 통합 런타임을 Azure-SSIS Integration Runtime의 프록시로 구성하는 방법을 알아봅니다.
ms.service: data-factory
ms.subservice: integration-services
ms.topic: conceptual
author: swinarko
ms.author: sawinark
ms.custom: seo-lt-2019, devx-track-azurepowershell
ms.date: 10/22/2021
ms.openlocfilehash: 776329b23ab4e0fdd11e48da6ec1a7cc3d21a7f7
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/05/2021
ms.locfileid: "131852316"
---
# <a name="configure-a-self-hosted-ir-as-a-proxy-for-an-azure-ssis-ir-in-azure-data-factory"></a>Azure Data Factory에서 자체 호스팅 IR을 Azure-SSIS IR의 프록시로 구성

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

이 문서에서는 자체 호스팅 IR(자체 호스팅 통합 런타임)을 프록시로 구성하여 ADF(Azure Data Factory)의 Azure-SSIS IR(Azure-SSIS Integration Runtime)에서 SSIS(SQL Server Integration Services) 패키지를 실행하는 방법을 설명합니다. 

이 기능을 사용하면 [Azure-SSIS IR을 가상 네트워크에 조인](./join-azure-ssis-integration-runtime-virtual-network.md)하지 않고도 데이터에 액세스하고 온-프레미스에서 작업을 실행할 수 있습니다. 이 기능은 회사 네트워크에 구성이 너무 복잡하거나 정책이 너무 제한적이어서 Azure-SSIS IR을 삽입할 수 없는 경우에 유용합니다.

지금은 SSIS 데이터 흐름 태스크 및 SQL/프로세스 실행 태스크에서만 이 기능을 실행할 수 있습니다. 

데이터 흐름 태스크에서 사용하도록 설정하면 이 기능은 적용 가능할 때마다 두 개의 준비 작업으로 분할됩니다. 
* **온-프레미스 준비 작업**: 이 작업은 자체 호스팅 IR에서 온-프레미스 데이터 저장소에 연결하는 데이터 흐름 구성 요소를 실행합니다. 온-프레미스 데이터 저장소에서 Azure Blob Storage의 준비 영역으로, 또는 그 반대로 데이터를 이동합니다.
* **클라우드 준비 작업**: 이 작업은 Azure-SSIS IR에서 온-프레미스 데이터 저장소에 연결하지 않는 데이터 흐름 구성 요소를 실행합니다. Azure Blob Storage의 준비 영역에서 클라우드 데이터 저장소로, 또는 그 반대로 데이터를 이동합니다.

데이터 흐름 태스크가 온-프레미스에서 클라우드로 데이터를 이동하는 경우 첫 번째 및 두 번째 준비 작업은 각각 온-프레미스 준비 작업과 클라우드 준비 작업입니다. 데이터 흐름 태스크가 클라우드에서 온-프레미스로 데이터를 이동하는 경우 첫 번째 및 두 번째 준비 작업은 각각 클라우드 준비 작업과 온-프레미스 준비 작업입니다. 데이터 흐름 태스크가 온-프레미스에서 온-프레미스로 데이터를 이동하는 경우 첫 번째 및 두 번째 준비 작업은 모두 온-프레미스 준비 작업입니다. 데이터 흐름 태스크가 클라우드에서 클라우드로 데이터를 이동하는 경우에는 이 기능이 적용되지 않습니다.

SQL/프로세스 실행 태스크에서 이 기능을 사용하면 자체 호스팅 IR에서 이 기능을 실행할 수 있습니다. 

이 기능의 다른 이점 및 기능을 활용하면 예를 들어 Azure-SSIS IR에서 아직 지원하지 않는 지역에 자체 호스팅 IR을 설정하고 데이터 원본의 방화벽에서 자체 호스팅 IR의 공용 고정 IP 주소를 허용할 수 있습니다.

## <a name="prepare-the-self-hosted-ir"></a>자체 호스팅 IR 준비

이 기능을 사용하려면 먼저 데이터 팩터리를 만들고 여기에 Azure-SSIS IR을 설정합니다. 아직 설정하지 않은 경우 [Azure-SSIS IR 설정](./tutorial-deploy-ssis-packages-azure.md)의 지침을 따르세요.

그런 다음 Azure-SSIS IR이 설정된 것과 동일한 데이터 팩터리에서 자체 호스팅 IR을 설정합니다. 이렇게 하려면 [자체 호스팅 IR 만들기](./create-self-hosted-integration-runtime.md)를 참조하세요.

마지막으로, 다음과 같이 온-프레미스 머신 또는 Azure VM(가상 머신)에 최신 버전의 자체 호스팅 IR과 추가 드라이버 및 런타임을 다운로드하여 설치합니다.
- 최신 버전의 [자체 호스팅 IR](https://www.microsoft.com/download/details.aspx?id=39717)을 다운로드하고 설치합니다.
- 패키지에서 OLEDB(개체 연결 및 포함 데이터베이스), ODBC(Open Database Connectivity) 또는 ADO.NET 커넥터를 사용하는 경우 자체 호스팅 IR이 설치된 컴퓨터에 관련 드라이버를 다운로드하여 설치합니다(아직 수행하지 않은 경우).  

  이전 버전의 SQL Server용 OLEDB 드라이버(SQL Server Native Client[SQLNCLI])를 사용하는 경우 [64비트 버전을 다운로드](https://www.microsoft.com/download/details.aspx?id=50402)합니다.  

  최신 버전의 SQL Server용 OLEDB 드라이버(MSOLEDBSQL)를 사용하는 경우 [64비트 버전을 다운로드](https://www.microsoft.com/download/details.aspx?id=56730)합니다.  
  
  PostgreSQL, MySQL, Oracle 등의 다른 데이터베이스 시스템에 OLEDB/ODBC/ADO.NET 드라이버를 사용하는 경우 해당 웹 사이트에서 64비트 버전을 다운로드할 수 있습니다.
- 패키지에 있는 Azure 기능 팩의 데이트 흐름 구성 요소를 사용하는 경우 자체 호스팅 IR이 설치된 머신와 동일한 머신에 [SQL Server 2017용 Azure Feature Pack을 다운로드하고 설치](https://www.microsoft.com/download/details.aspx?id=54798)합니다(아직 수행하지 않은 경우).
- 아직 설치하지 않은 경우 자체 호스팅 IR이 설치된 컴퓨터에서 [64비트 버전의 VC(Visual C++) 런타임을 다운로드하여 설치](https://www.microsoft.com/en-us/download/details.aspx?id=40784)합니다.

### <a name="enable-windows-authentication-for-on-premises-tasks"></a>온-프레미스 작업에 Windows 인증 활성화

온-프레미스 준비 작업을 수행하고 자체 호스팅 IR에서 SQL/프로세스 실행 태스크에 Windows 인증이 필요한 경우에는 [Windows 인증 기능을 Azure-SSIS IR에도 구성](/sql/integration-services/lift-shift/ssis-azure-connect-with-windows-auth)해야 합니다. 

온-프레미스 준비 작업 및 SQL/프로세스 실행 태스크는 자체 호스팅 IR 서비스 계정(*NT SERVICE\DIAHostService* 이 기본값)을 사용하여 호출되고, 데이터 저장소는 Windows 인증 계정을 사용하여 액세스됩니다. 두 계정 모두 특정 보안 정책을 할당해야 합니다. 자체 호스팅 IR 컴퓨터에서 **로컬 보안 정책** > **로컬 정책** > **사용자 권한 할당** 으로 이동하여 다음을 수행합니다.

1. 자체 호스팅 IR 서비스 계정에 '프로세스에 대한 메모리 할당량 조정' 및 '프로세스 수준 토큰 바꾸기' 정책을 할당합니다. 이는 기본 서비스 계정으로 자체 호스팅 IR을 설치할 때 자동으로 할당됩니다. 그렇지 않은 경우 해당 정책을 수동으로 할당합니다. 다른 서비스 계정을 사용하는 경우 동일한 정책을 할당합니다.

1. Windows 인증 계정에 '서비스로 로그온' 정책을 할당합니다.

## <a name="prepare-the-azure-blob-storage-linked-service-for-staging"></a>스테이징을 위해 Azure Blob Storage 연결된 서비스 준비

아직 만들지 않은 경우 Azure-SSIS IR이 설정된 동일한 데이터 팩터리에 Azure Blob Storage 연결된 서비스를 만듭니다. 이렇게 하려면 [Azure Data Factory 연결된 서비스 만들기](./quickstart-create-data-factory-portal.md#create-a-linked-service)를 참조하세요. 다음 단계는 반드시 수행해야 합니다.
- **데이터 저장소** 에서 **Azure Blob Storage** 를 선택합니다.  
- **통합 런타임을 통한 연결** 의 경우 **AutoResolveIntegrationRuntime**(자체 호스팅 IR 아님)을 선택하여, 이를 무시하고 대신 Azure-SSIS IR을 사용하여 Azure Blob Storage에 대한 액세스 자격 증명을 페치할 수 있습니다.
- **인증 방법** 에서 **계정 키**, **SAS URI**, **서비스 주체**, **관리 ID** 또는 **사용자가 할당한 관리 ID** 를 선택합니다.  

>[!TIP]
>**서비스 주체** 방법을 선택하는 경우 서비스 주체에 '스토리지 Blob 데이터 기여자' 이상의 역할을 부여합니다. 자세한 내용은 [Azure Blob Storage 커넥터](connector-azure-blob-storage.md#linked-service-properties)를 참조하세요. **관리 ID**/**사용자가 할당한 관리 ID** 방법을 선택하는 경우 ADF에 대해 지정한 시스템/사용자가 할당한 관리 ID에 Azure Blob Storage에 액세스할 수 있는 적절한 역할을 부여합니다. 자세한 내용은 [ADF에 대해 지정한 시스템/사용자가 할당한 관리 ID로 Azure AD(Azure Active Directory) 인증을 사용하여 Azure Blob Storage에 액세스](/sql/integration-services/connection-manager/azure-storage-connection-manager#managed-identities-for-azure-resources-authentication)를 참조하세요.

:::image type="content" source="media/self-hosted-integration-runtime-proxy-ssis/shir-azure-blob-storage-linked-service.png" alt-text="스테이징을 위해 Azure Blob Storage에 연결된 서비스 준비":::

## <a name="configure-an-azure-ssis-ir-with-your-self-hosted-ir-as-a-proxy"></a>자체 호스팅 IR을 프록시로 사용하여 Azure-SSIS IR 구성

준비 프로세스를 위해 자체 호스팅 IR 및 Azure Blob Storage에 연결된 서비스를 준비한 후에는, 이제 데이터 팩터리 포털 또는 앱에서 자체 호스팅 IR을 사용하여 새 또는 기존 Azure-SSIS IR을 구성할 수 있습니다. 그러나 이렇게 하기 전에 기존 Azure-SSIS IR이 이미 실행 중인 경우에는 이를 중지, 편집 및 다시 시작할 수 있습니다.

1. **통합 런타임 설정** 창에서 **일반 설정** 및 **배포 설정** 페이지를 **계속** 버튼을 눌러서 넘어갑니다. 

1. **고급 설정** 페이지에서 다음을 수행합니다.

   1. **자체 호스팅 통합 런타임을 Azure-SSIS Integration Runtime의 프록시로 설정** 확인란을 선택합니다. 

   1. **자체 호스팅 통합 런타임** 드롭다운 목록에서 기존 자체 호스팅 IR을 Azure-SSIS IR의 프록시로 선택합니다.

   1. **준비 스토리지에 연결된 서비스** 드롭다운 리스트에서, 기존 Azure Blob Storage에 연결된 서비스를 선택하거나 새로운 서비스를 준비 용도로 생성합니다.

   1. **준비 경로** 상자에서 선택한 Azure Blob Storage 계정에 Blob 컨테이너를 지정하거나 준비 용도로 기본값 항목을 사용하도록 비워 둡니다.

   1. **계속** 단추를 선택합니다.

   :::image type="content" source="./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings-shir.png" alt-text="자체 호스팅 IR을 사용하는 고급 설정":::

PowerShell을 사용하여 자체 호스팅 IR을 프록시로 사용하도록 새 또는 기존 Azure-SSIS IR을 구성할 수도 있습니다.

```powershell
$ResourceGroupName = "[your Azure resource group name]"
$DataFactoryName = "[your data factory name]"
$AzureSSISName = "[your Azure-SSIS IR name]"
# Self-hosted integration runtime info - This can be configured as a proxy for on-premises data access 
$DataProxyIntegrationRuntimeName = "" # OPTIONAL to configure a proxy for on-premises data access 
$DataProxyStagingLinkedServiceName = "" # OPTIONAL to configure a proxy for on-premises data access 
$DataProxyStagingPath = "" # OPTIONAL to configure a proxy for on-premises data access 

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
Start-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
    -DataFactoryName $DataFactoryName `
    -Name $AzureSSISName `
    -Force
```

## <a name="enable-ssis-packages-to-use-a-proxy"></a>프록시를 사용하여 SSIS 패키지 활성화

최신 SSDT를 Visual Studio용 SSIS 프로젝트 확장 또는 독립 실행형 설치 프로그램으로 사용하면 지원되는 데이터 흐름 구성 요소의 연결 관리자에서 새 `ConnectByProxy` 속성을 찾고 SQL/프로세스 실행 태스크에서 `ExecuteOnProxy` 속성을 찾을 수 있습니다.
* [Visual Studio용 SSIS 프로젝트 확장 다운로드](https://marketplace.visualstudio.com/items?itemName=SSIS.SqlServerIntegrationServicesProjects)
* [독립 실행형 설치 관리자 다운로드](/sql/ssdt/download-sql-server-data-tools-ssdt#ssdt-for-vs-2017-standalone-installer)   

온-프레미스 데이터에 액세스하는 구성 요소를 사용하여 데이터 흐름 태스크가 포함된 새 패키지를 디자인할 때,관련된 연결 관리자의 **속성** 창에서 `ConnectByProxy` 속성을 *True* 로 설정할 수 있습니다.

온-프레미스로 작동하는 SQL/프로세스 실행 태스크를 포함한 새 패키지를 디자인할 때, 작업 자체와 관련된 **속성** 창에서 `ExecuteOnProxy` 속성을 *True* 로 설정합니다.

:::image type="content" source="media/self-hosted-integration-runtime-proxy-ssis/shir-proxy-properties.png" alt-text="ConnectByProxy/ExecuteOnProxy 속성 활성화":::

또한 기존 패키지를 실행하는 경우에는 속성을 수동으로 변경하지 않고도 `ConnectByProxy`/`ExecuteOnProxy` 속성을 사용하도록 설정할 수 있습니다. 두 가지 옵션 중이 있습니다.
- **옵션 A**: 최신 SSDT를 사용하여 Azure-SSIS IR에서 실행할 패키지를 포함하는 프로젝트를 열고 다시 빌드하고 다시 배포합니다. 그런 다음 SSMS에서 패키지를 작동할 때 **패키지 실행** 팝업창의 **연결 관리자** 탭에 표시되는 연관된 연결 관리자에 대하여 `ConnectByProxy` 속성을 *True* 로 설정하여 사용하도록 설정할 수 있습니다.

  :::image type="content" source="media/self-hosted-integration-runtime-proxy-ssis/shir-connection-managers-tab-ssms.png" alt-text="ConnectByProxy/ExecuteOnProxy property2 활성화":::

  Data Factory 파이프라인에서 패키지를 실행할 때 [SSIS 패키지 실행 작업](./how-to-invoke-ssis-package-ssis-activity.md)의 **연결 관리자** 탭에 표시되는 연관된 연결 관리자에 대하여 `ConnectByProxy` 속성을 *True* 로 설정하여 사용하도록 설정할 수도 있습니다.
  
  :::image type="content" source="media/self-hosted-integration-runtime-proxy-ssis/shir-connection-managers-tab-ssis-activity.png" alt-text="ConnectByProxy/ExecuteOnProxy property3 활성화":::

- **옵션 B:** SSIS IR에서 실행할 패키지를 포함하는 프로젝트를 다시 배포합니다. SSMS에서 패키지를 실행할 때 속성 경로 및 `\Package.Connections[YourConnectionManagerName].Properties[ConnectByProxy]`/`\Package\YourExecuteSQLTaskName.Properties[ExecuteOnProxy]`/`\Package\YourExecuteProcessTaskName.Properties[ExecuteOnProxy]`를 제공하여 `ConnectByProxy`/`ExecuteOnProxy` 속성을 사용하도록 설정할 수 있으며, 또한 **패키지 실행** 팝업창의 **고급** 탭에서 속성이 재정의되면서 이를 *True* 로 설정하여 사용하도록 설정할 수 있습니다.

  :::image type="content" source="media/self-hosted-integration-runtime-proxy-ssis/shir-advanced-tab-ssms.png" alt-text="ConnectByProxy/ExecuteOnProxy property4 활성화":::

  Data Factory 파이프라인에서 패키지를 실행할 때 속성 경로 및 `\Package.Connections[YourConnectionManagerName].Properties[ConnectByProxy]`/`\Package\YourExecuteSQLTaskName.Properties[ExecuteOnProxy]`/`\Package\YourExecuteProcessTaskName.Properties[ExecuteOnProxy]`를 제공하여 `ConnectByProxy`/`ExecuteOnProxy` 속성을 사용하도록 설정할 수 있으며, 또한 [SSIS 패키지 실행 작업](./how-to-invoke-ssis-package-ssis-activity.md)의 **속성 재정의** 탭에서 속성이 재정의되면서 이를 *True* 로 설정하여 사용하도록 설정할 수도 있습니다.
  
  :::image type="content" source="media/self-hosted-integration-runtime-proxy-ssis/shir-property-overrides-tab-ssis-activity.png" alt-text="ConnectByProxy/ExecuteOnProxy property5 활성화":::

## <a name="debug-the-on-premises-tasks-and-cloud-staging-tasks"></a>온-프레미스 작업 및 클라우드 준비 작업 디버그

자체 호스팅 IR에서 *C:\ProgramData\SSISTelemetry* 폴더에 있는 런타임 로그와 *C:\ProgramData\SSISTelemetry\ExecutionLog* 폴더에 있는 온-프레미스 준비 작업의 실행 로그를 찾을 수 있습니다. SSISDB에서 패키지를 저장한 위치에 따라 SSISDB, 지정된 로깅 파일 경로 또는 Azure Monitor에서 클라우드 준비 작업의 실행 로그를 찾거나 [Azure Monitor 통합](./monitor-ssis.md)을 사용하도록 설정하는 등의 작업을 수행할 수 있습니다. 클라우드 준비 작업의 실행 로그에서 온-프레미스 준비 작업의 고유 ID를 확인할 수도 있습니다. 

:::image type="content" source="media/self-hosted-integration-runtime-proxy-ssis/shir-first-staging-task-guid.png" alt-text="첫 번째 준비 작업의 고유 ID":::

고객 지원 티켓을 제출한 경우 조사를 위해 최신 작업/실행 로그를 보내기 위해 자체 호스팅 IR에 설치된 **Microsoft Integration Runtime 구성 관리자** 의 **진단** 탭에서 **로그 보내기** 단추를 선택할 수 있습니다.

## <a name="billing-for-the-on-premises-tasks-and-cloud-staging-tasks"></a>온-프레미스 작업 및 클라우드 준비 작업에 대한 청구

자체 호스팅 IR에서 실행되는 온-프레미스 준비 작업 및 SQL/프로세스 실행 태스크는 자체 호스팅 IR에서 실행되는 데이터 이동 작업과 마찬가지로 별도의 요금이 청구됩니다. 이는 [Azure Data Factory 데이터 파이프라인 가격 책정](https://azure.microsoft.com/pricing/details/data-factory/data-pipeline/) 문서에 지정되어 있습니다.

Azure-SSIS IR에서 실행되는 클라우드 준비 작업은 별도로 청구되지 않지만 실행 중인 Azure-SSIS IR은 [Azure-SSIS IR 가격 책정](https://azure.microsoft.com/pricing/details/data-factory/ssis/) 문서에 지정된 대로 요금이 청구됩니다.

## <a name="enable-custom3rd-party-data-flow-components"></a>사용자 지정/타사 데이트 흐름 구성 요소 활성화 

사용자 지정/타사 데이트 흐름 구성 요소가 자체 호스팅 IR을 Azure-SSIS IR용 프록시로 사용하여 온-프레미스 데이터에 액세스할 수 있도록 하려면 다음 지침을 따르세요.

1. [표준/빠른 사용자 지정 설치](./how-to-configure-azure-ssis-ir-custom-setup.md)를 통해 Azure-SSIS IR에서 SQL Server 2017을 대상으로 하는 사용자 지정/타사 데이트 흐름 구성 요소를 설치합니다.

1. 아직 존재하지 않는 경우 자체 호스팅 IR에서 다음 DTSPath 레지스트리 키를 만듭니다.
   1. `C:\Program Files\Microsoft SQL Server\140\DTS\`로 설정된 `Computer\HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Microsoft SQL Server\140\SSIS\Setup\DTSPath`
   1. `C:\Program Files (x86)\Microsoft SQL Server\140\DTS\`로 설정된 `Computer\HKEY_LOCAL_MACHINE\SOFTWARE\WOW6432Node\Microsoft\Microsoft SQL Server\140\SSIS\Setup\DTSPath`
   
1. 위의 DTSPath 아래에 자체 호스팅 IR에 SQL Server 2017을 대상으로 하는 사용자 지정/타사 데이터 흐름 구성 요소를 설치하고 설치 프로세스가 아래 내용을 충족하는지 확인합니다.

   1. `<DTSPath>`, `<DTSPath>/Connections`, `<DTSPath>/PipelineComponents` 및 `<DTSPath>/UpgradeMappings` 폴더가 만들어집니다(아직 존재하지 않는 경우).
   
   1. `<DTSPath>/UpgradeMappings` 폴더에 확장 매핑을 위한 고유한 XML 파일을 만듭니다.
   
   1. 사용자 지정/타사 데이터 흐름 구성 요소 어셈블리가 참조하는 모든 어셈블리를 GAC(전역 어셈블리 캐시)에 설치합니다.

다음은 Azure-SSIS IR용 프록시로 빠른 사용자 지정 설치 및 자체 호스팅 IR을 사용하도록 데이트 흐름 구성 요소를 조정하는 파트너인 [Theobald Software](https://kb.theobald-software.com/xtract-is/XIS-for-Azure-SHIR) 및 [Aecorsoft](https://www.aecorsoft.com/blog/2020/11/8/using-azure-data-factory-to-bring-sap-data-to-azure-via-self-hosted-ir-and-ssis-ir)의 예시입니다.

## <a name="enforce-tls-12"></a>TLS 1.2 적용

준비에 Azure Blob Storage 포함하여 가장 강력한 암호화/가장 안전한 네트워크 프로토콜(TLS 1.2)만 사용하도록 구성된 데이터 저장소에 액세스해야 하는 경우 TLS 1.2만 사용하도록 설정하고 자체 호스팅 IR에서 동시에 이전 SSL/TLS 버전을 사용하지 않도록 설정해야 합니다. 이렇게 하려면 공개 미리 보기 Blob 컨테이너의 *CustomSetupScript/UserScenarios/TLS 1.2* 폴더에서 제공하는 *main.cmd* 스크립트를 다운로드하여 실행할 수 있습니다. [Azure Storage Explorer](https://storageexplorer.com/)를 사용하여 다음 SAS URI를 입력해 공개 미리 보기 Blob 컨테이너에 연결할 수 있습니다.

`https://ssisazurefileshare.blob.core.windows.net/publicpreview?sp=rl&st=2020-03-25T04:00:00Z&se=2025-03-25T04:00:00Z&sv=2019-02-02&sr=c&sig=WAD3DATezJjhBCO3ezrQ7TUZ8syEUxZZtGIhhP6Pt4I%3D`

## <a name="current-limitations"></a>현재 제한 사항

- Hadoop/HDFS/DQS 구성 요소를 제외하고 Azure-SSIS IR Standard Edition에서 기본 제공/미리 설치된 데이터 흐름 구성 요소만 현재 지원됩니다. [Azure-SSIS IR의 모든 기본 제공/미리 설치된 구성 요소](./built-in-preinstalled-components-ssis-integration-runtime.md)를 참조하세요.
- 관리 코드(.NET Framework)로 작성된 사용자 지정/타사 데이터 흐름 구성 요소만 현재 지원됩니다. 네이티브 코드(C++)로 작성된 데이터 흐름 구성 요소는 현재 지원되지 않습니다.
- 온-프레미스 및 클라우드 준비 작업에서 변수 값을 변경하는 작업은 현재 지원되지 않습니다.
- 온-프레미스 준비 작업에서 개체 형식의 변수 값을 변경해도 다른 작업에는 반영되지 않습니다.
- OLEDB 원본에서 *ParameterMapping* 은 현재 지원되지 않습니다. 차선책으로 변수를 사용한 SQL 명령을 *AccessMode* 로 사용하고 식을 사용하여 SQL 명령에 변수/매개 변수를 삽입하세요. 그림으로 *ParameterMappingSample.dtsx* 패키지를 공개 미리 보기 Blob 컨테이너의 *SelfHostedIRProxy/Limitations* 폴더에서 찾아 참조하세요. Azure Storage Explorer를 사용하여 위의 SAS URI를 입력해 공개 미리 보기 Blob 컨테이너에 연결할 수 있습니다.

## <a name="next-steps"></a>다음 단계

자체 호스팅 IR을 Azure-SSIS IR의 프록시로 구성한 후에는 Data Factory 파이프라인에서 SSIS 패키지 실행 작업으로 온-프레미스 데이터에 액세스하기 위해 패키지를 배포하고 실행할 수 있습니다. 자세히 알아보려면 [Data Factory 파이프라인에서 SSIS 패키지 실행 작업으로 SSIS 패키지 실행](./how-to-invoke-ssis-package-ssis-activity.md)을 참조하세요.
