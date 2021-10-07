---
title: '방법: Azure 함수를 사용 하 여 TokenProvider 작성'
description: 사용자 지정 토큰 공급자를 Azure 함수로 작성 하 고 배포 하는 방법을 설명 합니다.
services: azure-fluid
author: hickeys
ms.author: hickeys
ms.date: 10/05/2021
ms.topic: article
ms.service: azure-fluid
fluid.url: https://fluidframework.com/docs/build/tokenproviders/
ms.openlocfilehash: d6987b4e4592167fcb41a7f6654ff46140a79724
ms.sourcegitcommit: e82ce0be68dabf98aa33052afb12f205a203d12d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/07/2021
ms.locfileid: "129663036"
---
# <a name="how-to-write-a-tokenprovider-with-an-azure-function"></a>방법: Azure 함수를 사용 하 여 TokenProvider 작성

> [!NOTE]
> 이 미리 보기 버전은 서비스 수준 계약 없이 제공되며 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다.

[Fluid Framework](https://fluidframework.com/)에서 tokenproviders는가 `@fluidframework/azure-client` Azure 유체 Relay 서비스에 요청을 수행 하는 데 사용 하는 토큰을 만들고 서명 합니다. Fluid Framework는 **insecuretokenprovider** 라는 무엇 개발 목적으로 안전 하지 않은 간단한 tokenprovider를 제공 합니다. 각 유체 서비스는 인스턴스와 관련 사항을 서비스의 인증 및 보안 고려 사항에 따라 사용자 지정 TokenProvider를 구현 해야 합니다.

## <a name="implementing-your-own-tokenprovider-class"></a>사용자 고유의 TokenProvider 클래스 구현

사용자가 만드는 각 Azure 유체 Relay 서비스 테 넌 트에 **테 넌 트 ID** 및 고유한 **테 넌 트 비밀 키** 가 할당 됩니다. 비밀 키가 **공유 암호** 입니다. 앱/서비스에서이를 인식 하 고 Azure 유체 Relay 서비스에서이를 인식 합니다. 

TokenProviders는 서명 요청에 대 한 비밀 키를 알고 있어야 하지만 비밀 키를 클라이언트 코드에 포함할 수 없습니다. TokenProviders는 런타임에 유체 서버에 연결 하 여 비밀 키를 클라이언트에 노출 하지 않고 안전 하 게 가져옵니다. 이 작업은 별도의 두 API 호출 `fetchOrdererToken` , 및 `fetchStorageToken` 를 통해 수행 됩니다. 호스트에서 가져오므로 및 저장소 url을 각각 인출 해야 합니다. 두 함수 `TokenResponse` 는 모두 토큰 값을 나타내는 개체를 반환 합니다.

## <a name="tokenprovider-class-example"></a>TokenProvider 클래스 예제

보안 토큰 공급자를 구축 하는 한 가지 옵션은 서버를 사용 하지 않는 Azure 함수를 만들고 토큰 공급자로 노출 하는 것입니다. 이렇게 하면 *테 넌 트 비밀 키* 를 보안 서버에 저장할 수 있습니다. 그러면 응용 프로그램에서 Azure Function을 호출 하 여 토큰을 생성 합니다.

이 예제에서는 **AzureFunctionTokenProvider** 라는 클래스에서 해당 패턴을 구현 합니다. Azure 함수 및에 대 한 URL을 허용 `userId` 합니다 `userName` . 이 특정 구현은 패키지에서 내보내기로 제공 되기도 `@fluidframework/azure-client` 합니다.

```typescript
import { ITokenProvider, ITokenResponse } from "@fluidframework/azure-client";

export class AzureFunctionTokenProvider implements ITokenProvider {
  constructor(
    private readonly azFunctionUrl: string,
    private readonly userId: string,
    private readonly userName: string,
  );

  public async fetchOrdererToken(tenantId: string, documentId: string): Promise<ITokenResponse> {
        return {
            jwt: await this.getToken(tenantId, documentId),
        };
    }

    public async fetchStorageToken(tenantId: string, documentId: string): Promise<ITokenResponse> {
        return {
            jwt: await this.getToken(tenantId, documentId),
        };
    }
}
```

테 넌 트 비밀 키가 안전 하 게 유지 되도록 하기 위해 보안 백 엔드 위치에 저장 되며 Azure Function 내 에서만 액세스할 수 있습니다. 서명 된 토큰을 가져오는 한 가지 방법은 Azure 함수에 요청을 수행 하 여 및 및를 제공 하는 것입니다 `GET` `tenantID` `documentId` `userID` / `userName` . Azure Function은 테 넌 트 ID와 테 넌 트 키 암호 간의 매핑을 담당 하 여 Azure 유체 Relay 서비스에서 토큰을 수락 하 고 토큰을 적절 하 게 생성 하 고 서명 합니다.

```typescript
private async getToken(tenantId: string, documentId: string): Promise<string> {
    const params = {
        tenantId,
        documentId,
        userId: this.userId,
        userName: this.userName,
    };
    const token = this.getTokenFromServer(params);
    return token;
}
```

아래 예제에서는 라이브러리를 사용 하 여 [`axios`](https://www.npmjs.com/package/axios) HTTP 요청을 수행 합니다. 다른 라이브러리 또는 방법을 사용 하 여 HTTP 요청을 만들 수 있습니다.

```typescript
private async getTokenFromServer(input: any): Promise<string> {
    return axios.get(this.azFunctionUrl, {
        params: input,
    }).then((response) => {
        return response.data as string;
    }).catch((err) => {
        return err as string;
    });
}
```

이 예제에서는 테 넌 트 키를 전달 하 여 토큰을 인출 하는 사용자 고유의 **Httptrigger Azure 함수** 를 만드는 방법을 보여 줍니다.

```typescript
import { AzureFunction, Context, HttpRequest } from "@azure/functions";
import { ScopeType } from "@fluidframework/azure-client";
import { generateToken } from "@fluidframework/azure-service-utils";

//Replace "myTenantKey" with your key here.
const key = "myTenantKey";

const httpTrigger: AzureFunction = async function (context: Context, req: HttpRequest): Promise<void> {
    // tenantId, documentId, userId and userName are required parameters
    const tenantId = (req.query.tenantId || (req.body && req.body.tenantId)) as string;
    const documentId = (req.query.documentId || (req.body && req.body.documentId)) as string;
    const userId = (req.query.userId || (req.body && req.body.userId)) as string;
    const userName = (req.query.userName || (req.body && req.body.userName)) as string;
    const scopes = (req.query.scopes || (req.body && req.body.scopes)) as ScopeType[];

    if (!tenantId) {
        context.res = {
            status: 400,
            body: "No tenantId provided in query params",
        };
    }

    if (!key) {
        context.res = {
            status: 404,
            body: `No key found for the provided tenantId: ${tenantId}`,
        };
    }

    if (!documentId) {
        context.res = {
            status: 400,
            body: "No documentId provided in query params"
        };
    }

    let user = { name: userName, id: userId };

    // Will generate the token and returned by an ITokenProvider implementation to use with the AzureClient.
    const token = generateToken(
        tenantId,
        documentId,
        key,
        scopes ?? [ScopeType.DocRead, ScopeType.DocWrite, ScopeType.SummaryWrite],
        user
    );

    context.res = {
        status: 200,
        body: token
    };
};

export default httpTrigger;
```

`generateToken`함수는 테 넌 트의 비밀 키를 사용 하 여 서명 된 지정 된 사용자에 대 한 토큰을 생성 합니다. 이렇게 하면 암호 자체를 노출 하지 않고 토큰을 클라이언트에 반환할 수 있습니다. 대신 토큰은 지정 된 문서에 대 한 범위 지정 된 액세스를 제공 하는 데 사용 됩니다. 구현에서이 토큰을 반환 하 여 `ITokenProvider` 와 함께 사용할 수 있습니다 `AzureClient` .

## <a name="see-also"></a>참조

- [인증 토큰에 사용자 지정 데이터 추가](connect-fluid-azure-service.md#adding-custom-data-to-tokens)
