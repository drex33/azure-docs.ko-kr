---
title: Azure Sentinel의 고급 다단계 공격 감지
description: Azure Sentinel의 Fusion 기술을 사용하여 경고 피로를 줄이고 고급 다단계 공격 감지를 기반으로 하는 실행 가능한 인시던트를 만듭니다.
services: sentinel
documentationcenter: na
author: yelevin
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/09/2021
ms.author: yelevin
ms.custom: ignite-fall-2021
ms.openlocfilehash: 2a8a6167208e95a253a2826b47a871278e0f15dc
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131083900"
---
# <a name="advanced-multistage-attack-detection-in-azure-sentinel"></a>Azure Sentinel의 고급 다단계 공격 감지

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

> [!IMPORTANT]
> 일부 Fusion 감지(아래 참조)는 현재 **미리 보기** 로 제공됩니다. 베타 또는 미리 보기로 제공되거나 아직 일반 공급으로 릴리스되지 않은 Azure 기능에 적용되는 추가 약관은 [Microsoft Azure 미리 보기에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

[!INCLUDE [reference-to-feature-availability](includes/reference-to-feature-availability.md)]

Azure Sentinel 확장 가능한 기계 학습 알고리즘을 기반으로 하는 상관 관계 엔진인 Fusion을 사용하여 킬 체인의 다양한 단계에서 관찰되는 비정상적인 동작과 의심스러운 활동의 조합을 식별하여 다단계 공격(고급 영구 위협 또는 APT라고도 함)을 자동으로 검색합니다. 이렇게 찾은 내용을 기반으로 Azure Sentinel은 놓치기 쉬운 인시던트를 생성합니다. 이러한 인시던트는 둘 이상의 경고 또는 활동으로 구성됩니다. 설계 의도에 따라 이러한 인시던트는 볼륨이 작고, 충실도가 높고, 심각도가 높습니다.

환경에 맞게 사용자 지정되는 이 감지 기술은 [가양성](false-positives.md) 비율을 줄일 뿐 아니라 정보가 제한되거나 누락된 공격도 감지할 수 있습니다.

Fusion은 다양한 제품의 여러 신호를 상호 연관하여 고급 다단계 공격을 검색하기 때문에 성공적인 Fusion 검색은 경고가 아닌 Azure Sentinel **인시던트** 페이지에 **Fusion** 인시던트로 표시되며 *SecurityAlerts* 테이블이 아닌 **로그의** *인시던트* 테이블에 저장됩니다.

### <a name="configure-fusion"></a>Fusion 구성

Fusion은 고급 **다단계 공격 검색이라는** [분석 규칙으로](detect-threats-built-in.md#view-built-in-detections) Azure Sentinel 기본적으로 사용하도록 설정됩니다. 규칙의 상태를 보고 변경하거나, Fusion ML 모델에 포함하도록 원본 신호를 구성하거나, 환경에 적용되지 않을 수 있는 특정 검색 패턴을 Fusion 검색에서 제외할 수 있습니다. [Fusion 규칙 를 구성하는](configure-fusion-rules.md)방법을 알아봅니다.

> [!NOTE]
> Azure Sentinel 현재 30일간의 기록 데이터를 사용하여 Fusion 엔진의 기계 학습 알고리즘을 학습합니다. 이 데이터는 기계 학습 파이프라인을 통과할 때 항상 Microsoft의 키를 사용하여 암호화됩니다. 그러나 Azure Sentinel 작업 영역에서 CMK를 사용하도록 설정한 경우에는 학습 데이터가 [CMK(고객 관리형 키)](customer-managed-keys.md)를 사용하여 암호화되지 않습니다. Fusion을 옵트아웃하려면 **Configuration** Analytics 활성 규칙 Azure Sentinel 이동하여 \>  \> 고급 다단계 공격 검색 규칙을 마우스 오른쪽 **단추로** 클릭하고 **사용 안 함을 선택합니다.** **\>**

## <a name="fusion-for-emerging-threats"></a>새로운 위협에 대한 Fusion

> [!IMPORTANT]
>
> - 새로운 위협에 대한 Fusion 기반 검색은 현재 **미리 보기** 에 있습니다. 베타 또는 미리 보기로 제공되거나 아직 일반 공급으로 릴리스되지 않은 Azure 기능에 적용되는 추가 약관은 [Microsoft Azure 미리 보기에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

보안 이벤트의 양이 계속 증가하고 공격의 범위와 정교함이 계속 증가하고 있습니다. 알려진 공격 시나리오를 정의할 수 있지만 사용자 환경에서 새로운 위협과 알 수 없는 위협은 어떻게 해야 할까요?  

Azure Sentinel ML 지원 Fusion 엔진은 **확장된 ML 분석을** **적용하고** 광범위한 **비정상 신호 범위의** 상관 관계를 낮게 유지하면서 사용자 환경에서 새로운 위협과 알 수 없는 위협을 찾는 데 도움이 될 수 있습니다. 

Fusion 엔진의 ML 알고리즘은 지속적으로 기존 공격으로부터 학습하고 보안 분석가의 생각 방식에 따라 분석을 적용합니다. 따라서 환경 전체의 킬 체인에서 수백만 개의 비정상적인 동작에서 이전에 검색되지 않은 위협을 검색할 수 있으므로 공격자보다 한 단계 앞서 있는 데 도움이 됩니다.

**새로운 위협에 대한 Fusion은** 다음 원본에서 데이터 수집 및 분석을 지원합니다.

- [첫 번째 변칙 검색](soc-ml-anomalies.md)
- Microsoft 제품의 경고:
    - Azure Active Directory ID 보호
    - Azure Defender
    - IoT용 Azure Defender
    - Microsoft 365 Defender
    - Microsoft Cloud App Security
    - 엔드포인트에 대한 Microsoft Defender
    - Microsoft Defender for Identity
    - Office 365용 Microsoft Defender
- [기본 제공](detect-threats-built-in.md#scheduled) 및 [보안 분석가가 만든](detect-threats-custom.md) [**예약된 분석 규칙의 경고입니다.**](configure-fusion-rules.md#configure-scheduled-analytics-rules-for-fusion-detections) 분석 규칙은 Fusion에서 사용하려면 킬 체인(전술) 및 엔터티 매핑 정보를 포함해야 합니다.

Fusion이 새로운 위협에 대해 작동하도록 하려면 위에 나열된 *모든* 데이터 원본을 연결하지 않아도 됩니다. 그러나 연결한 데이터 원본이 많을수록 범위가 넓고 Fusion에서 더 많은 위협을 찾을 수 있습니다.

Fusion 엔진의 상관 관계로 인해 새로운 위협이 검색되면 **"Fusion에서 검색한 가능한 다단계 공격 활동"이라는** 심각도가 높은 인시던트 가 Azure Sentinel 작업 영역의 *인시던트* 테이블에 생성됩니다.

## <a name="fusion-for-ransomware"></a>랜섬웨어용 Fusion

Azure Sentinel Fusion 엔진은 다음 데이터 원본에서 다양한 유형의 여러 경고를 검색하고 랜섬웨어 활동과 관련될 수 있음을 확인하면 인시던트 생성을 수행합니다.

- [Azure Defender(Azure Security Center)](connect-azure-security-center.md)
- [엔드포인트에 대한 Microsoft Defender](./data-connectors-reference.md#microsoft-defender-for-endpoint)
- [Microsoft Defender for Identity](./data-connectors-reference.md#microsoft-defender-for-identity)
- [Microsoft Cloud App Security](./data-connectors-reference.md#microsoft-cloud-app-security-mcas)
- [Azure Sentinel 예약된 분석 규칙](detect-threats-built-in.md#scheduled). Fusion은 전술 정보 및 매핑된 엔터티를 사용하여 예약된 분석 규칙만 고려합니다.

이러한 Fusion 인시던트를 **랜섬웨어 활동과 관련된 여러 경고가 검색됨** 이라고 하며, 관련 경고가 특정 기간 내에 검색되고 공격의 **실행** 및 **방어 우회** 단계와 연결된 경우에 생성됩니다.

예를 들어 Azure Sentinel 특정 기간 내에 동일한 호스트에서 다음 경고가 트리거되는 경우 가능한 랜섬웨어 활동에 대한 인시던트 발생을 생성합니다.

| 경고 | 원본 | 심각도 |
| ----- | ------ | -------- |
| **Windows 오류 및 경고 이벤트** | Azure Sentinel 예약된 분석 규칙. | 정보 제공 |
| **'GandCrab' 랜섬웨어가 방지되었습니다.** | Azure Defender | 중간 |
| **'Emotet' 맬웨어가 검색됨** | 엔드포인트에 대한 Microsoft Defender | 정보 제공 |
| **'Tofsee' 백도어 검색됨** | Azure Defender | low |
| **'Parite' 맬웨어가 검색됨** | 엔드포인트에 대한 Microsoft Defender | 정보 제공 

## <a name="scenario-based-fusion-detections"></a>시나리오 기반 Fusion 검색

다음 섹션에서는 Fusion 상관 관계 엔진을 사용하여 Azure Sentinel 검색하는 [시나리오 기반 다단계 공격](fusion-scenario-reference.md)유형을 나열합니다. 위협 분류별로 그룹화됩니다.

이러한 Fusion 기반 공격 검색 시나리오를 사용하려면 연결된 데이터 원본을 Log Analytics 작업 영역에 수집해야 합니다. 아래 표의 링크를 선택하여 각 시나리오 및 관련 데이터 원본에 대해 알아봅니다.

> [!NOTE]
> 이러한 시나리오 중 일부는 **미리 보기** 이며 미리 보기라고 표시됩니다.

| 위협 분류  | 시나리오  |
| -------- | -------- |
| **컴퓨팅 리소스 남용**      | <ul><li>(미리 보기) [의심스러운 Azure Active Directory 로그인 *후* 여러 VM 만들기 활동](fusion-scenario-reference.md#multiple-vm-creation-activities-following-suspicious-azure-active-directory-sign-in) |
| **자격 증명 액세스**           | <ul><li>(미리 보기) [의심스러운 로그인 *후* 사용자가 여러 암호 재설정](fusion-scenario-reference.md#multiple-passwords-reset-by-user-following-suspicious-sign-in) <li>(미리 보기) [실패한 Azure  AD 로그인이 여러 개인 IP로 Palo Alto VPN에 성공적으로 <br> 로그인한 의심스러운 로그인](fusion-scenario-reference.md#suspicious-sign-in-coinciding-with-successful-sign-in-to-palo-alto-vpn-by-ip-with-multiple-failed-azure-ad-sign-ins) |
| **자격 증명 수집**       | <ul><li>[의심스러운 로그인 *후* 악의적인 자격 증명 도난 도구 실행](fusion-scenario-reference.md#malicious-credential-theft-tool-execution-following-suspicious-sign-in)    <li>[의심스러운 로그인 *후* 의심스러운 자격 증명 도난 활동](fusion-scenario-reference.md#suspected-credential-theft-activity-following-suspicious-sign-in)      |
| **암호화 마이닝**               | <ul><li>[의심스러운 로그인 *후* 암호화 마이닝 작업](fusion-scenario-reference.md#crypto-mining-activity-following-suspicious-sign-in)          |
| **데이터 소멸**            | <ul><li>[의심스러운 Azure AD 로그인 *후* 대량 파일 삭제](fusion-scenario-reference.md#mass-file-deletion-following-suspicious-azure-ad-sign-in)     <li>(미리 보기) [에서 성공적인 Azure  AD 로그인 <br> 후 대량 파일 삭제 Cisco 방화벽 어플라이언스에 의해 차단된 IP](fusion-scenario-reference.md#mass-file-deletion-following-successful-azure-ad-sign-in-from-ip-blocked-by-a-cisco-firewall-appliance)        <li>(미리 보기) [ <br> 여러 번의 실패한 Azure AD 로그인으로 IP로 Palo Alto VPN에 성공적으로 로그인한 *후* 대량 파일 삭제](fusion-scenario-reference.md#mass-file-deletion-following-successful-sign-in-to-palo-alto-vpn-by-ip-with-multiple-failed-azure-ad-sign-ins)        <li>(미리 보기) [의심스러운 Azure AD 로그인 *후* 의심스러운 이메일 삭제 작업](fusion-scenario-reference.md#suspicious-email-deletion-activity-following-suspicious-azure-ad-sign-in) |
| **데이터 유출**           | <ul><li>(미리 보기) [새 관리자 계정 활동에 *따른* 메일 전달 활동은 최근에 볼 수 없습니다.](fusion-scenario-reference.md#mail-forwarding-activities-following-new-admin-account-activity-not-seen-recently)      <li>[의심스러운 Azure AD 로그인 *후* 대량 파일 다운로드](fusion-scenario-reference.md#mass-file-download-following-suspicious-azure-ad-sign-in)       <li>(미리 보기) [에서 성공적인  Azure AD 로그인 <br> 후 대량 파일 다운로드 Cisco 방화벽 어플라이언스에 의해 차단된 IP](fusion-scenario-reference.md#mass-file-download-following-successful-azure-ad-sign-in-from-ip-blocked-by-a-cisco-firewall-appliance)       <li>(미리 보기) [이전에 보이지  않는 IP에서 파일 작업을 SharePoint](fusion-scenario-reference.md#mass-file-download-coinciding-with-sharepoint-file-operation-from-previously-unseen-ip) 대량 파일 다운로드        <li>[의심스러운 Azure AD 로그인 *후* 대량 파일 공유](fusion-scenario-reference.md#mass-file-sharing-following-suspicious-azure-ad-sign-in)         <li>모드 [의심 스러운 Azure AD 로그인 *다음* 에 공유 작업을 공유 하는 여러 Power BI 보고서](fusion-scenario-reference.md#multiple-power-bi-report-sharing-activities-following-suspicious-azure-ad-sign-in)         <li>[의심 스러운 Azure AD 로그인을 *따라* 사서함 반출 Office 365](fusion-scenario-reference.md#office-365-mailbox-exfiltration-following-a-suspicious-azure-ad-sign-in)        <li>모드 [맬웨어 검색 *후* 이전에 보이지 않는 IP의 파일 작업 SharePoint](fusion-scenario-reference.md#sharepoint-file-operation-from-previously-unseen-ip-following-malware-detection)         <li>모드 의심 스러운 [수신함 조작 규칙이 의심 스러운 AZURE AD 로그인 *다음* 에 설정](fusion-scenario-reference.md#suspicious-inbox-manipulation-rules-set-following-suspicious-azure-ad-sign-in) 되었습니다.        <li>모드 의심 스러운 [Power BI 보고서  공유 의심 스러운 Azure AD 로그인](fusion-scenario-reference.md#suspicious-power-bi-report-sharing-following-suspicious-azure-ad-sign-in)  |
| **서비스 거부**           | <ul><li>모드 [의심 스러운 AZURE AD 로그인을 *수행* 하는 여러 VM 삭제 작업](fusion-scenario-reference.md#multiple-vm-deletion-activities-following-suspicious-azure-ad-sign-in)          |
| **수평 이동**            | <ul><li>[의심 스러운 Azure AD 로그인 *다음* Office 365 가장](fusion-scenario-reference.md#office-365-impersonation-following-suspicious-azure-ad-sign-in)      <li>모드 의심 스러운 [수신함 조작 규칙이 의심 스러운 AZURE AD 로그인 *다음* 에 설정](fusion-scenario-reference.md#suspicious-inbox-manipulation-rules-set-following-suspicious-azure-ad-sign-in-1) 되었습니다.        |
| **악의적인 관리 활동**     | <ul><li>[의심 스러운 Azure AD 로그인 *다음* 에 의심 스러운 클라우드 앱 관리 활동](fusion-scenario-reference.md#suspicious-cloud-app-administrative-activity-following-suspicious-azure-ad-sign-in)    <li>모드 [새 관리자 계정 작업을 *수행* 하는 메일 전달 활동은 최근에 표시 되지 않음](fusion-scenario-reference.md#mail-forwarding-activities-following-new-admin-account-activity-not-seen-recently-1)          |
| **<br>합법적인 프로세스로 악성 실행**    | <ul><li>모드 [PowerShell에서 의심 스러운 네트워크 연결을 수행한 *후* <br> Palo Alto Networks 방화벽으로 플래그가 지정 된 비정상적인 트래픽](fusion-scenario-reference.md#powershell-made-a-suspicious-network-connection-followed-by-anomalous-traffic-flagged-by-palo-alto-networks-firewall)   <li>모드 [의심 스러운 원격 WMI 실행 *후* <br> Palo Alto Networks 방화벽으로 플래그가 지정 된 비정상적인 트래픽](fusion-scenario-reference.md#suspicious-remote-wmi-execution-followed-by-anomalous-traffic-flagged-by-palo-alto-networks-firewall)   <li>[의심 스러운 로그인 *다음* 에 의심 스러운 PowerShell 명령줄](fusion-scenario-reference.md#suspicious-powershell-command-line-following-suspicious-sign-in)          |
| **맬웨어 C2 또는 다운로드**      | <ul><li>모드 [실패 한 여러 사용자 로그인이 서비스에 Fortinet 하 여 검색 된 신호 패턴](fusion-scenario-reference.md#beacon-pattern-detected-by-fortinet-following-multiple-failed-user-sign-ins-to-a-service)     <li>모드 [의심 스러운 AZURE AD 로그인  을 Fortinet 하 여 감지 된 신호 패턴](fusion-scenario-reference.md#beacon-pattern-detected-by-fortinet-following-suspicious-azure-ad-sign-in)       <li>모드 [ <br> Palo Alto Networks 방화벽으로 플래그가 지정 된 비정상 트래픽 *뒤* 에 익명화 서비스에 대 한 네트워크 요청](fusion-scenario-reference.md#network-request-to-tor-anonymization-service-followed-by-anomalous-traffic-flagged-by-palo-alto-networks-firewall)       <li>모드 [ <br> Palo Alto Networks 방화벽으로 플래그가 지정 된 비정상 트래픽 *다음에* 무단 액세스 시도 기록을 사용 하 여 IP에 대 한 아웃 바운드 연결](fusion-scenario-reference.md#outbound-connection-to-ip-with-a-history-of-unauthorized-access-attempts-followed-by-anomalous-traffic-flagged-by-palo-alto-networks-firewall)    |
| **지속성**                 | <ul><li>모드 [의심 스러운 로그인 *후* 드문 응용 프로그램 동의](fusion-scenario-reference.md#rare-application-consent-following-suspicious-sign-in)         |
| **섬**                  | <ul><li>[의심 스러운 Azure AD 로그인 *후* 랜 섬 웨어 실행](fusion-scenario-reference.md#ransomware-execution-following-suspicious-azure-ad-sign-in)          |
| **원격 익스플로잇**         | <ul><li>모드 [공격 프레임 워크의 의심  스러운 사용, <br> Palo Alto Networks 방화벽에 의해 플래그가 지정 된 비정상 트래픽](fusion-scenario-reference.md#suspected-use-of-attack-framework-followed-by-anomalous-traffic-flagged-by-palo-alto-networks-firewall)          |
| **리소스 하이재킹**          | <ul><li>모드 [ <br> 의심 스러운 Azure AD 로그인 *후* 이전에 보이지 않는 호출자의 의심 스러운 리소스/리소스 그룹 배포](fusion-scenario-reference.md#suspicious-resource--resource-group-deployment-by-a-previously-unseen-caller-following-suspicious-azure-ad-sign-in)          |
|

## <a name="next-steps"></a>다음 단계

Fusion advanced 다단계 공격 감지에 대 한 자세한 정보를 확인 하세요.
- [Fusion 시나리오 기반 공격](fusion-scenario-reference.md)검색에 대해 자세히 알아보세요.
- [Fusion 규칙을 구성](configure-fusion-rules.md)하는 방법에 대해 알아봅니다.

고급 다단계 공격 감지에 대해 자세히 알아보았으므로, [Azure Sentinel 시작](get-visibility.md) 빠른 시작을 통해 데이터 및 잠재적 위협에 대한 가시성을 얻는 방법을 알아볼 수 있습니다.

준비된 인시던트를 조사할 준비가 되었으면 [Azure Sentinel을 사용하여 인시던트 조사](investigate-cases.md) 자습서를 살펴보세요.
