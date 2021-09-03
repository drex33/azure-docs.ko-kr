---
title: Microsoft Graph에 대한 액세스 토큰의 유효성을 검사하지 마세요.
description: Microsoft Graph에 대한 액세스 토큰은 불투명한 것으로 간주되어야 하며 고객 코드로 유효성을 검사해서는 안 된다는 경고 파일을 포함합니다. Microsoft Graph만 Microsoft Graph 액세스 토큰의 유효성을 검사합니다.
services: active-directory
author: hpsin
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: include
ms.date: 06/25/2021
ms.author: hirsin
ms.reviewer: hirsin
ms.custom: aaddev
ms.openlocfilehash: ab38a17f96bf10d5ae6745ede5c89c90eb47fda7
ms.sourcegitcommit: 82d82642daa5c452a39c3b3d57cd849c06df21b0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/07/2021
ms.locfileid: "113432076"
---
> [!WARNING]
> 코드에서 이 예제의 토큰을 포함하여 소유하지 않은 API에 대한 토큰을 확인하거나 읽으려고 시도하지 마세요.  Microsoft 서비스 토큰은 JWT로 유효성을 검사하지 않는 특수 형식을 사용할 수 있으며 소비자(Microsoft 계정) 사용자에 대해 암호화될 수도 있습니다. 토큰 읽기는 유용한 디버깅 및 학습 도구이지만 코드에서 이에 대한 종속성을 취하거나 제어하는 API용이 아닌 토큰에 대한 세부 사항을 가정하지 마세요.
