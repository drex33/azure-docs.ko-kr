---
title: Azure Virtual Desktop MSIX 앱 연결 PowerShell 스크립트 구성 - Azure
description: Azure Virtual Desktop에 대한 MSIX 앱 연결을 위한 PowerShell 스크립트를 만드는 방법입니다.
author: Heidilohr
ms.topic: how-to
ms.date: 04/13/2021
ms.author: helohr
manager: femila
ms.openlocfilehash: 6b54afdb77bd1fc3a958b959dad4fcb030e1fe2f
ms.sourcegitcommit: da9335cf42321b180757521e62c28f917f1b9a07
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/16/2021
ms.locfileid: "122538258"
---
# <a name="create-powershell-scripts-for-msix-app-attach"></a>MSIX 앱 연결용 PowerShell 스크립트 만들기

이 항목에서는 MSIX 앱 연결을 위한 PowerShell 스크립트를 설정하는 방법을 안내합니다.

## <a name="install-certificates"></a>인증서 설치

MSIX 앱 연결 패키지에서 앱을 호스팅하는 호스트 풀의 모든 세션 호스트에 인증서를 설치해야 합니다.

앱에 공개적으로 신뢰되지 않았거나 자체 서명된 인증서가 사용되는 경우 설치 방법은 다음과 같습니다.

1. 패키지를 마우스 오른쪽 단추로 클릭하고 **속성** 을 선택합니다.
2. 표시되는 창에서 **디지털 서명** 탭을 선택합니다. 탭의 목록에는 항목이 하나만 있어야 합니다. 해당 항목을 선택하여 항목을 강조 표시한 다음 **세부 정보** 를 선택합니다.
3. 디지털 서명 정보 창이 나타나면 **일반** 탭을 선택한 다음, **인증서 보기** 와 **인증서 설치** 를 차례로 선택합니다.
4. 설치 관리자가 열리면 **로컬 머신** 을 스토리지 위치로 선택하고 **다음** 을 선택합니다.
5. 설치 관리자에 앱이 디바이스를 변경하도록 허용할지 묻는 메시지가 표시되면 **예** 를 선택합니다.
6. **모든 인증서를 다음 저장소에 저장** 을 선택한 다음, **찾아보기** 를 선택합니다.
7. 인증서 저장소 선택 창이 나타나면 **신뢰할 수 있는 사용자** 를 선택한 후 **확인** 을 선택합니다.
8. **다음** 및 **마침** 을 선택합니다.

## <a name="enable-microsoft-hyper-v"></a>Microsoft Hyper-V 사용

`Mount-VHD` 명령은 스테이징에 필요하고 `Dismount-VHD`는 디스테이지에 필요하므로 Microsoft Hyper-V를 사용하도록 설정해야 합니다.

```powershell
Enable-WindowsOptionalFeature -Online -FeatureName Microsoft-Hyper-V -All
```

>[!NOTE]
>이렇게 변경하면 가상 머신을 다시 시작해야 합니다.

## <a name="prepare-powershell-scripts-for-msix-app-attach"></a>MSIX app attach용 PowerShell 스크립트 준비

MSIX app attach에는 다음 순서로 수행해야 하는 네 가지 단계가 있습니다.

1. 단계
2. 등록
3. 등록 취소
4. 스테이징 취소

