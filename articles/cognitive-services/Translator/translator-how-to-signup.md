---
title: Translator 리소스 만들기
titleSuffix: Azure Cognitive Services
description: 이 문서에서는 Azure Cognitive Services Translator 리소스를 만들고 구독 키와 엔드포인트 URL을 가져오는 방법을 보여줍니다.
services: cognitive-services
author: laujan
ms.author: lajanuar
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: how-to
ms.date: 02/16/2021
ms.openlocfilehash: 02d1e509068c2332164fea7236fe1fe554b3ef02
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131439275"
---
# <a name="create-a-translator-resource"></a>Translator 리소스 만들기

이 문서에서는 Azure Portal에서 Translator 리소스를 만드는 방법을 알아봅니다. [Azure Translator](../what-are-cognitive-services.md)는 [Azure Cognitive Services](translator-overview.md) REST API 제품군의 일부인 클라우드 기반 기계 번역 서비스입니다. Azure 리소스는 만드는 서비스의 인스턴스입니다. Azure 서비스에 대한 모든 API 요청에는 액세스를 인증하기 위한 **엔드포인트** URL 및 읽기 전용 **구독 키** 가 필요합니다.

## <a name="prerequisites"></a>사전 요구 사항

시작하려면 활성 [**Azure 계정**](https://azure.microsoft.com/free/cognitive-services/)이 필요합니다.  계정이 없는 경우 [**무료 12개월 구독을 만들 수 있습니다**](https://azure.microsoft.com/free/).

## <a name="translator-resource-types"></a>Translator 리소스 종류

Translator 서비스는 다음 두 가지 리소스 종류를 통해 액세스할 수 있습니다.

* **단일 서비스** 리소스 종류를 사용하면 단일 서비스 API 키 및 엔드포인트에 액세스할 수 있습니다.  

* **다중 서비스** 리소스 종류를 사용하면 단일 API 키 및 엔드포인트를 사용하여 여러 Cognitive Services에 액세스할 수 있습니다. Cognitive Services 리소스는 현재 다음 서비스에 대해 사용할 수 있습니다.
  * language ([번역기](../translator/translator-overview.md), [Language Understanding (LUIS)](../luis/what-is-luis.md), [language service](../text-analytics/overview.md))  
  * 비전([Computer Vision](../computer-vision/overview.md)), ([Face](../face/overview.md))  
  * 결정([Content Moderator](../content-moderator/overview.md))  

## <a name="create-your-resource"></a>리소스 만들기

* Azure Portal의 [**Translator 만들기**](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextTranslation) 페이지로 직접 이동하여 프로젝트 세부 정보를 완료합니다.

* Azure Portal의 [**Cognitive Services 만들기**](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesAllInOne) 페이지로 직접 이동하여 프로젝트 세부 정보를 완료합니다.

>[!TIP]
>원하는 경우 Azure Portal 홈페이지에서 다음과 같이 **만들기** 프로세스를 시작할 수 있습니다.
>
> 1. [**Azure Portal**](https://ms.portal.azure.com/#home) 홈페이지로 이동합니다.
> 1. Azure 서비스 메뉴에서 ➕**리소스 만들기** 를 선택합니다.
>1. **마켓플레이스 검색** 검색 상자에서 **Translator**(단일 서비스 리소스) 또는 **Cognitive Services**(다중 서비스 리소스)를 입력하고 선택합니다.  위의 [리소스 종류 선택](#create-your-resource)을 *참조* 하세요.
> 1. **만들기** 를 선택하면 프로젝트 세부 정보 페이지로 이동합니다.
><br/><br/>

## <a name="complete-your-project-and-instance-details"></a>프로젝트 및 인스턴스 세부 정보 완료

1. **구독**. 사용 가능한 Azure 구독 중 하나를 선택합니다.

1. **리소스 그룹**. 선택한 Azure 리소스 그룹은 새 리소스에 대한 가상 컨테이너 역할을 합니다. 새 리소스 그룹을 만들거나 동일한 수명 주기, 권한 및 정책을 공유하는 기존 리소스 그룹에 리소스를 추가할 수 있습니다.

1. **리소스 지역**. 비즈니스 또는 애플리케이션에서 특정 지역을 요구하지 않는 한 **전체** 를 선택합니다. Translator는 비지역 서비스이므로 특정 Azure 지역에 대한 종속성이 없습니다. [Azure의 지역 및 가용성 영역](../../availability-zones/az-overview.md)을 *참조하세요*.

1. **이름**. 리소스에 대해 선택한 이름을 입력합니다. 선택하는 이름이 Azure 내에서 고유해야 합니다.

> [!NOTE]
> 사용자 지정 도메인 엔드포인트가 필요한 Translator 기능을 사용하는 경우 이름 필드에 입력한 값이 엔드포인트에 대한 사용자 지정 도메인 이름 매개 변수가 됩니다.

5. **가격 책정 계층**. 요구 사항에 맞는 [가격 책정 계층](https://azure.microsoft.com/pricing/details/cognitive-services/translator)을 선택합니다.

   * 각 구독에는 체험 계층이 있습니다.
   * 체험 계층에는 유료 플랜과 동일한 특징과 기능이 있으며 만료되지 않습니다.
   * 계정당 무료 구독을 하나만 사용할 수 있습니다.</li></ul>

1. 다중 서비스 리소스를 만든 경우에는 확인란을 통해 추가 사용 정보를 확인해야 합니다.

1. **검토 + 생성** 를 선택합니다.

1. 서비스 약관을 검토하고 **만들기** 를 선택하여 리소스를 배포합니다.

1. 리소스가 성공적으로 배포되면 **리소스로 이동** 을 선택합니다.

### <a name="authentication-keys-and-endpoint-url"></a>인증 키 및 엔드포인트 URL

모든 Cognitive Services API 요청에는 인증을 위한 엔드포인트 URL 및 읽기 전용 키가 필요합니다.

* **인증 키**. 키는 Translation 서비스에 대한 모든 요청에 전달되는 고유한 문자열입니다. 쿼리 문자열 매개 변수를 통해 키를 전달하거나 HTTP 요청 헤더에서 키를 지정할 수 있습니다.

* **엔드포인트 URL**. 특정 Azure 지역이 필요하지 않는 한 API 요청에서 전체 엔드포인트를 사용합니다. [기준 URL](reference/v3-0-reference.md#base-urls)을 *참조* 하세요. 전체 엔드포인트 URL은 `api.cognitive.microsofttranslator.com`입니다.

## <a name="get-your-authentication-keys-and-endpoint"></a>인증 키 및 엔드포인트 가져오기

1. 새 리소스를 배포한 후 **리소스로 이동** 을 선택하거나 리소스 페이지로 직접 이동합니다.
1. 왼쪽 레일의 *리소스 관리* 에서 **키 및 엔드포인트** 를 선택합니다.
1. *Microsoft 메모장* 과 같은 편리한 위치에 구독 키와 엔드포인트 URL을 복사하고 붙여넣습니다.

:::image type="content" source="../media/cognitive-services-apis-create-account/get-cog-serv-keys.png" alt-text="키와 엔드포인트를 가져옵니다.":::

## <a name="how-to-delete-a--resource-or-resource-group"></a>리소스 또는 리소스 그룹을 삭제하는 방법

> [!Warning]
> 리소스 그룹을 삭제하면 그룹에 포함된 리소스도 모두 삭제됩니다.

Cognitive Services 또는 Translator 리소스를 제거하려면 **리소스를 삭제** 하거나 **리소스 그룹을 삭제** 하면 됩니다.

리소스 삭제:

1. Azure Portal에서 리소스 그룹으로 이동합니다.
1. 인접한 확인란을 선택하여 삭제할 리소스를 선택합니다.
1. 오른쪽 가장자리 근처의 상단 메뉴에서 **삭제** 를 선택합니다.
1. **삭제된 리소스** 대화 상자에서 *예* 를 입력합니다.
1. **삭제** 를 선택합니다.

리소스 그룹을 삭제하려면 다음을 수행합니다.

1. Azure Portal에서 리소스 그룹으로 이동합니다.
1. 왼쪽 가장자리 근처의 상단 메뉴 모음에서 **리소스 그룹 삭제** 를 선택합니다.
1. 리소스 그룹의 이름을 입력하고 **삭제** 를 선택하여 삭제 요청을 확인합니다.

## <a name="how-to-get-started-with-translator"></a>Translator를 시작하는 방법

이 빠른 시작에서는 REST API에서 Translator 서비스를 사용하는 방법을 알아봅니다.

> [!div class="nextstepaction"]
> [Translator 시작하기](quickstart-translator.md)

## <a name="more-resources"></a>추가 리소스

* [Microsoft Translator 코드 샘플](https://github.com/MicrosoftTranslator).  다국어 Translator 코드 샘플은 GitHub에서 사용할 수 있습니다.
* [Microsoft Translator 지원 포럼](https://www.aka.ms/TranslatorForum)
* [Azure 시작(3분 동영상)](https://azure.microsoft.com/get-started/?b=16.24)