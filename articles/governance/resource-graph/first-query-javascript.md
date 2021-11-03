---
title: '빠른 시작: 첫 번째 JavaScript 쿼리'
description: 이 빠른 시작에서는 단계에 따라 JavaScript용 Resource Graph 라이브러리를 사용하도록 설정하고 첫 번째 쿼리를 실행합니다.
ms.date: 07/09/2021
ms.topic: quickstart
ms.custom: devx-track-js, mode-api
ms.openlocfilehash: 9ff7237938deeebee33568fcb176fdde1b2275ef
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131068592"
---
# <a name="quickstart-run-your-first-resource-graph-query-using-javascript"></a>빠른 시작: JavaScript를 사용하여 첫 번째 Resource Graph 쿼리 실행

이 빠른 시작에서는 JavaScript 설치에 라이브러리를 추가하는 과정을 안내합니다. Azure Resource Graph를 사용하는 첫 번째 단계는 필요한 라이브러리를 사용하여 JavaScript 애플리케이션을 초기화하는 것입니다.

이 과정이 끝나면 JavaScript 설치에 라이브러리가 추가되고 첫 번째 Resource Graph 쿼리를 실행할 수 있습니다.

## <a name="prerequisites"></a>필수 구성 요소

- **Azure 구독**: Azure 구독이 아직 없는 경우 시작하기 전에 [체험](https://azure.microsoft.com/free/) 계정을 만듭니다.

- **Node.js**: [Node.js](https://nodejs.org/) 버전 12 이상이 필요합니다.

## <a name="application-initialization"></a>애플리케이션 초기화

JavaScript에서 Azure Resource Graph를 쿼리하도록 설정하려면 환경을 구성해야 합니다. 이 설정은 [Windows 10의 bash](/windows/wsl/install-win10)를 비롯하여 JavaScript를 사용할 수 있는 모든 위치에서 작동합니다.

1. 다음 명령을 실행하여 새 Node.js 프로젝트를 초기화합니다.

   ```bash
   npm init -y
   ```

1. yargs 모듈에 참조를 추가합니다.

   ```bash
   npm install yargs
   ```

1. Azure Resource Graph 모듈에 참조를 추가합니다.

   ```bash
   npm install @azure/arm-resourcegraph
   ```

1. Azure 인증 라이브러리에 참조를 추가합니다.

   ```bash
   npm install @azure/ms-rest-nodeauth
   ```

   > [!NOTE]
   > _package.json_ `@azure/arm-resourcegraph`가 버전 **2.0.0** 이상이고 `@azure/ms-rest-nodeauth`가 버전 **3.0.3** 이상인지 확인합니다.

## <a name="query-the-resource-graph"></a>Resource Graph 쿼리

1. _index.js_ 라는 새 파일을 만들고 다음 코드를 입력합니다.

   ```javascript
   const argv = require("yargs").argv;
   const authenticator = require("@azure/ms-rest-nodeauth");
   const resourceGraph = require("@azure/arm-resourcegraph");

   if (argv.query) {
       const query = async () => {
          const credentials = await authenticator.interactiveLogin();
          const client = new resourceGraph.ResourceGraphClient(credentials);
          const result = await client.resources(
             {
                 query: argv.query
             },
             { resultFormat: "table" }
          );
          console.log("Records: " + result.totalRecords);
          console.log(result.data);
       };

       query();
   }
   ```

   > [!NOTE]
   > 이 코드는 테넌트 기반 쿼리를 만듭니다. 쿼리를 [관리 그룹](../management-groups/overview.md) 또는 구독으로 제한하려면 `client.resources` 호출에 [쿼리 요청](/javascript/api/@azure/arm-resourcegraph/queryrequest)을 정의 및 추가하고 `managementGroups` 또는 `subscriptions`를 지정합니다.

1. 터미널에서 다음 명령을 입력합니다.

   ```bash
   node index.js --query "Resources | project name, type | limit 5"
   ```

   > [!NOTE]
   > 이 쿼리 예제에서는 `order by`와 같은 정렬 한정자를 제공하지 않으므로 이 쿼리를 여러 번 실행하면 요청마다 다른 리소스 세트가 생성될 수 있습니다.

1. 첫 번째 매개 변수를 `index.js`로 변경하고 **Name** 속성을 `order by`하도록 쿼리를 변경합니다.

   ```bash
   node index.js --query "Resources | project name, type | limit 5 | order by name asc"
   ```

   스크립트가 인증을 시도할 때 다음 메시지와 유사한 메시지가 터미널에 표시됩니다.

   > 로그인하려면 웹 브라우저를 사용하여 https://microsoft.com/devicelogin 페이지를 열고 FGB56WJUGK 코드를 입력하여 인증합니다.

   브라우저에서 인증하면 스크립트가 계속 실행됩니다.

   > [!NOTE]
   > 첫 번째 쿼리와 마찬가지로 이 쿼리를 여러 번 실행하면 요청마다 다른 리소스 집합이 생성될 수 있습니다. 쿼리 명령의 순서는 중요합니다. 이 예제에서 `order by`는 `limit` 뒤에 옵니다. 이 명령 순서는 먼저 쿼리 결과를 제한한 다음, 정렬합니다.

1. 첫 번째 매개 변수를 `index.js`로 변경하고 **Name** 속성에 먼저 `order by`를 수행한 다음, 상위 5개 결과로 `limit`하도록 쿼리를 변경합니다.

   ```bash
   node index.js --query "Resources | project name, type | order by name asc | limit 5"
   ```

최종 쿼리가 여러 번 실행될 때 환경이 전혀 변경되지 않는다고 가정하면 반환되는 결과는 **Name** 속성을 기준으로 일관되고 정렬되지만 여전히 상위 5개 결과로 제한됩니다.

## <a name="clean-up-resources"></a>리소스 정리

애플리케이션에서 설치된 라이브러리를 제거하려는 경우 다음 명령을 실행합니다.

```bash
npm uninstall @azure/arm-resourcegraph @azure/ms-rest-nodeauth yargs
```

## <a name="next-steps"></a>다음 단계

이 빠른 시작에서는 Resource Graph 라이브러리를 JavaScript 환경에 추가하고 첫 번째 쿼리를 실행했습니다. 리소스 그래프 언어에 대한 자세한 내용을 보려면 쿼리 언어 정보 페이지로 이동하세요.

> [!div class="nextstepaction"]
> [쿼리 언어에 대한 자세한 정보 가져오기](./concepts/query-language.md)
