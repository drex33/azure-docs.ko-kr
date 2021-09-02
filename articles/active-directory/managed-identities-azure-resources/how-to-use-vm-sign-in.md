---
title: Azure VM에 로그인 시 관리 ID 사용 - Azure ADV
description: 스크립트 클라이언트 로그인 및 리소스 액세스를 위해 Azure 리소스 서비스 주체의 Azure VM 관리 ID를 사용하는 단계별 지침 및 예입니다.
services: active-directory
documentationcenter: ''
author: barclayn
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 01/29/2021
ms.author: barclayn
ms.collection: M365-identity-device-management
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 02ec45e159cc5f10b0685d1e41ef2103c421aaa7
ms.sourcegitcommit: 16e25fb3a5fa8fc054e16f30dc925a7276f2a4cb
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/25/2021
ms.locfileid: "122829580"
---
# <a name="how-to-use-managed-identities-for-azure-resources-on-an-azure-vm-for-sign-in"></a>로그인을 위해 Azure VM에서 Azure 리소스에 대한 관리 ID를 사용하는 방법 

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]  
이 문서에서는 Azure 리소스 서비스 주체의 관리 ID를 사용한 로그인에 대한 PowerShell 및 CLI 스크립트 예제 및 오류 처리와 같은 중요한 항목에 대한 지침을 제공합니다.

[!INCLUDE [az-powershell-update](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>사전 요구 사항

[!INCLUDE [msi-qs-configure-prereqs](../../../includes/active-directory-msi-qs-configure-prereqs.md)]

이 문서에서 Azure PowerShell 및 Azure CLI 예제를 사용하려는 경우 최신 버전의 [Azure PowerShell](/powershell/azure/install-az-ps) 또는 [Azure CLI](/cli/azure/install-azure-cli)를 설치해야 합니다. 

> [!IMPORTANT]
> - 이 문서의 모든 샘플 스크립트는 Azure 리소스에 대한 관리 ID를 사용하는 VM에서 명령줄 클라이언트가 실행되고 있다고 가정합니다. Azure Portal에서 VM "연결" 기능을 사용하여 VM에 원격으로 연결합니다. VM에서 Azure 리소스에 대한 관리 ID 사용에 대한 자세한 내용은 [Azure Portal을 사용하여 VM에서 Azure 리소스에 대한 관리 ID 구성](qs-configure-portal-windows-vm.md) 또는 (PowerShell, CLI, 템플릿 또는 Azure SDK를 사용하는) 변형 문서 중 하나를 참조하세요. 
> - 리소스 액세스 중에 오류를 방지하려면 적절한 범위(VM 이상)에서 "읽기 권한자" 액세스 이상의 VM 관리 ID를 제공하여 VM에 Azure Resource Manager 작업을 허용해야 합니다. 자세한 내용은 [Azure Portal을 사용하여 리소스에 Azure 리소스 액세스를 위한 관리 ID 할당](howto-assign-access-portal.md)을 참조하세요.

## <a name="overview"></a>개요

Azure 리소스에 대한 관리 ID는 VM에서 [Azure 리소스에 대한 관리 ID를 사용하도록 설정할 때 만들어지는](overview.md) [서비스 주체 개체](../develop/developer-glossary.md#service-principal-object)를 제공합니다. 서비스 주체는 Azure 리소스에 액세스 권한을 부여하고 로그인 및 리소스 액세스를 위한 스크립트/명령줄에 의한 ID로 사용될 수 있습니다. 일반적으로 고유한 ID 하에서 보호된 리소스에 액세스하기 위해 스크립트 클라이언트는 다음을 수행해야 합니다.  

   - Azure AD에서 기밀/웹 클라이언트 애플리케이션으로 등록하고 동의합니다.
   - 앱의 자격 증명(스크립트에 포함됨)을 사용하여 해당 서비스 주체 하에 로그인합니다.

Azure 리소스에 대한 관리 ID를 사용하면 스크립트 클라이언트가 Azure 리소스 서비스 주체의 관리 ID에서 로그인할 수 있으므로 이러한 작업을 더 이상 수행할 필요가 없습니다. 

## <a name="azure-cli"></a>Azure CLI

다음 스크립트에서는 다음과 같은 방법을 설명합니다.

1. Azure 리소스 서비스 주체에 대한 VM의 관리 ID에서 Azure AD에 로그인  
2. Azure Resource Manager를 호출하고 VM의 서비스 주체 ID를 가져옵니다. CLI에서는 자동으로 토큰 획득/사용을 관리합니다. `<VM-NAME>`의 가상 머신 이름을 대체해야 합니다.  

   ```azurecli
   az login --identity
   
   $spID=$(az resource list -n <VM-NAME> --query [*].identity.principalId --out tsv)
   echo The managed identity for Azure resources service principal ID is $spID
   ```

## <a name="azure-powershell"></a>Azure PowerShell

다음 스크립트에서는 다음과 같은 방법을 설명합니다.

1. Azure 리소스 서비스 주체에 대한 VM의 관리 ID에서 Azure AD에 로그인  
2. Azure Resource Manager cmdlet을 호출하여 VM에 대한 정보를 가져옵니다. PowerShell에서는 자동으로 토큰 획득/사용을 관리합니다.  

   ```azurepowershell
   Add-AzAccount -identity

   # Call Azure Resource Manager to get the service principal ID for the VM's managed identity for Azure resources. 
   $vmInfoPs = Get-AzVM -ResourceGroupName <RESOURCE-GROUP> -Name <VM-NAME>
   $spID = $vmInfoPs.Identity.PrincipalId
   echo "The managed identity for Azure resources service principal ID is $spID"
   ```

## <a name="resource-ids-for-azure-services"></a>Azure 서비스의 리소스 ID

Azure AD를 지원하고 Azure 리소스의 관리 ID 및 해당하는 리소스 ID를 사용하여 테스트된 리소스의 목록은 [Azure AD 인증을 지원하는 Azure 서비스](services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication)를 참조하세요.

## <a name="error-handling-guidance"></a>오류 처리 지침 

다음과 같은 응답은 Azure 리소스의 VM 관리 ID가 올바르게 구성되지 않았음을 나타낼 수 있습니다.

- PowerShell: *Invoke-WebRequest: 원격 서버에 연결할 수 없습니다.*
- CLI: *MSI: 'HTTPConnectionPool(host='localhost', port=50342) 오류를 표시하여 `http://localhost:50342/oauth2/token`에서 토큰을 검색하지 못했습니다.* 

이러한 오류 중 하나가 발생하면 [Azure Portal](https://portal.azure.com)의 Azure VM으로 돌아가서 **ID** 페이지로 이동하여 **시스템 할당** 이 "예"로 설정되었는지 확인합니다.

## <a name="next-steps"></a>다음 단계

- Azure VM에서 Azure 리소스의 관리 ID를 사용하도록 설정하려면 [PowerShell을 사용하여 Azure VM에서 Azure 리소스에 대한 관리 ID 구성](qs-configure-powershell-windows-vm.md) 또는 [Azure CLI를 사용하여 Azure VM에서 Azure 리소스에 대한 관리 ID 구성](qs-configure-cli-windows-vm.md)을 참조하세요.
