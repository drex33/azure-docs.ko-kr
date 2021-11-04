---
title: Azure Active Directory Identity Protection의 위험 요소 조사
description: Azure Active Directory Identity Protection에서 위험한 사용자, 검색 항목 및 로그인을 조사하는 방법을 알아봅니다.
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: how-to
ms.date: 06/05/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: karenhoran
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: b15ccb564fe2c3551103e71b8c1ca2a901ec03e0
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131440760"
---
# <a name="how-to-investigate-risk"></a>방법: 위험 조사

Identity Protection은 조직의 사용자 환경에서 ID 위험을 조사하는 데 사용할 수 있는 세 가지 보고서를 조직에 제공합니다. 이러한 보고서는 **위험한 사용자**, **위험한 로그인**, **위험 검색** 입니다. 보안 전략의 약점을 보다 잘 이해하고 식별하려면 이벤트를 조사하는 것이 중요합니다.

세 보고서는 모두 Azure Portal 외부에서 추가로 분석할 수 있게 .CSV 형식으로 이벤트를 다운로드하는 것을 허용합니다. 위험한 사용자 및 위험한 로그인 보고서에서는 가장 최근의 2500개 항목을 다운로드할 수 있고, 위험 검색 보고서에서는 가장 최근의 5000개 레코드를 다운로드할 수 있습니다.

조직에서는 Microsoft Graph API 통합을 활용하여 조직의 권한으로 액세스할 수 있는 다른 원본으로 데이터를 집계할 수 있습니다.

이 세 개의 보고서는 **Azure Portal** > **Azure Active Directory** > **보안** 에서 확인할 수 있습니다.

## <a name="navigating-the-reports"></a>보고서 탐색

각 보고서는 보고서 맨 위에 표시된 기간에 해당하는 모든 검색 항목 목록으로 시작됩니다. 각 보고서에서 관리자 기본 설정에 따라 열을 추가하거나 제거할 수 있습니다. 관리자는 .CSV 또는 .JSON 형식으로 데이터를 다운로드하도록 선택할 수 있습니다. 보고서의 맨 위에 있는 필터를 사용하여 보고서를 필터링할 수 있습니다.

개별 항목을 선택하면 로그인이 도용되었는지, 안전한지 확인하는 기능, 사용자가 도용되었는지 확인하는 기능 또는 사용자 위험을 해제하는 기능과 같은 추가 항목이 보고서 맨 위에 활성화될 수 있습니다.

개별 항목을 선택하면 검색 항목 아래의 세부 정보 창이 확장됩니다. 관리자는 세부 정보 보기를 사용하여 각 검색 항목을 살펴보고 관련 작업을 수행할 수 있습니다. 

![위험한 로그인 및 세부 정보를 보여 주는 Identity Protection 보고서 예제](./media/howto-identity-protection-investigate-risk/identity-protection-risky-sign-ins-report.png)

## <a name="risky-users"></a>위험한 사용자

관리자는 위험한 사용자 보고서에 제공되는 정보를 통해 다음을 확인할 수 있습니다.

- 위험에 노출된 사용자, 위험을 해결한 사용자 또는 위험을 해제한 사용자
- 탐지 항목에 대한 세부 정보
- 모든 위험한 로그인 기록
- 위험 기록
 
그러면 관리자는 이러한 이벤트에 대한 작업을 수행하도록 선택할 수 있습니다. 관리자는 다음을 선택할 수 있습니다.

- 사용자 암호 재설정
- 사용자 도용 확인
- 사용자 위험 해제
- 사용자가 로그인하지 못하도록 차단
- Azure ATP를 사용하여 자세히 조사

## <a name="risky-sign-ins"></a>위험한 로그인

위험한 로그인 보고서에는 최대 지난 30일(1개월) 동안의 필터링 가능한 데이터가 포함되어 있습니다.

관리자는 위험한 로그인 보고서에 제공되는 정보를 통해 다음을 확인할 수 있습니다.

- 위험한 것으로 분류된 로그인, 도용된 것으로 확인된 로그인, 안전한 것으로 확인된 로그인, 위험이 해제된 로그인 또는 위험이 해결된 로그인
- 로그인 시도와 관련된 실시간 및 집계 위험 수준
- 트리거된 검색 유형
- 적용된 조건부 액세스 정책
- MFA 세부 정보
- 디바이스 정보
- 애플리케이션 정보
- 위치 정보

그러면 관리자는 이러한 이벤트에 대한 작업을 수행하도록 선택할 수 있습니다. 관리자는 다음을 선택할 수 있습니다.

- 로그인 도용 확인
- 로그인 보안 확인

> [!NOTE] 
> Identity Protection은 대화형인지 여부에 관계없이 모든 인증 흐름의 위험을 평가합니다. 이제 위험한 로그인 보고서에 대화형 및 비 대화형 로그인이 모두 표시됩니다. 이 보기를 수정하려면 "로그인 유형" 필터를 사용하세요.

## <a name="risk-detections"></a>위험 탐지

위험 검색 보고서에는 최대 지난 90일(3개월) 동안의 필터링 가능한 데이터가 포함되어 있습니다.

관리자는 위험 검색 보고서에 제공되는 정보를 통해 다음을 확인할 수 있습니다.

- 각 위험 검색에 대한 정보(예: 유형)
- 동시에 트리거되는 기타 위험
- 로그인 시도 위치
- MCAS(Microsoft Cloud App Security)의 세부 정보로 연결됩니다.

그러면 관리자는 사용자의 위험 또는 로그인 보고서로 돌아가서 수집된 정보에 따라 작업을 수행하도록 선택할 수 있습니다.

> [!NOTE] 
> 시스템에서는 위험 사용자 위험 점수에 영향을 준 위험 이벤트가 가양성이었는지 또는 MFA 프롬프트 또는 보안 암호 변경 완료와 같은 정책 적용으로 사용자 위험이 수정되었는지 검색할 수 있습니다. 따라서 시스템에서는 위험 상태를 해제하며, “AI 확인된 로그인 안전”의 위험 세부 정보가 표시되고 더 이상 사용자 위험에 영향을 주지 않습니다. 

## <a name="next-steps"></a>다음 단계

- [위험을 완화하는 데 사용할 수 있는 정책](concept-identity-protection-policies.md)

- [로그인 및 사용자 위험 정책 사용](howto-identity-protection-configure-risk-policies.md)
