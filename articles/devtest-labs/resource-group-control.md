---
title: DevTest Labs에서 Azure VM에 대한 리소스 그룹 지정
description: Azure DevTest Labs의 랩에서 VM에 대한 리소스 그룹을 지정하는 방법을 알아봅니다.
ms.topic: how-to
ms.date: 10/18/2021
ms.openlocfilehash: baeab2c54ae594cf9ecb70ae8c4ec7dd2b66588f
ms.sourcegitcommit: 92889674b93087ab7d573622e9587d0937233aa2
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/19/2021
ms.locfileid: "130178902"
---
# <a name="specify-a-resource-group-for-lab-virtual-machines-in-azure-devtest-labs"></a>Azure DevTest Labs에서 랩 가상 머신에 대한 리소스 그룹 지정

기본적으로 Azure DevTest Labs 새 가상 머신을 만들 때마다 새 리소스 그룹을 만듭니다. 랩 소유자는 랩 가상 머신을 특정 리소스 그룹에 만들도록 구성할 수 있습니다. 이 기능은 다음 시나리오에서 도움이 됩니다.

- 구독의 랩에서 더 적은 리소스 그룹을 만들도록 합니다.
- 랩이 구성된 고정 리소스 그룹 세트 내에서 작동하도록 합니다.
- Azure 구독 내에서 리소스 그룹을 만드는 데 필요한 승인 및 제한을 해결합니다.
- 단일 리소스 그룹 내의 모든 랩 리소스를 결합하여 해당 리소스를 추적하고 리소스 그룹 수준에서 리소스를 관리하는 [정책을](../governance/policy/overview.md) 적용하는 과정을 간소화합니다.

이 기능을 사용하면 스크립트로 모든 랩 VM에 사용되는 새 리소스 그룹 또는 기존 리소스 그룹을 Azure 구독 내에 지정할 수 있습니다. 현재 Azure DevTest Labs는 API를 통해 이 기능을 지원합니다.

> [!NOTE]
> DevTest Labs에서 랩을 만드는 경우 모든 구독 한도가 적용됩니다. 랩을 구독의 다른 리소스로 간주합니다. 리소스 그룹의 경우 한도는 [구독당 980개 리소스 그룹](../azure-resource-manager/management/azure-subscription-service-limits.md#subscription-limits)입니다. 

## <a name="use-azure-portal"></a>Azure Portal 사용
다음 단계에 따라 랩에서 만든 모든 VM에 사용되는 리소스 그룹을 지정합니다. 

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
2. 왼쪽 탐색 메뉴에서 **모든 서비스** 를 선택합니다. 
3. 목록에서 **DevTest Labs** 를 선택합니다.
4. 랩 목록에서 **랩** 을 선택합니다.  
5. 왼쪽 메뉴의 **설정** 섹션에서 **구성 및 정책** 을 선택합니다. 
6. 왼쪽 메뉴에서 **랩 설정** 을 선택합니다. 
7. **한 리소스 그룹의 모든 가상 머신** 을 선택합니다. 
8. 드롭다운 목록에서 기존 리소스 그룹을 선택하거나, **새로 만들기** 를 선택하고 리소스 그룹의 **이름** 을 입력하고 **확인** 을 선택합니다. 

    ![모든 랩 VM에 사용되는 리소스 그룹 선택](./media/resource-group-control/select-resource-group.png)

## <a name="use-powershell"></a>PowerShell 사용 
다음 예제에서는 PowerShell 스크립트를 사용하여 새 리소스 그룹에 모든 랩 가상 머신을 만드는 방법을 보여 줍니다.

```powershell
[CmdletBinding()]
Param(
    $subId,
    $labRg,
    $labName,
    $vmRg
)

az login | out-null

az account set --subscription $subId | out-null

$rgId = "/subscriptions/"+$subId+"/resourceGroups/"+$vmRg

"Updating lab '$labName' with vm rg '$rgId'..."

az resource update -g $labRg -n $labName --resource-type "Microsoft.DevTestLab/labs" --api-version 2018-10-15-preview --set properties.vmCreationResourceGroupId=$rgId

"Done. New virtual machines will now be created in the resource group '$vmRg'."
```

다음 명령을 사용하여 스크립트를 호출합니다. ResourceGroup.ps1은 이전 스크립트가 포함된 파일입니다.

```powershell
.\ResourceGroup.ps1 -subId <subscriptionID> -labRg <labRGNAme> -labName <LanName> -vmRg <RGName> 
```

## <a name="use-an-azure-resource-manager-template"></a>Azure Resource Manager 템플릿 사용
Azure Resource Manager 템플릿을 사용하여 랩을 만드는 경우 다음 예제와 같이 템플릿의 랩 속성 섹션에 있는 **vmCreationResourceGroupId** 속성을 사용합니다.

```json
        {
            "type": "microsoft.devtestlab/labs",
            "name": "[parameters('lab_name')]",
            "apiVersion": "2018-10-15-preview",
            "location": "eastus",
            "tags": {},
            "scale": null,
            "properties": {
                "vmCreationResourceGroupId": "/subscriptions/<SubscriptionID>/resourcegroups/<ResourceGroupName>",
                "labStorageType": "Premium",
                "premiumDataDisks": "Disabled",
                "provisioningState": "Succeeded",
                "uniqueIdentifier": "000000000f-0000-0000-0000-00000000000000"
            },
            "dependsOn": []
        },
```


## <a name="api-to-configure-a-resource-group-for-lab-vms"></a>랩 VM에 사용되는 리소스 그룹을 구성하는 API
랩 소유자가 이 API를 사용하는 경우 다음과 같은 옵션을 사용할 수 있습니다.

- 모든 가상 머신에 사용되는 **랩 리소스 그룹** 을 선택합니다.
- 모든 가상 머신에 사용되는 랩 리소스 그룹 이외의 **기존 리소스 그룹** 을 선택합니다.
- 모든 가상 머신에 사용되는 **새 리소스 그룹** 이름을 입력합니다.
- 기존 동작을 계속 사용합니다. 이 경우 랩의 VM마다 리소스 그룹이 생성됩니다.
 
이 설정은 랩에 생성된 새 가상 머신에 적용됩니다. 자체 리소스 그룹에 생성된 랩의 이전 VM은 영향을 받지 않고 유지됩니다. 랩에 생성된 환경은 자체 리소스 그룹에 계속 유지됩니다.

이 API를 사용하는 방법:
- API 버전 **2018-10-15-preview** 를 사용합니다.
- 새 리소스 그룹을 지정하는 경우 구독의 **리소스 그룹에 대한 쓰기 권한** 이 있는지 확인하세요. 쓰기 권한이 없는 경우 지정된 리소스 그룹에 새 가상 머신을 만들면 오류가 발생합니다.
- 이 API를 사용하는 동안 **전체 리소스 그룹 ID** 를 전달합니다. 예: `/subscriptions/<SubscriptionID>/resourceGroups/<ResourceGroupName>` 리소스 그룹이 랩과 동일한 구독에 있는지 확인합니다. 


## <a name="next-steps"></a>다음 단계
다음 문서를 참조하세요. 

- [랩에 대한 정책 설정](devtest-lab-set-lab-policy.md)
- [자주 묻는 질문](devtest-lab-faq.yml)
