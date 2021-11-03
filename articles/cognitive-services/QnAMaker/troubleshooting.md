---
title: 문제 해결 - QnA Maker
description: QnA Maker 서비스와 관련하여 가장 자주 묻는 질문의 큐레이팅된 목록은 서비스를 더 빠르게 도입하고 더 나은 결과를 얻는 데 도움이 됩니다.
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: troubleshooting
ms.date: 11/02/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: 1ce9b19e84d62959d76e7d8a3505b14a3f99e310
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131020505"
---
# <a name="troubleshooting-for-qna-maker"></a>QnA Maker의 문제 해결

QnA Maker 서비스와 관련하여 가장 자주 묻는 질문의 큐레이팅된 목록은 서비스를 더 빠르게 도입하고 더 나은 결과를 얻는 데 도움이 됩니다.

[!INCLUDE [Custom question answering](./includes/new-version.md)]

<a name="how-to-get-the-qnamaker-service-hostname"></a>

## <a name="manage-predictions"></a>예측 관리

<details>
<summary><b>쿼리 예측의 처리량 성능을 향상하려면 어떻게 해야 하나요?</b></summary>

**답변**: 처리량 성능 문제는 App Service와 Cognitive Search 모두에 맞게 스케일 업해야 함을 의미합니다. 성능을 향상시키려면 Cognitive Search에 복제본을 추가하는 것이 좋습니다.

[가격 책정 계층](Concepts/azure-resources.md)에 대해 자세히 알아보세요.
</details>

<details>
<summary><b>QnAMaker 서비스 엔드포인트를 가져오는 방법</b></summary>

**답변**: QnAMaker 서비스 엔드포인트는 QnAMaker 고객 지원팀 또는 UserVoice에 디버깅에 대해 문의할 때 유용합니다. 이 엔드포인트는 `https://your-resource-name.azurewebsites.net` 형식으로 된 URL입니다.

