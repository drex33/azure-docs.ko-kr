---
title: Linux 가상 머신에 커넥트
description: 랩(Azure DevTest Labs)에서 Linux 가상 머신에 연결하는 방법을 알아봅니다.
ms.topic: how-to
ms.date: 07/17/2020
ms.openlocfilehash: 956c27235dcdf9d57c908ce2e840e3c77518875b
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128676051"
---
# <a name="connect-to-a-linux-vm-in-your-lab-azure-devtest-labs"></a>랩(Azure DevTest Labs)에서 Linux VM에 연결
이 문서에서는 랩에서 Linux VM에 연결하는 방법을 보여 줍니다. 

## <a name="connect-to-a-linux-vm"></a>Linux VM에 연결
1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. 검색 창에서 **DevTest Labs** 를 검색하여 선택합니다. 

    :::image type="content" source="./media/connect-linux-virtual-machine/search-select.png" alt-text="DevTest Labs 검색 및 선택":::    
1. 랩 목록에서 **랩** 을 선택합니다.

    :::image type="content" source="./media/connect-linux-virtual-machine/select-lab.png" alt-text="랩 선택":::            
1. 해당 랩의 홈페이지에 있는 **내 가상 머신** 목록에서 Linux VM을 선택합니다. 

    :::image type="content" source="./media/connect-linux-virtual-machine/select-linux-vm.png" alt-text="Linux VM 선택":::        
5. **개요** 페이지에서 VM의 FQDN(정규화된 도메인 이름) 또는 IP 주소를 볼 수 있습니다. 다음 이미지에 표시된 것처럼 포트도 볼 수 있습니다.

    :::image type="content" source="./media/connect-linux-virtual-machine/vm-overview.png" alt-text="VM의 정규화된 도메인 이름":::    

    VM이 시작된 경우에도 **연결** 단추는 회색으로 표시됩니다. 이것은 의도된 상황입니다.
6.  SSH를 사용하여 Linux VM에 연결합니다. 다음 예제에서는 사용자 이름이 `vmuser`이고 포트가 `51637`인 FQDN `mydtl07172452621450000.eastus.cloudapp.azure.com`을 사용하여 VM에 연결합니다. 사용자가 VM에 연결하는 데 필요한 암호를 입력합니다. 

    ```bash
    ssh vmuser@mydtl07172452621450000.eastus.cloudapp.azure.com -p 51637
    ```

    [Putty](https://www.putty.org/) 또는 다른 SSH 클라이언트 같은 도구를 사용하여 VM에 연결할 수 있습니다. 

    SSH를 사용하여 연결한 후에는 데스크톱 환경([xfce](https://www.xfce.org)) 및 원격 데스크톱([xrdp](http://xrdp.org))을 설치하고 구성할 수 있습니다.  자세한 내용은 [Azure에서 원격 데스크톱을 설치 및 구성하여 Linux VM에 연결](../virtual-machines/linux/use-remote-desktop.md)을 참조하세요. 

## <a name="next-steps"></a>다음 단계
[방법: Windows VM에 연결](connect-windows-virtual-machine.md)
