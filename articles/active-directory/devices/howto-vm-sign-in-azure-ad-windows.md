---
title: Azure에서 Azure Active Directory를 사용하여 Windows 가상 머신에 로그인
description: Windows를 실행하는 Azure VM에 Azure AD 로그인
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: how-to
ms.date: 08/19/2021
ms.author: joflore
author: MicrosoftGuyJFlo
manager: karenhoran
ms.reviewer: sandeo
ms.custom: references_regions, devx-track-azurecli, subject-rbac-steps
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0842de3ef0d3bc154193413987ae2a57d87ead38
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128631536"
---
# <a name="login-to-windows-virtual-machine-in-azure-using-azure-active-directory-authentication"></a>Azure에서 Azure Active Directory 인증을 사용하여 Windows 가상 머신에 로그인

이제 조직은 Azure AD(Active Directory) 인증과 통합하여 Azure에서 Windows VM(가상 머신)의 보안을 향상할 수 있습니다. 이제 Azure AD를 핵심 인증 플랫폼으로 사용하여 **Windows Server 2019 Datacenter 에디션** 또는 **Windows 10 1809** 이상에 RDP할 수 있습니다. 또한 VM에 대한 액세스를 허용하거나 거부하는 Azure RBAC 및 조건부 액세스 정책을 중앙에서 컨트롤하고 적용할 수 있습니다. 이 문서에서는 Windows VM을 만들고 구성하고 Azure AD 인증을 사용해 로그인하는 방법을 보여 줍니다.

Azure에서 Azure AD 기반 인증을 사용하여 Windows VM에 로그인하는 경우 다음과 같은 여러 보안 이점이 있습니다.
- 회사 AD 자격 증명을 사용하여 Azure에서 Windows VM에 로그인합니다.
- 로컬 관리자 계정에 대한 의존성을 줄이면 자격 증명 손실/도난, 약한 자격 증명을 구성하는 사용자 등을 걱정할 필요가 없습니다.
- Azure AD 디렉터리에 구성된 암호 복잡성 및 암호 수명 정책을 통해 Windows VM을 보호할 수 있습니다.
- Azure RBAC(역할 기반 액세스 제어)를 사용하여 VM에 로그인할 수 있는 사용자를 일반 사용자 또는 관리자 권한으로 지정할 수 있습니다. 사용자가 팀에 합류하거나 떠나는 경우 적절한 액세스 권한을 부여하도록 VM에 대한 Azure RBAC 정책을 업데이트할 수 있습니다. 직원이 조직을 나가고 해당 사용자 계정을 비활성화하거나 Azure AD에서 제거한 경우 더 이상 리소스에 액세스할 수 없습니다.
- 조건부 액세스를 사용하면 Windows VM에 RDP를 사용하기 전에 다단계 인증과 낮은 사용자 및 로그인 위험과 같은 기타 신호를 요구하도록 정책을 구성합니다. 
- Azure 배포 및 감사 정책을 사용하여 Windows VM에 대한 Azure AD 로그인을 요구하고 VM에서 승인되지 않은 로컬 계정 사용에 플래그를 지정합니다.
- Azure Active Directory를 사용하여 Windows VM에 로그인하는 것은 Federation Services를 사용하는 고객에게도 적용됩니다.
- VDI 배포의 일부인 Azure Windows VM의 Intune을 사용하여 MDM 자동 등록을 통해 Azure AD 조인을 자동화하고 스케일링합니다. 자동 MDM 등록에는 Azure AD P1 라이선스가 필요합니다. Windows Server 2019 VM은 MDM 등록을 지원하지 않습니다.

> [!NOTE]
> 이 기능을 사용하도록 설정하면 Azure의 Windows VM이 Azure AD에 조인됩니다. 이 VM은 온-프레미스 AD 또는 Azure AD DS와 같은 다른 도메인에 조인할 수 없습니다. 조인해야 하는 경우 확장을 제거하여 Azure AD 테넌트에서 VM의 연결을 끊어야 합니다.

## <a name="requirements"></a>요구 사항

### <a name="supported-azure-regions-and-windows-distributions"></a>지원되는 Azure 지역 및 Windows 배포

이 기능이 지원되는 Windows 배포는 현재 다음과 같습니다.

- Windows Server 2019 Datacenter
- Windows 10 1809 이상

> [!IMPORTANT]
> Azure AD에 조인된 VM에 대한 원격 연결은 Azure AD에 등록되거나(Windows 10 20H1부터), Azure AD에 조인되거나 VM과 **동일한** 디렉터리에 하이브리드 Azure AD 조인된 Windows 10 PC에서만 허용됩니다. 

