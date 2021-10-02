---
title: 포털에서 SSIS 패키지 실행 작업 사용
description: 이 문서에서는 ssis 패키지 실행 작업 및 Azure Data Factory Studio 포털을 사용 하 여 Azure Data Factory 파이프라인에서 ssis (SQL Server Integration Services) 패키지를 실행 하는 방법을 설명 합니다.
ms.service: data-factory
ms.subservice: integration-services
ms.devlang: powershell
ms.topic: conceptual
ms.author: sawinark
author: swinarko
ms.custom: seo-lt-2019, devx-track-azurepowershell
ms.date: 10/01/2021
ms.openlocfilehash: 1e8c1777c8f2a95bd63ab9f03abc9c65fe0b1b6f
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/01/2021
ms.locfileid: "129357968"
---
# <a name="run-an-ssis-package-with-the-execute-ssis-package-activity-in-the-azure-data-factory-studio-portal"></a>Azure Data Factory Studio 포털에서 SSIS 패키지 실행 작업을 사용 하 여 SSIS 패키지를 실행 합니다.

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

이 문서에서는 ssis 패키지 실행 작업 및 Azure Data Factory Studio portal UI를 사용 하 여 Azure Data Factory 파이프라인에서 ssis (SQL Server Integration Services) 패키지를 실행 하는 방법을 설명 합니다. 

## <a name="prerequisites"></a>필수 조건

Azure-SSIS IR(Integration Runtime)이 없는 경우 [자습서: Azure-SSIS IR 프로비저닝](./tutorial-deploy-ssis-packages-azure.md)의 단계별 지침을 따라 만듭니다.

## <a name="create-a-pipeline-with-an-execute-ssis-package-activity"></a>SSIS 패키지 실행 작업으로 파이프라인 만들기
이 단계에서는 Data Factory UI 또는 앱을 사용하여 파이프라인을 만듭니다. 파이프라인에 SSIS 패키지 실행 작업을 추가하고 SSIS 패키지를 실행하도록 구성합니다. 

1. Azure Portal Data Factory 개요 또는 홈페이지에서 **작성자 및 모니터** 타일을 선택하여 별도의 탭에서 Data Factory UI 또는 앱을 시작합니다. 

   :::image type="content" source="./media/how-to-invoke-ssis-package-stored-procedure-activity/data-factory-home-page.png" alt-text="Data Factory 홈페이지":::

   홈페이지에서 **오케스트레이션** 을 선택합니다. 

   :::image type="content" source="./media/doc-common-process/get-started-page.png" alt-text="ADF 홈페이지를 보여 주는 스크린샷":::

1. **작업** 도구 상자에서 **일반** 을 확장합니다. **SSIS 패키지 실행** 작업을 파이프라인 디자이너 화면으로 끕니다. 

   :::image type="content" source="media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-designer.png" alt-text="SSIS 패키지 실행 작업을 디자이너 화면으로 끌기"::: 

   SSIS 패키지 실행 작업 개체를 선택하여 **일반**, **설정**, **SSIS 매개 변수**, **연결 관리자**, **속성 재정의** 탭을 구성합니다.

### <a name="general-tab"></a>일반 탭

SSIS 패키지 실행 작업의 **일반** 탭에서 다음 단계를 완료합니다.

:::image type="content" source="media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-general.png" alt-text="일반 탭의 속성 설정":::

   1. **이름** 에 SSIS 패키지 실행 작업의 이름을 입력합니다.

   1. **설명** 에 SSIS 패키지 실행 작업의 설명을 입력합니다.

   1. **시간 제한** 에 SSIS 패키지 실행 작업을 실행할 수 있는 최대 시간을 입력합니다. 기본값은 7일이며, 형식은 D.HH:MM:SS입니다.

   1. **다시 시도** 에 SSIS 패키지 실행 작업에 대한 최대 다시 시도 횟수를 입력합니다.

   1. **다시 시도 간격** 에 SSIS 패키지 실행 작업에 대한 다시 시도 사이의 시간(초)을 입력합니다. 기본값은 30초입니다.

   1. **보안 출력** 확인란을 선택하여 SSIS 패키지 실행 작업의 출력을 로깅에서 제외할지 여부를 선택합니다.

   1. **보안 입력** 확인란을 선택하여 SSIS 패키지 실행 작업의 입력을 로깅에서 제외할지 여부를 선택합니다.

### <a name="settings-tab"></a>설정 탭

SSIS 패키지 실행 작업의 **설정** 탭에서 다음 단계를 완료합니다.

:::image type="content" source="media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-settings.png" alt-text="설정 탭의 속성 설정 - 자동":::

   1. **Azure-SSIS IR** 에 대해 지정된 Azure-SSIS IR을 선택하여 SSIS 패키지 실행 작업을 실행합니다.

   1. **설명** 에 SSIS 패키지 실행 작업의 설명을 입력합니다.

   1. **Windows 인증** 확인란을 선택하여 SQL 서버/파일 공유 온-프레미스 또는 Azure Files와 같은 데이터 저장소에 액세스하는 데 Windows 인증을 사용할지 여부를 선택합니다.
   
      이 확인란을 선택하는 경우 **도메인**, **사용자 이름** 및 **암호** 상자에 패키지 실행 자격 증명의 값을 입력합니다. 예를 들어 Azure Files에 액세스할 때 도메인은 `Azure`, 사용자 이름은 `<storage account name>`, 암호는 `<storage account key>`입니다.

      또는 Azure Key Vault에 저장된 비밀을 해당 값으로 사용할 수 있습니다. 이렇게 하려면 옆에 있는 **AZURE KEY VAULT** 확인란을 선택합니다. 기존 키 자격 증명 모음 연결된 서비스를 선택 또는 편집하거나 새로 만듭니다. 그런 다음 값에 대한 비밀 이름 및 버전을 선택합니다. 키 자격 증명 모음 연결된 서비스를 만들거나 편집할 때 기존 키 자격 증명 모음을 선택 또는 편집하거나 새 키 자격 증명 모음을 만들 수 있습니다. 아직 수행하지 않은 경우 키 자격 증명 모음에 대한 Data Factory 관리 ID 액세스 권한을 부여해야 합니다. `<key vault linked service name>/<secret name>/<secret version>` 형식으로 비밀을 직접 입력할 수도 있습니다.
      
   1. **32비트 런타임** 확인란을 선택하여 패키지에서 32비트 런타임을 실행해야 하는지 여부를 선택합니다.

   1. **패키지 위치** 에서 **SSISDB**, **파일 시스템(패키지)** , **파일 시스템(프로젝트)** , **포함된 패키지** 또는 **패키지 저장소** 를 선택합니다. 

