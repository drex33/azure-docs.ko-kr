---
title: Azure Virtual Desktop 호스트 풀 만들기 - Azure
description: PowerShell 또는 Azure CLI를 사용하여 Azure Virtual Desktop에서 호스트 풀을 만드는 방법입니다.
author: Heidilohr
ms.topic: how-to
ms.date: 07/23/2021
ms.author: helohr
ms.custom: devx-track-azurepowershell
manager: femila
ms.openlocfilehash: dbd48f8ff2b3da5cec432f6b1fba7d272621535c
ms.sourcegitcommit: dcf1defb393104f8afc6b707fc748e0ff4c81830
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2021
ms.locfileid: "123100855"
---
# <a name="create-an-azure-virtual-desktop-host-pool-with-powershell-or-the-azure-cli"></a>PowerShell 또는 Azure CLI를 사용하여 Azure Virtual Desktop 호스트 풀 만들기

>[!IMPORTANT]
>이 콘텐츠는 Azure Resource Manager Azure Virtual Desktop 개체를 통해 Azure Virtual Desktop에 적용됩니다. Azure Resource Manager 개체 없이 Azure Virtual Desktop(클래식)을 사용하는 경우 [이 문서](./virtual-desktop-fall-2019/create-host-pools-powershell-2019.md)를 참조하세요.

호스트 풀은 Azure Virtual Desktop 테넌트 환경 내에서 하나 이상의 동일한 가상 머신 컬렉션입니다. 각 호스트 풀은 여러 RemoteApp 그룹, 하나의 데스크톱 앱 그룹 및 여러 세션 호스트와 연결할 수 있습니다.

## <a name="create-a-host-pool"></a>호스트 풀 만들기

### <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

아직 이 방법으로 수행하지 않은 경우 [PowerShell 모듈 설정](powershell-module.md)의 지침을 따르세요.

다음 cmdlet을 실행하여 Azure Virtual Desktop 환경에 로그인합니다.

```powershell
New-AzWvdHostPool -ResourceGroupName <resourcegroupname> -Name <hostpoolname> -WorkspaceName <workspacename> -HostPoolType <Pooled|Personal> -LoadBalancerType <BreadthFirst|DepthFirst|Persistent> -Location <region> -DesktopAppGroupName <appgroupname>
```

이 cmdlet은 호스트 풀, 작업 영역 및 데스크톱 앱 그룹을 만듭니다. 또한 데스크톱 앱 그룹을 작업 영역에 등록합니다. 이 cmdlet을 사용하여 작업 영역을 만들거나 기존 작업 영역을 사용할 수 있습니다.

등록 토큰을 만들기 위해 다음 cmdlet을 실행하여 세션 호스트의 호스트 풀 참가를 인증하고 로컬 컴퓨터의 새 파일에 저장합니다. *-ExpirationTime* 매개 변수를 사용하여 등록 토큰의 유효 기간을 지정할 수 있습니다.

>[!NOTE]
>토큰의 만료 날짜는 1시간 이상 한 달 이하여야 합니다. 이 한도를 벗어나 *-ExpirationTime* 을 설정하는 경우 이 cmdlet은 토큰을 만들지 않습니다.

```powershell
New-AzWvdRegistrationInfo -ResourceGroupName <resourcegroupname> -HostPoolName <hostpoolname> -ExpirationTime $((get-date).ToUniversalTime().AddDays(1).ToString('yyyy-MM-ddTHH:mm:ss.fffffffZ'))
```

예를 들어 2시간 후에 만료되는 토큰을 만들려면 다음 cmdlet을 실행합니다.

```powershell
New-AzWvdRegistrationInfo -ResourceGroupName <resourcegroupname> -HostPoolName <hostpoolname> -ExpirationTime $((get-date).ToUniversalTime().AddHours(2).ToString('yyyy-MM-ddTHH:mm:ss.fffffffZ'))
```

그런 다음, 이 cmdlet을 실행하여 호스트 풀의 기본 데스크톱 앱 그룹에 Azure Active Directory 사용자를 추가합니다.

```powershell
New-AzRoleAssignment -SignInName <userupn> -RoleDefinitionName "Desktop Virtualization User" -ResourceName <hostpoolname+"-DAG"> -ResourceGroupName <resourcegroupname> -ResourceType 'Microsoft.DesktopVirtualization/applicationGroups'
```

다음 cmdlet을 실행하여 호스트 풀의 기본 데스크톱 앱 그룹에 Azure Active Directory 사용자 그룹을 추가합니다.

```powershell
New-AzRoleAssignment -ObjectId <usergroupobjectid> -RoleDefinitionName "Desktop Virtualization User" -ResourceName <hostpoolname+"-DAG"> -ResourceGroupName <resourcegroupname> -ResourceType 'Microsoft.DesktopVirtualization/applicationGroups'
```

