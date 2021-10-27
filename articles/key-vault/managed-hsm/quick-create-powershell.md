---
title: Azure Key Vault에서 관리형 키 특성 만들기 및 검색 - Azure PowerShell
description: Azure PowerShell을 사용하여 Azure Key Vault에서 관리형 키를 설정하고 검색하는 방법을 보여주는 빠른 시작
author: msmbaldwin
ms.author: mbaldwin
ms.date: 01/26/2021
ms.topic: quickstart
ms.service: key-vault
ms.subservice: keys
tags:
- azure-resource-manager
ms.custom: devx-track-azurepowershell - mode-api
ms.openlocfilehash: a9e4db26d2d40818c5b98c9aa5a8e46c272992b2
ms.sourcegitcommit: 147910fb817d93e0e53a36bb8d476207a2dd9e5e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/18/2021
ms.locfileid: "130131090"
---
# <a name="quickstart-set-and-retrieve-a-managed-key-from-azure-key-vault-using-powershell"></a>빠른 시작: PowerShell을 사용하여 Azure Key Vault에서 관리형 키 설정 및 검색

이 빠른 시작에서는 Azure PowerShell을 사용하여 Azure Key Vault에서 Key Vault를 만듭니다. Azure Key Vault는 보안 비밀 저장소로 작동하는 클라우드 서비스입니다. 키, 암호, 인증서 및 기타 비밀을 안전하게 저장할 수 있습니다. Key Vault에 대한 자세한 내용을 보려면 [개요](../general/overview.md)를 살펴보세요. Azure PowerShell은 명령 또는 스크립트를 사용하여 Azure 리소스를 만들고 관리하는 데 사용됩니다. 이 작업을 완료하면 키를 저장할 수 있습니다.

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

로컬에서 PowerShell을 설치하고 사용하도록 선택하려는 경우 이 자습서를 진행하려면 Azure PowerShell 모듈 버전 1.0.0 이상이 필요합니다. `$PSVersionTable.PSVersion` 명령을 실행하여 버전을 찾습니다. 업그레이드해야 하는 경우 [Azure PowerShell 모듈 설치](/powershell/azure/install-az-ps)를 참조하세요. 또한 PowerShell을 로컬로 실행하는 경우 `Login-AzAccount`를 실행하여 Azure와 연결해야 합니다.

```azurepowershell-interactive
Login-AzAccount
```

## <a name="create-a-resource-group"></a>리소스 그룹 만들기

리소스 그룹은 Azure 리소스가 배포 및 관리되는 논리적 컨테이너입니다. Azure PowerShell [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) cmdlet을 사용하여 *centralus* 위치에 *myResourceGroup* 이라는 리소스 그룹을 만듭니다. 

