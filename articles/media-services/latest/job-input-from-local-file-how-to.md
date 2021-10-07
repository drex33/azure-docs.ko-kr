---
title: 로컬 파일에서 작업 입력 만들기
description: 이 토픽에서는 로컬 파일에서 Azure Media Services 작업 입력을 만드는 방법을 보여 줍니다.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: how-to
ms.date: 05/25/2021
ms.author: inhenkel
ms.openlocfilehash: fa8b26c2f26ed93bda0ba96b695e94951b72af6b
ms.sourcegitcommit: e82ce0be68dabf98aa33052afb12f205a203d12d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/07/2021
ms.locfileid: "129658218"
---
# <a name="create-a-job-input-from-a-local-file"></a>로컬 파일에서 작업 입력 만들기

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Media Services v3에서는, 비디오를 처리할 작업을 제출할 때 입력 비디오를 찾을 위치를 Media Services에 알려줘야 합니다. 입력 비디오는 Media Service 자산으로 저장할 수 있으며 이 경우 파일(로컬 또는 Azure Blob Storage에 저장됨)을 기반으로 입력 자산을 만듭니다. 이 항목에서는 로컬 파일에서 작업 입력을 만드는 방법을 보여줍니다. 전체 예제를 보려면 [GitHub 샘플](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/main/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs)을 참조하세요.

## <a name="prerequisites"></a>사전 요구 사항

* [Media Services 계정 만들기](./account-create-how-to.md)

## <a name="net-sample"></a>.NET 샘플

다음 코드는 입력 자산을 만들어서 작업에 대한 입력으로 사용하는 방법을 보여줍니다. CreateInputAsset 함수는 다음 작업을 수행합니다.

* 자산 만들기
* [스토리지에 있는 자산 컨테이너](../../storage/blobs/storage-quickstart-blobs-dotnet.md#upload-a-blob-to-a-container)에 대해 쓰기가 가능한 [SAS URL](../../storage/common/storage-sas-overview.md) 가져오기
* SAS URL을 사용하여 스토리지의 컨테이너에 파일 업로드

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#CreateInputAsset)]

다음 코드 조각에서는 출력 자산이 아직 없는 경우 새로 만듭니다.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#CreateOutputAsset)]

다음 코드 조각은 인코딩 작업을 제출합니다.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#SubmitJob)]

## <a name="job-error-codes"></a>작업 오류 코드

[오류 코드](/rest/api/media/jobs/get#joberrorcode)를 참조하세요.

## <a name="next-steps"></a>다음 단계

[HTTP URL에서 작업 입력 만들기](job-input-from-http-how-to.md)
