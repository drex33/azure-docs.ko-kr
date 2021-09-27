---
title: Azure Automation Runbook을 사용하여 StorSimple 장치 관리
description: 이 문서에서는 Azure Automation Runbook을 사용하여 Azure Portal에서 StorSimple 8000 시리즈 장치를 관리하는 방법을 설명합니다.
author: alkohli
ms.service: storsimple
ms.topic: how-to
ms.date: 10/23/2017
ms.author: alkohli
ms.openlocfilehash: 33385fa03d480c257789fd4cc139f9033e541a99
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124754940"
---
# <a name="use-azure-automation-runbooks-to-manage-storsimple-devices"></a>Azure Automation Runbook을 사용하여 StorSimple 디바이스 관리

이 문서에서는 Azure Automation Runbook을 사용하여 Azure Portal에서 StorSimple 8000 시리즈 디바이스를 관리하는 방법을 설명합니다. 이 Runbook을 실행하도록 환경을 구성하는 단계를 안내하는 샘플 Runbook도 포함되어 있습니다.


## <a name="configure-add-and-run-azure-runbook"></a>Azure Runbook 구성, 추가 및 실행

이 섹션에서는 StorSimple에 대한 Windows PowerShell 스크립트를 예로 들어 스크립트를 Runbook으로 가져온 다음 Runbook을 게시 및 실행하는 데 필요한 여러 단계를 자세히 설명합니다.

### <a name="prerequisites"></a>사전 요구 사항

시작하기 전에 다음 항목이 있어야 합니다.

* StorSimple 8000 시리즈 디바이스에 등록된 StorSimple 디바이스 관리자 서비스와 연결된 활성 Azure 구독

* 컴퓨터(또는 StorSimple용 Windows Server 호스트를 사용하는 경우 호스트)에 설치된 Windows PowerShell 5.0

### <a name="create-automation-runbook-module-in-windows-powershell"></a>Windows PowerShell에서 Automation Runbook 모듈 만들기

StorSimple 8000 시리즈 디바이스 관리를 위한 자동화 모듈을 만들려면 다음 단계를 수행합니다.

1. Windows PowerShell을 시작합니다. 새 폴더를 만들고 디렉터리를 해당 새 폴더로 변경합니다.

    ```powershell
        mkdir C:\scripts\StorSimpleSDKTools
        cd C:\scripts\StorSimpleSDKTools
    ```

