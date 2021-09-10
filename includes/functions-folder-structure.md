---
title: 포함 파일
description: 포함 파일
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 06/17/2021
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: cc661942dc1cf110a07df383149b03c8a4ea7409
ms.sourcegitcommit: 6a3096e92c5ae2540f2b3fe040bd18b70aa257ae
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/17/2021
ms.locfileid: "112321045"
---
특정 함수 앱의 모든 기능에 대한 코드는 호스트 구성 파일이 포함된 루트 프로젝트 폴더에 있습니다. [host.json](../articles/azure-functions/functions-host-json.md) 파일은 런타임별 구성을 포함하며 함수 앱의 루트 폴더에 있습니다. *bin* 폴더에는 함수 앱에 필요한 패키지 및 기타 라이브러리 파일이 포함되어 있습니다. 함수 앱에 필요한 특정 폴더 구조는 언어에 따라 달라집니다.

* [C# 컴파일(.csproj)](../articles/azure-functions/functions-dotnet-class-library.md#functions-class-library-project)
* [C# 스크립트(.csx)](../articles/azure-functions/functions-reference-csharp.md#folder-structure)
* [F# 스크립트](../articles/azure-functions/functions-reference-fsharp.md#folder-structure)
* [Java](../articles/azure-functions/functions-reference-java.md#folder-structure)
* [JavaScript](../articles/azure-functions/functions-reference-node.md#folder-structure)
* [Python](../articles/azure-functions/functions-reference-python.md#folder-structure)

Functions 런타임의 2.x 이상 버전부터 함수 앱의 모든 함수는 동일한 언어 스택을 공유해야 합니다. 
