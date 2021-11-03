---
author: sgilley
ms.service: machine-learning
ms.topic: include
ms.date: 09/23/2021
ms.author: sdgilley
ms.openlocfilehash: 1c1ab85b6da016b966bab7c66cd2a22f257d27d1
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131068406"
---
데이터 세트에 새 파일을 추가하려는 경우 증분 새로 고침을 사용하여 이러한 새 파일을 프로젝트에 추가합니다.   **증분 새로 고침** 을 사용 하도록 설정 하면 레이블 지정 완료 률에 따라 프로젝트에 새 파일을 추가할 수 있도록 데이터 집합을 주기적으로 확인 합니다.   프로젝트에 최대 500,000개의 파일이 포함되어 있으면 새 데이터에 대한 확인이 중지됩니다.

프로젝트에 파일을 더 추가하려면 [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/)를 사용하여 Blob Storage의 적절한 폴더에 업로드합니다. 

프로젝트에서 데이터 저장소의 새 데이터를 지속적으로 모니터링 하려면 **증분 새로 고침 사용** 을 선택 합니다. 사용 하도록 설정 하면 하루에 한 번 데이터를 프로젝트로 가져옵니다. 데이터 저장소에 새 데이터를 추가한 후에는 프로젝트에 표시 될 때까지 기다려야 합니다.  프로젝트의 **세부 정보** 탭에 있는 **증분 새로 고침** 섹션에서 데이터의 마지막 새로 고침 시간에 대한 타임스탬프를 볼 수 있습니다.
데이터 저장소의 새 파일을 프로젝트에 추가 하지 않으려면 선택을 취소 합니다.