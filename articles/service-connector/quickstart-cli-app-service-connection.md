---
title: 빠른 시작 - Azure CLI를 사용하여 App Service에서 서비스 연결 만들기
description: Azure CLI를 사용하여 App Service에서 서비스 연결을 만드는 방법을 보여 주는 빠른 시작
author: shizn
ms.author: xshi
ms.service: serviceconnector
ms.topic: quickstart
ms.date: 10/29/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: a960bc3064b970fbfd463d0a7f1577354ca7adf1
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/05/2021
ms.locfileid: "131850283"
---
# <a name="quickstart-create-a-service-connection-in-app-service-with-the-azure-cli"></a>빠른 시작: Azure CLI를 사용하여 App Service에서 서비스 연결 만들기

[Azure CLI(Azure 명령줄 인터페이스)](/cli/azure)는 Azure 리소스를 만들고 관리하는 데 사용되는 명령 세트입니다. Azure CLI는 모든 Azure 서비스에서 사용할 수 있으며, Azure를 빠르게 사용할 수 있도록 자동화에 초점을 두고 설계되었습니다. 이 빠른 시작에서는 Azure CLI를 사용하여 Azure Web PubSub 인스턴스를 만드는 옵션을 보여줍니다.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- 이 빠른 시작에는 Azure CLI 버전 2.30.0 이상이 필요합니다. Azure Cloud Shell을 사용하는 경우 최신 버전이 이미 설치되어 있습니다.

- 이 빠른 시작에서는 Azure에서 이미 실행 중인 App Service가 있다고 가정합니다. App Service가 없는 경우 [새로 만듭니다](../app-service/quickstart-dotnetcore.md).

## <a name="view-supported-target-service-types"></a>지원되는 대상 서비스 유형 보기

Azure CLI [az webapp connection](/cli/azure/webapp/connection) 명령을 사용하여 App Service에 대한 서비스 연결을 만들고 관리할 수 있습니다. 

```azurecli-interactive
az provider register -n Microsoft.ServiceLinker
az webapp connection list-support-types --output table
```

## <a name="create-a-service-connection"></a>서비스 연결 만들기

#### <a name="using-access-key"></a>[액세스 키 사용](#tab/Using-access-key)

Azure CLI [az webapp connection](/cli/azure/webapp/connection) 명령을 사용해 BLOB 스토리지에 대한 서비스 연결과 엑세스 키를 만들고 다음 정보를 제공할 수 있습니다.

- **원본 컴퓨팅 서비스 리소스 그룹 이름:** App Service의 리소스 그룹 이름입니다.
- **App Service 이름:** 대상 서비스에 연결하는 App Service의 이름입니다.
- **대상 서비스 리소스 그룹 이름:** BLOB 스토리지의 리소스 그룹 이름입니다.
- **스토리지 계정 이름:** BLOB 스토리지의 계정 이름입니다.

```azurecli-interactive
az webapp connection create storage-blob --secret
```

> [!NOTE]
> BLOB 스토리지가 없는 경우, `az webapp connection create storage-blob --new --secret`를 실행해 새로운 스토리지를 만들어 애플리케이션 서비스에 바로 연결할 수 있습니다.

#### <a name="using-managed-identity"></a>[관리 ID를 사용하는 경우](#tab/Using-Managed-Identity)

> [!IMPORTANT]
> 관리 ID를 사용하려면 [AZURE AD 역할 할당](/active-directory/managed-identities-azure-resources/howto-assign-access-portal)에 대한 권한이 있어야 합니다. 권한이 없으면 연결을 만들 수 없습니다. 구독 소유자에게 사용 권한을 요청하거나 액세스 키를 사용하여 연결을 만들 수 있습니다.

Azure CLI [az webapp connection](/cli/azure/webapp/connection) 명령을 사용해 BLOB 스토리지에 대한 서비스 연결과 시스템이 할당한 관리 ID를 만들고 다음 정보를 제공할 수 있습니다.

- **원본 컴퓨팅 서비스 리소스 그룹 이름:** App Service의 리소스 그룹 이름입니다.
- **App Service 이름:** 대상 서비스에 연결하는 App Service의 이름입니다.
- **대상 서비스 리소스 그룹 이름:** BLOB 스토리지의 리소스 그룹 이름입니다.
- **스토리지 계정 이름:** BLOB 스토리지의 계정 이름입니다.

```azurecli-interactive
az webapp connection create storage-blob --system-identity
```

> [!NOTE]
> BLOB 스토리지가 없는 경우, `az webapp connection create storage-blob --new --system-identity`를 실행해 새로운 스토리지를 만들어 애플리케이션 서비스에 바로 연결할 수 있습니다.

---

## <a name="view-connections"></a>연결 보기

Azure CLI [az webapp connection](/cli/azure/webapp/connection) 명령을 사용해 App Service에 대한 연결을 나열하고 다음 정보를 제공할 수 있습니다.

- **원본 컴퓨팅 서비스 리소스 그룹 이름:** App Service의 리소스 그룹 이름입니다.
- **App Service 이름:** 대상 서비스에 연결하는 App Service의 이름입니다.

```azurecli-interactive
az webapp connection list -g "<your-app-service-resource-group>" --webapp "<your-app-service-name>"
```

## <a name="next-steps"></a>다음 단계

아래 나열된 자습서를 따라 서비스 커넥터로 사용자만의 애플리케이션을 빌드할 수 있습니다.

> [!div class="nextstepaction"]
> [자습서: WebApp + Azure CLI를 사용하는 스토리지](./tutorial-csharp-webapp-storage-cli.md)

> [!div class="nextstepaction"]
> [자습서: WebApp + Azure CLI를 사용하는 PostgreSQL](./tutorial-django-webapp-postgres-cli.md)
