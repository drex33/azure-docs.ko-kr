---
title: 샘플 API 관리 정책 - Google OAuth 토큰을 사용하여 액세스 권한 부여
titleSuffix: Azure API Management
description: Azure API Managment 정책 샘플 - OAuth 토큰 공급자로 Google을 사용하여 엔드포인트에 대한 액세스 권한을 부여하는 방법을 보여줍니다.
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
ms.openlocfilehash: 6d80c416bd8dd8e1a8af1e7ea2228e71884a80c3
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128601029"
---
# <a name="authorize-access-using-google-oauth-token"></a>Google OAuth 토큰을 사용하여 액세스 권한 부여

이 문서에서는 OAuth 토큰 공급자로 Google을 사용하여 엔드포인트에 대한 액세스 권한을 부여하는 방법을 설명하는 Azure API Managment 정책 샘플을 보여 줍니다. 정책 코드를 설정하거나 편집하려면 [정책 설정 또는 편집](../set-edit-policies.md)에 설명된 단계를 따릅니다. 다른 예제를 보려면 [정책 샘플](../policy-reference.md)을 참조하세요.

## <a name="policy"></a>정책

코드를 **인바운드** 블록에 붙여넣습니다.

[!code-xml[Main](../../../api-management-policy-samples/examples/Simple Google OAuth validate-jwt.policy.xml)]

## <a name="next-steps"></a>다음 단계

APIM 정책에 대해 자세히 알아보기:

+ [변환 정책](../api-management-transformation-policies.md)
+ [정책 샘플](../policy-reference.md)