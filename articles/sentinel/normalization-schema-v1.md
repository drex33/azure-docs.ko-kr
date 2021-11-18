---
title: Microsoft Sentinel 네트워크 정규화 스키마(레거시 버전 - 공개 미리 보기)| Microsoft Docs
description: 이 문서에서는 Microsoft Sentinel 데이터 정규화 스키마를 표시합니다.
services: sentinel
cloud: na
documentationcenter: na
author: yelevin
manager: rkarlin
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: reference
ms.date: 11/09/2021
ms.author: yelevin
ms.custom: ignite-fall-2021
ms.openlocfilehash: b263a0d7be7d0bd42494dc5c5a931f9c97b00bfb
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/17/2021
ms.locfileid: "132712854"
---
# <a name="microsoft-sentinel-network-normalization-schema-legacy-version---public-preview"></a>Microsoft Sentinel 네트워크 정규화 스키마(레거시 버전 - 공개 미리 보기)

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

네트워크 정규화 스키마는 보고된 네트워크 이벤트를 설명하는 데 사용되며 Microsoft Sentinel에서 소스 독립적 분석을 사용하도록 설정하는 데 사용됩니다.

자세한 내용은 [정규화 및 ASIM(고급 SIEM 정보 모델)을](normalization.md)참조하세요.

