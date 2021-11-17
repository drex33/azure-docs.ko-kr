---
title: Node.js를 사용하여 비디오 파일을 인코딩하고 스트림하는 방법
description: Node.js를 사용하여 비디오 파일을 스트림하는 방법입니다. 이 자습서의 단계에 따라 새로운 Azure Media Services 계정을 만들고, 파일을 인코딩한 다음, Azure Media Player로 스트리밍합니다.
services: media-services
author: IngridAtMicrosoft
manager: femila
editor: ''
keywords: Azure Media Services, 스트림, Node.js
ms.service: media-services
ms.workload: media
ms.topic: tutorial
ms.date: 02/17/2021
ms.author: inhenkel
ms.openlocfilehash: f8eafe88168f908025321d1e3ce6304fa34c03ab
ms.sourcegitcommit: c434baa76153142256d17c3c51f04d902e29a92e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/10/2021
ms.locfileid: "132179951"
---
# <a name="how-to-encode-and-stream-video-files-with-nodejs"></a>Node.js를 사용하여 비디오 파일을 인코딩하고 스트림하는 방법

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

이 빠른 시작은 Azure Media Services를 사용하여 다양한 브라우저 및 디바이스에서 비디오 스트리밍을 인코딩하고 시작하는 것이 얼마나 쉬운지 보여 줍니다. 입력 비디오 파일은 HTTPS URL, SAS URL 또는 Azure Blob 스토리지에 있는 파일의 경로를 사용하여 지정할 수 있습니다.

이 빠른 시작이 완료되면 다음 방법을 알 수 있습니다.

- Node.js를 사용하여 인코딩하는 방법
- Node.js를 사용하여 스트림하는 방법
- Node.js를 사용하여 HTTPS URL에서 파일을 업로드하는 방법
- Node.js에서 HLS 또는 DASH 클라이언트 플레이어를 사용하는 방법

이 문서의 샘플에서는 콘텐츠를 인코딩하여 HTTPS URL을 통해 액세스할 수 있게 만듭니다. 현재 AMS v3은 HTTPS URL을 통한 청크 분할 전송 인코딩을 지원하지 않습니다.

![비디오 재생](./media/stream-files-nodejs-quickstart/final-video.png)

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>사전 요구 사항

