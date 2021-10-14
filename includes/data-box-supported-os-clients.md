---
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: include
ms.date: 09/14/2021
ms.author: alkohli
ms.openlocfilehash: acbb239a6fb839f12b31f8521e4a07fc6083458e
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "130000089"
---
디바이스에 연결된 클라이언트를 통해 데이터 복사 작업에 대해 지원되는 운영 체제의 목록은 다음과 같습니다.

| **운영 체제** | **버전** | **참고** |
| --- | --- | --- |
| Windows Server |2016 RS1 이상<br> 2019 RS5 이상 | 이러한 운영 체제의 이전 버전에서는 `robocopy /B` 대체 데이터 스트림 ()를 포함 하는 파일을 복사 하거나 acl (Access Control 목록)에서 EAs (확장 특성)를 사용 하는 데 RoboCopy를 백업 모드 ()에 사용할 수 없습니다. |
| Windows |7, 8, 10 |   |
| Linux    |         |   |
