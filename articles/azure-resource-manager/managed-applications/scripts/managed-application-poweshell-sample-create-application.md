---
title: Azure PowerShell 스크립트 샘플 - 관리되는 애플리케이션 배포
description: 구독에 관리되는 애플리케이션 정의를 배포하는 Azure PowerShell 샘플 스크립트를 제공합니다.
author: tfitzmac
ms.devlang: powershell
ms.topic: sample
ms.date: 10/27/2017
ms.author: tomfitz
ms.openlocfilehash: e12e9f50d5c2608006a64fb80362877735bdd01c
ms.sourcegitcommit: b00a2d931b0d6f1d4ea5d4127f74fc831fb0bca9
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/20/2021
ms.locfileid: "132862601"
---
# <a name="deploy-a-managed-application-for-a-service-catalog-with-powershell"></a>PowerShell을 사용하여 서비스 카탈로그용 관리되는 애플리케이션 배포

이 스크립트는 서비스 카탈로그에서 관리되는 애플리케이션 정의를 배포합니다.


[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>샘플 스크립트

[!code-powershell[main](../../../../powershell_scripts/managed-applications/create-application/create-application.ps1 "Create application")]


## <a name="script-explanation"></a>스크립트 설명

이 스크립트는 다음 명령을 사용하여 관리되는 애플리케이션을 배포합니다. 표에 있는 각 명령은 명령에 해당하는 문서에 연결됩니다.

| 명령 | 메모 |
|---|---|
| [New-AzManagedApplication](/powershell/module/az.resources/new-azmanagedapplication) | 관리되는 애플리케이션을 만듭니다. 템플릿에 대한 정의 ID 및 매개 변수를 제공합니다. |


## <a name="next-steps"></a>다음 단계

* 관리되는 애플리케이션에 대한 소개는 [Azure Managed Application 개요](../overview.md)를 참조하세요.
* PowerShell에 대한 자세한 내용은 [Azure PowerShell 설명서](/powershell/azure/get-started-azureps)를 참조하세요.
