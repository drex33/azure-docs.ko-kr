---
title: Linux VM용 Azure Disk Encryption 사용
description: 이 문서에서는 Linux VM용 Microsoft Azure Disk Encryption을 사용하도록 설정하는 방법에 대한 지침을 제공합니다.
author: msmbaldwin
ms.service: virtual-machines
ms.subservice: disks
ms.collection: linux
ms.topic: conceptual
ms.author: mbaldwin
ms.date: 08/06/2019
ms.custom: seodec18
ms.openlocfilehash: bf30a6d35249abf46d115f6bb6864a4363898b5b
ms.sourcegitcommit: 1aeff9f012cfd868104ef0159c5204e402d75696
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/24/2021
ms.locfileid: "133031942"
---
# <a name="azure-disk-encryption-for-linux-vms"></a>Linux VM용 Azure Disk Encryption

**적용 대상:** :heavy_check_mark: Linux VM :heavy_check_mark: 유연한 확장 집합 

Azure Disk Encryption을 사용하여 고객 조직의 보안 및 규정 준수 약정에 맞게 데이터를 안전하게 보호할 수 있습니다. 이 기능은 Linux의 [DM-Crypt](https://en.wikipedia.org/wiki/Dm-crypt) 기능을 사용하여 Azure VM(Virtual Machines)의 OS 및 데이터 디스크에 볼륨 암호화를 제공하며, 디스크 암호화 키 및 비밀을 제어하고 관리하는 데 유용한 [Azure Key Vault](../../key-vault/index.yml)와 통합됩니다.

Azure Disk Encryption은 Virtual Machines와 동일한 방식으로 영역 복원력이 있습니다. 자세한 내용은 [가용성 영역을 지원하는 Azure Services](../../availability-zones/az-region.md)를 참조하세요.

[클라우드용 Microsoft Defender를](../../security-center/index.yml)사용하는 경우 암호화되지 않은 VM이 있는 경우 경고가 생성됩니다. 이 경고는 심각도가 높다고 표시되며 이러한 VM을 암호화하도록 권장합니다.

![클라우드용 Microsoft Defender 디스크 암호화 경고](media/disk-encryption/security-center-disk-encryption-fig1.png)

> [!WARNING]
> - 이전에 VM을 암호화하기 위해 Azure AD에서 Azure Disk Encryption을 사용한 적이 있다면 VM을 암호화하는 데 이 옵션을 계속 사용해야 합니다. 자세한 내용은 [Azure AD(이전 릴리스)를 포함한 Azure Disk Encryption](disk-encryption-overview-aad.md)을 참조하세요. 
> - 특정 권장 사항으로 인해 데이터, 네트워크 또는 컴퓨팅 리소스 사용량이 증가할 수 있으며 이로 인해 라이선스 또는 구독 비용이 발생합니다. 사용자는 유효한 활성 Azure 구독을 포함하여 지원되는 지역에서 Azure에 리소스를 만들어야 합니다.

[Azure CLI를 사용하여 Linux VM 만들기 및 암호화 빠른 시작](disk-encryption-cli-quickstart.md) 또는 [Azure Powershell을 사용하여 Linux VM 만들기 및 암호화 빠른 시작](disk-encryption-powershell-quickstart.md)을 사용하여 몇 분 만에 Linux용 Azure Disk Encryption의 기본 사항을 배울 수 있습니다.

## <a name="supported-vms-and-operating-systems"></a>지원되는 VM 및 운영 체제

### <a name="supported-vms"></a>지원되는 VM

Linux VM은 [다양한 크기](../sizes.md)로 사용할 수 있습니다. Azure Disk Encryption은 1세대 및 2세대 VM에서 지원됩니다. Azure Disk Encryption은 Premium Storage가 있는 VM에 사용할 수도 있습니다.

[로컬 임시 디스크가 없는 Azure VM 크기](../azure-vms-no-temp-disk.yml)를 참조하세요.

Azure Disk Encryption은 [Basic, A 시리즈 VM](https://azure.microsoft.com/pricing/details/virtual-machines/series/) 또는 이러한 최소 메모리 요구 사항을 충족하지 않는 가상 머신에서도 사용할 수 없습니다.

| 가상 머신 | 최소 메모리 요구 사항 |
|--|--|
| 데이터 볼륨만 암호화하는 경우 Linux VM| 2GB |
| 데이터 및 OS 볼륨을 모두 암호화하는 경우와 루트(/) 파일 시스템 사용량이 4GB 이하인 경우의 Linux VM | 8GB |
| 데이터 및 OS 볼륨을 모두 암호화하는 경우와 루트(/) 파일 시스템 사용량이 4GB를 초과하는 경우의 Linux VM | 루트 파일 시스템 사용량 * 2입니다. 예를 들어 16GB의 루트 파일 시스템 사용량에는 32GB 이상의 RAM이 필요합니다. |

Linux 가상 머신에서 OS 디스크 암호화 프로세스가 완료되면 VM을 적은 메모리로 실행하도록 구성할 수 있습니다.

추가 예외는 [Azure Disk Encryption: 지원되지 않는 시나리오](disk-encryption-linux.md#unsupported-scenarios)를 참조하세요.

### <a name="supported-operating-systems"></a>지원되는 운영 체제

Azure Disk Encryption은 자체적으로 가능한 모든 Linux 서버 배포판에 속하는 [Azure 보증 Linux 배포판](endorsed-distros.md)의 일부에서 지원됩니다.

![Azure Disk Encryption을 지원하는 Linux 서버 배포판의 벤 다이어그램](./media/disk-encryption/ade-supported-distros.png)

Azure에서 보증되지 않는 Linux 서버 배포판은 Azure Disk Encryption을 지원하지 않습니다. 보증된 배포판의 경우 다음 배포판 및 버전에서만 Azure Disk Encryption을 지원합니다.


| 게시자 | 제안 | SKU | URN | 암호화에 지원되는 볼륨 유형 |
| --- | --- |--- | --- |
| Canonical | Ubuntu | 20.04-LTS | Canonical:0001-com-ubuntu-server-focal:20_04-lts:latest | OS 및 데이터 디스크 |
| Canonical | Ubuntu | 20.04-DAILY-LTS | Canonical:0001-com-ubuntu-server-focal-daily:20_04-daily-lts:latest | OS 및 데이터 디스크 |
| Canonical | Ubuntu | 20.04-LTS Gen2 | Canonical:0001-com-ubuntu-server-focal:20_04-lts-gen2:latest | OS 및 데이터 디스크 |
| Canonical | Ubuntu | 20.04-DAILY-LTS Gen2 |Canonical:0001-com-ubuntu-server-focal-daily:20_04-daily-lts-gen2:latest | OS 및 데이터 디스크 |
| Canonical | Ubuntu | 18.04-LTS | Canonical:UbuntuServer:18.04-LTS:latest | OS 및 데이터 디스크 |
| Canonical | Ubuntu 18.04 | 18.04-DAILY-LTS | Canonical:UbuntuServer:18.04-DAILY-LTS:latest | OS 및 데이터 디스크 |
| Canonical | Ubuntu 16.04 | 16.04-DAILY-LTS | Canonical:UbuntuServer:16.04-DAILY-LTS:latest | OS 및 데이터 디스크 |
| Canonical | Ubuntu 14.04.5</br>[4.15 이상으로 업데이트된 Azure 튜닝 커널 포함](disk-encryption-troubleshooting.md) | 14.04.5-LTS | Canonical:UbuntuServer:14.04.5-LTS:latest | OS 및 데이터 디스크 |
| Canonical | Ubuntu 14.04.5</br>[4.15 이상으로 업데이트된 Azure 튜닝 커널 포함](disk-encryption-troubleshooting.md) | 14.04.5-DAILY-LTS | Canonical:UbuntuServer:14.04.5-DAILY-LTS:latest | OS 및 데이터 디스크 |
| RedHat | RHEL 8.4 | 8.4 | RedHat:RHEL:8.4:latest | OS 및 데이터 디스크(아래 참고 사항 참조) |
| RedHat | RHEL 8.3 | 8.3 | RedHat:RHEL:8.3:latest | OS 및 데이터 디스크(아래 참고 사항 참조) |
| RedHat | RHEL 8-LVM | 8-LVM | RedHat:RHEL:8-LVM:8.2.20200905 | OS 및 데이터 디스크(아래 참고 사항 참조) |
| RedHat | RHEL 8.2 | 8.2 | RedHat:RHEL:8.2:latest | OS 및 데이터 디스크(아래 참고 사항 참조) |
| RedHat | RHEL 8.1 | 8.1 | RedHat:RHEL:8.1:latest | OS 및 데이터 디스크(아래 참고 사항 참조) |
| RedHat | RHEL 7-LVM | 7-LVM | RedHat:RHEL:7-LVM:7.9.2020111202 | OS 및 데이터 디스크(아래 참고 사항 참조) |
| RedHat | RHEL 7.9 | 7_9 | RedHat:RHEL:7_9:latest | OS 및 데이터 디스크(아래 참고 사항 참조) |
| RedHat | RHEL 7.8 | 7.8 | RedHat:RHEL:7.8:latest | OS 및 데이터 디스크(아래 참고 사항 참조) |
| RedHat | RHEL 7.7 | 7.7 | RedHat:RHEL:7.7:latest | OS 및 데이터 디스크(아래 참고 사항 참조) |
| RedHat | RHEL 7.6 | 7.6 | RedHat:RHEL:7.6:latest | OS 및 데이터 디스크(아래 참고 사항 참조) |
| RedHat | RHEL 7.5 | 7.5 | RedHat:RHEL:7.5:latest | OS 및 데이터 디스크(아래 참고 사항 참조) |
| RedHat | RHEL 7.4 | 7.4 | RedHat:RHEL:7.4:latest | OS 및 데이터 디스크(아래 참고 사항 참조) |
| RedHat | RHEL 7.3 | 7.3 | RedHat:RHEL:7.3:latest | OS 및 데이터 디스크(아래 참고 사항 참조) |
| RedHat | RHEL 7.2 | 7.2 | RedHat:RHEL:7.2:latest | OS 및 데이터 디스크(아래 참고 사항 참조) |
| RedHat | RHEL 6.8 | 6.8 | RedHat:RHEL:6.8:latest | 데이터 디스크(아래 참고 사항 참조) |
| RedHat | RHEL 6.7 | 6.7 | RedHat:RHEL:6.7:latest | 데이터 디스크(아래 참고 사항 참조) |
| OpenLogic | CentOS 8-LVM | 8-LVM | OpenLogic:CentOS-LVM:8-LVM:latest | OS 및 데이터 디스크 |
| OpenLogic | CentOS 8.4 | 8_4 | OpenLogic: CentOS: 8_4: 최신 | OS 및 데이터 디스크 |
| OpenLogic | CentOS 8.3 | 8_3 | OpenLogic: CentOS: 8_3: 최신 | OS 및 데이터 디스크 |
| OpenLogic | CentOS 8.2 | 8_2 | OpenLogic:CentOS:8_2:latest | OS 및 데이터 디스크 |
| OpenLogic | CentOS 8.1 | 8_1 | OpenLogic:CentOS:8_1:latest | OS 및 데이터 디스크 |
| OpenLogic | CentOS 7-LVM | 7-LVM | OpenLogic:CentOS-LVM:7-LVM:7.9.2021020400 | OS 및 데이터 디스크 |
| OpenLogic | CentOS 7.9 | 7_9 | OpenLogic:CentOS:7_9:latest | OS 및 데이터 디스크 |
| OpenLogic | CentOS 7.8 | 7_8 | OpenLogic:CentOS:7_8:latest | OS 및 데이터 디스크 |
| OpenLogic | CentOS 7.7 | 7.7 | OpenLogic:CentOS:7.7:latest | OS 및 데이터 디스크 |
| OpenLogic | CentOS 7.6 | 7.6 | OpenLogic:CentOS:7.6:latest | OS 및 데이터 디스크 |
| OpenLogic | CentOS 7.5 | 7.5 | OpenLogic:CentOS:7.5:latest | OS 및 데이터 디스크 |
| OpenLogic | CentOS 7.4 | 7.4 | OpenLogic:CentOS:7.4:latest | OS 및 데이터 디스크 |
| OpenLogic | CentOS 7.3 | 7.3 | OpenLogic:CentOS:7.3:latest | OS 및 데이터 디스크 |
| OpenLogic | CentOS 7.2n | 7.2n | OpenLogic:CentOS:7.2n:latest | OS 및 데이터 디스크 |
| OpenLogic | CentOS 7.1 | 7.1 | OpenLogic:CentOS:7.1:latest | 데이터 디스크만 |
| OpenLogic | CentOS 7.0 | 7.0 | OpenLogic:CentOS:7.0:latest | 데이터 디스크만 |
| OpenLogic | CentOS 6.8 | 6.8 | OpenLogic:CentOS:6.8:latest | 데이터 디스크만 |
| SUSE | openSUSE 42.3 | 42.3 | SUSE:openSUSE-Leap:42.3:latest | 데이터 디스크만 |
| SUSE | SLES 12-SP4 | 12-SP4 | SUSE:SLES:12-SP4:latest | 데이터 디스크만 |
| SUSE | SLES HPC 12-SP3 | 12-SP3 | SUSE:SLES-HPC:12-SP3:latest | 데이터 디스크만 |

> [!NOTE]
> 새 Azure Disk Encryption 구현은 RHEL OS 및 RHEL7 종량제 이미지용 데이터 디스크에서 지원됩니다.
>
> 또한 ADE는 RHEL Bring-Your-Own-Subscription 골드 이미지에서도 지원되지만 반드시 **먼저** 구독에 등록해야 합니다. 자세한 내용은 [Azure의 Red Hat Enterprise Linux Bring-Your-Own-Subscription 골드 이미지](../workloads/redhat/byos.md#encrypt-red-hat-enterprise-linux-bring-your-own-subscription-gold-images)를 참조하세요.
> 
> 특정 제품 유형에 대한 ADE 지원은 게시자가 제공한 수명 종료 날짜를 초과하지 않습니다. 
> 
> 레거시 ADE 솔루션(AAD 자격 증명 사용)은 새 VM에 권장되지 않으며 RHEL 7.8 이후의 RHEL 버전과 호환되지 않습니다.

## <a name="additional-vm-requirements"></a>추가 VM 요구 사항

Azure Disk Encryption을 사용하려면 시스템에 dm-crypt 및 vfat 모듈이 있어야 합니다. 기본 이미지에서 vfat을 제거하거나 사용하지 않도록 설정하면 시스템에서 키 볼륨을 읽거나 후속 재부팅 시 디스크 잠금을 해제하는 데 필요한 키를 가져올 수 없습니다. 시스템에서 vfat 모듈을 제거하거나 데이터 드라이브에서 OS 탑재 지점/폴더 확장을 적용하는 시스템 강화 단계는 Azure Disk Encryption과 호환되지 않습니다. 

암호화를 사용하도록 설정하기 전에 암호화할 데이터 디스크를 /etc/fstab에 올바르게 나열해야 합니다. 항목을 만들 때 "nofail" 옵션을 사용하고, 영구 블록 디바이스 이름을 선택합니다. "/dev/sdX" 형식의 디바이스 이름은 재부팅하는 동안, 특히 암호화 후에 동일한 디스크에 연결되지 않을 수 있습니다. 이 동작에 대한 자세한 내용은 [Linux VM 디바이스 이름 변경 문제 해결](/troubleshoot/azure/virtual-machines/troubleshoot-device-names-problems)을 참조하세요.

/etc/fstab 설정이 탑재에 대해 올바르게 구성되었는지 확인합니다. 이러한 설정을 구성하려면 mount -a 명령을 실행하거나 VM을 다시 부팅하고 다시 탑재를 트리거합니다. 이러한 작업이 완료되면 lsblk 명령의 출력을 확인하여 드라이브가 여전히 탑재되어 있는지 확인합니다. 

- 암호화를 사용하도록 설정하기 전에 /etc/fstab 파일에서 드라이브를 올바르게 탑재하지 않으면 Azure Disk Encryption에서 해당 드라이브를 올바르게 탑재할 수 없습니다.
- Azure Disk Encryption 프로세스는 암호화 프로세스의 일환으로 탑재 정보를 /etc/fstab에서 자체의 구성 파일로 이동합니다. 데이터 드라이브 암호화가 완료된 후 /etc/fstab에서 누락된 항목을 보고 놀라지 마세요.
- 암호화를 시작하기 전에 탑재된 데이터 디스크에 쓸 수 있는 모든 서비스 및 프로세스를 중지하고 사용하지 않도록 설정하여 재부팅한 후 자동으로 다시 시작되지 않도록 합니다. 이렇게 하면 이러한 파티션에서 파일을 열린 상태로 유지되므로 암호화 절차에서 파일을 다시 탑재할 수 없게 되어 암호화가 실패합니다. 
- 다시 부팅한 후 Azure Disk Encryption 프로세스에서 새로 암호화된 디스크를 탑재하는 데 시간이 걸립니다. 이러한 디스크는 다시 부팅한 후에 즉시 사용할 수 없습니다. 다른 프로세스가 액세스할 수 있게 되기 전에 이 프로세스에서 암호화된 드라이브를 시작, 잠금 해제 및 탑재하는 데 시간이 필요합니다. 이 프로세스는 시스템 특성에 따라 다시 부팅 후 2분 이상 걸릴 수 있습니다.

데이터 디스크를 탑재하고 필요한 /etc/fstab 항목을 만드는 데 사용되는 명령의 예는 다음과 같습니다.

```bash
UUID0="$(blkid -s UUID -o value /dev/sda1)"
UUID1="$(blkid -s UUID -o value /dev/sda2)"
mkdir /data0
mkdir /data1
echo "UUID=$UUID0 /data0 ext4 defaults,nofail 0 0" >>/etc/fstab
echo "UUID=$UUID1 /data1 ext4 defaults,nofail 0 0" >>/etc/fstab
mount -a
```
## <a name="networking-requirements"></a>네트워킹 요구 사항

Azure Disk Encryption 기능을 사용하도록 설정하려면 Linux VM이 다음 네트워크 엔드포인트 구성 요구 사항을 충족해야 합니다.
  - Key Vault에 연결할 토큰을 얻으려면 Linux VM에서 Azure Active Directory 엔드포인트인 \[login.microsoftonline.com\]에 연결할 수 있어야 합니다.
  - 암호화 키를 고객 Key Vault에 쓰려면 Linux VM에서 Key Vault 엔드포인트에 연결할 수 있어야 합니다.
  - Linux VM은 Azure 확장 리포지토리를 호스팅하는 Azure Storage 엔드포인트 및 VHD 파일을 호스팅하는 Azure Storage 계정에 연결할 수 있어야 합니다.
  -  보안 정책이 Azure VM에서 인터넷으로 액세스를 제한하는 경우 이전 URI를 확인하고 IP에 대한 아웃바운드 연결을 허용하도록 특정 규칙을 구성할 수 있습니다. 자세한 내용은 [방화벽 뒤에 있는 Azure Key Vault](../../key-vault/general/access-behind-firewall.md)를 참조하세요.  

## <a name="encryption-key-storage-requirements"></a>암호화 키 스토리지 요구 사항  

Azure Disk Encryption은 Azure Key Vault를 사용하여 키 디스크 암호화 키와 비밀을 제어하고 관리합니다. Key Vault 및 VM은 동일한 Azure 지역 및 구독에 있어야 합니다.

자세한 내용은 [Azure Disk Encryption을 위한 Key Vault 만들기 및 구성](disk-encryption-key-vault.md)을 참조하세요.

## <a name="terminology"></a>용어

다음 표에서는 Azure Disk Encryption 설명서에서 사용되는 일반적인 용어 중 일부를 정의합니다.

| 용어 | 정의 |
| --- | --- |
| Azure Key Vault | Key Vault는 FIPS(Federal Information Processing Standard) 검증 하드웨어 보안 모듈을 기반으로 하는 암호화 키 관리 서비스입니다. 이러한 표준은 암호화 키 및 중요한 비밀을 보호하는 데 도움이 됩니다. 자세한 내용은 [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) 설명서 및 [Azure Disk Encryption을 위한 Key Vault 만들기 및 구성](disk-encryption-key-vault.md)을 참조하세요. |
| Azure CLI | [Azure CLI](/cli/azure/install-azure-cli)는 명령줄에서 Azure 리소스를 관리하고 관리하는 데 최적화되어 있습니다.|
| DM-Crypt |[DM-Crypt](https://gitlab.com/cryptsetup/cryptsetup/wikis/DMCrypt)는 Linux VM에서 디스크 암호화를 설정하는 데 사용되는 Linux 기반의 투명한 디스크 암호화 하위 시스템입니다. |
| KEK(키 암호화 키) | 비밀을 보호하거나 래핑하는 데 사용할 수 있는 비대칭 키(RSA 2048)입니다. HSM(하드웨어 보안 모듈) 보호 키 또는 소프트웨어 보호 키를 제공할 수 있습니다. 자세한 내용은 [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) 설명서 및 [Azure Disk Encryption을 위한 Key Vault 만들기 및 구성](disk-encryption-key-vault.md)을 참조하세요. |
| PowerShell cmdlet | 자세한 내용은 [Azure PowerShell cmdlet](/powershell/azure/)을 참조하세요. |


## <a name="next-steps"></a>다음 단계

- [빠른 시작 - Azure CLI를 사용하여 Linux VM 만들기 및 암호화](disk-encryption-cli-quickstart.md)
- [빠른 시작 - Azure PowerShell을 사용하여 Linux VM 만들기 및 암호화](disk-encryption-powershell-quickstart.md) 
- [Linux VM에 대한 Azure Disk Encryption 시나리오](disk-encryption-linux.md)
- [Azure Disk Encryption 필수 구성 요소 CLI 스크립트](https://github.com/ejarvi/ade-cli-getting-started)
- [Azure Disk Encryption 필수 조건 PowerShell 스크립트](https://github.com/Azure/azure-powershell/tree/master/src/Compute/Compute/Extension/AzureDiskEncryption/Scripts)
- [Azure Disk Encryption을 위한 키 자격 증명 모음 만들기 및 구성](disk-encryption-key-vault.md)
