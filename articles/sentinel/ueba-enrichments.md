---
title: Azure Sentinel UEBA 강화 참조 | Microsoft Docs
description: 이 문서에서는 Azure Sentinel의 엔터티 동작 분석에 의해 생성된 엔터티 보강을 표시합니다.
author: yelevin
manager: rkarlin
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: reference
ms.date: 05/10/2021
ms.author: yelevin
ms.custom: ignite-fall-2021
ms.openlocfilehash: 944c2b155a0cfa48d98876aa463c27cf5c47d40c
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131083710"
---
# <a name="azure-sentinel-ueba-enrichments-reference"></a>Azure Sentinel UEBA 보강 참조

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

이 문서에서는 **로그** 에 있는 Azure Sentinel **BehaviorAnalytics** 테이블([엔터티 세부 정보 페이지](identify-threats-with-entity-behavior-analytics.md#how-to-use-entity-pages)에 언급됨)에 대해 설명하며 해당 테이블의 엔터티 보강 필드에 대한 세부 정보를 제공합니다. 이 내용은 보안 인시던트 조사에 집중하고 강화하는 데 사용할 수 있습니다.

BehaviorAnalytics 테이블의 다음 세 가지 동적 필드는 [아래 표](#entity-enrichments-dynamic-fields)에 설명되어 있습니다.

[UsersInsights](#usersinsights-field) 및 [DevicesInsights](#devicesinsights-field) 필드에는 Active Directory/Azure AD 및 Microsoft 위협 인텔리전스 원본의 엔터티 정보가 포함되어 있습니다.

[ActivityInsights](#activityinsights-field) 필드에는 Azure Sentinel의 엔터티 동작 분석에 의해 구축된 동작 프로필을 기반으로 하는 엔터티 정보가 포함됩니다. 

<a name="baseline-explained"></a>사용자 활동은 사용될 때마다 동적으로 컴파일되는 기준에 따라 분석됩니다. 각 활동에는 동적 기준이 파생되는 정의된 되돌아보기 기간이 있습니다. 되돌아보기 기간은 이 표의 [**기준**](#activityinsights-field) 열에 지정되어 있습니다.

> [!NOTE]
> 모든 [엔터티 보강 필드](#entity-enrichments-dynamic-fields) 표의 **보강 이름** 열에는 두 행의 정보가 표시됩니다. 
>
> - 첫 번째는 보강의 "식별 이름"(**굵게**)입니다.
> - 두 번째는 [**행동 분석 표**](#behavioranalytics-table)에 저장된 보강의 필드 이름 *(기울임꼴 및 괄호)* 입니다.

> [!IMPORTANT]
> 언급된 기능은 현재 미리 보기로 제공됩니다. [Azure Preview 추가 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)에는 베타, 미리 보기 또는 아직 일반 공급으로 릴리스되지 않은 Azure 기능에 적용되는 추가 법률 용어가 포함되어 있습니다.
>
## <a name="behavioranalytics-table"></a>BehaviorAnalytics 테이블

다음 표에서는 Azure Sentinel의 각 [엔터티 세부 정보 페이지](identify-threats-with-entity-behavior-analytics.md#how-to-use-entity-pages)에 표시되는 동작 분석 데이터를 설명합니다.

| 필드                     | 형식 | Description                                                  |
|---------------------------|------|--------------------------------------------------------------|
| **TenantId**              | 문자열 | 테넌트의 고유한 ID 번호입니다.                             |
| **SourceRecordId**        | 문자열 | EBA 이벤트의 고유한 ID 번호입니다.                          |
| **TimeGenerated**         | Datetime | 활동 발생의 타임스탬프입니다.                   |
| **TimeProcessed**         | Datetime | EBA 엔진에서 활동 처리의 타임스탬프입니다. |
| **ActivityType**          | 문자열 | 활동의 상위 수준 범주입니다.                        |
| **ActionType**            | 문자열 | 활동의 정규화된 이름입니다.                            |
| **UserName**              | 문자열 | 활동을 시작한 사용자의 사용자 이름입니다.           |
| **UserPrincipalName**     | 문자열 | 활동을 시작한 사용자의 전체 사용자 이름입니다.      |
| **EventSource**           | 문자열 | 원래 이벤트를 제공한 데이터 원본입니다.               |
| **SourceIPAddress**       | 문자열 | 활동이 시작된 IP 주소입니다.               |
| **SourceIPLocation** | 문자열 | 활동이 시작된 국가이며, IP 주소에서 보강됩니다. |
| **SourceDevice**          | 문자열 | 활동을 시작한 디바이스의 호스트 이름입니다.         |
| **DestinationIPAddress**  | 문자열 | 활동 대상의 IP 주소입니다.                   |
| **DestinationIPLocation** | 문자열 | 활동 대상의 국가이며, IP 주소에서 보강됩니다. |
| **DestinationDevice**     | 문자열 | 대상 디바이스의 이름입니다.                                  |
| **UsersInsights**         | 동적 | 관련 사용자의 상황별 보강입니다([아래 세부 정보](#usersinsights-field)). |
| **DevicesInsights**       | 동적 | 참여하는 디바이스의 상황별 보강입니다([아래 세부 정보](#devicesinsights-field)). |
| **ActivityInsights**      | 동적 | 프로파일링을 기반으로 하는 활동의 상황별 분석입니다([아래 세부 정보](#activityinsights-field)). |
| **InvestigationPriority** | int | 변칙 점수로, 0-10(0=무해, 10=매우 비정상) 범위입니다.   |



## <a name="entity-enrichments-dynamic-fields"></a>엔터티 보강 동적 필드

### <a name="usersinsights-field"></a>UsersInsights 필드

다음 표에서는 BehaviorAnalytics 테이블의 **UsersInsights** 동적 필드에 제공되는 보강에 대해 설명합니다.

| 보강 이름 | Description | 샘플 값 |
| --- | --- | --- |
| **계정 표시 이름**<br>*(AccountDisplayName)* | 사용자의 계정 표시 이름입니다. | 관리자, Hayden Cook |
| **계정 도메인**<br>*(AccountDomain)* | 사용자의 계정 도메인 이름입니다. |  |
| **계정 개체 ID**<br>*(AccountObjectID)* | 사용자의 계정 개체 ID입니다. | a58df659-5cab-446c-9dd0-5a3af20ce1c2 |
| **폭발 반지름**<br>*(BlastRadius)* | 폭발 반지름은 여러 가지 요소(조직 트리의 사용자 위치 및 사용자 Azure Active Directory 역할 및 권한)를 기준으로 계산됩니다. | 낮음, 보통, 높음 |
| **유휴 계정**<br>*(IsDormantAccount)* | 지난 180일간 계정이 사용되지 않았습니다. | True, False |
| **로컬 관리자**<br>*(IsLocalAdmin)* | 계정에 로컬 관리자 권한이 있습니다. | True, False |
| **새 계정**<br>*(IsNewAccount)* | 계정이 지난 30일 이내에 만들어졌습니다. | True, False |
| **온-프레미스 SID**<br>*(OnPremisesSID)* | 작업과 관련된 사용자의 온-프레미스 SID입니다. | S-1-5-21-1112946627-1321165628-2437342228-1103 |
|

### <a name="devicesinsights-field"></a>DevicesInsights 필드

다음 표에서는 BehaviorAnalytics 테이블의 **DevicesInsights** 동적 필드에 제공되는 보강에 대해 설명합니다.

| 보강 이름 | Description | 샘플 값 |
| --- | --- | --- |
| **브라우저**<br>*(Browser)* | 작업에 사용되는 브라우저입니다. | Edge, Chrome |
| **디바이스 패밀리**<br>*(DeviceFamily)* | 작업에 사용되는 디바이스 패밀리입니다. | Windows |
| **디바이스 유형**<br>*(DeviceType)* | 작업에 사용되는 클라이언트 디바이스 유형입니다. | 데스크톱 |
| **ISP**<br>*(ISP)* | 작업에 사용되는 인터넷 서비스 공급자입니다. |  |
| **운영 체제**<br>*(OperatingSystem)* | 작업에 사용되는 운영 체제입니다. | Windows 10 |
| **위협 인텔리전스 표시기 설명**<br>*(ThreatIntelIndicatorDescription)* | 작업에 사용된 IP 주소에서 확인된, 관찰된 위협 표시기에 대한 설명입니다. | 호스트는 봇네트의 멤버임: azorult |
| **위협 인텔리전스 표시기 유형**<br>*(ThreatIntelIndicatorType)* | 작업에 사용된 IP 주소에서 확인된 위협 표시기의 유형입니다. | 봇네트, C2, CryptoMining, 다크넷, Ddos, MaliciousUrl, 맬웨어, 피싱, 프록시, PUA, 관심 목록 |
| **사용자 에이전트**<br>*(UserAgent)* | 작업에 사용되는 사용자 에이전트입니다. | Microsoft Azure Graph Client Library 1.0,<br>Swagger-Codegen/1.4.0.0/csharp,<br>EvoSTS |
| **사용자 에이전트 패밀리**<br>*(UserAgentFamily)* | 작업에 사용되는 사용자 에이전트 패밀리입니다. | Chrome, Edge, Firefox |
|

### <a name="activityinsights-field"></a>ActivityInsights 필드

다음 표에서는 BehaviorAnalytics 테이블의 **ActivityInsights** 동적 필드에 제공되는 보강에 대해 설명합니다.

#### <a name="action-performed"></a>수행한 작업

| 보강 이름 | [기준](#baseline-explained)(일) | Description | 샘플 값 |
| --- | --- | --- | --- |
| **사용자가 작업을 처음으로 수행**<br>*(FirstTimeUserPerformedAction)* | 180 | 사용자가 작업을 처음으로 수행했습니다. | True, False |
| **사용자가 일반적으로 수행하지 않는 작업**<br>*(ActionUncommonlyPerformedByUser)* | 10 | 사용자가 작업을 일반적으로 수행하지 않습니다. | True, False |
| **피어 간에 일반적으로 수행되지 않는 작업**<br>*(ActionUncommonlyPerformedAmongPeers)* | 180 | 작업이 사용자의 피어 간에 일반적으로 수행되지 않습니다. | True, False |
| **테넌트에서 작업을 처음으로 수행**<br>*(FirstTimeActionPerformedInTenant)* | 180 | 조직의 임의 사용자가 작업을 처음으로 수행했습니다. | True, False |
| **테넌트에서 일반적으로 수행되지 않는 작업**<br>*(ActionUncommonlyPerformedInTenant)* | 180 | 작업이 조직에서 일반적으로 수행되지 않습니다. | True, False |
|

#### <a name="app-used"></a>사용한 앱

| 보강 이름 | [기준](#baseline-explained)(일) | Description | 샘플 값 |
| --- | --- | --- | --- |
| **사용자가 앱을 처음으로 사용**<br>*(FirstTimeUserUsedApp)* | 180 | 사용자가 앱을 처음으로 사용했습니다. | True, False |
| **사용자가 일반적으로 사용하지 않는 앱**<br>*(AppUncommonlyUsedByUser)* | 10 | 사용자가 앱을 일반적으로 사용하지 않습니다. | True, False |
| **피어 간에 일반적으로 사용되지 않는 앱**<br>*(AppUncommonlyUsedAmongPeers)* | 180 | 앱이 사용자의 피어 간에 일반적으로 사용되지 않습니다. | True, False |
| **테넌트에서 처음 관찰된 앱**<br>*(FirstTimeAppObservedInTenant)* | 180 | 앱이 조직에서 처음으로 관찰되었습니다. | True, False |
| **테넌트에서 일반적으로 사용되지 않는 앱**<br>*(AppUncommonlyUsedInTenant)* | 180 | 앱이 조직에서 일반적으로 사용되지 않습니다. | True, False |
| 

#### <a name="browser-used"></a>사용되는 브라우저

| 보강 이름 | [기준](#baseline-explained)(일) | Description | 샘플 값 |
| --- | --- | --- | --- |
| **사용자가 브라우저를 통해 처음으로 연결**<br>*(FirstTimeUserConnectedViaBrowser)* | 30 | 사용자가 브라우저를 처음으로 관찰했습니다. | True, False |
| **사용자가 일반적으로 사용하지 않는 브라우저**<br>*(BrowserUncommonlyUsedByUser)* | 10 | 사용자가 브라우저를 일반적으로 사용하지 않습니다. | True, False |
| **피어 간에 일반적으로 사용되지 않는 브라우저**<br>*(BrowserUncommonlyUsedAmongPeers)* | 30 | 브라우저가 사용자의 피어 간에 일반적으로 사용되지 않습니다. | True, False |
| **테넌트에서 처음으로 관찰된 브라우저**<br>*(FirstTimeBrowserObservedInTenant)* | 30 | 브라우저가 조직에서 처음으로 관찰되었습니다. | True, False |
| **테넌트에서 일반적으로 사용되지 않는 브라우저**<br>*(BrowserUncommonlyUsedInTenant)* | 30 | 브라우저가 조직에서 일반적으로 사용되지 않습니다. | True, False |
| 

#### <a name="country-connected-from"></a>연결된 국가

| 보강 이름 | [기준](#baseline-explained)(일) | Description | 샘플 값 |
| --- | --- | --- | --- |
| **사용자가 국가에서 처음으로 연결**<br>*(FirstTimeUserConnectedFromCountry)* | 90 | 사용자가 IP 주소에서 확인된 지리적 위치에서 처음으로 연결했습니다. | True, False |
| **사용자가 일반적으로 연결하지 않는 국가**<br>*(CountryUncommonlyConnectedFromByUser)* | 10 | 사용자가 IP 주소에서 확인된 지리적 위치에서 일반적으로 연결하지 않습니다. | True, False |
| **피어 간에 일반적으로 연결되지 않는 국가**<br>*(CountryUncommonlyConnectedFromAmongPeers)* | 90 | IP 주소에서 확인된 지리적 위치가 사용자의 피어에서 일반적으로 연결되지 않습니다. | True, False |
| **테넌트에서 관찰된 국가에서 처음으로 연결**<br>*(FirstTimeConnectionFromCountryObservedInTenant)* | 90 | 조직의 임의 사용자가 국가에서 처음으로 연결했습니다. | True, False |
| **테넌트에서 일반적으로 연결되지 않는 국가**<br>*(CountryUncommonlyConnectedFromInTenant)* | 90 | IP 주소에서 확인된 지리적 위치가 조직에서 일반적으로 연결되지 않습니다. | True, False |
| 

#### <a name="device-used-to-connect"></a>연결에 사용되는 디바이스

| 보강 이름 | [기준](#baseline-explained)(일) | Description | 샘플 값 |
| --- | --- | --- | --- |
| **사용자가 디바이스에서 처음으로 연결**<br>*(FirstTimeUserConnectedFromDevice)* | 30 | 사용자가 원본 디바이스에서 처음으로 연결했습니다. | True, False |
| **사용자가 일반적으로 사용하지 않는 디바이스**<br>*(DeviceUncommonlyUsedByUser)* | 10 | 사용자가 디바이스를 일반적으로 사용하지 않습니다. | True, False |
| **피어 간에 일반적으로 사용되지 않는 디바이스**<br>*(DeviceUncommonlyUsedAmongPeers)* | 180 | 디바이스가 사용자의 피어 간에 일반적으로 사용되지 않습니다. | True, False |
| **테넌트에서 처음으로 관찰된 디바이스**<br>*(FirstTimeDeviceObservedInTenant)* | 30 | 디바이스가 조직에서 처음으로 관찰되었습니다. | True, False |
| **테넌트에서 일반적으로 사용되지 않는 디바이스**<br>*(DeviceUncommonlyUsedInTenant)* | 180 | 디바이스가 조직에서 일반적으로 사용되지 않습니다. | True, False |
| 

#### <a name="other-device-related"></a>기타 디바이스 관련

| 보강 이름 | [기준](#baseline-explained)(일) | Description | 샘플 값 |
| --- | --- | --- | --- |
| **사용자가 디바이스에 처음으로 로그인**<br>*(FirstTimeUserLoggedOnToDevice)* | 180 | 사용자가 대상 디바이스에 처음으로 연결했습니다. | True, False |
| **테넌트에서 일반적으로 사용되지 않는 디바이스 패밀리**<br>*(DeviceFamilyUncommonlyUsedInTenant)* | 30 | 디바이스 패밀리가 조직에서 일반적으로 사용되지 않습니다. | True, False |
| 

#### <a name="internet-service-provider-used-to-connect"></a>연결에 사용되는 인터넷 서비스 공급자

| 보강 이름 | [기준](#baseline-explained)(일) | Description | 샘플 값 |
| --- | --- | --- | --- |
| **사용자가 ISP를 통해 처음으로 연결**<br>*(FirstTimeUserConnectedViaISP)* | 30 | 사용자가 ISP를 처음으로 관찰했습니다. | True, False |
| **사용자가 일반적으로 사용하지 않는 ISP**<br>*(ISPUncommonlyUsedByUser)* | 10 | 사용자가 ISP를 일반적으로 사용하지 않습니다. | True, False |
| **피어 간에 일반적으로 사용되지 않는 ISP**<br>*(ISPUncommonlyUsedAmongPeers)* | 30 | ISP가 사용자의 피어 간에 일반적으로 사용되지 않습니다. | True, False |
| **테넌트에서 ISP를 통해 처음으로 연결**<br>*(FirstTimeConnectionViaISPInTenant)* | 30 | ISP가 조직에서 처음으로 관찰되었습니다. | True, False |
| **테넌트에서 일반적으로 사용되지 않는 ISP**<br>*(ISPUncommonlyUsedInTenant)* | 30 | ISP가 조직에서 일반적으로 사용되지 않습니다. | True, False |
| 

#### <a name="resource-accessed"></a>액세스한 리소스

| 보강 이름 | [기준](#baseline-explained)(일) | Description | 샘플 값 |
| --- | --- | --- | --- |
| **사용자가 리소스에 처음으로 액세스**<br>*(FirstTimeUserAccessedResource)* | 180 | 사용자가 리소스에 처음으로 액세스했습니다. | True, False |
| **사용자가 일반적으로 액세스하지 않는 리소스**<br>*(ResourceUncommonlyAccessedByUser)* | 10 | 사용자가 리소스에 일반적으로 액세스하지 않습니다. | True, False |
| **피어 간에 일반적으로 액세스되지 않는 리소스**<br>*(ResourceUncommonlyAccessedAmongPeers)* | 180 | 리소스가 사용자의 피어 간에 일반적으로 액세스되지 않습니다. | True, False |
| **테넌트에서 처음으로 액세스된 리소스**<br>*(FirstTimeResourceAccessedInTenant)* | 180 | 조직의 임의 사용자가 리소스에 처음으로 액세스했습니다. | True, False |
| **테넌트에서 일반적으로 액세스되지 않는 리소스**<br>*(ResourceUncommonlyAccessedInTenant)* | 180 | 리소스가 조직에서 일반적으로 액세스되지 않습니다. | True, False |
| 

#### <a name="miscellaneous"></a>기타

| 보강 이름 | [기준](#baseline-explained)(일) | Description | 샘플 값 |
| --- | --- | --- | --- |
| **사용자가 작업을 마지막으로 수행**<br>*(LastTimeUserPerformedAction)* | 180 | 사용자가 마지막으로 동일한 작업을 수행했습니다. | \<Timestamp\> |
| **유사한 작업이 과거에 수행되지 않음**.<br>*(SimilarActionWasn'tPerformedInThePast)* | 30 | 사용자가 동일한 리소스 공급자에서 작업을 수행하지 않았습니다. | True, False |
| **원본 IP 위치**<br>*(SourceIPLocation)* | *해당 사항 없음* | 작업의 원본 IP에서 확인된 국가입니다. | [Surrey, 영국] |
| **일반적이지 않고 많은 양의 작업**<br>*(UncommonHighVolumeOfOperations)* | 7 | 사용자가 동일한 공급자 내에서 많은 유사한 작업을 수행했습니다. | True, False |
| **비정상적인 수의 Azure AD 조건부 액세스 실패**<br>*(UnusualNumberOfAADConditionalAccessFailures)* | 5 | 조건부 액세스로 인해 비정상적인 수의 사용자가 인증에 실패했습니다. | True, False |
| **비정상적인 수의 디바이스 추가**<br>*(UnusualNumberOfDevicesAdded)* | 5 | 사용자가 비정상적인 수의 디바이스를 추가했습니다. | True, False |
| **비정상적인 수의 디바이스 삭제**<br>*(UnusualNumberOfDevicesDeleted)* | 5 | 사용자가 비정상적인 수의 디바이스를 삭제했습니다. | True, False |
| **비정상적인 수의 사용자가 그룹에 추가됨**<br>*(UnusualNumberOfUsersAddedToGroup)* | 5 | 비정상적인 수의 사용자가 그룹에 추가되었습니다. | True, False |
|


## <a name="identityinfo-table-public-preview"></a>IdentityInfo 테이블(공개 미리 보기)

Azure Sentinel 작업 영역에 대해 [UEBA를 사용하도록 설정](enable-entity-behavior-analytics.md)한 후에는 Azure Active Directory의 데이터가, Azure Sentinel에서의 사용을 위해 Log Analytics의 **IdentityInfo** 테이블에 대해 동기화됩니다. 분석 규칙에서 Azure AD로부터 동기화된 사용자 데이터를 포함하여 사용 사례에 맞게 분석을 향상시키고 가양성을 줄일 수 있습니다.

최초 동기화는 며칠이 걸릴 수도 있으나 데이터가 완전히 동기화된 후에는

- Azure AD의 사용자 프로필에 대한 변경 내용이 15분 이내에 **IdentityInfo** 테이블에서 업데이트됩니다.

- **IdentityInfo** 테이블과 Azure AD 간의 그룹 및 역할 정보는 매일 동기화됩니다.

- Azure Sentinel은 21일마다 전체 Azure AD와의 동기화를 통해 오래된 레코드가 완전히 업데이트될 수 있게 합니다.

- **IdentityInfo** 테이블의 기본 보존 시간은 30일입니다.


> [!NOTE]
> 현재 기본 제공 역할만 지원됩니다.
>
> 사용자가 그룹에서 제거된 경우 삭제된 그룹에 대한 데이터는 현재 지원되지 않습니다.
>

다음 표는 Log Analytics의 **IdentityInfo** 테이블에 포함된 사용자 ID 데이터를 설명합니다.

| 필드                      | 형식     | 설명                                                                                                             |
| --------------------------- | -------- | -------------------------------------------------------- |
| **AccountCloudSID**             | 문자열   | 계정의 Azure AD 보안 식별자입니다.       |
| **AccountCreationTime**         | Datetime | 사용자 계정이 만들어진 날짜(UTC)입니다.    |
| **AccountDisplayName**          | 문자열   | 사용자 계정의 표시 이름입니다.           |
| **AccountDomain**               | 문자열   | 사용자 계정의 도메인 이름입니다.  |
| **AccountName**                 | 문자열   | 사용자 계정의 사용자 이름입니다.      |
| **AccountObjectId**             | 문자열   | 사용자 계정에 대한 Azure Active Directory 개체 ID입니다.           |
| **AccountSID**                  | 문자열   | 사용자 계정의 온-프레미스 보안 식별자입니다.      |
| **AccountTenantId**             | 문자열   | 사용자 계정의 Azure Active Directory 테넌트 ID입니다.    |
| **AccountUPN**                  | 문자열   | 사용자 계정의 사용자 계정 이름입니다.     |
| **AdditionalMailAddresses**     | 동적  | 사용자의 추가 이메일 주소입니다.   |
| **AssignedRoles**               | 동적  | 사용자 계정이 할당된 Azure AD 역할입니다.    |
| **도시**                        | 문자열   | 사용자 계정의 도시입니다.   |
| **국가**                     | 문자열   | 사용자 계정의 국가입니다.   |
| **DeletedDateTime**             | Datetime | 사용자가 삭제된 날짜와 시간입니다.       |
| **부서**                  | 문자열   | 사용자 계정의 부서입니다.    |
| **GivenName**                   | 문자열   | 사용자 계정의 이름입니다.     |
| **GroupMembership**             | 동적  | 사용자 계정이 멤버인 Azure AD 그룹입니다.      |
| **IsAccountEnabled**            | 부울     | 사용자 계정이 Azure AD에서 사용하도록 설정되었는지 여부를 나타냅니다.    |
| **JobTitle**                    | 문자열   | 사용자의 직위입니다.    |
| **MailAddress**                 | 문자열   | 사용자 계정의 기본 이메일 주소입니다.    |
| **관리자**                     | 문자열   | 사용자 계정의 관리자 별칭입니다.     |
| **OnPremisesDistinguishedName** | 문자열   | Azure AD DN(고유 이름)입니다. 고유 이름은 쉼표로 연결된 RDN(상대 고유 이름)의 시퀀스입니다. |
| **전화**                       | 문자열   | 사용자 계정의 전화 번호입니다.       |
| **SourceSystem**                | 문자열   | 사용자 데이터가 시작된 시스템입니다.   |
| **State**                       | 문자열   | 사용자 계정의 지리적 상태입니다.  |
| **StreetAddress**               | 문자열   | 사용자 계정의 사무실 주소입니다.    |
| **Surname**                     | 문자열   | 사용자의 성입니다. 계정입니다.      |
| **TenantId**                    | 문자열   |          사용자의 테넌트 ID입니다.   |
| **TimeGenerated**               | Datetime | 이벤트가 생성된 시간입니다(UTC).       |
| **형식**                        | 문자열   | 테이블의 이름입니다.          |
| **UserState**                   | 문자열   | Azure AD에서 사용자 계정의 현재 상태입니다(활성/사용 안 함/유휴/잠금).  |
| **UserStateChangedOn**          | Datetime | 계정 상태가 마지막으로 변경된 날짜입니다(UTC).     |
| **UserType**                    | 문자열   | 사용자 유형입니다.         |


## <a name="next-steps"></a>다음 단계

이 문서에서는 Azure Sentinel 엔터티 동작 분석 테이블 스키마에 대해 설명했습니다.

- [엔터티 동작 분석](identify-threats-with-entity-behavior-analytics.md)에 대해 자세히 알아봅니다.
- 조사에 [사용할 UEBA를 준비](investigate-with-ueba.md)합니다.
