---
title: Azure CDN 엔드포인트 다중 원본
description: Azure CDN 엔드포인트 다중 원본을 시작합니다.
services: cdn
author: asudbring
manager: KumudD
ms.service: azure-cdn
ms.topic: how-to
ms.date: 08/18/2021
ms.author: allensu
ms.openlocfilehash: 1e17c747c87a5abb184ae4b7b263a8b5bd1a3a49
ms.sourcegitcommit: d2875bdbcf1bbd7c06834f0e71d9b98cea7c6652
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/12/2021
ms.locfileid: "129855995"
---
# <a name="azure-cdn-endpoint-multi-origin"></a>Azure CDN 엔드포인트 다중 원본

다중 원본 지원은 가동 중지 시간을 제거하고 글로벌 백업을 설정합니다. 

Azure CDN 엔드포인트 내에서 여러 원본을 선택하면 제공된 중복성이 각 원본의 상태를 검색하고 필요한 경우 장애 조치(failover)하여 위험을 분산합니다.

하나 이상의 원본 그룹을 설치하고 기본 원본 그룹을 선택합니다. 각 원본 그룹은 유사한 워크로드를 수행할 수 있는 하나 이상의 원본 컬렉션입니다.

첫 번째 원본 그룹은 기본 원본 그룹으로 설정 됩니다. 다중 원본 기능은 CDN 끝점에 대 한 기본 원본 그룹을 선택할 때 사용 하도록 설정 됩니다. 다중 원본 기능을 사용 하도록 설정 하면 사용 하지 않도록 설정할 수 없으며 기본 원본 그룹을 삭제할 수 없습니다. 기본 원본 그룹은 요청을 원본으로 라우팅하는 데 사용 됩니다. 원본 그룹 구성을 업데이트 하 고 단일 원본 구성으로 전환할 수 있습니다. 또한 기본 원본 그룹 지정을 다른 원본 그룹으로 변경할 수 있습니다.

> [!NOTE]
> 현재 이 기능은 Microsoft의 Azure CDN에서만 사용할 수 있습니다. 

## <a name="create-the-origin-group"></a>원본 그룹 만들기

