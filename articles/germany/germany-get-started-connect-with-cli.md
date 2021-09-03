---
title: Azure CLI를 사용하여 Azure 독일에 연결 | Microsoft Docs
description: Azure CLI를 사용하여 Azure 독일에서 구독을 관리하는 방법에 대한 정보
ms.topic: article
ms.date: 10/16/2020
author: gitralf
ms.author: ralfwi
ms.service: germany
ms.custom: bfdocs, devx-track-azurecli
ms.openlocfilehash: b5fb2f9b55fbe949e2c171ee7bea989c1741ab5d
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/21/2020
ms.locfileid: "122535446"
---
# <a name="connect-to-azure-germany-by-using-azure-cli"></a>Azure CLI를 사용하여 Azure 독일에 연결

[!INCLUDE [closureinfo](../../includes/germany-closure-info.md)]

Azure CLI(Azure 명령줄 인터페이스)를 사용하려면 글로벌 Azure 대신 Azure 독일에 연결해야 합니다. Azure CLI를 사용하여 스크립트를 통해 많은 구독을 관리하거나 Azure Portal에서 현재 사용할 수 없는 기능에 액세스할 수 있습니다. 글로벌 Azure에서 Azure CLI를 사용한 적이 있다면 대부분 동일합니다.  

## <a name="azure-cli"></a>Azure CLI
[Azure CLI를 설치](/cli/azure/install-az-cli2)하는 여러 방법이 있습니다.  

Azure 독일에 연결하려면 클라우드를 설정합니다.

```azurecli
az cloud set --name AzureGermanCloud
```

클라우드가 설정되면 다음과 같이 로그인할 수 있습니다.

```azurecli
az login --username your-user-name@your-tenant.onmicrosoft.de
```

클라우드가 AzureGermanCloud로 올바르게 설정되었는지 확인하려면 다음 명령 중 하나를 실행한 후 AzureGermanCloud 항목에 대해 `isActive` 플래그가 `true`로 설정되어 있는지 확인합니다.

```azurecli
az cloud list
```

```azurecli
az cloud list --output table
```

## <a name="azure-classic-cli"></a>Azure 클래식 CLI
[Azure 클래식 CLI 설치](/cli/azure/install-azure-cli) 방법에는 여러 가지가 있습니다. 노드를 이미 설치한 경우 npm 패키지를 설치하는 것이 가장 쉬운 방법입니다.

npm 패키지에서 CLI를 설치하려면 [최신 Node.js 및 npm](https://nodejs.org/en/download/package-manager/)을 다운로드하고 설치했는지 확인합니다. 그런 다음 **npm install** **azure-cli** 패키지를 설치합니다.

```bash
npm install -g azure-cli
```

Linux 배포에서는 **npm** 명령을 정상적으로 실행하기 위해 다음과 같이 **sudo** 를 사용해야 할 수도 있습니다.

```bash
sudo npm install -g azure-cli
```

> [!NOTE]
> Linux 배포 또는 OS에 Node.js 및 npm을 설치하거나 업데이트해야 하는 경우 최신 Node.js LTS 버전(4.x)을 설치하는 것이 좋습니다. 이전 버전을 사용하는 경우 설치 오류가 발생할 수 있습니다.


Azure CLI가 설치된 후 Azure 독일에 로그인합니다.

```console
azure login --username your-user-name@your-tenant.onmicrosoft.de  --environment AzureGermanCloud
```

로그인한 후 평소와 같이 Azure CLI 명령을 실행할 수 있습니다.

```console
azure webapp list my-resource-group
```

## <a name="next-steps"></a>다음 단계
Azure 독일 연결에 대한 자세한 내용은 다음 리소스를 참조하세요.

* [PowerShell을 사용하여 Azure 독일에 연결](./germany-get-started-connect-with-ps.md)
* [Visual Studio를 사용하여 Azure 독일에 연결](./germany-get-started-connect-with-vs.md)
* [Azure Portal을 사용하여 Azure 독일에 연결](./germany-get-started-connect-with-portal.md)