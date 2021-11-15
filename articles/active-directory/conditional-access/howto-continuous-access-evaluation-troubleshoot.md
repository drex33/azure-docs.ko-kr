---
title: Azure AD에서 지속적인 액세스 평가를 통해 로그인 모니터링 및 문제 해결
description: Azure AD에서 지속적인 액세스 평가를 통해 사용자 상태 변경에서 문제를 더 빠르게 해결하고 이에 응답합니다.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: how-to
ms.date: 09/22/2021
ms.author: joflore
author: MicrosoftGuyJFlo
manager: karenhoran
ms.reviewer: jlu, shreyamalik
ms.collection: M365-identity-device-management
ms.openlocfilehash: 93d44065255dcb9f4977e93dd596eb3b80c1faa2
ms.sourcegitcommit: c27f71f890ecba96b42d58604c556505897a34f3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/05/2021
ms.locfileid: "129533164"
---
# <a name="monitor-and-troubleshoot-continuous-access-evaluation"></a>지속적인 액세스 평가 모니터링 및 문제 해결

관리자는 [CAE(지속적 액세스 평가)](concept-continuous-access-evaluation.md)가 여러 가지 방법으로 적용되는 로그인 이벤트를 모니터링하고 문제를 해결할 수 있습니다.

## <a name="continuous-access-evaluation-sign-in-reporting"></a>지속적인 액세스 평가 로그인 보고

관리자는 CAE가 적용된 사용자 로그인을 모니터링할 수 있습니다. 이 창은 다음 지침을 통해 배치할 수 있습니다.

1.  조건부 액세스 관리자, 보안 관리자 또는 전역 관리자 권한으로 **Azure Portal** 에 로그인합니다.
1.  **Azure Active Directory** > **로그인** 으로 이동합니다. 
1.  **CAE 토큰임** 필터를 적용합니다. 

