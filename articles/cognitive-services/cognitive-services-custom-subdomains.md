---
title: 사용자 지정 하위 도메인
titleSuffix: Azure Cognitive Services
description: 각 Cognitive Service 리소스에 대한 사용자 지정 하위 도메인 이름은 Azure Portal, Azure Cloud Shell 또는 Azure CLI를 통해 만들어집니다.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 12/04/2020
ms.author: pafarley
ms.openlocfilehash: 7eb2fe58ba9258da1bc2e9b75deafe09323228f0
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131086738"
---
# <a name="custom-subdomain-names-for-cognitive-services"></a>Cognitive Services용 사용자 지정 하위 도메인 이름

Azure Cognitive Services는 [Azure Portal](https://portal.azure.com), [Azure Cloud Shell](https://azure.microsoft.com/features/cloud-shell/) 또는 [Azure CLI](/cli/azure/install-azure-cli)를 통해 만든 각 리소스에 대해 사용자 지정 하위 도메인 이름을 사용합니다. 특정 Azure 지역의 모든 고객에게 공통적인 지역 엔드포인트와 달리 사용자 지정 하위 도메인 이름은 리소스마다 고유합니다. 인증을 위해 Azure AD(Azure Active Directory)와 같은 기능을 사용하려면 사용자 지정 하위 도메인 이름이 필요합니다.

## <a name="how-does-this-impact-existing-resources"></a>이 방법은 기존 리소스에 어떤 영향을 미치나요?

2019년 7월 1일 이전에 만들어진 Cognitive Services 리소스는 연결된 서비스에 대한 지역 엔드포인트를 사용합니다. 이러한 엔드포인트는 기존 리소스 및 새 리소스를 사용합니다.

Azure AD와 같은 기능을 사용할 수 있도록 사용자 지정 하위 도메인 이름을 활용하여 기존 리소스를 마이그레이션하려면 다음 지침을 따르세요.

1. Azure Portal에 로그인하고 사용자 지정 하위 도메인 이름을 추가할 Cognitive Services 리소스를 찾습니다.
2. **개요** 블레이드에서 **사용자 지정 도메인 이름 생성** 을 찾아 선택합니다.
3. 그러면 리소스에 대해 고유한 사용자 지정 하위 도메인을 만드는 지침과 함께 패널이 열립니다.
   > [!WARNING]
   > 사용자 지정 하위 도메인 이름을 만든 후에는 변경할 수 **없습니다**.

## <a name="do-i-need-to-update-my-existing-resources"></a>기존 리소스를 업데이트해야 하나요?

아니요. 지역 엔드포인트는 신규 및 기존 Cognitive Services에서 계속 작동하며 사용자 지정 하위 도메인 이름은 선택 사항입니다. 사용자 지정 하위 도메인 이름이 추가되더라도 지역 엔드포인트는 계속 리소스를 사용합니다.

## <a name="what-if-an-sdk-asks-me-for-the-region-for-a-resource"></a>SDK에서 리소스에 대한 지역을 요청하는 경우 어떻게 되나요?

> [!WARNING]
> Speech Services는 [프라이빗 엔드포인트](Speech-Service/speech-services-private-link.md)**만** 있는 사용자 지정 하위 도메인을 사용합니다. 다른 모든 경우에는 Speech Services 및 관련 SDK와 함께 **지역 엔드포인트** 를 사용합니다.

지역 엔드포인트와 사용자 지정 하위 도메인 이름이 모두 지원되며 서로 교환하여 사용할 수 있습니다. 그러나 전체 엔드포인트가 필요합니다.

지역 정보는 [Azure Portal](https://portal.azure.com)의 리소스에 대한 **개요** 블레이드에서 사용할 수 있습니다. 지역 엔드포인트의 전체 목록은 [지역 엔드포인트 목록이 있나요?](#is-there-a-list-of-regional-endpoints)를 참조하세요.

## <a name="are-custom-subdomain-names-regional"></a>사용자 지정 하위 도메인 이름은 지역적인가요?

예. 사용자 지정 하위 도메인 이름을 사용해도 Cognitive Services 리소스의 지역적 측면은 변경되지 않습니다.

## <a name="what-are-the-requirements-for-a-custom-subdomain-name"></a>사용자 지정 하위 도메인 이름에 대한 요구 사항은 무엇인가요?

사용자 지정 하위 도메인 이름은 리소스마다 고유합니다. 이름에는 영숫자 문자와 `-` 문자만 사용할 수 있습니다. 길이는 2~64자여야 하며, `-`로 끝날 수 없습니다.

## <a name="can-i-change-a-custom-domain-name"></a>사용자 지정 도메인 이름을 변경할 수 있나요?

아니요. 사용자 지정 하위 도메인 이름을 만들고 리소스와 연결한 후에는 변경할 수 없습니다.

## <a name="can-i-reuse-a-custom-domain-name"></a>사용자 지정 도메인 이름을 다시 사용할 수 있나요?

각 사용자 지정 하위 도메인 이름은 고유하므로 Cognitive Services 리소스에 할당한 사용자 지정 하위 도메인 이름을 다시 사용하려면 기존 리소스를 삭제해야 합니다. 리소스를 삭제한 후에는 사용자 지정 하위 도메인 이름을 다시 사용할 수 있습니다.

## <a name="is-there-a-list-of-regional-endpoints"></a>지역 엔드포인트 목록이 있나요?

예. 다음은 Azure Cognitive Services 리소스에 사용할 수 있는 지역 엔드포인트 목록입니다.

> [!NOTE]
> Translator 서비스와 Bing Search API는 전역 엔드포인트를 사용합니다.

| 엔드포인트 유형 | 지역 | 엔드포인트 |
|---------------|--------|----------|
| 공용 | 전역(Translator 및 Bing) | `https://api.cognitive.microsoft.com` |
| | 오스트레일리아 동부 | `https://australiaeast.api.cognitive.microsoft.com` |
| | 브라질 남부 | `https://brazilsouth.api.cognitive.microsoft.com` |
| | 캐나다 중부 | `https://canadacentral.api.cognitive.microsoft.com` |
| | 미국 중부 | `https://centralus.api.cognitive.microsoft.com` |
| | 동아시아 | `https://eastasia.api.cognitive.microsoft.com` |
| | 미국 동부 | `https://eastus.api.cognitive.microsoft.com` |
| | 미국 동부 2 | `https://eastus2.api.cognitive.microsoft.com` |
| | 프랑스 중부 | `https://francecentral.api.cognitive.microsoft.com` |
| | 인도 중부 | `https://centralindia.api.cognitive.microsoft.com` |
| | 일본 동부 | `https://japaneast.api.cognitive.microsoft.com` |
| | 한국 중부 | `https://koreacentral.api.cognitive.microsoft.com` |
| | 미국 중북부 | `https://northcentralus.api.cognitive.microsoft.com` |
| | 북유럽 | `https://northeurope.api.cognitive.microsoft.com` |
| | 남아프리카 북부 | `https://southafricanorth.api.cognitive.microsoft.com` |
| | 미국 중남부 | `https://southcentralus.api.cognitive.microsoft.com` |
| | 동남아시아 | `https://southeastasia.api.cognitive.microsoft.com` |
| | 영국 남부 | `https://uksouth.api.cognitive.microsoft.com` |
| | 미국 중서부 | `https://westcentralus.api.cognitive.microsoft.com` |
| | 서유럽 | `https://westeurope.api.cognitive.microsoft.com` |
| | 미국 서부 | `https://westus.api.cognitive.microsoft.com` |
| | 미국 서부 2 | `https://westus2.api.cognitive.microsoft.com` |
| US Gov | US Gov 버지니아 | `https://virginia.api.cognitive.microsoft.us` |
| 중국 | 중국 동부 2 | `https://chinaeast2.api.cognitive.azure.cn` |
| | 중국 북부 | `https://chinanorth.api.cognitive.azure.cn` |

## <a name="see-also"></a>참고 항목

* [Cognitive Services란?](./what-are-cognitive-services.md)
* [인증](authentication.md)