---
title: VM에 대한 Azure Automanage 사용자 지정 프로필 만들기
description: Azure Automanage 사용자 지정 프로필을 만들고 서비스 및 설정을 선택하는 방법을 알아봅니다.
author: ju-shim
ms.service: virtual-machines
ms.subservice: automanage
ms.workload: infrastructure
ms.topic: how-to
ms.date: 02/22/2021
ms.author: jushiman
ms.openlocfilehash: 821ad720645c433b9a487383e5ed9edb5bc21e61
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131483086"
---
# <a name="create-a-custom-profile-in-azure-automanage-for-vms"></a>VM에 대한 Azure Automanage 사용자 지정 프로필 만들기

컴퓨터 모범 사례에 대한 Azure Automanage 편집할 수 없는 기본 모범 사례 프로필이 있습니다. 그러나 더 많은 유연성이 필요한 경우 사용자 지정 프로필을 만들어 서비스 및 설정 집합을 선택하고 선택할 수 있습니다.

서비스 켜기 및 끄기를 지원합니다. 또한 현재 Azure Backup 및 [Microsoft Antimalware](..\backup\backup-azure-arm-vms-prepare.md#create-a-custom-policy) 설정을 사용자 지정할 [수](../security/fundamentals/antimalware.md#default-and-custom-antimalware-configuration)있습니다.


## <a name="prerequisites"></a>사전 요구 사항

Azure 구독이 없는 경우 시작하기 전에 [계정을 만드세요](https://azure.microsoft.com/pricing/purchase-options/pay-as-you-go/).

> [!NOTE]
> 평가판 계정은 이 자습서에서 사용되는 가상 머신에 액세스할 수 없습니다. 종량제 구독으로 업그레이드하세요.

> [!IMPORTANT]
> 구독에서 처음으로 Automanage를 사용하도록 설정하려면 다음 Azure RBAC 권한이 필요합니다. **소유자** 역할 또는 사용자 **액세스 관리자** 역할과 함께 **기여자.**


## <a name="sign-in-to-azure"></a>Azure에 로그인

[Azure Portal](https://portal.azure.com/)에 로그인합니다.


## <a name="enable-automanage-for-vms-on-an-existing-vm"></a>기존 VM에서 VM용 Automanage 사용

1. 검색 창에서 **Automanage – Azure 가상 머신 모범 사례** 를 검색하고 선택합니다.

2. **기존 VM에서 사용** 을 선택합니다.

3. **구성 프로필에서** **사용자 지정 프로필을** 선택합니다.

4. 기존 사용자 지정 프로필이 있는 경우 드롭다운에서 기존 사용자 지정 프로필을 선택하거나 새 **만들기를** 클릭하여 새 사용자 지정 프로필을 만듭니다.

5. 새 **프로필 만들기** 블레이드에서 세부 정보를 입력합니다.
    1. 프로필 이름
    1. Subscription
    1. 리소스 그룹
    1. 지역

    :::image type="content" source="media\virtual-machine-custom-profile\create-custom-profile.png" alt-text="사용자 지정 프로필 세부 정보를 입력합니다.":::

6. 원하는 서비스 및 설정으로 프로필을 조정하고 **만들기를** 클릭합니다.

7. **머신 선택** 블레이드에서 다음을 수행합니다.
    1. **구독** 및 **리소스 그룹** 을 기준으로 VM 목록을 필터링합니다.
    1. 온보딩하려는 각 가상 머신의 확인란을 선택합니다.
    1. **선택** 단추를 클릭합니다.

    :::image type="content" source="media\virtual-machine-custom-profile\existing-vm-select-machine.png" alt-text="사용 가능한 VM 목록에서 기존 VM 선택":::

    > [!NOTE]
    > **부적합한 머신 표시** 를 클릭하여 지원되지 않는 머신 목록과 이유를 확인합니다. 

## <a name="disable-automanage-for-vms"></a>VM용 Automanage 사용 안 함

자동 관리를 사용하지 않도록 설정하여 가상 머신용 Azure Automanage 사용을 신속하게 중지합니다.

:::image type="content" source="media\virtual-machine-custom-profile\disable-step-1.png" alt-text="가상 머신에서 Automanage 사용 안 함":::

1. 자동 관리형 VM을 모두 나열하는 **Automanage – Azure 머신 모범 사례** 페이지로 이동합니다.
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