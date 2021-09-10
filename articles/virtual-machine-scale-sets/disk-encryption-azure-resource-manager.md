---
title: Azure Resource Manager 템플릿을 사용하여 Virtual Machine Scale Set 만들기 및 암호화
description: 이 빠른 시작에서는 Azure Resource Manager 템플릿을 사용하여 Virtual Machine Scale Set를 만들고 암호화하는 방법을 배웁니다.
author: ju-shim
ms.author: jushiman
ms.topic: how-to
ms.service: virtual-machine-scale-sets
ms.subservice: disks
ms.date: 10/10/2019
ms.reviewer: mimckitt
ms.custom: mimckitt
ms.openlocfilehash: 058ba9a3b2593ec6f635485771d5bbe140beb510
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/23/2021
ms.locfileid: "122698991"
---
# <a name="encrypt-virtual-machine-scale-sets-with-azure-resource-manager"></a>Azure Resource Manager를 사용한 가상 머신 확장 집합 암호화

**적용 대상:** :heavy_check_mark: Linux VM :heavy_check_mark: Windows VM :heavy_check_mark: 단일 확장 집합

Azure Resource Manager 템플릿을 사용하여 가상 머신 확장 집합을 암호화하거나 암호 해독을 수행할 수 있습니다.

## <a name="deploying-templates"></a>템플릿 배포

먼저 시나리오에 적합한 템플릿을 선택합니다.

- [실행 중인 Linux 가상 머신 확장 집합에서 디스크 암호화 사용](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.compute/encrypt-running-vmss-linux)

- [실행 중인 Windows 가상 머신 확장 집합에서 디스크 암호화 사용](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.compute/encrypt-running-vmss-windows)

  - [Jumpbox를 통해 Linux VM의 가상 머신 확장 집합 배포 및 Linux 가상 머신 확장 집합에서 암호화 사용](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.compute/encrypt-vmss-linux-jumpbox)

  - [Jumpbox를 통해 Windows VM의 가상 머신 확장 집합 배포 및 Windows 가상 머신 확장 집합에서 암호화 사용](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.compute/encrypt-vmss-windows-jumpbox)

- [실행 중인 Linux 가상 머신 확장 집합에서 디스크 암호화 사용 안 함](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.compute/decrypt-vmss-linux)

- [실행 중인 Windows 가상 머신 확장 집합에서 디스크 암호화 사용 안 함](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.compute/decrypt-vmss-windows)

그런 다음, 다음 단계를 수행합니다.

1. **Deploy to Azure** 를 클릭합니다.
2. 필수 필드를 채운 후 사용 약관에 동의합니다.
3. **구매** 를 클릭하여 템플릿을 배포합니다.

## <a name="next-steps"></a>다음 단계

- [가상 머신 확장 집합을 위한 Azure Disk Encryption](disk-encryption-overview.md)
- [Azure CLI를 사용하는 가상 머신 확장 집합 암호화](disk-encryption-cli.md)
- [Azure PowerShell을 사용하는 가상 머신 확장 집합 암호화](disk-encryption-powershell.md)
- [Azure Disk Encryption을 위한 Key Vault 만들기 및 구성](disk-encryption-key-vault.md)
- [가상 머신 확장 집합 확장 시퀀싱을 통한 Azure Disk Encryption 사용](disk-encryption-extension-sequencing.md)
