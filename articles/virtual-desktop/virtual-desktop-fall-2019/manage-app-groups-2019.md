---
title: Azure Virtual Desktop(클래식)용 앱 그룹 관리 - Azure
description: Azure AD(Active Directory)에서 Azure Virtual Desktop(클래식) 테넌트를 설정하는 방법을 알아봅니다.
author: Heidilohr
ms.topic: tutorial
ms.date: 08/16/2021
ms.author: helohr
manager: femila
ms.openlocfilehash: 6fe87fb7fc0cbe9e727fe1539d8424d9ee1fe1b1
ms.sourcegitcommit: 05dd6452632e00645ec0716a5943c7ac6c9bec7c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/17/2021
ms.locfileid: "122253265"
---
# <a name="tutorial-manage-app-groups-for-azure-virtual-desktop-classic"></a>자습서: Azure Virtual Desktop(클래식)용 앱 그룹 관리

>[!IMPORTANT]
>이 콘텐츠는 Azure Resource Manager Azure Virtual Desktop 개체를 지원하지 않는 Azure Virtual Desktop(클래식)에 적용됩니다. Azure Resource Manager Azure Virtual Desktop 개체를 관리하려는 경우 [이 문서](../manage-app-groups.md)를 참조하세요.

새 Azure Virtual Desktop 호스트 풀에 대해 만든 기본 앱 그룹도 전체 데스크톱을 게시합니다. 뿐만 아니라 호스트 풀에 대한 하나 이상의 RemoteApp 애플리케이션 그룹을 만들 수 있습니다. 이 자습서에 따라 RemoteApp 앱 그룹을 만들고 개별 **시작** 메뉴 앱을 게시합니다.

이 자습서에서는 다음 방법을 알아봅니다.

> [!div class="checklist"]
> * RemoteApp 그룹 만들기
> * RemoteApp 프로그램에 액세스 권한을 부여합니다.

시작하기 전에 PowerShell 세션에서 사용할 [Azure Virtual Desktop PowerShell 모듈을 다운로드하고 가져옵니다](/powershell/windows-virtual-desktop/overview/)(아직 없는 경우). 그런 후, 다음 cmdlet을 실행하여 계정에 로그인합니다.

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
```

## <a name="create-a-remoteapp-group"></a>RemoteApp 그룹 만들기

1. 다음 PowerShell cmdlet을 실행하여 빈 RemoteApp 앱 그룹을 새로 만듭니다.

   ```powershell
   New-RdsAppGroup -TenantName <tenantname> -HostPoolName <hostpoolname> -Name <appgroupname> -ResourceType "RemoteApp"
   ```

2. (선택 사항) 앱 그룹이 생성되었는지 확인하려면 다음 cmdlet을 실행하여 호스트 풀에 대한 모든 앱 그룹 목록을 살펴보면 됩니다.

   ```powershell
   Get-RdsAppGroup -TenantName <tenantname> -HostPoolName <hostpoolname>
   ```

3. 다음 cmdlet을 실행하여 호스트 풀의 가상 머신 이미지에 대한 **시작** 메뉴 앱 목록을 가져옵니다. **FilePath**, **IconPath**, **IconIndex** 의 값과 게시하려는 애플리케이션의 기타 중요 정보를 적어 둡니다.

   ```powershell
   Get-RdsStartMenuApp -TenantName <tenantname> -HostPoolName <hostpoolname> -AppGroupName <appgroupname>
   ```

4. 다음 cmdlet을 실행하여 `AppAlias`를 기반으로 애플리케이션을 설치합니다. 3단계의 출력을 실행하면 `AppAlias`를 볼 수 있습니다.

   ```powershell
   New-RdsRemoteApp -TenantName <tenantname> -HostPoolName <hostpoolname> -AppGroupName <appgroupname> -Name <remoteappname> -AppAlias <appalias>
   ```

5. (선택 사항) 새 RemoteApp 프로그램을 1단계에서 만든 애플리케이션 그룹에 게시하려면 다음 cmdlet을 실행합니다.

   ```powershell
    New-RdsRemoteApp -TenantName <tenantname> -HostPoolName <hostpoolname> -AppGroupName <appgroupname> -Name <remoteappname> -Filepath <filepath>  -IconPath <iconpath> -IconIndex <iconindex>
   ```

6. 앱이 게시되었는지 확인하려면 다음 cmdlet을 실행합니다.

   ```powershell
    Get-RdsRemoteApp -TenantName <tenantname> -HostPoolName <hostpoolname> -AppGroupName <appgroupname>
   ```

7. 이 앱 그룹에 대해 게시하려는 애플리케이션마다 1-5단계를 반복합니다.
8. 다음 cmdlet을 실행하여 사용자에게 앱 그룹의 RemoteApp 프로그램에 대한 액세스 권한을 부여합니다.

   ```powershell
   Add-RdsAppGroupUser -TenantName <tenantname> -HostPoolName <hostpoolname> -AppGroupName <appgroupname> -UserPrincipalName <userupn>
   ```

## <a name="next-steps"></a>다음 단계

이 자습서에서는 앱 그룹을 만들고, RemoteApp 프로그램을 사용하여 채우고, 앱 그룹에 사용자를 할당하는 방법을 알아보았습니다. 유효성 검사 호스트 풀을 만드는 방법을 알아보려면 다음 자습서를 참조하세요. 프로덕션 환경에 배포하기 전에 유효성 검사 호스트 풀을 사용하여 서비스 업데이트를 모니터링할 수 있습니다.

> [!div class="nextstepaction"]
> [서비스 업데이트의 유효성을 검사하기 위한 호스트 풀 만들기](create-validation-host-pool-2019.md)
