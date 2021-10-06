---
author: baanders
description: 쿼리 API 대기 시간 정보 포함
ms.service: digital-twins
ms.topic: include
ms.date: 09/21/2021
ms.author: baanders
ms.openlocfilehash: 2e538b6f0197f408be649bd76cb4a15fbe22b4bb
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128557674"
---
>[!NOTE]
>그래프의 데이터를 변경한 후 변경 사항이 쿼리에 반영될 때까지 대기 시간은 최대 10초일 수 있습니다. 
>
>[DigitalTwins API](../articles/digital-twins/concepts-apis-sdks.md#overview-data-plane-apis)는 변경 사항을 즉시 반영하므로 즉각적인 응답이 필요한 경우 API 요청([DigitalTwins GetById](/rest/api/digital-twins/dataplane/twins/digitaltwins_getbyid))이나 SDK 호출([GetDigitalTwin](/dotnet/api/azure.digitaltwins.core.digitaltwinsclient.getdigitaltwin?view=azure-dotnet&preserve-view=true))을 사용하여 쿼리 대신 트윈 데이터를 가져옵니다.