---
title: Privileged Identity Management에 대한 Azure Active Directory 보안 작업
description: 기준을 설정하고 Azure Active Directory PIM(Privileged Identity Management)을 사용하여 PIM으로 관리되는 계정의 잠재적인 문제를 모니터링하고 경고하는 방법에 대한 지침입니다.
services: active-directory
author: BarbaraSelden
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 07/15/2021
ms.author: baselden
ms.reviewer: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 42a7b541b3f5b5c5d1ec462898615793d50b5493
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122535705"
---
# <a name="azure-active-directory-security-operations-for-privileged-identity-management-pim"></a>PIM(Privileged Identity Management)에 대한 Azure Active Directory 보안 작업

비즈니스 자산의 보안은 IT 시스템을 관리하는 권한 있는 계정의 무결성에 따라 달라집니다. 사이버 공격자는 관리자 계정과 기타 권한 있는 액세스 계정을 대상으로 하는 자격 증명 도난 공격을 사용하여 중요한 데이터에 액세스하려고 합니다.

클라우드 서비스의 경우 예방 및 대응은 클라우드 서비스 공급자와 고객의 공동 책임입니다. 

기존에는 조직 보안이 보안 경계인 네트워크 진입 및 출구 지점에 중점을 두었습니다. 그러나 이 방법은 SaaS 앱과 개인 디바이스에 효과적이지 않았습니다. Azure AD(Azure Active Directory)에서는 네트워크 보안 경계를 조직 ID 계층의 인증으로 바꿉니다. 사용자에게 권한 있는 관리 역할이 할당되기 때문에 온-프레미스, 클라우드, 하이브리드 환경에서 해당 액세스를 보호해야 합니다. 

사용자는 온-프레미스 IT 환경에 대한 모든 보안 계층을 전적으로 책임져야 합니다. Azure 클라우드 서비스를 사용하는 경우 예방 및 대응은 클라우드 서비스 공급자(Microsoft)와 고객(사용자)의 공동 책임입니다. 

* 공유 책임 모델에 대한 자세한 내용은 [클라우드의 공유 책임](../../security/fundamentals/shared-responsibility.md)을 참조하세요.

* 권한 있는 사용자의 액세스를 보호하는 방법에 대한 자세한 내용은 [Azure AD에서 하이브리드 및 클라우드 배포를 위한 권한 있는 액세스 보안](../roles/security-planning.md)을 참조하세요.

* 권한 있는 ID의 주요 개념에 대한 다양한 비디오, 방법 가이드, 콘텐츠는 [Privileged Identity Management 설명서](../privileged-identity-management/index.yml)를 참조하세요. 

PIM(Privileged Identity Management)은 조직의 중요한 리소스에 대한 액세스를 관리, 제어, 모니터링할 수 있도록 하는 Azure AD 서비스입니다. 이러한 리소스에는 Azure AD, Azure 및 Microsoft 365 또는 Microsoft Intune과 같은 기타 Microsoft Online Services의 리소스가 포함됩니다. PIM을 사용하여 다음과 같은 위험을 완화할 수 있습니다.

* 보안 정보와 리소스에 대한 액세스 권한이 있는 사람을 식별하고 인원수를 최소화합니다.

* 중요한 리소스에 대한 과도하거나, 불필요하거나, 잘못 사용된 액세스 권한을 검색합니다.

* 악의적인 작업자가 보안 정보나 리소스에 액세스할 가능성을 줄입니다.

* 권한 없는 사용자가 실수로 중요한 리소스에 영향을 줄 가능성을 줄입니다.

이 문서에서는 기준을 설정하고 권한 있는 계정의 로그인 및 사용을 감사하는 방법과 권한 있는 계정의 무결성을 유지하는 데 사용할 수 있는 감사 로그 원본에 대한 지침을 제공합니다. 

## <a name="where-to-look"></a>살펴볼 위치

조사 및 모니터링에 사용하는 로그 파일은 다음과 같습니다. 

* [Azure AD 감사 로그](../reports-monitoring/concept-audit-logs.md)

* [로그인 로그](../reports-monitoring/concept-all-sign-ins.md)

* [Microsoft 365 감사 로그](/microsoft-365/compliance/auditing-solutions-overview?view=o365-worldwide) 

* [Azure Key Vault 로그](../../key-vault/general/logging.md?tabs=Vault)

