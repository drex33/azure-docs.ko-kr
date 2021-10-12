---
title: Azure Logic Apps와 통합하는 함수 만들기
description: Azure Logic Apps 및 Azure Cognitive Services와 통합되는 함수를 만듭니다. 결과 워크플로는 트윗 감정을 분류하여 이메일 알림을 보냅니다.
author: craigshoemaker
ms.assetid: 60495cc5-1638-4bf0-8174-52786d227734
ms.topic: tutorial
ms.date: 04/10/2021
ms.author: cshoe
ms.custom: devx-track-csharp, mvc, cc996988-fb4f-47
ms.openlocfilehash: 0ff31245f739085ac2c8c1381df93a52e837d3a1
ms.sourcegitcommit: 1f29603291b885dc2812ef45aed026fbf9dedba0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/29/2021
ms.locfileid: "129232112"
---
# <a name="tutorial-create-a-function-to-integrate-with-azure-logic-apps"></a>자습서: Azure Logic Apps와 통합하는 함수 만들기

Azure Functions는 논리 앱 디자이너에서 Azure Logic Apps와 통합합니다. 이 통합을 통해 다른 Azure 및 타사 서비스와 함께 오케스트레이션에서 함수의 컴퓨팅 기능을 사용할 수 있습니다.

이 자습서에서는 Twitter 활동을 분석하는 워크플로를 만드는 방법을 보여줍니다. 트윗을 평가할 때 긍정 감정이 검색되면 워크플로에서 알림을 보냅니다.

이 자습서에서는 다음에 대해 알아봅니다.

> [!div class="checklist"]
> * Cognitive Services API 리소스를 만듭니다.
> * 트윗 감정을 분류하는 함수를 만듭니다.
> * Twitter에 연결하는 논리 앱을 만듭니다.
> * 논리 앱에 감정 검색을 추가합니다.
> * 함수에 논리 앱을 연결합니다.
> * 함수의 응답에 따라 전자 메일을 보냅니다.

## <a name="prerequisites"></a>사전 요구 사항

