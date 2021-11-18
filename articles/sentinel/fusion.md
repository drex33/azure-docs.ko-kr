---
title: Microsoft 센티널의 Advanced 다단계 공격 감지
description: Microsoft 센티널의 Fusion 기술을 사용 하 여 경고 피로를 줄이고 고급 다단계 공격 감지를 기반으로 하는 조치 가능한 인시던트를 만듭니다.
services: sentinel
documentationcenter: na
author: yelevin
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/09/2021
ms.author: yelevin
ms.custom: ignite-fall-2021
ms.openlocfilehash: 808910eb32973a3d567960c98364d1d60036d673
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/17/2021
ms.locfileid: "132721141"
---
# <a name="advanced-multistage-attack-detection-in-microsoft-sentinel"></a>Microsoft 센티널의 Advanced 다단계 공격 감지

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

> [!IMPORTANT]
> 일부 Fusion 감지(아래 참조)는 현재 **미리 보기** 로 제공됩니다. 베타 또는 미리 보기로 제공되거나 아직 일반 공급으로 릴리스되지 않은 Azure 기능에 적용되는 추가 약관은 [Microsoft Azure 미리 보기에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

[!INCLUDE [reference-to-feature-availability](includes/reference-to-feature-availability.md)]

Microsoft 센티널은 확장 가능한 기계 학습 알고리즘을 기반으로 하는 다단계 공격 (고급 영구 위협 또는 APT 라고도 함)을 자동으로 검색 하 여 kill 체인의 다양 한 단계에서 관찰 되는 비정상 동작 및 의심 스러운 활동의 조합을 식별 함으로써 자동으로 공격을 감지 합니다. 이러한 검색의 기반으로 Microsoft 센티널은 catch 하기 어려운 인시던트를 생성 합니다. 이러한 인시던트는 둘 이상의 경고 또는 활동으로 구성됩니다. 설계 의도에 따라 이러한 인시던트는 볼륨이 작고, 충실도가 높고, 심각도가 높습니다.

환경에 맞게 사용자 지정되는 이 감지 기술은 [가양성](false-positives.md) 비율을 줄일 뿐 아니라 정보가 제한되거나 누락된 공격도 감지할 수 있습니다.

Fusion은 다양 한 제품의 여러 신호의 상관 관계를 설정 하 여 고급 다단계 공격을 감지 합니다. 성공 Fusion 검색은 **경고가** 아닌 Microsoft 센티널 **인시던트** 페이지에 **Fusion 인시던트** 로 표시 되며 *securityalerts* 테이블이 아닌 **로그** 의 *인시던트* 테이블에 저장 됩니다.

### <a name="configure-fusion"></a>Fusion 구성

퓨전은 **고급 다단계 공격 감지** 라는 [분석 규칙](detect-threats-built-in.md#view-built-in-detections) 으로 Microsoft 센티널에서 기본적으로 사용 하도록 설정 됩니다. 규칙의 상태를 확인 및 변경 하거나, fusion ML 모델에 포함할 원본 신호를 구성 하거나, fusion 검색에서 사용자 환경에 적용 되지 않을 수 있는 특정 검색 패턴을 제외할 수 있습니다. [Fusion 규칙을 구성](configure-fusion-rules.md)하는 방법에 대해 알아봅니다.

> [!NOTE]
> Microsoft 센티널은 현재 30 일 동안의 기록 데이터를 사용 하 여 Fusion 엔진의 기계 학습 알고리즘을 학습 합니다. 이 데이터는 기계 학습 파이프라인을 통과할 때 항상 Microsoft의 키를 사용하여 암호화됩니다. 그러나 Microsoft 센티널 작업 영역에서 CMK를 사용 하도록 설정한 경우에는 [고객 관리 키 (cmk)](customer-managed-keys.md) 를 사용 하 여 학습 데이터를 암호화 하지 않습니다. Fusion을 옵트아웃 하려면 **Microsoft 센티널** \> **Configuration** \> **Analytics \> 활성 규칙** 으로 이동 하 고 **Advanced 다단계 Attack 검색** 규칙을 마우스 오른쪽 단추로 클릭 한 다음 **사용 안 함을 선택 합니다.**

## <a name="fusion-for-emerging-threats"></a>새로운 위협에 대 한 Fusion

> [!IMPORTANT]
>
> - 새로운 위협에 대 한 Fusion 기반 검색은 현재 **미리 보기로** 제공 됩니다. 베타 또는 미리 보기로 제공되거나 아직 일반 공급으로 릴리스되지 않은 Azure 기능에 적용되는 추가 약관은 [Microsoft Azure 미리 보기에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

보안 이벤트의 양이 계속 증가 하 고, 공격의 범위와 복잡성이 증가 하 고 있습니다. 알려진 공격 시나리오를 정의할 수 있지만 사용자 환경에서 새로운 위협 및 알 수 없는 위협에 대 한 정보는 무엇 인가요?  

Microsoft 센티널의 ML 기반 Fusion 엔진은 **확장 된 ML 분석** 을 적용 하 고 **광범위 한 비정상 신호의 범위** 를 상호 연결 하 여 경고를 피로 하는 방식으로 사용자 환경에서 **새로 추가 되거나 알 수 없는 위협을** 찾는 데 도움이 될 수 있습니다.

Fusion 엔진의 ML 알고리즘은 기존 공격 으로부터 지속적으로 배우고 보안 분석가가 생각 하는 방법에 따라 분석을 적용 합니다. 따라서 사용자 환경 전체에서 kill 체인을 통해 수백만 개의 비정상 동작에서 이전에 검색 되지 않은 위협을 발견할 수 있으므로 공격자의 한 단계를 진행 하는 데 도움이 됩니다.

**새로운 위협에 대 한 Fusion** 은 다음 원본에서 데이터 수집 및 분석을 지원 합니다.

- [기본 변칙 검색](soc-ml-anomalies.md)
- Microsoft 제품의 경고:
  - Azure Active Directory ID 보호
  - Microsoft Defender for Cloud
  - Microsoft Defender for IoT
  - Microsoft 365 Defender
  - Microsoft Defender for Cloud 앱
  - 엔드포인트에 대한 Microsoft Defender
  - Microsoft Defender for Identity
  - Office 365용 Microsoft Defender
- [기본 제공](detect-threats-built-in.md#scheduled) 및 [보안 분석가가 만든](detect-threats-custom.md) [**예약 된 분석 규칙의 경고**](configure-fusion-rules.md#configure-scheduled-analytics-rules-for-fusion-detections)입니다. 분석 규칙은 Fusion에서 사용 하기 위해 kill 체인 (전술) 및 엔터티 매핑 정보를 포함 해야 합니다.

새로운 위협에 대 한 Fusion을 만들기 위해 위에 나열 된 *모든* 데이터 원본을 연결할 필요는 없습니다. 그러나 연결 된 데이터 원본이 많을 수록 검사 범위가 광범위 하 고 더 많은 위협 Fusion이 발견 됩니다.

Fusion 엔진의 상관 관계로 인해 새로운 위협이 감지 되 면 Microsoft 센티널 작업 영역의 *인시던트* 테이블에 "**Fusion에서 감지한 가능한 다단계 공격 활동**" 이라는 높은 심각도 인시던트가 생성 됩니다.

## <a name="fusion-for-ransomware"></a>랜 섬 웨어에 대 한 Fusion

Microsoft 센티널의 Fusion 엔진은 다음 데이터 원본에서 다양 한 유형의 여러 경고를 감지 하 고 랜 섬 웨어 작업과 관련 될 수 있음을 확인 하는 경우 인시던트를 생성 합니다.

- [Microsoft Defender for Cloud](connect-defender-for-cloud.md)
- [엔드포인트에 대한 Microsoft Defender](./data-connectors-reference.md#microsoft-defender-for-endpoint)
- [Microsoft Defender for Identity](./data-connectors-reference.md#microsoft-defender-for-identity)
- [Microsoft Defender for Cloud 앱](./data-connectors-reference.md#microsoft-defender-for-cloud-apps)
- [Microsoft 센티널 예약 된 분석 규칙](detect-threats-built-in.md#scheduled). Fusion은 계획 된 분석 규칙만 전술 정보 및 매핑된 엔터티로 간주 합니다.

이러한 Fusion 인시던트를 **랜섬웨어 활동과 관련된 여러 경고가 검색됨** 이라고 하며, 관련 경고가 특정 기간 내에 검색되고 공격의 **실행** 및 **방어 우회** 단계와 연결된 경우에 생성됩니다.

예를 들어 특정 기간 내에 동일한 호스트에서 다음 경고가 트리거되는 경우 Microsoft 센티널은 가능한 랜 섬 웨어 활동에 대 한 인시던트를 생성 합니다.

| 경고 | 원본 | 심각도 |
| ----- | ------ | -------- |
| **오류 및 경고 이벤트 Windows** | Microsoft 센티널 예약 된 분석 규칙 | 정보 제공 |
| **' GandCrab ' 랜 섬 웨어를 방지 했습니다.** | Microsoft Defender for Cloud | 중간 |
| **' Emotet ' 맬웨어가 검색 되었습니다.** | 엔드포인트에 대한 Microsoft Defender | 정보 제공 |
| **' 백도어 검색 '이 검색 되었습니다.** | Microsoft Defender for Cloud | low |
| **' Parite ' 맬웨어가 검색 되었습니다.** | 엔드포인트에 대한 Microsoft Defender | 정보 제공 |

## <a name="scenario-based-fusion-detections"></a>시나리오 기반 Fusion 검색

다음 섹션에서는 Microsoft 센티널에서 Fusion 상관 관계 엔진을 사용 하 여 검색 하는 위협 분류 별로 그룹화 된 [시나리오 기반 다단계 공격](fusion-scenario-reference.md)유형을 나열 합니다.

이러한 Fusion 지원 공격 검색 시나리오를 사용 하도록 설정 하려면 연결 된 데이터 원본을 Log Analytics 작업 영역에 수집 해야 합니다. 아래 표에서 링크를 선택 하 여 각 시나리오와 관련 데이터 원본에 대해 알아보세요.

> [!NOTE]
> 이러한 시나리오 중 일부는 **미리 보기** 이며 미리 보기라고 표시됩니다.

| 위협 분류  | 시나리오  |
| -------- | -------- |
| **컴퓨팅 리소스 남용**      | <ul><li>모드 [의심 스러운 Azure Active Directory 로그인 *후* 여러 VM 만들기 작업](fusion-scenario-reference.md#multiple-vm-creation-activities-following-suspicious-azure-active-directory-sign-in) |
| **자격 증명 액세스**           | <ul><li>모드 [의심 스러운 로그인 *후* 사용자가 여러 암호 재설정](fusion-scenario-reference.md#multiple-passwords-reset-by-user-following-suspicious-sign-in) <li>모드 [ <br> 실패 한 여러 Azure AD 로그인을 사용 하 여 IP에 의해 PALO Alto VPN에 성공적으로 로그인 한 의심 스러운 로그인 *coinciding*](fusion-scenario-reference.md#suspicious-sign-in-coinciding-with-successful-sign-in-to-palo-alto-vpn-by-ip-with-multiple-failed-azure-ad-sign-ins) |
| **자격 증명 수집**       | <ul><li>[의심 스러운 로그인 *후* 악성 자격 증명 도난 도구 실행](fusion-scenario-reference.md#malicious-credential-theft-tool-execution-following-suspicious-sign-in)    <li>[의심 스러운 로그인 *후* 의 자격 증명 도난 행위 활동](fusion-scenario-reference.md#suspected-credential-theft-activity-following-suspicious-sign-in)      |
| **암호화-마이닝**               | <ul><li>[의심 스러운 로그인 *후* 의 암호화-마이닝 활동](fusion-scenario-reference.md#crypto-mining-activity-following-suspicious-sign-in)          |
| **데이터 소멸**            | <ul><li>[의심 스러운 Azure AD 로그인 *후* 대량 파일 삭제](fusion-scenario-reference.md#mass-file-deletion-following-suspicious-azure-ad-sign-in)     <li>모드 에서 성공한 Azure [AD 로그인 후 대량 파일 삭제  <br> Cisco 방화벽 어플라이언스에 의해 차단 되는 IP](fusion-scenario-reference.md#mass-file-deletion-following-successful-azure-ad-sign-in-from-ip-blocked-by-a-cisco-firewall-appliance)        <li>모드 [ <br> 여러 번의 실패 한 Azure AD 로그인으로 PALO Alto VPN에 대 한 성공적인 로그인 *후* 대량 파일 삭제](fusion-scenario-reference.md#mass-file-deletion-following-successful-sign-in-to-palo-alto-vpn-by-ip-with-multiple-failed-azure-ad-sign-ins)        <li>모드 [의심 스러운 AZURE AD 로그인 *후* 의심 스러운 이메일 삭제 활동](fusion-scenario-reference.md#suspicious-email-deletion-activity-following-suspicious-azure-ad-sign-in) |
| **데이터 반출**           | <ul><li>모드 [새 관리자 계정 작업을 *수행* 하는 메일 전달 활동은 최근에 표시 되지 않음](fusion-scenario-reference.md#mail-forwarding-activities-following-new-admin-account-activity-not-seen-recently)      <li>[대량 *파일 다운로드 의심* 스러운 Azure AD 로그인](fusion-scenario-reference.md#mass-file-download-following-suspicious-azure-ad-sign-in)       <li>모드 [대량 파일 다운로드 *다음* 에서 <br> Azure AD 로그인 성공 Cisco 방화벽 어플라이언스에 의해 차단 되는 IP](fusion-scenario-reference.md#mass-file-download-following-successful-azure-ad-sign-in-from-ip-blocked-by-a-cisco-firewall-appliance)       <li>모드 [이전에 보이지 않는 IP의 SharePoint 파일 작업 *으로* 대량 파일 다운로드 coinciding](fusion-scenario-reference.md#mass-file-download-coinciding-with-sharepoint-file-operation-from-previously-unseen-ip)        <li>[의심 스러운 Azure AD 로그인 *다음* 에 대량 파일 공유](fusion-scenario-reference.md#mass-file-sharing-following-suspicious-azure-ad-sign-in)         <li>(미리 보기) [의심스러운 Azure AD 로그인 *후* 여러 Power BI 보고서 공유 활동](fusion-scenario-reference.md#multiple-power-bi-report-sharing-activities-following-suspicious-azure-ad-sign-in)         <li>[의심스러운 Azure AD 로그인 *후* 사서함 반출 Office 365](fusion-scenario-reference.md#office-365-mailbox-exfiltration-following-a-suspicious-azure-ad-sign-in)        <li>(미리 보기) [맬웨어 검색 *후* 이전에 보이지 않는 IP에서 파일 작업 SharePoint](fusion-scenario-reference.md#sharepoint-file-operation-from-previously-unseen-ip-following-malware-detection)         <li>(미리 보기) [의심스러운 Azure AD 로그인 *후* 의심스러운 받은 편지함 조작 규칙 설정](fusion-scenario-reference.md#suspicious-inbox-manipulation-rules-set-following-suspicious-azure-ad-sign-in)        <li>(미리 보기) [의심스러운 Azure AD 로그인 *후* 의심스러운 Power BI 보고서 공유](fusion-scenario-reference.md#suspicious-power-bi-report-sharing-following-suspicious-azure-ad-sign-in)  |
| **서비스 거부**           | <ul><li>(미리 보기) [의심스러운 Azure AD 로그인 *후* 여러 VM 삭제 활동](fusion-scenario-reference.md#multiple-vm-deletion-activities-following-suspicious-azure-ad-sign-in)          |
| **수평 이동**            | <ul><li>[의심스러운 Azure AD 로그인 *후* Office 365 가장](fusion-scenario-reference.md#office-365-impersonation-following-suspicious-azure-ad-sign-in)      <li>(미리 보기) [의심스러운 Azure AD 로그인 *후* 의심스러운 받은 편지함 조작 규칙 설정](fusion-scenario-reference.md#suspicious-inbox-manipulation-rules-set-following-suspicious-azure-ad-sign-in-1)        |
| **악의적인 관리 활동**     | <ul><li>[의심스러운 Azure AD 로그인 *후* 의심스러운 클라우드 앱 관리 활동](fusion-scenario-reference.md#suspicious-cloud-app-administrative-activity-following-suspicious-azure-ad-sign-in)    <li>(미리 보기) [새 관리자 계정 활동에 *따른* 메일 전달 활동은 최근에 볼 수 없습니다.](fusion-scenario-reference.md#mail-forwarding-activities-following-new-admin-account-activity-not-seen-recently-1)          |
| **합법적인 프로세스를 사용하여 악의적인 실행 <br>**    | <ul><li>(미리 보기) [PowerShell이 의심스러운 네트워크 연결을 만든 *다음,* <br> Palo Alto Networks 방화벽에 의해 플래그가 지정된 비정상적인 트래픽이 발생합니다.](fusion-scenario-reference.md#powershell-made-a-suspicious-network-connection-followed-by-anomalous-traffic-flagged-by-palo-alto-networks-firewall)   <li>(미리 보기) [의심스러운 원격 WMI 실행 *후* <br> Palo Alto Networks 방화벽에 의해 플래그가 지정된 비정상적인 트래픽](fusion-scenario-reference.md#suspicious-remote-wmi-execution-followed-by-anomalous-traffic-flagged-by-palo-alto-networks-firewall)   <li>[의심스러운 로그인 *후* 의심스러운 PowerShell 명령줄](fusion-scenario-reference.md#suspicious-powershell-command-line-following-suspicious-sign-in)          |
| **맬웨어 C2 또는 다운로드**      | <ul><li>(미리 보기) [서비스에 대한 여러 사용자 로그인 실패 후 Fortinet에서 감지한 탐지 패턴](fusion-scenario-reference.md#beacon-pattern-detected-by-fortinet-following-multiple-failed-user-sign-ins-to-a-service)     <li>(미리 보기) [의심스러운 Azure AD 로그인 *후* Fortinet에서 탐지한 탐지 패턴](fusion-scenario-reference.md#beacon-pattern-detected-by-fortinet-following-suspicious-azure-ad-sign-in)       <li>(미리 보기) [TOR 익명화 서비스에 대한 네트워크 요청 *다음에* <br> Palo Alto Networks 방화벽에 의해 플래그가 지정된 비정상적인 트래픽](fusion-scenario-reference.md#network-request-to-tor-anonymization-service-followed-by-anomalous-traffic-flagged-by-palo-alto-networks-firewall)       <li>(미리 보기) 권한 없는 액세스 시도 기록과 그 뒤에 Palo Alto Networks [  <br> 방화벽에 의해 플래그가 지정된 비정상적인 트래픽이 있는 IP에 대한 아웃바운드 연결](fusion-scenario-reference.md#outbound-connection-to-ip-with-a-history-of-unauthorized-access-attempts-followed-by-anomalous-traffic-flagged-by-palo-alto-networks-firewall)    |
| **지속성**                 | <ul><li>(미리 보기) [의심스러운 로그인 *후* 드물게 애플리케이션 동의](fusion-scenario-reference.md#rare-application-consent-following-suspicious-sign-in)         |
| **랜 섬**                  | <ul><li>[의심스러운 Azure AD 로그인 *후* 랜섬웨어 실행](fusion-scenario-reference.md#ransomware-execution-following-suspicious-azure-ad-sign-in)          |
| **원격 익스플로잇**         | <ul><li>(미리 보기) [공격 프레임워크를  사용한 후 <br> Palo Alto Networks 방화벽에 의해 플래그가 지정된 비정상적인 트래픽이](fusion-scenario-reference.md#suspected-use-of-attack-framework-followed-by-anomalous-traffic-flagged-by-palo-alto-networks-firewall) 의심되는 경우          |
| **리소스 하이재킹**          | <ul><li>(미리 보기) [ <br> 의심스러운 Azure AD 로그인 *후* 이전에 보이지 않는 호출자가 의심스러운 리소스/리소스 그룹 배포](fusion-scenario-reference.md#suspicious-resource--resource-group-deployment-by-a-previously-unseen-caller-following-suspicious-azure-ad-sign-in)          |
|

## <a name="next-steps"></a>다음 단계

Fusion 고급 다단계 공격 검색에 대한 자세한 정보를 확인합니다.

- Fusion 시나리오 [기반 공격 검색에](fusion-scenario-reference.md)대해 자세히 알아봅니다.
- [Fusion 규칙 를 구성하는](configure-fusion-rules.md)방법을 알아봅니다.

이제 고급 다단계 공격 검색에 대해 자세히 알아보았습니다. 데이터 및 잠재적 위협에 대한 가시성을 얻는 방법을 알아보려면 [Microsoft Sentinel 시작](get-visibility.md)빠른 시작을 참조하세요.

생성된 인시던트 조사를 수행할 준비가 되면 [Microsoft Sentinel을](investigate-cases.md)사용하여 인시던트 조사 자습서를 참조하세요.
