---
title: 서버 다시 시작 - Azure PowerShell - Azure Database for MariaDB
description: 이 문서에서는 PowerShell을 사용하여 Azure Database for MariaDB 서버를 다시 시작하는 방법을 설명합니다.
author: savjani
ms.author: pariks
ms.service: mariadb
ms.topic: how-to
ms.date: 5/26/2020
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 14cde5b1302c46b819bb7d841fb5b84a43c580c0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98664871"
---
# <a name="restart-azure-database-for-mariadb-server-using-powershell"></a>PowerShell을 사용하여 Azure Database for MariaDB 서버 다시 시작

이 항목에서는 Azure Database for MariaDB 서버를 다시 시작하는 방법을 설명합니다. 유지 관리를 위해 서버를 다시 시작해야 할 수 있지만 이 경우 해당 작업 중에 잠시 가동이 중단됩니다.

서비스가 사용 중이면 서버가 다시 시작되지 않습니다. 예를 들어, 서비스가 vCore 크기를 조정하는 것과 같이 이전에 요청된 작업을 처리할 수 있습니다.

다시 시작을 완료하는 데 필요한 시간은 MariaDB 복구 프로세스에 따라 달라집니다. 다시 시작 시간을 줄이려면 다시 시작 전에 서버에서 발생하는 작업의 양을 최소화하는 것이 좋습니다.

## <a name="prerequisites"></a>사전 요구 사항

이 방법 가이드를 완료하려면 다음이 필요합니다.

- 로컬로 설치된 [Az PowerShell 모듈](/powershell/azure/install-az-ps) 또는 브라우저의 [Azure Cloud Shell](https://shell.azure.com/)
- [Azure Database for MariaDB 서버](quickstart-create-mariadb-server-database-using-azure-powershell.md)

> [!IMPORTANT]
> Az.MariaDb PowerShell 모듈이 미리 보기에 있지만 `Install-Module -Name Az.MariaDb -AllowPrerelease` 명령을 사용하여 Az PowerShell 모듈과 별도로 설치해야 합니다.
> Az.MariaDb PowerShell 모듈이 일반 공급되면 이후 Az PowerShell 모듈 릴리스에 포함되며 Azure Cloud Shell 내에서 기본적으로 사용할 수 있습니다.

PowerShell을 로컬로 사용하도록 선택한 경우 [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) cmdlet을 사용하여 Azure 계정에 연결합니다.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="restart-the-server"></a>서버 다시 시작

다음 명령을 사용하여 서버를 다시 시작합니다.

```azurepowershell-interactive
Restart-AzMariaDbServer -Name mydemoserver -ResourceGroupName myresourcegroup
```

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [PowerShell을 사용하여 Azure Database for MariaDB 서버 만들기](quickstart-create-mariadb-server-database-using-azure-powershell.md)