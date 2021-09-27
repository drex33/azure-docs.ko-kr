---
author: cherylmc
ms.author: cherylmc
ms.date: 08/30/2021
ms.service: virtual-wan
ms.topic: include
ms.openlocfilehash: ebaa86a60ae11c50658bfcfe58bbd0184bf15576
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128705684"
---
1. [Azure Portal](https://portal.azure.com)에서 연결하려는 가상 머신으로 이동합니다. **개요** 페이지에서 **연결** 을 선택한 다음, 드롭다운에서 **Bastion** 을 선택합니다.

   :::image type="content" source="./media/bastion-vm-rdp/connect.png" alt-text="커넥트 스크린샷." lightbox="./media/bastion-vm-rdp/connect.png":::

1. Bastion을 선택한 후 **Bastion 사용을** 선택합니다. 가상 네트워크에 대해 Bastion을 프로비전하지 않은 경우 [Bastion 구성](../articles/bastion/quickstart-host-portal.md)을 참조하세요.

   :::image type="content" source="./media/bastion-vm-rdp/select-use-bastion.png" alt-text="Bastion 사용을 보여주는 스크린샷.":::

1. Azure Bastion **사용하여 커넥트** 페이지에서 **연결 설정** 섹션을 확장하고 **RDP** 를 선택합니다. 표준 RDP 포트(3389)와 다른 인바운드 포트를 사용하려는 경우 **포트** 를 입력합니다.

   :::image type="content" source="./media/bastion-connect-vm-rdp-linux/connection-settings.png" alt-text="포트를 보여주는 스크린샷." lightbox="./media/bastion-connect-vm-rdp-linux/connection-settings.png":::

1. 사용자 **이름** 및 **암호** 를 입력한 **다음, 커넥트** 선택하여 VM에 연결합니다.

   :::image type="content" source="./media/bastion-connect-vm-rdp-linux/username-password.png" alt-text="커넥트 클릭하는 것을 보여주는 스크린샷." lightbox="./media/bastion-connect-vm-rdp-linux/username-password.png":::

1. 이 가상 머신에 대한 RDP 연결은 포트 443 및 Bastion 서비스를 사용하여 Azure Portal에서 직접 열립니다(HTML5를 통해). 

   VM에 연결된 상태에서 바로 가기 키를 사용하면 로컬 컴퓨터의 바로 가기 키와 동일한 동작이 수행되지 않을 수 있습니다. 예를 들어 Windows VM에 연결된 Windows 클라이언트 컴퓨터에서 "Ctrl+Alt+End"는 "Ctrl+Alt+Delete"의 바로 가기 키입니다. Windows VM에 연결된 Mac 클라이언트 컴퓨터에서 바로 가기 키는 "Fn+Ctrl+Alt+백스페이스"입니다.