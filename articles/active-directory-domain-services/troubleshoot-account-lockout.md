---
title: Azure AD Domain Services의 계정 잠금 문제 해결 | Microsoft Docs
description: Azure Active Directory Domain Services에서 사용자 계정이 잠기는 원인이 되는 일반적인 문제를 해결하는 방법에 대해 알아봅니다.
services: active-directory-ds
author: justinha
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: troubleshooting
ms.date: 07/06/2020
ms.author: justinha
ms.openlocfilehash: 487befb0e0d48a1ccf61a38af29c17c596fa70b8
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/01/2021
ms.locfileid: "129350095"
---
# <a name="troubleshoot-account-lockout-problems-with-an-azure-active-directory-domain-services-managed-domain"></a>Azure Active Directory Domain Services 관리되는 도메인의 계정 잠금 문제 해결

악의적인 로그인 시도가 반복적으로 행해지는 것을 방지하기 위해 Azure Active Directory Domain Services(Azure AD DS) 관리되는 도메인에서는 정의된 임계값 이후에 계정을 잠급니다. 이 계정 잠금은 로그인 공격 인시던트가 없는 경우에도 우연히 발생할 수 있습니다. 예를 들어 사용자가 반복해서 잘못된 암호를 입력하거나 서비스에서 이전 암호를 사용하려고 시도하는 경우 계정이 잠깁니다.

이 문제 해결 문서에서는 계정 잠금이 발생하는 이유와 동작을 구성하는 방법 및 보안 감사를 검토하여 잠금 이벤트 문제를 해결하는 방법에 대해 설명합니다.

## <a name="what-is-an-account-lockout"></a>계정 잠금이란?

Azure AD DS 관리되는 도메인의 사용자 계정은 실패한 로그인 시도에 대해 정의된 임계점이 충족되었을 때 잠깁니다. 이 계정 잠금 동작은 자동화된 디지털 공격일 가능성이 있는 반복적인 무차별 로그인 시도로부터 사용자를 보호하도록 설계되었습니다.

**기본적으로 2분 이내에 5번의 잘못된 암호 시도가 있는 경우 계정은 30분 동안 잠깁니다.**

기본 계정 잠금 임계값은 세분화된 암호 정책을 사용하여 구성됩니다. 특정 요구 사항 집합이 있는 경우 이러한 기본 계정 잠금 임계값을 재정의할 수 있습니다. 그러나 계정 잠금 횟수를 줄이기 위해 임계값 제한을 늘리는 것은 권장되지 않습니다. 먼저 계정 잠금 동작의 원인을 해결하세요.

### <a name="fine-grained-password-policy"></a>세분화된 암호 정책

FGPP(세분화된 암호 정책)는 도메인의 다양한 사용자별로 암호에 대한 특정 제한 사항 및 계정 잠금 정책을 적용할 수 있도록 해 줍니다. FGPP는 관리되는 도메인 내의 사용자에게만 영향을 미칩니다. Azure AD에서 관리되는 도메인으로 동기화된 클라우드 사용자 및 도메인 사용자는 관리되는 도메인 내에서만 암호 정책의 영향을 받습니다. 이들의 Azure AD 또는 온-프레미스 디렉터리 계정은 영향을 받지 않습니다.

정책은 관리되는 도메인의 그룹 연결을 통해 배포되고 정책 변경은 다음 사용자 로그인 시 적용됩니다. 정책을 변경해도 이미 잠긴 사용자 계정이 잠금 해제되지는 않습니다.

세분화된 암호 정책에 대한 자세한 내용 및 Azure AD DS에서 직접 생성된 사용자와 Azure AD DS에서 동기화되어 들어간 사용자 간의 차이점에 대한 자세한 내용은 [암호 및 계정 잠금 정책 구성][configure-fgpp]을 참조하세요.

## <a name="common-account-lockout-reasons"></a>일반적인 계정 잠금 이유

악의적인 의도나 요인을 제외하고 계정이 잠기는 가장 일반적인 이유는 다음과 같습니다.

* **사용자가 자기 자신을 잠갔습니다.**
    * 사용자가 최근에 암호를 변경한 후 이전 암호를 계속 사용했나요? 2분 이내 5번 시도 실패라는 기본 계정 잠금 정책이 실행된 것은 사용자가 실수로 이전 암호를 다시 시도한 것이 원인일 수 있습니다.
* **애플리케이션 또는 서비스에 이전 암호가 있습니다.**
    * 계정이 애플리케이션 또는 서비스에서 사용되는 경우 해당 리소스에서 이전 암호를 사용하여 로그인 시도를 반복할 수 있습니다. 이 동작은 계정이 잠기는 결과를 초래합니다.
    * 여러 다른 애플리케이션 또는 서비스에 걸친 계정 사용을 최소화하고 자격 증명이 사용되는 위치를 기록하세요. 계정 암호가 변경된 경우 연결된 애플리케이션 또는 서비스를 적절하게 업데이트하세요.