1. [Azure Portal](https://portal.azure.com)에서 QnAMaker 서비스(리소스 그룹)로 이동

    ![Azure Portal의 QnAMaker Azure 리소스 그룹](./media/qnamaker-how-to-troubleshoot/qnamaker-azure-resourcegroup.png)

1. QnA Maker 리소스와 연결된 App Service를 선택합니다. 일반적으로 이름은 동일합니다.

     ![QnAMaker App Service 선택](./media/qnamaker-how-to-troubleshoot/qnamaker-azure-appservice.png)

1. 엔드포인트 URL은 개요 섹션에서 확인 가능

    ![QnAMaker 엔드포인트](./media/qnamaker-how-to-troubleshoot/qnamaker-azure-gethostname.png)

</details>

## <a name="manage-the-knowledge-base"></a>기술 자료 관리

<details>
<summary><b>내 QnA Maker의 일부를 실수로 삭제했습니다. 어떻게 해야 하나요?</b></summary>

**답변**: Search 또는 Web App과 같은 QnA Maker 리소스와 함께 생성된 Azure 서비스를 삭제하지 마세요. 이는 QnA Maker가 작동하는 데 필요합니다. 서비스를 삭제하면 QnA Maker가 제대로 작동하지 않습니다.

질문 및 답변 쌍, 파일, URL, 사용자 지정 질문 및 답변, 기술 자료 또는 Azure 리소스를 포함한 모든 삭제는 영구적입니다. 기술 자료를 삭제하기 전에 **설정** 페이지에서 기술 자료를 내보냈는지 확인합니다.

</details>

<details>
<summary><b>URL/파일이 질문-답변 쌍을 추출하지 않는 이유는 무엇인가요?</b></summary>

**답변**: QnA Maker가 유효한 FAQ URL에서 일부 QnA(질문-답변) 콘텐츠를 자동으로 추출하지 못할 수 있습니다. 이러한 경우 QnA 콘텐츠를 .txt 파일에 붙여넣고 도구에서 수집할 수 있는지 확인할 수 있습니다. 또는 편집자로서 [QnA Maker 포털](https://qnamaker.ai)을 통해 기술 자료에 콘텐츠를 추가할 수 있습니다.

</details>

<details>
<summary><b>만들 수 있는 기술 자료 크기는 얼마인가요?</b></summary>

**답변**: 기술 자료 크기는 QnA Maker 서비스를 만들 때 선택한 Azure Search의 SKU에 따라 다릅니다. 자세한 내용은 [여기](./concepts/azure-resources.md)를 참조하세요.

</details>

<details>
<summary><b>새 기술 자료를 만들려고 할 때 드롭다운에 아무것도 표시될 수 없는 이유는 무엇인가요?</b></summary>

**답변**: Azure에서 QnA Maker 서비스를 아직 만들지 않았습니다. 만드는 방법에 대해 자세히 알아보려면 [여기](./How-To/set-up-qnamaker-service-azure.md)를 참조하세요.

</details>

<details>
<summary><b>기술 자료를 다른 사용자와 공유하려면 어떻게 하나요?</b></summary>

**답변**: 공유는 QnA Maker 서비스 수준에서 작동합니다. 즉, 서비스의 모든 기술 자료가 공유됩니다. 기술 자료에 대한 공동 작업 방법은 [여기](./index.yml)를 참조하세요.

</details>

<details>
<summary><b>동일한 AAD 테넌트에 있지 않은 contributor와 기술 자료를 공유하여 기술 자료를 수정할 수 있나요?</b></summary>

**답변**: 공유는 Azure 역할 기반 액세스 제어를 기반으로 합니다. 다른 사용자와 Azure의 _모든_ 리소스를 공유할 수 있는 경우 QnA Maker도 공유할 수 있습니다.

</details>

<details>
<summary><b>5개 QnAMaker 기술 자료가 포함된 App Service 요금제가 있는 경우. 5명의 각각 다른 사용자가 QnAMaker 기술 자료 1개에만 액세스할 수 있도록 해당 사용자에게 읽기/쓰기 권한을 할당할 수 있나요?</b></summary>

**답변**: 개별 기술 자료가 아닌 전체 QnAMaker 서비스를 공유할 수 있습니다.

</details>

<details>
<summary><b>양호한 일치 항목이 없는 경우 기본 메시지를 변경하려면 어떻게 하나요?</b></summary>

**답변**: 기본 메시지는 App Service 설정의 일부입니다.
- Azure Portal의 App Service 리소스로 이동

![qnamaker appservice](./media/qnamaker-faq/qnamaker-resource-list-appservice.png)
- **설정** 옵션 선택

![qnamaker appservice 설정](./media/qnamaker-faq/qnamaker-appservice-settings.png)
- **DefaultAnswer** 설정 값 변경
- App Service 다시 시작

![qnamaker appservice 다시 시작](./media/qnamaker-faq/qnamaker-appservice-restart.png)

</details>

<details>
<summary><b>SharePoint 링크가 추출되지 않는 이유는 무엇인가요?</b></summary>

**답변**: 자세한 내용은 [데이터 원본 위치](./concepts/data-sources-and-content.md#data-source-locations)를 참조하세요.

</details>

<details>
<summary><b>기술 자료에 수행한 업데이트가 게시에 반영되지 않습니다. 그 이유가 무엇인가요?</b></summary>

**답변**: 테이블 업데이트, 테스트 또는 설정이든 상관없이 모든 편집 작업은 먼저 저장해야 게시할 수 있습니다. 모든 편집 작업 후에 **저장 및 학습** 단추를 선택 해야 합니다.

</details>

<details>
<summary><b>기술 자료는 풍부한 데이터 또는 멀티미디어를 지원하나요?</b></summary>

**응답**:

#### <a name="multimedia-auto-extraction-for-files-and-urls"></a>파일 및 URL에 대한 멀티미디어 자동 추출

* URL - 제한된 HTML-마크다운 변환 기능입니다.
* 파일 - 지원되지 않음

#### <a name="answer-text-in-markdown"></a>마크다운 형식의 답변 텍스트
QnA 쌍이 기술 자료에 있으면 답변의 마크다운 텍스트를 편집하여 공용 URL에서 사용할 수 있는 미디어에 대한 링크를 포함할 수 있습니다.


</details>

<details>
<summary><b>QnA Maker에서 영어 이외의 언어를 지원하나요?</b></summary>

**답변**: [지원되는 언어](./overview/language-support.md)에 대한 자세한 내용을 참조하세요.

여러 언어의 콘텐츠가 있는 경우 각 언어마다 별도의 서비스를 만들어야 합니다.

</details>

## <a name="manage-service"></a>서비스 관리

<details>
<summary><b>내 App Service를 다시 시작해야 하는 경우는 언제인가요?</b></summary>

**답변**: **사용자 설정** [페이지](https://www.qnamaker.ai/UserSettings)의 **엔드포인트 키** 테이블에서 기술 자료에 대한 버전 값 옆에 주의 아이콘이 표시되면 App Service를 새로 고칩니다.

</details>

<details>
<summary><b>기존 Search 서비스를 삭제했습니다. 이 문제를 해결하려면 어떻게 해야 하나요?</b></summary>

**답변**: Azure Cognitive Search 인덱스를 삭제한 경우 작업이 최종이며 인덱스를 복구할 수 없습니다.

</details>

<details>
<summary><b>Search 서비스에서 `testkb` 인덱스를 삭제했습니다. 이 문제를 해결하려면 어떻게 해야 하나요?</b></summary>

**답변**: 검색 서비스에서 `testkb` 인덱스를 삭제한 경우 마지막으로 게시된 KB에서 데이터를 복원할 수 있습니다. GitHub에서 사용할 수 있는 복구 도구 [RestoreTestKBIndex](https://github.com/pchoudhari/QnAMakerBackupRestore/tree/master/RestoreTestKBFromProd)를 사용하세요. 

</details>

<details>
<summary><b>다음 오류가 발생했습니다. QnA Maker App 서비스의 CORS 설정이 https://www.qnamaker.ai 를 허용하는지 또는 조직별 네트워크 제한이 있는지 확인하세요. 이 문제를 해결하려면 어떻게 해야 하나요?</b></summary>

**답변**: App SERVICE 창의 API 섹션에서 CORS 설정을 * 또는 ""로 업데이트 https://www.qnamaker.ai 합니다. 이 문제가 해결되지 않으면 조직별 제한 사항을 확인합니다.

</details>

<details>
<summary><b>엔드포인트 키를 새로 고쳐야 하는 경우는 언제인가요?</b></summary>

**답변**: 엔드포인트 키가 손상되었다고 의심되는 경우 엔드포인트 키를 새로 고칩니다.

</details>

<details>
<summary><b>여러 언어를 사용하여 기술 자료에 대한 동일한 Azure Cognitive Search 리소스를 사용할 수 있나요?</b></summary>

**답변**: 여러 언어와 여러 기술 자료를 사용 하려면 사용자는 각 언어에 대 한 QnA Maker 리소스를 만들어야 합니다. 이렇게 하면 언어당 별도의 Azure Search 서비스를 만들게 됩니다. 단일 Azure Search 서비스에서 다른 언어로 된 기술 자료를 혼합하면 결국 성능이 저하된 결과의 관련성이 발생합니다.

</details>

<details>
<summary><b>QnA Maker에서 사용하는 Azure Cognitive Search 이름을 변경하려면 어떻게 해야 하나요?</b></summary>

**답변**: Azure Cognitive Search 리소스의 이름은 일부 임의 문자가 끝에 추가되는 QnA Maker 리소스 이름입니다. 이 이름으로는 QnA Maker의 여러 Search 리소스를 구별하기 어렵습니다. 별도의 Search 서비스(원하는 방식으로 명명)를 만들고 QnA 서비스에 연결합니다. 단계는 [Azure Search를 업그레이드](How-To/set-up-qnamaker-service-azure.md#upgrade-the-azure-cognitive-search-service)하기 위해 수행해야 하는 단계와 유사합니다.

</details>

<details>
<summary><b>QnA Maker에서 `Runtime core is not initialized,`를 반환하는 경우 어떻게 해결해야 하나요?</b></summary>

**답변**: App Service의 디스크 공간이 가득 찼을 수 있습니다. 디스크 공간을 수정하는 단계:

1. [Azure Portal](https://portal.azure.com)에서 QnA Maker의 App Service를 선택한 다음, 서비스를 중지합니다.
1. App Service에서 **개발 도구**, **고급 도구**, **이동** 을 차례로 선택합니다. 그러면 새 브라우저 창이 열립니다.
1. **디버그 콘솔**, **CMD** 를 차례로 선택 하 여 명령줄 도구를 엽니다.
1. _site/wwwroot/Data/QnAMaker/_ 디렉터리로 이동합니다.
1. 이름이 `rd`로 시작하는 모든 폴더를 제거합니다.

    다음을 **삭제하지 마세요**.

    * KbIdToRankerMappings.txt 파일
    * EndpointSettings.json 파일
    * EndpointKeys 폴더

1. App Service를 시작합니다.
1. 기술 자료에 액세스하여 지금 작동하는지 확인합니다.

</details>
<details>
<summary><b>내 Application Insights가 작동하지 않는 이유는 무엇인가요?</b></summary>

**답변**: 문제를 해결 하려면 아래 단계를 교차 확인 하 고 업데이트 하세요.

1. App Service -> 설정 그룹 -> 구성 섹션 -> 애플리케이션 설정 -> 이름 "UserAppInsightsKey" 매개 변수가 올바르게 구성되어 있고 해당 Application Insights 개요 탭("계측 키") GUID로 설정되어 있는지 확인합니다. 

1. App Service -> 설정 그룹 -> "Application Insights" 섹션에서 Application Insights가 사용하도록 설정되어 있고 해당 Application Insights 리소스에 연결되어 있는지 확인합니다.

</details>

<details>
<summary><b>내 Application Insights가 사용하도록 설정되어 있는데 제대로 작동하지 않는 이유는 무엇 가요?</b></summary>

**답변**: 아래에 명시된 단계를 따르세요. 

1.  이미 존재하는 값이 있는 경우 재정의하여 “APPINSIGHTS_INSTRUMENTATIONKEY” 이름 '의 값을 'UserAppInsightsKey' 이름에 복사합니다. 

1.  앱 설정에 'UserAppInsightsKey' 키가 없는 경우 해당 이름의 새 키를 추가하고 값을 복사하세요.

1.  이를 저장하면 App Service가 자동으로 다시 시작됩니다. 이렇게 하면 문제가 해결됩니다. 

</details>

## <a name="integrate-with-other-services-including-bots"></a>Bot을 비롯한 다른 서비스와의 통합

<details>
<summary><b>QnA Maker를 사용하기 위해 Bot Framework를 사용해야 하나요?</b></summary>

**답변**: 아니요, QnA Maker와 함께 [Bot Framework](https://github.com/Microsoft/botbuilder-dotnet)를 사용하지 않아도 됩니다. 그러나 QnA Maker는 [Azure Bot Service](/azure/bot-service/)에서 여러 템플릿 중 하나로 제공됩니다. Bot Service는 Microsoft Bot Framework를 통해 지능형 봇의 신속한 개발을 사용하도록 설정하고, 서버리스 환경에서 실행됩니다.

</details>

<details>
<summary><b>QnA Maker를 사용하여 새 봇을 만들려면 어떻게 하나요?</b></summary>

**답변**: [이](./Quickstarts/create-publish-knowledge-base.md) 문서의 지침에 따라 Azure Bot Service를 사용하여 봇을 만듭니다.

</details>

<details>
<summary><b>기존 Azure 봇 서비스에서 다른 기술 자료를 사용하려면 어떻게 해야 하나요?</b></summary>

**답변**: 기술 자료에 대한 다음 정보가 필요합니다.

* 기술 자료 ID.
* 기술 자료의 게시된 엔드포인트 사용자 지정 하위 도메인 이름으로, `host`라고 하며, 게시 후 **설정** 페이지에서 확인할 수 있습니다.
* 기술 자료의 게시된 엔드포인트 키 - 게시 후 **설정** 페이지에서 확인할 수 있습니다.

이 정보를 확인했으면 Azure Portal에서 봇의 App Service로 이동합니다. **설정 -> 구성 -> 애플리케이션 설정** 에서 해당 값을 변경합니다.

ABS 서비스에서 기술 자료의 엔드포인트 키에는 `QnAAuthkey`라는 레이블이 지정되어 있습니다.

</details>

<details>
<summary><b>두 개 이상의 클라이언트 애플리케이션에서 기술 자료를 공유할 수 있나요?</b></summary>

**답변**: 예, 클라이언트의 개수에 관계없이 기술 자료를 쿼리할 수 있습니다. 기술 자료의 응답이 느리거나 시간이 초과 된 것으로 나타나는 경우 기술 자료와 연결 된 app service에 대 한 서비스 계층을 업그레이드 하는 것이 좋습니다.

</details>

<details>
<summary><b>웹 사이트에 QnA Maker 서비스를 포함하려면 어떻게 하나요?</b></summary>

**답변**: 다음 단계를 수행하여 QnA Maker 서비스를 웹 사이트에 웹 채팅 컨트롤로 포함합니다.

1. [여기](./Quickstarts/create-publish-knowledge-base.md)에 제공된 지침을 따라 FAQ 봇을 만듭니다.
2. [여기](/azure/bot-service/bot-service-channel-connect-webchat)에 제공된 단계를 따라 웹 채팅을 사용하도록 설정합니다.

</details>

## <a name="data-storage"></a>데이터 스토리지

<details>
<summary><b>어떤 데이터가 저장되고 어디에 저장되나요?</b></summary>

**응답**:

QnA Maker 서비스를 만들 때 Azure 지역을 선택했습니다. 기술 자료 및 로그 파일에는 이 지역에 저장됩니다.

</details>
