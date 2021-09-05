---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 07/15/2021
ms.author: glenga
ms.openlocfilehash: 33a4b0f7d25162cf258e9ef6ad4ee438d6b76c8e
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122528928"
---
## <a name="local-settings"></a>로컬 설정

Azure의 함수 앱에서 실행하는 경우 함수에 필요한 설정은 [앱 설정에 안전하게 저장](../articles/azure-functions/functions-how-to-use-azure-function-app-settings.md#settings)됩니다. 로컬 개발 중에 이러한 설정은 local.settings.json 파일의 `Values` 개체에 대신 추가됩니다. local.settings.json 파일은 로컬 개발 도구에서 사용하는 설정도 저장합니다. 

local.settings.json에는 연결 문자열과 같은 비밀이 포함될 수 있으므로 원격 리포지토리에 저장해서는 안 됩니다. 로컬 설정에 대해 자세히 알아보려면 [로컬 설정 파일](../articles/azure-functions/functions-develop-local.md#local-settings-file)을 참조하세요.