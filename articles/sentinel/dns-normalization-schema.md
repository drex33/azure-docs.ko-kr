---
title: Azure Sentinel DNS 정규화 스키마 참조 | Microsoft Docs
description: 이 문서에서는 Azure Sentinel DNS 정규화 스키마에 대해 설명합니다.
services: sentinel
cloud: na
documentationcenter: na
author: batamig
manager: rkarlin
ms.assetid: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: reference
ms.date: 06/15/2021
ms.author: bagol
ms.openlocfilehash: 38f90522a465f6934c88249be3eae2b505c99303
ms.sourcegitcommit: d43193fce3838215b19a54e06a4c0db3eda65d45
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/20/2021
ms.locfileid: "122568367"
---
# <a name="azure-sentinel-dns-normalization-schema-reference-public-preview"></a>Azure Sentinel DNS 정규화 스키마 참조(퍼블릭 미리 보기)

DNS 정보 모델은 DNS 서버 또는 DNS 보안 시스템에서 보고된 이벤트를 설명하는 데 사용되며 Azure Sentinel에서 원본 독립적 분석을 활성화하는 데 사용됩니다.

자세한 내용은 [정규화 및 ASIM(Azure Sentinel 정보 모델)](normalization.md)을 참조하세요.

> [!IMPORTANT]
> DNS 정규화 스키마는 현재 미리 보기 상태입니다. 해당 기능은 별도의 서비스 수준 계약 없이 제공되며, 프로덕션 작업에는 사용하지 않는 것이 좋습니다.
>
> [Azure Preview 추가 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)에는 베타, 미리 보기 또는 아직 일반 공급으로 릴리스되지 않은 Azure 기능에 적용되는 추가 법률 용어가 포함되어 있습니다.
>

## <a name="guidelines-for-collecting-dns-events"></a>DNS 이벤트 수집에 대한 지침

DNS는 다수의 컴퓨터를 교차할 수 있는 고유한 프로토콜입니다. 또한 DNS는 UDP를 사용하기 때문에 요청과 응답이 분리되며 서로 직접 관련되지 않습니다.

다음 이미지는 네 개의 세그먼트를 포함하여 간소화된 DNS 요청 흐름을 보여 줍니다. 실제 요청은 더 많은 세그먼트가 포함되어 더 복잡할 수 있습니다.

:::image type="content" source="media/normalization/dns-request-flow.png" alt-text="간소화된 DNS 요청 흐름.":::

요청 및 응답 세그먼트는 DNS 요청 흐름에서 서로 직접 연결되지 않으므로 전체 로깅은 상당한 중복을 초래할 수 있습니다.

로그할 가장 중요한 세그먼트는 클라이언트에 대한 응답으로 도메인 이름 쿼리, 조회 결과 및 클라이언트의 IP 주소를 제공합니다. 많은 DNS 시스템에서 이 세그먼트만 기록하지만 다른 파트를 로그하는 값이 있습니다. 예를 들어 DNS 캐시 손상 공격은 업스트림 서버의 가짜 응답을 활용하는 경우가 많습니다.

데이터 원본이 전체 DNS 로깅을 지원하고 여러 세그먼트를 기록하도록 선택한 경우 Azure Sentinel에서 데이터 중복을 방지하도록 쿼리를 조정해야 합니다.

예를 들어 다음과 같은 정규화를 사용하여 쿼리를 수정할 수 있습니다.

```kql
imDNS | where SrcIpAddr != "127.0.0.1" and EventSubType == "response"
```

## <a name="parsers"></a>파서

DNS 정보 모델을 구현하는 KQL 함수에는 다음과 같은 이름이 있습니다.

