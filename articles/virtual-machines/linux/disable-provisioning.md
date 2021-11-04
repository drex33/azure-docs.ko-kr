---
title: 프로비전 에이전트를 사용하지 않도록 설정하거나 제거
description: Linux VM 및 이미지에서 프로비전 에이전트를 사용하지 않도록 설정하거나 제거하는 방법에 대해 알아봅니다.
author: danielsollondon
ms.service: virtual-machines
ms.collection: linux
ms.subservice: imaging
ms.topic: how-to
ms.workload: infrastructure
ms.date: 07/06/2020
ms.author: danis
ms.reviewer: cynthn
ms.openlocfilehash: 938e5b88c635213b5614efe4c9dc646a25ee8d06
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131471500"
---
# <a name="disable-or-remove-the-linux-agent-from-vms-and-images"></a>VM 및 이미지에서 Linux 에이전트를 사용하지 않도록 설정하거나 제거

**적용 대상:** :heavy_check_mark: Linux VM :heavy_check_mark: 유연한 확장 집합 

Linux 에이전트를 제거하기 전에 Linux 에이전트가 제거된 후 VM에서 수행할 수 없는 작업이 무엇인지 이해해야 합니다.

Azure VM(가상 머신) [확장](../extensions/overview.md)은 Azure VM에서 배포 후 구성 및 자동화 작업을 제공하는 소규모 애플리케이션으로, Azure 컨트롤 플레인에서 확장을 설치하고 관리합니다. [Azure Linux 에이전트](../extensions/agent-linux.md)의 작업은 플랫폼 확장 명령을 처리하고 VM 내에서 확장의 올바른 상태를 확인하는 작업입니다.

Azure 플랫폼은 VM 구성, 모니터링, 보안 및 유틸리티 애플리케이션에 이르는 다양한 확장을 호스팅합니다. 자사 및 타사 확장을 다양하게 선택할 수 있으며 확장을 사용하는 주요 시나리오의 예는 다음과 같습니다.
* Azure Backup, 모니터링, 디스크 암호화, 보안, 사이트 복제 등 자사 Azure 서비스를 지원합니다.
* SSH/암호 재설정
* VM 구성 - Chef, Puppet 에이전트 등을 설치하는 사용자 지정 스크립트를 실행합니다.
* AV 제품, VM 취약성 도구, VM 및 앱 모니터링 도구와 같은 타사 제품도 제공합니다.
* 확장은 새 VM 배포와 함께 번들로 묶을 수 있습니다. 예를 들어 대규모 배포의 일부이거나, VM 프로비전에서 애플리케이션을 구성하거나, 배포 후 지원되는 모든 확장 운영 체제에서 실행할 수 있습니다.

## <a name="disabling-extension-processing"></a>확장 처리 사용 안 함

