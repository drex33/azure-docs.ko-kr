---
title: ASIM(Azure Sentinel 정보 모델) 스키마 | Microsoft Docs
description: 이 문서에서는 ASIM(Azure Sentinel Information Model) 스키마와 이러한 스키마가 ASIM이 다양한 원본의 데이터를 균일한 프레젠테이션으로 표현으로 정규화하는 데 어떻게 도움이 되는지 설명합니다.
services: sentinel
cloud: na
documentationcenter: na
author: oshezaf
manager: rkarlin
ms.assetid: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 08/11/2021
ms.author: ofshezaf
ms.openlocfilehash: 828524e225f660cab2c11d23c5657ca82ae8781e
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124796517"
---
# <a name="azure-sentinel-information-model-asim-schemas-public-preview"></a>ASIM(Azure Sentinel 정보 모델) 스키마(퍼블릭 미리 보기)

[ASIM](normalization.md) 스키마는 활동을 나타내는 필드 집합입니다. 쿼리에서 정규화된 스키마의 필드를 사용하면 쿼리가 정규화된 모든 원본에서 작동합니다.

스키마 참조는 각 스키마를 구성하는 필드를 간략하게 설명합니다. ASIM은 현재 다음 스키마를 정의합니다.

 - [네트워크 세션](normalization-schema.md)
 - [DNS 작업](dns-normalization-schema.md)
 - [프로세스 이벤트](process-events-normalization-schema.md)
 - [인증 이벤트](authentication-normalization-schema.md)
 - [레지스트리 이벤트](registry-event-normalization-schema.md)
 - [파일 활동](file-event-normalization-schema.md)

> [!IMPORTANT]
> ASIM은 현재 미리 보기로 제공되고 있습니다. [Azure Preview 추가 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)에는 베타, 미리 보기 또는 아직 일반 공급으로 릴리스되지 않은 Azure 기능에 적용되는 추가 법률 용어가 포함되어 있습니다.
>

## <a name="schema-concepts"></a>스키마 개념

다음 개념은 데이터가 스키마에서 다루지 않는 정보를 포함하는 경우 스키마 참조 문서를 이해하고 정규화된 방식으로 스키마를 확장하는 데 도움이 됩니다.


