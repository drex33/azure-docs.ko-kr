---
title: Azure Portal을 사용하여 API 편집 | Microsoft Docs
description: APIM(API Management)을 사용하여 API를 편집하는 방법에 대해 알아봅니다. APIM 인스턴스에서 작업을 추가, 삭제 또는 이름을 변경하거나 API의 swagger를 편집합니다.
services: api-management
documentationcenter: ''
author: dlepow
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 11/08/2017
ms.author: danlep
ms.openlocfilehash: db8ceb0921d7ed18cfaa7651d208d5f7e899a6e7
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128654445"
---
# <a name="edit-an-api"></a>API 편집

이 자습서의 단계에서는 APIM(API Management)을 사용하여 API를 편집하는 방법을 보여 줍니다. 

+ APIM 인스턴스에서 작업을 추가, 삭제, 이름을 변경하여 수행할 수 있습니다. 
+ API의 swagger를 편집할 수 있습니다.

## <a name="prerequisites"></a>필수 구성 요소

+ [Azure API Management 인스턴스 만들기](get-started-create-service-instance.md)
+ [첫 번째 API 가져오기 및 게시](import-and-publish.md)

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-navigate-to-instance.md)]

## <a name="edit-an-api-in-apim"></a>APIM에서 API 편집

![APIM에서 API를 편집하는 프로세스를 강조 표시하는 스크린샷.](./media/edit-api/edit-api001.png)

1. **API** 탭을 클릭합니다.
2. 이전에 가져온 API 중 하나를 선택합니다.
3. **디자인** 탭을 선택합니다.
4. 편집하려는 하는 작업을 선택합니다.
5. 작업의 이름을 바꾸려면 **프런트 엔드** 창에서 **연필** 을 선택합니다.

## <a name="update-the-swagger"></a>swagger 업데이트

다음 단계를 수행하여 Azure Portal에서 백 엔드 API를 업데이트할 수 있습니다.

1. **모든 작업** 을 선택합니다.
2. **프런트 엔드** 창에서 연필을 클릭합니다.

    ![프런트 엔드 화면에서 연필 아이콘을 강조 표시하는 스크린샷.](./media/edit-api/edit-api002.png)

    API의 swagger가 나타납니다.

    ![api 편집](./media/edit-api/edit-api003.png)

3. swagger를 업데이트합니다.
4. **저장** 을 누릅니다.

[!INCLUDE [api-management-define-api-topics.md](../../includes/api-management-define-api-topics.md)]

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [APIM 정책 샘플](./policy-reference.md)
> [게시된 API 변환 및 보호](transform-api.md)