#### <a name="package-location-ssisdb"></a>패키지 위치: SSISDB

Azure SQL Database 서버/Managed Instance에서 호스팅된 SSIS 카탈로그(SSISDB)를 사용하여 Azure-SSIS IR이 프로비전된 경우 패키지 위치가 자동으로 **SSISDB** 로 선택되며, 직접 선택할 수도 있습니다. 선택된 경우 다음 단계를 완료합니다.

   1. Azure-SSIS IR이 실행 중이고 **수동 입력** 확인란이 선택 취소된 경우 SSISDB에서 기존 폴더, 프로젝트, 패키지, 환경을 찾아 선택할 수 있습니다. **새로 고침** 을 선택하여 새로 추가한 폴더, 프로젝트, 패키지, 환경을 찾아서 선택할 수 있도록 SSISDB에서 페치합니다. 패키지 실행을 위한 환경을 탐색하고 선택하려면 해당 환경을 SSISDB 아래의 동일한 폴더에서 참조로 추가하도록 프로젝트를 미리 구성해야 합니다. 자세한 내용은 [SSIS 환경 만들기 및 매핑](/sql/integration-services/packages/deploy-integration-services-ssis-projects-and-packages)을 참조하세요.

   1. **로깅 수준** 에서 패키지 실행에 대해 사전 정의된 로깅 범위를 선택합니다. 사용자 지정된 로깅 이름을 대신 입력하려면 **사용자 지정** 확인란을 선택합니다. 

   1. Azure-SSIS IR이 실행되지 않거나 **수동 입력** 확인란이 선택된 경우 `<folder name>/<project name>/<package name>.dtsx` 및 `<folder name>/<environment name>` 형식으로 SSISDB의 패키지 및 환경 경로를 직접 입력할 수 있습니다.

      :::image type="content" source="media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-settings2.png" alt-text="설정 탭의 속성 설정 - 수동":::

#### <a name="package-location-file-system-package"></a>패키지 위치: 파일 시스템(패키지)

SSISDB 없이 Azure-SSIS IR이 프로비전된 경우 패키지 위치가 자동으로 **파일 시스템(패키지)** 으로 선택되며, 직접 선택할 수도 있습니다. 선택된 경우 다음 단계를 완료합니다.