각 단계는 PowerShell 스크립트를 만듭니다. 각 단계에 대한 샘플 스크립트는 [여기](https://github.com/Azure/RDS-Templates/tree/master/msix-app-attach)에 있습니다.

### <a name="stage-powershell-script"></a>PowerShell 스크립트 스테이징

PowerShell 스크립트를 업데이트하기 전에 VHD에 볼륨의 볼륨 GUID가 있는지 확인해야 합니다. 볼륨 GUID를 가져오려면 다음을 수행합니다.

1.  스크립트를 실행할 VM 내에서 VHD가 있는 네트워크 공유를 엽니다.

2.  VHD를 마우스 오른쪽 단추로 클릭하고 **탑재** 를 선택합니다. 그러면 VHD가 드라이브 문자에 탑재됩니다.

3.  VHD를 탑재하면 **파일 탐색기** 창이 열립니다. 부모 폴더를 캡처하고 **$parentFolder** 변수를 업데이트합니다.

    >[!NOTE]
    >부모 폴더가 보이지 않으면 MSIX가 제대로 확장되지 않았다는 의미입니다. 이전 섹션을 다시 실행하고 다시 시도합니다.

4.  부모 폴더를 엽니다. 올바르게 확장된 경우 패키지와 이름이 같은 폴더가 보입니다. 이 폴더의 이름과 일치하도록 **$packageName** 변수를 업데이트합니다.

    `VSCodeUserSetup-x64-1.38.1_1.38.1.0_x64__8wekyb3d8bbwe`)을 입력합니다.

5.  명령 프롬프트를 열고 **mountvol** 을 입력합니다. 이 명령은 볼륨 목록과 해당 GUID를 표시합니다. 2단계에서 드라이브 문자가 VHD를 마운트한 드라이브와 일치하는 볼륨의 GUID를 복사합니다.

    예를 들어, mountvol 명령에 대한 예제 출력에서 VHD를 C 드라이브에 탑재한 경우 `C:\` 위의 값을 복사합니다.

    ```cmd
    Possible values for VolumeName along with current mount points are:

    \\?\Volume{a12b3456-0000-0000-0000-10000000000}\
    *** NO MOUNT POINTS ***

    \\?\Volume{c78d9012-0000-0000-0000-20000000000}\
        E:\

    \\?\Volume{d34e5678-0000-0000-0000-30000000000}\
        C:\

    ```


6.  방금 복사한 볼륨 GUID를 사용하여 **$volumeGuid** 변수를 업데이트합니다.

7. 관리 PowerShell 프롬프트를 열고 환경에 적용되는 변수로 다음 PowerShell 스크립트를 업데이트합니다.

    ```powershell
    #MSIX app attach staging sample

    #region variables
    $vhdSrc="<path to vhd>"
    $packageName = "<package name>"
    $parentFolder = "<package parent folder>"
    $parentFolder = "\" + $parentFolder + "\"
    $volumeGuid = "<vol guid>"
    $msixJunction = "C:\temp\AppAttach\"
    #endregion

    #region mountvhd
    try
    {
          Mount-Diskimage -ImagePath $vhdSrc -NoDriveLetter -Access ReadOnly
          Write-Host ("Mounting of " + $vhdSrc + " was completed!") -BackgroundColor Green
    }
    catch
    {
          Write-Host ("Mounting of " + $vhdSrc + " has failed!") -BackgroundColor Red
    }
    #endregion

    #region makelink
    $msixDest = "\\?\Volume{" + $volumeGuid + "}\"
    if (!(Test-Path $msixJunction))
    {
         md $msixJunction
    }

    $msixJunction = $msixJunction + $packageName
    cmd.exe /c mklink /j $msixJunction $msixDest
    #endregion

    #region stage
    [Windows.Management.Deployment.PackageManager,Windows.Management.Deployment,ContentType=WindowsRuntime] | Out-Null
    Add-Type -AssemblyName System.Runtime.WindowsRuntime
    $asTask = ([System.WindowsRuntimeSystemExtensions].GetMethods() | Where { $_.ToString() -eq 'System.Threading.Tasks.Task`1[TResult] AsTask[TResult,TProgress](Windows.Foundation.IAsyncOperationWithProgress`2[TResult,TProgress])'})[0]
    $asTaskAsyncOperation = $asTask.MakeGenericMethod([Windows.Management.Deployment.DeploymentResult], [Windows.Management.Deployment.DeploymentProgress])
    $packageManager = [Windows.Management.Deployment.PackageManager]::new()
    $path = $msixJunction + $parentFolder + $packageName 
    $path = ([System.Uri]$path).AbsoluteUri
    $asyncOperation = $packageManager.StagePackageAsync($path, $null, "StageInPlace")
    $task = $asTaskAsyncOperation.Invoke($null, @($asyncOperation))
    $task
    #endregion
    ```

### <a name="register-powershell-script"></a>PowerShell 스크립트 등록

등록 스크립트를 실행하려면 다음 PowerShell cmdlet에서 자리 표시자 값을 사용자 환경에 적용되는 값으로 대체하여 실행합니다.

```powershell
#MSIX app attach registration sample

