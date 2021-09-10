---
title: cloud-init를 사용하여 Linux VM에서 스왑 파티션 구성
description: Azure CLI를 사용하여 만드는 동안 cloud-init를 사용하여 Linux VM에 스왑 파일을 구성하는 방법
author: mimckitt
ms.service: virtual-machines
ms.collection: linux
ms.topic: how-to
ms.date: 11/29/2017
ms.author: mimckitt
ms.subservice: cloud-init
ms.openlocfilehash: 6ac16d84e71bbb59ee52b84f4a3e1e9fbcd80b32
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/23/2021
ms.locfileid: "122687921"
---
# <a name="use-cloud-init-to-configure-a-swap-partition-on-a-linux-vm"></a>cloud-init를 사용하여 Linux VM에서 스왑 파티션 구성

**적용 대상:** :heavy_check_mark: Linux VM :heavy_check_mark: 유연한 확장 집합 

이 문서에서는 [cloud-init](https://cloudinit.readthedocs.io)를 사용하여 다양한 Linux 배포판에서 스왑 파티션을 구성하는 방법을 보여 줍니다. 스왑 파티션은 일반적으로 해당 파티션이 필요한 배포에 따라 Linux 에이전트(WALA)에 의해 구성되었습니다.  이 문서에서는 cloud-init를 사용하여 프로비저닝 시간 동안 요청 시 스왑 파티션을 빌드하는 프로세스에 대해 간략하게 설명합니다.  기본적으로 cloud-init가 Azure에서 작동되는 방식과 지원되는 Linux 배포판에 대한 자세한 내용은 [cloud-init 개요](using-cloud-init.md)를 참조하세요.

## <a name="create-swap-partition-for-ubuntu-based-images"></a>Ubuntu 기반 이미지에 대한 스왑 파티션 만들기
기본적으로 Azure에서 Ubuntu 갤러리 이미지는 스왑 파티션을 만들지 않습니다. cloud-init를 사용하여 VM 프로비저닝 시간 동안 스왑 파티션 구성을 사용하도록 설정하려면 Ubuntu wiki의 [AzureSwapPartitions 문서](https://wiki.ubuntu.com/AzureSwapPartitions)를 참조하세요.

## <a name="create-swap-partition-for-red-hat-and-centos-based-images"></a>Red Hat 및 CentOS 기반 이미지에 대한 스왑 파티션 만들기

현재 셸에 *cloud_init_swappart.txtt* 파일을 만들고 다음 구성을 붙여넣습니다. 이 예제에서는 로컬 컴퓨터에 없는 Cloud Shell에서 파일을 만듭니다. 원하는 모든 편집기를 사용할 수 있습니다. `sensible-editor cloud_init_swappart.txt`를 입력하여 파일을 만들고 사용할 수 있는 편집기의 목록을 봅니다. #1을 선택하여 **nano** 편집기를 사용합니다. 전체 cloud-init 파일, 특히 첫 줄이 올바르게 복사되었는지 확인합니다.  

```yaml
#cloud-config
disk_setup:
  ephemeral0:
    table_type: gpt
    layout: [66, [33,82]]
    overwrite: true
fs_setup:
  - device: ephemeral0.1
    filesystem: ext4
  - device: ephemeral0.2
    filesystem: swap
mounts:
  - ["ephemeral0.1", "/mnt"]
  - ["ephemeral0.2", "none", "swap", "sw,nofail,x-systemd.requires=cloud-init.service", "0", "0"]
```

탑재가 성공적으로 완료되지 않은 경우에도 부팅이 계속되도록 하는 `nofail` 옵션을 사용하여 탑재가 만들어집니다.

이 이미지를 배포하기 전에 [az group create](/cli/azure/group) 명령을 사용하여 리소스 그룹을 만들어야 합니다. Azure 리소스 그룹은 Azure 리소스가 배포 및 관리되는 논리적 컨테이너입니다. 다음 예제에서는 *eastus* 위치에 *myResourceGroup* 이라는 리소스 그룹을 만듭니다.

```azurecli-interactive 
az group create --name myResourceGroup --location eastus
```

이제 [az vm create](/cli/azure/vm)로 VM을 만들고 다음과 같이 `--custom-data cloud_init_swappart.txt`로 cloud-init 파일을 지정합니다.

```azurecli-interactive 
az vm create \
  --resource-group myResourceGroup \
  --name centos74 \
  --image OpenLogic:CentOS:7-CI:latest \
  --custom-data cloud_init_swappart.txt \
  --generate-ssh-keys 
```

## <a name="verify-swap-partition-was-created"></a>스왑 파티션이 만들어졌는지 확인
이전 명령에서 출력에 표시된 VM의 공용 IP 주소에 대한 SSH. 사용자 고유의 **publicIpAddress** 를 다음과 같이 입력합니다.

```bash
ssh <publicIpAddress>
```

VM으로 SSH를 수행한 경우 스왑 파티션이 만들어졌는지 확인합니다.

```bash
swapon -s
```

이 명령의 출력은 다음과 같아야 합니다.

```text
Filename                Type        Size    Used    Priority
/dev/sdb2  partition   2494440 0   -1
```

> [!NOTE] 
> 스왑 파티션이 구성된 기존 Azure 이미지가 있고 새 이미지의 스왑 파티션 구성을 변경하려는 경우 기존 스왑 파티션을 제거해야 합니다. 자세한 내용은 'cloud-init를 사용하여 프로비전할 이미지 사용자 지정' 문서를 참조하세요.

## <a name="next-steps"></a>다음 단계
구성 변경에 대한 추가 cloud-init 예제를 보려면 다음을 참조하세요.
 
- [VM에 추가 Linux 사용자 추가](cloudinit-add-user.md)
- [패키지 관리자를 실행하여 첫 번째 부팅 시 기존 패키지 업데이트](cloudinit-update-vm.md)
- [VM 로컬 호스트 이름 변경](cloudinit-update-vm-hostname.md) 
- [애플리케이션 패키지 설치, 구성 파일 업데이트 및 키 삽입](tutorial-automate-vm-deployment.md)
