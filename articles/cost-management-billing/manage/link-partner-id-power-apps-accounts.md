---
title: Azure 자격 증명을 사용 하 여 Power Platform 및 Dynamics Customer Insights 계정에 파트너 ID 연결
description: 이 문서는 microsoft 파트너가 Azure 자격 증명을 사용 하 여 고객에 게 microsoft Power Apps, Power Automate, Power BI 및 Dynamics Customer Insights에 대 한 서비스를 제공 하는 데 도움이 됩니다.
author: bandersmsft
ms.reviewer: tpalmer
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: conceptual
ms.date: 12/01/2021
ms.author: banders
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 29715c5d244da6bef809d7a31ea735dc05bcb6e9
ms.sourcegitcommit: 5b25f76d0fd0ffb6784a2afab808fa55b3eac07b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/03/2021
ms.locfileid: "133518244"
---
# <a name="link-a-partner-id-to-your-power-platform-and-dynamics-customer-insights-accounts"></a>전원 플랫폼 및 Dynamics Customer Insights 계정에 파트너 ID 연결

microsoft 파트너는 Power Platform 및 dynamics Customer Insights 서비스 공급자 인 사용자가 microsoft Power Apps, Power Automate, Power BI 및 dynamics Customer Insights에서 서비스를 고객에 게 연결할 수 있습니다. 사용자는 Microsoft 파트너, 고객을 위한 Power Platform 및 Customer Insights 리소스를 관리, 구성 및 지원할 때 고객의 환경에 액세스할 수 있습니다. Azure 자격 증명과 PAL(파트너 관리자 링크)을 사용하여 서비스 제공에 사용되는 계정 자격 증명과 파트너 네트워크 ID를 연결할 수 있습니다.

