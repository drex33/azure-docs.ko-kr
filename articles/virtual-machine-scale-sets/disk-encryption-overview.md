---
title: Virtual Machine Scale Sets용 Azure Disk Encryption 사용
description: 이 문서에서는 Virtual Machine Scale Sets용 Microsoft Azure Disk Encryption을 사용하도록 설정하는 방법에 대한 지침을 제공합니다.
author: ju-shim
ms.author: jushiman
ms.topic: conceptual
ms.service: virtual-machine-scale-sets
ms.subservice: disks
ms.date: 10/10/2019
ms.reviewer: mimckitt
ms.openlocfilehash: 579315379923b5ba8a16f94cff5984e6e904a5ec
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/23/2021
ms.locfileid: "122691303"
---
# <a name="azure-disk-encryption-for-virtual-machine-scale-sets"></a>Virtual Machine Scale Sets를 위한 Azure Disk Encryption

**적용 대상:** :heavy_check_mark: Linux VM :heavy_check_mark: Windows VM :heavy_check_mark: 단일 확장 집합

Azure Disk Encryption은 가상 머신의 OS 및 데이터 디스크에 대한 볼륨 암호화를 제공하여 조직의 보안 및 규정 준수 약정에 맞게 데이터를 보호합니다. 자세한 내용은 [Azure Disk Encryption: Linux VM ](../virtual-machines/linux/disk-encryption-overview.md) 및 [Azure Disk Encryption: Windows VM](../virtual-machines/windows/disk-encryption-overview.md)을 참조하세요.  

Azure Disk Encryption은 다음과 같은 경우 Windows 및 Linux 가상 머신 확장 집합에도 적용할 수 있습니다.
- 관리 디스크를 통해 만든 확장 집합 Azure Disk 암호화는 네이티브(또는 비관리) 디스크 확장 집합에는 지원되지 않습니다.
- Windows 확장 집합의 OS 및 데이터 볼륨
- Linux 확장 집합의 데이터 볼륨 OS 디스크 암호화는 Linux 확장 집합에 대해 현재 지원되지 않습니다.

[Azure CLI를 사용하여 가상 머신 확장 집합 암호화](disk-encryption-cli.md) 또는 [Azure PowerShell을 사용하여 가상 머신 확장 집합 암호화](disk-encryption-powershell.md) 자습서를 사용하여 몇 분 만에 가상 머신 확장 집합에 대한 Azure Disk Encryption의 기본을 알아볼 수 있습니다.

## <a name="next-steps"></a>다음 단계

- [Azure Resource Manager를 사용하여 가상 머신 확장 집합 암호화](disk-encryption-azure-resource-manager.md)
- [Azure Disk Encryption을 위한 Key Vault 만들기 및 구성](disk-encryption-key-vault.md)
- [가상 머신 확장 집합 확장 시퀀싱을 통한 Azure Disk Encryption 사용](disk-encryption-extension-sequencing.md)
