---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/18/2021
ms.author: glenga
ms.openlocfilehash: 7257f70acb8b931791f3f08437ba61b05e1b4f6d
ms.sourcegitcommit: 4cd97e7c960f34cb3f248a0f384956174cdaf19f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/08/2021
ms.locfileid: "132026688"
---
이 문서에서는 두 가지 유형의 컴파일된 C# 함수 만들기를 지원합니다. 

| 실행 모델 | Description |
| --- | --- |
| **[In-Process](../articles/azure-functions/create-first-function-cli-csharp.md?tabs=in-process)**| 함수 코드는 Functions 호스트 프로세스와 동일한 프로세스에서 실행됩니다. .NET Core 3.1 및 .NET 6.0을 모두 지원합니다. 자세한 내용은 [Azure Functions를 사용하여 C# 클래스 라이브러리 함수 개발](../articles/azure-functions/functions-dotnet-class-library.md)을 참조하세요. |
| **[격리된 프로세스](../articles/azure-functions/create-first-function-cli-csharp.md?tabs=isolated-process)**| 함수 코드는 별도의 .NET 작업자 프로세스에서 실행됩니다. .NET 5.0 및 .NET 6.0을 모두 지원합니다. 자세한 내용은 [C#에서 격리된 프로세스 함수 개발](../articles/azure-functions/dotnet-isolated-process-guide.md)을 참조하세요. |