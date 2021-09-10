---
author: ilayrn
ms.service: data-explorer
ms.topic: include
ms.date: 01/20/2020
ms.author: ilayr
ms.openlocfilehash: f9143f3220ad5f90345436c3c1f97419e4d26fc4
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/22/2021
ms.locfileid: "114593337"
---
다음 표에서는 Azure Data Explorer 클러스터의 최대 한도에 대해 설명합니다.

| 리소스 | 제한 |
| --- | --- |
| 구독당 지역별 클러스터 수 | 20 |
| 클러스터당 인스턴스 수 | 1000 | 
| 클러스터의 데이터베이스 수 | 10000 |
| 리더 클러스터(데이터 공유 생산자)당 팔로워 클러스터(데이터 공유 소비자) 수 | 70 |

다음 표에서는 Azure Data Explorer 클러스터에서 수행되는 관리 작업에 대한 한도를 설명합니다.

| 범위 | 작업(Operation) | 제한 |
| --- | --- | --- |
| 클러스터 | 읽기(예: 클러스터 가져오기) | 5분당 500개 |
| 클러스터 | 쓰기(예: 데이터베이스 만들기) | 시간당 1,000개 |

