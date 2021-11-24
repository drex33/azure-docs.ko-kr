---
title: '빠른 시작: Azure Portal에서 랩 만들기'
description: 이 빠른 시작에서는 Azure Portal 및 Azure DevTest Labs를 사용하여 랩을 만듭니다.
ms.topic: quickstart
ms.date: 11/04/2021
ms.custom: mode-portal
ms.openlocfilehash: 6a3ef333358f45859bee7c391489854c2ed20b5d
ms.sourcegitcommit: 56235f8694cc5f88db3afcc8c27ce769ecf455b0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/24/2021
ms.locfileid: "133046062"
---
# <a name="quickstart-create-a-lab-in-azure-devtest-labs-in-azure-portal"></a>빠른 시작: Azure Portal의 Azure DevTest Labs에서 랩 만들기

Azure Portal을 사용하여 랩을 만들어 Azure DevTest Labs를 시작합니다. Azure DevTest Labs는 Azure VM(가상 머신) 및 네트워크와 같은 리소스 그룹을 포함합니다. 이 인프라를 사용하면 제한 및 할당량을 지정하여 해당 리소스를 더 잘 관리할 수 있습니다. 이 빠른 시작에서는 Azure Portal을 사용하여 랩을 만드는 프로세스를 안내합니다.

## <a name="prerequisites"></a>필수 구성 요소

