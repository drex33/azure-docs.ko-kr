---
author: laujan
ms.service: applied-ai-services
ms.subservice: forms-recognizer
ms.topic: include
ms.date: 05/25/2021
ms.author: lajanuar
ms.custom: ignite-fall-2021
ms.openlocfilehash: 9f4a863cb0c3ecf087d5efdcde37ce907a7a7d41
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131021710"
---
Azure Portal로 이동하여 <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFormRecognizer" title="새 Form Recognizer 리소스 만들기" target="_blank">새 Form Recognizer 리소스 </a>를 만듭니다. **만들기** 창에서 다음 정보를 제공합니다.

| 프로젝트 세부 정보   | Description   |
|--|--|
| **구독** | 액세스 권한이 부여된 Azure 구독을 선택합니다. |
| **리소스 그룹** | 리소스를 포함하는 Azure 리소스 그룹입니다. 새 그룹을 만들거나 기존 그룹에 추가할 수 있습니다. |
| **지역** | Cognitive Service 인스턴스의 위치입니다. 다른 위치를 사용하면 대기 시간이 발생할 수 있지만 리소스의 런타임 가용성에는 영향을 주지 않습니다. |
| **이름** | 리소스에 대한 설명이 포함된 이름입니다. 설명이 포함된 이름(예: *MyNameFormRecognizer*)을 사용하는 것이 좋습니다. |
| **가격 책정 계층** | 리소스 비용은 선택한 가격 책정 계층과 사용량에 따라 달라집니다. 자세한 내용은 API [가격 책정 세부 정보](https://azure.microsoft.com/pricing/details/cognitive-services/)를 참조하세요.

## <a name="retrieve-the-key-and-endpoint"></a>키 및 엔드포인트 검색

Form Recognizer 리소스의 배포가 완료되면 포털의 **모든 리소스** 목록에서 해당 리소스를 찾아 선택합니다. 키와 엔드포인트는 리소스의 **키 및 엔드포인트** 페이지에 있는 **리소스 관리** 아래에 있습니다. 앞으로 이동하기 전에 이들 둘 다 임시 위치에 저장합니다.
