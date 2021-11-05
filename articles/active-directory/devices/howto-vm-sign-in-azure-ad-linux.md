---
title: Azure에서 Azure Active Directory를 사용하여 Linux 가상 머신에 로그인(미리 보기)
description: Linux를 실행하는 Azure VM에 Azure AD 로그인
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: how-to
ms.date: 10/21/2021
ms.author: joflore
author: MicrosoftGuyJFlo
manager: karenhoran
ms.reviewer: sandeo
ms.custom: references_regions, devx-track-azurecli, subject-rbac-steps
ms.collection: M365-identity-device-management
ms.openlocfilehash: aeca09f5763cf11edc13cecd2df0c267fad8a23d
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131049751"
---
# <a name="preview-login-to-a-linux-virtual-machine-in-azure-with-azure-active-directory-using-ssh-certificate-based-authentication"></a>미리 보기: Azure에서 SSH 인증서 기반 인증을 사용하여 Azure Active Directory로 Linux 가상 머신에 로그인

Azure에서 Linux VM(가상 머신)의 보안을 강화하려면 Azure AD(Active Directory) 인증으로 통합하면 됩니다. 이제 Azure AD를 핵심 인증 플랫폼 및 인증 기관으로 사용하여 AD 및 SSH 인증서 기반 인증을 통해 Linux VM에 SSH할 수 있습니다. 이 기능을 통해 조직은 VM에 대한 액세스를 관리하는 Azure RBAC(역할 기반 액세스 제어) 및 조건부 액세스 정책을 중앙에서 제어하고 적용할 수 있습니다. 이 문서에서는 SSH 인증서 기반 인증을 사용하여 Linux VM을 생성 및 구성하고 Azure AD로 로그인하는 방법을 보여 줍니다.

