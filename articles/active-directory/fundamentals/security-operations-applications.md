---
title: 애플리케이션에 대한 Azure Active Directory 보안 작업
description: 애플리케이션을 모니터링하고 경고하여 보안 위협을 식별하는 방법을 알아봅니다.
services: active-directory
author: BarbaraSelden
manager: martinco
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 07/15/2021
ms.author: baselden
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: aaa8f116680b3876eebb2b96a4f4c5e40d6384a2
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122528526"
---
# <a name="azure-active-directory-security-operations-guide-for-applications"></a>애플리케이션에 대한 Azure Active Directory 보안 작업 가이드

애플리케이션은 보안 위반에 대한 공격 표면을 제공하므로 모니터링해야 합니다. 사용자 계정만큼 자주 대상이 되지는 않지만 위반이 발생할 수 있습니다. 애플리케이션이 사용자 개입 없이 실행되는 경우가 많으므로 공격을 탐지하기가 더 어려울 수 있습니다.

이 문서에서는 애플리케이션 이벤트를 모니터링하고 경고하는 지침을 제공합니다. 다음을 보장하기 위해 정기적으로 업데이트됩니다.

* 악의적인 애플리케이션에서 부당하게 데이터에 액세스하지 못하도록 방지합니다.

* 악의적인 행위자가 기존 애플리케이션을 손상시키지 못하도록 방지합니다.

* 새 애플리케이션을 더 안전하게 빌드하고 구성할 수 있도록 하는 인사이트를 수집합니다.

애플리케이션이 Azure AD(Active Directory)에서 작동하는 방법에 익숙하지 않은 경우 [Azure AD의 앱 및 서비스 주체](../develop/app-objects-and-service-principals.md)를 참조하세요.

> [!NOTE]
> [Azure Active Directory 보안 작업 개요](security-operations-introduction.md)를 아직 검토하지 않은 경우 지금 검토하는 것이 좋습니다.

## <a name="what-to-look-for"></a>살펴볼 항목

응용 프로그램 로그에서 보안 인시던트를 모니터링할 때 다음을 검토하여 정상적인 활동과 악의적인 활동을 구분합니다. 다음 이벤트는 보안 문제를 나타낼 수 있으며, 각각은 이 문서의 나머지 부분에서 설명합니다.

* 일반 비즈니스 프로세스 및 일정을 벗어나서 발생하는 모든 변경

* 애플리케이션 자격 증명 변경

* 애플리케이션 사용 권한

   * Azure AD 또는 Azure RBAC 역할에 할당된 서비스 주체

   * 높은 권한이 있는 권한이 부여된 애플리케이션

   * Azure Key Vault 변경

   * 최종 사용자의 애플리케이션 동의 부여

   * 위험 수준에 따른 최종 사용자 동의 중지

* 애플리케이션 구성 변경

   * URI(Universal Resource Identifier) 변경 또는 비표준

   * 애플리케이션 소유자 변경

   * 로그아웃 URL 수정

## <a name="where-to-look"></a>살펴볼 위치

조사 및 모니터링에 사용하는 로그 파일은 다음과 같습니다.

* [Azure AD 감사 로그](../reports-monitoring/concept-audit-logs.md)

* [로그인 로그](../reports-monitoring/concept-all-sign-ins.md)

* [Microsoft 365 감사 로그](/microsoft-365/compliance/auditing-solutions-overview?view=o365-worldwide)

* [Azure Key Vault 로그](../../key-vault/general/logging.md)

Azure Portal에서 Azure AD 감사 로그를 보고, CSV(쉼표로 구분된 값) 또는 JSON(JavaScript Object Notation) 파일로 다운로드할 수 있습니다. Azure Portal에는 모니터링 및 경고를 더 효율적으로 자동화하도록 허용하는 다른 도구와 Azure AD 로그를 통합하는 몇 가지 방법이 있습니다.

