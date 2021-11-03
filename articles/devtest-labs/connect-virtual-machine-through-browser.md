---
title: 랩 가상 머신에 브라우저 액세스 사용
description: 브라우저를 통해 가상 머신에 연결하는 방법을 알아봅니다.
ms.topic: how-to
ms.date: 10/29/2021
ms.openlocfilehash: f712d0090defa28f673807b835dbe3642cfdface
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131464372"
---
# <a name="connect-to-your-lab-virtual-machines-through-a-browser"></a>브라우저를 통해 랩 가상 머신에 커넥트 

DevTest Labs는 [브라우저를](../bastion/index.yml)통해 랩 VM(가상 머신)에 연결할 수 있는 Azure Bastion 와 통합됩니다. **Browser 연결을** 사용하도록 설정하면 랩 사용자가 브라우저를 통해 가상 머신에 액세스할 수 있습니다.  

이 방법 가이드에서는 브라우저 연결을 사용하여 랩 VM에 **연결합니다.**

## <a name="prerequisites"></a>사전 요구 사항

- [Bastion 구성 가상 네트워크 및 **브라우저 연결** 설정이 설정된](enable-browser-connection-lab-virtual-machines.md)랩 VM.

- 에서 팝업을 허용하도록 구성된 웹 `https://portal.azure.com:443` 브라우저입니다.

## <a name="launch-virtual-machine-in-a-browser"></a>브라우저에서 가상 머신 시작

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.

1. **DevTest Labs에서 랩으로** 이동합니다.

1. 가상 머신을 선택합니다.

1. 위쪽 메뉴에서 브라우저 **연결** 을 선택합니다.

1. 브라우저 **연결** 섹션에서 자격 증명을 입력한 **다음, 커넥트** 선택합니다.

    :::image type="content" source="./media/connect-virtual-machine-through-browser/lab-vm-browser-connect.png" alt-text="브라우저 연결 단추의 스크린샷.":::

## <a name="next-steps"></a>다음 단계

[Azure DevTest Labs에서 랩에 VM 추가](devtest-lab-add-vm.md)
