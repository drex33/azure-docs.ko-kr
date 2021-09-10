---
title: 이미지 오버레이 만드는 방법
description: 이미지 오버레이 만드는 방법 알아보기
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: how-to
ms.date: 08/31/2020
ms.openlocfilehash: 532e26e486e758b3fd8079f6f61f3e00e82dbdca
ms.sourcegitcommit: c2f0d789f971e11205df9b4b4647816da6856f5b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/23/2021
ms.locfileid: "122662308"
---
# <a name="how-to-create-an-image-overlay"></a>이미지 오버레이 만드는 방법

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Media Services를 사용하면 비디오 위에 이미지, 오디오 파일 또는 다른 비디오를 오버레이할 수 있습니다. 입력은 이미지 파일을 하나만 지정해야 합니다. 이미지 파일은 JPG, PNG, GIF 또는 BMP 형식 또는 오디오 파일(예를 들어 WAV, MP3, WMA, M4A 파일) 또는 비디오 파일에 지원하는 파일 형식으로 지정할 수 있습니다.


## <a name="prerequisites"></a>필수 조건

* 샘플에서 *appsettings.json* 파일을 구성하는 데 필요한 계정 정보를 수집합니다. 이 작업을 수행하는 방법을 잘 모르겠다면 [빠른 시작: Microsoft ID 플랫폼을 사용하여 애플리케이션 등록](../../active-directory/develop/quickstart-register-app.md)을 참조하세요. *appsettings.json* 파일에는 다음 값이 필요합니다.

```json
    {
    "AadClientId": "",
    "AadEndpoint": "https://login.microsoftonline.com",
    "AadSecret": "",
    "AadTenantId": "",
    "AccountName": "",
    "ArmAadAudience": "https://management.core.windows.net/",
    "ArmEndpoint": "https://management.azure.com/",
    "Location": "",
    "ResourceGroup": "",
    "SubscriptionId": ""
    }
```

변환 생성에 익숙하지 않은 경우 다음 작업을 완료하는 것이 좋습니다.

* [Media Services를 사용하여 비디오 및 오디오 인코딩](encode-concept.md) 읽기
* [사용자 지정 변환을 사용하여 인코딩하는 방법 - .NET](transform-custom-presets-how-to.md)을 읽어보세요. 해당 문서의 단계에 따라 변환 작업에 필요한 .NET을 설정하고 여기로 돌아와 오버레이 미리 설정 샘플을 사용해 보세요.
* [변환 참조 문서](/rest/api/media/transforms)를 참조하세요.

변환에 익숙한 경우 오버레이 샘플을 다운로드합니다.

## <a name="overlays-preset-sample"></a>오버레이 미리 설정 샘플

Media Services .NET 샘플 리포지토리를 복제합니다.

```bash
    git clone https://github.com/Azure-Samples/media-services-v3-dotnet.git
```

솔루션 폴더로 이동하여 Visual Studio Code 또는 Visual Studio 2019를 시작합니다.

VideoEncoding 폴더에서 다양한 인코딩 샘플을 사용할 수 있습니다. [VideoEncoding/Encoding_OverlayImage](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/main/VideoEncoding/Encoding_OverlayImage) 솔루션 폴더에서 프로젝트를 열어 오버레이 사용 방법을 알아보세요.

샘플 프로젝트에는 두 개의 미디어 파일이 포함되어 있습니다. 비디오 파일에 오버레이 할 비디오 파일 및 로고 이미지입니다.
* ignite.mp4
* cloud.png

