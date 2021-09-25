---
title: Azure DevTest Labs 가상 머신에서 브라우저 연결 사용
description: DevTest Labs가 이제 Azure Bastion과 통합됩니다. 랩 소유자는 브라우저를 통해 모든 랩 가상 머신에 액세스하도록 설정할 수 있습니다.
ms.topic: how-to
ms.date: 06/26/2020
ms.openlocfilehash: 7982c878f062e0b8623bde9da6ea585226a455c9
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128624295"
---
# <a name="enable-browser-connection-on-azure-devtest-labs-virtual-machines"></a>Azure DevTest Labs 가상 머신에서 브라우저 연결 사용 
DevTest Labs는 브라우저를 통해 가상 머신에 연결할 수 있도록 하는 [Azure Bastion](../bastion/index.yml)과 통합됩니다. 먼저 랩 가상 머신에서 브라우저 연결을 사용하도록 설정해야 합니다.

랩 소유자는 브라우저를 통해 모든 랩 가상 머신에 액세스하도록 설정할 수 있습니다. 다른 클라이언트, 에이전트 또는 소프트웨어가 필요하지 않습니다. Azure Bastion은 TLS를 통해 Azure Portal에서 직접 가상 머신에 안전하고 원활한 RDP/SSH 연결을 제공합니다. Azure Bastion을 통해 연결하는 경우에는 가상 머신에 공용 IP 주소가 필요하지 않습니다. 자세한 내용은 [Azure Bastion이란?](../bastion/bastion-overview.md)을 참조하세요.


이 문서에서는 랩 가상 머신에서 브라우저 연결을 사용하도록 설정하는 방법을 보여 줍니다.

## <a name="prerequisites"></a>필수 구성 요소 
- 기존 랩의 가상 네트워크에 Bastion 호스트를 배포 **하거나,** Bastion이 구성된 가상 네트워크에 랩을 연결합니다.
가상 네트워크에 Bastion 호스트를 배포하는 방법을 알아보려면 [Azure Bastion 호스트 만들기](../bastion/tutorial-create-host-portal.md)를 참조하세요. Bastion 호스트를 만들 때 랩의 가상 네트워크를 선택합니다. 
- 랩 사용자는 Bastion 호스트와 Bastion이 구성된 가상 네트워크에 대한 **읽기 권한자** 역할이 있어야 합니다. 

## <a name="create-a-second-sub-net-in-the-bastion-virtual-network"></a>Bastion 가상 네트워크에서 두 번째 서브넷 만들기
AzureBastionSubnet에서 Bastion이 아닌 리소스를 만들도록 허용하지 않기 때문에, 먼저 Bastion 가상 네트워크에서 두 번째 서브넷을 만들어야 합니다. 다음 이미지에 표시된 것처럼 Bastion 가상 네트워크 내에 다른 서브넷을 만듭니다.

![Azure Bastion 가상 네트워크의 두 번째 서브넷](./media/connect-virtual-machine-through-browser/second-subnet.png)

## <a name="enable-vm-creation-in-the-subnet"></a>서브넷에서 VM 만들기를 사용하도록 설정
이제 다음 단계를 수행하여 이 서브넷에서 VM을 만들 수 있습니다. 

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. 왼쪽 탐색 메뉴에서 **모든 서비스** 를 선택합니다. 
1. 목록에서 **DevTest Labs** 를 선택합니다. 
1. 랩 목록에서 *랩* 을 선택합니다. 

    > [!NOTE]
    > Azure Bastion은 현재 미국 서부, 미국 동부, 유럽 서부, 미국 중부, 오스트레일리아 동부 및 일본 동부 지역에서 일반적으로 사용할 수 있습니다. 따라서 이러한 지역 중 하나에 랩이 없는 경우 해당 지역 중 하나에서 랩을 만듭니다. 
    
1. 왼쪽 메뉴의 **설정** 섹션에서 **구성 및 정책** 을 선택합니다. 
1. **가상 네트워크** 를 선택합니다.
1. 도구 모음에서 **추가** 를 선택합니다. 
1. Bastion 호스트가 배포된 **가상 네트워크** 를 선택합니다. 
1. **AzureBastionSubnet** 이 아닌 이전에 만든 다른 VM에 대한 서브넷을 선택합니다. 페이지를 닫고 아래에 있는 목록에 서브넷이 표시되지 않으면 다시 엽니다. 

    ![서브넷에서 VM 만들기를 사용하도록 설정](./media/connect-virtual-machine-through-browser/enable-vm-creation-subnet.png)
1. **가상 머신 만들기에 사용** 옵션을 선택합니다. 
1. 도구 모음에서 **저장** 을 선택합니다. 
1. 랩에 대한 이전 가상 네트워크가 있는 경우 * *...* 및 **제거** 를 선택하여 제거합니다. 

## <a name="enable-browser-connection"></a>브라우저를 연결하도록 설정 

랩 내부에 Bastion이 구성된 가상 네트워크가 있으면 랩 소유자로서 랩 가상 머신에서 브라우저를 연결하도록 설정할 수 있습니다.

랩 가상 머신에서 브라우저를 연결하도록 설정하려면 다음 단계를 수행합니다.

1. Azure Portal에서 *랩* 으로 이동합니다.
1. **구성 및 정책** 을 선택합니다.
1. **설정** 에서 **브라우저 연결** 을 선택합니다. 이 옵션이 표시되지 않으면 **구성 정책** 페이지를 닫고 다시 엽니다. 

    ![브라우저를 연결하도록 설정](./media/enable-browser-connection-lab-virtual-machines/browser-connect.png)

## <a name="next-steps"></a>다음 단계
브라우저를 사용하여 VM에 연결하는 방법을 알아보려면 [브라우저를 통해 가상 머신에 연결](connect-virtual-machine-through-browser.md) 문서를 참조하세요.