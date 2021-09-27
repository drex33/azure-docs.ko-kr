---
title: Windows 가상 머신에 커넥트
description: 랩(Azure DevTest Labs)에서 Windows 가상 머신에 연결하는 방법을 알아봅니다.
ms.topic: how-to
ms.date: 07/17/2020
ms.openlocfilehash: 8fe7b93ce7aa2cf5521bd53393b086e221e84d2c
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128588013"
---
# <a name="connect-to-a-windows-vm-in-your-lab-azure-devtest-labs"></a>랩(Azure DevTest Labs)에서 Windows VM에 연결
이 문서에서는 랩에서 Windows VM에 연결하는 방법을 보여 줍니다. 

## <a name="connect-to-a-windows-vm"></a>Windows VM에 연결
1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. 검색 창에서 **DevTest Labs** 를 검색하여 선택합니다. 

    :::image type="content" source="./media/connect-windows-virtual-machine/search-select.png" alt-text="DevTest Labs 검색 및 선택":::    
1. 랩 목록에서 **랩** 을 선택합니다.

    :::image type="content" source="./media/connect-windows-virtual-machine/select-lab.png" alt-text="랩 선택":::            
1. 해당 랩의 홈페이지에 있는 **내 가상 머신** 목록에서 Windows VM을 선택합니다. 

    :::image type="content" source="./media/connect-windows-virtual-machine/select-windows-vm.png" alt-text="Windows VM 선택":::                
1. VM에 대한 **가상 머신** 페이지의 도구 모음에서 **연결** 을 선택합니다. VM이 중지된 경우 먼저 **시작** 을 선택하여 VM을 시작합니다.

    :::image type="content" source="./media/connect-windows-virtual-machine/select-connect.png" alt-text="도구 모음에서 연결 선택":::                    
1. Edge 브라우저를 사용하는 경우 **다운로드한 RDP 파일** 에 대한 링크가 브라우저 아래쪽에 표시됩니다. 

    :::image type="content" source="./media/connect-windows-virtual-machine/rdp-download.png" alt-text="RDP가 다운로드됨":::                        
1. RDP 파일을 열고 VM을 만들 때 입력한 VM 자격 증명을 입력합니다. 이제 Windows VM에 연결됩니다. 

## <a name="next-steps"></a>다음 단계
[방법: Linux VM에 연결](connect-linux-virtual-machine.md)
