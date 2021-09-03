---
title: Azure AD에서 페더레이션 구성의 변경 내용 모니터링 | Microsoft Docs
description: 이 문서에서는 Azure AD를 사용하여 페더레이션 구성의 변경 내용을 모니터링하는 방법을 설명합니다.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 06/21/2021
ms.subservice: hybrid
ms.author: billmath
ms.openlocfilehash: 7ffc5980eed3268f299ee0073cfa810c17878e53
ms.sourcegitcommit: 92dd25772f209d7d3f34582ccb8985e1a099fe62
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/15/2021
ms.locfileid: "114229712"
---
# <a name="monitor-changes-to-federation-configuration-in-your-azure-ad"></a>Azure AD에서 페더레이션 구성의 변경 내용 모니터링

온-프레미스 환경을 Azure AD와 페더레이션하는 경우 온-프레미스 ID 공급자와 Azure AD 간에 트러스트 관계를 설정합니다. 

이 설정된 트러스트로 인해 Azure AD는 온-프레미스 ID 공급자 사후 인증에서 발급된 보안 토큰을 사용하여 Azure AD에서 보호하는 리소스에 대한 액세스 권한을 부여합니다. 

따라서 이 트러스트(페더레이션 구성)를 긴밀하게 모니터링하고 비정상적이거나 의심스러운 활동을 캡처하는 것이 중요합니다.

트러스트 관계를 모니터링하려면 페더레이션 구성이 변경 될 때 알림을 받도록 경고를 설정하는 것이 좋습니다.


## <a name="set-up-alerts-to-monitor-the-trust-relationship"></a>트러스트 관계를 모니터링하는 경고 설정

다음 단계를 수행하여 트러스트 관계를 모니터링하는 경고를 설정합니다.

1. Azure Log Analytics 작업 영역으로 이동하도록 [Azure AD 감사 로그를 구성](../../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)합니다. 
2. Azure AD 로그 쿼리를 기반으로 트리거되는 [경고 규칙을 만듭니다](../../azure-monitor/alerts/alerts-log.md). 
3. 경고 조건이 충족될 때 알림을 받는 경고 규칙에 [작업 그룹을 추가](../../azure-monitor/alerts/action-groups.md)합니다.  

환경이 구성된 후 데이터는 다음과 같이 이동합니다. 

 1. Azure AD 로그는 테넌트의 활동별로 채워집니다.  
 2. 로그 정보는 Azure Log Analytics 작업 영역으로 이동합니다.  
 3. Azure Monitor의 백그라운드 작업은 위 구성 단계(2)에 있는 경고 규칙의 구성에 따라 로그 쿼리를 실행합니다.  
    ```
     AuditLogs 
     |  extend TargetResource = parse_json(TargetResources) 
     | where ActivityDisplayName contains "Set federation settings on domain" or ActivityDisplayName contains "Set domain authentication" 
     | project TimeGenerated, SourceSystem, TargetResource[0].displayName, AADTenantId, OperationName, InitiatedBy, Result, ActivityDisplayName, ActivityDateTime, Type 
     ```
     
 4. 쿼리 결과가 경고 논리와 일치하는 경우(즉, 결과 수가 1보다 크거나 같음) 작업 그룹이 시작됩니다. 작업 그룹이 시작되었으므로 5단계에서 흐름이 계속된다고 가정하겠습니다.  
 5. 알림은 경고를 구성하는 동안 선택된 작업 그룹으로 전송됩니다.

 > [!NOTE]
 >  경고를 설정하는 것 외에도 Azure AD 테넌트 내에서 구성된 도메인을 주기적으로 검토하고, 부실하거나, 인식되지 않거나, 의심스러운 도메인을 제거하는 것이 좋습니다. 




## <a name="next-steps"></a>다음 단계

- [Azure Monitor 로그에 Azure AD 로그 통합](../../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)
- [Azure Monitor를 사용하여 로그 경고 만들기, 보기 및 관리](../../azure-monitor/alerts/alerts-log.md)
- [Azure AD Connect를 사용하여 Azure AD로 AD FS 신뢰 관리](how-to-connect-azure-ad-trust.md)
- [Active Directory Federation Services 보안 모범 사례](/windows-server/identity/ad-fs/deployment/best-practices-securing-ad-fs)