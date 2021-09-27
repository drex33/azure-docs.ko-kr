---
title: SSDT를 사용하여 SSIS 패키지 실행
description: SSDT를 사용하여 Azure에서 SSIS 패키지를 실행하는 방법을 알아봅니다.
ms.service: data-factory
ms.subservice: integration-services
ms.topic: conceptual
ms.author: sawinark
author: swinarko
ms.custom: seo-lt-2019
ms.date: 09/06/2020
ms.openlocfilehash: 7ca8eeda244a75d9fd189425270b2b5525e694f3
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124787739"
---
# <a name="execute-ssis-packages-in-azure-from-ssdt"></a>SSDT를 사용하여 Azure에서 SSIS 패키지 실행

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

이 문서에서는 SSDT(SQL Server Data Tools)의 Azure 지원 SSIS(SQL Server Integration Services) 프로젝트 기능에 대해 설명합니다. SSIS 패키지의 클라우드 호환성을 평가하고 ADF(Azure Data Factory)의 Azure-SSIS IR(Integration Runtime)에서 SSIS 패키지를 실행할 수 있습니다. 이 기능을 사용하면 리프트 앤 시프트/Azure로 마이그레이션하기 전에 기존 패키지를 테스트하거나 Azure에서 실행할 새 패키지를 개발할 수 있습니다.

이 기능을 사용하여 새로 만든/기존 Azure-SSIS IR을 SSIS 프로젝트에 연결한 다음 그 위에서 패키지를 실행할 수 있습니다.  프로젝트 배포 모델에서 Azure SQL Database 서버 또는 관리되는 인스턴스가 호스트하는 SSIS 카탈로그(SSISDB)에 배포되는 패키지 실행을 지원합니다. 또한 패키지 배포 모델에서 Azure SQL Managed Instance가 호스트하는 파일 시스템, Azure Files 또는 SQL Server 데이터베이스(MSDB)에 배포되는 패키지 실행도 지원합니다. 

## <a name="prerequisites"></a>필수 조건

