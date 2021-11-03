---
title: Azure AD ID 보호에서 위험 검색 시뮬레이션
description: ID 보호에서 위험 검색을 시뮬레이션하는 방법 알아보기
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
ms.openlocfilehash: 353c80be29a5bf62ed98d82374fda61a4e7b7021
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131051575"
---
# <a name="simulating-risk-detections-in-identity-protection"></a>ID 보호에서 위험 검색 시뮬레이션

관리자는 다음 항목을 수행하기 위해 각자의 환경에서 위험을 시뮬레이션할 수 있습니다.

- 위험 검색 및 취약성을 시뮬레이션하여 ID 보호 환경에 데이터를 채웁니다.
- 위험 기반 조건부 액세스 정책을 설정하고 이러한 정책의 영향을 테스트합니다.

이 문서에서는 아래의 위험 검색 유형을 시뮬레이션하는 단계를 제공합니다.

- 익명 IP 주소(난이도 하)
- 익숙하지 않은 로그인 속성(난이도 중)
- 비정상적 이동 (난이도 상)

안전한 방법으로 다른 위험 검색을 시뮬레이션할 수 없습니다.

각 위험 검색에 대한 자세한 내용은 문서 [위험이란?](concept-identity-protection-risks.md)에서 찾을 수 있습니다.

## <a name="anonymous-ip-address"></a>익명 IP 주소

다음 절차를 완료하려면 다음을 사용해야 합니다.

