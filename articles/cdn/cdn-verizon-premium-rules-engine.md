---
title: Azure CDN으로 HTTP 동작 재정의 - Verizon Premium 규칙 엔진
description: 규칙 엔진을 사용하면 특정 유형의 콘텐츠 전달 차단과 같이 Verizon Premium의 Azure CDN에서 HTTP 요청을 처리하는 방식을 사용자 지정하여 캐싱 정책을 정의하고 HTTP 헤더를 수정할 수 있습니다.
services: cdn
author: duongau
ms.service: azure-cdn
ms.topic: how-to
ms.date: 05/31/2019
ms.author: duau
ms.openlocfilehash: 47c1f944c36b33094bc3a0e60411a11e819e8090
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131423397"
---
# <a name="override-http-behavior-using-the-azure-cdn-from-verizon-premium-rules-engine"></a>Verizon Premium 규칙 엔진에서 Azure CDN을 사용하여 HTTP 동작 재정의

[!INCLUDE [cdn-premium-feature](../../includes/cdn-premium-feature.md)]

## <a name="overview"></a>개요

Azure CDN 규칙 엔진을 사용하면 HTTP 요청을 처리하는 방법을 사용자 지정할 수 있습니다. 예를 들어 특정 콘텐츠 형식 배달을 차단하거나, 캐싱 정책을 정의하거나, HTTP 헤더를 수정할 수 있습니다. 이 자습서에서는 CDN 자산의 캐싱 동작을 변경하는 규칙을 만드는 방법을 보여줍니다. 규칙 엔진 구문에 대한 자세한 내용은 [Azure CDN 규칙 엔진 참조](cdn-verizon-premium-rules-engine-reference.md)를 참조하세요.

## <a name="access"></a>액세스 권한

규칙 엔진에 액세스하려면 먼저 **CDN 프로필** 페이지의 맨 위에서 **관리** 를 선택하여 Azure CDN 관리 페이지에 액세스해야 합니다. 엔드포인트가 DSA(동적 사이트 가속)에 최적화되었는지 여부에 따라 엔드포인트의 형식에 적절한 규칙 집합을 사용하여 규칙 엔진에 액세스할 수 있습니다.

- 일반 웹 배달 또는 다른 비 DSA 최적화를 위해 최적화된 엔드포인트:
    
    **HTTP Large** 탭을 선택한 다음, **규칙 엔진** 을 선택합니다.

    ![HTTP에 대한 규칙 엔진](./media/cdn-rules-engine/cdn-http-rules-engine.png)

- DSA에 최적화된 엔드포인트:
    
    **ADN** 탭을 선택한 다음, **규칙 엔진** 을 선택합니다.
    
    ADN은 DSA 콘텐츠를 지정하기 위해 Verizon에서 사용하는 용어입니다. 여기에서 만든 규칙은 DSA에 최적화되지 않은 프로필의 엔드포인트에서 무시됩니다.

    ![DSA에 대한 규칙 엔진](./media/cdn-rules-engine/cdn-dsa-rules-engine.png)

## <a name="tutorial"></a>자습서

1. **CDN 프로필** 페이지에서 **관리** 를 선택합니다.
   
    ![CDN 프로필 관리 단추](./media/cdn-rules-engine/cdn-manage-btn.png)
   
    CDN 관리 포털이 열립니다.

2. **HTTP Large** 탭을 선택한 다음, **규칙 엔진** 을 선택합니다.
   
    새 규칙에 대한 옵션이 표시됩니다.
   
    ![CDN 새 규칙 옵션](./media/cdn-rules-engine/cdn-new-rule.png)
   
   > [!IMPORTANT]
   > 여러 규칙이 나열된 순서는 규칙이 처리되는 방식에 영향을 줍니다. 후속 규칙은 이전 규칙에서 지정된 동작을 재정의할 수 있습니다. 예를 들어 요청 속성을 기반으로 리소스에 대한 액세스를 허용하는 규칙과 모든 요청에 대한 액세스를 거부하는 규칙이 있는 경우 두 번째 규칙이 첫 번째 규칙을 재정의합니다. 규칙은 동일한 속성으로 상호 작용하는 경우에만 이전 규칙을 재정의합니다.
   >

3. **이름/설명** 텍스트 상자에 이름을 입력합니다.

4. 규칙이 적용되는 요청의 형식을 식별합니다. 기본 일치 조건 **항상** 을 사용합니다.
   
   ![CDN 규칙 일치 조건](./media/cdn-rules-engine/cdn-request-type.png)
   
   > [!NOTE]
   > 여러 일치 조건이 드롭다운 목록에서 제공됩니다. 현재 선택한 일치 조건에 대한 내용은 왼쪽에 있는 파랑 정보 아이콘을 선택합니다.
   >
   >  조건식의 자세한 목록은 [규칙 엔진 조건식](cdn-verizon-premium-rules-engine-reference-match-conditions.md)을 참조하세요.
   >  
   > 일치 조건의 자세한 목록은 [규칙 엔진 일치 조건](cdn-verizon-premium-rules-engine-reference-match-conditions.md)을 참조하세요.
   >
   >

5. 새 기능을 추가하려면 **기능** 옆에 있는 **+** 단추를 선택합니다.  왼쪽의 드롭다운 목록에서 **Force Internal Max-Age**(내부 Max-Age 강제)를 선택합니다.  표시되는 텍스트 상자에 **300** 을 입력합니다. 나머지 기본값을 변경하지 않습니다.
   
   ![CDN 규칙 기능](./media/cdn-rules-engine/cdn-new-feature.png)
   
   > [!NOTE]
   > 여러 기능이 드롭다운 목록에서 제공됩니다. 현재 선택한 기능에 대한 내용은 왼쪽에 있는 파랑 정보 아이콘을 선택합니다.
   >
   > **Force Internal Max-Age** 의 경우 자산의 `Cache-Control` 및 `Expires` 헤더는 CDN 에지 노드가 원본의 자산을 새로 고치는 시기를 제어하도록 재정의됩니다. 이 예제에서 CDN 에지 노드는 원본에서 자산을 새로 고치기 전에 300초 또는 5분 동안 자산을 캐시합니다.
   >
   > 자세한 기능 목록은 [규칙 엔진 기능](cdn-verizon-premium-rules-engine-reference-features.md)을 참조하세요.
   >
   >

6. 새 규칙을 저장하려면 **추가** 를 선택합니다.  이제 새 규칙이 승인 대기 중입니다. 승인되면 상태가 **보류 중인 XML** 에서 **활성 XML** 로 변경됩니다.
   
   > [!IMPORTANT]
   > 규칙 변경이 Azure CDN을 통해 전파되기까지 최대 10분이 소요될 수 있습니다.
   >
   >

## <a name="see-also"></a>참고 항목

- [Azure CDN 개요](cdn-overview.md)
- [규칙 엔진 참조](cdn-verizon-premium-rules-engine-reference.md)
- [규칙 엔진 일치 조건](cdn-verizon-premium-rules-engine-reference-match-conditions.md)
- [규칙 엔진 조건식](cdn-verizon-premium-rules-engine-reference-conditional-expressions.md)
- [규칙 엔진 기능](cdn-verizon-premium-rules-engine-reference-features.md)
- [Azure Fridays: Azure CDN의 강력하고 새로운 프리미엄 기능](https://azure.microsoft.com/documentation/videos/azure-cdns-powerful-new-premium-features/)(동영상)