---
title: Azure AD에서 지속적인 액세스 평가
description: Azure AD에서 지속적인 액세스 평가로 사용자 상태 변경에 빠르게 응답
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 10/21/2021
ms.author: joflore
author: MicrosoftGuyJFlo
manager: karenhoran
ms.reviewer: jlu
ms.custom: has-adal-ref
ms.collection: M365-identity-device-management
ms.openlocfilehash: 71a1e5f8ee6bb3641fce243233a654d15363e254
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131012627"
---
# <a name="continuous-access-evaluation"></a>지속적인 액세스 평가

토큰 만료 및 새로 고침은 업계의 표준 메커니즘입니다. Outlook과 같은 클라이언트 애플리케이션이 Exchange Online과 같은 서비스에 연결하는 경우 API 요청은 OAuth 2.0 액세스 토큰을 사용하여 인증됩니다. 기본적으로 액세스 토큰은 1시간 동안 유효하며, 만료되면 새로 고침을 위해 클라이언트가 Azure AD로 리디렉션됩니다. 새로 고침 기간을 사용하면 사용자 액세스에 대한 정책을 다시 평가할 수 있습니다. 예: 조건부 액세스 정책으로 인해 또는 사용자가 디렉터리에서 사용하지 않도록 설정했기 때문에 토큰을 새로 고치지 않도록 선택할 수 있습니다. 

고객은 사용자에 대한 조건이 변경된 시간과 정책 변경이 적용되는 시점 사이의 지연 시간에 대해 우려를 표명했습니다. Azure AD는 "무딘 개체" 접근 방식으로 감소한 토큰 수명을 실험했지만, 위험을 제거하지 못하고 사용자 환경 및 안정성을 저하시킬 수 있음을 발견했습니다.

정책 위반 또는 보안 문제에 시기 적절하게 응답하려면 토큰 발급자(Azure AD) 및 신뢰 당사자(지원 앱) 간의 "대화"가 필요합니다. 이 양방향 대화는 두 가지 중요한 기능을 제공합니다. 신뢰 당사자는 네트워크 위치와 같이 속성이 변경되는 시기를 확인하고 토큰 발급자에게 알릴 수 있습니다. 또한 계정 손상, 사용 안 함 또는 기타 우려 사항으로 인해 지정된 사용자에 대한 토큰 존중을 중지하도록 신뢰 당사자에게 알리는 방법을 토큰 발급자에게 제공합니다. 이 대화에 대한 메커니즘은 CAE(지속적인 액세스 평가)입니다. 목표는 거의 실시간으로 응답하기 위한 것이지만, 이벤트 전파 시간으로 인해 최대 15분의 대기 시간이 관찰될 수 있습니다.

지속적인 액세스 평가의 초기 구현은 Exchange, Teams 및 SharePoint Online을 중심으로 합니다.

CAE를 사용하도록 애플리케이션을 준비하려면 [애플리케이션에서 지속적인 액세스 평가를 사용하도록 설정된 API를 사용하는 방법](../develop/app-resilience-continuous-access-evaluation.md)을 참조하세요.

### <a name="key-benefits"></a>주요 이점

- 사용자 종료 또는 암호 변경/재설정: 사용자 세션 해지가 거의 실시간으로 적용됩니다.
- 네트워크 위치 변경: 조건부 액세스 위치 정책이 거의 실시간으로 적용됩니다.
- 신뢰할 수 있는 네트워크 외부의 컴퓨터로 토큰 내보내기는 조건부 액세스 위치 정책을 통해 방지할 수 있습니다.

## <a name="scenarios"></a>시나리오 

지속적인 액세스 평가, 중요 이벤트 평가 및 조건부 액세스 정책 평가를 구성하는 두 가지 시나리오가 있습니다.

### <a name="critical-event-evaluation"></a>중요 이벤트 평가

연속 액세스 평가는 Exchange Online, SharePoint Online 및 Teams와 같은 서비스를 사용하도록 설정하여 중요한 Azure AD 이벤트를 구독하면 구현됩니다. 그러면 이러한 이벤트를 거의 실시간으로 평가하고 적용할 수 있습니다. 중요한 이벤트 평가는 모든 테넌트에서 사용할 수 있도록 조건부 액세스 정책을 사용하지 않습니다. 현재 평가되는 이벤트는 다음과 같습니다.

