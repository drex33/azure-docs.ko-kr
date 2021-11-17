---
title: AD FS 위험한 IP 보고서 통합 문서가 포함된 Azure AD Connect Health | Microsoft Docs
description: Azure Monitor 통합 문서가 포함된 Azure AD Connect Health AD FS 위험한 IP 보고서에 대해 설명합니다.
services: active-directory
documentationcenter: ''
ms.reviewer: ''
author: billmath
manager: karen444
ms.service: active-directory
ms.subservice: hybrid
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 10/14/2021
ms.author: billmath
ms.custom: ''
ms.collection: ''
ms.openlocfilehash: 8463da51788c7faa680f42aaeb1999c174419366
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131482858"
---
# <a name="risky-ip-report-workbook"></a>위험한 IP 보고서 통합 문서 
> [!NOTE]
> 위험한 IP 보고서 통합 문서를 사용하려면 [진단 설정] 블레이드에서 'ADFSSignInLogs'를 사용하도록 설정해야 합니다. Connect Health를 통해 Azure AD에 보낸 AD FS 로그인이 있는 Log Analytics 스트림입니다. Azure AD의 AD FS 로그인에 대해 자세히 알아보려면 여기에 있는 설명서를 참조하세요.

AD FS 고객은 암호 인증 엔드포인트를 인터넷에 공개하여 최종 사용자가 Microsoft 365와 같은 SaaS 애플리케이션에 액세스할 수 있게 하는 인증 서비스를 제공할 수 있습니다. 이 경우 악의적 행위자가 AD FS 시스템에 대한 로그인을 시도하여 사용자의 암호를 추측하고 종료하고 애플리케이션 리소스에 액세스할 수 있습니다. Windows Server 2012 R2의 AD FS 이후부터 AD FS에서는 이러한 유형의 공격을 방지하기 위해 엑스트라넷 계정 잠금 기능을 제공합니다. 더 낮은 버전을 사용하는 경우 AD FS 시스템을 Windows Server 2016으로 업그레이드하는 것이 좋습니다. <br />

또한 단일 IP 주소에서 여러 사용자에 대해 여러 로그인을 시도할 수도 있습니다. 이러한 경우 사용자당 시도 횟수가 AD FS의 계정 잠금 보호 임계값보다 낮을 수 있습니다. Azure AD Connect Health는 이제 이 조건을 감지하고 해당 문제가 발생하면 관리자에게 알리는 "위험한 IP 보고서"를 제공합니다. 이 보고서의 주요 이점은 다음과 같습니다. 
- 실패한 암호 기반 로그인의 임계값을 초과하는 IP 주소 검색
- 암호가 잘못되었거나 엑스트라넷 잠금 상태로 인해 실패한 로그인 지원
- Azure Alerts를 통한 경고 사용 지원
- 조직의 보안 정책과 일치하는 사용자 지정 가능한 임계값 설정
- 추가 분석을 위한 사용자 지정 가능한 쿼리 및 확장된 시각화
- 이전의 위험한 IP 보고서에서 확장된 기능(2022년 1월 24일 이후 더 이상 사용되지 않음)

## <a name="requirements"></a>요구 사항
1.  설치되고 최신 에이전트로 업데이트된 AD FS용 Connect Health
2.  "ADFSSignInLogs" 스트림이 사용하도록 설정된 Log Analytics 작업 영역
3.  Azure AD 모니터 통합 문서를 사용할 수 있는 권한 통합 문서를 사용하려면 다음이 필요합니다.
- 프리미엄(P1 또는 P2) 라이선스를 사용하는 Azure Active Directory 테넌트.
- Log Analytics 작업 영역 및 Azure AD의 역할(보안 관리자, 보안 읽기 권한자, 보고서 읽기 권한자, 전역 관리자)에 대한 액세스(Azure AD 포털을 통해 Log Analytics에 액세스하는 경우)


## <a name="what-is-in-the-report"></a>보고서 내용은 무엇인가요?
위험한 IP 보고서 통합 문서는 ADFSSignInLogs 스트림의 데이터에서 구동되며 위험한 IP를 빠르게 시각화하고 분석할 수 있는 기존 쿼리를 포함하고 있습니다. 임계값 개수에 대한 매개 변수를 구성하고 사용자 지정할 수 있습니다. 또한 통합 문서는 쿼리를 기반으로 하여 구성할 수 있으며, 각 쿼리는 조직의 요구 사항에 따라 업데이트하고 수정할 수 있습니다.

위험한 IP 통합 문서는 ADFSSignInLogs의 데이터를 분석하여 암호 스프레이 또는 암호 무차별 대입 공격을 탐지하는 데 도움이 됩니다. 통합 문서는 두 부분으로 구성되어 있습니다. 첫 번째 부분인 "위험한 IP 분석"은 지정된 오류 임계값 및 탐지 기간에 따라 위험한 IP 주소를 식별합니다. 두 번째 부분은 선택한 IP에 대한 로그인 세부 정보 및 오류 수를 제공합니다.

