---
title: 디바이스에 대한 Azure Active Directory 보안 작업
description: 기준을 설정하고 디바이스를 모니터링 및 보고하여 디바이스의 잠재적인 보안 위험을 식별하는 방법을 알아봅니다.
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
ms.openlocfilehash: bbe50839beb886d22ad05414e220781f87e5f75b
ms.sourcegitcommit: 91915e57ee9b42a76659f6ab78916ccba517e0a5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/15/2021
ms.locfileid: "130045467"
---
# <a name="azure-active-directory-security-operations-for-devices"></a>디바이스에 대한 Azure Active Directory 보안 작업

디바이스는 일반적으로 ID 기반 공격의 표적이 되지 않지만 보안 제어를 충족하고 속이거나 사용자를 가장하는 데 *사용될 수 있습니다*. 디바이스와 Azure AD와의 관계는 다음 네 가지 중 하나일 수 있습니다. 

* 등록 취소됨

* [Azure AD(Azure Active Directory) 등록됨](../devices/concept-azure-ad-register.md)

* [Azure AD 조인](../devices/concept-azure-ad-join.md)

* [하이브리드 Azure AD 조인](../devices/concept-azure-ad-join-hybrid.md)   
‎

등록 및 조인된 디바이스에는 기본 인증 아티팩트 및 경우에 따라 다단계 인증 아티팩트로 사용할 수 있는 [PRT(기본 새로 고침 토큰)](../devices/concept-primary-refresh-token.md)가 발급됩니다. 공격자는 자신의 디바이스를 등록하거나, 합법적인 디바이스에서 PRT를 사용하여 비즈니스 데이터에 액세스하거나, 합법적인 사용자 디바이스에서 PRT 기반 토큰을 훔치거나, Azure Active Directory의 디바이스 기반 컨트롤에서 잘못된 구성을 찾을 수 있습니다. 하이브리드 Azure AD 조인 디바이스를 사용하면 조인 프로세스가 시작되고 관리자가 제어하므로 사용 가능한 공격 방법이 줄어듭니다.

디바이스 통합 방법에 대한 자세한 내용은 [Azure AD 디바이스 배포 계획](../devices/plan-device-deployment.md) 문서에서 [통합 방법 선택](../devices/plan-device-deployment.md)을 참조하세요.

디바이스를 통해 인프라를 공격하는 악의적인 행위자의 위험을 줄이려면 다음을 모니터링합니다.

* 디바이스 등록 및 Azure AD 조인

* 애플리케이션에 액세스하는 비규격 디바이스

* BitLocker 키 검색

* 디바이스 관리자 역할

* 가상 머신에 로그인

## <a name="where-to-look"></a>확인할 위치

조사 및 모니터링에 사용하는 로그 파일은 다음과 같습니다. 

* [Azure AD 감사 로그](../reports-monitoring/concept-audit-logs.md)

* [로그인 로그](../reports-monitoring/concept-all-sign-ins.md)

* [Microsoft 365 감사 로그](/microsoft-365/compliance/auditing-solutions-overview) 

