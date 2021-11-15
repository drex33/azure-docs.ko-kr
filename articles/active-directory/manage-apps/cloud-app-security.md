---
title: Microsoft Cloud App Security에서 제공하는 앱 표시 유형 및 제어
titleSuffix: Azure AD
description: 앱 위험 수준을 식별하고, 위반 및 유출을 실시간으로 차단하고, 앱 커넥터를 사용하여 표시 유형 및 거버넌스를 위한 공급자 API를 활용하는 방법을 알아봅니다.
services: active-directory
author: davidmu1
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: conceptual
ms.workload: identity
ms.date: 07/29/2021
ms.author: davidmu
ms.collection: M365-identity-device-management
ms.reviewer: bokacevi, dacurwin
ms.openlocfilehash: 52c17d521ca1a3a4e191c4a4a82d37a98371f879
ms.sourcegitcommit: 1d56a3ff255f1f72c6315a0588422842dbcbe502
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/06/2021
ms.locfileid: "129619845"
---
# <a name="cloud-app-visibility-and-control-in-azure-active-directory"></a>Azure Active Directory의 클라우드 앱 표시 유형 및 제어

클라우드 앱 및 서비스를 최대한 활용하려면 IT 팀이 액세스를 지원하면서도 중요한 데이터 보호에 대한 제어를 유지하기 위한 적절한 균형점을 찾아야 합니다. Microsoft Cloud App Security는 다양한 가시성, 데이터 이동 제어 및 정교한 분석을 제공하여 모든 Microsoft 및 타사 클라우드 서비스에서의 사이버 위협을 식별하고 대처합니다.

## <a name="discover-and-manage-shadow-it-in-your-network"></a>네트워크의 섀도 IT 검색 및 관리

직원들이 사용하는 것으로 의심되는 클라우드 앱의 수를 IT 관리자에게 물어보면 평균 30개에서 40개라고 말하지만, 실제로는 평균 1,000개 이상의 개별 앱을 조직의 직원들이 사용하고 있습니다. 섀도 IT는 사용 중인 앱과 위험 수준을 파악하고 식별하는 데 도움이 됩니다. 80%의 직원은 아무도 검토하지 않은 비사용 권한 앱을 사용하고 보안 및 규정 준수 정책을 준수하지 않을 수 있습니다. 직원들이 회사 네트워크 외부에서 리소스와 앱에 액세스할 수 있으므로 더 이상 방화벽에 대한 규칙 및 정책을 사용하는 것만으로는 충분하지 않습니다.

Microsoft Cloud App Discovery(Azure Active Directory Premium P1 기능)를 사용하여 사용 중인 앱을 검색하고, 이러한 앱의 위험을 살펴보고, 위험한 새 앱을 식별하는 정책을 구성하고, 이러한 앱을 미승인하여 프록시 또는 방화벽 어플라이언스를 통해 기본적으로 차단합니다.

- 섀도 IT 검색 및 식별
- 평가 및 분석
- 앱 관리
- 고급 섀도 IT 검색 보고
- 사용 권한 앱 제어

### <a name="learn-more"></a>자세한 정보

- [네트워크에서 섀도 IT 검색 및 관리](/cloud-app-security/tutorial-shadow-it)
- [Cloud App Security를 사용하여 검색된 앱](/cloud-app-security/discovered-apps)

## <a name="user-session-visibility-and-control"></a>사용자 세션 표시 유형 및 제어

오늘날의 작업 공간에서는 사후에 클라우드 환경의 현황을 파악하는 것만으로는 충분하지 않은 경우가 많습니다. 직원들이 의도적으로 또는 실수로 데이터와 조직을 위험에 노출시키기 전에 위반 및 유출을 실시간으로 차단하는 것이 좋습니다. Microsoft Cloud App Security는 Azure AD(Azure Active Directory)와 함께 조건부 액세스 앱 제어를 사용하여 전체적이고 통합된 환경에서 이러한 기능을 제공합니다.

세션 제어는 역방향 프록시 아키텍처를 사용하며 Azure AD 조건부 액세스와 고유하게 통합됩니다. Azure AD 조건부 액세스를 사용하면 특정 조건에 따라 조직의 앱에 대한 액세스 제어를 적용할 수 있습니다. 조건은 조건부 액세스 정책이 누구에게(사용자 또는 사용자 그룹), 무엇에(클라우드 앱), 어디에(위치 및 네트워크) 적용되는지 정의합니다. 조건을 지정한 후에는 데이터를 실시간으로 보호할 수 있는 Cloud App Security로 사용자를 라우팅할 수 있습니다.  

이 제어를 통해 다음을 수행할 수 있습니다.

- 파일 다운로드 제어
- B2B 시나리오 모니터링  
- 파일에 대한 액세스 제어  
- 다운로드 시 문서 보호  

### <a name="learn-more"></a>자세한 정보

- [Cloud App Security에서 세션 제어로 앱 보호](/cloud-app-security/proxy-intro-aad)

## <a name="advanced-app-visibility-and-controls"></a>고급 앱 표시 유형 및 제어

앱 커넥터는 앱 공급자의 API를 사용하여 연결하는 앱에 대한 Microsoft Cloud App Security의 표시 유형과 제어를 강화할 수 있습니다.
Cloud App Security는 클라우드 공급자가 제공하는 API를 활용합니다. 각 서비스에는 제한, API 제한, 동적 시간 이동 API 기간 등의 고유한 프레임워크 및 API 제한 사항이 있습니다. Cloud App Security 제품 팀은 이러한 서비스와 협력하여 API 사용을 최적화하고 최상의 성능을 제공합니다. Cloud App Security 엔진은 서비스에서 API에 적용하는 다양한 제한 사항을 고려하여 허용된 최대 용량을 사용합니다. 테넌트의 모든 파일 검사와 같은 일부 작업은 다양 한 API 호출이 필요하므로 더 오랜 기간 동안 진행됩니다. 일부 정책은 몇 시간 또는 며칠 동안 실행될 것으로 예상하세요.

### <a name="learn-more"></a>자세한 정보

- [Cloud App Security에서 앱 연결](/cloud-app-security/enable-instant-visibility-protection-and-governance-actions-for-your-apps)

## <a name="next-steps"></a>다음 단계

- [네트워크에서 섀도 IT 검색 및 관리](/cloud-app-security/tutorial-shadow-it)
- [Cloud App Security를 사용하여 검색된 앱](/cloud-app-security/discovered-apps)
- [Cloud App Security에서 세션 제어로 앱 보호](/cloud-app-security/proxy-intro-aad)
- [Cloud App Security에서 앱 연결](/cloud-app-security/enable-instant-visibility-protection-and-governance-actions-for-your-apps)
