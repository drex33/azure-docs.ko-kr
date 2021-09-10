---
title: FedRAMP High Impact를 충족하도록 추가 컨트롤 구성
description: FedRAMP High Impact 수준을 충족하도록 추가 컨트롤을 구성하는 방법에 대한 자세한 지침입니다.
services: active-directory
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: how-to
author: barbaraselden
ms.author: baselden
manager: celested
ms.reviewer: martinco
ms.date: 4/26/2021
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: ed80ffbe92777066e09d443f3e5319ad721be653
ms.sourcegitcommit: 9ad20581c9fe2c35339acc34d74d0d9cb38eb9aa
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/27/2021
ms.locfileid: "110540301"
---
# <a name="configure-additional-controls-to-achieve-fedramp-high-impact-level"></a>FedRAMP High Impact 수준을 달성하도록 추가 컨트롤 구성

다음 제어 목록(및 제어 개선 사항)에는 Azure AD(Azure Active Directory) 테넌트의 구성이 필요할 수 있습니다.

다음 표의 각 행은 규범적 지침을 제공합니다. 이 지침은 제어 또는 제어 개선 사항과 관련된 공유 책임에 대한 조직의 대응을 개발하는 데 도움이 됩니다.

## <a name="audit-and-accountability"></a>감사 및 책임

다음 표의 지침은 다음과 관련이 있습니다.

* AU-02 감사 이벤트
* AU-03 감사 내용
* AU-06 감사 검토, 분석, 보고

| 컨트롤 ID 및 하위 부분| 고객 책임 및 지침 |
| - | - |
| AU-02 <br>AU-03 <br>AU-03(1)<br>AU-03(2)| 시스템이 AU-02 파트 a에 정의된 이벤트를 감사할 수 있는지 확인합니다. 사후 조사를 지원하기 위해 조직의 감사 가능한 이벤트 하위 집합 내의 다른 엔터티와 조정합니다. 감사 레코드의 중앙 집중식 관리를 구현합니다.<p>모든 계정 수명 주기 작업(계정 만들기, 수정, 사용, 사용 안 함, 제거 작업)은 Azure AD 감사 로그 내에서 감사됩니다. 모든 인증 및 권한 부여 이벤트는 Azure AD 로그인 로그 내에서 감사되며 탐지된 모든 위험은 ID 보호 로그에서 감사됩니다. 이러한 각 로그를 Azure Sentinel과 같은 SIEM(보안 정보 및 이벤트 관리) 솔루션으로 직접 스트리밍할 수 있습니다. 또는 Azure Event Hubs를 사용하여 타사 SIEM 솔루션과 로그를 통합합니다.<p>감사 이벤트<li> [Azure Active Directory 포털의 감사 활동 보고서](../reports-monitoring/concept-audit-logs.md)<li> [Azure Active Directory 포털의 로그인 활동 보고서](../reports-monitoring/concept-sign-ins.md)<li>[방법: 위험 조사](../identity-protection/howto-identity-protection-investigate-risk.md)<p>SIEM 통합<li> [Azure Sentinel: Azure AD(Azure Active Directory)에서 데이터 연결](../../sentinel/connect-azure-active-directory.md)<li>[Azure 이벤트 허브 및 기타 SIEM으로 스트림](../reports-monitoring/tutorial-azure-monitor-stream-logs-to-event-hub.md) |
| AU-06<br>AU-06(1)<br>AU-06(3)<br>AU-06(4)<br>AU-06(5)<br>AU-06(6)<br>AU-06(7)<br>AU-06(10)<br>| 매주 한 번 이상 감사 레코드를 검토하고 분석하여 부적절하거나 비정상적인 활동을 파악하여 결과를 담당 직원에게 보고합니다. <p>AU-02 및 AU-03에 대해 제공된 이전 지침에서는 감사 기록을 매주 검토하고 해당 담당자에게 보고할 수 있습니다. Azure AD만 사용하여 이러한 요구 사항을 충족할 수는 없습니다. Azure Sentinel과 같은 SIEM 솔루션도 사용해야 합니다. 자세한 내용은 [이란?](../../sentinel/overview.md)을 참조하세요. |

## <a name="incident-response"></a>사고 대응

다음 표의 지침은 다음과 관련이 있습니다.

