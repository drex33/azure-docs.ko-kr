---
title: '자습서: Azure Purview(미리 보기)를 사용하여 데이터 스캔'
description: 이 자습서에서는 시작 키트를 실행하여 데이터 자산을 설정한 다음, 데이터 원본의 데이터를 Azure Purview 카탈로그로 스캔합니다.
author: viseshag
ms.author: viseshag
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: tutorial
ms.date: 12/01/2020
ms.openlocfilehash: 723136a47dec007d16a6ab1a8be0400dbb34ae8c
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "121736346"
---
# <a name="tutorial-scan-data-with-azure-purview-preview"></a>자습서: Azure Purview(미리 보기)를 사용하여 데이터 스캔

> [!IMPORTANT]
> Azure Purview는 현재 미리 보기로 제공됩니다. [Microsoft Azure 미리 보기에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)에는 베타 또는 미리 보기로 제공되거나 아직 일반 공급으로 릴리스되지 않은 Azure 기능에 적용되는 추가 약관이 포함되어 있습니다.

이 *5부로 구성된 자습서 시리즈* 에서는 Azure Purview(미리 보기)를 사용하여 데이터 자산 전체에서 데이터 거버넌스를 관리하는 방법에 대한 기본 사항을 알아봅니다. 자습서의 이 파트에서 만드는 데이터 자산은 시리즈의 나머지 파트에 사용됩니다.

이 자습서 시리즈의 1부에서 수행하는 작업은 다음과 같습니다.

> [!div class="checklist"]
>
> * 다양한 Azure 데이터 리소스로 데이터 자산 만들기
> * 데이터를 카탈로그로 스캔

## <a name="prerequisites"></a>필수 조건

