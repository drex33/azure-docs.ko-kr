---
title: '자습서: Azure Bot Service로 FAQ 봇 만들기'
description: 이 자습서에서는 QnA Maker 및 Azure Bot Service를 사용하여 코드 없이 FAQ 봇을 만듭니다.
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: tutorial
ms.date: 08/31/2020
ms.custom: ignite-fall-2021
ms.openlocfilehash: 1d0e48da09cccbed74d3a4d8bfc0e89907a336a3
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131020486"
---
# <a name="tutorial-create-a-faq-bot-with-azure-bot-service"></a>자습서: Azure Bot Service를 사용하여 FAQ 봇 만들기
QnA Maker 및 Azure [Bot Service](https://azure.microsoft.com/services/bot-service/)를 사용하여 코드 없이 FAQ 봇을 만듭니다.

이 자습서에서는 다음 작업 방법을 알아봅니다.

<!-- green checkmark -->
> [!div class="checklist"]
> * QnA Maker 기술 자료를 Azure Bot Service에 연결
> * 봇 배포
> * 웹 채팅에서 봇과 채팅
> * 지원 채널에서 봇 강화

## <a name="create-and-publish-a-knowledge-base"></a>기술 자료 만들기 및 게시

[빠른 시작](../Quickstarts/create-publish-knowledge-base.md)에 따라 기술 자료를 만듭니다. 기술 자료가 성공적으로 게시되 면 아래 페이지에 연결됩니다.

> [!div class="mx-imgBorder"]
> ![성공적인 게시의 스크린샷](../media/qnamaker-create-publish-knowledge-base/publish-knowledge-base-to-endpoint.png)


## <a name="create-a-bot"></a>봇 만들기

게시 후에는 **게시** 페이지에서 봇을 만들 수 있습니다.

* 개별 봇의 여러 Azure 지역 또는 가격 책정 계획에 대한 동일한 기술 자료를 가리키는 여러 봇을 신속하게 만들 수 있습니다.
* 기술 자료에 봇을 하나만 사용하려면 **Azure Portal에서 모든 봇 보기** 링크를 사용하여 현재 봇 목록을 봅니다.

기술 자료를 변경하고 다시 게시하는 경우 봇에 다른 조치를 취할 필요가 없습니다. 이미 기술 자료와 함께 작동하도록 구성되어 있으며 향후 모든 기술 자료 변경 내용과 호환됩니다. 기술 자료를 게시할 때마다 기술 자료에 연결된 모든 봇이 자동으로 업데이트됩니다.

1. QnA Maker 포털의 **게시** 페이지에서 **봇 만들기** 를 선택합니다. 이 단추는 기술 자료를 게시한 후에만 표시됩니다.

    > [!div class="mx-imgBorder"]
    > ![봇 만들기 스크린샷](../media/qnamaker-create-publish-knowledge-base/create-bot-from-published-knowledge-base-page.png)

1. 새 브라우저 탭에서 Azure Portal의 Azure Bot Service 만들기 페이지가 열립니다. Azure Bot Service를 구성합니다. 봇과 QnA Maker는 웹앱 서비스 플랜을 공유할 수 있지만, 웹앱을 공유할 수는 없습니다. 즉, 봇의 **앱 이름** 이 QnA Maker 서비스의 앱 이름과 달라야 합니다.

    * **해야 할 일**
        * 고유하지 않은 경우 봇 핸들을 변경합니다.
        * SDK 언어를 선택합니다. 봇이 만들어지면 로컬 개발 환경에 코드를 다운로드하고 개발 프로세스를 계속할 수 있습니다.
    * **안 함**
        * 봇을 만들 때 Azure Portal에서 다음 설정을 변경하지 마세요. 기존 기술 자료에 대한 내용이 미리 채워집니다.
           * QnA 인증 기
           * 앱 서비스 플랜 및 위치


1. 봇을 만든 후 **봇 서비스** 리소스를 엽니다.
1. **봇 관리** 에서 **웹 채팅에서 테스트** 를 선택합니다.
1. **메시지 입력** 의 채팅 프롬프트에서 다음을 입력합니다.

    `Azure services?`

    채팅 봇은 기술 자료의 답변으로 응답합니다.

    > [!div class="mx-imgBorder"]
    > ![응답을 반환하는 봇의 스크린샷](../media/qnamaker-create-publish-knowledge-base/test-web-chat.png)


1. 추가 [지원 채널](/azure/bot-service/bot-service-manage-channels)에서 봇을 강화합니다.
    
## <a name="integrate-the-bot-with-channels"></a>봇을 채널과 통합

사용자가 만든 봇 서비스 리소스에서 **채널** 을 클릭합니다. 추가 [지원 채널](/azure/bot-service/bot-service-manage-channels)에서 봇을 강화할 수 있습니다.

   >[!div class="mx-imgBorder"]
   >![팀과의 통합 스크린샷](../media/qnamaker-tutorial-updates/connect-with-teams.png)
