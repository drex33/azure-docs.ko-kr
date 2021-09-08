---
title: Azure Virtual Desktop에 대한 앱 그룹 관리 - Azure
description: PowerShell 또는 Azure CLI를 사용하여 Azure Virtual Desktop 앱 그룹을 관리하는 방법.
author: Heidilohr
ms.topic: how-to
ms.date: 07/23/2021
ms.author: helohr
manager: femila
ms.openlocfilehash: f323819492fe89f7742c6b218afa4d2e1bf1b6c0
ms.sourcegitcommit: dcf1defb393104f8afc6b707fc748e0ff4c81830
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2021
ms.locfileid: "123103932"
---
# <a name="manage-app-groups-using-powershell-or-the-azure-cli"></a>PowerShell 또는 Azure CLI를 사용하여 앱 그룹 관리

>[!IMPORTANT]
>이 콘텐츠는 Azure Resource Manager Azure Virtual Desktop 개체를 통해 Azure Virtual Desktop에 적용됩니다. Azure Resource Manager 개체 없이 Azure Virtual Desktop(클래식)을 사용하는 경우 [이 문서](./virtual-desktop-fall-2019/manage-app-groups-2019.md)를 참조하세요.

새 Azure Virtual Desktop 호스트 풀에 대해 만든 기본 앱 그룹도 전체 데스크톱을 게시합니다. 뿐만 아니라 호스트 풀에 대한 하나 이상의 RemoteApp 애플리케이션 그룹을 만들 수 있습니다. 이 자습서에 따라 RemoteApp 앱 그룹을 만들고 개별 **시작** 메뉴 앱을 게시합니다.

이 자습서에서는 다음 방법을 알아봅니다.

> [!div class="checklist"]
> * RemoteApp 그룹 만들기
> * RemoteApp 프로그램에 액세스 권한을 부여합니다.

## <a name="prerequisites"></a>필수 구성 요소

### <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

이 문서에서는 [PowerShell 모듈 설정](powershell-module.md)의 지침에 따라 PowerShell 모듈을 설정하고 Azure 계정에 로그인했다고 가정합니다.

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

이 문서에서는 Azure CLI에 대한 환경을 이미 설정했으며 Azure 계정에 로그인했다고 가정합니다.

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

---

## <a name="create-a-remoteapp-group"></a>RemoteApp 그룹 만들기

### <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

PowerShell을 사용하여 RemoteApp 그룹을 만들려면 다음을 수행합니다.

1. 다음 PowerShell cmdlet을 실행하여 빈 RemoteApp 앱 그룹을 새로 만듭니다.

   ```powershell
   New-AzWvdApplicationGroup -Name <appgroupname> -ResourceGroupName <resourcegroupname> -ApplicationGroupType "RemoteApp" -HostPoolArmPath '/subscriptions/SubscriptionId/resourcegroups/ResourceGroupName/providers/Microsoft.DesktopVirtualization/hostPools/HostPoolName'-Location <azureregion>
   ```

2. (선택 사항) 앱 그룹이 생성되었는지 확인하려면 다음 cmdlet을 실행하여 호스트 풀에 대한 모든 앱 그룹 목록을 살펴보면 됩니다.

   ```powershell
   Get-AzWvdApplicationGroup -Name <appgroupname> -ResourceGroupName <resourcegroupname>
   ```

3. 다음 cmdlet을 실행하여 호스트 풀의 가상 머신 이미지에 대한 **시작** 메뉴 앱 목록을 가져옵니다. **FilePath**, **IconPath**, **IconIndex** 의 값과 게시하려는 애플리케이션의 기타 중요 정보를 적어 둡니다.

   ```powershell
   Get-AzWvdStartMenuItem -ApplicationGroupName <appgroupname> -ResourceGroupName <resourcegroupname> | Format-List | more
   ```

   출력에는 다음과 같은 형식으로 모든 시작 메뉴 항목이 표시되어야 합니다.

   ```powershell
   AppAlias            : access
   CommandLineArgument :
   FilePath            : C:\Program Files\Microsoft Office\root\Office16\MSACCESS.EXE
   FriendlyName        :
   IconIndex           : 0
   IconPath            : C:\Program Files\Microsoft Office\Root\VFS\Windows\Installer\{90160000-000F-0000-1000-0000000FF1CE}\accicons.exe
   Id                  : /subscriptions/resourcegroups/providers/Microsoft.DesktopVirtualization/applicationgroups/startmenuitems/Access
   Name                : 0301RAG/Access
   Type                : Microsoft.DesktopVirtualization/applicationgroups/startmenuitems

   AppAlias            : charactermap
   CommandLineArgument :
   FilePath            : C:\windows\system32\charmap.exe
   FriendlyName        :
   IconIndex           : 0
   IconPath            : C:\windows\system32\charmap.exe
   Id                  : /subscriptions/resourcegroups/providers/Microsoft.DesktopVirtualization/applicationgroups/startmenuitems/Character Map
   Name                : 0301RAG/Character Map
   Type                : Microsoft.DesktopVirtualization/applicationgroups/startmenuitems
   ```

