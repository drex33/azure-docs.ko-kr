---
title: 샘플 API Management 정책 - 상관 관계 ID를 포함하는 헤더 추가
titleSuffix: Azure API Management
description: Azure API Management 정책 샘플 - 상관 관계 ID를 포함하는 헤더를 인바운드 요청에 추가하는 방법을 보여줍니다.
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
ms.openlocfilehash: 421c4e0e2011f14ac076e4fffd334052e7f16800
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128670522"
---
# <a name="add-a-header-containing-a-correlation-id"></a>상관 관계 ID를 포함하는 헤더 추가

이 문서에서는 상관 관계 ID를 포함하는 헤더를 인바운드 요청에 추가하는 방법을 보여 주는 Azure API Management 정책 샘플을 제공합니다. 정책 코드를 설정하거나 편집하려면 [정책 설정 또는 편집](../set-edit-policies.md)에 설명된 단계를 따릅니다. 다른 예제를 보려면 [정책 샘플](../policy-reference.md)을 참조하세요.

## <a name="policy"></a>정책

코드를 **인바운드** 블록에 붙여넣습니다.

[!code-xml[Main](../../../api-management-policy-samples/examples/Add correlation id to inbound request.policy.xml)]

## <a name="next-steps"></a>다음 단계

APIM 정책에 대해 자세히 알아보기:

+ [변환 정책](../api-management-transformation-policies.md)
+ [정책 샘플](../policy-reference.md)