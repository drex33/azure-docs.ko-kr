---
title: 샘플 API Management 정책 - JWT 클레임에 따라 액세스 권한 부여
titleSuffix: Azure API Management
description: Azure API Managment 정책 샘플 - JWT 클레임 기반의 API에서 특정 HTTP 메서드에 대한 액세스 권한을 부여하는 방법을 보여 줍니다.
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
ms.openlocfilehash: 72f9659bd93cea4cc0adcc638897ba2109fcc046
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131065649"
---
# <a name="authorize-access-based-on-jwt-claims"></a>JWT 클레임을 기반으로 액세스 권한 부여

이 문서에서는 JWT 클레임 기반의 API에서 특정 HTTP 메서드에 대한 액세스 권한을 부여하는 방법을 설명하는 Azure API Managment 정책 샘플을 보여 줍니다. 정책 코드를 설정하거나 편집하려면 [정책 설정 또는 편집](../set-edit-policies.md)에 설명된 단계를 따릅니다. 다른 예제를 보려면 [정책 샘플](index.md)을 참조하세요.

## <a name="policy"></a>정책

코드를 **인바운드** 블록에 붙여넣습니다.

[!code-xml[Main](../../../api-management-policy-samples/examples/Pre-authorize requests based on HTTP method with validate-jwt.policy.xml)]

## <a name="next-steps"></a>다음 단계

APIM 정책에 대해 자세히 알아보기:

+ [변환 정책](../api-management-transformation-policies.md)
+ [정책 샘플](../policy-reference.md)
