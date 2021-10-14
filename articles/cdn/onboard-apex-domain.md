---
title: 루트 또는 루트 도메인을 기존 Azure CDN 엔드포인트에 등록 - Azure Portal
description: Azure Portal을 사용하여 루트 또는 루트 도메인을 기존 Azure CDN 엔드포인트에 등록하는 방법을 알아봅니다.
services: cdn
author: asudbring
ms.service: azure-cdn
ms.topic: how-to
ms.date: 11/07/2020
ms.author: allensu
ms.openlocfilehash: 025950ffcc264a3cfad1185ac1629c6a8fd44814
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/14/2021
ms.locfileid: "129992575"
---
# <a name="onboard-a-root-or-apex-domain-to-an-existing-azure-cdn-endpoint"></a>루트 또는 루트 도메인을 기존 Azure CDN 엔드포인트에 등록

Azure CDN는 CNAME 레코드를 사용하여 사용자 지정 도메인 등록을 위한 도메인 소유권의 유효성을 검사합니다. CDN은 CDN 프로필과 연결된 프런트 엔드 IP 주소를 노출하지 않습니다. 루트 도메인을 Azure CDN에 등록하려는 경우 IP 주소에 매핑할 수 없습니다.

DNS 프로토콜은 영역 루트에서 CNAME 레코드가 할당되는 것을 방지합니다. 예를 들어 도메인이 `contoso.com` 이면 에 대한 CNAME 레코드를 만들 `somelabel.contoso.com` 수 있지만 자체에 대한 CNAME은 만들 수 `contoso.com` 없습니다. 이 제한은 Azure CDN 뒤에 부하가 분산된 애플리케이션이 있는 애플리케이션 소유자에게 문제가 됩니다. CDN 프로필을 사용하려면 CNAME 레코드를 만들어야 하므로 영역 루트에서 CDN 프로필을 가리킬 수 없습니다.

이 문제는 Azure DNS 별칭 레코드를 사용하여 해결할 수 있습니다. CNAME 레코드와 달리 별칭 레코드는 영역 루트에 생성됩니다. 이제 애플리케이션 소유자는 영역 루트 레코드에서 공용 엔드포인트가 있는 CDN 프로필을 가리키도록 할 수 있습니다. 애플리케이션 소유자는 DNS 영역 내 다른 모든 도메인에 사용되는 CDN 프로필을 가리킵니다. 예를 들어 `contoso.com` 및 `www.contoso.com`은 동일한 CDN 프로필을 가리킬 수 있습니다. 

루트 또는 루트 도메인을 CDN 프로필에 매핑하려면 CNAME 평면화 또는 DNS 추적이 필요합니다. IP 주소에 도달할 때까지 DNS 공급자가 CNAME 항목을 재귀적으로 확인하는 메커니즘입니다. 이 기능은 CDN 엔드포인트에 대한 Azure DNS에서 지원됩니다. 

> [!NOTE]
> CNAME 평면화 또는 DNS 추적을 지원하는 다른 DNS 공급자도 있습니다. 그러나 Azure CDN은 고객의 도메인 호스팅을 위해 Azure DNS를 사용하는 것을 권장합니다.

Azure Portal를 사용하여 CDN에서 루트 도메인을 등록하고 TLS 종료를 위한 인증서와 연결하여 HTTPS를 사용하도록 설정할 수 있습니다. 루트 도메인은 루트 또는 naked 도메인으로도 불립니다.

## <a name="create-an-alias-record-for-zone-apex"></a>영역 루트에 대한 별칭 레코드 생성하기

1. 등록할 도메인에 대한 **Azure DNS** 구성을 엽니다.

2. **+ 레코드 집합** 을 선택합니다.

3. **레코드 세트 추가** 에서 다음 정보를 입력하거나 선택합니다.

    | 설정 | 값 |
    | ------- | ------|
    | 이름 | **@** 을 입력합니다. |
    | 유형 | **A** 를 선택합니다. |
    | 별칭 레코드 집합 | **Yes** 를 선택합니다. |
    | 별칭 형식 | **Azure 리소스** 를 선택합니다. |
    | 구독을 선택합니다. | 구독을 선택합니다. |
    | Azure 리소스 | CDN 엔드포인트를 선택합니다. |