> [!IMPORTANT]
> 이 기능은 현재 공개 미리 보기로 제공되고 있습니다. [디바이스 코드 흐름을 사용하는 이전 버전은 2021년 8월 15일부터 지원이 중단되었습니다](../../virtual-machines/linux/login-using-aad.md). 이전 버전에서 이 버전으로 마이그레이션하려면 [이전 버전에서 마이그레이션 미리 보기](#migration-from-previous-preview) 섹션을 참조하세요.
> 이 미리 보기는 서비스 수준 약정 없이 제공되며 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다. 테스트 후에 삭제하려는 테스트 가상 머신에서 이 기능을 사용합니다. 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

Azure에서 SSH 인증서 기반 인증을 Azure AD와 사용하여 Linux VM에 로그인하는 경우 다음과 같은 많은 혜택이 있습니다.

- Azure AD 자격 증명을 사용하여 Azure Linux VM에 로그인합니다.
- 사용자에게 SSH 키를 배포하거나 배포하는 모든 Azure Linux VM에서 SSH 공개 키를 프로비전할 필요 없이 SSH 키 기반 인증을 받습니다. 이 환경은 무단 액세스를 유발할 수 있는 부실 SSH 공개 키의 확산에 대한 우려를 갖는 것보다 훨씬 간단합니다.
- 로컬 관리자 계정, 자격 증명 탈취, 취약한 자격 증명의 영향을 줄입니다.
- Azure AD에 대해 구성된 암호 복잡성 및 암호 수명 정책은 Linux VM을 보호하는 데 도움이 될 수 있습니다.
- Azure 역할 기반 액세스 제어 또는 관리자 권한으로 VM에 로그인할 수 있는 사용자를 일반 사용자로 지정합니다. 사용자가 팀에 합류하거나 떠나는 경우 적절한 액세스 권한을 부여하도록 VM에 대한 Azure RBAC 정책을 업데이트할 수 있습니다. 직원이 조직을 나가고 해당 사용자 계정을 비활성화하거나 Azure AD에서 제거한 경우 더 이상 리소스에 액세스할 수 없습니다.
- 조건부 액세스의 경우, Linux VM에 SSH를 수행하기 전에 다단계 인증을 요구하거나 SSH에 사용하는 클라이언트 디바이스를 관리 디바이스(예: 규격 디바이스 또는 하이브리드 Azure AD 조인된)로 요구하도록 정책을 구성합니다. 
- Azure 배포 및 감사 정책을 사용하여 VM에 대한 Azure AD 로그인을 요구하고 VM에서 승인되지 않은 로컬 계정 사용에 플래그를 지정합니다.
- Azure Active Directory를 사용하여 Linux VM에 로그인하는 것은 페더레이션 서비스를 사용하는 고객에게도 적용할 수 있습니다.

## <a name="supported-linux-distributions-and-azure-regions"></a>지원되는 Azure 지역 및 Linux 배포

현재 이 기능의 미리 보기 기간 동안 지원되는 지역에 배포 시 다음과 같은 Linux 배포가 지원됩니다.

| 배포 | 버전 |
| --- | --- |
| CentOS | CentOS 7, CentOS 8 |
| Debian | Debian 9, Debian 10 |
| openSUSE | openSUSE Leap 42.3, openSUSE Leap 15.1+ |
| RedHat Enterprise Linux | RHEL 7.4 ~ RHEL 7.10, RHEL 8.3+ |
| SUSE Linux Enterprise Server | SLES 12, SLES 15.1+ |
| Ubuntu Server | Ubuntu Server 16.04 ~ Ubuntu Server 20.04 |

현재 이 기능의 미리 보기 기간 동안 다음과 같은 Azure 지역이 지원됩니다.

- Azure 글로벌

 
AKS(Azure Kubernetes Service) 클러스터에서 이 확장을 사용하는 것은 지원되지 않습니다. 자세한 내용은 [AKS 지원 정책](../../aks/support-policies.md)을 참조하세요.

CLI를 로컬로 설치하여 사용하도록 선택한 경우, 이 자습서는 Azure CLI 버전 2.22.1 이상을 실행 중인 상태를 요구합니다. `az --version`을 실행하여 버전을 찾습니다. 설치 또는 업그레이드해야 하는 경우 [Azure CLI 설치](/cli/azure/install-azure-cli)를 참조하세요.

## <a name="requirements-for-login-with-azure-ad-using-ssh-certificate-based-authentication"></a>SSH 인증서 기반 인증을 사용하여 Azure AD로 로그인하기 위한 요구 사항

Azure에서 Linux VM에 대한 SSH 인증서 기반 인증을 사용하여 Azure AD 로그인을 사용하도록 설정하려면 다음 네트워크, 가상 머신 및 클라이언트(SSH 클라이언트) 요구 사항을 충족하는지 확인해야 합니다.

### <a name="network"></a>네트워크

VM 네트워크 구성에서는 TCP 포트 443을 통해 다음 엔드포인트에 대한 아웃바운드 액세스를 허용해야 합니다.

Azure Global의 경우

- `https://packages.microsoft.com` -패키지 설치 및 업그레이드의 경우
- `http://169.254.169.254` - Azure Instance Metadata Service 엔드포인트
- `https://login.microsoftonline.com` – PAM(플러그형 인증 모듈) 기반 인증 흐름의 경우.
- `https://pas.windows.net` – Azure RBAC 흐름의 경우

Azure Government의 경우

- `https://packages.microsoft.com` -패키지 설치 및 업그레이드의 경우
- `http://169.254.169.254` - Azure Instance Metadata Service 엔드포인트
- `https://login.microsoftonline.us` – PAM(플러그형 인증 모듈) 기반 인증 흐름의 경우.
- `https://pasff.usgovcloudapi.net` – Azure RBAC 흐름의 경우

Azure 중국의 경우

- `https://packages.microsoft.com` -패키지 설치 및 업그레이드의 경우
- `http://169.254.169.254` - Azure Instance Metadata Service 엔드포인트
- `https://login.chinacloudapi.cn` – PAM(플러그형 인증 모듈) 기반 인증 흐름의 경우.
- `https://pas.chinacloudapi.cn` – Azure RBAC 흐름의 경우

### <a name="virtual-machine"></a>가상 머신

VM이 다음 기능으로 구성되어 있는지 확인합니다.

- 시스템이 할당한 관리 ID 이 옵션은 Azure Portal을 사용하여 VM을 만들고 Azure AD 로그인 옵션을 선택하는 경우 자동으로 선택됩니다. Azure CLI를 사용하여 신규 또는 기존 VM에서 시스템이 할당한 관리 ID를 사용하도록 설정할 수도 있습니다.
- `aadsshlogin` 및 `aadsshlogin-selinux`(적절한 경우). 이러한 패키지는 AADSSHLoginForLinux VM 확장을 사용하여 설치됩니다. 이 확장은 Azure Portal을 사용하여 VM을 만들고 Azure AD 로그인을 사용하도록 설정(관리 탭)하여 설치되거나 Azure CLI를 통해 설치됩니다.

### <a name="client"></a>클라이언트

클라이언트가 다음 요구 사항을 충족하는지 확인합니다.

- SSH 클라이언트는 인증을 위해 OpenSSH 기반 인증서를 지원해야 합니다. Az CLI(2.21.1 이상)를 OpenSSH(Windows 10 버전 1803 이상에 포함) 또는 Azure Cloud Shell과 함께 사용하여 이 요구 사항을 충족할 수 있습니다. 
- Az CLI용 SSH 확장. `az extension add --name ssh`를 사용하여 이를 설치할 수 있습니다. 확장이 사전 설치된 Azure Cloud Shell을 사용할 때는 이 확장을 설치할 필요가 없습니다.
- Az CLI 또는 OpenSSH 인증서를 지원하는 Azure Cloud Shell 이외의 다른 SSH 클라이언트를 사용하는 경우에도, SSH 확장과 함께 Az CLI를 사용하여 임시 SSH 인증서와 선택적으로 구성 파일을 검색한 다음, SSH 클라이언트에서 구성 파일을 사용해야 합니다.
- 클라이언트에서 VM의 공용 또는 개인 IP로의 TCP 연결(연결된 머신에 대한 ProxyCommand 또는 SSH 전달도 작동함).

## <a name="enabling-azure-ad-login-in-for-linux-vm-in-azure"></a>Azure에서 Linux VM에 대해 Azure AD 로그인 사용하도록 설정

Azure에서 Linux VM에 대해 Azure AD 로그인을 사용하려면, 먼저 Linux VM에 대해 Azure AD 로그인 옵션을 사용하도록 설정하고, VM에 로그인할 권한이 있는 사용자에 대한 Azure 역할 할당을 구성한 다음, Linux VM에 SSH에 대한 Az CLI 또는 Az Cloud Shell과 같은 OpensSSH를 지원하는 SSH 클라이언트를 사용해야 합니다. Linux VM에 대해 Azure AD 로그인을 사용하도록 설정할 수 있는 다음과 같은 여러 가지 방법이 있습니다.

- Linux VM 생성 시 Azure Portal 환경
- Windows VM 생성 시 또는 기존 Linux VM에 대한 Azure Cloud Shell 환경

### <a name="using-azure-portal-create-vm-experience-to-enable-azure-ad-login"></a>Azure Portal을 사용하여 Azure AD 로그인을 사용할 수 있는 VM 환경 만들기

Azure Portal을 사용하여 위에서 언급한 지원되는 Linux 배포판에 대해 Azure AD 로그인을 사용하도록 설정할 수 있습니다.

예를 들어, Azure AD 로그온을 사용하여 Azure에서 Ubuntu Server 18.04 LTS VM을 만들려면 다음을 수행합니다.

1. VM을 만들 수 있는 액세스 권한이 있는 계정으로 Azure Portal에 로그인하고 **+ 리소스 생성** 을 선택합니다.
1. **인기** 보기의 **Ubuntu Server 18.04 LTS** 에서 **생성** 을 클릭합니다.
1. **관리** 탭에서, 
   1. 박스에 체크하여 **Azure Active Directory(미리 보기)에 로그인** 을 사용하도록 설정할 수 있습니다.
   1. **시스템이 할당한 관리 ID** 가 선택되어 있는지 확인합니다.
1. 가상 머신을 만드는 과정의 나머지 단계를 진행합니다. 이 미리 보기 중에는 사용자 이름 및 암호/SSH 공개 키를 사용하여 관리자 계정을 만들어야 합니다.
 
### <a name="using-the-azure-cloud-shell-experience-to-enable-azure-ad-login"></a>Azure Cloud Shell 환경을 사용하여 Azure AD 로그인 사용 설정

Azure Cloud Shell은 이 항목의 단계를 실행하는 데 무료로 사용할 수 있는 대화형 셸입니다. 계정에서 사용할 수 있도록 공용 Azure 도구가 Cloud Shell에 사전 설치 및 구성되어 있습니다. 간단하게 복사 단추를 선택하여 코드를 복사하고, Cloud Shell에 붙여넣고, Enter 키를 눌러 실행하면 됩니다. Cloud Shell을 여는 몇 가지 방법이 있습니다.

- 코드 블록의 오른쪽 위 모서리에서 사용을 선택합니다.
- 브라우저에서 Cloud Shell을 엽니다.
- Azure Portal의 오른쪽 위 모서리에 있는 메뉴에서 Cloud Shell 단추를 선택합니다.

CLI를 로컬로 설치하여 사용하도록 선택한 경우, 이 문서는 Azure CLI 버전 2.22.1 이상을 실행 중인 상태를 요구합니다. `az --version`을 실행하여 버전을 찾습니다. 설치 또는 업그레이드해야 하는 경우 Azure CLI 설치 문서를 참조하세요.

1. [az group create](/cli/azure/group#az_group_create)를 사용하여 리소스 그룹을 만듭니다.
1. 지원되는 지역에서 지원되는 배포를 사용하여 [az vm create](/cli/azure/vm#az_vm_create&preserve-view=true)로 VM을 만듭니다.
1. [az vm extension set](/cli/azure/vm/extension?view=azure-cli-latest#az_vm_extension_set&preserve-view=true)를 통해 Azure AD 로그인 VM 확장을 설치합니다.

다음 예시에서는 *Ubuntu 18.04 LTS* 를 사용하는 *myVM* 이라는 VM을 *southcentralus* 지역에서 *AzureADLinuxVMPreview* 이라는 리소스 그룹에 배포합니다. 그런 다음, *Azure AD 로그인 VM 확장* 을 설치하여 Linux VM에 Azure AD 로그인을 사용하도록 설정합니다. VM 확장은 Azure 가상 머신에서 배포 후 구성 및 Automation 작업을 제공하는 작은 애플리케이션입니다.

필요에 따라 테스트 요구 사항을 지원하기 위해 예시를 사용자 지정할 수 있습니다.

```azurecli-interactive
az group create --name AzureADLinuxVMPreview --location southcentralus

az vm create \
    --resource-group AzureADLinuxVMPreview \
    --name myVM \   
    --image UbuntuLTS \
    --assign-identity \
    --admin-username azureuser \
    --generate-ssh-keys

az vm extension set \
    --publisher Microsoft.Azure.ActiveDirectory \
    --name AADSSHLoginForLinux \
    --resource-group AzureADLinuxVMPreview \
    --vm-name myVM
```

VM 및 지원 리소스를 만드는 데 몇 분이 걸립니다.

AADSSHLoginForLinux 확장은 Azure AD 인증을 사용하도록 설정하기 위해 실행 중인 VM 에이전트가 있는 기존(지원되는 배포) Linux VM에 설치할 수 있습니다. 이전에 만든 VM에 이 확장을 배포하는 경우, 해당 머신에 최소 1GB의 메모리가 할당되어 있는지 확인해야 하며, 그렇지 않으면 확장을 설치하지 못합니다.

VM에 확장이 설치되면 provisioningState에서 성공이 표시됩니다. VM에서 확장을 설치하려면 실행 중인 [VM 에이전트](../../virtual-machines/extensions/agent-linux.md)가 필요합니다.

## <a name="configure-role-assignments-for-the-vm"></a>VM에 대한 역할 할당 구성

VM을 만들었으므로 이제 VM에 로그인할 수 있는 사용자를 결정하는 Azure RBAC 정책을 구성해야 합니다. VM 로그인 권한 부여에는 두 개의 Azure 역할이 사용됩니다.

- **가상 머신 관리자 로그인**: 이 역할이 할당된 사용자는 관리자 권한으로 Azure 가상 머신에 로그인할 수 있습니다.
- **가상 머신 사용자 로그인**: 이 역할이 할당된 사용자는 일반 사용자 권한으로 Azure 가상 머신에 로그인할 수 있습니다.
 
사용자가 SSH를 통해 VM에 로그인하려면, 가상 머신 관리자 로그인 또는 가상 머신 사용자 로그인 역할 중 하나를 할당해야 합니다. VM에 대해 소유자 또는 참가자 역할이 할당된 Azure 사용자는 SSH를 통해 VM에 로그인하는 권한을 자동으로 갖지 않습니다. 이러한 구분은 가상 머신을 제어하는 사용자 집합과 가상 머신에 액세스할 수 있는 사용자 집합 간에 감사된 분리를 하기 위한 것입니다. 

VM에 대한 역할 할당을 구성할 수 있는 다음과 같은 여러 가지 방법이 있습니다.

- Azure AD 포털 환경
- Azure Cloud Shell 환경

> [!Note]
> 가상 머신 관리자 로그인 역할과 가상 머신 사용자 로그인 역할은 dataActions를 사용하므로 관리 그룹 범위에서 할당할 수 없습니다. 현재 이러한 역할은 구독, 리소스 그룹, 리소스 범위에서만 할당할 수 있습니다. 구독 당 [Azure 역할 할당 한도](../../role-based-access-control/troubleshooting.md#azure-role-assignments-limit)를 초과하는 위험을 방지하기 위해, 역할을 개별 VM 수준이 아닌 구독 또는 리소스 수준에서 할당하는 것이 좋습니다.

### <a name="using-azure-ad-portal-experience"></a>Azure AD Portal 환경 사용

Azure AD 사용 Linux VM에 대한 역할 할당을 구성하려면 다음을 수행합니다.

1. **액세스 제어(IAM)** 를 선택합니다.

1. **추가** > **역할 할당 추가** 를 선택하여 역할 할당 추가 페이지를 엽니다.

1. 다음 역할을 할당합니다. 세부 단계에 대해서는 [Azure Portal을 사용하여 Azure 역할 할당](../../role-based-access-control/role-assignments-portal.md)을 참조하세요.
    
    | 설정 | 값 |
    | --- | --- |
    | 역할 | **가상 머신 관리자 로그인** 또는 **가상 머신 사용자 로그인** |
    | 다음에 대한 액세스 할당 | 사용자, 그룹, 서비스 주체 또는 관리 ID |

    ![Azure Portal에서 역할 할당 페이지를 추가합니다.](../../../includes/role-based-access-control/media/add-role-assignment-page.png)

몇 분이 지나면 선택한 범위에서 보안 주체에 역할이 할당됩니다.
 
### <a name="using-the-azure-cloud-shell-experience"></a>Azure Cloud Shell 환경 사용

다음 예제에서는 [az role assignment create](/cli/azure/role/assignment#az_role_assignment_create)를 사용하여 현재 Azure 사용자의 VM에 대한 가상 머신 관리자 로그인 역할을 할당합니다. 현재 Azure 계정의 사용자 이름은 [az account show](/cli/azure/account#az_account_show)를 사용하여 가져옵니다. 또한 범위는 [az vm show](/cli/azure/vm#az_vm_show)를 사용하여 이전 단계에서 만든 VM으로 설정됩니다. 범위는 리소스 그룹 또는 구독 수준에서도 할당할 수 있고, 일반적인 Azure RBAC 상속 권한이 적용됩니다.

```azurecli-interactive
username=$(az account show --query user.name --output tsv)
vm=$(az vm show --resource-group AzureADLinuxVMPreview --name myVM --query id -o tsv)

az role assignment create \
    --role "Virtual Machine Administrator Login" \
    --assignee $username \
    --scope $vm
```

> [!NOTE]
> Azure AD 도메인과 로그온 사용자 이름 도메인이 일치하지 않으면 `--assignee`의 사용자 이름뿐만 아니라 `--assignee-object-id`을 사용하여 사용자 계정의 개체 ID를 지정해야 합니다. [az ad user list](/cli/azure/ad/user#az_ad_user_list)를 사용하여 사용자 계정의 개체 ID를 가져올 수 있습니다.

Azure RBAC를 사용하여 Azure 구독 리소스에 대한 액세스를 관리하는 방법에 대한 자세한 내용은 [Azure 역할 할당 단계](../../role-based-access-control/role-assignments-steps.md) 문서를 참조하세요.

## <a name="install-ssh-extension-for-az-cli"></a>Az CLI용 SSH 확장을 설치합니다.

Azure Cloud Shell을 사용하는 경우, Az CLI 및 Az CLI용 SSH 확장의 최소 필수 버전이 모두 Cloud Shell 환경에 이미 포함되어 있으므로 다른 설정은 필요하지 않습니다.

다음 명령을 실행하여 Az CLI에 대한 SSH 확장을 추가합니다.

```azurecli
az extension add --name ssh
```

확장에 필요한 최소 버전은 0.1.4입니다. 다음 명령을 사용하여 설치된 SSH 확장 버전을 확인합니다.

```azurecli
az extension show --name ssh
```

## <a name="using-conditional-access"></a>조건부 액세스 사용

사용자에 대한 MFA를 요구하거나, SSH 클라이언트를 실행하는 디바이스에 대한 규격/하이브리드 Azure AD 조인된 디바이스를 요구하는 조건부 액세스 정책을 적용할 수 있습니다. Azure AD 로그인을 사용하도록 설정된 Azure에서 Linux VM에 대한 액세스 권한을 부여하기 전에 낮은 사용자 및 로그인 위험을 확인하세요. 

조건부 액세스 정책을 적용하려면 클라우드 앱 또는 작업 할당 옵션에서 “Azure Linux VM 로그인” 앱을 선택한 다음, 다단계 인증 및/또는 규격/하이브리드 Azure AD 조인된 디바이스에 대한 요구를 충족한 후 사용자 및/또는 로그인 위험을 조건으로 사용하고 액세스 제어를 권한 부여로 사용해야 합니다.

> [!NOTE]
> SSH 클라이언트를 실행하는 클라이언트 디바이스에서 디바이스 규격 또는 하이브리드 Azure AD 조인이 필요한 조건부 액세스 정책의 규약은 Windows 및 macOS에서 실행되는 Az CLI 에서만 작동합니다. Linux 또는 Azure Cloud Shell에서 Az CLI를 사용하는 경우에는 지원되지 않습니다.

## <a name="login-using-azure-ad-user-account-to-ssh-into-the-linux-vm"></a>Azure AD 사용자 계정을 사용해 로그인하여 Linux VM에 SSH

### <a name="using-az-cli"></a>Az CLI 사용

먼저 az login을 실행한 다음 az ssh vm을 실행합니다.

```azurecli
az login 
```

이 명령은 브라우저 창을 실행하며, 사용자는 해당 Azure AD 계정을 사용하여 로그인할 수 있습니다. 

다음 예시에서는 VM에 대한 적절한 IP 주소를 자동으로 확인합니다.

```azurecli
az ssh vm -n myVM -g AzureADLinuxVMPreview
```

메시지가 표시되면 로그인 페이지에서 Azure AD 로그인 자격 증명을 입력하고, MFA를 수행하고, 또한/또는 디바이스 검사를 충족합니다. az CLI 세션이 필요한 조건부 액세스 조건 기준을 충족하지 않는 경우에만 메시지가 표시됩니다. 브라우저 창을 닫고 SSH 프롬프트로 돌아가면 VM에 자동으로 연결됩니다.

이제 할당된 대로 VM 사용자 또는 VM 관리자와 같은 역할 권한이 있는 Azure Linux 가상 머신에 로그인했습니다. 사용자 계정에 가상 머신 관리자 로그인 역할이 할당된 경우 sudo를 사용하여 루트 권한이 필요한 명령을 실행할 수 있습니다.

### <a name="using-az-cloud-shell"></a>Az Cloud Shell 사용

클라이언트 컴퓨터에 로컬로 설치할 필요 없이 Az Cloud Shell을 사용하여 VM에 연결할 수 있습니다. Azure Portal 오른쪽 위 모서리에 있는 셸 아이콘을 클릭하여 Cloud Shell을 시작합니다.
 
Az Cloud Shell은 로그인한 사용자의 컨텍스트에서 세션에 자동으로 연결됩니다. Linux용 Azure AD 로그인 미리 보기 중에, **az login을 다시 실행하고 대화형 로그인 흐름을 진행해야 합니다**.

```azurecli
az login
```

그런 다음 일반 az ssh vm 명령을 사용하여 VM의 이름, 리소스 그룹 또는 IP 주소를 사용하여 연결할 수 있습니다.

```azurecli
az ssh vm -n myVM -g AzureADLinuxVMPreview
```

> [!NOTE]
> Az Cloud Shell을 사용하는 경우, 디바이스 규격 또는 하이브리드 Azure AD 조인을 요구하는 조건부 액세스 정책 규약이 지원되지 않습니다.

### <a name="login-using-azure-ad-service-principal-to-ssh-into-the-linux-vm"></a>Azure AD 서비스 주체를 사용해 로그인하여 Linux VM에 SSH

Azure CLI는 사용자 계정 대신 서비스 주체를 통해 인증을 지원합니다. 서비스 주체는 특정 사용자와 연결되지 않은 계정이므로, 고객은 VM에 SSH하는 데 이를 사용하여 보유한 자동화 시나리오를 지원할 수 있습니다. 서비스 주체에는 VM 관리자 또는 VM 사용자 권한이 할당되어 있어야 합니다. 구독 또는 리소스 그룹 수준에서 사용 권한을 할당합니다. 

다음 예시에서는 리소스 그룹 수준에서 서비스 주체에 VM 관리자 권한을 할당합니다. 서비스 주체 개체 ID, 구독 ID, 리소스 그룹 이름 필드를 대체합니다.

```azurecli
az role assignment create \
    --role "Virtual Machine Administrator Login" \
    --assignee-object-id <service-principal-objectid> \
    --assignee-principal-type ServicePrincipal \
    --scope “/subscriptions/<subscription-id>/resourceGroups/<resourcegroup-name>"
```

다음 예시를 사용해 서비스 주체를 사용하여 Azure CLI 인증합니다. 서비스 주체를 사용하여 로그인하는 방법에 대한 자세한 내용은 [서비스 주체를 사용하여 Azure CLI에 로그인](/cli/azure/authenticate-azure-cli#sign-in-with-a-service-principal) 문서를 참조하세요.

```azurecli
az login --service-principal -u <sp-app-id> -p <password-or-cert> --tenant <tenant-id>
```

서비스 주체를 사용한 인증이 완료되면 일반 Az CLI SSH 명령을 사용하여 VM에 연결합니다.

```azurecli
az ssh vm -n myVM -g AzureADLinuxVMPreview
```

### <a name="exporting-ssh-configuration-for-use-with-ssh-clients-that-support-openssh"></a>OpenSSH를 지원하는 SSH 클라이언트에서 사용할 SSH 구성 내보내기

Azure AD를 사용하여 Azure Linux VM에 로그인하면 OpenSSH 인증서 및 구성 내보내기가 지원되므로 OpenSSH 기반 인증서를 지원하는 SSH 클라이언트를 사용하여 Azure AD에 로그인할 수 있습니다. 다음 예시에서는 VM에 할당된 모든 IP 주소에 대한 구성을 내보냅니다.

```azurecli
az ssh config --file ~/.ssh/config -n myVM -g AzureADLinuxVMPreview
```

또는 IP 주소만 지정하여 구성을 내보낼 수 있습니다. 예제의 IP 주소를 VM의 공용 또는 개인 IP 주소로 바꿉니다(개인 IP의 연결은 직접 가져와야 함). 이 명령에 대한 도움말을 위해 `az ssh config -h`를 입력합니다.

```azurecli
az ssh config --file ~/.ssh/config --ip 10.11.123.456
```

그런 다음 일반적인 OpenSSH 사용을 통해 VM에 연결할 수 있습니다. OpenSSH를 사용하는 모든 SSH 클라이언트를 통해 연결을 수행할 수 있습니다.

## <a name="sudo-and-azure-ad-login"></a>Sudo 및 Azure AD 로그인

VM 관리자 역할이 할당된 사용자는 Linux VM에 SSH를 성공적으로 수행하면 다른 상호 작용 또는 인증 요구 사항 없이 sudo를 실행할 수 있습니다. VM 사용자 역할이 할당된 사용자는 sudo를 실행할 수 없습니다.

## <a name="virtual-machine-scale-set-support"></a>가상 머신 확장 집합 지원

가상 머신 확장 집합이 지원되지만, 가상 머신 확장 집합 VM을 설정하고 연결하는 단계와 약간 다릅니다.

1. 가상 머신 확장 집합을 만들거나 이미 있는 가상 머신 확장 집합을 선택합니다. 가상 머신 확장 집합에 대한 시스템이 할당한 관리 ID를 사용하도록 설정합니다.

```azurecli
az vmss identity assign --vmss-name myVMSS --resource-group AzureADLinuxVMPreview
```

2. 가상 머신 확장 집합에 Azure AD 확장을 설치합니다.

```azurecli
az vmss extension set --publisher Microsoft.Azure.ActiveDirectory --name Azure ADSSHLoginForLinux --resource-group AzureADLinuxVMPreview --vmss-name myVMSS
```

가상 머신 확장 집합에는 일반적으로 공용 IP 주소가 없으므로 Azure Virtual Network에 도달할 수 있는 다른 컴퓨터에서 연결해야 합니다. 이 예제에서는 가상 머신 확장 집합 VM의 개인 IP를 사용하여 동일한 가상 네트워크의 머신에서 연결하는 방법을 보여 줍니다. 

```azurecli
az ssh vm --ip 10.11.123.456
```

> [!NOTE]
> `--resource-group` 및 `--name` 스위치를 사용하여 가상 머신 확장 집합 VM의 IP 주소를 자동으로 확인할 수 없습니다.

## <a name="migration-from-previous-preview"></a>이전 미리 보기에서 마이그레이션

디바이스 코드 흐름을 기반으로 하는 Linux용 Azure AD 로그인의 이전 버전을 사용하는 고객의 경우 Azure CLI를 사용하여 다음 단계를 완료합니다.

1. VM에서 AADLoginForLinux 확장을 제거합니다.
   
   ```azurecli
   az vm extension delete -g MyResourceGroup --vm-name MyVm -n AADLoginForLinux
   ```

1. VM에서 시스템이 할당한 관리 ID를 사용합니다.

   ```azurecli
   az vm identity assign -g myResourceGroup -n myVm
   ```

1. VM에 AADSSHLoginForLinux 확장을 설치합니다.

    ```azurecli
    az vm extension set \
        --publisher Microsoft.Azure.ActiveDirectory \
        --name AADSSHLoginForLinux \
        --resource-group myResourceGroup \
        --vm-name myVM
    ```

## <a name="using-azure-policy-to-ensure-standards-and-assess-compliance"></a>Azure Policy를 사용하여 표준 보장 및 규정 준수를 평가합니다.

Azure Policy를 사용하여 새 Linux 가상 머신과 기존 Linux 가상 머신에 Azure AD 로그인을 사용하도록 설정되어 있는지 확인하고 Azure Policy의 규정 준수 대시보드에서 사용자 환경의 규정 준수 상태를 대규모로 평가합니다. 이 기능을 사용하면 다양한 수준의 규약을 사용할 수 있습니다. Azure AD 로그인을 사용하도록 설정하지 않은 환경 내에서 신규 및 기존 Linux VM에 플래그를 지정할 수 있습니다. Azure Policy를 사용하여 Azure AD 로그인을 사용하도록 설정하지 않은 새 Linux VM에 Azure AD 확장을 배포하고 기존 Linux VM을 동일한 표준으로 수정할 수도 있습니다. 해당 기능 외에도 Azure Policy를 사용하여 머신에서 승인되지 않은 로컬 계정을 만든 Linux VM을 검색하고 플래그를 지정할 수 있습니다. 자세한 내용은 [Azure Policy](../../governance/policy/overview.md)를 검토하세요.

## <a name="troubleshoot-sign-in-issues"></a>로그인 문제 해결

Azure AD 자격 증명을 사용하여 SSH를 이용할 때 할당된 Azure 역할이 없거나 로그인 요청 프롬프트가 반복될 수 있습니다. 이러한 문제를 해결하려면 다음 섹션을 사용합니다.

### <a name="could-not-retrieve-token-from-local-cache"></a>로컬 캐시에서 토큰을 검색할 수 없습니다.

az 로그인을 다시 실행하고 대화형 로그인 흐름을 거쳐야 합니다. [Az Cloud Shell 사용](#using-az-cloud-shell) 섹션을 검토합니다.

### <a name="access-denied-azure-role-not-assigned"></a>액세스 거부: Azure 역할 할당되지 않음

SSH 프롬프트에서 다음과 같은 오류가 표시되면 사용자에게 가상 머신 관리자 로그인 또는 가상 머신 사용자 로그인 역할 중 하나를 부여한 VM에 대한 Azure RBAC 정책을 구성했는지 확인합니다. Azure 역할 할당에 문제가 발생하는 경우 [Azure RBAC 문제 해결](../../role-based-access-control/troubleshooting.md#azure-role-assignments-limit) 문서를 참조하세요.

### <a name="extension-install-errors"></a>확장 설치 오류

기존 컴퓨터에 대한 AADSSHLoginForLinux VM 확장 설치는 다음과 같은 알려진 오류 코드 중 하나를 출력하면서 실패합니다.

#### <a name="non-zero-exit-code-22"></a>0이 아닌 종료 코드: 22

AADSSHLoginForLinux VM 확장의 상태는 포털에서 전환 중으로 표시됩니다.

원인 1: 이 오류는 시스템이 할당한 관리 ID가 필요하기 때문에 발생합니다.

해결 방법 1: 다음 작업을 수행합니다.

1. 실패한 확장을 제거합니다.
1. Azure VM에서 시스템이 할당한 관리 ID를 사용하도록 설정합니다.
1. 확장 설치 명령을 다시 실행합니다.

#### <a name="non-zero-exit-code-23"></a>0이 아닌 종료 코드: 23

AADSSHLoginForLinux VM 확장의 상태는 포털에서 전환 중으로 표시됩니다.

원인 1: 이 오류는 이전 AADLoginForLinux VM 확장이 여전히 설치되어 있기 때문에 발생합니다.

해결 방법 1: 다음 작업을 수행합니다.

1. VM에서 이전 AADLoginForLinux VM 확장을 제거합니다. 새 AADSSHLoginForLinux VM 확장의 상태가 포털에서 프로비전 성공으로 변경됩니다.

#### <a name="az-ssh-vm-fails-with-keyerror-access_token"></a>Az ssh vm이 KeyError: 'access_token'을 출력하며 실패합니다.

원인 1: 오래된 버전의 Azure CLI 클라이언트를 사용하고 있습니다.

해결 방법 1: Azure CLI 클라이언트를 버전 2.21.0 이상으로 업그레이드합니다.

#### <a name="ssh-connection-closed"></a>SSH 연결 닫힘

사용자가 az 로그인을 사용하여 성공적으로 로그인한 후에 `az ssh vm -ip <addres>` 또는 `az ssh vm --name <vm_name> -g <resource_group>`를 사용하여 VM에 연결하는 데 실패하고 *<ip_address> port 22로 인한 연결 종료* 가 표시됩니다.

원인 1: 이 VM의 범위 내에서 사용자가 가상 머신 관리자/사용자 로그인 Azure RBAC 역할에 할당되지 않았습니다.

해결 방법 1: 이 VM의 범위 내에서 가상 머신 관리자/사용자 로그인 Azure RBAC 역할 중 하나에 사용자를 추가합니다.

원인 2: 사용자가 필수 Azure RBAC 역할에 있지만 시스템이 할당한 관리 ID가 VM에서 사용하지 않도록 설정되었습니다.

해결 방법 2: 다음 작업을 수행합니다.

1. VM에서 시스템이 할당한 관리 ID를 사용하도록 설정합니다.
1. `az ssh vm --ip <ip_address>`을 사용하여 연결을 시도하기 전에 몇 분 기다립니다.

### <a name="virtual-machine-scale-set-connection-issues"></a>가상 머신 확장 집합 연결 문제

가상 머신 확장 집합 인스턴스가 이전 모델을 실행하는 경우 가상 머신 확장 집합 VM 연결이 실패할 수 있습니다. 가상 머신 확장 집합 인스턴스를 최신 모델로 업그레이드하면 문제를 해결할 수 있습니다. 특히, Azure AD 로그인 확장이 설치된 후에 업그레이드가 수행되지 않은 경우에 해당합니다. 인스턴스를 업그레이드하면 표준 가상 머신 확장 집합 구성이 개별 인스턴스에 적용됩니다.

## <a name="preview-feedback"></a>미리 보기 피드백

[Azure AD 피드백 포럼](https://feedback.azure.com/d365community/forum/22920db1-ad25-ec11-b6e6-000d3a4f0789)에서 이 미리 보기 기능에 대한 사용자 의견을 공유하거나, 사용에 관한 문제를 보고할 수 있습니다.

## <a name="next-steps"></a>다음 단계
