---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 06/01/2021
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: 2e5699a4522ef9da30b51a97eba49d7bc6e2c001
ms.sourcegitcommit: 4cd97e7c960f34cb3f248a0f384956174cdaf19f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/08/2021
ms.locfileid: "132025985"
---
|언어                                 |1.x         |2.x| 3.x | 4.x |
|-----------------------------------------|------------|---| --- | --- |
|[C#](../articles/azure-functions/functions-dotnet-class-library.md)|GA(.NET Framework 4.8)|GA(.NET Core 2.1<sup>1)</sup>| GA(.NET Core 3.1)<br/>[GA(.NET 5.0)](../articles/azure-functions/dotnet-isolated-process-guide.md) | GA(.NET 6.0) |
|[JavaScript](../articles/azure-functions/functions-reference-node.md#node-version)|GA(Node 6)|GA(Node 10 및 8)| GA(Node 14, 12 및 10) | GA(노드 14) |
|[F#](../articles/azure-functions/functions-reference-fsharp.md)|GA(.NET Framework 4.8)|GA(.NET Core 2.1<sup>1)</sup>| GA(.NET Core 3.1) | GA(.NET 6.0) |
|[Java](../articles/azure-functions/functions-reference-java.md)|해당 없음|GA(Java 8)| GA(Java 11 및 8)| GA(Java 11 및 8)|
|[PowerShell](../articles/azure-functions/functions-reference-powershell.md) |해당 없음|GA(PowerShell Core 6)| GA(PowerShell 7.0 및 Core 6)| GA(PowerShell 7.0)|
|[Python](../articles/azure-functions/functions-reference-python.md#python-version)|해당 없음|GA(Python 3.7 및 3.6)| GA(Python 3.9, 3.8, 3.7, & 3.6)| GA(Python 3.9, 3.8)|
|[TypeScript](../articles/azure-functions/functions-reference-node.md#typescript)<sup>2</sup> |N/A|GA| GA | GA |

<sup>1</sup> 런타임 버전 2.x를 대상으로 하는 .NET 클래스 라이브러리 앱은 .NET Core 2.x 호환성 모드의 .NET Core 3.1에서 실행됩니다. 자세히 알아보려면 [Functions v2.x 고려 사항](../articles/azure-functions/functions-dotnet-class-library.md#functions-v2x-considerations)을 참조하세요.  
<sup>2</sup>JavaScript로의 변환 컴파일링(transpiling)을 통해 지원됩니다.

지원되는 언어 버전에 대한 자세한 내용은 언어별 개발자 가이드 문서를 참조하세요.   
언어 지원 변경 계획에 대한 정보는 [Azure 로드맵](https://azure.microsoft.com/roadmap/?tag=functions)을 참조하세요.