> [!IMPORTANT]
> 이 문서는 ASIM을 사용할 수 있기 전에 미리 보기로 릴리스된 네트워크 정규화 스키마의 버전 0.1과 관련이 있습니다. 네트워크 정규화 [스키마의 버전 0.2는](network-normalization-schema.md) ASIM과 일치하고 다른 향상된 기능을 제공합니다.
>
> 자세한 내용은 [네트워크 정규화 스키마 버전 간의 차이점을 참조하세요.](#changes)
>

## <a name="terminology"></a>용어

다음 용어는 Microsoft Sentinel 스키마에서 사용됩니다.

| 용어 | 정의 |
| ---- | ---------- |
| **보고 디바이스** | Microsoft Sentinel에 레코드를 보내는 시스템입니다. 이는 레코드의 주체 시스템이 아닐 수도 있습니다. |
| **레코드** | 보고 디바이스에서 전송되는 데이터의 단위입니다. 이 데이터 단위를 `log`, `event` 또는 `alert`라고도 하지만 다른 형식일 수도 있습니다.|
|

## <a name="data-types-and-formats"></a>데이터 형식 및 서식

다음 표에서는 정규화된 필드에 필요하고 다른 필드에 권장되는 데이터 값을 정규화하기 위한 지침을 제공합니다.

| 데이터 형식 | 실제 형식 | 서식과 값 |
| --------- | ------------- | ---------------- |
| **날짜/시간** | 사용된 수집 메서드 기능에 따라(내림차순) 다음 중 하나입니다.<ul><li>Log Analytics 기본 제공 날짜/시간 형식</li><li>Log Analytics 날짜/시간 숫자 표현을 사용하는 정수 필드</li><li>Log Analytics 날짜/시간 숫자 표현을 사용하는 문자열 필드</li></ul> | Log Analytics 날짜/시간 표현입니다. <br></br>Log Analytics 날짜/시간 표현은 Unix 시간 표현과 본질적으로는 비슷하지만 다른 점도 있습니다. 이 변환 지침을 참조하세요. <br><br>표준 시간대에 맞게 날짜와 시간을 조정해야 합니다. |
| **MAC 주소** | String | 콜론으로 구분된 16진수 표기법 |
| **IP 주소** | IP 주소 | 이 스키마에는 별도의 IPv4 및 IPv6 주소가 없습니다. 모든 IP 주소 필드에는 IPv4 주소 또는 IPv6 주소가 포함될 수 있습니다.<ul><li>점으로 구분된 10진수 표기법의 IPv4</li><li>8헥스텟 표기법의 IPv6로, 여기에 설명된 짧은 형식을 사용할 수 있습니다.</li></ul> |
| **사용자** | String | 다음 3개의 사용자 필드를 사용할 수 있습니다.<ul><li>사용자 이름</li><li>사용자 UPN</li><li>사용자 도메인</li></ul> |
| **사용자 ID** | String | 현재 다음 2개의 사용자 ID가 지원됩니다.<ul><li>사용자 SID</li><li>Azure Active Directory ID</li></ul> |
| **디바이스** | String | 다음 3개의 디바이스/호스트 열이 지원됩니다.<ul><li>ID</li><li>속성</li><li>FQDN(정규화된 도메인 이름)</li></ul> |
| **국가** | String | 다음 우선 순위에 따라 ISO 3166-1을 사용하는 문자열입니다.<ul><li>알파-2 코드(예: 미국의 경우 `US`)</li><li>알파-3 코드(예: 미국의 경우 `USA`)</li><li>짧은 이름</li></ul> |
| **지역** | String | ISO 3166-2를 사용하는 국가 지역 이름 |
| **도시** | String | |
| **경도** | Double | ISO 6709 좌표 표현(부호 있는 10진수) |
| **위도** | Double | ISO 6709 좌표 표현(부호 있는 10진수) |
| **해시 알고리즘** | String | 다음 4개의 해시 열이 지원됩니다.<ul><li>MD5</li><li>SHA1</li><li>SHA256</li><li>SHA512</li></ul> |
| **파일 유형** | String | 파일 형식의 유형:<ul><li>내선 번호</li><li>클래스</li><li>NamedType</li></ul> |
|

## <a name="network-sessions-table-schema"></a>네트워크 세션 테이블 스키마

다음은 버전이 1.0.0인 네트워크 세션 테이블의 스키마입니다.

| 필드 이름 | 값 형식 | 예제 | 설명 | 연결된 OSSEM 엔터티 |
|-|-|-|-|-|
| **EventType** | String | 트래픽 | 수집되는 이벤트 유형 | 이벤트 |
| **EventSubType** | String | 인증 | 해당하는 경우 형식에 대한 추가 설명 | 이벤트 |
| **EventCount** | 정수  | 10 | 집계된 이벤트 수(해당하는 경우) | 이벤트 |
| **EventEndTime** | 날짜/시간 | “데이터 형식”을 참조하세요. | 이벤트가 종료된 시간 | 이벤트 |
| EventMessage | 문자열 |  액세스 거부 | 레코드에 포함되었거나 레코드에서 생성된 일반 메시지 또는 설명 | 이벤트 |
| **DvcIpAddr** | IP 주소 |  23.21.23.34 | 레코드를 생성하는 디바이스의 IP 주소 | 디바이스<br>IP |
| **DvcMacAddr** | String | 06:10:9f:eb:8f:14 | 이벤트를 보낸 보고 디바이스의 네트워크 인터페이스에 대한 MAC 주소 | 디바이스<br>Mac |
| **DvcHostname** | 디바이스 이름(문자열) | syslogserver1.contoso.com | 메시지를 생성하는 디바이스의 디바이스 이름 | 디바이스 |
| **EventProduct** | String | OfficeSharepoint | 이벤트를 생성하는 제품 | 이벤트 |
| **EventProductVersion** | 문자열 | 9.0 |  이벤트를 생성하는 제품의 버전 | 이벤트 |
| **EventResourceId** | 디바이스 ID(문자열) | /subscriptions/3c1bb38c-82e3-4f8d-a115-a7110ba70d05 /resourcegroups/contoso77/providers /microsoft.compute/virtualmachines /syslogserver1 | 메시지를 생성하는 디바이스의 리소스 ID | 이벤트 |
| **EventReportUrl** | String | https://192.168.1.1/repoerts/ae3-56.htm | 보고 디바이스에서 만든 전체 보고서에 대한 링크 | 이벤트 |
| **EventVendor** | String | Microsoft | 이벤트를 생성하는 제품의 공급 업체 | 이벤트 |
| **EventResult** | 다중값: 성공, 부분, 실패, [비어 있음](문자열) | Success | 작업에 대해 보고된 결과로 해당 사항이 없는 경우 빈 값입니다. | 이벤트 |
| **EventResultDetails** | String | 잘못된 암호 | EventResult에 보고된 결과에 대한 이유 또는 세부 정보 | 이벤트 |
| **EventSchemaVersion** | Real | 0.1 | Microsoft Sentinel 스키마 버전. 현재 0.1입니다. | 이벤트 |
| **EventSeverity** | String | 낮음 | 보고된 작업이 보안에 영향을 주는 경우 영향의 심각도를 나타냅니다. | 이벤트 |
| **EventOriginalUid** | String | af6ae8fe-ff43-4a4c-b537-8635976a2b51 | 보고 디바이스의 레코드 ID | 이벤트 |
| **EventStartTime** | 날짜/시간 | “데이터 형식”을 참조하세요. | 이벤트가 언급된 시간 | 이벤트 |
| **TimeGenerated** | 날짜/시간 | “데이터 형식”을 참조하세요. | 보고 원본에서 보고한 이벤트가 발생한 시간입니다. | 사용자 지정 필드 |
| **EventTimeIngested** | 날짜/시간 | “데이터 형식”을 참조하세요. | 이벤트가 Microsoft 센티널에 수집 된 시간입니다. Microsoft 센티널에서 추가 됩니다. | 이벤트 |
| **EventUid** | Guid(문자열) | 516a64e3-8360-4f1e-a67c-d96b3d52df54 | Microsoft 센티널에서 행을 표시 하는 데 사용 하는 고유 식별자입니다. | 이벤트 |
| **NetworkApplicationProtocol** | String | HTTPS | 연결 또는 세션에서 사용하는 애플리케이션 계층 프로토콜입니다. | 네트워크 |
| **DstBytes** | int | 32455 | 연결 또는 세션의 대상에서 원본으로 보낸 바이트 수 | 대상 |
| **SrcBytes** | int | 46536 | 연결 또는 세션의 원본에서 대상으로 보낸 바이트 수 | 원본 |
| **NetworkBytes** | int | 78991 | 양방향으로 전송된 바이트 수. BytesReceived와 BytesSent가 모두 있는 경우 그 합계는 BytesTotal과 같아야 합니다. | 네트워크 |
| **NetworkDirection** | 다중값: 인바운드, 아웃바운드(문자열) | 인바운드 | 조직 내/외부로의 연결 또는 세션의 방향 | 네트워크 |
| **DstGeoCity** | String | Burlington | 대상 IP 주소와 연결된 도시 | 대상,<br>지역 |
| **DstGeoCountry** | 국가(문자열) | 미국 | 원본 IP 주소와 연결된 국가 | 대상,<br>지역 |
| **DstDvcHostname** | 디바이스 이름(문자열) |  victim_pc | 대상 디바이스의 디바이스 이름 | 대상<br>디바이스 |
| **DstDvcFqdn** | String | victim_pc.contoso.local | 로그를 만든 호스트의 정규화된 도메인 이름 | 대상,<br>디바이스 |
| **DstDomainHostname** | 문자열 | CONTOSO | 대상의 도메인, 대상 호스트의 도메인(웹 사이트, 도메인 이름 등)(예: DNS 조회 또는 NS 조회용) | 대상 |
| **DstInterfaceName** | 문자열 | Microsoft Hyper-V 네트워크 어댑터 | 대상 디바이스의 연결 또는 세션에 사용되는 네트워크 인터페이스 | 대상 |
| **DstInterfaceGuid** | 문자열 | 2BB33827-6BB6-48DB-8DE6-DB9E0B9F9C9B | 인증 요청에 사용된 네트워크 인터페이스의 GUID  | 대상 |
| **DstIpAddr** | IP 주소 | 2001:db8::ff00:42:8329 | 네트워크 패킷에서 가장 일반적인 대상 IP로 참조되는 연결 또는 세션 대상의 IP 주소 | 대상,<br>IP |
| **DstDvcIpAddr** | IP 주소 | 75.22.12.2 | 네트워크 패킷과 직접 연결되지 않은 디바이스의 대상 IP 주소 | 대상,<br>디바이스<br>IP
| **DstGeoLatitude** | 위도(Double) | 44.475833 | 대상 IP 주소와 연결된 지리적 좌표의 위도 | 대상,<br>지역 |
| **DstMacAddr** | String | 06:10:9f:eb:8f:14 | 연결 또는 세션이 종료된 네트워크 인터페이스의 MAC 주소로, 일반적으로 네트워크 패킷의 대상 MAC을 말합니다. | 대상,<br>MAC |
| **DstDvcMacAddr** | String | 06:10:9f:eb:8f:14 | 네트워크 패킷과 직접 연결되지 않은 디바이스의 대상 MAC 주소 | 대상,<br>디바이스<br>MAC |
| **DstDvcDomain** | String | CONTOSO | 대상 디바이스의 도메인 | 대상,<br>디바이스 |
| **DstPortNumber** | 정수 | 443 | 대상 IP 포트 | 대상,<br>포트 |
| **DstGeoRegion** | 지역(문자열) | 버몬트 | 대상 IP 주소와 연결된 국가 내의 지역 | 대상,<br>지역 |
| **DstResourceId** | 디바이스 ID(문자열) |  /subscriptions/3c1bb38c-82e3-4f8d-a115-a7110ba70d05 /resourcegroups/contoso77/providers /microsoft.compute/virtualmachines /victim | 대상 디바이스의 리소스 ID | 대상 |
| **DstNatIpAddr** | IP 주소 | 2::1 | 방화벽과 같은 중간 NAT 디바이스에서 보고되는 경우 NAT 디바이스에서 원본과의 통신에 사용하는 IP 주소 | 대상 NAT,<br>IP |
| **DstNatPortNumber** | int | 443 | 방화벽과 같은 중간 NAT 디바이스에서 보고되는 경우 NAT 디바이스에서 원본과의 통신에 사용하는 포트 | 대상 NAT,<br>포트 |
| **DstUserSid** | 사용자 SID |  S-12-1445 | 세션 대상과 연결된 ID의 사용자 ID로, 일반적으로 서버를 인증하는 데 사용되는 ID입니다. 자세한 내용은 [데이터 형식](#data-types-and-formats)을 참조하세요. | 대상,<br>사용자 |
| **DstUserAadId** | 문자열(guid) | ae92b0b4-cfba-4b42-85a0-fbd862f4df54 | 세션의 대상 끝에 있는 사용자의 Azure AD 계정 개체 ID | 대상,<br>사용자 |
| **DstUserName** | 사용자 이름(문자열) | johnd | 세션 대상에 연결된 ID의 사용자 이름  | 대상,<br>사용자 |
| **DstUserUpn** | 문자열 | johnd@anon.com | 세션 대상에 연결된 ID의 UPN | 대상,<br>사용자 |
| **DstUserDomain** | 문자열 | 작업 그룹 | 세션 대상의 계정 도메인 또는 컴퓨터 이름 | 대상,<br>사용자 |
| **DstZone** | String | Dmz | 보고 디바이스에 정의된 대상의 네트워크 영역 | 대상 |
| **DstGeoLongitude** | 경도(Double) | -73.211944 | 대상 IP 주소와 연결된 지리적 좌표의 경도 | 대상,<br>지역 |
| **DvcAction** | 다중값: 허용, 거부, 드롭(문자열) | 허용 | 방화벽과 같은 중간 디바이스에서 보고한 경우 디바이스에서 수행하는 작업 | 디바이스 |
| **DvcInboundInterface** | String | eth0 | 방화벽과 같은 중간 디바이스에서 보고하는 경우 원본 디바이스에 연결하는 데 사용하는 네트워크 인터페이스 | 디바이스 |
| **DvcOutboundInterface** | String  | 이더넷 어댑터 이더넷 4 | 방화벽과 같은 중간 디바이스에서 보고하는 경우 대상 디바이스에 연결하는 데 사용하는 네트워크 인터페이스 | 디바이스 |
| **NetworkDuration** | 정수 | 1500 | 네트워크 세션 또는 연결을 완료하는 데 걸리는 시간(밀리초) | 네트워크 |
| **NetworkIcmpCode** | 정수 | 34 | ICMP 메시지에서 ICMP 메시지 유형 숫자 값(RFC 2780 또는 RFC 4443) | 네트워크 |
| **NetworkIcmpType** | String | 대상에 연결할 수 없음 | ICMP 메시지에서 ICMP 메시지 유형 텍스트 표현(RFC 2780 또는 RFC 4443) | 네트워크 |
| **DstPackets** | int  | 446 | 연결 또는 세션의 대상에서 원본으로 전송된 패킷 수입니다. 패킷의 의미는 보고 디바이스에 의해 정의됩니다. | 대상 |
| **SrcPackets** | int  | 6478 | 연결 또는 세션 원본에서 대상으로 보낸 패킷 수입니다. 패킷의 의미는 보고 디바이스에 의해 정의됩니다. | 원본 |
| **NetworkPackets** | int  | 0 | 양방향으로 전송된 패킷 수입니다. PacketsReceived와 PacketsSent가 모두 있는 경우 그 합계는 BytesTotal과 같아야 합니다. | 네트워크 |
| **HttpRequestTime** | 정수 | 700 | 서버에 요청을 보내는 데 걸린 시간입니다(해당하는 경우). | Http |
| **HttpResponseTime** | 정수 | 800 | 서버에서 응답을 받는 데 걸린 시간입니다(해당하는 경우). | Http |
| **NetworkRuleName** | String | AnyAnyDrop | DeviceAction이 결정된 규칙의 이름 또는 ID | 네트워크 |
| **NetworkRuleNumber** | int |  23 | 일치 규칙 번호  | 네트워크 |
| **NetworkSessionId** | 문자열 | 172_12_53_32_4322__123_64_207_1_80 | 보고 디바이스에서 보고한 세션 식별자 (예: 인증 후 특정 애플리케이션에 대한 L7 세션 식별자) | 네트워크 |
| **SrcGeoCity** | String | Burlington | 원본 IP 주소와 연결된 도시 | 원본,<br>지역 |
| **SrcGeoCountry** | 국가(문자열) | 미국 | 원본 IP 주소와 연결된 국가 | 원본,<br>지역 |
| **SrcDvcHostname** | 디바이스 이름(문자열) |  villain | 원본 디바이스의 디바이스 이름 | 원본,<br>디바이스 |
| **SrcDvcFqdn** | 문자열 | Villain.malicious.com | 로그를 만든 호스트의 정규화된 도메인 이름 | 원본,<br>디바이스 |
| **SrcDvcDomain** | 문자열 | EVILORG | 세션이 시작된 디바이스의 도메인 | 원본,<br>디바이스 |
| **SrcDvcOs** | String | iOS | 원본 디바이스의 OS | 원본,<br>디바이스 |
| **SrcDvcModelName** | String | Samsung Galaxy Note | 원본 디바이스의 모델 이름 | 원본,<br>디바이스 |
| **SrcDvcModelNumber** | String | 10.0 | 원본 디바이스의 모델 번호 | 원본,<br>디바이스 |
| **SrcDvcType** | String | 모바일 | 원본 디바이스의 유형 | 원본,<br> 디바이스 |
| **SrcIntefaceName** | String | eth01 | 원본 디바이스에서 연결 또는 세션에 사용하는 네트워크 인터페이스 | 원본 |
| **SrcInterfaceGuid** | String | 46ad544b-eaf0-47ef-827c-266030f545a6 | 사용되는 네트워크 인터페이스의 GUID | 원본 |
| **SrcIpAddr** | IP 주소 | 77.138.103.108 | 연결 또는 세션이 시작된 IP 주소 | 원본,<br>IP |
| **SrcDvcIpAddr** | IP 주소 | 77.138.103.108 | 네트워크 패킷과 직접 연결되지 않은 디바이스의 원본 IP 주소(공급자가 수집하거나 명시적으로 계산) | 원본,<br>디바이스<br>IP |
| **SrcGeoLatitude** | 위도(Double) | 44.475833 | 원본 IP 주소와 연결된 지리적 좌표의 위도 | 원본,<br>지역 |
| **SrcGeoLongitude** | 경도(Double) | -73.211944 | 원본 IP 주소와 연결된 지리적 좌표의 경도 | 원본,<br>지역 |
| **SrcMacAddr** | String | 06:10:9f:eb:8f:14 | 연결 또는 세션이 시작된 네트워크 인터페이스의 MAC 주소입니다. | 원본,<br>Mac |
| **SrcDvcMacAddr** | String | 06:10:9f:eb:8f:14 | 네트워크 패킷과 직접 연결되지 않은 디바이스의 원본 MAC 주소 | 원본,<br>디바이스<br>Mac |
| **SrcPortNumber** | 정수 | 2335 | 연결이 시작된 IP 포트. 여러 연결을 구성하는 세션과 관련이 없을 수 있습니다. | 원본,<br>포트 |
| **SrcGeoRegion** | 지역(문자열) | 버몬트 | 원본 IP 주소와 연결된 국가 내의 지역 | 원본,<br>지역 |
| **SrcResourceId** | String | /subscriptions/3c1bb38c-82e3-4f8d-a115-a7110ba70d05 /resourcegroups/contoso77/providers /microsoft.compute/virtualmachines /syslogserver1 | 메시지를 생성하는 디바이스의 리소스 ID | 원본 |
| **SrcNatIpAddr** | IP 주소 | 4.3.2.1 | 방화벽과 같은 중간 NAT 디바이스에서 보고하는 경우 NAT 디바이스에서 대상과 통신하는 데 사용하는 IP 주소 | 원본 NAT,<br>IP |
| **SrcNatPortNumber** | 정수 | 345 | 방화벽과 같은 중간 NAT 디바이스에서 보고하는 경우 NAT 디바이스에서 대상과 통신하는 데 사용하는 포트 | 원본 NAT,<br>포트 |
| **SrcUserSid** | 사용자 ID(문자열) | S-15-1445 | 세션 원본과 연결된 ID의 사용자 ID. 일반적으로 사용자는 클라이언트에서 작업을 수행합니다. 자세한 내용은 [데이터 형식](#data-types-and-formats)을 참조하세요. | 원본,<br>사용자 |
| **SrcUserAadId** | 문자열(guid) | 16c8752c-7dd2-4cad-9e03-fb5d1cee5477 | 세션의 원본 끝에 있는 사용자의 Azure AD 계정 개체 ID | 원본,<br>사용자 |
| **SrcUserName** | 사용자 이름(문자열) | bob | 세션 원본과 연결된 ID의 사용자 이름. 일반적으로 사용자는 클라이언트에서 작업을 수행합니다. 자세한 내용은 [데이터 형식](#data-types-and-formats)을 참조하세요. | 원본<br>사용자 |
| **SrcUserUpn** | 문자열 | bob@alice.com | 세션을 시작하는 계정의 UPN | 원본,<br>사용자 |
| **SrcUserDomain** | 문자열 | 데스크톱 | 세션을 시작하는 계정의 도메인 | 원본,<br>사용자 |
| **SrcZone** | String | 탭 | 보고 디바이스에서 정의한 원본 네트워크 영역 | 원본 |
| **NetworkProtocol** | String | TCP | 연결 또는 세션에서 사용하는 IP 프로토콜로, 일반적으로 TCP, UDP 또는 ICMP가 있습니다. | 네트워크 |
| **CloudAppName** | String | Facebook | 프록시로 식별되는 HTTP 애플리케이션의 대상 애플리케이션 이름 | 클라우드 |
| **CloudAppId** | String | 124 | 프록시로 식별되는 HTTP 애플리케이션의 대상 애플리케이션 ID. 이 값은 일반적으로 사용되는 프록시와 관련이 있습니다. | 클라우드 |
| **CloudAppOperation** | String | DeleteFile | 프록시로 식별되는 HTTP 애플리케이션의 대상 애플리케이션 컨텍스트에서 사용자가 수행한 작업. 이 값은 일반적으로 사용되는 프록시와 관련이 있습니다. | 클라우드 |
| **CloudAppRiskLevel** | String | 3 | 프록시로 식별되는 HTTP 애플리케이션과 연결된 위험 수준. 이 값은 일반적으로 사용되는 프록시와 관련이 있습니다. | 클라우드 |
| **FileName** | String | ImNotMalicious.exe | 파일 이름 정보를 제공하는 FTP 및 HTTP와 같은 프로토콜에 대한 네트워크 연결을 통해 전송되는 파일 이름입니다. | 파일 |
| **FilePath** | String | C:\Malicious\ImNotMalicious.exe | 파일 이름을 포함한 파일의 전체 경로 | 파일 |
| **FileHashMd5** | String | 51BC68715FC7C109DCEA406B42D9D78F | 프로토콜의 네트워크 연결을 통해 전송되는 파일의 MD5 해시 값 | 파일 |
| **FileHashSha1** | String | 491AE3...C299821476F4 | 프로토콜의 네트워크 연결을 통해 전송되는 파일의 SHA1 해시 값 | 파일 |
| **FileHashSha256** | String | 9B8F8EDB...C129976F03 | 프로토콜의 네트워크 연결을 통해 전송되는 파일의 SHA256 해시 값 | 파일 |
| **FileHashSha512** | String | 5E127D...F69F73F01F361 | 프로토콜의 네트워크 연결을 통해 전송되는 파일의 SHA512 해시 값 | 파일 |
| **FileExtension** |  String | exe | FTP 및 HTTP와 같은 프로토콜의 네트워크 연결을 통해 전송되는 파일의 유형 | 파일
| **FileMimeType** | String | application/msword | FTP 및 HTTP와 같은 프로토콜에 대한 네트워크 연결을 통해 전송되는 파일의 MIME 형식 | 파일 |
| **FileSize** | 정수 | 23500 | 프로토콜의 네트워크 연결을 통해 전송되는 파일 크기(바이트) | 파일 |
| **HttpVersion** | String | 2.0 | HTTP/HTTPS 네트워크 연결의 HTTP 요청 버전 | Http |
| **HttpRequestMethod** | String | GET | HTTP/HTTPS 네트워크 세션의 HTTP 메서드 | Http |
| **HttpStatusCode** | String | 404 | HTTP/HTTPS 네트워크 세션의 HTTP 상태 코드 | Http |
| **HttpContentType** | String | multipart/form-data; boundary=something | HTTP/HTTPS 네트워크 세션의 HTTP 응답 콘텐츠 형식 헤더 | Http |
| **HttpReferrerOriginal** | String | https://developer.mozilla.org/en-US/docs/Web/JavaScript | HTTP/HTTPS 네트워크 세션의 HTTP 참조 페이지 헤더 | Http |
| **HttpUserAgentOriginal** | String | Mozilla/5.0(Windows NT 10.0; WOW64) AppleWebKit/537.36(KHTML, like Gecko) Chrome/83.0.4103.97 Safari/537.36 | HTTP/HTTPS 네트워크 세션의 HTTP 사용자 에이전트 헤더 | Http |
| **HttpRequestXff** | String | 120.12.41.1 | HTTP/HTTPS 네트워크 세션에 대한 HTTP X-Forwarded-For 헤더 | Http |
| **UrlCategory** | String | 검색 엔진 | 콘텐츠와 관련된 URL의 도메인을 기준으로 하는 URL의 정의된 그룹입니다. 예: 성인, 뉴스, 광고, 파킹 도메인 등 | url |
| **UrlOriginal** | String | https:// contoso.com/fo/?k=v&q=u#f | HTTP/HTTPS 네트워크 세션에 대한 HTTP 요청 URL | Url |
| **UrlHostname** | String | contoso.com | HTTP/HTTPS 네트워크 세션에 대한 HTTP 요청 URL의 도메인 부분 | Url |
| **ThreatCategory** | String | Trojan | IPS의 웹 보안 게이트웨이와 같은 보안 시스템에서 식별한 위협의 범주이며 이 네트워크 세션과 연결됩니다. | 위협 |
| **ThreatId** | String | Tr.124 | IPS의 웹 보안 게이트웨이와 같은 보안 시스템에서 식별한 위협의 ID이며 이 네트워크 세션과 연결됩니다. | 위협 |
| **ThreatName** | String | EICAR 테스트 파일 | 식별된 위협 또는 맬웨어의 이름 | 위협 |
| **AdditionalFields** | 동적(JSON 모음) | {<br>Property1: “val1”,<br>Property2: “val2”<br>} | 스키마에 일치하는 해당 열이 없으면 JSON 모음에 기타 필드를 저장할 수 있습니다.<br>쿼리 타임 구문 분석을 위해서는 JSON 모음을 사용하는 대신 추가 열을 승격하는 것이 좋습니다. 데이터를 JSON 코드에 압축하면 쿼리 성능이 저하되기 때문입니다. | 사용자 지정 필드 |
|


## <a name="differences-between-the-version-01-and-version-02"></a><a name="changes"></a>버전 0.1 및 버전 0.2의 차이점

Microsoft 센티널 네트워크 세션 정규화 스키마 버전 0.1의 원래 버전이 ASIM을 사용할 수 있게 되기 전에 미리 보기로 출시 되었습니다.

이 문서에서 설명 하는 버전 0.1과 [버전 0.2](network-normalization-schema.md) 의 차이점은 다음과 같습니다.

- 버전 0.2에서는 표준 및 소스 관련 파서 이름이 표준 ASIM 명명 규칙을 준수 하도록 변경 되었습니다.
- 버전 0.2에서는 특정 장치 유형을 수용 하기 위해 특정 지침과 소스에 관계 없는 파서를 추가 합니다.

다음 섹션에서는 [버전 0.2](network-normalization-schema.md) 이 특정 필드와 어떻게 다른 지 설명 합니다.

### <a name="added-fields-in-version-02"></a>버전 0.2에 추가 된 필드

다음 필드는 [버전 0.2](network-normalization-schema.md) 에 추가 되었으며 버전 0.1에는 없습니다.

:::row:::
   :::column span="":::
      - DstAppType
      - DstDeviceType
      - DstDomainType
      - DstDvcId
      - DstDvcIdType
      - DstOriginalUserType
      - DstUserIdType
      - DstUsernameType
      - DstUserType
      - DvcActionOriginal
      - DvcDomain
   :::column-end:::
   :::column span="":::
      - DvcDomainType
      - DvcFQDN
      - DvcId
      - DvcIdType
      - DvcIdType
      - EventOriginalSeverity
      - EventOriginalType
      - SrcAppId
      - SrcAppName
      - SrcAppType
   :::column-end:::
   :::column span="":::
      - SrcDeviceType
      - SrcDomainType
      - SrcDvcId
      - SrcDvcIdType
      - SrcOriginalUserType
      - SrcUserIdType
      - SrcUsernameType
      - SrcUserType
      - ThreatRiskLevelOriginal
      - Url
   :::column-end:::
:::row-end:::


### <a name="newly-aliased-fields-in-version-02"></a>버전 0.2의 새로 별칭이 있는 필드

이제 ASIM이 도입된 [버전 0.2에서](network-normalization-schema.md) 다음 필드가 별칭으로 처리됩니다.

|버전 0.1의 필드  |버전 0.2의 별칭  |
|---------|---------|
|SessionId     |     NetworkSessionId    |
|Duration     |     NetworkDuration    |
|IpAddr     | SrcIpAddr        |
|사용자     |     DstUsername    |
|호스트 이름     |   DstHostname      |
|UserAgent     |     HttpUserAgent    |
|     |         |

### <a name="modified-fields-in-version-02"></a>버전 0.2에서 수정된 필드

다음 필드는 [버전 0.2에서](network-normalization-schema.md)열거되며 제공된 목록의 특정 값이 필요합니다.

- EventType
- EventResultDetails
- EventSeverity

### <a name="renamed-fields-in-version-02"></a>버전 0.2에서 이름이 바뀐 필드

[버전 0.2에서](network-normalization-schema.md)다음 필드의 이름이 바뀌었습니다.

- **버전 0.2에서는 기본 제공 Log Analytics 필드를 사용합니다.**

    는 `ingestion_time()` 필드 이름이 아닌 KQL 함수입니다.

    |버전 0.1의 필드  |버전 0.2에서 이름이 변경되었습니다.  |
    |---------|---------|
    |  EventResourceId  |   _ResourceId      |
    | EventUid   |     _ItemId    |
    | EventTimeIngested   |  ingestion_time()       |
    |    |         |

- **ASIM 및 OSSEM의 향상된 내용에 맞게 이름이 변경되었습니다.**

    |버전 0.1의 필드  |버전 0.2에서 이름이 변경되었습니다.  |
    |---------|---------|
    |  HttpReferrerOriginal  |   HttpReferrer      |
    | HttpUserAgentOriginal   |     HttpUserAgent    |
    |    |         |

- **네트워크 세션 대상이 클라우드 서비스일 필요가 없도록 이름이 변경되었습니다.**

    |버전 0.1의 필드  |버전 0.2에서 이름이 변경되었습니다.  |
    |---------|---------|
    |  CloudAppId  |   DstAppId      |
    | CloudAppName   |     DstAppName    |
    | CloudAppRiskLevel   |  ThreatRiskLevel       |
    |    |         |

- **대/소문자 변경 및 사용자 엔터티의 ASIM 처리에 맞게 이름이 변경되었습니다.**

    |버전 0.1의 필드  |버전 0.2에서 이름이 변경되었습니다.  |
    |---------|---------|
    |  DstUserName  |   DstUsername      |
    | SrcUserName   |     SrcUsername    |
    |    |         |

- **ASIM 디바이스 엔터티에 더 잘 맞고 Azure의 이외의 리소스 ID를 허용하도록 이름이 변경되었습니다.**

    |버전 0.1의 필드  |버전 0.2에서 이름이 변경되었습니다.  |
    |---------|---------|
    |  DstResourceId  |   SrcDvcAzureRerouceId      |
    | SrcResourceId   |     SrcDvcAzureRerouceId    |
    |    |         |

- **`Dvc` 버전 0.1의 처리가 일치하지 않는 경우 필드 이름에서 문자열을 제거하도록 이름이 변경되었습니다.**

    |버전 0.1의 필드  |버전 0.2에서 이름이 변경되었습니다.  |
    |---------|---------|
    |  DstDvcDomain  |   DstDomain      |
    | DstDvcFqdn   |     DstFqdn    |
    |  DstDvcHostname  |   DstHostname      |
    | SrcDvcDomain   |     SrcDomain    |
    |  SrcDvcFqdn  |   SrcFqdn      |
    | SrcDvcHostname   |     SrcHostname    |
    |    |         |

- **ASIM 파일 표현 지침에 맞게 이름이 변경되었습니다.**

    |버전 0.1의 필드  |버전 0.2에서 이름이 변경되었습니다.  |
    |---------|---------|
    |  FileHashMd5  |   FileMD5      |
    | FileHashSha1   |     FileSHA1    |
    |  FileHashSha256  |   FileSHA256      |
    | FileHashSha512   |     FileSHA512    |
    |  FileMimeType  |   FileContentType      |
    |    |         |

### <a name="removed-fields-in-version-02"></a>버전 0.2에서 필드가 제거되었습니다.

다음 필드는 버전 0.1에만 있으며 [버전 0.2에서 제거되었습니다.](network-normalization-schema.md)

|이유  |제거된 필드  |
|---------|---------|
|**필드 이름에 문자열이 없으므로 제거되었습니다. `Dvc`**     |  - DstDvcIpAddr<br> - DstDvcMacAddr<br>- SrcDvcIpAddr<br>- SrcDvcMacAddr       |
|**URL의 ASIM 처리에 맞게 제거**     |  - UrlHostname       |
|**이러한 필드는 일반적으로 네트워크 세션 이벤트의 일부로 제공되지 않으므로 제거됩니다.**<br><br>이벤트에 이러한 필드가 포함된 경우 [프로세스 이벤트 스키마를](process-events-normalization-schema.md) 사용하여 디바이스 속성을 설명하는 방법을 이해합니다. |     - SrcDvcOs<br>-&nbsp;SrcDvcModelName<br>-&nbsp;SrcDvcModelNumber<br>- DvcMacAddr<br>- DvcOs         |
|**ASIM 파일 표현 지침에 맞게 제거**     |   - FilePath<br>- FileExtension      |
|**이 필드는 [인증](authentication-normalization-schema.md)스키마 와 같은 다른 스키마를 사용해야 함을 나타내기 때문에 제거됩니다.**     |  - CloudAppOperation       |
|**중복될 때 제거됩니다. `DstHostname`**     |  - DstDomainHostname         |
|     |         |


## <a name="next-steps"></a>다음 단계

자세한 내용은 다음을 참조하세요.

- [Microsoft Sentinel의 정규화](normalization.md)
- [Microsoft Sentinel 인증 정규화 스키마 참조(공개 미리 보기)](authentication-normalization-schema.md)
- [Microsoft Sentinel 파일 이벤트 정규화 스키마 참조(공개 미리 보기)](file-event-normalization-schema.md)
- [Microsoft Sentinel DNS 정규화 스키마 참조](dns-normalization-schema.md)
- [Microsoft Sentinel 프로세스 이벤트 정규화 스키마 참조](process-events-normalization-schema.md)
- [Microsoft Sentinel 레지스트리 이벤트 정규화 스키마 참조(공개 미리 보기)](registry-event-normalization-schema.md)
