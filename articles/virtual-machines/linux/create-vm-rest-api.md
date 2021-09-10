---
title: REST API를 통해 Linux VM 만들기
description: Azure에서 Azure REST API를 통해 관리 디스크 및 SSH 인증을 사용하는 Linux 가상 머신을 만드는 방법에 대해 알아봅니다.
author: cynthn
ms.service: virtual-machines
ms.collection: linux
ms.topic: how-to
ms.date: 06/05/2018
ms.author: cynthn
ms.openlocfilehash: af006f25733344ebd272087b897130f895478685
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/23/2021
ms.locfileid: "122689766"
---
# <a name="create-a-linux-virtual-machine-that-uses-ssh-authentication-with-the-rest-api"></a>REST API를 통해 SSH 인증을 사용하는 Linux 가상 머신 만들기

**적용 대상:** :heavy_check_mark: Linux VM :heavy_check_mark: 유연한 확장 집합 

Azure의 Linux VM(가상 머신)은 디스크, 네트워크 인터페이스 같은 다양한 리소스로 구성되며 위치, 크기, 운영 체제 이미지 및 인증 설정 등의 매개 변수를 정의합니다.

Azure Portal, Azure CLI 2.0, 여러 Azure SDK, Azure Resource Manager 템플릿 및 Terraform 또는 Ansible 같은 많은 타사 도구를 통해 Linux VM을 만들 수 있습니다. 최종적으로 이러한 모든 도구는 Linux VM을 만드는 데 REST API를 사용합니다.

이 문서에서는 REST API를 사용하여 관리 디스크 및 SSH 인증을 통해 Ubuntu 18.04-LTS를 실행하는 Linux VM을 만드는 방법을 보여 줍니다.

## <a name="before-you-start"></a>시작하기 전에

요청을 만들고 제출하기 전에 다음이 필요합니다.

* 구독에 대한 `{subscription-id}`
  * 구독이 여러 개인 경우 [여러 구독으로 작업](/cli/azure/manage-azure-subscriptions-azure-cli)을 참조합니다.
* 미리 만든 `{resourceGroupName}`
* 같은 리소스 그룹의 [가상 네트워크 인터페이스](../../virtual-network/virtual-network-network-interface.md)
* SSH 키 쌍(없는 경우 [새로 생성할](mac-create-ssh-keys.md) 수 있음)

## <a name="request-basics"></a>요청 기본 사항

가상 머신을 만들거나 업데이트하려면 다음 *PUT* 작업을 사용합니다.

``` http
PUT https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachines/{vmName}?api-version=2017-12-01
```

`{subscription-id}` 및 `{resourceGroupName}` 매개 변수 외에도 `{vmName}`(`api-version`은 선택 사항이지만 이 문서는 `api-version=2017-12-01`로 테스트됨)을 지정해야 합니다.

다음과 같은 헤더가 필요합니다.

