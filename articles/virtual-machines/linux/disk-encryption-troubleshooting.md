---
title: Linux VM용 Azure Disk Encryption 문제 해결
description: 이 문서에서는 Linux VM용 Microsoft Azure Disk Encryption에 대한 문제 해결 팁을 제공합니다.
author: msmbaldwin
ms.service: virtual-machines
ms.subservice: disks
ms.collection: linux
ms.topic: troubleshooting
ms.author: mbaldwin
ms.date: 08/06/2019
ms.custom: seodec18, devx-track-azurepowershell
ms.openlocfilehash: acacae6020debf5084ffb99f89760c938324abac
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132292872"
---
# <a name="azure-disk-encryption-for-linux-vms-troubleshooting-guide"></a>Linux VM용 Azure Disk Encryption 문제 해결 가이드

**적용 대상:** :heavy_check_mark: Linux VM :heavy_check_mark: 유연한 확장 집합 

이 가이드는 조직에서 Azure Disk Encryption을 사용하는 IT 전문가, 정보 보안 분석가 및 클라우드 관리자를 위한 것입니다. 이 문서는 디스크 암호화 관련 문제를 해결하는 데 도움을 드리기 위해 작성되었습니다.

아래 단계를 수행하기 전에 먼저 암호화하려는 VM이 [지원되는 VM 크기 및 운영 체제](disk-encryption-overview.md#supported-vms-and-operating-systems)에 속하는지와 모든 필수 조건을 충족하는지 확인합니다.

- [VM에 대한 추가 요구 사항](disk-encryption-overview.md#supported-vms-and-operating-systems)
- [네트워킹 요구 사항](disk-encryption-overview.md#networking-requirements)
- [암호화 키 스토리지 요구 사항](disk-encryption-overview.md#encryption-key-storage-requirements)

 

## <a name="troubleshooting-linux-os-disk-encryption"></a>Linux OS 디스크 암호화 문제 해결

Linux OS(운영 체제) 디스크 암호화에서는 전체 디스크 암호화 프로세스를 실행하기 전에 OS 드라이브를 분리해야 합니다. 드라이브를 분리할 수 없는 경우 "... 후 분리하지 못했습니다."라는 오류 메시지가 발생할 수 있습니다.

이 오류는 지원되는 재고 갤러리 이미지에서 환경이 변경된 VM에서 OS 디스크 암호화를 시도할 때 발생할 수 있습니다. OS 드라이브를 분리하는 확장의 기능을 방해할 수 있는 지원되는 이미지로부터의 편차. 편차 예는 다음 항목을 포함할 수 있습니다.
- 지원되는 파일 시스템 또는 파티션 구성표와 더 이상 일치하지 않는 사용자 지정 이미지
- SAP, MongoDB, Apache Cassandra 및 Docker와 같은 대규모 애플리케이션은 암호화하기 전에 OS에 설치되고 실행되는 경우 지원되지 않습니다. Azure Disk Encryption은 디스크 암호화에서 OS 드라이브를 준비할 때 필요한 대로 이러한 프로세스를 안전하게 종료할 수 없습니다. OS 드라이브에 대한 오픈 파일 핸들을 보유하는 활성 프로세스가 있는 경우 OS 드라이브는 탑재될 수 없습니다. 따라서 OS 드라이브를 암호화하는 데 실패합니다. 
- 암호화가 활성화된 시간에 근접하여 실행하는 사용자 지정 스크립트 또는 암호화 프로세스 중에 다른 변경 작업이 VM에서 수행되고 있는 경우 이 충돌은 Azure Resource Manager 템플릿에서 동시에 실행하도록 여러 확장을 정의하거나 사용자 지정 스크립트 확장 또는 다른 작업을 디스크 암호화와 동시에 실행할 때 발생할 수 있습니다. 이러한 단계를 직렬화하고 격리하면 문제가 해결될 수 있습니다.
- 암호화를 활성화하기 전에 SELinux(Security Enhanced Linux)가 비활성화되지 않았으므로 분리 단계가 실패합니다. 암호화가 완료되면 SELinux를 다시 활성화할 수 있습니다.
- OS 디스크는 LVM(논리 볼륨 관리자) 구성표를 사용합니다. 제한된 LVM 데이터 디스크 지원은 사용할 수 있지만, LVM OS 디스크는 사용할 수 없습니다.
- 최소 메모리 요구 사항이 충족되지 않았습니다(OS 디스크 암호화에 권장되는 메모리 크기는 7GB임).
- 데이터 드라이브가 /mnt/ 디렉터리 또는 다른 디렉터리(예: /mnt/data1, /mnt/data2, /data3 + /data3/data4) 아래에 재귀적으로 탑재되었습니다.

## <a name="update-the-default-kernel-for-ubuntu-1404-lts"></a> Ubuntu 14.04 LTS의 기본 커널 업데이트

Ubuntu 14.04 LTS 이미지는 기본 커널 버전 4.4와 함께 제공됩니다. 이 커널 버전에는 OS 암호화 프로세스 중에 메모리 부족 중단 프로그램이 dd 명령을 부적절하게 종료하는 알려진 문제가 있습니다. 이 버그는 최근 Azure 튜닝 Linux 커널에서 수정되었습니다. 이 오류를 방지하려면 이미지에서 암호화를 사용하도록 설정하기 전에 다음 명령을 사용하여 [Azure 튜닝 커널 4.15](https://packages.ubuntu.com/trusty/linux-azure) 이상으로 업데이트합니다.

```
sudo apt-get update
sudo apt-get install linux-azure
sudo reboot
```

VM이 새 커널로 다시 시작된 후 다음을 사용하여 새 커널 버전을 확인할 수 있습니다.

```
uname -a
```

## <a name="update-the-azure-virtual-machine-agent-and-extension-versions"></a>Azure 가상 머신 에이전트 및 확장 버전 업데이트

지원되지 않는 버전의 Azure 가상 머신 에이전트를 사용하면 가상 머신 이미지에서 Azure Disk Encryption 작업이 실패할 수 있습니다. 2\.2.38 이전 버전의 에이전트를 사용하는 Linux 이미지의 경우 암호화를 사용하도록 설정하기 전에 먼저 업데이트해야 합니다. 자세한 내용은 [VM에서 Azure Linux 에이전트를 업데이트하는 방법](../extensions/update-linux-agent.md) 및 [Azure의 가상 머신 에이전트에 대한 최소 버전 지원](https://support.microsoft.com/en-us/help/4049215/extensions-and-virtual-machine-agent-minimum-version-support)을 참조하세요.

올바른 버전의 Microsoft.Azure.Security.AzureDiskEncryption 또는 Microsoft.Azure.Security.AzureDiskEncryptionForLinux 게스트 에이전트 확장도 필요합니다. Azure 가상 머신 에이전트 필수 구성 요소가 충족되고 지원되는 버전의 가상 머신 에이전트가 사용되는 경우, 확장 버전은 플랫폼에 의해 자동으로 유지 관리 및 업데이트됩니다.

Microsoft.OSTCExtensions.AzureDiskEncryptionForLinux 확장은 이제 사용되지 않으며 더 이상 지원되지 않습니다.  

## <a name="unable-to-encrypt-linux-disks"></a>Linux 디스크를 암호화할 수 없음

경우에 따라 Linux 디스크 암호화가 "OS 디스크 암호화 시작됨" 상태에서 중단된 것으로 나타나고, SSH가 비활성화됩니다. 암호화 프로세스를 재고 갤러리 이미지에 대해 완료하는 데 3-16시간이 걸릴 수 있습니다. 다중 테라바이트 크기 데이터 디스크가 추가될 경우 이 프로세스는 며칠이 걸릴 수도 있습니다.

Linux OS 디스크 암호화 시퀀스는 OS 드라이브를 일시적으로 탑재 해제합니다. 그런 다음 암호화된 상태로 다시 탑재하기 전에 전체 OS 디스크의 블록 단위로 암호화를 수행합니다. Linux 디스크 암호화는 암호화를 진행하는 동안 VM을 동시에 사용할 수 없습니다. VM의 성능 특성은 암호화를 완료하는 데 필요한 시간에 큰 차이를 만들 수 있습니다. 이러한 특성은 디스크 크기 및 스토리지 계정이 표준 또는 프리미엄(SSD) 스토리지인지 여부를 포함합니다.

OS 드라이브가 암호화되는 동안 VM은 서비스 상태가 되고 진행 중인 프로세스의 중단을 방지하기 위해 SSH를 비활성화합니다.  암호화 상태를 확인하려면 Azure PowerShell [Get-AzVmDiskEncryptionStatus](/powershell/module/az.compute/get-azvmdiskencryptionstatus) 명령을 사용하고 **ProgressMessage** 필드를 확인합니다. **ProgressMessage** 는 데이터 및 OS 디스크가 암호화될 때 일련의 상태를 보고합니다.

```azurepowershell
PS > Get-AzVMDiskEncryptionStatus -ResourceGroupName "MyResourceGroup" -VMName "myVM"

OsVolumeEncrypted          : EncryptionInProgress
DataVolumesEncrypted       : EncryptionInProgress
OsVolumeEncryptionSettings :
ProgressMessage            : Transitioning

PS > Get-AzVMDiskEncryptionStatus -ResourceGroupName "MyResourceGroup" -VMName "myVM"

OsVolumeEncrypted          : EncryptionInProgress
DataVolumesEncrypted       : EncryptionInProgress
OsVolumeEncryptionSettings : Microsoft.Azure.Management.Compute.Models.DiskEncryptionSettings
ProgressMessage            : Encryption succeeded for data volumes

PS > Get-AzVMDiskEncryptionStatus -ResourceGroupName "MyResourceGroup" -VMName "myVM"

OsVolumeEncrypted          : EncryptionInProgress
DataVolumesEncrypted       : EncryptionInProgress
OsVolumeEncryptionSettings : Microsoft.Azure.Management.Compute.Models.DiskEncryptionSettings
ProgressMessage            : Provisioning succeeded

PS > Get-AzVMDiskEncryptionStatus -ResourceGroupName "MyResourceGroup" -VMName "myVM"

OsVolumeEncrypted          : EncryptionInProgress
DataVolumesEncrypted       : EncryptionInProgress
OsVolumeEncryptionSettings : Microsoft.Azure.Management.Compute.Models.DiskEncryptionSettings
ProgressMessage            : OS disk encryption started
```

**ProgressMessage** 는 대부분의 암호화 프로세스에 대해 **시작되는 OS 디스크 암호화** 에 남아 있습니다.  암호화가 성공적으로 완료되면 **ProgressMessage** 에서 다음을 반환합니다.

```azurepowershell
PS > Get-AzVMDiskEncryptionStatus -ResourceGroupName "MyResourceGroup" -VMName "myVM"

OsVolumeEncrypted          : Encrypted
DataVolumesEncrypted       : NotMounted
OsVolumeEncryptionSettings : Microsoft.Azure.Management.Compute.Models.DiskEncryptionSettings
ProgressMessage            : Encryption succeeded for all volumes
```

이 메시지가 제공되면 암호화된 OS 드라이브가 사용할 준비가 되었으며 VM을 다시 사용할 수 있게 됩니다.

이 프로세스가 실행되는 동안 부팅 정보, 진행 메시지 또는 오류가 프로세스 도중 OS 암호화가 실패했음을 보고하는 경우, 암호화되기 직전에 수행된 스냅샷 또는 백업으로 VM을 복원합니다. 메시지의 예는 이 가이드에 설명되어 있는 "분리하지 못했습니다." 오류입니다.

암호화를 다시 시도하기 전에 VM의 특성을 다시 평가하고 모든 필수 조건이 충족되는지 확인합니다.

## <a name="troubleshooting-azure-disk-encryption-behind-a-firewall"></a>방화벽 뒤에 있는 Azure Disk Encryption 문제 해결

[격리된 네트워크에서 디스크 암호화](disk-encryption-isolated-network.md)를 참조하세요.

## <a name="troubleshooting-encryption-status"></a>암호화 상태 문제 해결 

포털은 VM 내에서 암호화되지 않은 경우에도 디스크를 암호화된 상태로 표시할 수 있습니다.  상위 수준의 Azure Disk Encryption 관리 명령을 사용하는 대신 낮은 수준의 명령을 사용하여 VM 내에서 디스크의 암호를 직접 해독하면 이 오류가 발생할 수 있습니다.  상위 수준의 명령은 VM 내에서 디스크의 암호를 해독할 뿐만 아니라 VM 외부에서 중요한 플랫폼 수준 암호화 설정 및 VM에 연결된 확장 설정도 업데이트합니다.  이러한 설정이 그대로 유지되지 않으면 플랫폼이 암호화 상태를 보고하거나 VM을 올바르게 프로비저닝할 수 없습니다.

PowerShell을 사용하여 Azure Disk Encryption을 사용하지 않도록 설정하려면 [Disable-AzVMDiskEncryption](/powershell/module/az.compute/disable-azvmdiskencryption) 및 [Remove-AzVMDiskEncryptionExtension](/powershell/module/az.compute/remove-azvmdiskencryptionextension)을 차례로 사용합니다. 암호화를 사용하지 않도록 설정하기 전에 Remove-AzVMDiskEncryptionExtension을 실행하면 오류가 발생합니다.

CLI를 사용하여 Azure Disk Encryption을 사용하지 않도록 설정하려면 [az vm encryption disable](/cli/azure/vm/encryption)을 사용합니다.

## <a name="next-steps"></a>다음 단계

이 문서에서는 일반적인 Azure Disk Encryption 문제와 해당 문제 해결 방법에 대해 자세히 알아보았습니다. 이 서비스 및 기능에 대한 자세한 내용은 다음 문서를 참조하세요.

- [Microsoft Defender for Cloud에서 디스크 암호화 적용](../../security-center/asset-inventory.md)
- [휴지 상태의 Azure 데이터 암호화](../../security/fundamentals/encryption-atrest.md)