* [Azure Key Vault 로그](../..//key-vault/general/logging.md?tabs=Vault)

Azure Portal에서 Azure AD 감사 로그를 보고, CSV(쉼표로 구분된 값) 또는 JSON(JavaScript Object Notation) 파일로 다운로드할 수 있습니다. Azure Portal에는 모니터링 및 경고를 더 효율적으로 자동화하도록 허용하는 다른 도구와 Azure AD 로그를 통합하는 몇 가지 방법이 있습니다.

* **[Azure Sentinel](../../sentinel/overview.md)** - SIEM(보안 정보 및 이벤트 관리) 기능을 제공하여 엔터프라이즈 수준에서 인텔리전트 보안 분석을 가능하게 합니다. 

* **[Azure Monitor](../..//azure-monitor/overview.md)** - 다양한 조건에 대한 자동화된 모니터링 및 경고를 가능하게 합니다. 통합 문서를 만들거나 사용하여 다른 원본의 데이터를 결합할 수 있습니다.

* **[Azure Event Hubs](../../event-hubs/event-hubs-about.md) -SIEM과 통합**- [Azure AD 로그는 Azure Event Hub 통합을 통해 Splunk, ArcSight, QRadar 및 Sumo Logic과 같은 다른 SIEM](../reports-monitoring/tutorial-azure-monitor-stream-logs-to-event-hub.md)에 통합될 수 있습니다.

* **[MCAS(Microsoft Cloud App Security)](/cloud-app-security/what-is-cloud-app-security)** - 앱을 검색 및 관리하고, 앱과 리소스를 제어하고, 클라우드 앱의 규정 준수를 확인할 수 있습니다. 

모니터링하고 경고할 항목의 대부분은 조건부 액세스 정책의 영향입니다. [조건부 액세스 인사이트 및 보고 통합 문서](../conditional-access/howto-conditional-access-insights-reporting.md)를 사용하여 로그인에 대한 하나 이상의 조건부 액세스 정책과 디바이스 상태를 포함한 정책 결과를 조사할 수 있습니다. 이 통합 문서를 사용하면 영향 요약을 보고 특정 기간 동안의 영향을 식별할 수 있습니다. 통합 문서를 사용하여 특정 사용자의 로그인을 조사할 수도 있습니다. 

 이 문서의 나머지 부분에서는 모니터링하고 경고하는 것이 권장되는 사항에 대해 설명하며 위협 유형별로 구성되어 있습니다. 사전 구축된 특정 솔루션이 있는 경우 해당 솔루션에 연결하거나 표에 따라 샘플을 제공합니다. 그렇지 않으면 이전 도구를 사용하여 경고를 작성할 수 있습니다. 

 ## <a name="device-registrations-and-joins-outside-policy"></a>정책 외 디바이스 등록 및 조인

Azure AD 등록 및 Azure AD 조인된 디바이스에는 단일 인증 요소에 해당하는 PRT(기본 새로 고침 토큰)가 있습니다. 이러한 디바이스에는 강력한 인증 클레임이 포함될 수 있습니다. PRT에 강력한 인증 클레임이 포함된 경우에 대한 자세한 내용은 [PRT가 MFA 클레임을 가져오는 경우](../devices/concept-primary-refresh-token.md)를 참조하세요. 악의적인 행위자가 디바이스를 등록하거나 조인하는 것을 방지하려면 디바이스를 등록하거나 조인할 때 MFA(다단계 인증)가 필요합니다. 그런 다음 MFA 없이 등록되거나 조인된 모든 디바이스를 모니터링합니다. 또한 MFA 설정 및 정책, 디바이스 규정 준수 정책에 대한 변경 사항도 확인해야 합니다.

 | 모니터링 대상| 위험 수준| Where| 필터/하위 필터| 참고 |
| - |- |- |- |- |
| MFA 없이 디바이스 등록 또는 조인 완료| 중간| 로그인 로그| 활동: 디바이스 등록 서비스에 대한 인증 성공. <br>And<br>MFA 필요 없음| 다음과 같은 경우에 경고 생성: <br>MFA 없이 등록 또는 조인된 모든 디바이스 |
| Azure AD의 디바이스 등록 MFA 토글에 대한 변경| 높음| 감사 로그| 활동: 디바이스 등록 정책 설정| 검색: <br>토글이 꺼짐으로 설정됨. 감사 로그 항목이 없음. 정기 검사 예약. |
| 도메인 가입 또는 규격 디바이스를 요구하는 조건부 액세스 정책에 대한 변경.| 높음| 감사 로그| CA 정책 변경<br>| 다음과 같은 경우에 경고 생성: <br><li> 도메인 가입 또는 규정 준수를 요구하는 정책으로 변경.<li>신뢰할 수 있는 위치에 대한 변경.<li> MFA 정책 예외에 계정 또는 디바이스 추가. |


Azure Sentinel을 사용하여 MFA 없이 디바이스가 등록되거나 조인될 때 해당 관리자에게 알리는 경고를 만들 수 있습니다.

```
Sign-in logs

| where ResourceDisplayName == “Device Registration Service”

| where conditionalAccessStatus ==”success”

| where AuthenticationRequirement <> “multiFactorAuthentication”
```

[Microsoft Intune을 사용하여 디바이스 규정 준수 정책을 설정하고 모니터링](/mem/intune/protect/device-compliance-get-started)할 수도 있습니다.

## <a name="non-compliant-device-sign-in"></a>비규격 디바이스 로그인

규격 디바이스가 필요한 조건부 액세스 정책으로 모든 클라우드 및 SaaS(Software-as-a-Service) 애플리케이션에 대한 액세스를 차단하는 것은 불가능할 수 있습니다. 

[MDM(모바일 디바이스 관리)](/windows/client-management/mdm/)은 Windows 10 디바이스 규정 준수를 유지하는 데 도움이 됩니다. Windows 버전 1809에서는 정책의 [보안 기준](/windows/client-management/mdm/)을 발표했습니다. Azure Active Directory는 [MDM과 통합](/windows/client-management/mdm/azure-active-directory-integration-with-mdm)하여 기업 정책에 대한 디바이스 규정 준수를 시행하고 디바이스 규정 준수 상태를 보고할 수 있습니다. 

| 모니터링 대상| 위험 수준| Where| 필터/하위 필터| 참고 |
| - |- |- |- |- |
| 비규격 디바이스로 로그인| 높음| 로그인 로그| DeviceDetail.isCompliant ==false| 규격 디바이스에서 로그인해야 하는 경우 다음과 같은 경우 경고합니다.<br><li> 비규격 디바이스에 의한 모든 로그인.<li> MFA 또는 신뢰할 수 있는 위치가 없는 모든 액세스.<p>디바이스 요구에 대한 작업을 수행하는 경우 의심스러운 로그인을 모니터링합니다. |
| 알 수 없는 디바이스의 로그인| 낮음| 로그인 로그| <li>DeviceDetail이 비어 있음<li>단일 단계 인증<li>신뢰할 수 없는 위치| 검색: <br><li>규정 준수 디바이스 이외의 모든 액세스.<li>MFA 또는 신뢰할 수 있는 위치가 없는 모든 액세스 |


### <a name="use-loganalytics-to-query"></a>LogAnalytics를 사용하여 쿼리

**비규격 디바이스로 로그인**

```
SigninLogs

| where DeviceDetail.isCompliant ==false

| where conditionalAccessStatus == “success”
```
 

**알 수 없는 디바이스로 로그인**

```

SigninLogs
| where isempty(DeviceDetail.deviceId)

| where AuthenticationRequirement == "singleFactorAuthentication"

| where ResultType == "0"

| where NetworkLocationDetails == "[]"
```
 
## <a name="stale-devices"></a>부실 디바이스

부실 디바이스에는 지정된 기간 동안 로그인하지 않은 디바이스가 포함됩니다. 사용자가 새 디바이스를 얻거나 디바이스를 분실하거나 Azure AD 조인 디바이스가 초기화되거나 다시 프로비저닝되면 디바이스가 부실해질 수 있습니다. 사용자가 더 이상 테넌트와 연결되지 않은 경우에도 디바이스가 등록되거나 조인된 상태를 유지할 수 있습니다. PRT(기본 새로 고침 토큰)를 사용할 수 없도록 부실 디바이스를 제거해야 합니다.

| 모니터링 대상| 위험 수준| Where| 필터/하위 필터| 참고 |
| - |- |- |- |- |
| 마지막 로그인 날짜| 낮음| 그래프 API| approximateLastSignInDateTime| Graph API 또는 PowerShell을 사용하여 부실 디바이스를 식별하고 제거합니다. |

## <a name="bitlocker-key-retrieval"></a>BitLocker 키 검색

사용자의 디바이스를 손상시킨 공격자는 Azure AD에서 [BitLocker](/windows/security/information-protection/bitlocker/bitlocker-device-encryption-overview-windows-10) 키를 검색할 수 있습니다. 사용자가 키를 검색하는 것은 드문 일이므로 모니터링하고 조사해야 합니다.

| 모니터링 대상| 위험 수준| Where| 필터/하위 필터| 참고 |
| - |- |- |- |- |
| 키 검색| 중간| 감사 로그| OperationName == "Read BitLocker key”| 살펴볼 항목 <br><li>키 검색`<li> 사용자가 키를 검색하는 기타 비정상적인 행동. |


LogAnalytics에서 다음과 같은 쿼리를 만듭니다.

```
AuditLogs

| where OperationName == "Read BitLocker key” 
```

## <a name="device-administrator-roles"></a>디바이스 관리자 역할

전역 관리자 및 클라우드 디바이스 관리자는 모든 Azure AD 조인 디바이스에 대한 로컬 관리자 권한을 자동으로 얻습니다. 환경을 안전하게 유지하려면 이러한 권한이 있는 사람을 모니터링하는 것이 중요합니다.

| 모니터링 대상| 위험 수준| Where| 필터/하위 필터| 참고 |
| - |- |- |- |- |
| 전역 또는 디바이스 관리자 역할에 추가된 사용자| 높음| 감사 로그| 활동 유형 = 역할에 구성원 추가.| 검색:<li> 이러한 Azure AD 역할에 추가된 새 사용자.<li> 컴퓨터 또는 사용자에 의한 비정상적인 후속 동작. |


## <a name="non-azure-ad-sign-ins-to-virtual-machines"></a>가상 머신에 대한 비 Azure AD 로그인

Windows 또는 LINUX VM(가상 머신)에 대한 로그인이 Azure AD 계정이 아닌 다른 계정의 로그인일 경우 모니터링해야 합니다.

### <a name="azure-ad-sign-in-for-linux"></a>LINUX용 Azure AD 로그인

LINUX용 Azure AD 로그인을 사용하면 조직은 SSH(보안 셸 프로토콜)를 통해 Azure AD 계정을 사용하여 Azure LINUX VM에 로그인할 수 있습니다.

| 모니터링 대상| 위험 수준| Where| 필터/하위 필터| 참고 |
| - |- |- |- |- |
| 특히 SSH를 통한 비 Azure AD 계정 로그인| 높음| 로컬 인증 로그| Ubuntu:  <br>‎SSH 사용에 대한 /var/log/auth.log 모니터링<br>RedHat: <br>SSH 사용에 대한 /var/log/sssd/ 모니터링| 검색:<li> [비 Azure AD 계정이 VM에 성공적으로 연결](../devices/howto-vm-sign-in-azure-ad-linux.md)하는 항목 <li>다음 예제를 참조하세요. |


Ubuntu 예:

   May 9 23:49:39 ubuntu1804 aad_certhandler[3915]: Version: 1.0.015570001; user: localusertest01

   May 9 23:49:39 ubuntu1804 aad_certhandler[3915]: User 'localusertest01' is not an AAD user; returning empty result.

   May 9 23:49:43 ubuntu1804 aad_certhandler[3916]: Version: 1.0.015570001; user: localusertest01

   May 9 23:49:43 ubuntu1804 aad_certhandler[3916]: User 'localusertest01' is not an AAD user; returning empty result.

   May 9 23:49:43 ubuntu1804 sshd[3909]: Accepted publicly for localusertest01 from 192.168.0.15 port 53582 ssh2: RSA SHA256:MiROf6f9u1w8J+46AXR1WmPjDhNWJEoXp4HMm9lvJAQ

   May 9 23:49:43 ubuntu1804 sshd[3909]: pam_unix(sshd:session): session opened for user localusertest01 by (uid=0).

LINUX VM 로그인에 대한 정책을 설정하고 승인되지 않은 로컬 계정이 추가된 Linux VM을 감지하고 플래그를 지정할 수 있습니다. 자세히 알아보려면 [Azure Policy를 사용하여 표준 보장 및 규정 준수 평가](../devices/howto-vm-sign-in-azure-ad-linux.md)를 참조하세요. 

### <a name="azure-ad-sign-ins-for-windows-server"></a>Windows Server용 Azure AD 로그인

Windows용 Azure AD 로그인을 사용하면 조직에서 RDP(원격 데스크톱 프로토콜)를 통해 Azure AD 계정을 사용하여 Azure Windows 2019+ VM에 로그인할 수 있습니다.

| 모니터링 대상| 위험 수준| Where| 필터/하위 필터| 참고 |
| - |- |- |- |- |
| 특히 RDP를 통한 비 Azure AD 계정 로그인| 높음| Windows Server 이벤트 로그| Windows VM에 대화형 로그인| 이벤트 528, 로그온 유형 10(RemoteInteractive).<br>사용자가 터미널 서비스 또는 원격 데스크톱을 통해 로그인할 때 표시됩니다. |


## <a name="next-steps"></a>다음 단계

다음 추가 보안 작업 가이드 문서를 참조하세요.

[Azure AD 보안 작업 개요](security-operations-introduction.md)

[사용자 계정에 대한 보안 작업](security-operations-user-accounts.md)

[권한 있는 계정에 대한 보안 작업](security-operations-privileged-accounts.md)

[Privileged Identity Management에 대한 보안 작업](security-operations-privileged-identity-management.md)

[애플리케이션에 대한 보안 작업](security-operations-applications.md)

[디바이스에 대한 보안 작업](security-operations-devices.md)

 
[인프라에 대한 보안 작업](security-operations-infrastructure.md)