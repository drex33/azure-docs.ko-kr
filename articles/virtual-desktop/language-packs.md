---
title: Azure Virtual Desktop에서 Windows 10 VM에 언어 팩 설치 - Azure
description: Azure Virtual Desktop에서 Windows 10 다중 세션 VM을 위한 언어 팩을 설치하는 방법입니다.
author: Heidilohr
ms.topic: how-to
ms.date: 12/03/2020
ms.author: helohr
manager: femila
ms.openlocfilehash: e4df2c2bbe4806ae39d23373c36890cd92125bea
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/17/2021
ms.locfileid: "132704195"
---
# <a name="add-language-packs-to-a-windows-10-multi-session-image"></a>Windows 10 다중 세션 이미지에 언어 팩 추가

Azure Virtual Desktop은 사용자가 언제 어디에서든 배포할 수 있는 서비스입니다. 따라서 Windows 10 Enterprise 다중 세션 이미지에 표시되는 언어를 사용자가 사용자 지정할 수 있어야 합니다.

사용자의 언어 요구를 수용할 수 있는 두 가지 방법이 있습니다.

- 각 언어에 대해 사용자 정의된 이미지로 전용 호스트 풀을 빌드합니다.
- 언어 및 지역화 요구 사항이 서로 다른 사용자를 동일한 호스트 풀에 배치하지만, 무엇이든 필요한 언어를 선택할 수 있도록 이미지를 사용자 지정합니다.

두 번째 방법이 더 효과적이고, 비용 효율적입니다. 하지만 각의 요구에 가장 적합한 방법은 사용자의 결정에 따라 달라집니다. 이 문서에서는 이미지에 대해 언어를 사용자 지정하는 방법을 보여줍니다.

## <a name="prerequisites"></a>사전 요구 사항

여러 언어를 추가하기 위해 Windows 10 Enterprise 다중 세션 이미지를 사용자 지정하기 위해서는 다음 항목이 필요합니다.

- Windows 10 Enterprise 다중 세션 버전 1903 이상을 포함하는 Azure VM(가상 머신)

