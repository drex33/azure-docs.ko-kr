---
title: '방법: Azure Fluid Relay 서비스에 연결'
description: 라이브러리를 사용하여 Azure Fluid Relay 서비스에 연결하는 @fluidframework/azure-client 방법입니다.
services: azure-fluid
author: hickeys
ms.author: hickeys
ms.date: 10/05/2021
ms.topic: article
ms.service: azure-fluid
fluid.url: https://fluidframework.com/docs/deployment/azure-frs/
ms.openlocfilehash: a2093b063e5c2e048bdf0a0a418a8046348032c8
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/05/2021
ms.locfileid: "131849580"
---
# <a name="how-to-connect-to-an-azure-fluid-relay-service"></a>방법: Azure Fluid Relay 서비스에 연결

> [!NOTE]
> 이 미리 보기 버전은 서비스 수준 계약 없이 제공되며 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다.

이 문서에서는 Azure Fluid Relay 서비스를 프로비전하고 사용할 준비를 하는 단계를 안내합니다. 

> [!IMPORTANT]
> 앱을 Azure Fluid Relay 서버에 연결하려면 먼저 Azure [계정에서 Azure Fluid Relay 서버 리소스를 프로비전해야](provision-fluid-azure-portal.md) 합니다.

Azure Fluid Relay 서비스는 클라우드 호스팅 유체 서비스입니다. 패키지의 를 사용하여 Azure Fluid Relay 인스턴스에 Fluid 애플리케이션을 연결할 수 `AzureClient` `@fluidframework/azure-client` 있습니다. `AzureClient` 는 컨테이너 개체 자체를 서비스 독립적 상태로 유지하면서 [Fluid 컨테이너를](https://fluidframework.com/docs/build/containers/) 서비스에 연결하는 논리를 처리합니다. 이 클라이언트의 인스턴스 하나를 사용하여 여러 컨테이너를 관리할 수 있습니다.

아래 섹션에서는 사용자 고유의 애플리케이션에서 를 사용하는 방법을 `AzureClient` 설명합니다.

## <a name="connecting-to-the-service"></a>서비스에 연결

Azure Fluid Relay 인스턴스에 연결하려면 먼저 를 만들어야 `AzureClient` 합니다. 테넌트 ID, 주문자 및 스토리지 URL과 토큰 공급자를 포함한 일부 구성 매개 변수를 제공하여 서비스에 대해 현재 사용자에게 권한을 부여하는 데 사용할 JWT(JSON Web Token)를 생성해야 합니다. `@fluidframework/test-client-utils`패키지는 `InsecureTokenProvider` 개발 목적으로 사용할 수 있는 를 제공합니다.

> [!CAUTION]
> 를 `InsecureTokenProvider` 사용하면 클라이언트 쪽 코드 **번들에 테넌트 키 비밀이 노출되기** 때문에 개발 목적으로만 사용해야 합니다. 이 토큰은 `ITokenProvider` 테넌트 키로 서명을 담당하는 사용자 고유의 백 엔드 서비스에서 토큰을 가져오는 의 구현으로 바꿔야 합니다.

```javascript
const config = {
  tenantId: "myTenantId",
  tokenProvider: new InsecureTokenProvider("myTenantKey", { id: "userId" }),
  orderer: "https://myOrdererUrl",
  storage: "https://myStorageUrl",
};

const clientProps = {
  connection: config,
};

const client = new AzureClient(clientProps);
```

이제 인스턴스가 생겼으므로 이를 `AzureClient` 사용하여 유체 컨테이너를 만들거나 로드할 수 있습니다.

### <a name="token-providers"></a>토큰 공급자

[AzureFunctionTokenProvider는](https://github.com/microsoft/FluidFramework/blob/main/packages/framework/azure-client/src/AzureFunctionTokenProvider.ts) `ITokenProvider` 테넌트 키 암호가 클라이언트 쪽 번들 코드에 노출되지 않도록 하는 구현입니다. 는 `AzureFunctionTokenProvider` 현재 사용자 개체와 함께 가 추가된 Azure 함수 `/api/GetAzureToken` URL을 가져옵니다. 나중에 `GET` tenantId, documentId 및 userId/userName을 선택적 매개 변수로 전달하여 Azure Function에 대한 요청을 수행합니다.

```javascript
const config = {
  tenantId: "myTenantId",
  tokenProvider: new AzureFunctionTokenProvider(
    "myAzureFunctionUrl" + "/api/GetAzureToken",
    { userId: "userId", userName: "Test User" }
  ),
  orderer: "https://myOrdererUrl",
  storage: "https://myStorageUrl",
};

const clientProps = {
  connection: config,
};

const client = new AzureClient(clientProps);
```

### <a name="adding-custom-data-to-tokens"></a>토큰에 사용자 지정 데이터 추가

사용자 개체는 선택적 추가 사용자 세부 정보를 보유할 수도 있습니다. 예를 들면 다음과 같습니다.

```javascript
const userDetails = {
  email: "xyz@outlook.com",
  address: "Redmond",
};

const config = {
  tenantId: "myTenantId",
  tokenProvider: new AzureFunctionTokenProvider(
    "myAzureFunctionUrl" + "/api/GetAzureToken",
    { userId: "UserId", userName: "Test User", additionalDetails: userDetails }
  ),
  orderer: "https://myOrdererUrl",
  storage: "https://myStorageUrl",
};
```

Azure Function은 테넌트의 비밀 키를 사용하여 서명된 지정된 사용자에 대한 토큰을 생성하고 비밀 자체를 노출하지 않고 클라이언트에 반환합니다.

## <a name="managing-containers"></a>컨테이너 관리

`AzureClient`API는 `createContainer` 및 `getContainer` 함수를 노출하여 각각 컨테이너를 만들고 얻습니다. 두 함수는 모두 컨테이너 데이터 모델을 정의하는 컨테이너 _스키마를_ 취합니다. 함수의 경우 `getContainer` 가져오려는 컨테이너의 컨테이너에 대한 추가 매개 `id` 변수가 있습니다.

컨테이너 만들기 시나리오에서는 다음 패턴을 사용할 수 있습니다.

```javascript
const schema = {
  initialObjects: {
    /* ... */
  },
  dynamicObjectTypes: [
    /*...*/
  ],
};
const azureClient = new AzureClient(clientProps);
const { container, services } = await azureClient.createContainer(
  schema
);
const id = await container.attach();
```

`container.attach()`호출은 컨테이너가 실제로 서비스에 연결되고 Blob Storage에 기록될 때입니다. `id`이 컨테이너 인스턴스의 고유 식별자인 를 반환합니다.

동일한 공동 작업 세션에 조인하려는 모든 클라이언트는 동일한 컨테이너 를 사용하여 를 호출해야 `getContainer` `id` 합니다.

```javascript
const { container, services } = await azureClient.getContainer(
  id,
  schema
);
```

Fluid에서 내보내는 로그 기록을 시작하는 방법에 대한 자세한 내용은 [로깅 및 원격 분석을 참조하세요.](https://fluidframework.com/docs/testing/telemetry/)

다시 페치되는 컨테이너는 `initialObjects` 컨테이너 스키마에 정의된 대로 를 보유합니다. 스키마를 설정하고 개체를 사용하는 방법에 대한 자세한 내용은 fluidframework.com [데이터 모델링을](https://fluidframework.com/docs/build/data-modeling/) 참조하세요. `container`

## <a name="getting-audience-details"></a>대상 그룹 세부 정보 얻기

를 `createContainer` 호출하고 `getContainer` 두 개의 값, `container` 즉 위에서 설명한 와 `services` 개체를 반환합니다.

는 `container` 유체 데이터 모델을 포함하며 서비스에 구애받지 않습니다. 에서 반환된 이 컨테이너 개체에 대해 작성하는 `AzureClient` 코드는 다른 서비스에 대해 클라이언트에서 다시 사용할 수 있습니다. 예를 들어 를 사용하여 시나리오를 프로토타입화한 경우 `TinyliciousClient` 를 사용하여 로 이동할 때 Fluid 컨테이너 내의 공유 개체와 상호 작용하는 모든 코드를 다시 사용할 수 `AzureClient` 있습니다.

`services`개체에는 Azure Fluid Relay 서비스와 관련한 데이터가 포함됩니다. 이 개체에는 `audience` 현재 컨테이너에 연결된 사용자의 명단을 관리하는 데 사용할 수 있는 값이 포함되어 있습니다.

다음 코드에서는 개체를 사용하여 `audience` 현재 컨테이너에 있는 모든 멤버의 업데이트된 뷰를 유지 관리하는 방법을 보여줍니다.

```javascript
const { audience } = containerServices;
const audienceDiv = document.createElement("div");

const onAudienceChanged = () => {
  const members = audience.getMembers();
  const self = audience.getMyself();
  const memberStrings = [];
  const useAzure = process.env.FLUID_CLIENT === "azure";

  members.forEach((member) => {
    if (member.userId !== (self ? self.userId : "")) {
      if (useAzure) {
        const memberString = `${member.userName}: {Email: ${member.additionalDetails ? member.additionalDetails.email : ""},
                        Address: ${member.additionalDetails ? member.additionalDetails.address : ""}}`;
        memberStrings.push(memberString);
      } else {
        memberStrings.push(member.userName);
      }
    }
  });
  audienceDiv.innerHTML = `
            Current User: ${self ? self.userName : ""} <br />
            Other Users: ${memberStrings.join(", ")}
        `;
};

onAudienceChanged();
audience.on("membersChanged", onAudienceChanged);
```

`audience`에서는 사용자 ID와 사용자 이름을 가진 개체를 반환하는 두 가지 함수를 제공합니다. `AzureMember`

- `getMembers` 는 컨테이너에 연결된 모든 사용자의 맵을 반환합니다. 이러한 값은 멤버가 컨테이너에 조인하거나 컨테이너를 나갈 때마다 변경됩니다.
- `getMyself` 는 이 클라이언트의 현재 사용자를 반환합니다.

`audience` 에서는 멤버의 명단이 변경되면 에 대한 이벤트도 내어 으로 내세울 수 있습니다. `membersChanged`는 모든 명단 변경에 대해 `memberAdded` 발생하지만 및 `memberRemoved` 는 수정된 및 값을 가진 각각의 변경 내용에 대해 발생합니다. `clientId` `member` 이러한 이벤트가 발생한 후 에 대한 새 `getMembers` 호출은 업데이트된 멤버 명단을 반환합니다.

샘플 `AzureMember` 개체는 다음과 같습니다.

```json
{
  "userId": "0e662aca-9d7d-4ff0-8faf-9f8672b70f15",
  "userName": "Test User",
  "connections": [
    {
      "id": "c699c3d1-a4a0-4e9e-aeb4-b33b00544a71",
      "mode": "write"
    },
    {
      "id": "0e662aca-9d7d-4ff0-8faf-9f8672b70f15",
      "mode": "write"
    }
  ],
  "additionalDetails": {
    "email": "xyz@outlook.com",
    "address": "Redmond"
  }
}
```

개체는 사용자 ID, 이름 및 추가 세부 정보 외에도 `AzureMember` 배열을 `connections` 보유합니다. 사용자가 하나의 클라이언트만 사용하여 세션에 로그인한 경우 은 `connections` 클라이언트의 ID를 가진 값 하나만 가지며 가 읽기/쓰기 모드인 경우 입니다. 그러나 동일한 사용자가 여러 클라이언트에서 로그인하는 경우(즉, 다른 디바이스에서 로그인되거나 동일한 컨테이너를 사용하여 여러 브라우저 탭이 열려 있는 경우) `connections` 각 클라이언트에 대해 여러 값을 보유합니다. 위의 예제 데이터에서 이름이 "Test User"이고 ID가 "0e662aca-9d7d-4ff0-8faf-9f8672b70f15"인 사용자가 현재 두 클라이언트에서 열려 있는 컨테이너를 볼 수 있습니다. 필드의 값은 `additionalDetails` 토큰 생성에 제공된 값과 `AzureFunctionTokenProvider` 일치합니다.

이러한 함수와 이벤트를 결합하여 현재 세션에 있는 사용자의 실시간 보기를 표시할 수 있습니다.

**축하합니다.** 이제 Azure Fluid Relay 서비스에 Fluid 컨테이너를 성공적으로 연결하고 공동 작업 세션의 멤버에 대한 사용자 세부 정보를 다시 가져왔습니다.
