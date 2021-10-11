---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/18/2021
ms.author: glenga
ms.openlocfilehash: ed981abd0ff849fb2a88164b2814794a48603ce7
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128559909"
---
이 문서에서는 두 가지 유형의 컴파일된 C# 함수 만들기를 지원합니다. 

| 실행 모델 | Description |
| --- | --- |
| **[In-Process](../articles/azure-functions/create-first-function-cli-csharp.md?tabs=in-process)**| 함수 코드는 Functions 호스트 프로세스와 동일한 프로세스에서 실행됩니다. 자세한 내용은 [Azure Functions를 사용하여 C# 클래스 라이브러리 함수 개발](../articles/azure-functions/functions-dotnet-class-library.md)을 참조하세요. |
| **[격리된 프로세스](../articles/azure-functions/create-first-function-cli-csharp.md?tabs=isolated-process)**| 함수 코드는 별도의 .NET 작업자 프로세스에서 실행됩니다. 자세한 내용은 [Azure의 .NET 5.0에서 함수를 실행하는 방법에 대한 가이드](../articles/azure-functions/dotnet-isolated-process-guide.md)를 참조하세요. |