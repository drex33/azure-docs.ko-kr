---
title: Azure 자격 증명을 사용하여 Power Apps 계정에 파트너 ID 연결
description: 이 문서에서는 Microsoft 파트너가 Azure 자격 증명을 사용하여 고객이 Microsoft Power Apps를 사용하도록 돕는 방법을 안내합니다.
author: bandersmsft
ms.reviewer: akangaw
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: conceptual
ms.date: 03/16/2021
ms.author: banders
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: c7bc61691f0035c9f1aa0361ae3439cfb1e1485e
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128604401"
---
# <a name="link-a-partner-id-to-your-power-apps-accounts"></a>Power Apps 계정에 파트너 ID 연결

이 문서에서는 Power Apps 서비스 공급자인 Microsoft 파트너가 Microsoft Power Apps 고객에게 자사 서비스를 연결할 수 있도록 도와줍니다. Microsoft 파트너는 고객을 위해 Power Apps 서비스를 관리, 구성 및 지원할 때 고객의 환경에 액세스할 수 있습니다. Azure 자격 증명과 PAL(파트너 관리자 링크)을 사용하여 서비스 제공에 사용되는 계정 자격 증명과 파트너 네트워크 ID를 연결할 수 있습니다.

Microsoft는 PAL을 통해 Power Apps 고객이 있는 파트너를 식별하고 인식할 수 있습니다. Microsoft는 계정 사용 권한(Power Apps 역할) 및 범위(테넌트, 리소스 그룹, 리소스)에 따라 파트너의 조직에 사용량을 귀속시킵니다.

## <a name="get-access-from-your-customer"></a>고객의 액세스 권한 가져오기

파트너 ID를 연결하려면 먼저 고객이 다음 옵션 중 하나를 사용하여 자신의 Power Apps 리소스에 대한 액세스 권한을 사용자에게 부여해야 합니다.

- **게스트 사용자** - 고객이 사용자를 게스트 사용자로 추가하고 Power Apps 역할을 할당할 수 있습니다. 자세한 내용은 [다른 디렉터리에서 게스트 사용자 추가](../../active-directory/external-identities/what-is-b2b.md)를 참조하세요.
- **디렉터리 계정** - 고객이 자신의 디렉터리에 사용자 계정을 만들고 Power Apps 역할을 할당할 수 있습니다.
- **서비스 주체** - 고객이 자신의 디렉터리에서 사용자 조직의 앱 또는 스크립트를 추가하고 Power Apps 역할을 할당할 수 있습니다. 앱 또는 스크립트의 ID를 서비스 주체라고 합니다.
- **위임된 관리자** - 사용자가 테넌트 내에서 작업할 수 있도록 고객이 리소스 그룹을 위임할 수 있습니다. 자세한 내용은 [파트너: 위임된 관리자](/power-platform/admin/for-partners-delegated-administrator)를 참조하세요.

## <a name="link-customer-to-a-partner-id"></a>고객을 파트너 ID에 연결

고객의 리소스에 액세스할 수 있는 경우 Azure Portal, PowerShell 또는 Azure CLI를 사용하여 MPN ID(Microsoft 파트너 네트워크 ID)를 사용자 ID 또는 서비스 주체에 연결합니다. 파트너 ID를 각 고객 테넌트에 연결합니다.