필요에 따라 확장 처리를 사용하지 않도록 설정하는 몇 가지 방법이 있지만, 계속하려면 먼저 VM에 배포된 확장을 모두 **제거해야 합니다**. 예를 들어 Azure CLI를 사용하여 [나열](/cli/azure/vm/extension#az_vm_extension_list)하고 [삭제할](/cli/azure/vm/extension#az_vm_extension_delete) 수 있습니다.

```azurecli
az vm extension delete -g MyResourceGroup --vm-name MyVm -n extension_name
```
> [!Note]
> 
> 위의 작업을 수행하지 않으면 플랫폼은 40분 후 시간 제한 및 확장 구성을 보내려고 시도합니다.

### <a name="disable-at-the-control-plane"></a>컨트롤 플레인에서 사용 안 함
향후 확장이 필요한지 확실하지 않은 경우 VM에 Linux 에이전트를 설치된 상태로 두고 플랫폼에서 확장 처리 기능을 사용하지 않도록 설정할 수 있습니다. 이 옵션은 `Microsoft.Compute` api 버전 `2018-06-01` 이상에서 사용할 수 있으며 설치된 Linux 에이전트 버전에 대한 종속성은 없습니다.

```azurecli
az vm update -g <resourceGroup> -n <vmName> --set osProfile.allowExtensionOperations=false
```
위의 명령을 사용하여 'true'로 설정을 바꾸면 플랫폼에서 이 확장 처리를 쉽게 다시 사용하도록 설정할 수 있습니다.

## <a name="remove-the-linux-agent-from-a-running-vm"></a>실행 중인 VM에서 Linux 에이전트 제거

위에 따라 이전에 VM에서 모든 기존 확장을 **제거했는지** 확인합니다.

### <a name="step-1-remove-the-azure-linux-agent"></a>1단계: Azure Linux 에이전트 제거

연결된 구성 아티팩트는 제거하지 않고 Linux 에이전트만 제거하면 나중에 다시 설치할 수 있습니다. 다음 중 하나를 루트로 실행하여 Azure Linux 에이전트를 제거합니다.

#### <a name="for-ubuntu-1804"></a>Ubuntu의 경우 >=18.04
```bash
apt -y remove walinuxagent
```

#### <a name="for-redhat--77"></a>Redhat의 경우 >= 7.7
```bash
yum -y remove WALinuxAgent
```

#### <a name="for-suse"></a>SUSE의 경우
```bash
zypper --non-interactive remove python-azure-agent
```

### <a name="step-2-optional-remove-the-azure-linux-agent-artifacts"></a>2단계: (옵션) Azure Linux 에이전트 아티팩트 제거
> [!IMPORTANT] 
>
> Linux 에이전트의 연결된 아티팩트를 모두 제거할 수 있지만 나중에 다시 설치할 수는 없습니다. 따라서 먼저 Linux 에이전트를 사용하지 않도록 하고 위에 설명한 대로 수행하여 Linux 에이전트를 제거하는 것이 좋습니다. 

Linux 에이전트를 다시 설치하지 않을 경우 다음을 실행할 수 있습니다.

#### <a name="for-ubuntu-1804"></a>Ubuntu의 경우 >=18.04
```bash
apt -y purge walinuxagent
rm -rf /var/lib/waagent
rm -f /var/log/waagent.log
```

#### <a name="for-redhat--77"></a>Redhat의 경우 >= 7.7
```bash
yum -y remove WALinuxAgent
rm -f /etc/waagent.conf.rpmsave
rm -rf /var/lib/waagent
rm -f /var/log/waagent.log
```

#### <a name="for-suse"></a>SUSE의 경우
```bash
zypper --non-interactive remove python-azure-agent
rm -f /etc/waagent.conf.rpmsave
rm -rf /var/lib/waagent
rm -f /var/log/waagent.log
```

## <a name="preparing-an-image-without-the-linux-agent"></a>Linux 에이전트 없이 이미지 준비
이미 cloud-init을 포함하는 이미지가 있고 Linux 에이전트를 제거하더라도 cloud-init을 계속 사용하여 프로비전하려는 경우 2단계(및 필요에 따라 3단계)를 루트로 실행하여 Azure Linux 에이전트를 제거합니다. 그렇게 하면 cloud-init 구성 및 캐시된 데이터를 제거되고, 사용자 지정 이미지를 만들 수 있도록 VM을 준비합니다.

```bash
cloud-init clean --logs --seed 
```

## <a name="deprovision-and-create-an-image"></a>이미지 프로비전 해제 및 만들기
Linux 에이전트는 "waagent -deprovision+user" 단계를 사용하여 기존 이미지 메타데이터 중 일부를 정리할 수 있지만 에이전트가 제거된 후에는 아래와 같은 작업을 수행하여 다른 중요한 데이터를 제거해야 합니다.

- 모든 기존 ssh 호스트 키 제거

   ```bash
   rm /etc/ssh/ssh_host_*key*
   ```
- 관리자 계정 삭제

   ```bash
   touch /var/run/utmp
   userdel -f -r <admin_user_account>
   ```
- 루트 암호 삭제

   ```bash
   passwd -d root
   ```

위를 완료한 후에는 Azure CLI를 사용하여 사용자 지정 이미지를 만들 수 있습니다.


**일반 관리형 이미지 만들기**
```azurecli
az vm deallocate -g <resource_group> -n <vm_name>
az vm generalize -g <resource_group> -n <vm_name>
az image create -g <resource_group> -n <image_name> --source <vm_name>
```

**Azure Compute 갤러리에서 이미지 버전 만들기**

```azurecli
az sig image-version create \
    -g $sigResourceGroup 
    --gallery-name $sigName 
    --gallery-image-definition $imageDefName 
    --gallery-image-version 1.0.0 
    --managed-image /subscriptions/00000000-0000-0000-0000-00000000xxxx/resourceGroups/imageGroups/providers/images/MyManagedImage
```
### <a name="creating-a-vm-from-an-image-that-does-not-contain-a-linux-agent"></a>Linux 에이전트가 포함되지 않은 이미지에서 VM 만들기
Linux 에이전트가 없는 이미지에서 VM을 만드는 경우 확장이 이 VM에서 지원되지 않음을 VM 배포 구성에서 확인해야 합니다.

> [!NOTE] 
> 
> 위의 작업을 수행하지 않으면 플랫폼은 40분 후 시간 제한 및 확장 구성을 보내려고 시도합니다.

확장을 사용하지 않도록 설정된 VM을 배포하려면 Azure CLI를 [--enable-agent](/cli/azure/vm#az_vm_create)와 함께 사용할 수 있습니다.

```azurecli
az vm create \
    --resource-group $resourceGroup \
    --name $prodVmName \
    --image RedHat:RHEL:8.1-ci:latest \
    --admin-username azadmin \
    --ssh-key-value "$sshPubkeyPath" \
    --enable-agent false
```

또는 `"provisionVMAgent": false,`를 설정하고 ARM(Azure Resource Manager) 템플릿을 사용하여 이 작업을 수행할 수 있습니다.

```json
"osProfile": {
    "computerName": "[parameters('virtualMachineName')]",
    "adminUsername": "[parameters('adminUsername')]",
    "linuxConfiguration": {
        "disablePasswordAuthentication": "true",
        "provisionVMAgent": false,
        "ssh": {
            "publicKeys": [
                {
                    "path": "[concat('/home/', parameters('adminUsername'), '/.ssh/authorized_keys')]",
                    "keyData": "[parameters('adminPublicKey')]"
```

## <a name="next-steps"></a>다음 단계

자세한 내용은 [Linux 프로비저닝](provisioning.md)을 참조하세요.
