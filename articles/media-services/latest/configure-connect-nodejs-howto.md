---
title: Azure Media Services v3 API에 연결 - Node.js
description: 이 문서에서는 Node.js를 사용하여 Media Services v3 API에 연결하는 방법을 보여 줍니다.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 02/17/2021
ms.author: inhenkel
ms.custom: devx-track-js
ms.openlocfilehash: 7c6bba2084247d76a79de9ae9efb1b3ea356a5c4
ms.sourcegitcommit: 40866facf800a09574f97cc486b5f64fced67eb2
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/30/2021
ms.locfileid: "123221499"
---
# <a name="connect-to-media-services-v3-api---nodejs"></a>Media Services v3 API에 연결 - Node.js

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

이 문서에서는 서비스 주체 로그인 메서드를 사용하여 Azure Media Services v3 node.js SDK에 연결하는 방법을 보여 줍니다. *media-services-v3-node-tutorials* 샘플 리포지토리의 파일을 사용합니다. *HelloWorld-ListAssets* 샘플에는 연결한 다음 계정의 자산을 나열하는 코드가 포함되어 있습니다.

## <a name="prerequisites"></a>필수 구성 요소

- Visual Studio Code 설치.
- [Node.js](https://nodejs.org/en/download/)를 설치합니다.
- [TypeScript](https://www.typescriptlang.org/download)를 설치합니다.
- [Media Services 계정 만들기](./account-create-how-to.md) 리소스 그룹 이름과 Media Services 계정 이름을 기억해야 합니다.
- 애플리케이션에 대한 서비스 주체를 만듭니다. [액세스 API](./access-api-howto.md)를 참조하세요.<br/>**Pro 팁** 이 창을 열어 두거나 JSON 탭의 모든 항목을 메모장에 복사합니다. 
- 최신 버전의 [JavaScript용 AzureMediaServices SDK](https://www.npmjs.com/package/@azure/arm-mediaservices)를 얻어야 합니다.

> [!IMPORTANT]
> Azure Media Services [명명 규칙](media-services-apis-overview.md#naming-conventions)을 검토하여 엔터티에 대한 중요한 명명 제한 사항을 이해합니다.

## <a name="clone-the-nodejs-samples-repo"></a>Node.JS 샘플 리포지토리 복제

Azure 샘플에서 일부 파일을 사용합니다. Node.JS 샘플 리포지토리를 복제합니다.

```git
git clone https://github.com/Azure-Samples/media-services-v3-node-tutorials.git
```

## <a name="install-the-nodejs-packages"></a>Node.js 패키지를 설치합니다.

### <a name="install-azurearm-mediaservices"></a>@azure/arm-mediaservices 설치

```bash
npm install @azure/arm-mediaservices
```

### <a name="install-azurems-rest-nodeauth"></a>@azure/ms-rest-nodeauth 설치

“@azure/ms-rest-nodeauth”의 최소 버전, “^3.0.0”을 설치합니다.

```bash
npm install @azure/ms-rest-nodeauth@"^3.0.0"
```

이 예에서는 `package.json` 파일의 다음 패키지를 사용합니다.

|패키지|Description|
|---|---|
|`@azure/arm-mediaservices`|Azure Media Services SDK <br/>최신 Azure Media Services 패키지를 사용하려면 [npm install@azure/arm-mediaservices](https://www.npmjs.com/package/@azure/arm-mediaservices)을 확인하세요.|
|`@azure/ms-rest-nodeauth` | 서비스 주체나 관리 ID를 사용하는 AAD 인증에 필요|
|`@azure/storage-blob`|Storage SDK. 자산에 파일을 업로드할 때 사용합니다.|
|`@azure/ms-rest-js`| 로그인하는 데 사용합니다.|
|`@azure/storage-blob` | 인코딩을 위해 Azure Media Services의 자산에 파일을 업로드하고 다운로드하는 데 사용합니다.|
|`@azure/abort-controller`| 장기 실행 다운로드 작업 시간을 제한하기 위해 스토리지 클라이언트와 함께 사용합니다.|

### <a name="create-the-packagejson-file"></a>package.json 파일 만들기

1. 즐겨 찾기 편집기를 사용하여 `package.json` 파일을 만듭니다.
1. 파일을 열고 다음 코드를 붙여넣습니다.

```json
{
  "name": "media-services-node-sample",
  "version": "0.1.0",
  "description": "",
  "main": "./index.ts",
  "dependencies": {
    "@azure/arm-mediaservices": "^8.0.0",
    "@azure/abort-controller": "^1.0.2",
    "@azure/ms-rest-nodeauth": "^3.0.6",
    "@azure/storage-blob": "^12.4.0",
  }
}
```

## <a name="connect-to-nodejs-client-using-typescript"></a>TypeScript를 사용하여 Node.js 클라이언트에 연결

### <a name="sample-env-file"></a>샘플 *.env* 파일

이 파일의 콘텐츠를 *.env* 라는 파일에 복사합니다. 작업 리포지토리의 루트에 저장해야 합니다. 포털에서 Media Services 계정의 API 액세스 페이지에서 얻은 값입니다.

*.env* 파일에 입력하는 데 필요한 값에 액세스하려면 먼저 [API 액세스](./access-api-howto.md) 방법 문서를 읽고 검토하는 것이 좋습니다.
Azure Portal 또는 CLI를 사용하여 이 샘플의 환경 변수 파일에 입력하는 데 필요한 값을 얻을 수 있습니다.

*.env* 파일을 만들고 나면 샘플 작업을 시작할 수 있습니다.

```nodejs
# Values from the API Access page in the portal
AZURE_CLIENT_ID=""
AZURE_CLIENT_SECRET= ""
AZURE_TENANT_ID= ""

# Change this to match your AAD Tenant domain name. 
AAD_TENANT_DOMAIN = "microsoft.onmicrosoft.com"

# Set this to your Media Services Account name, resource group it is contained in, and location
AZURE_MEDIA_ACCOUNT_NAME = ""
AZURE_LOCATION= ""
AZURE_RESOURCE_GROUP= ""

# Set this to your Azure Subscription ID
AZURE_SUBSCRIPTION_ID= ""

# You must change these if you are using Gov Cloud, China, or other non standard cloud regions
AZURE_ARM_AUDIENCE= "https://management.core.windows.net"
AZURE_ARM_ENDPOINT="https://management.azure.com"

# DRM Testing
DRM_SYMMETRIC_KEY="add random base 64 encoded string here"
```

## <a name="run-the-sample-application-helloworld-listassets"></a>샘플 애플리케이션 *HelloWorld-ListAssets* 실행

1. 디렉터리를 *AMSv3Samples* 폴더로 변경

```bash
cd AMSv3Samples
```

2. *packages.json* 파일에서 사용되는 패키지를 설치합니다.

```
npm install 
```

3. 디렉터리를 *HelloWorld-ListAssets* 폴더로 변경합니다.

```bash
cd HelloWorld-ListAssets
```

4. AMSv3Samples 폴더에서 Visual Studio Code를 시작합니다. “.vscode” 폴더와 tsconfig.json 파일이 있는 폴더에서 시작하는 데 필요합니다.

```dotnetcli
cd ..
code .
```

*HelloWorld-ListAssets* 폴더를 열고, Visual Studio Code 편집기에서 *index.ts* 파일을 엽니다.

*index.ts* 파일에서 F5 키를 눌러 디버거를 시작합니다. 계정에 자산이 이미 있으면 자산 목록이 표시되어야 합니다. 계정이 비어 있으면 빈 목록이 표시됩니다.  

나열된 자산을 신속하게 보려면 포털을 사용하여 몇 개의 동영상 파일을 업로드합니다. 자산이 각각 자동으로 생성되고 이 스크립트를 다시 실행하면 이름이 반환됩니다.

### <a name="a-closer-look-at-the-helloworld-listassets-sample"></a>*HelloWorld-ListAssets* 샘플 자세히 보기

*HelloWorld-ListAssets* 샘플은 서비스 주체로 Media Services 클라이언트에 연결하고 계정의 자산을 나열하는 방법을 보여 줍니다. 수행하는 작업에 관한 자세한 설명은 코드의 주석을 참조하세요.

```ts
import * as msRestNodeAuth from "@azure/ms-rest-nodeauth";
import { AzureMediaServices } from '@azure/arm-mediaservices';
import { AzureMediaServicesOptions } from "@azure/arm-mediaservices/esm/models";
// Load the .env file if it exists
import * as dotenv from "dotenv";
dotenv.config();

export async function main() {
  // Copy the samples.env file and rename it to .env first, then populate it's values with the values obtained 
  // from your Media Services account's API Access page in the Azure portal.
  const clientId = process.env.AZURE_CLIENT_ID as string;
  const secret = process.env.AZURE_CLIENT_SECRET as string;
  const tenantDomain = process.env.AAD_TENANT_DOMAIN as string;
  const subscriptionId = process.env.AZURE_SUBSCRIPTION_ID as string;
  const resourceGroup = process.env.AZURE_RESOURCE_GROUP as string;
  const accountName = process.env.AZURE_MEDIA_ACCOUNT_NAME as string;

  let clientOptions: AzureMediaServicesOptions = {
    longRunningOperationRetryTimeout: 5, // set the time out for retries to 5 seconds
    noRetryPolicy: false // use the default retry policy.
  }

  const creds = await msRestNodeAuth.loginWithServicePrincipalSecret(clientId, secret, tenantDomain);
  const mediaClient = new AzureMediaServices(creds, subscriptionId, clientOptions);

  // List Assets in Account
  console.log("Listing Assets Names in account:")
  var assets = await mediaClient.assets.list(resourceGroup, accountName);

  assets.forEach(asset => {
    console.log(asset.name);
  });

  if (assets.odatanextLink) {
    console.log("There are more than 1000 assets in this account, use the assets.listNext() method to continue listing more assets if needed")
    console.log("For example:  assets = await mediaClient.assets.listNext(assets.odatanextLink)");
  }
}

main().catch((err) => {
  console.error("Error running sample:", err.message);
});
```

## <a name="more-samples"></a>다른 샘플

다음 샘플은 [리포지토리](https://github.com/Azure-Samples/media-services-v3-node-tutorials)에서 사용할 수 있습니다.

|프로젝트 이름|사용 사례|
|---|---|
|Live/index.ts| 기본 라이브 스트리밍 예제입니다. **경고** 라이브를 사용하는 경우 모든 리소스가 정리되고 포털에서 더는 청구되지 않는지 확인해야 합니다.|
|StreamFilesSample/index.ts| 로컬 파일을 업로드하거나 원본 URL에서 인코딩하는 기본 예제입니다. 샘플에서는 스토리지 SDK를 사용하여 콘텐츠를 다운로드하는 방법을 보여 주고 플레이어에 스트리밍하는 방법을 보여 줍니다. |
|StreamFilesWithDRMSample/index.ts| Widevine 및 PlayReady DRM을 사용하여 인코딩하고 스트림하는 방법을 설명합니다. |
|VideoIndexerSample/index.ts| 비디오 및 오디오 분석기 사전 설정을 사용하여 비디오 또는 오디오 파일에서 메타데이터와 인사이트를 생성하는 예제입니다. |

## <a name="see-also"></a>참고 항목

- [Node.js용 Azure Media Services 모듈의 참조 문서](/javascript/api/overview/azure/media-services)
- [JavaScript 및 Node.js 개발자용 Azure](/azure/developer/javascript/)
- [@azure/azure-sdk-for-js GitHub 리포지토리의 Media Services 소스 코드](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/mediaservices/arm-mediaservices)
- [Node.js 개발자를 위한 Azure 패키지 설명서](/javascript/api/overview/azure/)
- [Media Services 개념](concepts-overview.md)
- [npm install @azure/arm-mediaservices](https://www.npmjs.com/package/@azure/arm-mediaservices)
- [JavaScript 및 Node.js 개발자용 Azure](/azure/developer/javascript/)
- [@azure/azure-sdk-for-js 리포지토리의 Media Services 소스 코드](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/mediaservices/arm-mediaservices)

## <a name="next-steps"></a>다음 단계

Media Services [Node.js 참조](/javascript/api/overview/azure/mediaservices/management) 설명서를 살펴보고 node.js와 함께 Media Services API를 사용하는 방법을 보여주는 [샘플](https://github.com/Azure-Samples/media-services-v3-node-tutorials)을 확인하세요.
