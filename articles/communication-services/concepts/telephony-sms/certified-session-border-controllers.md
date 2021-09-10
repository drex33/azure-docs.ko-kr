---
title: Azure 직접 라우팅이 인증된 Session Border Controller - Azure Communication Services
description: Azure Communication Services 직접 라우팅이 인증된 Session Border Controller 목록 및 알려진 제한 사항
author: boris-bazilevskiy
manager: nmurav
services: azure-communication-services
ms.author: bobazile
ms.date: 06/30/2021
ms.topic: conceptual
ms.service: azure-communication-services
ms.openlocfilehash: 8b2468b13f7093129c137019d8ec7f60e4fd5e4f
ms.sourcegitcommit: 2eac9bd319fb8b3a1080518c73ee337123286fa2
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/31/2021
ms.locfileid: "123258005"
---
# <a name="list-of-session-border-controllers-certified-for-azure-communication-services-direct-routing"></a>Azure Communication Services 직접 라우팅이 인증된 Session Border Controller 목록
이 문서에는 Azure Communication Services 직접 라우팅이 인증된 Session Border Controller 목록이 포함되어 있습니다. 알려진 제한 사항도 제공됩니다.

Microsoft는 Azure 직접 라우팅을 사용하기 위해 Teams 직접 라우팅이 인증된 선택한 SBC(Session Border Controller) 공급업체와 협력하고 있습니다. 이 페이지에서 진행 상황을 확인할 수 있습니다. Teams 직접 라우팅이 인증된 SBC는 Azure 직접 라우팅과 호환될 수 있지만, SBC가 이 페이지에 표시될 때까지 SBC에 워크로드를 배치하지 않는 것이 좋습니다. 또한 인증되지 않은 SBC는 지원되지 않습니다. Azure 직접 라우팅은 Teams 직접 라우팅과 동일한 백 엔드에서 빌드되지만 몇 가지 차이점이 있습니다. Azure 직접 라우팅의 경우 인증에서 포괄적인 SBC 유효성 검사를 처리합니다.

Microsoft는 각 공급업체와 협력하여 다음을 수행합니다.
- SIP 상호 연결 프로토콜을 공동으로 작업합니다.
- 타사 랩을 사용하여 강력한 테스트를 수행합니다. 테스트를 통과한 디바이스만 인증됩니다.
- 프로덕션 및 사전 프로덕션 환경에서 인증된 모든 디바이스를 사용하여 매일 테스트를 실행합니다. 사전 프로덕션 환경에서 디바이스의 유효성을 검사하면 클라우드에 있는 새로운 버전의 Azure Communication Services 코드가 인증된 SBC를 사용합니다.
- SBC 공급업체와 공동 지원 프로세스를 설정합니다.

[!INCLUDE [Public Preview](../../includes/public-preview-include-document.md)]

미디어 바이패스는 Azure Communication Services에서 아직 지원되지 않습니다. 초기 미디어는 웹 기반 클라이언트에서 지원되지 않습니다.
다음 표에는 Azure Communication Services 직접 라우팅이 인증된 디바이스가 나와 있습니다.

Communication Services 직접 라우팅용 SBC 인증 프로그램에 대한 질문이 있으면 acsdrcertification@microsoft.com에 문의하세요.

## <a name="certified-sbc-vendors"></a>인증된 SBC 공급업체

|Vendor|제품|소프트웨어 버전|
|:--- |:--- |:--- 
|AudioCodes|중앙값 SBC|7.40A
|Metaswitch|Perimeta SBC|4.9|

주 버전에 부여된 인증을 확인합니다. 즉, 주 버전 뒤에 있는 SBC 펌웨어의 숫자를 포함하는 펌웨어가 지원됩니다.

## <a name="next-steps"></a>다음 단계

### <a name="conceptual-documentation"></a>개념 설명서

- [Azure Communication Services의 전화 번호 유형](./plan-solution.md)
- [Azure 직접 라우팅 계획](./direct-routing-infrastructure.md)
- [세션 테두리 컨트롤러 페어링 및 음성 라우팅 구성](./direct-routing-provisioning.md)
- [가격](../pricing.md)

### <a name="quickstarts"></a>빠른 시작

- [휴대폰에 전화 걸기](../../quickstarts/voice-video-calling/pstn-call.md)