- 사용자 계정 삭제 또는 사용하지 않도록 설정
- 사용자 암호 변경 또는 재설정
- 사용자에 대해 다단계 인증을 사용하도록 설정
- 관리자가 사용자의 모든 새로 고침 토큰을 명시적으로 해지
- Azure AD Identity Protection에서 높은 사용자 위험 감지

이 프로세스는 사용자가 중요한 이벤트 후 몇 분 내에 Microsoft 365 클라이언트 앱에서 조직의 SharePoint Online 파일, 이메일, 일정 또는 작업, Teams에 대한 액세스 권한을 상실하는 시나리오를 사용하도록 설정합니다. 

> [!NOTE] 
> Teams 및 SharePoint Online은 사용자 위험 이벤트를 지원하지 않습니다.

### <a name="conditional-access-policy-evaluation-preview"></a>조건부 액세스 정책 평가(미리 보기)

Exchange Online, SharePoint Online, Teams 및 MS Graph는 서비스 내에서 평가를 위해 주요 조건부 액세스 정책을 동기화 할 수 있습니다.

이 프로세스는 사용자가 네트워크 위치를 변경한 후 즉시 Microsoft 365 클라이언트 앱 또는 SharePoint Online에서 조직의 파일, 이메일, 일정 또는 작업에 대한 액세스 권한을 잃는 시나리오를 지원합니다.

> [!NOTE]
> 일부 앱 및 리소스 공급자 조합은 지원되지 않습니다. 아래 표를 참조하세요. Office는 Word, Excel 및 PowerPoint를 나타냅니다.

| | Outlook Web | Outlook Win32 | Outlook iOS | Outlook Android | Outlook Mac |
| :--- | :---: | :---: | :---: | :---: | :---: |
| **SharePoint Online** | 지원 여부 | 지원됨 | 지원됨 | 지원됨 | 지원 여부 |
| **Exchange Online** | 지원 여부 | 지원됨 | 지원됨 | 지원됨 | 지원 여부 |

| | Office 웹앱 | Office Win32 앱 | iOS용 Office | Android용 Office | Mac용 Office |
| :--- | :---: | :---: | :---: | :---: | :---: |
| **SharePoint Online** | 지원되지 않음 | 지원됨 | 지원됨 | 지원됨 | 지원 여부 |
| **Exchange Online** | 지원되지 않음 | 지원됨 | 지원됨 | 지원됨 | 지원 여부 |

| | OneDrive 웹 | OneDrive Win32 | OneDrive iOS | OneDrive Android | OneDrive Mac |
| :--- | :---: | :---: | :---: | :---: | :---: |
| **SharePoint Online** | 지원 여부 | 지원됨 | 지원됨 | 지원됨 | 지원 여부 |

| | Teams 웹 | Teams Win32 | Teams iOS | Teams Android | Teams Mac |
| :--- | :---: | :---: | :---: | :---: | :---: |
| **Teams Service** | 지원 여부 | 지원됨 | 지원됨 | 지원됨 | 지원 여부 |
| **SharePoint Online** | 지원 여부 | 지원됨 | 지원됨 | 지원됨 | 지원 여부 |
| **Exchange Online** | 지원 여부 | 지원됨 | 지원됨 | 지원됨 | 지원됨 |

## <a name="client-capabilities"></a>클라이언트 기능

### <a name="client-side-claim-challenge"></a>클라이언트 쪽 클레임 챌린지

연속 액세스를 평가하기 전에 클라이언트는 만료되지 않는 한 캐시에서 액세스 토큰을 재생합니다. CAE를 사용하는 경우 토큰이 만료되지 않았으면 리소스 공급자가 토큰을 거부할 수 있는 새로운 사례를 소개합니다. 캐시된 토큰이 만료되지 않았더라도 클라이언트에게 캐시를 우회하도록 알리기 위해 토큰이 거부되었으며 Azure AD에서 새 액세스 토큰을 발급해야 함을 나타내는 **클레임 챌린지** 라는 메커니즘을 도입했습니다. CAE에서 클레임 챌린지를 해석하기 위해서는 클라이언트 업데이트가 필요합니다. 아래 애플리케이션의 최신 버전은 다음과 같은 클레임 챌린지를 지원합니다.

