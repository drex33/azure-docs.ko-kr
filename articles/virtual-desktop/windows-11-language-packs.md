---
title: azure 가상 데스크톱에서 Windows 11 Enterprise vm에 언어 팩 설치-azure
description: Azure 가상 데스크톱에 Windows 11 Enterprise vm에 대 한 언어 팩을 설치 하는 방법입니다.
author: Heidilohr
ms.topic: how-to
ms.date: 10/04/2021
ms.author: helohr
manager: femila
ms.openlocfilehash: 365d2821ce0fec8fdf901ad4dc663e8325267803
ms.sourcegitcommit: c27f71f890ecba96b42d58604c556505897a34f3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/05/2021
ms.locfileid: "129539031"
---
# <a name="add-languages-to-a-windows-11-enterprise-image"></a>Windows 11 Enterprise 이미지에 언어 추가

전 세계의 조직 내 사용자가 Azure 가상 데스크톱 배포를 사용할 수 있도록 하는 것이 중요 합니다. 따라서 vm (가상 컴퓨터)에 사용 하는 Windows 11 Enterprise 이미지를 다양 한 언어 팩을 사용 하 여 사용자 지정할 수 있습니다. Windows 11 부터는 관리자가 아닌 사용자 계정이 표시 언어와 해당 언어 기능을 모두 추가할 수 있습니다. 이 기능은 개인 호스트 풀의 사용자에 대 한 언어 팩을 미리 설치할 필요가 없음을 의미 합니다. 풀링된 호스트 풀의 경우 사용자 지정 이미지에 추가할 언어를 추가 하는 것이 좋습니다. 이 문서의 지침을 사용 하 여 Windows 11 Enterprise의 단일 세션 및 다중 세션 버전을 모두 사용할 수 있습니다.

조직에 여러 언어가 있는 사용자가 포함 된 경우 다음 두 가지 옵션을 사용할 수 있습니다.

- 언어별로 사용자 지정 된 이미지를 사용 하 여 하나의 전용 호스트 풀을 만듭니다.
- 동일한 호스트 풀에서 여러 언어를 사용 하는 여러 사용자가 있습니다.

두 번째 옵션은 리소스 및 비용 측면에서 더 효율적 이지만 몇 가지 추가 단계가 필요 합니다. 다행히이 문서에서는 모든 언어 및 지역화 요구 사항을 사용자에 게 적용할 수 있는 이미지를 빌드하는 방법을 안내 합니다.

## <a name="requirements"></a>요구 사항

Windows 11 Enterprise VM에 언어를 추가 하려면 먼저 다음 항목을 준비 해야 합니다.

