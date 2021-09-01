---
title: 포함 파일
description: 포함 파일
services: virtual-machines-windows
author: rothja
manager: craigg
tags: azure-service-management
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 04/30/2018
ms.author: jroth
ms.custom: include file
ms.openlocfilehash: 2c33ad42eeb710edbffdf6448fc138eb6d6aae86
ms.sourcegitcommit: 03f0db2e8d91219cf88852c1e500ae86552d8249
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2021
ms.locfileid: "123078121"
---
## <a name="prepare-for-akv-integration"></a>AKV 통합 준비
Azure Key Vault 통합을 사용하여 SQL Server VM을 구성하려면 몇 가지 필수 조건이 있습니다. 

1. [Azure Powershell 설치](#install)
2. [Azure Active Directory 만들기](#register)
3. [키 자격 증명 모음 만들기](#createkeyvault)

다음 섹션에서는 이러한 필수 조건과 나중에 PowerShell cmdlet을 실행하기 위해 수집해야 하는 정보에 대해 설명합니다.

[!INCLUDE [updated-for-az](./updated-for-az.md)]

### <a name="install-azure-powershell"></a><a id="install"></a> Azure PowerShell 설치
최신 Azure PowerShell 모듈을 설치했는지 확인합니다. 자세한 내용은 [Azure PowerShell을 설치 및 구성하는 방법](/powershell/azure/install-az-ps)을 참조하세요.

### <a name="register-an-application-in-your-azure-active-directory"></a><a id="register"></a> Azure Active Directory 내 애플리케이션 등록

우선, 구독에 AAD( [Azure Active Directory](https://azure.microsoft.com/trial/get-started-active-directory/) )가 있어야 합니다. 이렇게 하면 여러 이점이 있지만, 그 중에서도 특정 사용자 및 애플리케이션에 키 자격 증명 모음에 대한 권한을 부여할 수 있다는 이점이 있습니다.

다음으로 AAD에 애플리케이션을 등록합니다. 이렇게 하면 VM에 필요한 Key Vault에 액세스할 수 있는 서비스 주체 계정이 제공됩니다. Azure Key Vault 문서의 [Azure Active Directory에 애플리케이션 등록](../articles/key-vault/general/manage-with-cli2.md#registering-an-application-with-azure-active-directory) 섹션에서 다음 단계를 찾아보거나 [이 블로그 게시물](/archive/blogs/kv/azure-key-vault-step-by-step)의 **애플리케이션용 ID 가져오기 섹션** 에서 스크린샷으로 단계를 볼 수 있습니다. 다음 단계를 완료하기 전에, SQL VM에서 Azure Key Vault 통합을 활성화할 때 필요한 다음 정보를 등록 과정에서 수집해야 합니다.

* 애플리케이션이 추가된 후 **등록된 앱** 블레이드에서 **애플리케이션 ID**(AAD ClientID 또는 AppID라고도 함)를 찾습니다.
    애플리케이션 ID는 나중에 Azure Key Vault 통합을 활성화하기 위해 PowerShell 스크립트의 **$spName** (서비스 주체 이름) 매개 변수에 할당됩니다.

   ![애플리케이션 ID](./media/virtual-machines-sql-server-akv-prepare/aad-application-id.png)

* 이 단계에서 키를 만드는 동안 다음 스크린샷에 보이는 것처럼 키 암호를 복사합니다. 이 키 암호는 나중에 PowerShell 스크립트의 **$spSecret** (서비스 주체 암호) 매개 변수에 할당됩니다.

   ![AAD 암호](./media/virtual-machines-sql-server-akv-prepare/aad-sp-secret.png)

* 애플리케이션 ID 및 암호는 또한 SQL Server에서 자격 증명을 만드는 데 사용됩니다.

* 이 새 애플리케이션 ID(또는 클라이언트 ID)에 **get**, **wrapKey**, **unwrapKey** 액세스 권한을 부여해야 합니다. 이 작업은 [Set-AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy) cmdlet을 통해 수행됩니다. 자세한 내용은 [Azure Key Vault 개요](../articles/key-vault/general/overview.md)를 참조하세요.

### <a name="create-a-key-vault"></a><a id="createkeyvault"></a> Key Vault 만들기
Azure Key Vault를 사용하여 암호화에 사용할 키를 VM에 저장하려면 키 자격 증명 모음에 액세스해야 합니다. 아직 Key Vault를 설정하지 않았으면 [Azure Key Vault 시작](../articles/key-vault/general/overview.md) 문서의 단계에 따라 새로 만듭니다. 다음 단계를 완료하기 전에, SQL VM에서 Azure Key Vault 통합을 활성화할 때 필요한 몇 가지 정보를 이 설정 과정에서 수집해야 합니다.

```azurepowershell
New-AzKeyVault -VaultName 'ContosoKeyVault' -ResourceGroupName 'ContosoResourceGroup' -Location 'East Asia'
```

주요 자격 증명 모음 만들기 단계에 이르면 반환된 **vaultUri** 속성을 잘 살펴보세요. 이 속성은 주요 자격 증명 모음 URL입니다. 아래와 같이 해당 단계에 제공된 예에서 주요 자격 증명 모음 이름이 ContosoKeyVault이므로 주요 자격 증명 모음 URL은 `https://contosokeyvault.vault.azure.net/`입니다.

주요 자격 증명 모음 URL은 나중에 Azure Key Vault 통합을 활성화하기 위해 PowerShell 스크립트의 **$akvURL** 매개 변수에 할당됩니다.

Key Vault를 만든 후 Key Vault에 키를 추가해야 하며 이 키는 나중에 SQL Server에서 비대칭 키를 만들 때 참조합니다.