:::image type="content" source="media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-settings3.png" alt-text="설정 탭 - 파일 시스템(패키지)의 속성 설정":::
   
   1. **패키지 경로** 상자의 패키지 파일(`.dtsx` 포함)에 UNC(범용 명명 규칙)를 제공하여 패키지를 지정합니다. **파일 저장소 찾아보기** 를 선택하거나 경로를 수동으로 입력하여 패키지를 찾아보고 선택할 수 있습니다. 예를 들어 Azure Files에 패키지를 저장하는 경우 해당 경로는 `\\<storage account name>.file.core.windows.net\<file share name>\<package name>.dtsx`입니다. 
   
   1. 패키지를 별도의 파일에 구성하는 경우 **구성 경로** 상자에서 구성 파일(`.dtsConfig` 포함)에 UNC 경로를 제공해야 합니다. **파일 저장소 찾아보기** 를 선택하거나 경로를 수동으로 입력하여 구성을 찾아보고 선택할 수 있습니다. 예를 들어 Azure Files에 구성을 저장하는 경우 해당 경로는 `\\<storage account name>.file.core.windows.net\<file share name>\<configuration name>.dtsConfig`입니다.

   1. 패키지 및 구성 파일에 액세스하는 데 사용할 자격 증명을 지정합니다. 이전에 패키지 실행 자격 증명(**Windows 인증** 용)을 입력한 경우 **패키지 실행 자격 증명과 동일함** 확인란을 선택하여 다시 사용할 수 있습니다. 그렇지 않은 경우 **도메인**, **사용자 이름** 및 **암호** 상자에 패키지 액세스 자격 증명 값을 입력합니다. 예를 들어 Azure Files에 패키지 및 구성을 저장하는 경우 도메인은 `Azure`, 사용자 이름은 `<storage account name>`, 암호는 `<storage account key>`입니다. 

      또는 Azure Key Vault에 저장된 비밀을 해당 값으로 사용할 수 있습니다. 이렇게 하려면 옆에 있는 **AZURE KEY VAULT** 확인란을 선택합니다. 기존 키 자격 증명 모음 연결된 서비스를 선택 또는 편집하거나 새로 만듭니다. 그런 다음 값에 대한 비밀 이름 및 버전을 선택합니다. 키 자격 증명 모음 연결된 서비스를 만들거나 편집할 때 기존 키 자격 증명 모음을 선택 또는 편집하거나 새 키 자격 증명 모음을 만들 수 있습니다. 아직 수행하지 않은 경우 키 자격 증명 모음에 대한 Data Factory 관리 ID 액세스 권한을 부여해야 합니다. `<key vault linked service name>/<secret name>/<secret version>` 형식으로 비밀을 직접 입력할 수도 있습니다. 

      이러한 자격 증명은 고유 경로 및 패키지에서 지정된 다른 구성에 의해 참조되는 패키지 실행 태스크의 자식 패키지 액세스에 사용됩니다. 

   1. SQL Server Data Tools(SSDT)를 통해 패키지를 만들 때 **EncryptAllWithPassword** 또는 **EncryptSensitiveWithPassword** 보호 수준을 사용한 경우 **암호화 암호** 상자에 암호의 값을 입력합니다. 또는 Azure Key Vault에 저장된 비밀을 해당 값으로 사용할 수 있습니다(위의 내용 참조).
      
      **EncryptSensitiveWithUserKey** 보호 수준을 사용한 경우 구성 파일이나 **SSIS 매개 변수**, **연결 관리자** 또는 **속성 재정의** 탭에 중요한 값을 다시 입력합니다(아래 내용 참조).
      
      **EncryptAllWithUserKey** 보호 수준을 사용하는 경우 지원되지 않습니다. SSDT 또는 `dtutil` 명령줄 유틸리티를 통해 다른 보호 수준을 사용하려면 패키지를 다시 구성해야  합니다. 

   1. **로깅 수준** 에서 패키지 실행에 대해 사전 정의된 로깅 범위를 선택합니다. 사용자 지정된 로깅 이름을 대신 입력하려면 **사용자 지정** 확인란을 선택합니다. 
   
   1. 패키지에 지정할 수 있는 표준 로그 공급자를 사용하여 패키지 실행을 로그하려면 **로깅 경로** 상자에 UNC 경로를 제공하여 로그 폴더를 지정합니다. **파일 저장소 찾아보기** 를 선택하거나 경로를 수동으로 입력하여 로그 폴더를 찾아보고 선택할 수 있습니다. 예를 들어 Azure Files에 로그를 저장하는 경우 로깅 경로는 `\\<storage account name>.file.core.windows.net\<file share name>\<log folder name>`입니다. 각 개별 패키지 실행에 대해 이 경로에 하위 폴더가 만들어지고, SSIS 패키지 실행 작업 실행 ID에 따라 이름이 지어지며, 로그 파일이 5분마다 생성됩니다. 
   
   1. 로그 폴더에 액세스하기 위한 자격 증명을 지정합니다. 이전에 패키지 액세스 자격 증명에 대한 값을 입력한 경우(위의 내용 참조) **패키지 액세스 자격 증명과 동일함** 확인란을 선택하여 다시 사용할 수 있습니다. 그렇지 않은 경우 **도메인**, **사용자 이름** 및 **암호** 상자에 로깅 액세스 자격 증명 값을 입력합니다. 예를 들어 Azure Files에 로그를 저장하는 경우 도메인은 `Azure`, 사용자 이름은 `<storage account name>`, 암호는 `<storage account key>`입니다. 또는 Azure Key Vault에 저장된 암호를 해당 값으로 사용할 수 있습니다(위의 내용 참조).
   
앞에서 언급한 모든 UNC 경로에 대해 정규화된 파일 이름은 260자 미만이어야 합니다. 디렉터리 이름은 248자 미만이어야 합니다.

#### <a name="package-location-file-system-project"></a>패키지 위치: 파일 시스템(프로젝트)

패키지 위치로 **파일 시스템(프로젝트)** 을 선택한 경우 다음 단계를 완료합니다.

