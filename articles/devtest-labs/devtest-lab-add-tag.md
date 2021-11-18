---
title: 랩에 태그 추가
description: Azure DevTest Labs에서 사용자 지정 태그를 만들고 태그를 사용하여 리소스를 분류하는 방법을 알아봅니다. 구독에서 해당 태그가 있는 모든 리소스를 확인할 수 있습니다.
ms.topic: how-to
ms.date: 06/26/2020
ms.openlocfilehash: feb2f833d6a028271060a152a301d4323571a865
ms.sourcegitcommit: 1244a72dbec39ac8cf16bb1799d8c46bde749d47
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/18/2021
ms.locfileid: "132758968"
---
# <a name="add-tags-to-a-lab-in-azure-devtest-labs"></a>Azure DevTest Labs에서 랩에 태그 추가

사용자 지정 태그를 만들어 DevTest Labs 리소스에 적용하면 리소스를 논리적으로 분류할 수 있습니다. 그러면 나중에 구독에서 해당 태그가 있는 모든 리소스를 빠르고 쉽게 확인할 수 있습니다. 태그는 청구 또는 관리를 위해 리소스를 구성해야 하는 경우 유용합니다.

태그 사용이 지원되는 리소스는 다음과 같습니다.

* 컴퓨팅 VM
* NIC
* IP 주소
* 부하 분산 장치
* Storage 계정
* 관리 디스크

[랩 만들기](devtest-lab-create-lab.md)를 수행할 때 태그를 적용한 다음 나중에 태그 블레이드의 구성 및 설정을 통해 태그를 관리할 수 있습니다.

모든 태그는 **이름**/**값** 쌍으로 구성됩니다. 예를 들어 이름이 *costcenter* 이고 값이 *34543* 인 태그를 만들 수 있습니다. 이러한 태그를 사용하면 나중에 조직의 해당 특정 영역에 대금을 청구할 수 있는 랩 리소스를 파악하는 데 도움이 될 수 있습니다. 구독을 구성하려는 방법에 적합한 이름과 값을 선택해야 합니다.

## <a name="steps-to-manage-tags-in-an-existing-lab"></a>기존 랩에서 태그를 관리하는 단계

1. [Azure Portal](https://go.microsoft.com/fwlink/p/?LinkID=525040)에 로그인합니다.
1. 필요한 경우 **모든 서비스** 를 선택하고 목록에서 **DevTest Labs** 를 선택합니다. 대시보드의 **모든 리소스** 아래에 랩이 이미 표시되어 있을 수 있습니다.
1. 랩 목록에서 태그를 추가하거나 관리하려는 랩을 선택합니다.
1. 랩의 **개요** 영역에서 **구성 및 정책** 을 선택합니다.

    ![구성 및 정책 단추](./media/devtest-lab-add-tag/devtestlab-config-and-policies.png)

1. 왼쪽의 **일반** 아래에서 **태그를** 선택합니다.
1. 이 랩에 대해 새 태그를 만들려면 **이름**/**값** 쌍을 입력하고 **저장** 을 선택합니다. 목록에서 기존 태그를 선택하여 해당 태그와 연결된 리소스를 확인하거나 관리할 수도 있습니다.

    ![태그 관리](./media/devtest-lab-add-tag/devtestlab-manage-tags.png)

> [!NOTE]
> 랩 수준에서 만든 태그는 구독에서 랩이 작동하는 모든 청구 가능 리소스를 통해 흐릅니다. 예를 들어 랩 수준 태그는 랩 VM의 기본 컴퓨팅 VM으로 흐릅니다. 비용 관리 컨텍스트에서 태그를 사용할 수 있습니다. 랩 수준 태그는 비용 관리에 대한 태그 필터에 표시됩니다.

## <a name="understanding-limitations-to-tags"></a>태그의 제한 이해

다음 제한 사항이 태그에 적용됩니다.

* 각 리소스 또는 리소스 그룹에는 최대 15개의 태그 이름/값 쌍이 포함될 수 있습니다. 이 제한은 리소스 그룹 또는 리소스에 직접 적용되는 태그에만 적용됩니다. 리소스 그룹은 각각 15개의 태그 이름/값 쌍이 있는 여러 리소스를 포함할 수 있습니다.
* 태그 이름은 512자로 제한되며 태그 값은 256자로 제한됩니다. 스토리지 계정에서 태그 이름은 128자로 제한되며 태그 값은 256자로 제한됩니다.
* 태그는 해당 리소스 그룹의 리소스에 의해 상속되지 않은 리소스 그룹에 적용됩니다.

PowerShell 또는 Azure CLI를 사용하여 태그를 관리하는 방법을 비롯하여 Azure에서 태그를 사용하는 방법과 관련된 보다 자세한 내용은 [태그를 사용하여 Azure 리소스 구성](../azure-resource-manager/management/tag-resources.md)에 나와 있습니다.

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="next-steps"></a>다음 단계
* 사용자 지정된 정책을 사용하여 구독 전체에 제한 사항 및 규칙을 적용할 수 있습니다. 정의한 정책을 사용하려면 모든 리소스에 특정 태그 값이 있어야 할 수도 있습니다. 자세한 내용은 [정책 및 일정 설정](devtest-lab-set-lab-policy.md)을 참조하세요.
* [DevTest Labs Azure Resource Manager 빠른 시작 템플릿 갤러리](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/QuickStartTemplates)를 탐색합니다.