```azurepowershell-interactive
New-AzResourceGroup -Name "myResourceGroup" -Location "centralus"

## Get your principal ID

To create a managed HSM, you will need your Azure Active Directory principal ID.  To obtain your ID, use the Azure PowerShell [Get-AzADUser](/powershell/module/az.resources/get-azaduser) cmdlet, passing your email address to the "UserPrincipalName" parameter:

```azurepowershell-interactive
Get-AzADUser -UserPrincipalName "<your@email.address>"
```

주체 ID는 "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx" 형식으로 반환됩니다.

## <a name="create-a-managed-hsm"></a>관리형 HSM 만들기

Azure PowerShell [New-AzKeyVaultManagedHsm](/powershell/module/az.keyvault/new-azkeyvaultmanagedhsm) cmdlet을 사용하여 새 Key Vault 관리형 HSM을 만듭니다. 몇 가지 정보를 제공해야 합니다.

- 관리형 HSM 이름: 숫자(0-9), 문자(a-z, A-Z) 및 하이픈(-)만 포함할 수 있는 3~24자의 문자열

  > [!Important]
  > 각 관리형 HSM에는 고유한 이름이 있어야 합니다. 다음 예제에서 \<your-unique-managed-hsm-name\>을 관리형 HSM 이름으로 바꿉니다.

- 리소스 그룹 이름: **myResourceGroup**
- 위치: **미국 중부**.
- 사용자 보안 주체 ID: 마지막 섹션에서 가져온 Azure Active Directory 보안 주체 ID를 "Administrator" 매개 변수로 전달합니다. 

```azurepowershell-interactive
New-AzKeyVaultManagedHsm -Name "<your-unique-managed-hsm-name>" -ResourceGroupName "myResourceGroup" -Location "centralus" -Administrator "<your-principal-ID>"
```

이 cmdlet의 출력은 새로 만든 관리형 HSM의 속성을 보여줍니다. 아래에 나열된 두 개의 속성을 기록합니다.

- **관리형 HSM 이름**: 위의 --name 매개 변수에 제공한 이름입니다.
- **자격 증명 모음 URI**: 이 예제에서 이는 https://&lt;your-unique-managed-hsm-name&gt;.vault.azure.net/입니다. REST API를 통해 사용자 자격 증명 모음을 사용하는 애플리케이션은 URI를 사용해야 합니다.

이때 사용자의 Azure 계정은 이 새 자격 증명 모음에서 모든 작업을 수행할 권한이 있는 유일한 계정입니다.

## <a name="activate-your-managed-hsm"></a>관리형 HSM 활성화

HSM이 활성화되기 전까지는 모든 데이터 평면 명령이 비활성화됩니다. 키를 만들거나 역할을 할당할 수 없습니다. create 명령을 실행하는 동안 할당된 지정된 관리자만 HSM을 활성화할 수 있습니다. HSM을 활성화하려면 [보안 도메인](security-domain.md)을 다운로드해야 합니다.

HSM을 활성화하려면 다음이 필요합니다.
- 최소 3개의 RSA 키 쌍(최대 10개)
- 보안 도메인(쿼럼)의 암호를 해독하는 데 필요한 최소 키 수를 지정합니다.

HSM을 활성화하려면 적어도 3개(최대 10개)의 RSA 공개 키를 HSM에 전송합니다. HSM은 이러한 키를 사용하여 보안 도메인을 암호화하고 다시 보냅니다. 이 보안 도메인 다운로드가 성공적으로 완료되면 HSM을 사용할 준비가 된 것입니다. 또한 보안 도메인의 암호를 해독하는 데 필요한 최소 프라이빗 키 수인 쿼럼을 지정해야 합니다.

아래 예제에서는 `openssl`([여기](https://slproweb.com/products/Win32OpenSSL.html)에서 Windows에 사용)을 사용하여 자체 서명된 인증서 3개를 생성하는 방법을 보여줍니다.

```console
openssl req -newkey rsa:2048 -nodes -keyout cert_0.key -x509 -days 365 -out cert_0.cer
openssl req -newkey rsa:2048 -nodes -keyout cert_1.key -x509 -days 365 -out cert_1.cer
openssl req -newkey rsa:2048 -nodes -keyout cert_2.key -x509 -days 365 -out cert_2.cer
```

> [!IMPORTANT]
> 이 단계에서 생성된 RSA 키 쌍 및 보안 도메인 파일을 안전하게 만들어 저장합니다.

Azure PowerShell [Export-AzKeyVaultSecurityDomain](/powershell/module/az.keyvault/export-azkeyvaultsecuritydomain) cmdlet을 사용하여 보안 도메인을 다운로드하고 관리형 HSM을 활성화합니다. 아래 예제에서는 3개의 RSA 키 쌍을 사용하고(이 명령에는 공개 키만 필요함) 쿼럼을 2로 설정합니다.

```azurepowershell-interactive
Export-AzKeyVaultSecurityDomain -Name "<your-unique-managed-hsm-name>" -Certificates "cert_0.cer", "cert_1.cer", "cert_2.cer" -OutputPath "MHSMsd.ps.json" -Quorum 2
```

보안 도메인 파일과 RSA 키 쌍을 안전하게 저장하세요. 재해 복구를 위해 또는 동일한 보안 도메인을 공유하는 다른 관리형 HSM을 만들기 위해 키를 공유할 수 있도록 해야 합니다.

보안 도메인을 성공적으로 다운로드한 후 HSM은 활성 상태가 되고 사용할 준비가 됩니다.

## <a name="clean-up-resources"></a>리소스 정리

[!INCLUDE [Create a key vault](../../../includes/key-vault-powershell-delete-resources.md)]

## <a name="next-steps"></a>다음 단계

이 빠른 시작에서는 Key Vault를 만들어 인증서를 저장했습니다. Key Vault 및 이를 애플리케이션과 통합하는 방법에 대해 자세히 알아보려면 아래 문서로 계속 진행하세요.

- [Azure Key Vault 개요](../general/overview.md) 참조
- [Azure PowerShell Key Vault cmdlets](/powershell/module/az.keyvault/)에 대한 참조를 참조하세요.
- [Azure Key Vault 보안 개요](../general/security-features.md)를 검토하세요.
