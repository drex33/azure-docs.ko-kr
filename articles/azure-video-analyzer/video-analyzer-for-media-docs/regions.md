---
title: Azure Video Analyzer for Media(이전의 Video Indexer)를 사용할 수 있는 지역
titleSuffix: Azure Video Analyzer for Media
description: 이 문서에서는 Azure Video Analyzer for Media(이전의 Video Indexer)를 사용할 수 있는 Azure 지역에 대해 설명합니다.
services: azure-video-analyzer
author: Juliako
manager: femila
ms.topic: article
ms.subservice: azure-video-analyzer-media
ms.date: 09/14/2020
ms.author: juliako
ms.openlocfilehash: 1db75ea3ba67d4e8f0551495274ec3fff38e29b4
ms.sourcegitcommit: 0af634af87404d6970d82fcf1e75598c8da7a044
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/15/2021
ms.locfileid: "112119174"
---
# <a name="azure-regions-in-which-video-analyzer-for-media-exists"></a>Video Analyzer for Media가 있는 Azure 지역

Azure Video Analyzer for Media(이전의 Video Indexer) API에는 호출을 라우팅할 Azure 지역으로 설정해야 하는 **location** 매개 변수가 있습니다. [Video Analyzer for Media를 사용할 수 있는 Azure 지역](https://azure.microsoft.com/global-infrastructure/services/?products=cognitive-services&regions=all)이어야 합니다.

## <a name="locations"></a>위치

`location` 매개 변수의 값으로 Azure 지역 코드 이름을 지정해야 합니다. 미리 보기 모드에서 Video Analyzer for Media를 사용하는 경우 값으로 `"trial"`을 입력해야 합니다. `trial`은 `location` 매개 변수의 기본값입니다. 그렇지 않으면 계정이 있고 호출이 라우팅되어야 하는 Azure 지역의 코드 이름을 가져오기 위해 Azure Portal을 사용하거나 [Azure CLI](/cli/azure) 명령을 실행할 수 있습니다.

### <a name="azure-portal"></a>Azure portal

1. [Video Analyzer for Media](https://www.videoindexer.ai/) 웹 사이트에 로그인합니다.
1. 페이지 오른쪽 위에서 **사용자 계정** 을 선택합니다.
1. 오른쪽 위에서 계정 위치를 찾습니다.  

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/location/location1.png" alt-text="위치":::
    
###  <a name="cli-command"></a>CLI 명령

```azurecli-interactive
az account list-locations
```

일단 위에 표시된 줄을 실행하면 모든 Azure 지역 목록이 표시됩니다. 찾으려는 *displayName* 이 있는 Azure 지역으로 이동한 후 **location** 매개 변수에 대해 해당 *name* 값을 사용합니다.

예를 들어 미국 서부 2의 Azure 지역에서(아래에 표시됨)는 **location** 매개 변수에 "westus2"를 사용합니다.

```json
   {
      "displayName": "West US 2",
      "id": "/subscriptions/00000000-0000-0000-0000-000000000000/locations/westus2",
      "latitude": "47.233",
      "longitude": "-119.852",
      "name": "westus2",
      "subscriptionId": null
    }
```

## <a name="next-steps"></a>다음 단계

- [API를 사용하여 언어 모델 사용자 지정](customize-language-model-with-api.md)
- [API를 사용하여 브랜드 모델 사용자 지정](customize-brands-model-with-api.md)
- [API를 사용하여 개인 모델 사용자 지정](customize-person-model-with-api.md)
