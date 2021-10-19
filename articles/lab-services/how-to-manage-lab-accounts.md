---
title: Azure Lab Services의 랩 계정 관리 | Microsoft Docs
description: Azure 구독에서 랩 계정을 만들거나 랩 계정을 모두 보거나 랩 계정을 삭제하는 방법을 알아봅니다.
ms.topic: how-to
ms.date: 06/26/2020
ms.openlocfilehash: 4de1948ecc240afecb658427975fc59228f4c82e
ms.sourcegitcommit: 92889674b93087ab7d573622e9587d0937233aa2
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/19/2021
ms.locfileid: "130180289"
---
# <a name="create-and-manage-lab-accounts"></a>랩 계정 만들기 및 관리
Azure Lab Services에서 랩 계정은 랩과 같은 관리 랩 형식의 컨테이너입니다. 관리자는 Azure Lab Services를 사용하여 랩 계정을 설정하고 계정에 랩을 만들 수 있는 랩 소유자에게 액세스 권한을 제공합니다. 이 문서는 랩 계정을 만들거나, 모든 랩 계정을 보거나, 랩 계정을 삭제하는 방법을 설명합니다.

## <a name="create-a-lab-account"></a>랩 계정 만들기
다음 단계는 Azure Portal을 사용하여 Azure DevTest Labs로 랩 계정을 만드는 방법을 설명합니다. 

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
2. 왼쪽 메뉴에서 **모든 서비스** 를 선택합니다. **DevOps** 섹션에서 **랩 계정** 을 선택합니다. **랩 계정** 옆에 있는 별표(`*`)를 선택하여 왼쪽 메뉴의 **즐겨찾기** 섹션에 추가합니다. 다음부터는 **즐겨찾기** 아래에서 **랩 계정** 을 선택합니다.

    ![모든 서비스 -> 랩 계정](./media/tutorial-setup-lab-account/select-lab-accounts-service.png)
3. **랩 계정** 페이지의 도구 모음에서 **추가** 를 선택하거나 페이지에서 **랩 계정 만들기** 를 선택합니다. 

    ![랩 계정 페이지에서 추가 선택](./media/tutorial-setup-lab-account/add-lab-account-button.png)
4. **랩 계정 만들기** 페이지의 **기본 사항** 탭에서 다음 작업을 수행합니다. 
    1. **랩 계정 이름** 에 이름을 입력합니다. 
    2. 랩 계정을 만들려는 **Azure 구독** 을 선택합니다.
    3. **리소스 그룹** 의 경우 **새로 만들기** 를 선택하고 리소스 그룹의 이름을 입력합니다.
    4. **위치** 에서 랩 계정을 만들 위치/지역을 선택합니다.
    5. **랩 작성자가 랩 위치를 선택하도록 허용** 필드에서 랩 작성자가 랩의 위치를 선택할 수 있도록 할지 여부를 지정합니다. 이 옵션은 기본적으로 '사용 안 함'으로 설정되어 있습니다. 이 기능이 '사용 안 함'으로 설정되면 랩 작성자가 만들고 있는 랩의 위치를 지정할 수 없습니다. 랩은 랩 계정과 가장 가까운 지리적 위치에 만들어집니다. 이 기능이 '사용'으로 설정되면 랩 작성자가 랩을 만들 때 위치를 선택할 수 있습니다. 자세한 내용은 [랩 작성자가 랩의 위치를 선택하도록 허용](allow-lab-creator-pick-lab-location.md)을 참조하세요. 

        ![랩 계정 만들기 -> 기본 사항](./media/how-to-manage-lab-accounts/create-lab-account-basics.png)
