---
title: 키 자격 증명 모음에 비밀 저장
description: Azure Key Vault에 비밀을 저장하고, VM, 수식 또는 환경을 만드는 동안 사용하는 방법을 알아봅니다.
ms.topic: how-to
ms.date: 06/26/2020
ms.openlocfilehash: 2c1849b6bf2ab5876fcbe0960df9bb8a7004bb78
ms.sourcegitcommit: e1037fa0082931f3f0039b9a2761861b632e986d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/12/2021
ms.locfileid: "132398101"
---
# <a name="store-secrets-in-a-key-vault-in-azure-devtest-labs"></a>Azure DevTest Labs의 키 자격 증명 모음에서 비밀 저장
Azure DevTest Labs 사용할 때 복잡한 비밀을 입력해야 할 수도 있습니다. 비밀에는 Windows VM에 대한 암호, Linux VM에 대한 공용 SSH 키 또는 아티팩트를 통해 Git 리포지블리를 복제하기 위한 개인용 액세스 토큰이 포함됩니다. 비밀은 종종 길며 임의의 문자를 갖습니다. 특히 동일한 비밀을 여러 번 사용하는 경우 입력이 까다롭고 불편할 수 있습니다.

이 문제를 해결하고 비밀을 안전한 장소에 유지하기 위해 DevTest Labs는 [Azure Key Vault](../key-vault/general/overview.md)에 비밀 저장을 지원합니다. 사용자가 처음으로 비밀을 저장하면 DevTest Labs는 랩과 동일한 리소스 그룹에 키 자격 증명 모음을 자동으로 만들고 비밀을 저장합니다. DevTest Labs에서는 각 사용자에 대한 별도 키 자격 증명 모음을 만듭니다. 

랩 사용자는 키 자격 증명 모음에 비밀을 만들려면 랩 가상 머신을 만들어야 합니다. 이 요구 사항은 DevTest Labs가 랩 사용자를 유효한 사용자 문서와 연결해야 하기 때문입니다. 그런 다음, DevTest Labs를 사용하면 사용자가 자신의 키 자격 증명 모음에 비밀을 만들고 저장할 수 있습니다.


## <a name="save-a-secret-in-azure-key-vault"></a>Azure Key Vault에 비밀 저장
Azure Key Vault에 비밀을 저장하려면 다음 단계를 수행합니다.

1. 왼쪽 메뉴에서 **내 비밀** 을 선택합니다.
2. 비밀에 **이름** 을 입력하세요. VM, 수식 또는 환경을 만들 때 드롭다운 목록에 이 이름이 표시됩니다. 
3. 비밀을 **값** 으로 입력합니다.

    ![비밀 저장을 보여 주는 스크린샷](media/devtest-lab-store-secrets-in-key-vault/store-secret.png)

## <a name="use-a-secret-from-azure-key-vault"></a>Azure Key Vault에서 비밀 사용
VM, 수식 또는 환경을 만드는 비밀을 입력하면 비밀을 수동으로 입력하거나 키 자격 증명 모음에서 저장된 비밀을 선택할 수 있습니다. 키 자격 증명 모음에 저장된 비밀을 사용하려면 다음 작업을 수행합니다.

1. **저장된 비밀 사용** 을 선택합니다. 
2. **비밀 선택** 에 대한 드롭다운 목록에서 비밀을 선택합니다. 

    ![VM을 만들 때 비밀을 사용하는 것을 보여 주는 스크린샷](media/devtest-lab-store-secrets-in-key-vault/secret-store-pick-a-secret.png)

## <a name="use-a-secret-in-an-azure-resource-manager-template"></a>Azure Resource Manager 템플릿에서 비밀 사용
다음 예제에 표시된 것처럼 VM을 만드는 데 사용되는 Azure Resource Manager 템플릿에서 비밀 이름을 지정할 수 있습니다.

![수식 또는 환경에서 비밀을 사용하는 것을 보여 주는 스크린샷.](media/devtest-lab-store-secrets-in-key-vault/secret-store-arm-template.png)

## <a name="next-steps"></a>다음 단계

- [비밀을 사용하여 VM 만들기](devtest-lab-add-vm.md) 
- [비밀을 사용하여 수식 만들기](devtest-lab-manage-formulas.md)
- [비밀을 사용하여 환경 만들기](devtest-lab-create-environment-from-arm.md)