* Azure 구독 Azure 구독이 아직 없는 경우 시작하기 전에 [체험](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) 계정을 만듭니다.
* [Azure Purview 계정](create-catalog-portal.md).
* 데이터 자산을 배포할 [시작 키트](https://github.com/Azure/Purview-Samples/blob/master/PurviewStarterKitV4.zip)

> [!NOTE]
> 시작 키트는 Windows에만 사용할 수 있습니다.

## <a name="sign-in-to-azure"></a>Azure에 로그인

[Azure Portal](https://portal.azure.com)에 로그인합니다.

## <a name="create-a-data-estate"></a>데이터 자산 만들기

이 섹션에서는 시작 키트 스크립트를 실행하여 시뮬레이션된 데이터 자산을 만듭니다. 데이터 자산이란 회사가 소유한 모든 데이터의 포트폴리오입니다. 시작 키트 스크립트는 다음 작업을 수행합니다.

* Azure Blob 스토리지 계정을 만들고 해당 계정을 데이터로 채웁니다.
* Azure Data Lake Storage Gen2 계정을 만듭니다.
* Azure Data Factory 인스턴스를 만들고 Azure Purview에 연결합니다.
* Azure Blob 스토리지와 Azure Data Lake Storage Gen2 계정 간에 복사 작업 파이프라인을 설정하고 트리거합니다.
* 연결된 계보를 Azure Data Factory에서 Azure Purview로 푸시합니다.

### <a name="prepare-to-run-the-starter-kit"></a>시작 키트 실행 준비

다음 단계에 따라 Windows 머신에서 시작 키트 클라이언트 소프트웨어를 설정합니다.

1. 원하는 위치에 [시작 키트를 다운로드](https://github.com/Azure/Purview-Samples/blob/master/PurviewStarterKitV4.zip)하고 콘텐츠를 추출합니다.


1. 컴퓨터의 Windows 작업 표시줄에 있는 검색 상자에 **PowerShell** 을 입력합니다. 검색 목록에서 **Windows PowerShell** 을 마우스 오른쪽 단추로 클릭한 다음, **관리자 권한으로 실행** 을 선택합니다.

1. PowerShell 창에 다음 명령을 입력하고 `<path-to-starter-kit>`를 추출한 시작 키트 파일의 폴더 경로로 바꿉니다.

   ```powershell
   dir -Path <path-to-starter-kit> -Recurse | Unblock-File
   ```

1. 다음 명령을 입력하여 Azure cmdlet을 설치합니다.

   ```powershell
   Install-Module -Name Az -AllowClobber -Scope CurrentUser
   ```

1. *계속하려면 NuGet 공급자가 필요합니다* 라는 경고 메시지가 표시되면 **Y** 를 입력하고 Enter 키를 누릅니다.

1. *신뢰할 수 없는 리포지토리* 라는 경고 메시지가 표시되면 **A** 를 입력하고 Enter 키를 누릅니다.

PowerShell이 필요한 모듈을 설치하는 데 최대 1분 정도 걸릴 수 있습니다.

### <a name="collect-data-needed-to-run-the-scripts"></a>스크립트를 실행하는 데 필요한 데이터 수집

PowerShell 스크립트를 실행하여 카탈로그를 부트스트랩하려면 먼저 스크립트에서 사용할 다음 인수의 값을 가져옵니다.

* TenantID
   1. [Azure Portal](https://portal.azure.com)에서 **Azure Active Directory** 를 선택합니다.
   1. 왼쪽 탐색 창의 **관리** 섹션에서 **속성** 을 선택합니다. 그런 다음, **테넌트 ID** 의 복사 아이콘을 선택하여 값을 클립보드에 저장합니다. 나중에 사용할 수 있도록 값을 텍스트 편집기에 붙여넣습니다.

* 구독 ID
   1. Azure Portal에서, 필수 구성 요소로 만든 Azure Purview 인스턴스의 이름을 검색하여 선택합니다.
   1. **개요** 섹션을 선택하고 **구독 ID** 의 GUID를 저장합니다.

   > [!NOTE]
   > - Azure Purview 계정을 만든 구독과 동일한 구독을 사용하고 있는지 확인합니다. 이 구독은 허용 목록에 배치된 구독과 동일합니다.
   > - 시작 키트를 실행한 후 Azure Purview에서 계보가 누락되는 경우가 있습니다. 이는 시작 키트에서 만든 Data Factory에는 Purview의 권한이 없기 때문입니다. [**이 문서 링크**](how-to-link-azure-data-factory.md#view-existing-data-factory-connections)를 선택하여 Data Factory가 올바르게 구성되고 Purview에서 적절한 역할이 할당되었는지 확인합니다.


* CatalogName: [Azure Purview 계정 만들기](create-catalog-portal.md)에서 만든 Azure Purview 계정의 이름입니다.

* CatalogResourceGroupName: Azure Purview 계정을 만든 리소스 그룹의 이름입니다.

### <a name="verify-permissions"></a>권한 확인

다음 단계에 따라 스크립트를 실행하는 사용자를 필수 구성 요소로 만든 Azure Purview 계정에 추가합니다. 사용자에게 *Purview 데이터 큐레이터* 및 *Purview 데이터 원본 관리자* 역할이 모두 필요합니다. 

Azure Purview 계정을 직접 만든 경우 자동으로 액세스 권한이 부여되므로 이 섹션을 건너뛰어도 됩니다.

1. Azure Portal에서 [Purview 계정] 페이지로 이동한 다음, 수정하려는 Azure Purview 계정을 선택합니다.

1. 계정의 페이지에서 **액세스 제어(IAM)** 탭을 선택하고 **+ 추가** 를 선택합니다.

1. **역할 할당 추가** 를 선택합니다.

1. *역할* 로 **Purview 데이터 큐레이터 역할** 을 입력합니다.
 
1. *다음에 대한 액세스 할당* 에는 기본값을 사용합니다. 기본값은 **사용자, 그룹 또는 서비스 사용자** 입니다.

1. 스크립트를 실행하는 사용자 이름을 **선택** 에 입력합니다.

1. **저장** 을 선택합니다.

1. **Purview 데이터 원본 관리자 역할** 로 설정된 *역할* 에서 이전 단계를 반복합니다.

자세한 내용은 [카탈로그 권한](catalog-permissions.md)을 참조하세요.

### <a name="run-the-client-side-setup-scripts"></a>클라이언트 쪽 설치 스크립트 실행

카탈로그 구성이 완료되면 PowerShell 창에서 다음 스크립트를 실행하여 자산을 만들고, 자리 표시자를 이전에 수집한 값으로 바꿉니다.

1. 다음 명령을 사용하여 시작 키트 디렉터리로 이동합니다. `path-to-starter-kit`를 추출된 파일의 폴더 경로로 바꿉니다.

   ```powershell
   cd <path-to-starter-kit>
   ```

1. 다음 명령은 로컬 컴퓨터의 실행 정책을 설정합니다. 실행 정책을 변경하라는 메시지가 표시되면 *모두 예* 에 **A** 를 입력합니다.

   ```powershell
   Set-ExecutionPolicy -ExecutionPolicy Unrestricted
   ```

1. 다음 명령을 사용하여 Azure에 연결합니다. `TenantID` 및 `SubscriptionID` 자리 표시자를 바꿉니다.

   ```powershell
   .\RunStarterKit.ps1 -ConnectToAzure -TenantId <TenantID> `
   -SubscriptionId <SubscriptionID>
   ```

   명령을 실행하면 Azure Active Directory 자격 증명을 사용하여 로그인하는 팝업 창이 표시될 수 있습니다.

    > [!TIP]
    > 테넌트에서 MFA를 사용하도록 설정한 경우 이 단계에서 MFA 오류가 발생할 수 있습니다. 이 경우 이 스크립트를 실행하는 계정에 대해 MFA를 잠시 사용하지 않도록 설정합니다. 그런 다음 다시 실행합니다.


1. 다음 명령을 사용하여 시작 키트를 실행합니다. `CatalogName`, `TenantID`, `SubscriptionID`, `NewResourceGroupName` 및 `CatalogResourceGroupName` 자리 표시자를 바꿉니다. `NewResourceGroupName`의 경우 데이터 자산을 포함할 리소스 그룹에 고유한 이름(소문자 영숫자만 포함)을 사용합니다.

   > [!IMPORTANT]
   > **newresourcegroupname** 에는 숫자 및 소문자만 사용하고 17자 미만이어야 합니다. **대문자 및 특수 문자는 허용되지 않습니다.** 이 제약 조건은 스토리지 계정 명명 규칙에서 온 것입니다.

   ```powershell
   .\RunStarterKit.ps1 -CatalogName <CatalogName> -TenantId <TenantID>`
   -ResourceGroup <newresourcegroupname> `
   -SubscriptionId <SubscriptionID> `
   -CatalogResourceGroup <CatalogResourceGroupName>
   ```

환경이 설정될 때까지 최대 10분 정도 걸릴 수 있습니다. 이 시간 동안 다양한 팝업 창이 표시될 수 있으며, 무시해도 됩니다. **BlobDataCreator.exe** 창을 닫지 마세요. 작업이 완료되면 자동으로 닫힙니다.

`Executing Copy pipeline xxxxxxxxxx-487e-4fc4-9628-92dd8c2c732b` 메시지가 표시되면 또 다른 **BlobDataCreator.exe** 인스턴스가 시작되어 실행이 완료될 때까지 기다립니다.

> [!IMPORTANT]
> '활성 작업 수'가 더 이상 감소하지 않으면 Blob 작성자 창을 종료하고 Powershell 창에서 Enter 키를 눌러도 됩니다.

프로세스가 완료되면 앞에서 입력한 이름의 리소스 그룹이 만들어집니다. Azure Data Factory, Azure Blob 스토리지 및 Azure Data Lake Storage Gen2 계정은 모두 이 리소스 그룹에 포함됩니다. 리소스 그룹은 지정한 구독에 포함됩니다.

## <a name="scan-data-into-the-catalog"></a>데이터를 카탈로그로 스캔

스캔은 사용자가 지정한 일정에 따라 카탈로그가 데이터 원본에 직접 연결하는 프로세스입니다. 카탈로그는 스캔, 계보, 포털 및 API를 통해 회사의 데이터 자산을 반영합니다. 안에 무엇이 있는지 검사하고 스키마를 추출하고 의미 체계를 이해하는 것이 목표입니다. 이 섹션에서는 시작 키트를 사용하여 생성한 데이터 원본의 스캔을 설정합니다.

앞에서 실행한 시작 키트 스크립트는 두 개의 데이터 원본 Azure Blob 스토리지와 Azure Data Lake Storage Gen2를 만들었습니다. 이러한 데이터 원본을 한 번에 하나씩 카탈로그로 스캔할 수 있습니다.

### <a name="authenticate-to-your-storage-with-managed-identity"></a>관리 ID를 사용하여 스토리지 인증

계정을 만들 때 Azure Purview 계정과 이름이 같은 관리 ID가 자동으로 생성됩니다. 데이터를 스캔하려면 먼저 스토리지 계정에 대한 Azure Purview 역할 권한을 부여해야 합니다.

1. 시작 키트를 사용하여 만든 리소스 그룹으로 이동하여 Blob 스토리지 계정을 선택합니다.

1. 왼쪽 탐색 메뉴에서 **액세스 제어(IAM)** 를 선택합니다. 그런 다음, **+ 추가** 를 선택합니다.

1. 역할을 **스토리지 Blob 데이터 읽기 권한자** 로 설정하고 **선택** 에 Azure Purview 계정 이름을 입력합니다. 그런 다음, **저장** 을 선택하여 Purview 계정에 이 역할을 할당합니다.

   :::image type="content" source="media/tutorial-scan-data/add-role-assignment.png" alt-text="역할 할당 추가":::

1. Azure Data Lake Storage Gen2에도 이전 단계를 반복합니다.

### <a name="scan-your-data-sources"></a>데이터 원본 스캔

1. [Azure Portal](https://portal.azure.com)에서 필수 구성 요소로 만든 Azure Purview 인스턴스의 이름을 검색하여 선택한 다음, *Open Purview Studio* 를 선택합니다. Purview Studio 홈 페이지로 자동으로 이동합니다.

1. 왼쪽 탐색 메뉴에서 **데이터 맵** 을 선택하고 **등록** 을 선택합니다. 그런 다음, **Azure Blob Storage** 를 선택하고 **계속** 을 선택합니다.

   :::image type="content" source="media/tutorial-scan-data/add-blob-storage.png" alt-text="Blob 스토리지 리소스 등록":::

1. **원본 등록** 페이지에서 **이름** 을 입력합니다. 앞에서 시작 키트를 사용하여 만든 Azure Blob 스토리지 계정의 **스토리지 계정 이름** 을 선택합니다. 계정 이름은 `<YourResourceGroupName>adcblob`입니다. **마침** 을 선택합니다.

   :::image type="content" source="./media/tutorial-scan-data/register-azure-blob-storage.png" alt-text="Azure Blob 스토리지 데이터 원본을 등록하는 설정을 보여주는 스크린샷" border="true":::

1. **데이터 원본** 맵 보기에서 Azure Blob Storage 타일의 **새 스캔** 을 선택합니다.

   :::image type="content" source="./media/tutorial-scan-data/select-setup-scan.png" alt-text="데이터 원본에서 스캔 설정을 선택하는 방법을 보여주는 스크린샷" border="true":::

1. **스캔** 페이지에서 스캔 이름을 입력하고 **자격 증명** 드롭다운에서 관리 ID를 선택한 다음, **계속** 을 선택합니다.

   :::image type="content" source="media/tutorial-scan-data/scan-blob.png" alt-text="Azure Purview에서 Blob 스토리지 스캔":::

1. 스캔 범위를 개별 Blob으로 지정할 수 있습니다. 이 자습서에서는 전체를 스캔하도록 모든 자산을 선택하고 **계속** 을 선택합니다.

   :::image type="content" source="media/tutorial-scan-data/scope-your-scan.png" alt-text="스토리지 스캔 범위 지정":::

1. 기본 스캔 규칙 세트를 선택하고 **계속** 을 선택합니다.

1. 반복 스캔에 대한 스캔 트리거를 설정할 수 있습니다. 이 자습서에서는 **한 번** 을 선택하고 **계속** 을 선택합니다.

1. **저장 후 실행** 을 선택하여 스캔을 완료합니다.

1. 이전 단계를 반복하여 Azure Data Lake Storage Gen2 계정을 스캔합니다.

## <a name="next-steps"></a>다음 단계

이 자습서에서는 다음 작업 방법을 알아보았습니다.
> [!div class="checklist"]
>
> * 시작 키트 스크립트를 실행하여 데이터 자산 환경 설정
> * Azure Purview 카탈로그로 데이터 스캔

다음 자습서를 진행하여 홈 페이지를 탐색하고 자산을 검색하는 방법을 알아보세요.

> [!div class="nextstepaction"]
> [홈 페이지를 탐색하고 자산 검색](tutorial-asset-search.md)