Azure Portal에서 Azure AD 감사 로그를 보고, CSV(쉼표로 구분된 값) 또는 JSON(JavaScript Object Notation) 파일로 다운로드할 수 있습니다. Azure Portal에는 모니터링과 경고를 더 효율적으로 자동화하는 다른 도구와 Azure AD 로그를 통합하는 몇 가지 방법이 있습니다.

* [**Azure Sentinel**](../../sentinel/overview.md) - SIEM(보안 정보 및 이벤트 관리) 기능을 제공하여 엔터프라이즈 수준에서 인텔리전트 보안 분석을 지원합니다. 

* [**Azure Monitor**](../../azure-monitor/overview.md) - 다양한 조건을 자동으로 모니터링하고 경고할 수 있습니다. 통합 문서를 만들거나 사용하여 다양한 원본의 데이터를 결합할 수 있습니다.

* **SIEM과 통합된** [**Azure Event Hubs**](../../event-hubs/event-hubs-about.md)- Azure Event Hub 통합을 통해 Splunk, ArcSight, QRadar, Sumo Logic과 같은 [다른 SIEM에 Azure AD 로그를 통합할 수 있습니다](../reports-monitoring/tutorial-azure-monitor-stream-logs-to-event-hub.md).

* [**MCAS(Microsoft Cloud App Security)** ](/cloud-app-security/what-is-cloud-app-security) - 앱을 검색 및 관리하고, 앱과 리소스를 제어하고, 클라우드 앱의 규정 준수를 확인할 수 있습니다. 

이 문서의 나머지 부분에서는 모니터링 및 경고 기준을 설정하는 방법에 대한 권장 사항을 계층 모델로 구성하여 제공합니다. 표 아래에는 미리 빌드된 솔루션 링크가 나와 있습니다. 앞의 도구를 사용하여 경고를 빌드할 수도 있습니다. 콘텐츠는 PIM의 다음 토픽 영역에 구성되어 있습니다.

* 기준

* Azure AD 역할 할당 

* Azure AD 역할 경고 설정

* Azure 리소스 역할 할당

* Azure 리소스에 대한 액세스 관리

* Azure 구독을 관리하도록 권한이 상승된 액세스

## <a name="baselines"></a>기준

권장되는 기준 설정은 다음과 같습니다.

| 모니터링 대상| 위험 수준| 권장| 역할| 참고 |
| - |- |- |- |- |
| Azure AD 역할 할당| 높음| <li>활성화 사유가 필요합니다.<li>활성화하려면 승인이 필요합니다.<li>2단계 승인자 프로세스를 설정합니다.<li>활성화 시 Azure Active Directory MFA(Multi-Factor Authentication)가 필요합니다.<li>최대 권한 상승 기간을 8시간으로 설정합니다.| <li>권한 있는 역할 관리<li>전역 관리자| 권한 있는 역할 관리자는 적격 역할 할당을 활성화하는 사용자 환경 변경을 포함하여 Azure AD 조직의 PIM을 사용자 지정할 수 있습니다. |
| Azure 리소스 역할 구성| 높음| <li>활성화 사유가 필요합니다.<li>활성화하려면 승인이 필요합니다.<li>2단계 승인자 프로세스를 설정합니다.<li>활성화 시 Azure MFA가 필요합니다.<li>최대 권한 상승 기간을 8시간으로 설정합니다.| <li>소유자<li>리소스 관리자<li>사용자 액세스 권한 <li>관리자<li>전역 관리자<li>보안 관리자| 계획된 변경이 아닌 경우 즉시 조사합니다. 이 설정을 사용하면 공격자가 사용자 환경의 Azure 구독에 액세스할 수 있습니다. |


## <a name="azure-ad-roles-assignment"></a>Azure AD 역할 할당

권한 있는 역할 관리자는 Azure AD 조직의 PIM을 사용자 지정할 수 있습니다. 여기에는 적격 역할 할당을 활성화하는 사용자 환경을 다음과 같이 변경하는 작업이 포함됩니다.

* 잘못된 작업자가 권한 있는 액세스를 활성화하기 위한 Azure MFA 요구 사항을 제거할 수 없도록 합니다.

* 악의적인 사용자가 권한 있는 액세스 활성화 사유와 승인을 무시할 수 없도록 합니다.

