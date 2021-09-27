---
author: vimeht
ms.author: vimeht
ms.date: 7/8/2021
ms.topic: include
ms.service: iot-hub-device-update
ms.openlocfilehash: d7970fff815449adf6412c2748e22b2d471e52b0
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128645464"
---
 > [!NOTE]
 > 지정된 리소스나 작업에 조정 가능한 한도가 없는 경우 기본 한도와 최대 한도는 동일합니다.
 > 한도를 조정할 수 있는 경우 표의 기본 한도 및 최대 한도 헤더에 다른 값이 포함됩니다. 한도는 기본 한도를 초과할 수 있지만 최대 한도를 초과할 수는 없습니다.
 > 한도나 할당량을 기본 한도 이상으로 높이려는 경우 [온라인 고객 지원 요청](https://azure.microsoft.com/support/options/)을 엽니다.


다음 표에서는 Azure Resource Manager의 Device Update for IoT Hub 리소스 한도를 제공합니다.

| 리소스 |  기본 제한 | 최대 제한 | 조정 가능 |
| --- | --- | --- | --- |
| 구독당 계정 | 2 | 25 | 예 |
| 계정당 인스턴스 수 | 2 | 25 | 예 |
| 계정 이름의 길이 | 최소: 3 <br/> 최대: 24 | 최소: 3 <br/> 최대: 24 | No |
| 인스턴스 이름의 길이 | 최소: 3 <br/> 최대: 36 | 최소: 3 <br/> 최대: 36 | No |



다음 표에서는 Device Update for IoT Hub 내의 작업과 관련된 다양한 한도를 제공합니다.

| 작업 |  기본 제한 | 최대 제한 | 조정 가능 |
| --- | --- | --- | --- |
| 인스턴스당 디바이스 수 | 10000 | 10000 | No |
| 인스턴스당 업데이트 공급자 수 | 25 | 25 | 아니요 |
| 인스턴스당, 공급자당 업데이트 이름 수 | 25 | 25 | 아니요 |
| 인스턴스당, 업데이트 공급자와 이름당 업데이트 버전 수 | 100 | 100 | 아니요 |
| 인스턴스당 총 업데이트 수 | 100 | 100 | 아니요 |
| 최대 단일 업데이트 파일 크기 | 2GB | 2GB | No |
| 단일 가져오기 작업 내 모든 파일의 최대 결합 크기 | 2GB | 2GB | No |
| 인스턴스당 디바이스 그룹 수 | 75 | 75 | 아니요 |
