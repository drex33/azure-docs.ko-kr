---
title: 자습서 - 프라이빗 클라우드에 액세스
description: Azure VMware Solution 프라이빗 클라우드에 액세스하는 방법 알아보기
ms.topic: tutorial
ms.date: 08/13/2021
ms.openlocfilehash: 6b4798bf5257be82475986c040b04b63ff5a483b
ms.sourcegitcommit: e7d500f8cef40ab3409736acd0893cad02e24fc0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122070793"
---
# <a name="tutorial-access-an-azure-vmware-solution-private-cloud"></a>자습서: Azure VMware Solution 프라이빗 클라우드에 액세스

Azure VMware Solution을 사용하면 온-프레미스 vCenter에서 프라이빗 클라우드를 관리할 수 없습니다. 대신 점프 상자를 통해 Azure VMware Solution vCenter 인스턴스에 연결해야 합니다. 

이 자습서에서는 [이전 자습서](tutorial-configure-networking.md)에서 만든 리소스 그룹에 점프 상자를 만들고 Azure VMware Solution vCenter에 로그인합니다. 이 점프 상자는 만든 것과 동일한 가상 네트워크에 있는 Windows VM(가상 머신)입니다.  vCenter와 NSX Manager 모두에 액세스할 수 있습니다. 

이 자습서에서는 다음 작업 방법을 알아봅니다.

> [!div class="checklist"]
> * Azure VMware Solution vCenter에 액세스하기 위한 Windows VM 만들기
> * 이 VM에서 vCenter에 로그인

## <a name="create-a-new-windows-virtual-machine"></a>새 Windows 가상 머신 만들기

1. 리소스 그룹에서 **추가** 를 선택하고 **Microsoft Windows 10** 을 검색하여 선택합니다. 그런 다음 **생성** 를 선택합니다.

   :::image type="content" source="media/tutorial-access-private-cloud/ss8-azure-w10vm-create.png" alt-text="점프 상자에 새 Windows 10 VM을 추가하는 방법의 스크린샷":::

1. 필드에서 필요한 정보를 입력한 다음, **검토 + 만들기** 를 선택합니다. 

   필드에 대한 자세한 내용은 다음 표를 참조하세요.

   | 필드 | 값 |
   | --- | --- |
   | **구독** | 값은 리소스 그룹에 속하는 구독으로 미리 채워져 있습니다. |
   | **리소스 그룹** | 값은 이전 자습서에서 만든 현재 리소스 그룹에 대해 미리 채워져 있습니다.  |
   | **가상 머신 이름** | VM에 대한 고유한 이름을 입력합니다. |
   | **지역** | VM의 지리적 위치를 선택합니다. |
   | **가용성 옵션** | 선택된 기본값을 그대로 둡니다. |
   | **이미지** | VM 이미지를 선택합니다. |
   | **크기** | 기본 크기 값을 그대로 둡니다. |
   | **인증 유형**  | **암호** 를 선택합니다. |
   | **사용자 이름** | VM에 로그온하기 위한 사용자 이름을 입력합니다. |
   | **암호** | VM에 로그온하기 위한 암호를 입력합니다. |
   | **암호 확인** | VM에 로그온하기 위한 암호를 입력합니다. |
   | **퍼블릭 인바운드 포트** | **없음** 을 선택합니다. <ul><li>VM에 액세스하려는 경우에만 VM에 대한 액세스를 제어하려면 [JIT 액세스](../security-center/security-center-just-in-time.md#jit-configure)를 사용합니다.</li><li>네트워크 포트를 노출하지 않고 인터넷에서 안전하게 점프 상자 서버에 액세스하려면 [Azure Bastion](../bastion/tutorial-create-host-portal.md)을 사용합니다.</li></ul>  |


1. 유효성 검사가 통과되면 **만들기** 를 선택하여 가상 머신 만들기 프로세스를 시작합니다.

## <a name="connect-to-the-local-vcenter-of-your-private-cloud"></a>프라이빗 클라우드의 로컬 vCenter에 연결

1. 점프 상자에서 클라우드 관리자 사용자 이름을 사용하여 VMware vCenter SSO로 vSphere 클라이언트에 로그인하고 사용자 인터페이스가 성공적으로 표시되는지 확인합니다.

1. Azure Portal에서 프라이빗 클라우드를 선택한 다음, **관리** > **ID** 를 선택합니다. 

   프라이빗 클라우드 vCenter 및 NSX-T Manager에 대한 URL 및 사용자 자격 증명이 표시됩니다.

   :::image type="content" source="media/tutorial-access-private-cloud/ss4-display-identity.png" alt-text="프라이빗 클라우드 vCenter와 NSX Manager URL 및 자격 증명을 보여 주는 스크린샷" lightbox="media/tutorial-access-private-cloud/ss4-display-identity.png":::

1. 이전 단계에서 만든 VM으로 이동하여 가상 머신에 연결합니다. 

   VM에 연결하는 데 도움이 필요한 경우 자세한 내용은 [가상 머신에 연결](../virtual-machines/windows/connect-logon.md#connect-to-the-virtual-machine)을 참조하세요.

1. Windows VM에서 브라우저를 열고 두 개의 탭에서 vCenter 및 NSX-T Manager URL로 이동합니다. 

1. vCenter 탭에서 이전 단계의 `cloudadmin@vsphere.local` 사용자 자격 증명을 입력합니다.

   :::image type="content" source="media/tutorial-access-private-cloud/ss5-vcenter-login.png" alt-text="VMware vSphere 로그인 페이지를 보여 주는 스크린샷" border="true":::

   :::image type="content" source="media/tutorial-access-private-cloud/ss6-vsphere-client-home.png" alt-text="vSphere Client의 클러스터-1의 요약을 보여 주는 스크린샷" border="true":::

1. 브라우저의 두 번째 탭에서 NSX-T Manager에 로그인합니다.

   :::image type="content" source="media/tutorial-access-private-cloud/ss10-nsx-manager-home.png" alt-text="NSX-T Manager 개요 스크린샷" border="true":::



## <a name="next-steps"></a>다음 단계

본 자습서에서는 다음 작업에 관한 방법을 학습했습니다.

> [!div class="checklist"]
> * vCenter에 연결하는 데 사용할 Windows VM 만들기
> * VM에서 vCenter에 로그인

가상 네트워크를 만들어 프라이빗 클라우드 클러스터에 대한 로컬 관리를 설정하는 방법을 알아보려면 다음 자습서를 계속 진행합니다.

> [!div class="nextstepaction"]
> [Virtual Network 만들기](tutorial-configure-networking.md)