### <a name="use-the-azure-portal-to-link-to-a-new-partner-id"></a>Azure Portal을 사용하여 새 파트너 ID에 연결

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. Azure Portal에서 [파트너 ID에 연결](https://portal.azure.com/#blade/Microsoft_Azure_Billing/managementpartnerblade)로 이동합니다.
1. 조직의 [Microsoft 파트너 네트워크](https://partner.microsoft.com/) ID를 입력합니다. 파트너 센터 프로필에 표시  **된 연결 된 MPN ID**  를 사용 해야 합니다. 이를 일반적으로 [파트너 위치 계정 MPN ID](/partner-center/account-structure)라고 합니다.  
    :::image type="content" source="./media/link-partner-id-power-apps-accounts/link-partner-id.png" alt-text="파트너 ID 창 링크를 보여주는 스크린샷" lightbox="./media/link-partner-id-power-apps-accounts/link-partner-id.png" :::
1. 다른 고객에게 파트너 ID를 연결하려면 디렉터리를 전환합니다. **디렉터리 전환** 아래에서 해당 디렉터리를 선택합니다.  
    :::image type="content" source="./media/link-partner-id-power-apps-accounts/switch-directory.png" alt-text="디렉터리를 전환할 수 있는 디렉터리 + 구독 창을 보여주는 스크린샷" lightbox="./media/link-partner-id-power-apps-accounts/switch-directory.png" :::

### <a name="use-powershell-to-link-to-a-new-partner-id"></a>PowerShell을 사용하여 새 파트너 ID에 연결

[Az.ManagementPartner](https://www.powershellgallery.com/packages/Az.ManagementPartner/) Azure PowerShell 모듈을 설치합니다.

사용자 계정 또는 서비스 주체를 사용하여 고객의 테넌트에 로그인합니다. 자세한 내용은 [PowerShell로 로그인](/powershell/azure/authenticate-azureps)을 참조하세요.

```azurepowershell-interactive
Update-AzManagementPartner -PartnerId 12345
```

새 파트너 ID에 연결합니다. 파트너 ID는 조직의 [Microsoft 파트너 네트워크](https://partner.microsoft.com/) ID입니다. 파트너 프로필에 표시된 **연결된 MPN ID** 를 사용해야 합니다.

```azurepowershell-interactive
new-AzManagementPartner -PartnerId 12345
```

연결된 파트너 ID 가져오기

```azurepowershell-interactive
get-AzManagementPartner
```

연결된 파트너 ID 업데이트

```azurepowershell-interactive
Update-AzManagementPartner -PartnerId 12345
```

연결된 파트너 ID 삭제

```azurepowershell-interactive
remove-AzManagementPartner -PartnerId 12345
```

### <a name="use-the-azure-cli-to-link-to-a-new-partner-id"></a>Azure CLI를 사용하여 새 파트너 ID에 연결

먼저, Azure CLI 확장을 설치합니다.

```azurecli-interactive
az extension add --name managementpartner
```

사용자 계정 또는 서비스 주체를 사용하여 고객의 테넌트에 로그인합니다. 자세한 내용은 [Azure CLI로 로그인](/cli/azure/authenticate-azure-cli)을 참조하세요.

```azurecli-interactive
az login --tenant TenantName
```

새 파트너 ID에 연결합니다. 파트너 ID는 조직의 [Microsoft 파트너 네트워크](https://partner.microsoft.com/) ID입니다.

```azurecli-interactive
az managementpartner create --partner-id 12345
```

연결된 파트너 ID 가져오기

```azurecli-interactive
az managementpartner show
```

연결된 파트너 ID 업데이트

```azurecli-interactive
az managementpartner update --partner-id 12345
```

연결된 파트너 ID 삭제

```azurecli-interactive
az managementpartner delete --partner-id 12345
```

### <a name="next-steps"></a>다음 단계

- Power Apps 계정에 파트너 ID를 연결하는 방법에 대한 질문과 대답은 [Cost Management + Billing FAQ](../cost-management-billing-faq.yml)를 참조하세요.
- [Microsoft 파트너 커뮤니티](https://aka.ms/PALdiscussion)의 토론에 참여하여 업데이트를 받거나 의견을 보냅니다.
- 낮은 수준의 코드 애플리케이션 개발 고급 전문화를 위한 PAL 기반 Power Apps 연결에 대한 자세한 내용은 [낮은 수준의 코드 애플리케이션 개발 고급 전문화 FAQ](https://assetsprod.microsoft.com/mpn/faq-low-code-app-development-advanced-specialization.pdf)를 참조하세요.
