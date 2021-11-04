---
title: QnA Maker 앱 관리-QnA Maker
description: QnA Maker를 사용하면 여러 사용자가 기술 자료를 공동으로 작업할 수 있습니다. QnA Maker는 활성 학습을 사용하여 기술 자료의 품질을 향상시키는 기능을 제공합니다. 기존 질문을 삭제하거나 변경하지 않고 검토, 수락 또는 거부 및 추가할 수 있습니다.
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 11/09/2020
ms.custom: ignite-fall-2021
ms.openlocfilehash: 973a1d100fb8182565628aab8cef66364e57a378
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131038806"
---
# <a name="manage-qna-maker-app"></a>QnA Maker 앱 관리

QnA Maker를 사용하면 협력자의 역할에 따라 협력자 액세스를 제한하는 기능을 제공하여 다양한 작성자 및 콘텐츠 편집자와 협업할 수 있습니다.
[QnA Maker 협력자 인증 개념](../Concepts/role-based-access-control.md)에 대해 자세히 알아보세요.

[!INCLUDE [Custom question answering](../includes/new-version.md)]

## <a name="add-azure-role-based-access-control-azure-rbac"></a>Azure RBAC(Azure 역할 기반 액세스 제어) 추가

QnA Maker를 사용하면 여러 사용자가 동일한 QnA Maker 리소스에 있는 모든 기술 자료에 대해 협업할 수 있습니다. 이 기능은 [Azure RBAC(Azure 역할 기반 액세스 제어)](../../../role-based-access-control/role-assignments-portal.md)를 통해 제공됩니다.

## <a name="access-at-the-cognitive-resource-level"></a>인지 리소스 수준에서 액세스

특정 기술 자료는 QnA Maker 서비스에서 공유할 수 없습니다. 보다 세부적인 액세스 제어를 원하는 경우 여러 QnA Maker 리소스에 기술 자료를 배포한 다음, 각 리소스에 역할을 추가하는 것이 좋습니다.

## <a name="add-a-role-to-a-resource"></a>리소스에 역할 추가

### <a name="add-a-user-account-to-the-cognitive-resource"></a>인지 리소스에 사용자 계정 추가

QnA Maker 리소스에 RBAC 컨트롤을 적용해야 합니다.

다음 단계에서는 협력자 역할을 사용하지만 이러한 단계를 사용하여 역할을 추가할 수 있습니다.

1. [Azure](https://portal.azure.com/) Portal에 로그인하고 인지 리소스로 이동합니다.

    ![QnA Maker 리소스 목록](../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-resource-list.png)

1. **액세스 제어(IAM)** 탭으로 이동합니다.

    ![QnA Maker IAM](../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-iam.png)

1. **추가** 를 선택합니다.

    ![QnA Maker IAM 추가](../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-iam-add.png)

1. 다음 목록에서 역할을 선택합니다.

    |역할|
    |--|
    |소유자|
    |참가자|
    |Cognitive Services QnA Maker 리더|
    |Cognitive Services QnA Maker 편집기|
    |Cognitive Services 사용자|

    :::image type="content" source="../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-add-role-iam.png" alt-text="QnA Maker IAM은 역할을 추가합니다.":::

1. 사용자의 이메일 주소를 입력하고 **저장** 을 누릅니다.

    ![QnA Maker IAM 메일 추가](../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-iam-add-email.png)

### <a name="view-qna-maker-knowledge-bases"></a>QnA Maker 기술 자료 보기

QnA Maker 서비스를 공유한 사용자가 [QnA Maker 포털](https://qnamaker.ai)에 로그인하면 역할에 따라 해당 서비스의 모든 기술 자료를 볼 수 있습니다.

기술 자료를 선택하면 해당 QnA Maker 리소스에 대한 현재 역할이 기술 자료 이름 옆에 표시됩니다.

:::image type="content" source="../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-knowledge-base-role-name.png" alt-text="웹 페이지의 왼쪽 위 모서리에 있는 기술 자료 이름 옆에 괄호 안에 역할 이름이 있는 편집 모드의 기술 자료 스크린샷.":::

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [기술 자료 만들기](./manage-knowledge-bases.md)