* **[Azure Sentinel](../../sentinel/overview.md)** - SIEM(보안 정보 및 이벤트 관리) 기능을 제공하여 엔터프라이즈 수준에서 인텔리전트 보안 분석을 가능하게 합니다. 

* **[Azure Monitor](../../azure-monitor/overview.md)** - 다양한 조건에 대한 자동화된 모니터링 및 경고를 가능하게 합니다. 통합 문서를 만들거나 사용하여 다른 원본의 데이터를 결합할 수 있습니다.

* **SIEM과 통합된 [Azure Event Hubs](../../event-hubs/event-hubs-about.md)** - [Azure AD 로그는 Splunk, ArcSight, QRadar 및 Sumo Logic과 같은 다른 SIEM에 통합](../reports-monitoring/tutorial-azure-monitor-stream-logs-to-event-hub.md)할 수 있습니다.

* **[MCAS](/cloud-app-security/what-is-cloud-app-security)(Microsoft Cloud App Security)** – 앱 검색 및 관리, 앱과 리소스 제어 및 클라우드 앱의 규정 준수 확인을 가능하게 합니다.

모니터링하고 경고할 항목의 대부분은 조건부 액세스 정책의 영향입니다. [조건부 액세스 인사이트 및 보고 통합 문서](../conditional-access/howto-conditional-access-insights-reporting.md)를 사용하여 로그인에 대한 하나 이상의 조건부 액세스 정책과 디바이스 상태를 포함한 정책 결과를 조사할 수 있습니다. 이 통합 문서를 사용하면 영향 요약을 보고 특정 기간 동안의 영향을 식별할 수 있습니다. 통합 문서를 사용하여 특정 사용자의 로그인을 조사할 수도 있습니다. 

 이 문서의 나머지 부분은 모니터링하고 경고하는 데 권장되는 대상에 대해 설명하며, 위협 유형별로 구성되어 있습니다. 미리 빌드된 특정 솔루션이 있는 경우 해당 솔루션에 연결하거나 표 뒤에서 샘플을 제공합니다. 그렇지 않으면 이전 도구를 사용하여 경고를 작성할 수 있습니다. 

## <a name="application-credentials"></a>애플리케이션 자격 증명

많은 애플리케이션에서 자격 증명을 사용하여 Azure AD에서 인증합니다. 필요한 프로세스 외부에 추가된 추가 자격 증명은 해당 자격 증명을 사용하는 악의적인 행위자가 될 수 있습니다. 클라이언트 암호를 사용하는 대신 신뢰할 수 있는 기관에서 발급한 X509 인증서 또는 관리 ID를 사용하는 것이 좋습니다. 그러나 클라이언트 암호를 사용해야 하는 경우 예방 조치 모범 사례에 따라 애플리케이션을 안전하게 유지합니다. 애플리케이션 및 서비스 주체 업데이트는 두 개의 항목으로 감사 로그에 기록됩니다. 

* 애플리케이션을 모니터링하여 자격 증명 만료 시간이 긴 애플리케이션을 식별합니다.

* 수명이 긴 자격 증명을 수명이 짧은 자격 증명으로 바꿉니다. 자격 증명이 코드 리포지토리에서 커밋되지 않고 안전하게 저장되도록 하는 단계를 수행합니다.


| 모니터링 대상| 위험 수준| Where| 필터/하위 필터| 참고 |
| -|-|-|-|-|
| 기존 애플리케이션에 추가된 자격 증명| 높음| Azure AD 감사 로그| Service-Core 디렉터리, Category-ApplicationManagement <br>작업: Application-Certificates 및 비밀 관리 업데이트<br>및<br>작업: 서비스 주체 업데이트/애플리케이션 업데이트| 자격 증명이 다음과 같은 경우 경고합니다.<li> 일반 업무 시간 또는 워크플로를 벗어나서 추가되었습니다.<li> 사용자 환경에서 사용되지 않는 형식입니다.<li> 서비스 주체를 지원하는 비 SAML 흐름에 추가되었습니다. |
| 수명이 정책에서 허용하는 것보다 긴 자격 증명| 중간| Microsoft Graph| 애플리케이션 키 자격 증명의 상태 및 종료 날짜<br>및<br>애플리케이션 암호 자격 증명| MS Graph API를 사용하여 자격 증명의 시작 및 종료 날짜를 확인하고, 허용 수명보다 긴 자격 증명을 평가할 수 있습니다. 이 표 뒤에 나오는 PowerShell 스크립트를 참조하세요. |

 다음과 같은 미리 작성된 모니터링 및 경고를 사용할 수 있습니다.

