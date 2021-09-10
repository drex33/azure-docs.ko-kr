---
title: Azure AD에서 비활성 사용자 계정을 관리하는 방법 | Microsoft Docs
description: Azure AD에서 더 이상 사용되지 않는 사용자 계정을 검색하고 처리하는 방법을 알아봅니다.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: mtillman
editor: ''
ms.assetid: ada19f69-665c-452a-8452-701029bf4252
ms.service: active-directory
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 05/06/2021
ms.author: markvi
ms.reviewer: besiler
ms.collection: M365-identity-device-management
ms.openlocfilehash: a5fbc6789fe83f07e0a22239e93a08eb9bd8cf4f
ms.sourcegitcommit: 16e25fb3a5fa8fc054e16f30dc925a7276f2a4cb
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/25/2021
ms.locfileid: "122830768"
---
# <a name="how-to-manage-inactive-user-accounts-in-azure-ad"></a>방법: Azure AD의 비활성 사용자 계정 관리

대규모 환경에서는 직원이 조직을 떠날 때 사용자 계정이 항상 삭제되는 것은 아닙니다. 이는 보안 위험을 의미하므로 IT 관리자는 이러한 사용되지 않는 사용자 계정을 감지하고 처리해야 합니다.

이 문서에서는 Azure AD에서 사용되지 않는 사용자 계정을 처리하는 방법을 설명합니다. 

> [!IMPORTANT]
> Microsoft Graph에서 `/beta` 버전의 API는 변경될 수 있습니다. 프로덕션 애플리케이션에서는 이러한 API의 사용이 지원되지 않습니다. v1.0에서 API를 사용할 수 있는지 확인하려면 **버전** 선택기를 사용합니다.

## <a name="what-are-inactive-user-accounts"></a>비활성 사용자 계정이란?

비활성 계정은 조직의 구성원이 리소스에 대한 액세스 권한을 얻기 위해 더 이상 필요하지 않은 사용자 계정입니다. 비활성 계정의 주요 식별 기준 하나는 해당 계정이 환경에 로그인하는 데 한동안 사용되지 않은 것입니다. 비활성 계정은 로그인 활동에 연결되므로 마지막으로 성공한 로그인의 타임스탬프를 사용하여 검색할 수 있습니다. 

이 방법을 사용하는 경우 환경에서 한동안이 어느 정도인지 정의하기가 어렵습니다. 예를 들어 사용자가 휴가 중이어서 환경에 한동안 로그인하지 않을 수 있습니다. 비활성 사용자 계정에 대한 델타를 정의할 때 사용자 환경에 로그인하지 않는 모든 합법적인 이유를 고려해야 합니다. 많은 조직에서 비활성 사용자 계정에 대한 델타는 90~180일입니다. 

마지막으로 성공한 로그인은 사용자가 계속해서 리소스에 액세스할 필요가 있다는 잠재적 정보를 제공합니다.  그룹 멤버 자격 또는 앱 액세스가 여전히 필요한지 또는 제거할 수 있는지를 확인하는 데 도움이 될 수 있습니다. 외부 사용자 관리의 경우 외부 사용자가 테넌트 내에서 활성 상태인지 아니면 정리되어야 하는지 파악할 수 있습니다. 

    
## <a name="how-to-detect-inactive-user-accounts"></a>비활성 사용자 계정을 검색하는 방법

**Microsoft Graph** API의 **signInActivity** 리소스 종류에 의해 노출되는 **lastSignInDateTime** 속성을 평가하여 비활성 계정을 검색합니다. **LastSignInDateTime** 속성은 사용자가 Azure AD에 성공적으로 대화형 로그인을 수행한 마지막 시간을 보여 줍니다. 이 속성을 사용하여 다음 시나리오에 대한 솔루션을 구현할 수 있습니다.

- **이름 기준 사용자**: 이 시나리오에서는 이름으로 특정 사용자를 검색합니다. 그러면 lastSignInDateTime을 평가할 수 있습니다. `https://graph.microsoft.com/beta/users?$filter=startswith(displayName,'markvi')&$select=displayName,signInActivity`

- **날짜 기준 사용자**: 이 시나리오에서는 지정된 날짜 이전의 lastSignInDateTime을 사용하여 사용자 목록을 요청합니다. `https://graph.microsoft.com/beta/users?filter=signInActivity/lastSignInDateTime le 2019-06-01T00:00:00Z`

> [!NOTE]
> 모든 사용자의 마지막 로그인 날짜에 대 한 보고서를 생성해야 하는 경우 다음 시나리오를 사용할 수 있습니다.
> **모든 사용자에 대한 마지막 로그인 날짜 및 시간**: 이 시나리오에서는 모든 사용자 목록과 각 사용자에 대한 마지막 lastSignInDateTime을 요청합니다. `https://graph.microsoft.com/beta/users?$select=displayName,signInActivity` 

## <a name="what-you-need-to-know"></a>알아야 하는 작업

이 섹션에서는 lastSignInDateTime 속성에 대해 알아야 할 사항을 설명합니다.

### <a name="how-can-i-access-this-property"></a>이 속성에 액세스하려면 어떻게 해야 하나요?

**lastSignInDateTime** 속성은 [Microsoft Graph REST API](/graph/overview#whats-in-microsoft-graph)의 [signInActivity 리소스 종류](/graph/api/resources/signinactivity?view=graph-rest-beta&preserve-view=true)에 의해 노출됩니다.   

> [!NOTE]
> signInActivity Graph API 엔드포인트는 아직 미국 정부 GCC High 환경에서 지원되지 않습니다.

### <a name="is-the-lastsignindatetime-property-available-through-the-get-azureaduser-cmdlet"></a>Get-AzureAdUser cmdlet을 통해 lastSignInDateTime 속성을 사용할 수 있나요?

아니요.

### <a name="what-edition-of-azure-ad-do-i-need-to-access-the-property"></a>이 속성에 액세스하는 데 필요한 Azure AD 버전은 무엇인가요?

이 속성에 액세스하려면 Azure Active Directory Premium 버전이 필요합니다.

### <a name="what-permission-do-i-need-to-read-the-property"></a>이 속성을 읽는 데 필요한 권한은 무엇인가요?

이 속성을 읽으려면 다음 권한을 부여해야 합니다. 

- AuditLogs.Read.All
- Organization.Read.All  


### <a name="when-does-azure-ad-update-the-property"></a>Azure AD는 이 속성을 언제 업데이트하나요?

각 대화형 로그인이 성공할 때마다 기본 데이터 저장소가 업데이트됩니다. 일반적으로 성공적인 로그인은 10분 이내에 관련 로그인 보고서에 표시됩니다.
 

### <a name="what-does-a-blank-property-value-mean"></a>빈 속성 값은 무엇을 의미하나요?

lastSignInDateTime 타임스탬프를 생성하려면 성공적인 로그인이 필요합니다. lastSignInDateTime 속성은 새로운 기능이므로 다음과 같은 경우 lastSignInDateTime 속성의 값을 비워 둘 수 있습니다.

- 사용자가 마지막으로 성공한 로그인이 2020년 4월 이전에 이루어진 경우
- 영향을 받는 사용자 계정이 성공적인 로그인에 사용된 적이 없는 경우

## <a name="next-steps"></a>다음 단계

* [인증서와 함께 Azure Active Directory reporting API를 사용하여 데이터 가져오기](tutorial-access-api-with-certificates.md).
* [감사 API 참조](/graph/api/resources/directoryaudit) 
* [로그인 활동 보고서 API 참조](/graph/api/resources/signin)