이 기능을 사용하려면 [여기](https://marketplace.visualstudio.com/items?itemName=SSIS.SqlServerIntegrationServicesProjects)에서 VS(Visual Studio)용 SSIS 프로젝트 확장을 포함하는 최신 SSDT를 다운로드하여 설치하세요. 또는 [여기](/sql/ssdt/download-sql-server-data-tools-ssdt#ssdt-for-vs-2017-standalone-installer)에서 최신 SSDT를 독립 실행형 설치 관리자로 다운로드하여 설치할 수도 있습니다.

## <a name="azure-enable-ssis-projects"></a>Azure를 지원하도록 SSIS 프로젝트 설정

### <a name="creating-new-azure-enabled-ssis-projects"></a>새 Azure 지원 SSIS 프로젝트 만들기

SSDT에서 **Integration Services 프로젝트(Azure 지원)** 템플릿을 사용하여 새 Azure 지원 SSIS 프로젝트를 만들 수 있습니다.

   :::image type="content" source="media/how-to-invoke-ssis-package-ssdt/ssdt-azure-enabled-new-project.png" alt-text="새 Azure 지원 SSIS 프로젝트":::

Azure 지원 프로젝트가 만들어지면 Azure Data Factory에서 SSIS에 연결하라는 메시지가 표시됩니다.

   :::image type="content" source="media/how-to-invoke-ssis-package-ssdt/ssdt-azure-enabled-integration-runtime-connect-prompt.png" alt-text="Azure-SSIS IR 연결 프롬프트":::

Azure-SSIS IR에 즉시 연결하려면 [Azure-SSIS IR에 연결](#connectssisir)에서 자세한 내용을 참조하세요. 나중에 SSDT의 솔루션 탐색기 창에서 프로젝트 노드를 마우스 오른쪽 단추로 클릭하여 표시되는 메뉴에서 연결할 수도 있습니다. 다음으로 **Azure Data Factory의 SSIS** 하위 메뉴에서 **Azure Data Factory의 SSIS에 연결** 항목을 선택합니다.

### <a name="azure-enabling-existing-ssis-projects"></a><a name="azureenableproject"></a> 기존 Azure 지원 SSIS 프로젝트

기존 SSIS 프로젝트의 경우 다음 단계에 따라 Azure를 지원하도록 설정할 수 있습니다.

1. SSDT의 솔루션 탐색기 창에서 프로젝트 노드를 마우스 오른쪽 단추로 클릭하여 메뉴를 표시합니다. 그런 다음 **Azure Data Factory의 SSIS** 하위 메뉴에서 **Azure 지원 프로젝트** 를 선택하여 **Azure 지원 프로젝트 마법사** 를 시작합니다.

   :::image type="content" source="media/how-to-invoke-ssis-package-ssdt/ssdt-azure-enabled-for-existing-project.png" alt-text="Azure를 지원하도록 기존 SSIS 프로젝트 설정":::

2. **Visual Studio 구성 선택** 페이지에서 기존 VS 구성을 선택하여 Azure에서 패키지 실행 설정을 적용합니다. 아직 만들지 않은 경우 새 구성을 만들 수도 있습니다. [새 VS 구성 만들기](/visualstudio/ide/how-to-create-and-edit-configurations)를 참조하세요. 로컬 및 클라우드 환경에서 패키지를 실행하기 위해 두 개 이상의 서로 다른 VS 구성을 사용하는 것이 좋습니다. 그러면 클라우드 구성에서 Azure를 지원하도록 프로젝트를 설정할 수 있습니다. 이러한 방식으로, 프로젝트 또는 패키지를 매개 변수화한 경우 다른 실행 환경(로컬 컴퓨터 또는 Azure)에 따라 런타임에 프로젝트 또는 패키지 매개 변수에 다른 값을 할당할 수 있습니다. 예를 들어 [패키지 실행 환경 전환](#switchenvironment)을 참조하세요.

   :::image type="content" source="media/how-to-invoke-ssis-package-ssdt/ssdt-azure-enabled-select-visual-studio-configurations.png" alt-text="Visual Studio 구성 선택":::

3. 기존 Azure 지원 SSIS 프로젝트를 사용하도록 설정하려면 대상 서버 버전을 Azure-SSIS IR에서 지원하는 최신 버전으로 설정해야 합니다. 현재 Azure-SSIS IR은 **SQL Server 2017** 을 기반으로 합니다. SQL Server 2017에서 지원되지 않는 추가 구성 요소가 패키지에 포함되어 있지 않은지 확인하세요. 또한 사용자 지정 설치를 통해 Azure-SSIS IR에 호환되는 추가 구성 요소가 모두 설치되어 있는지 확인하세요. [Azure-SSIS IR 사용자 지정](./how-to-configure-azure-ssis-ir-custom-setup.md)을 참조하세요. **다음** 단추를 선택하여 계속합니다.

   :::image type="content" source="media/how-to-invoke-ssis-package-ssdt/ssdt-azure-enabled-switch-target-server-version-step.png" alt-text="대상 서버 버전 전환":::

4. [Azure-SSIS IR에 연결](#connectssisir)을 참조하여 Azure-SSIS IR에 대한 프로젝트 연결을 완료합니다.

## <a name="connect-azure-enabled-projects-to-ssis-in-azure-data-factory"></a><a name="connectssisir"></a> Azure Data Factory의 SSIS에 Azure 지원 프로젝트 연결

Azure 지원 프로젝트를 ADF의 SSIS에 연결하면 패키지를 Azure Files에 업로드하고 Azure-SSIS IR에서 실행할 수 있습니다. 이렇게 하려면 다음 단계를 사용할 수 있습니다.

1. **ADF의 SSIS 소개** 페이지에서 내용을 검토하고 **다음** 단추를 선택하여 계속합니다.

   :::image type="content" source="media/how-to-invoke-ssis-package-ssdt/ssis-in-adf-connection-wizard.png" alt-text="ADF의 SSIS 소개":::

2. **ADF의 SSIS IR 선택** 페이지에서 기존 ADF 및 Azure-SSIS IR을 선택하여 패키지를 실행합니다. 패키지가 없는 경우 새로 만들 수도 있습니다.
   - 기존 Azure-SSIS IR을 선택하려면 먼저 관련 Azure 구독 및 ADF를 선택합니다.
   - Azure-SSIS IR이 없는 기존 ADF를 선택하는 경우에는 **SSIS IR 만들기** 단추를 선택하여 ADF 포털에서 새로 만듭니다. 만들어지면 이 페이지로 돌아와서 새 Azure-SSIS IR을 선택할 수 있습니다.
   - ADF가 없는 기존 Azure 구독을 선택하는 경우에는 **SSIS IR 만들기** 단추를 선택하여 **통합 런타임 만들기 마법사** 를 시작합니다. 마법사에서 사용자를 대신하여 자동으로 새 Azure 리소스 그룹, Data Factory 및 SSIS IR을 **YourPrefix-RG/DF/IR-YourCreationTime** 형식으로 명명하여 만들도록 지정된 위치 및 접두사를 입력할 수 있습니다. 만들어지면 이 페이지로 돌아와서 새 ADF와 Azure-SSIS IR을 선택할 수 있습니다.

   :::image type="content" source="media/how-to-invoke-ssis-package-ssdt/ssis-in-adf-connection-wizard2.png" alt-text="ADF에서 SSIS IR 선택":::

3. **Azure Storage 선택** 페이지에서 기존 Azure Storage 계정을 선택하여 Azure Files에 패키지를 업로드합니다. 계정이 없는 경우 새로 만들 수도 있습니다.
   - 기존 Azure Storage 계정을 선택하려면 먼저 관련 Azure 구독을 선택합니다.
   - Azure Storage 계정이 없는 Azure-SSIS IR과 동일한 Azure 구독을 선택하는 경우에는 **Azure Storage 만들기** 단추를 선택합니다. 사용자를 대신하여 Azure-SSIS IR 이름 접두사와 만든 날짜를 결합한 이름이 지정된 새 계정이 Azure-SSIS IR와 동일한 위치에 자동으로 만들어집니다. 만들어지면 이 페이지로 돌아와서 새 Azure Storage 계정을 선택할 수 있습니다.
   - Azure Storage 계정이 없는 다른 Azure 구독을 선택하는 경우에는 **Azure Storage 만들기** 단추를 선택하여 Azure Portal에 새 계정을 만듭니다. 만들어지면 이 페이지로 돌아와서 새 Azure Storage 계정을 선택할 수 있습니다.

   :::image type="content" source="media/how-to-invoke-ssis-package-ssdt/ssis-in-adf-connection-wizard3.png" alt-text="Azure Storage 선택":::

4. **연결** 단추를 선택하여 Azure-SSIS IR에 대한 프로젝트 연결을 완료합니다. SSDT의 솔루션 탐색기 창에서 **연결된 Azure 리소스** 노드 아래에 선택한 Azure-SSIS IR 및 Azure Storage 계정이 표시됩니다. 또한 Azure-SSIS IR의 상태가 정기적으로 새로 고쳐집니다. 해당 노드를 마우스 오른쪽 단추로 클릭하여 메뉴를 표시하고 **Start\Stop\Manage** 항목을 선택해 ADF 포털로 이동하여 Azure-SSIS IR을 관리할 수 있습니다.

## <a name="assess-ssis-projectpackages-for-executions-in-azure"></a>Azure에서 SSIS 프로젝트/패키지 실행 평가

### <a name="assessing-single-or-multiple-packages"></a>단일 또는 여러 패키지 평가

Azure에서 패키지를 실행하기 전에 평가를 통해 잠재적인 클라우드 호환성 문제를 파악할 수 있습니다. 여기에는 알고 있어야 하는 마이그레이션 블로커 및 추가 정보가 포함됩니다. 
-  단일 패키지를 한 번에 하나씩 평가하거나 프로젝트의 모든 패키지를 동시에 평가할 수 있습니다.

   :::image type="content" source="media/how-to-invoke-ssis-package-ssdt/ssdt-azure-assess-package.png" alt-text="패키지 평가":::

   :::image type="content" source="media/how-to-invoke-ssis-package-ssdt/ssdt-azure-assess-project.png" alt-text="프로젝트 평가":::

-  SSDT의 **평가 보고서** 창에서 드러난 모든 잠재적 클라우드 호환성 문제를 확인할 수 있습니다. 각 문제에 대한 자세한 설명 및 권장 사항이 제공됩니다. 이러한 문제를 완화해야 하는 사용자와 공유할 수 있는 CSV 파일로 평가 보고서를 내보낼 수도 있습니다. 

   :::image type="content" source="media/how-to-invoke-ssis-package-ssdt/ssdt-azure-assess-project-result.png" alt-text="평가 보고서":::

### <a name="suppressing-assessment-rules"></a>평가 규칙 제거

일부 잠재적 클라우드 호환성 문제가 해당되지 않거나 패키지에서 적절히 완화된 것이 확실한 경우 해당 문제를 노출하는 관련 평가 규칙을 제거할 수 있습니다. 이렇게 하면 이후의 평가 보고서에서 노이즈가 줄어듭니다.
-  SSDT의 **평가 보고서** 창에서 **평가 규칙 제거 구성** 링크를 선택하여 **평가 규칙 제거 설정** 창을 엽니다. 여기에서 제거할 평가 규칙을 선택할 수 있습니다.

   :::image type="content" source="media/how-to-invoke-ssis-package-ssdt/ssdt-azure-assessment-rule-suppression-settings.png" alt-text="평가 규칙 제거 설정":::

-  또는 SSDT의 솔루션 탐색기 창에서 프로젝트 노드를 마우스 오른쪽 단추로 클릭하여 메뉴를 표시합니다. **Azure Data Factory의 SSIS** 하위 메뉴에서 **Azure 지원 설정** 항목을 선택하여 프로젝트 속성 페이지가 포함된 창을 엽니다. **Azure 지원 설정** 섹션에서 **제거된 평가 규칙 ID** 속성을 선택합니다. 마지막으로, 줄임표( **...** ) 단추를 선택하여 **평가 규칙 제거 설정** 창을 엽니다. 여기에서 제거할 평가 규칙을 선택할 수 있습니다.

   :::image type="content" source="media/how-to-invoke-ssis-package-ssdt/ssdt-azure-enabled-azure-enabled-setting.png" alt-text="Azure 지원 설정":::

   :::image type="content" source="media/how-to-invoke-ssis-package-ssdt/ssdt-azure-assessment-rule-suppression-settings-via-azure-enabled-settings.png" alt-text="Azure 지원 설정을 통한 평가 규칙 제거 설정":::

## <a name="execute-ssis-packages-in-azure"></a>Azure에서 SSIS 패키지 실행

### <a name="configuring-azure-enabled-settings"></a><a name="azureenabledsettings"></a> Azure 지원 설정 구성

Azure에서 패키지를 실행하기 전에 패키지에 대한 Azure 지원 설정을 구성할 수 있습니다. 예를 들어 다음 단계를 수행하여 Azure-SSIS IR에서 Windows 인증을 사용하도록 설정하여 온-프레미스/클라우드 데이터 저장소에 액세스할 수 있습니다.

1. SSDT의 솔루션 탐색기 창에서 프로젝트 노드를 마우스 오른쪽 단추로 클릭하여 메뉴를 표시합니다. 다음으로 **Azure Data Factory의 SSIS** 하위 메뉴에서 **Azure 지원 설정** 항목을 선택하여 프로젝트 속성 페이지가 포함된 창을 엽니다.

   :::image type="content" source="media/how-to-invoke-ssis-package-ssdt/ssdt-azure-enabled-azure-enabled-setting.png" alt-text="Azure 지원 설정":::

2. **Azure 지원 설정** 섹션에서 **Windows 인증 사용** 속성을 선택하고 드롭다운 메뉴에서 **True** 를 선택합니다. 다음으로 **Windows 인증 자격 증명** 속성을 선택하고 줄임표( **...** ) 단추를 선택하여 **Windows 인증 자격 증명** 창을 엽니다.

   :::image type="content" source="media/how-to-invoke-ssis-package-ssdt/ssdt-azure-enabled-windows-authentication-open.png" alt-text="Windows 인증을 사용하도록 설정":::

3. Windows 인증 자격 증명을 입력합니다. 예를 들어 Azure Files에 액세스하려면 **도메인**, **사용자 이름** 및 **암호** 에 `Azure`, `YourStorageAccountName`, `YourStorageAccountKey`을 각각 입력할 수 있습니다.

   :::image type="content" source="media/how-to-invoke-ssis-package-ssdt/ssdt-azure-enabled-windows-authentication-credential.png" alt-text="Windows 인증 자격 증명":::

### <a name="starting-package-executions"></a>패키지 실행 시작

Azure 지원 프로젝트를 ADF의 SSIS에 연결하고, 클라우드 호환성을 평가하고, 잠재적인 문제를 완화하고 나면 Azure-SSIS IR에서 패키지를 실행/테스트할 수 있습니다.
-  SSDT 도구 모음에서 **시작** 단추를 선택하여 드롭다운 메뉴를 표시합니다. 다음으로 **Azure에서 실행** 항목을 선택합니다.

   :::image type="content" source="media/how-to-invoke-ssis-package-ssdt/ssdt-azure-enabled-execute-package.png" alt-text="Azure에서 실행":::

-  또는, SSDT의 솔루션 탐색기 창에서 프로젝트 노드를 마우스 오른쪽 단추로 클릭하여 메뉴를 표시합니다. 다음으로 **Azure에서 패키지 실행** 항목을 선택합니다.

   :::image type="content" source="media/how-to-invoke-ssis-package-ssdt/ssdt-azure-enabled-execute-package2.png" alt-text="Azure에서 패키지 실행":::

> [!NOTE]
> Azure에서 패키지를 실행하려면 실행 중인 Azure-SSIS IR이 있어야 합니다. 따라서 Azure-SSIS IR이 중지된 경우에는 Azure-SSIS IR을 시작하는 대화 상자 창이 표시됩니다. 사용자 지정 설치 시간을 제외하고 이 프로세스는 5분 이내에 완료되지만 Azure-SSIS IR을 가상 네트워크에 조인하는 데 약 20~30 분이 걸릴 수 있습니다. Azure에서 패키지를 실행한 후에는 SSDT의 솔루션 탐색기 창에서 해당 노드를 마우스 오른쪽 단추로 클릭하여 메뉴를 표시하고 **Start\Stop\Manage** 항목을 선택해 ADF 포털로 이동하여 비용 관리를 위해 Azure-SSIS IR을 중지할 수 있습니다.

### <a name="using-execute-package-task"></a>패키지 실행 태스크 사용

패키지에 로컬 파일 시스템에 저장된 자식 패키지를 참조하는 패키지 실행 태스크가 포함되어 있는 경우 다음과 같은 추가 단계를 수행합니다.

1. 프로젝트에 연결된 동일한 Azure Storage 계정으로 Azure Files에 자식 패키지를 업로드하고 새 UNC(범용 명명 규칙) 경로를 가져옵니다. 예: `\\YourStorageAccountName.file.core.windows.net\ssdtexecution\YourChildPackage1.dtsx`

2. 패키지 실행 태스크의 파일 연결 관리자에서 자식 패키지의 파일 경로를 새 UNC 경로로 바꿉니다.
   - SSDT를 실행하는 로컬 컴퓨터에서 새 UNC 경로에 액세스할 수 없는 경우 파일 연결 관리자의 속성 패널에 해당 경로를 입력할 수 있습니다.
   - 또는 파일 경로 변수를 사용하여 런타임에 올바른 값을 할당할 수 있습니다.

패키지에 동일한 프로젝트의 자식 패키지를 참조하는 패키지 실행 태스크가 포함되어 있으면 추가 단계가 필요하지 않습니다.

### <a name="switching-package-protection-level"></a>패키지 보호 수준 변경

Azure에서 SSIS 패키지를 실행하는 경우 **EncryptSensitiveWithUserKey**/**EncryptAllWithUserKey** 보호 수준을 지원하지 않습니다. 따라서 패키지가 해당 보호 수준을 사용하도록 구성된 경우에는 각각 **EncryptSensitiveWithPassword**/**EncryptAllWithPassword** 보호 수준을 사용하도록 임시로 전환합니다. 또한 Azure-SSIS IR에서 실행을 위해 패키지를 Azure Files로 업로드하는 경우에도 임의의 암호화 암호가 생성됩니다.

> [!NOTE]
> 패키지에 **EncryptSensitiveWithUserKey**/**EncryptAllWithUserKey** 보호 수준을 사용하도록 구성된 자식 패키지를 참조하는 패키지 실행 태스크가 포함되어 있는 경우 패키지를 실행하기 전에 각각 **EncryptSensitiveWithPassword**/**EncryptAllWithPassword** 보호 수준을 사용하도록 해당 자식 패키지를 수동으로 다시 구성해야 합니다.

패키지가 이미 **EncryptSensitiveWithPassword**/**EncryptAllWithPassword** 보호 수준을 사용하도록 구성된 경우에는 변경 없이 그대로 유지됩니다. 그러나 Azure-SSIS IR에서 실행을 위해 패키지를 Azure Files로 업로드하는 경우에는 임의의 암호화 암호가 생성됩니다.

### <a name="switching-package-execution-environments"></a><a name="switchenvironment"> 패키지 실행 환경 전환</a>

프로젝트 배포 모델에서 프로젝트/패키지를 매개 변수화하는 경우 여러 VS 구성을 만들어 패키지 실행 환경을 전환할 수 있습니다. 이러한 방식으로 런타임에 프로젝트/패키지 매개 변수에 환경 관련 값을 할당할 수 있습니다. 로컬 및 클라우드 환경에서 패키지를 실행하기 위해 두 개 이상의 서로 다른 VS 구성을 사용하는 것이 좋습니다. 그러면 클라우드 구성에서 Azure를 지원하도록 프로젝트를 설정할 수 있습니다. 패키지 실행 환경을 로컬 컴퓨터와 Azure 간에 전환하는 단계별 예제는 다음과 같습니다.

1. 패키지에 파일의 특성을 설정하는 파일 시스템 태스크가 포함되어 있다고 가정해 보겠습니다. 이 태스크를 로컬 컴퓨터에서 실행하는 경우 로컬 파일 시스템에 저장된 파일의 특성을 설정합니다. Azure-SSIS IR에서 실행하면 Azure Files에 저장된 파일의 특성을 설정합니다. 먼저 문자열 형식의 패키지 매개 변수를 만들고 파일 이름을 **FilePath** 로 지정하여 대상 파일 경로의 값을 저장합니다.

   :::image type="content" source="media/how-to-invoke-ssis-package-ssdt/ssdt-azure-enabled-example-define-parameters.png" alt-text="패키지 매개 변수 만들기":::

2. 다음으로, **파일 시스템 태스크 편집기** 창의 **일반** 페이지에서 **원본 연결** 섹션의 **SourceVariable** 속성을 **FilePath** 패키지 매개 변수로 매개 변수화합니다. 

   :::image type="content" source="media/how-to-invoke-ssis-package-ssdt/ssdt-azure-enabled-example-update-task-with-parameters.png" alt-text="원본 연결 매개 변수화":::

3. 기본적으로 **개발** 이라는 로컬 환경에 패키지 실행에 대한 기존 VS 구성이 있습니다. 아직 만들지 않은 경우 **Azure** 라는 클라우드 환경에서 패키지를 실행하기 위한 새 VS 구성을 만듭니다. [새 VS 구성 만들기](/visualstudio/ide/how-to-create-and-edit-configurations)를 참조하세요.

4. 패키지의 매개 변수를 보면서 **구성에 매개 변수 추가** 단추를 선택하여 패키지에 대한 **매개 변수 값 관리** 창을 엽니다. 다음으로 **개발** 및 **Azure** 구성에서 **FilePath** 패키지 매개 변수에 서로 다른 대상 파일 경로 값을 할당합니다.

   :::image type="content" source="media/how-to-invoke-ssis-package-ssdt/ssdt-azure-enabled-example-override-parameter.png" alt-text="매개 변수 값 할당":::

5. 아직 설정하지 않은 경우, 클라우드 구성에 대해 Azure를 지원하도록 프로젝트를 설정합니다. [Azure를 지원하도록 기존 SSIS 프로젝트 설정](#azureenableproject)을 참조하세요. 다음으로, 아직 구성하지 않은 경우, Azure 지원 설정을 구성하여 Azure-SSIS IR이 Azure Files에 액세스하는 데 Windows 인증을 사용하도록 설정합니다. [Azure 지원 설정 구성](#azureenabledsettings)을 참조하세요.

6. Azure에서 패키지를 실행합니다. **개발** 구성을 선택하여 패키지 실행 환경을 다시 로컬 컴퓨터로 전환할 수 있습니다.

   :::image type="content" source="media/how-to-invoke-ssis-package-ssdt/ssdt-azure-enabled-example-switch-configurations.png" alt-text="Visual Studio 구성으로 전환":::

### <a name="using-package-configuration-file"></a>패키지 구성 파일 사용

패키지 배포 모델에서 패키지 구성 파일을 사용하는 경우 런타임에 패키지 속성에 환경 관련 값을 할당할 수 있습니다. Azure-SSIS IR에서 실행할 수 있도록 패키지를 포함하는 파일이 자동으로 Azure Files에 업로드됩니다.

### <a name="checking-package-execution-logs"></a>패키지 실행 로그 확인

패키지 실행을 시작한 후 SSDT의 **진행률** 창에서 해당 로그를 서식 지정하고 표시합니다. 장기 실행 패키지의 경우 로그를 분 단위로 정기적으로 업데이트합니다. SSDT 도구 모음에서 **중지** 단추를 선택하여 패키지 실행을 즉시 취소할 수 있습니다. 또한 UNC 경로 `\\<YourStorageAccountName>.file.core.windows.net\ssdtexecution\<YourProjectName-FirstConnectTime>\<YourPackageName-tmp-ExecutionTime>\logs`에서 로그 원시 데이터를 일시적으로 확인할 수 있습니다(로그는 1일 후에 정리됨).

## <a name="current-limitations"></a>현재 제한 사항

-  Azure 지원 SSDT는 상용/글로벌 클라우드 지역만 지원하며, 현재는 정부/국가 클라우드 지역은 지원하지 않습니다.

## <a name="next-steps"></a>다음 단계

Azure에서 SSDT를 사용한 패키지 실행이 만족스러우면 ADF 파이프라인에서 SSIS 패키지 실행 작업으로 패키지를 배포하고 실행할 수 있습니다. [ADF 파이프라인에서 SSIS 패키지 실행 작업으로 SSIS 패키지 실행](./how-to-invoke-ssis-package-ssis-activity.md)을 참조하세요.