---
author: tfitzmac
ms.service: azure-app-configuration
ms.topic: include
ms.date: 08/19/2021
ms.author: tomfitz
ms.openlocfilehash: 3b04007ef7329231da7de31ec465de2c1585c7e4
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/23/2021
ms.locfileid: "122752859"
---
| 리소스 | 제한 | 의견 |
| --- | --- | ---|
| 무료 계층에 대한 구성 저장소 | 구독당 1개 저장소 |
| 표준 계층에 대한 구성 저장소 | 구독당 무제한 저장소 | 
| 무료 계층에 대한 구성 저장소 요청 | 일별 1000건의 요청  | 할당량을 모두 사용하고 나면, 하루가 지나기 전까지 모든 요청에 대해 HTTP 상태 코드 429가 반환됩니다. |
| 표준 계층에 대한 구성 저장소 요청 | 시간당 30,000  |할당량이 소진되면 요청은 해당 시간이 끝날 때까지 너무 많은 요청을 나타내는 HTTP 상태 코드 429를 반환할 수 있습니다.|  
| 무료 계층용 스토리지 | 10MB |
| 표준 계층용 스토리지 | 1GB |
| 키 및 값 | 10KB  | 모든 메타데이터를 포함한 단일 키-값 항목의 경우
