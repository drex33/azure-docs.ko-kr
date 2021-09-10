---
title: Windows용 Azure Disk Encryption
description: 가상 머신 확장을 사용하여 Azure Disk Encryption을 Windows 가상 머신에 배포합니다.
ms.topic: article
ms.service: virtual-machines
ms.subservice: disks
author: ejarvi
ms.author: ejarvi
ms.collection: windows
ms.date: 03/19/2020
ms.openlocfilehash: 679682ed0a268306bfb6c7afcf9abbd94a4d45b7
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122566908"
---
# <a name="azure-disk-encryption-for-windows-microsoftazuresecurityazurediskencryption"></a>Windows용 Azure Disk Encryption(Microsoft.Azure.Security.AzureDiskEncryption)

## <a name="overview"></a>개요

Azure Disk Encryption은 BitLocker를 활용하여 Windows를 실행하는 Azure 가상 머신에서 전체 디스크 암호화를 제공합니다.  이 솔루션은 Azure Key Vault와 통합되어 키 자격 증명 모음 구독의 디스크 암호화 키와 비밀을 관리합니다.

## <a name="prerequisites"></a>필수 구성 요소

사전 요구 사항의 전체 목록은 [Windows VM용 Azure Disk Encryption](../windows/disk-encryption-overview.md), 특히 다음 섹션을 참조하세요.

