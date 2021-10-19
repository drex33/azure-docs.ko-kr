---
title: Windows 템플릿 컴퓨터 설정 가이드 | Microsoft Docs
description: 랩 서비스에서 Windows 템플릿 컴퓨터를 준비하는 일반적인 단계입니다.  이러한 단계는 Windows 업데이트 일정 설정, OneDrive 설치 및 Office 설치를 포함합니다.
author: EMaher
ms.topic: how-to
ms.date: 06/26/2020
ms.author: enewman
ms.openlocfilehash: 9b84823e356f59cda00b80052add584153a4aad6
ms.sourcegitcommit: 92889674b93087ab7d573622e9587d0937233aa2
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/19/2021
ms.locfileid: "130180118"
---
# <a name="guide-to-setting-up-a-windows-template-machine-in-azure-lab-services"></a>Azure Lab Services에서 Windows 템플릿 컴퓨터를 설정하는 방법에 대한 가이드

Azure Lab Services에 대한 Windows 10 템플릿 컴퓨터를 설정하는 경우 고려해야 할 몇 가지 모범 사례 및 팁이 있습니다. 아래 구성 단계는 모두 선택 사항입니다.  그러나 이러한 준비 단계는 학생의 생산성을 높이고, 클래스 시간 중단을 최소화하고, 최신 기술을 사용하고 있는지 확인하는데 도움이 될 수 있습니다.

>[!IMPORTANT]
>이 문서에는 컴퓨터 템플릿 수정 프로세스를 간소화하는 PowerShell 코드 조각이 포함되어 있습니다.  표시되는 모든 PowerShell 스크립트에 대해 Windows PowerShell에서 관리자 권한으로 실행하는 것이 좋습니다. Windows 10에서는 시작 메뉴를 마우스 오른쪽 단추로 클릭한 다음 "Windows PowerShell(관리자)"을 선택하는 것이 빠른 방법입니다.

## <a name="install-and-configure-onedrive"></a>OneDrive 설치 및 구성

가상 컴퓨터를 다시 설정하는 경우 학생 데이터가 손실되지 않도록 하려면 데이터를 클라우드로 다시 가져오는 것이 좋습니다.  Microsoft OneDrive는 학생들이 자신의 데이터를 보호하도록 도울 수 있습니다.  

### <a name="install-onedrive"></a>OneDrive 설치

OneDrive를 수동으로 다운로드하여 설치하려면 [OneDrive](https://onedrive.live.com/about/download/) 또는 [비즈니스용 OneDrive](https://onedrive.live.com/about/business/) 다운로드 페이지를 참조하세요.

다음 PowerShell 스크립트를 사용할 수도 있습니다.  그러면 OneDrive의 최신 버전을 자동으로 다운로드하여 설치합니다.  OneDrive 클라이언트가 설치되면 설치 프로그램을 실행합니다.  이 예제에서는 `/allUsers` 스위치를 사용하여 컴퓨터의 모든 사용자에 대해 OneDrive를 설치합니다. 또한 `/silent` 스위치를 사용하여 OneDrive를 자동으로 설치합니다.

```powershell
Write-Host "Downloading OneDrive Client..."
$DownloadPath = "$env:USERPROFILE/Downloads/OneDriveSetup.exe"
if((Test-Path $DownloadPath) -eq $False )
{
    Write-Host "Downloading OneDrive..."
    $web = new-object System.Net.WebClient
    $web.DownloadFile("https://go.microsoft.com/fwlink/p/?LinkId=248256",$DownloadPath)
} else {
    Write-Host "OneDrive installer already exists at " $DownloadPath
}

Write-Host "Installing OneDrive..."
& $env:USERPROFILE/Downloads/OneDriveSetup.exe /allUsers /silent
```

### <a name="onedrive-customizations"></a>OneDrive 사용자 지정

[OneDrive에 대해 수행할 수 있는 사용자 지정](/onedrive/use-group-policy)이 많이 있습니다. 몇 가지 일반적인 사용자 지정 항목을 살펴 보겠습니다.

#### <a name="silently-move-windows-known-folders-to-onedrive"></a>알려진 Windows 폴더를 OneDrive로 자동 이동

문서, 다운로드 및 사진과 같은 폴더는 학생 파일을 저장하는 데 주로 사용됩니다. 이러한 폴더가 OneDrive에 백업되도록 하려면 이러한 폴더를 OneDrive로 이동하는 것이 좋습니다.

Active Directory를 사용하지 않는 컴퓨터에 있는 경우 사용자가 OneDrive에 인증되면 해당 폴더를 OneDrive로 수동으로 이동할 수 있습니다.

1. 파일 탐색기를 열기
2. 문서, 다운로드 또는 그림 폴더를 마우스 오른쪽 단추로 클릭합니다.
3. 속성으로 이동 > 위치.  폴더를 OneDrive 디렉터리의 새 폴더로 이동합니다.

가상 컴퓨터가 Active Directory에 연결된 경우 학생에게 알려진 폴더를 OneDrive로 자동으로 이동하라는 메시지를 표시하도록 템플릿 컴퓨터를 설정할 수 있습니다.  

먼저 조직 ID를 검색해야 합니다.  자세한 지침은 [Microsoft 365 조직 ID 찾기](/onedrive/find-your-office-365-tenant-id)를 참조하세요.  다음 PowerShell을 사용하여 조직 ID를 가져올 수도 있습니다.

```powershell
Install-Module MSOnline -Confirm
Connect-MsolService
$officeTenantID = Get-MSOLCompanyInformation |
    Select-Object -expand objectID |
    Select-Object -expand Guid
```

조직 ID가 있으면 다음 PowerShell을 사용하여 알려진 폴더를 OneDrive로 이동하라는 메시지를 표시하도록 설정합니다.

```powershell
if ($officeTenantID -eq $null)
{
        Write-Error "Variable `$officeTenantId must be set to your Office Tenant Id before continuing."
}
New-Item -Path "HKLM:\SOFTWARE\Policies\Microsoft\OneDrive"
New-ItemProperty -Path "HKLM:\SOFTWARE\Policies\Microsoft\OneDrive"
    -Name "KFMSilentOptIn" -Value $officeTenantID -PropertyType STRING