다음 cmdlet을 실행하여 등록 토큰을 변수로 내보냅니다. 이 변수는 나중에 [Azure Virtual Desktop 호스트 풀에 가상 머신 등록](#register-the-virtual-machines-to-the-azure-virtual-desktop-host-pool) 시 사용합니다.

```powershell
$token = Get-AzWvdRegistrationInfo -ResourceGroupName <resourcegroupname> -HostPoolName <hostpoolname>
```

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

아직 수행하지 않은 경우 Azure CLI에 대한 환경을 준비합니다.

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

로그인한 후 [az desktopvirtualization hostpool create](/cli/azure/desktopvirtualization#az_desktopvirtualization_hostpool_create) 명령을 사용하여 새 호스트 풀을 만들고 선택적으로 세션 호스트가 호스트 풀에 조인할 수 있는 등록 토큰을 만듭니다.

```azurecli
az desktopvirtualization hostpool create --name "MyHostPool" \
   --resource-group "MyResourceGroup" \
   --location "MyLocation" \
   --host-pool-type "Pooled" \
   --load-balancer-type "BreadthFirst" \
   --max-session-limit 999 \
   --personal-desktop-assignment-type "Automatic" \
   --registration-info expiration-time="2022-03-22T14:01:54.9571247Z" registration-token-operation="Update" \
   --sso-context "KeyVaultPath" \
   --description "Description of this host pool" \
   --friendly-name "Friendly name of this host pool" \
   --tags tag1="value1" tag2="value2"
```

---

## <a name="create-virtual-machines-for-the-host-pool"></a>호스트 풀에 대한 가상 머신 만들기

이제 Azure Virtual Desktop 호스트 풀에 참가할 수 있는 Azure 가상 머신을 만들 수 있습니다.

다음과 같은 여러 방법으로 가상 머신을 만들 수 있습니다.

- [Azure Gallery 이미지에서 가상 머신 만들기](../virtual-machines/windows/quick-create-portal.md#create-virtual-machine)
- [관리 이미지에서 가상 머신 만들기](../virtual-machines/windows/create-vm-generalized-managed.md)
- [관리되지 않는 이미지에서 가상 머신 만들기](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.compute/vm-from-user-image)

>[!NOTE]
>Windows 7을 사용하여 호스트 OS로 가상 머신을 배포하는 경우에는 만들기 및 배포 프로세스가 약간 다릅니다. 자세한 내용은 [ Virtual Desktop에서 Windows 7 가상 머신 배포](./virtual-desktop-fall-2019/deploy-windows-7-virtual-machine.md)를 참조하세요.

세션 호스트 가상 머신을 만든 후에는 [Windows 라이선스를 세션 호스트 VM에 적용](./apply-windows-license.md#apply-a-windows-license-to-a-session-host-vm)하여 다른 라이선스 비용 지출 없이 Windows 또는 Windows Server 가상 머신을 실행합니다.

## <a name="prepare-the-virtual-machines-for-azure-virtual-desktop-agent-installations"></a>Azure Virtual Desktop 에이전트 설치를 위한 가상 머신 준비

Azure Virtual Desktop 에이전트를 설치하고 가상 머신을 Azure Virtual Desktop 호스트 풀에 등록하기에 앞서 가상 머신을 준비하려면 다음 몇 가지가 필요합니다.

- 머신을 도메인에 조인해야 합니다. 이렇게 하면 들어오는 Azure Virtual Desktop 사용자를 해당 Azure Active Directory 계정에서 Active Directory 계정에 매핑하고 가상 머신에 대한 액세스를 허용할 수 있습니다.
- 가상 머신이 Windows Server OS를 실행할 경우 RDSH(원격 데스크톱 세션 호스트) 역할을 설치해야 합니다. RDSH 역할을 사용하면 Azure Virtual Desktop 에이전트를 올바르게 설치할 수 있습니다.

성공적인 도메인 조인을 위해 각 가상 머신에서 다음을 수행합니다.

1. 가상 머신을 만들 때 입력한 자격 증명으로 [가상 머신에 연결](../virtual-machines/windows/quick-create-portal.md#connect-to-virtual-machine)합니다.
2. 가상 머신에서 **제어판** 을 시작하고 **시스템** 을 선택합니다.
3. **컴퓨터 이름**, **설정 변경**, **변경...** 을 차례로 선택합니다.
4. **도메인** 을 선택한 다음, 가상 네트워크에 Active Directory 도메인을 입력합니다.
5. 도메인 조인 머신에 대한 권한이 있는 도메인 계정으로 인증합니다.

    >[!NOTE]
    > Azure AD DS(Azure Active Directory Domain Services) 환경에 VM을 조인하는 경우 도메인 가입 사용자가 [AAD DC 관리자 그룹](../active-directory-domain-services/tutorial-create-instance-advanced.md#configure-an-administrative-group)의 멤버이기도 한지 확인합니다.

>[!IMPORTANT]
>Windows Installer를 비활성화하는 정책이나 구성은 사용하지 않는 것이 좋습니다. Windows Installer를 비활성화하면 서비스에서 세션 호스트에 에이전트 업데이트를 설치할 수 없고 세션 호스트가 제대로 작동하지 않습니다.

## <a name="register-the-virtual-machines-to-the-azure-virtual-desktop-host-pool"></a>Azure Virtual Desktop 호스트 풀에 가상 머신 등록

Azure Virtual Desktop 호스트 풀에 가상 머신을 등록하는 작업은 Azure Virtual Desktop 에이전트 설치만큼 간단합니다.

Azure Virtual Desktop 에이전트를 등록하려면 각 가상 머신에서 다음을 수행합니다.

1. 가상 머신을 만들 때 입력한 자격 증명으로 [가상 머신에 연결](../virtual-machines/windows/quick-create-portal.md#connect-to-virtual-machine)합니다.
2. Azure Virtual Desktop 에이전트를 다운로드하여 설치합니다.
   - [Azure Virtual Desktop 에이전트](https://query.prod.cms.rt.microsoft.com/cms/api/am/binary/RWrmXv)를 다운로드합니다.
   - 설치 관리자를 실행합니다. 설치 관리자가 등록 토큰을 요청하면 **Get-AzWvdRegistrationInfo** cmdlet에서 가져온 값을 입력합니다.
3. Azure Virtual Desktop 에이전트 부팅 로더를 다운로드하여 설치합니다.
   - [Azure Virtual Desktop 에이전트 부팅 로더](https://query.prod.cms.rt.microsoft.com/cms/api/am/binary/RWrxrH)를 다운로드합니다.
   - 설치 관리자를 실행합니다.

>[!IMPORTANT]
>Azure에서 Azure Virtual Desktop 환경의 보안을 유지하도록 돕기 위해 VM에서 인바운드 포트 3389를 열지 않는 것이 좋습니다. Azure Virtual Desktop에서는 사용자가 인바운드 포트 3389를 열지 않아도 호스트 풀의 VM에 액세스할 수 있습니다. 문제 해결을 위해 포트 3389를 열어야 하는 경우 [Just-In-Time VM 액세스](../security-center/security-center-just-in-time.md)를 사용하는 것이 좋습니다. 또한 VM을 공용 IP에 할당하지 않는 것이 좋습니다.

## <a name="update-the-agent"></a>에이전트 업데이트

다음 상황 중 하나에 해당하는 경우 에이전트를 업데이트해야 합니다.

- 이전에 등록한 세션 호스트를 새 호스트 풀로 마이그레이션하려고 합니다.
- 업데이트 후에도 호스트 풀에 세션 호스트가 나타나지 않음

에이전트를 업데이트하려면 다음을 수행합니다.

1. VM에 관리자로 로그인합니다.
2. **서비스** 로 이동한 다음, **Rdagent** 및 **원격 데스크톱 에이전트 로더** 프로세스를 중지합니다.
3. 다음으로, 에이전트 및 부팅 로더 MSI를 찾습니다. 이는 **C:\DeployAgent** 폴더에 있거나 설치할 때 저장한 위치에 있습니다.
4. 다음 파일을 찾아 제거합니다.
     
     - Microsoft.RDInfra.RDAgent.Installer-x64-verx.x.x
     - Microsoft.RDInfra.RDAgentBootLoader.Installer-x64

   이러한 파일을 제거하려면 각 파일 이름을 마우스 오른쪽 단추로 클릭한 다음, **제거** 를 선택합니다.
5. 필요에 따라 다음 레지스트리 설정을 제거할 수도 있습니다.
     
     - Computer\HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\RDInfraAgent
     - Computer\HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\RDAgentBootLoader

6. 이러한 항목을 제거하면 이전 호스트 풀과의 모든 연결이 제거됩니다. 이 호스트를 서비스에 다시 등록하려면 [Azure Virtual Desktop 호스트 풀에 가상 머신 등록](create-host-pools-powershell.md#register-the-virtual-machines-to-the-azure-virtual-desktop-host-pool)의 지침을 따르세요.


## <a name="next-steps"></a>다음 단계

이제 호스트 풀이 만들어졌으므로 이 풀을 RemoteApp으로 채울 수 있습니다. Azure Virtual Desktop에서 앱을 관리하는 방법에 대한 자세한 내용은 앱 그룹 관리 자습서를 참조하세요.

> [!div class="nextstepaction"]
> [앱 그룹 관리 자습서](./manage-app-groups.md)
