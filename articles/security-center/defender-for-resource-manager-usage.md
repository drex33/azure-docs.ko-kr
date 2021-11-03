---
title: 리소스 관리자 경고에 대해 Microsoft Defender에 응답 하는 방법
description: 리소스 관리자 Microsoft Defender에서 경고에 응답 하는 데 필요한 단계에 대해 알아봅니다.
author: memildin
ms.author: memildin
ms.date: 12/07/2020
ms.topic: how-to
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: 913f1f07a588842d1cc2b4a845d5e9ff025b77fb
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131453120"
---
# <a name="respond-to-microsoft-defender-for-resource-manager-alerts"></a>리소스 관리자 경고에 대해 Microsoft Defender에 응답

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

Microsoft Defender에서 리소스 관리자에 대 한 경고를 수신 하는 경우 아래 설명 된 대로 경고를 조사 하 고 응답 하는 것이 좋습니다. Microsoft Defender for 리소스 관리자는 연결 된 모든 리소스를 보호 하므로 경고를 트리거한 응용 프로그램이 나 사용자에 게 친숙 한 경우에도 모든 경고를 둘러싼 상황을 확인 하는 것이 중요 합니다.  


## <a name="step-1-contact"></a>1단계. 연락처

1. 리소스 소유자에게 문의하여 동작이 예상된 것인지 또는 의도한 것인지를 확인합니다.
1. 예상된 작업인 경우, 경고를 해제합니다.
1. 예상되지 않은 작업인 경우, 다음 단계에 설명된 대로 관련 사용자 계정, 구독, 가상 머신을 손상된 것으로 처리하고 완화합니다.

## <a name="step-2-immediate-mitigation"></a>2단계. 즉각적인 위협 완화 

1. 손상된 사용자 계정을 다음과 같이 재구성합니다.
    - 익숙하지 않은 경우, 위협 행위자가 생성된 것일 수 있으니 삭제합니다.
    - 친숙한 경우 인증 자격 증명을 변경합니다.
    - Azure 활동 로그를 사용하여 사용자가 수행한 모든 활동을 검토하고 의심스러운 모든 활동을 식별합니다.

1. 손상된 구독을 다음과 같이 재구성합니다.
    - 손상된 자동화 계정에서 익숙하지 않은 Runbook 제거
    - 구독에 대한 IAM 권한을 검토하고 익숙하지 않은 사용자 계정은 사용 권한을 제거
    - 구독에서 모든 Azure 리소스를 검토하고 익숙하지 않은 리소스는 삭제
    - Microsoft Defender for Cloud에서 구독에 대 한 보안 경고를 검토 하 고 조사 합니다.
    - Azure 활동 로그를 사용하여 구독에서 수행된 모든 작업을 검토하고 의심스러운 작업을 식별

1. 손상된 가상 머신 재구성
    - 모든 사용자 암호 변경
    - 머신에서 전체 맬웨어 방지 검사 실행
    - 맬웨어가 없는 원본에서 이미지로 다시 설치


## <a name="next-steps"></a>다음 단계

이 페이지에서는 Microsoft Defender에서 리소스 관리자에 대 한 경고에 응답 하는 프로세스를 설명 했습니다. 관련 정보는 다음 페이지를 참조하세요.

- [리소스 관리자 용 Microsoft Defender 소개](defender-for-resource-manager-introduction.md)
- [보안 경고 표시 안 함](alerts-suppression-rules.md)
- [클라우드 데이터를 위해 계속 Defender를 내보냅니다.](continuous-export.md)