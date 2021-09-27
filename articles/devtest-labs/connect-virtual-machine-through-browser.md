---
title: 브라우저를 통해 가상 머신에 연결
description: 브라우저를 통해 가상 머신에 연결하는 방법을 알아봅니다.
ms.topic: how-to
ms.date: 06/26/2020
ms.openlocfilehash: dbbaa4286aac91e362a2024f1705add8f48d566d
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128645002"
---
# <a name="connect-to-your-virtual-machines-through-a-browser"></a>브라우저를 통해 가상 머신에 연결 

DevTest Labs는 브라우저를 통해 가상 머신에 연결할 수 있도록 하는 [Azure Bastion](../bastion/index.yml)과 통합됩니다. DevTest Labs에서 이 기능을 사용하도록 설정하는 방법에 대해 알아보려면 [랩 가상 머신에서 브라우저 연결 사용](enable-browser-connection-lab-virtual-machines.md)을 참조하세요.

*브라우저 연결* 을 사용하도록 설정하면 랩 사용자는 브라우저를 통해 가상 머신에 액세스할 수 있습니다.  

## <a name="create-a-lab-virtual-machine"></a>랩 가상 머신 만들기

먼저 Bastion이 구성된 가상 네트워크 내에 랩 가상 머신을 만들어야 합니다. 사용자가 만든 두 번째 **서브넷** 을 선택합니다. 이는 AzureBastionSubnet이 아니어야 합니다. **고급 설정** 탭으로 이동하여 가상 머신을 만드는 동안 가상 네트워크를 선택할 수 있습니다.

![가상 머신 만들기](./media/connect-virtual-machine-through-browser/create-virtual-machine.png)

## <a name="launch-virtual-machine-in-a-browser"></a>브라우저에서 가상 머신 시작

가상 머신을 만든 후 *브라우저 연결* 단추를 클릭하고 머신에 대한 사용자 이름 및 암호를 입력하여 브라우저에서 가상 머신을 시작할 수 있습니다.  

![브라우저에서 시작](./media/connect-virtual-machine-through-browser/browser-connect.png)

## <a name="next-steps"></a>다음 단계

[Azure DevTest Labs에서 랩에 VM 추가](devtest-lab-add-vm.md)
