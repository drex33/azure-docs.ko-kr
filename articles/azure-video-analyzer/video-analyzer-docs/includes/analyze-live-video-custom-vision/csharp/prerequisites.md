---
author: Juliako
ms.service: azure-video-analyzer
ms.topic: include
ms.date: 04/30/2021
ms.author: juliako
ms.openlocfilehash: 168d85d3c70564a5d18fda38785b0d4338105fe1
ms.sourcegitcommit: ee8ce2c752d45968a822acc0866ff8111d0d4c7f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/14/2021
ms.locfileid: "113735264"
---
이 자습서의 필수 구성 요소는 다음과 같습니다.


* 활성 구독이 있는 Azure 계정. 계정이 아직 없는 경우 [체험 계정을 만듭니다](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

    [!INCLUDE [azure-subscription-permissions](../../common-includes/azure-subscription-permissions.md)]
* 다음 확장이 포함된 [Visual Studio Code](https://code.visualstudio.com/)
    * [Azure IoT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools)    
    * [C#](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp)
* [.NET Core 3.1 SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1). 

> [!Important]
> 이 Custom Vision 모듈은 **Intel x86 및 amd64** 아키텍처만 지원합니다. 계속하기 전에 에지 디바이스의 아키텍처를 확인하세요.

## <a name="set-up-azure-resources"></a>Azure 리소스 설정

[![Azure에 배포](https://aka.ms/deploytoazurebutton)](https://aka.ms/ava-click-to-deploy)

[!INCLUDE [resources](../../common-includes/azure-resources.md)]
