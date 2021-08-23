---
title: Azure WAF(웹 애플리케이션 방화벽) Geomatch 사용자 지정 규칙
description: 이 문서에서는 Azure Application Gateway의 WAF(웹 애플리케이션 방화벽) geomatch 사용자 지정 규칙에 대한 개요를 제공합니다.
services: web-application-firewall
ms.topic: article
author: vhorne
ms.service: web-application-firewall
ms.date: 07/30/2021
ms.author: victorh
ms.openlocfilehash: 42fcc0daf7fd494918d04dacf4fb65661c837acf
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122566458"
---
# <a name="geomatch-custom-rules"></a>Geomatch 사용자 지정 규칙

사용자 지정 규칙을 사용하면 애플리케이션 및 보안 정책의 정확한 요구에 맞게 맞춤형 규칙을 만들 수 있습니다. 이제 국가/지역별로 웹 애플리케이션에 대한 액세스를 제한할 수 있습니다. 모든 사용자 지정 규칙과 마찬가지로 이 논리는 애플리케이션의 요구 사항에 맞게 다른 규칙과 결합될 수 있습니다.

Azure Portal에서 지역 필터링 사용자 지정 규칙을 만들려면 *지리적 위치* 를 일치 유형으로 선택한 다음, 애플리케이션에서 허용/차단할 국가/지역을 선택합니다. Azure PowerShell 또는 Azure Resource Manager를 사용하여 지역 일치 규칙을 만들 경우 일치 변수 `RemoteAddr` 및 연산자 `Geomatch`를 사용합니다. 자세한 정보는 [PowerShell에서 사용자 지정 규칙을 만드는 방법](configure-waf-custom-rules.md) 및 추가 [사용자 지정 규칙 예제](create-custom-waf-rules.md)를 참조하세요.

## <a name="countryregion-codes"></a>국가/지역 코드

Geomatch 연산자를 사용하는 경우 선택기는 다음과 같은 두 자리 국가/지역 코드 중 하나일 수 있습니다. 

|국가/지역 코드 | 국가/지역 이름 |
| ----- | ----- |
| AD | 안도라 |
| AE | 아랍에미리트|
| AF | 아프가니스탄|
| AG | 앤티가 바부다|
| AL | 알바니아|
| AM | 아르메니아|
| AO | 앙골라|
| AR | 아르헨티나|
| AS | 아메리칸 사모아|
| AT | 오스트리아|
| AU | 오스트레일리아|
| AZ | 아제르바이잔|
| BA | 보스니아 헤르체고비나|
| BB | 바베이도스|
| BD | 방글라데시|
| BE | 벨기에|
| BF | 부르키나파소|
| BG | 불가리아|
| BH | 바레인|
| BI | 부룬디|
| BJ | 베냉|
| BL | 생바르텔레미|
| BN | 브루나이|
| BO | 볼리비아|
| BR | 브라질|
| BS | 바하마|
| BT | 부탄|
| BW | 보츠와나|
| BY | 벨로루시|
| BZ | 벨리즈|
| CA | Canada|
| CD | 콩고 민주 공화국|
| CF | 중앙 아프리카 공화국|
| CH | 스위스|
| CI | 코트디부아르|
| CL | 칠레|
| CM | 카메룬|
| CN | 중국|
| CO | 콜롬비아|
| CR | 코스타리카|
| CU | 쿠바|
| CV | 카보베르데|
| CY | 키프로스|
| CZ | 체코|
| DE | 독일|
| DK | 덴마크|
| DO | 도미니카 공화국|
| DZ | 알제리|
| EC | 에콰도르|
| EE | 에스토니아|
| EG | 이집트|
| ES | 스페인|
| ET | 에티오피아|
| FI | 핀란드|
| FJ | 피지|
| FM | 미크로네시아|
| FR | 프랑스|
| GB | United Kingdom|
| GE | 조지아|
| GF | 프랑스령 기아나|
| GH | 가나|
| GN | 기니|
| GP | 과들루프|
| GR | 그리스|
| GT | 과테말라|
| GY | 가이아나|
| HK | 홍콩 특별 행정구|
| HN | 온두라스|
| HR | 크로아티아|
| HT | 아이티|
| HU | 헝가리|
| ID | 인도네시아|
| IE | 아일랜드|
| IL | 이스라엘|
| IN | 인도|
| IQ | 이라크|
| IR | 이란|
| IS | 아이슬란드|
| IT | 이탈리아|
| JM | 자메이카|
| JO | 요르단|
| JP | 일본|
| KE | 케냐|
| KG | 키르기스스탄|
| KH | 캄보디아|
| KI | 키리바시|
| KN | 세인트키츠 네비스|
| KP | 북한|
| KR | 한국|
| KW | 쿠웨이트|
| KY | 케이맨 제도|
| KZ | 카자흐스탄|
| LA | 라오스|
| LB | 레바논|
| LI | 리히텐슈타인|
| LK | 스리랑카|
| LR | 라이베리아|
| LS | 레소토|
| LT | 리투아니아|
| LU | 룩셈부르크|
| LV | 라트비아|
| LY | 리비아 |
| MA | 모로코|
| MD | 몰도바|
| MG | 마다가스카르|
| MK | 북마케도니아|
| ML | 말리|
| MM | 미얀마|
| MN | 몽골|
| MO | 마카오|
| MQ | 마르티니크|
| MR | 모리타니|
| MT | 몰타|
| MV | 몰디브|
| MW | 말라위|
| MX | 멕시코|
| MY | 말레이시아|
| MZ | 모잠비크|
| 해당 없음 | 나미비아|
| NE | 니제르|
| NG | 나이지리아|
| NI | 니카라과|
| NL | 네덜란드|
| 아니요 | 노르웨이|
| NP | 네팔|
| NR | 나우루|
| NZ | 뉴질랜드|
| OM | 오만|
| PA | 파나마|
| PE | 페루|
| PH | 필리핀|
| PK | 파키스탄|
| PL | 폴란드|
| PR | 푸에르토리코|
| PT | 포르투갈|
| PW | 팔라우|
| PY | 파라과이|
| QA | 카타르|
| RE | 리유니언|
| RO | 루마니아|
| RS | 세르비아|
| RU | 러시아|
| RW | 르완다|
| SA | 사우디아라비아|
| SD | 수단|
| SE | 스웨덴|
| SG | 싱가포르|
| SI | 슬로베니아|
| SK | 슬로바키아|
| SN | 세네갈|
| SO | 소말리아|
| SR | 수리남|
| SS | 남부 세단|
| SV | 엘살바도르|
| SY | 시리아|
| SZ | 스와질란드|
| TC | 터크스 케이커스 제도|
| TG | 토고|
| TH | 태국|
| TN | 튀니지|
| TR | 터키|
| TT | 트리니다드 토바고|
| TW | 대만|
| TZ | 탄자니아|
| UA | 우크라이나|
| UG | 우간다|
| US | 미국|
| UY | 우루과이|
| UZ | 우즈베키스탄|
| VC | 세인트 빈센트 그레나딘|
| VE | 베네수엘라|
| VG | 영국령 버진 아일랜드|
| VI | 미국령 버진 아일랜드|
| VN | 베트남|
| ZA | 남아프리카|
| ZM | 잠비아|
| ZW | 짐바브웨|

## <a name="next-steps"></a>다음 단계

사용자 지정 규칙에 대해 학습한 후 [자체 사용자 지정 규칙을 만듭니다](create-custom-waf-rules.md).
