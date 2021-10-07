---
title: DHCP 정규화 스키마 참조 | Azure Sentinel Microsoft Docs
description: 이 문서에서는 Azure Sentinel DHCP 정규화 스키마에 대해 설명합니다.
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
ms.date: 10/04/2021
ms.author: bagol
ms.openlocfilehash: f8ad30e833e7b400c7d308b1f99dd457af438fa0
ms.sourcegitcommit: 1d56a3ff255f1f72c6315a0588422842dbcbe502
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/06/2021
ms.locfileid: "129622448"
---
# <a name="azure-sentinel-dhcp-normalization-schema-reference-public-preview"></a>Azure Sentinel DHCP 정규화 스키마 참조(공개 미리 보기)

DHCP 정보 모델은 DHCP 서버에서 보고하는 이벤트를 설명하는 데 사용되며 Azure Sentinel 소스 독립적 분석을 사용하도록 설정하는 데 사용됩니다.

자세한 내용은 [정규화 및 ASIM(Azure Sentinel 정보 모델)](normalization.md)을 참조하세요.

> [!IMPORTANT]
> DHCP 정규화 스키마는 현재 미리 보기로 제공됩니다. 해당 기능은 별도의 서비스 수준 계약 없이 제공되며, 프로덕션 작업에는 사용하지 않는 것이 좋습니다.
>
> [Azure Preview 추가 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)에는 베타, 미리 보기 또는 아직 일반 공급으로 릴리스되지 않은 Azure 기능에 적용되는 추가 법률 용어가 포함되어 있습니다.
>

## <a name="schema-overview"></a>스키마 개요

ASIM DHCP 스키마는 클라이언트 시스템에서 임대된 DHCP IP 주소에 대한 요청 제공 및 부여된 임대를 사용하여 DNS 서버를 업데이트하는 등 DHCP 서버 활동을 나타냅니다.

