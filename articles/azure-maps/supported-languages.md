---
title: Microsoft Azure Maps를 사용하여 지역화 지원
description: 지도, 검색, 라우팅, 날씨 및 트래픽 인시던트와 같은 서비스를 지원하는 Azure Maps 지역을 확인하세요. View 매개 변수를 설정하는 방법에 대해 알아봅니다.
author: stevemunk
ms.author: v-munksteve
ms.date: 12/07/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
ms.openlocfilehash: 7a48190d58097d1cbbdf1223c25916ee6f6ca0e3
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/14/2021
ms.locfileid: "130001564"
---
# <a name="localization-support-in-azure-maps"></a>Azure Maps의 지역화 지원

Azure Maps는 국가/지역에 따라 다양한 언어와 보기를 지원합니다. 이 문서에서는 Azure Maps 구현을 안내하는 데 유용한 지원 언어와 보기를 제공합니다.

## <a name="azure-maps-supported-languages"></a>Azure Maps 지원 언어

Azure Maps는 서비스 전반에 걸쳐 다양한 언어로 지역화되었습니다. 다음 표에서는 각 서비스에 지원되는 언어 코드를 제공합니다.  
  
| ID         | 속성                   |  지도 | 검색 | 라우팅 | Weather | 트래픽 인시던트 | JS 지도 컨트롤 |
|------------|------------------------|:-----:|:------:|:-------:|:--------:|:-----------------:|:--------------:|
| af-ZA      | 아프리칸스어              |       |   ✓   |    ✓    |          |                   |                |
| ar-SA      | 아랍어                 |   ✓   |   ✓   |    ✓    |   ✓     |         ✓         |        ✓      |
| bn-BD      | 벵골어(방글라데시)    |       |       |         |     ✓    |                   |                |
| bn-IN      | 벵골어(인도)         |       |       |         |     ✓    |                   |                |
| bs-BA      | 보스니아어                |       |       |         |     ✓    |                   |                |
| eu-ES      | 바스크어                 |       |   ✓   |         |          |                   |                |
| bg-BG      | 불가리아어              |   ✓   |   ✓   |    ✓   |     ✓    |                   |        ✓       |
| ca-ES      | 카탈로니아어                |       |    ✓  |         |    ✓     |                   |                |
| zh-HanS    | 중국어 (간체)   |       |  zh-CN |        |   zh-CN   |                   |                |
| zh-HanT    | 중국어(홍콩 특별 행정구)|       |        |        |   zh-HK   |                   |                |
| zh-HanT    | 중국어(대만)       | zh-TW |  zh-TW |  zh-TW |   zh-TW   |                   |      zh-TW     |
| hr-HR      | 크로아티아어               |       |    ✓   |        |    ✓     |                   |                |
| cs-CZ      | 체코어                  |   ✓   |    ✓   |    ✓  |    ✓     |         ✓         |        ✓       |
| da-DK      | 덴마크어                 |   ✓   |    ✓   |    ✓   |    ✓    |        ✓          |        ✓       |
| nl-BE      | 네덜란드어(벨기에)        |       |    ✓   |         |     ✓   |                   |                |
| nl-NL      | 네덜란드어(네덜란드)    |   ✓   |    ✓   |    ✓   |     ✓    |        ✓         |        ✓       |
| en-AU      | 영어(오스트레일리아)    |   ✓   |    ✓   |    ✓   |     ✓    |       ✓          |        ✓       |
| en-NZ      | 영어(뉴질랜드)  |   ✓   |    ✓   |    ✓   |     ✓    |       ✓          |        ✓       |
| en-GB      | 영어(영국)|   ✓   |    ✓   |    ✓   |     ✓    |       ✓          |        ✓       |
| ko-KR      | 영어(미국)          |   ✓   |    ✓   |    ✓   |      ✓   |       ✓          |        ✓       |
| et-EE      | 에스토니아어               |       |    ✓   |        |      ✓    |        ✓         |                |
| fil-PH     | 필리핀어               |       |         |        |     ✓    |                   |                |
| fi-FI      | 핀란드어                |   ✓   |    ✓   |    ✓   |      ✓   |         ✓         |        ✓       |
| fr-FR      | 프랑스어                 |   ✓   |    ✓   |    ✓   |      ✓   |         ✓         |        ✓       |
| fr-CA      | 프랑스어(캐나다)        |       |    ✓   |         |     ✓    |                   |                |
| gl-ES      | 갈리시아어               |       |    ✓   |         |          |                   |                |
| de-DE      | 독일어                 |   ✓   |    ✓   |    ✓   |   ✓      |         ✓         |        ✓       |
| el-GR      | 그리스어                  |   ✓   |    ✓   |    ✓   |    ✓     |         ✓         |        ✓       |
| gu-IN      | 구자라트어               |       |         |        |     ✓    |                   |                |
| he-IL      | 히브리어                 |       |    ✓   |         |     ✓    |         ✓         |                |
| hi-IN      | 힌디어                  |       |        |         |     ✓    |                   |                |
| hu-HU      | 헝가리어              |   ✓   |    ✓  |    ✓    |     ✓    |         ✓         |        ✓       |
| is-IS      | 아이슬란드어              |       |       |          |     ✓    |                   |                |
| id-ID      | 인도네시아어             |   ✓   |   ✓   |    ✓    |     ✓    |         ✓         |        ✓       |
| it-IT      | 이탈리아어                |   ✓   |   ✓   |    ✓    |      ✓   |         ✓         |        ✓       |
| ja-JP      | 일본어               |       |        |         |     ✓    |                   |                |
| kn-IN      | 칸나다어                |       |        |         |     ✓    |                   |                |
| kk-KZ      | 카자흐어                 |       |    ✓   |         |     ✓    |                   |                |
| en-US      | 한국어                 |   ✓   |        |    ✓    |     ✓    |                   |        ✓       |
| es-419     | 스페인어(라틴 아메리카) |       |    ✓   |         |           |                   |                |
| lv-LV      | 라트비아어                |       |    ✓   |         |     ✓    |         ✓         |                |
| lt-LT      | 리투아니아어             |   ✓   |    ✓   |    ✓    |     ✓    |         ✓         |        ✓      |
| mk-MK      | 마케도니아어             |       |         |         |     ✓    |                   |                |
| ms-MY      | 말레이어(라틴 문자)          |   ✓   |    ✓   |    ✓    |    ✓     |                   |        ✓       |
| mr-IN      | 마라티어                |       |         |         |     ✓    |                   |                |
| nb-NO      | 노르웨이어(복말)       |   ✓   |    ✓   |    ✓    |      ✓   |         ✓         |        ✓       |
| NGT        | 중립 참조 자료 - 사용 가능한 경우 로컬 스크립트로 된 모든 지역의 공식 언어 |   ✓     |        |         |       |        |      ✓          |
| NGT-Latn   | 중립 참조 자료 - 라틴 지명. 사용 가능한 경우 라틴 스크립트가 사용됩니다. |   ✓     |        |         |         |                |        ✓         |
| pl-PL      | 폴란드어                 |   ✓   |    ✓   |    ✓    |     ✓    |         ✓         |        ✓       |
| pt-BR      | 포르투갈어(브라질)    |   ✓   |    ✓   |    ✓    |      ✓   |                   |        ✓       |
| pt-PT      | 포르투갈어(포르투갈)  |   ✓   |    ✓   |    ✓    |      ✓   |         ✓         |        ✓       |
| pa-IN      | 펀잡어                |       |         |         |     ✓    |                   |                |
| ro-RO      | 루마니아어               |       |    ✓    |         |     ✓    |         ✓         |                |
| ru-RU      | 러시아어                |   ✓   |    ✓   |    ✓    |      ✓   |         ✓         |        ✓       |
| sr-Cyrl-RS | 세르비아어(키릴 자모)     |       |  sr-RS  |         |  sr-RS    |                   |                |
| sr-Latn-RS | 세르비아어(라틴 문자)        |       |         |         |  sr-latn  |                   |                |
| sk-SK      | 슬로바키아어                 |   ✓   |    ✓   |    ✓    |     ✓    |         ✓         |        ✓       |
| sl-SI      | 슬로베니아어              |   ✓   |    ✓   |    ✓    |     ✓    |                   |        ✓       |
| es-ES      | 스페인어                |   ✓   |    ✓   |    ✓    |     ✓    |         ✓         |        ✓       |
| es-MX      | 스페인어(멕시코)       |   ✓   |        |    ✓    |     ✓    |                   |        ✓       |
| sv-SE      | 스웨덴어                |   ✓   |    ✓   |    ✓    |     ✓    |         ✓         |        ✓       |
| ta-IN      | 타밀어(인도)          |       |        |          |     ✓    |                    |                |
| te-IN      | 텔루구어(인도)         |       |        |          |     ✓    |                    |                |
| th-TH      | 태국어                   |   ✓   |    ✓   |    ✓    |     ✓    |         ✓         |        ✓       |
| tr-TR      | 터키어                |   ✓   |    ✓   |    ✓    |     ✓    |         ✓         |        ✓       |
| uk-UA      | 우크라이나어              |       |    ✓   |          |     ✓    |                   |                |
| ur-PK      | 우르두어                   |       |        |          |     ✓    |                   |                |
| uz-Latn-UZ | 우즈베크어                  |       |        |          |     ✓    |                   |                |
| vi-VN      | 베트남어             |       |    ✓   |          |     ✓    |                   |                |

