---
title: Azure Functions에서 지원되는 언어
description: 지원되는 언어(GA)와 미리 보기에 있는 언어 및 Functions 개발을 다른 언어로 확장하는 방법을 알아봅니다.
ms.topic: conceptual
ms.date: 11/27/2019
ms.openlocfilehash: 02a416fa669cd71702e991c79677575bd4563bfb
ms.sourcegitcommit: bc29cf4472118c8e33e20b420d3adb17226bee3f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/08/2021
ms.locfileid: "113493251"
---
# <a name="supported-languages-in-azure-functions"></a>Azure Functions에서 지원되는 언어

이 문서에서는 Azure Functions에서 사용할 수 있는 언어에 대해 제공되는 지원 수준을 설명합니다. 또한 기본적으로 지원되지 않는 언어를 사용하여 함수를 만드는 전략에 대해서도 설명합니다.

## <a name="levels-of-support"></a>지원 수준

지원에는 두 가지 수준이 있습니다.

* **일반 공급(GA)** - 완전하게 지원되며 프로덕션용으로 승인되었습니다.
* **미리 보기** - 아직 지원되지 않지만 향후 GA 상태가 될 예정입니다.

## <a name="languages-by-runtime-version"></a>런타임 버전별 언어 

[세 가지 버전의 Azure Functions 런타임](functions-versions.md)을 사용할 수 있습니다. 다음 표는 각 런타임 버전에서 지원되는 언어를 나타냅니다.

[!INCLUDE [functions-supported-languages](../../includes/functions-supported-languages.md)]

### <a name="language-major-version-support"></a>언어 주 버전 지원

Azure Functions는 지원되는 프로그래밍 언어의 주요 버전에 대한 지원을 보장합니다. 대부분의 언어의 경우 지원되는 주 버전을 업데이트하기 위해 부 버전 또는 패치 버전이 출시되었습니다. 부 버전 또는 패치 버전의 예로는 Python 3.9.1 및 Node 14.17이 있습니다. 지원되는 언어의 새로운 부 버전을 사용할 수 있게 되면 함수 앱에서 사용하는 부 버전이 이러한 최신 부 버전 또는 패치 버전으로 자동 업그레이드됩니다. 

> [!NOTE]
>Azure Functions는 새로운 부 버전을 사용할 수 있게 되면 언제든지 이전 부 버전의 지원을 제거할 수 있으므로 함수 앱을 프로그래밍 언어의 특정 부/패치 버전에 고정하지 않아야 합니다.  
>

## <a name="custom-handlers"></a>사용자 지정 처리기

사용자 지정 처리기는 Azure Functions 호스트에서 이벤트를 받는 간단한 웹 서버입니다. HTTP 기본 형식을 지원하는 모든 언어는 사용자 지정 처리기를 구현할 수 있습니다. 즉, 사용자 지정 처리기를 사용하여 공식적으로 지원되지 않는 언어로 함수를 만들 수 있습니다. 자세한 내용은 [Azure Functions 사용자 지정 처리기](functions-custom-handlers.md)를 참조하세요.

## <a name="language-extensibility"></a>언어 확장성

버전 2.x부터 런타임은 [언어 확장성](https://github.com/Azure/azure-webjobs-sdk-script/wiki/Language-Extensibility)을 제공하도록 설계되었습니다. 2.x 런타임의 JavaScript 및 Java 언어는 이 확장성을 사용하여 빌드됩니다.

## <a name="next-steps"></a>다음 단계

지원되는 언어로 함수를 개발하는 방법에 대한 자세한 내용은 다음 리소스를 참조하세요.

+ [C# 클래스 라이브러리 개발자 참조](functions-dotnet-class-library.md)
+ [C# 스크립트 개발자 참조](functions-reference-csharp.md)
+ [Java 개발자 참조](functions-reference-java.md)
+ [JavaScript 개발자 참조](functions-reference-node.md)
+ [PowerShell 개발자 참조](functions-reference-powershell.md)
+ [Python 개발자 참조](functions-reference-python.md)
+ [TypeScript 개발자 참조](functions-reference-node.md#typescript)