DHCP 이벤트에서 가장 중요한 필드는 [SrcIpAddr](#srcipaddr) 및 [SrcHostname입니다.](#srchostname)이 필드는 DHCP 서버가 임대를 부여하여 바인딩하고 [IpAddr](#ipaddr) 및 [Hostname](#hostname) 필드에 의해 각각 별칭이 표시됩니다. [SrcMacAddr](#srcmacaddr) 필드는 IP 주소가 임대되지 않을 때 사용되는 클라이언트 컴퓨터를 나타내기 때문에 중요합니다.  

DHCP 서버는 보안 문제 또는 네트워크 포화로 인해 클라이언트를 거부할 수 있습니다. 또한 제한된 네트워크에 연결하는 IP 주소를 임대하여 클라이언트를 격리할 수도 있습니다. [EventResult](#eventresult), [EventResultDetails](#eventresultdetails) 및 [DvcAction](#dvcaction) 필드는 DHCP 서버 응답 및 작업에 대한 정보를 제공합니다.

임대 기간은 [DhcpLeaseDuration 필드에 저장됩니다.](#dhcpleaseduration)

## <a name="schema-details"></a>스키마 세부 정보

ASIM은 [OSSEM(오픈 소스 보안 이벤트 메타데이터)](https://github.com/OTRF/OSSEM) 프로젝트에 맞춰 정렬됩니다.

OSSEM에는 ASIM DHCP 스키마와 비슷한 DHCP 스키마가 없습니다.

### <a name="log-analytics-fields"></a>Log Analytics 필드

다음 필드는 각 레코드에 대해 Log Analytics에서 생성되며 [사용자 지정 커넥터 생성](create-custom-connector.md) 시 재정의할 수 있습니다.

| **필드** | **형식** | **설명** |
| --- | --- | --- |
| <a name=timegenerated></a>**TimeGenerated** | 날짜/시간 | 보고 디바이스에서 이벤트가 생성된 시간입니다. |
| **\_ResourceId** | guid | 보고 디바이스 또는 서비스의 Azure 리소스 ID이거나 Syslog, CEF 또는 WEF를 사용하여 전달된 이벤트에 대한 로그 전달자 리소스 ID입니다. |
| **형식** | String | 레코드를 가져온 원본 테이블입니다. 이 필드는 여러 채널을 통해 다른 테이블로 동일한 이벤트를 수신할 수 있고 [EventVendor](#eventvendor) 및 [EventProduct](#eventproduct) 값이 동일한 경우에 유용합니다.<br><br>예를 들어 Sysmon 이벤트는 Event 테이블 또는 WindowsEvent 테이블에 수집할 수 있습니다. |
| | | |

> [!NOTE]
> 보안과 관련이 덜한 더 많은 Log Analytics 필드는 [Azure Monitor](../azure-monitor/logs/log-standard-columns.md)에 설명되어 있습니다.
> 

### <a name="event-fields"></a>이벤트 필드

이벤트 필드는 모든 스키마에 공통되며, 활동 자체와 보고 디바이스를 설명합니다.

| **필드** | **클래스** | **형식**  | **토론(Discussion)** |
| --- | --- | --- | --- |
| **EventMessage** | 선택 | 문자열 | 레코드에 포함되거나 레코드에서 생성된 일반 메시지 또는 설명입니다. |
| **EventCount** | 필수 | 정수 | 레코드에서 설명하는 이벤트 수입니다.<br><br>이 값은 원본에서 집계를 지원할 때 사용되며, 단일 레코드에서 여러 이벤트를 나타낼 수 있습니다.<br><br>다른 소스의 경우 로 설정해야 `1` 합니다.<br><br>예: `1`|
| **EventStartTime** | 필수 | 날짜/시간 | 원본에서 집계를 지원하고 레코드에서 여러 이벤트를 나타내는 경우 이 필드를 사용하여 첫 번째 이벤트가 생성된 시간을 지정합니다. <br><br>다른 경우에는 [TimeGenerated](#timegenerated) 필드의 별칭을 지정합니다. |
| **EventEndTime** | Alias || [TimeGenerated](#timegenerated) 필드에 대한 별칭입니다. |
| **EventType** | 필수 | Enumerated | 레코드에서 보고하는 작업을 표시합니다. <br><Br> 가능한 값은 `Assign` `Renew` , 및 `Release` `DNS Update` 입니다. <br><br>예: `Assign`| 
| <a name="eventresult"></a>**EventResult** | 필수 | Enumerated | , , , `Success` `Partial` `Failure` `NA` (해당 사항 안 함) 값 중 하나입니다.<br> <br>값은 이러한 값으로 정규화되어야 하는 다른 조건을 사용하여 원본 레코드에 제공될 수 있습니다. 또는 원본에서 EventResult 값을 파생하기 위해 분석해야 하는 [EventResultDetails](#eventresultdetails) 필드만 제공할 수 있습니다.  DHCP 서버에서 클라이언트를 격리하는 경우 이 필드를 로 설정해야 `Partial` 합니다.<br><br>예: `Success`|
| <a name="eventresultdetails"></a>**EventResultDetails** | Alias | | EventResult](#eventresult) 필드에 보고된 결과의 이유 또는 세부 정보입니다. <br><Br> 가능한 값은 `Exhausted`, `Quarantined` 및 `Denied`입니다. <br><br>예: `Exhausted` |
| **EventOriginalResultDetails**    | 선택    | 문자열     |  원본에서 제공하는 경우 [EventResultDetails](#eventresultdetails)에 대한 원래 레코드에 제공된 값입니다. Windows DHCP 서버 로그의 경우 QResult 필드 값을 여기에 저장합니다. |
| **EventOriginalUid** | 선택 | 문자열 | 원본에서 제공하는 경우 원래 레코드의 고유 ID입니다. |
| **EventOriginalType**   | 선택    | 문자열  |  원본에서 제공하는 경우 원본 이벤트 유형 또는 ID입니다.<br><br>예: `DNS Assign Failed` |
| <a name ="eventproduct"></a>**EventProduct** | 필수 | String | 이벤트를 생성하는 제품 이 필드는 원본 레코드에서 사용하지 못할 수 있으며, 이 경우 파서에서 설정해야 합니다. <br><br>예: `DHCP Server` |
| **EventProductVersion** | 선택 | 문자열 | 이벤트를 생성하는 제품의 버전 이 필드는 원본 레코드에서 사용하지 못할 수 있으며, 이 경우 파서에서 설정해야 합니다. <br><br>예: `12.1` |
| <a name="eventvendor"></a>**EventVendor** | 필수 | String | 이벤트를 생성하는 제품의 공급 업체 이 필드는 원본 레코드에서 사용하지 못할 수 있으며, 이 경우 파서에서 설정해야 합니다.<br><br>예: `Microsoft`|
| **EventSchemaVersion** | 필수 | String | 여기에 설명 된 스키마 버전은 **0.1.0** 입니다. |
| **EventSchema** | 필수 | String | 여기에 설명 된 스키마 이름은 **Dhcp** 입니다. |
| **EventReportUrl** | 선택 | 문자열 | 이벤트에 대한 추가 정보를 제공하는 리소스에 대해 이벤트에 제공된 URL입니다. |
| <a name="dvc"></a>**Dvc** | Alias | String | DHCP 서버의 고유 식별자입니다.<br><br>예: `ContosoDc.Contoso.Azure`<br><br>이 필드에는 [Dvcfqdn](#dvcfqdn), [dvcid](#dvcid), [DvcHostname](#dvchostname)또는 [dvcipaddr](#dvcipaddr) 필드가 별칭으로 있을 수 있습니다. 명백한 장치가 없는 클라우드 원본의 경우 [이벤트 제품](#eventproduct) 필드와 동일한 값을 사용 합니다. |
| <a name="dvcipaddr"></a>**DvcIpAddr** | 권장 | IP 주소 | DHCP 서버의 IP 주소입니다.<br><br>예: `2001:db8::ff00:42:8329` |
| <a name="dvchostname"></a>**DvcHostname** | 필수 | String | DHCP 서버의 호스트 이름으로, 도메인 정보를 제외 합니다. 사용할 수 있는 장치 이름이 없으면이 필드에 관련 IP 주소를 저장 합니다.<br><br>예: `DESKTOP-1282V4D` |
| <a name="dvcdomain"></a>**DvcDomain** | 권장 | String | DHCP 서버의 도메인입니다.<br><br>예: `Contoso` |
| <a name="dvcdomaintype"></a>**DvcDomainType** | 권장 | Enumerated | [Dvcdomain](#dvcdomain) 의 유형입니다 (알려진 경우). 가능한 값은 다음과 같습니다.<br>- `Windows (contoso\mypc)`<br>- `FQDN (docs.microsoft.com)`<br><br>**참고**:이 필드는 [dvcdomain](#dvcdomain) 필드가 사용 되는 경우에 필요 합니다. |
| <a name="dvcfqdn"></a>**DvcFQDN** | 선택 | 문자열 | 사용 가능한 경우 도메인 정보를 포함 하 여 DHCP 서버의 호스트 이름입니다. <br><br> 예: `Contoso\DESKTOP-1282V4D`<br><br>**참고**:이 필드는 기존의 FQDN 형식과 Windows domain\hostname 형식을 둘 다 지원 합니다. [DvcDomainType](#dvcdomaintype) 필드에는 사용 된 형식이 반영 됩니다.  |
| <a name="dvcid"></a>**DvcId** | 선택 | 문자열 | 레코드에 보고 된 DHCP 서버의 ID입니다.<br><br>예제: `ac7e9755-8eae-4ffc-8a02-50ed7a2216c3` |
| **DvcIdType** | 선택 사항 | Enumerated | 알려진 경우 [Dvcid](#dvcid)의 유형입니다. 가능한 값은 다음과 같습니다.<br> - `AzureResourceId`<br>- `MDEid`<br><br>여러 Id를 사용할 수 있는 경우 목록에서 첫 번째 Id를 사용 하 고 필드 이름  **DvcAzureResourceId** 및 **Dvcmdeid** 를 각각 사용 하 여 다른 id를 저장 합니다.<br><br>**참고**:이 필드는 [dvcid](#dvcid) 필드가 사용 되는 경우에 필요 합니다. |
| **EventSeverity** | 선택 사항 | Enumerated | `Low`DHCP 서버가 클라이언트를 격리 한 경우로 설정 하 고, `Medium` 서버에서 클라이언트를 차단한 경우로 설정 합니다. 그렇지 않으면로 설정 `Informational` 합니다. <br><br>예: `Informational`|
| <a name="dvcaction"></a>**DvcAction** | 선택 사항 | Enumerated | DHCP 서버에서 수행 하는 작업입니다. 가능한 값은 `Allow`, `Deny` 및 `Quarantine`입니다.<br><br>예: `Deny` |
| <a name="additionalfields"></a>**AdditionalFields** | 선택 사항 | 동적 | 원본에서 보존할 가치가 있는 기타 정보를 제공하는 경우 원본 필드 이름으로 유지하거나 **AdditionalFields** 동적 필드를 만들고 추가 정보를 키/값 쌍으로 추가합니다. |
| | | | |

### <a name="dhcp-specific-fields"></a>DHCP 관련 필드

아래 필드는 DHCP 이벤트와 관련이 있지만 대부분은 다른 스키마의 필드와 비슷하며 동일한 명명 규칙을 따릅니다.

| **필드** | **클래스** | **형식** | **참고** |
| --- | --- | --- | --- |
| <a name="srcipaddr"></a>**SrcIpAddr** | 필수 | IP 주소 | DHCP 서버에 의해 클라이언트에 할당 된 IP 주소입니다.<br><br>예: `192.168.12.1` |
| <a name="ipaddr"></a>**IpAddr** | Alias | | [Srcipaddr](#srcipaddr) 의 별칭 |
| <a name="requestedipaddr"></a>**Request편집기** | 선택 사항 | IP 주소 | DHCP 클라이언트에서 요청 하는 IP 주소입니다 (사용 가능한 경우).<br><br>예: `192.168.12.3` |
| <a name="srchostname"></a>**SrcHostname** | 필수 | String | DHCP 임대를 요청 하는 장치의 호스트 이름입니다. 사용할 수 있는 장치 이름이 없으면이 필드에 관련 IP 주소를 저장 합니다.<br><br>예: `DESKTOP-1282V4D` |
| <a name="hostname"></a>**N** | Alias | | [SrcHostname](#srchostname) 에 대 한 별칭 |
| <a name="srcdomain"></a>**Srcdomain** | 권장 | String | 원본 장치의 도메인입니다.<br><br>예: `Contoso` |
| <a name="srcdomaintype"></a>**SrcDomainType** | 권장 | Enumerated | [Srcdomain](#srcdomain)의 유형 (알려진 경우)입니다. 가능한 값은 다음과 같습니다.<br>- `Windows` (예: `contoso` )<br>- `FQDN` (예: `microsoft.com` )<br><br>[Srcdomain](#srcdomain) 을 사용 하는 경우 필수 사항입니다. |
| **SrcFQDN** | 선택 | 문자열 | 사용 가능한 경우 도메인 정보를 포함한 원본 디바이스 호스트 이름입니다. <br><br>**참고**:이 필드는 기존의 FQDN 형식 및 Windows domain\hostname 형식을 모두 지원 합니다. [SrcDomainType](#srcdomaintype) 필드에는 사용 된 형식이 반영 됩니다. <br><br>예: `Contoso\DESKTOP-1282V4D` |
| <a name="srcdvcid"></a>**SrcDvcId** | 선택 | 문자열 | 레코드에 보고된 원본 디바이스의 ID입니다.<br><br>예: `ac7e9755-8eae-4ffc-8a02-50ed7a2216c3` |
| **SrcDvcIdType** | 선택 사항 | Enumerated | [Srcdvcid](#srcdvcid)의 유형입니다 (알려진 경우). 가능한 값은 다음과 같습니다.<br> - `AzureResourceId`<br>- `MDEid`<br><br>여러 Id를 사용할 수 있는 경우 위의 목록에서 첫 번째 Id를 사용 하 고 다른 Id를 **SrcDvcAzureResourceId** 및 **Srcdvcmdeid** 에 각각 저장 합니다.<br><br>**참고**:이 필드는 [srcdvcid](#srcdvcid) 를 사용 하는 경우에 필요 합니다. |
| **SrcDeviceType** | 선택 사항 | Enumerated | 원본 디바이스의 형식입니다. 가능한 값은 다음과 같습니다.<br>- `Computer`<br>- `Mobile Device`<br>- `IOT Device`<br>- `Other` |
| <a name="srcuserid"></a>**SrcUserId** | 선택 | 문자열 | 원본 사용자의 컴퓨터에서 읽을 수 있는 영숫자 고유 표현입니다. 형식 및 지원되는 형식은 다음과 같습니다.<br>- **SID**(Windows): `S-1-5-21-1377283216-344919071-3415362939-500`<br>- **UID**(Linux): `4578`<br>- **AADID**(Azure Active Directory): `9267d02c-5f76-40a9-a9eb-b686f3ca47aa`<br>- **OktaId**: `00urjk4znu3BcncfY0h7`<br>- **AWSId**: `72643944673`<br><br>[Srcuseridtype](#srcuseridtype) 필드에 ID 유형을 저장 합니다. 다른 Id를 사용할 수 있는 경우 필드 이름을 SrcUserSid, SrcUserUid, SrcUserAadId, SrcUserOktaId 및 UserAwsId로 각각 정규화 하는 것이 좋습니다.<br><br>예: `S-1-12` |
| <a name="srcuseridtype"></a>**SrcUserIdType** | 선택 사항 | Enumerated | [Srcuserid](#srcuserid) 필드에 저장 된 ID의 유형입니다. 지원 되는 값은 `SID` ,, `UIS` `AADID` , `OktaId` 및 `AWSId` 입니다. |
| <a name="srcusername"></a>**SrcUsername** | 선택 | 문자열 | 사용 가능한 경우 도메인 정보를 포함 하는 원본 사용자 이름입니다. 다음 형식 중 하나를 사용 하 고 우선 순위를 지정 합니다.<br>- **Upn/메일**: `johndow@contoso.com`<br>- **Windows**: `Contoso\johndow`<br>- **DN**: `CN=Jeff Smith,OU=Sales,DC=Fabrikam,DC=COM`<br>- **단순**: `johndow`. 도메인 정보를 사용할 수 없는 경우에만 간단한 양식을 사용 합니다.<br><br>[SrcUsernameType](#srcusernametype) 필드에 사용자 이름 유형을 저장 합니다. 다른 Id를 사용할 수 있는 경우 필드 이름을 **Srcuserupn**, **srcuserupn** 및 **SrcUserDn** 으로 정규화 하는 것이 좋습니다.<br><br>자세한 내용은 [사용자 엔터티](normalization-about-schemas.md#the-user-entity)를 참조하세요.<br><br>예: `AlbertE` |
| **사용자 이름** | Alias | | [Srcusername](#srcusername) 의 별칭 |
| <a name="srcusernametype"></a>**SrcUsernameType** | 선택 사항 | Enumerated | [Srcusername](#srcusername) 필드에 저장 된 사용자 이름 유형을 지정 합니다. 지원 되는 값은 `UPN` ,, `Windows` `DN` 및 `Simple` 입니다. 자세한 내용은 [사용자 엔터티](normalization-about-schemas.md#the-user-entity)를 참조하세요.<br><br>예: `Windows` |
| **SrcUserType** | 선택 사항 | Enumerated | Actor의 형식입니다. 허용된 값은<br>- `Regular`<br>- `Machine`<br>- `Admin`<br>- `System`<br>- `Application`<br>- `Service Principal`<br>- `Other`<br><br>**참고**: 값은 이러한 값으로 정규화되어야 하는 다른 조건을 사용하여 원본 레코드에 제공될 수 있습니다. [EventOriginalUserType](#srcoriginalusertype) 필드에 원래 값을 저장 합니다. |
| <a name="srcoriginalusertype"></a>**SrcOriginalUserType** | | | 원본에서 제공 하는 경우 원래 원본 사용자 형식입니다. |
| <a name="srcmacaddr"></a>**SrcMacAddr** | 필수 | MAC 주소 | DHCP 임대를 요청 하는 클라이언트의 MAC 주소입니다. <br><br>**참고**: Windows DHCP 서버는 파서가 삽입 해야 하는 콜론을 생략 하 고 비표준 방식으로 MAC 주소를 기록 합니다.<br><br>예: `06:10:9f:eb:8f:14` |
| <a name="dhcpleaseduration"></a>**DhcpLeaseDuration** | 선택 | 정수 | 클라이언트에 부여 된 임대의 길이 (초)입니다. |  
|<a name="dhcpsessionid"></a>**DhcpSessionId** | 선택 | 문자열 | 보고 디바이스에서 보고한 세션 식별자 DHCP 서버의 Windows TransactionID 필드로 설정 합니다. <br><br>예: `2099570186` |
| **SessionId** | Alias | String | [DhcpkSessionId](#dhcpsessionid) 에 대 한 별칭 |
| <a name="dhcpsessionduration"></a>**DhcpSessionDuration** | 선택 | 정수 | DHCP 세션을 완료 하는 데 걸린 시간 (밀리초)입니다.<br><br>예: `1500` |
| **기간** | Alias | | [Dhcpsessionduration](#dhcpsessionduration) 의 별칭 |
| **DhcpSrcDHCId** | 선택 | 문자열 | [RFC4701](https://datatracker.ietf.org/doc/html/rfc4701) 에서 정의한 DHCP 클라이언트 ID입니다. |
| **DhcpCircuitId** | 선택 | 문자열 | [RFC3046](https://datatracker.ietf.org/doc/html/rfc3046) 에 정의 된 DHCP 회로 ID |
| **DhcpSubscriberId** | 선택 | 문자열 | [RFC3993](https://datatracker.ietf.org/doc/html/rfc3993) 에 의해 정의 된 DHCP 구독자 ID |
| **DhcpVendorClassId**  | 선택 | 문자열 | [RFC3925](https://datatracker.ietf.org/doc/html/rfc3925)에 정의 된 DHCP 공급 업체 클래스 Id입니다. |
| **DhcpVendorClass**  | 선택 | 문자열 | [RFC3925](https://datatracker.ietf.org/doc/html/rfc3925)에 의해 정의 된 DHCP 공급 업체 클래스입니다.|
| **DhcpUserClassId**  | 선택 | 문자열 | [RFC3004](https://datatracker.ietf.org/doc/html/rfc3004)에 의해 정의 된 DHCP 사용자 클래스 Id입니다.|
| **DhcpUserClass** | 선택 | 문자열 | [RFC3004](https://datatracker.ietf.org/doc/html/rfc3004)에 의해 정의 된 DHCP 사용자 클래스입니다.|
| | | | |

## <a name="next-steps"></a>다음 단계

자세한 내용은 다음을 참조하세요.

- [ASIM 웨비나](https://www.youtube.com/watch?v=WoGD-JeC7ng)를 시청하거나 [슬라이드](https://1drv.ms/b/s!AnEPjr8tHcNmjDY1cro08Fk3KUj-?e=murYHG)를 검토
- [Azure Sentinel 정보 모델 스키마](normalization-about-schemas.md)
- [Azure Sentinel 정보 모델 파서](normalization-about-parsers.md)
- [Azure Sentinel 정보 모델 콘텐츠](normalization-content.md)