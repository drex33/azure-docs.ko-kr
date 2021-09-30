---
title: Linux용 Azure Disk Encryption
description: 가상 머신 확장을 사용하여 Linux용 Azure Disk Encryption을 가상 머신에 배포합니다.
ms.topic: article
ms.service: virtual-machines
ms.subservice: disks
author: ejarvi
ms.author: ejarvi
ms.date: 03/19/2020
ms.collection: linux
ms.openlocfilehash: e49b017980fa918be57d4dcc8f9c880bc73c6226
ms.sourcegitcommit: 613789059b275cfae44f2a983906cca06a8706ad
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/29/2021
ms.locfileid: "129275148"
---
# <a name="azure-disk-encryption-for-linux-microsoftazuresecurityazurediskencryptionforlinux"></a>Linux용 Azure Disk Encryption(Microsoft.Azure.Security.AzureDiskEncryptionForLinux)

## <a name="overview"></a>개요

Azure Disk Encryption은 Linux에서 dm-crypt 서브 시스템을 활용하여 [Azure Linux 배포판 선택](../linux/disk-encryption-overview.md)에서 전체 디스크 암호화를 제공합니다.  이 솔루션은 Azure Key Vault와 통합되어 디스크 암호화 키와 비밀을 관리합니다.

## <a name="prerequisites"></a>필수 구성 요소

필수 구성 요소의 전체 목록은 [Linux VM용 Azure Disk Encryption](../linux/disk-encryption-overview.md), 특히 다음 섹션을 참조하세요.