이제 다음 Azure 클라우드에서 이 기능을 사용할 수 있습니다.

- Azure 글로벌
- Azure Government
- Azure 중국

### <a name="network-requirements"></a>네트워크 요구 사항

Azure에서 Windows VM에 Azure AD 인증을 활성화하려면 VM 네트워크 구성에서 다음과 같이 TCP 443 포트를 통하는 엔드포인트에 대한 아웃바운드 액세스를 허용하는지 확인해야 합니다.

Azure 글로벌의 경우
- `https://enterpriseregistration.windows.net` - 디바이스 등록
- `http://169.254.169.254` - Azure Instance Metadata Service 엔드포인트
- `https://login.microsoftonline.com` - 인증 흐름
- `https://pas.windows.net` - Azure RBAC 흐름

Azure Government의 경우
- `https://enterpriseregistration.microsoftonline.us` - 디바이스 등록
- `http://169.254.169.254` - Azure Instance Metadata Service
- `https://login.microsoftonline.us` - 인증 흐름
- `https://pasff.usgovcloudapi.net` - Azure RBAC 흐름

Azure 중국의 경우
- `https://enterpriseregistration.partner.microsoftonline.cn` - 디바이스 등록
- `http://169.254.169.254` - Azure Instance Metadata Service 엔드포인트
- `https://login.chinacloudapi.cn` - 인증 흐름
- `https://pas.chinacloudapi.cn` - Azure RBAC 흐름

## <a name="enabling-azure-ad-login-in-for-windows-vm-in-azure"></a>Azure에서 Windows VM에 Azure AD 로그인 사용

Azure에서 Windows VM에 Azure AD 로그인을 사용하려면 먼저 Windows VM에 Azure AD 로그인 옵션을 사용하도록 설정해야 합니다. 그런 다음 VM에 로그인할 수 있는 권한이 있는 사용자에 대한 Azure 역할 할당을 구성해야 합니다.
Windows VM에 Azure AD 로그인을 사용하도록 설정하는 방법에는 여러 가지가 있습니다.

- Windows VM을 만들 때 Azure Portal 환경 사용
- Windows VM을 만들 때 **또는 기존 Windows VM에** Azure Cloud Shell 환경 사용

### <a name="using-azure-portal-create-vm-experience-to-enable-azure-ad-login"></a>Azure Portal을 사용하여 Azure AD 로그인을 사용할 수 있는 VM 환경 만들기

Windows Server 2019 Datacenter 또는 Windows 10 1809 이상 VM 이미지에 Azure AD 로그인을 사용하도록 설정할 수 있습니다. 

Azure에서 Azure AD 로그온을 사용하는 Windows Server 2019 Datacenter VM을 만들려면 다음을 수행합니다. 

