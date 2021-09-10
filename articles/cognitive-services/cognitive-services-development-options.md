---
title: Azure Cognitive Services 개발 옵션
description: 클라이언트 라이브러리, REST API, Logic Apps, Power Automate, Azure Functions, Azure App Service, Azure Databricks 등과 같은 다양한 개발 및 배포 옵션과 함께 Azure Cognitive Services를 사용하는 방법을 알아봅니다.
services: cognitive-services
manager: nitinme
author: erhopf
ms.author: erhopf
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 10/22/2020
ms.openlocfilehash: 8c40af42248e41346d10ec0028c1f13ff7ef22d6
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/22/2021
ms.locfileid: "114457950"
---
# <a name="cognitive-services-development-options"></a>Cognitive Services 개발 옵션

이 문서에서는 Azure Cognitive Services를 시작하는 데 도움이 되는 개발 및 배포 옵션에 대한 대략적인 개요를 제공합니다.

Azure Cognitive Services는 개발자가 기계 학습에 대한 깊은 지식 없이도 애플리케이션과 제품에 인텔리전스를 구축할 수 있도록 지원하는 클라우드 기반 AI 서비스입니다. Cognitive Services를 사용하면 애플리케이션에서 바로 사용할 수 있도록 Microsoft에서 구축, 학습 및 업데이트한 AI 기능 또는 모델에 액세스할 수 있습니다. 대부분의 경우 비즈니스 요구에 맞게 모델을 사용자 지정할 수 있는 옵션도 있습니다. 

Cognitive Services는 의사 결정, 언어, 음성 및 비전의 네 가지 범주로 구성됩니다. 일반적으로 Microsoft에서 제공하는 REST API, 클라이언트 라이브러리 및 사용자 지정 도구(예: 명령줄 인터페이스)를 통해 이러한 서비스에 액세스합니다. 그러나 이는 성공을 위한 한 가지 방법일 뿐입니다. Azure를 통해 다음과 같은 여러 개발 옵션에 액세스할 수도 있습니다.

* Logic Apps 및 Power Automate와 같은 자동화 및 통합 도구
* Azure Functions 및 App Service와 같은 배포 옵션입니다. 
* 안전한 액세스를 위한 Cognitive Services Docker 컨테이너입니다.
* Apache Spark, Azure Databasericks, Azure Synapse Analytics 및 Azure Kubernetes Service for Big Data 시나리오와 같은 도구입니다. 

시작하기 전에, Cognitive Services가 주로 두 가지 고유 작업에 사용된다는 것을 알고 있어야 합니다. 수행하려는 작업에 따라 다양한 개발 및 배포 옵션을 선택할 수 있습니다. 