1. [Azure 포털](https://portal.azure.com)

2. Azure CDN 프로필을 선택한 다음, 다중 원본에 대해 구성할 엔드포인트를 선택합니다.

3. 엔드포인트 구성에서 **설정** 에 있는 **원본** 을 선택합니다.

    :::image type="content" source="./media/endpoint-multiorigin/endpoint-multiorigin-1.png" alt-text="CDN 엔드포인트" border="true":::

4. 다중 원본을 사용하도록 설정하려면 원본 그룹이 하나 이상 필요합니다. **원본 그룹 만들기** 를 선택합니다.

    :::image type="content" source="./media/endpoint-multiorigin/endpoint-multiorigin-2.png" alt-text="원본 설정" border="true":::

5. **원본 그룹 추가** 구성에서 다음 정보를 입력하거나 선택합니다.

   | 설정           | 값                                                                 |
   |-------------------|-----------------------------------------------------------------------|
   | 원본 그룹 이름 | 원본 그룹의 이름을 입력합니다.                                   |
   | 프로브 상태      | **사용** 을 선택합니다. </br> Azure CDN은 전 세계 여러 지점에서 상태 프로브를 실행하여 원본 상태를 확인합니다. 추가 비용을 방지하기 위해 현재 원본 그룹이 활성화되지 않은 경우에는 사용하도록 설정하지 마세요.
   | 프로브 경로        | 상태를 확인하는 데 사용되는 원본에 대한 경로입니다. |
   | 프로브 간격    | 프로브 간격으로 1, 2 또는 4분을 선택합니다.                        |
   | 프로브 프로토콜    | **HTTP** 또는 **HTTPS** 를 선택합니다.                                         |
   | 프로브 방법      | **Head** 또는 **Get** 을 선택합니다.                                           |
   | 기본 원본 그룹 | 기본 원본 그룹으로 설정할 상자를 선택합니다.
    
   :::image type="content" source="./media/endpoint-multiorigin/endpoint-multiorigin-3.png" alt-text="원본 그룹 추가" border="true":::

6. **추가** 를 선택합니다.

## <a name="add-multiple-origins"></a>여러 원본 추가

1. 엔드포인트의 원본 설정에서 **+ 원본 만들기** 를 선택합니다.

    :::image type="content" source="./media/endpoint-multiorigin/endpoint-multiorigin-5.png" alt-text="원본 만들기" border="true":::

2. **원본 추가** 구성에서 다음 정보를 입력하거나 선택합니다.

   | 설정           | 값                                                                 |
   |-------------------|-----------------------------------------------------------------------|
   | 이름        | 원본의 이름을 입력합니다.        |
   | 원본 형식 | **스토리지**, **클라우드 서비스**, **웹앱** 또는 **사용자 지정 원본** 을 선택합니다.                                   |
   | 원본 호스트 이름        | 원본 호스트 이름을 선택하거나 입력합니다.  이전 설정에서 지정한 사용할 수 있는 모든 원본 형식이 드롭다운에 나열됩니다. 원본 형식으로 **사용자 지정 원본** 을 선택한 경우 사용자 지정 원본 서버의 도메인을 입력합니다. |
   | 원본 호스트 헤더    | Azure CDN에서 각 요청과 함께 보낼 호스트 헤더를 입력하거나 기본값을 유지합니다.                        |
   | HTTP 포트   | HTTP 포트를 입력합니다.                                         |
   | HTTPS 포트     | HTTPS 포트를 입력합니다.                                           |
   | 우선 순위    | 1과 5 사이의 숫자를 입력합니다.       |
   | 무게      | 1과 1000 사이의 숫자를 입력합니다.   |

    > [!NOTE]
    > 원본이 원본 그룹 내에 생성되면 우선 순위와 가중치를 가져와야 합니다. 원본 그룹에 원본이 하나만 있는 경우 기본 우선 순위와 가중치는 1로 설정됩니다. 원본이 정상 상태이면 트래픽이 가장 높은 우선 순위 원본으로 라우팅됩니다. 원본이 비정상으로 확인되면 연결이 우선 순위에 따라 다른 원본으로 전환됩니다. 두 원본의 우선 순위가 동일한 경우 트래픽은 원본에 대해 지정된 가중치에 따라 배포됩니다. 

    :::image type="content" source="./media/endpoint-multiorigin/endpoint-multiorigin-6.png" alt-text="다른 원본 추가" border="true":::

3. **추가** 를 선택합니다.

4. 모든 원본에 대한 원본 경로를 설정하려면 **원본 구성** 을 선택합니다.

    원본 경로는 원본에 전달된 요청을 구성할 때 Microsoft CDN이 사용할 URL을 다시 작성하는데 사용됩니다. 또한 들어오는 요청의 나머지 부분도 모두 전달합니다. 기본값으로 이 경로는 제공되지 않습니다. 따라서 Microsoft CDN은 원본에 대한 요청의 수신 URL 경로를 사용합니다.

    원본 경로: `/fwd/`

    수신 URL 경로: `/foo/a/b/image1.jpg` </br> Microsoft CDN에서 원본으로의 URL: `fwd/foo/a/b/image1.jpg.`

    :::image type="content" source="./media/endpoint-multiorigin/endpoint-multiorigin-7.png" alt-text="원본 경로 구성" border="true":::

5. **확인** 을 선택합니다.

## <a name="configure-origins-and-origin-group-settings"></a>원본 및 원본 그룹 설정 구성

여러 개의 원본 및 원본 그룹이 있는 경우 원본을 다른 그룹에 추가하거나 제거할 수 있습니다. 동일한 그룹 내의 원본은 유사한 워크로드를 처리해야 합니다. 트래픽은 정상 상태, 우선 순위 및 가중치 값에 따라 이러한 원본에 배포됩니다. 

1. Azure CDN 엔드포인트의 원본 설정에서 구성하려는 원본 그룹의 이름을 선택합니다.

    :::image type="content" source="./media/endpoint-multiorigin/endpoint-multiorigin-8.png" alt-text="원본 및 원본 그룹 설정 구성" border="true":::

2. **원본 그룹 업데이트** 에서 **+ 원본 선택** 을 선택합니다.

    :::image type="content" source="./media/endpoint-multiorigin/endpoint-multiorigin-9.png" alt-text="원본 그룹 업데이트" border="true":::

4. 풀 다운 상자에서 그룹에 추가할 원본을 선택하고 **확인** 을 선택합니다.

5. 원본이 그룹에 추가되었는지 확인한 다음, **저장** 을 선택합니다.

    :::image type="content" source="./media/endpoint-multiorigin/endpoint-multiorigin-10.png" alt-text="그룹에 추가된 추가 원본 확인" border="true":::

## <a name="remove-origin-from-origin-group"></a>원본 그룹에서 원본 제거

1. Azure CDN 엔드포인트의 원본 설정에서 원본 그룹의 이름을 선택합니다.

    :::image type="content" source="./media/endpoint-multiorigin/endpoint-multiorigin-8.png" alt-text="그룹에서 원본 제거" border="true":::

2. 원본 그룹에서 원본을 제거하려면 원본 옆에 있는 휴지통 아이콘을 선택하고 **저장** 을 선택합니다.

    :::image type="content" source="./media/endpoint-multiorigin/endpoint-multiorigin-11.png" alt-text="원본 그룹 업데이트 원본 삭제" border="true":::

## <a name="override-origin-group-with-rules-engine"></a>규칙 엔진을 통해 원본 그룹 재정의

표준 규칙 엔진을 사용하여 트래픽이 다른 원본 그룹에 배포되는 방식을 사용자 지정합니다.

요청 URL에 따라 다른 그룹에 트래픽을 분산합니다.

1. CDN 엔드포인트의 **규칙 엔진** 을 **설정** 에서 선택합니다.

:::image type="content" source="./media/endpoint-multiorigin/endpoint-multiorigin-12.png" alt-text="규칙 엔진" border="true":::

2. **규칙 추가** 를 선택합니다.

3. 규칙 이름을 **이름** 에 입력합니다.

4. **+ 조건** 을 선택하고 **URL 경로** 를 선택합니다.

5. **연산자** 풀에서 **포함** 을 선택합니다.

6. **값** 에 **/images** 를 입력합니다.

7. **+ 작업 추가** 를 선택하고 **원본 그룹 재정의** 를 선택합니다.

8. **원본 그룹** 의 풀 다운 상자에서 원본 그룹을 선택합니다.

:::image type="content" source="./media/endpoint-multiorigin/endpoint-multiorigin-13.png" alt-text="규칙 엔진 조건" border="true":::

URL 경로에 **/images** 가 포함된 경우 모든 수신 요청에 대해 요청은 **(myorigingroup)** 작업 섹션의 원본 그룹에 할당 됩니다. 

## <a name="next-steps"></a>다음 단계
이 문서에서는 Azure CDN 엔드포인트 다중 원본을 사용하도록 설정했습니다.

Azure CDN 및 이 문서에 언급된 다른 Azure 서비스에 대한 자세한 내용은 다음을 참조하세요.

* [Azure CDN](./cdn-overview.md)
* [Azure CDN 제품 기능 비교](./cdn-features.md)