#region variables
$packageName = "<package name>"
$path = "C:\Program Files\WindowsApps\" + $packageName + "\AppxManifest.xml"
#endregion

#region register
Add-AppxPackage -Path $path -DisableDevelopmentMode -Register
#endregion
```

### <a name="deregister-powershell-script"></a>PowerShell 스크립트 등록 취소

이 스크립트의 경우 **$packageName** 의 자리 표시자를 테스트 중인 패키지의 이름으로 바꿉니다.

```powershell
#MSIX app attach deregistration sample

#region variables
$packageName = "<package name>"
#endregion

#region deregister
Remove-AppxPackage -PreserveRoamableApplicationData $packageName
#endregion
```

### <a name="destage-powershell-script"></a>PowerShell 스크립트 스테이징 취소

이 스크립트의 경우 **$packageName** 의 자리 표시자를 테스트 중인 패키지의 이름으로 바꿉니다. 프로덕션 배포에서는 종료 시 이 작업을 실행하는 것이 가장 좋습니다.

```powershell
#MSIX app attach de staging sample

$vhdSrc="<path to vhd>"

#region variables
$packageName = "<package name>"
$msixJunction = "C:\temp\AppAttach"
#endregion

#region deregister
Remove-AppxPackage -AllUsers -Package $packageName
Remove-Item "$msixJunction\$packageName" -Recurse -Force -Verbose
#endregion