|개념  |설명  |
|---------|---------|
|**필드 이름**     |   각 스키마의 핵심은 필드 이름입니다. 필드 이름은 다음 그룹에 속합니다. <br><br>- 모든 스키마에 공통된 필드 <br>- 특정 스키마에 국한된 필드 <br>- 스키마에 참여하는 사용자와 같은 엔터티를 나타내는 필드 엔터티를 나타내는 필드는 [스키마 간에 유사합니다](#entities). <br><br>원본에 문서화된 스키마에 없는 필드가 있는 경우 일관성을 유지하기 위해 정규화됩니다. 추가 필드가 엔터티를 나타내는 경우 엔터티 필드 지침에 따라 정규화됩니다. 그렇지 않으면 스키마는 모든 스키마에서 일관성을 유지하려고 합니다.<br><br> 예를 들어, DNS 서버 활동 로그는 사용자 정보를 제공하지 않지만 엔드포인트의 DNS 활동 로그에는 사용자 엔터티 지침에 따라 정규화할 수 있는 사용자 정보가 포함될 수 있습니다.      |
|**필드 형식**     |  각 데이터 필드에는 형식이 있습니다. Log Analytics 작업 영역에는 제한된 데이터 형식 세트가 집합이 있습니다. 따라서 Azure Sentinel은 Log Analytics에서 적용하지 않지만 스키마 호환성을 위해 필요한 많은 스키마 필드에 대해 논리적 형식을 사용합니다. 논리적 필드 형식은 값과 필드 이름이 원본 간에 일관되도록 합니다.  <br><br>자세한 내용은 [논리적 형식](#logical-types)을 참조하세요.     |
|**필드 클래스**     |필드에는 파서에서 필드를 구현해야 하는 경우를 정의하는 여러 클래스가 있을 수 있습니다. <br><br>-    **필수** 필드는 모든 파서에 나타나야 합니다. 원본이 이 값에 대한 정보를 제공하지 않거나 데이터를 추가할 수 없는 경우 정규화된 스키마를 참조하는 대부분의 콘텐츠 항목이 지원되지 않습니다.<br>-  **권장** 필드는 사용 가능한 경우 정규화해야 합니다. 그러나 모든 원본에서 사용할 수 있는 것은 아니며, 정규화된 스키마를 참조하는 콘텐츠 항목은 가용성을 고려해야 합니다. <br>-  **선택적** 필드(사용 가능한 경우)는 정규화되거나 원래 형식을 유지할 수 있습니다. 일반적으로 최소 파서는 성능상의 이유로 이러한 필드를 정규화하지 않습니다.    |
|**엔터티**     | 이벤트는 사용자, 호스트, 프로세스 또는 파일과 같은 엔터티를 중심으로 진행되며 각 엔터티는 설명하는 데 여러 필드가 필요할 수 있습니다. 예를 들어 호스트에는 이름 및 IP 주소가 있을 수 있습니다. <br><br>단일 레코드에는 원본 및 대상 호스트와 같이 유형이 같은 엔터티가 여러 개 포함될 수 있습니다. <br><br>Azure Sentinel 정보 모델은 엔터티를 일관되게 설명하는 방법을 정의하며 엔터티는 스키마 확장을 허용합니다. <br><br>예를 들어 네트워크 세션 스키마에는 프로세스 정보가 포함되지 않지만 일부 이벤트 원본은 추가할 수 있는 프로세스 정보를 제공합니다. 자세한 내용은 [엔터티](#entities)를 참조하세요. |
|**별칭**     |  경우에 따라 사용자마다 필드에 다른 이름을 지정하려고 할 수 있습니다. 예를 들어 DNS 용어에서는 `query`라는 필드가 예상되지만, 도메인 이름을 포함하는 경우가 좀 더 일반적입니다. 별칭은 지정된 값에 여러 이름을 허용하여 이러한 모호성 문제를 해결합니다. 별칭 클래스는 별칭이 있는 필드와 동일합니다.       |
| | |

## <a name="logical-types"></a>논리적 형식

각 데이터 필드에는 형식이 있습니다. 일부는 기본 제공 Azure Log Analytics 형식(예: `string`, `int`, `datetime` 또는 `dynamic`)을 갖습니다. 필드 값을 정규화해야 하는 방법을 나타내는 논리적 형식을 갖는 필드도 있습니다.

|데이터 형식  |실제 형식  |서식과 값  |
|---------|---------|---------|
|**Boolean**     |   Bool      |    부울 값의 숫자 또는 문자열 표현 대신 네이티브 KQL 부울 데이터 형식을 사용합니다.     |
|**Enumerated**     |  String       |   필드에 대해 명시적으로 정의된 값 목록입니다. 스키마 정의에는 허용되는 값이 나열됩니다.      |
|**날짜/시간**     |  수집 방법 기능에 따라 다음 물리적 표현을 내림차순으로 사용합니다. <br><br>- Log Analytics 기본 제공 날짜/시간 형식 <br>- Log Analytics 날짜/시간 숫자 표현을 사용하는 정수 필드 <br>- Log Analytics 날짜/시간 숫자 표현을 사용하는 문자열 필드 <br>- 지원되는 [Log Analytics 날짜/시간 형식](/azure/data-explorer/kusto/query/scalar-data-types/datetime)을 저장하는 문자열 필드       |  [Log Analytics 날짜/시간 표현](/azure/kusto/query/scalar-data-types/datetime)은 Unix 시간 표현과 비슷하지만 다른 점도 있습니다. 자세한 내용은 [변환 지침](/azure/kusto/query/datetime-timespan-arithmetic)을 참조하세요. <br><br>**참고**: 해당하는 경우 시간을 표준 시간대로 조정해야 합니다. |
|**MAC 주소**     |  String       | 콜론으로 구분된 16진수 표기법        |
|**IP 주소**     |String         |    Azure Sentinel 스키마에는 별도의 IPv4 및 IPv6 주소가 없습니다. 모든 IP 주소 필드에는 다음과 같이 IPv4 주소 또는 IPv6 주소가 포함될 수 있습니다. <br><br>- 점으로 구분된 10진수 표기법의 **IPv4**. 예를 들면 다음과 같습니다.  <br>- 8헥스텟 표기법의 **IPv6** 로, 짧은 형식을 사용할 수 있습니다.<br><br>예를 들면 다음과 같습니다.<br>`192.168.10.10`(IPv4)<br>`FEDC:BA98:7654:3210:FEDC:BA98:7654:3210`(IPv6)<br>`1080::8:800:200C:417A`(IPv6 약식)     |
|**FQDN**        |   문자열      |    점 표기법을 사용하는 정규화된 도메인 이름(예: `docs.microsoft.com`) |
|**국가**     |   String      |    다음 우선 순위에 따라 [ISO 3166-1](https://www.iso.org/iso-3166-country-codes.html)을 사용하는 문자열입니다. <br><br> - 알파-2 코드(예: 미국의 경우 `US`) <br> - 알파-3 코드(예: 미국의 경우 `USA`) <br>- 약식 이름<br><br>코드 목록은 [ISO(국제 표준화 기구) 웹 사이트](https://www.iso.org/obp/ui/#search)에서 찾을 수 있습니다.|
|**지역**     | String        |   ISO 3166-2를 사용하는 국가 지역 이름<br><br>코드 목록은 [ISO(국제 표준화 기구) 웹 사이트](https://www.iso.org/obp/ui/#search)에서 찾을 수 있습니다.|
|**도시**     |  String       |         |
|**경도**     | Double        |  ISO 6709 좌표 표현(부호 있는 10진수)       |
|**위도**     | Double        |    ISO 6709 좌표 표현(부호 있는 10진수)     |
|**MD5**     |    String     |  32개 16진수 문자       |
|**SHA1**     |   String      | 40개 16진수 문자        |
|**SHA256**     | String        |  64개 16진수 문자       |
|**SHA512**     |   String      |  128개 16진수 문자       |
| | |

## <a name="common-fields"></a>공용 필드

다음 필드는 모든 ASIM 스키마에 공통됩니다. 여기에는 스키마마다 세부 사항이 다른 상황을 지원하기 위해 각 스키마에 대한 공통 필드가 나와 있습니다. 예를 들어, **EventSchemaVersion** 필드 값이 다를 수 있으므로 스키마마다 **EventType** 필드의 값이 다를 수 있습니다. 

| 필드               | 클래스       | 형식       |  설명        |
|---------------------|-------------|------------|--------------------|
| <a name="timegenerated"></a>**TimeGenerated** | 기본 제공 | Datetime | 보고 디바이스에서 이벤트가 생성된 시간입니다.|
| **_ResourceId**   | 기본 제공 |  guid     | 보고 디바이스 또는 서비스의 Azure 리소스 ID이거나 Syslog, CEF 또는 WEF를 사용하여 전달된 이벤트에 대한 로그 전달자 리소스 ID입니다. |
| **형식** | 기본 제공 | String | 레코드를 가져온 원본 테이블입니다. 이 필드는 서로 다른 테이블에 대 한 두 채널을 통해 동일한 이벤트를 받을 수 있지만와가 같은 경우에 유용 합니다 `EventVendor` `EventProduct` . 예를 들어 이벤트 테이블이 나 SecurityEvent 테이블로 Sysmon 이벤트를 수집할 수 있습니다. |
| **EventMessage**        | 선택    | 문자열     |     레코드에 포함되거나 레코드에서 생성된 일반 메시지 또는 설명입니다.   |
| **EventCount**          | 필수   | 정수    |     레코드에서 설명하는 이벤트 수입니다. <br><br>이 값은 원본에서 집계를 지원할 때 사용되며, 단일 레코드에서 여러 이벤트를 나타낼 수 있습니다. <br><br>다른 원본의 경우 `1`로 설정합니다.   |
| **EventStartTime**      | 필수   | 날짜/시간  |      원본에서 집계를 지원하고 레코드에서 여러 이벤트를 나타내는 경우 이 필드는 첫 번째 이벤트가 생성된 시간을 지정합니다. <br><br>그렇지 않은 경우 이 필드는 [TimeGenerated](#timegenerated) 필드의 별칭을 지정합니다. |
| **EventEndTime**        | 필수   | Alias      |      [TimeGenerated](#timegenerated) 필드에 대한 별칭입니다.    |
|  <a name=eventtype></a>**EventType**           | 필수   | Enumerated |    레코드에서 보고하는 작업을 설명합니다. 각 스키마는 이 필드에 유효한 값 목록을 문서화합니다. |
| **EventSubType** | 선택 사항 | Enumerated | [EventType](#eventtype) 필드에 보고된 작업의 하위 작업에 대해 설명합니다. 각 스키마는 이 필드에 유효한 값 목록을 문서화합니다. |
| <a name="eventresult"></a>**EventResult** | 필수 | Enumerated | **Success**, **Partial**, **Failure**, **NA**(해당 사항 없음) 값 중 하나입니다.<br> <br>값은 이러한 값으로 정규화되어야 하는 다른 조건을 사용하여 원본 레코드에 제공될 수 있습니다. 또는 원본에서 [EventResultDetails](#eventresultdetails) 필드만 제공할 수 있습니다. 이 필드는 EventResult 값을 도출하기 위해 분석되어야 합니다.<br><br>예: `Success`|
| <a name=eventresultdetails></a>**EventResultDetails** | 필수 | Alias | [**EventResult**](#eventresult) 필드에 보고된 결과에 대한 이유 또는 세부 정보. 각 스키마는 이 필드에 유효한 값 목록을 문서화합니다.<br><br>예: `NXDOMAIN`|
| **EventOriginalUid**    | 선택    | 문자열     |   원본에서 제공하는 경우 원래 레코드의 고유 ID입니다.<br><br>예: `69f37748-ddcd-4331-bf0f-b137f1ea83b`|
| **EventOriginalType**   | 선택    | 문자열     |   원본에서 제공하는 경우 원본 이벤트 유형 또는 ID입니다. 예를 들어 이 필드는 원래 Windows 이벤트 ID를 저장하는 데 사용됩니다.<br><br>예: `4624`|
| <a name ="eventproduct"></a>**EventProduct**        | 필수   | String     |             이벤트를 생성하는 제품 <br><br>예: `Sysmon`<br><br>**참고**: 이 필드는 원본 레코드에서 사용할 수 없습니다. 이러한 경우 이 필드는 파서에서 설정해야 합니다.           |
| **EventProductVersion** | 선택    | 문자열     | 이벤트를 생성하는 제품의 버전 <br><br>예: `12.1`      |
| **EventVendor**         | 필수   | String     |           이벤트를 생성하는 제품의 공급 업체 <br><br>예: `Microsoft`  <br><br>**참고**: 이 필드는 원본 레코드에서 사용할 수 없습니다. 이러한 경우 이 필드는 파서에서 설정해야 합니다.  |
| **EventSchemaVersion**  | 필수   | String     |    스키마의 버전입니다. 각 스키마는 현재 버전을 문서화합니다.         |
| **EventReportUrl**      | 선택    | 문자열     | 이벤트에 대한 추가 정보를 제공하는 리소스에 대해 이벤트에 제공된 URL입니다.|
| **Dvc** | 필수       | String     |               이벤트가 발생한 디바이스의 고유 식별자입니다. <br><br>이 필드는 [DvcId](#dvcid), [DvcHostname](#dvchostname) 또는 [DvcIpAddr](#dvcipaddr) 필드의 별칭을 지정할 수 있습니다. 명백한 디바이스가 없는 클라우드 원본의 경우 [이벤트 제품](#eventproduct) 필드와 동일한 값을 사용하세요.           |
| <a name ="dvcipaddr"></a>**DvcIpAddr**           | 권장 | IP 주소 |         이벤트가 발생한 디바이스의 IP 주소입니다.  <br><br>예: `45.21.42.12`    |
| <a name ="dvchostname"></a>**DvcHostname**         | 권장 | 호스트 이름   |               이벤트가 발생한 디바이스의 호스트 이름입니다. <br><br>예: `ContosoDc.Contoso.Azure`               |
| <a name ="dvcid"></a>**DvcId**               | 선택    | 문자열     |  이벤트가 발생한 디바이스의 고유 ID입니다. <br><br>예: `41502da5-21b7-48ec-81c9-baeea8d7d669`   |
| **DvcMacAddr**          | 선택 사항    | MAC        |   이벤트가 발생한 디바이스의 MAC 주소입니다.  <br><br>예: `00:1B:44:11:3A:B7`       |
| **DvcOs**               | 선택    | 문자열     |         이벤트가 발생한 디바이스에서 실행 중인 운영 체제입니다.    <br><br>예: `Windows`    |
| **DvcOsVersion**        | 선택    | 문자열     |   이벤트가 발생한 디바이스의 운영 체제 버전입니다. <br><br>예: `10` |
| **AdditionalFields**    | 선택 사항    | 동적    | 원본에서 보존할 가치가 있는 추가 정보를 제공하는 경우 원본 필드 이름으로 유지하거나 동적 **AdditionalFields** 필드를 만들고 추가 정보를 키/값 쌍으로 추가합니다.    |
| | | | |

> [!NOTE]
> Log Analytics는 보안 사용 사례와 관련성이 낮은 다른 필드도 추가합니다. 자세한 내용은 [Azure Monitor 로그의 표준 열](../azure-monitor/logs/log-standard-columns.md)을 참조하세요.
>


## <a name="entities"></a>엔터티

이벤트는 사용자, 호스트, 프로세스 또는 파일과 같은 엔터티를 중심으로 진행됩니다. 엔터티 표현을 사용하면 동일한 형식의 여러 엔터티가 단일 레코드의 일부가 될 수 있으며 동일한 엔터티에 대해 여러 특성을 지원할 수 있습니다.

엔터티 기능을 사용하려면 다음 지침에 따라 엔터티를 나타내야 합니다.

|지침  |설명  |
|---------|---------|
|**설명자 및 앨리어싱**     | 단일 이벤트에는 원본 및 대상 호스트와 같이 동일한 형식의 엔터티가 둘 이상 포함되어 있기 때문에 특정 엔터티와 연결된 모든 필드를 식별하기 위한 접두사로 *설명자* 가 사용됩니다. <br><br>정규화를 유지하기 위해 Azure Sentinel 정보 모델은 작은 표준 설명자 집합을 사용하여 엔터티의 특정 역할에 가장 적합한 설명자를 선택합니다.  <br><br>형식의 단일 엔터티가 이벤트와 관련된 경우 설명자를 사용할 필요가 없습니다. 또한 설명자가 없는 필드 집합은 각 형식에 대해 가장 많이 사용되는 엔터티를 별칭으로 사용합니다.  |
|**식별자 및 형식**     | 정규화된 스키마를 사용하면 이벤트에 공존하게 하려는 각 엔터티에 대해 여러 식별자를 사용할 수 있습니다. 원본 이벤트에 정규화된 스키마에 매핑할 수 없는 다른 엔터티 식별자가 있는 경우 해당 식별자를 원본 양식에 유지하거나 `AdditionalFields` 동적 필드를 사용합니다. <br><br>식별자에 대한 형식 정보를 유지 관리하려면 해당하는 경우 형식을 동일한 이름 및 `Type` 접미사를 지정하여 필드에 저장합니다. `UserIdType`)을 입력합니다.         |
|**특성**     |   엔터티에는 식별자로 사용되지 않아 설명자로 정규화할 수도 있는 다른 특성도 있습니다. 예를 들어 원본 사용자에게 도메인 정보가 있는 경우 정규화된 필드는 `SrcUserDomain`입니다.      |
| | |

각 스키마는 중앙 엔터티 및 엔터티 필드를 명시적으로 정의합니다. 다음 지침을 사용하면 중앙 스키마 필드를 이해할 수 있을 뿐만 아니라 스키마에서 명시적으로 정의되지 않은 다른 엔터티 또는 엔터티 필드를 사용하여 정규화된 방식으로 스키마를 확장할 수 있습니다.

### <a name="the-user-entity"></a>User 엔터티

사용자에 사용되는 설명자는 다음 시나리오에 설명된 대로 **행위자**, **대상 사용자** 및 **업데이트된 사용자** 입니다.

|활동  |전체 시나리오  |별칭 지정에 사용되는 단일 엔터티 시나리오  |
|---------|---------|---------|
|**사용자 만들기**     |  **행위자** 가 **대상 사용자** 를 만들었거나 수정했습니다.       |  (대상) **사용자** 가 만들어졌습니다.       |
|**사용자 수정**     |   **행위자** 가 **대상 사용자** 에서 **업데이트된 사용자** 로 명칭을 변경했습니다. **업데이트된 사용자** 에게는 일반적으로 사용자와 연결된 모든 정보가 있지는 않으며 **대상 사용자** 와 약간 중복됩니다.      |         |
|**네트워크 연결**     |    원본 호스트에서 **행위자** 로 실행되는 프로세스로, 대상 호스트에서 **대상 사용자** 로 실행 중인 프로세스와 통신합니다.     |         |
|**DNS 요청**     | **행위자** 가 DNS 쿼리를 시작했습니다.        |         |
|**로그인**     |    **행위자** 가 시스템에 **대상 사용자** 로 로그인했습니다.     |(대상) 사용자가 로그인함         |
|**프로세스 만들기**     |   **행위자**(시작 프로세스와 연결된 사용자)가 프로세스 만들기를 시작했습니다. 만든 프로세스는 **대상 사용자**(대상 프로세스와 관련된 사용자)의 자격 증명으로 실행됩니다.      |  만든 프로세스는 (대상) **사용자** 의 자격 증명으로 실행됩니다.       |
|**Email**     |     **행위자** 는 **대상 사용자** 에게 메일을 보냅니다.    |         |
| | | |

다음 표에서는 사용자에 대해 지원되는 식별자를 설명합니다.

|정규화된 필드  |형식  |형식 및 지원되는 형식  |
|---------|---------|---------|
|**UserId**     |    String     |   시스템에서 사용자에 대한 머신이 읽을 수 있는 영숫자, 고유 표현입니다. <br><br>형식 및 지원되는 형식은 다음과 같습니다.<br>    - **SID**(Windows): `S-1-5-21-1377283216-344919071-3415362939-500`<br>    -  **UID**(Linux): `4578`<br>    -    **AADID**(Azure Active Directory): `9267d02c-5f76-40a9-a9eb-b686f3ca47aa`<br>    - **OktaId**: `00urjk4znu3BcncfY0h7`<br>    - **AWSId**: `72643944673`<br><br>    `UserIdType` 필드에 ID 형식을 저장합니다. 다른 ID를 사용할 수 있는 경우 필드 이름을 각각 `UserSid`, `UserUid`, `UserAADID`, `UserOktaId` 및 `UserAwsId`로 정규화하는 것이 좋습니다.       |
|**사용자 이름**     |  String       |   사용 가능한 경우 도메인 정보를 포함하며 다음 형식 중 하나와 다음 우선 순위에 따라 도메인 정보를 포함하는 사용자 이름입니다. <br> -   **Upn/메일**: `johndow@contoso.com` <br>  -    **Windows**: `Contoso\johndow` <br> -   **DN**: `CN=Jeff Smith,OU=Sales,DC=Fabrikam,DC=COM` <br>  - **단순**: `johndow`. 도메인 정보를 사용할 수 없는 경우에만 이 양식을 사용합니다. <br><br> `UsernameType` 필드에 사용자 이름 유형을 저장합니다.    |
| | | |


### <a name="the-process-entity"></a>프로세스 엔터티

사용자에게 사용되는 설명자는 다음 시나리오에 설명된 대로 `Acting Process`, `Target Process` 및 `Parent Process`입니다.

- **네트워크 연결**. **작동 프로세스** 에서 네트워크 연결을 시작하여 원격 시스템의 **대상 프로세스** 와 통신했습니다.
- **DNS 요청**.  **작동 프로세스** 에서 DNS 쿼리를 시작했습니다.
- **로그인**.  **작동 프로세스** 에서 **대상 프로세스** 를 대신 실행하는 원격 시스템에 대해 로그인을 시작했습니다.
- **프로세스 만들기**. **작동 프로세스** 에서 **대상 프로세스** 만들기를 시작했습니다. **부모 프로세스** 는 작동 프로세스의 부모입니다.

다음 표에서는 프로세스에 대해 지원되는 식별자를 설명합니다.

|정규화된 필드  |형식  |형식 및 지원되는 형식  |
|---------|---------|---------|
|**ID**     |    String     |   OS 할당 프로세스 ID입니다.      |
|**Guid**     |  String       |   OS 할당 프로세스 GUID입니다. GUID는 일반적으로 시스템이 다시 시작되면 고유하게 다시 지정되지만 ID는 재사용되는 경우가 많습니다.   |
|**경로**     |    String     |   디렉터리 및 파일 이름을 포함하는 프로세스의 전체 경로 이름입니다.       |
|**이름**     |  Alias       |  프로세스 이름은 경로의 별칭입니다.   |
| | | |


자세한 내용은 [Azure Sentinel 프로세스 이벤트 정규화 스키마 참조서(퍼블릭 미리 보기)](process-events-normalization-schema.md)를 참조하세요.

### <a name="the-device-entity"></a>디바이스 엔터티

정규화 스키마는 사용자의 직관을 최대한 따르려고 하므로 시나리오에 따라 다양한 방법으로 디바이스를 처리하려고 합니다.

- 이벤트 컨텍스트가 원본 및 대상 디바이스를 암시하는 경우 `Src` 및 `Target` 설명자가 사용됩니다. 이 경우 `Dvc` 설명자가 보고 장치에 사용됩니다.

- 로컬 OS 이벤트와 같은 단일 디바이스 이벤트의 경우 `Dvc` 설명자가 사용됩니다.

- 이벤트에서 다른 게이트웨이 디바이스가 참조되고 값이 보고 디바이스와 다른 경우 `Gateway` 설명자가 사용됩니다.

디바이스 처리 지침은 다음과 같이 추가적으로 명시됩니다.

- **네트워크 연결**. **원본 디바이스**(`Src`)에서 **대상 디바이스**(`Target`)로의 연결이 설정되었습니다 (보고) **디바이스**(`Dvc`)에서 연결을 보고했습니다.
- **프록시 처리된 네트워크 연결**. **게이트웨이 디바이스**(`Gateway`)를 통해 **원본 디바이스**(`Src`)에서 **대상 디바이스**(`Target`)로의 연결이 설정되었습니다 (보고) **디바이스** 에서 연결을 보고했습니다.
- **DNS 요청**.  **원본 디바이스**(`Src`)에서 DNS 쿼리를 시작했습니다
- **로그인** **원본 디바이스**(`Src`)에서 **대상 디바이스**(`Target`)의 원격 시스템으로의 서명이 시작되었습니다.
- **프로세스**   **디바이스**(`Dvc`)에서 프로세스가 시작되었습니다.

다음 표에서는 디바이스에 대해 지원되는 식별자를 설명합니다.

|정규화된 필드  |형식  |형식 및 지원되는 형식  |
|---------|---------|---------|
|**호스트 이름**     |    String     |        |
|**FQDN**     |  String       |   정규화된 도메인 이름   |
|**IpAddr**     |    IP 주소     |   디바이스에 여러 IP 주소가 있을 수 있지만 이벤트에는 일반적으로 단일 식별 IP 주소가 있습니다. 예외는 관련 IP 주소가 두 개 있을 수 있는 게이트웨이 디바이스입니다. 게이트웨이 디바이스에 대해 `UpstreamIpAddr` 및 `DownstreamIpAddr`을 사용합니다.      |
|**HostId**     |  String       |     |
| | | |


> [!NOTE]
> `Domain`은 디바이스의 일반적인 특성이지만 완전한 식별자가 아닙니다.
>

자세한 내용은 [Azure Sentinel 인증 정규화 스키마 참조서(퍼블릭 미리 보기)](authentication-normalization-schema.md)를 참조하세요.

### <a name="sample-entity-mapping"></a>샘플 엔터티 매핑

이 섹션에서는 Azure Sentinel에 대해 이벤트 데이터를 정규화하는 방법을 설명하는 예제로 [Windows 이벤트 4624](/windows/security/threat-protection/auditing/event-4624)를 사용합니다.

이 이벤트에는 다음과 같은 엔터티가 있습니다.

|Microsoft 용어  |원래 이벤트 필드 접두사 |ASIM 필드 접두사  |설명  |
|---------|---------|---------|---------|
|**Subject**     | `Subject`        |   `Actor`      |  성공적인 로그인에 대한 정보를 보고한 사용자입니다.      |
|**새 로그온**     |    `Target`     |  `TargetUser`       |  로그인이 수행된 사용자입니다.       |
|**처리**     |    -     |     `ActingProcess`    |   로그인을 시도한 프로세스입니다.      |
|**네트워크 정보**     |   -      |   `Src`      |     로그인 시도가 수행된 머신입니다.    |
| | | | |


이러한 엔터티를 기준으로 [Windows 이벤트 4624](/windows/security/threat-protection/auditing/event-4624)는 다음과 같이 정규화됩니다(일부 필드는 선택 사항).

|정규화된 필드  |원본 필드  |예제의 값  |참고  |
|---------|---------|---------|---------|
|**ActorUserId**     |  SubjectUserSid       |  S-1-5-18        |        |
|**ActorUserIdType**     |  -       | SID        |         |
|**ActorUserName**     |   SubjectDomainName\ SubjectUserName      |  WORKGROUP\WIN-GG82ULGC9GO$       |  두 필드를 연결하여 빌드       |
|**ActorUserNameType**     |   -      |   Windows      |         |
|**ActorSessionId**     | SubjectLogonId        |  0x3e7       |         |
|**TargetUserId**     |   TargetUserSid      |    S-1-5-21-1377283216-344919071-3415362939-500     |         |
|**UserId**     |    TargetUserSid     |   alias      |         |
|**TargetUserIdType**     |   -      |    SID     |         |
|**TargetUserName**     | TargetDomainName\ TargerUserName        |   Administrator\WIN-GG82ULGC9GO$      |   두 필드를 연결하여 빌드      |
|**사용자 이름**     |  TargetDomainName\ TargerUserName       |  alias       |         |
|**TargetUserNameType**     | -        |  Windows       |         |
|**TargetSessionId**     |   TargetLogonId      |  0x8dcdc       |         |
|**ActingProcessName**     |  ProcessName       |  C:\\Windows\\System32\\svchost.exe       |         |
|**ActingProcessId**     |    ProcessId     |     0x44c    |         |
|**SrcHostname**     |    WorkstationName     | Windows        |         |
|**SrcIpAddr**     |  IpAddress       |     127.0.0.1    |         |
|**SrcPortNumber**     |  IpPort       |  0       |         |
|**TargetHostname**     |   Computer      |  WIN-GG82ULGC9GO           |         |
|**호스트 이름**     |     Computer    |     Alias    |         |
| | | | |


## <a name="next-steps"></a>다음 단계

이 문서에서는 Azure Sentinel 정규화와 Azure Sentinel 정보 모델의 정규화 개요를 제공합니다.

자세한 내용은 다음을 참조하세요.

- [Azure Sentinel 정보 모델 개요](normalization.md)
- [Azure Sentinel 정보 모델 파서](normalization-about-parsers.md)
- [Azure Sentinel 정보 모델 콘텐츠](normalization-content.md)