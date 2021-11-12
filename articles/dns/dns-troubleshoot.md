---
title: 문제 해결 가이드 - Azure DNS
description: 이 학습 경로에서 일반적인 Azure DNS 관련 문제를 해결하기 시작합니다.
services: dns
author: rohinkoul
ms.service: dns
ms.topic: troubleshooting
ms.date: 11/10/2021
ms.author: rohink
ms.openlocfilehash: bc003c640e51b855f446878aa0b5829b2d2c09c5
ms.sourcegitcommit: e1037fa0082931f3f0039b9a2761861b632e986d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/12/2021
ms.locfileid: "132400360"
---
# <a name="azure-dns-troubleshooting-guide"></a>Azure DNS 문제 해결 가이드

이 문서에서는 일반적인 Azure DNS 질문에 대한 문제 해결 정보를 제공합니다.

이러한 단계를 수행해도 문제가 해결되지 않으면 [커뮤니티를 지원하기 위한 Microsoft Q&A 질문 페이지](/answers/topics/azure-virtual-network.html)에서 문제를 검색하거나 게시할 수 있습니다. 또는 Azure 지원 요청을 방문할 수 있습니다.

## <a name="i-cant-create-a-dns-zone"></a>DNS 영역을 만들 수 없습니다.

일반적인 문제를 해결하려면 다음 단계 중 하나 이상을 수행해 봅니다.

1.  Azure DNS 감사 로그를 검토하여 실패 이유를 확인합니다.
2.  각 DNS 영역 이름은 해당 리소스 그룹 내에서 고유해야 합니다. 즉, 이름이 동일한 두 DNS 영역에서 리소스 그룹을 공유할 수 없습니다. 다른 영역 이름을 사용하거나 다른 리소스 그룹을 사용해 보세요.
3.  "{subscription ID} 구독에서 영역의 최대 수에 도달했거나 최대 수를 초과했습니다."라는 오류가 표시될 수 있습니다. 다른 Azure 구독을 사용하거나 일부 영역을 삭제하세요. 아니면 Azure 지원부에 문의하여 구독 제한을 높이세요.
4.  "영역 '{zone name}'을(를) 사용할 수 없습니다."라는 오류가 표시될 수 있습니다. 이 오류는 Azure DNS가 이 DNS 영역에 대해 이름 서버를 할당할 수 없음을 의미합니다. 다른 영역 이름을 사용해 보세요. 또는 도메인 이름 소유자인 경우 Azure 지원에 문의하여 이름 서버를 할당할 수 있습니다.

### <a name="recommended-articles"></a>추천 문서

* [DNS 영역 및 레코드](dns-zones-records.md)
* [DNS 영역 만들기](./dns-getstarted-portal.md)

## <a name="i-cant-create-a-dns-record"></a>DNS 레코드를 만들 수 없습니다.

일반적인 문제를 해결하려면 다음 단계 중 하나 이상을 수행해 봅니다.

1.  Azure DNS 감사 로그를 검토하여 실패 이유를 확인합니다.
2.  레코드 집합이 이미 있습니까?  Azure DNS는 레코드 *집합*, 다시 말해서 이름과 유형이 같은 레코드 컬렉션을 사용하여 레코드를 관리합니다. 이름과 유형이 같은 레코드가 이미 있는 상태에서 같은 레코드를 추가하려면 기존 레코드 집합을 편집해야 합니다.
3.  DNS 영역 루트(영역의 '루트')에서 레코드를 만들려고 하시나요? 그렇다면 레코드 이름으로 ‘@’ 문자를 사용하는 것이 DNS 규칙입니다. 또한 DNS 표준은 영역 루트에서 CNAME 레코드를 허용하지 않습니다.
4.  CNAME 충돌이 있나요?  DNS 표준은 다른 유형의 레코드와 이름이 동일한 CNAME 레코드를 허용하지 않습니다. 기존 CNAME이 있으면 다른 유형의 레코드와 이름이 같은 레코드를 만들 수 없습니다.  마찬가지로 유형이 다른 기존 레코드와 이름이 일치하면 CNAME을 만들 수 없습니다. 다른 레코드를 제거하거나 다른 레코드 이름을 선택하여 충돌을 제거하세요.
5.  DNS 영역에서 허용되는 레코드 집합 수 제한에 도달했나요? 현재 레코드 집합 수와 최대 레코드 집합 수는 Azure Portal에서 해당 영역의 '속성'에 표시됩니다. 이 제한에 도달한 경우 일부 레코드 세트를 삭제하거나 Azure 지원에 문의하여 이 영역에 대한 레코드 세트 제한을 늘린 다음, 다시 시도하세요. 

