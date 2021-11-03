---
author: laujan
ms.service: applied-ai-services
ms.subservice: forms-recognizer
ms.topic: include
ms.date: 11/02/2021
ms.author: lajanuar
ms.custom: ignite-fall-2021
ms.openlocfilehash: 5b9bf09957f70ed08a6d0c7a4988f4c0fde6fab9
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131021660"
---
사용자 지정 모델 학습 데이터의 SAS URL을 검색하려면 Azure Portal의 스토리지 리소스로 이동하고 **Storage Explorer** 탭을 선택합니다. 컨테이너로 이동하고 마우스 오른쪽 단추를 클릭한 후 **공유 액세스 서명 가져오기** 를 선택합니다. 스토리지 계정 자체가 아니라 컨테이너에 대한 SAS를 가져오는 것이 중요합니다. **읽기**, **쓰기**, **삭제** 및 **목록 보기** 권한이 선택되어 있는지 확인하고 **만들기** 를 클릭합니다. 그런 다음, **URL** 섹션의 값을 임시 위치에 복사합니다. `https://<storage account>.blob.core.windows.net/<container name>?<SAS value>` 형식이어야 합니다.
