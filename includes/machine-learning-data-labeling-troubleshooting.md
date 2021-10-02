---
author: sgilley
ms.service: machine-learning
ms.topic: include
ms.date: 09/23/2021
ms.author: sdgilley
ms.openlocfilehash: 8122a9c001ce56edefaa0215fc20fe183d2cd00c
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/01/2021
ms.locfileid: "129372828"
---
이러한 문제가 발생하면 다음 팁을 사용하세요.

|문제  |해결 방법  |
|---------|---------|
|Blob 데이터 저장소에 생성된 데이터 세트만 사용할 수 있습니다.     |  현재 릴리스의 알려진 제한 사항입니다.       |
|생성 후 프로젝트는 오랜 시간 동안 "초기화 중"을 표시합니다.     | 페이지를 수동으로 새로 고칩니다. 초기화는 초당 약 20 datapoints 완료 되어야 합니다. 자동 새로 고침의 부족은 알려진 문제입니다.         |
|새로 레이블이 지정 된 항목은 데이터 검토에 표시 되지 않습니다.     |   레이블이 지정 된 모든 항목을 로드 하려면 **첫 번째** 단추를 선택 합니다. **첫 번째** 단추를 클릭하면 목록의 맨 앞으로 돌아가지만 레이블이 지정된 모든 데이터가 로드됩니다.      |
|특정 레이블 지정자에게 작업 세트를 할당할 수 없습니다.     |   현재 릴리스의 알려진 제한 사항입니다.  |