4. 다음 cmdlet을 실행하여 `AppAlias`를 기반으로 애플리케이션을 설치합니다. 3단계의 출력을 실행하면 `AppAlias`를 볼 수 있습니다.

   ```powershell
   New-AzWvdApplication -AppAlias <appalias> -GroupName <appgroupname> -Name <remoteappname> -ResourceGroupName <resourcegroupname> -CommandLineSetting <DoNotAllow|Allow|Require>
   ```

5. (선택 사항) 새 RemoteApp 프로그램을 1단계에서 만든 애플리케이션 그룹에 게시하려면 다음 cmdlet을 실행합니다.

   ```powershell
   New-AzWvdApplication -GroupName <appgroupname> -Name <remoteappname> -ResourceGroupName <resourcegroupname> -Filepath <filepath> -IconPath <iconpath> -IconIndex <iconindex> -CommandLineSetting <DoNotAllow|Allow|Require>
   ```

6. 앱이 게시되었는지 확인하려면 다음 cmdlet을 실행합니다.

   ```powershell
   Get-AzWvdApplication -GroupName <appgroupname> -ResourceGroupName <resourcegroupname>
   ```

7. 이 앱 그룹에 대해 게시하려는 애플리케이션마다 1-5단계를 반복합니다.
8. 다음 cmdlet을 실행하여 사용자에게 앱 그룹의 RemoteApp 프로그램에 대한 액세스 권한을 부여합니다.

   ```powershell
   New-AzRoleAssignment -SignInName <userupn> -RoleDefinitionName "Desktop Virtualization User" -ResourceName <appgroupname> -ResourceGroupName <resourcegroupname> -ResourceType 'Microsoft.DesktopVirtualization/applicationGroups'
   ```

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

> [!NOTE]
> Azure CLI는 현재 시작 메뉴 앱을 가져오는 명령을 제공하지 않으며, 새 RemoteApp 프로그램을 만들거나 애플리케이션 그룹에 게시하는 명령을 제공하지 않습니다. Azure PowerShell 사용

Azure CLI를 사용하여 RemoteApp 그룹을 만들려면:

1. [az desktopvirtualization applicationgroup create](/cli/azure/desktopvirtualization##az_desktopvirtualization_applicationgroup_create) 명령을 사용하여 새 원격 애플리케이션 그룹을 만듭니다.

   ```azurecli
   az desktopvirtualization applicationgroup create --name "MyApplicationGroup" \
      --resource-group "MyResourceGroup" \
      --location "MyLocation" \
      --application-group-type "RemoteApp" \
      --host-pool-arm-path "/subscriptions/MySubscriptionGUID/resourceGroups/MyResourceGroup/providers/Microsoft.DesktopVirtualization/hostpools/MyHostPool"
      --tags tag1="value1" tag2="value2" \
      --friendly-name "Friendly name of this application group" \
      --description "Description of this application group" 
   ```
    
2. (선택 사항) 앱 그룹이 생성되었는지 확인하려면 다음 명령을 실행하여 호스트 풀에 대한 모든 앱 그룹 목록을 살펴보면 됩니다.

   ```azurecli
   az desktopvirtualization applicationgroup list \
      --resource-group "MyResourceGroup"
   ```
---

## <a name="next-steps"></a>다음 단계

자습서에서 이 방법 가이드를 제공하는 경우 [서비스 업데이트의 유효성을 검사하는 호스트 풀 만들기](create-validation-host-pool.md)를 확인합니다. 프로덕션 환경에 배포하기 전에 유효성 검사 호스트 풀을 사용하여 서비스 업데이트를 모니터링할 수 있습니다.