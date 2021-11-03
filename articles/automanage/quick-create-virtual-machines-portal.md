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
ms.openlocfilehash: f471ce5ca6ae4204dd3dce8ae80057b34379e52e
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131020904"
---
# <a name="quickstart-enable-azure-automanage-for-virtual-machines-in-the-azure-portal"></a>빠른 시작: Azure Portal에서 가상 머신용 Azure Automanage 사용

Azure Portal을 사용하여 신규 또는 기존 가상 머신에 Automanage를 사용하도록 설정하여 가상 머신용 Azure Automanage를 시작하세요.


## <a name="prerequisites"></a>필수 구성 요소

Azure 구독이 없는 경우 시작하기 전에 [계정을 만드세요](https://azure.microsoft.com/pricing/purchase-options/pay-as-you-go/).

> [!NOTE]
> 평가판 계정은 이 자습서에서 사용되는 가상 머신에 액세스할 수 없습니다. 종량제 구독으로 업그레이드하세요.

> [!IMPORTANT]
> 기존 Automanage 계정을 사용하여 Automanage를 사용하도록 설정하려면 VM이 포함된 리소스 그룹에 대한 **기여자** 역할이 있어야 합니다. 새 Automanage 계정으로 Automanage를 사용하도록 설정하는 경우 다음 권한이 필요합니다. 구독에 대한 **사용자 액세스 관리자** 역할과 함께 **소유자** 역할 또는 **기여자**.


## <a name="sign-in-to-azure"></a>Azure에 로그인

[Azure Portal](https://aka.ms/AutomanagePortal-Ignite21)에 로그인합니다.

## <a name="enable-automanage-on-existing-machines"></a>기존 컴퓨터에서 Automanage 사용

1. 검색 창에서 **Automanage – Azure 가상 머신 모범 사례** 를 검색하고 선택합니다.

2. **기존 VM에서 사용** 을 선택합니다.

    :::image type="content" source="media\quick-create-virtual-machine-portal\zero-vm-list-view.png" alt-text="기존 VM에서 사용":::

3. **머신 선택** 블레이드에서 다음을 수행합니다.
    1. **구독** 및 **리소스 그룹** 을 기준으로 머신 목록을 필터링합니다.
    1. 온보딩하려는 각 가상 머신의 확인란을 선택합니다.
    1. **선택** 단추를 클릭합니다.
    > [!NOTE]
    > Azure VM과 Azure Arc 지원 서버를 모두 선택할 수 있습니다.

    :::image type="content" source="media\quick-create-virtual-machine-portal\existing-vm-select-machine.png" alt-text="사용 가능한 VM 목록에서 기존 VM 선택":::

4. **환경** 에서 **개발/테스트** 또는 **프로덕션** 환경 유형을 선택합니다.

    :::image type="content" source="media\quick-create-virtual-machine-portal\existing-vm-quick-create.png" alt-text="환경을 선택합니다.":::

   **환경 세부 정보 비교** 를 클릭하여 환경 간의 차이점을 확인합니다.
    1. 테스트의 경우 *개발/테스트*, 프로덕션의 경우 *프로덕션* 드롭다운에서 환경을 선택합니다.
    1. **확인** 단추를 클릭합니다.

    :::image type="content" source="media\quick-create-virtual-machine-portal\browse-production-profile.png" alt-text="프로덕션 환경을 찾아봅니다.":::

5. 기본적으로 구성 기본 설정에 대해 **Azure 모범 사례** 기본 설정이 선택됩니다. 이를 변경하려면 새 기본 설정을 만들거나 기존 기본 설정을 선택합니다.

    :::image type="content" source="media\quick-create-virtual-machine-portal\create-preference.png" alt-text="기본 설정을 만듭니다.":::

6. **사용** 단추를 클릭합니다.


## <a name="enable-automanage-for-a-new-vm"></a>새 VM에 대한 Automanage 사용

[여기](https://aka.ms/AzureAutomanagePreview)에서 Azure Portal에 로그인하여 새 VM을 만들고 Automanage를 사용하도록 설정합니다.

1. **기본** 탭에서 VM 세부 정보를 작성합니다.

> [!NOTE]
> Automanage [지원되는 지역](automanage-virtual-machines.md#supported-regions), Automanage 지원되는 [Linux 배포판](automanage-linux.md#supported-linux-distributions-and-versions) 및 [Windows Server 버전](automanage-windows-server.md#supported-windows-server-versions)을 선택합니다.

2. **관리** 탭으로 이동하여 **Automanage 환경** 을 선택합니다.

    :::image type="content" source="media\quick-create-virtual-machine-portal\vmcreate-managementtab.png" alt-text="관리 탭에서 Automanage를 사용하도록 설정합니다.":::

3. 나머지 기본값을 그대로 둔 다음, 페이지의 아래에서 **검토 + 만들기** 단추를 선택합니다.

4. 유효성 검사가 통과되었다는 메시지가 표시되면 **만들기** 를 선택합니다.

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

가상 머신에 Automanage를 사용하도록 설정할 때 사용자 지정된 기본 설정을 만들고 적용하는 방법을 알아보세요.

> [!div class="nextstepaction"]
> [VM용 Azure Automanage - 사용자 지정 구성 기본 설정](virtual-machines-custom-preferences.md)