5. 완료되면 **다음: 고급** 을 선택하여 **고급** 탭으로 이동한 후, 다음 단계를 수행합니다. 
    1. 기존 **공유 이미지 갤러리** 를 선택하거나 새로 만듭니다. 다른 사용자가 다시 사용할 수 있도록 템플릿 VM을 공유 이미지 갤러리에 저장할 수 있습니다. 공유 이미지 갤러리에 대한 자세한 내용은 [Azure Lab Services에서 공유 이미지 갤러리 사용](how-to-use-shared-image-gallery.md)을 참조하세요.
    2. 사용자가 연결을 끊을 때 **Windows 가상 머신을 자동으로 종료** 할지 여부를 지정합니다. 가상 머신이 자동으로 종료되기 전에 사용자가 다시 연결될 때까지 대기해야 하는 기간을 지정합니다. 
    3. **가상 네트워크 피어 지정** 에서 랩 네트워크에 대한 VNet(가상 네트워크) 피어 지정을 선택합니다. 이 계정에서 생성된 랩은 선택한 VNet에 연결되어 있으며 선택한 VNet의 리소스에 액세스할 수 있습니다. 자세한 내용은 [랩의 가상 네트워크를 피어 가상 네트워크에 연결](how-to-connect-peer-virtual-network.md)을 참조하세요.    
    8. 랩의 VM에 대해 **주소 범위** 를 지정합니다. 주소 범위는 CIDR(Classless Inter-Domain Routing) 표기법을 따라야 합니다(예: 10.20.0.0/23). 랩의 가상 머신은 이 주소 범위에서 만들어집니다. 자세한 내용은 [랩의 VM에 대해 주소 범위 지정](how-to-connect-peer-virtual-network.md#specify-an-address-range-for-vms-in-the-lab-account)을 참조하세요.  

        > [!NOTE]
        > **주소 범위** 속성은 랩에 **피어 가상 네트워크** 를 사용하도록 설정한 경우에만 적용됩니다.

        ![랩 계정 만들기 -> 고급](./media/how-to-manage-lab-accounts/create-lab-account-advanced.png)  
6. 완료되면 **다음: 태그** 를 선택하여 **태그** 탭으로 전환합니다. 랩 계정에 연결하려는 태그를 추가합니다. 태그는 동일한 태그를 여러 개의 리소스 및 리소스 그룹에 적용하여 리소스를 범주화하고 통합된 청구를 볼 수 있는 이름/값 쌍입니다. 자세한 내용은 [태그를 사용하여 Azure 리소스 구성](../azure-resource-manager/management/tag-resources.md)을 참조하세요.

    ![태그 탭이 강조 표시된 "랩 계정 생성하기" 페이지를 보여주는 스크린샷.](./media/how-to-manage-lab-accounts/create-lab-account-tags.png)
7. 이 페이지 하단에 있는 **검토 + 만들기** 를 선택하여 **검토 + 만들기** 탭으로 전환합니다. 
4. 이 페이지에서 요약 정보를 검토하고 **만들기** 를 선택합니다. 

    ![랩 계정 만들기 -> 태그](./media/how-to-manage-lab-accounts/create-lab-account-review-create.png)
5. 배포가 완료될 때까지 기다렸다가 **다음 단계** 를 확장하고 다음 이미지와 같이 **리소스로 이동** 을 선택합니다. 

    도구 모음의 **종 모양 아이콘**(**알림**)을 선택하고 배포가 성공했는지 확인한 다음, **리소스로 이동** 을 선택할 수도 있습니다. 

    또는 **랩 계정** 페이지에서 **새로 고침** 을 선택하고 생성된 랩 계정을 선택합니다. 

    ![랩 계정 만들기 창](./media/tutorial-setup-lab-account/go-to-lab-account.png)    
6. 다음과 같은 **랩 계정** 페이지가 표시됩니다.

    ![랩 계정 페이지](./media/tutorial-setup-lab-account/lab-account-page.png)

## <a name="view-lab-accounts"></a>랩 계정 보기
1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
2. 메뉴에서 **모든 리소스** 를 선택합니다. 
3. **형식** 에 대한 **랩 계정** 을 선택합니다. 
    구독, 리소스 그룹, 위치 및 태그별로 필터링할 수도 있습니다. 

    ![모든 리소스 -> 랩 계정](./media/how-to-manage-lab-accounts/all-resources-lab-accounts.png)


## <a name="delete-a-lab-account"></a>랩 계정 삭제
랩 계정을 목록으로 표시하는 이전 섹션의 지침을 따릅니다. 다음 지침에 따라 랩 계정을 삭제합니다. 

1. 삭제하려는 **랩 계정** 을 선택합니다. 
2. 도구 모음에서 **삭제** 를 선택합니다. 

    ![랩 계정 -> 삭제 단추](./media/how-to-manage-lab-accounts/delete-button.png)
1. 확인을 위해 **예** 를 입력합니다.
1. **삭제** 를 선택합니다. 

    ![랩 계정 삭제 - 확인](./media/how-to-manage-lab-accounts/delete-lab-account-confirmation.png)

> [!NOTE]
> Az.LabServices PowerShell 모듈(미리 보기)을 사용하여 랩 계정을 관리할 수도 있습니다. 자세한 내용은 [GitHub의 Az.LabServices 홈페이지](https://github.com/Azure/azure-devtestlab/tree/master/samples/ClassroomLabs/Modules/Library)를 참조하세요.

## <a name="next-steps"></a>다음 단계
TOC(목차)의 **방법 가이드** -> **랩 계정 만들기 및 구성(랩 계정 소유자)** 섹션에 있는 다른 문서를 참조하세요. 