| 모니터링 대상| 위험 수준| Where| 필터/하위 필터| 참고 |
| - |- |- |- |- |
| 권한 있는 계정 권한의 추가 변경 내용에 대한 경고| 높음| Azure AD 감사 로그| 범주 = 역할 관리<br>및<br>활동 유형 – 적격 구성원 추가(영구) <br>및<br>활동 유형 – 적격 구성원 추가(적격) <br>및<br>상태 = 성공/실패<br>및<br>수정된 속성 = Role.DisplayName| 권한 있는 역할 관리자와 전역 관리자에 대한 변경 내용을 모니터링하고 항상 경고합니다. <li>공격자가 역할 할당 설정을 수정할 수 있는 권한을 얻으려고 시도 중임을 나타낼 수 있습니다.<li> 정의된 임계값이 없는 경우 사용자는 60분에 4회, 권한 있는 계정은 60분에 2회 변경 시 경고합니다. |
| 권한 있는 계정 권한의 대량 삭제 변경 내용에 대한 경고| 높음| Azure AD 감사 로그| 범주 = 역할 관리<br>및<br>활동 유형 – 적격 구성원 제거(영구) <br>및<br>활동 유형 – 적격 구성원 제거(적격) <br>및<br>상태 = 성공/실패<br>및<br>수정된 속성 = Role.DisplayName| 계획된 변경이 아닌 경우 즉시 조사합니다. 이 설정을 사용하면 공격자가 사용자 환경의 Azure 구독에 액세스할 수 있습니다. |
| PIM 설정에 대한 변경 내용| 높음| Azure AD 감사 로그| 서비스 = PIM<br>및<br>범주 = 역할 관리<br>및<br>활동 유형 = PIM의 역할 설정 업데이트<br>및<br>상태 이유 = 활성화 시 MFA 사용 안 함(예)| 권한 있는 역할 관리자와 전역 관리자에 대한 변경 내용을 모니터링하고 항상 경고합니다. <li>공격자가 역할 할당 설정을 수정할 수 있는 권한을 이미 획득했음을 나타낼 수 있습니다.<li>작업 중 하나가 PIM 권한 상승의 보안을 약화시키고 공격자가 권한 있는 계정을 획득하기 쉽게 만들 수 있습니다. |
| 권한 상승 승인 및 거부| 높음| Azure AD 감사 로그| 서비스 = 액세스 검토<br>및<br>범주 = UserManagement<br>및<br>활동 유형 = 요청 승인/거부<br>및<br>시작된 작업자 = UPN| 모든 권한 상승을 모니터링해야 합니다. 공격 타임라인을 명확하게 나타낼 수 있으므로 모든 권한 상승을 로그합니다. |
| 경고 설정이 사용 안 함으로 변경됩니다.| 높음| Azure AD 감사 로그| 서비스 = PIM<br>및<br>범주 = 역할 관리<br>및<br>활동 유형 = PIM 경고 사용 안 함<br>및<br>상태 = 성공/실패| 항상 경고합니다. <li>권한 있는 액세스를 활성화하기 위한 Azure MFA 요구 사항과 관련된 경고를 제거하는 잘못된 작업자를 검색하는 데 도움이 됩니다.<li>의심스럽거나 안전하지 않은 활동을 탐지하는 데 도움이 됩니다. |


Azure AD 감사 로그에서 역할 설정 변경 내용을 식별하는 방법에 대한 자세한 내용은 [Privileged Identity Management에서 Azure AD 역할에 대한 감사 기록 보기](../privileged-identity-management/pim-how-to-use-audit-log.md)를 참조하세요. 

## <a name="azure-resource-role-assignment"></a>Azure 리소스 역할 할당

Azure 리소스 역할 할당을 모니터링하면 리소스 역할에 대한 활동과 활성화를 파악할 수 있습니다. 리소스에 대한 공격 표면을 만들기 위해 잘못 사용될 수도 있습니다. 이 유형의 활동을 모니터링하는 경우 다음을 검색하려고 합니다.

* 특정 리소스의 쿼리 역할 할당

* 모든 자식 리소스에 대한 역할 할당

* 모든 활성 및 적격 역할 할당 변경 내용

