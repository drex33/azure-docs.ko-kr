---
author: tfitzmac
ms.service: azure-app-configuration
ms.topic: include
ms.date: 08/19/2021
ms.author: tomfitz
ms.openlocfilehash: 16929503e0710b61a5dd0cd0057059487369f8b5
ms.sourcegitcommit: 9f1a35d4b90d159235015200607917913afe2d1b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/21/2021
ms.locfileid: "122635553"
---
| 리소스 | 제한 | 의견 |
| --- | --- | ---|
| 무료 계층에 대한 구성 저장소 | 구독당 1개 저장소 |
| 표준 계층에 대한 구성 저장소 | 구독당 무제한 저장소 | 
| 무료 계층에 대한 구성 저장소 요청 | 일별 1000건의 요청  | 할당량을 모두 사용하고 나면, 하루가 지나기 전까지 모든 요청에 대해 HTTP 상태 코드 429가 반환됩니다. |
| 표준 계층에 대한 구성 저장소 요청 | 시간당 30,000  |할당량이 소진되면 요청은 해당 시간이 끝날 때까지 너무 많은 요청을 나타내는 HTTP 상태 코드 429를 반환할 수 있습니다.|  |
| 무료 계층용 스토리지 | 10MB |
| 표준 계층용 스토리지 | 1GB |
| 요청 크기 | 10KB  | 크기는 모든 메타데이터를 포함하는 요청을 기준으로 합니다. 
