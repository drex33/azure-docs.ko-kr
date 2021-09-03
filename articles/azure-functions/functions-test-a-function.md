---
title: Azure Functions 테스트
description: Visual Studio의 C# 함수 및 VS Code의 JavaScript 함수에 대한 자동화된 테스트 만들기
author: craigshoemaker
ms.topic: conceptual
ms.custom: devx-track-csharp, devx-track-js
ms.date: 03/25/2019
ms.author: cshoe
ms.openlocfilehash: 21bc9991534084ace3be025f1ee0b295ea78558f
ms.sourcegitcommit: f4e04fe2dfc869b2553f557709afaf057dcccb0b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2021
ms.locfileid: "113223809"
---
# <a name="strategies-for-testing-your-code-in-azure-functions"></a>Azure Functions에서 코드를 테스트하기 위한 전략

이 문서에서는 Azure Functions에 대한 자동화된 테스트를 만드는 방법에 대해 설명합니다.

모든 코드를 테스트하는 것이 좋지만, Functions의 논리를 래핑하고 함수 외부에 테스트를 만들면 최상의 결과를 얻을 수 있습니다. 논리를 무시하면 Functions의 코드 줄이 제한되고 함수가 단독으로 다른 클래스 또는 모듈을 호출할 수 있습니다. 그러나 이 문서에서는 HTTP 및 타이머 트리거된 함수에 대한 자동화된 테스트를 만드는 방법을 설명합니다.

다음에 나오는 내용은 서로 다른 언어 및 환경을 대상으로 하는 두 가지 섹션으로 분할됩니다. 테스트를 빌드하는 방법은 다음을 참조하세요.

