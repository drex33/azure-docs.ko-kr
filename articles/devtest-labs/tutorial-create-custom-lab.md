---
title: 랩 자습서 만들기
description: 이 자습서에서는 Azure Portal을 사용하여 Azure DevTest Labs에서 랩을 만듭니다. 랩 관리자는 랩을 설정하고, 랩에 VM을 만들고, 정책을 구성합니다.
ms.topic: tutorial
ms.date: 11/03/2021
ms.openlocfilehash: 4a26501511adf65d2db8509120a1d4a014ef73ba
ms.sourcegitcommit: 96deccc7988fca3218378a92b3ab685a5123fb73
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/04/2021
ms.locfileid: "131578600"
---
# <a name="tutorial-set-up-a-lab-in-devtest-labs-using-the-azure-portal"></a>자습서: Azure Portal을 사용하여 DevTest Labs에서 랩 설정

이 자습서에서는 Azure Portal을 사용하여 랩을 만듭니다. 랩 관리자는 조직에 랩을 설정하고, 랩에 Azure VM(Virtual Machines)을 만들고, 정책을 구성합니다. 랩 사용자(예: 개발자 및 테스터)는 랩에서 VM을 클레임하고 연결하여 사용합니다. 

이 자습서에서는 다음과 같은 작업을 수행하는 방법을 살펴봅니다.

> [!div class="checklist"]
> * 랩 만들기
> * 랩에 Azure VM(Virtual Machines) 추가
> * 사용자를 추가하고 **DevTest Labs 사용자** 역할에 할당

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

## <a name="create-a-lab"></a>랩 만들기

다음 단계는 Azure Portal을 사용하여 Azure DevTest Labs에서 랩을 만드는 방법을 설명합니다. 

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.

1. 검색 텍스트 상자에 `DevTest Labs`를 입력한 다음, 결과에서 **DevTest Labs** 를 선택합니다.

    :::image type="content" source="./media/tutorial-create-custom-lab/portal-search-devtest-labs.png" alt-text="DevTest Labs에 대한 포털 검색 스크린샷":::

1. **DevTest Labs** 페이지에서 **+ 만들기** 를 선택합니다.

1. **Devtest Lab 만들기** 페이지의 **기본 설정** 탭에서 다음 정보를 제공합니다.

    |속성 | Description |
    |---|---|
    |구독| 드롭다운 목록에서 랩에 사용할 Azure 구독을 선택합니다.|
    |Resource&nbsp;group| 드롭다운 목록에서 기존 리소스 그룹을 선택하거나 **새로 만들기** 를 선택합니다.|
    |랩 이름| 랩에 대한 이름을 입력합니다.|
    |위치| 드롭다운 목록에서 랩에 사용되는 위치를 선택합니다.|
    |공용 환경| 기본값인 **On** 상태로 둡니다. 퍼블릭 환경 리포지토리에는 랩 사용자가 랩 내에서 PaaS 리소스를 만들 수 있도록 하는 큐레이팅된 Azure Resource Manager 템플릿 목록이 포함되어 있습니다.|

    :::image type="content" source="./media/tutorial-create-custom-lab/create-custom-lab-blade.png" alt-text="DevTest Labs 만들기에 대한 기본 설정 탭의 스크린샷":::

1. **검토 + 만들기** 를 선택하여 구성이 유효한지 확인한 후 **만들기** 를 선택합니다. 이 자습서에서는 다른 탭은 기본값으로 두어도 충분합니다.

1. 만들기 프로세스가 완료되면 배포 알림에서 **리소스로 이동** 을 선택합니다.

    :::image type="content" source="./media/tutorial-create-custom-lab/creation-notification.png" alt-text="DevTest Labs 배포 알림의 스크린샷":::

1. 랩의 **개요** 페이지는 다음 이미지와 비슷합니다.

    :::image type="content" source="./media/tutorial-create-custom-lab/lab-home-page.png" alt-text="DevTest Labs 개요 페이지의 스크린샷":::

## <a name="add-a-vm-to-the-lab"></a>랩에 VM 추가

