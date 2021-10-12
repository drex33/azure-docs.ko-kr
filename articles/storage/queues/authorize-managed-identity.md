---
title: 관리 ID를 사용하여 큐 데이터에 대한 액세스 권한 부여
titleSuffix: Azure Storage
description: Azure 리소스에 대한 관리 ID를 사용하여 Azure VM, 함수 앱 등에서 실행되는 애플리케이션의 큐 데이터 액세스 권한을 부여합니다.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 10/11/2021
ms.author: tamram
ms.reviewer: santoshc
ms.subservice: common
ms.custom: devx-track-csharp
ms.openlocfilehash: 0aac0689a18d9c2a525fbd15aa2b47e4ca960ebb
ms.sourcegitcommit: d2875bdbcf1bbd7c06834f0e71d9b98cea7c6652
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/12/2021
ms.locfileid: "129859533"
---
# <a name="authorize-access-to-queue-data-with-managed-identities-for-azure-resources"></a>Azure 리소스에 대한 관리 ID를 사용하여 큐 데이터에 대한 액세스 권한 부여

Azure Queue Storage Azure 리소스에 대한 관리 ID를 사용하는 Azure AD(Azure Active Directory) [인증을 지원합니다.](../../active-directory/managed-identities-azure-resources/overview.md) Azure 리소스에 대한 관리 ID는 Azure VM(가상 머신), 함수 앱, 가상 머신 확장 집합 및 기타 서비스에서 실행되는 애플리케이션의 Azure AD 자격 증명을 사용하여 큐 데이터에 대한 액세스 권한을 부여할 수 있습니다. Azure AD 인증과 함께 Azure 리소스의 관리 ID를 사용하면 클라우드에서 실행되는 애플리케이션에 자격 증명을 저장할 필요가 없습니다.

이 문서에서는 Azure 리소스에 대한 관리 ID를 사용하여 Azure VM에서 큐 데이터에 대한 액세스 권한을 부여하는 방법을 보여줍니다.

## <a name="enable-managed-identities-on-a-vm"></a>VM에서 관리 ID 사용

Azure 리소스에 대한 관리 ID를 사용하여 VM에서 큐에 대한 액세스 권한을 부여하려면 먼저 VM에서 Azure 리소스에 대한 관리 ID를 사용하도록 설정해야 합니다. Azure 리소스의 관리 ID를 사용하도록 설정하는 방법을 알아보려면 다음 문서 중 하나를 참조하세요.

- [Azure Portal](../../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md)
- [Azure PowerShell](../../active-directory/managed-identities-azure-resources/qs-configure-powershell-windows-vm.md)
- [Azure CLI](../../active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm.md)
- [Azure Resource Manager 템플릿](../../active-directory/managed-identities-azure-resources/qs-configure-template-windows-vm.md)
- [Azure Resource Manager 클라이언트 라이브러리](../../active-directory/managed-identities-azure-resources/qs-configure-sdk-windows-vm.md)

관리 ID에 대한 자세한 내용은 [Azure 리소스의 관리 ID](../../active-directory/managed-identities-azure-resources/overview.md)를 참조하세요.

## <a name="assign-an-rbac-role-to-a-managed-identity"></a>관리 ID에 RBAC 역할 할당

Azure AD 보안 주체가 Azure Storage 계정의 데이터에 액세스하려고 할 때 해당 보안 주체에는 데이터 리소스에 대한 권한이 있어야 합니다. 보안 주체가 Azure의 관리 ID이든 개발 환경에서 코드를 실행하는 Azure AD 사용자 계정이든 관계없이 보안 주체에는 Azure Storage 데이터에 대한 액세스 권한을 부여하는 Azure 역할이 할당되어야 합니다. Azure RBAC를 통한 데이터 액세스 권한 할당에 대한 자세한 내용은 [큐 데이터에 액세스하기 위한 Azure 역할 할당을 참조하세요.](assign-azure-role-data-access.md)

> [!NOTE]
> Azure Storage 계정을 만들면 Azure AD를 통해 데이터에 액세스할 수 있는 권한이 자동으로 할당되지 않습니다. Azure Storage에 Azure 역할을 직접 명시적으로 할당해야 합니다. 구독, 리소스 그룹, 스토리지 계정 또는 큐 수준으로 지정할 수 있습니다.

## <a name="use-a-managed-identity-to-create-a-queue-in-net"></a>관리 ID를 사용하여 .NET에서 큐 만들기

Azure ID 클라이언트 라이브러리는 [Azure SDK를](https://github.com/Azure/azure-sdk)통해 azure AD(Azure Active Directory)로 권한 부여를 위한 OAuth 2.0 액세스 토큰을 얻는 프로세스를 간소화합니다. Azure ID 클라이언트 라이브러리를 사용하여 액세스 토큰을 얻는 경우 애플리케이션이 개발 환경 또는 Azure에서 실행 중인지에 관계없이 동일한 코드를 사용하여 토큰을 획득할 수 있습니다. 자세한 내용은 [Azure ID 라이브러리를 사용하여 권한 부여를 위한 액세스 토큰 얻기를 참조하세요.](../common/identity-library-acquire-token.md)

코드에서 Azure Storage 요청에 권한을 부여하는 데 사용할 수 있는 토큰을 얻으려면 [DefaultAzureCredential](/dotnet/api/azure.identity.defaultazurecredential) 클래스의 인스턴스를 만듭니다. 그런 다음 토큰을 사용하여 Azure Storage 데이터 작업을 수행할 권한이 있는 서비스 클라이언트 개체를 만들 수 있습니다. **DefaultAzureCredential** 클래스를 사용하여 관리 ID에 Azure Storage 액세스 권한을 부여하는 자세한 내용은 [.NET용 Azure ID 클라이언트 라이브러리를](/dotnet/api/overview/azure/identity-readme)참조하세요.

다음 코드 예제에서는 인증된 토큰 자격 증명을 구하고 이를 사용하여 서비스 클라이언트 개체를 만든 다음, 서비스 클라이언트를 사용하여 큐를 만드는 방법을 보여줍니다.

```csharp
public static void CreateQueue(string accountName, string queueName)
{
    // Construct the blob container endpoint from the arguments.
    string queueEndpoint = string.Format("https://{0}.queue.core.windows.net/{1}",
                                                accountName,
                                                queueName);

    // Get a token credential and create a service client object for the queue.
    QueueClient queueClient = new QueueClient(new Uri(queueEndpoint), 
                                                new DefaultAzureCredential());

    try
    {
        // Create the queue.
        queueClient.CreateIfNotExists();
    }
    catch (RequestFailedException e)
    {
        Console.WriteLine("Exception: {0}", e.Message);
    }
}
```

> [!NOTE]
> Azure AD를 사용하여 큐 데이터에 대한 요청에 권한을 부여하려면 해당 요청에 HTTPS를 사용해야 합니다.

## <a name="next-steps"></a>다음 단계

- [대기열 데이터에 액세스하기 위한 Azure 역할 할당](assign-azure-role-data-access.md)
- [네이티브 또는 웹 애플리케이션에서 Blob 또는 큐 데이터에 대한 액세스 권한 부여](../common/storage-auth-aad-app.md)