* [예측 및 분석을 위한 개발 옵션](#development-options-for-prediction-and-analysis)
* [모델 사용자 지정 및 구성 도구](#tools-to-customize-and-configure-models)

## <a name="development-options-for-prediction-and-analysis"></a>예측 및 분석을 위한 개발 옵션 

모델을 사용자 지정하고 구성하는 데 사용하는 도구는 Cognitive Services를 호출하는 데 사용하는 도구와 다릅니다. 기본적으로 대부분의 Cognitive Services를 사용하면 사용자 지정 없이 데이터를 보내고 정보를 받을 수 있습니다. 예를 들면 다음과 같습니다. 

* Computer Vision 서비스에 이미지를 보내 단어와 구를 검색하거나 프레임에 있는 사람 수를 계산할 수 있습니다.
* 음성 서비스에 오디오 파일을 전송하고 음성 텍스트를 가져오는 동시에 음성을 텍스트로 변환할 수 있습니다.
* PDF를 Form Recognizer 서비스로 보내고 해당 셀 내부의 테이블, 셀 및 텍스트를 검색하면 좌표 및 세부 정보가 포함된 JSON 출력을 얻을 수 있습니다.

Azure는 다양한 유형의 사용자를 위해 설계된 다양한 도구를 제공하며, 대부분은 Cognitive Services와 함께 사용할 수 있습니다. 디자이너 기반 도구는 사용하기 쉽고 신속하게 설정 및 자동화할 수 있지만 사용자 지정하는 데는 제한이 있을 수 있습니다. REST API 및 클라이언트 라이브러리는 사용자에게 더 많은 제어와 유연성을 제공하지만 솔루션을 구축하려면 더 많은 노력, 시간 및 전문 지식이 필요합니다. REST API 및 클라이언트 라이브러리를 사용하면 C#, Java, Python, JavaScript 또는 기타 많이 사용되는 프로그래밍 언어와 같은 최신 프로그래밍 언어로 작업하는 것처럼 익숙할 것입니다. 

Cognitive Services로 작업할 수 있는 다양한 방법을 살펴보겠습니다.

### <a name="client-libraries-and-rest-apis"></a>클라이언트 라이브러리 및 REST API

Cognitive Services 클라이언트 라이브러리 및 REST API는 서비스에 대한 직접 액세스를 제공합니다. 이러한 도구는 Cognitive Services에 대한 프로그래밍 방식 액세스를 제공하며, 대부분의 경우 모델 및 솔루션을 프로그래밍 방식으로 사용자 지정할 수 있습니다. 

* **대상 사용자**: 개발자 및 데이터 과학자
* **이점**: 모든 언어 및 환경에서 서비스를 호출할 수 있는 최상의 유연성을 제공합니다. 
* **UI**: 해당 없음 - 코드만
* **구독**: Azure 계정 + Cognitive Services 리소스

사용 가능한 클라이언트 라이브러리 및 REST API에 대해 자세히 알아보려면 [Cognitive Services 개요](index.yml)를 사용하여 비전, 의사 결정, 언어 및 음성에 대한 빠른 시작 중 하나를 선택하고 서비스를 시작합니다.

### <a name="cognitive-services-for-big-data"></a>빅 데이터에 대한 Cognitive Services

빅 데이터용 Cognitive Services를 사용하면 지속적으로 향상되는 인텔리전트 모델을 Apache Spark&trade; 및 SQL 컴퓨팅에 직접 포함할 수 있습니다. 이러한 도구를 사용하면 개발자가 낮은 수준의 네트워킹 세부 정보를 알고 있을 필요가 없으므로 스마트 분산 애플리케이션을 만드는 데 집중할 수 있습니다. 빅 데이터에 대한 Cognitive Services는 Azure Databricks, Azure Synapse, Azure Kubernetes Service 및 Data Connector와 같은 플랫폼 및 커넥터를 지원합니다.

* **대상 사용자**: 데이터 과학자 및 데이터 엔지니어
* **이점**: 빅 데이터용 Azure Cognitive Services는 사용자가 Apache Spark를 통해 테라바이트 단위의 데이터를 위한 채널로 Cognitive Services를 사용할 수 있도록 지원합니다&trade;. 데이터 저장소가 있는 대규모 인텔리전트 애플리케이션을 쉽게 만들 수 있습니다.
* **UI**: 해당 없음 - 코드만
* **구독**: Azure 계정 + Cognitive Services 리소스

Cognitive Services용 빅 데이터에 대해 자세히 알아보려면 먼저 [개요](./big-data/cognitive-services-for-big-data.md)를 참조하는 것이 좋습니다. 빌드를 시작할 준비가 되었으면 [Python](./big-data/samples-python.md) 또는 [Scala](./big-data/samples-scala.md) 샘플을 사용해 보세요.

### <a name="azure-functions-and-azure-service-web-jobs"></a>Azure Functions 및 Azure 서비스 웹 작업

[Azure Functions](../azure-functions/index.yml) 및 [Azure App Service 웹 작업](../app-service/index.yml)은 모두 개발자용으로 설계된 코드 우선 통합 서비스를 제공하며 [Azure App Services](../app-service/index.yml)를 기반으로 합니다. 이러한 제품은 코드 작성을 위한 서버리스 인프라를 제공합니다. 이 코드 내에서 클라이언트 라이브러리와 REST API를 사용하여 서비스를 호출할 수 있습니다. 

* **대상 사용자**: 개발자 및 데이터 과학자
* **이점**: 이벤트 트리거 코드를 실행할 수 있는 서버리스 컴퓨팅 서비스입니다. 
* **UI**: 예
* **구독**: Azure 계정 + Cognitive Services 리소스 + Azure Functions 구독

### <a name="azure-logic-apps"></a>Azure Logic Apps 

[Azure Logic Apps](../logic-apps/index.yml)는 Power Automate와 동일한 워크플로 디자이너 및 커넥터를 공유하지만 Visual Studio 및 DevOps와의 통합을 포함하여 보다 고급 기능 및 제어를 제공합니다. Power Automate를 사용하면 API에 대한 프록시 또는 래퍼를 제공하는 서비스별 커넥터를 통해 Cognitive Services 리소스와 쉽게 통합할 수 있습니다. 이러한 커넥터는 Power Automate에서 사용할 수 있는 것과 동일한 커넥터입니다. 

* **대상 사용자**: 개발자, 통합자, IT 전문가, DevOps
* **이점**: 낮은 코드 솔루션에서 고급 옵션과 통합을 제공하는 디자이너 중심(선언적) 개발 모델
* **UI**: 예
* **구독**: Azure 계정 + Cognitive Services 리소스 + Logic Apps 배포

### <a name="power-automate"></a>Power Automate 

Power Automate는 코드를 작성하지 않고도 앱과 서비스 간에 자동화된 워크플로를 생성하는 데 도움이 되는 [Power Platform](/power-platform/) 서비스입니다. Power Automate 솔루션에서는 Cognitive Services 리소스와 쉽게 상호 작용할 수 있도록 여러 커넥터를 제공합니다. Power Automate는 Logic Apps를 기반으로 빌드됩니다. 

* **대상 사용자**: 비즈니스 사용자(분석가) 및 SharePoint 관리자
* **이점**: 데스크톱에서 마우스 클릭, 키 입력 및 복사/붙여넣기 단계를 기록하여 반복적인 수동 작업을 자동화합니다.
* **UI 도구**: 예 - UI 전용
* **구독**: Azure 계정 + Cognitive Services 리소스 + Power Automate 구독 + Office 365 구독

### <a name="ai-builder"></a>AI Builder 

[AI Builder](/ai-builder/overview)는 프로세스를 자동화하고 결과를 예측하여 비즈니스 성과를 개선하는 데 사용할 수 있는 Microsoft Power Platform 기능입니다. AI Builder는 포인트 앤 클릭 환경을 통해 AI의 강력한 기능을 솔루션에 제공합니다. Form Recognizer, Text Analytics 및 Computer Vision과 같은 많은 Cognitive Services가 직접 통합되어 있으므로 자체 Cognitive Services를 만들 필요가 없습니다. 

* **대상 사용자**: 비즈니스 사용자(분석가) 및 SharePoint 관리자
* **이점**: 포인트 클릭 환경을 통해 AI 기능을 제공하는 턴키 솔루션입니다. 코딩이나 데이터 과학 기술이 필요하지 않습니다.
* **UI 도구**: 예 - UI 전용
* **구독**: AI Builder

### <a name="continuous-integration-and-deployment"></a>연속 통합 및 배포

Azure DevOps 및 GitHub Actions를 사용하여 배포를 관리할 수 있습니다. [아래 섹션](#continuous-integration-and-delivery-with-devops-and-github-actions)에서는 음성 및 Language Understanding (LUIS) 서비스를 위한 사용자 지정 모델을 학습시키고 배포하기 위한 CI/CD 통합의 두 가지 예를 보여 줍니다. 

* **대상 사용자**: 개발자, 데이터 과학자 및 데이터 엔지니어
* **이점**: 프로그래밍 방식으로 애플리케이션과 모델을 지속적으로 조정, 업데이트 및 배포할 수 있습니다. 데이터를 정기적으로 사용하면 음성, 비전, 언어 및 의사 결정을 위한 모델을 개선하고 업데이트할 때 상당한 이점이 있습니다. 
* **UI 도구**: 해당 없음 - 코드 전용 
* **구독**: Azure 계정 + Cognitive Services 리소스 + GitHub 계정

## <a name="tools-to-customize-and-configure-models"></a>모델 사용자 지정 및 구성 도구

Cognitive Services를 사용하여 애플리케이션 또는 워크플로를 빌드하는 과정을 진행하면서 원하는 성능을 얻기 위해 모델을 사용자 지정해야 할 수 있습니다. 많은 서비스를 통해 미리 빌드된 모델을 기반으로 특정 비즈니스 요구 사항을 충족할 수 있습니다. 사용자 지정 가능한 모든 서비스에 대해 코드 기반 학습을 위한 API뿐만 아니라 프로세스를 살펴볼 수 있는 UI 기반 환경을 제공합니다. 예를 들면 다음과 같습니다.

* 단어 오류율(WER) 3% 미만으로 의학 용어를 올바르게 인식하도록 사용자 지정 음성 모델을 학습시키려고 합니다
* Custom Vision으로 침엽수와 낙엽수의 차이를 구분할 수 있는 이미지 분류자를 빌드하려고 합니다.
* 향상된 자동화된 고객 환경을 위해 개인 음성 데이터로 사용자 지정 신경망 음성을 빌드하려고 합니다.

모델을 학습시키고 구성하는 데 사용하는 도구는 Cognitive Services를 호출하는 데 사용하는 도구와 다릅니다. 대부분의 경우 사용자 지정을 지원하는 Cognitive Services는 모델을 학습, 평가 및 배포하는 데 도움이 되도록 설계된 포털 및 UI 도구를 제공합니다. 몇 가지 옵션에 대해 간단히 살펴보겠습니다.<br><br>

| 핵심 요소 | 서비스 | 사용자 지정 UI | 빠른 시작 |
|--------|---------|------------------|------------|
| Vision | Custom Vision | https://www.customvision.ai/ | [빠른 시작](./custom-vision-service/quickstarts/image-classification.md?pivots=programming-language-csharp) | 
| Vision | Form Recognizer | [샘플 레이블 지정 도구](https://aka.ms/fott-2.1-ga) | [방법 문서](./form-recognizer/label-tool.md?tabs=v2-0) |
| 의사 결정 | Content Moderator | https://contentmoderator.cognitive.microsoft.com/dashboard | [빠른 시작](./content-moderator/review-tool-user-guide/human-in-the-loop.md) |
| 의사 결정 | Personalizer | UI는 Azure Portal의 Personalizer 리소스에서 사용할 수 있습니다. | [빠른 시작](./personalizer/quickstart-personalizer-sdk.md) |
| 언어 | 언어 이해(LUIS) | https://www.luis.ai/ | |
| 언어 | QnA Maker | https://www.qnamaker.ai/ | [빠른 시작](./qnamaker/quickstarts/create-publish-knowledge-base.md) |
| 언어 | Translator/Custom Translator | https://portal.customtranslator.azure.ai/ | [빠른 시작](./translator/custom-translator/quickstart-build-deploy-custom-model.md) |
| Speech | 사용자 지정 명령 | https://speech.microsoft.com/ | [빠른 시작](./speech-service/custom-commands.md) |
| Speech | Custom Speech | https://speech.microsoft.com/ | [빠른 시작](./speech-service/custom-speech-overview.md) |
| Speech | Custom Voice | https://speech.microsoft.com/ | [빠른 시작](./speech-service/how-to-custom-voice.md) |  

### <a name="continuous-integration-and-delivery-with-devops-and-github-actions"></a>DevOps 및 GitHub Actions를 통한 연속 통합 및 제공

Language Understanding 및 Speech Service는 Azure DevOps 및 GitHub Actions로 구동되는 연속 통합 및 연속 배포 솔루션을 제공합니다. 이러한 도구는 사용자 지정 모델의 자동화된 학습, 테스트 및 릴리스 관리에 사용됩니다. 

* [Custom Speech용 CI/CD](./speech-service/how-to-custom-speech-continuous-integration-continuous-deployment.md)
* [LUIS용 CI/CD](./luis/luis-concept-devops-automation.md)

## <a name="on-premises-containers"></a>온-프레미스 컨테이너 

대부분의 Cognitive Services는 온-프레미스 액세스 및 사용을 위해 컨테이너에 배포할 수 있습니다. 이러한 컨테이너를 사용하면 규정 준수, 보안 또는 기타 운영상의 이유로 Cognitive Services를 데이터에 더 가깝게 가져올 수 있는 유연성이 제공됩니다. Cognitive Services 컨테이너의 전체 목록은 [Cognitive Services용 온-프레미스 컨테이너](./cognitive-services-container-support.md)를 참조하세요.

## <a name="next-steps"></a>다음 단계
<!--
* Learn more about low code development options for Cognitive Services -->
* [Cognitive Services 리소스 만들기 및 빌드 시작](./cognitive-services-apis-create-account.md?tabs=multiservice%252clinux)
