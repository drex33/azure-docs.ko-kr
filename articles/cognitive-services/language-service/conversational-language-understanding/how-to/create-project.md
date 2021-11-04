---
title: 대화형 Language Understanding에서 프로젝트를 만드는 방법
titleSuffix: Azure Cognitive Services
description: 이 문서를 사용하여 대화형 Language Understanding에서 프로젝트를 만드는 방법을 알아봅니다.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: overview
ms.date: 11/02/2021
ms.author: aahi
ms.custom: language-service-clu, ignite-fall-2021
ms.openlocfilehash: c837912ea60b0caf91e2d04382af343bd4b6a96a
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131101357"
---
# <a name="how-to-create-projects-in-conversational-language-understanding"></a>대화형 Language Understanding에서 프로젝트를 만드는 방법

대화형 Language Understanding를 사용하면 **대화** 및 **오케스트레이션 워크플로** 프로젝트라는 두 가지 종류의 프로젝트를 만들 수 있습니다.

## <a name="sign-in-to-language-studio"></a>Language Studio에 로그인
시작하려면 먼저 [Language Studio](https://aka.ms/languageStudio)에 로그인하고 언어 리소스를 만들어야 합니다. 설치가 완료되면 **완료** 를 선택합니다.

## <a name="navigate-to-conversational-language-understanding"></a>대화형 Language Understanding으로 이동

언어 스튜디오에서 **대화형 언어 이해** 섹션을 찾아 **대화형 언어 이해** 를 선택합니다.

대화형 Language Understanding 프로젝트 페이지가 표시됩니다.

:::image type="content" source="../media/projects-page.png" alt-text="대화형 Language Understanding 프로젝트 페이지를 보여주는 스크린샷." lightbox="../media/projects-page.png":::

## <a name="create-a-conversation-project"></a>대화 프로젝트 만들기
대화를 선택한 후에는 다음 세부 정보를 제공해야 합니다.
- 이름: 프로젝트 이름
- 설명 - 선택적 프로젝트 설명
- 텍스트 주 언어: 프로젝트의 주 언어입니다. 학습 데이터는 주로 이 언어로 되어 있어야 합니다.
- 여러 언어 사용: 한 번에 여러 언어를 지원하도록 프로젝트를 설정할지 여부를 지정합니다.

:::image type="content" source="../media/clu-project-modal.png" alt-text="대화형 Language Understanding 대화 프로젝트 생성 창을 보여주는 스크린샷." lightbox="../media/clu-project-modal.png":::

작업이 완료되면 다음을 클릭하고 세부 정보를 검토한 다음, 프로젝트 만들기를 클릭하여 프로세스를 완료합니다. 

## <a name="create-an-orchestration-workflow-project"></a>오케스트레이션 워크플로 프로젝트 만들기

오케스트레이션을 선택한 후에는 다음 세부 정보를 제공해야 합니다.
- 이름: 프로젝트 이름
- 설명 - 선택적 프로젝트 설명
- 텍스트 주 언어: 프로젝트의 주 언어입니다. 학습 데이터는 주로 이 언어로 되어 있어야 합니다.
- 여러 언어 사용: 한 번에 여러 언어를 지원하도록 프로젝트를 설정할지 여부를 지정합니다.

작업이 완료되면 이제 오케스트레이션하려는 다른 프로젝트 및 서비스에 연결하는 옵션을 사용할 수 있습니다. 각 연결은 해당 형식 및 관련 데이터로 표시됩니다. 의도에는 **이름**, **프로젝트 형식**(LUIS, 사용자 지정 질문 응답(QnA) 또는 대화형 Language Understanding)이 있어야 하고 이름으로 연결하려는 프로젝트를 선택해야 합니다. 

> [!NOTE]
> 연결할 수 있는 프로젝트 목록에는 오케스트레이션 프로젝트를 만드는 데 사용하는 것과 동일한 언어 리소스에서 소유하고 있는 프로젝트만 있습니다.

이 단계는 선택 사항이며 프로젝트를 만든 후에도 의도 연결을 추가하는 옵션이 있습니다.

:::image type="content" source="../media/orchestration-project-detail.png" alt-text="대화형 Language Understanding 오케스트레이션 워크플로 프로젝트 모달을 보여주는 스크린샷." lightbox="../media/orchestration-project-detail.png":::

## <a name="import-a-project"></a>프로젝트 가져오기

대화 프로젝트 페이지로 이동하여 프로젝트를 선택하고 **내보내기** 를 눌러 언제든지 대화형 Language Understanding 프로젝트를 JSON 파일로 내보낼 수 있습니다.
해당 프로젝트를 새 프로젝트로 다시 가져올 수 있습니다. 동일한 이름을 가진 프로젝트를 가져오는 경우 프로젝트의 데이터를 새로 가져온 프로젝트의 데이터로 바꿉니다.

:::image type="content" source="../media/export.png" alt-text="대화형 Language Understanding 내보내기 단추를 보여주는 스크린샷." lightbox="../media/export.png":::

기존 LUIS 애플리케이션을 사용하는 경우 LUIS 애플리케이션 JSON을 대화형 Language Understanding으로 직접 _가져올_ 수 있으며, 현재 사용 가능한 모든 부분(의도, ML 엔터티 및 발언)이 포함된 대화 프로젝트가 생성됩니다. 자세한 내용은 [LUIS와 이전 버전과의 호환성](../concepts/backwards-compatibility.md)을 참조하세요.

**새 프로젝트 만들기** 옆에 있는 화살표 단추를 클릭하고 **가져오기** 를 선택한 다음, LUIS 또는 대화형 Language Understanding JSON 파일을 선택합니다.

:::image type="content" source="../media/import.png" alt-text="대화형 Language Understanding 가져오기 단추를 보여주는 스크린샷." lightbox="../media/import.png":::

## <a name="next-steps"></a>다음 단계

[스키마 빌드](./build-schema.md)