- [지원되는 VM 및 운영 체제](../linux/disk-encryption-overview.md#supported-vms-and-operating-systems)
- [추가 VM 요구 사항](../linux/disk-encryption-overview.md#additional-vm-requirements)
- [네트워킹 요구 사항](../linux/disk-encryption-overview.md#networking-requirements)
- [암호화 키 스토리지 요구 사항](../linux/disk-encryption-overview.md#encryption-key-storage-requirements)

## <a name="extension-schema"></a>확장 스키마

Azure Disk Encryption(ADE)에는 두 가지 버전의 확장 스키마가 있습니다.
- v1.1 - AAD(Azure Active Directory) 속성을 사용하지 않는 최신 권장 스키마
- v0.1 - AAD(Azure Active Directory) 속성이 필요한 이전 스키마

대상 스키마를 선택하려면 `typeHandlerVersion` 속성을 사용하려는 스키마 버전과 동일하게 설정해야 합니다.

### <a name="schema-v11-no-aad-recommended"></a>스키마 v1.1: AAD 없음(권장)

v1.1 스키마가 권장되며 AAD(Azure Active Directory) 속성은 필요하지 않습니다.

> [!NOTE]
> `DiskFormatQuery`매개 변수는 더 이상 사용 되지 않습니다. 암호화 시 데이터 디스크의 형식을 지정 하는 데 권장 되는 방법인 대신 EncryptFormatAll 옵션으로 대체 되었습니다.

```json
{
  "type": "extensions",
  "name": "[name]",
  "apiVersion": "2019-07-01",
  "location": "[location]",
  "properties": {
        "publisher": "Microsoft.Azure.Security",
        "type": "AzureDiskEncryptionForLinux",
        "typeHandlerVersion": "1.1",
        "autoUpgradeMinorVersion": true,
        "settings": {
          "DiskFormatQuery": "[diskFormatQuery]",
          "EncryptionOperation": "[encryptionOperation]",
          "KeyEncryptionAlgorithm": "[keyEncryptionAlgorithm]",
          "KeyVaultURL": "[keyVaultURL]",
          "KeyVaultResourceId": "[KeyVaultResourceId]",
          "KeyEncryptionKeyURL": "[keyEncryptionKeyURL]",
          "KekVaultResourceId": "[KekVaultResourceId",
          "SequenceVersion": "sequenceVersion]",
          "VolumeType": "[volumeType]"
        }
  }
}
```


### <a name="schema-v01-with-aad"></a>스키마 v0.1: AAD 사용

0\.1 스키마에는 `AADClientID`와 `AADClientSecret` 또는 `AADClientCertificate` 중 하나가 필요합니다.

`AADClientSecret`사용:

```json
{
  "type": "extensions",
  "name": "[name]",
  "apiVersion": "2019-07-01",
  "location": "[location]",
  "properties": {
    "protectedSettings": {
      "AADClientSecret": "[aadClientSecret]",
      "Passphrase": "[passphrase]"
    },
    "publisher": "Microsoft.Azure.Security",
    "type": "AzureDiskEncryptionForLinux",
    "typeHandlerVersion": "0.1",
    "settings": {
      "AADClientID": "[aadClientID]",
      "DiskFormatQuery": "[diskFormatQuery]",
      "EncryptionOperation": "[encryptionOperation]",
      "KeyEncryptionAlgorithm": "[keyEncryptionAlgorithm]",
      "KeyEncryptionKeyURL": "[keyEncryptionKeyURL]",
      "KeyVaultURL": "[keyVaultURL]",
      "SequenceVersion": "sequenceVersion]",
      "VolumeType": "[volumeType]"
    }
  }
}
```

`AADClientCertificate`사용:

```json
{
  "type": "extensions",
  "name": "[name]",
  "apiVersion": "2019-07-01",
  "location": "[location]",
  "properties": {
    "protectedSettings": {
      "AADClientCertificate": "[aadClientCertificate]",
      "Passphrase": "[passphrase]"
    },
    "publisher": "Microsoft.Azure.Security",
    "type": "AzureDiskEncryptionForLinux",
    "typeHandlerVersion": "0.1",
    "settings": {
      "AADClientID": "[aadClientID]",
      "DiskFormatQuery": "[diskFormatQuery]",
      "EncryptionOperation": "[encryptionOperation]",
      "KeyEncryptionAlgorithm": "[keyEncryptionAlgorithm]",
      "KeyEncryptionKeyURL": "[keyEncryptionKeyURL]",
      "KeyVaultURL": "[keyVaultURL]",
      "SequenceVersion": "sequenceVersion]",
      "VolumeType": "[volumeType]"
    }
  }
}
```


### <a name="property-values"></a>속성 값

참고: 모든 속성 값은 대/소문자를 구분합니다.

| Name | 값/예제 | 데이터 형식 |
| ---- | ---- | ---- |
| apiVersion | 2019-07-01 | date |
| publisher | Microsoft.Azure.Security | 문자열 |
| type | AzureDiskEncryptionForLinux | 문자열 |
| typeHandlerVersion | 1.1, 0.1 | int |
| (0.1 스키마) AADClientID | xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx | guid |
| (0.1 스키마) AADClientSecret | password | 문자열 |
| (0.1 스키마) AADClientCertificate | thumbprint | 문자열 |
| (선택 사항)(0.1 스키마) 암호 | password | 문자열 |
| DiskFormatQuery | {"dev_path":"","name":"","file_system":""} | JSON 사전 |
| EncryptionOperation | EnableEncryption, EnableEncryptionFormatAll | 문자열 |
| (선택 사항 - 기본 RSA-OAEP) KeyEncryptionAlgorithm | 'RSA-OAEP', 'RSA-OAEP-256', 'RSA1_5' | 문자열 |
| KeyVaultURL | url | 문자열 |
| KeyVaultResourceId | url | 문자열 |
| (선택 사항) KeyEncryptionKeyURL | url | 문자열 |
| (선택 사항) KekVaultResourceId | url | 문자열 |
| (선택 사항) SequenceVersion | uniqueidentifier | 문자열 |
| VolumeType | OS, Data, All | 문자열 |

## <a name="template-deployment"></a>템플릿 배포

스키마 v1.1을 기반으로 하는 템플릿 배포의 예는 Azure 빠른 시작 템플릿 [encrypt-running-linux-vm-without-aad](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.compute/encrypt-running-linux-vm-without-aad)를 참조하세요.

스키마 v0.1을 기반으로 하는 템플릿 배포의 예는 Azure 빠른 시작 템플릿 [encrypt-running-linux-vm](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.compute/encrypt-running-linux-vm)을 참조하세요.

>[!WARNING]
> - 이전에 VM을 암호화하기 위해 Azure AD에서 Azure Disk Encryption을 사용한 적이 있다면 VM을 암호화하는 데 이 옵션을 계속 사용해야 합니다.
> - Linux OS 볼륨을 암호화하는 경우, VM은 사용할 수 없는 것으로 간주해야 합니다. 암호화 프로세스 중에 액세스해야 할 모든 열린 파일을 차단하는 문제를 방지하려면 암호화가 진행 중일 때 SSH 로그인을 하지 말 것을 적극 권장합니다. 진행률을 확인하려면 [Get-AzVMDiskEncryptionStatus](/powershell/module/az.compute/get-azvmdiskencryptionstatus) PowerShell cmdlet을 사용하거나 [vm encryption show](/cli/azure/vm/encryption#az_vm_encryption_show) CLI 명령을 사용합니다. 이 프로세스는 30GB OS 볼륨을 처리하는 데 몇 시간이 걸리고 데이터 볼륨을 암호화하는 데 추가 시간이 걸릴 수 있습니다. 데이터 볼륨 암호화 시간은 encrypt format all 옵션이 사용되지 않는 한 데이터 볼륨의 크기 및 수량에 비례합니다.
> - Linux VM에서 암호화 사용 안 함은 데이터 볼륨에 대해서만 지원됩니다. OS 볼륨이 암호화된 경우 이 설정은 데이터 또는 OS 볼륨에서 지원되지 않습니다.

>[!NOTE]
> 또한 `VolumeType` 매개 변수를 All로 설정하면 데이터 디스크가 적절히 탑재된 경우에만 암호화됩니다.

## <a name="troubleshoot-and-support"></a>문제 해결 및 지원

### <a name="troubleshoot"></a>문제 해결

문제 해결 방법은 [Azure Disk Encryption 문제 해결 가이드](../linux/disk-encryption-troubleshooting.md)를 참조하세요.

### <a name="support"></a>지원

이 문서의 어디에서든 도움이 필요한 경우 [MSDN Azure 및 Stack Overflow 포럼](https://azure.microsoft.com/support/community/)에서 Azure 전문가에게 문의할 수 있습니다.

또는 Azure 기술 지원 인시던트를 제출할 수 있습니다. [Azure 지원](https://azure.microsoft.com/support/options/)으로 이동하여 지원받기를 선택합니다. Azure 지원을 사용하는 방법에 대한 자세한 내용은 [Microsoft Azure 지원 FAQ](https://azure.microsoft.com/support/faq/)를 참조하세요.

## <a name="next-steps"></a>다음 단계

* VM 확장에 대한 자세한 내용은 [Linux용 가상 머신 확장 및 기능 개요](features-linux.md)를 참조하세요.
* Linux용 Azure Disk Encryption에 대한 자세한 내용은 [Linux 가상 머신](../../security/fundamentals/azure-disk-encryption-vms-vmss.md#linux-virtual-machines)을 참조하세요.
