---
title: 샘플 API 관리 정책-백 엔드 서비스에 기능 추가
titleSuffix: Azure API Management
description: Azure API Management 정책 샘플 - 백 엔드 서비스에 기능을 추가하는 방법을 보여줍니다. 예를 들어 일기 예보 API에서 위도 및 경도 대신 장소 이름을 허용합니다.
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
ms.openlocfilehash: 561912a534d36768416f7343a92cc02b61498826
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128618783"
---
# <a name="add-capabilities-to-a-backend-service"></a>백 엔드 서비스에 기능 추가

이 문서에서는 백 엔드 서비스에 기능을 추가하는 방법을 보여 주는 Azure API Management 정책 샘플을 제공합니다. 예를 들어 일기 예보 API에서 위도 및 경도 대신 장소 이름을 허용합니다. 정책 코드를 설정하거나 편집하려면 [정책 설정 또는 편집](../set-edit-policies.md)에 설명된 단계를 따릅니다. 다른 예제를 보려면 [정책 샘플](../policy-reference.md)을 참조하세요.

## <a name="policy"></a>정책

코드를 **인바운드** 블록에 붙여넣습니다.

[!code-xml[Main](../../../api-management-policy-samples/examples/Call out to an HTTP endpoint and cache the response.policy.xml)]

## <a name="next-steps"></a>다음 단계

APIM 정책에 대해 자세히 알아보기:

+ [변환 정책](../api-management-transformation-policies.md)
+ [정책 샘플](../policy-reference.md)