* IR-04 인시던트 처리

* IR-05 인시던트 모니터링

| 컨트롤 ID 및 하위 파트| 고객 책임 및 지침 |
| - | - |
| IR-04<br>IR-04(1)<br>IR-04(2)<br>IR-04(3)<br>IR-04(4)<br>IR-04(6)<br>IR-04(8)<br>IR-05<br>IR-05(1)| 인시던트 처리 및 모니터링 기능을 구현합니다. 여기에는 자동 인시던트 처리, 동적 재구성, 작업 연속성, 정보 상관 관계, 내부자 위협, 외부 조직과의 상관 관계, 인시던트 모니터링 및 자동 추적이 포함됩니다. <p>감사 로그는 모든 구성 변경 내용을 기록합니다. 인증 및 권한 부여 이벤트는 로그인 로그 내에서 감사되며 탐지된 모든 위험은 ID 보호 로그에서 감사됩니다. 이러한 각 로그를 Azure Sentinel과 같은 SIEM 솔루션으로 직접 스트리밍할 수 있습니다. 또는 Azure Event Hubs를 사용하여 타사 SIEM 솔루션과 로그를 통합합니다. Microsoft Graph 또는 Azure AD PowerShell을 사용하여 SIEM 내의 이벤트를 기반으로 동적 재구성을 자동화합니다.<p>감사 이벤트<br><li>[Azure Active Directory 포털의 감사 활동 보고서](../reports-monitoring/concept-audit-logs.md)<li>[Azure Active Directory 포털의 로그인 활동 보고서](../reports-monitoring/concept-sign-ins.md)<li>[방법: 위험 조사](../identity-protection/howto-identity-protection-investigate-risk.md)<p>SIEM 통합<li>[Azure Sentinel: Azure AD(Azure Active Directory)에서 데이터 연결](../../sentinel/connect-azure-active-directory.md)<li>[Azure 이벤트 허브 및 기타 SIEM으로 스트림](../reports-monitoring/tutorial-azure-monitor-stream-logs-to-event-hub.md)<p>동적 재구성<li>[AzureAD 모듈](/powershell/module/azuread/)<li>[Microsoft Graph 개요](/graph/overview?view=graph-rest-1.0&preserve-view=true) |

## <a name="personnel-security"></a>직원 보안

다음 표의 지침은 다음과 관련이 있습니다.

* PS-04 직원 고용 종료

| 컨트롤 ID 및 하위 파트| 고객 책임 및 지침 |
| - | - |
| PS-04<br>PS-04(2)| 시스템에 대한 액세스 비활성화를 담당하는 직원에게 자동으로 알립니다. <p>계정을 사용하지 않도록 설정하고 8시간 이내에 연결된 모든 인증자 및 자격 증명을 해지합니다. <p>외부 HR 시스템, 온-프레미스 Active Directory 또는 클라우드에서 직접 Azure AD 계정의 프로비저닝(종료 시 비활성화 포함)을 구성합니다. 기존 세션을 해지하여 모든 시스템 액세스를 종료합니다. <p>계정 프로비전<li> AC-02의 자세한 지침을 참조하세요. <p>연결된 모든 인증자 해지 <li> [Azure Active Directory에서 긴급 상황 시 사용자 액세스 해지](../enterprise-users/users-revoke-access.md) |


## <a name="system-and-information-integrity"></a>시스템 및 정보 무결성

다음 표의 지침은 다음과 관련이 있습니다.

* SI-04 정보 시스템 모니터링

 컨트롤 ID 및 하위 파트| 고객 책임 및 지침 |
| - | - |
| SI-04<br>SI-04(1)| 정보 시스템 전체 모니터링 및 침입 탐지 시스템을 구현합니다. <p>정보 시스템 모니터링 솔루션 내에 모든 Azure AD 로그(감사, 로그인, ID 보호)를 포함합니다. <p>Azure AD 로그를 SIEM 솔루션으로 스트림합니다(IA-04 참조). |

## <a name="next-steps"></a>다음 단계

[액세스 제어 구성](fedramp-access-controls.md)

[식별 및 인증 컨트롤 구성](fedramp-identification-and-authentication-controls.md)

[기타 컨트롤 구성](fedramp-other-controls.md)
 