---
title: Microsoft Sentinel 인증 정규화 스키마 참조 | Microsoft Docs
description: 이 문서에서는 Microsoft Sentinel 인증 정규화 스키마에 대해 설명합니다.
author: batamig
ms.topic: reference
ms.date: 11/09/2021
ms.author: bagol
ms.custom: ignite-fall-2021
ms.openlocfilehash: 10b6c715ed9e8e1d37ff8c1d239d792433920192
ms.sourcegitcommit: 1244a72dbec39ac8cf16bb1799d8c46bde749d47
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/18/2021
ms.locfileid: "132758075"
---
# <a name="microsoft-sentinel-authentication-normalization-schema-reference-public-preview"></a>Microsoft Sentinel 인증 정규화 스키마 참조(공개 미리 보기)

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

인증 정보 모델은 사용자 인증, 로그인 및 로그아웃과 관련된 이벤트를 설명하는 데 사용됩니다. 인증 이벤트는 일반적으로 이벤트 스트림의 일부로 다른 이벤트와 함께 많은 보고 디바이스에서 보냅니다.

예를 들어 Windows는 여러 인증 이벤트를 다른 OS 활동 이벤트와 함께 보냅니다. 따라서 대부분의 경우 인증 이벤트는 서로 다른 Microsoft Sentinel 테이블에 저장되며 KQL 함수를 사용하여 정규화됩니다. 이 함수는 관련 인증 이벤트만 필터링합니다.

인증 이벤트에는 VPN 게이트웨이 또는 도메인 컨트롤러와 같은 인증에 중점을 둔 시스템의 이벤트와 최종 시스템(예: 컴퓨터 또는 방화벽)에 대한 직접 인증이 모두 포함됩니다.

Microsoft Sentinel의 정규화에 대한 자세한 내용은 [정규화 및 ASIM(고급 SIEM 정보 모델)을](normalization.md)참조하세요.

> [!IMPORTANT]
> 인증 정규화 스키마는 현재 미리 보기에 있습니다. 해당 기능은 별도의 서비스 수준 계약 없이 제공되며, 프로덕션 작업에는 사용하지 않는 것이 좋습니다.
>
> [Azure Preview 추가 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)에는 베타, 미리 보기 또는 아직 일반 공급으로 릴리스되지 않은 Azure 기능에 적용되는 추가 법률 용어가 포함되어 있습니다.
>

## <a name="parsers"></a>파서

Microsoft Sentinel은 다음과 같은 기본 제공 제품별 인증 이벤트 파서를 제공합니다. 

- Log Analytics 에이전트 또는 Azure Monitor 에이전트를 사용하여 수집되고 보안 이벤트(4624, 4625, 4634 및 4647)로 보고된 **Windows 로그인**
- Microsoft 365 Defender 커넥터를 사용하여 수집되고, Microsoft 365 Defender for Endpoint에서 보고한 **Windows 로그인**
- Azure Active Directory 커넥터를 사용하여 수집된 **Azure Active Directory 로그인**. 일반 비대화형 관리 ID 및 서비스 주체 로그인에 대해 별도의 파서가 제공됩니다.
- AWS CloudTrail 커넥터를 사용하여 수집된 **AWS 로그인**
- Okta 커넥터를 사용하여 수집된 **Okta 인증**

나열된 모든 파서를 통합하여 구성된 모든 원본에서 데이터를 분석하도록 하는 원본 중립적 파서를 사용하려면 쿼리에서 **imAuthentication** 을 테이블 이름으로 사용합니다.