```

### <a name="use-onedrive-files-on-demand"></a>OneDrive 요청 기반 파일 관리 사용

학생들이 OneDrive 계정 내에 많은 파일을 포함할 수 있습니다. 컴퓨터의 공간을 절약하고 다운로드 시간을 줄일 수 있도록 학생의 OneDrive 계정에 저장된 모든 파일을 요청 시로 설정하는 것이 좋습니다.  요청 시 파일은 사용자가 파일에 액세스한 후에만 다운로드됩니다.

```powershell
New-Item -Path "HKLM:\SOFTWARE\Policies\Microsoft\OneDrive" -Force
New-ItemProperty -Path "HKLM:\SOFTWARE\Policies\Microsoft\OneDrive"
    -Name "FilesOnDemandEnabled" -Value "00000001" -PropertyType DWORD
```

### <a name="silently-sign-in-users-to-onedrive"></a>OneDrive 자동 로그인 사용자

OneDrive는 로그온한 사용자의 Windows 자격 증명을 사용하여 자동으로 로그인하도록 설정할 수 있습니다.  자동 로그인은 학생이 학교 자격 증명을 사용하여 로그인하는 클래스에 유용합니다.

```powershell
New-Item -Path "HKLM:\SOFTWARE\Policies\Microsoft\OneDrive"
New-ItemProperty -Path "HKLM:\SOFTWARE\Policies\Microsoft\OneDrive"
    -Name "SilentAccountConfig" -Value "00000001" -PropertyType DWORD
```

### <a name="disable-the-tutorial-that-appears-at-the-end-of-onedrive-setup"></a>OneDrive 설정 끝에 표시되는 자습서를 사용하지 않음으로 설정

이 설정을 사용하면 OneDrive 설정 종료 시 웹 브라우저에서 자습서를 시작할 수 없습니다.

```powershell
New-Item -Path "HKLM:\SOFTWARE\Policies\Microsoft\OneDrive" -Force
New-ItemProperty -Path "HKLM:\SOFTWARE\Policies\Microsoft\OneDrive"
    -Name "DisableTutorial" -Value "00000001" -PropertyType DWORD -Force
```

### <a name="set-the-maximum-size-of-a-file-that-to-be-download-automatically"></a>자동으로 다운로드되는 파일의 최대 크기를 설정합니다

이 설정은 요청 시를 사용하도록 OneDrive 파일을 설정하지 않은 디바이스에서 Windows 자격증명을 사용하여 고객과 동기화한 OneDrive 자동 로그인 사용자와 함께 사용된다. 지정된 임계값(MB) 보다 큰 OneDrive를 사용하는 모든 사용자에게는 OneDrive 동기화 클라이언트(OneDrive.exe)에서 파일을 다운로드하기 전에 동기화 할 폴더를 선택하라는 메시지가 표시됩니다.  이 예제에서 "1111-2222-3333-4444"은 조직 ID이고 0005000은 5GB의 임계값을 설정합니다.

```powershell
New-Item -Path "HKLM:\SOFTWARE\Policies\Microsoft\OneDrive"
New-Item -Path "HKLM:\SOFTWARE\Policies\Microsoft\OneDrive\DiskSpaceCheckThresholdMB"
New-ItemProperty -Path "HKLM:\SOFTWARE\Policies\Microsoft\OneDrive\DiskSpaceCheckThresholdMB"
    -Name "1111-2222-3333-4444" -Value "0005000" -PropertyType DWORD
