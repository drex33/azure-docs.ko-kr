---
title: Fluid Framework 릴리스와 버전 호환성
description: >
  Azure 유체 Relay 서비스와 호환 되는 Fluid Framework 릴리스의 버전을 확인 하는 방법입니다.
services: azure-fluid
author: tylerbutler
ms.author: tylerbu
ms.date: 09/28/2021
ms.topic: article
ms.service: azure-fluid
ms.openlocfilehash: 62493eeb270a171fe874a877dfd51ac404eaab27
ms.sourcegitcommit: e82ce0be68dabf98aa33052afb12f205a203d12d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/07/2021
ms.locfileid: "129663063"
---
# <a name="version-compatibility-with-fluid-framework-releases"></a>Fluid Framework 릴리스와 버전 호환성

> [!NOTE]
> 이 미리 보기 버전은 서비스 수준 계약 없이 제공되며 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다.

응용 프로그램을 Azure 유체 Relay 서비스에 연결 하려면 라이브러리를 사용 **@fluidframework/azure-client** 합니다. 또한 **유체 프레임 워크** 라이브러리를 사용 하 여 핵심 데이터 구조를 사용 하 고 Fluid Framework 제공 합니다.

Azure 유체 Relay를 사용 하 고 있으므로 먼저 사용 가능한 최신 버전을 설치 하 @fluidframework/azure-client 고 해당 버전을 사용 하 여 사용할 유체 프레임 워크 라이브러리의 버전을 결정 해야 합니다. 라이브러리는 종속 된 유체 프레임 워크 패키지의 버전에 대 한 *피어 종속성* 을 나타냅니다.

다음 명령을 사용 하 여 [설치-peerdeps](https://www.npmjs.com/package/install-peerdeps) 도구를 사용 하 여 @fluidframework/azure-client 및 호환 되는 유체 프레임 워크 버전을 모두 설치할 수 있습니다.

```bash
npx install-peerdeps @fluidframework/azure-client
```

> [!TIP]
> 공개 미리 보기 중에는 **@fluidframework/azure-client** 및 **유체 프레임 워크** 의 버전이 일치 합니다. 즉,의 현재 릴리스가 **@fluidframework/azure-client** 0.48 이면 **유체 프레임 워크** 0.48와 호환 됩니다. 또한 역도 마찬가지입니다.

## <a name="compatibility-table"></a>호환성 테이블

| npm 패키지                         | 최소 버전 | API                                                              |
| ----------------------------------  | :-------------- | :--------------------------------------------------------------- |
| @fluidframework/azure-client        | [0.48.4][]      | [API](https://fluidframework.com/docs/apis/azure-client/)        |
| 유체 프레임 워크                     | [0.48.4][]      | [API](https://fluidframework.com/docs/apis/fluid-framework/)     |
| @fluidframework/azure-service-utils | [0.48.4][]      | [API](https://fluidframework.com/docs/apis/azure-service-utils/) |
| @fluidframework/test-client-utils   | [0.48.4][]      | [API](https://fluidframework.com/docs/apis/test-client-utils/)   |

[0.48.4]: https://fluidframework.com/docs/updates/v0.48/

## <a name="next-steps"></a>다음 단계

- [Azure 유체 Relay 서비스 프로 비전](../how-tos/connect-fluid-azure-service.md)
- [Azure 유체 Relay 서비스에 커넥트](../how-tos/connect-fluid-azure-service.md)
- [로컬 테스트에 AzureClient 사용](../how-tos/local-mode-with-azure-client.md)
