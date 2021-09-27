---
title: Azure Media Services v3 API에서 얼굴 찾기 및 편집 | Microsoft Docs
description: Azure Media Services v3은 비디오 파일을 제출하고, 얼굴을 감지하고, 단일 결합 단계로 또는 편집이 가능하도록 2단계 작업을 통해 선택적으로 편집(흐리게 처리)할 수 있도록 얼굴 감지 및 편집(흐리게) 프리셋을 제공합니다. 이 문서에서는 v3 API에서 Face Detector 사전 설정을 사용하여 얼굴을 찾고 편집하는 방법을 보여줍니다.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 03/25/2021
ms.author: johndeu
ms.custom: devx-track-csharp
ms.openlocfilehash: 2c434720863a7ecff4192720874547f6f8c2e8ed
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128621417"
---
# <a name="find-and-redact-blur-faces-with-the-face-detector-preset"></a>Face Detector 사전 설정을 사용하여 얼굴 찾기 및 편집(흐리게)

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Azure Media Services v3 API에는 클라우드에서 확장성 있는 얼굴 감지 및 편집(흐리게 처리)을 제공하는 Face Detector 사전 설정이 포함되어 있습니다. 얼굴 편집을 사용하면 선택한 개인의 얼굴을 흐리게 표시하기 위해 동영상을 수정할 수 있습니다. 공공 안전과 새 미디어 시나리오를 위해 얼굴 편집 서비스를 사용할 수 있습니다. 짧은 장면이라도 여러 명의 얼굴이 포함된 경우 수동으로 편집하려면 많은 시간이 걸릴 수 있지만 이 사전 설정을 사용하면 몇 번의 간단한 단계를 통해 얼굴을 편집할 수 있습니다.

이 문서에서는 **Face Detector 사전 설정** 에 대한 세부 정보를 제공하고 .NET용 Azure Media Services SDK와 함께 사용하는 방법을 보여줍니다.

## <a name="compliance-privacy-and-security"></a>규정 준수, 프라이버시 및 보안

