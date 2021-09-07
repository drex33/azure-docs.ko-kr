---
title: Custom Vision 프로젝트 복사 및 이동
titleSuffix: Azure Cognitive Services
description: ExportProject 및 ImportProject API를 사용하여 Custom Vision 프로젝트를 복사하고 이동하는 방법을 알아봅니다.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: how-to
ms.date: 09/08/2020
ms.author: pafarley
ms.openlocfilehash: d546726e03420b41b4ff445418e30274904c6e5c
ms.sourcegitcommit: 9f1a35d4b90d159235015200607917913afe2d1b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/21/2021
ms.locfileid: "122635061"
---
# <a name="copy-and-move-your-custom-vision-projects"></a>Custom Vision 프로젝트 복사 및 이동

Custom Vision 프로젝트를 만들고 학습한 후에 프로젝트를 다른 리소스로 복사하고자 할 수 있습니다. 예를 들어 개발 환경에서 프로덕션 환경으로 프로젝트를 이동하거나 데이터 보안을 강화하기 위해 프로젝트를 다른 Azure 지역의 계정으로 백업하려고 할 수 있습니다.

**[ExportProject](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Training_3.3/operations/5eb0bcc6548b571998fddeb3)** 및 **[ImportProject](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Training_3.3/operations/5eb0bcc7548b571998fddee3)** API는 한 Custom Vision 계정에서 다른 계정으로 프로젝트를 복사할 수 있도록 하여 이 시나리오를 가능하게 합니다. 이 가이드에서는 cURL로 이러한 REST API를 사용하는 방법을 보여 줍니다. Postman과 같은 HTTP 요청 서비스를 사용하여 요청을 발행할 수도 있습니다.