| Name | 설명 | 사용 지침 |
| --- | --- | --- |
| **imDNS** | *공용 구조체* 를 사용하여 모든 DNS 원본의 정규화된 이벤트를 포함하는 집계 파서입니다. |- 원본 독립적 분석에서 원본을 추가하거나 제거하려면 이 파서를 업데이트합니다. <br><br>- 원본 독립적 쿼리에서 이 함수를 사용합니다.|
| **imDNS\<vendor\>\<product\>** | 원본 관련 파서는 *imDNSWindowsOMS* 와 같은 특정 원본에 대한 정규화를 구현합니다. |- 기본 제공 정규화 파서가 없는 경우 원본용으로 원본에 독립적인 파서를 추가합니다. 새 파서의 참조를 포함하도록 집계 파서를 업데이트합니다. <br><br>- 구문 분석 및 정규화 문제를 해결하기 위해 원본 관련 파서를 업데이트합니다.<br><br>- 원본 관련 분석에 원본 관련 파서를 사용합니다.|
| | | |

파서는 [Azure Sentinel GitHub 리포지토리](https://github.com/Azure/Azure-Sentinel/tree/master/Parsers/ASimDns/ARM)에서 배포할 수 있습니다.

## <a name="normalized-content"></a>정규화된 콘텐츠

DNS ASIM 스키마 지원에는 정규화된 인증 파서가 있는 다음과 같은 기본 제공 분석 규칙에 대한 지원도 포함됩니다. Azure Sentinel GitHub 리포지토리에 대한 링크는 아래에 참조로 제공되는 한편, [ Analytics 규칙 갤러리](detect-threats-built-in.md)에서 이러한 규칙을 찾을 수도 있습니다. 연결된 GitHub 페이지를 사용하여 나열된 규칙에 대한 관련 헌팅 쿼리를 복사합니다.

다음 기본 제공 분석 규칙은 이제 정규화된 DNS 파서에서 작동합니다.
 - [과도한 NXDOMAIN DNS 쿼리(정규화된 DNS)](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/ASimDNS/imDns_ExcessiveNXDOMAINDNSQueries.yaml)
 - [마이닝 풀과 관련된 DNS 이벤트(정규화된 DNS)](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/ASimDNS/imDNS_Miners.yaml)
 - [ToR 프록시와 관련된 DNS 이벤트(정규화된 DNS)](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/ASimDNS/imDNS_TorProxies.yaml)
 - [알려진 Barium 도메인](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/MultipleDataSources/BariumDomainIOC112020.yaml)
 - [알려진 Barium IP 주소](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/MultipleDataSources/BariumIPIOC112020.yaml) 
 - [Exchange Server 취약점 공개 2021년 3월 IoC 매치](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/MultipleDataSources/ExchangeServerVulnerabilitiesMarch2021IoCs.yaml)
 - [알려진 GALLIUM 도메인 및 해시](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/MultipleDataSources/GalliumIOCs.yaml)
 - [알려진 IRIDIUM IP](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/MultipleDataSources/IridiumIOCs.yaml)
 - [NOBELIUM - 도메인 및 IP IOC - 2021년 3월](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/MultipleDataSources/NOBELIUM_DomainIOCsMarch2021.yaml)
 - [알려진 Phosphorus 그룹 도메인/IP](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/MultipleDataSources/PHOSPHORUSMarch2019IOCs.yaml)
 - [알려진 STRONTIUM 그룹 도메인 - 2019년 7월](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/MultipleDataSources/STRONTIUMJuly2019IOCs.yaml)
 - [Solorigate 네트워크 비콘](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/MultipleDataSources/Solorigate-Network-Beacon.yaml)
 - [DCU 무력화에 포함된 THALLIUM 도메인](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/MultipleDataSources/ThalliumIOCs.yaml)
 - [알려진 ZINC Comebacker 및 Klackring 맬웨어 해시](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/MultipleDataSources/ZincJan272021IOCs.yaml)



## <a name="schema-details"></a>스키마 세부 정보

DNS 정보 모델은 [OSSEM DNS 엔터티 스키마](https://github.com/OTRF/OSSEM/blob/master/docs/cdm/entities/dns.md)에 맞춰 정렬됩니다.

자세한 내용은 [IANA(Internet Assigned Numbers Authority) 도메인 이름 시스템 매개 변수 참조](https://www.iana.org/assignments/dns-parameters/dns-parameters.xhtml)를 참조하세요.

### <a name="log-analytics-fields"></a>Log Analytics 필드

다음 필드는 각 레코드에 대해 Log Analytics에서 생성되며 [사용자 지정 커넥터 생성](create-custom-connector.md) 시 재정의할 수 있습니다.

| **필드** | **형식** | **설명** |
| --- | --- | --- |
| <a name=timegenerated></a>**TimeGenerated** | 날짜/시간 | 보고 디바이스에서 이벤트가 생성된 시간입니다. |
| **\_ResourceId** | guid | 보고 디바이스 또는 서비스의 Azure 리소스 ID이거나 Syslog, CEF 또는 WEF를 사용하여 전달된 이벤트에 대한 로그 전달자 리소스 ID입니다. |
| | | |

> [!NOTE]
> 보안과 관련이 덜한 더 많은 Log Analytics 필드는 [Azure Monitor](../azure-monitor/logs/log-standard-columns.md)에 설명되어 있습니다.
> 

### <a name="event-fields"></a>이벤트 필드

이벤트 필드는 모든 스키마에 공통되며, 활동 자체와 보고 디바이스를 설명합니다.

| **필드** | **클래스** | **형식** | **예제** | **토론(Discussion)** |
| --- | --- | --- | --- | --- |
| **EventMessage** | 선택 | 문자열 | | 레코드에 포함되거나 레코드에서 생성된 일반 메시지 또는 설명입니다. |
| **EventCount** | 필수 | 정수 | `1` | 레코드에서 설명하는 이벤트 수입니다. <br><br>이 값은 원본에서 집계를 지원할 때 사용되며, 단일 레코드에서 여러 이벤트를 나타낼 수 있습니다. <br><br>다른 원본의 경우 **1** 로 설정되어야 합니다. |
| **EventStartTime** | 필수 | 날짜/시간 | | 원본에서 집계를 지원하고 레코드에서 여러 이벤트를 나타내는 경우 이 필드를 사용하여 첫 번째 이벤트가 생성된 시간을 지정합니다. <br><br>다른 경우에는 [TimeGenerated](#timegenerated) 필드의 별칭을 지정합니다. |
| **EventEndTime** | | Alias || [TimeGenerated](#timegenerated) 필드에 대한 별칭입니다. |
| **EventType** | 필수 | Enumerated | `lookup` | 레코드에서 보고하는 작업을 표시합니다. <br><Br> DNS 레코드의 경우 이 값은 [DNS 연산 코드](https://www.iana.org/assignments/dns-parameters/dns-parameters.xhtml)가 됩니다. |
| **EventSubType** | 선택 사항 | Enumerated || **request** 또는 **response** 입니다. 대부분의 원본에서는 [응답만 기록](#guidelines-for-collecting-dns-events)하므로 값은 종종 **response** 입니다.  |
| **EventResult** | 필수 | Enumerated | `Success` | **Success**, **Partial**, **Failure**, **NA**(해당 사항 없음) 값 중 하나입니다.<br> <br>값은 이러한 값으로 정규화되어야 하는 다른 조건을 사용하여 원본 레코드에 제공될 수 있습니다. 또는 원본에서 [EventResultDetails](#eventresultdetails) 필드만 제공할 수 있습니다. 이 필드는 EventResult 값을 도출하기 위해 분석되어야 합니다.<br> <br>이 레코드가 응답이 아닌 요청을 나타내는 경우 **NA** 로 설정합니다. |
| <a name=eventresultdetails></a>**EventResultDetails** | 필수 | Alias | `NXDOMAIN` | **_EventResult_** 필드에 보고된 결과에 대한 이유 또는 세부 정보. [ResponseCodeName](#responsecodename) 필드의 별칭을 지정합니다.|
| **EventOriginalUid** | 선택 | 문자열 | | 원본에서 제공하는 경우 원래 레코드의 고유 ID입니다. |
| **EventOriginalType**   | 선택    | 문자열  | `lookup` |   원본에서 제공하는 경우 원본 이벤트 유형 또는 ID입니다. |
| <a name ="eventproduct"></a>**EventProduct** | 필수 | String | `DNS Server` | 이벤트를 생성하는 제품 이 필드는 원본 레코드에서 사용하지 못할 수 있으며, 이 경우 파서에서 설정해야 합니다. |
| **EventProductVersion** | 선택 | 문자열 | `12.1` | 이벤트를 생성하는 제품의 버전 이 필드는 원본 레코드에서 사용하지 못할 수 있으며, 이 경우 파서에서 설정해야 합니다. |
| **EventVendor** | 필수 | String | `Microsoft` | 이벤트를 생성하는 제품의 공급 업체 이 필드는 원본 레코드에서 사용하지 못할 수 있으며, 이 경우 파서에서 설정해야 합니다. |
| **EventSchemaVersion** | 필수 | String | `0.1.1` | 여기에 설명된 스키마의 버전은 **0.1.1** 입니다. |
| **EventReportUrl** | 선택 | 문자열 | | 이벤트에 대한 추가 정보를 제공하는 리소스에 대해 이벤트에 제공된 URL입니다. |
| <a name="dvc"></a>**Dvc** | 필수       | String     |    `ContosoDc.Contoso.Azure` |           이벤트가 발생한 디바이스의 고유 식별자입니다. <br><br>이 필드는 [DvcId](#dvcid), [DvcHostname](#dvchostname) 또는 [DvcIpAddr](#dvcipaddr) 필드의 별칭을 지정할 수 있습니다. 명백한 디바이스가 없는 클라우드 원본의 경우 [이벤트 제품](#eventproduct) 필드와 동일한 값을 사용하세요.         |
| <a name ="dvcipaddr"></a>**DvcIpAddr**           | 권장 | IP 주소 |  `45.21.42.12` |       프로세스 이벤트가 발생한 디바이스의 IP 주소입니다.  |
| <a name ="dvchostname"></a>**DvcHostname**         | 권장 | 호스트 이름   | `ContosoDc.Contoso.Azure` |              프로세스 이벤트가 발생한 디바이스의 호스트 이름입니다.                |
| <a name ="dvcid"></a>**DvcId**               | 선택    | 문자열     || 프로세스 이벤트가 발생한 디바이스의 고유 ID입니다. <br><br>예: `41502da5-21b7-48ec-81c9-baeea8d7d669`   |
| <a name=additionalfields></a>**AdditionalFields** | 선택 사항 | 동적 | | 원본에서 보존할 가치가 있는 기타 정보를 제공하는 경우 원본 필드 이름으로 유지하거나 **AdditionalFields** 동적 필드를 만들고 추가 정보를 키/값 쌍으로 추가합니다. |
| | | | | |

### <a name="dns-specific-fields"></a>DNS 관련 필드

아래 필드는 DNS 이벤트와 관련이 있습니다. 즉, 대부분은 다른 스키마에서 유사점이 있으므로 동일한 명명 규칙을 따릅니다.

| **필드** | **클래스** | **형식** | **예제** | **참고** |
| --- | --- | --- | --- | --- |
| **SrcIpAddr** | 필수 | IP 주소 |  `192.168.12.1 `| DNS 요청을 보내는 클라이언트의 IP 주소입니다. 재귀 DNS 요청의 경우 이 값은 일반적으로 보고 디바이스이며 대부분의 경우 **127.0.0.1** 로 설정됩니다. |
| **SrcPortNumber** | 선택 | 정수 |  `54312` | DNS 쿼리의 원본 포트입니다. |
| **DstIpAddr** | 선택 사항 | IP 주소 |  `127.0.0.1` | DNS 요청을 수신하는 서버의 IP 주소입니다. 일반 DNS 요청의 경우 이 값은 일반적으로 보고 디바이스이며 대부분의 경우 **127.0.0.1** 로 설정됩니다. |
| **DstPortNumber** | 선택 | 정수 |  `53` | 대상 포트 번호 |
| **IpAddr** | | Alias | | SrcIpAddr의 별칭 |
| <a name=query></a>**DnsQuery** | 필수 | FQDN | `www.malicious.com` | 확인해야 하는 도메인입니다. <br><br>**참고**: 일부 원본에서 여러 다른 형식으로 이 쿼리를 보냅니다. 예를 들어 DNS 프로토콜 자체에서 쿼리 끝에 점( **.** )이 있습니다. 이 점은 제거해야 합니다.<br><br>DNS 프로토콜에서는 단일 요청을 통해 여러 개를 쿼리할 수 있지만, 이 시나리오는 거의 발생하지 않습니다. 요청에 여러 쿼리가 있으면 첫 번째 쿼리를 이 필드에 저장하고 나머지는 선택적으로 [AdditionalFields](#additionalfields) 필드에 보관합니다. |
| **도메인** | | Alias || [Query](#query)에 대한 별칭입니다. |
| **DnsQueryType** | 선택 | 정수 | `28` | 이 필드에는 [DNS Resource Record Type 코드](https://www.iana.org/assignments/dns-parameters/dns-parameters.xhtml))가 포함될 수 있습니다. |
| **DnsQueryTypeName** | 필수 | Enumerated | `AAAA` | 이 필드에는 [DNS Resource Record Type](https://www.iana.org/assignments/dns-parameters/dns-parameters.xhtml) 이름이 포함될 수 있습니다. <br><br>**참고**: IANA는 값의 대소문자를 정의하지 않으므로 분석에서 필요에 따라 대소문자를 정규화해야 합니다. 원본이 쿼리 유형 이름이 아닌 숫자 쿼리 유형 코드만 제공하는 경우 파서는 이 값으로 보강할 조회 테이블을 포함해야 합니다. |
| <a name=responsename></a>**DnsResponseName** | 선택 | 문자열 | | 레코드에 포함된 응답의 콘텐츠입니다.<br> <br> DNS 응답 데이터는 보고 디바이스 간에 일관되지 않고 구문 분석이 복잡하므로 원본 독립적 분석의 가치가 낮습니다. 따라서 정보 모델에 구문 분석 및 정규화가 필요하지 않으며 Azure Sentinel에서 보조 함수를 사용하여 응답 정보를 제공합니다. 자세한 내용은 [DNS 응답 처리](#handling-dns-response)를 참조하세요.|
| <a name=responsecodename></a>**DnsResponseCodeName** |  필수 | Enumerated | `NXDOMAIN` | [DNS 응답 코드](https://www.iana.org/assignments/dns-parameters/dns-parameters.xhtml). <br><br>**참고**: IANA는 값의 대소문자를 정의하지 않으므로 분석에서 대소문자를 정규화해야 합니다. 원본이 응답 코드 이름이 아닌 숫자 응답 코드만 제공하는 경우 파서는 이 값으로 보강할 조회 테이블을 포함해야 합니다. <br><br> 이 레코드가 응답이 아닌 요청을 나타내는 경우 **NA** 로 설정합니다. |
| **DnsResponseCode** | 선택 | 정수 | `3` | [DNS 숫자 응답 코드](https://www.iana.org/assignments/dns-parameters/dns-parameters.xhtml).|
| **TransactionIdHex** | 권장 | String | | DNS 고유 16진수 트랜잭션 ID입니다. |
| **NetworkProtocol** | 선택 사항 | Enumerated | `UDP` | 네트워크 확인 이벤트에서 사용하는 전송 프로토콜입니다. 값은 **UDP** 또는 **TCP** 일 수 있으며 DNS의 경우 가장 일반적으로 **UDP** 로 설정됩니다. |
| **DnsQueryClass** | 선택 | 정수 | | [DNS 클래스 ID](https://www.iana.org/assignments/dns-parameters/dns-parameters.xhtml).<br> <br>실제로는 **IN** 클래스(ID 1)만 사용되므로 이 필드의 가치가 떨어집니다.|
| **DnsQueryClassName** | 선택 | 문자열 | `"IN"` | [DNS 클래스 이름](https://www.iana.org/assignments/dns-parameters/dns-parameters.xhtml).<br> <br>실제로는 **IN** 클래스(ID 1)만 사용되므로 이 필드의 가치가 떨어집니다. |
| <a name=flags></a>**DnsFlags** | 선택 사항 | 문자열 목록 | `["DR"]` | 보고 디바이스에서 제공하는 플래그 필드입니다. 플래그 정보가 여러 필드에 제공되는 경우 구분 기호로 쉼표를 사용하여 연결합니다. <br><br>DNS 플래그는 구문 분석이 복잡하고 분석에서 덜 자주 사용되기 때문에 구문 분석 및 정규화가 필요하지 않으며 Azure Sentinel에서는 보조 기능을 사용하여 플래그 정보를 제공합니다. 자세한 내용은 [DNS 응답 처리](#handling-dns-response)를 참조하세요.|
| <a name=UrlCategory></a>**UrlCategory** |   | String | `Educational \\ Phishing` | DNS 이벤트 원본은 요청된 도메인의 범주를 조회할 수도 있습니다. 이 필드는 Azure Sentinel 네트워크 스키마에 맞게 **_UrlCategory_** 라고 합니다. <br><br>**_DomainCategory_** 는 DNS에 맞는 별칭으로 추가됩니다. |
| **DomainCategory** | | Alias | | [UrlCategory](#UrlCategory)에 대한 별칭입니다. |
| **ThreatCategory** |   | String |   | DNS 이벤트 원본이 DNS 보안도 제공하는 경우 DNS 이벤트도 평가할 수 있습니다. 예를 들어 위협 인텔리전스 데이터베이스에서 IP 주소 또는 도메인을 검색하고 위협 범주를 사용하여 도메인 또는 IP 주소를 할당할 수 있습니다. |
| **EventSeverity** | 선택 | 문자열 | `"Informational"` | DNS 이벤트 원본이 DNS 보안도 제공하는 경우 DNS 이벤트를 평가할 수 있습니다. 예를 들어 위협 인텔리전스 데이터베이스에서 IP 주소 또는 도메인을 검색하고 평가에 따라 심각도를 할당할 수 있습니다. |
| **DvcAction** | 선택 | 문자열 | `"Blocked"` | DNS 이벤트 원본도 DNS 보안을 제공하는 경우 요청에 대해 차단과 같은 작업을 취할 수 있습니다. |
| | | | | |

### <a name="deprecated-aliases"></a>사용되지 않는 별칭

다음 필드는 현재 사용되지 않지만 이전 버전과의 호환성을 위해 유지 관리되는 별칭입니다.

- Query(DnsQuery에 대한 별칭)
- QueryType(DnsQueryType에 대한 별칭)
- QueryTypeName(DnsQueryTypeName에 대한 별칭)
- ResponseName(DnsReasponseName에 대한 별칭)
- ResponseCodeName(DnsResponseCodeName에 대한 별칭)
- ResponseCode(DnsResponseCode에 대한 별칭)
- QueryClass(DnsQueryClass에 대한 별칭)
- QueryClassName(DnsQueryClassName에 대한 별칭)
- Flags(DnsFlags에 대한 별칭)

### <a name="additional-entities"></a>추가 엔터티

이벤트는 사용자, 호스트, 프로세스 또는 파일과 같은 엔터티를 중심으로 진행되며 각 엔터티는 설명하는 데 여러 필드가 필요할 수 있습니다. 예를 들면 다음과 같습니다.

- 호스트에는 이름 및 IP 주소가 모두 포함될 수 있습니다.
- 단일 레코드에는 원본 및 대상 호스트와 같이 유형이 같은 엔터티가 여러 개 포함될 수 있습니다.

이 문서에서 설명하는 DNS 스키마에는 엔터티를 설명하는 필드가 포함되어 있습니다. 원본에 엔터티를 설명하는 다른 정보가 포함된 경우 다음 테이블에 나열된 엔터티를 기준으로 필드를 추가하여 이 정보를 캡처합니다.

자세한 내용은 [Azure Sentinel의 정규화](normalization.md)를 참조하세요.


| **엔터티** | **필드** | **형식** | **필수 필드** | **참고** |
| --- | --- | --- | --- | --- |
| **배우** | 작업자\* | 사용자 |  | 대부분의 DNS 이벤트 원본에서는 일반적으로 DNS 프로토콜의 일부가 아닌 사용자 정보를 제공하지 않습니다. <br><br>어떤 경우에는 보고 디바이스가 일반적으로 원본 IP 주소를 사용자 정보로 해석하여 사용자 정보를 제공합니다. 이 경우 스키마 엔터티 설명서에 설명된 대로 사용자를 나타냅니다. 정보가 소스 IP 주소를 기반으로 하므로 **작업자** 를 설명자로 사용합니다. <br><br>**참고**: **작업자** 를 엔터티 설명자로 사용하는 경우 **사용자** 필드를 추가할 필요가 없습니다. |
| **원본 디바이스** | Src\* | 디바이스 | `SrcIpAddr` | DNS 이벤트 원본은 일반적으로 요청 원본의 IP 주소를 보고하므로 **SrcIpAddr** 은 필수입니다. <br><br>보고 디바이스가 요청 원본에 대한 추가 정보를 제공하거나 데이터를 보강하는 경우 디바이스 엔터티 지침을 통해 **Src** 를 필드 접두사로 사용하여 정규화합니다. |
| **대상 디바이스** | Dst\* | 디바이스 |  | DNS 이벤트 원본은 일반적으로 요청 대상에 대한 정보를 보고하지 않습니다. 보고 디바이스가 요청 대상에 대한 추가 정보를 제공하거나 데이터를 보강하는 경우 디바이스 엔터티 지침을 통해  **Dst** 를 접두사로 사용하여 정규화합니다. |
| **보고 디바이스** | Dvc\* | 디바이스 | `Dvc` | 대부분의 이벤트는 보고 디바이스에 대한 정보를 포함합니다. 해당 필드에는 접두사 Dvc를 사용합니다.|
| **프로세스 정보** | 프로세스\* | 프로세스 |  | 클라이언트 디바이스에서 DNS 쿼리를 생성한 프로세스와 관련된 정보입니다. |
| | | | | |

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

플래그 데이터에는 구문 분석 및 정규화가 필요하지 않습니다. 대신, 보고 디바이스에서 제공한 플래그 데이터를 [플래그](#flags) 필드에 저장합니다.

구문 분석되지 않은 응답을 입력으로 사용하고 다음 순서로 각 플래그를 나타내는 부울 값과 함께 동적 목록을 반환하는 `_imDNS<vendor>Flags_`라는 추가 KQL 함수를 제공할 수도 있습니다.

- 인증됨(AD)
- 신뢰할 수 있음(AA)
- 검사 사용 안 함(CD)
- 재귀 사용 가능(RA)
- 재귀 필요(RD)
- 잘림(TC)
- Z

## <a name="next-steps"></a>다음 단계

자세한 내용은 다음을 참조하세요.

- [Azure Sentinel의 정규화](normalization.md)
- [Azure Sentinel 인증 정규화 스키마 참조(퍼블릭 미리 보기)](authentication-normalization-schema.md)
- [Azure Sentinel 데이터 정규화 스키마 참조](normalization-schema.md)
- [Azure Sentinel 파일 이벤트 정규화 스키마 참조(퍼블릭 미리 보기)](file-event-normalization-schema.md)
- [Azure Sentinel 프로세스 이벤트 정규화 스키마 참조](process-events-normalization-schema.md)
- [Azure Sentinel 레지스트리 이벤트 정규화 스키마 참조(퍼블릭 미리 보기)](registry-event-normalization-schema.md)
