---
title: '방법: Azure Function을 사용하여 TokenProvider 작성'
description: 사용자 지정 토큰 공급자를 Azure 함수로 작성 하 고 배포 하는 방법을 설명 합니다.
services: azure-fluid
author: hickeys
ms.author: hickeys
ms.date: 10/05/2021
ms.topic: article
ms.service: azure-fluid
fluid.url: https://fluidframework.com/docs/build/tokenproviders/
ms.openlocfilehash: 80524d6ab2da2e805e1107755cef4cfb367f6d2a
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131039452"
---
# <a name="how-to-write-a-tokenprovider-with-an-azure-function"></a>방법: Azure Function을 사용하여 TokenProvider 작성

> [!NOTE]
> 이 미리 보기 버전은 서비스 수준 계약 없이 제공되며 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다.

[Fluid Framework](https://fluidframework.com/)에서 tokenproviders는가 `@fluidframework/azure-client` Azure 유체 Relay 서비스에 요청을 수행 하는 데 사용 하는 토큰을 만들고 서명 합니다. Fluid Framework는 **insecuretokenprovider** 라는 무엇 개발 목적으로 안전 하지 않은 간단한 tokenprovider를 제공 합니다. 각 유체 서비스는 인스턴스와 관련 사항을 서비스의 인증 및 보안 고려 사항에 따라 사용자 지정 TokenProvider를 구현 해야 합니다.

사용자가 만드는 각 Azure 유체 Relay 서비스 테 넌 트에 **테 넌 트 ID** 및 고유한 **테 넌 트 비밀 키** 가 할당 됩니다. 비밀 키가 **공유 암호** 입니다. 앱/서비스에서이를 인식 하 고 Azure 유체 Relay 서비스에서이를 인식 합니다. TokenProviders는 서명 요청에 대 한 비밀 키를 알고 있어야 하지만 비밀 키를 클라이언트 코드에 포함할 수 없습니다.

## <a name="implement-an-azure-function-to-sign-tokens"></a>토큰에 서명 하는 Azure 함수 구현

보안 토큰 공급자를 작성 하는 한 가지 옵션은 HTTPS 끝점을 만들고 토큰을 검색 하기 위해 해당 끝점에 인증 된 HTTPS 요청을 만드는 TokenProvider 구현을 만드는 것입니다. 이를 통해 *테 넌 트 비밀 키* 를 [Azure Key Vault](../../key-vault/general/overview.md)와 같은 안전한 위치에 저장할 수 있습니다.

전체 솔루션에는 두 가지 부분이 있습니다.

1. 요청을 수락 하 고 Azure 유체 Relay 토큰을 반환 하는 HTTPS 끝점입니다.
1. 끝점에 대 한 URL을 수락한 다음 해당 끝점에 대 한 요청을 수행 하 여 토큰을 검색 하는 ITokenProvider 구현입니다.

### <a name="create-an-endpoint-for-your-tokenprovider-using-azure-functions"></a>Azure Functions를 사용 하 여 TokenProvider에 대 한 끝점 만들기

[Azure Functions](../../azure-functions/functions-overview.md) 은 이러한 HTTPS 끝점을 만드는 빠른 방법입니다. 아래 예제에서는 **AzureFunctionTokenProvider** 라는 클래스에서 해당 패턴을 구현 합니다. Azure 함수 및에 대 한 URL을 허용 `userId` 합니다 `userName` . 이 특정 구현은 패키지에서 내보내기로 제공 되기도 `@fluidframework/azure-client` 합니다.

이 예제에서는 테 넌 트 키를 전달 하 여 토큰을 인출 하는 사용자 고유의 **Httptrigger Azure 함수** 를 만드는 방법을 보여 줍니다.

```typescript
import { AzureFunction, Context, HttpRequest } from "@azure/functions";
import { ScopeType } from "@fluidframework/azure-client";
import { generateToken } from "@fluidframework/azure-service-utils";

// NOTE: retrieve the key from a secure location.
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
        return;
    }

    if (!key) {
        context.res = {
            status: 404,
            body: `No key found for the provided tenantId: ${tenantId}`,
        };
        return;
    }

    if (!documentId) {
        context.res = {
            status: 400,
            body: "No documentId provided in query params"
        };
        return;
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

`generateToken`패키지에 있는 함수는 `@fluidframework/azure-service-utils` 테 넌 트의 비밀 키를 사용 하 여 서명 된 지정 된 사용자에 대 한 토큰을 생성 합니다. 이렇게 하면 암호를 노출 하지 않고 토큰을 클라이언트에 반환할 수 있습니다. 대신 토큰은 지정 된 문서에 대 한 범위 지정 된 액세스를 제공 하기 위해 암호를 사용 하 여 서버 쪽에서 생성 됩니다. 아래 예제 ITokenProvider는이 Azure 함수에 대 한 HTTP 요청을 수행 하 여 토큰을 검색 합니다.

### <a name="deploy-the-azure-function"></a>Azure Function 배포

Azure Functions는 여러 가지 방법으로 배포할 수 있습니다. Azure Functions 배포에 대 한 자세한 내용은 [Azure Functions 설명서](../../azure-functions/functions-continuous-deployment.md) 의 **배포** 섹션을 참조 하세요.

### <a name="implement-the-tokenprovider"></a>TokenProvider 구현

TokenProviders는 여러 가지 방법으로 구현 될 수 있지만 두 개의 별도 API 호출, 즉 및를 구현 해야 합니다. `fetchOrdererToken` `fetchStorageToken` 이러한 api는 각각 유체 가져오므로 및 storage 서비스에 대 한 토큰을 인출 합니다. 두 함수 `TokenResponse` 는 모두 토큰 값을 나타내는 개체를 반환 합니다. Fluid Framework 런타임은 토큰을 검색 하기 위해 필요에 따라이 두 api를 호출 합니다.


테 넌 트 비밀 키가 안전 하 게 유지 되도록 하기 위해 보안 백 엔드 위치에 저장 되며 Azure Function 내 에서만 액세스할 수 있습니다. 토큰을 검색 하려면 `GET` 배포 된 Azure 함수에 대해 또는 요청을 수행 하 여 `POST` 및 및를 제공 해야 `tenantID` `documentId` `userID` / `userName` 합니다. Azure 함수는 토큰을 적절 하 게 생성 하 고 서명 하기 위해 테 넌 트 ID와 테 넌 트 키 암호 간의 매핑을 담당 합니다.

아래 예제 구현에서는 [axios](https://www.npmjs.com/package/axios) 라이브러리를 사용 하 여 HTTP 요청을 수행 합니다. 다른 라이브러리 또는 방법을 사용 하 여 서버 코드에서 HTTP 요청을 만들 수 있습니다.

```typescript
import { ITokenProvider, ITokenResponse } from "@fluidframework/routerlicious-driver";
import axios from "axios";
import { AzureMember } from "./interfaces";

/**
 * Token Provider implementation for connecting to an Azure Function endpoint for
 * Azure Fluid Relay token resolution.
 */
export class AzureFunctionTokenProvider implements ITokenProvider {
    /**
     * Creates a new instance using configuration parameters.
     * @param azFunctionUrl - URL to Azure Function endpoint
     * @param user - User object
     */
    constructor(
        private readonly azFunctionUrl: string,
        private readonly user?: Pick<AzureMember, "userId" | "userName" | "additionalDetails">,
    ) { }

    public async fetchOrdererToken(tenantId: string, documentId?: string): Promise<ITokenResponse> {
        return {
            jwt: await this.getToken(tenantId, documentId),
        };
    }

    public async fetchStorageToken(tenantId: string, documentId: string): Promise<ITokenResponse> {
        return {
            jwt: await this.getToken(tenantId, documentId),
        };
    }

    private async getToken(tenantId: string, documentId: string | undefined): Promise<string> {
        const response = await axios.get(this.azFunctionUrl, {
            params: {
                tenantId,
                documentId,
                userId: this.user?.userId,
                userName: this.user?.userName,
                additionalDetails: this.user?.additionalDetails,
            },
        });
        return response.data as string;
    }
}
```
## <a name="see-also"></a>참고 항목

- [인증 토큰에 사용자 지정 데이터 추가](connect-fluid-azure-service.md#adding-custom-data-to-tokens)
- [방법: Azure 정적 Web Apps를 사용 하 여 유체 응용 프로그램 배포](deploy-fluid-static-web-apps.md)
