---
title: 관리 id를 사용 하 여 테이블 데이터에 대 한 액세스 권한 부여 (미리 보기)
titleSuffix: Azure Storage
description: Azure 리소스에 관리 되는 id를 사용 하 여 Azure Vm, 함수 앱 등에서 실행 되는 응용 프로그램의 테이블 데이터 액세스 권한을 부여 합니다.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 10/11/2021
ms.author: tamram
ms.reviewer: santoshc
ms.subservice: common
ms.custom: devx-track-csharp
ms.openlocfilehash: 0d6410115f9ba12f0beb5dc1408a076753182797
ms.sourcegitcommit: d2875bdbcf1bbd7c06834f0e71d9b98cea7c6652
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/12/2021
ms.locfileid: "129859544"
---
# <a name="authorize-access-to-table-data-with-managed-identities-for-azure-resources-preview"></a>Azure 리소스에 대 한 관리 id를 사용 하 여 테이블 데이터에 대 한 액세스 권한 부여 (미리 보기)

azure Table Storage [는 azure 리소스에 대 한 관리 id](../../active-directory/managed-identities-azure-resources/overview.md)를 사용 하 여 Azure Active Directory (azure AD) 인증을 지원 합니다. Azure 리소스에 대 한 관리 id는 azure Vm (가상 머신), 함수 앱, 가상 머신 확장 집합 및 기타 서비스에서 실행 되는 응용 프로그램의 Azure AD 자격 증명을 사용 하 여 테이블 데이터에 대 한 액세스 권한을 부여할 수 있습니다. Azure AD 인증과 함께 Azure 리소스의 관리 ID를 사용하면 클라우드에서 실행되는 애플리케이션에 자격 증명을 저장할 필요가 없습니다.

이 문서에서는 Azure 리소스에 대 한 관리 id를 사용 하 여 Azure VM에서 테이블 데이터에 대 한 액세스 권한을 부여 하는 방법을 보여 줍니다.

> [!IMPORTANT]
> 테이블에 대한 Azure AD 권한 부여는 현재 **미리 보기** 로 제공됩니다. 베타, 미리 보기로 제공되거나 아직 일반 공급으로 릴리스되지 않은 Azure 기능에 적용되는 약관은 [Microsoft Azure 미리 보기에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

## <a name="enable-managed-identities-on-a-vm"></a>VM에서 관리 ID 사용

Azure 리소스에 관리 되는 id를 사용 하 여 VM에서 테이블에 대 한 액세스 권한을 부여 하려면 먼저 VM에서 Azure 리소스에 대 한 관리 되는 id를 사용 하도록 설정 해야 합니다. Azure 리소스의 관리 ID를 사용하도록 설정하는 방법을 알아보려면 다음 문서 중 하나를 참조하세요.

- [Azure Portal](../../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md)
- [Azure PowerShell](../../active-directory/managed-identities-azure-resources/qs-configure-powershell-windows-vm.md)
- [Azure CLI](../../active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm.md)
- [Azure Resource Manager 템플릿](../../active-directory/managed-identities-azure-resources/qs-configure-template-windows-vm.md)
- [Azure Resource Manager 클라이언트 라이브러리](../../active-directory/managed-identities-azure-resources/qs-configure-sdk-windows-vm.md)

관리 ID에 대한 자세한 내용은 [Azure 리소스의 관리 ID](../../active-directory/managed-identities-azure-resources/overview.md)를 참조하세요.

## <a name="assign-an-rbac-role-to-a-managed-identity"></a>관리 id에 RBAC 역할 할당

Azure AD 보안 주체가 Azure Storage 계정의 데이터에 액세스 하려고 하면 해당 보안 주체에 게 데이터 리소스에 대 한 권한이 있어야 합니다. 보안 주체가 Azure에서 관리 되는 id이 든, 개발 환경에서 코드를 실행 하는 Azure AD 사용자 계정 인지 여부에 관계 없이 보안 주체는 Azure Storage 데이터에 대 한 액세스 권한을 부여 하는 Azure 역할에 할당 되어야 합니다. Azure RBAC를 통해 데이터 액세스 권한을 할당 하는 방법에 대 한 자세한 내용은 [blob 데이터에 액세스 하기 위한 azure 역할 할당](assign-azure-role-data-access.md)을 참조 하세요.

> [!NOTE]
> Azure Storage 계정을 만들면 Azure AD를 통해 데이터에 액세스할 수 있는 권한이 자동으로 할당되지 않습니다. Azure Storage에 Azure 역할을 직접 명시적으로 할당해야 합니다. 구독, 리소스 그룹, 스토리지 계정 또는 테이블 수준으로 지정할 수 있습니다.

## <a name="use-a-managed-identity-to-create-a-table-in-net"></a>관리 id를 사용 하 여 .NET에서 테이블 만들기

azure id 클라이언트 라이브러리는 azure [SDK](https://github.com/Azure/azure-sdk)를 통해 Azure Active Directory (azure AD)를 사용 하 여 권한 부여에 대 한 OAuth 2.0 액세스 토큰을 가져오는 프로세스를 간소화 합니다. Azure Id 클라이언트 라이브러리를 사용 하 여 액세스 토큰을 가져오는 경우 응용 프로그램이 개발 환경 또는 Azure에서 실행 되는지에 관계 없이 동일한 코드를 사용 하 여 토큰을 가져올 수 있습니다. 자세한 내용은 [Azure id 라이브러리를 사용 하 여 권한 부여에 대 한 액세스 토큰 가져오기](../common/identity-library-acquire-token.md)를 참조 하세요.

코드에서 Azure Storage 요청에 권한을 부여 하는 데 사용할 수 있는 토큰을 가져오려면 [DefaultAzureCredential](/dotnet/api/azure.identity.defaultazurecredential) 클래스의 인스턴스를 만듭니다. 그런 다음 토큰을 사용 하 여 Azure Storage에서 데이터 작업을 수행할 수 있는 권한을 부여 받은 서비스 클라이언트 개체를 만들 수 있습니다. **DefaultAzureCredential** 클래스를 사용 하 여 Azure Storage에 대 한 액세스 권한을 관리 되는 id에 부여 하는 방법에 대 한 자세한 내용은 [.net 용 Azure id 클라이언트 라이브러리](/dotnet/api/overview/azure/identity-readme)를 참조 하세요.

다음 코드 예제에서는 인증 된 토큰 자격 증명을 가져와서이를 사용 하 여 서비스 클라이언트 개체를 만든 다음, 서비스 클라이언트를 사용 하 여 테이블을 만드는 방법을 보여 줍니다.

```csharp
public static void CreateTable(string accountName, string tableName)
{
    // Construct the table endpoint from the arguments.
    string tableEndpoint = string.Format("https://{0}.table.core.windows.net/",
                                                accountName);

    // Get a token credential and create a service client object for the table.
    TableClient tableClient = new TableClient(new Uri(tableEndpoint), 
                                                tableName, 
                                                new DefaultAzureCredential());

    try
    {
        // Create the table.
        tableClient.Create();

    }
    catch (RequestFailedException e)
    {
        Console.WriteLine("Exception: {0}", e.Message);
    }
}
```

> [!NOTE]
> Azure AD를 사용 하 여 테이블 데이터에 대 한 요청에 권한을 부여 하려면 해당 요청에 대해 HTTPS를 사용 해야 합니다.

## <a name="next-steps"></a>다음 단계

- [테이블 데이터에 액세스하기 위한 Azure 역할 할당](assign-azure-role-data-access.md)
- [Azure Active Directory를 사용하여 테이블에 대한 액세스 권한 부여](authorize-access-azure-active-directory.md)