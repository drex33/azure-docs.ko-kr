---
title: 키 자격 증명 모음에 비밀 저장
description: Azure Key Vault에 비밀을 저장하고, VM, 수식 또는 환경을 만드는 동안 사용하는 방법을 알아봅니다.
ms.topic: how-to
ms.date: 06/26/2020
ms.openlocfilehash: 2aaa177c895b57a07ed94de48081de69beedcda8
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128596026"
---
# <a name="store-secrets-in-a-key-vault-in-azure-devtest-labs"></a>Azure DevTest Labs의 키 자격 증명 모음에서 비밀 저장
Azure DevTest Labs를 사용하는 경우 복잡한 비밀을 입력해야 할 수 있습니다. 예를 들어 Windows VM의 암호, Linux VM의 공용 SSH 키 또는 아티팩트를 통해 Git 리포지토리를 복제하기 위한 개인용 액세스 토큰 등입니다. 비밀은 일반적으로 길며 임의 문자를 포함합니다. 따라서 특히 여러 번 동일한 비밀을 사용하는 경우 비밀 입력은 까다롭고 불편할 수 있습니다.

이 문제를 해결하고 비밀을 안전한 장소에 보관하기 위해 DevTest Labs가 [Azure 키 자격 증명 모음](../key-vault/general/overview.md)에서 비밀 저장을 지원합니다. 사용자가 처음으로 비밀을 저장하는 경우 DevTest Labs 서비스는 랩을 포함하고 키 자격 증명 모음에 비밀을 저장하는 동일한 리소스 그룹에서 키 자격 증명 모음을 자동으로 만듭니다. DevTest Labs에서는 각 사용자에 대한 별도 키 자격 증명 모음을 만듭니다. 

랩 사용자는 먼저 랩 가상 머신을 만들어야 키 자격 증명 모음에 비밀을 만들 수 있습니다. 랩 사용자가 키 자격 증명 모음에 비밀을 만들어 저장하도록 허용하려면 먼저 DevTest Lab 서비스가 랩 사용자를 유효한 사용자 문서와 연결해야 하기 때문입니다. 


## <a name="save-a-secret-in-azure-key-vault"></a>Azure Key Vault에 비밀 저장
Azure Key Vault에 비밀을 저장하려면 다음 단계를 수행합니다.

1. 왼쪽 메뉴에서 **내 비밀** 을 선택합니다.
2. 비밀에 **이름** 을 입력하세요. VM, 수식 또는 환경을 만들 때 드롭다운 목록에 이 이름이 표시됩니다. 
3. 비밀을 **값** 으로 입력합니다.

    ![비밀 저장](media/devtest-lab-store-secrets-in-key-vault/store-secret.png)

## <a name="use-a-secret-from-azure-key-vault"></a>Azure Key Vault에서 비밀 사용
VM, 수식 또는 환경을 만들기 위해 비밀을 입력해야 하는 경우 수동으로 비밀을 입력하거나 키 자격 증명 모음에서 저장된 비밀을 선택할 수 있습니다. 키 자격 증명 모음에 저장된 비밀을 사용하려면 다음 작업을 수행합니다.

1. **저장된 비밀 사용** 을 선택합니다. 
2. **비밀 선택** 에 대한 드롭다운 목록에서 비밀을 선택합니다. 

    ![VM에서 비밀 사용](media/devtest-lab-store-secrets-in-key-vault/secret-store-pick-a-secret.png)

## <a name="use-a-secret-in-an-azure-resource-manager-template"></a>Azure Resource Manager 템플릿에서 비밀 사용
다음 예제에 표시된 것처럼 VM을 만드는 데 사용되는 Azure Resource Manager 템플릿에서 비밀 이름을 지정할 수 있습니다.

![수식이나 환경에서 비밀 사용](media/devtest-lab-store-secrets-in-key-vault/secret-store-arm-template.png)

## <a name="next-steps"></a>다음 단계

- [비밀을 사용하여 VM 만들기](devtest-lab-add-vm.md) 
- [비밀을 사용하여 수식 만들기](devtest-lab-manage-formulas.md)
- [비밀을 사용하여 환경 만들기](devtest-lab-create-environment-from-arm.md)