1. **DevTest Lab** 페이지의 도구 모임에서 **+ Add** 를 선택합니다.

    :::image type="content" source="./media/tutorial-create-custom-lab/add-vm-to-lab-button.png" alt-text="DevTest Labs 개요 페이지 및 추가 단추의 스크린샷":::

1. **기본 선택** 페이지에서 VM에 대한 Marketplace 이미지를 선택합니다. 이 가이드에서는 **Windows Server 2019 Datacenter** 를 사용합니다. 다른 이미지를 사용하는 경우 특정 옵션이 다를 수 있습니다.

1. **기본 설정** 탭에서 다음 정보를 제공합니다.

    |속성 |Description |
    |---|---|
    |Virtual&nbsp;machine&nbsp;name| 텍스트 상자에는 자동 생성된 고유 이름이 미리 채워져 있습니다. 이 이름은 뒤에 고유한 3자리 숫자가 붙는 메일 주소 내의 사용자 이름에 해당합니다. 그대로 두거나 선택한 고유한 이름을 입력합니다.|
    |사용자 이름| 텍스트 상자에는 자동 생성된 고유 이름이 미리 채워져 있습니다. 이 이름은 이메일 주소 내의 사용자 이름에 해당합니다. 그대로 두거나 선택한 이름을 입력합니다. 사용자에게 가상 머신에서 **관리자** 권한이 부여됩니다.|
    |저장된 암호 사용| 이 연습에서는 해당 확인란을 선택하지 않은 상태로 둡니다. 먼저 Azure Key Vault에서 암호를 저장한 다음, 여기에서 사용할 수 있습니다. 자세한 내용은 [키 자격 증명 모음에 비밀 저장](devtest-lab-store-secrets-in-key-vault.md)을 참조하세요. 저장된 암호를 사용하려면 확인란을 선택한 다음, **암호** 드롭다운 목록에서 암호를 선택합니다.|
    |암호|8~123자 사이의 암호를 입력합니다.|
    |기본 암호로 저장| 이 확인란을 선택하여 랩과 연결된 Azure Key Vault에 암호를 저장합니다.|
    |가상 머신 크기| 기본값을 유지하거나 **크기 변경** 을 선택하여 다른 물리적 구성 요소를 선택합니다. 이 연습에서는 **Standard_D4_v3** 를 사용합니다.|
    |OS 디스크 유형|기본값을 유지하거나 드롭다운 목록에서 다른 옵션을 선택합니다.|
    |Artifacts| 이 자습서에는 사용하지 않습니다.|

   :::image type="content" source="./media/tutorial-create-custom-lab/portal-lab-vm-basic-settings.png" alt-text="가상 머신 기본 설정 페이지의 스크린샷":::

1. **고급 설정** 탭에서 다음 정보를 입력합니다.

    |속성 |설명 |
    |---|---|
    |가상 네트워크| 그대로 두거나 드롭다운 목록에서 다른 네트워크를 선택합니다.|
    |서브넷&nbsp;선택기| 그대로 두거나 드롭다운 목록에서 다른 서브넷을 선택합니다.|
    |IP 주소| 이 연습에서는 기본값인 **공유** 를 그대로 둡니다. **공유** 를 선택한 경우 Azure DevTest Labs에서는 자동으로 Windows VM에 RDP 및 Linux VM에 SSH를 사용하도록 설정합니다. **퍼블릭** 을 선택한 경우 DevTest Labs에서 변경 없이 RDP 및 SSH가 사용하도록 설정됩니다.  |
    |만료 날짜| 만료 날짜가 없도록 그대로 두거나 달력 아이콘을 선택하여 만료 날짜를 설정합니다.|
    |이 컴퓨터를 클레임 가능하도록 설정| **아니요** 상태로 둡니다. 랩 사용자가 VM을 클레임할 수 있도록 하려면 **예** 를 선택합니다. 머신을 클레임 가능으로 표시하면 만들 때 소유권이 할당되지 않습니다. |
    |인스턴스 수| **1** 상태로 둡니다. 만들 가상 머신 인스턴스 수입니다.|
    |Automation | 선택 사항입니다. **ARM 템플릿 보기** 를 선택하면 새 페이지에서 템플릿이 열립니다. 나중에 동일한 가상 머신을 만들기 위해 템플릿을 복사 및 저장할 수 있습니다. 저장한 Azure Resource Manager 템플릿은 [Azure PowerShell로 새 VM을 배포](../azure-resource-manager/templates/overview.md)하는 데 사용할 수 있습니다.|

   :::image type="content" source="./media/tutorial-create-custom-lab/portal-lab-vm-advanced-settings.png" alt-text="가상 머신 고급 설정 페이지":::

