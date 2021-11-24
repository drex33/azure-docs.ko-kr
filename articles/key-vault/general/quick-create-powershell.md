---
title: 빠른 시작 - Azure PowerShell을 사용하여 Azure Key Vault 만들기
description: Azure PowerShell을 사용하여 Azure Key Vault를 만드는 방법을 보여주는 빠른 시작
services: key-vault
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: quickstart
ms.date: 01/27/2021
ms.author: mbaldwin
ms.custom: mode-api
ms.openlocfilehash: bb7764f0e7cb50188a2f4f5443ddfbbbbc5ce074
ms.sourcegitcommit: 56235f8694cc5f88db3afcc8c27ce769ecf455b0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/24/2021
ms.locfileid: "133047994"
---
# <a name="quickstart-create-a-key-vault-using-powershell"></a>빠른 시작: PowerShell을 사용하여 키 자격 증명 모음 만들기

Azure Key Vault는 [키](../keys/index.yml), [비밀](../secrets/index.yml) 및 [인증서](../certificates/index.yml)에 대한 보안 저장소를 제공하는 클라우드 서비스입니다. Key Vault에 대한 자세한 내용은 [Azure Key Vault 정보](overview.md)를 참조하세요. 키 자격 증명 모음에 저장할 수 있는 항목에 대한 자세한 내용은 [키, 비밀 및 인증서 정보](about-keys-secrets-certificates.md)를 참조하세요.

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

이 빠른 시작에서는 [Azure PowerShell](/powershell/azure/)을 사용하여 키 자격 증명 모음을 만듭니다. 로컬에서 PowerShell을 설치하고 사용하도록 선택하려는 경우 이 자습서를 진행하려면 Azure PowerShell 모듈 버전 1.0.0 이상이 필요합니다. `$PSVersionTable.PSVersion` 명령을 실행하여 버전을 찾습니다. 업그레이드해야 하는 경우 [Azure PowerShell 모듈 설치](/powershell/azure/install-az-ps)를 참조하세요. 또한 PowerShell을 로컬로 실행하는 경우 `Login-AzAccount`를 실행하여 Azure와 연결해야 합니다.

```azurepowershell-interactive
Login-AzAccount
```

## <a name="create-a-resource-group"></a>리소스 그룹 만들기

[!INCLUDE [Create a resource group](../../../includes/key-vault-powershell-rg-creation.md)]

## <a name="create-a-key-vault"></a>키 자격 증명 모음 만들기

[!INCLUDE [Create a key vault](../../../includes/key-vault-powershell-kv-creation.md)]

## <a name="clean-up-resources"></a>리소스 정리

[!INCLUDE [Create a key vault](../../../includes/key-vault-powershell-delete-resources.md)]

## <a name="next-steps"></a>다음 단계

이 빠른 시작에서는 Azure PowerShell을 사용하여 Key Vault를 만들었습니다. Key Vault 및 이를 애플리케이션과 통합하는 방법에 대해 자세히 알아보려면 아래 문서로 계속 진행하세요.

- [Azure Key Vault 개요](overview.md) 참조
- [Azure PowerShell Key Vault cmdlets](/powershell/module/az.keyvault/)에 대한 참조를 참조하세요.
- [Azure Key Vault 보안 개요](security-features.md)를 검토하세요.
