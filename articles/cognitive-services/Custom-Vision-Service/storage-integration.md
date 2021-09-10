---
title: 알림 및 모델 백업을 위해 Azure Storage 통합
titleSuffix: Azure Cognitive Services
description: Custom Vision 모델을 학습하거나 내보낼 때 Azure Storage를 통합하여 푸시 알림을 받는 방법을 알아봅니다. 내보낸 모델의 백업을 저장할 수도 있습니다.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: how-to
ms.date: 06/25/2021
ms.author: pafarley
ms.openlocfilehash: f21313a357c5c86087d3505cfdf654d9958d3893
ms.sourcegitcommit: 7c44970b9caf9d26ab8174c75480f5b09ae7c3d7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/27/2021
ms.locfileid: "112982369"
---
# <a name="integrate-azure-storage-for-notifications-and-backup"></a>알림 및 백업을 위해 Azure Storage 통합

Custom Vision 프로젝트를 Azure Blob Storage 큐와 통합하여 프로젝트 학습/내보내기 작업 및 게시된 모델의 백업 복사본에 대한 푸시 알림을 받을 수 있습니다. 이 기능은 작업이 오래 실행될 때 결과를 위해 서비스를 지속적으로 폴링하는 것을 방지하는 데 유용합니다. 대신 스토리지 큐 알림을 워크플로에 통합할 수 있습니다.

이 가이드에서는 cURL로 이러한 REST API를 사용하는 방법을 보여 줍니다. Postman과 같은 HTTP 요청 서비스를 사용하여 요청을 발행할 수도 있습니다.

> [!NOTE]
> 푸시 알림은 **CreateProject** API의 선택적 _notificationQueueUri_ 매개 변수에 따라 달라지고, 모델 백업에도 선택적 _exportModelContainerUri_ 매개 변수를 사용해야 합니다. 이 가이드에서는 전체 기능 집합에 대해 둘 다 사용합니다.

## <a name="prerequisites"></a>필수 구성 요소

- Azure의 Custom Vision 리소스. 없는 경우 Azure Portal로 이동하여 [새 Custom Vision 리소스를 만듭니다](https://portal.azure.com/?microsoft_azure_marketplace_ItemHideKey=microsoft_azure_cognitiveservices_customvision#create/Microsoft.CognitiveServicesCustomVision?azure-portal=true). 이 기능은 현재 Cognitive Service 리소스를 지원하지 않습니다(올인원 키).
- BLOB 컨테이너가 있는 Azure Storage 계정. 이 단계에서 도움이 필요한 경우 [스토리지 빠른 시작](../../storage/blobs/storage-quickstart-blobs-portal.md)을 따르세요.
- [PowerShell 버전 6.0 이상](/powershell/scripting/install/installing-powershell-core-on-windows) 또는 유사한 명령줄 애플리케이션.

## <a name="set-up-azure-storage-integration"></a>Azure Storage 통합 설정

Azure Portal에서 Custom Vision 학습 리소스로 이동하여 **ID** 페이지를 선택하고 시스템이 할당한 관리 ID를 사용하도록 설정합니다.

다음으로 Azure Portal의 스토리지 리소스로 이동합니다. 리소스의 **액세스 제어(IAM)** 페이지로 이동한 후 **+ 역할 할당 추가(미리 보기)** 를 선택합니다. 그런 다음, 통합 기능 중 하나 또는 둘 모두에 대한 역할 할당을 추가합니다.
* 모델 백업 기능을 사용할 계획인 경우 **Storage Blob 데이터 기여자** 역할을 선택하고 Custom Vision 학습 리소스를 구성원으로 추가합니다. **검토 및 게시** 를 선택하여 완료합니다.
* 알림 큐 기능을 사용할 계획인 경우 **Storage Queue 데이터 기여자** 역할을 선택하고 Custom Vision 학습 리소스를 구성원으로 추가합니다. **검토 및 게시** 를 선택하여 완료합니다.

역할 할당에 대한 도움말을 보려면 [Azure Portal을 사용하여 Azure 역할 할당](https://review.docs.microsoft.com/azure/role-based-access-control/role-assignments-portal)을 참조하세요.

### <a name="get-integration-urls"></a>통합 URL 가져오기

다음으로 Custom Vision 리소스가 이러한 엔드포인트에 액세스할 수 있도록 하는 URL을 얻습니다.

알림 큐 통합 URL의 경우 스토리지 계정의 **큐** 페이지로 이동하여 새 큐를 추가하고 해당 URL을 임시 위치에 저장합니다.

> [!div class="mx-imgBorder"]
> ![Azure Storage 큐 페이지](./media/storage-integration/queue-url.png) 

모델 백업 통합 URL의 경우 스토리지 계정의 **컨테이너** 페이지로 이동하여 새 컨테이너를 만듭니다. 그런 다음 이를 선택하고 **속성** 페이지로 이동합니다. URL을 임시 위치에 복사합니다.
 
> [!div class="mx-imgBorder"]
> ![Azure Storage 컨테이너 속성 페이지](./media/storage-integration/container-url.png) 


## <a name="integrate-custom-vision-project"></a>Custom Vision 프로젝트 통합

이제 통합 URL이 있으므로 Azure Storage 기능을 통합하는 새 Custom Vision 프로젝트를 만들 수 있습니다. 또한 기존 프로젝트를 업데이트하여 기능을 추가할 수 있습니다.

### <a name="create-new-project"></a>새 프로젝트 만들기

[CreateProject](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Training_3.3/operations/5eb0bcc6548b571998fddeae) API를 호출할 때 선택적 매개 변수 _exportModelContainerUri_ 및 _notificationQueueUri_ 를 추가합니다. 이전 섹션에서 얻은 URL 값을 할당합니다. 

```curl
curl -v -X POST "{endpoint}/customvision/v3.3/Training/projects?exportModelContainerUri={inputUri}&notificationQueueUri={inputUri}&name={inputName}"
-H "Training-key: {subscription key}"
```

`200/OK` 응답을 수신한 경우 이는 URL이 성공적으로 설정되었음을 의미합니다. JSON 응답에도 URL 값이 표시되어야 합니다.

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
},
"exportModelContainerUri": {url}
"notificationQueueUri": {url}
  },
  "created": "string",
  "lastModified": "string",
  "thumbnailUri": "string",
  "drModeEnabled": true,
  "status": "Succeeded"
}
```

### <a name="update-existing-project"></a>기존 프로젝트 업데이트

Azure Storage 기능 통합으로 기존 프로젝트를 업데이트하려면 업데이트하려는 프로젝트의 ID를 사용하여 [UpdateProject](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Training_3.3/operations/5eb0bcc6548b571998fddeb1) API를 호출합니다. 

```curl
curl -v -X PATCH "{endpoint}/customvision/v3.3/Training/projects/{projectId}"
-H "Content-Type: application/json"
-H "Training-key: {subscription key}"