1. VM을 만들 수 있는 액세스 권한이 있는 계정으로 [Azure Portal](https://portal.azure.com)에 로그인하고 **+ 리소스 만들기** 를 선택합니다.
1. Marketplace 검색 창에서 **Windows Server** 를 입력합니다.
   1. **Windows Server** 를 클릭하고 소프트웨어 플랜 드롭다운 목록에서 **Windows Server 2019 Datacenter** 를 선택합니다.
   1. **만들기** 를 클릭합니다.
1. “관리” 탭에서 Azure Active Directory 섹션의 **AAD 자격 증명으로 로그인** 옵션을 끄기에서 **켜기** 로 바꿔 사용하도록 설정합니다.
1. ID 섹션의 **시스템 할당 관리 ID** 가 **On** 으로 설정되어 있는지 확인합니다. Azure AD 자격 증명으로 로그인을 사용하도록 설정하면 이 작업이 자동으로 수행됩니다.
1. 가상 머신을 만드는 과정의 나머지 단계를 진행합니다. VM의 관리자 사용자 이름 및 암호를 만들어야 합니다.

![Azure AD 자격 증명으로 로그인 VM 만들기](./media/howto-vm-sign-in-azure-ad-windows/azure-portal-login-with-azure-ad.png)

> [!NOTE]
> Azure AD 자격 증명을 사용하여 VM에 로그인하려면 먼저 아래 섹션 중 하나에 설명된 대로 VM에 대한 역할 할당을 구성해야 합니다.

### <a name="using-the-azure-cloud-shell-experience-to-enable-azure-ad-login"></a>Azure Cloud Shell 환경을 사용하여 Azure AD 로그인 사용 설정

Azure Cloud Shell은 이 항목의 단계를 실행하는 데 무료로 사용할 수 있는 대화형 셸입니다. 계정에서 사용할 수 있도록 공용 Azure 도구가 Cloud Shell에 사전 설치 및 구성되어 있습니다. 간단하게 복사 단추를 선택하여 코드를 복사하고, Cloud Shell에 붙여넣고, Enter 키를 눌러 실행하면 됩니다. Cloud Shell을 여는 몇 가지 방법이 있습니다.

- 코드 블록의 오른쪽 위 모서리에서 **사용** 을 선택합니다.
- 브라우저에서 Cloud Shell을 엽니다.
- Azure Portal의 오른쪽 위 모서리에 있는 메뉴에서 [Cloud Shell](https://portal.azure.com) 단추를 선택합니다.

CLI를 로컬로 설치하여 사용하도록 선택한 경우 Azure CLI 버전 2.0.31 이상을 실행 중이어야 합니다. 버전을 찾으려면 az --version을 실행합니다. 설치 또는 업그레이드해야 하는 경우 [Azure CLI 설치](/cli/azure/install-azure-cli) 문서를 참조하세요.

1. [az group create](/cli/azure/group#az_group_create)를 사용하여 리소스 그룹을 만듭니다. 
1. 지원되는 지역에서 지원되는 배포를 사용하여 [az vm create](/cli/azure/vm#az_vm_create)로 VM을 만듭니다. 
1. Azure AD 로그인 VM 확장 설치 

다음 예제에서는 Win2019Datacenter를 사용하는 myVM이라는 VM을 southcentralus 지역에서 myResourceGroup이라는 리소스 그룹에 배포합니다. 다음 예제에서는 필요에 따라 고유한 리소스 그룹 및 VM 이름을 제공할 수 있습니다.

```AzureCLI
az group create --name myResourceGroup --location southcentralus

az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --image Win2019Datacenter \
    --assign-identity \
    --admin-username azureuser \
    --admin-password yourpassword
```

> [!NOTE]
> Azure AD 로그인 VM 확장을 설치하기 전에 가상 머신에서 시스템 할당 관리 ID를 사용하도록 설정해야 합니다.

VM 및 지원 리소스를 만드는 데 몇 분이 걸립니다.

마지막으로 Azure AD 로그인 VM 확장을 설치하여 Windows VM에 Azure AD 로그인을 사용하도록 설정합니다. VM 확장은 Azure 가상 머신에서 배포 후 구성 및 Automation 작업을 제공하는 작은 애플리케이션입니다. [az vm extension set](/cli/azure/vm/extension#az_vm_extension_set)를 사용하여 `myResourceGroup` 리소스 그룹에서 `myVM`이라는 VM에 AADLoginForWindows 확장을 설치합니다.

> [!NOTE]
> 기존 Windows Server 2019 또는 Windows 10 1809 이상 VM에 AADLoginForWindows 확장을 설치하여 Azure AD 인증을 사용하도록 설정할 수 있습니다. AZ CLI의 예제는 다음과 같습니다.

```AzureCLI
az vm extension set \
    --publisher Microsoft.Azure.ActiveDirectory \
    --name AADLoginForWindows \
    --resource-group myResourceGroup \
    --vm-name myVM
```

VM에 확장이 설치되면 `Succeeded`인 `provisioningState`가 표시됩니다.

## <a name="configure-role-assignments-for-the-vm"></a>VM에 대한 역할 할당 구성

VM을 만들었으므로 이제 VM에 로그인할 수 있는 사용자를 결정하는 Azure RBAC 정책을 구성해야 합니다. VM 로그인 권한 부여에는 두 개의 Azure 역할이 사용됩니다.

- **가상 머신 관리자 로그인**: 이 역할이 할당된 사용자는 관리자 권한으로 Azure 가상 머신에 로그인할 수 있습니다.
- **가상 머신 사용자 로그인**: 이 역할이 할당된 사용자는 일반 사용자 권한으로 Azure 가상 머신에 로그인할 수 있습니다.

> [!NOTE]
> 사용자가 RDP를 통해 VM에 로그인하려면 가상 머신 관리자 로그인 또는 가상 머신 사용자 로그인 역할 중 하나를 할당해야 합니다. VM에 대해 소유자 또는 기여자 역할이 할당된 Azure 사용자에게 RDP를 통해 VM에 로그인하는 권한이 자동으로 부여되지는 않습니다. 이는 가상 머신을 제어하는 사용자 집합과 가상 머신에 액세스하는 사용자 집합 사이에 감사된 분리를 제공하기 위해서입니다.

VM에 대한 역할 할당을 구성하는 방법에는 여러 가지가 있습니다.

- Azure AD Portal 환경 사용
- Azure Cloud Shell 환경 사용

> [!NOTE]
> 가상 머신 관리자 로그인 역할과 가상 머신 사용자 로그인 역할은 dataActions를 사용하므로 관리 그룹 범위에서 할당할 수 없습니다. 현재 이러한 역할은 구독, 리소스 그룹 또는 리소스 범위에서만 할당할 수 있습니다.

### <a name="using-azure-ad-portal-experience"></a>Azure AD Portal 환경 사용

Azure AD 사용 Windows Server 2019 Datacenter VM에 대한 역할 할당을 구성하려면:

1. **액세스 제어(IAM)** 를 선택합니다.

1. **추가** > **역할 할당 추가** 를 선택하여 역할 할당 추가 페이지를 엽니다.

1. 다음 역할을 할당합니다. 세부 단계에 대해서는 [Azure Portal을 사용하여 Azure 역할 할당](../../role-based-access-control/role-assignments-portal.md)을 참조하세요.
    
    | 설정 | 값 |
    | --- | --- |
    | 역할 | **가상 머신 관리자 로그인** 또는 **가상 머신 사용자 로그인** |
    | 다음에 대한 액세스 할당 | 사용자, 그룹, 서비스 주체 또는 관리 ID |

    ![Azure Portal에서 역할 할당 페이지를 추가합니다.](../../../includes/role-based-access-control/media/add-role-assignment-page.png)

### <a name="using-the-azure-cloud-shell-experience"></a>Azure Cloud Shell 환경 사용

다음 예제에서는 [az role assignment create](/cli/azure/role/assignment#az_role_assignment_create)를 사용하여 현재 Azure 사용자의 VM에 대한 가상 머신 관리자 로그인 역할을 할당합니다. 활성 Azure 계정의 사용자 이름은 [az account show](/cli/azure/account#az_account_show)를 사용하여 가져옵니다. 또한 범위는 [az vm show](/cli/azure/vm#az_vm_show)를 사용하여 이전 단계에서 만든 VM으로 설정됩니다. 범위는 리소스 그룹 또는 구독 수준에서도 할당할 수 있고, 일반적인 Azure RBAC 상속 권한이 적용됩니다. 자세한 내용은 [Azure에서 Azure Active Directory 인증을 사용하여 Linux 가상 머신에 로그인](../../virtual-machines/linux/login-using-aad.md)을 참조하세요.

```   AzureCLI
$username=$(az account show --query user.name --output tsv)
$vm=$(az vm show --resource-group myResourceGroup --name myVM --query id -o tsv)

az role assignment create \
    --role "Virtual Machine Administrator Login" \
    --assignee $username \
    --scope $vm
```

> [!NOTE]
> AAD 도메인과 로그온 사용자 이름 도메인이 일치하지 않으면 `--assignee`의 사용자 이름뿐 아니라 `--assignee-object-id`를 사용하여 사용자 계정의 개체 ID를 지정해야 합니다. [az ad user list](/cli/azure/ad/user#az_ad_user_list)를 사용하여 사용자 계정의 개체 ID를 가져올 수 있습니다.

Azure RBAC를 사용하여 Azure 구독 리소스에 대한 액세스를 관리하는 방법에 대한 자세한 내용은 다음 문서를 참조하세요.

- [Azure CLI를 사용하여 Azure 역할 할당](../../role-based-access-control/role-assignments-cli.md)
- [Azure Portal을 사용하여 Azure 역할 할당](../../role-based-access-control/role-assignments-portal.md)
- [Azure PowerShell을 사용하여 Azure 역할 할당](../../role-based-access-control/role-assignments-powershell.md)

## <a name="using-conditional-access"></a>조건부 액세스 사용

Azure AD 로그인을 사용하도록 설정된 Azure의 Windows VM에 대한 액세스 권한을 부여하기 전에 다단계 인증 또는 사용자 로그인 위험 검사와 같은 조건부 액세스 정책을 적용할 수 있습니다. 조건부 액세스 정책을 적용하려면 클라우드 앱 또는 작업 할당 옵션에서 “**Azure Windows VM 로그인**” 앱을 선택하고, 로그인 위험을 조건으로 사용하거나 권한 부여 액세스 제어로 다단계 인증을 사용해야 합니다. 

> [!NOTE]
> ‘Azure Windows VM 로그인’ 앱에 대한 액세스를 요청하기 위한 권한 부여 액세스 제어로 ‘다단계 인증 필요’를 사용하는 경우 Azure에서 대상 Windows VM에 대한 RDP 세션을 시작하는 클라이언트의 일부로 다단계 인증 클레임을 제공해야 합니다. Windows 10 클라이언트에서 이를 수행하는 유일한 방법은 비즈니스용 Windows Hello PIN 또는 RDP 클라이언트에 생체 인식 인증을 사용하는 것입니다. 생체 인식 인증에 대한 지원이 Windows 10 버전 1809의 RDP 클라이언트에 추가되었습니다. 비즈니스용 Windows Hello 인증을 사용하는 원격 데스크톱은 인증서 신뢰 모델을 사용하고 현재 키 신뢰 모델에 사용할 수 없는 배포에서만 사용할 수 있습니다.

> [!WARNING]
> 사용자별로 활성화되는/적용되는 Azure AD Multi-Factor Authentication은 VM 로그인에 지원되지 않습니다.

## <a name="log-in-using-azure-ad-credentials-to-a-windows-vm"></a>Azure AD 자격 증명을 사용하여 Windows VM에 로그인

> [!IMPORTANT]
> Azure AD에 조인된 VM에 대한 원격 연결은 Azure AD에 등록되거나(필요한 최소 빌드 20H1), Azure AD에 조인되거나 VM과 **동일한** 디렉터리에 하이브리드 Azure AD 조인된 Windows 10 PC에서만 허용됩니다. 또한 Azure AD 자격 증명을 사용하는 RDP의 경우 사용자는 두 개의 Azure 역할, 즉 가상 컴퓨터 관리자 로그인 또는 가상 컴퓨터 사용자 로그인 중 하나에 속해야 합니다. Azure AD에 등록된 Windows 10 PC를 사용하는 경우 `AzureAD\UPN` 형식(예: `AzureAD\john@contoso.com`)으로 자격 증명을 입력해야 합니다. 지금은 AADLoginForWindows 확장과 함께 Azure Active Directory 인증을 사용하여 로그인하는 데 Azure Bastion을 사용할 수 없습니다. 직접 RDP만 지원됩니다.

Azure AD를 사용하여 Windows Server 2019 가상 머신에 로그인하려면: 

1. Azure AD 로그온을 사용하도록 설정된 가상 머신의 개요 페이지로 이동합니다.
1. **연결** 을 선택하여 가상 머신에 연결 블레이드를 엽니다.
1. **RDP 파일 다운로드** 를 선택합니다.
1. **열기** 를 선택하여 원격 데스크톱 연결 클라이언트를 시작합니다.
1. **연결** 을 선택하여 Windows 로그온 대화 상자를 시작합니다.
1. Azure AD 자격 증명을 사용하여 로그온합니다.

이제 VM 사용자 또는 VM 관리자와 같은 할당된 역할 권한으로 Windows Server 2019 Azure 가상 머신에 로그인되었습니다. 

> [!NOTE]
> Azure Portal의 가상 머신 개요 페이지로 이동하고 연결 옵션을 사용할 필요 없이 .RDP 파일을 컴퓨터에 로컬로 저장하여 가상 머신에 대한 향후 원격 데스크톱 연결을 시작할 수 있습니다.

## <a name="using-azure-policy-to-ensure-standards-and-assess-compliance"></a>Azure Policy를 사용하여 표준 보장 및 규정 준수 평가

Azure Policy를 사용하여 새 Windows 가상 머신과 기존 Windows 가상 머신에 Azure AD 로그인을 사용하도록 설정되어 있는지 확인하고 Azure Policy의 규정 준수 대시보드에서 사용자 환경의 규정 준수 상태를 대규모로 평가합니다. 이 기능을 사용하면 다양한 수준의 적용을 사용할 수 있습니다. Azure AD 로그인을 사용하지 않는 환경 내에서 신규 및 기존 Windows VM에 플래그를 지정할 수 있습니다. 또한 Azure Policy를 사용하여 Azure AD 로그인을 사용하지 않는 새 Windows VM에 Azure AD 확장을 배포하고 기존 Windows VM을 동일한 표준으로 수정할 수 있습니다. 해당 기능 외에도 Azure Policy를 사용하여 머신에서 만든 승인되지 않은 로컬 계정이 있는 Windows VM을 검색하고 플래그를 지정할 수 있습니다. 자세한 내용은 [Azure Policy](../../governance/policy/overview.md)를 검토하세요.

## <a name="troubleshoot"></a>문제 해결

### <a name="troubleshoot-deployment-issues"></a>배포 문제 해결

VM이 Azure AD 조인 프로세스를 완료하려면 AADLoginForWindows 확장을 설치해야 합니다. VM 확장이 올바르게 설치되지 않은 경우 다음 단계를 수행합니다.

1. 로컬 관리자 계정을 사용하여 VM에 RDP로 연결하고 다음 아래에서 `CommandExecution.log` 파일을 검사합니다.
   
   `C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.ActiveDirectory.AADLoginForWindows\0.3.1.0.`

   > [!NOTE]
   > 초기 실패 후 확장이 다시 시작되면 배포 오류가 있는 로그가 `CommandExecution_YYYYMMDDHHMMSSSSS.log`로 저장됩니다. "
1. VM에서 PowerShell 창을 열고 Azure 호스트에서 실행되는 IMDS(Instance Metadata Service) 엔드포인트에 대한 쿼리가 다음을 반환하는지 확인합니다.

   | 실행할 명령 | 예상 출력 |
   | --- | --- |
   | `curl -H @{"Metadata"="true"} "http://169.254.169.254/metadata/instance?api-version=2017-08-01"` | Azure VM에 대한 올바른 정보 |
   | `curl -H @{"Metadata"="true"} "http://169.254.169.254/metadata/identity/info?api-version=2018-02-01"` | Azure 구독과 연결된 유효한 테넌트 ID |
   | `curl -H @{"Metadata"="true"} "http://169.254.169.254/metadata/identity/oauth2/token?resource=urn:ms-drs:enterpriseregistration.windows.net&api-version=2018-02-01"` | 이 VM에 할당된 관리 ID에 대해 Azure Active Directory에서 발급한 유효한 액세스 토큰 |

   > [!NOTE]
   > 액세스 토큰은 [calebb.net](http://calebb.net/)과 같은 도구를 사용하여 디코딩할 수 있습니다. 액세스 토큰의 `appid`가 VM에 할당된 관리 ID와 일치하는지 확인합니다.

1. PowerShell을 사용하여 VM에서 필요한 엔드포인트에 액세스할 수 있는지 확인합니다.
   
   - `curl https://login.microsoftonline.com/ -D -`
   - `curl https://login.microsoftonline.com/<TenantID>/ -D -`
   - `curl https://enterpriseregistration.windows.net/ -D -`
   - `curl https://device.login.microsoftonline.com/ -D -`
   - `curl https://pas.windows.net/ -D -`

   > [!NOTE]
   > `<TenantID>`를 Azure 구독에 연결된 Azure AD 테넌트 ID로 바꿉니다.<br/> `enterpriseregistration.windows.net` 및 `pas.windows.net`는 예상되는 동작인 404 찾을 수 없음을 반환해야 합니다.
            
1. `dsregcmd /status`를 실행하여 디바이스 상태를 볼 수 있습니다. 목표는 디바이스 상태가 `AzureAdJoined : YES`로 표시되는 것입니다.

   > [!NOTE]
   > Azure AD 조인 작업은 이벤트 뷰어에서 `User Device Registration\Admin` 로그 아래 캡처됩니다.

AADLoginForWindows 확장이 특정 오류 코드와 함께 실패하는 경우 다음 단계를 수행할 수 있습니다.

#### <a name="issue-1-aadloginforwindows-extension-fails-to-install-with-terminal-error-code-1007-and-exit-code--2145648574"></a>문제 1: AADLoginForWindows 확장을 설치하지 못했습니다(터미널 오류 코드 '1007' 및 종료 코드: -2145648574).

이 종료 코드는 확장이 Azure AD 테넌트 정보를 쿼리할 수 없기 때문에 `DSREG_E_MSI_TENANTID_UNAVAILABLE`로 변환됩니다.

1. Azure VM이 Instance Metadata Service에서 TenantID를 검색할 수 있는지 확인합니다.

   - 로컬 관리자로 VM에 RDP로 연결하고 VM의 관리자 권한 PowerShell 창에서 다음 명령을 실행하여 엔드포인트가 유효한 테넌트 ID를 반환하는지 확인합니다.
      
      - `curl -H Metadata:true http://169.254.169.254/metadata/identity/info?api-version=2018-02-01`

1. VM 관리자가 AADLoginForWindows 확장을 설치하려고 하지만 애초에 시스템 할당 관리 ID가 VM을 사용하도록 설정하지 않았습니다. VM의 ID 블레이드로 이동합니다. 시스템 할당됨 탭에서 상태가 켜기로 설정되어 있는지 확인합니다.

#### <a name="issue-2-aadloginforwindows-extension-fails-to-install-with-exit-code--2145648607"></a>문제 2: AADLoginForWindows 확장을 설치하지 못했습니다(종료 코드: -2145648607).

이 종료 코드는 확장이 `https://enterpriseregistration.windows.net` 엔드포인트에 연결할 수 없기 때문에 `DSREG_AUTOJOIN_DISC_FAILED`로 변환됩니다.

1. PowerShell을 사용하여 VM에서 필요한 엔드포인트에 액세스할 수 있는지 확인합니다.

   - `curl https://login.microsoftonline.com/ -D -`
   - `curl https://login.microsoftonline.com/<TenantID>/ -D -`
   - `curl https://enterpriseregistration.windows.net/ -D -`
   - `curl https://device.login.microsoftonline.com/ -D -`
   - `curl https://pas.windows.net/ -D -`
   
   > [!NOTE]
   > `<TenantID>`를 Azure 구독에 연결된 Azure AD 테넌트 ID로 바꿉니다. 테넌트 ID를 찾아야 하는 경우 커서로 계정 이름을 가리켜서 테넌트 ID를 가져오거나 Azure Portal에서 **Azure Active Directory > 속성 > 디렉터리 ID** 를 선택하면 됩니다.<br/>`enterpriseregistration.windows.net` 및 `pas.windows.net`는 예상되는 동작인 404 찾을 수 없음을 반환해야 합니다.

1. ‘호스트 `<URL>`을 확인할 수 없습니다’와 함께 명령이 실패하는 경우 다음 명령을 실행하여 VM에서 사용 중인 DNS 서버를 확인합니다
   
   `nslookup <URL>`

   > [!NOTE] 
   > `<URL>`을 엔드포인트에 사용되는 정규화된 도메인 이름(예: `login.microsoftonline.com`)으로 바꿉니다.

1. 다음으로 공용 DNS 서버를 지정하면 명령이 성공할 수 있는지 확인합니다.

   `nslookup <URL> 208.67.222.222`

1. 필요한 경우 Azure VM이 속한 네트워크 보안 그룹에 할당된 DNS 서버를 변경합니다.

#### <a name="issue-3-aadloginforwindows-extension-fails-to-install-with-exit-code-51"></a>문제 3: AADLoginForWindows 확장을 설치하지 못했습니다(종료 코드: 51).

종료 코드 51은 ‘이 확장은 VM의 운영 체제에서 지원되지 않습니다’로 변환됩니다.

AADLoginForWindows 확장은 Windows Server 2019 또는 Windows 10(빌드 1809 이상)에만 설치하도록 되어 있습니다. Windows 버전이 지원되는지 확인하세요. Windows 빌드가 지원되지 않는 경우 VM 확장을 제거합니다.

### <a name="troubleshoot-sign-in-issues"></a>로그인 문제 해결

Azure AD 자격 증명을 사용하여 RDP를 시도할 때 발생하는 몇 가지 일반적인 오류에는 할당된 Azure 역할 없음, 권한 없는 클라이언트 또는 2FA 로그인 방법 필요 등이 있습니다. 다음 정보를 사용하여 이러한 문제를 해결할 수 있습니다.

`dsregcmd /status`를 실행하여 디바이스 및 SSO 상태를 볼 수 있습니다. 목표는 디바이스 상태가 `AzureAdJoined : YES`로 표시되고, `SSO State`가 `AzureAdPrt : YES`로 표시되는 것입니다.

또한 Azure AD 계정을 사용한 RDP 로그인은 이벤트 뷰어에서 `AAD\Operational` 이벤트 로그 아래 캡처됩니다.

#### <a name="azure-role-not-assigned"></a>Azure 역할 할당되지 않음

VM에 대한 원격 데스크톱 연결을 시작할 때 다음과 같은 오류 메시지가 표시되는 경우: 

- 이 디바이스를 사용할 수 없도록 계정이 구성되어 있습니다. 자세한 내용은 시스템 관리자에게 문의하세요.

![이 디바이스를 사용할 수 없도록 계정이 구성되어 있습니다.](./media/howto-vm-sign-in-azure-ad-windows/rbac-role-not-assigned.png)

사용자에게 가상 머신 관리자 로그인 또는 가상 머신 사용자 로그인 역할을 부여한 VM에 대한 [Azure RBAC 정책을 구성](../../virtual-machines/linux/login-using-aad.md)했는지 확인합니다.

> [!NOTE]
> Azure 역할 할당에 문제가 발생하는 경우 [Azure RBAC 문제 해결](../../role-based-access-control/troubleshooting.md#azure-role-assignments-limit)을 참조하세요.
 
#### <a name="unauthorized-client"></a>권한 없는 클라이언트

VM에 대한 원격 데스크톱 연결을 시작할 때 다음과 같은 오류 메시지가 표시되는 경우: 

- 자격 증명이 작동하지 않았습니다.

![자격 증명이 작동하지 않았습니다](./media/howto-vm-sign-in-azure-ad-windows/your-credentials-did-not-work.png)

원격 데스크톱 연결을 시작하는 데 사용하는 Windows 10 PC가 Azure AD에 조인되어 있거나, VM이 조인된 디렉터리와 동일한 Azure AD 디렉터리에 하이브리드 Azure AD 조인되어 있는지 확인합니다. 디바이스 ID에 대한 자세한 내용은 [디바이스 ID란?](./overview.md)을 참조하세요.

> [!NOTE]
> Windows 10 Build 20H1에서는 Azure AD 등록 PC가 VM에 대한 RDP 연결을 시작하는 기능에 대한 지원이 추가되었습니다. Azure AD 등록(Azure AD 조인 또는 하이브리드 Azure AD 조인이 아닌) PC를 RDP 클라이언트로 사용하여 VM에 대한 연결을 시작하는 경우 `AzureAD\UPN` 형식(예: `AzureAD\john@contoso.com`)으로 자격 증명을 입력해야 합니다.

Azure AD 조인이 완료된 후 AADLoginForWindows 확장이 제거되지 않았는지 확인합니다.

또한 ‘네트워크 보안: 온라인 ID를 사용하도록 이 컴퓨터에 대한 PKU2U 인증 요청을 허용’ 보안 정책을 서버 **및** 클라이언트 모두에서 사용하도록 설정해야 합니다.
 
#### <a name="mfa-sign-in-method-required"></a>MFA 로그인 방법 필요

VM에 대한 원격 데스크톱 연결을 시작할 때 다음과 같은 오류 메시지가 표시되는 경우: 

- 사용하려는 로그인 방법은 허용되지 않습니다. 다른 로그인 방법을 시도하거나 시스템 관리자에게 문의하세요.

![사용하려는 로그인 방법은 허용되지 않습니다.](./media/howto-vm-sign-in-azure-ad-windows/mfa-sign-in-method-required.png)

리소스에 액세스하기 전에 MFA(다단계 인증)를 요구하는 조건부 액세스 정책을 구성한 경우 VM에 대한 원격 데스크톱 연결을 시작하는 Windows 10 PC가 Windows Hello와 같은 강력한 인증 방법을 사용하여 로그인하도록 해야 합니다. 원격 데스크톱 연결에 강력한 인증 방법을 사용하지 않는 경우 이전 오류가 표시됩니다.

- 자격 증명이 작동하지 않았습니다.

![자격 증명이 작동하지 않았습니다](./media/howto-vm-sign-in-azure-ad-windows/your-credentials-did-not-work.png)

> [!WARNING]
> 사용자별로 활성화되는/적용되는 Azure AD Multi-Factor Authentication은 VM 로그인에 지원되지 않습니다. 이 설정을 사용하면 “Your credentials do not work.”(자격 증명이 작동하지 않습니다.)라는 오류 메시지와 함께 로그인에 실패합니다.

다음 단계를 수행하여 사용자별 MFA 설정을 제거하면 위의 문제를 해결할 수 있습니다.

```

# Get StrongAuthenticationRequirements configure on a user
(Get-MsolUser -UserPrincipalName username@contoso.com).StrongAuthenticationRequirements
 
# Clear StrongAuthenticationRequirements from a user
$mfa = @()
Set-MsolUser -UserPrincipalName username@contoso.com -StrongAuthenticationRequirements $mfa
 
# Verify StrongAuthenticationRequirements are cleared from the user
(Get-MsolUser -UserPrincipalName username@contoso.com).StrongAuthenticationRequirements

```

비즈니스용 Windows Hello를 배포하지 않은 경우와 현재 이 옵션을 사용할 수 없는 경우 MFA를 요구하는 클라우드 앱 목록에서 “**Azure Windows VM 로그인**” 앱을 제외하는 조건부 액세스 정책을 구성하여 MFA 요구 사항을 제외할 수 있습니다. 비즈니스용 Windows Hello에 대한 자세한 내용은 [비즈니스용 Windows Hello 개요](/windows/security/identity-protection/hello-for-business/hello-identity-verification)를 참조하세요.

> [!NOTE]
> RDP를 사용한 비즈니스용 Windows Hello PIN 인증은 여러 버전의 Windows 10에서 지원됐지만 RDP를 사용한 생체 인식 인증에 대한 지원은 Windows 10 버전 1809에서 추가되었습니다. RDP 도중 비즈니스용 Windows Hello 인증 사용은 인증서 신뢰 모델을 사용하고 현재 키 신뢰 모델에 사용할 수 없는 배포에서만 사용할 수 있습니다.
 
[Azure AD 피드백 포럼](https://feedback.azure.com/forums/169401-azure-active-directory?category_id=166032)에서 이 기능에 대한 사용자 의견을 공유하거나, 사용에 관한 문제를 보고할 수 있습니다.

## <a name="next-steps"></a>다음 단계

Azure Active Directory에 대한 자세한 내용은 [Azure Active Directory란?](../fundamentals/active-directory-whatis.md)을 참조하세요.