* **다른 환경에서 암호가 변경되었고 새 암호가 아직 동기화되지 않았습니다.**
    * 온-프레미스 AD DS 환경과 같이 관리되는 도메인 외부에서 계정 암호가 변경된 경우 암호 변경이 Azure AD를 통해 또는 관리되는 도메인으로 동기화되는 데 몇 분이 걸릴 수 있습니다.
    * 사용자가 암호 동기화 프로세스가 완료되기 전에 관리되는 도메인의 리소스에 로그인하려고 시도하는 경우 해당 사용자의 계정이 잠깁니다.

## <a name="troubleshoot-account-lockouts-with-security-audits"></a>보안 감사를 사용하여 계정 잠금 문제 해결

계정 잠금 이벤트가 언제 발생하며 어디에서 발생하는지 파악하여 문제를 해결하려면 [Azure AD DS에 대해 보안 감사를 사용하도록 설정][security-audit-events]합니다. 감사 이벤트는 이 기능을 사용하도록 설정한 시점부터 캡처됩니다. 해결할 계정 잠금 문제가 발생하기 ‘전’에 보안 감사를 사용하도록 설정하는 것이 가장 좋습니다. 사용자 계정에 잠금 문제가 반복적으로 발생하는 경우 다음에 같은 상황이 발생할 것에 대비해 보안 감사를 사용하도록 설정할 수 있습니다.

보안 감사를 사용하도록 설정하면 다음 샘플 쿼리에 ‘계정 잠금 이벤트’(코드 ‘4740’)를 검토하는 방법이 표시됩니다. 

지난 7일간의 모든 계정 잠금 이벤트를 봅니다.

```Kusto
AADDomainServicesAccountManagement
| where TimeGenerated >= ago(7d)
| where OperationName has "4740"
```

*Driley* 라는 이름의 계정에 대해 지난 7일간 발생한 모든 계정 잠금 이벤트를 봅니다.

```Kusto
AADDomainServicesAccountLogon
| where TimeGenerated >= ago(7d)
| where OperationName has "4740"
| where "driley" == tolower(extract("Logon Account:\t(.+[0-9A-Za-z])",1,tostring(ResultDescription)))
```

날짜와 시간이 오름차순으로 정렬된 2020년 6월 26일 오전 9시부터 2020년 7월 1일 자정 사이의 모든 계정 잠금 이벤트를 봅니다.

```Kusto
AADDomainServicesAccountManagement
| where TimeGenerated >= datetime(2020-06-26 09:00) and TimeGenerated <= datetime(2020-07-01)
| where OperationName has "4740"
| sort by TimeGenerated asc
```

**참고**

“소스 워크스테이션:”의 4776 및 4740 이벤트 세부 정보가 비어있는 것을 발견할 수 있습니다. 이는 다른 디바이스를 통해 네트워크에 로그온하는 동안 잘못된 암호 시도가 발생했기 때문입니다.
예: RADIUS 서버를 사용하는 경우 이 서버에서 인증을 AAD DS에 전달할 수 있습니다. RDP를 DC 백 엔드에 사용하도록 설정하려면 Netlogon 로그를 구성합니다.

03/04 19:07:29 [LOGON] [10752] contoso: SamLogon: Transitive Network logon of contoso\Nagappan.Veerappan from  (via LOB11-RADIUS) Entered 

03/04 19:07:29 [LOGON] [10752] contoso: SamLogon: Transitive Network logon of contoso\Nagappan.Veerappan from  (via LOB11-RADIUS) Returns 0xC000006A

03/04 19:07:35 [LOGON] [10753] contoso: SamLogon: Transitive Network logon of contoso\Nagappan.Veerappan from  (via LOB11-RADIUS) Entered 

03/04 19:07:35 [LOGON] [10753] contoso: SamLogon: Transitive Network logon of contoso\Nagappan.Veerappan from  (via LOB11-RADIUS) Returns 0xC000006A

진단 캡처를 구성할 수 있도록 NSG의 내 DC에 대한 RDP를 백 엔드에 사용하도록 설정합니다(즉, netlogon).
[인바운드 보안 규칙](alert-nsg.md#inbound-security-rules)

기본 NSG를 이미 수정한 경우 [포트 3389 - 원격 데스크톱을 사용한 관리](network-considerations.md#port-3389---management-using-remote-desktop) 단계를 수행합니다.

모든 서버에서 Netlogon 로그를 사용하도록 설정하려면 [Netlogon 서비스에 대한 디버그 로깅 사용](/troubleshoot/windows-client/windows-security/enable-debug-logging-netlogon-service) 단계를 수행합니다.

## <a name="next-steps"></a>다음 단계

계정 잠금 임계값을 조정하는 세분화된 암호 정책에 대한 자세한 내용은 [암호 및 계정 잠금 정책 구성][configure-fgpp]을 참조하세요.

가상 머신을 관리되는 도메인에 연결하는 데 문제가 있는 경우 [도움말을 찾고 Azure Active Directory에 대한 지원 티켓을 엽니다][azure-ad-support].

<!-- INTERNAL LINKS -->
[configure-fgpp]: password-policy.md
[security-audit-events]: security-audit-events.md
[azure-ad-support]: ../active-directory/fundamentals/active-directory-troubleshooting-support-howto.md
