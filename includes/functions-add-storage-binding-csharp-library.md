---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 08/15/2021
ms.author: glenga
ms.openlocfilehash: 4f2aa82388882c192213b168faedd4f61069ae64
ms.sourcegitcommit: 16e25fb3a5fa8fc054e16f30dc925a7276f2a4cb
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/25/2021
ms.locfileid: "122829553"
---
C# 프로젝트에서 바인딩은 함수 메서드의 바인딩 특성으로 정의됩니다. 특정 정의는 앱이 In-Process(C# 클래스 라이브러리)에서 실행하는지 아니면 격리된 프로세스에서 실행되는지에 따라 달라집니다.  

# <a name="in-process"></a>[In-Process](#tab/in-process)

*HttpExample.cs* 프로젝트 파일을 열고 다음 매개 변수를 `Run` 메서드 정의에 추가합니다.

:::code language="csharp" source="~/functions-docs-csharp/functions-add-output-binding-storage-queue-cli/HttpExample.cs" range="17":::

`msg` 매개 변수는 `ICollector<T>` 형식이며, 함수가 완료될 때 출력 바인딩에 작성되는 메시지의 컬렉션을 나타냅니다. 이 경우 출력은 `outqueue`라는 스토리지 큐입니다. 스토리지 계정에 대한 연결 문자열은 `StorageAccountAttribute`로 설정됩니다. 이 특성은 스토리지 계정 연결 문자열을 포함하는 설정을 나타내며 클래스, 메서드 또는 매개 변수 수준에서 적용될 수 있습니다. 이 경우 이미 기본 스토리지 계정을 사용하고 있으므로 `StorageAccountAttribute`를 생략할 수 있습니다.

Run 메서드 정의는 이제 다음과 같이 표시됩니다.  

:::code language="csharp" source="~/functions-docs-csharp/functions-add-output-binding-storage-queue-cli/HttpExample.cs" range="14-18":::

# <a name="isolated-process"></a>[격리된 프로세스](#tab/isolated-process)

*HttpExample.cs* 프로젝트 파일을 열고 다음 `MultiResponse` 클래스를 추가합니다.

:::code language="csharp" source="~/functions-docs-csharp/functions-add-output-binding-storage-queue-isolated/HttpExample.cs" range="33-38":::

`MultiResponse` 클래스를 사용하면 `outqueue`라는 스토리지 큐와 HTTP 성공 메시지 모두에 쓸 수 있습니다. `QueueOutput` 특성이 문자열 배열에 적용되므로 여러 메시지를 큐로 보낼 수 있습니다. 

스토리지 계정에 대한 연결 문자열은 `Connection` 속성에 의해 설정됩니다. 이 경우 이미 기본 스토리지 계정을 사용하고 있으므로 `Connection`를 생략할 수 있습니다.

---