활성 구독이 있는 Azure 계정. [체험 계정을 만듭니다](https://azure.microsoft.com/free/?WT.mc_id=A261C142F). 랩을 만들려면 구독 소유자여야 합니다.

## <a name="sign-in-to-the-azure-portal"></a>Azure Portal에 로그인

다음 링크를 선택하면 Azure DevTest Labs에서 새 랩 만들기를 시작할 수 있는 Azure Portal 페이지로 이동합니다.

[몇 분 이내에 Azure DevTest Labs 시작](https://go.microsoft.com/fwlink/?LinkID=627034&clcid=0x409)

## <a name="create-a-devtest-labs-resource"></a>DevTest Labs 리소스 만들기

**Devtest Lab 만들기** 페이지에는 5개의 탭이 있습니다. 첫 번째 탭은 **기본 설정** 입니다.

> [!TIP]
> 각 페이지의 맨 아래에는 **자동화를 위한 템플릿을 다운로드** 할 수 있는 링크가 있습니다.

### <a name="basic-settings-tab"></a>기본 설정 탭

다음 정보를 지정합니다.

|속성 | 설명 |
|---|---|
|Subscription| 드롭다운 목록에서 랩에 사용할 Azure 구독을 선택합니다.|
|Resource&nbsp;group| 드롭다운 목록에서 기존 리소스 그룹을 선택하거나 **새로 만들기** 를 선택합니다.|
|랩 이름| 랩에 대해 구독 내에서 고유한 이름을 입력합니다.|
|위치| 드롭다운 목록에서 랩에 사용되는 위치를 선택합니다.|
|공용 환경| 공용 환경 리포지토리에는 랩 사용자가 랩 내에서 PaaS 리소스를 만들 수 있도록 하는 큐레이팅된 Azure Resource Manager 템플릿 목록이 포함되어 있습니다. 자세한 내용은 [퍼블릭 환경 구성 및 사용](devtest-lab-configure-use-public-environments.md)을 참조하세요.|

:::image type="content" source="./media/devtest-lab-create-lab/portal-create-basic-settings.png" alt-text="DevTest Labs 만들기에 대한 기본 설정 탭의 스크린샷":::


### <a name="auto-shutdown-tab"></a>자동 종료 탭

자동 종료를 사용하면 매일 예약된 시간에 랩의 모든 머신을 자동으로 종료할 수 있습니다. 자동 종료 기능은 주로 비용 절감 기능입니다. 랩을 만든 후 자동 종료 설정을 변경하려면 [Azure DevTest Labs에서 랩에 대한 모든 정책 관리](./devtest-lab-set-lab-policy.md#set-auto-shutdown)를 참조하세요.

다음 정보를 지정합니다.

|속성 | Description |
|---|---|
|사용| 이 정책을 사용하도록 설정하려면 **켜기** 를 선택하고, 사용하지 않도록 설정하려면 **끄기** 를 선택합니다.|
|예약된&nbsp;종료| 현재 랩의 모든 VM을 종료할 시간을 입력합니다.|
|표준 시간대| 드롭다운 목록에서 표준 시간대를 선택합니다.|
|자동 종료 전에 알림을 보내시겠습니까? | 지정된 자동 종료 시간 30분 전에 알림을 보내려면 **예** 또는 **아니요** 를 선택합니다. **예** 를 선택할 경우 알림을 게시하거나 보낼 위치를 지정하는 이메일 주소 또는 웹후크 URL 엔드포인트를 입력합니다. 사용자에게 알림이 전송되고 종료를 지연할 수 있는 옵션이 제공됩니다.|
|Webhook URL| 자동 종료가 발생하려고 하면 알림이 지정된 웹후크 엔드포인트에 게시됩니다.|
|메일 주소| 경고 알림 이메일을 받으려면 세미콜론으로 구분된 이메일 주소 세트를 입력합니다.|

:::image type="content" source="./media/devtest-lab-create-lab/portal-create-auto-shutdown.png" alt-text="자동 종료 일정 세부 정보의 스크린샷":::

### <a name="networking-tab"></a>네트워킹 탭

기본 네트워크가 만들어지거나(나중에 변경/구성할 수 있음) 기존 가상 네트워크를 선택할 수 있습니다.

다음 정보를 지정합니다.

|속성 | 설명 |
|---|---|
|가상&nbsp;네트워크| 기본값을 유지하거나 드롭다운 목록에서 기존 값을 선택합니다. 가상 네트워크는 Azure에서 논리적으로 서로 격리됩니다. 기본적으로 동일한 가상 네트워크의 가상 머신은 서로 액세스할 수 있습니다.|
|서브넷| 기본값을 유지하거나 드롭다운 목록에서 기존 값을 선택합니다. 서브넷은 가상 머신을 서로 격리하거나 인터넷에서 격리하는 데 사용할 수 있는 가상 네트워크의 IP 주소 범위입니다.|

:::image type="content" source="./media/devtest-lab-create-lab/portal-create-networking.png" alt-text="네트워킹 세부 정보의 스크린샷":::

### <a name="tags-tab"></a>태그 탭

태그는 랩 리소스를 범주별으로 관리하고 구성하는 데 유용합니다. 자세한 내용은 [랩에 태그 추가](devtest-lab-add-tag.md)를 참조하세요.

다음 정보를 지정합니다.

|속성 | Description |
|---|---|
|이름| 태그 이름은 대/소문자를 구분하지 않으며, 512자로 제한됩니다.|
|값| 태그 값은 대/소문자를 구분하며, 256자로 제한됩니다.|

:::image type="content" source="./media/devtest-lab-create-lab/portal-create-tags.png" alt-text="태그 세부 정보의 스크린샷":::

### <a name="review--create-tab"></a>리뷰 + 만들기 탭

**검토 + 만들기** 탭은 모든 구성의 유효성을 검사합니다. 모든 설정이 유효하면 위쪽에 **성공** 이 표시됩니다. 설정을 검토한 다음, **만들기** 를 선택합니다. 포털 페이지 오른쪽 위에 있는 **알림** 영역을 확인하여 랩 만들기 프로세스 상태를 모니터링할 수 있습니다. 

:::image type="content" source="./media/devtest-lab-create-lab/portal-review-and-create.png" alt-text="검토 및 만들기 세부 정보의 스크린샷":::

## <a name="post-creation"></a>만들기 후

1. 만들기 프로세스가 완료되면 배포 알림에서 **리소스로 이동** 을 선택합니다.

    :::image type="content" source="./media/devtest-lab-create-lab/creation-notification.png" alt-text="DevTest Labs 배포 알림의 스크린샷":::

1. 랩의 **개요** 페이지는 다음 이미지와 비슷합니다.

    :::image type="content" source="./media/devtest-lab-create-lab/lab-home-page.png" alt-text="DevTest Labs 개요 페이지의 스크린샷":::

## <a name="clean-up-resources"></a>리소스 정리

Azure에서 랩을 실행하는 데 드는 요금이 발생하지 않도록 리소스를 삭제합니다. 다음 문서로 계속 진행하여 VM을 랩에 추가하려는 경우 해당 문서를 완료한 후 리소스를 정리할 수 있습니다. 액세스 권한이 없는 경우 다음 단계를 수행하세요.

1. 만든 랩의 홈 페이지로 돌아갑니다.

1. 위쪽 메뉴에서 **삭제** 를 선택합니다.

   :::image type="content" source="./media/devtest-lab-create-lab/portal-lab-delete.png" alt-text="랩 삭제 단추의 스크린샷":::

1. **삭제하시겠습니까?** 페이지의 텍스트 상자에서 랩 이름을 입력한 다음, **삭제** 를 선택합니다.

1. 삭제하는 동안 화면 위쪽에서 **알림** 을 선택하여 진행 상황을 볼 수 있습니다. 랩을 삭제하는 데 시간이 걸립니다. 랩이 삭제되면 다음 단계를 계속합니다.

1. 기존 리소스 그룹에서 랩을 만든 경우 모든 랩 리소스가 제거됩니다. 이 자습서에 대한 새 리소스 그룹을 만든 경우 이제 비어 있으며 삭제할 수 있습니다. 랩이 아직 남아 있는 동안에는 리소스 그룹을 먼저 삭제할 수 없습니다.

## <a name="next-steps"></a>다음 단계
이 빠른 시작에서는 랩을 만들었습니다. VM을 추가하는 방법을 알아보려면 다음 문서로 계속 진행하세요.

> [!div class="nextstepaction"]
> [Azure DevTest Labs에서 랩에 가상 머신 만들기 및 추가](devtest-lab-add-vm.md)