- [지원되는 VM 및 운영 체제](../windows/disk-encryption-overview.md#supported-vms-and-operating-systems)
- [네트워킹 요구 사항](../windows/disk-encryption-overview.md#networking-requirements)
- [그룹 정책 요구 사항](../windows/disk-encryption-overview.md#group-policy-requirements)

## <a name="extension-schema"></a>확장 스키마

Azure Disk Encryption(ADE)에는 두 가지 버전의 확장 스키마가 있습니다.
- v2.2 - AAD(Azure Active Directory) 속성을 사용하지 않는 최신 권장 스키마
- v1.1 - AAD(Azure Active Directory) 속성이 필요한 이전 스키마

대상 스키마를 선택하려면 `typeHandlerVersion` 속성을 사용하려는 스키마 버전과 동일하게 설정해야 합니다.

### <a name="schema-v22-no-aad-recommended"></a>스키마 v2.2: AAD 없음(권장)

V 2.2 스키마는 모든 새 VM에 권장되며, Azure Active Directory 속성이 필요하지 않습니다.

```json
{
  "type": "extensions",
  "name": "[name]",
  "apiVersion": "2019-07-01",
  "location": "[location]",
  "properties": {
        "publisher": "Microsoft.Azure.Security",
        "type": "AzureDiskEncryption",
        "typeHandlerVersion": "2.2",
        "autoUpgradeMinorVersion": true,
        "settings": {
          "EncryptionOperation": "[encryptionOperation]",
          "KeyEncryptionAlgorithm": "[keyEncryptionAlgorithm]",
          "KeyVaultURL": "[keyVaultURL]",
          "KeyVaultResourceId": "[keyVaultResourceID]",
          "KeyEncryptionKeyURL": "[keyEncryptionKeyURL]",
          "KekVaultResourceId": "[kekVaultResourceID]",
          "SequenceVersion": "sequenceVersion]",
          "VolumeType": "[volumeType]"
        }
  }
}
```


### <a name="schema-v11-with-aad"></a>Schema v1.1: AAD 사용

1\.1 스키마는 `aadClientID` 및 `aadClientSecret`와 `AADClientCertificate` 중 하나가 필요하며, 새 VM에는 권장되지 않습니다.

`aadClientSecret`사용:

```json
{
  "type": "extensions",
  "name": "[name]",
  "apiVersion": "2019-07-01",
  "location": "[location]",
  "properties": {
    "protectedSettings": {
      "AADClientSecret": "[aadClientSecret]"
    },
    "publisher": "Microsoft.Azure.Security",
    "type": "AzureDiskEncryption",
    "typeHandlerVersion": "1.1",
    "settings": {
      "AADClientID": "[aadClientID]",
      "EncryptionOperation": "[encryptionOperation]",
      "KeyEncryptionAlgorithm": "[keyEncryptionAlgorithm]",
      "KeyVaultURL": "[keyVaultURL]",
      "KeyVaultResourceId": "[keyVaultResourceID]",
      "KeyEncryptionKeyURL": "[keyEncryptionKeyURL]",
      "KekVaultResourceId": "[kekVaultResourceID]",
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
      "AADClientCertificate": "[aadClientCertificate]"
    },
    "publisher": "Microsoft.Azure.Security",
    "type": "AzureDiskEncryption",
    "typeHandlerVersion": "1.1",
    "settings": {
      "AADClientID": "[aadClientID]",
      "EncryptionOperation": "[encryptionOperation]",
      "KeyEncryptionAlgorithm": "[keyEncryptionAlgorithm]",
      "KeyVaultURL": "[keyVaultURL]",
      "KeyVaultResourceId": "[keyVaultResourceID]",
      "KeyEncryptionKeyURL": "[keyEncryptionKeyURL]",
      "KekVaultResourceId": "[kekVaultResourceID]",
      "SequenceVersion": "sequenceVersion]",
      "VolumeType": "[volumeType]"
    }
  }
}
```


### <a name="property-values"></a>속성 값

참고: 모든 값은 대/소문자를 구분합니다.

| Name | 값/예제 | 데이터 형식 |
| ---- | ---- | ---- |
| apiVersion | 2019-07-01 | date |
| publisher | Microsoft.Azure.Security | 문자열 |
| type | AzureDiskEncryption | 문자열 |
| typeHandlerVersion | 2.2, 1.1 | 문자열 |
| (0.1 스키마) AADClientID | xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx | guid |
| (1.1 스키마) AADClientSecret | password | 문자열 |
| (1.1 스키마) AADClientCertificate | thumbprint | 문자열 |
| EncryptionOperation | EnableEncryption | 문자열 |
| (선택 사항 - 기본 RSA-OAEP) KeyEncryptionAlgorithm | 'RSA-OAEP', 'RSA-OAEP-256', 'RSA1_5' | 문자열 |
| KeyVaultURL | url | 문자열 |
| KeyVaultResourceId | url | 문자열 |
| (선택 사항) KeyEncryptionKeyURL | url | 문자열 |
| (선택 사항) KekVaultResourceId | url | 문자열 |
| (선택 사항) SequenceVersion | uniqueidentifier | 문자열 |
| VolumeType | OS, Data, All | 문자열 |

## <a name="template-deployment"></a>템플릿 배포

스키마 v2.2를 기반으로 하는 템플릿 배포의 예는 Azure 빠른 시작 템플릿 [encrypt-running-windows-vm-without-aad](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.compute/encrypt-running-windows-vm-without-aad)를 참조하세요.

스키마 v1.1을 기반으로 하는 템플릿 배포의 예는 Azure 빠른 시작 템플릿 [encrypt-running-windows-vm](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.compute/encrypt-running-windows-vm)을 참조하세요.

>[!NOTE]
> 또한 `VolumeType` 매개 변수를 All로 설정하면 데이터 디스크는 형식이 올바르게 지정된 경우에만 암호화됩니다.

## <a name="troubleshoot-and-support"></a>문제 해결 및 지원

### <a name="troubleshoot"></a>문제 해결

문제 해결 방법은 [Azure Disk Encryption 문제 해결 가이드](../windows/disk-encryption-troubleshooting.md)를 참조하세요.

### <a name="support"></a>지원

이 문서의 어디에서든 도움이 필요한 경우 [MSDN Azure 및 Stack Overflow 포럼](https://azure.microsoft.com/support/community/)에서 Azure 전문가에게 문의할 수 있습니다.

또는 Azure 기술 지원 인시던트를 제출할 수 있습니다. [Azure 지원](https://azure.microsoft.com/support/options/)으로 이동하여 지원받기를 선택합니다. Azure 지원을 사용하는 방법에 대한 자세한 내용은 [Microsoft Azure 지원 FAQ](https://azure.microsoft.com/support/faq/)를 참조하세요.

## <a name="next-steps"></a>다음 단계

* 확장에 대한 자세한 내용은 [Windows용 가상 머신 확장 및 기능](features-windows.md)을 참조하세요.
* Windows용 Azure Disk Encryption에 대한 자세한 내용은 [Windows 가상 머신](../../security/fundamentals/azure-disk-encryption-vms-vmss.md#windows-virtual-machines)을 참조하세요.