1. **기본 설정** 탭으로 돌아간 다음, **만들기** 를 선택합니다.

1. **DevTest Lab** 페이지로 돌아갑니다. **내 랩** 에서 **클레임 가능한 가상 머신** 을 선택합니다.

   :::image type="content" source="./media/tutorial-create-custom-lab/portal-lab-vm-creation-status.png" alt-text="랩 VM 만들기 상태 페이지의 스크린샷":::

1. 몇 분 후에 가상 머신이 표시되지 않으면 **새로 고침** 을 선택합니다. 설치 시간은 선택한 하드웨어, 기본 이미지 및 아티팩트에 따라 달라집니다. 이 연습에서 사용되는 구성에 대한 설치는 약 12분이 걸렸습니다.

## <a name="add-a-user-to-the-devtest-labs-user-role"></a>사용자를 DevTest Labs 사용자 역할에 추가

1. 만든 랩을 포함하는 리소스 그룹으로 이동합니다. [사용자 액세스 관리자](../role-based-access-control/built-in-roles.md#user-access-administrator) 또는 [소유자](../role-based-access-control/built-in-roles.md#owner)여야 합니다.

1. 왼쪽 메뉴에서 **액세스 제어(IAM)** 를 선택합니다.

1. **추가** > **역할 할당 추가** 를 선택합니다.

    ![역할 할당 추가 메뉴가 열려 있는 액세스 제어(IAM) 페이지.](../../includes/role-based-access-control/media/add-role-assignment-menu-generic.png)

1. **역할** 탭에서 **DevTest Labs User** 역할을 선택합니다.

    ![역할 탭이 선택된 역할 할당 페이지를 추가합니다.](../../includes/role-based-access-control/media/add-role-assignment-role-generic.png)

1. **구성원** 탭에서 역할을 할당할 사용자를 선택합니다.

1. **검토 + 할당** 탭에서 **검토 + 할당** 을 선택하여 역할을 할당합니다.

## <a name="clean-up-resources"></a>리소스 정리

Azure에서 랩 및 VM을 실행하는 데 드는 요금이 발생하지 않도록 리소스를 삭제합니다. 랩에서 VM에 액세스하기 위한 다음 자습서를 진행하려는 경우 해당 자습서를 완료한 후 리소스를 정리할 수 있습니다. 액세스 권한이 없는 경우 다음 단계를 수행하세요. 

1. 만든 랩의 홈 페이지로 돌아갑니다.

1. 위쪽 메뉴에서 **삭제** 를 선택합니다.

   :::image type="content" source="./media/tutorial-create-custom-lab/portal-lab-delete.png" alt-text="랩 삭제 단추의 스크린샷":::

1. **삭제하시겠습니까?** 페이지의 텍스트 상자에서 랩 이름을 입력한 다음, **삭제** 를 선택합니다.

1. 삭제하는 동안 화면 위쪽에서 **알림** 을 선택하여 진행 상황을 볼 수 있습니다. 랩을 삭제하는 데 시간이 걸립니다. 랩이 삭제되면 다음 단계를 계속합니다.

1. 기존 리소스 그룹에서 랩을 만든 경우 모든 랩 리소스가 제거됩니다. 이 자습서에 대한 새 리소스 그룹을 만든 경우 이제 비어 있으며 삭제할 수 있습니다. 랩이 아직 남아 있는 동안에는 리소스 그룹을 먼저 삭제할 수 없습니다.

## <a name="next-steps"></a>다음 단계

이 자습서에서는 랩을 만들고 VM을 사용한 후 사용자에게 랩에 액세스할 수 있는 권한을 부여합니다. 랩 사용자로 랩에 액세스하는 방법에 대해 알아보려면 다음 자습서로 이동합니다.

> [!div class="nextstepaction"]
> [자습서: 랩에 액세스](tutorial-use-custom-lab.md)