#region Detach VHD
Dismount-DiskImage -ImagePath $vhdSrc -Confirm:$false
#endregion
```

>[!NOTE]
>디스테이징 스크립트를 실행한 후 **$volumeGuid** 지점이 남아 있는 동안에도 기기를 종료할 수 있습니다.

## <a name="set-up-simulation-scripts-for-the-msix-app-attach-agent"></a>MSIX app attach 에이전트에 대한 시뮬레이션 스크립트 설정

스크립트를 만들면 이 스크립트를 사용자가 수동으로 실행하거나 시작, 로그온, 로그오프, 종료 스크립트로 자동 실행되도록 설정할 수 있습니다. 이러한 유형의 스크립트에 대해 자세히 알아보려면 [그룹 정책에서 시작, 종료, 로그온, 로그오프 스크립트 사용](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/dn789196(v=ws.11)/)을 참조하세요.

이러한 자동 스크립트 각각은 app attach 스크립트의 한 단계를 실행합니다.

- 시작 스크립트는 스테이징 스크립트를 실행합니다.
- 로그온 스크립트는 등록 스크립트를 실행합니다.
- 로그오프 스크립트는 등록 취소 스크립트를 실행합니다.
- 종료 스크립트는 스테이징 취소 스크립트를 실행합니다.

>[!NOTE]
>스테이지 스크립트를 사용하여 작업 스케줄러를 실행할 수 있습니다. 스크립트를 실행하려면 작업 트리거를 **컴퓨터가 시작될 때** 로 설정한 다음 **가장 높은 권한으로 실행** 을 활성화합니다.

## <a name="use-packages-offline"></a>오프라인으로 패키지 사용

네트워크 내에서 [비즈니스용 Microsoft 스토어](https://businessstore.microsoft.com/) 또는 [교육용 Microsoft 스토어](https://educationstore.microsoft.com/)의 패키지를 사용하는 경우 또는 인터넷에 연결되지 않은 디바이스에서 패키지를 사용하는 경우에는 Microsoft Store에서 패키지 라이선스를 확보하여 디바이스에 설치해야 앱을 성공적으로 실행할 수 있습니다. 디바이스가 온라인 상태이고 비즈니스용 Microsoft 스토어에 연결할 수 있으면 필요한 라이선스가 자동으로 다운로드되지만 오프라인이면 라이선스를 수동으로 설정해야 합니다.

라이선스 파일을 설치하려면 WMI 브리지 공급자에서 MDM_EnterpriseModernAppManagement_StoreLicenses02_01 클래스를 호출하는 PowerShell 스크립트를 사용해야 합니다.

오프라인 사용을 위해 라이선스를 설정하는 방법은 다음과 같습니다.

1. 비즈니스용 Microsoft 스토어에서 앱 패키지, 라이선스 및 필수 프레임워크를 다운로드합니다. 인코딩된 라이선스 파일과 인코딩되지 않은 라이선스 파일이 모두 필요합니다. 자세한 다운로드 지침은 [여기](/microsoft-store/distribute-offline-apps#download-an-offline-licensed-app)에서 찾을 수 있습니다.
2. 3단계의 스크립트에서 다음 변수를 업데이트합니다.
      1. `$contentID`는 인코딩되지 않은 라이선스 파일(.xml)의 ContentID 값입니다. 원하는 텍스트 편집기에서 라이선스 파일을 열 수 있습니다.
      2. `$licenseBlob`은 인코딩된 라이선스 파일(. bin)의 라이선스 Blob에 대한 전체 문자열입니다. 원하는 텍스트 편집기에서 인코딩된 라이선스 파일을 열 수 있습니다.
3. 관리자 PowerShell 프롬프트에서 다음 스크립트를 실행합니다. 라이선스 설치를 수행하기에 좋은 위치는 [스테이징 스크립트](#stage-powershell-script)의 끝이며 관리자 프롬프트에서도 실행해야 합니다.

```powershell
$namespaceName = "root\cimv2\mdm\dmmap"
$className = "MDM_EnterpriseModernAppManagement_StoreLicenses02_01"
$methodName = "AddLicenseMethod"
$parentID = "./Vendor/MSFT/EnterpriseModernAppManagement/AppLicenses/StoreLicenses"

#TODO - Update $contentID with the ContentID value from the unencoded license file (.xml)
$contentID = "{'ContentID'_in_unencoded_license_file}"

#TODO - Update $licenseBlob with the entire String in the encoded license file (.bin)
$licenseBlob = "{Entire_String_in_encoded_license_file}"

$session = New-CimSession

#The final string passed into the AddLicenseMethod should be of the form <License Content="encoded license blob" />
$licenseString = '<License Content='+ '"' + $licenseBlob +'"' + ' />'

$params = New-Object Microsoft.Management.Infrastructure.CimMethodParametersCollection
$param = [Microsoft.Management.Infrastructure.CimMethodParameter]::Create("param",$licenseString ,"String", "In")
$params.Add($param)


try
{
   $instance = New-CimInstance -Namespace $namespaceName -ClassName $className -Property @{ParentID=$parentID;InstanceID=$contentID}
   $session.InvokeMethod($namespaceName, $instance, $methodName, $params)

}
catch [Exception]
{
     write-host $_ | out-string
}
```

## <a name="next-steps"></a>다음 단계

이 기능은 현재 지원되지 않지만 [Azure Virtual Desktop TechCommunity](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop)에서 커뮤니티에 질문할 수 있습니다.

[Azure Virtual Desktop 피드백 허브](https://support.microsoft.com/help/4021566/windows-10-send-feedback-to-microsoft-with-feedback-hub-app)에서 Azure Virtual Desktop에 대한 피드백을 남길 수도 있습니다.
