---
title: '방법: Azure Fluid Relay 서비스에 연결'
description: 라이브러리를 사용 하 여 Azure 유체 Relay 서비스에 연결 하는 방법 @fluidframework/azure-client 입니다.
services: azure-fluid
author: hickeys
ms.author: hickeys
ms.date: 10/05/2021
ms.topic: article
ms.service: azure-fluid
fluid.url: https://fluidframework.com/docs/deployment/azure-frs/
ms.openlocfilehash: b9fe1c6a0b70d2e2765ce9da8733916b6226c0da
ms.sourcegitcommit: 92889674b93087ab7d573622e9587d0937233aa2
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/19/2021
ms.locfileid: "130178959"
---
# <a name="how-to-connect-to-an-azure-fluid-relay-service"></a>방법: Azure Fluid Relay 서비스에 연결

> [!NOTE]
> 이 미리 보기 버전은 서비스 수준 계약 없이 제공되며 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다.

이 문서에서는 Azure 유체 Relay 서비스를 프로 비전 하 고 사용할 수 있도록 준비 하는 단계를 안내 합니다. 

> [!IMPORTANT]
> Azure 유체 Relay 서버에 앱을 연결 하려면 azure 계정에 [Azure 유체 relay 서버 리소스를 프로 비전](provision-fluid-azure-portal.md) 해야 합니다.

