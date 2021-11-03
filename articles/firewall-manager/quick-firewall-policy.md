---
title: '빠른 시작: Azure Firewall 및 방화벽 정책 만들기 - Resource Manager 템플릿'
description: 이 빠른 시작에서는 Azure Firewall 및 방화벽 정책을 배포합니다.
services: firewall-manager
author: vhorne
ms.author: victorh
ms.date: 02/17/2021
ms.topic: quickstart
ms.service: firewall-manager
ms.custom: devx-track-azurepowershell, subject-armqs, mode-arm
ms.openlocfilehash: 4754b2202b5c36d664ac77e4f5626ac9c88f3681
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131057783"
---
# <a name="quickstart-create-an-azure-firewall-and-a-firewall-policy---arm-template"></a>빠른 시작: Azure Firewall 및 방화벽 정책 만들기 - ARM 템플릿

이 빠른 시작에서는 ARM 템플릿(Azure Resource Manager 템플릿)을 사용하여 Azure Firewall 및 방화벽 정책을 만듭니다. 방화벽 정책에는 `www.microsoft.com`에 대한 연결을 허용하는 애플리케이션 규칙과 **WindowsUpdate** FQDN 태그를 사용하여 Windows 업데이트에 대한 연결을 허용하는 규칙이 있습니다. 네트워크 규칙은 13.86.101.172에서 시간 서버에 대한 UDP 연결을 허용합니다.

또한 IP 그룹은 **원본** IP 주소를 정의하는 규칙에 사용됩니다.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Azure Firewall Manager에 대한 자세한 내용은 [Azure Firewall Manager란?](overview.md)을 참조하세요.

Azure Firewall에 대한 자세한 내용은 [Azure Firewall이란?](../firewall/overview.md)을 참조하세요.

IP 그룹에 대한 자세한 내용은 [Azure Firewall의 IP 그룹](../firewall/ip-groups.md)을 참조하세요.

환경이 필수 구성 요소를 충족하고 ARM 템플릿 사용에 익숙한 경우 **Azure에 배포** 단추를 선택합니다. 그러면 Azure Portal에서 템플릿이 열립니다.

[![Azure에 배포](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fquickstarts%2Fmicrosoft.network%2Fazurefirewall-create-with-firewallpolicy-apprule-netrule-ipgroups%2Fazuredeploy.json)

## <a name="prerequisites"></a>필수 구성 요소

- 활성 구독이 있는 Azure 계정. [체험 계정을 만듭니다](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="review-the-template"></a>템플릿 검토

이 템플릿은 시나리오를 지원하는 데 필요한 리소스와 함께 허브 가상 네트워크를 만듭니다.

이 빠른 시작에서 사용되는 템플릿은 [Azure 빠른 시작 템플릿](https://azure.microsoft.com/resources/templates/azurefirewall-create-with-firewallpolicy-apprule-netrule-ipgroups/)에서 나온 것입니다.

:::code language="json" source="~/quickstart-templates/quickstarts/microsoft.network/azurefirewall-create-with-firewallpolicy-apprule-netrule-ipgroups/azuredeploy.json":::

템플릿에는 여러 개의 Azure 리소스가 정의되어 있습니다.

- [**Microsoft.Network/ipGroups**](/azure/templates/microsoft.network/ipGroups)
- [**Microsoft.Network/firewallPolicies**](/azure/templates/microsoft.network/firewallPolicies)
- [**Microsoft.Network/firewallPolicies/ruleCollectionGroups**](/azure/templates/microsoft.network/firewallPolicies/ruleCollectionGroups)
- [**Microsoft.Network/azureFirewalls**](/azure/templates/microsoft.network/azureFirewalls)
- [**Microsoft.Network/virtualNetworks**](/azure/templates/microsoft.network/virtualnetworks)
- [**Microsoft.Network/publicIPAddresses**](/azure/templates/microsoft.network/publicipaddresses)

## <a name="deploy-the-template"></a>템플릿 배포

Azure에 ARM 템플릿을 배포합니다.

1. **Azure에 배포** 를 선택하여 Azure에 로그인하고 템플릿을 엽니다. 템플릿은 Azure Firewall, 가상 WAN 및 가상 허브, 네트워크 인프라 및 두 개의 가상 머신을 만듭니다.

   [![Azure에 배포](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fquickstarts%2Fmicrosoft.network%2Fazurefirewall-create-with-firewallpolicy-apprule-netrule-ipgroups%2Fazuredeploy.json)

2. 포털의 **규칙 및 Ipgroups를 사용하여 방화벽 및 FirewallPolicy 만들기** 페이지에서 다음 값을 입력하거나 선택합니다.
   - 구독: 기존 구독에서 선택합니다.
   - 리소스 그룹:  기존 리소스 그룹에서 선택하거나 **새로 만들기** 를 선택하고 **확인** 을 선택합니다.
   - 지역: 지역을 선택합니다.
   - 방화벽 이름: 방화벽의 이름을 입력합니다.

3. **검토 + 만들기** 를 선택한 다음, **만들기** 를 선택합니다. 배포를 완료하는 데 10분 이상 걸릴 수 있습니다.

## <a name="review-deployed-resources"></a>배포된 리소스 검토

배포가 완료되면 다음과 유사한 리소스가 표시됩니다.

:::image type="content" source="media/quick-firewall-policy/qs-deployed-resources.png" alt-text="배포된 리소스":::

## <a name="clean-up-resources"></a>리소스 정리

방화벽으로 만든 리소스가 더 이상 필요하지 않으면 리소스 그룹을 삭제합니다. 이렇게 하면 방화벽과 모든 관련 리소스가 제거됩니다.

리소스 그룹을 삭제하려면 `Remove-AzResourceGroup` cmdlet을 호출합니다.

```azurepowershell-interactive
Remove-AzResourceGroup -Name "<your resource group name>"
```

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [Azure Firewall Manager 정책 개요](policy-overview.md)