:::image type="content" source="media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-settings4.png" alt-text="설정 탭 - 파일 시스템(프로젝트)의 속성 설정":::

   1. **프로젝트 경로** 상자의 프로젝트 파일(`.ispac` 포함)과 **패키지 이름** 의 프로젝트에 있는 패키지 파일(`.dtsx` 포함)에 UNC 경로를 제공하여 실행할 패키지를 지정합니다. **파일 저장소 찾아보기** 를 선택하거나 경로를 수동으로 입력하여 프로젝트를 찾아보고 선택할 수 있습니다. 예를 들어 Azure Files에 프로젝트를 저장하는 경우 해당 경로는 `\\<storage account name>.file.core.windows.net\<file share name>\<project name>.ispac`입니다.

   1. 프로젝트 및 패키지 파일에 액세스할 자격 증명을 지정합니다. 이전에 패키지 실행 자격 증명(**Windows 인증** 용)을 입력한 경우 **패키지 실행 자격 증명과 동일함** 확인란을 선택하여 다시 사용할 수 있습니다. 그렇지 않은 경우 **도메인**, **사용자 이름** 및 **암호** 상자에 패키지 액세스 자격 증명 값을 입력합니다. 예를 들어 Azure Files에 프로젝트 및 패키지를 저장하는 경우 도메인은 `Azure`, 사용자 이름은 `<storage account name>`, 암호는 `<storage account key>`입니다. 

      또는 Azure Key Vault에 저장된 비밀을 해당 값으로 사용할 수 있습니다. 이렇게 하려면 옆에 있는 **AZURE KEY VAULT** 확인란을 선택합니다. 기존 키 자격 증명 모음 연결된 서비스를 선택 또는 편집하거나 새로 만듭니다. 그런 다음 값에 대한 비밀 이름 및 버전을 선택합니다. 키 자격 증명 모음 연결된 서비스를 만들거나 편집할 때 기존 키 자격 증명 모음을 선택 또는 편집하거나 새 키 자격 증명 모음을 만들 수 있습니다. 아직 수행하지 않은 경우 키 자격 증명 모음에 대한 Data Factory 관리 ID 액세스 권한을 부여해야 합니다. `<key vault linked service name>/<secret name>/<secret version>` 형식으로 비밀을 직접 입력할 수도 있습니다. 

      이러한 자격 증명은 동일한 프로젝트에서 참조되는 패키지 실행 태스크에서 자식 패키지에 액세스하는 데도 사용됩니다. 

   1. SSDT를 통해 패키지를 만들 때 **EncryptAllWithPassword** 또는 **EncryptSensitiveWithPassword** 보호 수준을 사용한 경우 **암호화 암호** 상자에 암호의 값을 입력합니다. 또는 Azure Key Vault에 저장된 비밀을 해당 값으로 사용할 수 있습니다(위의 내용 참조).
      
      **EncryptSensitiveWithUserKey** 보호 수준을 사용한 경우 **SSIS 매개 변수**, **연결 관리자** 또는 **속성 재정의** 탭에 중요한 값을 다시 입력합니다(아래 내용 참조).
      
      **EncryptAllWithUserKey** 보호 수준을 사용하는 경우 지원되지 않습니다. SSDT 또는 `dtutil` 명령줄 유틸리티를 통해 다른 보호 수준을 사용하려면 패키지를 다시 구성해야  합니다. 

   1. **로깅 수준** 에서 패키지 실행에 대해 사전 정의된 로깅 범위를 선택합니다. 사용자 지정된 로깅 이름을 대신 입력하려면 **사용자 지정** 확인란을 선택합니다. 
   
   1. 패키지에 지정할 수 있는 표준 로그 공급자를 사용하여 패키지 실행을 로그하려면 **로깅 경로** 상자에 UNC 경로를 제공하여 로그 폴더를 지정합니다. **파일 저장소 찾아보기** 를 선택하거나 경로를 수동으로 입력하여 로그 폴더를 찾아보고 선택할 수 있습니다. 예를 들어 Azure Files에 로그를 저장하는 경우 로깅 경로는 `\\<storage account name>.file.core.windows.net\<file share name>\<log folder name>`입니다. 각 개별 패키지 실행에 대해 이 경로에 하위 폴더가 만들어지고, SSIS 패키지 실행 작업 실행 ID에 따라 이름이 지어지며, 로그 파일이 5분마다 생성됩니다. 
   
   1. 로그 폴더에 액세스하기 위한 자격 증명을 지정합니다. 이전에 패키지 액세스 자격 증명에 대한 값을 입력한 경우(위의 내용 참조) **패키지 액세스 자격 증명과 동일함** 확인란을 선택하여 다시 사용할 수 있습니다. 그렇지 않은 경우 **도메인**, **사용자 이름** 및 **암호** 상자에 로깅 액세스 자격 증명 값을 입력합니다. 예를 들어 Azure Files에 로그를 저장하는 경우 도메인은 `Azure`, 사용자 이름은 `<storage account name>`, 암호는 `<storage account key>`입니다. 또는 Azure Key Vault에 저장된 암호를 해당 값으로 사용할 수 있습니다(위의 내용 참조).
   
앞에서 언급한 모든 UNC 경로에 대해 정규화된 파일 이름은 260자 미만이어야 합니다. 디렉터리 이름은 248자 미만이어야 합니다.

#### <a name="package-location-embedded-package"></a>패키지 위치: 포함된 패키지

패키지 위치로 **포함된 패키지** 를 선택한 경우 다음 단계를 완료합니다.

:::image type="content" source="media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-settings5.png" alt-text="설정 탭 - 포함된 패키지의 속성 설정":::

   1. 패키지 파일(`.dtsx` 포함)을 끌어다 놓거나 파일 폴더에서 제공된 상자로 **업로드** 합니다. 패키지가 자동으로 압축되고 작업 페이로드에 포함됩니다. 포함되면 나중에 패키지를 **다운로드** 하고 편집할 수 있습니다. 또한 여러 작업에서 사용할 수 있는 파이프라인 매개 변수에 할당하여 포함된 패키지를 **매개 변수화** 할 수 있으며, 이를 통해 파이프라인 페이로드의 크기를 최적화합니다. 프로젝트 파일(`.ispac` 포함)을 포함하는 기능은 현재 지원되지 않으므로 포함된 패키지의 프로젝트 수준 범위에서 SSIS 매개 변수/연결 관리자를 사용할 수 없습니다.
   
   1. 포함된 패키지가 모두 암호화되지 않은 경우 패키지 실행 태스크(EPT) 사용을 감지하면 **패키지 실행 태스크** 확인란이 자동으로 선택되고 파일 시스템 경로에서 참조하는 자식 패키지가 자동으로 추가되므로 이를 포함할 수도 있습니다.
   
      EPT 사용을 감지할 수 없는 경우 **패키지 실행 태스크** 확인란을 수동으로 선택하고 파일 시스템 경로에서 참조하는 자식 패키지를 하나씩 추가해야 이를 포함할 수 있습니다. 자식 패키지가 SQL Server 데이터베이스(MSDB)에 저장된 경우 이를 포함할 수 없으므로 Azure-SSIS IR에서 MSDB에 액세스하고 SQL Server 참조를 사용하여 페치할 수 있어야 합니다. 프로젝트 파일(`.ispac` 포함)을 포함하는 기능은 현재 지원되지 않으므로 자식 패키지에 대한 프로젝트 기반 참조를 사용할 수 없습니다.
   
   1. SSDT를 통해 패키지를 만들 때 **EncryptAllWithPassword** 또는 **EncryptSensitiveWithPassword** 보호 수준을 사용한 경우 **암호화 암호** 상자에 암호의 값을 입력합니다. 
   
      또는 Azure Key Vault에 저장된 비밀을 해당 값으로 사용할 수 있습니다. 이렇게 하려면 옆에 있는 **AZURE KEY VAULT** 확인란을 선택합니다. 기존 키 자격 증명 모음 연결된 서비스를 선택 또는 편집하거나 새로 만듭니다. 그런 다음 값에 대한 비밀 이름 및 버전을 선택합니다. 키 자격 증명 모음 연결된 서비스를 만들거나 편집할 때 기존 키 자격 증명 모음을 선택 또는 편집하거나 새 키 자격 증명 모음을 만들 수 있습니다. 아직 수행하지 않은 경우 키 자격 증명 모음에 대한 Data Factory 관리 ID 액세스 권한을 부여해야 합니다. `<key vault linked service name>/<secret name>/<secret version>` 형식으로 비밀을 직접 입력할 수도 있습니다.
      
      **EncryptSensitiveWithUserKey** 보호 수준을 사용한 경우 구성 파일이나 **SSIS 매개 변수**, **연결 관리자** 또는 **속성 재정의** 탭에 중요한 값을 다시 입력합니다(아래 내용 참조).
      
      **EncryptAllWithUserKey** 보호 수준을 사용하는 경우 지원되지 않습니다. SSDT 또는 `dtutil` 명령줄 유틸리티를 통해 다른 보호 수준을 사용하려면 패키지를 다시 구성해야  합니다.

   1. **로깅 수준** 에서 패키지 실행에 대해 사전 정의된 로깅 범위를 선택합니다. 사용자 지정된 로깅 이름을 대신 입력하려면 **사용자 지정** 확인란을 선택합니다. 
   
   1. 패키지에 지정할 수 있는 표준 로그 공급자를 사용하여 패키지 실행을 로그하려면 **로깅 경로** 상자에 UNC 경로를 제공하여 로그 폴더를 지정합니다. **파일 저장소 찾아보기** 를 선택하거나 경로를 수동으로 입력하여 로그 폴더를 찾아보고 선택할 수 있습니다. 예를 들어 Azure Files에 로그를 저장하는 경우 로깅 경로는 `\\<storage account name>.file.core.windows.net\<file share name>\<log folder name>`입니다. 각 개별 패키지 실행에 대해 이 경로에 하위 폴더가 만들어지고, SSIS 패키지 실행 작업 실행 ID에 따라 이름이 지어지며, 로그 파일이 5분마다 생성됩니다. 
   
   1. **도메인**, **사용자 이름** 및 **암호** 상자에 값을 입력하여 로그 폴더에 액세스하기 위한 자격 증명을 지정합니다. 예를 들어 Azure Files에 로그를 저장하는 경우 도메인은 `Azure`, 사용자 이름은 `<storage account name>`, 암호는 `<storage account key>`입니다. 또는 Azure Key Vault에 저장된 암호를 해당 값으로 사용할 수 있습니다(위의 내용 참조).
   
