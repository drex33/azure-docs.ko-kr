---
title: 빠른 시작 - Azure Communication Services를 사용하여 전화 번호 가져오기 및 관리
description: Azure Communication Services를 사용하여 전화 번호를 관리하는 방법 알아보기
author: prakulka
manager: nmurav
services: azure-communication-services
ms.author: prakulka
ms.date: 06/30/2021
ms.topic: quickstart
ms.service: azure-communication-services
ms.subservice: pstn
ms.custom: references_regions
zone_pivot_groups: acs-azp-java-net-python-csharp-js
ms.openlocfilehash: a99f0d3aaf65ad7188f02c2c587653bd53f9948b
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128677115"
---
# <a name="quickstart-get-and-manage-phone-numbers"></a>빠른 시작: 전화 번호 가져오기 및 관리

[!INCLUDE [Regional Availability Notice](../../includes/regional-availability-include.md)]

[!INCLUDE [Bulk Acquisition Instructions](../../includes/phone-number-special-order.md)]

::: zone pivot="platform-azp"
[!INCLUDE [Azure portal](./includes/phone-numbers-portal.md)]
::: zone-end

::: zone pivot="programming-language-csharp"
[!INCLUDE [Azure portal](./includes/phone-numbers-net.md)]
::: zone-end

::: zone pivot="programming-language-java"
[!INCLUDE [Java](./includes/phone-numbers-java.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [Python](./includes/phone-numbers-python.md)]
::: zone-end

::: zone pivot="programming-language-javascript"
[!INCLUDE [JavaScript](./includes/phone-numbers-js.md)]
::: zone-end

## <a name="troubleshooting"></a>문제 해결

일반적인 질문 및 문제:

- 전화 구매는 미국에서만 지원됩니다. 전화 번호를 구매하려면 다음 사항을 확인하세요.
  - 연결된 Azure 구독 청구 주소가 미국에 있습니다. 지금은 리소스를 다른 구독으로 이동할 수 없습니다.
  - Communication Services 리소스가 미국 데이터 위치로 프로비저닝됩니다. 지금은 리소스를 다른 데이터 위치로 이동할 수 없습니다.

- 전화 번호가 릴리스되면 청구 주기가 끝날 때까지 해당 전화 번호를 릴리스하거나 재구매할 수 없습니다.

- Communication Services 리소스가 삭제될 경우 그 즉시 해당 리소스와 연결된 전화 번호가 자동으로 재임대됩니다.

## <a name="next-steps"></a>다음 단계

이 빠른 시작에서는 다음을 수행하는 방법을 알아보았습니다.

> [!div class="checklist"]
> * 전화 번호 구매
> * 전화 번호 관리
> * 전화 번호 재임대

> [!div class="nextstepaction"]
> [SMS 보내기](../telephony-sms/send.md)
> [전화 시작](../voice-video-calling/getting-started-with-calling.md)