```

## <a name="install-and-configure-microsoft-365"></a>Microsoft 365 설치 및 구성

### <a name="install-microsoft-365"></a>Microsoft 365 설치

템플릿 컴퓨터에 Office가 필요한 경우 [ODT(Office 배포 도구)](https://www.microsoft.com/download/details.aspx?id=49117)를 통해 office를 설치하는 것이 좋습니다. [Microsoft 365 앱 관리 센터](https://config.office.com/)를 사용하여 재사용 가능한 구성 파일을 만들어 선택할 아키텍처, Office에서 필요한 기능 및 업데이트 빈도를 선택해야 합니다.

1. [Microsoft 365 앱 관리 센터](https://config.office.com/) 로 이동하여 사용자 고유의 구성 파일을 다운로드합니다.
2. [ODT(Office 배포 도구)](https://www.microsoft.com/download/details.aspx?id=49117)를 다운로드합니다.  다운로드한 파일은 `setup.exe`입니다.
3. `setup.exe /download configuration.xml`을 실행하여 Office 구성 요소를 다운로드합니다.
4. `setup.exe /configure configuration.xml`을 실행하여 Office 구성 요소를 실행합니다.

### <a name="change-the-microsoft-365-update-channel"></a>Microsoft 365 업데이트 채널 변경

Office 구성 도구를 사용하여 Office에서 업데이트를 수신하는 빈도를 설정할 수 있습니다. 그러나 설치 후 Office에서 업데이트를 수신하는 빈도를 수정해야 하는 경우에는 업데이트 채널 URL을 변경할 수 있습니다. 채널 URL 주소 업데이트는 조직의 디바이스에 대한 [Microsoft 365 앱 업데이트 채널 변경](/deployoffice/change-update-channels)에서 찾을 수 있습니다. 아래 예제에서는 월별 업데이트 채널을 사용하도록 Microsoft 365를 설정하는 방법을 보여 줍니다.

```powershell
# Update to the Microsoft 365 Monthly Channel
Set-ItemProperty
    -Path "HKLM:\SOFTWARE\Microsoft\Office\ClickToRun\Configuration\CDNBaseUrl"
    -Name "CDNBaseUrl"
    -Value "http://officecdn.microsoft.com/pr/492350f6-3a01-4f97-b9c0-c7c6ddf67d60"
```

## <a name="install-and-configure-updates"></a>업데이트를 설치 및 구성

### <a name="install-the-latest-windows-updates"></a>최신 Windows 업데이트 설치

템플릿 VM을 게시하기 전에 보안을 위해 템플릿 컴퓨터에 최신 Microsoft 업데이트를 설치하는 것이 좋습니다.  또한 예기치 않은 시간에 업데이트가 실행될 때 학생의 작업이 중단되는 것을 잠재적으로 방지할 수 있습니다.

1. 시작 메뉴에서 **설정** 시작
2. **업데이트** 및 보안을 클릭
3. **업데이트 확인** 을 클릭
4. 업데이트를 다운로드 및 설치합니다.

PowerShell을 사용하여 템플릿 컴퓨터를 업데이트할 수도 있습니다.

```powershell
Set-ExecutionPolicy Bypass -Scope Process -Force
Install-PackageProvider -Name NuGet -MinimumVersion 2.8.5.201 -Confirm
Install-Module PSWindowsUpdate -Confirm
Install-WindowsUpdate -MicrosoftUpdate
Set-ExecutionPolicy default -Force
```

>[!NOTE]
>일부 업데이트의 경우 컴퓨터를 다시 시작해야 할 수 있습니다.  다시 부팅해야 하는 경우 메시지가 표시됩니다.

### <a name="install-the-latest-updates-for-microsoft-store-apps"></a>Microsoft Store 앱에 대한 최신 업데이트 설치

모든 Microsoft Store 앱을 최신 버전으로 업데이트하는 것이 좋습니다.  Microsoft Store에서 애플리케이션을 수동으로 업데이트하는 지침은 다음과 같습니다.  

1. **Microsoft Store** 애플리케이션을 시작합니다.
2. 애플리케이션의 위쪽 모퉁이에 있는 사용자 사진 옆에 있는 줄임표(...)를 클릭합니다.
3. 드롭다운 메뉴에서 **다운로드** 및 업데이트를 선택합니다.
4. **업데이트 가져오기** 단추를 클릭합니다.

PowerShell을 사용하여 이미 설치된 Microsoft Store 애플리케이션을 업데이트할 수도 있습니다.

```powershell
(Get-WmiObject -Namespace "root\cimv2\mdm\dmmap" -Class "MDM_EnterpriseModernAppManagement_AppManagement01").UpdateScanMethod()
```

### <a name="stop-automatic-windows-updates"></a>Windows 자동 업데이트 중지

Windows를 최신 버전으로 업데이트한 후 Windows 업데이트를 중지하는 것을 고려할 수 있습니다.  자동 업데이트는 예약된 클래스 시간에 방해가 될 수 있습니다.  코스가 더 오래 실행되는 경우 학생에게 수동으로 업데이트를 확인하거나 예약된 클래스 시간 외의 시간에 자동 업데이트를 설정하도록 요청하는 것이 좋습니다.  Windows 업데이트에 대한 사용자 지정 옵션에 대한 자세한 내용은 [추가 Windows 업데이트 설정 관리](/windows/deployment/update/waas-wu-settings)를 참조하세요.

다음 PowerShell 스크립트를 사용하여 자동 Windows 업데이트를 중지할 수 있습니다.

```powershell
New-Item -Path "HKLM:\SOFTWARE\Policies\Microsoft\Windows\AU"
New-ItemProperty -Path "HKLM:\SOFTWARE\Policies\Microsoft\Windows\AU"
    -Name "NoAutoUpdate" -Value "1" -PropertyType DWORD
