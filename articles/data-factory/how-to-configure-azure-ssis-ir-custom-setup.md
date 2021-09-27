---
title: Azure-SSIS Integration Runtime 설치 사용자 지정
description: 이 문서에서는 Azure-SSIS Integration Runtime에 대한 사용자 지정 설치 인터페이스를 사용하여 추가 구성 요소를 설치하거나 설정을 변경하는 방법을 설명합니다.
ms.service: data-factory
ms.subservice: integration-services
ms.topic: conceptual
author: swinarko
ms.author: sawinark
ms.custom: seo-lt-2019
ms.date: 04/30/2021
ms.openlocfilehash: 626afa6926dea10a633a5c7d5438ec8b8c578b6a
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124798658"
---
# <a name="customize-the-setup-for-an-azure-ssis-integration-runtime"></a>Azure-SSIS Integration Runtime 설치 사용자 지정

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

사용자 지정 설치를 통해 Azure Data Factory(ADF)에서 Azure SQL Server Integration Services(SSIS) Integration Runtime(IR)을 사용자 지정할 수 있습니다. Azure-SSIS IR의 프로비저닝 또는 재구성 중에 자체 단계를 추가할 수 있습니다. 

사용자 지정 설치를 사용하여 Azure-SSIS IR의 기본 운영 구성 또는 환경을 변경할 수 있습니다. 예를 들어, Windows 서비스를 추가로 시작하려면 파일 공유에 대한 자격 증명 액세스를 유지하거나 강력한 암호화/더 안전한 네트워크 프로토콜(TLS 1.2)을 사용합니다. 또는 어셈블리, 드라이버 또는 확장과 같은 추가 구성 요소를 Azure-SSIS IR의 각 노드에 설치할 수 있습니다. 사용자 지정, 오픈 소스, 타사 구성 요소 등을 사용할 수 있습니다. 기본 제공/미리 설치된 구성 요소에 대한 자세한 내용은 [Azure-SSIS IR의 기본 제공/미리 설치된 구성 요소](./built-in-preinstalled-components-ssis-integration-runtime.md)를 참조하세요.

다음 두 가지 방법 중 하나를 사용하여 Azure-SSIS IR에서 사용자 지정 설치를 수행할 수 있습니다. 
* **스크립트를 사용하는 표준 사용자 지정 설치**: 스크립트 및 관련 파일을 준비하고, Azure Storage 계정의 Blob 컨테이너에 모두 업로드합니다. 그런 다음, Azure-SSIS IR을 설치하거나 재구성할 때 Blob 컨테이너에 대한 SAS(공유 액세스 서명) URI(Uniform Resource Identifier)를 제공합니다. 그러면 각 Azure-SSIS IR의 각 노드에서 Blob 컨테이너로부터 스크립트 및 관련 파일을 다운로드하고, 관리자 권한으로 사용자 지정 설치를 실행합니다. 사용자 지정 설치가 완료되면 각 노드에서 실행에 대한 표준 출력 및 기타 로그를 Blob 컨테이너에 업로드합니다.
* **스크립트를 사용하지 않는 기본 사용자 지정 설치**: 일부 일반적인 시스템 구성 및 Windows 명령을 실행하거나 스크립트를 사용하지 않고 자주 사용하거나 추천되는 추가 구성 요소를 설치합니다.

사용 허가되지 않은 평가판 구성 요소 및 사용 허가된 유료 구성 요소는 모두 표준 및 기본 사용자 지정 설치를 통해 설치할 수 있습니다. ISV(독립 소프트웨어 공급업체)의 경우, [유료 또는 사용 허가된 Azure-SSIS IR용 구성 요소 개발](how-to-develop-azure-ssis-ir-licensed-components.md)을 참조하세요.

> [!IMPORTANT]
> 향후 향상된 기능을 활용하려면 사용자 지정 설치에서 v3 이상 시리즈의 노드를 Azure-SSIS IR에 사용하는 것이 좋습니다.

## <a name="current-limitations"></a>현재 제한 사항

표준 사용자 지정 설치에만 적용되는 제한 사항은 다음과 같습니다.

- 스크립트에서 *gacutil.exe* 를 사용하여 어셈블리를 GAC(글로벌 어셈블리 캐시)에 설치하려면 사용자 지정 설치의 일부로 *gacutil.exe* 를 제공해야 합니다. 또는 공개 미리 보기 Blob 컨테이너의 *샘플* 폴더에 제공된 복사본을 사용할 수 있습니다. 아래의 **표준 사용자 지정 설치 샘플** 섹션을 참조하세요.