2. 이전 단계에서 만든 폴더에서 [NuGet CLI를 다운로드](https://www.nuget.org/downloads)합니다. 다양한 버전의 _nuget.exe_ 가 있습니다. 사용하는 SDK에 해당하는 버전을 선택합니다. 각 다운로드 링크는 _.exe_ 파일을 직접 가리킵니다. 브라우저에서 실행하지 말고, 마우스 오른쪽 단추를 클릭하여 컴퓨터에 파일을 저장해야 합니다.

    또한 다음 명령을 실행하여 이전에 만든 동일한 폴더에 스크립트를 다운로드한 후 저장할 수도 있습니다.

    ```
        wget https://dist.nuget.org/win-x86-commandline/latest/nuget.exe -Out C:\scripts\StorSimpleSDKTools\nuget.exe
    ```

3. 종속 SDK를 다운로드합니다.

    ```
        C:\scripts\StorSimpleSDKTools\nuget.exe install Microsoft.Azure.Management.Storsimple8000series
        C:\scripts\StorSimpleSDKTools\nuget.exe install Microsoft.IdentityModel.Clients.ActiveDirectory -Version 2.28.3
        C:\scripts\StorSimpleSDKTools\nuget.exe install Microsoft.Rest.ClientRuntime.Azure.Authentication -Version 2.2.9-preview
    ```

4. 샘플 Github 프로젝트에서 스크립트를 다운로드합니다.

    ```
        wget https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Monitor-Backups.ps1 -Out Monitor-Backups.ps1
    ```

5. StorSimple 8000 Series 디바이스 관리를 위한 Azure Automation Runbook 모듈을 만듭니다. Windows Powershell 창에서 다음 명령을 입력합니다.

    ```powershell
        # set path variables
        $downloadDir = "C:\scripts\StorSimpleSDKTools"
        $moduleDir = "$downloadDir\AutomationModule\Microsoft.Azure.Management.StorSimple8000Series"

        #don't change the folder name "Microsoft.Azure.Management.StorSimple8000Series"
        mkdir "$moduleDir"
        Copy-Item "$downloadDir\Microsoft.IdentityModel.Clients.ActiveDirectory.2.28.3\lib\net45\Microsoft.IdentityModel.Clients.ActiveDirectory*.dll" $moduleDir
        Copy-Item "$downloadDir\Microsoft.Rest.ClientRuntime.Azure.3.3.7\lib\net452\Microsoft.Rest.ClientRuntime.Azure*.dll" $moduleDir
        Copy-Item "$downloadDir\Microsoft.Rest.ClientRuntime.2.3.8\lib\net452\Microsoft.Rest.ClientRuntime*.dll" $moduleDir
        Copy-Item "$downloadDir\Newtonsoft.Json.6.0.8\lib\net45\Newtonsoft.Json*.dll" $moduleDir
        Copy-Item "$downloadDir\Microsoft.Rest.ClientRuntime.Azure.Authentication.2.2.9-preview\lib\net45\Microsoft.Rest.ClientRuntime.Azure.Authentication*.dll" $moduleDir
        Copy-Item "$downloadDir\Microsoft.Azure.Management.Storsimple8000series.1.0.0\lib\net452\Microsoft.Azure.Management.Storsimple8000series*.dll" $moduleDir

        #Don't change the name of the Archive
        compress-Archive -Path "$moduleDir" -DestinationPath Microsoft.Azure.Management.StorSimple8000Series.zip
    ```

6. 자동화 모듈 zip 파일이 `C:\scripts\StorSimpleSDKTools`에 생성되었는지 확인합니다.

    ![자동화 모듈 zip 파일을 사용 하는 StorSimpleSDKTools 폴더를 보여주는 스크린샷](./media/storsimple-8000-automation-azurerm-runbook/verify-automation-module.png)

7. Windows PowerShell을 통해 자동화 모듈이 생성되면 다음 출력이 표시됩니다.

    ```powershell
    mkdir C:\scripts\StorSimpleSDKTools
    ```

    ```Output
        Directory: C:\scripts

    Mode                LastWriteTime         Length Name
    ----                -------------         ------ ----
    d-----       10/18/2017   8:43 AM                StorSimpleSDKTools
    ```

    ```powershell
    wget https://dist.nuget.org/win-x86-commandline/latest/nuget.exe -Out C:\scripts\StorSimpleSDKTools\nuget.exe
    ```

    ```powershell
    C:\scripts\StorSimpleSDKTools\nuget.exe install Microsoft.Azure.Management.Storsimple8000series
    ```

    ```Output
    -------------------------------------------
    CUT              CUT
    -------------------------------------------
    Successfully installed 'Microsoft.Azure.Management.Storsimple8000series 1.0.0' to C:\scripts\StorSimpleSDKTools
    Executing nuget actions took 1.77 sec
    ```

    ```powershell
    C:\scripts\StorSimpleSDKTools\nuget.exe install Microsoft.IdentityModel.Clients.ActiveDirectory -Version 2.28.3
    ```

    ```Output
    -------------------------------------------
    CUT              CUT
    -------------------------------------------
    Successfully installed 'Microsoft.IdentityModel.Clients.ActiveDirectory 2.28.3' to C:\scripts\StorSimpleSDKTools
    Executing nuget actions took 927.64 ms
    ```

    ```powershell
    C:\scripts\StorSimpleSDKTools\nuget.exe install Microsoft.Rest.ClientRuntime.Azure.Authentication -Version 2.2.9-preview
    ```

    ```Output
    -------------------------------------------
    CUT              CUT
    -------------------------------------------
    Successfully installed 'Microsoft.Rest.ClientRuntime.Azure.Authentication 2.2.9-preview' to C:\scripts\StorSimpleSDKTools
    Executing nuget actions took 717.48 ms
    ```

    ```powershell
    wget https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Monitor-Backups.ps1 -Out Monitor-Backups.ps1
    # set path variables
    $downloadDir = "C:\scripts\StorSimpleSDKTools"
    $moduleDir = "$downloadDir\AutomationModule\Microsoft.Azure.Management.StorSimple8000Series"
    #don't change the folder name "Microsoft.Azure.Management.StorSimple8000Series"
    mkdir "$moduleDir"
    ```

    ```Output
        Directory: C:\scripts\StorSimpleSDKTools\AutomationModule

    Mode                LastWriteTime         Length Name
    ----                -------------         ------ ----
    d-----       10/18/2017   8:48 AM                Microsoft.Azure.Management.StorSimple8000Series
    ```

    ```powershell
    Copy-Item "$downloadDir\Microsoft.IdentityModel.Clients.ActiveDirectory.2.28.3\lib\net45\Microsoft.IdentityModel.Clients.ActiveDirectory*.dll" $moduleDir
    Copy-Item "$downloadDir\Microsoft.Rest.ClientRuntime.Azure.3.3.7\lib\net452\Microsoft.Rest.ClientRuntime.Azure*.dll" $moduleDir
    Copy-Item "$downloadDir\Microsoft.Rest.ClientRuntime.2.3.8\lib\net452\Microsoft.Rest.ClientRuntime*.dll" $moduleDir
    Copy-Item "$downloadDir\Newtonsoft.Json.6.0.8\lib\net45\Newtonsoft.Json*.dll" $moduleDir
    Copy-Item "$downloadDir\Microsoft.Rest.ClientRuntime.Azure.Authentication.2.2.9-preview\lib\net45\Microsoft.Rest.ClientRuntime.Azure.Authentication*.dll" $moduleDir
    Copy-Item "$downloadDir\Microsoft.Azure.Management.Storsimple8000series.1.0.0\lib\net452\Microsoft.Azure.Management.Storsimple8000series*.dll" $moduleDir
    #Don't change the name of the Archive
    compress-Archive -Path "$moduleDir" -DestinationPath Microsoft.Azure.Management.StorSimple8000Series.zip
    ```

### <a name="import-publish-and-run-automation-runbook"></a>Automation Runbook 가져오기, 게시 및 실행

1. Azure Portal에서 Azure 실행 자동화 계정을 만듭니다. 이렇게 하려면 **Azure Marketplace > 모두** 로 이동한 다음 **자동화** 를 검색합니다. **Automation 계정** 을 선택합니다.

    ![search-automation](./media/storsimple-8000-automation-azurerm-runbook/automation1.png)

2. **Automation 계정 추가** 블레이드에서 다음을 수행합니다.

   1. Automation 계정의 **이름** 을 입력합니다.
   2. StorSimple 디바이스 관리자 서비스에 연결된 **구독** 을 선택합니다.
   3. 새 리소스 그룹을 만들거나 기존 리소스 그룹에서 선택합니다.
   4. **위치**(가능한 경우 서비스가 실행되고 있는 위치)를 선택합니다.
   5. 기본 **실행 계정 만들기** 옵션을 선택된 상태로 둡니다.
   6. 선택적으로 **대시보드에 고정** 을 선택합니다. **만들기** 를 클릭합니다.

       ![구독, 계정으로 Azure Run 생성하기, 생성하기 2 값 등이 포함된 Automation 계정 추가 창을 보여주는 스크린샷](./media/storsimple-8000-automation-azurerm-runbook/create-automation-account.png)

      Automation 계정이 생성되면 알림이 표시됩니다. Automation 계정을 만드는 방법에 대한 자세한 내용은 [실행 계정 만들기](../automation/manage-runas-account.md)를 참조하세요.

3. 생성된 Automation 계정이 StorSimple 디바이스 관리자 서비스에 액세스할 수 있는지 확인하려면 Automation 계정에 적절한 권한을 할당해야 합니다. StorSimple 디바이스 관리자 서비스의 **액세스 제어** 로 이동합니다. **+ 추가** 를 클릭하고 Azure Automation 계정의 이름을 입력합니다. 설정을 **저장** 합니다.

    ![add-permissions-automation-account](./media/storsimple-8000-automation-azurerm-runbook/goto-add-roles.png)

4. 새로 만든 계정에서 **공유 리소스 > 모듈** 로 이동한 다음 **+ 모듈 추가** 를 클릭합니다.

5. **모듈 추가** 블레이드에서 압축된 모듈의 위치를 찾은 다음 모듈을 선택하여 엽니다. **확인** 을 클릭합니다.

    ![업로드 파일 값과 확인이 포함된 모듈 추가 창을 보여주는 스크린샷](./media/storsimple-8000-automation-azurerm-runbook/add-module.png)

6. **프로세스 자동화 > Runbook으로 이동한 다음 + Runbook 추가** 를 클릭합니다. **Runbook 추가** 블레이드에서 **기존 Runbook 가져오기** 를 클릭합니다. **Runbook 파일** 의 Windows PowerShell 스크립트 파일을 가리킵니다. Runbook 유형이 자동으로 선택됩니다. Runbook의 이름과 선택적 설명을 입력합니다. **만들기** 를 클릭합니다.

    ![add-module](./media/storsimple-8000-automation-azurerm-runbook/import-runbook.png)

7. Runbook이 Runbook 목록에 추가됩니다. 이 Runbook을 선택하고 클릭합니다.

    ![click-new-runbook](./media/storsimple-8000-automation-azurerm-runbook/verify-runbook-created.png)

8. Runbook을 편집하고 **테스트 창** 을 클릭합니다. StorSimple 디바이스 관리자 서비스 이름, StorSimple 디바이스 이름, 구독 등의 매개 변수를 제공합니다. 테스트를 **시작** 합니다. 실행이 완료되면 보고서가 생성됩니다. 자세한 내용은 [Runbook을 테스트하는 방법](../automation/learn/powershell-runbook-managed-identity.md)을 참조하세요.

    ![매개 변수 값을 입력하고 테스트를 시작하는 테스트 이동을 보여주는 스크린샷](./media/storsimple-8000-automation-azurerm-runbook/test-runbook.png)

9. 테스트 창에서 Runbook의 출력을 검사합니다. 만족스러우면 창을 닫습니다. **게시** 를 클릭하고, 확인 메시지가 표시되면 Runbook을 확인 및 게시합니다.

    ![Runbook 게시를 계속하라는 메시지가 표시된 Edit PowerShell Runbook 창을 보여주는 스크린샷](./media/storsimple-8000-automation-azurerm-runbook/publish-runbook.png)

## <a name="next-steps"></a>다음 단계

[StorSimple 디바이스 관리자 서비스](storsimple-8000-manager-service-administration.md)를 사용하여 StorSimple 디바이스를 관리합니다.