| 요청 헤더   | Description |
|------------------|-----------------|
| *Content-Type:*  | 필수 사항입니다. `application/json`로 설정합니다. |
| *권한 부여* | 필수 사항입니다. 유효한 `Bearer` [액세스 토큰](/rest/api/azure/#authorization-code-grant-interactive-clients)으로 설정합니다. |

REST API 요청 작업에 대한 일반 내용은 [REST API 요청/응답 구성 요소](/rest/api/azure/#components-of-a-rest-api-requestresponse)를 참조하세요.

## <a name="create-the-request-body"></a>요청 본문 만들기

다음과 같은 일반적인 정의가 요청 본문을 빌드하는 데 사용됩니다.

| 이름                       | 필수 | Type                                                                                | Description  |
|----------------------------|----------|-------------------------------------------------------------------------------------|--------------|
| 위치                   | True     | 문자열                                                                              | 리소스 위치. |
| name                       |          | 문자열                                                                              | 가상 머신의 이름. |
| properties.hardwareProfile |          | [HardwareProfile](/rest/api/compute/virtualmachines/createorupdate#hardwareprofile) | 가상 머신에 대한 하드웨어 설정을 지정합니다. |
| properties.storageProfile  |          | [StorageProfile](/rest/api/compute/virtualmachines/createorupdate#storageprofile)   | 가상 머신 디스크에 대한 스토리지 설정을 지정합니다. |
| properties.osProfile       |          | [OSProfile](/rest/api/compute/virtualmachines/createorupdate#osprofile)             | 가상 머신에 대한 운영 체제 설정을 지정합니다. |
| properties.networkProfile  |          | [NetworkProfile](/rest/api/compute/virtualmachines/createorupdate#networkprofile)   | 가상 머신의 네트워크 인터페이스를 지정합니다. |

요청 본문 예제는 아래에 있습니다. `keyData`에서 `{computerName}` 및 `{name}` 매개 변수의 VM 이름, `networkInterfaces`에 만든 네트워크 인터페이스 이름, `adminUsername` 및 `path`의 사용자 이름, SSH 키 쌍의 *public* 부분(위치 예: `~/.ssh/id_rsa.pub`)을 지정했는지 확인합니다. 다른 매개 변수의 경우 `location` 및 `vmSize`를 포함하여 수정할 수 있습니다.  

```json
{
  "location": "eastus",
  "name": "{vmName}",
  "properties": {
    "hardwareProfile": {
      "vmSize": "Standard_DS1_v2"
    },
    "storageProfile": {
      "imageReference": {
        "sku": "18.04-LTS",
        "publisher": "Canonical",
        "version": "latest",
        "offer": "UbuntuServer"
      },
      "osDisk": {
        "caching": "ReadWrite",
        "managedDisk": {
          "storageAccountType": "Premium_LRS"
        },
        "name": "myVMosdisk",
        "createOption": "FromImage"
      }
    },
    "osProfile": {
      "adminUsername": "{your-username}",
      "computerName": "{vmName}",
      "linuxConfiguration": {
        "ssh": {
          "publicKeys": [
            {
              "path": "/home/{your-username}/.ssh/authorized_keys",
              "keyData": "ssh-rsa AAAAB3NzaC1{snip}mf69/J1"
            }
          ]
        },
        "disablePasswordAuthentication": true
      }
    },
    "networkProfile": {
      "networkInterfaces": [
        {
          "id": "/subscriptions/{subscription-id}/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/networkInterfaces/{existing-nic-name}",
          "properties": {
            "primary": true
          }
        }
      ]
    }
  }
}
```

요청 본문에 사용할 수 있는 정의의 전체 목록은 [가상 머신 요청 본문 정의 만들기 또는 업데이트](/rest/api/compute/virtualmachines/createorupdate#definitions)를 참조하세요.

## <a name="sending-the-request"></a>요청 보내기

이 HTTP 요청을 보내기 위해 원하는 클라이언트를 사용할 수 있습니다. **사용** 단추를 클릭하여 [브라우저 도구](/rest/api/compute/virtualmachines/createorupdate)를 사용할 수도 있습니다.

### <a name="responses"></a>응답

가상 머신 만들기 또는 업데이트하는 작업에 대한 성공적인 응답에는 두 가지가 있습니다.

| 이름        | 유형                                                                              | Description |
|-------------|-----------------------------------------------------------------------------------|-------------|
| 200 정상      | [VirtualMachine](/rest/api/compute/virtualmachines/createorupdate#virtualmachine) | 정상          |
| 201 생성됨 | [VirtualMachine](/rest/api/compute/virtualmachines/createorupdate#virtualmachine) | 생성일     |

VM을 만든 이전 요청 본문 예제에서 압축된 *201 생성됨* 응답은 *vmId* 가 할당되었으며 *provisioningState* 가 *만들어지고 있음* 을 보여 줍니다.

```json
{
    "vmId": "e0de9b84-a506-4b95-9623-00a425d05c90",
    "provisioningState": "Creating"
}
```

REST API 응답에 대한 자세한 내용은 [응답 메시지 처리](/rest/api/azure/#process-the-response-message)를 참조하세요.

## <a name="next-steps"></a>다음 단계

Azure REST API 또는 Azure CLI, Azure PowerShell 등의 다른 관리 도구에 대한 자세한 내용은 다음 항목을 참조하세요.

- [Azure Compute 공급자 REST API](/rest/api/compute/)
- [Azure REST API 시작하기](/rest/api/azure/)
- [Azure CLI](/cli/azure/)
- [Azure PowerShell 모듈](/powershell/azure/)