## <a name="azure-maps-supported-views"></a>Azure Maps 지원 보기

> [!Note]
> 2019년 8월 1일부 터 Azure Maps는 다음 국가/지역에서 출시되었습니다.
>
> * 아르헨티나
> * 인도
> * 모로코
> * 파키스탄
>
> 2019년 8월 1일 이후에 **View** 매개 변수는 위에 나열된 새 지역/국가에 대해 반환되는 지도 콘텐츠를 정의합니다. Azure Maps **View** 매개 변수("사용자 지역 매개 변수"라고도 함)는 해당 국가/지역에 대한 올바른 지도를 표시하고 지도에 표시되는 경계 및 레이블을 포함하여 Azure Maps 서비스를 통해 반환되는 지정학적 분쟁 콘텐츠를 지정하는 두 문자로 된 ISO-3166 국가 코드입니다. 

서비스에서 사용하는 REST API 및 SDK에 필요한 대로 **View** 매개 변수를 설정해야 합니다.
  
### <a name="rest-apis"></a>Rest API
  
필요에 따라 View 매개 변수를 설정했는지 확인합니다. View 매개 변수는 Azure Maps 서비스를 통해 반환되는 지정학적 분쟁 콘텐츠를 지정합니다. 

영향을 받는 Azure Maps REST 서비스:

