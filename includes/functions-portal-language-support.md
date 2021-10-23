---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 07/23/2021
ms.author: glenga
ms.openlocfilehash: 8d580bbfd87f1d1df9d36be95a98c65d80736806
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130224313"
---
## <a name="language-support-details"></a>언어 지원 세부 정보 

다음 표에서는 Linux 또는 Windows에서 실행할 수 있는 기능에서 지 원하는 언어를 보여 줍니다. 또한 언어가 Azure Portal 편집을 지원 하는지 여부를 나타냅니다. 이 언어는 [Azure Portal에서 함수 앱을 만들](../articles/azure-functions/functions-create-function-app-portal.md#create-a-function-app)때 선택 하는 **런타임 스택** 옵션을 기반으로 합니다. 이 `--worker-runtime` 옵션은 Azure Functions Core Tools에서 명령을 사용 하는 경우와 동일 합니다 `func init` . 

| 언어 | 런타임 스택 | Linux | Windows | 포털 내 편집<sup>1</sup> |
|:--- |:-- |:--|:--- |:--- |
| [C # 클래스 라이브러리](../articles/azure-functions/functions-dotnet-class-library.md)<sup>2</sup> |.NET|✓ |✓ | | 
| [C # 스크립트](../articles/azure-functions/functions-reference-csharp.md) | .NET | ✓ |✓ |✓ |
| [JavaScript](../articles/azure-functions/functions-reference-node.md) | Node.js |✓ |✓ | ✓ |
| [Python](../articles/azure-functions/functions-reference-python.md) | Python |✓ | | |
| [Java](../articles/azure-functions/functions-reference-java.md) | Java |✓ |✓ | |
| [PowerShell](../articles/azure-functions/functions-reference-powershell.md) |PowerShell Core | |✓ |✓ |
| [TypeScript](../articles/azure-functions/functions-reference-node.md) | Node.js |✓ |✓ |  |
| [Go/Rust/other](../articles/azure-functions/functions-custom-handlers.md) | 사용자 지정 처리기 |✓ |✓ | |

<sup>1</sup> Linux에서 실행 하는 경우 포털 내 편집은 [전용 (App Service) 계획](../articles/azure-functions/dedicated-plan.md)에서만 지원 됩니다.   
<sup>2</sup> 포털에서 현재 .net 5.0에서 실행 되는 함수 앱을 만들 수 없습니다. 자세한 내용은 [Azure Functions를 사용 하 여 .net 5 함수 개발 및 게시](../articles/azure-functions/dotnet-isolated-process-guide.md)를 참조 하세요. 

자세한 내용은 [운영 체제/런타임 지원](../articles/azure-functions/functions-scale.md#operating-systemruntime)을 참조 하세요. 

포털 내 편집을 사용할 수 없는 경우에는 대신 [함수를 로컬로 개발](../articles/azure-functions/functions-develop-local.md#local-development-environments)해야 합니다.