---
title: '방법: Azure Fluid Relay에서 테스트 자동화 사용'
description: 테스트 자동화 라이브러리를 사용하여 유체 애플리케이션에 대한 자동화된 테스트를 만드는 방법
services: azure-fluid
author: hickeys
ms.author: hickeys
ms.date: 10/05/2021
ms.topic: article
ms.service: azure-fluid
fluid.url: https://fluidframework.com/docs/testing/testing/
ms.openlocfilehash: 0087be9dccc1b040720ea53f5e6aae6d64f6fa17
ms.sourcegitcommit: e82ce0be68dabf98aa33052afb12f205a203d12d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/07/2021
ms.locfileid: "129663070"
---
# <a name="how-to-use-test-automation-with-azure-fluid-relay"></a>방법: Azure Fluid Relay에서 테스트 자동화 사용

> [!NOTE]
> 이 미리 보기 버전은 서비스 수준 계약 없이 제공되며 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다.

테스트 및 자동화는 코드의 품질과 수명을 유지하는 데 중요합니다. 내부적으로 Fluid는 [Mocha,](https://mochajs.org/) [Jest,](https://jestjs.io/) [Puppeteer](https://github.com/puppeteer/puppeteer)및 [Webpack](https://webpack.js.org/)에서 구동하는 다양한 단위 및 통합 테스트를 사용합니다.

**@fluidframework/azure-local-service** 로컬을 사용하거나 Azure Fluid Relay 서비스에서 테스트 테넌트를 사용하여 테스트를 실행할 수 있습니다. 원격 서비스와 로컬 서비스 모두에 연결하도록 **AzureClient를** 구성할 수 있습니다. 이를 통해 라이브 및 로컬 서비스 인스턴스에 대한 테스트 간에 단일 클라이언트 형식을 사용할 수 있습니다. 유일한 차이점은 클라이언트를 만드는 데 사용되는 구성입니다.

## <a name="automation-against-azure-fluid-relay"></a>Azure Fluid Relay에 대한 자동화

자동화는 프로덕션 테넌트와 동일한 방식으로 Azure Fluid Relay에 대한 테스트 테넌트 에 연결할 수 있으며 적절한 연결 구성만 필요합니다. 자세한 내용은 [방법: Azure Fluid Relay 서비스 커넥트](connect-fluid-azure-service.md) 참조하세요.

## <a name="creating-an-adaptable-test-client"></a>적응 가능한 테스트 클라이언트 만들기

적응 가능한 테스트 클라이언트를 만들려면 서비스 대상에 따라 AzureClient를 다르게 구성해야 합니다. 아래 함수는 환경 변수를 사용하여 이를 확인합니다. 테스트 스크립트에서 환경 변수를 설정하여 대상으로 지정된 서비스를 제어할 수 있습니다.

```typescript
function createAzureClient(): AzureClient {
    const useAzure = process.env.FLUID_CLIENT === "azure";
    const tenantKey = useAzure ? process.env.FLUID_TENANTKEY as string : "";
    const user = { id: "userId", name: "Test User" };

    const connectionConfig = useAzure ? {
        tenantId: "myTenantId",
        tokenProvider: new InsecureTokenProvider(tenantKey, user),
        orderer: "https://myOrdererUrl",
        storage: "https://myStorageUrl",
    } : {
        tenantId: LOCAL_MODE_TENANT_ID,
        tokenProvider: new InsecureTokenProvider("", user),
        orderer: "http://localhost:7070",
        storage: "http://localhost:7070",
    };

    const clientProps = {
        connection: config,
    };

    return new AzureClient(clientProps);
}
```

테스트에서는 이 함수를 호출하여 기본 서비스에 대한 자체적인 우려 없이 AzureClient 개체를 만들 수 있습니다. 아래 [Mocha](https://mochajs.org/) 테스트는 테스트를 실행하기 전에 서비스 클라이언트를 만든 다음, 이를 사용하여 각 테스트를 실행합니다. 서비스 클라이언트를 사용하여 오류가 throw되지 않는 한 통과하는 컨테이너를 만드는 단일 테스트가 있습니다.

```typescript
describe("ClientTest", () => {
    const client = createAzureClient();
    let documentId: string;

    it("can create Azure container successfully", async () => {
        const schema: ContainerSchema = {
            initialObjects: {
                customMap: SharedMap
            },
        };
        documentId = await container.attach();
        const { container, services } = await azureClient.createContainer(schema);
    });
});

```

## <a name="running-tests"></a>테스트 실행

프로젝트의 package.json에 다음 npm 스크립트를 추가하여 테스트를 실행할 수 있습니다.

```json
"scripts": {
    "start:local": "npx @fluidframework/azure-local-service@latest > local-service.log 2>&1",
    "test:mocha": "mocha",
    "test:azure": "cross-env process.env.FLUID_CLIENT='\"azure\"' && npm run test:mocha",
    "test:local": "start-server-and-test start:local 7070 test:mocha"
}
```

위의 스크립트에 필요한 의존성을 설치하려면 다음 명령을 사용할 수 있습니다.

```bash
npm install cross-env start-server-and-test mocha
```

`test:local`스크립트는 [start-server-and-test](https://www.npmjs.com/package/start-server-and-test) 라이브러리를 사용하여 로컬 서버를 시작하고, 포트 7070(로컬 서버에서 사용하는 기본 포트)이 응답할 때까지 기다렸다가 테스트 스크립트를 실행한 다음 로컬 서버를 종료합니다.
