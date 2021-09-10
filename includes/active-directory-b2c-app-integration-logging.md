---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 07/29/2021
ms.author: mimart
ms.openlocfilehash: 720b464c02b929590df46123e0caf9a9e83fe2f4
ms.sourcegitcommit: 40866facf800a09574f97cc486b5f64fced67eb2
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/30/2021
ms.locfileid: "123220286"
---
## <a name="configure-logging"></a>로깅 구성

MSAL 라이브러리는 문제를 진단하는 데 유용한 로그 메시지를 생성합니다. 앱은 로깅을 구성할 수 있습니다. 이 앱은 세부 정보 수준과 개인 데이터와 조직 데이터가 기록되는지 여부에 대한 제어를 사용자 지정할 수도 있습니다. 

MSAL 로깅 콜백을 만들고 사용자에게 인증 문제가 있을 때 로그를 제출할 수 있는 방법을 제공하는 것이 좋습니다. MSAL은 다음과 같은 수준의 로깅 세부 정보를 제공합니다.

- **오류**: 무언가 잘못되어 오류가 발생했습니다. 이 수준은 디버깅 및 문제 식별에 사용됩니다.
- **경고**: 오류 또는 장애가 반드시 발생한 것은 아니지만, 문제를 진단하고 파악하기 위한 정보입니다.
- **정보**: MSAL은 디버깅이 아니라 정보 제공을 위해 이벤트를 기록합니다.
- **자세한 정보**: 이 값은 기본 수준입니다. MSAL은 라이브러리 동작의 전체 세부 정보를 기록합니다.

기본적으로 MSAL 로거는 개인 또는 조직 데이터를 캡처하지 않습니다. 라이브러리는 개인 및 조직 데이터 로깅을 사용하도록 설정하는 옵션을 제공합니다.
