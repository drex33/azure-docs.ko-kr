---
author: laujan
ms.service: applied-ai-services
ms.subservice: forms-recognizer
ms.topic: include
ms.date: 05/25/2021
ms.author: lajanuar
ms.openlocfilehash: f1024edf7d05d47ba14478a1561c29f7859a245c
ms.sourcegitcommit: 5f659d2a9abb92f178103146b38257c864bc8c31
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/17/2021
ms.locfileid: "122327138"
---
Azure Portal로 이동하여 <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFormRecognizer" title="새 Form Recognizer 리소스 만들기" target="_blank">새 Form Recognizer 리소스 </a>를 만듭니다. **만들기** 창에서 다음 정보를 제공합니다.

| 프로젝트 세부 정보   | Description   |
|--|--|
| **구독** | 액세스 권한이 부여된 Azure 구독을 선택합니다. |
| **리소스 그룹** | 리소스가 포함될 [Azure 리소스 그룹](/azure/cloud-adoption-framework/govern/resource-consistency/resource-access-management#what-is-an-azure-resource-group)입니다. 새 그룹을 만들거나 기존 그룹에 추가할 수 있습니다. |
| **지역** | Cognitive Service 인스턴스의 위치입니다. 다른 위치를 사용하면 대기 시간이 발생할 수 있지만 리소스의 런타임 가용성에는 영향을 주지 않습니다. |
| **이름** | 리소스에 대한 설명이 포함된 이름입니다. 설명이 포함된 이름(예: *MyNameFormRecognizer*)을 사용하는 것이 좋습니다. |
| **가격 책정 계층** | 리소스 비용은 선택한 가격 책정 계층과 사용량에 따라 달라집니다. 자세한 내용은 API [가격 책정 세부 정보](https://azure.microsoft.com/pricing/details/cognitive-services/)를 참조하세요.

> [!NOTE]
> Azure Portal에서 Cognitive Service 리소스를 만들 때는 다중 서비스 구독 키(여러 Cognitive Service에서 사용) 또는 단일 서비스 구독 키(특정 Cognitive Service에서만 사용)를 만들 수 있는 옵션이 제공됩니다. 현재 Form Recognizer는 다중 서비스 구독에 포함되지 않습니다.

## <a name="retrieve-the-key-and-endpoint"></a>키 및 엔드포인트 검색

Form Recognizer 리소스의 배포가 완료되면 포털의 **모든 리소스** 목록에서 해당 리소스를 찾아 선택합니다. 키와 엔드포인트는 리소스의 **키 및 엔드포인트** 페이지에 있는 **리소스 관리** 아래에 있습니다. 앞으로 이동하기 전에 이들 둘 다 임시 위치에 저장합니다.