-   Windows 11 Enterprise가 설치 된 Azure VM
-   언어 및 선택적 기능 (LoF) ISO. iso는 [Windows 11 언어 및 선택적 기능 LoF ISO](https://software-download.microsoft.com/download/sg/22000.1.210604-1628.co_release_amd64fre_CLIENT_LOF_PACKAGES_OEM.iso) 에서 다운로드할 수 있습니다.
-   Windows 파일 서버 VM의 Azure Files 공유 또는 파일 공유

>[!NOTE]
>사용자 지정 이미지를 만드는 데 사용할 Azure VM에서 파일 공유 리포지토리에 액세스할 수 있어야 합니다.

## <a name="create-a-content-repository-for-language-packages-and-features-on-demand"></a>언어 패키지 및 주문형 기능에 대한 콘텐츠 리포지토리 만들기

콘텐츠 리포지토리를 만들려면 VM에 언어 및 기능을 추가 하는 데 사용할 수 있습니다.

1. Azure에서 언어를 추가 하려는 VM을 엽니다.

2. VM에 대 한 [요구 사항](#requirements) 에서 다운로드 한 ISO 파일을 열고 탑재 합니다.

3. 파일 공유에 폴더를 만듭니다.

4. ISO에 있는 **LanguagesAndOptionalFeatures** 폴더의 모든 콘텐츠를 만든 폴더로 복사 합니다.

     >[!NOTE]
     > 제한 된 저장소로 작업 하는 경우 탑재 된 "언어 및 선택적 기능" ISO를 리포지토리로 사용할 수 있습니다. 리포지토리를 만드는 방법에 대 한 자세한 내용은 [사용자 지정 패키지 및 언어 팩 리포지토리 빌드](/windows-hardware/manufacture/desktop/languages-overview#build-a-custom-fod-and-language-pack-repository)를 참조 하세요.

     >[!IMPORTANT]
     > 일부 언어에서는 서로 다른 명명 규칙을 따르는 위성 패키지에 포함된 추가 글꼴이 필요합니다. 일본어의 경우 일본어 글꼴 파일 이름에 “Jpan"이 포함됩니다.
     >
     > [!div class="mx-imgBorder"]
     > ![파일 이름에 “Jpan” 언어 태그가 있는 일본어 언어 팩 예제.](media/language-pack-example.png)

5. 사용자 지정 이미지를 빌드하기 위해 사용할 VM에서 읽기 액세스 권한을 갖도록 언어 콘텐츠 리포지토리 공유에 권한을 설정합니다.

## <a name="create-a-custom-windows-11-enterprise-image-manually"></a>수동으로 사용자 지정 Windows 11 Enterprise 이미지 만들기

다음 단계를 수행 하 여 사용자 지정 이미지를 만들 수 있습니다.

1. azure VM을 배포 하 고 azure 갤러리로 이동 하 여 사용 중인 Windows 11 Enterprise의 현재 버전을 선택 합니다.

2. VM을 배포한 후 RDP를 로컬 관리자로 사용하여 연결합니다.

3. [언어 패키지 및 주문형 기능에 대 한 콘텐츠 리포지토리 만들기](#create-a-content-repository-for-language-packages-and-features-on-demand) 에서 만든 파일 공유 저장소에 커넥트 하 여 문자 드라이브 (예: E 드라이브)에 탑재 합니다.

4. 관리자 권한 powershell 세션에서 다음 powershell 스크립트를 실행 하 여 Windows 11 Enterprise에 언어 팩과 위성 패키지를 설치 합니다.
   
   ```powershell
   ########################################################
   ## Add Languages to running Windows Image for Capture##
   ########################################################
   ##Disable Language Pack Cleanup##
   Disable-ScheduledTask -TaskPath "\Microsoft\Windows\AppxDeploymentClient\" -TaskName "Pre-staged app cleanup"
   Disable-ScheduledTask -TaskPath "\Microsoft\Windows\MUI\" -TaskName "LPRemove"
   Disable-ScheduledTask -TaskPath "\Microsoft\Windows\LanguageComponentsInstaller" -TaskName "Uninstallation"
   reg add "HKEY_LOCAL_MACHINE\SOFTWARE\Policies\Microsoft\Control Panel\International" /v "BlockCleanupOfUnusedPreinstalledLangPacks" /t REG_DWORD /d 1 /f

   ##Set Language Pack Content Stores##
   $LIPContent = "E:"

   ##Set Path of CSV File##
   $CSVFile = "Windows-10-1809-FOD-to-LP-Mapping-Table.csv"
   $filePath = (Get-Location).Path + "/$CSVFile"

   ##Import Necesarry CSV File##
   $FODList = Import-Csv -Path $filePath -Delimiter ";"

   ##Set Language (Target)##
   $targetLanguage = "es-es"

   $sourceLanguage = (($FODList | Where-Object {$_.'Target Lang' -eq $targetLanguage}) | Where-Object {$_.'Source Lang' -ne $targetLanguage} | Select-Object -Property 'Source Lang' -Unique).'Source Lang'
   if(!($sourceLanguage)){
       $sourceLanguage = $targetLanguage
   }

   $langGroup = (($FODList | Where-Object {$_.'Target Lang' -eq $targetLanguage}) | Where-Object {$_.'Lang Group:' -ne ""} | Select-Object -Property 'Lang Group:' -Unique).'Lang Group:'

   ##List of additional features to be installed##
   $additionalFODList = @(
       "$LIPContent\Microsoft-Windows-NetFx3-OnDemand-Package~31bf3856ad364e35~amd64~~.cab", 
       "$LIPContent\Microsoft-Windows-MSPaint-FoD-Package~31bf3856ad364e35~amd64~$sourceLanguage~.cab",
       "$LIPContent\Microsoft-Windows-SnippingTool-FoD-Package~31bf3856ad364e35~amd64~$sourceLanguage~.cab",
       "$LIPContent\Microsoft-Windows-Lip-Language_x64_$sourceLanguage.cab" ##only if applicable##
   )
   
   $additionalCapabilityList = @(
    "Language.Basic~~~$sourceLanguage~0.0.1.0",
    "Language.Handwriting~~~$sourceLanguage~0.0.1.0",
    "Language.OCR~~~$sourceLanguage~0.0.1.0",
    "Language.Speech~~~$sourceLanguage~0.0.1.0",
    "Language.TextToSpeech~~~$sourceLanguage~0.0.1.0"
    )

    ##Install all FODs or fonts from the CSV file###
    Dism /Online /Add-Package /PackagePath:$LIPContent\Microsoft-Windows-Client-Language-Pack_x64_$sourceLanguage.cab
    Dism /Online /Add-Package /PackagePath:$LIPContent\Microsoft-Windows-Lip-Language-Pack_x64_$sourceLanguage.cab
    foreach($capability in $additionalCapabilityList){
       Dism /Online /Add-Capability /CapabilityName:$capability /Source:$LIPContent
    }

    foreach($feature in $additionalFODList){
    Dism /Online /Add-Package /PackagePath:$feature
    }

    if($langGroup){
    Dism /Online /Add-Capability /CapabilityName:Language.Fonts.$langGroup~~~und-$langGroup~0.0.1.0 
    }

    ##Add installed language to language list##
    $LanguageList = Get-WinUserLanguageList
    $LanguageList.Add("$targetlanguage")
    Set-WinUserLanguageList $LanguageList -force
    ```

    >[!NOTE]
    >이 예제 스크립트는 스페인어 (es) 언어 코드를 사용 합니다. 다른 언어에 적합 한 파일을 자동으로 설치 하려면 *$targetLanguage* 매개 변수를 올바른 언어 코드로 변경 합니다. 언어 코드 목록은 [Windows에 사용 가능한 언어 팩](/windows-hardware/manufacture/desktop/available-language-packs-for-windows)을 참조 하세요.

    설치 해야 하는 언어의 수에 따라 스크립트를 완료 하는 데 시간이 걸릴 수 있습니다. 다른 *$targetLanguage* 매개 변수를 사용 하 여 스크립트를 다시 실행 하 여 초기 설치 후 추가 언어를 설치할 수도 있습니다.

5. 적절 한 설치 파일을 자동으로 선택 하려면 [사용 가능한 Windows 10 1809 언어와 기능을 요청](https://download.microsoft.com/download/7/6/0/7600F9DC-C296-4CF8-B92A-2D85BAFBD5D2/Windows-10-1809-FOD-to-LP-Mapping-Table.xlsx ) 하는 테이블을 CSV 파일로 다운로드 하 고 저장 한 다음 PowerShell 스크립트와 동일한 폴더에 저장 합니다.

6. 스크립트 실행이 완료되었으면 **시작** > **설정** > **시간 및 언어** > **언어** 로 이동하여 언어 팩이 올바르게 설치되었는지 확인합니다. 언어 파일이 있으면 모든 설정이 완료된 것입니다.

7. 마지막으로, 언어를 설치 하는 동안 VM이 인터넷에 연결 된 경우에는 정리 프로세스를 실행 하 여 불필요 한 언어 경험 팩을 제거 해야 합니다. 파일을 정리 하려면 다음 명령을 실행 합니다.

    ```powershell
    ##Cleanup to prepare sysprep##
    Remove-AppxPackage -Package Microsoft.LanguageExperiencePackes-ES_22000.8.13.0_neutral__8wekyb3d8bbwe

    Remove-AppxPackage -Package Microsoft.OneDriveSync_22000.8.13.0_neutral__8wekyb3d8bbwe
    ```

    다른 언어 팩을 정리 하려면 "es"를 다른 언어 코드로 바꿉니다.

8. 정리가 완료 되 면 공유의 연결을 끊습니다. 

## <a name="finish-customizing-your-image"></a>이미지 사용자 지정 완료

언어 팩을 설치한 후 사용자 지정된 이미지에 추가하려는 다른 소프트웨어를 설치할 수 있습니다.

이미지 사용자 지정을 완료했으면 시스템 준비 도구(sysprep)를 실행해야 합니다.

sysprep을 실행하려면 다음을 수행합니다.

1. 상승된 권한으로 명령 프롬프트를 열고 다음 명령을 실행하여 이미지를 일반화합니다.  
   
     ```cmd
     C:\Windows\System32\Sysprep\sysprep.exe /oobe /generalize /shutdown
     ```

2. 문제가 발생 하는 경우  **Windows**  >  **System32**  >  **Sysprep**  >  **Panther** 의 C 드라이브에 있는 setuperr.log 파일을 확인 합니다. 그런 다음 [Microsoft Store 앱과 함께 Sysprep 실패](/troubleshoot/windows-client/deployment/sysprep-fails-remove-or-update-store-apps) 의 지침에 따라 설치 문제를 해결 합니다.

3. 설치가 완료 되 면 [Azure에서 일반화 된 vm의 관리 되는 이미지 만들기](../virtual-machines/windows/capture-image-resource.md)의 지침에 따라 VM을 중지 하 고 관리 되는 이미지에서 캡처합니다.

4. 이제 사용자 지정 이미지를 사용 하 여 Azure 가상 데스크톱 호스트 풀을 배포할 수 있습니다. 호스트 풀 배포 방법을 알아보려면 [자습서: Azure Portal을 사용하여 호스트 풀 만들기](create-host-pools-azure-marketplace.md)를 참조하세요.

>[!NOTE]
>사용자가 표시 언어를 변경 하는 경우 Azure 가상 데스크톱 세션에서 로그 아웃 한 후 다시 로그인 해야 합니다. 시작 메뉴에서 로그 아웃 해야 합니다.

## <a name="next-steps"></a>다음 단계

[Windows 10 다중 세션 이미지에 언어 팩 추가](language-packs.md)에서 Windows 10 다중 세션 vm에 대 한 언어 패키지를 설치 하는 방법에 대해 알아봅니다.

알려진 문제 목록은 [Windows 10 언어 추가: 알려진 문제](/windows-hardware/manufacture/desktop/language-packs-known-issue)를 참조 하세요.