> [!TIP]
> Python 클라이언트 라이브러리를 사용하는 이 시나리오의 예는 GitHub에서 [Custom Vision 프로젝트 이동](https://github.com/Azure-Samples/custom-vision-move-project/tree/master/) 리포지토리를 참조하세요.

## <a name="business-scenarios"></a>비즈니스 시나리오

앱 또는 비즈니스가 Custom Vision 프로젝트 사용에 따라 달라지는 경우 다른 지역의 다른 Custom Vision 계정으로 모델을 복사하는 것이 좋습니다. 그러면 지역 가동 중단이 발생하는 경우 복사된 지역의 프로젝트에 액세스할 수 있습니다.

##  <a name="prerequisites"></a>필수 구성 요소

- 두 개의 Azure Custom Vision 리소스. 없는 경우 Azure Portal로 이동하여 [새 Custom Vision 리소스를 만듭니다](https://portal.azure.com/?microsoft_azure_marketplace_ItemHideKey=microsoft_azure_cognitiveservices_customvision#create/Microsoft.CognitiveServicesCustomVision?azure-portal=true).
- Custom Vision 리소스의 학습 키 및 엔드포인트 URL. Azure Portal의 리소스 **개요** 탭에서 이러한 값을 찾을 수 있습니다.
- 만들어진 Custom Vision 프로젝트. 이 작업을 수행하는 방법에 대한 지침은 [분류자 빌드](./getting-started-build-a-classifier.md)를 참조하세요.
* [PowerShell 버전 6.0 이상](/powershell/scripting/install/installing-powershell-core-on-windows) 또는 유사한 명령줄 유틸리티

## <a name="process-overview"></a>프로세스 개요

프로젝트를 복사하는 프로세스는 다음 단계로 구성됩니다.

1. 먼저 복사하려는 원본 계정에서 프로젝트의 ID를 가져옵니다.
1. 그런 다음 원본 계정의 프로젝트 ID 및 학습 키를 사용하여 **ExportProject** API를 호출합니다. 임시 토큰 문자열을 가져오게 됩니다.
1. 그런 다음 대상 계정의 토큰 문자열과 학습 키를 사용하여 **ImportProject** API를 호출합니다. 그러면 프로젝트가 대상 계정 아래에 나열됩니다.

## <a name="get-the-project-id"></a>프로젝트 ID 가져오기

먼저 **[GetProjects](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Training_3.3/operations/5eb0bcc6548b571998fddead)** 를 호출하여 기존 Custom Vision 프로젝트 및 해당 ID의 목록을 확인합니다. 원본 계정의 학습 키 및 엔드포인트를 사용합니다.

```curl
curl -v -X GET "{endpoint}/customvision/v3.3/Training/projects"
-H "Training-key: {training key}"
```

본문에 프로젝트 목록과 해당 메타데이터가 있는 `200\OK` 응답을 받게 됩니다. `"id"` 값은 다음 단계에 대해 복사할 문자열입니다.

```json
[
  {
    "id": "00000000-0000-0000-0000-000000000000",
    "name": "string",
    "description": "string",
    "settings": {
      "domainId": "00000000-0000-0000-0000-000000000000",
      "classificationType": "Multiclass",
      "targetExportPlatforms": [
        "CoreML"
      ],
      "useNegativeSet": true,
      "detectionParameters": "string",
      "imageProcessingSettings": {
        "augmentationMethods": {}
      }
    },
    "created": "string",
    "lastModified": "string",
    "thumbnailUri": "string",
    "drModeEnabled": true,
    "status": "Succeeded"
  }
]
```

## <a name="export-the-project"></a>프로젝트 내보내기

프로젝트 ID와 원본 학습 키 및 엔드포인트를 사용하여 **[ExportProject](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Training_3.3/operations/5eb0bcc6548b571998fddeb3)** 를 호출합니다.

```curl
curl -v -X GET "{endpoint}/customvision/v3.3/Training/projects/{projectId}/export"
-H "Training-key: {training key}"
```

내보낸 프로젝트에 대한 메타데이터 및 참조 문자열 `"token"`이 있는 `200/OK` 응답을 받게 됩니다. 토큰의 값을 복사합니다.

```json
{
  "iterationCount": 0,
  "imageCount": 0,
  "tagCount": 0,
  "regionCount": 0,
  "estimatedImportTimeInMS": 0,
  "token": "string"
}
```

> [!TIP]
> 프로젝트를 가져올 때 "잘못된 토큰" 오류가 발생하면 토큰 URL 문자열이 웹으로 인코딩되지 않은 것일 수 있습니다. [URL 인코더](https://meyerweb.com/eric/tools/dencoder/)를 사용하여 토큰을 인코딩할 수 있습니다.

## <a name="import-the-project"></a>프로젝트 가져오기

참조 토큰과 함께 대상 학습 키 및 엔드포인트를 사용하여 **[ImportProject](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Training_3.3/operations/5eb0bcc7548b571998fddee3)** 를 호출합니다. 새 계정에서 프로젝트에 이름을 지정할 수도 있습니다.

```curl
curl -v -G -X POST "{endpoint}/customvision/v3.3/Training/projects/import"
--data-urlencode "token={token}" --data-urlencode "name={name}"
-H "Training-key: {training key}" -H "Content-Length: 0"
```

새로 가져온 프로젝트에 대한 메타데이터가 있는 `200/OK` 응답을 받게 됩니다.

```json
{
  "id": "00000000-0000-0000-0000-000000000000",
  "name": "string",
  "description": "string",
  "settings": {
    "domainId": "00000000-0000-0000-0000-000000000000",
    "classificationType": "Multiclass",
    "targetExportPlatforms": [
      "CoreML"
    ],
    "useNegativeSet": true,
    "detectionParameters": "string",
    "imageProcessingSettings": {
      "augmentationMethods": {}
    }
  },
  "created": "string",
  "lastModified": "string",
  "thumbnailUri": "string",
  "drModeEnabled": true,
  "status": "Succeeded"
}
```

## <a name="next-steps"></a>다음 단계

이 가이드에서는 Custom Vision 리소스 간에 프로젝트를 복사하고 이동하는 방법을 알아보았습니다. 다음으로 API 참조 문서를 탐색하여 Custom Vision으로 수행할 수 있는 다른 작업을 살펴봅니다.
* [REST API 참조 설명서](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Training_3.3/operations/5eb0bcc6548b571998fddeb3)