* 활성 [Twitter](https://twitter.com/) 계정.
* [Outlook.com](https://outlook.com/) 계정(알림 전송용).

> [!NOTE]
> Gmail 커넥터를 사용하려는 경우 G Suite 비즈니스 계정만 논리 앱에서 제한 없이 이 커넥터를 사용할 수 있습니다. Gmail 소비자 계정이 있는 경우 특정 Google 승인 앱과 서비스에서만 Gmail 커넥터를 사용하거나 [Gmail 커넥터에서 인증에 사용할 Google 클라이언트 앱을 만들](/connectors/gmail/#authentication-and-bring-your-own-application) 수 있습니다. <br><br>자세한 내용은 [Azure Logic Apps의 Google 커넥터에 대한 데이터 보안 및 개인정보처리방침](../connectors/connectors-google-data-security-privacy-policy.md)을 참조하세요.

## <a name="create-text-analytics-resource"></a>Text Analytics 리소스 만들기

Cognitive Services API는 Azure에서 개별 리소스로 사용할 수 있습니다. Text Analytics API를 사용하여 게시된 트윗의 감정을 검색합니다.

1. [Azure Portal](https://portal.azure.com/)에 로그인합니다.

1. Azure Portal의 왼쪽 위 모서리에서 **리소스 만들기** 를 선택합니다.

1. _범주_ 에서 **AI + Machine Learning** 을 선택합니다.

1. _Text Analytics_ 에서 **만들기** 를 선택합니다.

1. _Text Analytics 만들기_ 화면에서 다음 값을 입력합니다.

    | 설정 | 값 | 설명 |
    | ------- | ----- | ------- |
    | 구독 | Azure 구독 이름 | |
    | 리소스 그룹 | **tweet-sentiment-tutorial** 이라는 새 리소스 그룹을 만듭니다. | 나중에 이 리소스 그룹을 삭제하여 이 자습서에서 만든 모든 리소스를 제거합니다. |
    | 지역 | 가장 가까운 지역을 선택합니다. | |
    | 속성 | **TweetSentimentApp** | |
    | 가격 책정 계층 | **무료 F0** 을 선택합니다. | |

1. **검토 + 만들기** 를 선택합니다.

1. **만들기** 를 선택합니다.

1. 배포가 완료되면 **리소스로 이동** 을 선택합니다.

## <a name="get-text-analytics-settings"></a>Text Analytics 설정 가져오기

Text Analytics 리소스를 만든 경우 몇 가지 설정을 복사하고 나중에 사용하기 위해 따로 보관합니다.

1. **키 및 엔드포인트** 를 선택합니다.

1. 입력 상자 끝에 있는 아이콘을 클릭하여 **키 1** 을 복사합니다.

1. 값을 텍스트 편집기에 붙여넣습니다.

1. 입력 상자 끝에 있는 아이콘을 클릭하여 **엔드포인트** 를 복사합니다.

1. 값을 텍스트 편집기에 붙여넣습니다.

## <a name="create-the-function-app"></a>함수 앱 만들기

1. 위쪽 검색 상자에서 **함수 앱** 을 검색하고 선택합니다.

1. **만들기** 를 선택합니다.

1. 다음 값을 입력합니다.

    | 설정 | 제안 값 | 설명 |
    | ------- | ----- | ------- |
    | 구독 | Azure 구독 이름 | |
    | 리소스 그룹 | **tweet-sentiment-tutorial** | 이 자습서 전체에서 동일한 리소스 그룹 이름을 사용합니다. |
    | 함수 앱 이름 | **TweetSentimentAPI** + 고유 접미사 | 함수 애플리케이션 이름은 전체적으로 고유합니다. 유효한 문자는 `a-z`(대/소문자 구분 안 함), `0-9` 및 `-`입니다. |
    | 게시 | **코드** | |
    | 런타임 스택 | **.NET** | 제공되는 함수 코드는 C#으로 되어 있습니다. |
    | 버전 | 최신 버전을 선택합니다. | |
    | 지역 | 가장 가까운 지역을 선택합니다. | |

1. **검토 + 만들기** 를 선택합니다.

1. **만들기** 를 선택합니다.

1. 배포가 완료되면 **리소스로 이동** 을 선택합니다.

## <a name="create-an-http-triggered-function"></a>HTTP 트리거 함수 만들기  

1. _함수_ 창의 왼쪽 메뉴에서 **함수** 를 선택합니다.

1. 위쪽 메뉴에서 **추가** 를 선택하고 다음 값을 입력합니다.

    | 설정 | 값 | 설명 |
    | ------- | ----- | ------- |
    | 개발 환경 | **포털에서 개발** | |
    | 템플릿 | **HTTP 트리거** | |
    | 새 함수 | **TweetSentimentFunction** | 함수의 이름입니다. |
    | 권한 부여 수준 | **함수** | |

1. **추가** 단추를 선택합니다.

1. **코드 + 테스트** 단추를 선택합니다.

1. 다음 코드를 코드 편집기 창에 붙여넣습니다.

    ```csharp
    #r "Newtonsoft.Json"
    
    using System;
    using System.Net;
    using Microsoft.AspNetCore.Mvc;
    using Microsoft.Extensions.Logging;
    using Microsoft.Extensions.Primitives;
    using Newtonsoft.Json;
    
    public static async Task<IActionResult> Run(HttpRequest req, ILogger log)
    {
    
        string requestBody = String.Empty;
        using (StreamReader streamReader =  new  StreamReader(req.Body))
        {
            requestBody = await streamReader.ReadToEndAsync();
        }
    
        dynamic score = JsonConvert.DeserializeObject(requestBody);
        string value = "Positive";
    
        if(score < .3)
        {
            value = "Negative";
        }
        else if (score < .6) 
        {
            value = "Neutral";
        }
    
        return requestBody != null
            ? (ActionResult)new OkObjectResult(value)
           : new BadRequestObjectResult("Pass a sentiment score in the request body.");
    }
    ```

    감정 점수가 값에 대한 범주 이름을 반환하는 함수로 전달됩니다.

1. 도구 모음에서 **저장** 단추를 선택하여 변경 내용을 저장합니다.

    > [!NOTE]
    > 함수를 테스트하려면 위쪽 메뉴에서 **테스트/실행** 을 선택합니다. _입력_ 탭에서 _본문_ 입력 상자에 `0.9` 값을 입력한 다음, **실행** 을 선택합니다. _출력_ 섹션의 _HTTP 응답 콘텐츠_ 상자에 _양수_ 값이 반환되는지 확인합니다.

다음으로 Azure Functions,Twitter 및 Cognitive Services API와 통합하는 논리 앱을 만듭니다.

## <a name="create-a-logic-app"></a>논리 앱 만들기

1. 위쪽 검색 상자에서 **논리 앱** 을 검색하고 선택합니다.

1. **추가** 를 선택합니다.

1. **사용** 을 선택하고 다음 값을 입력합니다.

    | 설정 | 제안 값 |
    | ------- | --------------- |
    | 구독 | Azure 구독 이름 |
    | 리소스 그룹 | **tweet-sentiment-tutorial** |
    | 논리 앱 이름. | **TweetSentimentApp** |
    | 지역 | 가장 가까운 지역, 가급적 이전 단계에서 선택한 동일한 지역을 선택합니다. |

    다른 모든 설정에 대한 기본값을 적용합니다.

1. **검토 + 만들기** 를 선택합니다.

1. **만들기** 를 선택합니다.

1. 배포가 완료되면 **리소스로 이동** 을 선택합니다.

1. **빈 논리 앱** 단추를 선택합니다.

    :::image type="content" source="media/functions-twitter-email/blank-logic-app-button.png" alt-text="빈 논리 앱 단추":::

1. 도구 모음에서 **저장** 단추를 선택하여 진행 상황을 저장합니다.

이제 Logic Apps 디자이너를 사용하여 App Service 및 트리거를 애플리케이션에 추가할 수 있습니다.

## <a name="connect-to-twitter"></a>Twitter에 연결

앱이 새 트윗을 폴링할 수 있도록 Twitter에 대한 연결을 만듭니다.

1. 위쪽 검색 상자에서 **Twitter** 를 검색합니다.

1. **Twitter** 아이콘을 선택합니다.

1. **새 트윗이 게시될 때** 트리거를 선택합니다.

1. 다음 값을 입력하여 연결을 설정합니다.

    | 설정 |  값 |
    | ------- | ---------------- |
    | 연결 이름 | **MyTwitterConnection** |
    | 인증 유형 | **기본 공유 애플리케이션 사용** |

1. **로그인** 을 선택합니다.

1. 팝업 창에 표시되는 메시지에 따라 Twitter에 로그인을 완료합니다.

1. 그런 다음, _새 트윗이 게시될 때_ 상자에 다음 값을 입력합니다.

    | 설정 | 값 |
    | ------- | ----- |
    | 검색 텍스트 | **#my-twitter-tutorial** |
    | 얼마나 자주 항목을 확인하시겠습니까? | 텍스트 상자에서 **1** 및 <br> 드롭다운에서 **시간**. 다른 값을 입력할 수 있지만 Twitter 커넥터의 현재 [제한 사항](/connectors/twitterconnector/#limits)을 검토해야 합니다.  |

1. 도구 모음에서 **저장** 단추를 선택하여 진행 상황을 저장합니다.

다음으로 텍스트 분석에 연결하여 수집된 트윗의 감정을 검색합니다.

## <a name="add-text-analytics-sentiment-detection"></a>Text Analytics 감정 검색 추가

1. **새 단계** 를 선택합니다.

1. 검색 상자에서 **Text Analytics** 를 검색합니다.

1. **Text Analytics** 아이콘을 선택합니다.

1. **감정 검색** 을 선택하고 다음 값을 입력합니다.

    | 설정 | 값 |
    | ------- | ----- |
    | 연결 이름 | **TextAnalyticsConnection** |
    | 계정 키 | 이전에 따로 설정한 Text Analytics 계정 키에 붙여넣습니다. |
    | 사이트 URL | 이전에 따로 설정한 Text Analytics 엔드포인트에 붙여넣습니다. |

1. **만들기** 를 선택합니다.

1. _새 매개 변수 추가_ 상자 내부를 클릭하고 팝업에 표시되는 **문서** 옆의 확인란을 선택합니다.

1. _문서 Id - 1_ 텍스트 상자 내부를 클릭하여 동적 콘텐츠 팝업을 엽니다.

1. _동적 콘텐츠_ 검색 상자에서 **id** 를 검색하고 **트윗 id** 를 클릭합니다.

1. _문서 텍스트 - 1_ 텍스트 상자 내부를 클릭하여 동적 콘텐츠 팝업을 엽니다.

1. _동적 콘텐츠_ 검색 상자에서 **텍스트** 를 검색하고 **트윗 텍스트** 를 클릭합니다.

1. **작업 선택** 에서 **Text Analytics** 를 입력한 다음, **감정 검색** 작업을 클릭합니다.

1. 도구 모음에서 **저장** 단추를 선택하여 진행 상황을 저장합니다.

_감정 검색_ 상자는 다음 스크린샷처럼 나타납니다.

:::image type="content" source="media/functions-twitter-email/detect-sentiment.png" alt-text="감정 설정 검색":::

## <a name="connect-sentiment-output-to-function-endpoint"></a>함수 엔드포인트에 감정 출력 연결

1. **새 단계** 를 선택합니다.

1. 검색 상자에서 **Azure Functions** 를 검색합니다.

1. **Azure Functions** 아이콘을 선택합니다.

1. 검색 상자에서 함수 이름을 검색합니다. 위의 지침을 따랐다면 함수 이름은 **TweetSentimentAPI** 로 시작합니다.

1. 함수 아이콘을 선택합니다.

1. **TweetSentimentFunction** 항목을 선택합니다.

1. _요청 본문_ 상자 내부를 클릭하고 팝업 창에서 _감정 검색_ **점수** 항목을 선택합니다.

1. 도구 모음에서 **저장** 단추를 선택하여 진행 상황을 저장합니다.

## <a name="add-conditional-step"></a>조건부 단계 추가

1. **작업 추가** 단추를 선택합니다.

1. _컨트롤_ 상자 내부를 클릭하고 팝업 창에서 **컨트롤** 을 검색하여 선택합니다.

1. **조건** 을 선택합니다.

1. _값 선택_ 상자 내부를 클릭하고 팝업 창에서 _TweetSentimentFunction_ **본문** 항목을 선택합니다.

1. _값 선택_ 상자에 **양수** 를 입력합니다.

1. 도구 모음에서 **저장** 단추를 선택하여 진행 상황을 저장합니다.

## <a name="add-email-notifications"></a>이메일 알림 추가

1. _True_ 상자 아래에서 **작업 추가** 단추를 선택합니다.

1. 텍스트 상자에서 **Office 365 Outlook** 을 검색하고 선택합니다.

1. **보내기** 를 검색하고 텍스트 상자에서 **이메일 보내기** 를 선택합니다.

1. **로그인** 단추를 선택합니다.

1. 팝업 창에 표시되는 메시지에 따라 Office 365 Outlook에 로그인을 완료합니다.

1. _받는 사람_ 상자에 이메일 주소를 입력합니다.

1. _제목_ 상자 내부를 클릭하고 _TweetSentimentFunction_ 에서 **본문** 항목을 클릭합니다. _본문_ 항목이 목록에 표시되지 않으면 **자세히 보기** 링크를 클릭하여 옵션 목록을 확장합니다.

1. _제목_ 에서 _본문_ 항목 뒤에 **트윗 보낸 사람:** 텍스트를 입력합니다.

1. _트윗 보낸 사람:_ 텍스트를 입력한 후 상자를 다시 클릭하고 _새 트윗이 게시될 때_ 옵션 목록에서 **사용자 이름** 을 선택합니다.

1. _본문_ 상자 내부를 클릭하고 _새 트윗이 게시될 때_ 옵션 목록에서 **트윗 텍스트** 를 선택합니다. _트윗 텍스트_ 항목이 목록에 표시되지 않으면 **자세히 보기** 링크를 클릭하여 옵션 목록을 확장합니다.

1. 도구 모음에서 **저장** 단추를 선택하여 진행 상황을 저장합니다.

이제 이메일 상자는 이 스크린샷처럼 보입니다.

:::image type="content" source="media/functions-twitter-email/email-notification.png" alt-text="이메일 알림":::

## <a name="run-the-workflow"></a>워크플로 실행

1. Twitter 계정에서 **I'm enjoy # my-twitter-tutorial**  텍스트를 트윗합니다.

1. Logic Apps 디자이너로 돌아가서 **실행** 단추를 선택합니다.

1. 워크플로의 메시지가 있는지 이메일을 확인하세요.

## <a name="clean-up-resources"></a>리소스 정리

이 자습서에서 만든 모든 Azure 서비스 및 계정을 정리하려면 리소스 그룹을 삭제합니다.

1. 위쪽 검색 상자에서 **리소스 그룹** 을 검색합니다.

1. **tweet-sentiment-tutorial** 을 선택합니다.

1. **리소스 그룹 삭제** 를 선택합니다.

1. 텍스트 상자에 **tweet-sentiment-tutorial** 을 입력합니다.

1. **삭제** 단추를 선택합니다.

또는 Twitter 계정으로 돌아가서 피드에서 테스트 트윗을 삭제할 수도 있습니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [Azure Functions를 사용하여 서버 없는 API 만들기](functions-create-serverless-api.md)
