---
title: Microsoft Sentinel DNS 정규화 스키마 참조 | Microsoft Docs
description: 이 문서에서는 Microsoft Sentinel DNS 정규화 스키마에 대해 설명합니다.
author: batamig
ms.topic: reference
ms.date: 11/09/2021
ms.author: bagol
ms.custom: ignite-fall-2021
ms.openlocfilehash: 084fcf5f14857e3a14c019adb53cafbe856a8982
ms.sourcegitcommit: 66b6e640e2a294a7fbbdb3309b4829df526d863d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/01/2021
ms.locfileid: "133368237"
---
# <a name="microsoft-sentinel-dns-normalization-schema-reference-public-preview"></a>Microsoft Sentinel DNS 정규화 스키마 참조(공개 미리 보기)

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

DNS 정보 모델은 DNS 서버 또는 DNS 보안 시스템에서 보고하는 이벤트를 설명하는 데 사용되며, Microsoft Sentinel에서 소스와 관계 없는 분석을 사용하도록 설정하는 데 사용됩니다.

자세한 내용은 [정규화 및 ASIM(고급 SIEM 정보 모델)을](normalization.md)참조하세요.

> [!IMPORTANT]
> DNS 정규화 스키마는 현재 미리 보기 상태입니다. 해당 기능은 별도의 서비스 수준 계약 없이 제공되며, 프로덕션 작업에는 사용하지 않는 것이 좋습니다.
>
> [Azure Preview 추가 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)에는 베타, 미리 보기 또는 아직 일반 공급으로 릴리스되지 않은 Azure 기능에 적용되는 추가 법률 용어가 포함되어 있습니다.
>

## <a name="schema-overview"></a>스키마 개요

ASIM DNS 스키마는 DNS 서버 또는 DNS 서버에 DNS 요청을 보내는 디바이스에 의해 기록될 수 있는 DNS 프로토콜 활동을 나타냅니다. DNS 프로토콜 활동에는 DNS 쿼리, DNS 서버 업데이트 및 DNS 대량 데이터 전송이 포함됩니다. 스키마는 프로토콜 활동을 나타내므로 RFC 및 공식적으로 할당된 매개 변수 목록에 의해 제어되며, 해당하는 경우 이 문서에서 참조됩니다. DNS 스키마는 DNS 서버 감사 이벤트를 나타내지 않습니다. 

DNS 서버에서 보고하는 가장 중요한 활동은 필드가 로 설정된 DNS `EventType` `Query` 쿼리입니다.   

DNS 이벤트에서 가장 중요한 필드는 다음과 같습니다.

