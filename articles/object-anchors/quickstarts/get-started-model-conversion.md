---
title: '빠른 시작: 앱에서 사용할 Object Anchors 모델 만들기'
description: 이 빠른 시작에서는 3D 모델에서 Object Anchors 모델을 만드는 방법을 알아봅니다.
author: craigktreasure
manager: virivera
ms.author: crtreasu
ms.date: 06/10/2021
ms.topic: quickstart
ms.service: azure-object-anchors
ms.custom: mode-other
ms.openlocfilehash: 378e0d9419bfa239829b4ca3fd3c83b0395e844d
ms.sourcegitcommit: 56235f8694cc5f88db3afcc8c27ce769ecf455b0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/24/2021
ms.locfileid: "133055737"
---
# <a name="quickstart-create-an-object-anchors-model-from-a-3d-model"></a>빠른 시작: 3D 모델에서 Object Anchors 모델 만들기

Azure Object Anchors는 3D 모델을 HoloLens에 대한 개체 인식 혼합 현실 환경을 지원하는 AI 모델로 변환하는 관리형 클라우드 서비스입니다. 이 빠른 시작에서는 [.NET용 Azure Object Anchors 변환 SDK](/dotnet/api/overview/azure/mixedreality.objectanchors.conversion-readme-pre)를 사용하여 3D 모델에서 Object Anchors 모델을 만드는 방법에 대해 설명합니다.

이 문서에서 배울 내용은 다음과 같습니다.

> [!div class="checklist"]
> * Object Anchors 계정을 만듭니다.
> * [.NET용 Azure Object Anchors 변환 SDK](/dotnet/api/overview/azure/mixedreality.objectanchors.conversion-readme-pre)([NuGet](https://www.nuget.org/packages/Azure.MixedReality.ObjectAnchors.Conversion/))로 3D 모델을 변환하여 Object Anchors 모델을 만듭니다.

## <a name="prerequisites"></a>사전 요구 사항

이 빠른 시작을 완료하려면 다음 항목이 있어야 합니다.

* <a href="https://www.visualstudio.com/downloads/" target="_blank">Visual Studio 2019</a>가 설치된 Windows 머신.
* <a href="https://git-scm.com" target="_blank">Windows용 Git</a>.
* <a href="https://dotnet.microsoft.com/download/dotnet-core/3.1">.NET Core 3.1 SDK</a>.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [Create Account](../../../includes/object-anchors-get-started-create-account.md)]

## <a name="get-the-sample-project"></a>샘플 프로젝트 가져오기

[!INCLUDE [Clone Sample Repo](../../../includes/object-anchors-clone-sample-repository.md)]

## <a name="convert-a-3d-model"></a>3D 모델 변환

이제 3D 모델을 변환할 수 있습니다.

1. Visual Studio에서 `quickstarts/conversion/Conversion.sln`을 엽니다. 이 솔루션에는 C# 콘솔 프로젝트가 포함되어 있습니다.

2. 프로젝트의 루트에 있는 `Configuration.cs` 파일을 열고 다음 필드에서`set-me` 값을 바꿉니다.

   | 필드         | Description                                                         |
   |---------------|---------------------------------------------------------------------|
   | AccountDomain | 위에서 만든 Object Anchors 계정의 **계정 도메인** 입니다. |
   | AccountId     | 위에서 만든 Object Anchors 계정의 **계정 ID** 입니다.     |
   | AccountKey    | 위에서 만든 Object Anchors 계정의 **기본 키** 입니다.     |

   확인해야 하는 4개의 추가 필드가 있습니다.

    | 필드                    | Description                       |
    | ---                      | ---                               |
    | InputAssetPath           | 로컬 머신의 3D 모델에 대한 절대 경로입니다. 지원되는 파일 형식은 `fbx`, `ply`, `obj`, `glb`, `gltf`입니다. |
    | AssetDimensionUnit       | 3D 모델의 측정 단위입니다. 지원되는 모든 측정 단위는 `Azure.MixedReality.ObjectAnchors.Conversion.AssetLengthUnit` 열거를 사용하여 액세스할 수 있습니다. |
    | Gravity                  | 3D 모델의 중력 벡터 방향입니다. 이 3D 벡터는 모델의 좌표계에서 아래쪽 방향을 제공합니다. 예를 들어 음수 `y`가 모델의 3D 공간에서 아래쪽 방향을 나타내는 경우 이 값은 `Vector3(0.0f, -1.0f, 0.0f)`입니다. |

3. 프로젝트를 빌드하고 실행하여 3D 모델을 업로드하고 서비스에 새 변환 작업을 등록하고 완료될 때까지 기다립니다. 작업이 완료되면 `InputAssetPath`에 지정된 파일 옆에 Object Anchors 모델이 다운로드됩니다. 다음 콘솔 출력과 비슷하게 표시됩니다.

   ```shell
    Asset   : ***********
    Gravity : ***********
    Unit    : ***********
    Attempting to upload asset...
    Attempting to create asset conversion job...
    Successfully created asset conversion job. Job ID: ***********
    Waiting for job completion...

    Asset conversion job completed successfully.
    Attempting to download result as '***********'...
    Success!
   ```

   나중에 참조할 수 있도록 **작업 ID** 를 기록해 둡니다. 디버깅 또는 문제 해결에 유용할 수 있습니다.

4. 작업이 성공적으로 완료되면 지정된 출력 위치에 `<Model-Filename-Without-Extension>_<JobID>.ou` 형식의 파일이 표시되어야 합니다. 예를 들어 3D 모델 파일 이름이 `chair.ply`이고 작업 ID가 `00000000-0000-0000-0000-000000000000`인 경우 서비스 출력 파일 이름은 `chair_00000000-0000-0000-0000-000000000000.ou`입니다.

## <a name="error-codes"></a>오류 코드
실패한 자산 변환 작업에서 발생할 수 있는 다양한 오류 코드와 각 오류를 처리하는 방법에 대한 자세한 내용은 [변환 오류 코드 페이지](..\model-conversion-error-codes.md)를 참조하세요.

[!INCLUDE [Clean-up section](../../../includes/clean-up-section-portal.md)]

## <a name="next-steps"></a>다음 단계

이 빠른 시작에서는 Object Anchors 계정을 만들고 3D 모델을 변환하여 Object Anchors 모델을 만들었습니다. 혼합 현실 앱에서 Object Anchors SDK와 모델을 통합하는 방법에 대해 알아보려면 다음 문서를 계속 진행하세요.

> [!div class="nextstepaction"]
> [Unity HoloLens](get-started-unity-hololens.md)

> [!div class="nextstepaction"]
> [MRTK를 사용한 Unity HoloLens](get-started-unity-hololens-mrtk.md)

> [!div class="nextstepaction"]
> [HoloLens DirectX](get-started-hololens-directx.md)

> [!div class="nextstepaction"]
> [변환 SDK](/dotnet/api/overview/azure/mixedreality.objectanchors.conversion-readme-pre)