- 언어 ISO, FOD(주문형 기능) 디스크 1 및 이미지에 사용되는 OS 버전의 수신함 앱 ISO. 에서 다운로드할 수 있습니다.
     
     - 언어 ISO:
        - [Windows 10, 버전 1903 또는 1909 언어 팩 ISO](https://software-download.microsoft.com/download/pr/18362.1.190318-1202.19h1_release_CLIENTLANGPACKDVD_OEM_MULTI.iso)
        - [Windows 10, 버전 2004, 20H2 또는 21H1 언어 팩 ISO](https://software-download.microsoft.com/download/pr/19041.1.191206-1406.vb_release_CLIENTLANGPACKDVD_OEM_MULTI.iso)

     - FOD 디스크 1 ISO:
        - [Windows 10, 버전 1903 또는 1909 FOD 디스크 1 ISO](https://software-download.microsoft.com/download/pr/18362.1.190318-1202.19h1_release_amd64fre_FOD-PACKAGES_OEM_PT1_amd64fre_MULTI.iso)
        - [Windows 10, 버전 2004, 20H2 또는 21H1 FD 디스크 1 ISO](https://software-download.microsoft.com/download/pr/19041.1.191206-1406.vb_release_amd64fre_FOD-PACKAGES_OEM_PT1_amd64fre_MULTI.iso)
        
     - 수신함 앱 ISO:
        - [Windows 10, 버전 1903 또는 1909 수신함 앱 ISO](https://software-download.microsoft.com/download/pr/18362.1.190318-1202.19h1_release_amd64fre_InboxApps.iso)
        - [Windows 10, 버전 2004 수신함 앱 ISO](https://software-download.microsoft.com/download/pr/19041.1.191206-1406.vb_release_amd64fre_InboxApps.iso)
        - [Windows 10, 버전 20H2 수신함 앱 ISO](https://software-download.microsoft.com/download/pr/19041.508.200905-1327.vb_release_svc_prod1_amd64fre_InboxApps.iso)
        - [Windows 10, 버전 21H1 수신함 앱 ISO](https://software-download.microsoft.com/download/sg/19041.928.210407-2138.vb_release_svc_prod1_amd64fre_InboxApps.iso)
     
     - LXP(Local Experience Pack) ISO 파일을 사용하여 이미지를 지역화하는 경우, 최상의 언어 경험을 위해 적합한 LXP ISO도 다운로드해야 합니다.
        - Windows 10, 버전 1903 또는 1909를 사용하는 경우:
          - [Windows 10, 버전 1903 또는 1909 LXP ISO](https://software-download.microsoft.com/download/pr/Win_10_1903_32_64_ARM64_MultiLng_LngPkAll_LXP_ONLY.iso)
        - Windows 10, 버전 2004, 20H2 또는 21H1을 사용하는 경우 [Windows 10에서 언어 추가: 알려진 문제](/windows-hardware/manufacture/desktop/language-packs-known-issue)의 정보에 따라 다음 LXP ISO 중 적합한 항목을 확인합니다.
          - [Windows 10, 버전 2004, 20H2 또는 21H1 **10C** LXP ISO](https://software-download.microsoft.com/download/pr/LanguageExperiencePack.2010C.iso)
          - [Windows 10, 버전 2004, 20H2 또는 21H1 **11C** LXP ISO](https://software-download.microsoft.com/download/pr/LanguageExperiencePack.2011C.iso)
          - [Windows 10, 버전 2004, 20H2 또는 21H1 **1C** LXP ISO](https://software-download.microsoft.com/download/pr/LanguageExperiencePack.2101C.iso)
          - [Windows 10, 버전 2004, 20H2 또는 21H1 **2C** LXP ISO](https://software-download.microsoft.com/download/pr/LanguageExperiencePack.2102C.iso)
          - [Windows 10, 버전 2004, 20H2 또는 21H1 **4B** LXP ISO](https://software-download.microsoft.com/download/sg/LanguageExperiencePack.2104B.iso)
          - [Windows 10, 버전 2004, 20H2 또는 21H1 **5C** LXP ISO](https://software-download.microsoft.com/download/sg/LanguageExperiencePack.2105C.iso)
          - [Windows 10, 버전 2004, 20H2 또는 21H1 **7C** LXP ISO](https://software-download.microsoft.com/download/pr/LanguageExperiencePack.2107C.iso)
          - [Windows 10, 버전 2004, 20H2 또는 21H1 **9C** LXP ISO](https://software-download.microsoft.com/download/db/LanguageExperiencePack.2109C.iso)
          - [Windows 10, 버전 2004, 20H2 또는 21H1 **10C** LXP ISO](https://software-download.microsoft.com/download/sg/LanguageExperiencePack.2110C.iso)

- Azure Files 공유 또는 Windows 파일 서버 가상 머신의 파일 공유

>[!NOTE]
>사용자 지정 이미지를 만들기 위해 사용하려는 Azure VM에서 파일 공유(리포지토리)에 액세스할 수 있어야 합니다.

## <a name="create-a-content-repository-for-language-packages-and-features-on-demand"></a>언어 패키지 및 주문형 기능에 대한 콘텐츠 리포지토리 만들기

언어 패키지 및 FOD를 위한 콘텐츠 리포지토리와 수신함 앱 패키지를 위한 리포지토리를 만들려면 다음을 수행합니다.

1. Azure VM의 [필수 구성 요소](#prerequisites)에 있는 링크에서 Windows 10 다중 언어 ISO, FOD 및 Windows 10 Enterprise 다중 세션을 위한 수신함 앱, 버전 1903/1909 및 2004 이미지를 다운로드합니다.

2. VM에서 ISO 파일을 열고 탑재합니다.

3. 언어 팩 ISO로 이동하고 **LocalExperiencePacks** 및 **x64\\langpacks** 폴더에서 콘텐츠를 복사한 후 이를 파일 공유에 붙여넣습니다.

4. **FOD ISO 파일** 로 이동하고 모든 콘텐츠를 복사한 후 파일 공유에 붙여넣습니다.
5. 수신함 앱 ISO에서 **amd64fre** 폴더로 이동하고 준비한 수신함 앱의 리포지토리에 있는 콘텐츠를 복사합니다.

     >[!NOTE]
     > 제한된 스토리지로 작업하는 경우, 사용자에게 필요한 언어에 대해서만 파일을 복사합니다. 파일 이름에서 언어 코드를 보고 파일을 구분할 수 있습니다. 예를 들어 프랑스어 파일에는 해당 이름에 "fr-FR" 코드가 포함됩니다. 사용 가능한 모든 언어에 대한 전체 언어 코드 목록은 [Windows에 사용 가능한 언어 팩](/windows-hardware/manufacture/desktop/available-language-packs-for-windows)을 참조하세요.

     >[!IMPORTANT]
     > 일부 언어에서는 서로 다른 명명 규칙을 따르는 위성 패키지에 포함된 추가 글꼴이 필요합니다. 일본어의 경우 일본어 글꼴 파일 이름에 “Jpan"이 포함됩니다.
     >
     > [!div class="mx-imgBorder"]
     > ![파일 이름에 “Jpan” 언어 태그가 있는 일본어 언어 팩 예제.](media/language-pack-example.png)

6. 사용자 지정 이미지를 빌드하기 위해 사용할 VM에서 읽기 액세스 권한을 갖도록 언어 콘텐츠 리포지토리 공유에 권한을 설정합니다.

## <a name="create-a-custom-windows-10-enterprise-multi-session-image-manually"></a>수동으로 사용자 지정 Windows 10 Enterprise 다중 세션 이미지 만들기

수동으로 사용자 지정 Windows 10 Enterprise 다중 세션 이미지를 만들려면 다음을 수행합니다.

1. Azure VM을 배포한 후 Azure Gallery로 이동하고 사용 중인 Windows 10 Enterprise 다중 세션의 현재 버전을 선택합니다.
2. VM을 배포한 후 RDP를 로컬 관리자로 사용하여 연결합니다.
3. VM에 모든 최신 Windows 업데이트가 포함되었는지 확인합니다. 필요한 경우 업데이트를 다운로드하고 VM을 다시 시작합니다.
4. 언어 패키지, FOD, 수신함 앱 파일 공유 리포지토리에 연결하고 이를 문자 드라이브(예: E 드라이브)에 탑재합니다.

## <a name="create-a-custom-windows-10-enterprise-multi-session-image-automatically"></a>자동으로 사용자 지정 Windows 10 Enterprise 다중 세션 이미지 만들기

자동화된 프로세스를 통해 이미지를 설치하고 싶으면 PowerShell에서 스크립트를 설정할 수 있습니다. 다음 스크립트 샘플을 사용하여 Windows 10 Enterprise 다중 세션, 버전 2004에 대한 스페인어(스페인), 프랑스어(프랑스) 및 중국어(PRC) 언어 팩과 위성 패키지를 설치할 수 있습니다. 스크립트는 언어 인터페이스 팩과 모든 필수 위성 패키지를 이미지에 통합합니다. 하지만 다른 언어를 설치하도록 이 스크립트를 수정할 수도 있습니다. 상승된 권한으로 PowerShell 세션에서 스크립트를 실행해야 합니다. 그렇지 않으면 작동하지 않습니다.

```powershell
########################################################
## Add Languages to running Windows Image for Capture##
########################################################

##Disable Language Pack Cleanup##
Disable-ScheduledTask -TaskPath "\Microsoft\Windows\AppxDeploymentClient\" -TaskName "Pre-staged app cleanup"

##Set Language Pack Content Stores##
[string]$LIPContent = "E:"

##Spanish##
Add-AppProvisionedPackage -Online -PackagePath $LIPContent\es-es\LanguageExperiencePack.es-es.Neutral.appx -LicensePath $LIPContent\es-es\License.xml
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-Client-Language-Pack_x64_es-es.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-LanguageFeatures-Basic-es-es-Package~31bf3856ad364e35~amd64~~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-LanguageFeatures-Handwriting-es-es-Package~31bf3856ad364e35~amd64~~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-LanguageFeatures-OCR-es-es-Package~31bf3856ad364e35~amd64~~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-LanguageFeatures-Speech-es-es-Package~31bf3856ad364e35~amd64~~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-LanguageFeatures-TextToSpeech-es-es-Package~31bf3856ad364e35~amd64~~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-NetFx3-OnDemand-Package~31bf3856ad364e35~amd64~es-es~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-InternetExplorer-Optional-Package~31bf3856ad364e35~amd64~es-es~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-MSPaint-FoD-Package~31bf3856ad364e35~amd64~es-es~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-Notepad-FoD-Package~31bf3856ad364e35~amd64~es-es~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-PowerShell-ISE-FOD-Package~31bf3856ad364e35~amd64~es-es~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-Printing-WFS-FoD-Package~31bf3856ad364e35~amd64~es-es~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-StepsRecorder-Package~31bf3856ad364e35~amd64~es-es~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-WordPad-FoD-Package~31bf3856ad364e35~amd64~es-es~.cab
$LanguageList = Get-WinUserLanguageList
$LanguageList.Add("es-es")
Set-WinUserLanguageList $LanguageList -force

##French##
Add-AppProvisionedPackage -Online -PackagePath $LIPContent\fr-fr\LanguageExperiencePack.fr-fr.Neutral.appx -LicensePath $LIPContent\fr-fr\License.xml
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-Client-Language-Pack_x64_fr-fr.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-LanguageFeatures-Basic-fr-fr-Package~31bf3856ad364e35~amd64~~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-LanguageFeatures-Handwriting-fr-fr-Package~31bf3856ad364e35~amd64~~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-LanguageFeatures-OCR-fr-fr-Package~31bf3856ad364e35~amd64~~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-LanguageFeatures-Speech-fr-fr-Package~31bf3856ad364e35~amd64~~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-LanguageFeatures-TextToSpeech-fr-fr-Package~31bf3856ad364e35~amd64~~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-NetFx3-OnDemand-Package~31bf3856ad364e35~amd64~fr-fr~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-InternetExplorer-Optional-Package~31bf3856ad364e35~amd64~fr-FR~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-MSPaint-FoD-Package~31bf3856ad364e35~amd64~fr-FR~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-Notepad-FoD-Package~31bf3856ad364e35~amd64~fr-FR~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-PowerShell-ISE-FOD-Package~31bf3856ad364e35~amd64~fr-FR~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-Printing-WFS-FoD-Package~31bf3856ad364e35~amd64~fr-FR~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-StepsRecorder-Package~31bf3856ad364e35~amd64~fr-FR~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-WordPad-FoD-Package~31bf3856ad364e35~amd64~fr-FR~.cab
$LanguageList = Get-WinUserLanguageList
$LanguageList.Add("fr-fr")
Set-WinUserLanguageList $LanguageList -force

##Chinese(PRC)##
Add-AppProvisionedPackage -Online -PackagePath $LIPContent\zh-cn\LanguageExperiencePack.zh-cn.Neutral.appx -LicensePath $LIPContent\zh-cn\License.xml
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-Client-Language-Pack_x64_zh-cn.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-LanguageFeatures-Basic-zh-cn-Package~31bf3856ad364e35~amd64~~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-LanguageFeatures-Fonts-Hans-Package~31bf3856ad364e35~amd64~~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-LanguageFeatures-Handwriting-zh-cn-Package~31bf3856ad364e35~amd64~~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-LanguageFeatures-OCR-zh-cn-Package~31bf3856ad364e35~amd64~~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-LanguageFeatures-Speech-zh-cn-Package~31bf3856ad364e35~amd64~~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-LanguageFeatures-TextToSpeech-zh-cn-Package~31bf3856ad364e35~amd64~~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-NetFx3-OnDemand-Package~31bf3856ad364e35~amd64~zh-cn~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-InternetExplorer-Optional-Package~31bf3856ad364e35~amd64~zh-cn~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-MSPaint-FoD-Package~31bf3856ad364e35~amd64~zh-cn~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-Notepad-FoD-Package~31bf3856ad364e35~amd64~zh-cn~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-PowerShell-ISE-FOD-Package~31bf3856ad364e35~amd64~zh-cn~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-Printing-WFS-FoD-Package~31bf3856ad364e35~amd64~zh-cn~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-StepsRecorder-Package~31bf3856ad364e35~amd64~zh-cn~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-WordPad-FoD-Package~31bf3856ad364e35~amd64~zh-cn~.cab
$LanguageList = Get-WinUserLanguageList
$LanguageList.Add("zh-cn")
Set-WinUserLanguageList $LanguageList -force
```

설치할 언어 수에 따라 스크립트를 실행하는 데 시간이 오래 걸릴 수 있습니다.

스크립트 실행이 완료되었으면 **시작** > **설정** > **시간 및 언어** > **언어** 로 이동하여 언어 팩이 올바르게 설치되었는지 확인합니다. 언어 파일이 있으면 모든 설정이 완료된 것입니다.

Windows 이미지에 추가 언어를 추가한 후 추가된 언어를 지원하기 위해서는 수신함 앱도 업데이트해야 합니다. 이 작업을 수행하려면 수신함 앱 ISO의 콘텐츠를 사용하여 사전 설치된 앱을 새로 고치면 됩니다.
VM이 인터넷에 액세스할 수 없는 환경에서 이 새로 고침을 수행하려면 다음 PowerShell 스크립트 템플릿을 사용하여 프로세스를 자동화하고 설치된 수신함 앱 버전만 업데이트할 수 있습니다.

```powershell
#########################################
## Update Inbox Apps for Multi Language##
#########################################
##Set Inbox App Package Content Stores##
[string] $AppsContent = "F:\"

##Update installed Inbox Store Apps##
foreach ($App in (Get-AppxProvisionedPackage -Online)) {
    $AppPath = $AppsContent + $App.DisplayName + '_' + $App.PublisherId
    Write-Host "Handling $AppPath"
    $licFile = Get-Item $AppPath*.xml
    if ($licFile.Count) {
        $lic = $true
        $licFilePath = $licFile.FullName
    } else {
        $lic = $false
    }
    $appxFile = Get-Item $AppPath*.appx*
    if ($appxFile.Count) {
        $appxFilePath = $appxFile.FullName
        if ($lic) {
            Add-AppxProvisionedPackage -Online -PackagePath $appxFilePath -LicensePath $licFilePath 
        } else {
            Add-AppxProvisionedPackage -Online -PackagePath $appxFilePath -skiplicense
        }
    }
}

```

>[!IMPORTANT]
>ISO에 포함된 수신함 앱은 사전 설치된 Windows 앱의 최신 버전이 아닙니다. 모든 앱의 최신 버전을 가져오려면 Windows 스토어 앱을 사용하여 앱을 업데이트하고, 추가 언어를 설치한 후 업데이트에 대해 수동 검색을 수행해야 합니다.

완료되었으면 공유 연결을 해제합니다.

## <a name="finish-customizing-your-image"></a>이미지 사용자 지정 완료

언어 팩을 설치한 후 사용자 지정된 이미지에 추가하려는 다른 소프트웨어를 설치할 수 있습니다.

이미지 사용자 지정을 완료했으면 시스템 준비 도구(sysprep)를 실행해야 합니다.

sysprep을 실행하려면 다음을 수행합니다.

1. 상승된 권한으로 명령 프롬프트를 열고 다음 명령을 실행하여 이미지를 일반화합니다.  
   
     ```cmd
     C:\Windows\System32\Sysprep\sysprep.exe /oobe /generalize /shutdown
     ```

2. VM을 중지한 후 [Azure에서 일반화된 VM의 관리되는 이미지 만들기](../virtual-machines/windows/capture-image-resource.md)의 지침에 따라 관리되는 이미지로 캡처합니다.

3. 이제 사용자 지정된 이미지를 사용하여 Azure Virtual Desktop 호스트 풀을 배포할 수 있습니다. 호스트 풀 배포 방법을 알아보려면 [자습서: Azure Portal을 사용하여 호스트 풀 만들기](create-host-pools-azure-marketplace.md)를 참조하세요.

## <a name="enable-languages-in-windows-settings-app"></a>Windows 설정 앱에서 언어 사용

마지막으로 호스트 풀을 배포한 후 설정 메뉴에서 원하는 언어를 선택할 수 있도록 각 사용자의 언어 목록에 언어를 추가해야 합니다.

사용자가 설치된 언어를 선택할 수 있도록 하려면, 사용자로 로그인한 후, 다음 PowerShell cmdlet을 실행하여 설치된 언어 팩을 언어 메뉴에 추가합니다. 또한 사용자가 세션에 로그인할 때 활성화되는 자동화된 작업 또는 로그온 스크립트로 이 스크립트를 설정할 수 있습니다.

```powershell
$LanguageList = Get-WinUserLanguageList
$LanguageList.Add("es-es")
$LanguageList.Add("fr-fr")
$LanguageList.Add("zh-cn")
Set-WinUserLanguageList $LanguageList -force
```

사용자가 언어 설정을 변경한 후에는 변경 사항이 적용되도록 Azure Virtual Desktop 세션에서 로그아웃하고 다시 로그인해야 합니다. 

## <a name="next-steps"></a>다음 단계

언어 팩의 알려진 문제에 대해 알고 싶으면 [Windows 10, 버전 1803 및 이후 버전에서 언어 팩 추가: 알려진 문제](/windows-hardware/manufacture/desktop/language-packs-known-issue)를 참조하세요.

Windows 10 Enterprise 다중 세션에 대해 다른 질문이 있으면 [FAQ](windows-10-multisession-faq.yml)를 확인하세요.
