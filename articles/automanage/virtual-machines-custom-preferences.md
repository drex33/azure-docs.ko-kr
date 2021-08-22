---
title: VM용 Azure Automanage에서 사용자 지정 기본 설정 만들기
description: Azure Automanage에서 환경 구성을 조정하고 사용자 고유의 기본 설정을 지정하는 방법에 대해 알아봅니다.
author: ju-shim
ms.service: virtual-machines
ms.subservice: automanage
ms.workload: infrastructure
ms.topic: how-to
ms.date: 02/22/2021
ms.author: jushiman
ms.openlocfilehash: edd82086dba6f603c50edd23c3f3757b95dcdb9d
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/22/2021
ms.locfileid: "114468367"
---
# <a name="create-a-custom-preference-in-azure-automanage-for-vms"></a>VM용 Azure Automanage에서 사용자 지정 기본 설정 만들기

가상 머신용 Azure Automanage는 필요한 경우 기본 환경을 조정할 수 있습니다. 이 문서에서는 신규 또는 기존 VM에서 자동 관리를 사용하도록 설정하는 경우 사용자 고유의 기본 설정을 지정하는 방법을 설명합니다.

현재 [Azure Backup](..\backup\backup-azure-arm-vms-prepare.md#create-a-custom-policy) 및 [Microsoft Antimalware](../security/fundamentals/antimalware.md#default-and-custom-antimalware-configuration)에 대한 사용자 지정을 지원합니다.


> [!NOTE]
> Automanage를 사용하는 동안에는 VM의 환경 또는 기본 설정을 변경할 수 없습니다. 해당 VM에 대해 Automanage를 사용하지 않도록 설정한 다음 원하는 구성 환경 및 기본 설정으로 Automanage를 다시 사용하도록 설정해야 합니다.


## <a name="prerequisites"></a>사전 요구 사항

Azure 구독이 없는 경우 시작하기 전에 [계정을 만드세요](https://azure.microsoft.com/pricing/purchase-options/pay-as-you-go/).

> [!NOTE]
> 평가판 계정은 이 자습서에서 사용되는 가상 머신에 액세스할 수 없습니다. 종량제 구독으로 업그레이드하세요.

> [!IMPORTANT]
> Automanage를 사용하도록 설정하려면 다음 Azure RBAC 권한이 필요합니다. **Owner** 역할 또는 **Contributor** 와 **User Access Administrator** 역할


## <a name="sign-in-to-azure"></a>Azure에 로그인

[Azure Portal](https://portal.azure.com/)에 로그인합니다.


## <a name="enable-automanage-for-vms-on-an-existing-vm"></a>기존 VM에서 VM용 Automanage 사용

1. 검색 창에서 **Automanage – Azure 가상 머신 모범 사례** 를 검색하고 선택합니다.

2. **기존 VM에서 사용** 을 선택합니다.

3. **머신 선택** 블레이드에서 다음을 수행합니다.
    1. **구독** 및 **리소스 그룹** 을 기준으로 VM 목록을 필터링합니다.
    1. 온보딩하려는 각 가상 머신의 확인란을 선택합니다.
    1. **선택** 단추를 클릭합니다.

    :::image type="content" source="media\virtual-machine-custom-preferences\existing-vm-select-machine.png" alt-text="사용 가능한 VM 목록에서 기존 VM 선택":::

    > [!NOTE]
    > **부적합한 머신 표시** 를 클릭하여 지원되지 않는 머신 목록과 이유를 확인합니다. 

4. **구성** 에서 **환경 비교** 를 클릭합니다.

    :::image type="content" source="media\virtual-machine-custom-preferences\existing-vm-quick-create.png" alt-text="환경을 비교합니다.":::

5. **환경 세부 정보** 블레이드의 드롭다운 메뉴에서 환경을 선택합니다. 이때, 테스트하려면 ‘개발/테스트’, 제작하려면 ‘프로덕션’을 선택하고 **확인** 을 클릭합니다. 

    :::image type="content" source="media\virtual-machine-custom-preferences\browse-production-profile.png" alt-text="프로덕션 환경을 찾아봅니다.":::

6. 환경을 선택한 후에는 **구성 기본 설정** 을 선택할 수 있습니다. 기본적으로 Azure 모범 사례 기본 설정이 사용됩니다. 이 기본 설정에는 각 서비스에 대한 권장 설정이 사용됩니다. 사용자 지정 기본 설정을 만들어 해당 설정을 수정합니다. 
    1. **새 기본 설정 만들기** 를 클릭합니다.
    1. **구성 기본 설정 만들기** 블레이드에서 기본 탭을 작성합니다.
        1. Subscription
        1. Resource group
        1. 기본 설정 이름
        1. 지역

    :::image type="content" source="media\virtual-machine-custom-preferences\create-preference.png" alt-text="구성 기본 설정을 입력합니다.":::

7. 원하는 구성 기본 설정을 조정합니다.
        
    > [!NOTE]
    > 환경 구성을 변경하는 경우에도 모범 사례에 부합하는 조정만 허용됩니다.

8. 구성 설정의 세부 정보를 검토합니다.
9. **만들기** 단추를 클릭합니다.

10. **사용** 단추를 클릭합니다.


## <a name="disable-automanage-for-vms"></a>VM용 Automanage 사용 안 함

자동 관리를 사용하지 않도록 설정하여 가상 머신용 Azure Automanage 사용을 신속하게 중지합니다.

:::image type="content" source="media\virtual-machine-custom-preferences\disable-step-1.png" alt-text="가상 머신에서 Automanage 사용 안 함":::

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

FAQ에서 가장 자주 묻는 질문과 대답을 확인하세요. 

> [!div class="nextstepaction"]
> [질문과 대답](faq.yml)