---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 06/01/2021
ms.author: glenga
ms.openlocfilehash: 729275766806bf5fe47d35b04ddc58b11c58217b
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128908495"
---
## <a name="supported-versions"></a>지원되는 버전

Functions 런타임 버전은 .NET의 특정 버전과 호환됩니다. Functions 버전에 대해 자세히 알아보려면 [Azure Functions 런타임 버전 개요](../articles/azure-functions/functions-versions.md)를 참조하세요. In Process와 Out of Process(격리) 중 어떤 상태로 함수가 실행되는지에 따라 지원되는 버전이 달라집니다. 

다음 표에서는 특정 버전의 Functions에서 사용할 수 있는 가장 높은 수준의 .NET Core 또는 .NET Framework를 보여 줍니다. 

| Functions 런타임 버전 | In-Process<br/>([.NET 클래스 라이브러리](../articles/azure-functions/functions-dotnet-class-library.md)) | Out of Process<br/>([.NET 격리](../articles/azure-functions/dotnet-isolated-process-guide.md)) |
| ---- | ---- | --- |
| Functions 4.x(미리 보기) | .NET 6.0(미리 보기)| .NET 6.0(미리 보기)<sup>2</sup> |
| Functions 3.x | .NET Core 3.1 | .NET 5.0 |
| Functions 2.x | .NET Core 2.1<sup>1</sup> | 해당 없음 |
| Functions 1.x | .NET Framework 4.8 | 해당 없음 |

<sup>1</sup> 자세한 내용은 [Functions v2.x 고려 사항을 참조하세요.](../articles/azure-functions/functions-dotnet-class-library.md#functions-v2x-considerations)    
<sup>2</sup> 현재는 Azure Functions Core Tools 사용하여 격리된 프로세스 함수만 만들 수 있습니다. 자세한 내용은 [빠른 시작: 명령줄에서 Azure에서 C# 함수 만들기를 참조하세요.](../articles/azure-functions/create-first-function-cli-csharp.md?tabs=isolated-process)  

특정한 이전 부 버전의 삭제를 비롯하여 Azure Functions 릴리스에 대한 최신 소식을 보려면 [Azure App Service 공지](https://github.com/Azure/app-service-announcements/issues)를 주기적으로 확인하세요.
