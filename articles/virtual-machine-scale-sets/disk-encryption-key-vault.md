---
title: Azure Disk Encryption을 위한 키 자격 증명 모음 만들기 및 구성
description: 이 문서에는 Azure Disk Encryption과 함께 사용할 키 자격 증명 모음을 만들어 구성하는 단계가 나와 있습니다.
author: ju-shim
ms.author: jushiman
ms.topic: tutorial
ms.service: virtual-machine-scale-sets
ms.subservice: disks
ms.date: 10/10/2019
ms.reviewer: mimckitt
ms.custom: mimckitt
ms.openlocfilehash: 904a78d9c8fcdc83b7fa837d744cf955ceda8d2b
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/23/2021
ms.locfileid: "122691390"
---
# <a name="create-and-configure-a-key-vault-for-azure-disk-encryption"></a>Azure Disk Encryption을 위한 Key Vault 만들기 및 구성

**적용 대상:** :heavy_check_mark: Linux VM :heavy_check_mark: Windows VM :heavy_check_mark: 균일한 확장 집합

Azure Disk Encryption은 Azure Key Vault를 사용하여 키 디스크 암호화 키와 비밀을 제어하고 관리합니다.  키 자격 증명 모음에 대한 자세한 내용은 [Azure Key Vault 시작](../key-vault/general/overview.md) 및 [키 자격 증명 모음 보안](../key-vault/general/secure-your-key-vault.md)을 참조하세요.

Azure Disk Encryption을 위한 키 자격 증명 모음 만들기 및 구성은 다음과 같이 세 단계로 이루어져 있습니다.

1. 필요한 경우 리소스 그룹 만들기.
2. 키 자격 증명 모음 만들기. 
3. 키 자격 증명 모음 고급 액세스 정책 설정.

이러한 단계는 다음 빠른 시작에 설명되어 있습니다.

원할 경우 KEK(키 암호화 키)를 생성하거나 가져올 수도 있습니다.

## <a name="install-tools-and-connect-to-azure"></a>도구 설치 및 Azure에 연결

이 문서의 단계는 [Azure CLI](/cli/azure/), [Azure PowerShell Az 모듈](/powershell/azure/) 또는 [Azure Portal](https://portal.azure.com)을 사용하여 완료할 수 있습니다.

### <a name="connect-to-your-azure-account"></a>Azure 계정에 연결

Azure CLI 또는 Azure PowerShell을 사용하기 전에 먼저 Azure 구독에 연결해야 합니다. 메시지가 표시되면 [Azure CLI로 로그인](/cli/azure/authenticate-azure-cli), [Azure PowerShell로 로그인](/powershell/azure/authenticate-azureps) 또는 자격 증명을 Azure Portal에 제공합니다.

```azurecli-interactive
az login
```

```azurepowershell-interactive
Connect-AzAccount
```

[!INCLUDE [disk-encryption-key-vault](../../includes/disk-encryption-key-vault.md)]
 
## <a name="next-steps"></a>다음 단계

- [Azure Disk Encryption 개요](disk-encryption-overview.md)
- [Azure CLI를 사용하는 가상 머신 확장 집합 암호화](disk-encryption-cli.md)
- [Azure PowerShell을 사용하는 가상 머신 확장 집합 암호화](disk-encryption-powershell.md)