```

## <a name="install-foreign-language-packs"></a>외국어 언어 팩 설치

가상 컴퓨터에 추가 언어가 설치되어 있어야 하는 경우 Microsoft Store를 통해 추가할 수 있습니다.

1. Microsoft Store를 시작합니다
2. "언어 팩" 검색
3. 설치할 언어 선택

템플릿 VM에 이미 로그온되어있는 경우 "언어 팩 설치" 바로 가기(`ms-settings:regionlanguage?activationSource=SMC-IA-4027670`)를 사용하여 적절한 설정 페이지로 직접 이동합니다.

## <a name="remove-unneeded-built-in-apps"></a>불필요한 기본 제공 앱 제거

Windows 10에는 특정 클래스에 필요하지 않을 수 있는 다양한 기본 제공 애플리케이션이 함께 제공됩니다. 학생용 컴퓨터 이미지를 간소화하기 위해 템플릿 컴퓨터에서 일부 애플리케이션을 제거하는 것이 좋습니다.  설치된 애플리케이션의 목록을 보려면 PowerShell `Get-AppxPackage` cmdlet을 사용합니다.  아래 예제에서는 제거할 수 있는 설치된 모든 애플리케이션을 보여줍니다.

```powershell
Get-AppxPackage | Where {$_.NonRemovable -eq $false} | select Name
```

애플리케이션을 제거하려면 Remove-Appx cmdlet을 사용합니다.  아래 예제에서는 XBox와 관련된 모든 항목을 제거하는 방법을 보여줍니다.

```powershell
Get-AppxPackage -Name *xbox* | foreach { if (-not $_.NonRemovable) { Remove-AppxPackage $_} }
```

## <a name="install-common-teaching-related-applications"></a>일반적인 교육 관련 애플리케이션 설치

Windows 스토어 앱을 통해 일반적으로 학습에 사용되는 다른 앱을 설치합니다. 제안에는 [Microsoft 화이트 보드 앱](https://www.microsoft.com/store/productId/9MSPC6MP8FM4), [Microsoft Teams](https://www.microsoft.com/store/productId/9MSPC6MP8FM4), [Minecraft 교육용 버전](https://education.minecraft.net/)등의 애플리케이션이 포함됩니다. 이러한 애플리케이션은 Windows 스토어를 통하거나 템플릿 VM의 해당 웹 사이트를 통해 수동으로 설치해야 합니다.

## <a name="conclusion"></a>결론

이 문서에는 효과적인 클래스를 위해 Windows 템플릿 VM을 준비하는 선택적 단계가 나와 있습니다.  이러한 단계에는 OneDrive 설치 및 Microsoft 365 설치, Windows 용 업데이트 설치 및 Microsoft Store 앱에 대한 업데이트 설치가 포함됩니다.  또한 클래스에 가장 적합한 일정으로 업데이트를 설정하는 방법을 설명했습니다.  

## <a name="next-steps"></a>다음 단계
비용 관리에 도움이 되도록 Windows 종료 동작을 제어하는 방법에 대한 문서를 참조하세요. [windows 종료 동작 제어 가이드](how-to-windows-shutdown.md)