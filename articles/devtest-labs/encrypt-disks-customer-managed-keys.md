---
title: 고객 관리형 키를 사용하여 OS 디스크 암호화
description: Azure DevTest Labs에서 고객 관리 키를 사용하여 OS(운영 체제) 디스크를 암호화하는 방법을 알아봅니다.
ms.topic: how-to
ms.date: 09/01/2020
ms.openlocfilehash: 2da0e89c5a3f34d9ba967088ea3881f6a30bc1e2
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128632351"
---
# <a name="encrypt-operating-system-os-disks-using-customer-managed-keys-in-azure-devtest-labs"></a>Azure DevTest Labs에서 고객 관리 키를 사용하여 운영 체제(OS) 디스크 암호화
SSE(서버 쪽 암호화)는 데이터를 보호하고 조직의 보안 및 규정 준수 노력에 부합하는 데 도움이 됩니다. SSE는 클라우드에 데이터를 유지할 때 기본적으로 Azure의 관리 디스크(OS 및 데이터 디스크)에 저장된 데이터를 자동으로 암호화합니다. Azure의 [디스크 암호화](../virtual-machines/disk-encryption.md)에 대해 자세히 알아보세요. 

DevTest Labs 내에서 랩의 일부로 생성된 모든 OS 디스크 및 데이터 디스크는 플랫폼 관리 키를 사용하여 암호화됩니다. 그러나 랩 소유자는 자체 키를 사용하여 랩 가상 머신 OS 디스크를 암호화하도록 선택할 수 있습니다. 사용자 고유의 키를 사용하여 암호화를 관리하는 경우 랩 OS 디스크의 데이터를 암호화하는 데 사용할 **고객 관리형 키** 를 지정할 수 있습니다. 고객 관리형 키를 사용한 SSE(서버 측 암호화) 및 기타 관리 디스크 암호화 유형을 자세히 알아보려면 [고객 관리형 키](../virtual-machines/disk-encryption.md#customer-managed-keys)를 참조하세요. 또한 [고객 관리형 키 사용에 대한 제한 사항](../virtual-machines/disks-enable-customer-managed-keys-portal.md#restrictions)을 참조하세요.

> [!NOTE]
> - 현재 고객 관리형 키를 사용하는 디스크 암호화는 DevTest Labs의 OS 디스크에만 지원됩니다. 
> 
> - 이 설정은 랩에서 새로 만든 OS 디스크에 적용됩니다. 특정 시점에 디스크 암호화 집합을 변경하도록 선택하는 경우 랩의 이전 디스크는 이전 디스크 암호화 집합을 사용하여 계속 암호화된 상태로 유지됩니다. 

다음 섹션에서는 랩 소유자가 고객 관리형 키를 사용하여 암호화를 설정할 수 있는 방법을 보여줍니다.

## <a name="pre-requisites"></a>필수 구성 요소

1. 디스크 암호화가 설정되지 않은 경우 이 문서에 따라 [Key Vault 및 디스크 암호화 집합을 설정](../virtual-machines/disks-enable-customer-managed-keys-portal.md)합니다. 디스크 암호화 설정에 대한 다음 요구 사항을 확인하세요. 

    - 디스크 암호화 집합은 **랩과 동일한 지역 및 구독** 에 있어야 합니다. 
    - 랩 소유자는 랩 OS 디스크를 암호화하는 데 사용될 디스크 암호화 집합에 대해 최소한 **읽기 권한자 수준 액세스** 가 있는지 확인합니다. 
1. 2020년 8월 1일 이전에 생성된 랩의 경우 랩 소유자는 랩 시스템 할당 ID가 사용하도록 설정되었는지 확인해야 합니다. 이렇게 하려면 랩 소유자는 랩으로 이동하고, **구성 및 정책** 을 클릭하고, **ID(미리 보기)** 블레이드를 클릭하고, 시스템 할당 ID **상태** 를 **켜짐** 으로 변경하고, **저장** 을 클릭합니다. 2020년 8월 1일 이후 생성된 새 랩의 경우 랩의 시스템 할당 ID는 기본적으로 사용하도록 설정됩니다. 

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/encrypt-disks-customer-managed-keys/managed-keys.png" alt-text="관리 키":::
1. 랩이 모든 랩 OS 디스크에 대한 암호화를 처리하려면 랩 소유자가 디스크 암호화 집합에 대한 랩의 **시스템 할당 ID** 읽기 권한자 역할과 기본 Azure 구독에 대한 가상 머신 참가자 역할을 명시적으로 부여해야 합니다. 랩 소유자는 다음 단계를 완료하여 이 작업을 수행할 수 있습니다.

   
    1. Azure 리소스에 대한 사용자 액세스를 관리할 수 있도록 Azure 구독 수준에서 [사용자 액세스 관리자 역할](../role-based-access-control/built-in-roles.md#user-access-administrator)의 멤버여야 합니다. 
    1. **디스크 암호화 집합** 페이지의 왼쪽 메뉴에서 **액세스 제어(IAM)** 를 선택합니다. 
    1. 도구 모음에서 **+ 추가** 를 선택하고 **역할 할당 추가** 를 선택합니다.  

        :::image type="content" source="./media/encrypt-disks-customer-managed-keys/add-role-management-menu.png" alt-text="역할 관리 추가 - 메뉴":::
    1. **역할 할당 추가** 페이지에서 **읽기 권한자** 역할 또는 추가 액세스를 허용하는 역할을 선택합니다. 
    1. 디스크 암호화 집합이 사용될 랩 이름을 입력하고 드롭다운 목록에서 랩 이름(랩에 대한 시스템 할당 ID)을 선택합니다. 
    
        :::image type="content" source="./media/encrypt-disks-customer-managed-keys/select-lab.png" alt-text="랩의 시스템 관리 ID 선택":::        
    1. 도구 모음에서 **저장** 을 선택합니다. 

        :::image type="content" source="./media/encrypt-disks-customer-managed-keys/save-role-assignment.png" alt-text="역할 할당 저장":::
3. **구독** -> **액세스 제어(IAM)** 페이지를 사용하여 랩의 **시스템 할당 ID** 를 **가상 머신 참가자** 역할에 추가합니다. 이 단계는 이전 단계의 작업과 유사합니다. 

    
    1. Azure Portal의 **구독** 페이지로 이동합니다. 
    1. **액세스 제어(IAM)** 를 선택합니다. 
    1. 도구 모음에서 **+ 추가** 를 선택하고 **역할 할당 추가** 를 선택합니다. 
    
        :::image type="content" source="./media/encrypt-disks-customer-managed-keys/subscription-access-control-page.png" alt-text="구독 -> 액세스 제어(IAM) 페이지":::
    1. **역할 할당 추가** 페이지에서 역할에 **가상 머신 참가자** 를 선택합니다.
    1. 랩 이름을 입력하고 드롭다운 목록에서 **랩 이름**(랩에 대한 시스템 할당 ID)을 선택합니다. 
    1. 도구 모음에서 **저장** 을 선택합니다. 

## <a name="encrypt-lab-os-disks-with-a-customer-managed-key"></a>고객 관리형 키를 사용하여 랩 OS 디스크 암호화 

1. Azure Portal의 랩 홈페이지에서 왼쪽 메뉴의 **구성 및 정책** 을 선택합니다. 
1. **구성 및 정책** 페이지의 **암호화** 섹션에서 **디스크(미리 보기)** 를 선택합니다. 기본적으로 **암호화 유형** 은 **플랫폼 관리 키를 사용하여 미사용 암호화** 로 설정됩니다.

    :::image type="content" source="./media/encrypt-disks-customer-managed-keys/disks-page.png" alt-text="구성 및 정책 페이지의 디스크 탭":::
1. **암호화 유형** 의 경우 드롭다운 목록에서 **고객 관리형 키를 사용하여 미사용 암호화** 를 선택합니다. 
1. **디스크 암호화 설정** 에 대해 이전에 만든 디스크 암호화 집합을 선택합니다. 랩의 시스템 할당 ID가 액세스할 수 있는 것과 동일한 디스크 암호화 집합입니다.
1. 도구 모음에서 **저장** 을 선택합니다. 

    :::image type="content" source="./media/encrypt-disks-customer-managed-keys/disk-encryption-set.png" alt-text="고객 관리형 키로 암호화 사용":::
1. *이 설정은 랩에서 새로 생성된 머신에 적용됩니다. 이전 OS 디스크는 이전 디스크 암호화 집합으로 암호화된 상태로 유지됩니다* 텍스트가 있는 메시지 상자에 **확인** 을 선택합니다. 

    구성이 완료되면 랩 OS 디스크는 디스크 암호화 집합을 사용하여 제공된 고객 관리형 키로 암호화됩니다. 
   
## <a name="how-to-validate-if-disks-are-being-encrypted"></a>디스크가 암호화되는지 유효성을 검사하는 방법

1. 랩에서 고객 관리형 키를 사용하여 디스크 암호화를 사용하도록 설정한 후 생성한 랩 가상 머신으로 이동합니다.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/encrypt-disks-customer-managed-keys/enabled-encryption-vm.png" alt-text="디스크 암호화를 사용하도록 설정된 VM":::
1. VM의 리소스 그룹을 클릭하고 OS 디스크를 클릭합니다.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/encrypt-disks-customer-managed-keys/vm-resource-group.png" alt-text="VM 리소스 그룹":::
1. 암호화로 이동하여 선택한 디스크 암호화로 암호화가 고객 관리형 키로 설정되어 있는지 확인합니다.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/encrypt-disks-customer-managed-keys/validate-encryption.png" alt-text="암호화 유효성 검사":::
  
## <a name="next-steps"></a>다음 단계

다음 문서를 참조하세요. 

- [Azure Disk Encryption](../virtual-machines/disk-encryption.md) 
- [고객 관리형 키](../virtual-machines/disk-encryption.md#customer-managed-keys)