Azure 유체 Relay 서비스는 클라우드 호스 티 드 서비스입니다. 패키지의를 사용 하 여 유체 응용 프로그램을 Azure 유체 Relay 인스턴스에 연결할 수 있습니다 `AzureClient` `@fluidframework/azure-client` . `AzureClient` 컨테이너 개체 자체 서비스를 독립적으로 유지 하면서 [유체 컨테이너](https://fluidframework.com/docs/build/containers/) 를 서비스에 연결 하는 논리를 처리 합니다. 이 클라이언트의 한 인스턴스를 사용 하 여 여러 컨테이너를 관리할 수 있습니다.

아래 섹션에서는 응용 프로그램에서를 사용 하는 방법을 설명 합니다 `AzureClient` .

## <a name="connecting-to-the-service"></a>서비스에 연결

Azure 유체 Relay 인스턴스에 연결 하려면 먼저를 만들어야 `AzureClient` 합니다. 테 넌 트 ID, 가져오므로 및 저장소 url을 비롯 한 일부 구성 매개 변수를 제공 하 고, 서비스에 대해 현재 사용자에 게 권한을 부여 하는 데 사용 되는 JWT (JSON Web Token)를 생성 하는 토큰 공급자를 제공 해야 합니다. `@fluidframework/test-client-utils`패키지는 `InsecureTokenProvider` 개발 목적으로 사용할 수 있는를 제공 합니다.

> [!CAUTION]
> 는 `InsecureTokenProvider` **클라이언트 쪽 코드 번들에 테 넌 트 키 암호를 노출** 하므로 개발 용도로만 사용 해야 합니다. 이는 `ITokenProvider` 테 넌 트 키를 사용 하 여 서명을 담당 하는 고유한 백 엔드 서비스에서 토큰을 가져오는의 구현으로 바꾸어야 합니다.

```javascript
const config = {
  tenantId: "myTenantId",
  tokenProvider: new InsecureTokenProvider("myTenantKey", {
    id: "userId",
    name: "Test User",
  }),
  orderer: "https://myOrdererUrl",
  storage: "https://myStorageUrl",
};

const clientProps = {
  connection: config,
};

const client = new AzureClient(clientProps);
```

이제 인스턴스가 있으므로 `AzureClient` 이를 사용 하 여 유체 컨테이너를 만들거나 로드할 수 있습니다.

### <a name="token-providers"></a>토큰 공급자

[AzureFunctionTokenProvider](https://github.com/microsoft/FluidFramework/blob/main/packages/framework/azure-client/src/AzureFunctionTokenProvider.ts) 는 `ITokenProvider` 테 넌 트 키 비밀이 클라이언트 쪽 번들 코드에 노출 되지 않도록 하는의 구현입니다. 는 `AzureFunctionTokenProvider` `/api/GetAzureToken` 현재 사용자 개체와 함께가 추가 하는 AZURE 함수 URL을 사용 합니다. 이후에는 `GET` tenantId, documentId 및 userId/userName을 선택적 매개 변수로 전달 하 여 Azure 함수에 요청을 만듭니다.

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

사용자 개체는 선택적 추가 사용자 세부 정보를 포함할 수도 있습니다. 예를 들어:

```javascript
const userDetails: ICustomUserDetails = {
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

Azure 함수는 테 넌 트의 비밀 키를 사용 하 여 서명 된 지정 된 사용자에 대 한 토큰을 생성 하 고 비밀 자체를 노출 하지 않고 클라이언트에 반환 합니다.

## <a name="managing-containers"></a>컨테이너 관리

`AzureClient`API는 `createContainer` 및 `getContainer` 를 각각 컨테이너를 만들고 가져오는 함수를 노출 합니다. 두 함수 모두 컨테이너 데이터 모델을 정의 하는 _컨테이너 스키마_ 를 사용 합니다. 함수의 경우 `getContainer` 페치할 컨테이너의 컨테이너에 대 한 추가 매개 변수가 있습니다 `id` .

컨테이너 생성 시나리오에서 다음과 같은 패턴을 사용할 수 있습니다.

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

`container.attach()`실제로 컨테이너는 서비스에 연결 되 고 blob 저장소에 기록 될 때 호출 됩니다. `id`이 컨테이너 인스턴스에 대 한 고유 식별자 인을 반환 합니다.

동일한 공동 작업 세션을 조인 하려는 모든 클라이언트는 동일한 컨테이너를 사용 하 여를 호출 해야 `getContainer` `id` 합니다.

```javascript
const { container, services } = await azureClient.getContainer(
  id,
  schema
);
```

유체에서 내보내는 로그 기록을 시작 하는 방법에 대 한 자세한 내용은 [로깅 및 원격 분석](https://fluidframework.com/docs/testing/telemetry/) 을 참조 하세요.

다시 인출 되는 컨테이너는 `initialObjects` 컨테이너 스키마에 정의 된 대로을 보유 합니다. 스키마를 설정 하 고 개체를 사용 하는 방법에 대해 자세히 알아보려면 fluidframework.com의 [데이터 모델링](https://fluidframework.com/docs/build/data-modeling/) 을 참조 하세요 `container` .

## <a name="getting-audience-details"></a>대상 세부 정보 가져오기

및에 대 한 호출은 `createContainer` `getContainer` `container` 위에 설명 된 a--및 개체의 두 값을 반환 `services` 합니다.

는 `container` 유체 데이터 모델을 포함 하며 서비스에 독립적입니다. 에서 반환 된이 컨테이너 개체에 대해 작성 하는 모든 코드 `AzureClient` 는 다른 서비스의 클라이언트에서 다시 사용할 수 있습니다. 이에 대 한 예는를 사용 하 여 시나리오를 프로토타입화 한 경우를 `TinyliciousClient` 사용 하 여 이동할 때 유체 컨테이너 내에서 공유 개체와 상호 작용 하는 모든 코드를 재사용할 수 있습니다 `AzureClient` .

`services`개체는 Azure 유체 Relay 서비스와 관련 된 데이터를 포함 합니다. 이 개체에는 `audience` 현재 컨테이너에 연결 되어 있는 사용자의 명단을 관리 하는 데 사용할 수 있는 값이 포함 되어 있습니다.

다음 코드에서는 개체를 사용 하 여 `audience` 현재 컨테이너에 있는 모든 멤버의 업데이트 된 뷰를 유지 관리 하는 방법을 보여 줍니다.

```javascript
const { audience } = containerServices;
const audienceDiv = document.createElement("div");

const onAudienceChanged = () => {
  const members = audience.getMembers();
  const self = audience.getMyself();
  const memberStrings: string[] = [];
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

`audience` 에서는 `AzureMember` 사용자 ID와 사용자 이름이 있는 개체를 반환 하는 두 가지 함수를 제공 합니다.

- `getMembers` 컨테이너에 연결 된 모든 사용자의 맵을 반환 합니다. 이러한 값은 멤버가 컨테이너를 조인 하거나 떠날 때마다 변경 됩니다.
- `getMyself` 이 클라이언트의 현재 사용자를 반환 합니다.

`audience` 또한 멤버의 명단이 변경 되는 경우에 대 한 이벤트도 내보냅니다. `membersChanged` 는 모든 명단 변경에 대해 발생 하는 반면 `memberAdded` 및는 `memberRemoved` `clientId` 수정 된 및 값을 사용 하 여 해당 변경에 대해 발생 `member` 합니다. 이러한 이벤트가 발생 한 후에 대 한 새 호출은 `getMembers` 업데이트 된 멤버 명단을 반환 합니다.

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

사용자 ID, 이름 및 추가 세부 정보와 함께 `AzureMember` 개체도 배열을 포함 `connections` 합니다. 사용자가 한 개의 클라이언트를 사용 하 여 세션에 로그인 한 경우에는 `connections` 클라이언트 ID와 클라이언트 ID가 읽기/쓰기 모드인 경우에만 값이 하나씩 포함 됩니다. 그러나 동일한 사용자가 여러 클라이언트에서 로그인 한 경우 (즉, 서로 다른 장치에서 로그인 하거나 동일한 컨테이너를 사용 하 여 여러 브라우저 탭이 열려 있는 경우)에는 `connections` 각 클라이언트에 대 한 여러 값이 포함 됩니다. 위의 예제 데이터에서 이름이 "테스트 사용자"이 고 ID가 "0e662aca-9d7d-4ff0-8faf-9f8672b70f15" 인 사용자에 게는 현재 두 개의 다른 클라이언트에서 열린 컨테이너가 있는 것을 볼 수 있습니다. 필드의 값은 `additionalDetails` 토큰 생성에 제공 된 값과 일치 합니다 `AzureFunctionTokenProvider` .

이러한 함수와 이벤트를 결합 하 여 현재 세션의 사용자를 실시간으로 표시할 수 있습니다.

**축하합니다.** 이제 유체 컨테이너를 Azure 유체 Relay 서비스에 성공적으로 연결 하 고 공동 작업 세션에서 멤버에 대 한 사용자 세부 정보를 다시 가져왔습니다.