[ ![필터를 로그인 로그에 추가하여 CAE 적용 여부 확인](./media/howto-continuous-access-evaluation-troubleshoot/azure-ad-sign-ins-log-apply-filter.png) ](./media/howto-continuous-access-evaluation-troubleshoot/azure-ad-sign-ins-log-apply-filter.png#lightbox)

여기서 관리자에게 사용자의 로그인 이벤트에 대한 정보가 표시됩니다. 로그인을 선택하여 조건부 액세스 정책이 적용되었고 CAE가 사용하도록 설정되었는지와 같은 세션에 대한 세부 정보를 확인합니다. 

지정된 로그인 시도는 대화형 또는 비대화형 탭에 표시될 수 있습니다. 관리자는 사용자의 로그인을 추적할 때 두 탭을 모두 확인해야 할 수 있습니다.

### <a name="searching-for-specific-sign-in-attempts"></a>특정 로그인 시도 검색

필터를 사용하여 검색 범위를 좁힙니다. 예를 들어 사용자가 Teams에 로그인한 경우 [애플리케이션] 필터를 사용하여 이를 Teams로 설정합니다. 관리자는 특정 로그인을 찾기 위해 대화형 및 비대화형 탭 모두에서 로그인을 확인해야 할 수 있습니다. 검색 범위를 더 좁히기 위해 관리자는 여러 필터를 적용할 수 있습니다.

## <a name="continuous-access-evaluation-workbooks"></a>지속적인 액세스 평가 통합 문서

지속적인 액세스 평가 인사이트 통합 문서를 사용하면 관리자가 테넌트에 대한 CAE 사용 인사이트를 보고 모니터링할 수 있습니다. 테이블에는 IP가 일치하지 않는 인증 시도가 표시됩니다. 이 통합 문서는 조건부 액세스 범주 아래에서 템플릿으로 찾을 수 있습니다. 

### <a name="accessing-the-cae-workbook-template"></a>CAE 통합 문서 템플릿 액세스

통합 문서가 표시되기 전에 Log Analytics 통합을 완료해야 합니다. Azure AD 로그인 로그를 Log Analytics 작업 영역으로 스트리밍하는 방법에 대한 자세한 내용은 [Azure AD 로그를 Azure Monitor 로그와 통합](../reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md) 문서를 참조하세요.
 
1.  조건부 액세스 관리자, 보안 관리자 또는 전역 관리자 권한으로 **Azure Portal** 에 로그인합니다. 
1.  **Azure Active Directory** > **통합 문서** 로 차례로 이동합니다.
1.  **퍼블릭 템플릿** 아래에서 **지속적 액세스 평가 인사이트** 를 검색합니다.

[ ![갤러리에서 CAE 인사이트 통합 문서를 찾아서 계속 모니터링](./media/howto-continuous-access-evaluation-troubleshoot/azure-ad-workbooks-continuous-access-evaluation.png) ](./media/howto-continuous-access-evaluation-troubleshoot/azure-ad-workbooks-continuous-access-evaluation.png#lightbox)

**지속적 액세스 평가 인사이트** 통합 문서에는 다음 테이블이 포함되어 있습니다.

### <a name="potential-ip-address-mismatch-between-azure-ad-and-resource-provider"></a>Azure AD와 리소스 공급자 간의 잠재적 IP 주소 불일치  

![잠재적 IP 주소 불일치를 보여 주는 통합 문서 테이블 1](./media/howto-continuous-access-evaluation-troubleshoot/continuous-access-evaluation-insights-workbook-table-1.png)

Azure AD와 리소스 공급자 테이블 간의 잠재적 IP 주소 불일치 테이블을 사용하면 관리자가 Azure AD에서 검색한 IP 주소가 리소스 공급자에서 검색한 IP 주소와 일치하지 않는 세션을 조사할 수 있습니다. 

이 통합 문서 테이블은 해당 IP 주소와 세션 중에 CAE 토큰이 발급되었는지 여부를 표시하여 이러한 시나리오를 조명합니다. 

#### <a name="ip-address-configuration"></a>IP 주소 구성

ID 공급자 및 리소스 공급자는 서로 다른 IP 주소를 볼 수 있습니다. 이 불일치는 다음 예로 인해 발생할 수 있습니다.

- 네트워크에서 분할 터널링을 구현합니다.
- 리소스 공급자가 IPv6 주소를 사용하고, Azure AD에서 IPv4 주소를 사용합니다.
- 네트워크 구성으로 인해 Azure AD에서 클라이언트로부터 하나의 IP 주소를 확인하고, 리소스 공급자는 클라이언트의 다른 IP 주소를 확인합니다.

이 시나리오가 환경에 있는 경우 무한 루프를 방지하기 위해 Azure AD에서 1시간 CAE 토큰을 발급하고 해당 1시간 기간 동안 클라이언트 위치 변경을 적용하지 않습니다. 이 경우에도 클라이언트 위치 변경 이벤트 외의 다른 이벤트를 계속 평가하므로 기존의 1시간 토큰에 비해 보안이 향상됩니다.

관리자는 시간 범위 및 애플리케이션별로 필터링된 레코드를 볼 수 있습니다. 관리자는 검색된 불일치 IP 수와 지정된 기간 동안의 총 로그인 수를 비교할 수 있습니다. 

사용자의 차단을 해제하기 위해 관리자는 특정 IP 주소를 신뢰할 수 있는 명명된 위치에 추가할 수 있습니다.

1.  조건부 액세스 관리자, 보안 관리자 또는 전역 관리자 권한으로 **Azure Portal** 에 로그인합니다. 
1.  **Azure Active Directory** > **보안** > **조건부 액세스** > **명명된 위치** 로 이동합니다. 여기서는 신뢰할 수 있는 IP 위치를 만들거나 업데이트할 수 있습니다.

> [!NOTE]
> P 주소를 신뢰할 수 있는 명명된 위치로 추가하기 전에 IP 주소가 실제로 의도한 조직에 속하는지 확인합니다.

명명된 위치에 대한 자세한 내용은 [위치 조건 사용](location-condition.md#named-locations) 문서를 참조하세요.
 
## <a name="next-steps"></a>다음 단계

- [Azure Monitor 로그에 Azure AD 로그 통합](../reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)
- [위치 조건 사용](location-condition.md#named-locations)
- [지속적인 액세스 평가](concept-continuous-access-evaluation.md)