- 스크립트에서 하위 폴더를 참조하려는 경우 *msiexec.exe* 에서 루프 폴더를 참조하는 `.\` 표기법을 지원하지 않습니다. `msiexec /i ".\MySubfolder\MyInstallerx64.msi" ...` 대신 `msiexec /i "MySubfolder\MyInstallerx64.msi" ...`와 같은 명령을 사용합니다.

- 관리 공유 또는 Windows에서 자동으로 만드는 숨겨진 네트워크 공유는 현재 Azure-SSIS IR에서 지원되지 않습니다.

- IBM iSeries Access ODBC 드라이버는 Azure-SSIS IR에서 지원되지 않습니다. 사용자 지정 설치 중에 설치 오류가 표시될 수 있습니다. 이 경우 IBM 지원에 문의하여 지원을 요청하세요.

## <a name="prerequisites"></a>사전 요구 사항

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Azure-SSIS IR을 사용자 지정하려면 다음 항목이 필요합니다.

- [Azure 구독](https://azure.microsoft.com/)

- [Azure-SSIS IR 프로 비전](./tutorial-deploy-ssis-packages-azure.md)

- [Azure Storage 계정](https://azure.microsoft.com/services/storage/) 기본 사용자 지정 설치에는 필요하지 않습니다. 표준 사용자 지정 설치의 경우 사용자 지정 설치 스크립트 및 관련 파일을 Blob 컨테이너에 업로드하고 저장합니다. 사용자 지정 설정 프로세스에서는 실행 로그도 같은 Blob 컨테이너에 업로드합니다.

## <a name="instructions"></a>Instructions

ADF UI에 대한 사용자 지정 설정으로 Azure-SSIS IR를 프로비저닝하거나 다시 구성할 수 있습니다. PowerShell을 사용하여 동일한 작업을 수행 하려는 경우 [Azure PowerShell](/powershell/azure/install-az-ps)을 다운로드하여 설치합니다.

### <a name="standard-custom-setup"></a>표준 사용자 지정 설치

ADF UI에서 표준 사용자 지정 설정으로 Azure-SSIS IR를 프로비저닝하거나 다시 구성하려면 다음 단계를 완료합니다.

1. 사용자 지정 설정 스크립트 및 관련 파일(예: .bat, .cmd, .exe, .dll, .msi, 또는 .ps1 파일)을 준비합니다.

   * 사용자 지정 설치의 진입점인 *main.cmd* 라는 스크립트 파일이 있어야 합니다.  
   * 스크립트가 자동으로 실행되도록 하려면 먼저 로컬 머신에서 테스트해야 합니다.  
   * 다른 도구(예: *msiexec.exe*)에서 생성한 추가 로그가 Blob 컨테이너에 업로드되도록 하려면 미리 정의된 `CUSTOM_SETUP_SCRIPT_LOG_DIR` 환경 변수를 스크립트의 로그 폴더로 지정합니다(예: *msiexec /i xxx.msi /quiet /lv %CUSTOM_SETUP_SCRIPT_LOG_DIR%\install.log*).

1. [Azure Storage Explorer](https://storageexplorer.com/)를 다운로드하여 설치하고 엽니다.

   a. **로컬 및 연결됨** 아래에서 마우스 오른쪽 단추로 **스토리지 계정** 을 클릭한 다음, **Azure Storage에 연결** 을 선택합니다.

      :::image type="content" source="media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image1.png" alt-text="Azure Storage에 연결":::

   b. **스토리지 계정 또는 서비스** 를 선택하고 **계정 이름 및 키** 를 선택한 후 **다음** 을 선택합니다.

   다. Azure Storage 계정 이름과 키를 입력하고 **다음** 을 선택하고 **연결** 을 선택합니다.

      :::image type="content" source="media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image3.png" alt-text="스토리지 계정 이름 및 키 제공":::

   d. 연결된 Azure Storage 계정 아래에서 마우스 오른쪽 단추로 **Blob 컨테이너** 를 클릭하고, **Blob 컨테이너 만들기** 를 선택하고, 새 Blob 컨테이너의 이름을 지정합니다.

      :::image type="content" source="media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image4.png" alt-text="Blob 컨테이너 만들기":::

   e. 새 Blob 컨테이너를 선택하고, 사용자 지정 설치 스크립트 및 관련 파일을 업로드합니다. *main.cmd* 는 폴더가 아니라 Blob 컨테이너의 최상위 수준에 업로드해야 합니다. Blob 컨테이너에는 필요한 사용자 지정 설치 파일만 포함되어 있으므로 나중에 Azure-SSIS IR에 다운로드하는 데 시간이 오래 걸리지 않습니다. 사용자 지정 설치의 최대 기간은 현재 45분 이내로 설정되어 있습니다. 여기에는 Blob 컨테이너에서 모든 파일을 다운로드하여 Azure-SSIS IR에 설치하는 시간이 포함됩니다. 설치에 더 많은 시간이 필요한 경우 지원 티켓을 제출합니다.

      :::image type="content" source="media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image5.png" alt-text="Blob 컨테이너에 파일 업로드":::

   f. Blob 컨테이너를 마우스 오른쪽 단추로 클릭하고, **공유 액세스 서명 가져오기** 를 선택합니다.

      :::image type="content" source="media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image6.png" alt-text="Blob 컨테이너에 대한 공유 액세스 서명 가져오기":::

   g. 충분히 긴 만료 시간 및 읽기/쓰기/목록 권한을 사용하여 Blob 컨테이너에 대한 SAS URI를 만듭니다. 사용자 지정 설정 스크립트 및 관련 파일을 다운로드하여 실행하기 위해 SAS URI가 필요합니다. 이는 Azure-SSIS IR 노드가 이미지로 다시 설치되거나 다시 시작될 때마다 발생합니다. 또한 설치 실행 로그를 업로드하려면 쓰기 권한이 있어야 합니다.

      > [!IMPORTANT]
      > 특히 이 기간 동안 정기적으로 Azure-SSIS IR을 중지하고 시작하는 경우 SAS URI가 만료되지 않고 만들기에서 삭제까지의 Azure-SSIS IR의 전체 수명 주기 동안 사용자 지정 설치 리소스가 항상 사용할 수 있는지 확인합니다.

      :::image type="content" source="media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image7.png" alt-text="Blob 컨테이너에 대한 공유 액세스 서명 생성":::

   h. Blob 컨테이너의 SAS URI를 복사하여 저장합니다.

      :::image type="content" source="media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image8.png" alt-text="공유 액세스 서명 복사 및 저장":::

1. **통합 런타임 설정** 창의 **고급 설정** 페이지에서 **추가 시스템 구성/구성 요소 설치를 사용하여 Azure-SSIS Integration Runtime 사용자 지정** 확인란을 선택합니다. 그런 다음 **사용자 지정 설치 컨테이너 SAS URI** 텍스트 상자에서 Blob 컨테이너의 SAS URI를 입력합니다.

   :::image type="content" source="./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings-custom.png" alt-text="사용자 지정 설치가 포함된 고급 설정":::

표준 사용자 지정 설치가 완료되고 Azure-SSIS IR이 시작되면 Blob 컨테이너의 *main.cmd.log* 폴더에서 모든 사용자 지정 설치 로그를 확인할 수 있습니다. 여기에는 *main.cmd* 및 기타 실행 로그의 표준 출력이 포함됩니다.

### <a name="express-custom-setup"></a>기본 사용자 지정 설치

ADF UI에서 기본 사용자 지정 설치로 Azure-SSIS IR을 프로비저닝하거나 다시 구성하려면 다음 단계를 완료합니다.

1. **통합 런타임 설정** 창의 **고급 설정** 페이지에서 **추가 시스템 구성/구성 요소 설치를 사용하여 Azure-SSIS Integration Runtime 사용자 지정** 확인란을 선택합니다. 

1. **새로 만들기** 를 선택하여 **기본 사용자 지정 설치 추가** 창을 연 다음, **기본 사용자 지정 설치 형식** 드롭다운 목록에서 형식을 선택합니다. 현재는 cmdkey 명령을 실행하고, 환경 변수를 추가하고, Azure PowerShell을 설치하고, 허가 받은 구성 요소를 설치하기 위해 기본 사용자 지정 설치를 제안합니다.

#### <a name="running-cmdkey-command"></a>cmdkey 명령 실행

기본 사용자 지정 설치에서 **cmdkey 명령 실행** 형식을 선택하는 경우 Azure-SSIS IR에서 Windows cmdkey 명령을 실행할 수 있습니다. 이렇게 하려면 **/Add**, **/User**, **/Pass** 텍스트 상자에 대상 컴퓨터의 이름 또는 도메인 이름, 사용자 이름 또는 계정 이름, 암호 또는 계정 키를 각각 입력합니다. 이렇게 해서 Azure-SSIS IR에서 SQL Server, 파일 공유 또는 Azure Files에 대한 액세스 자격 증명을 유지할 수 있습니다. 예를 들어, Azure Files에 액세스하려면 **/Add**, **/user**, **/Pass** 에 `YourAzureStorageAccountName.file.core.windows.net`, `azure\YourAzureStorageAccountName` 및 `YourAzureStorageAccountKey`를 각각 입력할 수 있습니다. 이는 로컬 컴퓨터에서 Windows [cmdkey](/windows-server/administration/windows-commands/cmdkey) 명령을 실행하는 것과 비슷합니다. 현재는 cmdkey 명령 실행에 기본 사용자 지정 설치 하나만 지원됩니다. 여러 cmdkey 명령을 실행하려면 표준 사용자 지정 설치를 대신 사용합니다.

#### <a name="adding-environment-variables"></a>환경 변수 추가

기본 사용자 지정 설치 시 **환경 변수 추가** 형식을 선택하는 경우 Azure-SSIS IR에 Windows 환경 변수를 추가할 수 있습니다. 이렇게 하려면 **변수 이름** 및 **변수 값** 텍스트 상자에 각각 환경 변수 이름과 값을 입력합니다. 이렇게 해서 Azure-SSIS IR에서 실행되는 패키지의 환경 변수(예: 스크립트 구성 요소/작업)를 사용할 수 있습니다. 이는 로컬 컴퓨터에서 Windows [set](/windows-server/administration/windows-commands/set_1) 명령을 실행하는 것과 비슷합니다.

#### <a name="installing-azure-powershell"></a>Azure PowerShell 설치

기본 사용자 지정 설치 시 **Azure PowerShell 설치** 형식을 선택하는 경우 Azure-SSIS IR에서 PowerShell의 Az 모듈을 설치할 수 있습니다. 그러기 위해서는 [지원되는 목록](https://www.powershellgallery.com/packages/az)에서 원하는 Az 모듈 버전 번호(x.y.z)를 입력합니다. 패키지에서 Azure PowerShell cmdlet/스크립트를 실행하여 Azure 리소스를 관리할 수 있습니다(예: [Azure Analysis Services(AAS)](../analysis-services/analysis-services-powershell.md)).

#### <a name="installing-licensed-components"></a>사용 허가된 구성 요소 설치

기본 사용자 지정 설치 시 **사용 허가된 구성 요소 설치** 형식을 선택하는 경우, **구성 요소 이름** 드롭다운 목록에서 ISV 파트너의 통합 구성 요소를 선택할 수 있습니다.

* **SentryOne의 Task Factory** 구성 요소를 선택하는 경우 **라이선스 키** 상자에서 구매한 제품 라이선스 키를 입력하여 SentryOne의 [Task Factory](https://www.sentryone.com/products/task-factory/high-performance-ssis-components) 구성 요소 제품군을 Azure-SSIS IR에 설치할 수 있습니다. 현재 통합 버전은 **2020.21.2** 입니다.

* **oh22의 HEDDA.IO** 구성 요소를 선택하는 경우, oh22의 [HEDDA.IO](https://github.com/oh22is/HEDDA.IO/tree/master/SSIS-IR) 데이터 품질/정리 구성 요소를 Azure-SSIS IR에 설치할 수 있습니다. 그러기 위해서는 서비스를 미리 구매해야 합니다. 현재 통합 버전은 **1.0.14** 입니다.

* **oh22의 SQLPhonetics.NET** 구성 요소를 선택하는 경우 oh22의 [SQLPhonetics.NET](https://appsource.microsoft.com/product/web-apps/oh22.sqlphonetics-ssis) 데이터 품질/일치 구성 요소를 Azure-SSIS IR에 설치할 수 있습니다. 그러기 위해서는 **라이선스 키** 텍스트 상자에 미리 구매한 제품 라이선스 키를 입력합니다. 현재 통합 버전은 **1.0.45** 입니다.
   
* **KingswaySoft의 SSIS Integration Toolkit** 구성 요소를 선택하는 경우 **라이선스 키** 상자에서 구매한 제품 라이선스 키를 입력하여 KingswaySoft의 CRM/ERP/마케팅/협업 앱(예: Microsoft Dynamics/SharePoint/Project Server, Oracle/Salesforce Marketing Cloud 등)용 [SSIS 통합 도구 키트](https://www.kingswaysoft.com/products/ssis-integration-toolkit-for-microsoft-dynamics-365) 커넥터 제품군을 Azure-SSIS IR에 설치할 수 있습니다. 현재 통합 버전은 **20.2** 입니다.

* **KingswaySoft의 SSIS Productivity Pack** 구성 요소를 선택하는 경우 **라이선스 키** 상자에서 구매한 제품 라이선스 키를 입력하여 KingswaySoft의 [SSIS Productivity Pack](https://www.kingswaysoft.com/products/ssis-productivity-pack) 구성 요소 제품군을 Azure-SSIS IR에 설치할 수 있습니다. 현재 통합 버전은 **20.2** 입니다.

* **Theobald Software의 Xtract IS** 구성 요소를 선택하는 경우 구매한 제품 라이선스 파일을 **라이선스 파일** 상자에 끌어서 놓거나 업로드하여 Theobald Software의 SAP 시스템(ERP, S/4HANA, BW)용 [Xtract IS](https://theobald-software.com/en/xtract-is/) 커넥터 제품군을 Azure-SSIS IR에 설치할 수 있습니다. 현재 통합 버전은 **6.5.13.18** 입니다.

* **AecorSoft의 Integration Service** 구성 요소를 선택하는 경우, AecorSoft에서 SAP와 Salesforce 시스템용 [Integration Service](https://www.aecorsoft.com/en/products/integrationservice) 커넥터 제품군을 Azure-SSIS IR에 설치할 수 있습니다. 그러기 위해서는 **라이선스 키** 텍스트 상자에 미리 구매한 제품 라이선스 키를 입력합니다. 현재 통합 버전은 **3.0.00** 입니다.

* **CData의 SSIS Standard Package** 구성 요소를 선택하는 경우 Microsoft SharePoint 커넥터와 같이 CData에서 가장 인기 있는 구성 요소의 [SSIS Standard Package](https://www.cdata.com/kb/entries/ssis-adf-packages.rst#standard) 제품군을 Azure-SSIS IR에 설치할 수 있습니다. 그러기 위해서는 **라이선스 키** 텍스트 상자에 미리 구매한 제품 라이선스 키를 입력합니다. 현재 통합 버전은 **19.7354** 입니다.

* **CData의 SSIS Extended Package** 구성 요소를 선택하는 경우, **SSIS Extended Package** 의 Microsoft Dynamics 365 Business Central 커넥터 및 기타 구성 요소와 같이 CData의 모든 구성 요소에 대한 [SSIS Standard Package](https://www.cdata.com/kb/entries/ssis-adf-packages.rst#extended) 제품군을 Azure-SSIS IR에 설치할 수 있습니다. 그러기 위해서는 **라이선스 키** 텍스트 상자에 미리 구매한 제품 라이선스 키를 입력합니다. 현재 통합 버전은 **19.7354** 입니다. 크기가 크기 때문에 설치 시간 초과를 방지하기 위해 Azure-SSIS IR에 노드 당 CPU 코어가 4개 이상인지 확인하세요.

추가된 기본 사용자 지정 설치가 **고급 설정** 페이지에 표시됩니다. 저장소를 제거하려면 해당 확인란을 선택한 다음, **삭제** 를 선택합니다.

### <a name="azure-powershell"></a>Azure PowerShell

Azure PowerShell을 사용하여 사용자 지정 설정과 함께 Azure-SSIS IR를 프로비저닝하거나 다시 구성하려면 다음 단계를 완료합니다.

1. Azure-SSIS IR이 이미 시작되었거나 실행 중인 경우 먼저 중지합니다.

1. 그런 다음 Azure-SSIS IR를 시작하기 전에 `Set-AzDataFactoryV2IntegrationRuntime` cmdlet을 실행하여 사용자 지정 설치를 추가하거나 제거할 수 있습니다.

   ```powershell
   $ResourceGroupName = "[your Azure resource group name]"
   $DataFactoryName = "[your data factory name]"
   $AzureSSISName = "[your Azure-SSIS IR name]"
   # Custom setup info: Standard/express custom setups
   $SetupScriptContainerSasUri = "" # OPTIONAL to provide a SAS URI of blob container for standard custom setup where your script and its associated files are stored
   $ExpressCustomSetup = "[RunCmdkey|SetEnvironmentVariable|InstallAzurePowerShell|SentryOne.TaskFactory|oh22is.SQLPhonetics.NET|oh22is.HEDDA.IO|KingswaySoft.IntegrationToolkit|KingswaySoft.ProductivityPack|Theobald.XtractIS|AecorSoft.IntegrationService|CData.Standard|CData.Extended or leave it empty]" # OPTIONAL to configure an express custom setup without script

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
   Start-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
       -DataFactoryName $DataFactoryName `
       -Name $AzureSSISName `
       -Force
   ```

### <a name="standard-custom-setup-samples"></a>표준 사용자 지정 설치 샘플

표준 사용자 지정 설치의 일부 샘플을 보고 다시 사용하려면, 다음 단계를 완료합니다.

1. Azure Storage Explorer를 사용하여 공개 미리 보기 Blob 컨테이너에 연결합니다.

   a. **로컬 및 연결됨** 아래에서 마우스 오른쪽 단추로 **스토리지 계정** 을 클릭한 다음, **Azure Storage에 연결** 을 선택합니다.

      :::image type="content" source="media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image1.png" alt-text="Azure Storage에 연결":::

   b. **Blob 컨테이너** 를 선택하고 **SAS(공유 액세스 서명 URL)** 를 선택한 후 **다음** 을 선택합니다.

   다. **Blob 컨테이너 SAS URL** 텍스트 상자에 아래의 공개 미리 보기 Blob 컨테이너에 대한 SAS URI를 입력하고, **다음** 을 선택한 다음, **연결** 을 선택합니다.

      `https://ssisazurefileshare.blob.core.windows.net/publicpreview?sp=rl&st=2020-03-25T04:00:00Z&se=2025-03-25T04:00:00Z&sv=2019-02-02&sr=c&sig=WAD3DATezJjhBCO3ezrQ7TUZ8syEUxZZtGIhhP6Pt4I%3D`

   d. 왼쪽 창에서 연결된 **publicpreview** Blob 컨테이너를 선택한 다음, *CustomSetupScript* 폴더를 두 번 클릭합니다. 이 폴더에는 다음 항목이 있습니다.

      * *Sample* 폴더 - 기본 작업을 Azure-SSIS IR의 각 노드에 설치하는 사용자 지정 설치가 포함되어 있습니다. 이 작업은 몇 초 동안 대기하면서 아무 것도 수행하지 않습니다. 또한 폴더에는 *gacutil* 폴더도 포함되어 있습니다. 이 폴더의 전체 콘텐츠(*gacutil.exe*, *gacutil.exe.config* 및 *1033\gacutlrc.dll*)는 있는 그대로 Blob 컨테이너에 복사할 수 있습니다.

      * *UserScenarios* 폴더 - 실제 사용자 시나리오의 몇 가지 사용자 지정 설치 샘플이 포함되어 있습니다. Azure-SSIS IR에 여러 샘플을 설치하려는 경우, 사용자 지정 설치 스크립트(*main.cmd*) 파일을 단일 파일로 결합하고 모든 관련 파일을 사용하여 Blob 컨테이너에 업로드할 수 있습니다.

        :::image type="content" source="media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image11.png" alt-text="공개 미리 보기 Blob 컨테이너의 콘텐츠":::

   e. *UserScenarios* 폴더를 두 번 클릭하여 다음 항목을 찾습니다.

      * *.NET FRAMEWORK 3.5* 폴더 - 이전 버전의 .NET Framework를 Azure-SSIS IR의 각 노드에 설치하는 사용자 지정 설치 스크립트(*main.cmd*)가 포함되어 있습니다. 일부 사용자 지정 구성 요소에 이 버전이 필요할 수 있습니다.

      * *BCP* 폴더 - SQL Server 명령줄 유틸리티(*MsSqlCmdLnUtils.msi*)를 Azure-SSIS IR의 각 노드에 설치하는 사용자 지정 설치 스크립트(*main.cmd*)가 포함되어 있습니다. *bcp*(대량 복사 프로그램)은 그런 유틸리티 중 하나입니다.

      * *DNS SUFFIX* 폴더 - 정규화되지 않은 단일 레이블 도메인 이름에 자체 DNS 접미사(예: *test.com*)를 추가하고 Azure-SSIS IR의 DNS 쿼리에서 사용하기 전에 정규화된 도메인 이름(FQDN)으로 전환하는 사용자 지정 설치 스크립트(*main.cmd*)가 포함되어 있습니다.

      * *EXCEL* 폴더 - C# 어셈블리 및 라이브러리를 Azure-SSIS IR의 각 노드에 설치하는 사용자 지정 설치 스크립트(*main.cmd*)가 포함되어 있습니다. Script Tasks에서 이를 사용하여 Excel 파일을 동적으로 읽고 쓸 수 있습니다. 
      
        먼저 [*ExcelDataReader.dll*](https://www.nuget.org/packages/ExcelDataReader/) 및 [*DocumentFormat.OpenXml.dll*](https://www.nuget.org/packages/DocumentFormat.OpenXml/)을 다운로드한 다음, *main.cmd* 와 함께 모두 Blob 컨테이너에 업로드합니다. 또는, 표준 Excel 커넥터(연결 관리자, 원본, 대상)만 사용하려는 경우 해당 커넥터를 포함하는 재배포 가능 액세스가 이미 Azure-SSIS IR에 미리 설치되어 있으므로 사용자 지정 설치가 필요 없습니다.
      
      * *MYSQL ODBC* 폴더 - MySQL ODBC 드라이버를 Azure-SSIS IR의 각 노드에 설치하는 사용자 지정 설치 스크립트(*main.cmd*)가 포함되어 있습니다. 이 설치를 통해 ODBC 커넥터(연결 관리자, 원본, 대상)를 사용하여 MySQL 서버에 연결할 수 있습니다. 
     
        먼저 [최신 64비트 및 32비트 버전의 MySQL ODBC 드라이버 설치 프로그램(예: *mysql-connector-odbc-8.0.13-winx64.msi* 및 *mysql-connector-odbc-8.0.13-win32.msi*)을 다운로드](https://dev.mysql.com/downloads/connector/odbc/)한 다음, *main.cmd* 와 함께 모두 Blob 컨테이너에 업로드합니다.
        
        연결에 DSN(데이터 원본 이름)을 사용하는 경우 설치 스크립트에 DSN 구성이 필요합니다. 예: C:\Windows\SysWOW64\odbcconf.exe /A {CONFIGSYSDSN "MySQL ODBC 8.0 Unicode Driver" "DSN=\<dsnname\>|PORT=3306|SERVER=\<servername\>"}

      * *ORACLE ENTERPRISE* 폴더 - Oracle 커넥터 및 OCI 드라이버를 Azure-SSIS IR Enterprise Edition의 각 노드에 설치하는 사용자 지정 설치 스크립트(*main.cmd*) 및 자동 설치 구성 파일(*client.rsp*)이 포함되어 있습니다. 이 설치를 통해 Oracle 연결 관리자, 원본, 대상을 사용하여 Oracle 서버에 연결할 수 있습니다. 
      
        먼저 [Microsoft 다운로드 센터](https://www.microsoft.com/en-us/download/details.aspx?id=55179)에서 Oracle용 Microsoft Connectors v5.0(*AttunitySSISOraAdaptersSetup.msi*, *AttunitySSISOraAdaptersSetup64.msi*)을 다운로드하고, [Oracle](https://www.oracle.com/database/technologies/oracle19c-windows-downloads.html)에서 최신 Oracle 클라이언트(예: *winx64_12102_client.zip*)를 다운로드합니다. 그런 다음 *main.cmd* 및 *client.rsp* 와 함께 모두 Blob 컨테이너에 업로드합니다. TNS를 사용하여 Oracle에 연결하는 경우, *tnsnames.ora* 도 다운로드하여 편집하고 Blob 컨테이너에 업로드해야 합니다. 그렇게 하면 설치 중에 Oracle 설치 폴더로 복사할 수 있습니다.

      * *ORACLE STANDARD ADO.NET* 폴더 - Oracle ODP.NET 드라이버를 Azure-SSIS IR의 각 노드에 설치하는 사용자 지정 설치 스크립트(*main.cmd*)가 포함되어 있습니다. 이 설치를 통해 ADO.NET 연결 관리자, 원본, 대상을 사용하여 Oracle 서버에 연결할 수 있습니다. 
      
        먼저 [최신 Oracle ODP.NET 드라이버(예: *ODP.NET_Managed_ODAC122cR1.zip*)를 다운로드](https://www.oracle.com/technetwork/database/windows/downloads/index-090165.html)한 다음, *main.cmd* 와 함께 모두 Blob 컨테이너에 업로드합니다.
       
      * *ORACLE STANDARD ODBC* 폴더 - Oracle ODBC 드라이버를 Azure-SSIS IR의 각 노드에 설치하는 사용자 지정 설치 스크립트(*main.cmd*)가 포함되어 있습니다. 또한 이 스크립트는 DSN(데이터 원본 이름)을 구성합니다. 이 설치를 통해 ODBC 연결 관리자, 원본, 대상 또는 파워 쿼리 연결 관리자 및 ODBC 데이터 원본 형식을 포함하는 원본을 사용하여 Oracle 서버에 연결할 수 있습니다. 
      
        먼저 최신 Oracle Instant Client(기본 패키지 또는 기본 라이트 패키지) 및 ODBC 패키지를 다운로드한 다음, *main.cmd* 와 함께 모두 Blob 컨테이너에 업로드합니다.
        * [64비트 패키지 다운로드](https://www.oracle.com/technetwork/topics/winx64soft-089540.html)(기본 패키지: *instantclient-basic-windows.x64-18.3.0.0.0dbru.zip*, 기본 Lite 패키지: *instantclient-basiclite-windows.x64-18.3.0.0.0dbru.zip*, ODBC 패키지: *instantclient-odbc-windows.x64-18.3.0.0.0dbru.zip*) 
        * [32비트 패키지 다운로드](https://www.oracle.com/technetwork/topics/winsoft-085727.html)(기본 패키지: *instantclient-basic-nt-18.3.0.0.0dbru.zip*, 기본 Lite 패키지: *instantclient-basiclite-nt-18.3.0.0.0dbru.zip*, ODBC 패키지: *instantclient-odbc-nt-18.3.0.0.0dbru.zip*)

      * *ORACLE STANDARD OLEDB* 폴더 - Oracle OLEDB 드라이버를 Azure-SSIS IR의 각 노드에 설치하기 위한 사용자 지정 설치 스크립트(*main.cmd*)가 포함되어 있습니다. 이 설치를 통해 OLEDB 연결 관리자, 원본, 대상을 사용하여 Oracle 서버에 연결할 수 있습니다. 
     
        먼저 [최신 Oracle OLEDB 드라이버(예: *ODAC122010Xcopy_x64.zip*)를 다운로드](https://www.oracle.com/partners/campaign/index-090165.html)한 다음, *main.cmd* 와 함께 모두 Blob 컨테이너에 업로드합니다.

      * *POSTGRESQL ODBC* 폴더 - PostgreSQL ODBC 드라이버를 Azure-SSIS IR의 각 노드에 설치하는 사용자 지정 설치 스크립트(*main.cmd*)가 포함되어 있습니다. 이 설치를 통해 ODBC 연결 관리자, 원본, 대상을 사용하여 PostgreSQL 서버에 연결할 수 있습니다. 
     
        먼저 [최신 64비트 및 32비트 버전의 PostgreSQL ODBC 드라이버 설치 프로그램(예: *psqlodbc_x64.msi* 및 *psqlodbc_x86.msi*)을 다운로드](https://www.postgresql.org/ftp/odbc/versions/msi/)한 다음, *main.cmd* 와 함께 모두 Blob 컨테이너에 업로드합니다.

      * *SAP BW* 폴더 - SAP .NET 연결 어셈블리(*librfc32.dll*)를 Azure-SSIS IR Enterprise Edition의 각 노드에 설치하는 사용자 지정 설치 스크립트(*main.cmd*)가 포함되어 있습니다. 이 설치를 통해 SAP BW 연결 관리자, 원본 및 대상을 사용하여 SAP BW 서버에 연결할 수 있습니다. 
      
        먼저 SAP 설치 폴더의 64비트 또는 32비트 버전의 *librfc32.dll* 을 *main.cmd* 와 함께 모두 Blob 컨테이너에 업로드합니다. 그런 다음, 스크립트에서 설치 중에 SAP 어셈블리를 *%windir%\SysWow64* 또는 *%windir%\System32* 폴더에 복사합니다.

      * *STORAGE* 폴더 - Azure PowerShell을 Azure-SSIS IR의 각 노드에 설치하는 사용자 지정 설치 스크립트(*main.cmd*)가 포함되어 있습니다. 이 설치를 통해 [Azure PowerShell cmdlets/스크립트를 실행하여 Azure Storage를 관리](../storage/blobs/storage-quickstart-blobs-powershell.md)하는 SSIS 패키지를 배포 및 실행할 수 있습니다. 
      
        *main.cmd*, *AzurePowerShell.msi* 샘플(또는 최신 버전 사용) 및 *storage.ps1* 을 Blob 컨테이너에 복사합니다. *PowerShell.dtsx* 를 패키지에 대한 템플릿으로 사용합니다. 패키지 템플릿은 수정 가능한 PowerShell 스크립트(*storage.ps1*)를 다운로드하는 [Azure Blob 다운로드 작업](/sql/integration-services/control-flow/azure-blob-download-task)과 각 노드에서 스크립트를 실행하는 [프로세스 작업 실행](https://blogs.msdn.microsoft.com/ssis/2017/01/26/run-powershell-scripts-in-ssis/)을 결합합니다.

      * *TERADATA* 폴더 - 사용자 지정 설치 스크립트(*main.cmd*), 관련 파일(*install.cmd*) 및 설치 관리자 패키지( *.msi*)가 포함되어 있습니다. 이러한 파일은 Teradata 커넥터, TPT(Teradata Parallel Transporter) API 및 ODBC 드라이버를 Azure-SSIS IR Enterprise Edition의 각 노드에 설치합니다. 이 설치를 통해 Teradata 연결 관리자, 원본, 대상을 사용하여 Teradata 서버에 연결할 수 있습니다. 
      
        먼저 [Teradata 도구 및 유틸리티 15.x zip 파일(예: *TeradataToolsAndUtilitiesBase__windows_indep.15.10.22.00.zip*)을 다운로드](http://partnerintelligence.teradata.com)한 다음, 앞에서 언급한 *.cmd* 및 *.msi* 파일과 함께 모두 Blob 컨테이너에 업로드합니다.

      * *TLS 1.2* 폴더 - Azure-SSIS IR의 각 노드에서 강력한 암호화와 보다 안전한 네트워크 프로토콜(TLS 1.2)을 사용하기 위한 사용자 지정 설치 스크립트(*main.cmd*)가 포함되어 있습니다. 또한 이 스크립트는 이전 SSL/TLS 버전(SSL 3.0, TLS 1.0, TLS 1.1)을 동시에 사용하지 않도록 설정합니다.

      * *ZULU OPENJDK* 폴더 - Zulu OpenJDK를 Azure-SSIS IR의 각 노드에 설치하는 사용자 지정 설치 스크립트(*main.cmd*) 및 PowerShell 파일(*iinstall_openjdk.ps1*)이 포함되어 있습니다. 이 설치를 통해 Azure Data Lake Store 및 Flexible File 커넥터를 사용하여 ORC 및 Parquet 파일을 처리할 수 있습니다. 자세한 내용은 [Integration Services용 Azure 기능 팩](/sql/integration-services/azure-feature-pack-for-integration-services-ssis#dependency-on-java)을 참조하세요. 
      
        먼저 [최신 Zulu OpenJDK(예: *zulu8.33.0.1-jdk8.0.192-win_x64.zip*)를 다운로드](https://www.azul.com/downloads/zulu/zulu-windows/)한 다음, *main.cmd* 및 *install_openjdk.ps1* 과 함께 모두 Blob 컨테이너에 업로드합니다.

        :::image type="content" source="media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image12.png" alt-text="사용자 시나리오 폴더의 폴더":::

   f. 표준 사용자 지정 설치 샘플을 사용해 보려면, 선택한 폴더의 콘텐츠를 Blob 컨테이너에 복사합니다.

1. ADF UI에서 Azure-SSIS IR을 프로비저닝하거나 다시 구성할 때 **통합 런타임 설정** 창의 **고급 설정** 페이지에서 **추가 시스템 구성/구성 요소 설치를 사용하여 Azure-SSIS Integration Runtime 사용자 지정** 확인란을 선택합니다. 그런 다음 **사용자 지정 설치 컨테이너 SAS URI** 텍스트 상자에서 Blob 컨테이너의 SAS URI를 입력합니다.
   
1. Azure PowerShell을 사용하여 Azure-SSIS IR를 프로비전하거나 다시 구성하는 경우, 이미 시작/실행 중이면 중지하고 Blob 컨테이너의 SAS URI를 `SetupScriptContainerSasUri` 매개 변수 값으로 사용하여 `Set-AzDataFactoryV2IntegrationRuntime` cmdlet을 실행한 다음 Azure-SSIS IR을 시작합니다.

1. 표준 사용자 지정 설치가 완료되고 Azure-SSIS IR이 시작되면 Blob 컨테이너의 *main.cmd.log* 폴더에서 모든 사용자 지정 설치 로그를 확인할 수 있습니다. 여기에는 *main.cmd* 및 기타 실행 로그의 표준 출력이 포함됩니다.

## <a name="next-steps"></a>다음 단계

- [Azure-SSIS IR Enterprise Edition 설정](how-to-configure-azure-ssis-ir-enterprise-edition.md)
- [Azure-SSIS IR에 대해 유료 혹은 사용 허가된 있는 구성 요소 개발](how-to-develop-azure-ssis-ir-licensed-components.md)