PAL을 통해 Microsoft는 전원 플랫폼과 Customer Insights 고객을 보유 하 고 있는 파트너를 식별 하 고 인식할 수 있습니다. Microsoft 특성은 계정의 사용 권한 (사용자 역할) 및 범위 (테 넌 트, 리소스 등)를 기반으로 파트너 조직에 사용 됩니다. 이 특성은 [Microsoft 낮은 코드 고급 특수화](https://partner.microsoft.com/membership/advanced-specialization#tab-content-2)및 [파트너 성과급](https://partner.microsoft.com/asset/collection/microsoft-commerce-incentive-resources#/)과 같은 고급 특수화에 사용할 수 있습니다. 

다음 섹션에서는이 방법에 대해 자세히 설명 합니다.
- 고객의 액세스 권한 가져오기
- 파트너 ID에 액세스 계정 연결
- 제품 리소스에 대 한 액세스 계정 특성

일반적으로 최종 단계는 파트너 사용자가 리소스를 만들고, 편집 하 고, 업데이트 하는 것 이므로 일반적으로 자동으로 발생 합니다. 파트너가 Microsoft Power Apps, Power Automate, Power BI 및 Dynamics Customer Insights 관련 된 작업에 대 한 적절 한 크레딧을 받을 수 있도록 하는 것은 중요 한 단계입니다.

## <a name="get-access-from-your-customer"></a>고객의 액세스 권한 가져오기

파트너 ID를 연결 하기 전에 고객은 자신의 전원 플랫폼 또는 Customer Insights 리소스에 대 한 액세스 권한을 부여 해야 합니다. 다음 옵션 중 하나를 사용 합니다.

- **게스트 사용자** -고객이 게스트 사용자로 추가 하 고 작업 중인 제품에 대 한 액세스를 제공할 수 있습니다. 자세한 내용은 [다른 디렉터리에서 게스트 사용자 추가](../../active-directory/external-identities/what-is-b2b.md)를 참조하세요.
- **디렉터리 계정** -고객은 자신의 디렉터리에서 사용자 계정을 만들고 작업 중인 제품에 대 한 액세스를 제공할 수 있습니다.
- **서비스 사용자** -고객이 자신의 디렉터리에 있는 조직에서 앱 또는 스크립트를 추가 하 고 작업 중인 제품에 대 한 액세스를 제공할 수 있습니다. 앱 또는 스크립트의 ID를 서비스 주체라고 합니다.
- **위임 된 관리자** -Power Platform의 경우 고객은 테 넌 트 내에서 사용자가 작업할 수 있도록 리소스 그룹을 위임할 수 있습니다. 자세한 내용은 [파트너: 위임된 관리자](/power-platform/admin/for-partners-delegated-administrator)를 참조하세요.

## <a name="link-your-access-account-to-your-partner-id"></a>파트너 ID에 액세스 계정 연결

고객의 리소스에 액세스할 수 있는 경우 Azure Portal, PowerShell 또는 Azure CLI를 사용하여 MPN ID(Microsoft 파트너 네트워크 ID)를 사용자 ID 또는 서비스 주체에 연결합니다. 파트너 ID를 각 고객 테넌트에 연결합니다.

### <a name="use-the-azure-portal-to-link-to-a-new-partner-id"></a>Azure Portal을 사용하여 새 파트너 ID에 연결

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. Azure Portal에서 [파트너 ID에 연결](https://portal.azure.com/#blade/Microsoft_Azure_Billing/managementpartnerblade)로 이동합니다.
1. 조직의 [Microsoft 파트너 네트워크](https://partner.microsoft.com/) ID를 입력합니다. 파트너 센터 프로필에 표시  **된 연결 된 MPN ID**  를 사용 해야 합니다. 일반적으로 [파트너 위치 계정 MPN ID](/partner-center/account-structure)라고 합니다.  
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

## <a name="attribute-your-access-account-to-the-product-resource"></a>제품 리소스에 대 한 액세스 계정 특성
PAL (파트너 관리자 링크)을 통해 연결 된 파트너 사용자는 해당 특정 리소스의 사용량을 계산 하기 위해 전원 플랫폼 또는 Dynamics Customer Insights에 대 한 리소스의 특성을 지정 해야 합니다. 대부분의 경우 파트너 사용자가 리소스를 만들고, 편집 하 고, 업데이트 하는 중 이므로 자동으로 발생 합니다. 아래 논리 외에 PAL 링크가 사용 되는 특정 프로그램 (예: [Microsoft 낮은 코드 고급 특수화](https://partner.microsoft.com/membership/advanced-specialization#tab-content-2) 또는 파트너 성과급)에는 프로덕션 환경에 있어야 하 고 유료 사용량과 관련 된 리소스와 같은 다른 요구 사항이 있을 수 있습니다.

| 제품           | 기본 메트릭   | 리소스 | 특성 사용 사용자 논리                                                                                                                                                                             |
|-------------------|------------------|----------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Power Apps | MAU (월간 활성 사용자) | 애플리케이션 |사용자는 응용 프로그램의 소유자/공동 소유자 여야 합니다. 자세한 내용은 [조직과 캔버스 앱 공유](/powerapps/maker/canvas-apps/share-app)를 참조 하세요. 단일 응용 프로그램에 매핑되는 여러 파트너의 경우 사용자의 활동을 검토 하 여 ' 최신 ' 파트너를 선택 합니다. |
| Power Automate | MAU (월간 활성 사용자) | 흐름 | 사용자는 흐름의 작성자 여야 합니다. 여러 파트너에 대 한 논리가 없는 생성자가 하나만 있을 수 있습니다.  |
| Power BI            | MAU (월간 활성 사용자)   | 데이터 세트 | 사용자는 데이터 집합의 게시자 여야 합니다. 자세한 내용은 [Power BI Desktop에서 데이터 세트 및 보고서 게시](/power-bi/create-reports/desktop-upload-desktop-files)를 참조하세요. 단일 데이터 집합에 매핑되는 여러 파트너의 경우 사용자의 활동을 검토 하 여 ' 최신 ' 파트너를 선택 합니다. |
| Customer Insights | 통합 프로필 | 인스턴스 | 인스턴스의 모든 활성 사용자는 특성 사용 사용자로 처리 됩니다. 단일 인스턴스에 매핑되는 여러 파트너의 경우 사용자의 활동을 검토 하 여 ' 최신 ' 파트너를 선택 합니다. |


### <a name="next-steps"></a>다음 단계

- Power Apps 계정에 파트너 ID를 연결하는 방법에 대한 질문과 대답은 [Cost Management + Billing FAQ](../cost-management-billing-faq.yml)를 참조하세요.
- [Microsoft 파트너 커뮤니티](https://aka.ms/PALdiscussion)의 토론에 참여하여 업데이트를 받거나 의견을 보냅니다.
- 낮은 수준의 코드 애플리케이션 개발 고급 전문화를 위한 PAL 기반 Power Apps 연결에 대한 자세한 내용은 [낮은 수준의 코드 애플리케이션 개발 고급 전문화 FAQ](https://assetsprod.microsoft.com/mpn/faq-low-code-app-development-advanced-specialization.pdf)를 참조하세요.
