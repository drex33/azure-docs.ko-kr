---
title: Azure Cloud Shell 빠른 시작 - PowerShell
description: Azure Cloud Shell을 사용하여 브라우저에서 PowerShell을 사용하는 방법을 알아봅니다.
author: maertendmsft
ms.author: damaerte
tags: azure-resource-manager
ms.service: azure
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.topic: article
ms.date: 10/18/2018
ms.custom: devx-track-azurepowershell, ignite-fall-2021
ms.openlocfilehash: 79568dc04d8c5bce95eaa15d207f7caecfff2f5d
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131080824"
---
# <a name="quickstart-for-powershell-in-azure-cloud-shell"></a>Azure Cloud Shell의 PowerShell에 대한 빠른 시작

이 문서는 [Azure Portal](https://portal.azure.com/)에서 Azure Cloud Shell의 PowerShell을 사용하는 방법을 자세히 설명합니다.

> [!NOTE]
> [Azure Cloud Shell의 Bash](quickstart.md) 빠른 시작도 사용할 수 있습니다.

## <a name="start-cloud-shell"></a>Cloud Shell 시작

1. Azure Portal의 위쪽 탐색 모음에서 **Azure Cloud Shell** 버튼을 클릭합니다.

   ![Azure Portal에서 Azure Cloud Shell을 시작하는 방법을 보여 주는 스크린샷.](media/quickstart-powershell/shell-icon.png)

2. 드롭다운에서 PowerShell 환경을 선택하면 Azure 드라이브 `(Azure:)`에 들어가게 됩니다.

   ![Azure Cloud Shell에 맞는 PowerShell 환경을 선택하는 방법을 보여 주는 스크린샷.](media/quickstart-powershell/environment-ps.png)

## <a name="run-powershell-commands"></a>PowerShell 명령 실행

Azure Cloud Shell에서 다음과 같은 일반 PowerShell 명령을 실행합니다.

```azurepowershell-interactive
PS Azure:\> Get-Date

# Expected Output
Friday, July 27, 2018 7:08:48 AM

PS Azure:\> Get-AzVM -Status

# Expected Output
ResourceGroupName       Name       Location                VmSize   OsType     ProvisioningState  PowerState
-----------------       ----       --------                ------   ------     -----------------  ----------
MyResourceGroup2        Demo        westus         Standard_DS1_v2  Windows    Succeeded           running
MyResourceGroup         MyVM1       eastus            Standard_DS1  Windows    Succeeded           running
MyResourceGroup         MyVM2       eastus   Standard_DS2_v2_Promo  Windows    Succeeded           deallocated
```

### <a name="interact-with-virtual-machines"></a>가상 머신과 상호 작용

`VirtualMachines` 디렉터리를 통해 현재 구독에서 모든 가상 머신을 찾을 수 있습니다.

```azurepowershell-interactive
PS Azure:\MySubscriptionName\VirtualMachines> dir

    Directory: Azure:\MySubscriptionName\VirtualMachines


Name       ResourceGroupName  Location  VmSize          OsType              NIC ProvisioningState  PowerState
----       -----------------  --------  ------          ------              --- -----------------  ----------
TestVm1    MyResourceGroup1   westus    Standard_DS2_v2 Windows       my2008r213         Succeeded     stopped
TestVm2    MyResourceGroup1   westus    Standard_DS1_v2 Windows          jpstest         Succeeded deallocated
TestVm10   MyResourceGroup2   eastus    Standard_DS1_v2 Windows           mytest         Succeeded     running
```

#### <a name="invoke-powershell-script-across-remote-vms"></a>PowerShell 스크립트를 원격 VM에 걸쳐 호출

 > [!WARNING]
 > [Azure VM의 원격 관리 문제 해결](troubleshooting.md#troubleshooting-remote-management-of-azure-vms)을 참조하세요.

  MyVM1이란 VM이 있다고 가정하고 `Invoke-AzVMCommand`를 사용하여 원격 컴퓨터에 PowerShell 스크립트 블록을 호출합니다.

  ```azurepowershell-interactive
  Enable-AzVMPSRemoting -Name MyVM1 -ResourceGroupname MyResourceGroup
  Invoke-AzVMCommand -Name MyVM1 -ResourceGroupName MyResourceGroup -Scriptblock {Get-ComputerInfo} -Credential (Get-Credential)
  ```

  또한 먼저 VirtualMachines 디렉터리로 이동하고 다음과 같이 `Invoke-AzVMCommand`를 실행할 수 있습니다.

  ```azurepowershell-interactive
  PS Azure:\> cd MySubscriptionName\ResourceGroups\MyResourceGroup\Microsoft.Compute\virtualMachines
  PS Azure:\MySubscriptionName\ResourceGroups\MyResourceGroup\Microsoft.Compute\virtualMachines> Get-Item MyVM1 | Invoke-AzVMCommand -Scriptblock {Get-ComputerInfo} -Credential (Get-Credential)

  # You will see output similar to the following:

  PSComputerName                                          : 65.52.28.207
  RunspaceId                                              : 2c2b60da-f9b9-4f42-a282-93316cb06fe1
  WindowsBuildLabEx                                       : 14393.1066.amd64fre.rs1_release_sec.170327-1835
  WindowsCurrentVersion                                   : 6.3
  WindowsEditionId                                        : ServerDatacenter
  WindowsInstallationType                                 : Server
  WindowsInstallDateFromRegistry                          : 5/18/2017 11:26:08 PM
  WindowsProductId                                        : 00376-40000-00000-AA947
  WindowsProductName                                      : Windows Server 2016 Datacenter
  WindowsRegisteredOrganization                           :
   ...
  ```

#### <a name="interactively-log-on-to-a-remote-vm"></a>원격 VM에 대화형으로 로그온

`Enter-AzVM`을 사용하여 Azure에서 실행하는 VM에 대화형으로 로그인할 수 있습니다.

  ```azurepowershell-interactive
  PS Azure:\> Enter-AzVM -Name MyVM1 -ResourceGroupName MyResourceGroup -Credential (Get-Credential)
  ```

디렉터리를 먼저 탐색 하 `VirtualMachines` 고 다음과 같이 실행할 수도 있습니다 `Enter-AzVM` .

```azurepowershell-interactive
PS Azure:\MySubscriptionName\ResourceGroups\MyResourceGroup\Microsoft.Compute\virtualMachines> Get-Item MyVM1 | Enter-AzVM -Credential (Get-Credential)
```

### <a name="discover-webapps"></a>WebApps 검색

`WebApps` 디렉터리에 들어가면 웹앱 리소스를 쉽게 탐색할 수 있습니다.

```azurepowershell-interactive
PS Azure:\MySubscriptionName> dir .\WebApps\

    Directory: Azure:\MySubscriptionName\WebApps

Name            State    ResourceGroup      EnabledHostNames                  Location
----            -----    -------------      ----------------                  --------
mywebapp1       Stopped  MyResourceGroup1   {mywebapp1.azurewebsites.net...   West US
mywebapp2       Running  MyResourceGroup2   {mywebapp2.azurewebsites.net...   West Europe
mywebapp3       Running  MyResourceGroup3   {mywebapp3.azurewebsites.net...   South Central US

# You can use Azure cmdlets to Start/Stop your web apps
PS Azure:\MySubscriptionName\WebApps> Start-AzWebApp -Name mywebapp1 -ResourceGroupName MyResourceGroup1

Name           State    ResourceGroup        EnabledHostNames                   Location
----           -----    -------------        ----------------                   --------
mywebapp1      Running  MyResourceGroup1     {mywebapp1.azurewebsites.net ...   West US

# Refresh the current state with -Force
PS Azure:\MySubscriptionName\WebApps> dir -Force

    Directory: Azure:\MySubscriptionName\WebApps

Name            State    ResourceGroup      EnabledHostNames                  Location
----            -----    -------------      ----------------                  --------
mywebapp1       Running  MyResourceGroup1   {mywebapp1.azurewebsites.net...   West US
mywebapp2       Running  MyResourceGroup2   {mywebapp2.azurewebsites.net...   West Europe
mywebapp3       Running  MyResourceGroup3   {mywebapp3.azurewebsites.net...   South Central US
```

## <a name="ssh"></a>SSH

SSH를 사용하여 서버 또는 VM을 인증하려면 Cloud Shell에서 공개-프라이빗 키 쌍을 생성하고 퍼블릭 키를 원격 컴퓨터의 `authorized_keys`(예: `/home/user/.ssh/authorized_keys`)에 게시합니다.

> [!NOTE]
> Cloud Shell에서 `ssh-keygen`을 사용하여 SSH 프라이빗-공개 키를 만들고 이 키를 `$env:USERPROFILE\.ssh`에 게시할 수 있습니다.

### <a name="using-ssh"></a>SSH 사용

[여기](../virtual-machines/linux/quick-create-powershell.md)의 지침에 따라 Azure PowerShell cmdlet을 사용하여 새 VM 구성을 만듭니다.
`New-AzVM`을 호출하여 배포를 시작하기 전에 SSH 공개 키를 VM 구성에 추가합니다.
새로 만든 VM에는 공개 키가 `~\.ssh\authorized_keys` 위치에 포함되므로 자격 증명이 없는 SSH 세션을 VM에 사용할 수 있습니다.

```azurepowershell-interactive
# Create VM config object - $vmConfig using instructions on linked page above

# Generate SSH keys in Cloud Shell
ssh-keygen -t rsa -b 2048 -f $HOME\.ssh\id_rsa 

# Ensure VM config is updated with SSH keys
$sshPublicKey = Get-Content "$HOME\.ssh\id_rsa.pub"
Add-AzVMSshPublicKey -VM $vmConfig -KeyData $sshPublicKey -Path "/home/azureuser/.ssh/authorized_keys"

# Create a virtual machine
New-AzVM -ResourceGroupName <yourResourceGroup> -Location <vmLocation> -VM $vmConfig

# SSH to the VM
ssh azureuser@MyVM.Domain.Com
```

## <a name="list-available-commands"></a>사용할 수 있는 명령 목록

`Azure` 드라이브에서 상황별 Azure 명령을 가져오려면 `Get-AzCommand`를 입력합니다.

또는 사용 가능한 Azure 명령을 알아보려면 언제나 `Get-Command *az* -Module Az.*`을 사용할 수 있습니다.

## <a name="install-custom-modules"></a>사용자 지정 모듈 설치

`Install-Module`을 실행하면 [PowerShell 갤러리][gallery]에서 모듈을 설치할 수 있습니다.

## <a name="get-help"></a>Get-Help

Azure Cloud Shell에서 PowerShell에 대한 정보를 가져오려면 `Get-Help`을 입력합니다.

```azurepowershell-interactive
Get-Help
```

특정 명령에 대해 여전히 `Get-Help`를 실행한 후 뒤 이어 cmdlet을 실행할 수 있습니다.

```azurepowershell-interactive
Get-Help Get-AzVM
```

## <a name="use-azure-files-to-store-your-data"></a>Azure Files를 사용하여 데이터 저장

예를 들어 `helloworld.ps1`이란 스크립트를 만들어 `clouddrive`에 저장하고 셸 세션 전반에 걸쳐 사용할 수 있습니다.

```azurepowershell-interactive
cd $HOME\clouddrive
# Create a new file in clouddrive directory
New-Item helloworld.ps1
# Open the new file for editing
code .\helloworld.ps1
# Add the content, such as 'Hello World!'
.\helloworld.ps1
Hello World!
```

다음에 Azure Cloud Shell의 PowerShell을 사용할 때 `helloworld.ps1` 파일은 Azure Files 공유를 탑재한 `$HOME\clouddrive` 디렉터리에 존재할 것입니다.

## <a name="use-custom-profile"></a>사용자 지정 프로필 사용

PowerShell 프로필 `profile.ps1`(또는 `Microsoft.PowerShell_profile.ps1`)을 만들어 PowerShell 환경을 사용자 지정할 수 있습니다.
그것을 `$profile.CurrentUserAllHosts`(또는 `$profile.CurrentUserAllHosts`)에 저장하여 Cloud Shell 세션의 모든 PowerShell에 로드될 수 있게 합니다.

프로필을 만드는 방법에 대해서는 [프로필 소개][profile]를 참조합니다.

## <a name="use-git"></a>Git 사용

Cloud Shell에서 Git 리포지토리를 복제하려면 [개인용 액세스 토큰][githubtoken]을 만들고 이를 사용자 이름으로 사용합니다. 토큰이 있다면 다음과 같이 리포지토리를 복제합니다.

```azurepowershell-interactive
  git clone https://<your-access-token>@github.com/username/repo.git
```

## <a name="exit-the-shell"></a>셸 종료

세션을 종료하려면 `exit`을 입력합니다.

[bashqs]: quickstart.md
[gallery]: https://www.powershellgallery.com/
[customex]: /azure/virtual-machines/extensions/custom-script-windows
[profile]: /powershell/module/microsoft.powershell.core/about/about_profiles
[azmount]: ../storage/files/storage-how-to-use-files-windows.md
[githubtoken]: https://help.github.com/articles/creating-a-personal-access-token-for-the-command-line/
