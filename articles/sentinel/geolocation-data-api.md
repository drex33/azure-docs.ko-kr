---
title: REST API를 사용 하 여 Microsoft 센티널의 지리적 위치 데이터로 엔터티 보강 Microsoft Docs
description: 이 문서에서는 REST API를 통해 지리적 위치 데이터로 Microsoft 센티널의 엔터티를 보강 하는 방법을 설명 합니다.
services: sentinel
documentationcenter: na
author: batamig
manager: rkarlin
editor: ''
ms.service: microsoft-sentinel
ms.subservice: microsoft-sentinel
ms.devlang: na
ms.topic: reference
ms.custom: mvc, ignite-fall-2021
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/09/2021
ms.author: bagol
ms.openlocfilehash: cce16ca2f0db3d4f44c4077e4760f599e565bae6
ms.sourcegitcommit: 2ed2d9d6227cf5e7ba9ecf52bf518dff63457a59
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/16/2021
ms.locfileid: "132520601"
---
# <a name="enrich-entities-in-microsoft-sentinel-with-geolocation-data-via-rest-api-public-preview"></a>REST API를 통해 지리적 위치 데이터로 Microsoft 센티널의 엔터티 보강 (공개 미리 보기)

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

이 문서에서는 REST API를 사용 하 여 지리적 위치 데이터로 Microsoft 센티널의 엔터티를 보강 하는 방법을 보여 줍니다.

> [!IMPORTANT]
> 이 기능은 현재 미리 보기로 제공됩니다. [Azure Preview 추가 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)에는 베타, 미리 보기 또는 아직 일반 공급으로 릴리스되지 않은 Azure 기능에 적용되는 추가 법률 용어가 포함되어 있습니다.
>

## <a name="common-uri-parameters"></a>공통 URI 매개 변수

다음은 지리적 위치 API에 대한 일반적인 URI 매개 변수입니다.




| Name | In(다음 안에) | 필수 | Type | Description |
|-|-|-|-|-|
| **{subscriptionId}** | path | 예 | GUID | Azure 구독 ID |
| **{resourceGroupName}** | path | 예 | 문자열 | 구독 내의 리소스 그룹 이름 |
| **{api-version}** | Query | 예 | 문자열 | 이 요청에 사용되는 프로토콜의 버전 2021년 4월 30일부터 지리적 위치 API 버전은 *2019-01-01-preview* 입니다.|
| **{ipAddress}** | Query | 예 | 문자열 | 지리적 위치 정보가 필요한 IP 주소(IPv4 또는 IPv6 형식)입니다.   |
|

## <a name="enrich-ip-address-with-geolocation-information"></a>지리적 위치 정보를 통해 IP 주소 보강

이 명령은 지정된 IP 주소에 대한 지리적 위치 데이터를 검색합니다.

### <a name="request-uri"></a>요청 URI

| 방법 | 요청 URI |
|-|-|
| **GET** | `https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.SecurityInsights/enrichment/ip/geodata/?ipaddress={ipAddress}&api-version={api-version}` |
|

### <a name="responses"></a>응답

|상태 코드  |Description  |
|---------|---------|
|**200**     |   Success      |
|**400**     |      IP 주소가 제공되지 않았거나 잘못된 형식입니다.    |
|**404**     | 이 IP 주소에 대한 지리적 위치 데이터를 찾을 수 없습니다.         |
|**429**     |      요청이 너무 많습니다. 지정된 시간 프레임에서 다시 시도합니다.    |
|     |         |

### <a name="fields-returned-in-the-response"></a>응답에 반환된 필드

|필드 이름  |Description  |
|---------|---------|
|**ASN**     |  이 IP 주소와 연결된 자치 시스템 번호       |
|**이동 통신 사업자**     |  이 IP 주소에 대한 이동 통신 사업자의 이름       |
|**도시**     |   이 IP 주소가 있는 도시      |
|**cityCf**     | 'city' 필드의 값이 0-100의 배율로 정확하다는 신뢰도의 숫자 등급        |
|**대륙**     | 이 IP 주소가 있는 대륙        |
|**country**     |이 IP 주소가 있는 구/군        |
|**countryCf**     |   'country' 필드의 값이 0-100의 배율로 정확하다는 신뢰도의 숫자 등급      |
|**ipAddr**     |   IP 주소의 점선 10진수 또는 콜론으로 구분된 문자열 표현      |
|**ipRoutingType**     |   이 IP 주소의 연결 형식에 대한 설명      |
|**latitude**     |     이 IP 주소의 위도    |
|**longitude**     |  이 IP 주소의 경도       |
|**조직**     |  이 IP 주소에 대한 조직의 이름       |
|**organizationType**     | 이 IP 주소에 대한 조직의 유형        |
|**region**     |    이 IP 주소가 있는 지리적 지역     |
|**state**     |  이 IP 주소가 있는 시/도       |
|**stateCf**     | 'state' 필드의 값이 0-100의 배율로 정확하다는 신뢰도의 숫자 등급        |
|**stateCode**     |   이 IP 주소가 있는 시/도의 약식 이름      |
|     |         |


## <a name="throttling-limits-for-the-api"></a>API에 대한 스로틀 제한

이 API는 시간당 사용자당 100개 호출로 제한됩니다.

### <a name="sample-response"></a>샘플 응답

```rest
"body":
{
    "asn": "12345",
    "carrier": "Microsoft",
    "city": "Redmond",
    "cityCf": 90,
    "continent": "north america",
    "country": "united states",
    "countryCf": 99
    "ipAddr": "1.2.3.4",
    "ipRoutingType": "fixed",
    "latitude": "40.2436",
    "longitude": "-100.8891",
    "organization": "Microsoft",
    "organizationType": "tech",
    "region": "western usa",
    "state": "washington",
    "stateCf": null
    "stateCode": "wa"
}
```

## <a name="next-steps"></a>다음 단계

Microsoft 센티널에 대해 자세히 알아보려면 다음 문서를 참조 하세요.

- 엔터티에 대해 자세히 알아봅니다.

    - [Microsoft 센티널 엔터티 형식 참조](entities-reference.md)
    - [Microsoft 센티널에서 엔터티를 사용 하 여 데이터 분류 및 분석](entities-in-azure-sentinel.md)
    - [Microsoft 센티널의 엔터티에 데이터 필드 매핑](map-data-fields-to-entities.md)

- [Microsoft 센티널 API](/rest/api/securityinsights/) 의 다른 용도 살펴보기
