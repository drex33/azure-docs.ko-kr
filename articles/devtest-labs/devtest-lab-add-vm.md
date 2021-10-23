---
title: 랩에 가상 머신 만들기 및 추가
description: Azure portal을 사용하여 Azure DevTest Labs에서 랩에 가상 머신을 추가하는 방법을 알아봅니다. 사용자 지정 이미지 또는 수식 중 하나를 기준으로 선택할 수 있습니다.
ms.topic: how-to
ms.date: 10/20/2021
ms.openlocfilehash: 326d8923b27abff2ee480f6b981392a311be4f30
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130235291"
---
# <a name="create-and-add-virtual-machines-to-a-lab-in-azure-devtest-labs"></a>Azure DevTest Labs 랩에 가상 머신 만들기 및 추가

이 문서에서는 Azure Portal 사용하여 기존 DevTest Labs의 랩에 Azure VM(가상 머신)을 만들고 추가하는 방법을 안내합니다.

## <a name="create-and-add-virtual-machines"></a>가상 머신 만들기 및 추가

1. [Azure Portal](https://portal.azure.com/)에 로그인합니다.

1. **DevTest Labs에서 랩으로** 이동합니다.

1. **개요** 페이지에서 + **추가를** 선택합니다.

   :::image type="content" source="./media/devtest-lab-add-vm/portal-lab-add-vm.png" alt-text="추가 단추를 보여주는 랩 개요 페이지입니다.":::

1. **기본 선택** 페이지에서 VM에 대한 Marketplace 이미지를 선택합니다. 이 가이드에서는 **Windows 11 Pro** 사용합니다. 다른 이미지를 사용하는 경우 특정 옵션이 다를 수 있습니다.

1. 기본 **사항 설정** 탭에서 다음 정보를 제공합니다.

    |속성 |설명 |
    |---|---|
    |가상 &nbsp; 머신 &nbsp; 이름| 텍스트 상자는 자동으로 생성되는 고유한 이름으로 미리 채워져 있습니다. 이름은 이메일 주소 내의 사용자 이름 뒤에 고유한 3자리 숫자에 해당합니다. 그대로 두거나 선택한 고유한 이름을 입력합니다.|
    |사용자 이름| 텍스트 상자는 자동으로 생성되는 고유한 이름으로 미리 채워져 있습니다. 이 이름은 이메일 주소 내의 사용자 이름에 해당합니다. 그대로 두거나 선택한 이름을 입력합니다. 사용자에게 가상 머신에 대한 **관리자** 권한이 부여됩니다.|
    |저장된 비밀 사용| 이 워크스루에서는 확인란을 선택하지 않은 상태로 둡니다. 먼저 Azure Key Vault 비밀을 저장한 다음 여기에서 사용할 수 있습니다. 자세한 내용은 [키 자격 증명 모음에 비밀 저장](devtest-lab-store-secrets-in-key-vault.md)을 참조하세요. 저장된 비밀을 사용하려면 확인란을 선택한 다음 비밀 **드롭다운** 목록에서 비밀을 선택합니다.|
    |암호|8자에서 123자 사이의 암호를 입력합니다.|
    |기본 암호로 저장| 확인란을 선택하여 랩과 연결된 Azure Key Vault 암호를 저장합니다.|
    |가상 머신 크기| 기본값을 유지하거나 **크기 변경을** 선택하여 다른 물리적 구성 요소를 선택합니다. 이 워크스루에서는 **Standard_B2** 사용합니다.|
    |OS 디스크 유형|기본값을 유지하거나 드롭다운 목록에서 다른 옵션을 선택합니다.|
    |Artifacts| **아티팩트 추가 또는 제거** 를 선택합니다. 기본 이미지에 추가하려는 아티팩트를 선택하고 구성합니다. 각 랩에는 공용 DevTest Labs 아티팩트 리포지토리의 아티팩트 및 직접 만들고 사용자 고유의 아티팩트 리포지토리에 추가한 아티팩트가 포함됩니다. 확장된 지침은 [설치 중에 아티팩트 추가를](#add-artifacts-during-installation)참조하세요.|

   :::image type="content" source="./media/devtest-lab-add-vm/portal-lab-vm-basic-settings.png" alt-text="가상 머신 기본 설정 페이지.":::

1. 고급 **설정** 탭을 선택하고 다음 정보를 제공합니다.

    |속성 |설명 |
    |---|---|
    |가상 네트워크| 그대로 두거나 드롭다운 목록에서 다른 네트워크를 선택합니다.|
    |서브넷 &nbsp; 선택기| 그대로 두거나 드롭다운 목록에서 다른 서브넷을 선택합니다.|
    |IP 주소| 이 워크스루의 경우 기본값인 **공유** 를 그대로 둡니다.|
    |만료 날짜| 만료 날짜가 없도록 그대로 두거나 달력 아이콘을 선택하여 만료 날짜를 설정합니다.|
    |이 컴퓨터를 클레임할 수 있도록 만들기| 랩 사용자가 VM을 클레임할 수 있도록 하려면 **예를** 선택합니다. 컴퓨터를 클레임 가능으로 표시하면 생성 시 소유권이 할당되지 않습니다. 이 워크스루에서는 **예를** 선택합니다.|
    |인스턴스 수| 이 워크스루에서는 **2를** 입력합니다. 만들 가상 머신 인스턴스의 수입니다.|
    |Automation | 선택 사항입니다. ARM **템플릿 보기를** 선택하면 새 페이지에서 템플릿이 열립니다. 템플릿을 복사하고 저장하여 나중에 동일한 가상 머신을 만들 수 있습니다. 저장한 Azure Resource Manager 템플릿은 [Azure PowerShell로 새 VM을 배포](../azure-resource-manager/templates/overview.md)하는 데 사용할 수 있습니다.|

   :::image type="content" source="./media/devtest-lab-add-vm/portal-lab-vm-advanced-settings.png" alt-text="가상 머신 고급 설정 페이지.":::

1. **기본 설정** 탭으로 돌아가서 **만들기를** 선택합니다.

1. **DevTest Lab** 페이지의 **내 랩** 아래에서 **클레임할 수 있는 가상 머신을** 선택합니다.

   :::image type="content" source="./media/devtest-lab-add-vm/portal-lab-vm-creation-status.png" alt-text="랩 VM 만들기 상태 페이지.":::

1. 몇 분 후 가상 머신이 표시되지 않으면 **새로 고침을** 선택합니다. 설치 시간은 선택한 하드웨어, 기본 이미지 및 아티팩트별로 달라집니다. 이 워크스루에 사용된 구성에 대한 설치는 약 25분이었습니다.

## <a name="add-artifacts-during-installation"></a>설치하는 동안 아티팩트 추가

이러한 단계는 이전 섹션의 확장된 지침입니다. 기본 설정 **탭에서** Artifacts **추가 또는 제거를** 선택한 후에 단계가 시작됩니다. 아티팩트에 대한 자세한 내용은 [DevTest Labs에서 사용할 사용자 고유의 아티팩트를 제작하는 방법 알아보기를 참조하세요.](devtest-lab-artifact-author.md)

1. **아티팩트 추가** 페이지에서 아티팩트 식별 후 **>** (보다 큽니다 기호)를 선택합니다. 그런 다음, **확인** 을 선택합니다.

   :::image type="content" source="./media/devtest-lab-add-vm/portal-add-artifact-during.png" alt-text="가상 머신에 아티팩트 추가":::

1. 다른 **아티팩트( PowerShell 모듈 설치)를** 선택합니다. 이 아티팩트에서는 추가 정보, 특히 PowerShell 모듈의 이름이 필요합니다. **Az** 를 입력한 **다음, 확인을** 선택합니다.

1. VM에 필요한 만큼 계속해서 아티팩트를 추가합니다.

1. 선택한  아티팩트 중 하나에서 ...(줄임표)를 선택하고 설치 순서를 변경하는 기능을 포함하여 다양한 옵션을 기록해 둡니다.

1. 아티팩트 추가가 완료되면 **확인을** 선택하여 기본 **설정** 탭으로 돌아갑니다.

## <a name="add-artifacts-after-installation"></a>설치 후 아티팩트 추가

VM을 만든 후 아티팩트도 추가할 수 있습니다. 

1. **DevTest Lab** 페이지의 **내 랩** 아래에서 모든 **리소스를 선택합니다.** **모든 리소스에는** 클레임된 VM과 클레임되지 않은 VM이 모두 나열됩니다.

    :::image type="content" source="./media/devtest-lab-add-vm/portal-lab-vm-all-resources.png" alt-text="모든 리소스의 상태를 보여주는 랩입니다.":::

1. **상태가** **사용 가능으로** 표시되면 VM을 선택합니다.

1. 가상 **머신** 페이지에서 **시작을** 선택하여 VM을 시작합니다.

1. 페이지가 **실행** 중으로 표시되면 몇 분 후에 **작업** 아래에서 **Artifacts** 선택합니다.

   :::image type="content" source="./media/devtest-lab-add-vm/portal-lab-vm-overview.png" alt-text="시작 단추를 보여주는 랩 VM 개요입니다.":::

1. **아티팩트 적용을** 선택하여 **아티팩트 추가** 페이지를 엽니다.

1. 여기에서 단계는 기본적으로 위의 설치 중에 [아티팩트 추가와](#add-artifacts-during-installation)동일합니다.

## <a name="next-steps"></a>다음 단계

* VM을 만든 후에는 해당 VM의 창에서 **연결** 을 선택하여 VM에 연결할 수 있습니다.
* [DevTest Labs VM용 사용자 지정 아티팩트 작성](devtest-lab-artifact-author.md)방법을 알아봅니다.
* [DevTest Labs Azure Resource Manager 빠른 시작 템플릿 갤러리](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/QuickStartTemplates)를 탐색합니다.
