---
title: Microsoft Sentinel 네트워크 세션 정규화 스키마 참조(공개 미리 보기) | Microsoft Docs
description: 이 문서에서는 Microsoft Sentinel 네트워크 세션 정규화 스키마를 표시합니다.
services: sentinel
cloud: na
documentationcenter: na
author: oshezaf
manager: rkarlin
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: reference
ms.date: 11/09/2021
ms.author: ofshezaf
ms.custom: ignite-fall-2021
ms.openlocfilehash: bf13dee6c6a33ae16440357013a34dc36ef93826
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/17/2021
ms.locfileid: "132712911"
---
# <a name="microsoft-sentinel-network-session-normalization-schema-reference-public-preview"></a>Microsoft Sentinel 네트워크 세션 정규화 스키마 참조(공개 미리 보기)

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

네트워크 세션 정규화 스키마는 IP 네트워크 활동을 설명하는 데 사용됩니다. 여기에는 네트워크 연결 및 네트워크 세션이 포함됩니다. 이러한 이벤트는 예를 들어 운영 체제, 라우터, 방화벽, 침입 방지 시스템 및 웹 보안 게이트웨이에 의해 보고됩니다.

Microsoft Sentinel의 정규화에 대한 자세한 내용은 [정규화 및 ASIM(고급 SIEM 정보 모델)을](normalization.md)참조하세요.