앞에서 언급한 모든 UNC 경로에 대해 정규화된 파일 이름은 260자 미만이어야 합니다. 디렉터리 이름은 248자 미만이어야 합니다.

#### <a name="package-location-package-store"></a>패키지 위치: 패키지 저장소

패키지 위치로 **패키지 저장소** 를 선택한 경우 다음 단계를 완료합니다.

:::image type="content" source="media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-settings6.png" alt-text="설정 탭 - 패키지 저장소의 속성 설정":::
   
   1. **패키지 저장소 이름** 에서 Azure-SSIS IR에 연결된 기존 패키지 저장소를 선택합니다.

   1. **패키지 경로** 상자에서 선택한 패키지 저장소에서 경로(`.dtsx` 제외)를 제공하여 실행할 패키지를 저장합니다. 선택한 패키지 저장소가 파일 시스템/Azure Files 상단에 있는 경우 **파일 저장소 찾아보기** 를 선택하여 패키지를 찾아보고 선택할 수 있습니다. 그렇지 않은 경우 `<folder name>\<package name>` 형식으로 경로를 입력할 수 있습니다. 또한 [레거시 SSIS 패키지 저장소](/sql/integration-services/service/package-management-ssis-service)와 유사한 SQL Server Management Studio(SSMS)를 통해 선택한 패키지 저장소로 새 패키지를 가져올 수 있습니다. 자세한 내용은 [Azure-SSIS IR 패키지 저장소를 사용하여 SSIS 패키지 관리](./azure-ssis-integration-runtime-package-store.md)를 참조하세요.

   1. 패키지를 별도의 파일에 구성하는 경우 **구성 경로** 상자에서 구성 파일(`.dtsConfig` 포함)에 UNC 경로를 제공해야 합니다. **파일 저장소 찾아보기** 를 선택하거나 경로를 수동으로 입력하여 구성을 찾아보고 선택할 수 있습니다. 예를 들어 Azure Files에 구성을 저장하는 경우 해당 경로는 `\\<storage account name>.file.core.windows.net\<file share name>\<configuration name>.dtsConfig`입니다.

   1. **구성 액세스 자격 증명** 확인란을 선택하여 구성 파일에 액세스하기 위한 자격 증명을 별도로 지정할지 여부를 선택합니다. 선택한 패키지 저장소가 Azure SQL Managed Instance에서 호스트되는 MSDB(SQL Server 데이터베이스) 위에 있거나 구성 파일을 저장하지 않는 경우에 필요합니다.
   
      이전에 패키지 실행 자격 증명(**Windows 인증** 용)을 입력한 경우 **패키지 실행 자격 증명과 동일함** 확인란을 선택하여 다시 사용할 수 있습니다. 그렇지 않은 경우 **도메인**, **사용자 이름** 및 **암호** 상자에 구성 액세스 자격 증명 값을 입력합니다. 예를 들어 Azure Files에 구성을 저장하는 경우 도메인은 `Azure`, 사용자 이름은 `<storage account name>`, 암호는 `<storage account key>`입니다. 

      또는 Azure Key Vault에 저장된 비밀을 해당 값으로 사용할 수 있습니다. 이렇게 하려면 옆에 있는 **AZURE KEY VAULT** 확인란을 선택합니다. 기존 키 자격 증명 모음 연결된 서비스를 선택 또는 편집하거나 새로 만듭니다. 그런 다음 값에 대한 비밀 이름 및 버전을 선택합니다. 키 자격 증명 모음 연결된 서비스를 만들거나 편집할 때 기존 키 자격 증명 모음을 선택 또는 편집하거나 새 키 자격 증명 모음을 만들 수 있습니다. 아직 수행하지 않은 경우 키 자격 증명 모음에 대한 Data Factory 관리 ID 액세스 권한을 부여해야 합니다. `<key vault linked service name>/<secret name>/<secret version>` 형식으로 비밀을 직접 입력할 수도 있습니다.

   1. SSDT를 통해 패키지를 만들 때 **EncryptAllWithPassword** 또는 **EncryptSensitiveWithPassword** 보호 수준을 사용한 경우 **암호화 암호** 상자에 암호의 값을 입력합니다. 또는 Azure Key Vault에 저장된 비밀을 해당 값으로 사용할 수 있습니다(위의 내용 참조).
      
      **EncryptSensitiveWithUserKey** 보호 수준을 사용한 경우 구성 파일이나 **SSIS 매개 변수**, **연결 관리자** 또는 **속성 재정의** 탭에 중요한 값을 다시 입력합니다(아래 내용 참조).
      
      **EncryptAllWithUserKey** 보호 수준을 사용하는 경우 지원되지 않습니다. SSDT 또는 `dtutil` 명령줄 유틸리티를 통해 다른 보호 수준을 사용하려면 패키지를 다시 구성해야  합니다. 

   1. **로깅 수준** 에서 패키지 실행에 대해 사전 정의된 로깅 범위를 선택합니다. 사용자 지정된 로깅 이름을 대신 입력하려면 **사용자 지정** 확인란을 선택합니다. 
   
   1. 패키지에 지정할 수 있는 표준 로그 공급자를 사용하여 패키지 실행을 로그하려면 **로깅 경로** 상자에 UNC 경로를 제공하여 로그 폴더를 지정합니다. **파일 저장소 찾아보기** 를 선택하거나 경로를 수동으로 입력하여 로그 폴더를 찾아보고 선택할 수 있습니다. 예를 들어 Azure Files에 로그를 저장하는 경우 로깅 경로는 `\\<storage account name>.file.core.windows.net\<file share name>\<log folder name>`입니다. 각 개별 패키지 실행에 대해 이 경로에 하위 폴더가 만들어지고, SSIS 패키지 실행 작업 실행 ID에 따라 이름이 지어지며, 로그 파일이 5분마다 생성됩니다. 
   
   1. **도메인**, **사용자 이름** 및 **암호** 상자에 값을 입력하여 로그 폴더에 액세스하기 위한 자격 증명을 지정합니다. 예를 들어 Azure Files에 로그를 저장하는 경우 도메인은 `Azure`, 사용자 이름은 `<storage account name>`, 암호는 `<storage account key>`입니다. 또는 Azure Key Vault에 저장된 암호를 해당 값으로 사용할 수 있습니다(위의 내용 참조).
   
