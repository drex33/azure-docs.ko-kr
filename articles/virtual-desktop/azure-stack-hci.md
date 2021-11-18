---
title: Azure Stack HCI (미리 보기)에 대 한 Azure 가상 데스크톱 설정-Azure
description: Azure Stack HCI (미리 보기)에 대 한 Azure 가상 데스크톱을 설정 하는 방법입니다.
author: Heidilohr
ms.topic: how-to
ms.date: 11/02/2021
ms.author: helohr
manager: femila
ms.custom: ignite-fall-2021
ms.openlocfilehash: ce89ec45e9c4f59107b205f04ce2c3d83afc8ce2
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/17/2021
ms.locfileid: "132704024"
---
# <a name="set-up-azure-virtual-desktop-for-azure-stack-hci-preview"></a>Azure Stack HCI (미리 보기)에 대 한 Azure 가상 데스크톱 설정

> [!IMPORTANT]
> Azure Stack HCI 용 Azure 가상 데스크톱은 현재 미리 보기로 제공 됩니다.
> 베타, 미리 보기로 제공되거나 아직 일반 공급으로 릴리스되지 않은 Azure 기능에 적용되는 약관은 [Microsoft Azure 미리 보기에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

Azure Stack HCI (미리 보기)에 대 한 Azure 가상 데스크톱을 사용 하 여 온-프레미스 Azure Stack HCI 인프라에서 Azure 가상 데스크톱 세션 호스트를 사용할 수 있습니다. 자세한 내용은 [AZURE STACK HCI 용 Azure 가상 데스크톱 (미리 보기)](azure-stack-hci-overview.md)을 참조 하세요.

## <a name="requirements"></a>요구 사항

Azure Stack HCI 용 Azure 가상 데스크톱을 사용 하려면 다음 항목이 필요 합니다.

- [Azure에 등록 된 AZURE STACK HCI 클러스터](/azure-stack/hci/deploy/register-with-azure).

- 모든 필수 관리자 권한이 있는 Azure 가상 데스크톱 세션 호스트 풀 만들기에 대 한 Azure 구독.

- [Azure Active Directory와 동기화 된 온-프레미스 AD (Active Directory)](/azure/architecture/reference-architectures/identity/azure-ad)입니다.

- 온-프레미스 네트워크에서 Azure에 안정적으로 연결 합니다.

- 온-프레미스 네트워크에서 가상 컴퓨터에 대 한 Azure 가상 데스크톱의 [필수 url 목록](safe-url-list.md) 에 나열 된 모든 필수 url에 액세스 합니다.

## <a name="configure-azure-virtual-desktop-for-azure-stack-hci"></a>Azure Stack HCI에 대 한 Azure 가상 데스크톱 구성

Azure Stack HCI에 대 한 Azure 가상 데스크톱을 설정 하려면:

1. [호스트 풀 설정 프로세스 시작](create-host-pools-azure-marketplace.md#begin-the-host-pool-setup-process)의 지침에 따라 가상 컴퓨터 없이 새 호스트 풀을 만듭니다. 해당 섹션의 끝 부분에서이 문서로 돌아와서 2 단계에서 시작 합니다.

2. 유효성 검사 환경 속성을 사용 하도록 [호스트 풀을 유효성 검사 호스트 풀로 정의](create-validation-host-pool.md#define-your-host-pool-as-a-validation-host-pool) 의 단계에 따라 새로 만든 호스트 풀을 유효성 검사 호스트 풀로 구성 합니다.

3. [작업 영역 정보](create-host-pools-azure-marketplace.md#workspace-information) 의 지침에 따라 자신에 대 한 작업 영역을 만듭니다.

4. [새 VM 만들기](/azure-stack/hci/manage/vm#create-a-new-vm)의 지침에 따라 Azure Stack HCI 인프라에 새 가상 컴퓨터를 배포 합니다. 지원 되는 OS를 사용 하 여 VM을 배포 하 고 도메인에 가입 시킵니다.

   >[!NOTE]
   >VM이 Windows Server OS를 실행 하는 경우 RDSH (원격 데스크톱 세션 호스트) 역할을 설치 합니다.

5. Azure에서 연결 된 컴퓨터 에이전트를 설치 하 여 Azure Arc를 통해 새 가상 컴퓨터를 관리할 수 있도록 합니다. [Azure Arc 사용 서버를 사용 하 여 하이브리드 컴퓨터 커넥트](../azure-arc/servers/learn/quick-enable-hybrid-vm.md) 의 지시에 따라 Windows 에이전트를 가상 머신에 설치 합니다.

6. [Azure 가상 데스크톱 에이전트](agent-overview.md)를 설치 하 여 이전에 만든 Azure 가상 데스크톱 호스트 풀에 가상 컴퓨터를 추가 합니다. 그런 다음 azure [가상 데스크톱 호스트 풀에 Vm 등록](create-host-pools-powershell.md#register-the-virtual-machines-to-the-azure-virtual-desktop-host-pool) 의 지침에 따라 Vm을 Azure 가상 데스크톱 서비스에 등록 합니다.

7. [앱 그룹 만들기 및 사용자 할당 관리](manage-app-groups.md) 의 지시에 따라 테스트를 위한 앱 그룹을 만들고 사용자 액세스 권한을 할당 합니다.

8. [웹 클라이언트로](./user-documentation/connect-web.md) 이동 하 여 사용자에 게 새 배포에 대 한 액세스 권한을 부여 합니다.

## <a name="optional-configurations"></a>선택적 구성

Azure Stack HCI에 대 한 Azure 가상 데스크톱을 설정 했으므로 이제 배포 요구 사항에 따라 몇 가지 추가 작업을 수행할 수 있습니다.

### <a name="create-a-profile-container-using-a-file-share-on-azure-stack-hci"></a>Azure Stack HCI에서 파일 공유를 사용 하 여 프로필 컨테이너 만들기

파일 공유를 사용 하 여 프로필 컨테이너를 만들려면 다음을 수행 합니다.

1. 단일 또는 클러스터 된 Windows 서버 VM 배포에 파일 공유를 배포 합니다. 파일 서버 역할을 사용 하는 Windows 서버 vm은 세션 호스트 vm이 배포 되는 동일한 클러스터에서 공동 배치 수도 있습니다.

2. 가상 머신을 만들 때 입력한 자격 증명으로 가상 머신에 연결합니다.

3. 가상 머신에서 **제어판** 을 시작하고 **시스템** 을 선택합니다.

4. 컴퓨터 이름을 선택 하 고 **설정 변경** 을 선택한 다음 **변경 ...** 을 선택 합니다.

5. **도메인** 을 선택 하 고 가상 네트워크에 Active Directory 도메인을 입력 합니다.

6. 도메인 조인 머신에 대한 권한이 있는 도메인 계정으로 인증합니다.

7. [파일 공유 역할을 하는 Vm 준비](create-host-pools-user-profile.md#prepare-the-virtual-machine-to-act-as-a-file-share-for-user-profiles) 의 지침에 따라 배포를 위한 vm을 준비 합니다.

8. [FSLogix 프로필 컨테이너 구성](create-host-pools-user-profile.md#configure-the-fslogix-profile-container) 의 지침에 따라 프로필 컨테이너를 사용 하도록 구성 합니다.

### <a name="download-supported-os-images-from-azure-marketplace"></a>Azure Marketplace에서 지원 되는 OS 이미지 다운로드

배포에 대 한 Azure 가상 데스크톱과 Azure Stack HCI 지원에 대 한 모든 OS 이미지를 실행할 수 있습니다. Azure 가상 데스크톱이 지 원하는 Os에 대 한 자세한 내용은 [지원 되는 VM OS 이미지](overview.md#supported-virtual-machine-os-images)를 참조 하세요.

이미지를 다운로드 하는 두 가지 옵션이 있습니다.

- 원하는 OS 이미지를 사용 하 여 VM을 배포한 다음 [Azure에서 Windows VHD 다운로드](../virtual-machines/windows/download-vhd.md)의 지침을 따릅니다.
- VM을 배포 하지 않고 Azure에서 Windows VHD (가상 하드 디스크)를 다운로드 합니다.

VM을 배포 하지 않고 Windows VHD를 다운로드 하는 경우 몇 가지 추가 단계가 있습니다. VM을 배포 하지 않고 Azure에서 VHD를 다운로드 하려면 다음 섹션의 지침을 순서 대로 완료 해야 합니다.

### <a name="requirements-to-download-a-vhd-without-a-vm"></a>VM 없이 VHD를 다운로드 하기 위한 요구 사항

시작 하기 전에 Azure에 연결 되어 있고 명령 프롬프트나 bash 환경에서 [Azure Cloud Shell](../cloud-shell/quickstart.md) 를 실행 하 고 있는지 확인 합니다. Azure CLI (명령줄 인터페이스)에서 CLI 참조 명령을 실행할 수도 있습니다.

로컬 설치를 사용 하는 경우 [az login](/cli/azure/reference-index#az_login) 명령을 실행 하 여 Azure에 로그인 합니다.

그런 다음 표시 되는 다른 프롬프트에 따라 로그인을 완료 합니다. 추가 로그인 옵션은 [Azure CLI를 사용하여 로그인](/cli/azure/authenticate-azure-cli)을 참조하세요.

Azure CLI를 처음 사용 하는 경우 [Azure CLI에서 확장 사용](/cli/azure/azure-cli-extensions-overview)의 지침에 따라 필요한 확장을 설치 합니다.

마지막으로 [az version](/cli/azure/reference-index?#az_version) 명령을 실행 하 여 클라이언트가 최신 상태 인지 확인 합니다. 최신 버전이 아닌 경우 [az upgrade](/cli/azure/reference-index?#az_upgrade) 명령을 실행 하 여 최신 버전으로 업그레이드 합니다.

### <a name="search-azure-marketplace-for-azure-virtual-desktop-images"></a>Azure 가상 데스크톱 이미지에 대 한 검색 Azure Marketplace

Azure Portal에서 Azure Marketplace **검색** 기능을 사용 하 여 원하는 이미지를 찾을 수 있습니다. Azure 가상 데스크톱에 대해 특별히 이미지를 찾으려면 다음 예제 쿼리 중 하나를 실행 하면 됩니다.

다중 세션 Windows 10 하려는 경우 다음 조건으로 검색을 실행할 수 있습니다.

```azure
az vm image list --all --publisher "microsoftwindowsdesktop" --offer "windows-10" --sku "21h1-evd-g2"
```

이 명령은 다음 URN을 반환 해야 합니다.

```azure
MicrosoftWindowsDesktop:Windows-10:21h1-evd-g2:latest
```

Windows Server 2019 datacenter를 찾고 있는 경우 Azure CLI에서 다음 조건을 실행할 수 있습니다.

```azure
az vm image list --all --publisher "microsoftwindowsserver" --offer "WindowsServer" --sku "2019-Datacenter-gen2"
```

이 명령은 다음 URN을 반환 해야 합니다.

```azure
MicrosoftWindowsServer:windowsserver-gen2preview:2019-datacenter-gen2:latest
```

>[!IMPORTANT]
>2 세대 ("gen2") 이미지만 사용 해야 합니다. Azure Stack HCI 용 Azure 가상 데스크톱은 최초의 생성 ("gen1") 이미지를 사용 하 여 VM을 만드는 것을 지원 하지 않습니다. "-G1" 접미사가 있는 Sku를 사용 하지 마십시오.

### <a name="create-a-new-azure-managed-disk-from-the-image"></a>이미지에서 새 Azure 관리 디스크 만들기

다음으로 Azure Marketplace에서 다운로드 한 이미지를 통해 Azure 관리 디스크를 만들어야 합니다.

Azure 관리 디스크를 만들려면 다음을 수행 합니다.

1. Azure 명령줄 프롬프트에서 다음 명령을 실행 하 여 관리 디스크의 매개 변수를 설정 합니다. 괄호 안의 항목을 시나리오와 관련 된 값으로 바꿔야 합니다.

```azure
$urn = <URN of the Marketplace image> #Example: “MicrosoftWindowsServer:WindowsServer:2019-Datacenter:Latest”
$diskName = <disk name> #Name for new disk to be created
$diskRG = <resource group> #Resource group that contains the new disk
```

2. 이러한 명령을 실행 하 여 디스크를 만들고 SAS (Serial Attached SCSI) 액세스 URL을 생성 합니다.

```azure
az disk create -g $diskRG -n $diskName --image-reference $urn
$sas = az disk grant-access --duration-in-seconds 36000 --access-level Read --name $diskName --resource-group $diskRG
$diskAccessSAS = ($sas | ConvertFrom-Json)[0].accessSas
```

### <a name="export-a-vhd-from-the-managed-disk-to-azure-stack-hci-cluster"></a>관리 디스크에서 Azure Stack HCI 클러스터로 VHD 내보내기

그런 다음 관리 디스크에서 만든 VHD를 Azure Stack HCI 클러스터로 내보내야 합니다. 그러면 새 Vm을 만들 수 있습니다. 일반 웹 브라우저 또는 Storage Explorer에서 다음 메서드를 사용할 수 있습니다.

VHD를 내보내려면:

1. 브라우저를 열고 [이미지에서 새 Azure 관리 디스크 만들기](#create-a-new-azure-managed-disk-from-the-image)에서 생성 한 관리 디스크의 SAS URL로 이동 합니다. 이 URL에서 Azure Marketplace 다운로드 한 이미지에 대 한 VHD 이미지를 다운로드할 수 있습니다.

2. VHD 이미지를 다운로드 합니다. 다운로드 프로세스는 몇 분 정도 걸릴 수 있으므로 잠시 기다려 주십시오. 다음 섹션으로 이동 하기 전에 이미지가 완전히 다운로드 되었는지 확인 합니다.

>[!NOTE]
>Azcopy를 실행 하는 경우 다음 명령을 실행 하 여 md5check를 건너뛰어야 할 수 있습니다.
>
> ```azure
> azcopy copy “$sas" "destination_path_on_cluster" --check-md5 NoCheck
> ```

### <a name="clean-up-the-managed-disk"></a>관리 디스크 정리

VHD를 완료 하 고 나면 관리 디스크를 삭제 하 여 공간을 확보 해야 합니다.

만든 관리 디스크를 삭제 하려면 다음 명령을 실행 합니다.

```azure
az disk revoke-access --name $diskName --resource-group $diskRG 
az disk delete --name $diskName --resource-group $diskRG --yes
```

이 명령을 완료 하는 데 몇 분 정도 걸릴 수 있으므로 잠시 기다려 주십시오.

>[!NOTE]
>필요에 따라 다음 명령을 실행 하 여 다운로드 VHD를 동적 VHDx로 변환할 수도 있습니다.
>
> ```powershell
> Convert-VHD -Path " destination_path_on_cluster\file_name.vhd" -DestinationPath " destination_path_on_cluster\file_name.vhdx" -VHDType Dynamic
> ```

## <a name="next-steps"></a>다음 단계

기본 사항 또는 가격 책정 정보에 대 한 메모리를 새로 고쳐야 하는 경우 [Azure 가상 데스크톱 AZURE STACK HCI](azure-stack-hci-overview.md)로 이동 합니다.

추가 질문이 있는 경우 [FAQ](azure-stack-hci-faq.yml)를 확인 하세요.
