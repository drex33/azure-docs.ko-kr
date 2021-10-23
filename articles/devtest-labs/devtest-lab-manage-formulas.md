---
title: Azure DevTest Labs 수식을 관리하여 VM 만들기
description: 이 문서에서는 기본(사용자 지정 이미지, Marketplace 이미지 또는 다른 수식) 또는 기존 VM에서 수식을 만드는 방법을 보여 줍니다.
ms.topic: how-to
ms.date: 06/26/2020
ms.openlocfilehash: 6aea6af48344e6dfbdb811281e4ca0e8203968d3
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130226547"
---
# <a name="manage-azure-devtest-labs-formulas"></a>Azure DevTest Labs 수식 관리

[!INCLUDE [devtest-lab-formula-definition](../../includes/devtest-lab-formula-definition.md)]

이 문서에서는 기본(사용자 지정 이미지, Marketplace 이미지 또는 다른 수식) 또는 기존 VM에서 수식을 만드는 방법을 보여 줍니다. 이 문서에서 기존 수식을 관리하는 방법을 안내합니다.

## <a name="create-a-formula"></a>수식 만들기
DevTest Lab *사용자* 권한이 있으면 수식을 기준으로 사용하여 VM을 만들 수 있습니다. 수식을 만드는 방법은 두 가지입니다. 

* 기준에서 - 수식의 모든 특성을 정의하려는 경우에 사용합니다.
* 기존 랩 VM을 통해 - 기존 VM의 설정을 기반으로 수식을 만들려는 경우에 사용합니다.

사용자 및 사용 권한을 추가하는 방법에 대한 자세한 내용은 [Azure DevTest Labs에 소유자 및 사용자 추가](./devtest-lab-add-devtest-user.md)를 참조하세요.

### <a name="create-a-formula-from-a-base"></a>기준에서 수식 만들기
다음 단계에서는 사용자 지정 이미지, Marketplace 이미지 또는 다른 수식에서 수식을 만드는 과정을 안내합니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.

2. **모든 서비스** 를 선택한 다음, 목록에서 **DevTest Labs** 를 선택합니다.

3. 랩 목록에서 원하는 랩을 탭합니다.  

4. 랩의 페이지의 왼쪽 메뉴에서 **수식(재사용 가능 기본)** 을 선택합니다.
5. **수식** 페이지에서 **+ 추가** 를 선택합니다.
   
    ![수식 추가](./media/devtest-lab-create-formulas/add-formula.png)
6. **기본 선택** 페이지에서 만들려는 수식의 기본 원본(사용자 지정 이미지, Marketplace 이미지 또는 Shared Image Gallery 이미지)을 선택합니다.

    :::image type="content" source="./media/devtest-lab-create-formulas/select-base.png" alt-text="기본 이미지 선택":::
1. **수식 만들기** 페이지의 **기본 설정** 탭에서 다음 값을 지정합니다.
   
    * **수식 이름** - 수식의 이름을 입력합니다. 이 값은 VM을 만들 때 기본 이미지 목록에 표시됩니다. 이름 입력 시에 유효성이 검사되고, 유효하지 않으면, 유효한 이름에 필요한 사항을 알려주는 메시지가 표시됩니다.
    - 수식에 대한 선택적 **설명** 을 입력합니다. 
    * **사용자 이름** - 관리자 권한이 부여된 사용자 이름을 입력합니다.
    * **암호** - 지정된 사용자에 대해 사용하려는 암호와 연결된 값을 입력하거나 드롭다운 목록에서 선택합니다. 키 자격 증명 모음에 비밀을 저장하고 랩 리소스를 만들 때 이 비밀을 사용하는 방법을 알아보려면 [Azure Key Vault에 비밀 저장](devtest-lab-store-secrets-in-key-vault.md)을 참조하세요.

        암호를 사용하는 대신 Azure Key Vault의 비밀을 사용하려면 **저장된 비밀 사용** 을 선택합니다. 
    * **가상 머신 크기** - VM의 크기를 변경하려면 **크기 변경** 을 선택합니다. 
    - **OS 디스크 유형** -사용하려는 디스크 유형(표준 HDD, 표준 SSD 또는 프리미엄 SSD)을 선택합니다.
    * **아티팩트** - **아티팩트 추가 또는 제거** 페이지를 선택합니다. 여기서 기본 이미지에 추가하려는 아티팩트를 선택하고 구성합니다. 아티팩트에 대한 자세한 내용은 [Azure DevTest Labs 가상 머신에 대한 사용자 지정 아티팩트 만들기](devtest-lab-artifact-author.md)를 참조하세요.

        ![기본 설정 페이지](./media/devtest-lab-create-formulas/basic-settings.png)