- [Node.js](https://nodejs.org/en/download/)
- [Media Services 계정 만들기](./create-account-howto.md)<br/>리소스 그룹 이름 및 Media Services 계정 이름에 사용한 값을 기억해 두세요.
- [Azure CLI를 사용하여 Azure Media Services API 액세스](./access-api-howto.md)의 단계를 수행하고 자격 증명을 저장합니다. API에 액세스할 때 필요합니다.
- 먼저 Node.js 클라이언트 SDK를 사용하는 방법을 이해하기 위해 [Node.js를 사용하여 구성 및 연결](./configure-connect-nodejs-howto.md) 방법을 안내합니다.

## <a name="download-and-configure-the-sample"></a>샘플 다운로드 및 구성

다음 명령을 사용하여 스트리밍 Node.js 샘플이 포함된 GitHub 리포지토리를 머신에 복제합니다.  

 ```bash
 git clone https://github.com/Azure-Samples/media-services-v3-node-tutorials.git
 ```

샘플은 [StreamFilesSample](https://github.com/Azure-Samples/media-services-v3-node-tutorials/tree/master/AMSv3Samples/StreamFilesSample) 폴더에 있습니다.

다운로드한 프로젝트에서 [index.ts](https://github.com/Azure-Samples/media-services-v3-node-tutorials/blob/master/AMSv3Samples/StreamFilesSample/index.ts)를 엽니다. [API에 액세스](./access-api-howto.md)하여 얻은 값과 자격 증명을 사용하여 루트 폴더의 *sample.env* 파일을 업데이트합니다. *sample.env* 파일의 이름을 *.env* 로 바꿉니다(예, 확장명만).

샘플은 다음 작업을 수행합니다.

1. [콘텐츠 인식 인코딩 사전 설정](./encode-content-aware-concept.md)을 사용하여 **변환** 을 만듭니다. 먼저 지정된 변환이 있는지 확인합니다.
1. 인코딩 **작업** 에서 출력을 포함하는 데 사용되는 출력 **자산** 을 만듭니다.
1. 필요에 따라 Storage Blob SDK를 사용하여 로컬 파일을 업로드합니다.
1. HTTPS URL 또는 업로드된 파일을 기반으로 하는 **작업** 입력을 만듭니다.
1. 이전에 만든 입력 및 출력을 사용하여 인코딩 **작업** 을 제출합니다.
1. 작업의 상태를 확인합니다.
1. 인코딩 작업의 출력을 로컬 폴더에 다운로드합니다.
1. 플레이어에서 사용할 **스트리밍 로케이터** 를 만듭니다.
1. HLS 및 DASH에 대한 스트리밍 URL을 빌드합니다.
1. 플레이어 애플리케이션인 Azure Media Player에서 콘텐츠를 재생합니다.

## <a name="run-the-sample"></a>샘플 실행

1. 애플리케이션에서 인코딩된 파일을 다운로드합니다. 출력 파일을 저장할 폴더를 만들고, [index.js](https://github.com/Azure-Samples/media-services-v3-node-tutorials/blob/main/AMSv3Samples/StreamFilesSample/index.ts#L59) 파일에서 **outputFolder** 변수의 값을 업데이트합니다. 기본적으로 "Temp"로 설정됩니다.
1. **명령 프롬프트** 를 열고, 샘플의 디렉터리로 이동합니다.
1. 디렉터리를 AMSv3Samples 폴더로 변경합니다.

    ```bash
    cd AMSv3Samples
    ```

1. *packages.json* 파일에서 사용되는 패키지를 설치합니다.

    ```bash
    npm install 
    ```

1. 디렉터리를 *StreamFilesSample* 폴더로 변경합니다.

    ```bash
    cd StreamFilesSample
    ```

1. *AMSv3Samples* 폴더에서 Visual Studio Code를 시작합니다. ( *.vscode* 폴더 및 *tsconfig.json* 파일이 있는 폴더에서 시작하는 데 필요합니다.)

    ```bash
    cd ..
    code .
    ```

*StreamFilesSample* 폴더를 열고, Visual Studio Code 편집기에서 *index.ts* 파일을 엽니다.
*index.ts* 파일에서 F5 키를 눌러 디버거를 시작합니다.

## <a name="test-with-azure-media-player"></a>Azure Media Player로 테스트

Azure Media Player를 사용하여 스트림을 테스트합니다. Shaka 플레이어, HLS.js, Dash.js 등과 같은 HLS 또는 DASH 호환 플레이어를 사용할 수도 있습니다.

샘플에서 생성된 링크를 클릭하고 DASH 매니페스트가 이미 로드된 AMP 플레이어를 시작할 수 있어야 합니다.

> [!NOTE]
> 플레이어가 https 사이트에 호스트 될 경우 URL을 "https"로 업데이트해야 합니다.

1. 웹 브라우저를 열고 [https://aka.ms/azuremediaplayer/](https://aka.ms/azuremediaplayer/)로 이동합니다.
2. **URL:** 상자에서 애플리케이션을 실행할 때 얻은 스트리밍 URL 값 중 하나를 붙여넣습니다. URL을 HLS, Dash 또는 Smooth 형식으로 붙여넣을 수 있으며, 디바이스에서 자동으로 재생하기 위해 Azure Media Player에서 적절한 스트리밍 프로토콜로 전환됩니다.
3. **플레이어 업데이트** 를 누릅니다.

Azure Media Player는 테스트용으로 사용할 수 있지만 프로덕션 환경에서는 사용할 수 없습니다.

## <a name="clean-up-resources"></a>리소스 정리

이 자습서를 위해 만든 Media Services 및 스토리지 계정을 비롯하여 리소스 그룹의 어떠한 리소스도 더 이상 필요하지 않은 경우 리소스 그룹을 삭제합니다.

다음 CLI 명령을 실행합니다.

```azurecli
az group delete --name amsResourceGroup
```

## <a name="more-developer-documentation-for-nodejs-on-azure"></a>Azure의 Node.js에 대한 추가 개발자 설명서

- [JavaScript 및 Node.js 개발자용 Azure](/azure/developer/javascript/)
- [@azure/azure-sdk-for-js GitHub 리포지토리의 Media Services 소스 코드](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/mediaservices/arm-mediaservices)
- [Node.js 개발자를 위한 Azure 패키지 설명서](/javascript/api/overview/azure/)

## <a name="see-also"></a>참고 항목

- [작업 오류 코드](/rest/api/media/jobs/get#joberrorcode).
- [npm install @azure/arm-mediaservices](https://www.npmjs.com/package/@azure/arm-mediaservices)
- [JavaScript 및 Node.js 개발자용 Azure](/azure/developer/javascript/)
- [@azure/azure-sdk-for-js 리포지토리의 Media Services 소스 코드](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/mediaservices/arm-mediaservices)

## <a name="next-steps"></a>다음 단계

> [Media Services 개념](concepts-overview.md)
