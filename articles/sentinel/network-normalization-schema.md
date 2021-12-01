---
title: Microsoft Sentinel 네트워크 세션 정규화 스키마 참조(공개 미리 보기) | Microsoft Docs
description: 이 문서에서는 Microsoft Sentinel 네트워크 세션 정규화 스키마를 표시합니다.
author: oshezaf
ms.topic: reference
ms.date: 11/17/2021
ms.author: ofshezaf
ms.openlocfilehash: 1d04c049a7ba090f2bdc7a3c61fe966079fa7f26
ms.sourcegitcommit: 66b6e640e2a294a7fbbdb3309b4829df526d863d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/01/2021
ms.locfileid: "133365162"
---
# <a name="microsoft-sentinel-network-session-normalization-schema-reference-public-preview"></a>Microsoft Sentinel 네트워크 세션 정규화 스키마 참조(공개 미리 보기)

네트워크 세션 정규화 스키마는 IP 네트워크 활동을 설명하는 데 사용됩니다. 여기에는 네트워크 연결 및 네트워크 세션이 포함됩니다. 이러한 이벤트는 예를 들어 운영 체제, 라우터, 방화벽, 침입 방지 시스템 및 웹 보안 게이트웨이에 의해 보고됩니다.

Microsoft Sentinel의 정규화에 대한 자세한 내용은 [정규화 및 ASIM(고급 SIEM 정보 모델)을](normalization.md)참조하세요.