* Azure Sentinel – [새 앱 또는 서비스 주체 자격 증명이 추가될 때 경고](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/AuditLogs/NewAppOrServicePrincipalCredential.yaml) 

* Azure Monitor – [Soligate 위험을 평가하는 데 도움이 되는 Azure AD 통합 문서 - Microsoft Tech Community](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/azure-ad-workbook-to-help-you-assess-solorigate-risk/ba-p/2010718)

* MCAS – [Cloud App Security 변칙 검색 경고 조사 가이드](/cloud-app-security/investigate-anomaly-alerts)

* PowerShell - [자격 증명 수명을 확인하는 PowerShell 스크립트 샘플](https://github.com/madansr7/appCredAge)

## <a name="application-permissions"></a>애플리케이션 사용 권한

관리자 계정과 마찬가지로 권한 있는 역할을 애플리케이션에 할당할 수 있습니다. 앱에는 Azure AD 역할(예: 전역 관리자) 또는 Azure RBAC 역할(예: 구독 소유자)을 할당할 수 있습니다. 사용자 없이 백그라운드 서비스로 실행할 수 있으므로 애플리케이션에 높은 권한이 있는 역할 또는 권한이 부여될 때마다 면밀히 모니터링합니다. 

### <a name="service-principal-assigned-to-a-role"></a>역할에 할당된 서비스 주체


| 모니터링 대상| 위험 수준| Where| 필터/하위 필터| 참고 |
|-|-|-|-|-|
| Azure RBAC 역할 또는 Azure AD 역할에 할당된 앱| 높음에서 중간까지| Azure AD 감사 로그| 유형: 서비스 사용자<br>작업: "역할에 멤버 추가" 또는 "역할에 적격 멤버 추가"<br>또는<br>"역할에 범위가 지정된 멤버 추가"| 높은 권한이 있는 역할(예: 전역 관리자)의 경우 위험은 높음입니다. 낮은 권한이 있는 역할의 경우 위험은 중간입니다. 애플리케이션이 일반 변경 관리 또는 구성 절차를 벗어나서 Azure 역할 또는 Azure AD 역할에 할당될 때마다 경고합니다. |

### <a name="application-granted-highly-privileged-permissions"></a>높은 권한이 있는 권한이 부여된 애플리케이션

애플리케이션은 최소 권한의 원칙도 따라야 합니다. 애플리케이션 권한을 조사하여 실제로 필요한지 확인합니다. 기존 애플리케이션을 식별하고 권한 있는 권한을 강조 표시하는 데 도움이 되는 [앱 동의 부여 보고서](https://aka.ms/getazureadpermissions)를 만들 수 있습니다.

| 모니터링 대상|위험 수준|Where| 필터/하위 필터| 참고|
|-|-|-|-|-|
| " *.All"(Directory.ReadWrite.All) 권한 또는 광범위한 권한(Mail.* )과 같은 높은 권한이 있는 권한이 부여된 앱| 높음 |Azure AD 감사 로그| "서비스 주체에 앱 역할 할당 추가" <br>이 경우<br> Target에서 중요한 데이터가 있는 API(예: Microsoft Graph)를 식별합니다. <br>및<br>AppRole.Value에서 높은 권한이 있는 애플리케이션 권한(앱 역할)을 식별합니다.| " *.All"(Directory.ReadWrite.All) 권한 또는 광범위한 권한(Mail.* )과 같은 광범위한 권한이 부여된 앱 |
| 애플리케이션 권한(앱 역할) 또는 높은 권한이 있는 위임된 권한을 부여하는 관리자 |높음| Microsoft 365 포털| "서비스 주체에 앱 역할 할당 추가" <br>이 경우<br>Target에서 중요한 데이터가 있는 API(예: Microsoft Graph)를 식별합니다.<br>"위임된 권한 부여 추가", <br>이 경우<br>Target에서 중요한 데이터가 있는 API(예: Microsoft Graph)를 식별합니다. <br>및<br>DelegatedPermissionGrant.Scope에는 높은 권한이 포함됩니다.| 전역 관리자, 애플리케이션 관리자 또는 클라우드 애플리케이션 관리자가 애플리케이션에 동의하면 경고합니다. 특히 일반 작업 및 변경 절차를 벗어난 동의를 찾습니다. |
| Microsoft Graph, Exchange, SharePoint 또는 Azure AD에 대한 권한이 애플리케이션에 부여됨 |높음| Azure AD 감사 로그| "위임된 권한 부여 추가" <br>또는<br>"서비스 주체에 앱 역할 할당 추가" <br>이 경우<br>Target에서 중요한 데이터가 있는 API(예: Microsoft Graph, Exchange Online 등)를 식별합니다.| 이전 행에서와 같이 경고합니다. |
| 다른 API에 대한 애플리케이션 권한(앱 역할)이 부여됨 |중간| Azure AD 감사 로그| "서비스 주체에 앱 역할 할당 추가" <br>이 경우<br>Target에서 다른 API를 식별합니다.| 이전 행에서와 같이 경고합니다. |
| 모든 사용자를 대신하여 높은 권한이 있는 위임된 권한이 부여됨 |높음| Azure AD 감사 로그| "위임된 권한 부여 추가", 여기서는 Target에서 중요한 데이터가 있는 API(예: Microsoft Graph)를 식별합니다. <br> DelegatedPermissionGrant.Scope에는 높은 권한이 있는 권한이 포함됩니다. <br>및<br>DelegatedPermissionGrant.ConsentType은 "AllPrincipals"입니다.| 이전 행에서와 같이 경고합니다. |

앱 권한을 모니터링하는 방법에 대한 자세한 내용은 [위험한 OAuth 앱 조사 및 수정](/cloud-app-security/investigate-risky-oauth) 자습서를 참조하세요.

### <a name="azure-key-vault"></a>Azure Key Vault

Azure Key Vault를 사용하여 테넌트의 비밀을 저장할 수 있습니다. 특히 Key Vault 구성 및 작업에 대한 변경 내용에 주의하는 것이 좋습니다. 

| 모니터링 대상| 위험 수준| Where| 필터/하위 필터| 참고 |
|-|-|-|-|-|
| Key Vault에 액세스하는 방법, 시기 및 사용자| 중간| [Azure Key Vault 로그](../../key-vault/general/logging.md?tabs=Vault)| 리소스 종류: Key Vault| 살펴볼 항목 <li> 일반 프로세스 및 시간을 벗어난 Key Vault에 대한 액세스 <li> Key Vault ACL에 대한 변경 내용 |

Azure Key Vault가 설정되면 [Key Vault에 액세스하는 방법과 시기](../../key-vault/general/logging.md?tabs=Vault)를 보여 주는 로깅을 [사용하도록 설정](../../key-vault/general/howto-logging.md?tabs=azure-cli)하고, 상태에 영향을 주는 경우 이메일, 전화 통화, 문자 메시지 또는 [이벤트 그리드](../../key-vault/general/event-grid-overview.md) 알림을 통해 할당된 사용자 또는 배포 목록에 알리도록 Key Vault에 대한 [경고를 구성](../../key-vault/general/alert.md)해야 합니다. 또한 Key Vault 인사이트를 사용하는 [모니터링](../../key-vault/general/alert.md)이 설정되면 Key Vault 요청, 성능, 오류 및 대기 시간에 대한 스냅샷이 제공됩니다. [Log Analytics](../../azure-monitor/logs/log-analytics-overview.md)에는 Key Vault를 선택한 다음, "모니터링" 아래에서 "로그"를 선택한 후에 액세스할 수 있는 Azure Key Vault에 대한 몇 가지 [예제 쿼리](../../azure-monitor/logs/queries.md)도 있습니다.

### <a name="end-user-consent"></a>최종 사용자 동의

| 모니터링 대상| 위험 수준| Where| 필터/하위 필터| 참고 |
|-|-|-|-|-|
| 애플리케이션에 대한 최종 사용자 동의| 낮음| Azure AD 감사 로그| 작업: 애플리케이션에 동의/ConsentContext.IsAdminConsent = false| 살펴볼 항목: <li>높은 프로필 또는 높은 권한이 있는 계정<li> 앱 요청 높은 위험 수준 권한<li>이름이 의심스러운 앱(예: 일반 앱, 철자가 틀린 앱 등) |


애플리케이션에 동의하는 행위 자체가 악의적인 것은 아닙니다. 그러나 의심스러운 애플리케이션을 찾는 새로운 최종 사용자 동의 부여를 조사합니다. [사용자 동의 작업을 제한](../../security/fundamentals/steps-secure-identity.md)할 수 있습니다.

동의 작업에 대한 자세한 내용은 다음 리소스를 참조하세요.

* [Azure Active Directory에서 애플리케이션에 대한 동의 관리 및 동의 요청 평가](../manage-apps/manage-consent-requests.md)

* [불법 동의 부여 검색 및 수정 - Office 365](/microsoft-365/security/office-365-security/detect-and-remediate-illicit-consent-grants?view=o365-worldwide)

* [인시던트 대응 플레이북 - 앱 동의 부여 조사](/security/compass/incident-response-playbook-app-consent)

### <a name="end-user-stopped-due-to-risk-based-consent"></a>위험 기반 동의로 인해 중지된 최종 사용자 

| 모니터링 대상| 위험 수준| Where| 필터/하위 필터| 참고 |
|-|-|-|-|-|
| 위험 기반 동의로 인해 중지된 최종 사용자 동의| 중간| Azure AD 감사 로그| 핵심 디렉터리/ApplicationManagement/애플리케이션에 동의<br> 오류 상태 이유 = Microsoft.online.Security.userConsent<br>BlockedForRiskyAppsExceptions| 위험으로 인해 동의가 중지될 때마다 모니터링하고 분석합니다. 살펴볼 항목:<li>높은 프로필 또는 높은 권한이 있는 계정<li> 앱 요청 높은 위험 수준 권한<li>이름이 의심스러운 앱(예: 일반 앱, 철자가 틀린 앱 등) |

## <a name="application-configuration-changes"></a>애플리케이션 구성 변경

애플리케이션의 구성에 대한 변경 내용을 모니터링합니다. 특히 URI(Uniform Resource Identifier), 소유권 및 로그아웃 URL에 대한 구성이 변경됩니다.

### <a name="dangling-uri-and-redirect-uri-changes"></a>짝이 맞지 않는 URI 및 리디렉션 URI 변경 

| 모니터링 대상| 위험 수준| Where| 필터/하위 필터| 참고 |
|-|-|-|-|-|
| 짝이 맞지 않는 URI| 높음| Azure AD 로그 및 애플리케이션 등록| Service-Core 디렉터리, Category-ApplicationManagement<br>작업: 애플리케이션 업데이트<br>성공 – AppAddress 속성 이름| 예를 들어 더 이상 존재하지 않거나 명시적으로 소유하지 않은 도메인 이름을 가리키는 짝이 맞지 않는 URI를 찾습니다. |
| 리디렉션 URI 구성 변경| 높음| Azure AD 로그| Service-Core 디렉터리, Category-ApplicationManagement<br>작업: 애플리케이션 업데이트<br>성공 – AppAddress 속성 이름| HTTPS*를 사용하지 않는 URI, 와일드카드가 URL의 끝 또는 도메인에 있는 URI, 애플리케이션에 고유하지 않은 URI, 제어하지 않는 도메인을 가리키는 URI를 찾습니다. |

이러한 변경 내용이 검색될 때마다 경고합니다.

### <a name="appid-uri-added-modified-or-removed"></a>추가, 수정 또는 제거된 AppID URI


| 모니터링 대상| 위험 수준| Where| 필터/하위 필터| 참고 |
|-|-|-|-|-|
| AppID URI 변경| 높음| Azure AD 로그| Service-Core 디렉터리, Category-ApplicationManagement<br>작업: 업데이트<br>애플리케이션<br>작업: 서비스 주체 업데이트| URI 추가, 수정 또는 제거와 같은 AppID URI 수정 사항을 찾습니다. |


승인된 변경 관리 절차를 벗어난 이러한 변경 내용이 검색될 때마다 경고합니다.

### <a name="new-owner"></a>새 소유자


| 모니터링 대상| 위험 수준| Where| 필터/하위 필터| 참고 |
|-|-|-|-|-|
| 애플리케이션 소유권 변경| 중간| Azure AD 로그| Service-Core 디렉터리, Category-ApplicationManagement<br>작업: 애플리케이션에 소유자 추가| 일반 변경 관리 작업을 벗어나서 애플리케이션 소유자로 추가되는 사용자의 인스턴스를 찾습니다. |

### <a name="logout-url-modified-or-removed"></a>수정되거나 제거된 로그아웃 URL

| 모니터링 대상| 위험 수준| Where| 필터/하위 필터| 참고 |
|-|-|-|-|-|
| 로그아웃 URL 변경| 낮음| Azure AD 로그| Service-Core 디렉터리, Category-ApplicationManagement<br>작업: 애플리케이션 업데이트<br>및<br>작업: 서비스 주체 업데이트| 로그아웃 URL에 대한 수정 사항을 찾습니다. 빈 항목 또는 존재하지 않는 위치에 대한 항목이 있으면 사용자가 세션을 종료할 수 없습니다. |

## <a name="additional-resources"></a>추가 리소스

유용한 리소스에 대한 링크는 다음과 같습니다.

* Github Azure AD 도구 키트 - [https://github.com/microsoft/AzureADToolkit](https://github.com/microsoft/AzureADToolkit)

* Azure Key Vault 보안 개요 및 보안 지침 - [Azure Key Vault 보안 개요](../../key-vault/general/security-features.md)

* Solorgate 위험 정보 및 도구 - [Solorigate 위험에 액세스하는 데 도움이 되는 Azure AD 통합 문서](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/azure-ad-workbook-to-help-you-assess-solorigate-risk/ba-p/2010718)

* OAuth 공격 탐지 지침 - [OAuth 앱에 비정상적인 자격 증명 추가](/cloud-app-security/investigate-anomaly-alerts)

SIEM에 대한 Azure AD 모니터링 구성 정보 - [Azure Monitor 통합이 포함된 파트너 도구](../..//azure-monitor/essentials/stream-monitoring-data-event-hubs.md)

 ## <a name="next-steps"></a>다음 단계

다음 보안 작업 가이드 문서를 참조하세요.

[Azure AD 보안 작업 개요](security-operations-introduction.md)

[사용자 계정에 대한 보안 작업](security-operations-user-accounts.md)

[권한 있는 계정에 대한 보안 작업](security-operations-privileged-accounts.md)

[Privileged Identity Management에 대한 보안 작업](security-operations-privileged-identity-management.md)

[애플리케이션에 대한 보안 작업](security-operations-applications.md)

[디바이스에 대한 보안 작업](security-operations-devices.md)

 
[인프라에 대한 보안 작업](security-operations-infrastructure.md)