4. **TTL** 값을 입력합니다.

5. **확인** 을 선택하여 변경 내용을 제출합니다.

    :::image type="content" source="./media/onboard-apex-domain/cdn-apex-alias-record.png" alt-text="영역 루트에 대한 별칭 레코드":::

6. 위의 단계에서는 CDN 리소스를 가리키는 영역 루트 레코드를 만듭니다. CNAME 레코드 매핑 **cdnverify** 는 CDN 프로필에 도메인을 등록하는 데 사용됩니다.
    1. 예: **cdnverify.contoso.com**.
    

## <a name="onboard-the-custom-domain-on-your-cdn"></a>CDN에서 사용자 지정 도메인 등록

사용자 지정 도메인을 등록한 후에 CDN 엔드포인트에 추가할 수 있습니다. 

1. [Azure Portal](https://portal.azure.com/)에 로그인하고 사용자 지정 도메인에 매핑하려는 엔드포인트를 포함하는 CDN 프로필로 이동합니다.
    
2. **CDN 프로필** 페이지에서 사용자 지정 도메인과 연결할 CDN 엔드포인트를 선택합니다.

    :::image type="content" source="media/onboard-apex-domain/cdn-endpoint-selection.png" alt-text="CDN 엔드포인트 선택" border="true":::
    
3. **+ 사용자 지정 도메인** 을 선택합니다. 

   :::image type="content" source="media/onboard-apex-domain/cdn-custom-domain-button.png" alt-text="사용자 지정 도메인 추가 단추" border="true":::

4. **사용자 지정 도메인 추가** 에서 **엔드포인트 호스트 이름** 은 미리 채워져 있으며 CDN 엔드포인트 URL **\<endpoint-hostname>** .azureedge.net에서 파생됩니다. 이는 변경할 수 없습니다.

5. **사용자 지정 호스트 이름** 의 경우 CNAME 레코드의 원본 도메인으로 사용하려면 사용자 지정 루트 또는 루트 도메인을 입력합니다. 
    1. 예를 들어 **contoso.com** 입니다. **cdnverify 하위 도메인 이름을 사용하지 마세요**.

    :::image type="content" source="media/onboard-apex-domain/cdn-add-custom-domain.png" alt-text="사용자 지정 도메인 추가" border="true":::

6. **추가** 를 선택합니다.

   Azure에서 입력한 사용자 지정 도메인 이름에 대한 CNAME 레코드가 있는지 확인합니다. CNAME이 올바르면 사용자 지정 도메인의 유효성이 검사됩니다. 

   새 사용자 지정 도메인 설정이 모든 CDN 에지 노드에 전파되려면 다소 시간이 걸릴 수 있습니다. 
    - **Microsoft의 Azure CDN 표준** 프로필의 경우 일반적으로 10분 이내에 전파가 완료됩니다. 
    - **Akamai의 Azure CDN Standard** 프로필의 경우, 일반적으로 1분 이내에 전파가 완료됩니다. 
    - **Verizon의 Azure CDN 표준** 및 **Verizon의 Azure CDN 프리미엄** 프로필의 경우 일반적으로 10분 이내에 전파가 완료됩니다.   

## <a name="enable-https-on-your-custom-domain"></a>사용자 지정 도메인에 HTTPS를 사용하도록 설정

Azure CDN에 대한 사용자 지정 도메인에서 HTTPS를 사용하도록 설정하는 방법에 대한 자세한 내용은 [자습서: Azure CDN 사용자 지정 도메인에서 HTTPS 구성](cdn-custom-ssl.md)을 참조하세요.

## <a name="next-steps"></a>다음 단계

- [CDN 엔드포인트를 만드는 방법](cdn-create-new-endpoint.md)을 알아봅니다.