| | 웹 | Win32 | iOS | Android | Mac |
| :--- | :---: | :---: | :---: | :---: | :---: |
| **Outlook** | 지원 여부 | 지원됨 | 지원됨 | 지원됨 | 지원 여부 |
| **Teams** | 지원 여부 | 지원됨 | 지원됨 | 지원됨 | 지원 여부 |
| **Office** | 지원되지 않음 | 지원됨 | 지원됨 | 지원됨 | 지원 여부 |
| **OneDrive** | 지원 여부 | 지원됨 | 지원됨 | 지원됨 | 지원 여부 |

### <a name="token-lifetime"></a>토큰 수명

위험 및 정책이 실시간으로 평가되므로 지속적인 액세스 평가 인식 세션을 협상하는 클라이언트는 더 이상 정적 액세스 토큰 수명 정책을 사용하지 않습니다. 이 변경은 CAE 인식 세션을 협상하는 클라이언트에 대해 구성 가능한 토큰 수명 정책이 적용되지 않음을 의미합니다.

토큰 수명은 CAE 세션에서 최대 28시간으로 연장됩니다. 해지는 임의의 기간뿐만 아니라 중요한 이벤트 및 정책 평가에 의해 결정됩니다. 이러한 변경은 보안 상태에 영향을 주지 않고 애플리케이션의 안정성을 향상시킵니다. 

CAE 지원 클라이언트를 사용하지 않는 경우 기본 액세스 토큰 수명은 1 시간으로 유지됩니다. 기본값은 [CTL(구성 가능한 토큰 수명)](../develop/active-directory-configurable-token-lifetimes.md) 미리 보기 기능을 사용하여 액세스 토큰 수명을 구성한 경우에만 변경됩니다.

## <a name="example-flow-diagrams"></a>흐름 다이어그램 예

### <a name="user-revocation-event-flow"></a>사용자 해지 이벤트 흐름

![사용자 해지 이벤트 흐름](./media/concept-continuous-access-evaluation/user-revocation-event-flow.png)

1. CAE 지원 클라이언트는 일부 리소스에 대한 액세스 토큰을 요청하는 자격 증명 또는 새로 고침 토큰을 Azure AD에 제공합니다.
1. 액세스 토큰이 클라이언트에 대한 다른 아티팩트와 함께 반환됩니다.
1. 관리자가 명시적으로 [사용자의 모든 새로 고침 토큰을 해지](/powershell/module/azuread/revoke-azureaduserallrefreshtoken)합니다. Azure AD에서 리소스 공급자로 해지 이벤트가 전송됩니다.
1. 액세스 토큰이 리소스 공급자에게 제공됩니다. 리소스 공급자는 토큰의 유효성 검사를 평가하고 사용자에 대한 해지 이벤트가 있는지 여부를 확인합니다. 리소스 공급자는 이 정보를 사용하여 리소스에 대한 액세스 권한 부여 여부를 결정합니다.
1. 이 경우 리소스 공급자는 액세스를 거부하고 401+ 클레임 챌린지를 다시 클라이언트에 보냅니다.
1. CAE 지원 클라이언트가 401+ 클레임 챌린지를 이해합니다. 캐시를 우회하고 1단계로 돌아가서 클레임 챌린지와 함께 새로 고침 토큰을 Azure AD로 다시 보냅니다. 그러면 Azure AD는 모든 조건을 다시 평가하며 이 경우 사용자에게 다시 인증하라는 메시지를 표시합니다.

### <a name="user-condition-change-flow-preview"></a>사용자 조건 변경 흐름(미리 보기):

다음 예제에서는 조건부 액세스 관리자가 특정 IP 범위에서만 액세스할 수 있도록 위치 기반 조건부 액세스 정책을 구성했습니다.

![사용자 조건 이벤트 흐름](./media/concept-continuous-access-evaluation/user-condition-change-flow.png)

1. CAE 지원 클라이언트는 일부 리소스에 대한 액세스 토큰을 요청하는 자격 증명 또는 새로 고침 토큰을 Azure AD에 제공합니다.
1. Azure AD는 모든 조건부 액세스 정책을 평가하여 사용자와 클라이언트가 조건을 충족하는지 여부를 확인합니다.
1. 액세스 토큰이 클라이언트에 대한 다른 아티팩트와 함께 반환됩니다.
1. 사용자가 허용된 IP 범위 밖으로 이동
1. 클라이언트가 허용되는 IP 범위 외부에서 리소스 공급자에게 액세스 토큰을 제공합니다.
1. 리소스 공급자는 토큰의 유효성을 평가하고 Azure AD에서 동기화된 위치 정책을 확인합니다.
1. 이 경우 리소스 공급자는 액세스를 거부하고 401+ 클레임 챌린지를 다시 클라이언트에 보냅니다. 클라이언트는 허용되는 IP 범위에서 오지 않기 때문에 챌린지됩니다.
1. CAE 지원 클라이언트가 401+ 클레임 챌린지를 이해합니다. 캐시를 우회하고 1단계로 돌아가서 클레임 챌린지와 함께 새로 고침 토큰을 Azure AD로 다시 보냅니다. Azure AD는 모든 조건을 다시 평가하며 이 경우 액세스를 거부합니다.

