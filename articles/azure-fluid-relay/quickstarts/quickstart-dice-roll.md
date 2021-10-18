---
title: '빠른 시작: 주사위 롤러'
description: Azure Fluid Relay 서비스를 사용하여 주사위 롤링 앱을 빠르게 만듭니다.
author: hickeys
ms.service: azure-fluid
ms.topic: quickstart
ms.date: 09/09/2021
ms.author: hickeys
ms.openlocfilehash: f3a25a47cc1bee143f562e936f7927f48d452b4c
ms.sourcegitcommit: e82ce0be68dabf98aa33052afb12f205a203d12d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/07/2021
ms.locfileid: "129662167"
---
# <a name="quickstart-dice-roller"></a>빠른 시작: 주사위 롤러

> [!NOTE]
> 이 미리 보기 버전은 서비스 수준 계약 없이 제공되며 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다.

이 빠른 시작에서는 Azure Fluid Relay 서비스를 사용하는 주사위 롤러 앱을 만드는 프로세스를 안내합니다. 이 빠른 시작은 두 부분으로 나뉩니다. 1부에서는 앱 자체를 만들고 로컬 Fluid 서버에 대해 실행합니다. 2부에서는 로컬 개발 서버 대신 Azure Fluid Relay 서비스에 대해 실행하도록 앱을 다시 구성합니다.

이 빠른 시작에 사용된 샘플 코드는 [여기](https://github.com/microsoft/FluidHelloWorld/tree/main-azure)에서 사용할 수 있습니다.

## <a name="set-up-your-development-environment"></a>개발 환경 설정

이 빠른 시작을 따르려면 [Azure Fluid Relay 서비스](../how-tos/provision-fluid-azure-portal.md)가 프로비전된 Azure 계정이 필요합니다. 계정이 없는 경우 [Azure를 무료로 사용](https://azure.com/free)해 볼 수 있습니다.

컴퓨터에 다음 소프트웨어도 설치되어 있어야 합니다.

- 코드 편집기 - [Visual Studio Code](https://code.visualstudio.com/)를 사용하는 것이 좋습니다.
- [Git](https://git-scm.com/downloads)
- [Node.js](https://nodejs.org/en/download) 버전 12.17 이상

## <a name="getting-started-locally"></a>로컬로 시작

먼저 GitHub에서 샘플 앱을 다운로드해야 합니다. 새 명령 창을 열고 코드를 다운로드하려는 폴더로 이동하고 Git을 사용하여 [FluidHelloWorld 리포지토리](https://github.com/microsoft/FluidHelloWorld)를 복제합니다. 복제 프로세스는 프로젝트 파일이 있는 FluidHelloWorld라는 하위 폴더를 만듭니다.

```cli
git clone -b main-azure https://github.com/microsoft/FluidHelloWorld.git
```

새로 만든 폴더로 이동하여 종속성을 설치하고 앱을 시작합니다.

```cli
cd FluidHelloWorld
npm install
...
npm start
```


`npm start` 명령을 실행하면 두 가지가 발생합니다. 먼저 로컬 프로세스에서 Fluid 서버가 시작됩니다. 이 서버는 개발 전용입니다. 나중에 Azure 호스팅 프로덕션 서버로 업그레이드합니다. 둘째, 새 브라우저 탭이 주사위 롤러 앱의 새 인스턴스를 포함하는 페이지로 열립니다. 동일한 URL로 새 탭을 열어 주사위 롤러 앱의 추가 인스턴스를 만들 수 있습니다. 앱의 각 인스턴스는 기본적으로 로컬 Fluid 서비스를 사용하도록 구성됩니다. 앱의 모든 인스턴스에서 **롤** 단추를 클릭하고 모든 클라이언트에서 주사위 상태가 변경됨을 확인합니다.

## <a name="upgrading-to-azure-fluid-relay"></a>Azure Fluid Relay로 업그레이드

Azure Fluid Relay 서비스에 대해 실행하려면 로컬 서버 대신 Azure 서비스에 연결하도록 앱의 구성을 업데이트해야 합니다.

### <a name="configure-and-create-an-azure-client"></a>Azure 클라이언트 구성 및 만들기

Azure 클라이언트를 구성하려면 `app.js`의 `serviceConfig` 개체 값을 Azure Fluid Relay 서비스 구성 값으로 바꿉니다. 이러한 값은 Azure Portal에서 Fluid Relay 리소스의 "액세스 키" 섹션에서 찾을 수 있습니다.

```javascript
const serviceConfig = {
    connection: {
        tenantId: LOCAL_MODE_TENANT_ID, // REPLACE WITH YOUR TENANT ID
        tokenProvider: new InsecureTokenProvider("" /* REPLACE WITH YOUR PRIMARY KEY */, { id: "userId" }),
        orderer: "http://localhost:7070", // REPLACE WITH YOUR ORDERER ENDPOINT
        storage: "http://localhost:7070", // REPLACE WITH YOUR STORAGE ENDPOINT
    }
};
```

> [!WARNING]
> 개발 중에 `InsecureTokenProvider`를 사용하여 Azure Fluid Relay 서비스에서 수락할 인증 토큰을 생성하고 서명할 수 있습니다. 그러나 이름에서 알 수 있듯이 이는 안전하지 않으며 프로덕션 환경에서 사용하면 안 됩니다. Azure Fluid Relay 리소스 만들기 프로세스는 보안 요청에 서명하는 데 사용할 수 있는 비밀 키를 제공합니다. **이 비밀이 노출되지 않도록 하려면 프로덕션으로 릴리스하기 전에 개발자가 제공하는 안전한 백 엔드 서비스에서 토큰을 가져오는 ITokenProvider의 다른 구현으로 대체해야 합니다.**

### <a name="build-and-run-the-client-only"></a>클라이언트만 빌드 및 실행

이제 로컬 서버 대신 Azure를 사용하도록 앱을 지정했으므로 클라이언트 앱과 함께 로컬 Fluid 서버를 시작할 필요가 없습니다. 이 명령을 사용하여 서버를 시작하지 않고도 클라이언트를 시작할 수 있습니다. 

```bash
npm run start:client
```

🥳**축하합니다**🎉 Fluid 협업의 세계를 여는 첫 번째 단계를 성공적으로 수행했습니다.