* Get Map Tile
* Get Map Image
* Get Search Fuzzy
* Get Search POI
* Get Search POI Category
* Get Search Nearby
* Get Search Address
* Get Search Address Structured
* Get Search Address Reverse
* Get Search Address Reverse Cross Street
* Post Search Inside Geometry
* Post Search Address Batch
* Post Search Address Reverse Batch
* Post Search Along Route
* Post Search Fuzzy Batch

### <a name="sdks"></a>SDK

필요에 따라 **View** 매개 변수를 설정하고 최신 버전의 Web SDK 및 Android SDK를 설치했는지 확인합니다. 영향을 받는 SDK:

* Azure Maps 웹 SDK
* Azure Maps Android SDK

기본적으로 View 매개 변수는 요청에서 정의하지 않은 경우에도 **Unified** 로 설정됩니다. 사용자의 위치를 확인합니다. 그런 다음, 해당 위치에 대한 **View** 매개 변수를 올바르게 설정합니다. 또는 'View=Auto'를 설정할 수 있습니다. 이 경우 요청의 IP 주소를 기준으로 지도 데이터를 반환합니다.  Azure Maps의 **View** 매개 변수는 Azure Maps를 통해 사용자에게 액세스 권한이 부여된 지도, 이미지 및 기타 데이터와 타사 콘텐츠를 사용할 수 있는 국가/지역의 지도 관련 법률을 포함하는 해당 법률에 따라 사용해야 합니다.

다음 표에서는 지원되는 보기를 제공합니다.

| 보기         | Description                            |  지도 | 검색 | JS 지도 컨트롤 |
|--------------|----------------------------------------|:-----:|:------:|:--------------:|
| AE           | 아랍에미리트(아랍어 보기)     |   ✓   |        |     ✓          |
| AR           | 아르헨티나(아르헨티나어 보기)           |   ✓   |    ✓   |     ✓          |
| BH           | 바레인(아랍어 보기)                  |   ✓   |        |     ✓          |
| IN           | 인도(인도어 보기)                    |   ✓   |   ✓    |     ✓          |
| IQ           | 이라크(아랍어 보기)                     |   ✓   |        |     ✓          |
| JO           | 요르단(아랍어 보기)                   |   ✓   |        |     ✓          |
| KW           | 쿠웨이트(아랍어 보기)                   |   ✓   |        |     ✓          |
| LB           | 레바논(아랍어 보기)                  |   ✓   |        |     ✓          |
| MA           | 모로코(모로코어 보기)                |   ✓   |   ✓    |     ✓         |
| OM           | 오만(아랍어 보기)                     |   ✓   |        |     ✓          |
| PK           | 파키스탄(파키스탄어 보기)              |   ✓   |    ✓   |     ✓         |
| PS           | 팔레스타인 자치 정부(아랍어 보기)    |   ✓   |        |     ✓          |
| QA           | 카타르(아랍어 보기)                    |   ✓   |        |     ✓          |
| SA           | 사우디아라비아(아랍어 보기)             |   ✓   |        |     ✓          |
| SY           | 시리아(아랍어 보기)                    |   ✓   |        |     ✓          |
| YE           | 예멘(아랍어 보기)                    |   ✓   |        |     ✓          |
| Auto         | 요청의 IP 주소를 기준으로 지도 데이터를 반환합니다.|   ✓   |    ✓   |     ✓          |
| 통합형      | 통합 보기(기타)                  |   ✓   |   ✓     |     ✓          |
