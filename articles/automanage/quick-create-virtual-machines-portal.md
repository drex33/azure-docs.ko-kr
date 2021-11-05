---
title: 빠른 시작 - Azure Portal에서 VM용 Azure Automanage 사용
description: Azure Portal에서 신규 또는 기존 VM에 가상 머신용 Automanage를 신속하게 사용하도록 설정하는 방법을 알아봅니다.
author: ju-shim
ms.author: jushiman
ms.date: 02/17/2021
ms.topic: quickstart
ms.service: virtual-machines
ms.subservice: automanage
ms.workload: infrastructure
ms.custom: mode-portal
ms.openlocfilehash: ab9a6f5ff85fe0b5b2d1df4d2a3f64bb4cb21207
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131465704"
---
# <a name="quickstart-enable-azure-automanage-for-virtual-machines-in-the-azure-portal"></a>빠른 시작: Azure Portal에서 가상 머신용 Azure Automanage 사용

Azure Portal을 사용하여 신규 또는 기존 가상 머신에 Automanage를 사용하도록 설정하여 가상 머신용 Azure Automanage를 시작하세요.


## <a name="prerequisites"></a>필수 구성 요소

Azure 구독이 없는 경우 시작하기 전에 [계정을 만드세요](https://azure.microsoft.com/pricing/purchase-options/pay-as-you-go/).

> [!NOTE]
> 평가판 계정은 이 자습서에서 사용되는 가상 머신에 액세스할 수 없습니다. 종량제 구독으로 업그레이드하세요.

> [!IMPORTANT]
> Automanage를 사용하도록 설정하려면 VM이 포함된 리소스 그룹에 대한 **기여자** 역할이 있어야 합니다. 구독에서 처음으로 Automanage를 사용하도록 설정하는 경우 다음 권한이 필요합니다. 구독에 대한 **사용자 액세스 관리자** 역할과 함께 **소유자** 역할 또는 **기여자**


## <a name="sign-in-to-azure"></a>Azure에 로그인

[Azure 포털](https://portal.azure.com)

## <a name="enable-automanage-on-existing-machines"></a>기존 컴퓨터에서 Automanage 사용

1. 검색 창에서 **Automanage – Azure 가상 머신 모범 사례** 를 검색하고 선택합니다.

2. **기존 VM에서 사용** 을 선택합니다.

    :::image type="content" source="media\quick-create-virtual-machine-portal\zero-vm-list-view.png" alt-text="기존 VM에서 사용":::

4. **구성 프로필** 에서 프로필 유형: **Azure 모범 사례 - 프로덕션** 또는 **Azure 모범 사례 - 개발/테스트** 또는 [**사용자 지정 프로필**](virtual-machines-custom-profile.md)을 선택합니다.

    :::image type="content" source="media\quick-create-virtual-machine-portal\existing-vm-quick-create.png" alt-text="환경을 선택합니다.":::

   **모범 사례 프로필 보기** 를 클릭하여 환경 간의 차이점을 확인합니다.
    1. 테스트의 경우 *개발/테스트*, 프로덕션의 경우 *프로덕션* 드롭다운에서 환경을 선택합니다.
    1. **확인** 단추를 클릭합니다.

    :::image type="content" source="media\quick-create-virtual-machine-portal\browse-production-profile.png" alt-text="프로덕션 환경을 찾아봅니다.":::

5. **머신 선택** 블레이드에서 다음을 수행합니다.
    1. **구독** 및 **리소스 그룹** 을 기준으로 머신 목록을 필터링합니다.
    1. 온보딩하려는 각 가상 머신의 확인란을 선택합니다.
    1. **선택** 단추를 클릭합니다.
    > [!NOTE]
    > Azure VM과 Azure Arc 지원 서버를 모두 선택할 수 있습니다.

    :::image type="content" source="media\quick-create-virtual-machine-portal\existing-vm-select-machine.png" alt-text="사용 가능한 VM 목록에서 기존 VM 선택":::


6. **사용** 단추를 클릭합니다.


## <a name="disable-automanage-for-vms"></a>VM용 Automanage 사용 안 함

자동 관리를 사용하지 않도록 설정하여 가상 머신용 Azure Automanage 사용을 신속하게 중지합니다.

:::image type="content" source="media\automanage-virtual-machines\disable-step-1.png" alt-text="가상 머신에서 Automanage 사용 안 함":::

1. 모든 자동 관리형 VM이 나열되는 **Automanage – Azure 가상 머신 모범 사례** 페이지로 이동합니다.
1. 사용하지 않도록 설정할 가상 머신 옆의 확인란을 선택합니다.
1. **자동 관리 사용 안 함** 버튼을 클릭합니다.
1. **사용 안 함** 에 동의하기 전에 결과 팝업의 메시지를 자세히 읽어 보세요.


## <a name="clean-up-resources"></a>리소스 정리

가상 머신용 Azure Automanage를 사용해 보기 위해 새 리소스 그룹을 만들었는데 더 이상 필요하지 않다면 리소스 그룹을 삭제할 수 있습니다. 그룹을 삭제하면 리소스 그룹에 포함된 모든 리소스와 VM도 삭제됩니다.

Azure Automanage는 리소스를 저장할 기본 리소스 그룹을 만듭니다. "DefaultResourceGroupRegionName" 및 "AzureBackupRGRegionName"이라는 명명 규칙을 가진 리소스 그룹을 확인하여 모든 리소스를 정리합니다.

1. **리소스 그룹** 을 선택합니다.
1. 리소스 그룹 페이지에서 **삭제** 를 선택합니다.
1. 메시지가 표시되면 리소스 그룹의 이름을 확인한 다음, **삭제** 를 선택합니다.


## <a name="next-steps"></a>다음 단계

이 빠른 시작에서는 VM용 Azure Automanage를 사용하도록 설정했습니다.

가상 머신에 Automanage를 사용하도록 설정할 때 사용자 지정된 프로필을 만들고 적용하는 방법을 알아봅니다.

> [!div class="nextstepaction"]
> [Azure Automanage 사용자 지정 프로필](virtual-machines-custom-profile.md)