앞에서 언급한 모든 UNC 경로에 대해 정규화된 파일 이름은 260자 미만이어야 합니다. 디렉터리 이름은 248자 미만이어야 합니다.

### <a name="ssis-parameters-tab"></a>SSIS 매개 변수 탭

SSIS 패키지 실행 작업의 **SSIS 매개 변수** 탭에서 다음 단계를 완료합니다.

:::image type="content" source="media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-ssis-parameters.png" alt-text="SSIS 매개 변수 탭의 속성 설정":::

   1. Azure-SSIS IR을 실행 중인 경우 패키지 위치로 **SSISDB** 가 선택되고, **설정** 탭의 **수동 항목** 확인란의 선택이 취소되고, SSISDB에서 선택한 프로젝트 및 패키지의 기존 SSIS 매개 변수가 표시되어 값을 할당할 수 있습니다. 그렇지 않은 경우 하나씩 입력하여 값을 수동으로 할당할 수 있습니다. 패키지를 성공적으로 실행하려면 값을 올바르게 입력했는지 확인합니다. 
   
   1. SSDT를 통해 패키지를 만들 때 **EncryptSensitiveWithUserKey** 보안 수준을 사용하고 **파일 시스템(패키지)** , **파일 시스템(프로젝트)** , **포함된 패키지** 또는 **패키지 저장소** 가 패키지 위치로 선택된 경우 이 탭에서 중요한 매개 변수를 다시 입력하고 값을 할당할 수 있습니다. 
   
매개 변수에 값을 할당할 때 식, 함수, Data Factory 시스템 변수 및 Data Factory 파이프라인 매개 변수 또는 변수를 사용하여 동적 콘텐츠를 추가할 수 있습니다.

또는 Azure Key Vault에 저장된 비밀을 해당 값으로 사용할 수 있습니다. 이렇게 하려면 옆에 있는 **AZURE KEY VAULT** 확인란을 선택합니다. 기존 키 자격 증명 모음 연결된 서비스를 선택 또는 편집하거나 새로 만듭니다. 그런 다음 값에 대한 비밀 이름 및 버전을 선택합니다. 키 자격 증명 모음 연결된 서비스를 만들거나 편집할 때 기존 키 자격 증명 모음을 선택 또는 편집하거나 새 키 자격 증명 모음을 만들 수 있습니다. 아직 수행하지 않은 경우 키 자격 증명 모음에 대한 Data Factory 관리 ID 액세스 권한을 부여해야 합니다. `<key vault linked service name>/<secret name>/<secret version>` 형식으로 비밀을 직접 입력할 수도 있습니다. 

### <a name="connection-managers-tab"></a>연결 관리자 탭

