---
author: sgilley
ms.service: machine-learning
ms.topic: include
ms.date: 09/23/2021
ms.author: sdgilley
ms.openlocfilehash: 2a1c7c65721483e851a7686c38736f0664b965ad
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/01/2021
ms.locfileid: "129372825"
---
데이터 세트에 새 파일을 추가하려는 경우 증분 새로 고침을 사용하여 이러한 새 파일을 프로젝트에 추가합니다.   **증분 새로** 고침을 사용하도록 설정하면 레이블 지정 완성율에 따라 새 이미지가 프로젝트에 추가될 수 있도록 정기적으로 데이터 세트를 확인합니다.   프로젝트에 최대 500,000개의 파일이 포함되어 있으면 새 데이터에 대한 확인이 중지됩니다.

프로젝트에 파일을 더 추가하려면 [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/)를 사용하여 Blob Storage의 적절한 폴더에 업로드합니다. 

프로젝트에서 데이터 저장소의 새 데이터를 지속적으로 모니터링 하려면 **증분 새로 고침 사용** 을 선택 합니다. 사용 하도록 설정 하면 하루에 한 번 데이터를 프로젝트로 가져옵니다. 데이터 저장소에 새 데이터를 추가한 후에는 프로젝트에 표시 될 때까지 기다려야 합니다.  프로젝트의 **세부 정보** 탭에 있는 **증분 새로 고침** 섹션에서 데이터의 마지막 새로 고침 시간에 대한 타임스탬프를 볼 수 있습니다.
데이터 저장소의 새 파일을 프로젝트에 추가 하지 않으려면 선택을 취소 합니다.