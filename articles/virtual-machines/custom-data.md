---
title: 사용자 지정 데이터 및 Azure Virtual Machines
description: 사용자 지정 데이터와 Azure Virtual Machines에서의 Cloud-Int 사용에 대한 자세한 정보
services: virtual-machines
author: mimckitt
ms.service: virtual-machines
ms.topic: how-to
ms.date: 03/06/2020
ms.author: mimckitt
ms.openlocfilehash: bf81d49bbbfaae2b96dfb712ae69568695ba6f23
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/23/2021
ms.locfileid: "122687518"
---
# <a name="custom-data-and-cloud-init-on-azure-virtual-machines"></a>사용자 지정 데이터와 Azure Virtual Machines에서의 Cloud-Int

**적용 대상:** :heavy_check_mark: Linux VM :heavy_check_mark: Windows VM :heavy_check_mark: 유연한 확장 집합

프로비전 시 스크립트 또는 기타 메타데이터를 Microsoft Azure 가상 머신에 삽입해야 할 수 있습니다.  다른 클라우드에서는 이 개념을 사용자 데이터라고 하는 경우가 많습니다.  Microsoft Azure에는 사용자 지정 데이터라는 유사한 기능이 있습니다. 

사용자 지정 데이터는 첫 부팅/최초 설정 시에만 VM에 제공되므로 이를 ‘프로비저닝’이라고 합니다. 프로비저닝은 VM Create 매개변수(예: 호스트 이름, 사용자 이름, 암호, 인증서, 사용자 지정 데이터, 키)를 VM에 제공하고 프로비저닝 에이전트(예: [Linux Agent](./extensions/agent-linux.md) 및 [cloud-init](./linux/using-cloud-init.md#troubleshooting-cloud-init))가 이를 처리하는 과정입니다. 


## <a name="passing-custom-data-to-the-vm"></a>VM에 사용자 지정 데이터 전달
사용자 지정 데이터를 사용하려면 base64를 사용하여 내용을 인코딩한 다음, API에 전달해야 합니다. 단, AZ CLI와 같이 변환을 대신해주는 CLI 도구를 사용할 경우에는 예외입니다. 파일 크기는 64KB를 초과할 수 없습니다.

CLI에서 사용자 지정 데이터를 파일로 전달할 수 있고 이는 base64로 변환됩니다.
```bash
az vm create \
  --resource-group myResourceGroup \
  --name centos74 \
  --image OpenLogic:CentOS-CI:7-CI:latest \
  --custom-data cloud-init.txt \
  --generate-ssh-keys
```

Azure Resource Manager(ARM)에는 [base64 함수](../azure-resource-manager/templates/template-functions-string.md#base64)가 있습니다.

```json
"name": "[parameters('virtualMachineName')]",
"type": "Microsoft.Compute/virtualMachines",
"apiVersion": "2019-07-01",
"location": "[parameters('location')]",
"dependsOn": [
..],
"variables": {
        "customDataBase64": "[base64(parameters('stringData'))]"
    },
"properties": {
..
    "osProfile": {
        "computerName": "[parameters('virtualMachineName')]",
        "adminUsername": "[parameters('adminUsername')]",
        "adminPassword": "[parameters('adminPassword')]",
        "customData": "[variables('customDataBase64')]"
        },
```

## <a name="processing-custom-data"></a>사용자 지정 데이터 처리
VM에 설치된 프로비저닝 에이전트가 플랫폼과의 연결을 처리하고 파일 시스템에 할당합니다. 

### <a name="windows"></a>Windows
사용자 지정 데이터는 *%SYSTEMDRIVE%\AzureData\CustomData.bin* 에 바이너리 파일로 저장되지만 처리되지는 않습니다. 이 파일을 처리하려면 사용자 지정 이미지를 빌드하고 CustomData.bin을 처리하는 코드를 작성해야 합니다.

### <a name="linux"></a>Linux  
Linux OS에서 사용자 지정 데이터는 ovf-env.xml 파일을 통해 VM에 전달되는데, 프로비저닝 중에 */var/lib/waagent* 디렉터리로 복사됩니다.  Microsoft Azure Linux Agent의 최신 버전도 편의를 위해 base64로 인코딩된 데이터를 */var/lib/waagent/CustomData* 에 복사합니다.

현재 Azure는 두 가지 프로비저닝 에이전트를 지원합니다.
* Linux Agent - 기본적으로 Linux 에이전트는 사용자 지정 데이터를 처리하지 않습니다. 이를 활성화한 상태에서 사용자 지정 이미지를 빌드해야 합니다. [설명서](https://github.com/Azure/WALinuxAgent#configuration)에 나와 있는 관련 설정은 다음과 같습니다.
    * Provisioning.DecodeCustomData
    * Provisioning.ExecuteCustomData

사용자 지정 데이터를 활성화하고 스크립트를 실행하면 VM이 준비되었다는 보고 또는 프로비저닝이 성공했다는 보고를 스크립트가 완료될 때까지 미룹니다. 스크립트가 허용된 VM 프로비저닝 시간(40분)을 초과하면 VM 만들기가 실패합니다. 참고로 스크립트가 실행되지 않거나 실행 중에 오류가 발생하면 치명적인 프로비저닝 실패로 간주되지 않으므로 스크립트의 완료 상태를 알려주는 알림 경로를 생성해야 합니다.

사용자 지정 데이터 실행 문제를 해결하려면 */var/log/waagent.log* 를 검토하세요.

* cloud-init - 기본적으로 사용자 지정 데이터를 처리합니다. cloud-init는 사용자 지정 데이터의 [여러 가지 형식](https://cloudinit.readthedocs.io/en/latest/topics/format.html)(예: cloud-init 구성, 스크립트)을 지원합니다. Linux Agent와 마찬가지로 cloud-init는 사용자 지정 데이터를 처리합니다. 구성 처리 또는 스크립트 실행 중에 오류가 발생할 경우 치명적인 프로비저닝 실패로 간주되지 않으므로 스크립트의 완료 상태를 알려주는 알림 경로를 생성해야 합니다. 그러나 Linux Agent와 달리 cloud-init는 사용자 지정 데이터 구성이 완료되기를 기다리지 않고 플랫폼에 VM이 준비되었다고 보고합니다. Azure의 cloud-init에 대한 자세한 내용은 [설명서](./linux/using-cloud-init.md)를 참조하세요.


사용자 지정 데이터 실행 문제를 해결하려면 문제 해결 [설명서](./linux/using-cloud-init.md#troubleshooting-cloud-init)를 참조하세요.


## <a name="faq"></a>FAQ
### <a name="can-i-update-custom-data-after-the-vm-has-been-created"></a>VM이 생성된 후 사용자 지정 데이터를 업데이트할 수 있나요?
단일 VM의 경우, VM 모델의 사용자 지정 데이터는 업데이트할 수 없지만 VMSS에서는 [REST API](/rest/api/compute/virtualmachinescalesets/update)를 통해 VMSS 사용자 지정 데이터를 업데이트할 수 있습니다(PS 또는 AZ CLI 클라이언트에는 적용되지 않음). VMSS 모델에서 사용자 지정 데이터 업데이트:
* VMSS의 기존 인스턴스는 새로 이미지를 생성하지 않는 한 업데이트된 사용자 지정 데이터를 가져오지 않습니다.
* 업그레이드된 VMSS의 기존 인스턴스는 업데이트된 사용자 지정 데이터를 가져오지 않습니다.
* 새 인스턴스는 새 사용자 지정 데이터를 받게 됩니다.

### <a name="can-i-place-sensitive-values-in-custom-data"></a>사용자 지정 데이터에 중요한 값을 저장할 수 있나요?
사용자 지정 데이터에는 중요한 데이터를 저장하지 **않는** 것이 좋습니다. 자세한 내용은 [Azure 보안 및 암호화 모범 사례](../security/fundamentals/data-encryption-best-practices.md)를 참조하세요.


### <a name="is-custom-data-made-available-in-imds"></a>사용자 지정 데이터가 IMDS에 제공되나요?
IMDS에서는 사용자 지정 데이터를 사용할 수 없습니다. IMDS를 통해 사용자 데이터를 대신 사용하는 것이 좋습니다. 자세한 내용은 [Azure Instance Metadata Service를 통한 사용자 데이터](./linux/instance-metadata-service.md?tabs=linux#get-user-data)를 참조하세요.