> [!IMPORTANT]
> 이 문서에서는 ASIM을 사용할 수 있기 전에 버전 [0.1이](normalization-schema-v1.md) 릴리스되었고 여러 곳에서 ASIM과 일치하지 않는 네트워크 정규화 스키마의 버전 0.2에 대해 설명합니다. 자세한 내용은 [네트워크 정규화 스키마 버전 간의 차이점을 참조하세요.](normalization-schema-v1.md#changes) 
>

> [!IMPORTANT]
> 네트워크 정규화 스키마는 현재 미리 보기로 제공됩니다. 해당 기능은 별도의 서비스 수준 계약 없이 제공되며, 프로덕션 작업에는 사용하지 않는 것이 좋습니다.
>
> [Azure Preview 추가 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)에는 베타, 미리 보기 또는 아직 일반 공급으로 릴리스되지 않은 Azure 기능에 적용되는 추가 법률 용어가 포함되어 있습니다.
>

## <a name="support-for-common-network-telemetry-sources"></a>일반적인 네트워크 원격 분석 원본에 대한 지원

네트워크 정규화 스키마는 모든 IP 네트워크 세션을 나타낼 수 있지만 다음을 비롯한 일반적인 원본 유형을 지원하도록 특별히 설계되었습니다.

- Netflow
- 방화벽
- 침입 방지 시스템
- 웹 서버
- 웹 보안 게이트웨이

다음 섹션에서는 다양한 원본 유형에 대해 스키마를 정규화하고 사용하는 지침을 제공합니다. 각 원본 유형은 다음을 수행할 수 있습니다.
- 보조 필드 목록의 추가 필드 지원: [중간 디바이스 필드,](#Intermediary) [HTTP 세션 필드](#http-session-fields)및 검사 [필드.](#inspection-fields) 일부 필드는 특정 소스 형식의 컨텍스트에서만 필수일 수 있습니다.
- 및 와 같은 일반적인 이벤트 필드에 원본 유형별 값을 `EventType` `EventResult` 허용합니다.
- 파서 외에도 `imNetworkSession` 또는 `imWebSession` 파서 또는 둘 다 지원합니다. `inNetworkNotable`

### <a name="netflow-log-sources"></a>Netflow 로그 원본

| Task | 설명|
| --- | --- |
| **Netflow 이벤트 정규화** | Netflow 이벤트를 정규화하려면 [네트워크 세션 필드에](#network-session-fields)매핑합니다. Netflow 원격 분석은 집계를 지원하며 `EventCount` 필드 값은 이를 반영하고 Netflow *Flows* 값으로 설정해야 합니다. |
| **Netflow 이벤트 사용** | Netflow 이벤트는 [imNetworkSession](#use-parsers) 소스 독립적 파서의 일부로 표면화됩니다. 집계 쿼리를 만들 때 항상 로 설정되지 않을 수 있는 필드 값을 고려해야 `EventCount` `1` 합니다. |
| | |

### <a name="firewall-log-sources"></a>방화벽 로그 원본

| Task | 설명 |
| --- | --- |
| **방화벽 이벤트 정규화** | 방화벽에서 이벤트를 정규화하려면 관련 이벤트를 [이벤트,](#event-fields) [네트워크 세션](#network-session-fields)및 세션 [검사](#inspection-fields) 필드에 매핑합니다. 해당 이벤트를 필터링하고 [inNetworkNotables](#use-parsers) 소스 독립적 파서에 추가합니다. |
| **방화벽 이벤트 사용** | 방화벽 이벤트는 [imNetworkSession](#use-parsers) 소스 독립적 파서의 일부로 표면화됩니다. 방화벽 검사 엔진으로 식별되는 관련 이벤트도 [inNetworkNotables](#use-parsers) 원본 독립적 파서의 일부로 표면화됩니다. |
| | |

### <a name="intrusion-prevention-systems-ips-log-sources"></a>IPS(침입 방지 시스템) 로그 원본

| Task | 설명 |
| --- | --- |
| **IPS 이벤트 정규화** | 침입 방지 시스템에서 이벤트를 정규화하려면 [이벤트 필드,](#event-fields) [네트워크 세션 필드](#network-session-fields)및 세션 검사 [필드를](#inspection-fields)매핑합니다. [imNetworkSession](#use-parsers) 및 [inNetworkNotables](#use-parsers) 소스 독립적 파서 둘 다에 원본별 파서가 포함되어야 합니다. |
| **IPS 이벤트 사용** | IPS 이벤트는 [imNetworkSession](#use-parsers) 및 [inNetworkNotables](#use-parsers) 소스 독립적 파서의 일부로 표면화됩니다. |
| | |

### <a name="web-servers"></a>웹 서버

| Task | 설명 |
| --- | --- |
| **웹 서버 이벤트 정규화** | 웹 서버에서 이벤트를 정규화하려면 [이벤트 필드,](#event-fields) [네트워크 세션 필드](#network-session-fields)및 HTTP 세션 [필드를 매핑합니다.](#http-session-fields) 값을 로 설정하고 `EventType` 및 필드에 대한 HTTP `HTTP Session` 세션별 지침을 `EventResult` `EventResultDetails` 따릅니다. <br><br>[imNetworkSession 및 imWebSession](#use-parsers) 소스 독립적 [](#use-parsers) 파서 모두에 원본별 파서가 포함되어야 합니다. |
| **웹 서버 이벤트 사용** | 웹 서버 이벤트는 [imNetworkSession](#use-parsers) 소스 독립적 파서의 일부로 표시됩니다. 그러나 HTTP 관련 필드를 사용하려면 [imWebSession](#use-parsers) 파서 를 사용합니다. |
| | |

### <a name="web-security-gateways"></a>웹 보안 게이트웨이

| Task | 설명 |
| --- | --- |
| **웹 보안 게이트웨이 이벤트 정규화** | 웹 서버 게이트웨이에서 이벤트를 정규화하려면 [이벤트 필드,](#event-fields)네트워크 세션 필드, HTTP [세션 필드,](#network-session-fields) [세션 검사](#inspection-fields) [필드](#http-session-fields)및 선택적으로 중간 필드를 매핑합니다. <br><br>를 로 설정하고 `EventType` `HTTP` 및 필드에 대한 HTTP 세션별 지침을 `EventResult` `EventResultDetails` 따르세요. <br><br>[imNetworkSession 및 imWebSession](#use-parsers) 소스 독립적 [](#use-parsers) 파서 모두에 원본별 파서가 포함되어 있는지 확인합니다. 또한 검사 엔진에서 검색한 이벤트를 필터링하고 [inNetworkNotables](#use-parsers) 소스에 구애받지 않는 파서에 추가합니다. |
| **웹 보안 게이트웨이 이벤트 사용** | 웹 서버 이벤트는 [imNetworkSession](#use-parsers) 소스 독립적 파서의 일부로 표시됩니다. <br><br>- HTTP 관련 필드를 사용하려면 [imWebSession](#use-parsers) 파서 를 사용합니다.<br>- 검색된 세션을 분석하려면 [inNetworkNotables](#use-parsers) 소스에 구애받지 않는 파서 를 사용합니다. |
| | |


## <a name="use-parsers"></a>파서 사용

모든 기본 제공 파서와 통합하는 소스에 구애받지 않는 파서 를 사용하고 구성된 모든 원본에서 분석이 실행되도록 하려면 다음 파서 중에서 사용합니다.


- **imNetworkSession**( 모든 네트워크 세션용)
- **imWebSession**- HTTP 세션의 경우 일반적으로 웹 서버, 웹 프록시 및 웹 보안 게이트웨이에서 보고됩니다.
- **inNetworkNotables**- 검색 엔진에서 검색한 세션에 대해 일반적으로 의심스러운 것으로 표시됩니다. 주목할 만한 이벤트는 일반적으로 침입 방지 시스템, 방화벽 및 웹 보안 게이트웨이에서 보고됩니다.

[Microsoft Sentinel GitHub 리포지토리에서](https://aka.ms/AzSentinelNetworkSession) [소스와 관련이 없는 소스별 파서를](normalization-about-parsers.md) 배포합니다.

### <a name="built-in-source-specific-parsers"></a>기본 제공 원본별 파서

Microsoft Sentinel은 다음과 같은 기본 제공 제품별 네트워크 세션 파서를 제공합니다.

- 원본 특정 파서:
  - **엔드포인트용 Microsoft 365 Defender** - vimNetworkSessionMicrosoft365Defender
  - **Microsoft Defender for IoT - 엔드포인트(MD4IoT)** - vimNetworkSessionMD4IoT
  - **Linux용 Microsoft Sysmon** - vimNetworkSessionSysmonLinux
  - **Windows 이벤트 방화벽** - log Analytics 에이전트 또는 Azure Monitor 에이전트를 사용하여 수집된 Windows 이벤트 515x를 사용하여 수집된 방화벽 작업을 이벤트 또는 WindowsEvent 테이블인 vimNetworkSessionMicrosoftWindowsEventFirewall로 Windows. 

파서를 [Microsoft Sentinel GitHub 리포지토리에서](https://aka.ms/AzSentinelNetworkSession)배포할 수 있습니다.

### <a name="add-your-own-normalized-parsers"></a>사용자 고유의 정규화된 파서 추가

네트워크 세션 정보 모델에 대한 사용자 지정 파서 구현 시 구문을 사용하여 KQL 함수의 이름을 `imNetworkSession<vendor><Product>` 지정합니다. 이 함수는 원본과 관련된 모든 필드를 매핑해야 합니다.

로그 소스에 따라 필요에 따라 KQL 함수를 관련 소스에 관계 없이 파서에 추가합니다. 자세한 내용은 다음을 참조하세요.

- [Netflow 로그 원본](#netflow-log-sources)
- [방화벽 로그 원본](#firewall-log-sources)
- [IPS(침입 방지 시스템) 로그 원본](#intrusion-prevention-systems-ips-log-sources)
- [웹 서버](#web-servers)
- [웹 보안 게이트웨이](#web-security-gateways)

## <a name="schema-details"></a>스키마 세부 정보

네트워크 세션 정보 모델은 [OSSEM 네트워크 엔터티 스키마](https://github.com/OTRF/OSSEM/blob/master/docs/cdm/entities/network.md)입니다.

업계 모범 사례를 준수하기 위해 네트워크 세션 스키마는 설명자 **Src** 및 **Dst를** 사용하여 필드 이름에 토큰 **Dvc를** 포함하지 않고 네트워크 세션 원본 및 대상 디바이스를 식별합니다.

따라서 예를 들어 원본 디바이스 호스트 이름 및 IP 주소의 이름은 **SrcHostname** 및 **SrcIpAddr로** 각각 지정되며 **Src *Dvc* Hostname 및 Src** ***Dvc* IpAddr이** 아닙니다. 접두사 **Dvc는** 해당하는 경우 보고 또는 중간 디바이스에만 사용됩니다.

원본 및 대상 디바이스와 연결된 사용자 및 애플리케이션을 설명하는 필드는 **Src** 및 **Dst** 설명자도 사용합니다.

다른 ASIM 스키마는 일반적으로 **Dst** 대신 **Target을** 사용합니다.

### <a name="log-analytics-fields"></a>Log Analytics 필드

다음 필드는 각 레코드에 대해 Log Analytics에서 생성되며 사용자 지정 커넥터 를 만들 때 [재정의할](create-custom-connector.md)수 있습니다.


| 필드 | 형식 | 토론(Discussion) |
|-------|------|------------|
| <a name="timegenerated"></a>**TimeGenerated** | Datetime | 보고 디바이스에서 이벤트가 생성된 시간입니다. |
| **\_ResourceId** | guid | 보고 디바이스 또는 서비스의 Azure 리소스 ID 또는 Syslog, CEF 또는 WEF를 사용하여 전달된 이벤트의 로그 전달자 리소스 ID입니다. |
| **형식** | String | 레코드를 가져온 원본 테이블입니다. 이 필드는 여러 채널을 통해 다른 테이블에 대해 동일한 이벤트를 수신할 수 있지만 및 값이 동일한 경우에 `EventVendor` `EventProduct` 유용합니다. <br><br>예를 들어 Sysmon 이벤트는 **Event** 테이블 또는 **SecurityEvent** 테이블에 수집할 수 있습니다. |
| | | |

> [!NOTE]
> Log Analytics는 보안 사용 사례와 관련성이 낮은 다른 필드도 추가합니다. 자세한 내용은 [Azure Monitor 로그의 표준 열](../azure-monitor/logs/log-standard-columns.md)을 참조하세요.
>

### <a name="event-fields"></a>이벤트 필드

이벤트 필드는 모든 스키마에 공통되며, 작업 자체와 보고 디바이스를 설명합니다.

| 필드 | 클래스 | 형식 | 설명 |
|-------|-------|------|-------------|
| **EventMessage** | 선택 | 문자열 | 레코드에 포함되거나 레코드에서 생성된 일반 메시지 또는 설명입니다. |
| **EventCount** | 필수 | 정수 | 레코드에서 설명하는 이벤트 수입니다. <br><br>이 값은 원본에서 집계를 지원할 때 사용되며, 단일 레코드에서 여러 이벤트를 나타낼 수 있습니다. <br><br>**참고:** Netflow 원본은 집계를 지원하며 **EventCount** 필드는 Netflow **FLOWS** 필드의 값으로 설정해야 합니다. 다른 소스의 경우 값은 일반적으로 로 `1` 설정됩니다. |
| **EventStartTime** | 필수 | 날짜/시간 | 원본이 집계를 지원하고 레코드가 여러 이벤트를 나타내는 경우 이 필드는 첫 번째 이벤트가 생성된 시간을 지정합니다. 그렇지 않은 경우 이 필드는 [TimeGenerated](#timegenerated) 필드의 별칭을 지정합니다. |
| **EventEndTime** | 필수 | Alias | [TimeGenerated](#timegenerated) 필드에 대한 별칭입니다. |
| **EventType** | 필수 | Enumerated | 레코드에서 보고하는 작업을 설명합니다.<br><br> 네트워크 세션 레코드의 경우 지원되는 값은 다음과 같습니다.<br>- `NetworkConnection`<br>- `NetworkSession`<br>- `HTTPsession` |
| **EventSubType** | 선택 | 문자열 | 해당하는 경우 이벤트 유형에 대한 추가 설명입니다. <br> 네트워크 세션 레코드의 경우 지원되는 값은 다음과 같습니다.<br>- `Start`<br>- `End` |
| **EventResult** | 필수 | Enumerated | 다음 값 중 하나로 정규화된 이벤트 결과를 설명합니다. <br> - `Success` <br> - `Partial` <br> - `Failure` <br> - `NA`(해당 사항 없음) <br><br>HTTP 세션의 경우 `Success` 는 보다 낮은 상태 코드로 정의되며 보다 높은 상태 `400` `Failure` 코드로 `400` 정의됩니다. HTTP 상태 코드 목록은 W3 Org 를 [참조하세요.](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html)<br><br>원본은 [EventResultDetails](#eventresultdetails)  필드에 대한 값만 제공할 수 있으며  **EventResult**  값을 얻으려면 분석해야 합니다. |
| <a name="eventresultdetails"></a>**EventResultDetails** | 선택 | 문자열 | HTTP 세션의 경우 값은 HTTP 상태 코드여야 합니다. <br><br>**참고**: 값은 이러한 값으로 정규화되어야 하는 다른 조건을 사용하여 원본 레코드에 제공될 수 있습니다. 원래 값은 **EventOriginalResultDetails** 필드에 저장되어야 합니다.|
| **EventOriginalResultDetails**    | 선택    | 문자열     |  원본에서 제공하는 경우 [EventResultDetails](#eventresultdetails)에 대한 원래 레코드에 제공된 값입니다.|
| **EventSeverity** | 필수 | Enumerated | 검색된 위협 또는 경고를 나타내는 경우 이벤트의 심각도입니다. 가능한 값은 `Informational` , `Low` , 및 `Medium` `High` 입니다. <br><br>이벤트가 위협을 나타내지 않는 경우 `Informational` 값을 사용합니다.<br><br>**참고**: 값은 이러한 값으로 정규화되어야 하는 다른 조건을 사용하여 원본 레코드에 제공될 수 있습니다. 원래 값을 [EventOriginalSeverity 필드에 저장합니다.](#eventoriginalseverity) |
| <a name="eventoriginalseverity"></a>**EventOriginalSeverity** | 선택 | 문자열 | 원본 레코드에 제공된 원래 심각도 값입니다. |
| **EventOriginalUid** | 선택 | 문자열 | 원본에서 제공하는 경우 원래 레코드의 고유 ID입니다.<br><br>예: `69f37748-ddcd-4331-bf0f-b137f1ea83b` |
| **EventOriginalType** | 선택 | 문자열 | 원본에서 제공하는 경우 원본 이벤트 유형 또는 ID입니다. <br><br>예: `5031` |
| <a name="eventproduct"></a>**EventProduct** | 필수 | String | 이벤트를 생성하는 제품<br><br>예: `Sysmon`<br><br>**참고**: 이 필드는 원본 레코드에서 사용할 수 없습니다. 이러한 경우 이 필드는 파서에서 설정해야 합니다. |
| **EventProductVersion** | 선택 | 문자열 | 이벤트를 생성하는 제품의 버전<br><br>예: `12.1` |
| **EventVendor** | 필수 | String | 이벤트를 생성하는 제품의 공급 업체<br><br>예: `Microsoft`<br><br>**참고**: 이 필드는 원본 레코드에서 사용할 수 없습니다. 이러한 경우 이 필드는 파서에서 설정해야 합니다. |
| **EventSchema** | 필수 | String | 스키마의 이름입니다. 여기에 설명 된 스키마 이름은 `NetworkSession` 입니다. |
| **EventSchemaVersion** | 필수 | String | 스키마의 버전입니다. 여기에 설명 된 스키마의 버전은 `0.2` 입니다. |
| **EventReportUrl** | 선택 | 문자열 | 이벤트에 대한 추가 정보를 제공하는 리소스에 대해 이벤트에 제공된 URL입니다. |
| **Dvc** | Alias | String | 보고 또는 중간 장치의 고유 식별자입니다.<br><br>예: `ContosoDc.Contoso.Azure`<br><br>이 필드에는 [Dvcfqdn](#dvcfqdn), [dvcid](#dvcid), [DvcHostname](#dvchostname)또는 [dvcipaddr](#dvcipaddr) 필드가 별칭으로 있을 수 있습니다. 명백한 장치가 없는 클라우드 원본의 경우 [이벤트 제품](#eventproduct) 필드와 동일한 값을 사용 합니다. |
| <a name="dvcipaddr"></a>**DvcIpAddr** | 권장 | IP 주소 | 보고 또는 중간 장치의 IP 주소입니다.<br><br>예: `2001:db8::ff00:42:8329` |
| <a name="dvchostname"></a>**DvcHostname** | 필수 | String | 보고 또는 중간 장치의 호스트 이름 (도메인 정보 제외)입니다. 사용할 수 있는 장치 이름이 없으면이 필드에 관련 IP 주소를 저장 합니다.<br><br>예: `DESKTOP-1282V4D` |
| <a name="dvcdomain"></a>**DvcDomain** | 권장 | String | 보고 또는 중간 장치의 도메인입니다.<br><br>예: `Contoso` |
| <a name="dvcdomaintype"></a>**DvcDomainType** | 권장 | Enumerated | [Dvcdomain](#dvcdomain) 의 유형입니다 (알려진 경우). 가능한 값은 다음과 같습니다.<br>- `Windows (contoso\mypc)`<br>- `FQDN (docs.microsoft.com)`<br><br>**참고**:이 필드는 [dvcdomain](#dvcdomain) 필드가 사용 되는 경우에 필요 합니다. |
| <a name="dvcfqdn"></a>**DvcFQDN** | 선택 | 문자열 | 사용 가능한 경우 도메인 정보를 포함 하 여 보고 또는 중간 장치의 호스트 이름입니다. <br><br> 예: `Contoso\DESKTOP-1282V4D`<br><br>**참고**:이 필드는 기존의 FQDN 형식과 Windows domain\hostname 형식을 둘 다 지원 합니다. [DvcDomainType](#dvcdomaintype) 필드에는 사용 된 형식이 반영 됩니다.  |
| <a name="dvcid"></a>**DvcId** | 선택 | 문자열 | 레코드에 보고 된 보고 또는 중간 장치의 ID입니다.<br><br>예제: `ac7e9755-8eae-4ffc-8a02-50ed7a2216c3` |
| **DvcIdType** | 선택 사항 | Enumerated | 알려진 경우 [Dvcid](#dvcid)의 유형입니다. 가능한 값은 다음과 같습니다.<br> - `AzureResourceId`<br>- `MDEid`<br><br>여러 Id를 사용할 수 있는 경우 목록에서 첫 번째 Id를 사용 하 고 필드 이름  **DvcAzureResourceId** 및 **Dvcmdeid** 를 각각 사용 하 여 다른 id를 저장 합니다.<br><br>**참고**:이 필드는 [dvcid](#dvcid) 필드가 사용 되는 경우에 필요 합니다. |
| **AdditionalFields** | 선택 사항 | 동적 | 원본에서 보존할 추가 정보를 제공 하는 경우 원래 필드 이름으로 유지 하거나 동적  **Additionalfields**  필드를 만들고 추가 정보를 키/값 쌍으로 추가 합니다. |
| | | | |

### <a name="network-session-fields"></a>네트워크 세션 필드

다음 필드는 모든 네트워크 세션 활동 로깅에 공통적으로 수행 됩니다.

| 필드 | 클래스 | 형식 | 설명 |
|-------|-------|------|-------------|
|<a name="dstipaddr"></a> **DstIpAddr** | 권장 | IP 주소 | 연결 또는 세션 대상의 IP 주소입니다. <br><br>예: `2001:db8::ff00:42:8329`<br><br>**참고**: [DstHostname](#dsthostname) 가 지정 된 경우이 값은 필수입니다. |
| <a name="dstportnumber"></a>**DstPortNumber** | 선택 | 정수 | 대상 IP 포트<br><br>예: `443` |
| <a name="dsthostname"></a>**DstHostname** | 권장 | String | 대상 장치 호스트 이름 (도메인 정보 제외) 사용할 수 있는 장치 이름이 없으면이 필드에 관련 IP 주소를 저장 합니다.<br><br>예: `DESKTOP-1282V4D`<br><br>**참고**: [DstIpAddr](#dstipaddr) 가 지정 된 경우이 값은 필수입니다. |
| **호스트 이름** | Alias | | [DstHostname](#dsthostname) 에 대 한 별칭 |
| <a name="dstdomain"></a>**DstDomain** | 권장 | String | 대상 장치의 도메인입니다.<br><br>예: `Contoso` |
| <a name="dstdomaintype"></a>**DstDomainType** | 권장 | Enumerated | [Dstdomain](#dstdomain)의 유형 (알려진 경우)입니다. 가능한 값은 다음과 같습니다.<br>- `Windows (contoso\mypc)`<br>- `FQDN (docs.microsoft.com)`<br><br>[Dstdomain](#dstdomain) 을 사용 하는 경우 필수 사항입니다. |
| **DstFQDN** | 선택 | 문자열 | 사용 가능한 경우 도메인 정보를 포함 하는 대상 장치 호스트 이름 <br><br>예: `Contoso\DESKTOP-1282V4D` <br><br>**참고**:이 필드는 기존의 FQDN 형식 및 Windows domain\hostname 형식을 모두 지원 합니다. [DstDomainType](#dstdomaintype) 는 사용 되는 형식을 반영 합니다.   |
| <a name="dstdvcid"></a>**DstDvcId** | 선택 | 문자열 | 레코드에 보고 된 대상 장치의 ID입니다.<br><br>예: `ac7e9755-8eae-4ffc-8a02-50ed7a2216c3` |
| **DstDvcIdType** | 선택 사항 | Enumerated | [Dstdvcid](#dstdvcid)의 유형입니다 (알려진 경우). 가능한 값은 다음과 같습니다.<br> - `AzureResourceId`<br>- `MDEidIf`<br><br>여러 Id를 사용할 수 있는 경우 위의 목록에서 첫 번째 Id를 사용 하 고  **DstDvcAzureResourceId** 또는 **Dstdvcmdeid** 필드에 각각 다른 id를 저장 합니다.<br><br>**Dstdeviceid** 를 사용 하는 경우 필수 사항입니다.|
| **DstDeviceType** | 선택 사항 | Enumerated | 대상 장치의 유형입니다. 가능한 값은 다음과 같습니다.<br>- `Computer`<br>- `Mobile Device`<br>- `IOT Device`<br>- `Other` |
| <a name="dstuserid"></a>**DstUserId** | 선택 | 문자열 | 대상 사용자의 컴퓨터에서 읽을 수 있는 영숫자 고유 표현입니다. <br><br>지원 되는 형식 및 유형은 다음과 같습니다.<br>- **SID** (Windows):`S-1-5-21-1377283216-344919071-3415362939-500`<br>- **UID**  (Linux): `4578`<br>-  **AADID** (Azure Active Directory):`9267d02c-5f76-40a9-a9eb-b686f3ca47aa`<br>-  **OktaId**: `00urjk4znu3BcncfY0h7`<br>-  **AWSId**: `72643944673`<br><br>[Dstuseridtype](#dstuseridtype) 필드에 ID 유형을 저장 합니다. 다른 Id를 사용할 수 있는 경우 필드 이름을 **Dstusersid**, **dstuseruid**, **DstUserAADID**, **Dstuseroktaid** 및 **userawsid** 로 각각 정규화 하는 것이 좋습니다. 자세한 내용은 [사용자 엔터티](normalization-about-schemas.md#the-user-entity)를 참조하세요.<br><br>예: `S-1-12` |
| <a name="dstuseridtype"></a>**DstUserIdType** | 선택 사항 | Enumerated | [Dstuserid](#dstuserid) 필드에 저장 된 ID의 유형입니다. <br><br>지원 되는 값은 `SID` ,, `UIS` `AADID` , `OktaId` 및 `AWSId` 입니다. |
| <a name="dstusername"></a>**DstUsername** | 선택 | 문자열 | 사용 가능한 경우 도메인 정보를 포함 하는 대상 사용자 이름입니다. <br><br>다음 형식 중 하나를 사용 하 고 우선 순위를 지정 합니다.<br>- **Upn/메일**: `johndow@contoso.com`<br>- **Windows**: `Contoso\johndow`<br>- **DN**: `CN=Jeff Smith,OU=Sales,DC=Fabrikam,DC=COM`<br>- **단순**: `johndow`. 도메인 정보를 사용할 수 없는 경우에만 간단한 양식을 사용 합니다.<br><br>[DstUsernameType](#dstusernametype) 필드에 사용자 이름 유형을 저장 합니다. 다른 Id를 사용할 수 있는 경우 필드 이름을 **Dstuserupn**, **dstuserupn** 및 **DstUserDn** 으로 정규화 하는 것이 좋습니다. 자세한 내용은 [사용자 엔터티](normalization-about-schemas.md#the-user-entity)를 참조 하세요.<br><br>예: `AlbertE` |
| **사용자** | Alias | | [Dstusername](#dstusername) 에 대 한 별칭 |
| <a name="dstusernametype"></a>**DstUsernameType** | 선택 사항 | Enumerated | [Dstusername](#dstusername) 필드에 저장 된 사용자 이름 유형을 지정 합니다. 지원 되는 값은 `UPN` ,, `Windows` `DN` 및 `Simple` 입니다. 자세한 내용은 [사용자 엔터티](normalization-about-schemas.md#the-user-entity)를 참조하세요.<br><br>예: `Windows` |
| **DstUserType** | 선택 사항 | Enumerated | Actor의 형식입니다. 지원되는 값은 다음과 같습니다.<br>- `Regular`<br>- `Machine`<br>- `Admin`<br>- `System`<br>- `Application`<br>- `Service Principal`<br>- `Other`<br><br>**참고**: 값은 이러한 값으로 정규화되어야 하는 다른 조건을 사용하여 원본 레코드에 제공될 수 있습니다. [DstOriginalUserType](#dstoriginalusertype) 필드에 원래 값을 저장 합니다. |
| <a name="dstoriginalusertype"></a>**DstOriginalUserType** | 선택 | 문자열 | 원본에서 제공 하는 경우 원래 대상 사용자 형식입니다. |
| **DstUserDomain** | 선택 | 문자열 | 이 필드는 이전 버전과의 호환성을 위해서만 유지됩니다. ASIM을 사용하려면 도메인 정보(사용 가능한 [경우)가 DstUsername](#dstusername) 필드의 일부가 되어야 합니다. |
| <a name="dstappname"></a>**DstAppName** | 선택 | 문자열 | 대상 애플리케이션의 이름입니다.<br><br>예: `Facebook` |
| <a name="dstappid"></a>**DstAppId** | 선택 | 문자열 | 보고 디바이스에서 보고한 대상 애플리케이션의 ID입니다.<br><br>예: `124` |
| **DstAppType** | 선택 | 문자열 | 작업자를 대신하여 권한을 부여하는 애플리케이션의 형식입니다. 지원되는 값은 다음과 같습니다.<br>- `Process`<br>- `Service`<br>- `Resource`<br>- `URL`<br>- `SaaS application`<br>- `Other`<br><br>[DstAppName](#dstappname) 또는 [DstAppId를](#dstappid) 사용하는 경우 이 필드는 필수입니다. |
| **DstZone** | 선택 | 문자열 | 보고 디바이스에 정의된 대상의 네트워크 영역<br><br>예: `Dmz` |
| **DstInterfaceName** | 선택 | 문자열 | 대상 디바이스의 연결 또는 세션에 사용되는 네트워크 인터페이스<br><br>예: `Microsoft Hyper-V Network Adapter` |
| **DstInterfaceGuid** | 선택 | 문자열 | 대상 디바이스에서 사용되는 네트워크 인터페이스의 GUID입니다.<br><br>예제:<br>`46ad544b-eaf0-47ef-`<br>`827c-266030f545a6` |
| **DstMacAddr** | 선택 | 문자열 | 대상 디바이스의 연결 또는 세션에 사용되는 네트워크 인터페이스의 MAC 주소입니다.<br><br>예: `06:10:9f:eb:8f:14` |
| **DstGeoCountry** | 선택 사항 | 국가 | 대상 IP 주소와 연결된 국가입니다. 자세한 내용은 [논리적 형식](normalization-about-schemas.md#logical-types)을 참조하세요.<br><br>예: `USA` |
| **DstGeoRegion** | 선택 사항 | 지역 | 대상 IP 주소와 연결된 국가 내의 지역 또는 주입니다. 자세한 내용은 [논리적 형식](normalization-about-schemas.md#logical-types)을 참조하세요.<br><br>예: `Vermont` |
| **DstGeoCity** | 선택 사항 | City | 대상 IP 주소와 연결된 도시입니다. 자세한 내용은 [논리적 형식](normalization-about-schemas.md#logical-types)을 참조하세요.<br><br>예: `Burlington` |
| **DstGeoLatitude** | 선택 사항 | 위도 | 대상 IP 주소와 연결된 지리적 좌표의 위도입니다. 자세한 내용은 [논리적 형식](normalization-about-schemas.md#logical-types)을 참조하세요.<br><br>예: `44.475833` |
| **DstGeoLongitude** | 선택 사항 | 경도 | 대상 IP 주소와 연결된 지리적 좌표의 경도입니다. 자세한 내용은 [논리적 형식](normalization-about-schemas.md#logical-types)을 참조하세요.<br><br>예: `73.211944` |
| <a name="srcipaddr"></a>**SrcIpAddr** | 권장 | IP 주소 | 연결 또는 세션이 시작된 IP 주소 **SrcHostname이** 지정된 경우 이 값은 필수입니다.<br><br>예: `77.138.103.108` |
| **IpAddr** | Alias | | [SrcIpAddr에](#srcipaddr) 대한 별칭 |
| **SrcPortNumber** | 선택 | 정수 | 연결이 시작된 IP 포트. 여러 연결을 구성하는 세션과 관련이 없을 수 있습니다.<br><br>예: `2335` |
| **SrcHostname** | 권장 | String | 도메인 정보를 제외한 원본 디바이스 호스트 이름입니다. 사용할 수 있는 디바이스 이름이 없는 경우 이 필드에 관련 IP 주소를 저장합니다. [SrcIpAddr가](#srcipaddr) 지정된 경우 이 값은 필수입니다.<br><br>예: `DESKTOP-1282V4D` |
|<a name="srcdomain"></a>**SrcDomain** | 권장 | String | 원본 디바이스의 도메인입니다.<br><br>예: `Contoso` |
| <a name="srcdomaintype"></a>**SrcDomainType** | 권장 | Enumerated | 알려진 경우 [SrcDomain 의 형식입니다.](#srcdomain) 가능한 값은 다음과 같습니다.<br>- `Windows` (예: `contoso` )<br>- `FQDN` (예: `microsoft.com` )<br><br>[Srcdomain](#srcdomain) 을 사용 하는 경우 필수 사항입니다. |
| **SrcFQDN** | 선택 | 문자열 | 사용 가능한 경우 도메인 정보를 포함한 원본 디바이스 호스트 이름입니다. <br><br>**참고**:이 필드는 기존의 FQDN 형식 및 Windows domain\hostname 형식을 모두 지원 합니다. [SrcDomainType](#srcdomaintype) 필드에는 사용 된 형식이 반영 됩니다. <br><br>예: `Contoso\DESKTOP-1282V4D` |
| <a name="srcdvcid"></a>**SrcDvcId** | 선택 | 문자열 | 레코드에 보고된 원본 디바이스의 ID입니다.<br><br>예: `ac7e9755-8eae-4ffc-8a02-50ed7a2216c3` |
| **SrcDvcIdType** | 선택 사항 | Enumerated | [Srcdvcid](#srcdvcid)의 유형입니다 (알려진 경우). 가능한 값은 다음과 같습니다.<br> - `AzureResourceId`<br>- `MDEid`<br><br>여러 Id를 사용할 수 있는 경우 위의 목록에서 첫 번째 Id를 사용 하 고 다른 Id를 **SrcDvcAzureResourceId** 및 **Srcdvcmdeid** 에 각각 저장 합니다.<br><br>**참고**:이 필드는 [srcdvcid](#srcdvcid) 를 사용 하는 경우에 필요 합니다. |
| **SrcDeviceType** | 선택 사항 | Enumerated | 원본 디바이스의 형식입니다. 가능한 값은 다음과 같습니다.<br>- `Computer`<br>- `Mobile Device`<br>- `IOT Device`<br>- `Other` |
| <a name="srcuserid"></a>**SrcUserId** | 선택 | 문자열 | 원본 사용자의 컴퓨터에서 읽을 수 있는 영숫자 고유 표현입니다. 형식 및 지원되는 형식은 다음과 같습니다.<br>-  **SID** (Windows):`S-1-5-21-1377283216-344919071-3415362939-500`<br>-  **UID**  (Linux): `4578`<br>-  **AADID** (Azure Active Directory):`9267d02c-5f76-40a9-a9eb-b686f3ca47aa`<br>-  **OktaId**: `00urjk4znu3BcncfY0h7`<br>-  **AWSId**: `72643944673`<br><br>[Srcuseridtype](#srcuseridtype) 필드에 ID 유형을 저장 합니다. 다른 Id를 사용할 수 있는 경우 필드 이름을 SrcUserSid, SrcUserUid, SrcUserAadId, SrcUserOktaId 및 UserAwsId로 각각 정규화 하는 것이 좋습니다. 자세한 내용은 사용자 엔터티를 참조 하세요.<br><br>예: S-1-12 |
| <a name="srcuseridtype"></a>**SrcUserIdType** | 선택 사항 | Enumerated | [Srcuserid](#srcuserid) 필드에 저장 된 ID의 유형입니다. 지원 되는 값은 `SID` ,, `UIS` `AADID` , `OktaId` 및 `AWSId` 입니다. |
| <a name="srcusername"></a>**SrcUsername** | 선택 | 문자열 | 사용 가능한 경우 도메인 정보를 포함 하는 원본 사용자 이름입니다. 다음 형식 중 하나를 사용 하 고 우선 순위를 지정 합니다.<br>- **Upn/메일**: `johndow@contoso.com`<br>- **Windows**: `Contoso\johndow`<br>- **DN**: `CN=Jeff Smith,OU=Sales,DC=Fabrikam,DC=COM`<br>- **단순**: `johndow`. 도메인 정보를 사용할 수 없는 경우에만 간단한 양식을 사용 합니다.<br><br>[SrcUsernameType](#srcusernametype) 필드에 사용자 이름 유형을 저장 합니다. 다른 Id를 사용할 수 있는 경우 필드 이름을 **Srcuserupn**, **srcuserupn** 및 **SrcUserDn** 으로 정규화 하는 것이 좋습니다.<br><br>자세한 내용은 [사용자 엔터티](normalization-about-schemas.md#the-user-entity)를 참조하세요.<br><br>예: `AlbertE` |
| <a name="srcusernametype"></a>**SrcUsernameType** | 선택 사항 | Enumerated | [Srcusername](#srcusername) 필드에 저장 된 사용자 이름 유형을 지정 합니다. 지원 되는 값은 `UPN` ,, `Windows` `DN` 및 `Simple` 입니다. 자세한 내용은 [사용자 엔터티](normalization-about-schemas.md#the-user-entity)를 참조하세요.<br><br>예: `Windows` |
| **SrcUserType** | 선택 사항 | Enumerated | Actor의 형식입니다. 허용된 값은<br>- `Regular`<br>- `Machine`<br>- `Admin`<br>- `System`<br>- `Application`<br>- `Service Principal`<br>- `Other`<br><br>**참고**: 값은 이러한 값으로 정규화되어야 하는 다른 조건을 사용하여 원본 레코드에 제공될 수 있습니다. [Eventoriginalseverity](#eventoriginalseverity) 필드에 원래 값을 저장 합니다. |
| **SrcOriginalUserType** | | | 원본에서 제공 하는 경우 원래 원본 사용자 형식입니다. |
| **SrcUserDomain** | 선택 | 문자열 | 이 필드는 이전 버전과의 호환성을 위해서만 유지 됩니다. ASIM을 사용 하려면 사용 가능한 경우 도메인 정보를 [Srcusername](#srcusername) 필드에 포함 해야 합니다. |
| <a name="srcappname"></a>**SrcAppName** | 선택 | 문자열 | 원본 응용 프로그램의 이름입니다. <br><br>예: `filezilla.exe` |
| <a name="srcappid"></a>**SrcAppId** | 선택 | 문자열 | 보고 장치에서 보고 하는 대상 응용 프로그램의 ID입니다.<br><br>예: `124` |
| **SrcAppType** | 선택 | 문자열 | 원본 응용 프로그램의 형식입니다. 지원되는 값은 다음과 같습니다.<br>- `Process`<br>- `Service`<br>- `Resource`<br>- `Other`<br><br>이 필드는 [Srcappname](#srcappname) 또는 [srcappname](#srcappid) 를 사용 하는 경우 필수입니다. |
| **SrcZone** | 선택 | 문자열 | 보고 디바이스에서 정의한 원본 네트워크 영역<br><br>예: `Internet` |
| **SrcIntefaceName** | 선택 | 문자열 | 원본 디바이스에서 연결 또는 세션에 사용하는 네트워크 인터페이스 <br><br>예: `eth01` |
| **SrcInterfaceGuid** | 선택 | 문자열 | 원본 디바이스에서 사용되는 네트워크 인터페이스의 GUID입니다.<br><br>예제:<br>`46ad544b-eaf0-47ef-`<br>`827c-266030f545a6` |
| **SrcMacAddr** | 선택 | 문자열 | 연결 또는 세션이 시작된 네트워크 인터페이스의 MAC 주소입니다.<br><br>예: `06:10:9f:eb:8f:14` |
| **SrcGeoCountry** | 선택 사항 | 국가 | 원본 IP 주소와 연결된 국가입니다.<br><br>예: `USA` |
| **SrcGeoRegion** | 선택 사항 | 지역 | 원본 IP 주소와 연결된 국가 내의 지역입니다.<br><br>예: `Vermont` |
| **SrcGeoCity** | 선택 사항 | City | 원본 IP 주소와 연결된 도시입니다.<br><br>예: `Burlington` |
| **SrcGeoLatitude** | 선택 사항 | 위도 | 원본 IP 주소와 연결된 지리적 좌표의 위도입니다.<br><br>예: `44.475833` |
| **SrcGeoLongitude** | 선택 사항 | 경도 | 원본 IP 주소와 연결된 지리적 좌표의 경도입니다.<br><br>예: `73.211944` |
| **NetworkApplicationProtocol** | 선택 | 문자열 | 연결 또는 세션에서 사용하는 애플리케이션 계층 프로토콜입니다. [DstPortNumber](#dstportnumber) 값이 제공되는 경우 **NetworkApplicationProtocol도** 포함하는 것이 좋습니다. 원본에서 값을 사용할 수 없는 경우 [DstPortNumber](#dstportnumber) 값에서 값을 파생합니다.<br><br>예: `HTTP` |
| **NetworkProtocol** | 선택 사항 | Enumerated | [IANA](https://www.iana.org/assignments/protocol-numbers/protocol-numbers.xhtml)프로토콜 할당 에 나열된 연결 또는 세션에서 사용하는 IP 프로토콜입니다. 일반적으로 `TCP` , `UDP` 또는 `ICMP` 입니다.<br><br>예: `TCP` |
| **NetworkDirection** | 선택 사항 | Enumerated | 조직 내/외부 연결 또는 세션의 방향입니다. 지원되는 값은 `Inbound` , `Outbound` , `Listen` 입니다. `Listen` 는 디바이스가 네트워크 연결을 수락하기 시작했지만 실제로 반드시 연결된 것은 아님을 나타냅니다.|
| <a name="networkduration"></a>**NetworkDuration** | 선택 | 정수 | 네트워크 세션 또는 연결을 완료하는 데 소요되는 시간(밀리초)입니다.<br><br>예: `1500` |
| **기간** | Alias | | [NetworkDuration에](#networkduration) 대한 별칭 |
| **NetworkIcmpCode** | 선택 | 정수 | ICMP 메시지의 경우 ICMP 메시지는 IPv4 네트워크 연결의 경우 [RFC 2780](https://datatracker.ietf.org/doc/html/rfc2780) 또는 IPv6 네트워크 연결의 경우 [RFC 4443에](https://datatracker.ietf.org/doc/html/rfc4443) 설명된 대로 숫자 값을 입력합니다. [NetworkIcmpType](#networkicmptype) 값이 제공된 경우 이 필드는 필수입니다. 값을 원본에서 사용할 수 없는 경우  [NetworkIcmpType](#networkicmptype) 필드에서 값을 대신 파생합니다.<br><br>예: `34` |
|<a name="networkicmptype"></a> **NetworkIcmpType** | 선택 | 문자열 | ICMP 메시지의 경우 IPv4 네트워크 연결의 경우 [RFC 2780](https://datatracker.ietf.org/doc/html/rfc2780) 또는 IPv6 네트워크 연결의 경우 [RFC 4443에](https://datatracker.ietf.org/doc/html/rfc4443) 설명된 대로 ICMP 메시지 형식 텍스트 표현입니다.<br><br>예: `Destination Unreachable` |
| **DstBytes** | 권장 | 정수 | 연결 또는 세션의 대상에서 원본으로 보낸 바이트 수 이벤트가 집계되는 경우 **DstBytes는** 집계된 모든 세션의 합계여야 합니다.<br><br>예: `32455` |
| **SrcBytes** | 권장 | 정수 | 연결 또는 세션의 원본에서 대상으로 보낸 바이트 수 이벤트가 집계되는 경우 **SrcBytes는** 집계된 모든 세션의 합계여야 합니다.<br><br>예: `46536` |
| **NetworkBytes** | 선택 | 정수 | 양방향으로 전송된 바이트 수. **BytesReceived와** **BytesSent가** 모두 있는 경우 **BytesTotal은** 해당 합계와 같아야 합니다. 이벤트가 집계되는 경우 **NetworkBytes는** 집계된 모든 세션의 합계여야 합니다.<br><br>예: `78991` |
| **DstPackets** | 선택 | 정수 | 연결 또는 세션의 대상에서 원본으로 전송된 패킷 수입니다. 패킷의 의미는 보고 디바이스에 의해 정의됩니다. 이벤트가 집계되는 경우 **DstPackets는** 집계된 모든 세션의 합계여야 합니다.<br><br>예: `446` |
| **SrcPackets** | 선택 | 정수 | 연결 또는 세션 원본에서 대상으로 보낸 패킷 수입니다. 패킷의 의미는 보고 디바이스에 의해 정의됩니다. 이벤트가 집계되는 경우 **SrcPackets는** 집계된 모든 세션의 합계여야 합니다.<br><br>예: `6478` |
| **NetworkPackets** | 선택 | 정수 | 양방향으로 전송된 패킷 수입니다. **PacketsReceived와** **PacketsSent가** 모두 있는 경우 **BytesTotal은** 해당 합계와 같아야 합니다. 패킷의 의미는 보고 디바이스에 의해 정의됩니다. 이벤트가 집계되는 경우 **NetworkPackets는** 집계된 모든 세션의 합계여야 합니다.<br><br>예: `6924` |
|<a name="networksessionid"></a>**NetworkSessionId** | 선택 | 문자열 | 보고 디바이스에서 보고한 세션 식별자 <br><br>예: `172\_12\_53\_32\_4322\_\_123\_64\_207\_1\_80` |
| **SessionId** | Alias | String | [NetworkSessionId에](#networksessionid) 대한 별칭 |
| | | | |

### <a name="intermediary-device-fields"></a><a name="Intermediary"></a>중간 디바이스 필드

다음 필드는 네트워크 세션을 릴레이하는 방화벽 또는 프록시와 같은 중간 디바이스에 대한 정보가 레코드에 포함된 경우에 유용합니다.

| 필드 | 클래스 | 형식 | 설명 |
| --- | --- | --- | --- |
| **DstNatIpAddr** | 선택 사항 | IP 주소 | 중간 NAT 디바이스에서 보고하는 경우 NAT 디바이스에서 원본과의 통신에 사용하는 IP 주소입니다.<br><br>예: `2::1` |
| **DstNatPortNumber** | 선택 | 정수 | 중간 NAT 디바이스에서 보고하는 경우 NAT 디바이스에서 원본과의 통신에 사용하는 포트입니다.<br><br>예: `443` |
| **SrcNatIpAddr** | 선택 사항 | IP 주소 | 중간 NAT 디바이스에서 보고하는 경우 NAT 디바이스에서 대상과의 통신에 사용하는 IP 주소입니다.<br><br>예: `4.3.2.1` |
| **SrcNatPortNumber** | 선택 | 정수 | 중간 NAT 디바이스에서 보고하는 경우 NAT 디바이스에서 대상과의 통신에 사용하는 포트입니다.<br><br>예: `345` |
| **DvcInboundInterface** | 선택 | 문자열 | 중간 디바이스에서 보고하는 경우 NAT 디바이스에서 원본 디바이스에 연결하기 위해 사용하는 네트워크 인터페이스입니다.<br><br>예: `eth0` |
| **DvcOutboundInterface** | 선택 | 문자열 | 중간 디바이스에서 보고하는 경우 NAT 디바이스에서 대상 디바이스에 연결하기 위해 사용하는 네트워크 인터페이스입니다.<br><br>예: `Ethernet adapter Ethernet 4e` |
| | | | |

### <a name="http-session-fields"></a><a name="http-session-fields"></a>HTTP 세션 필드

HTTP 세션은 HTTP 프로토콜을 사용하는 네트워크 세션입니다. 이러한 세션은 웹 서버, 웹 프록시 및 웹 보안 게이트웨이에서 보고되는 경우가 많습니다. HTTP 세션과 관련한 추가 필드는 다음과 같습니다.

| 필드 | 클래스 | 형식 | 설명 |
| --- | --- | --- | --- |
| **Url** | 권장 | String | HTTP/HTTPS 네트워크 세션의 경우 매개 변수를 포함한 전체 HTTP 요청 URL입니다. 이 필드는 이벤트가 HTTP 세션을 나타내는 경우 필수입니다.<br><br>예: `https://contoso.com/fo/?k=v&amp;q=u#f` |
| **UrlCategory** | 선택 | 문자열 | URL 또는 URL의 도메인 부분에 대해 정의된 그룹화입니다. 범주는 일반적으로 웹 보안 게이트웨이에서 제공되며 URL이 가리키는 사이트의 콘텐츠를 기반으로 합니다.<br><br>예: 검색 엔진, 성인, 뉴스, 광고 및 파킹된 도메인 |
| **UrlOriginal** | 선택 | 문자열 | URL이 보고 디바이스에 의해 수정되고 두 값이 모두 제공된 경우 URL의 원래 값입니다. |
| **HttpVersion** | 선택 | 문자열 | HTTP/HTTPS 네트워크 연결의 HTTP 요청 버전<br><br>예: `2.0` |
| **HttpRequestMethod** | 권장 | Enumerated | HTTP/HTTPS 네트워크 세션의 HTTP 메서드 값은 [RFC 7231](https://datatracker.ietf.org/doc/html/rfc7231#section-4) 및 [RFC 5789](https://datatracker.ietf.org/doc/html/rfc5789#section-2)에 정의된 대로 이며 `GET` , , , 및 `HEAD` 를 `POST` `PUT` `DELETE` `CONNECT` `OPTIONS` `TRACE` `PATCH` 포함합니다.<br><br>예: `GET` |
| **HttpStatusCode** | Alias | | HTTP/HTTPS 네트워크 세션의 HTTP 상태 코드 [EventResultDetails 에 대한 별칭입니다.](#eventresultdetails) |
| <a name="httpcontenttype"></a>**HttpContentType** | 선택 | 문자열 | HTTP/HTTPS 네트워크 세션의 HTTP 응답 콘텐츠 형식 헤더 <br><br>**참고:** **HttpContentType** 필드에는 콘텐츠 형식과 실제 형식을 얻는 데 사용되는 인코딩과 같은 추가 매개 변수가 모두 포함될 수 있습니다.<br><br> 예: `text/html; charset=ISO-8859-4` |
| **HttpContentFormat** | 선택 | 문자열 | [HttpContentType의](#httpcontenttype) 콘텐츠 형식 부분입니다. <br><br> 예: `text/html` |
| **HttpReferrer** | 선택 | String | HTTP/HTTPS 네트워크 세션의 HTTP 참조 페이지 헤더<br><br>**참고:** ASIM은 OSSEM과 동기화되어 원래 HTTP 헤더 맞춤법이 아닌 *참조 관리자* 에 올바른 철자를 사용합니다.<br><br>예: `https://developer.mozilla.org/docs` |
| <a name="httpuseragent"></a>**HttpUserAgent** | 선택 | 문자열 | HTTP/HTTPS 네트워크 세션의 HTTP 사용자 에이전트 헤더<br><br>예제:<br> `Mozilla/5.0`(Windows NT 10.0; WOW64)<br>`AppleWebKit/537.36` (Gecko와 같은 KHTML)<br>`Chrome/83.0.4103.97 Safari/537.36` |
| **UserAgent** | Alias | | 별칭을 [HttpUserAgent로](#httpuseragent) |
| **HttpRequestXff** | 선택 사항 | IP 주소 | HTTP/HTTPS 네트워크 세션에 대한 HTTP X-Forwarded-For 헤더<br><br>예: `120.12.41.1` |
| **HttpRequestTime** | 선택 | 정수 | 해당하는 경우 서버에 요청을 보내는 데 걸린 시간(밀리초)입니다.<br><br>예: `700` |
| **HttpResponseTime** | 선택 | 정수 | 해당하는 경우 서버에서 응답을 받는 데 걸린 시간(밀리초)입니다.<br><br>예: `800` |
| **FileName** | 선택 | 문자열 | HTTP 업로드의 경우 업로드된 파일의 이름입니다. |
| **FileMD5** | 선택 사항 | MD5 | HTTP 업로드의 경우 업로드된 파일의 MD5 해시입니다.<br><br>예: `75a599802f1fa166cdadb360960b1dd0` |
| **FileSHA1** | 선택 사항 | SHA1 | HTTP 업로드의 경우 업로드된 파일의 SHA1 해시입니다.<br><br>예제:<br>`d55c5a4df19b46db8c54`<br>`c801c4665d3338acdab0` |
| **FileSHA256** | 선택 사항 | SHA256 | HTTP 업로드의 경우 업로드된 파일의 SHA256 해시입니다.<br><br>예제:<br>`e81bb824c4a09a811af17deae22f22dd`<br>`2e1ec8cbb00b22629d2899f7c68da274` |
| **FileSHA512** | 선택 사항 | SHA512 | HTTP 업로드의 경우 업로드된 파일의 SHA512 해시입니다. |
| **FileSize** | 선택 | 정수 | HTTP 업로드의 경우 업로드된 파일의 크기(바이트)입니다. |
| **FileContentType** | 선택 | 문자열 | HTTP 업로드의 경우 업로드된 파일의 콘텐츠 형식입니다. |
| | | | |

또한 다음 표준 네트워킹 스키마 필드에는 HTTP 세션에 사용되는 경우 추가 지침이 있습니다.

- **EventType은** &quot; HTTP 세션이어야 합니다.&quot;
- **EventResultDetails는** HTTP 상태 코드로 설정해야 합니다.
- **EventResult는** &quot; &quot; HTTP 상태 코드가 400보다 낮으면 Success이고, &quot; 그렇지 않으면 Failure여야 &quot; 합니다.

### <a name="inspection-fields"></a><a name="inspection-fields"></a>검사 필드

다음 필드는 방화벽, IPS 또는 웹 보안 게이트웨이와 같은 보안 디바이스가 수행한 검사를 나타내는 데 사용됩니다.

| 필드 | 클래스 | 형식 | 설명 |
| --- | --- | --- | --- |
| **NetworkRuleName** | 선택 | 문자열 | [DvcAction이](#dvcaction) 결정된 규칙의 이름 또는 ID입니다.<br><br> 예: `AnyAnyDrop` |
| **NetworkRuleNumber** | 선택 | 정수 | [DvcAction이](#dvcaction) 결정된 규칙의 수입니다.<br><br>예: `23` |
| **규칙** | 필수 | String | `NetworkRuleName` 또는 `NetworkRuleNumber` |
| <a name="dvcaction"></a>**DvcAction** | 선택 사항 | Enumerated | 네트워크 세션에서 수행된 작업입니다. 지원되는 값은 다음과 같습니다.<br>- `Allow`<br>- `Deny`<br>- `Drop`<br>- `Drop ICMP`<br>- `Reset`<br>- `Reset Source`<br>- `Reset Destination`<br>- `Encrypt`<br>- `Decrypt`<br>- `VPNroute`<br><br>**참고**: 값은 이러한 값으로 정규화되어야 하는 다른 조건을 사용하여 원본 레코드에 제공될 수 있습니다. 원래 값은 [DvcOriginalAction](#dvcoriginalaction) 필드에 저장되어야 합니다.<br><br>예: `drop` |
| <a name="dvcoriginalaction"></a>**DvcOriginalAction** | 선택 | 문자열 | 보고 디바이스에서 제공하는 원래 [DvcAction입니다.](#dvcaction) |
| **ThreatId** | 선택 | 문자열 | 네트워크 세션에서 식별된 위협 또는 맬웨어의 ID입니다.<br><br>예: `Tr.124` |
| **ThreatName** | 선택 | 문자열 | 네트워크 세션에서 식별된 위협 또는 맬웨어의 이름입니다.<br><br>예: `EICAR Test File` |
| **ThreatCategory** | 선택 | 문자열 | 네트워크 세션에서 식별된 위협 또는 맬웨어의 범주입니다.<br><br>예: `Trojan` |
| **ThreatRiskLevel** | 선택 | 정수 | 세션과 연결된 위험 수준입니다. 수준은 **0에서 100** 사이의 숫자여야 합니다.<br><br>**참고:** 값은 이 크기 조정으로 정규화되어야 하는 다른 배율로 원본 레코드에 제공될 수 있습니다. 원래 값은 [ThreatRiskLevelOriginal](#threatriskleveloriginal)에 저장되어야 합니다. |
| <a name="threatriskleveloriginal"></a>**ThreatRiskLevelOriginal** | 선택 | 문자열 | 보고 디바이스에서 보고한 위험 수준입니다. |
| | | | |

### <a name="other-fields"></a>기타 필드

이벤트가 네트워크 세션의 엔드포인트 중 하나에서 보고되는 경우 세션을 시작하거나 종료한 프로세스에 대한 정보가 포함될 수 있습니다. 이러한 경우 [ASIM 프로세스 이벤트 스키마를](process-events-normalization-schema.md) 사용하여 이 정보를 정규화합니다.

## <a name="next-steps"></a>다음 단계

자세한 내용은 다음을 참조하세요.

- [Microsoft Sentinel의 정규화](normalization.md)
- [Microsoft Sentinel 인증 정규화 스키마 참조(공개 미리 보기)](authentication-normalization-schema.md)
- [Microsoft Sentinel 파일 이벤트 정규화 스키마 참조(공개 미리 보기)](file-event-normalization-schema.md)
- [Microsoft Sentinel DNS 정규화 스키마 참조](dns-normalization-schema.md)
- [Microsoft Sentinel 프로세스 이벤트 정규화 스키마 참조](process-events-normalization-schema.md)
- [Microsoft Sentinel 레지스트리 이벤트 정규화 스키마 참조(공개 미리 보기)](registry-event-normalization-schema.md)