8. **고급 설정** 탭으로 전환하고 다음 값을 지정합니다.
    - **가상 네트워크** - 가상 네트워크를 변경하려면 **Vnet 변경** 을 선택합니다. 
    - **서브넷** - 서브넷을 변경하려면 **서브넷 변경** 을 선택합니다. 
    - **IP 주소 구성** - 공용, 프라이빗 또는 공유 IP 주소를 원하는 경우 지정합니다. 공유 IP 주소에 대한 자세한 내용은 [Azure DevTest Labs에서 공유 IP 주소 이해](./devtest-lab-shared-ip.md)를 참조하세요.
    - **만료 날짜 및 시간** -이 필드는 편집할 수 없습니다. 
    - **Make this machine claimable**(이 컴퓨터를 클레임 가능하도록 지정) - 컴퓨터를 "클레임 가능"하도록 지정하는 것은 생성 시 소유권을 할당하지 않는다는 것을 의미합니다. 대신 랩 사용자는 랩의 페이지에서 머신에 대한 소유권("클레임")을 가져올 수 있습니다.  

        ![‘수식 만들기(재사용 가능 기본)’ 페이지에 대한 고급 설정을 보여 주는 스크린샷입니다.](./media/devtest-lab-create-formulas/advanced-settings.png)
    - Shared Image Gallery 이미지를 기본으로 선택한 경우 갤러리에서 기본으로 사용할 이미지의 버전을 선택할 수 있는 **이미지 버전** 필드도 표시됩니다. 

        ![고급 설정 페이지](./media/devtest-lab-create-formulas/advanced-settings-shared-image-gallery.png)
8. **제출** 을 선택하여 수식을 만듭니다.

9. 수식이 만들어지면, **수식** 페이지의 목록에 표시됩니다.

### <a name="create-a-formula-from-a-vm"></a>VM에서 수식 만들기
다음 단계는 기존 VM을 기반으로 수식을 만드는 과정을 안내합니다. 

> [!NOTE]
> VM에서 수식을 만들려면 2016년 3월 30일 이후에 VM을 만들었어야 합니다. 
> 
> 

1. [Azure Portal](https://go.microsoft.com/fwlink/p/?LinkID=525040)에 로그인합니다.
2. **모든 서비스** 를 선택한 다음, 목록에서 **DevTest Labs** 를 선택합니다.
3. 랩 목록에서 원하는 랩을 탭합니다.  
4. 랩의 **개요** 페이지에서 만들려는 수식의 원본인 VM을 선택합니다.
   
    ![랩 VM](./media/devtest-lab-create-formulas/my-vms.png)
5. VM의 페이지에서 **수식 만들기(재사용 가능 기본)** 를 선택합니다.
   
    ![Create formula](./media/devtest-lab-create-formulas/create-formula-menu.png)
6. **수식 만들기** 페이지에서 새 수식에 대한 **이름** 및 **설명** 을 입력합니다.
   
    ![수식 만들기 페이지](./media/devtest-lab-create-formulas/create-formula-blade.png)
7. **확인** 을 선택하여 수식을 만듭니다.

## <a name="modify-a-formula"></a>수식 수정
수식을 수정하려면 다음 단계를 수행합니다.

1. [Azure Portal](https://go.microsoft.com/fwlink/p/?LinkID=525040)에 로그인합니다.
2. **모든 서비스** 를 선택한 다음, 목록에서 **DevTest Labs** 를 선택합니다.
3. 랩 목록에서 원하는 랩을 탭합니다.  
4. 랩의 페이지에서 **수식(재사용 가능 기본)** 을 선택합니다.
   
    ![‘수식(재사용 가능 기본)’이 선택된 랩 페이지를 보여 주는 스크린샷입니다.](./media/devtest-lab-manage-formulas/lab-settings-formulas.png)
5. **랩 수식** 페이지에서 수정하려는 수식을 선택합니다.
6. **수식 업데이트** 페이지에서 원하는 내용을 편집하고 **업데이트** 를 선택합니다.

## <a name="delete-a-formula"></a>수식 삭제
수식을 삭제하려면 다음 단계를 수행합니다.

1. [Azure Portal](https://go.microsoft.com/fwlink/p/?LinkID=525040)에 로그인합니다.
2. **모든 서비스** 를 선택한 다음, 목록에서 **DevTest Labs** 를 선택합니다.
3. 랩 목록에서 원하는 랩을 탭합니다.  
4. 랩의 **설정** 페이지에서 **수식** 을 선택합니다.
   
    ![수식 메뉴](./media/devtest-lab-manage-formulas/lab-settings-formulas.png)
5. **랩 수식** 페이지에서 삭제하려는 수식의 오른쪽에 있는 줄임표를 클릭합니다.
   
    ![수식 옵션에 대한 줄임표가 강조 표시된 ‘랩 수식’ 페이지를 보여 주는 스크린샷입니다.](./media/devtest-lab-manage-formulas/lab-formulas-blade.png)
6. 수식의 상황에 맞는 메뉴에서 **삭제** 를 선택합니다.
   
    ![수식 상황에 맞는 메뉴](./media/devtest-lab-manage-formulas/formula-delete-context-menu.png)
7. 삭제 확인 대화 상자에서 **예** 를 탭합니다.

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="related-blog-posts"></a>관련 블로그 게시물
* [사용자 지정 이미지 또는 수식?](./devtest-lab-faq.yml)

## <a name="next-steps"></a>다음 단계
VM을 만들 때 사용할 수식을 만들었으면 다음 단계는 [VM을 랩에 추가](devtest-lab-add-vm.md)하는 것입니다.
