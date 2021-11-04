---
author: Juliako
ms.service: azure-video-analyzer
ms.topic: include
ms.date: 04/30/2021
ms.author: juliako
ms.openlocfilehash: 3c4a156f745b5c32e238a7865a9c26710baf7ed0
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131101089"
---
이 자습서의 필수 구성 요소는 다음과 같습니다.

* 활성 구독이 있는 Azure 계정. 계정이 아직 없는 경우 [체험 계정을 만들](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) 수 있습니다.

    [!INCLUDE [azure-subscription-permissions](../../common-includes/azure-subscription-permissions.md)]
- 다음 확장이 포함된 [Visual Studio Code](https://code.visualstudio.com/)
  * [Azure IoT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools)
  * [Python](https://marketplace.visualstudio.com/items?itemName=ms-python.python)
  * [Python 3](https://www.python.org/downloads/)(3.6.9 이상), [Pip 3](https://pip.pypa.io/en/stable/installing/) 및 필요에 따라 [venv](https://docs.python.org/3/library/venv.html). 

> [!Important]
> 이 Custom Vision 모듈은 **Intel x86 및 amd64** 아키텍처만 지원합니다. 계속하기 전에 에지 디바이스의 아키텍처를 확인하세요.

## <a name="set-up-azure-resources"></a>Azure 리소스 설정

[![Azure에 배포](https://aka.ms/deploytoazurebutton)](https://aka.ms/ava-click-to-deploy)

[!INCLUDE [resources](../../common-includes/azure-resources.md)]