- [xUnit을 사용한 Visual Studio의 C#](#c-in-visual-studio)
- [Jest를 사용한 VS Code의 JavaScript](#javascript-in-vs-code)
- [pytest를 사용하는 Python](./functions-reference-python.md?tabs=application-level#unit-testing)

샘플 리포지토리는 [GitHub](https://github.com/Azure-Samples/azure-functions-tests)에서 사용할 수 있습니다.

## <a name="c-in-visual-studio"></a>Visual Studio의 C#

다음 예제에서는 Visual Studio에서 C# 함수 앱을 만들고 [xUnit](https://github.com/xunit/xunit)을 사용하여 실행 및 테스트하는 방법을 설명합니다.

![Visual Studio의 C#을 사용하여 Azure Functions 테스트](./media/functions-test-a-function/azure-functions-test-visual-studio-xunit.png)

### <a name="setup"></a>설치 프로그램

환경을 설정하려면 함수 및 테스트 앱을 만듭니다. 다음 단계에서는 테스트를 지원하는 데 필요한 앱 및 함수를 만들 수 있습니다.

1. [새 Functions 앱을 만들고](./functions-get-started.md) 이름을 **Functions** 로 지정합니다.
2. [템플릿에서 HTTP 함수를 만들고](./functions-get-started.md) 이름을 **MyHttpTrigger** 로 지정합니다.
3. [템플릿에서 타이머 함수를 만들고](./functions-create-scheduled-function.md) 이름을 **MyTimerTrigger** 로 지정합니다.
4. 솔루션에서 [xUnit 테스트 앱을 만들고](https://xunit.net/docs/getting-started/netcore/cmdline) 이름을 **Functions.Tests** 로 지정합니다.
5. Nuget을 사용하여 테스트 앱의 참조를 [Microsoft.AspNetCore.Mvc](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc/)에 추가합니다.
6. *Functions.Tests* 앱에서 [*Functions* 앱을 참조](/visualstudio/ide/managing-references-in-a-project)합니다.

### <a name="create-test-classes"></a>테스트 클래스 만들기

이제 프로젝트가 만들어졌으므로 자동화된 테스트를 실행하는 데 사용되는 클래스를 만들 수 있습니다.

각 함수는 [ILogger](/dotnet/api/microsoft.extensions.logging.ilogger) 인스턴스를 사용하여 메시지 로깅을 처리합니다. 일부 테스트는 메시지를 기록하지 않거나 로깅 구현 방법에 관여하지 않습니다. 기타 테스트는 테스트 통과 여부를 결정하기 위해 기록된 메시지를 평가해야 합니다.

테스트 중에 평가할 메시지의 내부 목록을 보유하는 `ListLogger`라는 새 클래스를 만듭니다. 필요한 `ILogger` 인터페이스를 구현하려면 클래스에 범위가 필요합니다. 다음 클래스는 `ListLogger` 클래스에 전달할 테스트 사례에 대한 범위를 모방합니다.

**Nullscope.cs** 라는 *Functions.Tests* 프로젝트에 새 클래스를 만들고 다음 코드를 입력합니다.

```csharp
using System;

namespace Functions.Tests
{
    public class NullScope : IDisposable
    {
        public static NullScope Instance { get; } = new NullScope();

        private NullScope() { }

        public void Dispose() { }
    }
}
```

그런 다음 **Listlogger.cs** 라는 *Functions.Tests* 프로젝트에 새 클래스를 만들고 다음 코드를 입력합니다.

```csharp
using Microsoft.Extensions.Logging;
using System;
using System.Collections.Generic;
using System.Text;

namespace Functions.Tests
{
    public class ListLogger : ILogger
    {
        public IList<string> Logs;

        public IDisposable BeginScope<TState>(TState state) => NullScope.Instance;

        public bool IsEnabled(LogLevel logLevel) => false;

        public ListLogger()
        {
            this.Logs = new List<string>();
        }

        public void Log<TState>(LogLevel logLevel,
                                EventId eventId,
                                TState state,
                                Exception exception,
                                Func<TState, Exception, string> formatter)
        {
            string message = formatter(state, exception);
            this.Logs.Add(message);
        }
    }
}
```

`ListLogger` 클래스는 `ILogger` 인터페이스에서 계약된 대로 다음 멤버를 구현합니다.

- **BeginScope**: 범위는 로깅에 컨텍스트를 추가합니다. 이 경우 테스트가 작동할 수 있으려면 테스트가 `NullScope` 클래스의 정적 인스턴스를 가리키면 됩니다.

- **IsEnabled**: `false`의 기본값이 제공됩니다.

- **Log**: 이 메서드는 제공된 `formatter` 함수를 사용하여 메시지의 형식을 지정하고 결과 텍스트를 `Logs` 컬렉션에 추가합니다.

`Logs` 컬렉션은 `List<string>`의 인스턴스이고 생성자에서 초기화됩니다.

그런 다음 **LoggerTypes.cs** 라는 *Functions.Tests* 프로젝트에 새 파일을 만들고 다음 코드를 입력합니다.

```csharp
namespace Functions.Tests
{
    public enum LoggerTypes
    {
        Null,
        List
    }
}
```

이 열거형은 테스트에서 사용하는 로거 형식을 지정합니다.

이제 **TestFactory.cs** 라는 *Functions.Tests* 프로젝트에 새 클래스를 만들고 다음 코드를 입력합니다.

```csharp
using Microsoft.AspNetCore.Http;
using Microsoft.AspNetCore.Http.Internal;
using Microsoft.Extensions.Logging;
using Microsoft.Extensions.Logging.Abstractions;
using Microsoft.Extensions.Primitives;
using System.Collections.Generic;

namespace Functions.Tests
{
    public class TestFactory
    {
        public static IEnumerable<object[]> Data()
        {
            return new List<object[]>
            {
                new object[] { "name", "Bill" },
                new object[] { "name", "Paul" },
                new object[] { "name", "Steve" }

            };
        }

        private static Dictionary<string, StringValues> CreateDictionary(string key, string value)
        {
            var qs = new Dictionary<string, StringValues>
            {
                { key, value }
            };
            return qs;
        }

        public static HttpRequest CreateHttpRequest(string queryStringKey, string queryStringValue)
        {
            var context = new DefaultHttpContext();
            var request = context.Request;
            request.Query = new QueryCollection(CreateDictionary(queryStringKey, queryStringValue));
            return request;
        }

        public static ILogger CreateLogger(LoggerTypes type = LoggerTypes.Null)
        {
            ILogger logger;

            if (type == LoggerTypes.List)
            {
                logger = new ListLogger();
            }
            else
            {
                logger = NullLoggerFactory.Instance.CreateLogger("Null Logger");
            }

            return logger;
        }
    }
}
```

`TestFactory` 클래스는 다음 멤버를 구현합니다.

- **Data**: 이 속성은 샘플 데이터의 [IEnumerable](/dotnet/api/system.collections.ienumerable) 컬렉션을 반환합니다. 키 값 쌍은 쿼리 문자열로 전달되는 값을 나타냅니다.

- **CreateDictionary**: 이 메서드는 키/값 쌍을 인수로 허용하고 쿼리 문자열 값을 나타내는 `QueryCollection`를 만드는 데 사용되는 새 `Dictionary`를 반환합니다.

- **CreateHttpRequest**: 이 메서드는 지정된 쿼리 문자열 매개 변수로 초기화된 HTTP 요청을 만듭니다.

- **CreateLogger**: 로거 형식에 따라 이 메서드는 테스트에 사용되는 로거 클래스를 반환합니다. `ListLogger`는 테스트에서 평가에 사용할 수 있는 기록된 메시지를 추적합니다.

마지막으로 **FunctionsTests.cs** 라는 *Functions.Tests* 프로젝트에 새 클래스를 만들고 다음 코드를 입력합니다.

```csharp
using Microsoft.AspNetCore.Mvc;
using Microsoft.Extensions.Logging;
using Xunit;

namespace Functions.Tests
{
    public class FunctionsTests
    {
        private readonly ILogger logger = TestFactory.CreateLogger();

        [Fact]
        public async void Http_trigger_should_return_known_string()
        {
            var request = TestFactory.CreateHttpRequest("name", "Bill");
            var response = (OkObjectResult)await MyHttpTrigger.Run(request, logger);
            Assert.Equal("Hello, Bill. This HTTP triggered function executed successfully.", response.Value);
        }

        [Theory]
        [MemberData(nameof(TestFactory.Data), MemberType = typeof(TestFactory))]
        public async void Http_trigger_should_return_known_string_from_member_data(string queryStringKey, string queryStringValue)
        {
            var request = TestFactory.CreateHttpRequest(queryStringKey, queryStringValue);
            var response = (OkObjectResult)await MyHttpTrigger.Run(request, logger);
            Assert.Equal($"Hello, {queryStringValue}. This HTTP triggered function executed successfully.", response.Value);
        }

        [Fact]
        public void Timer_should_log_message()
        {
            var logger = (ListLogger)TestFactory.CreateLogger(LoggerTypes.List);
            MyTimerTrigger.Run(null, logger);
            var msg = logger.Logs[0];
            Assert.Contains("C# Timer trigger function executed at", msg);
        }
    }
}
```

이 클래스에 구현된 멤버는 다음과 같습니다.

- **Http_trigger_should_return_known_string**: 이 테스트는 HTTP 함수에 대한 `name=Bill`의 쿼리 문자열 값이 포함된 요청을 만들고 예상 응답이 반환되었는지 확인합니다.

- **Http_trigger_should_return_string_from_member_data**: 이 테스트는 HTTP 함수에 샘플 데이터를 제공하기 위해 xUnit 특성을 사용합니다.

- **Timer_should_log_message**: 이 테스트는 `ListLogger` 인스턴스를 만들고 타이머 함수에 전달합니다. 함수가 실행되면 로그를 확인하여 예상 메시지가 있는지 확인합니다.

테스트에서 애플리케이션 설정에 액세스하려는 경우 모의 환경 변수 값을 사용하여 인스턴스를 `IConfiguration` 함수에 [삽입](./functions-dotnet-dependency-injection.md)할 수 있습니다.

### <a name="run-tests"></a>테스트 실행

테스트를 실행하려면 **테스트 탐색기** 로 이동하고 **모두 실행** 을 클릭합니다.

![Visual Studio의 C#을 사용하여 Azure Functions 테스트](./media/functions-test-a-function/azure-functions-test-visual-studio-xunit.png)

### <a name="debug-tests"></a>테스트 디버그

테스트를 디버그하려면 테스트에 중단점을 설정하고, **테스트 탐색기** 로 이동하고, **실행 > 마지막 실행 디버그** 를 클릭합니다.

## <a name="javascript-in-vs-code"></a>VS Code의 JavaScript

다음 예제에서는 VS Code에서 JavaScript 함수 앱을 만들고 [Jest](https://jestjs.io)를 사용하여 실행 및 테스트하는 방법을 설명합니다. 이 프로시저는 [VS Code Functions 확장](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions)을 사용하여 Azure Functions를 만듭니다.

![VS Code의 JavaScript를 사용하여 Azure Functions 테스트](./media/functions-test-a-function/azure-functions-test-vs-code-jest.png)

### <a name="setup"></a>설치 프로그램

환경을 설정하려면 `npm init`를 실행하여 빈 폴더에서 새 Node.js 앱을 초기화합니다.

```bash
npm init -y
```

다음으로, 다음 명령을 실행하여 Jest를 설치합니다.

```bash
npm i jest
```

이제 _package.json_ 을 업데이트하여 기존 테스트 명령을 다음 명령으로 바꿉니다.

```bash
"scripts": {
    "test": "jest"
}
```

### <a name="create-test-modules"></a>테스트 모듈 만들기

프로젝트가 초기화된 상태에서 자동화된 테스트를 실행하는 데 사용되는 모듈을 만들 수 있습니다. 먼저 지원 모듈을 포함할 *testing* 이라는 새 폴더를 만듭니다.

*testing* 폴더에서 새 파일을 추가하고, 이름을 **defaultContext.js** 로 지정하고, 다음 코드를 추가합니다.

```javascript
module.exports = {
    log: jest.fn()
};
```

이 모듈은 기본 실행 컨텍스트를 나타내는 *log* 함수를 모방합니다.

다음으로, 새 파일을 추가하고, 이름을 **defaultTimer.js** 로 지정하고, 다음 코드를 추가합니다.

```javascript
module.exports = {
    IsPastDue: false
};
```

이 모듈은 가짜 타이머 인스턴스인 is를 나타내는 `IsPastDue` 속성을 구현합니다. 테스트 도구는 단순히 함수를 직접 호출하여 결과를 테스트하는 것이므로 NCRONTAB 식과 같은 타이머 구성은 여기에 필요하지 않습니다.

그런 다음, VS Code Functions 확장을 사용하여 [새 JavaScript HTTP 함수를 만들고](/azure/developer/javascript/tutorial-vscode-serverless-node-01) 이름을 *HttpTrigger* 로 지정합니다. 함수가 만들어지면 **index.test.js** 라는 동일한 폴더에 새 파일을 추가하고 다음 코드를 추가합니다.

```javascript
const httpFunction = require('./index');
const context = require('../testing/defaultContext')

test('Http trigger should return known text', async () => {

    const request = {
        query: { name: 'Bill' }
    };

    await httpFunction(context, request);

    expect(context.log.mock.calls.length).toBe(1);
    expect(context.res.body).toEqual('Hello Bill');
});
```

템플릿의 HTTP 함수는 쿼리 문자열에 제공된 이름과 연결된 “Hello” 문자열을 반환합니다. 이 테스트는 요청의 가짜 인스턴스를 만들고 HTTP 함수로 전달합니다. 테스트는 *log* 메서드가 한 번 호출되고 반환된 텍스트가 “Hello Bill”과 같은지 확인합니다.

그런 다음, VS Code Functions 확장을 사용하여 새 JavaScript 타이머 함수를 만들고 이름을 *TimerTrigger* 로 지정합니다. 함수가 만들어지면 **index.test.js** 라는 동일한 폴더에 새 파일을 추가하고 다음 코드를 추가합니다.

```javascript
const timerFunction = require('./index');
const context = require('../testing/defaultContext');
const timer = require('../testing/defaultTimer');

test('Timer trigger should log message', () => {
    timerFunction(context, timer);
    expect(context.log.mock.calls.length).toBe(1);
});
```

템플릿의 타이머 함수는 함수 본문 끝에 메시지를 기록합니다. 이 테스트는 *log* 함수가 한 번 호출되었는지 확인합니다.

### <a name="run-tests"></a>테스트 실행

테스트를 실행하려면 **Ctrl+~** 를 눌러 명령 창을 열고 `npm test`를 실행합니다.

```bash
npm test
```

![VS Code의 JavaScript를 사용하여 Azure Functions 테스트](./media/functions-test-a-function/azure-functions-test-vs-code-jest.png)

### <a name="debug-tests"></a>테스트 디버그

테스트를 디버그하려면 *launch.json* 파일에 다음 구성을 추가합니다.

```json
{
  "type": "node",
  "request": "launch",
  "name": "Jest Tests",
  "disableOptimisticBPs": true,
  "program": "${workspaceRoot}/node_modules/jest/bin/jest.js",
  "args": [
      "-i"
  ],
  "internalConsoleOptions": "openOnSessionStart"
}
```

그런 다음, 테스트에 중단점을 설정하고 **F5** 키를 누릅니다.

## <a name="next-steps"></a>다음 단계

이제 함수에 대한 자동화된 테스트를 작성하는 방법을 알아보았으므로 다음 리소스를 계속 진행합니다.

- [HTTP 이외 트리거 함수를 수동으로 실행](./functions-manually-run-non-http.md)
- [Azure Functions 오류 처리](./functions-bindings-error-pages.md)
- [Azure Function Event Grid 트리거 로컬 디버깅](./functions-debug-event-grid-trigger-local.md)
