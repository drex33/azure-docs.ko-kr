---
title: Azure Disk Encryption을 위한 키 자격 증명 모음 만들기 및 구성
description: 이 문서에서는 Linux VM에서 Azure Disk Encryption과 함께 사용할 키 자격 증명 모음을 만들고 구성하는 단계를 설명합니다.
ms.service: virtual-machines
ms.collection: linux
ms.subservice: disks
ms.topic: conceptual
author: msmbaldwin
ms.author: mbaldwin
ms.date: 08/06/2019
ms.custom: seodec18, devx-track-azurecli
ms.openlocfilehash: 44464e762ea15bd7b9f95988fc85d5bfb969973d
ms.sourcegitcommit: 2da83b54b4adce2f9aeeed9f485bb3dbec6b8023
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/24/2021
ms.locfileid: "122770440"
---
# <a name="creating-and-configuring-a-key-vault-for-azure-disk-encryption"></a>Azure Disk Encryption을 위한 키 자격 증명 모음 만들기 및 구성

**적용 대상:** :heavy_check_mark: Linux VM :heavy_check_mark: 유연한 확장 집합 

Azure Disk Encryption은 Azure Key Vault를 사용하여 키 디스크 암호화 키와 비밀을 제어하고 관리합니다.  키 자격 증명 모음에 대한 자세한 내용은 [Azure Key Vault 시작](../../key-vault/general/overview.md) 및 [키 자격 증명 모음 보안](../../key-vault/general/security-features.md)을 참조하세요. 


> [!WARNING]
> - 이전에 VM을 암호화하기 위해 Azure AD에서 Azure Disk Encryption을 사용한 적이 있다면 VM을 암호화하는 데 이 옵션을 계속 사용해야 합니다. 자세한 내용은 [Azure AD를 사용하여 Azure Disk Encryption을 위한 키 자격 증명 모음 만들기 및 구성](disk-encryption-key-vault-aad.md)을 참조하세요.

Azure Disk Encryption을 위한 키 자격 증명 모음 만들기 및 구성은 다음과 같이 세 단계로 이루어져 있습니다.

1. 필요한 경우 리소스 그룹 만들기.
2. 키 자격 증명 모음 만들기. 
3. 키 자격 증명 모음 고급 액세스 정책 설정.

이러한 단계는 다음 빠른 시작에 설명되어 있습니다.

- [Azure CLI를 사용하여 Linux VM 만들기 및 암호화](disk-encryption-cli-quickstart.md)
- [Azure Powershell을 사용하여 Linux VM 만들기 및 암호화](disk-encryption-powershell-quickstart.md)

원할 경우 KEK(키 암호화 키)를 생성하거나 가져올 수도 있습니다.

> [!Note]
> 이 문서의 단계는 [Azure Disk Encryption 필수 구성 요소 CLI 스크립트](https://github.com/ejarvi/ade-cli-getting-started) 및 [Azure Disk Encryption 필수 구성 요소 PowerShell 스크립트](https://github.com/Azure/azure-powershell/tree/master/src/Compute/Compute/Extension/AzureDiskEncryption/Scripts)에서 자동화됩니다.

## <a name="install-tools-and-connect-to-azure"></a>도구 설치 및 Azure에 연결

이 문서의 단계는 [Azure CLI](/cli/azure/), [Azure PowerShell Az 모듈](/powershell/azure/) 또는 [Azure Portal](https://portal.azure.com)을 사용하여 완료할 수 있습니다. 

브라우저를 통해 포털에 액세스할 수 있지만 Azure CLI 및 Azure PowerShell에는 로컬 설치가 필요합니다. 자세한 내용은 [Linux용 Azure Disk Encryption: 도구 설치](disk-encryption-linux.md#install-tools-and-connect-to-azure)를 참조하세요.

### <a name="connect-to-your-azure-account"></a>Azure 계정에 연결

Azure CLI 또는 Azure PowerShell을 사용하기 전에 먼저 Azure 구독에 연결해야 합니다. 메시지가 표시되면 [Azure CLI로 로그인](/cli/azure/authenticate-azure-cli), [Azure PowerShell로 로그인](/powershell/azure/authenticate-azureps) 또는 자격 증명을 Azure Portal에 제공합니다.

```azurecli-interactive
az login
```

```azurepowershell-interactive
Connect-AzAccount
```

[!INCLUDE [disk-encryption-key-vault](../../../includes/disk-encryption-key-vault.md)]
 
 
## <a name="next-steps"></a>다음 단계

- [Azure Disk Encryption 필수 구성 요소 CLI 스크립트](https://github.com/ejarvi/ade-cli-getting-started)
- [Azure Disk Encryption 필수 조건 PowerShell 스크립트](https://github.com/Azure/azure-powershell/tree/master/src/Compute/Compute/Extension/AzureDiskEncryption/Scripts)
- [Linux VM에 대한 Azure Disk Encryption 시나리오](disk-encryption-linux.md) 알아보기
- [Azure Disk Encryption 문제 해결](disk-encryption-troubleshooting.md) 방법 알아보기
- [Azure Disk Encryption 샘플 스크립트](disk-encryption-sample-scripts.md) 읽기
