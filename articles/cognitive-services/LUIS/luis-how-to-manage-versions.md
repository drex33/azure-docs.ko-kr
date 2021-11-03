---
title: 버전 관리 - LUIS
titleSuffix: Azure Cognitive Services
description: 버전을 통해 다양한 모델을 빌드하고 게시할 수 있습니다. 모델에 변경 사항을 적용하기 전에 현재 활성 모델을 앱의 다른 버전에 을 복제하는 것이 좋습니다.
services: cognitive-services
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: how-to
ms.date: 10/25/2021
ms.openlocfilehash: a6d1b95f99c56430d10403c74679114a72b19ce0
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131062210"
---
# <a name="use-versions-to-edit-and-test-without-impacting-staging-or-production-apps"></a>스테이징 또는 프로덕션 앱에 영향을 주지 않고 버전을 사용하여 편집 및 테스트

버전을 통해 다양한 모델을 빌드하고 게시할 수 있습니다. 모델에 변경 사항을 적용하기 전에 현재 활성 모델을 앱의 다른 [버전](./luis-concept-app-iteration.md)에 복제하는 것이 좋습니다.

활성 버전은 LUIS 포털 **빌드** 섹션에서 의도, 엔터티, 기능 및 패턴으로 편집하는 버전입니다. 작성 API를 사용하는 경우 버전 관련 REST API 호출의 경로에 버전이 포함되므로 활성 버전을 설정할 필요가 없습니다.

버전을 사용하려면 **내 앱** 페이지에서 해당 이름을 선택하여 앱을 연 다음, 위쪽 표시줄에서 **관리** 를 선택하고 왼쪽 탐색에서 **버전** 을 선택합니다.

버전 목록은 게시된 버전, 게시 위치 및 현재 활성 버전을 보여 줍니다.

## <a name="clone-a-version"></a>버전 복제

1. 복제할 버전을 선택한 다음, 도구 모음에서 **복제** 를 선택합니다.

2. **복제 버전** 대화 상자에서 “0.2”와 같은 새 버전에 사용할 이름을 입력합니다.

   ![버전 복제 대화 상자](./media/luis-how-to-manage-versions/version-clone-version-dialog.png)

     > [!NOTE]
     > 버전 ID는 문자, 숫자 또는 ‘.’으로만 구성할 수 있으며 10자 이하여야 합니다.

   지정한 이름으로 새 버전이 생성되고 활성 버전으로 설정됩니다.

## <a name="set-active-version"></a>활성 버전 설정

목록에서 버전을 선택한 다음, 도구 모음에서 **활성화** 를 선택합니다.

## <a name="import-version"></a>버전 가져오기

애플리케이션의 `.json` 또는 `.lu` 버전을 가져올 수 있습니다.

1. 도구 모음에서 **가져오기** 를 선택하고 형식을 선택합니다.

2. **새 버전 가져오기** 팝업 창에서 10자의 새 버전 이름을 입력합니다. 파일에 있는 버전이 이미 앱에 있는 경우 버전 ID를 설정하기만 하면 됩니다.

    ![관리 섹션, 버전 페이지, 새 버전 가져오기](./media/luis-how-to-manage-versions/versions-import-pop-up.png)

    버전을 가져오면 새 버전이 활성 버전이 됩니다.

### <a name="import-errors"></a>가져오기 오류

* 토크나이저 오류: 가져올 때 **토크나이저 오류** 가 표시되면 현재 앱에서 사용하는 것과 다른 [토크나이저](luis-language-support.md#custom-tokenizer-versions)를 사용하는 버전을 가져오려고 하는 것입니다. 이 문제를 해결하려면 [토크나이저 버전 간 마이그레이션](luis-language-support.md#migrating-between-tokenizer-versions)을 참조하세요.

<a name = "export-version"></a>

## <a name="other-actions"></a>다른 작업

* 버전을 **삭제** 하려면 목록에서 버전을 선택한 다음, 도구 모음에서 **삭제** 를 선택합니다. **확인** 을 선택합니다.
* 버전의 **이름을 바꾸려면** 목록에서 버전을 선택한 다음, 도구 모음에서 **이름 바꾸기** 를 선택합니다. 새 이름을 입력하고 **완료** 를 선택합니다.
* 버전을 **내보내려면** 목록에서 버전을 선택한 다음, 도구 모음에서 **앱 내보내기** 를 선택합니다. 백업을 위해 내보내거나 소스 제어에 저장하려면 JSON 또는 LU를 선택하고 [LUIS 컨테이너에서 이 앱을 사용](luis-container-howto.md)하려면 **컨테이너 내보내기** 를 선택합니다.

## <a name="see-also"></a>참고 항목

애플리케이션 가져오기 및 내보내기를 위한 REST API를 보려면 다음 링크를 참조하세요.

* [애플리케이션 가져오기](https://westus.dev.cognitive.microsoft.com/docs/services/luis-programmatic-apis-v3-0-preview/operations/5892283039e2bb0d9c2805f5)
* [애플리케이션 내보내기](https://westus.dev.cognitive.microsoft.com/docs/services/luis-programmatic-apis-v3-0-preview/operations/5890b47c39e2bb052c5b9c40)