- [DnsQuery](#query)- 쿼리가 발급된 도메인 이름을 보고합니다.
- 요청이 생성된 IP 주소를 나타내는 [SrcIpAddr(IpAddr에](#ipaddr)별칭 지정)입니다. [](#srcipaddr) 
- [EventResultDetails](#eventresultdetails)- 요청이 성공했는지 여부와 그렇지 않은 경우 그 이유를 보고합니다.
- 사용 가능한 경우 [DnsResponseName](#responsename)- 서버에서 쿼리에 제공한 답변을 보유합니다. ASIM은 응답을 구문 분석할 필요가 없으며 해당 형식은 원본마다 다릅니다. 소스에 구애받지 않은 콘텐츠에서 이 필드를 사용하려면 `has` 또는 연산자를 사용하여 콘텐츠를 `contains` 검색합니다.

클라이언트 디바이스에서 수집된 DNS [이벤트에는 사용자](#user) 및 [프로세스](#process) 정보도 포함될 수 있습니다. 

## <a name="guidelines-for-collecting-dns-events"></a>DNS 이벤트 수집에 대한 지침

DNS는 다수의 컴퓨터를 교차할 수 있는 고유한 프로토콜입니다. 또한 DNS는 UDP를 사용하기 때문에 요청과 응답이 분리되며 서로 직접 관련되지 않습니다.

다음 이미지는 네 개의 세그먼트를 포함하여 간소화된 DNS 요청 흐름을 보여 줍니다. 실제 요청은 더 많은 세그먼트가 포함되어 더 복잡할 수 있습니다.

:::image type="content" source="media/normalization/dns-request-flow.png" alt-text="간소화된 DNS 요청 흐름.":::

요청 및 응답 세그먼트는 DNS 요청 흐름에서 서로 직접 연결되지 않으므로 전체 로깅은 상당한 중복을 초래할 수 있습니다.

로그할 가장 중요한 세그먼트는 클라이언트에 대한 응답으로 도메인 이름 쿼리, 조회 결과 및 클라이언트의 IP 주소를 제공합니다. 많은 DNS 시스템에서 이 세그먼트만 기록하지만 다른 파트를 로그하는 값이 있습니다. 예를 들어 DNS 캐시 손상 공격은 업스트림 서버의 가짜 응답을 활용하는 경우가 많습니다.

데이터 원본이 전체 DNS 로깅을 지원하고 여러 세그먼트를 기록하도록 선택한 경우 Microsoft Sentinel에서 데이터 중복을 방지하기 위해 쿼리를 조정해야 합니다.

예를 들어 다음과 같은 정규화를 사용하여 쿼리를 수정할 수 있습니다.

```kql
imDNS | where SrcIpAddr != "127.0.0.1" and EventSubType == "response"
```

## <a name="parsers"></a>파서

### <a name="source-agnostic-parsers"></a>원본 중립적 파서

모든 첫 번째 파서가 통합되는 소스에 구애받지 않는 파서 를 사용하고 구성된 모든 원본에서 분석이 실행되도록 하려면 다음 KQL 함수를 쿼리의 테이블 이름으로 사용합니다.

| 이름 | 설명 | 사용 지침 |
| --- | --- | --- |
| **imDNS** | *공용 구조체* 를 사용하여 모든 DNS 원본의 정규화된 이벤트를 포함하는 집계 파서입니다. |- 원본 독립적 분석에서 원본을 추가하거나 제거하려면 이 파서를 업데이트합니다. <br><br>- 원본 독립적 쿼리에서 이 함수를 사용합니다.|
| **ASimDNS** | `imDns`함수와 비슷하지만 매개 변수가 지원되지 않으므로 **로그** 페이지 시간 선택기가 값을 사용하도록 강제하지 `custom` 않습니다. |- 원본 독립적 분석에서 원본을 추가하거나 제거하려면 이 파서를 업데이트합니다.<br><br>- 매개 변수를 사용할 계획이 없는 경우 소스에 구애받지 않는 쿼리에서 이 함수를 사용합니다.|
| **vimDNS\<vendor\>\<product\>** | 원본별 파서가 *vimDNSWindowsOMS* 와 같은 특정 원본에 대한 정규화를 구현합니다. |- 기본으로 사용할 수 있는 정규화 파서가 없는 경우 원본에 대한 원본별 파서 추가 새 `im` 파서에 대한 참조를 포함하도록 집계 파서를 업데이트합니다. <br><br>- 구문 분석 및 정규화 문제를 해결하기 위해 원본 관련 파서를 업데이트합니다.<br><br>- 원본 관련 분석에 원본 관련 파서를 사용합니다.|
| **ASimDNS\<vendor\>\<product\>** | 원본별 파서가 특정 원본에 대한 정규화를 구현합니다. 함수와 달리 `vim*` `ASimDNS*` 함수는 매개 변수를 지원하지 않습니다. |- 기본으로 사용할 수 있는 정규화 파서가 없는 경우 원본에 대한 원본별 파서 추가 `ASim`새 파서에 대한 참조를 포함하도록 집계 파서를 업데이트합니다.<br><br>- 구문 분석 및 정규화 문제를 해결하기 위해 원본 관련 파서를 업데이트합니다.<br><br>- 매개 변수를 사용하지 `ASim` 않는 경우 대화형 쿼리에 원본별 파서 사용|
| | | |

파서를 [Microsoft Sentinel GitHub 리포지토리에서](https://aka.ms/azsentinelDNS)배포할 수 있습니다.

### <a name="out-of-the-box-source-specific-parsers"></a>소스별 파서가 가장 중요합니다.

Microsoft Sentinel은 다음과 같은 제품별 DNS 파서를 제공합니다.

| **이름** | **설명** |
| --- | --- |
|**Microsoft DNS 서버**  |   **DNS 커넥터 및 Log Analytics 에이전트를 사용하여 수집됩니다.** <br> - `ASimDnsMicrosoftOMS` (일반) <br>- `vimDnsMicrosoftOMS` (매개변수) <br><br>  **NXlog를 사용하여 수집:** <br> - `ASimDnsMicrosoftNXlog` (일반)<br>- `vimDnsMicrosoftNXlog` (매개 변수화) |
| **Azure Firewall** |- `ASimDnsAzureFirewall` (일반)<br>- `vimDnsAzureFirewall` (매개 변수화) |
|**Sysmon for Windows(이벤트** 22) | 및 테이블을 모두 지원하는 **Log Analytics 에이전트 또는 Azure Monitor 에이전트를 사용하여 수집됩니다.** `Event` `WindowsEvent` <br>- `ASimDnsMicrosoftSysmon` (일반)<br>- `vimDnsMicrosoftSysmon` (매개변수)  |
|**Cisco Umbrella**  | - `ASimDnsCiscoUmbrella` (일반)<br>- `vimDnsCiscoUmbrella` (매개변수)  |
|**Infoblox NIOS**  |- `ASimDnsInfobloxNIOS` (일반)<br>- `vimDnsInfobloxNIOS` (매개변수) |
| **GCP DNS** |- `ASimDnsGcp` (일반)<br>- `vimDnsGcp`  (매개변수) |
| **Corelight Zeek DNS 이벤트** | - `ASimDnsCorelightZeek` (일반)<br>- `vimDnsCorelightZeek`  (매개변수) |
| **zScaler ZIA** |- `AsimDnszScalerZIA` (일반)<br>- `vimDnszScalerZIA` (매개변수)  |
| | |

파서를 [Microsoft Sentinel GitHub 리포지토리에서](https://aka.ms/azsentinelDNS)배포할 수 있습니다.

### <a name="add-your-own-normalized-parsers"></a>사용자 고유의 정규화된 파서 추가

Dns 정보 모델에 대한 사용자 지정 파서 구현 시 다음 구문을 사용하여 KQL 함수의 이름을 지정합니다.

- `vimDns<vendor><Product>` 매개변수 파서의 경우
- `ASimDns<vendor><Product>` 일반 파서의 경우

### <a name="filtering-parser-parameters"></a>파서 매개 변수 필터링

`im`및 `vim*` 파서는 [필터링 매개 변수](normalization-about-parsers.md#optimized-parsers)를 지원 합니다. 이러한 파서는 선택 사항이 며 쿼리 성능을 향상 시킬 수 있습니다.

다음 필터링 매개 변수를 사용할 수 있습니다.

| 이름     | 유형      | Description |
|----------|-----------|-------------|
| **starttime** | Datetime | 이 시간 이후에 실행 된 DNS 쿼리만 필터링 합니다. |
| **endtime** | Datetime | 이 시간 이전에 실행이 완료 된 DNS 쿼리만 필터링 합니다. |
| **srcipaddr** | 문자열 | 이 원본 IP 주소에서 DNS 쿼리만 필터링 합니다. |
| **domain_has_any**| 동적 | `domain`(또는 `query` )에 나열 된 도메인 이름 (또는)이 이벤트 도메인의 일부로 포함 된 DNS 쿼리만 필터링 합니다.
| **responsecodename** | 문자열 | 응답 코드 이름이 제공 된 값과 일치 하는 DNS 쿼리만 필터링 합니다. <br>`NXDOMAIN` |
| **response_has_ipv4** | 문자열 | 응답 필드가 제공 된 IP 주소 또는 IP 주소 접두사로 시작 하는 DNS 쿼리만 필터링 합니다. 단일 IP 주소 또는 접두사를 필터링 하려는 경우이 매개 변수를 사용 합니다. <br><br>응답을 제공 하지 않는 원본에 대해서는 결과가 반환 되지 않습니다.|
| **response_has_any_prefix** | 동적| 응답 필드가 나열 된 IP 주소 또는 IP 주소 접두사로 시작 하는 DNS 쿼리만 필터링 합니다. <br><br>IP 주소 또는 접두사 목록을 필터링 하려는 경우이 매개 변수를 사용 합니다. <br><br>응답을 제공 하지 않는 원본에 대해서는 결과가 반환 되지 않습니다. |
| **이벤트**| 문자열 | 지정 된 형식의 DNS 쿼리만 필터링 합니다. 값을 지정 하지 않으면 조회 쿼리만 반환 됩니다. |
| | | |

예를 들어 도메인 이름을 확인 하는 데 실패 한 마지막 날의 DNS 쿼리만 필터링 하려면 다음을 사용 합니다.

```kql
imDns (responsecodename = 'NXDOMAIN', starttime = ago(1d), endtime=now())
```

지정 된 도메인 이름 목록에 대 한 DNS 쿼리만 필터링 하려면 다음을 사용 합니다.

```kql
let torProxies=dynamic(["tor2web.org", "tor2web.com", "torlink.co",...]);
imDns (domain_has_any = torProxies)
```

## <a name="normalized-content"></a>정규화된 콘텐츠

정규화 된 DNS 이벤트를 사용 하는 분석 규칙의 전체 목록은 [dns 쿼리 보안 콘텐츠](normalization-content.md#dns-query-security-content) 섹션을 참조 하세요.

## <a name="schema-details"></a>스키마 세부 정보

DNS 정보 모델은 [OSSEM DNS 엔터티 스키마](https://github.com/OTRF/OSSEM/blob/master/docs/cdm/entities/dns.md)에 맞춰 정렬됩니다.

자세한 내용은 [IANA(Internet Assigned Numbers Authority) 도메인 이름 시스템 매개 변수 참조](https://www.iana.org/assignments/dns-parameters/dns-parameters.xhtml)를 참조하세요.

### <a name="common-fields"></a>공용 필드

모든 스키마에 공통 된 필드는 [Asim 스키마 개요](normalization-about-schemas.md#common)에 설명 되어 있습니다. 다음 필드에는 DNS 이벤트에 대 한 특정 지침이 있습니다.

| **필드** | **클래스** | **형식**  | **설명** |
| --- | --- | --- | --- |
| **EventType** | 필수 | Enumerated | 레코드에서 보고 한 작업을 나타냅니다. <br><br> DNS 레코드의 경우 이 값은 [DNS 연산 코드](https://www.iana.org/assignments/dns-parameters/dns-parameters.xhtml)가 됩니다. <br><br>예: `lookup`|
| **EventSubType** | 선택 사항 | Enumerated | **request** 또는 **response** 입니다. <br><br>대부분의 원본에서는 [응답만 기록](#guidelines-for-collecting-dns-events)하므로 값은 종종 **response** 입니다.  |
| <a name=eventresultdetails></a>**EventResultDetails** | Alias | | **_EventResult_** 필드에 보고된 결과에 대한 이유 또는 세부 정보. <br><br> [ResponseCodeName](#responsecodename) 필드의 별칭을 지정합니다.|
| **EventSchemaVersion** | 필수 | String | 여기에 설명 된 스키마 버전은 **0.1.3** 입니다. |
| **EventSchema** | 필수 | String | 여기에 설명 된 스키마 이름은 **Dns** 입니다. |
| **Dvc** 필드| -      | -    | DNS 이벤트의 경우 장치 필드는 DNS 이벤트를 보고 하는 시스템을 참조 합니다. |
| | | | |


### <a name="dns-specific-fields"></a>DNS 관련 필드

아래 필드는 다른 스키마의 필드와 유사 하지만 동일한 명명 규칙을 따르는 DNS 이벤트와 관련이 있습니다.

| **필드** | **클래스** | **형식** | **참고 사항** |
| --- | --- | --- | --- |
| <a name="src"></a>**소스** | 권장       | String     |    원본 장치의 고유 식별자입니다. <br><br>이 필드는 [Srcdvcid](#srcdvcid), [SrcHostname](#srchostname)또는 [srcipaddr](#srcipaddr) 필드에 별칭을 할 수 있습니다. <br><br>예: `192.168.12.1`       |
| <a name="srcipaddr"></a>**SrcIpAddr** | 권장 | IP 주소 | DNS 요청을 보내는 클라이언트의 IP 주소입니다. 재귀 DNS 요청의 경우이 값은 일반적으로 보고 장치 이며 대부분의 경우로 설정 됩니다 `127.0.0.1` . <br><br>예: `192.168.12.1` |
| **SrcPortNumber** | 선택 | 정수 | DNS 쿼리의 원본 포트입니다.<br><br>예: `54312` |
| <a name="ipaddr"></a>**IpAddr** | Alias | | [Srcipaddr](#srcipaddr) 에 대 한 별칭 |
| **SrcGeoCountry** | 선택 사항 | 국가 | 원본 IP 주소와 연결 된 국가입니다.<br><br>예: `USA` |
| **SrcGeoRegion** | 선택 사항 | 지역 | 원본 IP 주소와 연결 된 국가 내의 지역입니다.<br><br>예: `Vermont` |
| **SrcGeoCity** | 선택 사항 | City | 원본 IP 주소와 연결 된 구/군/시입니다.<br><br>예: `Burlington` |
| **SrcGeoLatitude** | 선택 사항 | 위도 | 원본 IP 주소와 연결 된 지리적 좌표의 위도입니다.<br><br>예: `44.475833` |
| **SrcGeoLongitude** | 선택 사항 | 경도 | 원본 IP 주소와 연결 된 지리적 좌표의 경도입니다.<br><br>예: `73.211944` |
| **SrcRiskLevel** | 선택 | 정수 | 원본과 관련 된 위험 수준입니다. 값은에 대 한 범위에 맞게 조정 되어야 합니다 `0` `100` `0` `100` .<br><br>예: `90` |
| <a name="srchostname"></a>**SrcHostname** | 권장 | String | 원본 장치 호스트 이름 (도메인 정보 제외) 사용할 수 있는 장치 이름이 없으면이 필드에 관련 IP 주소를 저장 합니다. 이 값은 [Srcipaddr](#srcipaddr) 가 지정 된 경우 필수입니다.<br><br>예: `DESKTOP-1282V4D` |
| **호스트 이름** | Alias | | [SrcHostname](#srchostname) 에 대 한 별칭 |
|<a name="srcdomain"></a>**Srcdomain** | 권장 | String | 원본 장치의 도메인입니다.<br><br>예: `Contoso` |
| <a name="srcdomaintype"></a>**SrcDomainType** | 권장 | Enumerated | [Srcdomain](#srcdomain)의 유형 (알려진 경우)입니다. 가능한 값은 다음과 같습니다.<br>- `Windows` (예: `contoso` )<br>- `FQDN` (예: `microsoft.com` )<br><br>[Srcdomain](#srcdomain) 을 사용 하는 경우 필수 사항입니다. |
| **SrcFQDN** | 선택 | 문자열 | 사용 가능한 경우 도메인 정보를 포함한 원본 디바이스 호스트 이름입니다. <br><br>**참고**:이 필드는 기존의 FQDN 형식 및 Windows domain\hostname 형식을 모두 지원 합니다. [SrcDomainType](#srcdomaintype) 필드에는 사용 된 형식이 반영 됩니다. <br><br>예: `Contoso\DESKTOP-1282V4D` |
| <a name="srcdvcid"></a>**SrcDvcId** | 선택 | 문자열 | 레코드에 보고된 원본 디바이스의 ID입니다.<br><br>예: `ac7e9755-8eae-4ffc-8a02-50ed7a2216c3` |
| **SrcDvcIdType** | 선택 사항 | Enumerated | [Srcdvcid](#srcdvcid)의 유형입니다 (알려진 경우). 가능한 값은 다음과 같습니다.<br> - `AzureResourceId`<br>- `MDEid`<br><br>여러 Id를 사용할 수 있는 경우 위의 목록에서 첫 번째 Id를 사용 하 고 다른 Id를 **SrcDvcAzureResourceId** 및 **Srcdvcmdeid** 에 각각 저장 합니다.<br><br>**참고**:이 필드는 [srcdvcid](#srcdvcid) 를 사용 하는 경우에 필요 합니다. |
| **SrcDeviceType** | 선택 사항 | Enumerated | 원본 디바이스의 형식입니다. 가능한 값은 다음과 같습니다.<br>- `Computer`<br>- `Mobile Device`<br>- `IOT Device`<br>- `Other` |
| <a name="srcuserid"></a>**SrcUserId** | 선택 | 문자열 | 원본 사용자의 컴퓨터에서 읽을 수 있는 영숫자 고유 표현입니다. 형식 및 지원되는 형식은 다음과 같습니다.<br>-  **SID** (Windows):`S-1-5-21-1377283216-344919071-3415362939-500`<br>-  **UID**  (Linux): `4578`<br>-  **AADID** (Azure Active Directory):`9267d02c-5f76-40a9-a9eb-b686f3ca47aa`<br>-  **OktaId**: `00urjk4znu3BcncfY0h7`<br>-  **AWSId**: `72643944673`<br><br>[Srcuseridtype](#srcuseridtype) 필드에 ID 유형을 저장 합니다. <br><br>다른 Id를 사용할 수 있는 경우 필드 이름을 **Srcusersid**, **srcuseruid**, **SrcUserAadId**, **Srcuseroktaid** 및 **userawsid** 로 각각 정규화 하는 것이 좋습니다. 자세한 내용은 [사용자 엔터티](normalization-about-schemas.md#the-user-entity)를 참조하세요.<br><br>예: S-1-12 |
| <a name="srcuseridtype"></a>**SrcUserIdType** | 선택 사항 | Enumerated | [Srcuserid](#srcuserid) 필드에 저장 된 ID의 유형입니다. 지원 되는 값은 `SID` ,, `UIS` `AADID` , `OktaId` 및 `AWSId` 입니다. |
| <a name="srcusername"></a>**SrcUsername** | 선택 | 문자열 | 사용 가능한 경우 도메인 정보를 포함 하는 원본 사용자 이름입니다. 다음 형식 중 하나를 사용 하 고 우선 순위를 지정 합니다.<br>- **Upn/메일**: `johndow@contoso.com`<br>- **Windows**: `Contoso\johndow`<br>- **DN**: `CN=Jeff Smith,OU=Sales,DC=Fabrikam,DC=COM`<br>- **단순**: `johndow`. 도메인 정보를 사용할 수 없는 경우에만 간단한 양식을 사용 합니다.<br><br>[SrcUsernameType](#srcusernametype) 필드에 사용자 이름 유형을 저장 합니다. 다른 Id를 사용할 수 있는 경우 필드 이름을 **Srcuserupn**, **srcuserupn** 및 **SrcUserDn** 으로 정규화 하는 것이 좋습니다.<br><br>자세한 내용은 [사용자 엔터티](normalization-about-schemas.md#the-user-entity)를 참조하세요.<br><br>예: `AlbertE` |
| <a name="user"></a>**정의** | Alias | | [Srcusername](#srcusername) 에 대 한 별칭 |
| <a name="srcusernametype"></a>**SrcUsernameType** | 선택 사항 | Enumerated | [Srcusername](#srcusername) 필드에 저장 된 사용자 이름 유형을 지정 합니다. 지원 되는 값은 `UPN` ,, `Windows` `DN` 및 `Simple` 입니다. 자세한 내용은 [사용자 엔터티](normalization-about-schemas.md#the-user-entity)를 참조하세요.<br><br>예: `Windows` |
| **SrcUserType** | 선택 사항 | Enumerated | Actor의 형식입니다. 허용된 값은<br>- `Regular`<br>- `Machine`<br>- `Admin`<br>- `System`<br>- `Application`<br>- `Service Principal`<br>- `Other`<br><br>**참고**: 값은 이러한 값으로 정규화되어야 하는 다른 조건을 사용하여 원본 레코드에 제공될 수 있습니다. [EventOriginalUserType](#eventoriginalusertype) 필드에 원래 값을 저장 합니다. |
| <a name="eventoriginalusertype"></a>**SrcOriginalUserType** | 선택 | 문자열 | 원본에서 제공 하는 경우 원래 원본 사용자 형식입니다. |
| **SrcUserDomain** | 선택 | 문자열 | 이 필드는 이전 버전과의 호환성을 위해서만 유지 됩니다. ASIM을 사용 하려면 사용 가능한 경우 도메인 정보를 [Srcusername](#srcusername) 필드에 포함 해야 합니다. |
| <a name="srcprocessname"></a>**SrcProcessName**              | 선택     | 문자열     |   DNS 요청을 시작 하는 프로세스의 파일 이름입니다. 이 이름은 일반적으로 프로세스 이름으로 간주됩니다.  <br><br>예: `C:\Windows\explorer.exe`  |
| <a name="process"></a>**프로세스**        | Alias        |            | [SrcProcessName](#srcprocessname) 에 대 한 별칭 <br><br>예: `C:\Windows\System32\rundll32.exe`|
| **SrcProcessId**| 필수    | String        | DNS 요청을 시작 하는 프로세스의 PID (프로세스 ID)입니다.<br><br>예제: `48610176`           <br><br>**참고**: 형식은 다양한 시스템을 지원하도록 *문자열* 로 정의되지만, Windows와 Linux에서 이 값은 숫자여야 합니다. <br><br>Windows 또는 Linux 컴퓨터를 사용하고 다른 형식을 사용한 경우 값을 변환해야 합니다. 예를 들어 16진수 값을 사용한 경우 10진수 값으로 변환합니다.    |
| **SrcProcessGuid**              | 선택     | 문자열     |  DNS 요청을 시작 하는 프로세스의 생성 된 고유 식별자 (GUID)입니다.   <br><br> 예: `EF3BD0BD-2B74-60C5-AF5C-010000001E00`            |
| <a name="dst"></a>**대상** | 권장       | String     |    DNS 요청을 받는 서버의 고유 식별자입니다. <br><br>이 필드는 [Dstdvcid](#dstdvcid), [DstHostname](#dsthostname)또는 [DstIpAddr](#dstipaddr) 필드에 별칭을 할 수 있습니다. <br><br>예: `192.168.12.1`       |
| <a name="dstipaddr"></a>**DstIpAddr** | 선택 사항 | IP 주소 | DNS 요청을 수신하는 서버의 IP 주소입니다. 일반 DNS 요청의 경우이 값은 일반적으로 보고 장치 이며 대부분의 경우로 설정 됩니다 `127.0.0.1` .<br><br>예: `127.0.0.1` |
| **DstGeoCountry** | 선택 사항 | 국가 | 대상 IP 주소와 연결 된 국가입니다. 자세한 내용은 [논리적 형식](normalization-about-schemas.md#logical-types)을 참조하세요.<br><br>예: `USA` |
| **DstGeoRegion** | 선택 사항 | 지역 | 대상 IP 주소와 연결 된 국가 내의 지역 또는 상태입니다. 자세한 내용은 [논리적 형식](normalization-about-schemas.md#logical-types)을 참조하세요.<br><br>예: `Vermont` |
| **DstGeoCity** | 선택 사항 | City | 대상 IP 주소와 연결 된 구/군/시입니다. 자세한 내용은 [논리적 형식](normalization-about-schemas.md#logical-types)을 참조하세요.<br><br>예: `Burlington` |
| **DstGeoLatitude** | 선택 사항 | 위도 | 대상 IP 주소와 연결 된 지리적 좌표의 위도입니다. 자세한 내용은 [논리적 형식](normalization-about-schemas.md#logical-types)을 참조하세요.<br><br>예: `44.475833` |
| **DstGeoLongitude** | 선택 사항 | 경도 | 대상 IP 주소와 연결된 지리적 좌표의 경도입니다. 자세한 내용은 [논리적 형식](normalization-about-schemas.md#logical-types)을 참조하세요.<br><br>예: `73.211944` |
| **DstcRiskLevel** | 선택 | 정수 | 대상과 연결된 위험 수준입니다. 값은 0에서 100까지의 범위로 조정되어야 하며, 0은 무해하고 100은 위험성이 높습니다.<br><br>예: `90` |
| **DstPortNumber** | 선택 | 정수  | 대상 포트 번호입니다.<br><br>예: `53` |
| <a name="dsthostname"></a>**DstHostname** | 선택 | 문자열 | 도메인 정보를 제외한 대상 디바이스 호스트 이름입니다. 사용할 수 있는 디바이스 이름이 없는 경우 이 필드에 관련 IP 주소를 저장합니다.<br><br>예: `DESKTOP-1282V4D`<br><br>**참고:** [DstIpAddr이](#dstipaddr) 지정된 경우 이 값은 필수입니다. |
| <a name="dstdomain"></a>**DstDomain** | 선택 | 문자열 | 대상 디바이스의 도메인입니다.<br><br>예: `Contoso` |
| <a name="dstdomaintype"></a>**DstDomainType** | 선택 사항 | Enumerated | 알려진 경우 [DstDomain 의 형식입니다.](#dstdomain) 가능한 값은 다음과 같습니다.<br>- `Windows (contoso\mypc)`<br>- `FQDN (docs.microsoft.com)`<br><br>[DstDomain을](#dstdomain) 사용하는 경우 필수입니다. |
| **DstFQDN** | 선택 | 문자열 | 사용 가능한 경우 도메인 정보를 포함하는 대상 디바이스 호스트 이름입니다. <br><br>예: `Contoso\DESKTOP-1282V4D` <br><br>**참고:** 이 필드는 기존 FQDN 형식과 Windows domain\hostname 형식을 모두 지원합니다. [DstDomainType은](#dstdomaintype) 사용된 형식을 반영합니다.   |
| <a name="dstdvcid"></a>**DstDvcId** | 선택 | 문자열 | 레코드에 보고된 대상 디바이스의 ID입니다.<br><br>예: `ac7e9755-8eae-4ffc-8a02-50ed7a2216c3` |
| **DstDvcIdType** | 선택 사항 | Enumerated | 알려진 경우 [DstDvcId](#dstdvcid)의 형식입니다. 가능한 값은 다음과 같습니다.<br> - `AzureResourceId`<br>- `MDEidIf`<br><br>여러 개의ID를 사용할 수 있는 경우 위 목록의 첫 번째 ID를 사용하고  **DstDvcAzureResourceId** 또는 **DstDvcMDEid** 필드에 각각 다른 것을 저장합니다.<br><br>**DstDeviceId를** 사용하는 경우 필요합니다.|
| **DstDeviceType** | 선택 사항 | Enumerated | 대상 디바이스의 유형입니다. 가능한 값은 다음과 같습니다.<br>- `Computer`<br>- `Mobile Device`<br>- `IOT Device`<br>- `Other` |
| <a name=query></a>**DnsQuery** | 필수 | FQDN | 확인해야 하는 도메인입니다. <br><br>**참고**: 일부 원본에서 여러 다른 형식으로 이 쿼리를 보냅니다. 예를 들어 DNS 프로토콜 자체에서 쿼리 끝에 점( **.** )이 있습니다. 이 점은 제거해야 합니다.<br><br>DNS 프로토콜에서는 단일 요청을 통해 여러 개를 쿼리할 수 있지만, 이 시나리오는 거의 발생하지 않습니다. 요청에 여러 쿼리가 있으면 첫 번째 쿼리를 이 필드에 저장하고 나머지는 선택적으로 [AdditionalFields](normalization-about-schemas.md#additionalfields) 필드에 보관합니다.<br><br>예: `www.malicious.com` |
| **도메인** | Alias | | [Query](#query)에 대한 별칭입니다. |
| **DnsQueryType** | 선택 | 정수 | 이 [필드에는 DNS 리소스 레코드 형식 코드 가](https://www.iana.org/assignments/dns-parameters/dns-parameters.xhtml)포함될 수 있습니다. <br><br>예: `28`|
| **DnsQueryTypeName** | 권장 | Enumerated | 이 필드에는 [DNS Resource Record Type](https://www.iana.org/assignments/dns-parameters/dns-parameters.xhtml) 이름이 포함될 수 있습니다. <br><br>**참고**: IANA는 값의 대소문자를 정의하지 않으므로 분석에서 필요에 따라 대소문자를 정규화해야 합니다. 원본이 쿼리 유형 이름이 아닌 숫자 쿼리 유형 코드만 제공하는 경우 파서는 이 값으로 보강할 조회 테이블을 포함해야 합니다.<br><br>예: `AAAA`|
| <a name=responsename></a>**DnsResponseName** | 선택 | 문자열 | 레코드에 포함된 응답의 콘텐츠입니다.<br> <br> DNS 응답 데이터는 보고 디바이스 간에 일관되지 않고 구문 분석이 복잡하므로 원본 독립적 분석의 가치가 낮습니다. 따라서 정보 모델에는 구문 분석 및 정규화가 필요하지 않으며 Microsoft Sentinel은 보조 함수를 사용하여 응답 정보를 제공합니다. 자세한 내용은 [DNS 응답 처리](#handling-dns-response)를 참조하세요.|
| <a name=responsecodename></a>**DnsResponseCodeName** |  필수 | Enumerated | [DNS 응답 코드](https://www.iana.org/assignments/dns-parameters/dns-parameters.xhtml). <br><br>**참고**: IANA는 값의 대소문자를 정의하지 않으므로 분석에서 대소문자를 정규화해야 합니다. 원본이 응답 코드 이름이 아닌 숫자 응답 코드만 제공하는 경우 파서는 이 값으로 보강할 조회 테이블을 포함해야 합니다. <br><br> 이 레코드가 응답이 아닌 요청을 나타내는 경우 **NA** 로 설정합니다. <br><br>예: `NXDOMAIN` |
| **DnsResponseCode** | 선택 | 정수 | [DNS 숫자 응답 코드](https://www.iana.org/assignments/dns-parameters/dns-parameters.xhtml). <br><br>예: `3`|
| **TransactionIdHex** | 권장 | String | DNS 고유 16진수 트랜잭션 ID입니다. |
| **NetworkProtocol** | 선택 사항 | Enumerated | 네트워크 확인 이벤트에서 사용하는 전송 프로토콜입니다. 값은 **UDP** 또는 **TCP** 일 수 있으며 DNS의 경우 가장 일반적으로 **UDP** 로 설정됩니다. <br><br>예: `UDP`|
| **DnsQueryClass** | 선택 | 정수 | [DNS 클래스 ID](https://www.iana.org/assignments/dns-parameters/dns-parameters.xhtml).<br> <br>실제로는 **IN** 클래스(ID 1)만 사용되므로 이 필드의 가치가 떨어집니다.|
| **DnsQueryClassName** | 선택 | 문자열 | [DNS 클래스 이름](https://www.iana.org/assignments/dns-parameters/dns-parameters.xhtml).<br> <br>실제로는 **IN** 클래스(ID 1)만 사용되므로 이 필드의 가치가 떨어집니다. <br><br>예: `IN`|
| <a name=flags></a>**DnsFlags** | 선택 사항 | 문자열 목록 | 보고 디바이스에서 제공하는 플래그 필드입니다. 플래그 정보가 여러 필드에 제공되는 경우 구분 기호로 쉼표를 사용하여 연결합니다. <br><br>DNS 플래그는 구문 분석이 복잡하며 분석에서 자주 사용되지 않기 때문에 구문 분석 및 정규화가 필요하지 않으며 Microsoft Sentinel은 보조 함수를 사용하여 플래그 정보를 제공합니다. 자세한 내용은 [DNS 응답 처리](#handling-dns-response)를 참조하세요. <br><br>예: `["DR"]`|
| <a name=UrlCategory></a>**UrlCategory** |  선택 | 문자열 | DNS 이벤트 원본은 요청된 도메인의 범주를 조회할 수도 있습니다. 필드는 Microsoft Sentinel 네트워크 스키마에 맞게 **_UrlCategory라고_** 합니다. <br><br>**_DomainCategory_** 는 DNS에 맞는 별칭으로 추가됩니다. <br><br>예: `Educational \\ Phishing` |
| **DomainCategory** | 선택 사항 | Alias | [UrlCategory](#UrlCategory)에 대한 별칭입니다. |
| **ThreatCategory** | 선택 | 문자열 | DNS 이벤트 원본이 DNS 보안도 제공하는 경우 DNS 이벤트도 평가할 수 있습니다. 예를 들어 위협 인텔리전스 데이터베이스에서 IP 주소 또는 도메인을 검색하고 위협 범주를 사용하여 도메인 또는 IP 주소를 할당할 수 있습니다. |
| **EventSeverity** | 선택 | 문자열 | DNS 이벤트 원본이 DNS 보안도 제공하는 경우 DNS 이벤트를 평가할 수 있습니다. 예를 들어 위협 인텔리전스 데이터베이스에서 IP 주소 또는 도메인을 검색하고 평가에 따라 심각도를 할당할 수 있습니다. <br><br>예: `Informational`|
| <a name="dnsnetworkduration"></a>**DnsNetworkDuration** | 선택 | 정수 | DNS 요청을 완료하는 데 소요되는 시간(밀리초)입니다.<br><br>예: `1500` |
| **기간** | Alias | | [DnsNetworkDuration에](#dnsnetworkduration) 대한 별칭 |
| **DnsFlagsAuthenticated** | 선택 사항 | 부울 | `AD`DNSSEC와 관련된 DNS 플래그는 응답의 응답 및 기관 섹션에 포함된 모든 데이터가 해당 서버의 정책에 따라 서버에서 확인되었음을 응답에 나타냅니다. 자세한 내용은 [RFC 3655 섹션 6.1을](https://tools.ietf.org/html/rfc3655#section-6.1) 참조하세요.    |
| **DnsFlagsAuthoritative** | 선택 사항 | 부울 | DNS `AA` 플래그는 서버의 응답이 신뢰할 수 있는지 여부를 나타냅니다.    |
| **DnsFlagsCheckingDisabled** | 선택 사항 | 부울 | `CD`DNSSEC와 관련된 DNS 플래그는 쿼리에서 확인되지 않은 데이터가 쿼리를 보내는 시스템에 허용됨을 나타냅니다. 자세한 내용은 [RFC 3655 섹션 6.1을](https://tools.ietf.org/html/rfc3655#section-6.1) 참조하세요.   |
| **DnsFlagsRecursionAvailable** | 선택 사항 | 부울 | DNS `RA` 플래그는 응답에서 서버가 재귀 쿼리를 지원함을 나타냅니다.   |
| **DnsFlagsRecursionDesired** | 선택 사항 | 부울 | DNS `RD` 플래그는 요청에서 클라이언트가 서버에서 재귀 쿼리를 사용하도록 함을 나타냅니다.   |
| **DnsFlagsTruncates** | 선택 사항 | 부울 | DNS `TC` 플래그는 응답이 최대 응답 크기를 초과하여 잘렸음을 나타냅니다.  |
| **DnsFlagsZ** | 선택 사항 | 부울 | DNS `Z` 플래그는 이전 DNS 시스템에서 보고될 수 있는 사용되지 않는 DNS 플래그입니다.  |
|<a name="dnssessionid"></a>**DnsSessionId** | 선택 | 문자열 | 보고 디바이스에서 보고한 DNS 세션 식별자입니다. <br><br>예: `EB4BFA28-2EAD-4EF7-BC8A-51DF4FDF5B55` |
| **SessionId** | Alias | String | [DnsSessionId에](#dnssessionid) 대한 별칭 |
| | | | |

### <a name="deprecated-aliases"></a>사용되지 않는 별칭

다음 필드는 현재 사용되지 않지만 이전 버전과의 호환성을 위해 유지 관리되는 별칭입니다. 2021 년 12 월 31 일에 스키마에서 제거 됩니다.

- Query(DnsQuery에 대한 별칭)
- QueryType(DnsQueryType에 대한 별칭)
- QueryTypeName(DnsQueryTypeName에 대한 별칭)
- ResponseName(DnsReasponseName에 대한 별칭)
- ResponseCodeName(DnsResponseCodeName에 대한 별칭)
- ResponseCode(DnsResponseCode에 대한 별칭)
- QueryClass(DnsQueryClass에 대한 별칭)
- QueryClassName(DnsQueryClassName에 대한 별칭)
- Flags(DnsFlags에 대한 별칭)

### <a name="schema-updates"></a>스키마 업데이트

다음은 스키마의 버전 0.1.2 변경 된 내용입니다.
- 필드를 추가 했습니다 `EventSchema` . 현재는 선택적 이지만 2022 1 월 1 일에는 필수 항목입니다.
- 전용 플래그 필드가 추가 되었습니다. 결합 된 **[플래그](#flags)** 필드는 `DnsFlagsAuthoritative` ,,,, `DnsFlagsCheckingDisabled` `DnsFlagsRecursionAvailable` `DnsFlagsRecursionDesired` `DnsFlagsTruncates` 및 `DnsFlagsZ` 입니다.

다음은 스키마의 버전 0.1.3 변경 된 내용입니다.
- 이제 스키마는 `Src*` , 및 필드를 명시적으로 문서화 `Dst*` `Process*` `User*` 합니다.
- `Dvc*`최신 공통 필드 정의와 일치 하도록 추가 필드를 추가 했습니다. 
- `Src` `Dst` 원본 및 대상 시스템에 대 한 선행 식별자에 및을 별칭으로 추가 했습니다.
- 선택적 `DnsNetworkDuration` 및 `Duration` 이에 대 한 별칭을 추가 했습니다.
- 선택적 지역 위치 및 위험 수준 필드를 추가 했습니다.

## <a name="handling-dns-response"></a>DNS 응답 처리

대부분의 경우 기록된 DNS 이벤트에는 크고 자세한 응답 정보가 포함되지 않습니다. 레코드에 추가 응답 정보가 포함된 경우 레코드에 표시되는 대로 [ResponseName](#responsename) 필드에 저장합니다.

구문 분석되지 않은 응답을 입력으로 사용하고 다음 구조의 동적 값을 반환하는 `_imDNS<vendor>Response_`라는 추가 KQL 함수도 제공할 수 있습니다.

```kql
[
    {
        "part": "answer"
        "query": "yahoo.com."
        "TTL": 1782
        "Class": "IN"
        "Type": "A"
        "Response": "74.6.231.21"
    }
    {
        "part": "authority"
        "query": "yahoo.com."
        "TTL": 113066
        "Class": "IN"
        "Type": "NS"
        "Response": "ns5.yahoo.com"
    }
    ...
]
```

동적 값의 각 사전에 있는 필드는 각 DNS 응답의 필드에 해당합니다. `part` 엔터티는 사전이 속한 응답의 파트를 반영하기 위해 `answer`, `authority` 또는 `additional`을 포함해야 합니다.

> [!TIP]
> 최적의 성능을 보장하려면 더 나은 성능을 보장하기 위해 초기 필터링 후에 필요할 때만 `imDNS<vendor>Response` 함수를 호출합니다.
>

## <a name="handling-dns-flags"></a>DNS 플래그 처리

플래그 데이터에는 구문 분석 및 정규화가 필요하지 않습니다. 대신, 보고 디바이스에서 제공한 플래그 데이터를 [플래그](#flags) 필드에 저장합니다. 개별 플래그의 값을 결정 하는 것이 바로 앞에 있는 경우 전용 플래그 필드를 사용할 수도 있습니다. 

또한 `_imDNS<vendor>Flags_` 구문 분석 되지 않은 응답 또는 전용 플래그 필드를 입력으로 사용 하 고 동적 목록을 반환 하 고 각 플래그를 다음 순서로 나타내는 부울 값을 사용 하 여 라는 추가 KQL 함수를 제공할 수 있습니다.

- 인증됨(AD)
- 신뢰할 수 있음(AA)
- 검사 사용 안 함(CD)
- 재귀 사용 가능(RA)
- 재귀 필요(RD)
- 잘림(TC)
- Z

## <a name="next-steps"></a>다음 단계

자세한 내용은 다음을 참조하세요.

- [Microsoft 센티널의 정규화](normalization.md)
- [고급 SIEM 정보 모델 스키마](normalization-about-schemas.md)
- [고급 SIEM 정보 모델 파서](normalization-about-parsers.md)
- [고급 SIEM 정보 모델 콘텐츠](normalization-content.md)