### <a name="recommended-articles"></a>추천 문서

* [DNS 영역 및 레코드](dns-zones-records.md)
* [DNS 영역 만들기](./dns-getstarted-portal.md)

## <a name="i-cant-resolve-my-dns-record"></a>DNS 레코드를 확인할 수 없음

DNS 이름 확인은 여러 가지 이유로 실패할 수 있는 다단계 프로세스입니다. 다음 단계를 따르면 Azure DNS에 호스트된 영역의 DNS 레코드에 대한 DNS 확인이 실패하는 이유를 조사할 수 있습니다.

1.  Azure DNS에서 DNS 레코드가 올바르게 구성되어 있는지 확인합니다. Azure Portal에서 DNS 레코드를 검토하여 영역 이름, 레코드 이름 및 레코드 유형이 올바른지 확인합니다.
2.  Azure DNS 이름 서버에서 DNS 레코드가 올바르게 확인되는지 확인합니다.
    - 로컬 PC에서 DNS 쿼리를 작성할 수 있다면 이름 서버의 현재 상태를 반영하지 않는 캐시된 결과가 표시될 수 있습니다.  또한 회사 네트워크는 종종 DNS 프록시 서버를 사용하는데, 이 서버는 DNS 쿼리를 특정 이름 서버로 보내지 못하게 합니다.  이러한 문제를 방지하려면 [digwebinterface](https://digwebinterface.com) 같은 웹 기반 이름 확인 서비스를 사용하세요.
    - Azure Portal에 표시된 대로 DNS 영역의 올바른 이름 서버를 지정하세요.
    - DNS 이름이 올바른지(영역 이름을 포함하여 정규화된 이름을 지정해야 함), 또 레코드 유형이 올바른지 확인합니다.
3.  DNS 도메인 이름이 [Azure DNS 이름 서버에 올바르게 위임되었는지](dns-domain-delegation.md) 확인합니다. [DNS 위임 유효성 검사를 제공하는 여러 타사 웹 사이트](https://www.bing.com/search?q=dns+check+tool)가 있습니다. 이 테스트는 *영역* 위임 테스트이므로 정규화된 레코드 이름이 아닌 DNS 영역 이름만 입력해야 합니다.
4.  위의 단계를 완료하면 DNS 레코드가 올바르게 확인될 것입니다. [digwebinterface](https://digwebinterface.com)를 사용하여 문제가 해결되었는지 확인할 수 있습니다. 이번에는 기본 이름 서버 설정을 사용하면 됩니다.

### <a name="recommended-articles"></a>추천 문서

* [Azure DNS에 도메인 위임](dns-domain-delegation.md)

## <a name="dns-zone-status-and-unhealthy-delegation-scenarios"></a>DNS 영역 상태 및 비정상 위임 시나리오

DNS 영역 상태는 영역의 현재 상태를 나타냅니다. DNS 영역 상태는 **알 수 없음**, **사용 가능** 및 **저하** 됨이 될 수 있습니다.

### <a name="unknown"></a>Unknown

리소스를 새로 만들 때 이러한 새 리소스에 대 한 상태 신호를 즉시 사용할 수 없습니다. DNS 영역에 대 한 올바른 상태 신호를 얻기 위해 최대 24 시간이 경과할 수 있습니다. 이 시간까지 DNS 영역의 상태는 **알 수 없음** 으로 표시 됩니다.

리소스 상태 검사가 6 시간 넘게 DNS 영역에 대 한 정보를 받지 못한 경우 영역을 알 수 없음으로 표시 합니다. 이 상태는 리소스 상태를 명확 하 게 표시 하지는 않지만 문제 해결 프로세스의 중요 한 데이터 요소입니다. 신호가 수신 되 고 리소스가 예상 대로 실행 되 면 몇 분 후 리소스 상태가 **사용 가능** 으로 변경 됩니다.

다음 스크린샷은 리소스 상태 검사 메시지의 예입니다.

:::image type="content" source="./media/dns-troubleshoot/unknown-status.png" alt-text="알 수 없는 상태의 스크린샷":::

### <a name="available"></a>사용 가능

**사용 가능한**   상태는 리소스 상태 검사에서 DNS 영역에 대 한 위임 문제를 검색 하지 못했음을 나타냅니다. 이 상태는 NS 위임 레코드가 주 영역에서 적절 하 게 유지 관리 되 고 자식 영역에 대 한 레코드가 주 영역에 없는 것을 의미 합니다. 

다음 스크린샷은 리소스 상태 검사 메시지의 예입니다.

:::image type="content" source="./media/dns-troubleshoot/available-status.png" alt-text="사용 가능한 상태의 스크린샷":::

### <a name="degraded"></a>성능 저하됨

**저하** 된 상태는 리소스 상태 검사에서 DNS 영역에 대 한 위임 문제를 검색 했음을 나타냅니다. 위임 구성을 수정 하 고 상태가 **사용 가능** 으로 변경 될 때까지 24 시간 동안 기다립니다.  

다음 스크린샷은 리소스 상태 검사 메시지의 예입니다.

:::image type="content" source="./media/dns-troubleshoot/degraded-status.png" alt-text="성능 저하 상태의 스크린샷.":::

구성을 수정한 후 24 시간이 경과 하 고 DNS 영역이 여전히 저하 된 경우 지원 담당자에 게 문의 하세요.  

### <a name="configuration-error-scenario"></a>구성 오류 시나리오

다음 시나리오에서는 구성 오류가 DNS 영역의 비정상 상태를 초래한 위치를 보여 줍니다.

**비정상 위임**

주 영역에는 기본 영역에서 자식 영역으로 트래픽을 위임 하는 데 도움이 되는 NS 위임 레코드가 포함 되어 있습니다. NS 위임 레코드가 부모 영역에 있는 경우 DNS 서버는 사용자 쿼리를 기반으로 하 여 해당 자식 영역에 대 한 직접 트래픽 및 연결 레코드를 제외한 NS 위임 레코드 아래의 다른 모든 레코드를 마스킹 해야 합니다. 부모 영역에 NS 위임 레코드 아래의 자식 영역 (위임 된 영역)에 대 한 다른 레코드가 포함 되어 있으면 해당 영역은 비정상으로 표시 되 고 상태가 **저하** 됩니다.

**글 루 레코드 란?** -IP 주소를 사용 하 여 위임 된/자식 영역에 트래픽을 전달 하는 데 도움이 되는 위임 레코드 아래의 레코드 이며 다음에 표시 된 것 처럼 구성 됩니다.

| 설정 | 값 |
| ------- | ----- |
| **영역** | contoso.com |
| **위임 레코드** | 자식 NS </br> ns1.child.contoso.com |
| **글 루 레코드** | ns1 1.1.1.1 |

#### <a name="example-of-an-unhealthy-zone"></a>비정상 영역 예

다음은 NS 위임 아래의 레코드를 포함 하는 영역의 예입니다.

* 영역 이름: contoso.com

| 이름 | 유형 | TTL | 값 |
| ---- | ---- | --- | ----- |
| @ | NS | 3600 | ns1-04.azure-dns.com. |
| @ | SOA | 3600 | _SOA 값_ |
| * | A | 3600 | 255.255.255.255 |
| **자식** | **NS** | **3600** | **ns1-08.azure-dns.com** (NS 위임 레코드) |
| _**foo. 자식**_ | _**은**_ | _**3600**_ | _**10.10.10.10**_ |
| _**txt. 자식**_ | _**TXT**_ | _**3600**_ | _**"텍스트 레코드"**_ |
| abc. 테스트 | A | 3600 | 5.5.5.5 |

위의 예제에서 **자식은** NS 위임 레코드입니다. **Child.contoso.com** 및 _**txt**_ 레코드는 자식 영역에만 있어야 하 _**는 레코드입니다**_ . 이러한 레코드는 부모 영역 **contoso.com** 에서 제거 되지 않으면 불일치를 일으킬 수 있습니다. 이러한 불일치로 인해 영역이 **저하** 된 상태로 간주 될 수 있습니다.

#### <a name="examples-of-when-a-zone-is-considered-healthy-or-unhealthy"></a>영역이 정상 또는 비정상으로 간주 되는 경우의 예

| 예 | 상태 |
| ------- | ------ |
| 영역에는 NS 위임 레코드, glue 레코드 및 기타 레코드가 포함 되지 않습니다. | **정상** |
| 영역에는 NS 위임 레코드만 포함 됩니다. | **정상** |
| 영역에는 NS 위임 레코드와 glue 레코드만 포함 됩니다. | **정상** |
| 영역에는 자식 영역에 있어야 하는 NS 위임 레코드 및 기타 레코드 (glue 레코드 제외)가 위임 레코드 아래에 포함 됩니다. | **비정상** |
| 영역에는 NS 위임 레코드, glue 레코드 및 기타 레코드 (glue 레코드 제외)가 포함 됩니다. | **비정상** |

**어떻게 해결할 수 있나요?** -문제를 해결 하려면 부모 영역의 NS 위임 레코드 아래에 있는 glue 레코드를 제외한 모든 레코드를 찾아 제거 합니다.

**비정상 위임 레코드를 찾는 방법** -영역에서 비정상 위임 레코드를 찾기 위해 스크립트를 만들었습니다.  이 스크립트는 비정상 상태인 레코드를 보고 합니다.

1. 에 있는 스크립트를 저장 합니다. [Azure DNS에서 비정상 DNS 레코드 찾기-PowerShell 스크립트 샘플](./scripts/find-unhealthy-dns-records.md)

2. 스크립트 편집기에서 설명한 대로 스크립트를 실행 합니다.  요구 사항에 맞게 스크립트를 편집할 수 있습니다.

**기록 정보** -리소스 상태의 상태 기록 섹션에서 최대 14 일 동안의 상태 기록에 액세스할 수 있습니다. 이 섹션에는 리소스 상태에서 보고 한 가동 중지 시간 가동 중지 시간 (사용 가능한 경우)에 대 한 이유가 포함 되어 있습니다. 현재 Azure는 24 시간 세분성에서 DNS 영역 리소스의 가동 중지 시간을 보여 줍니다.

## <a name="how-do-i-specify-the-service-and-protocol-for-an-srv-record"></a>SRV 레코드에 대한 ‘서비스’ 및 ‘프로토콜’을 지정하려면 어떻게 해냐 하나요?

Azure DNS는 DNS 레코드를 레코드 집합, 다시 말해서 이름과 유형이 같은 레코드 컬렉션으로 관리합니다. SRV 레코드 집합의 경우 레코드 집합 이름에 포함하여 '서비스'와 '프로토콜'을 지정해야 합니다. 기타 SRV 매개 변수('우선 순위', '가중치', '포트', '대상')는 레코드 집합의 각 레코드에 대해 별도로 지정합니다.

SRV 레코드 이름(서비스 이름 'sip', 프로토콜 'tcp') 예제:

- \_sip.\_tcp (영역 루트에 레코드 생성)
- \_sip.\_tcp.sipservice ('sipservice'라는 이름의 레코드 생성)

### <a name="recommended-articles"></a>추천 문서

* [DNS 영역 및 레코드](dns-zones-records.md)
* [Azure Portal을 사용하여 DNS 레코드 집합 및 레코드 만들기](./dns-getstarted-portal.md)
* [SRV 레코드 형식(Wikipedia)](https://en.wikipedia.org/wiki/SRV_record)

## <a name="next-steps"></a>다음 단계

* [Azure DNS 영역 및 레코드](dns-zones-records.md) 알아보기
* Azure DNS 사용을 시작하려면 [DNS 영역 만들기](./dns-getstarted-portal.md) 및 [DNS 레코드 만들기](./dns-getstarted-portal.md) 방법에 대해 알아보세요.
* 기존 DNS 영역을 마이그레이션하려면 [DNS 영역 파일 가져오기 및 내보내기](dns-import-export.md) 방법에 대해 알아보세요.