Azure Media Services에서 분석을 사용할 때 적용되는 모든 법률을 준수해야 합니다. 타인의 권리를 침해하는 방식으로 Azure Media Services 또는 다른 Azure 서비스를 사용하면 안 됩니다. 생체 인식 데이터를 비롯한 비디오를 처리하고 저장하기 위해 Azure Media Services 서비스에 업로드하려면 비디오에 나오는 모든 사람의 적절한 동의를 포함하여 적절한 권한이 모두 있어야 합니다. Azure Media Services의 규정 준수, 개인 정보 보호 및 보안에 대해 알아보려면 Azure [Cognitive Services 사용 약관](https://azure.microsoft.com/support/legal/cognitive-services-compliance-and-privacy/)을 참조하세요. Microsoft의 개인 정보 보호 의무 및 데이터 처리에 대한 내용은 Microsoft의 [개인정보처리방침](https://privacy.microsoft.com/PrivacyStatement), [OST(온라인 서비스 사용 약관)](https://www.microsoft.com/licensing/product-licensing/products) 및 [“DPA”(데이터 처리 추록)](https://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=67)를 검토하세요. 데이터 보존, 삭제/소멸을 비롯한 추가 개인 정보 취급 방침은 OST 및 [여기](../../azure-video-analyzer/video-analyzer-for-media-docs/faq.yml)에서 확인할 수 있습니다. Azure Media Services를 사용하는 것은 Cognitive Services 사용 약관, OST, DPA 및 개인정보처리방침을 따르는 것에 동의하는 것입니다.

## <a name="face-redaction-modes"></a>얼굴 편집 모드

얼굴 편집은 동일한 개인이 다른 각도에서도 흐리게 표시될 수 있도록 동영상의 모든 프레임에서 얼굴을 감지하고 앞뒤 시간의 얼굴 개체를 추적합니다. 자동화된 편집 프로세스는 복잡하며 항상 모든 얼굴을 100% 흐릿하게 편집하지는 않습니다. 이러한 이유로, 최종 흐림 단계에 대한 파일을 제출하기 전에 편집 단계를 통해 흐림 효과의 품질 및 정확도를 향상시키기 위해 기본 설정에서 2단계 모드를 사용할 수 있습니다. 

완전 자동 **결합된** 모드 외에도 2단계 워크플로를 사용하면 얼굴 ID 목록을 통해 흐리게 처리하거나 흐리게 처리하지 않으려는 얼굴을 선택할 수 있습니다. 프레임별 조정을 임의로 수행하기 위해 사전 설정에서는 JSON 형식의 메타데이터 파일을 두 번째 단계에 대한 입력으로 사용합니다. 이 워크플로는 **분석** 및 **편집** 모드로 분할됩니다.

두 모드를 하나의 작업에서 두 작업을 실행하는 단일 단계로 결합할 수 있습니다. 이러한 모드를 **결합된** 모드라고 합니다.  이 문서에서 샘플 코드는 샘플 소스 파일에 대해 간소화된 단일 경로 **결합된** 모드를 사용하는 방법을 보여줍니다.

### <a name="combined-mode"></a>결합된 모드

이렇게 하면 필요한 JSON 파일을 수동으로 편집하지 않고 편집된 MP4 비디오 파일이 단일 단계에 생성됩니다. 작업의 자산 폴더에 있는 출력은 선택한 흐림 효과를 사용하여 흐리게 처리된 얼굴을 포함하는 단일 .mp4 파일이 됩니다. **SourceResolution** 으로 설정된 resolution 속성을 사용하면 최상의 편집 결과를 얻을 수 있습니다.

| 단계 | 파일 이름 | 메모 |
| --- | --- | --- |
| 입력 자산 |"ignite-sample.mp4" |WMV, MOV 또는 MP4 형식의 동영상 |
| 사전 설정 구성 |Face Detector 구성 | **mode**: FaceRedactorMode.Combined,  **blurType**: BlurType.Med, **resolution**: AnalysisResolution.SourceResolution |
| 출력 자산 |"ignite-redacted.mp4 |얼굴에 흐림 효과가 적용된 비디오 |

### <a name="analyze-mode"></a>분석 모드

2단계 워크플로의 **분석** 단계는 동영상 입력을 사용하여 얼굴 위치 목록이 있는, 얼굴 ID 및 JSON 파일과 검색된 각 얼굴의 jpg 이미지를 생성합니다.
얼굴 id의 후속 실행은 분석 패스의 후속 실행에서 동일 하지 않을 것을 권장 합니다.

| 단계 | 파일 이름 | 메모 |
| --- | --- | --- |
| 입력 자산 |"ignite-sample.mp4" |WMV, MPV 또는 MP4 형식의 동영상 |
| 사전 설정 구성 |Face Detector 구성 |**mode**: FaceRedactorMode.Analyze, **resolution**: AnalysisResolution.SourceResolution|
| 출력 자산 |ignite-sample_annotations.json |얼굴 위치의 주석 데이터(JSON 형식). 얼굴 id는 이후 분석 패스의 후속 실행에서 동일 하지 않을 수 있습니다. 사용자가 흐리게 표시하는 테두리 상자를 수정하기 위해 편집할 수 있습니다. 아래 샘플을 참조하세요. |
| 출력 자산 |foo_thumb%06d.jpg [foo_thumb000001.jpg, foo_thumb000002.jpg] |검색된 각 얼굴을 잘라낸 jpg(숫자는 얼굴의 레이블 ID) |

#### <a name="output-example"></a>출력 예제

```json
{
  "version": 1,
  "timescale": 24000,
  "offset": 0,
  "framerate": 23.976,
  "width": 1280,
  "height": 720,
  "fragments": [
    {
      "start": 0,
      "duration": 48048,
      "interval": 1001,
      "events": [
        [],
        [],
        [],
        [],
        [],
        [],
        [],
        [],
        [],
        [],
        [],
        [],
        [],
        [
          {
            "index": 13,
            "id": 1138,
            "x": 0.29537,
            "y": -0.18987,
            "width": 0.36239,
            "height": 0.80335
          },
          {
            "index": 13,
            "id": 2028,
            "x": 0.60427,
            "y": 0.16098,
            "width": 0.26958,
            "height": 0.57943
          }
        ],

    ... truncated
```

### <a name="redact-blur-mode"></a>편집(흐리게) 모드

워크플로의 두 번째 단계에서는 하나의 자산으로 결합해야 하는 여러 입력을 사용합니다.

여기에는 흐리게 표시할 ID 목록, 원본 동영상 및 주석 JSON이 포함됩니다. 이 모드에서는 주석을 사용하여 입력 동영상에 흐리게 표시를 적용합니다.

분석 단계의 출력에는 원본 동영상이 포함되지 않습니다. 동영상을 편집 모드 작업의 입력 자산으로 업로드하고 기본 파일로 선택해야 합니다.

| 단계 | 파일 이름 | 메모 |
| --- | --- | --- |
| 입력 자산 |"ignite-sample.mp4" |WMV, MPV 또는 MP4 형식의 동영상. 1단계와 동일한 동영상입니다. |
| 입력 자산 |"ignite-sample_annotations.json" |1단계의 주석 메타데이터 파일로, 얼굴을 흐릿하게 변경하려는 경우 선택적으로 수정합니다. 외부 애플리케이션, 코드 또는 텍스트 편집기에서 편집해야 합니다. |
| 입력 자산 | "ignite-sample_IDList.txt"(선택 사항) | 줄로 구분된 편집할 얼굴 ID의 새 목록(선택 사항). 비워 두는 경우 원본의 모든 얼굴에 흐림을 적용합니다. 이 목록을 사용하여 특정 얼굴을 흐리게 처리하지 않도록 선택할 수 있습니다. |
| Face Detector 사전 설정  |사전 설정 구성  | **mode**: FaceRedactorMode.Redact, **blurType**: BlurType.Med |
| 출력 자산 |"ignite-sample-redacted.mp4" |주석을 기반으로 흐리게 표시하기가 적용된 동영상 |

#### <a name="example-output"></a>예제 출력

IDList에서 하나의 ID가 선택된 출력입니다.
얼굴 id는 이후 분석 패스의 후속 실행에서 동일 하지 않을 수 있습니다.

예제 foo_IDList.txt

```
1
2
3
```

## <a name="blur-types"></a>흐리게 형식

**결합된** 또는 **편집** 모드에서 JSON 입력 구성을 통해 선택할 수 있는 5가지 흐리게 모드가 있습니다. **낮음**, **중간**, **높음**, **상자** 및 **검정**. 기본적으로 **중간** 이 사용됩니다.

아래에 흐리게 형식의 샘플을 확인할 수 있습니다.

#### <a name="low"></a>낮음

![낮은 해상도의 흐리게 설정 예제입니다.](./media/media-services-face-redaction/blur-1.png)

#### <a name="med"></a>중간

![중간 해상도의 흐리게 설정 예제입니다.](./media/media-services-face-redaction/blur-2.png)

#### <a name="high"></a>높음

![높은 해상도의 흐리게 설정 예제입니다.](./media/media-services-face-redaction/blur-3.png)

#### <a name="box"></a>Box

![출력 디버깅에 사용할 상자 모드입니다.](./media/media-services-face-redaction/blur-4.png)

#### <a name="black"></a>흑인

![모든 얼굴을 검은색 상자로 가리는 검은색 상자 모드입니다.](./media/media-services-face-redaction/blur-5.png)

## <a name="elements-of-the-output-json-file"></a>출력 JSON 파일의 요소

편집 MP는 한 동영상 프레임 내에서 최대 64명의 얼굴을 검색할 수 있는 고정밀도 얼굴 위치 검색 및 추적을 제공합니다. 정면이 최상의 결과를 제공하며 측면 또는 작은 얼굴(24x24 픽셀보다 작거나 같음)의 경우에는 어려울 수 있습니다.

[!INCLUDE [media-services-analytics-output-json](../../../includes/media-services-analytics-output-json.md)]

## <a name="net-sample-code"></a>.NET 샘플 코드

다음 프로그램에서는 **결합된** 단일 단계 편집 모드를 사용하는 방법을 보여줍니다.

- 자산을 만들고 미디어 파일을 자산에 업로드합니다.
- 모드 및 blurType 설정을 사용하는 Face Detector 사전 설정을 구성합니다.
- Face Detector 사전 설정을 사용하여 새로운 Transform 만들기
- 출력 편집된 비디오 파일을 다운로드합니다.

## <a name="download-and-configure-the-sample"></a>샘플 다운로드 및 구성

다음 명령을 사용하여 .NET 샘플이 포함된 GitHub 리포지토리를 컴퓨터에 복제합니다.  

 ```bash
 git clone https://github.com/Azure-Samples/media-services-v3-dotnet.git
 ```

샘플은 [FaceRedactor](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/main/VideoAnalytics/FaceRedactor) 폴더에 있습니다. 다운로드한 프로젝트에서 [appsettings.json](https://github.com/Azure-Samples/media-services-v3-dotnet/blob/main/VideoAnalytics/FaceRedactor/appsettings.json) 파일을 엽니다. 값을 [API에 액세스](./access-api-howto.md)하여 가져온 자격 증명으로 바꿉니다.

**선택적으로** 리포지토리의 루트에서 **[sample.env](https://github.com/Azure-Samples/media-services-v3-dotnet/blob/main/sample.env)** 파일을 복사하고 세부 정보를 입력한 다음, 해당 파일의 이름을 **.env**(앞에 점이 있어야 합니다!)로 변경하여 리포지토리의 모든 샘플 프로젝트에서 사용할 수 있도록 합니다.  이렇게 하면 각 샘플에서 appsettings.json 파일을 입력할 필요가 없고, 자체 Git 허브 복제 리포지토리에 대한 설정 확인으로부터 보호를 받습니다.

#### <a name="examples"></a>예

이 코드는 **결합된** 모드에서 **FaceDetectorPreset** 을 설정하는 방법을 보여줍니다.

[!code-csharp[Main](../../../media-services-v3-dotnet/VideoAnalytics/FaceRedactor/Program.cs#FaceDetectorPreset)]

이 코드 샘플은 만드는 도중 사전 설정이 Transform 개체로 전달되는 방법을 보여줍니다. Transform을 만든 후에는 여기에 작업을 직접 제출할 수 있습니다.

[!code-csharp[Main](../../../media-services-v3-dotnet/VideoAnalytics/FaceRedactor/Program.cs#FaceDetectorPresetTransform)]

## <a name="next-steps"></a>다음 단계

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>피드백 제공

[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
