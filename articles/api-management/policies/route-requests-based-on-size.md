---
title: 샘플 API Management 정책 - 메시지 본문 크기에 따라 요청 라우팅
titleSuffix: Azure API Management
description: Azure API Managment 정책 샘플 - 본문 크기에 따라 요청을 라우팅하는 방법을 보여줍니다.
services: api-management
documentationcenter: ''
author: dlepow
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 10/13/2017
ms.author: danlep
ms.openlocfilehash: 6643190be29f03f751c67e7ee917f87efd1edb1f
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128657446"
---
# <a name="route-the-request-based-on-the-size-of-its-body"></a>본문 크기에 따라 요청 라우팅

이 문서에서는 본문 크기에 따라 요청을 라우팅하는 방법을 설명하는 Azure API Managment 정책 샘플을 보여 줍니다. 정책 코드를 설정하거나 편집하려면 [정책 설정 또는 편집](../set-edit-policies.md)에 설명된 단계를 따릅니다. 다른 예제를 보려면 [정책 샘플](../policy-reference.md)을 참조하세요.

## <a name="policy"></a>정책

코드를 **인바운드** 블록에 붙여넣습니다.

[!code-xml[Main](../../../api-management-policy-samples/examples/Route requests based on size.policy.xml)]

## <a name="next-steps"></a>다음 단계

APIM 정책에 대해 자세히 알아보기:

+ [변환 정책](../api-management-transformation-policies.md)
+ [정책 샘플](../policy-reference.md)