--data-ascii "{body}" 
```

요청 본문(`body`)을 다음 JSON 형식으로 설정하고 _exportModelContainerUri_ 및 _notificationQueueUri_ 에 적절한 값을 채웁니다.

```json
{
  "name": "string",
  "description": "string",
  "settings": {
    "domainId": "00000000-0000-0000-0000-000000000000",
    "classificationType": "Multiclass",
    "targetExportPlatforms": [
      "CoreML"
    ],
    "imageProcessingSettings": {
      "augmentationMethods": {}
},
"exportModelContainerUri": {url}
"notificationQueueUri": {url}

  },
  "status": "Succeeded"
}
```

`200/OK` 응답을 수신한 경우 이는 URL이 성공적으로 설정되었음을 의미합니다.

## <a name="verify-the-connection"></a>연결 확인 

이전 섹션의 API 호출은 Azure Storage 계정에서 새 정보를 이미 트리거했어야 합니다. 

지정된 컨테이너에서 **CustomVision-TestPermission** 폴더 안에 테스트 BLOB이 있어야 합니다. 이 BLOB은 일시적으로만 존재합니다.

알림 큐에 다음과 같은 형식의 테스트 알림이 있어야 합니다.
 
```json
{
"version": "1.0" ,
"type": "ConnectionTest",
"Content":
    {
    "projectId": "00000000-0000-0000-0000-000000000000"
    }
}
```

## <a name="get-event-notifications"></a>이벤트 알림 받기

준비가 되면 프로젝트에서 [TrainProject](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Training_3.3/operations/5eb0bcc7548b571998fddee1) API를 호출하여 일반적인 학습 작업을 수행합니다.

스토리지 알림 큐에서 학습이 완료되는 대로 알림을 받게 됩니다.

```json
{
"version": "1.0" ,
"type": "Training",
"Content":
    {
    "projectId": "00000000-0000-0000-0000-000000000000",
    "iterationId": "00000000-0000-0000-0000-000000000000",
    "trainingStatus": "TrainingCompleted"
    }
}
```

`"trainingStatus"` 필드는 `"TrainingCompleted"` 또는 `"TrainingFailed"` 중 하나일 수 있습니다. `"iterationId"` 필드는 학습된 모델의 ID입니다.

## <a name="get-model-export-backups"></a>모델 내보내기 백업 가져오기

준비가 되면 [ExportIteration](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Training_3.3/operations/5eb0bcc6548b571998fddece) API를 호출하여 학습된 모델을 지정된 플랫폼으로 내보냅니다.

지정된 스토리지 컨테이너에 내보낸 모델의 백업 복사본이 표시됩니다. BLOB 이름의 형식은 다음과 같습니다.

```
{projectId} - {iterationId}.{platformType}
```

또한 내보내기가 완료될 때 큐에서 알림을 받게 됩니다. 

```json
{
"version": "1.0" ,
"type": "Export",
"Content":
    {
    "projectId": "00000000-0000-0000-0000-000000000000",
    "iterationId": "00000000-0000-0000-0000-000000000000",
    "exportStatus": "ExportCompleted",
    "modelUri": {url}
    }
}
```

`"exportStatus"` 필드는 `"ExportCompleted"` 또는 `"ExportFailed"` 중 하나일 수 있습니다. `"modelUri"` 필드에는 처음에 큐 알림을 통합했다고 가정하고 컨테이너에 저장된 백업 모델의 URL이 포함됩니다. 통합하지 않았다면 `"modelUri"` 필드에는 Custom Vision 모델 BLOB용 SAS URL이 표시됩니다.

## <a name="next-steps"></a>다음 단계

이 가이드에서는 Custom Vision 리소스 간에 프로젝트를 복사하고 이동하는 방법을 알아보았습니다. 다음으로 API 참조 문서를 탐색하여 Custom Vision으로 수행할 수 있는 다른 작업을 살펴봅니다.
* [REST API 참조 설명서(학습)](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Training_3.3/operations/5eb0bcc6548b571998fddeb3)
* [REST API 참조 설명서(예측)](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Prediction_3.1/operations/5eb37d24548b571998fde5f3)