## <a name="enable-or-disable-cae-preview"></a>CAE 사용 또는 사용 안 함(미리 보기)

1. **Azure Portal** 에 조건부 액세스 관리자, 보안 관리자 또는 전역 관리자로 로그인합니다.
1. **Azure Active Directory** > **보안** > **지속적인 액세스 평가** 로 이동합니다.
1. **미리 보기 사용** 을 선택합니다.
1. **저장** 을 선택합니다.

이 페이지에서 필요에 따라 미리 보기에 적용될 사용자 및 그룹을 제한할 수 있습니다.

> [!NOTE]
> [**continuousAccessEvaluationPolicy**](/graph/api/continuousaccessevaluationpolicy-get?view=graph-rest-beta&preserve-view=true&tabs=http#request-body)를 통해 Microsoft Graph를 쿼리하여 테넌트의 CAE 구성을 확인할 수 있습니다. HTTP 200 응답 및 연결된 응답 본문은 테넌트에 CAE가 사용하도록 설정되었는지 또는 사용하지 않도록 설정되었는지 여부를 나타냅니다. Microsoft Graph가 HTTP 404 응답을 반환하는 경우 CAE가 구성되지 않습니다.

![Azure Portal에서 CAE 미리 보기 사용](./media/concept-continuous-access-evaluation/enable-cae-preview.png)

### <a name="available-options"></a>사용 가능한 옵션

조직에는 CAE를 사용하도록 설정하는 옵션이 제공됩니다.

1. 기본으로 선택한 **일반 공급 후 사용하도록 자동 설정** 을 그대로 두면 일반적으로 CAE를 사용할 수 있을 때 기능이 사용하도록 설정됩니다.
1. **미리 보기 사용하도록 설정** 을 선택하는 고객은 새로운 기능을 즉시 활용하고 일반 공급 시에는 변경할 필요가 없습니다. 
1. **미리 보기 사용 안 함** 을 선택하는 고객은 조직의 고유한 속도로 CAE를 채택할 시간이 있습니다. 이 설정은 일반 공급 시 **사용 암 함** 으로 유지됩니다.

#### <a name="migration"></a>마이그레이션

CAE 설정이 조건부 액세스 아래로 이동했습니다. 이전에 보안에서 CAE 설정을 구성한 고객은 이러한 설정을 조건부 액세스 정책으로 마이그레이션해야 합니다. 다음 단계를 사용하여 설정을 조건부 액세스 정책으로 마이그레이션합니다.

:::image type="content" source="media/concept-continuous-access-evaluation/migrate-continuous-access-evaluation.png" alt-text="연속 액세스 평가를 조건부 액세스 정책으로 마이그레이션하는 옵션을 보여주는 포털 보기입니다." lightbox="media/concept-continuous-access-evaluation/migrate-continuous-access-evaluation.png":::

1. **Azure Portal** 에 조건부 액세스 관리자, 보안 관리자 또는 전역 관리자로 로그인합니다. 
1.  **Azure Active Directory** > **보안** > **지속적인 액세스 평가(미리 보기)** 로 이동합니다. 
1.  그러면 정책 **마이그레이션** 옵션이 표시됩니다. 이 작업은 이 시점에서 액세스할 수 있는 유일한 작업입니다.
1. **조건부 액세스** 로 이동하면 설정이 구성된 **CAE 설정에서 만들어진 CA 정책** 이라는 새 정책을 찾을 수 있습니다. 관리자는 이 정책을 사용자 지정하거나 정책을 대체할 자체 정책을 만들도록 선택할 수 있습니다.

세션 제어로서의 연속 액세스 평가에 대한 자세한 내용은 [연속 액세스 평가 사용자 지정](concept-conditional-access-session.md#customize-continuous-access-evaluation) 섹션에서 찾을 수 있습니다.

### <a name="strict-enforcement"></a>엄격한 적용 

조건부 액세스에서 최신 CAE 설정을 사용하는 경우 엄격한 적용은 IP 주소 변형 및 클라이언트 기능의 두 가지 요인에 따라 보안을 강화할 수 있는 새로운 기능입니다. 이 기능은 지정된 정책에 대한 CAE 옵션을 사용자 지정하는 동안 사용하도록 설정할 수 있습니다. 엄격한 적용을 켜면 CAE는 [IP 주소 변형](#ip-address-variation) 또는 CAE [클라이언트 기능 부족](#client-capabilities) 인스턴스를 검색할 때 액세스 권한을 철회합니다.

## <a name="limitations"></a>제한 사항

### <a name="group-membership-and-policy-update-effective-time"></a>그룹 멤버 자격 및 정책 업데이트 유효 시간

관리자가 만든 조건부 액세스 정책 및 그룹 멤버 자격에 대한 변경 내용을 적용하려면 최대 1일이 걸릴 수 있습니다. 지연은 Azure AD와 Exchange Online 및 SharePoint Online과 같은 리소스 공급자 간의 복제에서 비롯됩니다. 지연을 2시간으로 줄이는 정책 업데이트에 대한 몇 가지 최적화가 수행되었습니다. 그러나 아직 모든 시나리오를 다루지는 않습니다.  

조건부 액세스 정책 또는 그룹 멤버 자격 변경 내용을 특정 사용자에게 즉시 적용해야 하는 경우 두 가지 옵션이 있습니다. 

- [revoke-azureaduserallrefreshtoken PowerShell 명령](/powershell/module/azuread/revoke-azureaduserallrefreshtoken)을 실행하여 지정된 사용자의 모든 새로 고침 토큰을 철회합니다.
- Azure Portal 사용자 프로필 페이지에서 "세션 철회"를 선택하여 업데이트된 정책이 즉시 적용되도록 사용자 세션을 철회합니다.

### <a name="ip-address-variation"></a>IP 주소 변형

ID 공급자 및 리소스 공급자는 서로 다른 IP 주소를 볼 수 있습니다. 이 불일치는 다음과 같은 이유로 발생할 수 있습니다.

- 조직의 네트워크 프록시 구현
- ID 공급자와 리소스 공급자 간의 잘못된 IPv4/IPv6 구성
 
예제:

- ID 공급자는 클라이언트에서 하나의 IP 주소를 볼 수 있습니다.
- 리소스 공급자에게는 프록시를 통과한 후 클라이언트와 다른 IP 주소가 표시됩니다.
- ID 공급자에게 표시되는 IP 주소는 정책에서 허용되는 IP 범위의 일부이지만 리소스 공급자의 IP 주소는 그렇지 않습니다.

이러한 시나리오로 인한 무한 루프를 방지하기 위해 Azure AD는 1시간 CAE 토큰을 발급하고 클라이언트 위치 변경을 적용하지 않습니다. 이 경우 클라이언트 위치 변경 이벤트 외의 [다른 이벤트](#critical-event-evaluation)도 계속 평가하고 있으므로 기존 1시간 토큰에 비해 보안이 향상됩니다.

### <a name="supported-location-policies"></a>지원되는 위치 정책

CAE에는 [IP 기반 명명된 위치](../conditional-access/location-condition.md#ip-address-ranges)에 대한 인사이트만 있습니다. CAE에는 [MFA에서 신뢰할 수 있는 IP](../authentication/howto-mfa-mfasettings.md#trusted-ips) 또는 국가 기반 위치와 같은 다른 위치 조건에 대한 인사이트가 없습니다. 사용자가 MFA에서 신뢰할 수 있는 IP, MFA 신뢰할 수 있는 IP 또는 국가 위치를 포함하는 신뢰할 수 있는 위치에서 온 경우 해당 사용자가 다른 위치로 이동한 후에는 CAE가 적용되지 않습니다. 이러한 경우 Azure AD는 즉각적인 IP 적용 확인 없이 1시간 액세스 토큰을 발급합니다. 

> [!IMPORTANT]
> 지속적인 액세스 평가를 위한 위치를 구성하는 경우 [IP 기반 조건부 액세스 위치 조건](../conditional-access/location-condition.md)만 사용하고, **IPv4 및 IPv6를 둘 모두 포함** 하여 ID 공급자 및 리소스 공급자가 볼 수 있는 모든 IP 주소를 구성합니다. Azure AD Multi-Factor Authentication의 서비스 설정 페이지에서 사용할 수 있는 국가 위치 조건 또는 신뢰할 수 있는 IP 기능을 사용하지 마세요.

### <a name="office-and-web-account-manager-settings"></a>Office 및 웹 계정 관리자 설정

| Office 업데이트 채널 | DisableADALatopWAMOverride | DisableAADWAM |
| --- | --- | --- |
| 반기 엔터프라이즈 채널 | 사용하도록 설정됨 또는 1로 설정되면 CAE가 지원되지 않습니다. | 사용하도록 설정됨 또는 1로 설정되면 CAE가 지원되지 않습니다. |
| 현재 채널 <br> 또는 <br> 월별 엔터프라이즈 채널 | CAE는 설정에 관계없이 지원됩니다. | CAE는 설정에 관계없이 지원됩니다. |

Office 업데이트 채널에 대한 자세한 내용은 [Microsoft 365 앱의 업데이트 채널 개요](/deployoffice/overview-update-channels)를 참조하세요. 조직에서는 WAM(웹 계정 관리자)을 사용하지 않도록 설정하지 않는 것이 좋습니다.

### <a name="coauthoring-in-office-apps"></a>Office 앱에서 공동 작성

여러 사용자가 동시에 문서에 대해 공동 작업하는 경우 사용자 해지 또는 정책 변경 이벤트에 따라 CAE에서 문서에 대한 액세스 권한을 즉시 철회하지 않을 수 있습니다. 이 경우 다음 작업을 수행하면 사용자는 액세스 권한을 완전히 상실됩니다. 

- 문서 닫기
- Office 앱 닫기
- 10시간 후

이 시간을 줄이기 위해 SharePoint 관리자는 [SharePoint Online에서 네트워크 위치 정책을 구성](/sharepoint/control-access-based-on-network-location)하여 SharePoint Online 및 비즈니스용 OneDrive에 저장된 문서에 대한 공동 작성 세션의 최대 수명을 줄일 수 있습니다. 이 구성이 변경되면 공동 작성 세션의 최대 수명이 15분으로 줄어들고 SharePoint Online PowerShell 명령 "[Set-SPOTenant –IPAddressWACTokenLifetime](/powershell/module/sharepoint-online/set-spotenant)"을 사용하여 더 세부적으로 조정할 수 있습니다.

### <a name="enable-after-a-user-is-disabled"></a>사용자를 사용하지 않도록 설정한 후 사용하도록 설정

사용자를 사용하지 않도록 설정한 직후 사용하도록 설정하면 다운스트림 Microsoft 서비스에서 계정이 사용됨으로 인식되기까지 약간의 대기 시간이 있습니다.

- SharePoint Online 및 Teams에는 일반적으로 15분 지연이 있습니다. 
- Exchange Online에는 일반적으로 35-40분 지연이 있습니다. 

### <a name="push-notifications"></a>푸시 알림

푸시 알림이 릴리스되기 전에는 IP 주소 정책이 평가되지 않습니다. 이 시나리오는 푸시 알림이 아웃바운드이고 평가할 연결된 IP 주소가 없기 때문에 존재합니다. 사용자가 해당 푸시 알림을 클릭하는 경우(예: Outlook의 이메일) 이메일이 표시되기 전에 CAE IP 주소 정책이 계속 적용됩니다. 푸시 알림은 IP 주소 정책으로 보호되지 않는 메시지 미리 보기를 표시합니다. 다른 모든 CAE 확인은 푸시 알림을 보내기 전에 수행됩니다. 사용자 또는 디바이스의 액세스 권한이 제거된 경우 적용은 문서화된 기간 내에 발생합니다. 

## <a name="faqs"></a>FAQ

### <a name="how-will-cae-work-with-sign-in-frequency"></a>CAE가 로그인 빈도에서 어떻게 작동하나요?

로그인 빈도는 CAE 사용 여부에 관계없이 적용됩니다.

## <a name="next-steps"></a>다음 단계

- [애플리케이션에서 지속적인 액세스 권한 평가를 사용하도록 설정된 API를 사용하는 방법](../develop/app-resilience-continuous-access-evaluation.md)
- [클레임 챌린지, 클레임 요청 및 클라이언트 기능](../develop/claims-challenge.md)
- [조건부 액세스: 세션](concept-conditional-access-session.md)
- [연속 액세스 평가 모니터링 및 문제 해결](howto-continuous-access-evaluation-troubleshoot.md)