[![위치가 있는 통합 문서 보기의 스크린샷](./media/how-to-connect-health-adfs-risky-ip-workbook/workbook-template-overview-1.png)](./media/how-to-connect-health-adfs-risky-ip-workbook/workbook-template-overview-1.png#lightbox)

- 통합 문서는 위험한 IP 위치를 빠르게 분석할 수 있도록 지도 시각화 및 지역 분석을 표시합니다.
- 위험한 IP 세부 정보 테이블은 과거의 위험한 IP 보고서의 기능과 비슷합니다. 테이블의 필드에 대한 자세한 내용은 아래 섹션을 참조하세요.
- 위험한 IP 타임라인은 타임라인 보기에서 요청의 변칙 또는 급증에 대한 빠른 보기를 표시합니다.
- IP별 로그인 세부 정보 및 오류 수를 통해 IP 또는 사용자별로 자세히 필터링된 보기를 세부 정보 테이블에서 확장할 수 있습니다. 

위험한 IP 보고서 테이블의 각 항목에는 지정된 임계값을 초과하는 실패한 AD FS 로그인 활동에 대한 정보가 표시됩니다. 제공하는 정보는 다음과 같습니다. [![열 머리글이 강조 표시된 위험한 IP 보고서를 보여 주는 스크린샷](./media/how-to-connect-health-adfs-risky-ip-workbook/risky-ip-table-1.png)](./media/how-to-connect-health-adfs-risky-ip-workbook/risky-ip-table-1.png#lightbox)

| 보고서 항목 | Description |
| ------- | ----------- |
| 탐지 기간 시작 시간 | 탐지 시간 범위가 시작되면 Azure Portal 현지 시간 기준의 타임스탬프가 표시됩니다.<br /> 모든 일별 이벤트는 자정 UTC 시간에 생성됩니다. <br />시간별 이벤트의 타임스탬프는 시간의 시작 시점으로 반올림됩니다. 첫 번째 활동 시작 시간은 내보낸 파일의 "firstAuditTimestamp"에서 찾을 수 있습니다. |
| 탐지 기간 | 탐지 시간 범위 형식이 표시됩니다. 집계 트리거 형식은 시간 또는 일 단위입니다. 이렇게 하면 하루 동안에 걸쳐 분산된 시도 횟수에서 자주 발생한 무차별 암호 대입 공격 및 느린 공격을 탐지하는 데 도움이 될 수 있습니다. |
| IP 주소 | 잘못된 암호 또는 엑스트라넷 잠금 로그인 활동이 있는 위험한 단일 IP 주소입니다. 이는 IPv4 또는 IPv6 주소일 수 있습니다. |
| 잘못된 암호 오류 수(50126) | 탐지 시간 범위 동안 IP 주소에서 잘못된 암호 오류가 발생했습니다. 잘못된 암호 오류는 특정 사용자에게 여러 번 발생할 수 있습니다. 만료된 암호로 인해 실패한 시도는 포함되지 않습니다. |
| 엑스트라넷 잠금 오류 수(30030) | 탐지 시간 범위 동안 IP 주소에서 엑스트라넷 잠금 오류가 발생했습니다. 엑스트라넷 잠금 오류는 특정 사용자에게 여러 번 발생할 수 있습니다. 이 정보는 엑스트라넷 잠금이 AD FS(2012R2 버전 이상)로 구성된 경우에만 표시됩니다. <b>참고</b> 암호를 사용하는 엑스트라넷 로그인을 허용하는 경우 이 기능을 설정하는 것이 좋습니다. |
| 시도한 고유 사용자 | 탐지 시간 범위 동안 IP 주소에서 시도한 고유 사용자 계정의 수입니다. 이 정보는 단일 사용자 공격 패턴 및 다중 사용자 공격 패턴을 구분하는 메커니즘을 제공합니다.  |

IP 주소 또는 사용자 이름을 기준으로 보고서를 필터링하여 각 위험한 IP 이벤트에 대한 로그인 세부 정보의 확장된 보기를 확인합니다.

## <a name="load-balancer-ip-addresses-in-the-list"></a>목록의 부하 분산 장치 IP 주소
부하 분산 장치는 실패한 로그인 작업을 집계하여 경고 임계값에 도달합니다. 부하 분산 장치 IP 주소가 표시되는 경우 웹 애플리케이션 프록시 서버에 요청을 전달할 때 외부 부하 분산 장치에서 클라이언트 IP 주소를 보내지 않을 가능성이 큽니다. 포워드 클라이언트 IP 주소를 전달하도록 올바르게 부하 분산 장치를 구성하세요. 

## <a name="configure-threshold-settings"></a>임계값 설정 구성
알림 임계값은 임계값 설정을 통해 업데이트할 수 있습니다. 먼저 임계값은 기본적으로 시스템에 설정되어 있습니다. 임계값 설정은 시간 또는 일 단위의 탐지 시간으로 설정할 수 있으며, 필터에서 사용자 지정할 수 있습니다.

[![임계값 필터](./media/how-to-connect-health-adfs-risky-ip-workbook/threshold-settings-1.png)](./media/how-to-connect-health-adfs-risky-ip-workbook/threshold-settings-1.png#lightbox)

| 임계값 항목 | Description |
| --- | --- |
| 잘못된 암호 + 엑스트라넷 잠금 임계값  | 잘못된 암호 수와 엑스트라넷 잠금 수의 합계가 **시간 또는 일** 단위의 임계값을 초과하는 경우 활동을 보고하고 경고 알림을 트리거하는 임계값 설정입니다.|
| 엑스트라넷 잠금 오류 임계값 | 엑스트라넷 잠금 수가 **시간 또는 일** 단위의 임계값을 초과하는 경우 활동을 보고하고 경고 알림을 트리거하는 임계값 설정입니다. 기본값은 50입니다.|

**시간 또는 일** 탐지 기간은 임계값을 사용자 지정하기 위한 필터 위의 토글 단추를 통해 구성할 수 있습니다. 

## <a name="configure-notification-alerts-using-azure-monitor-alerts-through-the-azure-portal"></a>Azure Portal을 통해 Azure Monitor 경고를 사용하여 알림 경고 구성
[![Azure Alerts 규칙](./media/how-to-connect-health-adfs-risky-ip-workbook/azure-alerts-rule-1.png)](./media/how-to-connect-health-adfs-risky-ip-workbook/azure-alerts-rule-1.png#lightbox)
1.  Azure Portal의 검색 창에서 "Monitor"를 검색하여 Azure "Monitor" 서비스로 이동합니다. 왼쪽 메뉴에서 "경고"를 선택한 다음, "+ 새 경고 규칙"을 선택합니다. 
2.  "경고 규칙 만들기" 블레이드에서 다음을 수행합니다.
   * 범위: "리소스 선택"을 클릭하고, 모니터링하려는 ADFSSignInLogs가 포함된 Log Analytics 작업 영역을 선택합니다.
   * 조건: "조건 추가"를 클릭합니다. 신호 유형에 대해 "로그"를 선택하고, Monitor 서비스에 대해 "로그 분석"을 선택합니다. "사용자 지정 로그 검색"을 선택합니다. 

3. 경고를 트리거하기 위한 조건을 구성합니다. Connect Health 위험한 IP 보고서의 이메일 알림을 일치시키려면 아래 지침을 따르세요.
   * 다음 쿼리를 복사하여 붙여넣고, 오류 수 임계값을 지정합니다. 이 쿼리는 지정된 오류 임계값을 초과하는 IP 수를 생성합니다.

```
ADFSSignInLogs
| extend ErrorCode = ResultType
| where ErrorCode in ("50126", "300030")
| summarize badPasswordErrorCount = countif(ErrorCode == "50126"), extranetLockoutErrorCount = countif(ErrorCode == "300030") by IPAddress
| where extranetLockoutErrorCount > [TODO: put error count threshold here] 
```
또는 결합된 임계값의 경우 다음과 같습니다.
```
badPasswordErrorCount + extranetLockoutErrorCount > [TODO: put error count threshold here] // Customized thresholds
```

> [!NOTE]
> 경고 논리는 엑스트라넷 잠금 오류 수 또는 결합된 잘못된 암호 및 엑스트라넷 잠금 오류 수에서 하나 이상의 IP가 지정된 임계값을 초과하는 경우 경고가 트리거됨을 의미합니다. 쿼리를 평가하는 빈도를 선택하여 위험한 IP를 탐지할 수 있습니다.


## <a name="faq"></a>FAQ
**보고서에 부하 분산 장치 IP 주소가 표시되는 이유는 무엇인가요?**  <br />
부하 분산 장치 IP 주소가 표시되는 경우 웹 애플리케이션 프록시 서버에 요청을 전달할 때 외부 부하 분산 장치에서 클라이언트 IP 주소를 보내지 않을 가능성이 큽니다. 포워드 클라이언트 IP 주소를 전달하도록 올바르게 부하 분산 장치를 구성하세요. 

**IP 주소를 차단하려면 어떻게 해야 할까요?**  <br />
식별된 악성 IP 주소는 방화벽에 추가되거나 Exchange에서 차단해야 합니다.   <br />

**이 보고서에 항목이 표시되지 않는 이유는 무엇인가요?** <br />
- 'ADFSSignInLogs' Log Analytics 스트림은 진단 설정에서 사용하도록 설정되지 않습니다.
- 실패한 로그인 활동이 임계값 설정을 초과하지 않습니다.
- AD FS 서버 목록에서 "상태 서비스가 최신이 아닙니다" 경고가 활성화되어 있지 않은지 확인합니다.  [이 경고 문제를 해결하는 방법](how-to-connect-health-data-freshness.md)에 대해 자세히 알아보세요.
- AD FS 팜에서는 감사를 사용할 수 없습니다.

## <a name="next-steps"></a>다음 단계
* [Azure AD Connect Health](./whatis-azure-ad-connect.md)
* [Azure AD Connect Health Agent 설치](how-to-connect-health-agent-install.md)