SSIS 패키지 실행 작업의 **연결 관리자** 탭에서 다음 단계를 완료합니다.

:::image type="content" source="media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-connection-managers.png" alt-text="연결 관리자 탭의 속성 설정":::

   1. Azure-SSIS IR을 실행 중인 경우 패키지 위치로 **SSISDB** 가 선택되고, **설정** 탭의 **수동 항목** 확인란의 선택이 취소되고, SSISDB에서 선택한 프로젝트 및 패키지의 기존 연결 관리자가 표시되어 값을 속성에 할당할 수 있습니다. 그렇지 않은 경우 하나씩 입력하여 값을 속성에 수동으로 할당할 수 있습니다. 패키지를 성공적으로 실행하려면 값을 올바르게 입력했는지 확인합니다. 
   
      SSDT에 포함된 패키지를 열어 연결 관리자에 대한 올바른 **SCOPE**, **NAME** 및 **PROPERTY** 이름을 가져올 수 있습니다. 패키지를 연 후에는 SSDT의 **속성** 창에 있는 모든 속성의 이름과 값을 표시하는 관련 연결 관리자를 선택합니다. 이 정보를 사용하여 런타임에서 연결 관리자 속성의 값을 재정의할 수 있습니다. 

      :::image type="content" source="media/how-to-invoke-ssis-package-ssis-activity/ssdt-connection-manager-properties.png" alt-text="SSDT에서 연결 관리자 속성 가져오기":::

      예를 들어 SSDT에서 원래 패키지를 수정하지 않고 런타임에서 **ConnectByProxy**, **ConnectionString** 및 **ConnectUsingManagedIdentity** 의 값을 재정의하여 SQL Server에서 실행되는 온-프레미스 간 데이터 흐름을 ADF의 SSIS IR에서 실행되는 온-프레미스에서 클라우드로의 데이터 흐름으로 변환할 수 있습니다.
      
      이러한 런타임 재정의는 온-프레미스 데이터에 액세스할 때 SSIS IR에 대한 프록시로 자체 호스팅 IR(SHIR)을 사용하도록 설정할 수 있습니다. [SSIS IR에 대한 프록시로 SHIR 구성](./self-hosted-integration-runtime-proxy-ssis.md)을 참조하세요. 최신 MSOLEDBSQL 드라이버를 사용하는 Azure SQL Database/Managed Instance 연결은 ADF 관리 ID를 사용하여 Azure Active Directory(AAD) 인증을 사용 설정합니다. [OLEDB 연결에 대한 ADF 관리 ID로 AAD 인증 구성](/sql/integration-services/connection-manager/ole-db-connection-manager#managed-identities-for-azure-resources-authentication)을 참조하세요.

      :::image type="content" source="media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-connection-managers2.png" alt-text="연결 관리자 탭의 SSDT에서 속성 설정":::
   
   1. SSDT를 통해 패키지를 만들 때 **EncryptSensitiveWithUserKey** 보안 수준을 사용하고 **파일 시스템(패키지)** , **파일 시스템(프로젝트)** , **포함된 패키지** 또는 **패키지 저장소** 가 패키지 위치로 선택된 경우 이 탭에서 중요한 연결 관리자 속성을 다시 입력하고 값을 할당할 수 있습니다. 

연결 관리자 속성에 값을 할당할 때 식, 함수, Data Factory 시스템 변수 및 Data Factory 파이프라인 매개 변수 또는 변수를 사용하여 동적 콘텐츠를 추가할 수 있습니다. 

또는 Azure Key Vault에 저장된 비밀을 해당 값으로 사용할 수 있습니다. 이렇게 하려면 옆에 있는 **AZURE KEY VAULT** 확인란을 선택합니다. 기존 키 자격 증명 모음 연결된 서비스를 선택 또는 편집하거나 새로 만듭니다. 그런 다음 값에 대한 비밀 이름 및 버전을 선택합니다. 키 자격 증명 모음 연결된 서비스를 만들거나 편집할 때 기존 키 자격 증명 모음을 선택 또는 편집하거나 새 키 자격 증명 모음을 만들 수 있습니다. 아직 수행하지 않은 경우 키 자격 증명 모음에 대한 Data Factory 관리 ID 액세스 권한을 부여해야 합니다. `<key vault linked service name>/<secret name>/<secret version>` 형식으로 비밀을 직접 입력할 수도 있습니다. 

### <a name="property-overrides-tab"></a>속성 재정의 탭

SSIS 패키지 실행 작업의 **속성 재정의** 탭에서 다음 단계를 완료합니다.

:::image type="content" source="media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-property-overrides.png" alt-text="속성 재정의 탭의 속성 설정":::

   1. 선택한 패키지의 기존 속성 경로를 하나씩 입력하여 수동으로 값에 속성을 할당합니다. 패키지를 성공적으로 실행하려면 값을 올바르게 입력했는지 확인합니다. 예를 들어 사용자 변수 값을 재정의하려면 `\Package.Variables[User::<variable name>].Value`와 같이 해당 경로를 입력합니다. 

      SSDT에 해당 패키지를 포함하는 패키지를 열어 패키지 속성에 대한 올바른 **속성 경로** 를 가져올 수 있습니다. 패키지가 열린 후 SSDT의 **속성** 창에서 해당 제어 흐름 및 **구성** 속성을 선택합니다. 다음으로 **구성** 속성 옆에 있는 줄임표( **...** ) 단추를 선택하여 패키지 [배포 모델에서 패키지 구성을 만드는](/sql/integration-services/packages/legacy-package-deployment-ssis#create-package-configurations) 데 일반적으로 사용되는 **패키지 구성 도우미** 를 엽니다. 

      :::image type="content" source="media/how-to-invoke-ssis-package-ssis-activity/ssdt-package-properties.png" alt-text="SSDT - 구성 속성에서 패키지 속성 가져오기":::

      **패키지 구성 도우미** 에서 **패키지 구성 사용** 확인란을 선택하고 **추가...** 단추를 선택하여 **패키지 구성 마법사** 를 엽니다. 
      
      **패키지 구성 마법사** 의 **구성 유형** 드롭다운 메뉴에서 **XML 구성 파일** 항목을 선택한 다음 **직접 구성 설정 지정** 단추를 선택하고, 구성 파일 이름을 입력하고, **다음 >** 단추를 선택합니다. 

      :::image type="content" source="media/how-to-invoke-ssis-package-ssis-activity/ssdt-package-properties2.png" alt-text="SSDT - 구성 도우미에서 패키지 속성 가져오기":::

      마지막으로 원하는 경로를 가진 패키지 속성을 선택하고 **다음 >** 단추를 선택합니다.  이제 원하는 패키지 속성 경로를 복사 후 붙여넣고 구성 파일에 저장할 수 있습니다. 이 정보를 사용하여 런타임에서 패키지 속성의 값을 재정의할 수 있습니다. 

      :::image type="content" source="media/how-to-invoke-ssis-package-ssis-activity/ssdt-package-properties3.png" alt-text="SSDT - 구성 마법사에서 패키지 속성 가져오기":::
   
   1. SSDT를 통해 패키지를 만들 때 **EncryptSensitiveWithUserKey** 보안 수준을 사용하고 **파일 시스템(패키지)** , **파일 시스템(프로젝트)** , **포함된 패키지** 또는 **패키지 저장소** 가 패키지 위치로 선택된 경우 이 탭에서 중요한 패키지 속성을 다시 입력하고 값을 할당할 수 있습니다. 
   
패키지 속성에 값을 할당할 때 식, 함수, Data Factory 시스템 변수 및 Data Factory 파이프라인 매개 변수 또는 변수를 사용하여 동적 콘텐츠를 추가할 수 있습니다.

구성 파일 및 **SSIS 매개 변수** 탭에 할당된 값은 **연결 관리자** 또는 **속성 재정의** 탭을 사용하여 재정의할 수 있습니다. **연결 관리자** 탭에 할당된 값 역시 **속성 재정의** 탭을 사용하여 재정의할 수 있습니다.

파이프라인 구성에 대한 유효성을 검사하려면 도구 모음에서 **유효성 검사** 를 선택합니다. **파이프라인 유효성 검사 보고서** 를 닫으려면 **>>** 를 선택합니다.

Data Factory에 파이프라인을 게시하려면 **모두 게시** 를 선택합니다. 

## <a name="run-the-pipeline"></a>파이프라인 실행
이 단계에서는 파이프라인 실행을 트리거합니다. 

1. 파이프라인 실행을 트리거하려면 도구 모음에서 **트리거** 를 선택하고 **지금 트리거** 를 선택합니다. 

   :::image type="content" source="./media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-trigger.png" alt-text="지금 트리거":::

2. **파이프라인 실행** 창에서 **마침** 을 선택합니다. 

## <a name="monitor-the-pipeline"></a>파이프라인 모니터링

1. 왼쪽의 **모니터** 탭으로 전환합니다. 다른 정보(예: **실행 시작** 시간)와 함께 파이프라인 실행 및 해당 상태를 확인합니다. 보기를 새로 고치려면 **새로 고침** 을 선택합니다.

   :::image type="content" source="./media/how-to-invoke-ssis-package-stored-procedure-activity/pipeline-runs.png" alt-text="파이프라인 실행":::

2. **작업** 열에서 **활동 실행 보기** 링크를 선택합니다. 파이프라인에는 하나의 작업만 있으므로 하나의 작업 실행만 파이프라인으로 표시됩니다. SSIS 패키지 실행 작업입니다.

   :::image type="content" source="./media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-runs.png" alt-text="작업 실행":::

3. SQL 서버의 SSISDB 데이터베이스에 대해 다음 쿼리를 실행하여 패키지가 실행되었는지 확인합니다. 

   ```sql
   select * from catalog.executions
   ```

   :::image type="content" source="./media/how-to-invoke-ssis-package-stored-procedure-activity/verify-package-executions.png" alt-text="패키지 실행 확인":::

4. 파이프라인 작업 실행의 출력에서 SSISDB 실행 ID를 가져올 수 있고 더 포괄적인 실행 로그 및 SQL Server Management Studio에서 오류 메시지를 확인하는 ID를 사용할 수도 있습니다.

   :::image type="content" source="media/how-to-invoke-ssis-package-ssis-activity/get-execution-id.png" alt-text="실행 ID를 가져옵니다.":::

## <a name="schedule-the-pipeline-with-a-trigger"></a>트리거를 사용하여 파이프라인 예약

또한 파이프라인이 일정대로(예: 시간별, 일별) 실행되도록 파이프라인에 대해 예약된 트리거를 만들 수도 있습니다. 예를 들어 [데이터 팩터리 만들기 - Data Factory UI](quickstart-create-data-factory-portal.md#trigger-the-pipeline-on-a-schedule)를 참조하세요.

## <a name="next-steps"></a>다음 단계
- [PowerShell을 사용 하 여 Azure Data Factory에서 SSIS 패키지 실행 작업을 사용 하 여 SSIS 패키지를 실행 합니다.](how-to-invoke-ssis-package-ssis-activity-powershell.md)
- [Azure Data Factory 파이프라인에서 SSIS 작업을 사용하여 ETL/ELT 워크플로 현대화 및 확장](https://techcommunity.microsoft.com/t5/SQL-Server-Integration-Services/Modernize-and-Extend-Your-ETL-ELT-Workflows-with-SSIS-Activities/ba-p/388370)