| 모니터링 대상| 위험 수준| Where| 필터/하위 필터| 참고 |
| - |- |- |- |- |
| 권한 있는 계정 활동에 대한 감사 경고 리소스 감사 로그| 높음| PIM에서, Azure 리소스 아래, 리소스 감사| 작업: PIM의 역할에 적격 구성원 추가가 완료됨(시간 제한) <br>및<br>기본 대상 <br>및<br>유형 사용자<br>및<br>상태 = 성공<br>| 항상 경고합니다. Azure의 모든 리소스를 관리할 적격 역할을 추가하는 잘못된 작업자를 검색하는 데 도움이 됩니다. |
| 경고 사용 안 함에 대한 감사 경고 리소스 감사| 중간| PIM에서, Azure 리소스 아래, 리소스 감사| 작업: 경고 사용 안 함<br>및<br>기본 대상: 너무 많은 소유자가 리소스에 할당됨<br>및<br>상태 = 성공| 경고 창에서 경고를 사용하지 않도록 설정하여 악의적인 활동이 조사되지 않도록 하는 잘못된 작업자를 검색하는 데 도움이 됩니다. |
| 경고 사용 안 함에 대한 감사 경고 리소스 감사| 중간| PIM에서, Azure 리소스 아래, 리소스 감사| 작업: 경고 사용 안 함<br>및<br>기본 대상: 너무 많은 영구 소유자가 리소스에 할당됨<br>및<br>상태 = 성공| 잘못된 작업자가 경고 창에서 경고를 사용하지 않도록 설정하여 악의적인 활동이 조사되지 않도록 할 수 없게 합니다. |
| 경고 사용 안 함에 대한 감사 경고 리소스 감사| 중간| PIM에서, Azure 리소스 아래, 리소스 감사| 작업: 경고 사용 안 함<br>및<br>기본 대상 중복 역할이 생성됨<br>및<br>상태 = 성공| 잘못된 작업자가 경고 창에서 경고를 사용하지 않도록 설정하여 악의적인 활동이 조사되지 않도록 할 수 없게 합니다. |


경고를 구성하고 Azure 리소스 역할을 감사하는 방법에 대한 자세한 내용은 다음을 참조하세요.

* [Privileged Identity Management에서 Azure 리소스 역할에 대한 보안 경고 구성](../privileged-identity-management/pim-resource-roles-configure-alerts.md)

* [PIM(Privileged Identity Management)에서 Azure 리소스 역할에 대한 감사 보고서 보기](../privileged-identity-management/azure-pim-resource-rbac.md)

## <a name="access-management-for-azure-resources-and-subscriptions"></a>Azure 리소스 및 구독에 대한 액세스 관리

소유자 또는 사용자 액세스 관리자 구독 역할에 할당된 사용자나 그룹 구성원과 Azure AD에서 구독 관리를 사용하도록 설정한 Azure AD 전역 관리자는 기본적으로 리소스 관리자 권한이 있습니다. 관리자는 Azure 리소스용 PIM(Privileged Identity Management)을 사용하여 역할을 할당하고, 역할 설정을 구성하며, 액세스를 검토할 수 있습니다. 

리소스 관리자 권한이 있는 사용자는 리소스용 PIM을 관리할 수 있습니다. 이로 인해 모니터링하고 완화해야 하는 위험은 이 기능을 통해 가상 머신이나 스토리지 계정과 같은 Azure 구독 리소스에 대한 권한 있는 액세스가 잘못된 작업자에게 부여될 수 있다는 것입니다.

| 모니터링 대상| 위험 수준| Where| 필터/하위 필터| 참고 |
| - |- |- |- |- |
| 권한 상승| 높음| Azure AD, 관리 아래, 속성| 정기적으로 설정을 검토합니다.<br>Azure 리소스에 대한 액세스 관리| 전역 관리자는 Azure 리소스에 대한 액세스 관리를 사용하도록 설정하여 권한을 상승시킬 수 있습니다.<br>잘못된 작업자가 Active Directory와 연결된 모든 Azure 구독과 관리 그룹에서 역할을 할당할 수 있는 권한을 얻지 않았는지 확인합니다. |


자세한 내용은 [Privileged Identity Management에서 Azure 리소스 역할 할당](../privileged-identity-management/pim-resource-roles-assign-roles.md)을 참조하세요.

## <a name="next-steps"></a>다음 단계
다음 보안 작업 가이드 문서를 참조하세요.

[Azure AD 보안 작업 개요](security-operations-introduction.md)

[사용자 계정에 대한 보안 작업](security-operations-user-accounts.md)

[권한 있는 계정에 대한 보안 작업](security-operations-privileged-accounts.md)

[Privileged Identity Management에 대한 보안 작업](security-operations-privileged-identity-management.md)

[애플리케이션에 대한 보안 작업](security-operations-applications.md)

[디바이스에 대한 보안 작업](security-operations-devices.md)
 
[인프라에 대한 보안 작업](security-operations-infrastructure.md)