> [!IMPORTANT]
> 이 문서에서는 ASIM을 사용할 수 있기 전에 버전 0.1이 릴리스되었고 여러 곳에서 ASIM과 일치하지 않는 네트워크 정규화 [스키마의 버전 0.2.x에](normalization-schema-v1.md) 대해 설명합니다. 자세한 내용은 [네트워크 정규화 스키마 버전 간의 차이점을 참조하세요.](normalization-schema-v1.md#changes) 
>

> [!IMPORTANT]
> 네트워크 정규화 스키마는 현재 미리 보기로 제공됩니다. 해당 기능은 별도의 서비스 수준 계약 없이 제공되며, 프로덕션 작업에는 사용하지 않는 것이 좋습니다.
>
> [Azure Preview 추가 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)에는 베타, 미리 보기 또는 아직 일반 공급으로 릴리스되지 않은 Azure 기능에 적용되는 추가 법률 용어가 포함되어 있습니다.
>

## <a name="schema-overview"></a>스키마 개요

네트워크 정규화 스키마는 모든 유형의 IP 네트워크 세션을 나타낼 수 있지만, Netflow, 방화벽 및 침입 방지 시스템과 같은 일반적인 원본 유형을 지원하도록 특별히 설계되었습니다.

## <a name="parsers"></a>파서

### <a name="source-agnostic-parsers"></a>원본 중립적 파서

모든 첫 번째 파서가 통합되는 소스에 구애받지 않는 파서 를 사용하고 구성된 모든 원본에서 분석이 실행되도록 하려면 다음 KQL 함수를 쿼리의 테이블 이름으로 사용합니다.

| 이름 | 설명 | 사용 지침 |
| ---- | --- | --- |
| <a name="imnetworksession"></a>**imNetworkSession** | 합집합을 사용하여 모든  *네트워크 세션* 원본의 정규화된 이벤트를 포함하는 집계 파서입니다. |- 원본 독립적 분석에서 원본을 추가하거나 제거하려면 이 파서를 업데이트합니다. <br><br>- 원본 독립적 쿼리에서 이 함수를 사용합니다.|
| **ASimNetworkSession** | [imNetworkSession](#imnetworksession) 함수와 비슷하지만 매개 변수가 지원되지 않으므로 **로그** 페이지 시간 선택기가 값을 사용하도록 강제하지 `custom` 않습니다. |- 원본과 관계없는 분석에서 원본을 추가하거나 제거하려는 경우 이러한 파서 업데이트<br><br>- 매개 변수를 사용할 계획이 없는 경우 소스에 구애받지 않는 쿼리에서 이 함수를 사용합니다.|
| **vimNetworkSession\<vendor\>\<product\>** | 원본별 파서가 특정 원본에 대한 정규화를 구현합니다. <br><br>예: `vimNetworkSessionSysmonLinux` |- 기본으로 사용할 수 있는 정규화 파서가 없는 경우 원본에 대한 원본별 파서 추가 새 `im` 파서에 대한 참조를 포함하도록 집계 파서를 업데이트합니다. <br><br>- 구문 분석 및 정규화 문제를 해결하기 위해 원본 관련 파서를 업데이트합니다.<br><br>- 원본 관련 분석에 원본 관련 파서를 사용합니다.|
 **ASimNetworkSession\<vendor\>\<product\>>** | 원본별 파서가 특정 원본에 대한 정규화를 구현합니다. <br><br>함수와 달리 `vim*` `ASim*` 함수는 매개 변수를 지원하지 않습니다. |- 기본으로 사용할 수 있는 정규화 파서가 없는 경우 원본에 대한 원본별 파서 추가 `ASim`새 파서에 대한 참조를 포함하도록 집계 파서를 업데이트합니다.<br><br>- 구문 분석 및 정규화 문제를 해결하기 위해 원본 관련 파서를 업데이트합니다.<br><br>- 매개 변수를 사용하지 `ASim` 않는 경우 대화형 쿼리에 원본별 파서 사용|
| | | |


[Microsoft Sentinel GitHub 리포지토리에서](https://aka.ms/DeployASIM)ASIM 파서를 배포합니다.

### <a name="out-of-the-box-source-specific-parsers"></a>소스별 파서가 가장 중요합니다.

Microsoft Sentinel은 다음과 같은 기본 제공 제품별 네트워크 세션 파서를 제공합니다.

| **이름** | **설명** |
| --- | --- |
| **엔드포인트용 Microsoft 365 Defender** | - 매개변수: vimNetworkSessionMicrosoft365Defender <br> - 일반: ASimNetworkSessionMicrosoft365Defender | 
| **Microsoft Defender for IoT - 엔드포인트(MD4IoT)** | - 매개변수: vimNetworkSessionMD4IoT <br> - 일반: ASimNetworkSessionMD4IoT  |
| **Linux용 Microsoft Sysmon** | - 매개변수: vimNetworkSessionSysmonLinux<br> - 일반: ASimNetworkSessionSysmonLinux  |
| **Windows 이벤트 방화벽** | Windows 이벤트 515x를 사용하여 수집된 방화벽 활동을 Windows Log Analytics 에이전트 또는 Azure Monitor 에이전트를 사용하여 Event 또는 WindowsEvent 테이블에 수집됩니다.<br><br> - 매개변수: vimNetworkSessionMicrosoftWindowsEventFirewall <br> - 일반: ASimNetworkSessionMicrosoftWindowsEventFirewall
| | |


### <a name="add-your-own-normalized-parsers"></a>사용자 고유의 정규화된 파서 추가

네트워크 세션 정보 모델에 대한 사용자 지정 파서 구현 시 다음 구문을 사용하여 KQL 함수의 이름을 지정합니다.

- `vimNetworkSession<vendor><Product>` 매개변수 파서의 경우
- `ASimNetworkSession<vendor><Product>` 일반 파서의 경우

그런 다음, 또는 각각에 새 파서 를 `imNetworkSession` `ASimNetworkSession` 추가합니다.

### <a name="filtering-parser-parameters"></a>파서 매개 변수 필터링

`im`및 `vim*` 파서가 [매개 변수 필터링을 지원합니다.](normalization-about-parsers.md#optimized-parsers) 이러한 파서가 선택 사항이지만 쿼리 성능을 향상시킬 수 있습니다.

사용할 수 있는 필터링 매개 변수는 다음과 같습니다.

| 이름     | 유형      | Description |
|----------|-----------|-------------|
| **Starttime** | Datetime | 이 시간 이후에 **시작된** 네트워크 세션만 필터링합니다. |
| **Endtime** | Datetime | 이 시간 또는 그 이전에 **실행되기 시작한** 네트워크 세션만 필터링합니다. |
| **srcipaddr_has_any_prefix** | 동적 | [원본 IP 주소 필드](#srcipaddr) 접두사를 나열된 값 중 하나에 있는 네트워크 세션만 필터링합니다. |
| **dstipaddr_has_any_prefix** | 동적 | [나열된](#dstipaddr) 값 중 하나에 대상 IP 주소 필드 접두사를 포함하는 네트워크 세션만 필터링합니다. |
| **dstportnum** | int | 지정된 대상 포트 번호를 사용하여 네트워크 세션만 필터링합니다. |
| **hostname_has_any** | 동적 | [대상 호스트](#dsthostname) 이름 필드에 나열된 값이 있는 네트워크 세션만 필터링합니다. |
| **dvcaction** | 동적 | [디바이스 작업 필드가](#dvcaction) 나열된 값 중 어느 것이든지 네트워크 세션만 필터링합니다. | 
| **eventresult** | 문자열 | 특정 **EventResult** 값을 사용하여 네트워크 세션만 필터링합니다. |
| | | |

예를 들어 지정된 도메인 이름 목록에 대한 웹 세션만 필터링하려면 다음을 사용합니다.

```kql
let torProxies=dynamic(["tor2web.org", "tor2web.com", "torlink.co",...]);
imNetworkSession (hostname_has_any = torProxies)
```

## <a name="schema-details"></a>스키마 세부 정보

네트워크 세션 정보 모델은 [OSSEM 네트워크 엔터티 스키마](https://github.com/OTRF/OSSEM/blob/master/docs/cdm/entities/network.md)입니다.

업계 모범 사례를 준수하기 위해 네트워크 세션 스키마는 설명자 **Src** 및 **Dst를** 사용하여 필드 이름에 토큰 **Dvc를** 포함하지 않고 네트워크 세션 원본 및 대상 디바이스를 식별합니다.

따라서 예를 들어 원본 장치 호스트 이름 및 IP 주소는 각각 **SrcHostname** 및 **Srcipaddr** 로 이름이 지정 되며 **src *Dvc* 호스트 이름** 및 **src *dvc* IpAddr** 가 아닙니다. **Dvc** 접두사는 보고 또는 중간 장치에만 사용 됩니다 (해당 하는 경우).

원본 및 대상 장치와 연결 된 사용자 및 응용 프로그램을 설명 하는 필드도 **Src** 및 **Dst** 설명자를 사용 합니다.

다른 ASIM 스키마는 일반적으로 **Dst** 대신 **대상을** 사용 합니다.


### <a name="common-fields"></a>공용 필드

모든 스키마에 공통 된 필드는 [Asim 스키마 개요](normalization-about-schemas.md#common)에 설명 되어 있습니다. 다음 필드에는 이벤트를 처리 하기 위한 특정 지침이 있습니다.

| 필드               | 클래스       | 형식       |  Description        |
|---------------------|-------------|------------|--------------------|
| **EventCount** | 필수 | 정수 | Netflow 원본은 집계를 지원 하며 **Eventcount** 필드는 netflow **흐름** 필드의 값으로 설정 되어야 합니다. 다른 원본의 경우 값은 일반적으로로 설정 됩니다 `1` . |
| **EventType** | 필수 | Enumerated | 레코드에서 보고하는 작업을 설명합니다.<br><br> 네트워크 세션 레코드의 경우 지원 되는 값은 다음과 같습니다.<br>- `NetworkConnection`<br>- `NetworkSession` |
| **EventSubType** | 선택 | 문자열 | 이벤트 유형에 대 한 추가 설명입니다 (해당 하는 경우). <br> 네트워크 세션 레코드의 경우 지원 되는 값은 다음과 같습니다.<br>- `Start`<br>- `End` |
| **EventSchema** | 필수 | String | 여기에 설명 된 스키마 이름은 `NetworkSession` 입니다. |
| **EventSchemaVersion**  | 필수   | String     | 스키마의 버전입니다. 여기에 설명된 스키마의 버전은 `0.2.1`입니다.         |
| <a name="dvcaction"></a>**DvcAction** | 선택 사항 | Enumerated | 네트워크 세션에서 수행 되는 작업입니다. 지원되는 값은 다음과 같습니다.<br>- `Allow`<br>- `Deny`<br>- `Drop`<br>- `Drop ICMP`<br>- `Reset`<br>- `Reset Source`<br>- `Reset Destination`<br>- `Encrypt`<br>- `Decrypt`<br>- `VPNroute`<br><br>**참고**: 값은 이러한 값으로 정규화되어야 하는 다른 조건을 사용하여 원본 레코드에 제공될 수 있습니다. 원래 값은 [Dvcoriginalaction](normalization-about-schemas.md#dvcoriginalaction) 필드에 저장 해야 합니다.<br><br>예: `drop` |
| **Dvc** 필드|        |      | 네트워크 세션 이벤트의 경우 장치 필드는 네트워크 세션 이벤트를 보고 하는 시스템을 참조 합니다.  |
| | | | |

### <a name="network-session-fields"></a>네트워크 세션 필드

다음 필드는 모든 네트워크 세션 활동 로깅에 공통적으로 수행 됩니다.

| 필드 | 클래스 | 형식 | Description |
|-------|-------|------|-------------|
| <a name="dst"></a>**대상** | 권장       | String     |    DNS 요청을 받는 서버의 고유 식별자입니다. <br><br>이 필드는 [Dstdvcid](#dstdvcid), [DstHostname](#dsthostname)또는 [DstIpAddr](#dstipaddr) 필드에 별칭을 할 수 있습니다. <br><br>예: `192.168.12.1`       |
|<a name="dstipaddr"></a> **DstIpAddr** | 권장 | IP 주소 | 연결 또는 세션 대상의 IP 주소입니다. <br><br>예: `2001:db8::ff00:42:8329`<br><br>**참고**: [DstHostname](#dsthostname) 가 지정 된 경우이 값은 필수입니다. |
| <a name="dstportnumber"></a>**DstPortNumber** | 선택 | 정수 | 대상 IP 포트<br><br>예: `443` |
| <a name="dsthostname"></a>**DstHostname** | 권장 | String | 대상 장치 호스트 이름 (도메인 정보 제외) 사용할 수 있는 장치 이름이 없으면이 필드에 관련 IP 주소를 저장 합니다.<br><br>예: `DESKTOP-1282V4D`<br><br>**참고**: [DstIpAddr](#dstipaddr) 가 지정 된 경우이 값은 필수입니다. |
| <a name="hostname"></a>**N** | Alias | | [DstHostname](#dsthostname) 에 대 한 별칭 |
| <a name="dstdomain"></a>**DstDomain** | 권장 | String | 대상 장치의 도메인입니다.<br><br>예: `Contoso` |
| <a name="dstdomaintype"></a>**DstDomainType** | 권장 | Enumerated | [Dstdomain](#dstdomain)의 유형 (알려진 경우)입니다. 가능한 값은 다음과 같습니다.<br>- `Windows (contoso\mypc)`<br>- `FQDN (docs.microsoft.com)`<br><br>[Dstdomain](#dstdomain) 을 사용 하는 경우 필수 사항입니다. |
| **DstFQDN** | 선택 | 문자열 | 사용 가능한 경우 도메인 정보를 포함 하는 대상 장치 호스트 이름 <br><br>예: `Contoso\DESKTOP-1282V4D` <br><br>**참고**:이 필드는 기존의 FQDN 형식 및 Windows domain\hostname 형식을 모두 지원 합니다. [DstDomainType](#dstdomaintype) 는 사용 되는 형식을 반영 합니다.   |
| <a name="dstdvcid"></a>**DstDvcId** | 선택 | 문자열 | 레코드에 보고 된 대상 장치의 ID입니다.<br><br>예: `ac7e9755-8eae-4ffc-8a02-50ed7a2216c3` |
| **DstDvcIdType** | 선택 사항 | Enumerated | [Dstdvcid](#dstdvcid)의 유형입니다 (알려진 경우). 가능한 값은 다음과 같습니다.<br> - `AzureResourceId`<br>- `MDEidIf`<br><br>여러 Id를 사용할 수 있는 경우 위의 목록에서 첫 번째 Id를 사용 하 고  **DstDvcAzureResourceId** 또는 **Dstdvcmdeid** 필드에 각각 다른 id를 저장 합니다.<br><br>**Dstdeviceid** 를 사용 하는 경우 필수 사항입니다.|
| **DstDeviceType** | 선택 사항 | Enumerated | 대상 장치의 유형입니다. 가능한 값은 다음과 같습니다.<br>- `Computer`<br>- `Mobile Device`<br>- `IOT Device`<br>- `Other` |
| <a name="dstuserid"></a>**DstUserId** | 선택 | 문자열 | 대상 사용자의 컴퓨터에서 읽을 수 있는 영숫자 고유 표현입니다. <br><br>지원 되는 형식 및 유형은 다음과 같습니다.<br>- **SID** (Windows):`S-1-5-21-1377283216-344919071-3415362939-500`<br>- **UID**  (Linux): `4578`<br>-  **AADID** (Azure Active Directory):`9267d02c-5f76-40a9-a9eb-b686f3ca47aa`<br>-  **OktaId**: `00urjk4znu3BcncfY0h7`<br>-  **AWSId**: `72643944673`<br><br>[Dstuseridtype](#dstuseridtype) 필드에 ID 유형을 저장 합니다. 다른 Id를 사용할 수 있는 경우 필드 이름을 **Dstusersid**, **dstuseruid**, **DstUserAADID**, **Dstuseroktaid** 및 **userawsid** 로 각각 정규화 하는 것이 좋습니다. 자세한 내용은 [사용자 엔터티](normalization-about-schemas.md#the-user-entity)를 참조하세요.<br><br>예: `S-1-12` |
| <a name="dstuseridtype"></a>**DstUserIdType** | 선택 사항 | Enumerated | [Dstuserid](#dstuserid) 필드에 저장 된 ID의 유형입니다. <br><br>지원 되는 값은 `SID` ,, `UIS` `AADID` , `OktaId` 및 `AWSId` 입니다. |
| <a name="dstusername"></a>**DstUsername** | 선택 | 문자열 | 사용 가능한 경우 도메인 정보를 포함 하는 대상 사용자 이름입니다. <br><br>다음 형식 중 하나를 사용 하 고 우선 순위를 지정 합니다.<br>- **Upn/메일**: `johndow@contoso.com`<br>- **Windows**: `Contoso\johndow`<br>- **DN**: `CN=Jeff Smith,OU=Sales,DC=Fabrikam,DC=COM`<br>- **단순**: `johndow`. 도메인 정보를 사용할 수 없는 경우에만 간단한 양식을 사용 합니다.<br><br>[DstUsernameType](#dstusernametype) 필드에 사용자 이름 유형을 저장 합니다. 다른 Id를 사용할 수 있는 경우 필드 이름을 **Dstuserupn**, **dstuserupn** 및 **DstUserDn** 으로 정규화 하는 것이 좋습니다. 자세한 내용은 [사용자 엔터티](normalization-about-schemas.md#the-user-entity)를 참조 하세요.<br><br>예: `AlbertE` |
| <a name="user"></a>**정의** | Alias | | [Dstusername](#dstusername) 에 대 한 별칭 |
| <a name="dstusernametype"></a>**DstUsernameType** | 선택 사항 | Enumerated | [Dstusername](#dstusername) 필드에 저장 된 사용자 이름 유형을 지정 합니다. 지원 되는 값은 `UPN` ,, `Windows` `DN` 및 `Simple` 입니다. 자세한 내용은 [사용자 엔터티](normalization-about-schemas.md#the-user-entity)를 참조하세요.<br><br>예: `Windows` |
| **DstUserType** | 선택 사항 | Enumerated | Actor의 형식입니다. 지원되는 값은 다음과 같습니다.<br>- `Regular`<br>- `Machine`<br>- `Admin`<br>- `System`<br>- `Application`<br>- `Service Principal`<br>- `Other`<br><br>**참고**: 값은 이러한 값으로 정규화되어야 하는 다른 조건을 사용하여 원본 레코드에 제공될 수 있습니다. [DstOriginalUserType](#dstoriginalusertype) 필드에 원래 값을 저장 합니다. |
| <a name="dstoriginalusertype"></a>**DstOriginalUserType** | 선택 | 문자열 | 원본에서 제공 하는 경우 원래 대상 사용자 형식입니다. |
| **DstUserDomain** | 선택 | 문자열 | 이 필드는 이전 버전과의 호환성을 위해서만 유지 됩니다. ASIM을 사용 하려면 사용 가능한 경우 도메인 정보 (사용 가능한 경우)를 [Dstusername](#dstusername) 필드에 포함 해야 합니다. |
| <a name="dstappname"></a>**DstAppName** | 선택 | 문자열 | 대상 응용 프로그램의 이름입니다.<br><br>예: `Facebook` |
| <a name="dstappid"></a>**DstAppId** | 선택 | 문자열 | 보고 장치에서 보고 하는 대상 응용 프로그램의 ID입니다.<br><br>예: `124` |
| **DstAppType** | 선택 | 문자열 | 작업자를 대신하여 권한을 부여하는 애플리케이션의 형식입니다. 지원되는 값은 다음과 같습니다.<br>- `Process`<br>- `Service`<br>- `Resource`<br>- `URL`<br>- `SaaS application`<br>- `Other`<br><br>이 필드는 [Dstappname](#dstappname) 또는 [dstappname](#dstappid) 를 사용 하는 경우 필수입니다. |
| **DstZone** | 선택 | 문자열 | 보고 디바이스에 정의된 대상의 네트워크 영역<br><br>예: `Dmz` |
| **DstInterfaceName** | 선택 | 문자열 | 대상 디바이스의 연결 또는 세션에 사용되는 네트워크 인터페이스<br><br>예: `Microsoft Hyper-V Network Adapter` |
| **DstInterfaceGuid** | 선택 | 문자열 | 대상 디바이스에서 사용되는 네트워크 인터페이스의 GUID입니다.<br><br>예:<br>`46ad544b-eaf0-47ef-`<br>`827c-266030f545a6` |
| **DstMacAddr** | 선택 | 문자열 | 대상 디바이스의 연결 또는 세션에 사용되는 네트워크 인터페이스의 MAC 주소입니다.<br><br>예: `06:10:9f:eb:8f:14` |
| <a name="dstvlanid"></a>**DstVlanId** | 선택 | 문자열 | 대상 디바이스와 관련된 VLAN ID입니다.<br><br>예제: `130` |
| **OuterVlanId** | 선택 사항 | Alias | [DstVlanId](#dstvlanid)에 대한 별칭입니다. <br><br>대부분의 경우 VLAN은 원본 또는 대상으로 확인할 수 없지만 내부 또는 외부로 특징지어지게 됩니다. 이 별칭은 VLAN이 외부로 특성화될 때 [DstVlanId를](#dstvlanid) 사용해야 임을 의미합니다. |
| **DstGeoCountry** | 선택 사항 | 국가 | 대상 IP 주소와 연결된 국가입니다. 자세한 내용은 [논리적 형식](normalization-about-schemas.md#logical-types)을 참조하세요.<br><br>예: `USA` |
| **DstGeoRegion** | 선택 사항 | 지역 | 대상 IP 주소와 연결된 국가 내의 지역 또는 주입니다. 자세한 내용은 [논리적 형식](normalization-about-schemas.md#logical-types)을 참조하세요.<br><br>예: `Vermont` |
| **DstGeoCity** | 선택 사항 | City | 대상 IP 주소와 연결된 도시입니다. 자세한 내용은 [논리적 형식](normalization-about-schemas.md#logical-types)을 참조하세요.<br><br>예: `Burlington` |
| **DstGeoLatitude** | 선택 사항 | 위도 | 대상 IP 주소와 연결된 지리적 좌표의 위도입니다. 자세한 내용은 [논리적 형식](normalization-about-schemas.md#logical-types)을 참조하세요.<br><br>예: `44.475833` |
| **DstGeoLongitude** | 선택 사항 | 경도 | 대상 IP 주소와 연결된 지리적 좌표의 경도입니다. 자세한 내용은 [논리적 형식](normalization-about-schemas.md#logical-types)을 참조하세요.<br><br>예: `73.211944` |
| <a name="src"></a>**Src** | 권장       | String     |    원본 디바이스의 고유 식별자입니다. <br><br>이 필드는 [SrcDvcId,](#srcdvcid) [SrcHostname](#srchostname)또는 [SrcIpAddr](#srcipaddr) 필드의 별칭을 나타낼 수 있습니다. <br><br>예: `192.168.12.1`       |
| <a name="srcipaddr"></a>**SrcIpAddr** | 권장 | IP 주소 | 연결 또는 세션이 시작된 IP 주소 **SrcHostname이** 지정된 경우 이 값은 필수입니다.<br><br>예: `77.138.103.108` |
| <a name="ipaddr"></a>**IpAddr** | Alias | | [SrcIpAddr에](#srcipaddr) 대한 별칭 |
| **SrcPortNumber** | 선택 | 정수 | 연결이 시작된 IP 포트. 여러 연결을 구성하는 세션과 관련이 없을 수 있습니다.<br><br>예: `2335` |
| <a name="srchostname"></a> **SrcHostname** | 권장 | String | 도메인 정보를 제외한 원본 디바이스 호스트 이름입니다. 사용할 수 있는 디바이스 이름이 없는 경우 이 필드에 관련 IP 주소를 저장합니다. [SrcIpAddr가](#srcipaddr) 지정된 경우 이 값은 필수입니다.<br><br>예: `DESKTOP-1282V4D` |
|<a name="srcdomain"></a>**SrcDomain** | 권장 | String | 원본 디바이스의 도메인입니다.<br><br>예: `Contoso` |
| <a name="srcdomaintype"></a>**SrcDomainType** | 권장 | Enumerated | 알려진 경우 [SrcDomain 의 형식입니다.](#srcdomain) 가능한 값은 다음과 같습니다.<br>- `Windows` (예: `contoso` )<br>- `FQDN` (예: `microsoft.com` )<br><br>[SrcDomain을](#srcdomain) 사용하는 경우 필요합니다. |
| **SrcFQDN** | 선택 | 문자열 | 사용 가능한 경우 도메인 정보를 포함한 원본 디바이스 호스트 이름입니다. <br><br>**참고:** 이 필드는 기존 FQDN 형식과 Windows domain\hostname 형식을 모두 지원합니다. [SrcDomainType](#srcdomaintype) 필드는 사용된 형식을 반영합니다. <br><br>예: `Contoso\DESKTOP-1282V4D` |
| <a name="srcdvcid"></a>**SrcDvcId** | 선택 | 문자열 | 레코드에 보고된 원본 디바이스의 ID입니다.<br><br>예: `ac7e9755-8eae-4ffc-8a02-50ed7a2216c3` |
| **SrcDvcIdType** | 선택 사항 | Enumerated | [Srcdvcid](#srcdvcid)의 유형입니다 (알려진 경우). 가능한 값은 다음과 같습니다.<br> - `AzureResourceId`<br>- `MDEid`<br><br>여러 Id를 사용할 수 있는 경우 위의 목록에서 첫 번째 Id를 사용 하 고 다른 Id를 **SrcDvcAzureResourceId** 및 **Srcdvcmdeid** 에 각각 저장 합니다.<br><br>**참고**:이 필드는 [srcdvcid](#srcdvcid) 를 사용 하는 경우에 필요 합니다. |
| **SrcDeviceType** | 선택 사항 | Enumerated | 원본 디바이스의 형식입니다. 가능한 값은 다음과 같습니다.<br>- `Computer`<br>- `Mobile Device`<br>- `IOT Device`<br>- `Other` |
| <a name="srcuserid"></a>**SrcUserId** | 선택 | 문자열 | 원본 사용자의 컴퓨터에서 읽을 수 있는 영숫자 고유 표현입니다. 형식 및 지원되는 형식은 다음과 같습니다.<br>-  **SID** (Windows):`S-1-5-21-1377283216-344919071-3415362939-500`<br>-  **UID**  (Linux): `4578`<br>-  **AADID** (Azure Active Directory):`9267d02c-5f76-40a9-a9eb-b686f3ca47aa`<br>-  **OktaId**: `00urjk4znu3BcncfY0h7`<br>-  **AWSId**: `72643944673`<br><br>[Srcuseridtype](#srcuseridtype) 필드에 ID 유형을 저장 합니다. 다른 Id를 사용할 수 있는 경우 필드 이름을 SrcUserSid, SrcUserUid, SrcUserAadId, SrcUserOktaId 및 UserAwsId로 각각 정규화 하는 것이 좋습니다. 자세한 내용은 사용자 엔터티를 참조 하세요.<br><br>예: S-1-12 |
| <a name="srcuseridtype"></a>**SrcUserIdType** | 선택 사항 | Enumerated | [Srcuserid](#srcuserid) 필드에 저장 된 ID의 유형입니다. 지원 되는 값은 `SID` ,, `UIS` `AADID` , `OktaId` 및 `AWSId` 입니다. |
| <a name="srcusername"></a>**SrcUsername** | 선택 | 문자열 | 사용 가능한 경우 도메인 정보를 포함 하는 원본 사용자 이름입니다. 다음 형식 중 하나를 사용 하 고 우선 순위를 지정 합니다.<br>- **Upn/메일**: `johndow@contoso.com`<br>- **Windows**: `Contoso\johndow`<br>- **DN**: `CN=Jeff Smith,OU=Sales,DC=Fabrikam,DC=COM`<br>- **단순**: `johndow`. 도메인 정보를 사용할 수 없는 경우에만 간단한 양식을 사용 합니다.<br><br>[SrcUsernameType](#srcusernametype) 필드에 사용자 이름 유형을 저장 합니다. 다른 Id를 사용할 수 있는 경우 필드 이름을 **Srcuserupn**, **srcuserupn** 및 **SrcUserDn** 으로 정규화 하는 것이 좋습니다.<br><br>자세한 내용은 [사용자 엔터티](normalization-about-schemas.md#the-user-entity)를 참조하세요.<br><br>예: `AlbertE` |
| <a name="srcusernametype"></a>**SrcUsernameType** | 선택 사항 | Enumerated | [Srcusername](#srcusername) 필드에 저장 된 사용자 이름 유형을 지정 합니다. 지원 되는 값은 `UPN` ,, `Windows` `DN` 및 `Simple` 입니다. 자세한 내용은 [사용자 엔터티](normalization-about-schemas.md#the-user-entity)를 참조하세요.<br><br>예: `Windows` |
| **SrcUserType** | 선택 사항 | Enumerated | Actor의 형식입니다. 허용된 값은<br>- `Regular`<br>- `Machine`<br>- `Admin`<br>- `System`<br>- `Application`<br>- `Service Principal`<br>- `Other`<br><br>**참고**: 값은 이러한 값으로 정규화되어야 하는 다른 조건을 사용하여 원본 레코드에 제공될 수 있습니다. [SrcOriginalUserType](#srcoriginalusertype) 필드에 원래 값을 저장 합니다. |
| <a name="srcoriginalusertype"></a>**SrcOriginalUserType** | | | 원본에서 제공 하는 경우 원래 원본 사용자 형식입니다. |
| **SrcUserDomain** | 선택 | 문자열 | 이 필드는 이전 버전과의 호환성을 위해서만 유지 됩니다. ASIM을 사용 하려면 사용 가능한 경우 도메인 정보를 [Srcusername](#srcusername) 필드에 포함 해야 합니다. |
| <a name="srcappname"></a>**SrcAppName** | 선택 | 문자열 | 원본 응용 프로그램의 이름입니다. <br><br>예: `filezilla.exe` |
| <a name="srcappid"></a>**SrcAppId** | 선택 | 문자열 | 보고 장치에서 보고 하는 대상 응용 프로그램의 ID입니다.<br><br>예: `124` |
| **SrcAppType** | 선택 | 문자열 | 원본 응용 프로그램의 형식입니다. 지원되는 값은 다음과 같습니다.<br>- `Process`<br>- `Service`<br>- `Resource`<br>- `Other`<br><br>이 필드는 [Srcappname](#srcappname) 또는 [srcappname](#srcappid) 를 사용 하는 경우 필수입니다. |
| **SrcZone** | 선택 | 문자열 | 보고 디바이스에서 정의한 원본 네트워크 영역<br><br>예: `Internet` |
| **SrcIntefaceName** | 선택 | 문자열 | 원본 디바이스에서 연결 또는 세션에 사용하는 네트워크 인터페이스 <br><br>예: `eth01` |
| **SrcInterfaceGuid** | 선택 | 문자열 | 원본 장치에서 사용 되는 네트워크 인터페이스의 GUID입니다.<br><br>예:<br>`46ad544b-eaf0-47ef-`<br>`827c-266030f545a6` |
| **SrcMacAddr** | 선택 | 문자열 | 연결 또는 세션이 시작 된 네트워크 인터페이스의 MAC 주소입니다.<br><br>예: `06:10:9f:eb:8f:14` |
| <a name="srcvlanid"></a>**SrcVlanId** | 선택 | 문자열 | 원본 장치와 관련 된 VLAN ID입니다.<br><br>예제: `130` |
| **InnerVlanId** | 선택 사항 | Alias | [SrcVlanId](#srcvlanid)에 대 한 별칭입니다. <br><br>대부분의 경우 VLAN은 원본 또는 대상으로 확인할 수 없지만 내부 또는 외부로 특징을 지정할 수 있습니다. 이 별칭은 VLAN이 내부로 규정 될 때 [SrcVlanId](#srcvlanid) 를 사용 해야 함을 나타냅니다.    |
| **SrcGeoCountry** | 선택 사항 | 국가 | 원본 IP 주소와 연결 된 국가입니다.<br><br>예: `USA` |
| **SrcGeoRegion** | 선택 사항 | 지역 | 원본 IP 주소와 연결 된 국가 내의 지역입니다.<br><br>예: `Vermont` |
| **SrcGeoCity** | 선택 사항 | City | 원본 IP 주소와 연결 된 구/군/시입니다.<br><br>예: `Burlington` |
| **SrcGeoLatitude** | 선택 사항 | 위도 | 원본 IP 주소와 연결 된 지리적 좌표의 위도입니다.<br><br>예: `44.475833` |
| **SrcGeoLongitude** | 선택 사항 | 경도 | 원본 IP 주소와 연결 된 지리적 좌표의 경도입니다.<br><br>예: `73.211944` |
| **NetworkApplicationProtocol** | 선택 | 문자열 | 연결 또는 세션에서 사용하는 애플리케이션 계층 프로토콜입니다. [DstPortNumber](#dstportnumber) 값이 제공 되 면 **networkapplicationprotocol** 도 포함 하는 것이 좋습니다. 원본에서 값을 사용할 수 없는 경우 [DstPortNumber](#dstportnumber) 값에서 값을 파생 시킵니다.<br><br>예: `FTP` |
| **NetworkProtocol** | 선택 사항 | Enumerated | [IANA 프로토콜 할당](https://www.iana.org/assignments/protocol-numbers/protocol-numbers.xhtml)에 나열 된 대로 연결 또는 세션에서 사용 하는 IP 프로토콜입니다. 일반적으로 `TCP` , `UDP` 또는 `ICMP` 입니다.<br><br>예: `TCP` |
| **NetworkDirection** | 선택 사항 | Enumerated | 조직에 대 한 연결 또는 세션의 방향입니다. 지원 되는 값은 `Inbound` , `Outbound` , `Listen` 입니다. `Listen` 장치에서 네트워크 연결을 수락 하기 시작 했지만 실제로는 연결 되어 있지 않아도 됨을 나타냅니다.|
| <a name="networkduration"></a>**NetworkDuration** | 선택 | 정수 | 네트워크 세션 또는 연결을 완료 하는 데 걸린 시간 (밀리초)입니다.<br><br>예: `1500` |
| **기간** | Alias | | [Networkduration](#networkduration) 의 별칭 |
| **NetworkIcmpCode** | 선택 | 정수 | ICMP 메시지의 경우 [rfc 2780](https://datatracker.ietf.org/doc/html/rfc2780) 에서 IPv4 네트워크 연결 또는 IPv6 네트워크 연결에 대 한 [rfc 4443](https://datatracker.ietf.org/doc/html/rfc4443) 에 설명 된 대로 icmp 메시지 유형 숫자 값을 입력 합니다. [NetworkIcmpType](#networkicmptype) 값을 제공 하는 경우이 필드는 필수입니다. 원본에서 값을 사용할 수 없는 경우  [NetworkIcmpType](#networkicmptype) 필드에서 값을 파생 시킵니다.<br><br>예: `34` |
|<a name="networkicmptype"></a> **NetworkIcmpType** | 선택 | 문자열 | ICMP 메시지의 경우 [rfc 2780](https://datatracker.ietf.org/doc/html/rfc2780) for IPv4 network Connections 또는 IPv6 네트워크 연결용 [rfc 4443](https://datatracker.ietf.org/doc/html/rfc4443) 에 설명 된 대로 icmp 메시지 유형 텍스트를 표시 합니다.<br><br>예: `Destination Unreachable` |
| NetworkConnectionHistory | 선택 | 문자열 | TCP 플래그 및 기타 잠재적 IP 헤더 정보입니다. |
| **DstBytes** | 권장 | 정수 | 연결 또는 세션의 대상에서 원본으로 보낸 바이트 수 이벤트가 집계 되는 경우 **Dstbytes** 는 집계 된 모든 세션에 대 한 합계 여야 합니다.<br><br>예: `32455` |
| **SrcBytes** | 권장 | 정수 | 연결 또는 세션의 원본에서 대상으로 보낸 바이트 수 이벤트가 집계 된 경우 **Srcbytes** 는 집계 된 모든 세션에 대 한 합계 여야 합니다.<br><br>예: `46536` |
| **NetworkBytes** | 선택 | 정수 | 양방향으로 전송된 바이트 수. **Bytesreceived** 와 **BytesSent** 가 모두 있으면 **BytesTotal** 는 해당 합계와 같아야 합니다. 이벤트가 집계 된 경우 **Networkbytes** 는 집계 된 모든 세션에 대 한 합계 여야 합니다.<br><br>예: `78991` |
| **DstPackets** | 선택 | 정수 | 연결 또는 세션의 대상에서 원본으로 전송된 패킷 수입니다. 패킷의 의미는 보고 디바이스에 의해 정의됩니다. 이벤트가 집계되는 경우 **DstPackets는** 집계된 모든 세션의 합계여야 합니다.<br><br>예: `446` |
| **SrcPackets** | 선택 | 정수 | 연결 또는 세션 원본에서 대상으로 보낸 패킷 수입니다. 패킷의 의미는 보고 디바이스에 의해 정의됩니다. 이벤트가 집계되는 경우 **SrcPackets는** 집계된 모든 세션의 합계여야 합니다.<br><br>예: `6478` |
| **NetworkPackets** | 선택 | 정수 | 양방향으로 전송된 패킷 수입니다. **PacketsReceived와** **PacketsSent가** 모두 있는 경우 **BytesTotal은** 해당 합계와 같아야 합니다. 패킷의 의미는 보고 디바이스에 의해 정의됩니다. 이벤트가 집계되는 경우 **NetworkPackets는** 집계된 모든 세션의 합계여야 합니다.<br><br>예: `6924` |
|<a name="networksessionid"></a>**NetworkSessionId** | 선택 | 문자열 | 보고 디바이스에서 보고한 세션 식별자 <br><br>예: `172\_12\_53\_32\_4322\_\_123\_64\_207\_1\_80` |
| **SessionId** | Alias | String | [NetworkSessionId에](#networksessionid) 대한 별칭 |
| | | | |

### <a name="intermediary-device-fields"></a><a name="Intermediary"></a>중간 디바이스 필드

다음 필드는 네트워크 세션을 릴레이하는 방화벽 또는 프록시와 같은 중간 디바이스에 대한 정보가 레코드에 포함된 경우에 유용합니다.

| 필드 | 클래스 | 형식 | Description |
| --- | --- | --- | --- |
| **DstNatIpAddr** | 선택 사항 | IP 주소 | 중간 NAT 디바이스에서 보고하는 경우 NAT 디바이스에서 원본과의 통신에 사용하는 IP 주소입니다.<br><br>예: `2::1` |
| **DstNatPortNumber** | 선택 | 정수 | 중간 NAT 디바이스에서 보고하는 경우 NAT 디바이스에서 원본과의 통신에 사용하는 포트입니다.<br><br>예: `443` |
| **SrcNatIpAddr** | 선택 사항 | IP 주소 | 중간 NAT 디바이스에서 보고하는 경우 NAT 디바이스에서 대상과의 통신에 사용하는 IP 주소입니다.<br><br>예: `4.3.2.1` |
| **SrcNatPortNumber** | 선택 | 정수 | 중간 NAT 디바이스에서 보고하는 경우 NAT 디바이스에서 대상과의 통신에 사용하는 포트입니다.<br><br>예: `345` |
| **DvcInboundInterface** | 선택 | 문자열 | 중간 디바이스에서 보고하는 경우 NAT 디바이스에서 원본 디바이스에 연결하기 위해 사용하는 네트워크 인터페이스입니다.<br><br>예: `eth0` |
| **DvcOutboundInterface** | 선택 | 문자열 | 중간 디바이스에서 보고하는 경우 NAT 디바이스에서 대상 디바이스에 연결하기 위해 사용하는 네트워크 인터페이스입니다.<br><br>예: `Ethernet adapter Ethernet 4e` |
| | | | |


### <a name="inspection-fields"></a><a name="inspection-fields"></a>검사 필드

다음 필드는 방화벽, IPS 또는 웹 보안 게이트웨이와 같은 보안 디바이스가 수행한 검사를 나타내는 데 사용됩니다.

| 필드 | 클래스 | 형식 | Description |
| --- | --- | --- | --- |
| **NetworkRuleName** | 선택 | 문자열 | [DvcAction이](#dvcaction) 결정된 규칙의 이름 또는 ID입니다.<br><br> 예: `AnyAnyDrop` |
| **NetworkRuleNumber** | 선택 | 정수 | [DvcAction이](#dvcaction) 결정된 규칙의 수입니다.<br><br>예: `23` |
| **규칙** | 필수 | String | `NetworkRuleName` 또는 `NetworkRuleNumber` |
| **ThreatId** | 선택 | 문자열 | 네트워크 세션에서 식별된 위협 또는 맬웨어의 ID입니다.<br><br>예: `Tr.124` |
| **ThreatName** | 선택 | 문자열 | 네트워크 세션에서 식별된 위협 또는 맬웨어의 이름입니다.<br><br>예: `EICAR Test File` |
| **ThreatCategory** | 선택 | 문자열 | 네트워크 세션에서 식별된 위협 또는 맬웨어의 범주입니다.<br><br>예: `Trojan` |
| **ThreatRiskLevel** | 선택 | 정수 | 세션과 연결된 위험 수준입니다. 수준은 **0에서 100** 사이의 숫자여야 합니다.<br><br>**참고:** 값은 이 크기 조정으로 정규화되어야 하는 다른 배율로 원본 레코드에 제공될 수 있습니다. 원래 값은 [ThreatRiskLevelOriginal](#threatriskleveloriginal)에 저장되어야 합니다. |
| <a name="threatriskleveloriginal"></a>**ThreatRiskLevelOriginal** | 선택 | 문자열 | 보고 디바이스에서 보고한 위험 수준입니다. |
| | | | |

### <a name="other-fields"></a>기타 필드

이벤트가 네트워크 세션의 엔드포인트 중 하나에서 보고되는 경우 세션을 시작하거나 종료한 프로세스에 대한 정보가 포함될 수 있습니다. 이러한 경우 [ASIM 프로세스 이벤트 스키마를](process-events-normalization-schema.md) 사용하여 이 정보를 정규화합니다.

### <a name="schema-updates"></a>스키마 업데이트

다음은 스키마 버전 0.2.1의 변경 내용입니다.
- `Src`원본 `Dst` 및 대상 시스템의 선행 식별자로 및 를 별칭으로 추가했습니다.
- `NetworkConnectionHistory`, , `SrcVlanId` , `DstVlanId` `InnerVlanId` 및 필드가 추가되었습니다.`OuterVlanId` 
## <a name="next-steps"></a>다음 단계

자세한 내용은 다음을 참조하세요.

- [Microsoft Sentinel의 정규화](normalization.md)
- [고급 SIEM 정보 모델 스키마](normalization-about-schemas.md)
- [고급 SIEM 정보 모델 파서](normalization-about-parsers.md)
- [고급 SIEM 정보 모델 콘텐츠](normalization-content.md)