이 샘플에서는 먼저 CreateCustomTransform 메서드의 비디오 위에 이미지를 오버레이할 수 있는 사용자 지정 변환을 만듭니다.  *[StandardEncoderPreset](/rest/api/media/transforms/create-or-update#standardencoderpreset)* 의 *[Filters](/rest/api/media/transforms/create-or-update#filters)* 속성을 사용하여 비디오 오버레이 설정을 포함하는 새 필터 컬렉션을 할당합니다.

[VideoOverlay](/rest/api/media/transforms/create-or-update#videooverlay) 작업에 제출된 작업 입력 파일 목록에서 매핑하고 오버레이 이미지 또는 비디오로 사용할 올바른 입력 소스 파일을 찾는 데 필요한 *InputLabel* 이라는 속성이 포함되어 있습니다.  작업을 제출할 때 이 동일한 레이블 이름이 변환의 설정과 일치시키는 데 사용됩니다. 샘플에서는 문자열 상수 *OverlayLabel* 에 표시된 대로 “로고”의 레이블 이름을 사용하고 있습니다.

다음 코드 조각에는 오버레이를 사용하도록 변환의 형식을 지정하는 방법이 나타나 있습니다.

```csharp
new TransformOutput
                {
                    Preset = new StandardEncoderPreset
                    {
                        Filters = new Filters
                        {
                            Overlays = new List<Overlay>
                            {
                                new VideoOverlay
                                {
                                    InputLabel = OverlayLabel,   // same as the one used in the JobInput to identify which asset is the overlay image
                                    Position = new Rectangle( "1200","670") // left, top position of the overlay in absolute pixel position relative to the source videos resolution. 
    
                                }
                            }
                        },
                        Codecs = new List<Codec>
                        {
                            new AacAudio
                            {
                            },
                            new H264Video
                            {
                                KeyFrameInterval = TimeSpan.FromSeconds(2),
                                Layers = new List<H264Layer>
                                {
                                    new H264Layer
                                    {
                                        Profile = H264VideoProfile.Baseline,
                                        Bitrate = 1000000, // 1Mbps
                                        Width = "1280",
                                        Height = "720"
                                    },
                                    new H264Layer   // Adding a second layer to see that the image also is scaled and positioned the same way on this layer. 
                                    {
                                        Profile = H264VideoProfile.Baseline,
                                        Bitrate = 600000, // 600 kbps
                                        Width = "480",
                                        Height = "270"
                                    }
                                }
                            }
                        },
                        Formats = new List<Format>
                        {
                            new Mp4Format
                            {
                                FilenamePattern = "{Basename}_{Bitrate}{Extension}",
                            }
                        }
                    }
                }
```

변환에 작업을 제출할 때는 먼저 두 개의 입력 자산을 만들어야 합니다.

* 자산 1 - 이 샘플에서 만든 첫 번째 자산은 로컬 비디오 파일 “ignite.mp4”입니다. 이 비디오는 복합의 배경으로 사용하고 위에 로고 이미지를 오버레이합니다. 
* 자산 2 - 이 샘플에서 두 번째 자산(overlayImageAsset 변수에 저장)에는 로고에 사용할 .png 파일이 포함되어 있습니다. 이 이미지는 인코딩하는 동안 비디오에 배치됩니다.

작업이 *SubmitJobAsync* 메서드 안에 만들어지면 먼저 목록<> 개체를 사용하여 JobInput 배열을 생성합니다.  목록에는 두 원본 자산에 대한 참조가 포함됩니다.

위의 변환에 정의된 필터에서 오버레이로 사용할 입력 자산을 확인하고 일치시키기 위해 “로고” 레이블 이름을 다시 사용하여 일치를 처리합니다. 레이블 이름은 .png 이미지의 JobInputAsset에 추가됩니다. 그러면 오버레이 작업을 수행할 때 어떤 자산을 사용할지를 변환에 알릴 수 있습니다. 오버레이하려는 다양한 로고 또는 그래픽이 포함된 Media Services에 저장된 다양한 자산으로 이 동일한 변환을 다시 사용하고, 일치시킬 변환에 동일한 레이블 이름 “로고”를 사용하는 동안 작업에 전달된 자산 이름을 변경할 수 있습니다.

``` csharp
    // Add both the Video and the Overlay image assets here as inputs to the job.
    List<JobInput> jobInputs = new List<JobInput>() {
        new JobInputAsset(assetName: inputAssetName),
        new JobInputAsset(assetName: overlayAssetName, label: OverlayLabel)
    };
```

Visual Studio Code 실행 및 디버그 창에서 프로젝트를 선택하여 샘플을 실행합니다. 샘플은 인코딩 작업의 진행 상황을 출력하고 마지막으로 프로젝트 루트의 /Output 폴더에 콘텐츠를 다운로드합니다. 또는 콘텐츠가 전체 Visual Studio 경우 /bin/Output 폴더에 있을 수 있습니다. 

또한 샘플은 스트리밍 콘텐츠를 게시하고 호환되는 플레이어에서 사용할 수 있는 전체 HLS, DASH 및 부드러운 스트리밍 매니페스트 파일 URL을 출력합니다.  매니페스트 URL을 [Azure Media Player 데모](http://ampdemo.azureedge.net/)에 쉽게 복사하고 /manifest로 끝나는 URL을 URL 상자에 붙여넣은 다음, ‘플레이어 업데이트’를 클릭합니다.

## <a name="api-references"></a>API 참조

* [VideoOverlay 개체](/rest/api/media/transforms/create-or-update#videooverlay)
* [필터](/rest/api/media/transforms/create-or-update#filters)
* [StandardEncoderPreset](/rest/api/media/transforms/create-or-update#standardencoderpreset)


[!INCLUDE [reference dotnet sdk references](./includes/reference-dotnet-sdk-references.md)]

## <a name="next-steps"></a>다음 단계

[!INCLUDE [transforms next steps](./includes/transforms-next-steps.md)]