- [Tor 브라우저](https://www.torproject.org/projects/torbrowser.html.en) - 익명 IP 주소를 시뮬레이션할 수 있습니다. 조직이 Tor 브라우저를 사용하여 제한하는 경우 가상 머신을 사용해야 합니다.
- 아직 Azure AD Multi-Factor Authentication에 등록되어 있지 않은 테스트 계정.

**익명 IP에서 로그인을 시뮬레이트하려면 다음 단계를 수행합니다.**

1. [Tor 브라우저](https://www.torproject.org/projects/torbrowser.html.en)를 사용하여 [https://myapps.microsoft.com](https://myapps.microsoft.com)로 이동합니다.   
2. **익명 IP 주소에서 로그인** 보고서에 표시하려는 계정의 자격 증명을 입력합니다.

로그인이 10 ~ 15분 이내에 ID 보호 대시보드에 나타납니다. 

## <a name="unfamiliar-sign-in-properties"></a>일반적이지 않은 로그인 속성

알 수 없는 위치를 시뮬레이트하려면 테스트 계정이 이전에 로그인하지 않은 위치 및 디바이스에서 로그인해야 합니다.

다음 프로시저는 새로 만든 항목을 사용합니다.

- VPN 연결: 새 위치를 시뮬레이트합니다.
- 가상 머신: 새 디바이스를 시뮬레이트합니다.

다음 절차를 완료하려면 다음 항목을 포함하는 사용자 계정을 사용해야 합니다.

- 최소 30일 로그인 기록.
- Azure AD Multi-Factor Authentication 사용.

**익숙하지 않은 위치에서 로그인을 시뮬레이트하려면 다음 단계를 수행합니다.**

1. 테스트 계정을 사용하여 로그인하는 경우 MFA(multi-factor authentication) 챌린지가 전달되지 않아 MFA 챌린지에 실패합니다.
2. 새 VPN을 사용하여 [https://myapps.microsoft.com](https://myapps.microsoft.com)으로 이동하고 테스트 계정의 자격 증명을 입력합니다.

로그인이 10 ~ 15분 이내에 ID 보호 대시보드에 나타납니다.

## <a name="atypical-travel"></a>비정상적 이동

알고리즘이 기계 학습을 사용하여 익숙한 디바이스에서 비정상적 이동 또는 디렉터리의 다른 사용자가 사용하는 VPN의 로그인 등 가양성을 제거하기 때문에 비정상적 이동 조건을 시뮬레이션하는 작업은 어렵습니다. 또한 알고리즘은 위험 검색의 생성을 시작하기 전에 사용자의 14일간의 로그인 및 10회의 로그인 기록이 필요합니다. 복잡한 기계 학습 모델 및 위의 규칙으로 인해 다음 단계를 실행하면 위험 검색으로 이어지지 않을 가능성이 있습니다. 이 단계를 복제해서 여러 Azure AD 계정에서 이러한 위험 검색을 시뮬레이션할 수도 있습니다.

**비정상적인 이동 위험 검색을 시뮬레이션하려면 다음 단계를 수행 합니다.**

1. 표준 브라우저를 사용하여 [https://myapps.microsoft.com](https://myapps.microsoft.com)으로 이동합니다.  
2. 비정상적 이동 위험 검색의 생성 대상이 되는 계정의 자격 증명을 입력합니다.
3. 사용자 에이전트를 변경합니다. 개발자 도구(F12)에서 Microsoft Edge의 사용자 에이전트를 변경할 수 있습니다.
4. 사용자의 IP 주소를 변경합니다. VPN, Tor 추가 항목을 사용하거나 다른 데이터 센터의 Azure에서 새 가상 머신을 만들어 사용자의 IP 주소를 변경할 수 있습니다.
5. 이전과 동일한 자격 증명을 사용하여 이전 로그인 후 몇 분 내에 [https://myapps.microsoft.com](https://myapps.microsoft.com)에 로그인합니다.

로그인이 2-4시간 이내에 ID 보호 대시보드에 나타납니다.

## <a name="testing-risk-policies"></a>위험 정책 테스트

이 섹션에서는 [방법: 위험 정책 구성 및 설정](howto-identity-protection-configure-risk-policies.md) 문서에서 만든 사용자 및 로그인 위험 정책을 테스트하는 단계를 제공합니다.

### <a name="user-risk-policy"></a>사용자 위험 정책

사용자 위험 보안 정책을 테스트하려면 다음 단계를 수행합니다.

1. [Azure Portal](https://portal.azure.com)로 이동합니다.
1. **Azure Active Directory** > **보안** > **Identity Protection** > **개요** 로 이동합니다.
1. **사용자 위험 정책 구성** 을 선택합니다.
   1. **할당** 에서
      1. **사용자** - 롤아웃을 제한하는 경우 **모든 사용자** 또는 **개인 및 그룹 선택** 을 선택할 수 있습니다.
         1. 원하는 경우 정책에서 사용자를 제외할 수 있습니다.
      1. **조건** - **사용자 위험** Microsoft는 이 옵션을 **높음** 으로 설정할 것을 권장합니다.
   1. **컨트롤** 에서
      1. **액세스** - Microsoft는 **액세스를 허용** 하고 **암호 변경을 요구** 할 것을 권장합니다.
   1. **정책 적용** - **끄기**
   1. **저장** - 이 작업을 수행하면 **개요** 페이지로 돌아갑니다.
1. 예를 들어 위험 검색 중 하나를 몇 차례 시뮬레이션하여 테스트 계정의 사용자 위험을 상승시킵니다.
1. 몇 분 정도 기다린 다음, 사용자에 대하여 위험이 상승되었는지 확인합니다. 상승되지 않았다면 사용자에 대해 여러 번 더 위험 검색을 시뮬레이션합니다.
1. 위험 정책으로 돌아가서 **정책 적용** 을 **켜기** 로 설정하고 정책 변경 내용을 **저장** 합니다.
1. 이제 위험 수준이 상승한 사용자로 로그인하여 사용자 위험 기반 조건부 액세스를 테스트할 수 있습니다.

### <a name="sign-in-risk-security-policy"></a>로그인 위험 보안 정책

로그인 위험 정책을 테스트하려면 다음 단계를 수행합니다.

1. [Azure Portal](https://portal.azure.com)로 이동합니다.
1. **Azure Active Directory** > **보안** > **Identity Protection** > **개요** 로 이동합니다.
1. **로그인 위험 정책 구성** 을 선택합니다.
   1. **할당** 에서
      1. **사용자** - 롤아웃을 제한하는 경우 **모든 사용자** 또는 **개인 및 그룹 선택** 을 선택할 수 있습니다.
         1. 원하는 경우 정책에서 사용자를 제외할 수 있습니다.
      1. **조건** - **로그인 위험** Microsoft는 이 옵션을 **보통 이상** 으로 설정할 것을 권장합니다.
   1. **컨트롤** 에서
      1. **액세스** - Microsoft는 **액세스를 허용** 하고 **다단계 인증을 요구** 할 것을 권장합니다.
   1. **정책 적용** - **설정**
   1. **저장** - 이 작업을 수행하면 **개요** 페이지로 돌아갑니다.
1. 이제 위험한 세션을 사용하여(예를 들어 Tor 브라우저 사용) 로그인하여 로그인 위험 기반 조건부 액세스를 테스트할 수 있습니다. 

## <a name="next-steps"></a>다음 단계

- [위험이란?](concept-identity-protection-risks.md)

- [방법: 위험 정책 구성 및 사용](howto-identity-protection-configure-risk-policies.md)

- [Azure Active Directory ID 보호](overview-identity-protection.md)
