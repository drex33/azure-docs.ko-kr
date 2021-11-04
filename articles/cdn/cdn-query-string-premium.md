---
title: 쿼리 문자열을 사용하여 Azure CDN 캐싱 동작 제어 - 프리미엄 계층
description: Azure CDN 쿼리 문자열 캐싱은 웹 요청에 쿼리 문자열이 포함된 경우 파일이 캐시되는 방식을 제어합니다. 이 문서에서는 Verizon 제품의 Azure CDN Premium에서 쿼리 문자열 캐싱을 설명합니다.
services: cdn
documentationcenter: ''
author: duongau
manager: danielgi
editor: ''
ms.assetid: 99db4a85-4f5f-431f-ac3a-69e05518c997
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 06/11/2018
ms.author: duau
ms.openlocfilehash: 7bf5a9ad9665ae0942f7aa7f61319fccf47bbea3
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131470056"
---
# <a name="control-azure-cdn-caching-behavior-with-query-strings---premium-tier"></a>쿼리 문자열을 사용하여 Azure CDN 캐싱 동작 제어 - 프리미엄 계층
> [!div class="op_single_selector"]
> * [표준 계층](cdn-query-string.md)
> * [프리미엄 계층](cdn-query-string-premium.md)
> 

## <a name="overview"></a>개요
Azure CDN(Content Delivery Network)을 사용하면 쿼리 문자열이 포함된 웹 요청에 대해 파일이 캐시되는 방식을 제어할 수 있습니다. 쿼리 문자열이 있는 웹 요청에서 쿼리 문자열은 물음표(?) 다음에 나오는 요청 부분입니다. 쿼리 문자열은 필드 이름 및 해당 값이 등호(=)로 구분된 하나 이상의 키-값 쌍을 포함할 수 있습니다. 각 키-값 쌍은 앰퍼샌드(&)로 구분됩니다. 예를 들어 http:\//www.contoso.com/content.mov?field1=value1&field2=value2입니다. 요청의 쿼리 문자열에 둘 이상의 키-값 쌍이 있는 경우 해당 순서는 중요하지 않습니다. 

> [!IMPORTANT]
> 표준 및 프리미엄 CDN 제품은 동일한 쿼리 문자열 캐싱 기능을 제공하지만 사용자 인터페이스는 다릅니다. 이 문서는 **Verizon의 Azure CDN Premium** 에 대한 인터페이스를 설명합니다. Azure CDN Standard 제품을 사용한 쿼리 문자열 캐싱에 대해서는 [쿼리 문자열이 포함된 Azure CDN 캐싱 동작 제어 - 표준 계층](cdn-query-string.md)을 참조하세요.
>


세 가지 쿼리 문자열 모드를 사용할 수 있습니다.

- **standard-cache**: 기본 모드입니다. 이 모드에서는 CDN POP(상호 접속 위치) 노드가 첫 번째 요청에서 요청자의 쿼리 문자열을 원본 서버로 전달하고 자산을 캐시합니다. 캐시된 자산이 만료될 때까지 POP 서버에서 제공되는 해당 자산의 모든 후속 요청은 쿼리 문자열을 무시합니다.

    >[!IMPORTANT] 
    > 이 계정의 모든 경로에 대해 토큰 권한 부여를 사용하도록 설정하면 표준 캐시 모드만 사용할 수 있습니다. 

- **no-cache**: 이 모드에서 쿼리 문자열이 포함된 요청은 CDN POP 노드에서 캐시되지 않습니다. POP 노드가 원본 서버에서 직접 자산을 검색하고 각 요청과 함께 요청자에게 전달합니다.

- **unique-cache**: 이 모드에서는 쿼리 문자열을 포함하여 고유한 URL이 있는 각 요청이 고유 캐시가 있는 고유 자산으로 처리됩니다. 예를 들어 example.ashx?q=test1에 대한 요청의 경우 원본 서버에서의 응답이 POP 노드에서 캐시되고 그와 동일한 쿼리 문자열을 가진 후속 캐시에 대해 반환됩니다. example.ashx?q=test2에 대한 요청은 자체 TTL(Time To Live) 설정과 함께 별도의 자산으로 캐시됩니다.
   
    >[!IMPORTANT] 
    > 쿼리 문자열에 세션 ID나 사용자 이름과 같이 요청마다 변경되는 매개 변수가 포함되어 있으면 캐시 적중률이 낮아지므로 이 모드를 사용하지 마세요.

## <a name="changing-query-string-caching-settings-for-premium-cdn-profiles"></a>Premium CDN 프로필에 대한 쿼리 문자열 캐싱 설정 변경
1. CDN 프로필을 연 다음 **관리** 를 클릭합니다.
   
    ![CDN 프로필 관리 단추](./media/cdn-query-string-premium/cdn-manage-btn.png)
   
    CDN 관리 포털이 열립니다.
2. **HTTP Large** 탭을 가리킨 다음 **캐시 설정** 플라이아웃 메뉴를 가리킵니다. **Query-String Caching**(쿼리 문자열 캐싱)을 클릭합니다.
   
    쿼리 문자열 캐싱 옵션이 표시됩니다.
   
    ![CDN 쿼리 문자열 캐싱 옵션](./media/cdn-query-string-premium/cdn-query-string.png)
3. 쿼리 문자열 모드를 선택한 다음 **업데이트** 를 클릭합니다.

> [!IMPORTANT]
> 등록이 CDN 전체에 전파되기까지 시간이 걸리기 때문에, 캐시 문자열 설정 변경이 즉시 표시되지 않을 수 있습니다. 전파는 일반적으로 10분 안에 완료됩니다.
 

