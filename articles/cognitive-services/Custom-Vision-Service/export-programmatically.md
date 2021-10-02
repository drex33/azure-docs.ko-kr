---
title: 프로그래밍 방식으로 모델 내보내기
titleSuffix: Azure Cognitive Services
description: Custom Vision 클라이언트 라이브러리를 사용하여 학습된 모델을 내보냅니다.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: conceptual
ms.date: 06/28/2021
ms.author: pafarley
ms.openlocfilehash: b67c4e6165b90a580a3a29583bdfe93a98d327c8
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/01/2021
ms.locfileid: "129361389"
---
# <a name="export-a-model-programmatically"></a>프로그래밍 방식으로 모델 내보내기

[Custom Vision 웹 사이트](https://www.customvision.ai/)에서 사용할 수 있는 모든 내보내기 옵션은 클라이언트 라이브러리를 통해 프로그래밍 방식으로 수행할 수도 있습니다. 로컬 디바이스에서 사용하는 모델 반복을 재학습 및 업데이트하는 프로세스를 완전히 자동화할 수 있도록 이 작업을 수행할 수 있습니다.

이 가이드에서는 Python SDK를 사용하여 모델을 ONNX 파일로 내보내는 방법을 보여 줍니다.

## <a name="create-a-training-client"></a>학습 클라이언트 만들기

모델 반복을 내보낼 [CustomVisionTrainingClient](/python/api/azure-cognitiveservices-vision-customvision/azure.cognitiveservices.vision.customvision.training.customvisiontrainingclient) 개체가 있어야 합니다. Custom Vision 학습 리소스 Azure 엔드포인트 및 구독 키에 대한 변수를 만들고 이를 사용하여 클라이언트 개체를 만듭니다.

```python
ENDPOINT = "PASTE_YOUR_CUSTOM_VISION_TRAINING_ENDPOINT_HERE"
training_key = "PASTE_YOUR_CUSTOM_VISION_TRAINING_SUBSCRIPTION_KEY_HERE"

credentials = ApiKeyCredentials(in_headers={"Training-key": training_key})
trainer = CustomVisionTrainingClient(ENDPOINT, credentials)
```

> [!IMPORTANT]
> 완료되면 코드에서 키를 제거하고 공개적으로 게시하지 마세요. 프로덕션의 경우 자격 증명을 안전하게 저장하고 액세스하는 방법을 사용하는 것이 좋습니다. 자세한 내용은 [Cognitive Services 보안](../cognitive-services-security.md) 문서를 참조하세요.

## <a name="call-the-export-method"></a>내보내기 메서드 호출

**export_iteration** 메서드를 호출합니다.
* 내보낼 모델의 프로젝트 ID, 반복 ID를 제공합니다. 
* *platform* 매개 변수는 내보낼 플랫폼을 지정합니다. 허용된 값은 `CoreML`, `TensorFlow`, `DockerFile`, `ONNX`, `VAIDK` 및 `OpenVino`입니다. 
* *flavor* 매개 변수는 내보낼 모델의 형식을 지정합니다. 허용된 값은 `Linux`, `Windows`, `ONNX10`, `ONNX12`, `ARM`, `TensorFlowNormal` 및 `TensorFlowLite`입니다.
* *raw* 매개 변수는 개체 모델 응답과 함께 원시 JSON 응답을 검색하는 옵션을 제공합니다.

```python
project_id = "PASTE_YOUR_PROJECT_ID"
iteration_id = "PASTE_YOUR_ITERATION_ID"
platform = "ONNX"
flavor = "ONNX10"
export = trainer.export_iteration(project_id, iteration_id, platform, flavor, raw=False)
```

자세한 내용은 **[export_iteration](/python/api/azure-cognitiveservices-vision-customvision/azure.cognitiveservices.vision.customvision.training.operations.customvisiontrainingclientoperationsmixin#export-iteration-project-id--iteration-id--platform--flavor-none--custom-headers-none--raw-false----operation-config-)** 메서드를 참조하세요.

## <a name="download-the-exported-model"></a>내보낸 모델 다운로드

다음으로 **get_exports** 메서드를 호출하여 내보내기 작업의 상태를 확인합니다. 작업이 비동기적으로 실행되므로 작업이 완료될 때까지 이 메서드를 폴링해야 합니다. 완료되면 디바이스에 모델 반복을 다운로드할 수 있는 URI를 검색할 수 있습니다.

```python
while (export.status == "Exporting"):
    print ("Waiting 10 seconds...")
    time.sleep(10)
    exports = trainer.get_exports(project_id, iteration_id)
    # Locate the export for this iteration and check its status  
    for e in exports:
        if e.platform == export.platform and e.flavor == export.flavor:
            export = e
            break
    print("Export status is: ", export.status)
```

자세한 내용은 **[get_exports](/python/api/azure-cognitiveservices-vision-customvision/azure.cognitiveservices.vision.customvision.training.operations.customvisiontrainingclientoperationsmixin#get-exports-project-id--iteration-id--custom-headers-none--raw-false----operation-config-)** 메서드를 참조하세요.

그런 다음, 내보낸 모델을 디바이스의 위치에 프로그래밍 방식으로 다운로드할 수 있습니다.

```python
if export.status == "Done":
    # Success, now we can download it
    export_file = requests.get(export.download_uri)
    with open("export.zip", "wb") as file:
        file.write(export_file.content)
```

## <a name="next-steps"></a>다음 단계

다음 문서 또는 샘플 중 하나를 탐색하여 내보낸 모델을 애플리케이션에 통합합니다.

* [Python에서 Tensorflow 모델 사용](export-model-python.md)
* [Windows Machine Learning에서 ONNX 모델 사용](custom-vision-onnx-windows-ml.md)
* Swift를 사용한 실시간 이미지 분류를 위한 [iOS 애플리케이션 내 CoreML 모델](https://go.microsoft.com/fwlink/?linkid=857726) 샘플을 참조하세요.
* Android에서 실시간 이미지 분류를 위한 [Android 애플리케이션 내 Tensorflow 모델](https://github.com/Azure-Samples/cognitive-services-android-customvision-sample) 샘플을 참조하세요.
* Xamarin iOS 앱에서 실시간 이미지 분류를 위한 [Xamarin을 사용하는 CoreML 모델](https://github.com/xamarin/ios-samples/tree/master/ios11/CoreMLAzureModel) 샘플을 참조하세요.