[Microsoft Sentinel GitHub 리포지토리에서](https://aka.ms/AzSentinelAuth) [소스와 관련이 없는 소스별 파서를](normalization-about-parsers.md) 배포합니다.



## <a name="normalized-content"></a>정규화된 콘텐츠

인증 ASIM 스키마 지원에는 정규화된 인증 파서가 있는 다음과 같은 기본 제공 분석 규칙에 대한 지원도 포함됩니다. Microsoft Sentinel GitHub 리포지토리에 대한 링크는 아래 참조로 제공되지만 [Microsoft Sentinel Analytics 규칙 갤러리에서](detect-threats-built-in.md)이러한 규칙을 찾을 수도 있습니다. 연결된 GitHub 페이지를 사용하여 나열된 규칙에 대한 관련 헌팅 쿼리를 복사합니다.

- [잠재적인 암호 스프레이 공격(인증 정규화 사용)](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/ASimAuthentication/imAuthPasswordSpray.yaml)
 - [사용자 자격 증명에 대한 무차별 암호 대입 공격(인증 정규화 사용)](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/ASimAuthentication/imAuthBruteForce.yaml)
 - [3시간 이내에 다른 국가의 사용자 로그인(인증 정규화 사용)](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/ASimAuthentication/imAuthSigninsMultipleCountries.yaml)
 - [사용자지 않도록 설정된 계정에 대한 로그인을 시도하는 IP에서 로그인(인증 정규화 사용)](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/ASimAuthentication/imSigninAttemptsByIPviaDisabledAccounts.yaml)


정규화된 인증 분석 규칙은 원본 전체에서 공격을 탐지하므로 고유합니다. 따라서 예를 들어 사용자가 서로 다른 국가에서 관련 없는 다른 시스템에 로그인한 경우 Microsoft Sentinel은 이제 이 위협을 감지합니다.

## <a name="schema-details"></a>스키마 세부 정보

인증 정보 모델은 [OSSEM 로그온 엔터티 스키마](https://github.com/OTRF/OSSEM/blob/master/docs/cdm/entities/logon.md)에 맞춰 정렬됩니다.

다음 표에서 *형식* 은 논리적 형식을 나타냅니다. 자세한 내용은 [논리적 형식](normalization-about-schemas.md#logical-types)을 참조하세요.

### <a name="log-analytics-fields"></a>Log Analytics 필드

다음 필드는 각 레코드에 대해 Log Analytics에서 생성되며, 사용자 지정 커넥터를 만들 때 재정의할 수 있습니다.

|필드  |형식  |설명  |
|---------|---------|---------|
|<a name ="timegenerated"></a>**TimeGenerated**     |  Datetime       |보고 디바이스에서 이벤트가 생성된 시간입니다.         |
|**_ResourceId**     | guid        |  보고 디바이스 또는 서비스의 Azure 리소스 ID이거나 Syslog, CEF 또는 WEF를 사용하여 전달된 이벤트에 대한 로그 전달자 리소스 ID입니다.       |
| **형식** | String | 레코드를 가져온 원본 테이블입니다. 이 필드는 여러 채널을 통해 다른 테이블로 동일한 이벤트를 수신할 수 있고 EventVendor 및 EventProduct 값이 동일한 경우에 유용합니다.<br><br>예를 들어 Sysmon 이벤트는 Event 테이블 또는 WindowsEvent 테이블에 수집할 수 있습니다. |
|     |         |         |

> [!NOTE]
> Log Analytics는 보안 사용 사례와 관련성이 낮은 다른 필드도 추가합니다. 자세한 내용은 [Azure Monitor 로그의 표준 열](../azure-monitor/logs/log-standard-columns.md)을 참조하세요.
>

### <a name="event-fields"></a>이벤트 필드

이벤트 필드는 모든 스키마에 공통되며, 활동 자체와 보고 디바이스를 설명합니다.

| 필드               | 클래스       | 형식       |  Description        |
|---------------------|-------------|------------|--------------------|
| **EventMessage**        | 선택    | 문자열     |     레코드에 포함되거나 레코드에서 생성된 일반 메시지 또는 설명입니다.   |
| **EventCount**          | 필수   | 정수    |     레코드에서 설명하는 이벤트 수입니다. <br><br>이 값은 원본에서 집계를 지원할 때 사용되며, 단일 레코드에서 여러 이벤트를 나타낼 수 있습니다. <br><br>다른 원본의 경우 `1`로 설정합니다. <br><br>**참고**: 이 필드는 일관성을 위해 포함되지만, 일반적으로 인증 이벤트에는 사용되지 않습니다.  |
| **EventStartTime**      | 필수   | 날짜/시간  |      원본에서 집계를 지원하고 레코드에서 여러 이벤트를 나타내는 경우 이 필드는 첫 번째 이벤트가 생성된 시간을 지정합니다. 그렇지 않은 경우 이 필드는 [TimeGenerated](#timegenerated) 필드의 별칭을 지정합니다.<br><br>**참고**: 이 필드는 일관성을 위해 포함되지만, 일반적으로 인증 이벤트에는 사용되지 않습니다.  |
| **EventEndTime**        | 필수   | Alias      |      [TimeGenerated](#timegenerated) 필드에 대한 별칭입니다.    |
| **EventType**           | 필수   | Enumerated |    레코드에서 보고하는 작업을 설명합니다. <br><br>인증 레코드의 경우 지원되는 값은 다음과 같습니다. <br>- `Logon` <br>- `Logoff`<br><br>**참고**: 값은 이러한 값으로 정규화되어야 하는 다른 조건을 사용하여 원본 레코드에 제공될 수 있습니다. 원래 값은 [EventOriginalType](#eventoriginaltype) 필드에 저장해야 합니다.|
| <a name ="eventoriginaltype"></a>**EventOriginalType**           | 선택   | 문자열 |    원본 레코드에 제공된 이벤트 유형 또는 ID입니다. <br><br>예: `4625`|
| **EventResult**         | 필수   | Enumerated |  지원되는 다음 값 중 하나로 정규화된 이벤트의 결과를 설명합니다. <br><br>- `Success`<br>- `Partial`<br>- `Failure`<br>- `NA`(해당 사항 없음) <br><br>**참고**: 값은 이러한 값으로 정규화되어야 하는 다른 조건을 사용하여 원본 레코드에 제공될 수 있습니다. <br><br>또한 원본에서 **EventResult** 값을 얻기 위해 분석해야 하는 [EventResultDetails](#eventresultdetails) 필드에 대한 값만 제공할 수 있습니다. |
| <a name ="eventresultdetails"></a>**EventResultDetails**         | 선택   | 문자열 |  다음 값 중 하나입니다. <br><br>-  `No such user or password`. 이 값은 원래 이벤트에서 암호를 참조하지 않고 이러한 사용자가 없다고 보고하는 경우에도 사용해야 합니다. <br>-   `Incorrect password`<br>-   `Account expired`<br>-  `Password expired`<br>- `User locked`<br>-  `User disabled`<br>-    `Logon violates policy`. 이 값은 원본 이벤트에서 MFA 필요, 업무 시간 외 로그온, 조건부 액세스 제한 또는 너무 빈번한 시도 등을 보고하는 경우에 사용해야 합니다.<br>-  `Session expired`<br>-  `Other`<br><br>**참고**: 값은 이러한 값으로 정규화되어야 하는 다른 조건을 사용하여 원본 레코드에 제공될 수 있습니다.|
| **EventSubType**    | 선택    | 문자열     |   일반적으로 Windows 로그온 유형에 사용되는 로그인 유형입니다. <br><br>예: `Interactive`|
| **EventOriginalResultDetails**    | 선택    | 문자열     |  원본에서 제공하는 경우 [EventResultDetails](#eventresultdetails)에 대한 원래 레코드에 제공된 값입니다.|
| **EventOriginalUid**    | 선택    | 문자열     |   원본에서 제공하는 경우 원래 레코드의 고유 ID입니다.|
| **EventOriginalType**   | 선택    | 문자열     |   원본에서 제공하는 경우 원본 이벤트 유형 또는 ID입니다.<br><br>예: `4624`|
| <a name ="eventproduct"></a>**EventProduct**        | 필수   | String     |             이벤트를 생성하는 제품 <br><br>예: `Windows`<br><br>**참고**: 이 필드는 원본 레코드에서 사용할 수 없습니다. 이러한 경우 이 필드는 파서에서 설정해야 합니다.           |
| **EventProductVersion** | 선택    | 문자열     | 이벤트를 생성하는 제품의 버전 <br><br>예: `10` <br><br>**참고**: 이 필드는 원본 레코드에서 사용할 수 없습니다. 이러한 경우 이 필드는 파서에서 설정해야 합니다.     |
| **EventVendor**         | 필수   | String     |           이벤트를 생성하는 제품의 공급 업체 <br><br>예: `Microsoft`  <br><br>**참고**: 이 필드는 원본 레코드에서 사용할 수 없습니다. 이러한 경우 이 필드는 파서에서 설정해야 합니다.  |
| **EventSchemaVersion**  | 필수   | String     |    스키마의 버전입니다. 여기에 설명된 스키마의 버전은 `0.1`입니다.         |
| **EventReportUrl**      | 선택    | 문자열     | 이벤트에 대한 추가 정보를 제공하는 리소스에 대해 이벤트에 제공된 URL입니다.|
| <a name ="dvc"></a>**Dvc** | 필수       | String     |              이벤트가 발생한 디바이스의 고유 식별자입니다. <br><br>이 필드는 [DvcId](#dvcid), [DvcHostname](#dvchostname) 또는 [DvcIpAddr](#dvcipaddr) 필드의 별칭을 지정할 수 있습니다. 명백한 디바이스가 없는 클라우드 원본의 경우 [이벤트 제품](#eventproduct) 필드와 동일한 값을 사용하세요.             |
| <a name ="dvcipaddr"></a>**DvcIpAddr**           | 권장 | IP 주소 |         프로세스 이벤트가 발생한 디바이스의 IP 주소입니다.  <br><br>예: `45.21.42.12`  <br><br>**참고**: 식별자를 사용할 수 있지만 형식을 알 수 없는 경우 이 필드를 사용하지 마세요. 자세한 내용은 [Dvc](#dvc)를 참조하세요.  |
| <a name ="dvchostname"></a>**DvcHostname**         | 권장 | 호스트 이름   |               프로세스 이벤트가 발생한 디바이스의 호스트 이름입니다. <br><br>예: `ContosoDc.Contoso.Azure`      <br><br>**참고**: 식별자를 사용할 수 있지만 형식을 알 수 없는 경우 이 필드를 사용하지 마세요. 자세한 내용은 [Dvc](#dvc)를 참조하세요.          |
| <a name ="dvcid"></a>**DvcId**               | 선택    | 문자열     |  프로세스 이벤트가 발생한 디바이스의 고유 ID입니다. <br><br>예: `41502da5-21b7-48ec-81c9-baeea8d7d669`   |
| **AdditionalFields**    | 선택 사항    | 동적    | 원본에서 보존할 가치가 있는 추가 정보를 제공하는 경우 원본 필드 이름으로 유지하거나 동적 **AdditionalFields** 필드를 만들고 추가 정보를 키/값 쌍으로 추가합니다.    |
| | | | |


## <a name="authentication-specific-fields"></a>인증 관련 필드

아래 표에 나열된 필드는 인증 이벤트와 관련이 있지만, 다른 스키마의 필드와 비슷하며 비슷한 명명 규칙을 따릅니다.

인증 이벤트에서 참조하는 엔터티는 다음과 같습니다.

- **배우**
- **ActingApp**
- **SrcDvc**
- **TargetUser**
- **TargetApp**
- **TargetDvc**

이러한 엔터티 간의 관계는 다음과 같이 명확하게 설명됩니다.

*원본 디바이스*(**SrcDvc**)에서 *작업 애플리케이션*(**ActingApp**)을 실행하는 **작업자** 는 **TargetUser** 를 *대상 디바이스*(**TargetDvc**)의 *대상 애플리케이션*(**TargetApp**)에 인증하려고 시도합니다.

| 필드          | 클래스        | 형식       | 설명   |
|---------------|--------------|------------|-----------------|
|**LogonMethod** |선택 |문자열 |인증을 수행하는 데 사용되는 방법입니다. <br><br>예: `Username & Password` |
|**LogonProtocol** |선택 |문자열 |인증을 수행하는 데 사용되는 프로토콜입니다. <br><br>예: `NTLM` |
| <a name="actoruserid"></a>**ActorUserId**    | 선택 사항  | UserId     |   컴퓨터에서 읽을 수 있는 영숫자의 작업자에 대한 고유한 표현입니다. 자세한 내용은 [사용자 엔터티](normalization-about-schemas.md#the-user-entity)를 참조하세요.  <br><br>예: `S-1-12-1-4141952679-1282074057-627758481-2916039507`    |
| **ActorUserIdType**| 선택 사항  | UserIdType     |  [ActorUserId](#actoruserid) 필드에 저장된 ID의 형식입니다. 자세한 내용은 [사용자 엔터티](normalization-about-schemas.md#the-user-entity)를 참조하세요.         |
| <a name="actorusername"></a>**ActorUsername**  | 선택 사항    | 사용자 이름     | 사용 가능한 경우 도메인 정보를 포함한 작업자의 사용자 이름입니다. 자세한 내용은 [사용자 엔터티](normalization-about-schemas.md#the-user-entity)를 참조하세요.<br><br>예: `AlbertE`     |
| **ActorUsernameType**              | 선택 사항    | UsernameType |   [ActorUsername](#actorusername) 필드에 저장된 사용자 이름의 형식을 지정합니다. 자세한 내용은 [사용자 엔터티](normalization-about-schemas.md#the-user-entity)를 참조하세요. <br><br>예: `Windows`       |
| **ActorUserType** | 선택 | 문자열 | 작업자의 형식입니다. <br><br>예: `Guest` |
| **ActorSessionId** | 선택     | 문자열     |   작업자의 로그인 세션에 대한 고유 ID입니다.  <br><br>예: `102pTUgC3p8RIqHvzxLCHnFlg`  |
| **ActingAppId** | 선택 | 문자열 | 프로세스, 브라우저 또는 서비스를 포함하여 작업자를 대신하여 권한을 부여하는 애플리케이션의 ID입니다. <br><br>예: `0x12ae8` |
| **ActiveAppName** | 선택 | 문자열 | 프로세스, 브라우저 또는 서비스를 포함하여 작업자를 대신하여 권한을 부여하는 애플리케이션의 이름입니다. <br><br>예: `C:\Windows\System32\svchost.exe` |
| **ActingAppType** | 선택 사항 | Enumerated | 작업 애플리케이션의 형식입니다. 지원되는 값은 다음과 같습니다. <br> <br>- `Process` <br>- `Browser` <br>- `Resource` <br>- `Other` |
| **HttpUserAgent** |   선택    | 문자열 |  HTTP 또는 HTTPS를 통해 인증이 수행되는 경우 이 필드의 값은 인증을 수행할 때 작업 애플리케이션에서 제공하는 user_agent HTTP 헤더입니다.<br><br>예: `Mozilla/5.0 (iPhone; CPU iPhone OS 12_1 like Mac OS X) AppleWebKit/605.1.15 (KHTML, like Gecko) Version/12.0 Mobile/15E148 Safari/604.1` |
|<a name="targetuserid"></a> **TargetUserId**   | 선택 사항 | UserId     | 컴퓨터에서 읽을 수 있는 영숫자의 대상 사용자에 대한 고유한 표현입니다. 자세한 내용은 [사용자 엔터티](normalization-about-schemas.md#the-user-entity)를 참조하세요.            <br><br> 예: `00urjk4znu3BcncfY0h7`    |
| **TargetUserIdType**               | 선택 사항 | UserIdType     | [TargetUserId](#targetuserid) 필드에 저장된 사용자 ID의 형식입니다. 자세한 내용은 [사용자 엔터티](normalization-about-schemas.md#the-user-entity)를 참조하세요.            <br><br> 예제: `SID`  |
| <a name="targetusername"></a>**TargetUsername** | 선택 사항 | 사용자 이름     | 사용 가능한 경우 도메인 정보를 포함한 대상 사용자의 사용자 이름입니다. 자세한 내용은 [사용자 엔터티](normalization-about-schemas.md#the-user-entity)를 참조하세요.  <br><br>예: `MarieC`      |
| **TargetUsernameType**             |선택 사항  | UsernameType | [TargetUsername](#targetusername) 필드에 저장된 사용자 이름의 형식을 지정합니다. 자세한 내용은 [사용자 엔터티](normalization-about-schemas.md#the-user-entity)를 참조하세요.          |
| **TargetUserType** | 선택 | 문자열 | 대상 사용자의 형식입니다. <br><br>예: `Member` |
| **TargetSessionId** | 선택 | 문자열 | 원본 디바이스에 있는 TargetUser의 로그인 세션 식별자입니다. |
| **사용자**           | Alias        |     String       | [TargetUsername](#targetusername)에 대한 별칭 또는 [TargetUsername](#targetusername)이 정의되지 않은 경우 [TargetUserId](#targetuserid)에 대한 별칭입니다. <br><br>예: `CONTOSO\dadmin`     |
|**SrcDvcId** |선택 |문자열 |레코드에 보고된 원본 디바이스의 ID입니다. <br><br>예: `ac7e9755-8eae-4ffc-8a02-50ed7a2216c3` |
| <a name="srcdvchostname"></a>**SrcDvcHostname** |선택 사항 | 호스트 이름| 사용 가능한 경우 도메인 정보를 포함한 원본 디바이스 호스트 이름입니다. 자세한 내용은 [디바이스 엔터티](normalization-about-schemas.md#the-device-entity)를 참조하세요. <br><br>예: `Constoso\DESKTOP-1282V4D`|
| **SrcDvcHostnameType**|선택 사항 |HostnameType |알려진 경우 [SrcDvcHostname](#srcdvchostname)의 형식입니다. 자세한 내용은 [디바이스 엔터티](normalization-about-schemas.md#the-device-entity)를 참조하세요. |
|**SrcDvcType** |선택 사항 |Enumerated |원본 디바이스의 형식입니다. 가능한 값은 다음과 같습니다. <br><br>- `Computer`<br>- `Mobile Device` <br>- `IOT Device` <br>- `Other` |
|**SrcDvcIpAddr**|권장 |IP 주소 |원본 디바이스의 IP 주소입니다. <br><br>예: `185.175.35.214` |
| **SrcDvcOs**|선택 |문자열 |원본 디바이스의 OS입니다. <br><br>예: `Windows 10` |
|**SrcIsp** | 선택|문자열 |원본 디바이스에서 인터넷에 연결하는 데 사용하는 ISP(인터넷 서비스 공급자)입니다. <br><br>예: `corpconnect` |
| **SrcGeoCountry**|선택 사항 |국가 |예: `Canada` <br><br>자세한 내용은 [논리적 형식](normalization-about-schemas.md#logical-types)을 참조하세요. |
| **SrcGeoCity**|선택 사항 |City |예: `Montreal` <br><br>자세한 내용은 [논리적 형식](normalization-about-schemas.md#logical-types)을 참조하세요. |
|**SrcGeoRegion** | 선택 사항|지역 | 예: `Quebec` <br><br>자세한 내용은 [논리적 형식](normalization-about-schemas.md#logical-types)을 참조하세요.|
| **SrcGeoLongtitude**|선택 사항 |경도  | 예: `-73.614830` <br><br>자세한 내용은 [논리적 형식](normalization-about-schemas.md#logical-types)을 참조하세요.|
| **SrcGeoLatitude**|선택 사항 |위도 |예: `45.505918` <br><br>자세한 내용은 [논리적 형식](normalization-about-schemas.md#logical-types)을 참조하세요. |
|**TargetAppId** |선택 | 문자열| 권한 부여가 필요한 애플리케이션의 ID이며, 디바이스에서 할당하는 경우가 많습니다. <br><br>예: `89162` |
|<a name="targetappname"></a>**TargetAppName** |선택 |문자열 |서비스, URL 또는 SaaS 애플리케이션을 포함하여 권한 부여가 필요한 애플리케이션의 이름입니다. <br><br>예: `Saleforce` |
| **TargetAppType**|선택 |문자열 |작업자를 대신하여 권한을 부여하는 애플리케이션의 형식입니다. 지원되는 값은 다음과 같습니다.  <br><br>- `Process` <br>- `Service` <br>- `Resource` <br>- `URL` <br>- `SaaS application` <br>- `Other`|
|**TargetUrl** |선택 |문자열 |대상 애플리케이션과 연결된 URL입니다. <br><br>예: `https://console.aws.amazon.com/console/home?fromtb=true&hashArgs=%23&isauthcode=true&nc2=h_ct&src=header-signin&state=hashArgsFromTB_us-east-1_7596bc16c83d260b` |
|**LogonTarget**| Alias| |[TargetAppName](#targetappname), *URL* 또는 [TargetDvcHostname](#targetdvchostname) 필드 중 인증 대상을 가장 잘 설명하는 필드에 대한 별칭입니다. |
|**TargetDvcId** |선택 | 문자열|레코드에 보고된 대상 디바이스의 ID입니다. <br><br> 예: `2739` |
|<a name="targetdvchostname"></a>**TargetDvcHostname** | 권장| String|사용 가능한 경우 도메인 정보를 포함한 대상 디바이스 호스트 이름입니다. 자세한 내용은 [디바이스 엔터티](normalization-about-schemas.md#the-device-entity)를 참조하세요. |
| **TargetDvcHostnameType**|권장 | String|[TargetDvcHostname](#targetdvchostname)의 형식입니다. 자세한 내용은 [디바이스 엔터티](normalization-about-schemas.md#the-device-entity)를 참조하세요. |
|**TargetDvcType** |선택 사항 | Enumerated|대상 디바이스의 이름입니다. 지원되는 값은 다음과 같습니다. <br><br>- `Computer`<br>- `Mobile Device` <br>- `IOT Device` <br>- `Other` |
|<a name="targetdvcipaddr"></a>**TargetDvcIpAddr** |선택 사항 | IP 주소|대상 디바이스의 IP 주소입니다. <br><br>예: `2.2.2.2` |
|**TargetDvc** |Alias | |   대상 디바이스의 고유 식별자입니다. <br><br>특정 원본에 가장 적합한 값([TargetDvcHostname](#targetdvchostname), [TargetDvcIpAddr](#targetdvcipaddr))의 별칭을 선택하거나 더 적절한 경우 다른 ID를 선택합니다. |
| **TargetDvcOs**| 선택| 문자열| 대상 디바이스의 OS입니다. <br><br>예: `Windows 10`|
| **TargetPortNumber**|선택 |정수 |대상 디바이스의 포트입니다.|
| | | | |




## <a name="next-steps"></a>다음 단계

자세한 내용은 다음을 참조하세요.

- [Microsoft Sentinel의 정규화](normalization.md)
- [Microsoft Sentinel DNS 정규화 스키마 참조](dns-normalization-schema.md)
- [Microsoft Sentinel 파일 이벤트 정규화 스키마 참조(공개 미리 보기)](file-event-normalization-schema.md)
- [Microsoft Sentinel 네트워크 정규화 스키마 참조](./network-normalization-schema.md)
- [Microsoft Sentinel 프로세스 이벤트 정규화 스키마 참조(공개 미리 보기)](process-events-normalization-schema.md)
