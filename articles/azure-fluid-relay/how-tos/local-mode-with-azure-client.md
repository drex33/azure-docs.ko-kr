---
title: '방법: 로컬 테스트에 AzureClient 사용'
description: 서버를 사용 하지 않고 AzureClient를 사용 하 여 응용 프로그램을 테스트 하는 방법
services: azure-fluid
author: hickeys
ms.author: hickeys
ms.date: 10/05/2021
ms.topic: reference
ms.service: azure-fluid
ms.openlocfilehash: f73c5e62471f6c038d7338244ec4756b533fe2d7
ms.sourcegitcommit: e82ce0be68dabf98aa33052afb12f205a203d12d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/07/2021
ms.locfileid: "129663072"
---
# <a name="how-to-use-azureclient-for-local-testing"></a>방법: 로컬 테스트에 AzureClient 사용

> [!NOTE]
> 이 미리 보기 버전은 서비스 수준 계약 없이 제공되며 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다.

이 문서에서는 로컬 모드에서 **Azureclient** 를 구성 하 고이를 사용 하 여 유체 응용 프로그램을 로컬로 테스트 하는 단계를 안내 합니다.

## <a name="configure-and-create-an-azureclient"></a>AzureClient 구성 및 만들기

**Azureclient** 는 아래와 같은 구성을 전달 하 여 로컬 Azure 유체 Relay 인스턴스에 대해 실행 되도록 구성할 수 있습니다.

```js
    import { AzureClient, AzureConnectionConfig, LOCAL_MODE_TENANT_ID } from "@fluidframework/azure-client";
    import { InsecureTokenProvider } from "@fluidframework/test-client-utils";

    const clientProps = {
        connection: {
            tenantId: LOCAL_MODE_TENANT_ID,
            tokenProvider: new InsecureTokenProvider("", { id: "123", name: "Test User" }),
            orderer: "http://localhost:7070",
            storage: "http://localhost:7070",
        },
    };

    const azureClient = new AzureClient(clientProps);
```

이 예제에서는 **Insecuretokenprovider** 를 사용 하 여 Azure 유체 Relay 서비스에 허용 되는 인증 토큰을 생성 하 고 서명 합니다. 그러나 이름에서 알 수 있듯이이는 안전 하지 않으며 프로덕션 환경에서 사용 하면 안 됩니다. InsecureTokenProvider에 대 한 자세한 내용은 [앱의 인증 및 권한 부여](https://fluidframework.com/docs/build/auth/#the-token-provider)를 참조 하세요.

로컬로 실행 하려면 먼저 가져오므로 및 storage url을 구성 하 여 로컬 Azure 유체 Relay 서비스 인스턴스가 실행 되는 도메인 및 포트 (기본적으로)를 가리키도록 구성 합니다 http://localhost:7070 . 마지막 단계는를로 설정 하는 것입니다 `tenantId` `LOCAL_MODE_TENANT_ID` . 이러한 모든 설정은 함께 AzureClient를 구성 하 여 로컬 Azure 유체 Relay 서비스와 작동 합니다.  

## <a name="enabling-debug-logging"></a>디버그 로깅 사용

브라우저 콘솔에서 다음 설정을 사용 하 여 Fluid Framework에서 기본 제공 디버그 로깅을 사용 하도록 설정할 수 있습니다.

`localStorage.debug = 'fluid:*'`

고급 시나리오의 경우를 `logger` AzureClient에 전달할 수 있습니다. 이렇게 하면 로깅 동작을 사용자 지정할 수 있습니다. 로 거 또는 원격 분석에 대 한 자세한 내용은 fluidframework.com의 [로깅 및 원격 분석](https://fluidframework.com/docs/testing/telemetry/) 을 참조 하세요. 

## <a name="running-azure-fluid-relay-service-locally"></a>로컬에서 Azure 유체 Relay 서비스 실행

AzureClient의 로컬 모드를 사용 하려면 먼저 로컬 서버를 시작 해야 합니다. `npx @fluidframework/azure-local-service@latest`터미널 창에서를 실행 하면 Azure 유체 Relay 로컬 서버가 시작 됩니다. 서버가 시작 되 면 로컬 서비스에 대해 응용 프로그램을 실행할 수 있습니다.
