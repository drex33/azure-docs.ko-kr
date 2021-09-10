---
title: Storage Explorer 시작 | Microsoft Docs
description: Storage Explorer로 Azure Storage 리소스 관리 시작. Azure Storage Explorer를 다운로드하여 설치하고, 스토리지 계정 또는 서비스에 연결합니다.
services: storage
author: cawaMS
ms.service: storage
ms.devlang: multiple
ms.topic: article
ms.date: 11/08/2019
ms.author: cawa
ms.openlocfilehash: 969e2d2424b9f9af1dcd921a1cebfa9407e313eb
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/22/2021
ms.locfileid: "114438430"
---
# <a name="get-started-with-storage-explorer"></a>Storage Explorer 시작

## <a name="overview"></a>개요

Microsoft Azure Storage Explorer는 Windows, macOS 및 Linux에서 Azure Storage 데이터를 쉽게 사용할 수 있게 하는 독립 실행형 앱입니다.

이 문서에서는 Azure Storage 계정에 연결하고 관리하는 몇 가지 방법을 알아봅니다.

:::image type="content" alt-text="Microsoft Azure Storage Explorer" source="./media/vs-azure-tools-storage-manage-with-storage-explorer/vs-storage-explorer-overview.png":::

## <a name="prerequisites"></a>필수 구성 요소

# <a name="windows"></a>[Windows](#tab/windows)

Windows의 다음 버전은 Storage Explorer를 지원합니다.

* Windows 10(권장)
* Windows 8
* Windows 7

Windows의 모든 버전에서 Storage Explorer에는 .NET Framework 4.7.2 이상이 필요합니다.

# <a name="macos"></a>[macOS](#tab/macos)

다음 버전의 macOS는 Storage Explorer를 지원합니다.

* macOS 10.12 Sierra 이상 버전

# <a name="linux"></a>[Linux](#tab/linux)

Storage Explorer는 Linux의 가장 일반적인 배포판에 대해 [Snap Store](https://snapcraft.io/storage-explorer)에서 사용할 수 있습니다. 이 설치에 대해 Snap Store를 권장합니다. Storage Explorer 스냅은 새 버전이 Snap Store에 게시될 때 모든 종속성 및 업데이트를 설치합니다.

지원되는 배포판은 [`snapd`설치 페이지](https://snapcraft.io/docs/installing-snapd)를 참조하세요.

Storage Explorer는 암호 관리자를 사용해야 합니다. 수동으로 암호 관리자에 연결해야 할 수도 있습니다. 다음 명령을 실행하여 Storage Explorer를 시스템의 암호 관리자에 연결할 수 있습니다.

```bash
snap connect storage-explorer:password-manager-service :password-manager-service
```

Storage Explorer는 *.tar.gz* 다운로드로도 사용할 수 있습니다. *.tar.gz* 를 사용하는 경우 종속성을 수동으로 설치해야 합니다. Linux의 다음 배포판은 *.tar.gz* 설치를 지원합니다.

* Ubuntu 20.04 x64
* Ubuntu 18.04 x64
* Ubuntu 16.04 x64

*.tar.gz* 설치는 다른 배포판에서 작동할 수도 있지만 나열된 배포판만 공식적으로 지원됩니다.

Linux에 Storage Explorer를 설치하는 데 도움이 되는 자세한 내용은 Azure Storage Explorer 문제 해결 가이드의 [Linux 종속성](./storage/common/storage-explorer-troubleshooting.md#linux-dependencies)을 참조하세요.

---

## <a name="download-and-install"></a>다운로드 및 설치

Storage Explorer를 다운로드하여 설치하려면 [Azure Storage Explorer](https://www.storageexplorer.com)를 참조하세요.

## <a name="connect-to-a-storage-account-or-service"></a>스토리지 계정 또는 서비스에 연결

Storage Explorer는 Azure 리소스에 연결하는 여러 방법을 제공합니다.

* [Azure에 로그인하여 구독 및 해당 리소스에 액세스](#sign-in-to-azure)
* [개별 Azure Storage 리소스에 연결](#attach-to-an-individual-resource)
* [CosmosDB 리소스에 연결](#connect-to-azure-cosmos-db)

### <a name="sign-in-to-azure"></a>Azure에 로그인

> [!NOTE]
> 로그인한 후 리소스에 완전히 액세스하려면 Storage Explorer에 관리 권한(Azure Resource Manager)과 데이터 레이어 권한이 모두 필요합니다. 즉, 스토리지 계정, 계정의 컨테이너, 컨테이너의 데이터 액세스에 대한 Azure AD(Azure Active Directory) 권한이 필요합니다. 데이터 계층에 대한 사용 권한만 있는 경우 리소스에 연결할 때 **Azure AD(Azure Active Directory)를 사용하여 로그인** 옵션을 선택하는 것이 좋습니다. Storage Explorer에 필요한 특정 사용 권한에 대한 자세한 내용은 [Azure Storage Explorer 문제 해결 가이드](./storage/common/storage-explorer-troubleshooting.md#azure-rbac-permissions-issues)를 참조하세요.

1. Storage Explorer에서 **보기** > **계정 관리** 를 선택하거나 **계정 관리** 단추를 선택합니다.

    :::image type="content" alt-text="계정 관리" source ="./media/vs-azure-tools-storage-manage-with-storage-explorer/vs-storage-explorer-manage-accounts.png":::

1. 이제 **계정 관리** 에 사용자가 로그인한 모든 Azure 계정이 표시됩니다. 다른 계정에 연결하려면 **계정 추가...** 를 선택합니다.

1. **Azure Storage에 연결** 대화 상자가 열립니다. **리소스 선택** 패널에서 **구독** 을 선택합니다.

    :::image type="content" alt-text="연결 대화 상자" source="./media/vs-azure-tools-storage-manage-with-storage-explorer/vs-storage-explorer-connect-dialog.png":::

1. **Azure 환경 선택** 패널에서 로그인할 Azure 환경을 선택합니다. 글로벌 Azure, 국가 클라우드 또는 Azure Stack 인스턴스에 로그인할 수 있습니다. 그런 후 **다음** 을 선택합니다.

    :::image type="content" alt-text="로그인 옵션" source="./media/vs-azure-tools-storage-manage-with-storage-explorer/vs-storage-explorer-connect-environment.png":::

    > [!TIP]
    > Azure Stack에 대한 자세한 내용은 [Azure Stack 구독 또는 스토리지 계정에 Storage Explorer 연결](/azure-stack/user/azure-stack-storage-connect-se)을 참조하세요.

1. Storage Explorer는 로그인할 웹 페이지를 엽니다.

1. Azure 계정으로 성공적으로 로그인하면 계정과 계정에 연결된 Azure 구독이 **계정 관리** 아래에 나타납니다. 작업하려는 Azure 구독을 선택한 후 **적용** 을 선택합니다.

    :::image type="content" alt-text="Azure 구독 선택" source="./media/vs-azure-tools-storage-manage-with-storage-explorer/vs-storage-explorer-account-panel.png":::

1. **EXPLORER** 에 선택한 Azure 구독과 연결된 스토리지 계정이 표시됩니다.

    :::image type="content" alt-text="선택한 Azure 구독" source="./media/vs-azure-tools-storage-manage-with-storage-explorer/vs-storage-explorer-subscription-node.png":::

### <a name="attach-to-an-individual-resource"></a>개별 리소스에 연결

Storage Explorer를 사용하면 다양한 인증 방법을 사용하여 Azure Data Lake Storage Gen2 컨테이너와 같은 개별 리소스에 연결할 수 있습니다. 일부 인증 방법은 특정 리소스 종류에 대해서만 지원됩니다.

| 리소스 유형    | Azure AD | 계정 이름 및 키 | 공유 액세스 서명(SAS)  | 공용(익명) |
|------------------|----------|----------------------|--------------------------------|--------------------|
| Storage 계정 | 예      | 예                  | 예(연결 문자열 또는 URL) | No                 |
| Blob 컨테이너  | 예      | 아니요                   | 예(URL)                      | Yes                |
| Gen2 컨테이너  | 예      | 아니요                   | 예(URL)                      | Yes                |
| Gen2 디렉터리 | 예      | 아니요                   | 예(URL)                      | Yes                |
| 파일 공유      | 아니요       | 아니요                   | 예(URL)                      | No                 |
| 큐           | 예      | 아니요                   | 예(URL)                      | No                 |
| 테이블           | 아니요       | 아니요                   | 예(URL)                      | No                 |
 
또한 Storage Explorer는 에뮬레이터의 구성된 포트를 사용하여 [로컬 스토리지 에뮬레이터](#local-storage-emulator)에 연결할 수 있습니다.

개별 리소스에 연결하려면 왼쪽 도구 모음에서 **연결** 단추를 선택합니다. 그런 다음, 연결하려는 리소스 종류에 대한 지침을 따릅니다.

:::image type="content" alt-text="Azure Storage 옵션에 연결" source="./media/vs-azure-tools-storage-manage-with-storage-explorer/vs-storage-explorer-connect-button.png":::

스토리지 계정에 대한 연결이 성공적으로 추가되면 **로컬 및 연결됨** > **스토리지 계정** 아래에 새 트리 노드가 나타납니다.

다른 리소스 유형의 경우 **로컬 및 연결됨** > **스토리지 계정** >  **(연결된 컨테이너)** 아래에 새 노드가 추가됩니다. 노드는 해당 형식과 일치하는 그룹 노드 아래에 표시됩니다. 예를 들어 Azure Data Lake Storage Gen2 컨테이너에 대한 새 연결은 **Blob 컨테이너** 아래에 표시됩니다.

Storage Explorer에서 연결을 추가할 수 없거나 연결을 성공적으로 추가한 후 데이터에 액세스할 수 없는 경우 [Azure Storage Explorer 문제 해결 가이드](./storage/common/storage-explorer-troubleshooting.md)를 참조하세요.

다음 섹션에서는 개별 리소스에 연결하는 데 사용할 수 있는 다양한 인증 방법에 대해 설명합니다.

#### <a name="azure-ad"></a>Azure AD

Storage Explorer는 Azure 계정을 사용하여 다음 리소스 종류에 연결할 수 있습니다.
* Blob 컨테이너
* Azure Data Lake Storage Gen2 컨테이너
* Azure Data Lake Storage Gen2 디렉터리
* 큐
 
리소스에 대한 데이터 계층 액세스 권한이 있지만 관리 계층 액세스 권한이 없는 경우 Azure AD가 기본 옵션입니다.

1. [위에 설명된 단계](#sign-in-to-azure)를 사용하여 하나 이상의 Azure 계정에 로그인합니다.
1. **Azure Storage에 연결** 대화 상자의 **리소스 선택** 패널에서 **Blob 컨테이너**, **ADLS Gen2 컨테이너** 또는 **큐** 를 선택합니다.
1. **Azure AD(Azure Active Directory)를 사용하여 로그인** 을 선택하고 **다음** 을 선택합니다.
1. Azure 계정 및 테넌트를 선택합니다. 계정 및 테넌트는 연결하려는 Storage 리소스에 액세스할 수 있어야 합니다. **다음** 을 선택합니다.
1. 연결에 사용할 표시 이름과 리소스의 URL을 입력합니다. **다음** 을 선택합니다.
1. **요약** 패널에서 연결 정보를 검토합니다. 연결 정보가 올바른 경우 **연결** 을 선택합니다.

#### <a name="account-name-and-key"></a>계정 이름 및 키

Storage Explorer는 스토리지 계정의 이름과 키를 사용하여 스토리지 계정에 연결할 수 있습니다.

[Azure Portal](https://portal.azure.com)에서 계정 키를 찾을 수 있습니다. 스토리지 계정 페이지를 열고 **설정** > **액세스 키** 를 선택합니다.

1. **Azure Storage에 연결** 대화 상자의 **리소스 선택** 패널에서 **스토리지 계정** 을 선택합니다.
1. **계정 이름 및 키** 를 선택하고 **다음** 을 선택합니다.
1. 연결에 사용할 표시 이름, 계정 이름 및 계정 키 하나를 입력합니다. 적절한 Azure 환경을 선택합니다. **다음** 을 선택합니다.
1. **요약** 패널에서 연결 정보를 검토합니다. 연결 정보가 올바른 경우 **연결** 을 선택합니다.

#### <a name="shared-access-signature-sas-connection-string"></a>SAS(공유 액세스 서명) 연결 문자열

Storage Explorer는 SAS(공유 액세스 서명)가 있는 연결 문자열을 사용하여 스토리지 계정에 연결할 수 있습니다. SAS 연결 문자열은 다음과 같습니다.

```text
SharedAccessSignature=sv=2020-04-08&ss=btqf&srt=sco&st=2021-03-02T00%3A22%3A19Z&se=2020-03-03T00%3A22%3A19Z&sp=rl&sig=fFFpX%2F5tzqmmFFaL0wRffHlhfFFLn6zJuylT6yhOo%2FY%3F;
BlobEndpoint=https://contoso.blob.core.windows.net/;
FileEndpoint=https://contoso.file.core.windows.net/;
QueueEndpoint=https://contoso.queue.core.windows.net/;
TableEndpoint=https://contoso.table.core.windows.net/;
```

1. **Azure Storage에 연결** 대화 상자의 **리소스 선택** 패널에서 **스토리지 계정** 을 선택합니다.
1. **SAS(공유 액세스 서명)** 를 선택하고 **다음** 을 선택합니다.
1. 연결에 사용할 표시 이름 및 스토리지 계정의 SAS 연결 문자열을 입력합니다. **다음** 을 선택합니다.
1. **요약** 패널에서 연결 정보를 검토합니다. 연결 정보가 올바른 경우 **연결** 을 선택합니다.

#### <a name="shared-access-signature-sas-url"></a>SAS(공유 액세스 서명) URL

Storage Explorer는 SAS URI를 사용하여 다음 리소스 종류에 연결할 수 있습니다.
* Blob 컨테이너
* Azure Data Lake Storage Gen2 컨테이너 또는 디렉터리
* 파일 공유
* 큐
* 테이블

SAS URI는 다음과 같습니다.

```text
https://contoso.blob.core.windows.net/container01?sv=2020-04-08&st=2021-03-02T00%3A30%3A33Z&se=2020-03-03T00%3A30%3A33Z&sr=c&sp=rl&sig=z9VFdWffrV6FXU51T8b8HVfipZPOpYOFLXuQw6wfkFY%3F
```

1. **Azure Storage에 연결** 대화 상자의 **리소스 선택** 패널에서 연결하려는 리소스를 선택합니다.
1. **SAS(공유 액세스 서명)** 를 선택하고 **다음** 을 선택합니다.
1. 연결에 사용할 표시 이름과 리소스의 SAS URI를 입력합니다. **다음** 을 선택합니다.
1. **요약** 패널에서 연결 정보를 검토합니다. 연결 정보가 올바른 경우 **연결** 을 선택합니다.

#### <a name="local-storage-emulator"></a>로컬 스토리지 에뮬레이터

Storage Explorer는 Azure Storage 에뮬레이터에 연결할 수 있습니다. 현재 지원되는 에뮬레이터는 두 개가 있습니다.

* [Azure Storage Emulator](storage/common/storage-use-emulator.md)(Windows만)
* [Azurite](https://github.com/azure/azurite)(Windows, macOS 또는 Linux)

에뮬레이터가 기본 포트에서 수신 대기하는 경우 **로컬 및 연결됨** > **스토리지 계정** > **에뮬레이터 - 기본 포트** 노드를 사용하여 에뮬레이터에 액세스할 수 있습니다.

연결에 다른 이름을 사용하려는 경우 또는 에뮬레이터가 기본 포트에서 실행되고 있지 않은 경우:

1. 에뮬레이터를 시작합니다.

   > [!IMPORTANT]
   > Storage Explorer는 자동으로 에뮬레이터를 시작하지 않습니다. 사용자가 직접 시작해야 합니다.

1. **Azure Storage에 연결** 대화 상자의 **리소스 선택** 패널에서 **로컬 스토리지 에뮬레이터** 를 선택합니다.
1. 연결에 사용할 표시 이름과 사용하려는 각 에뮬레이트된 서비스의 포트 번호를 입력합니다. 서비스에 사용하지 않으려면 해당 포트를 비워 둡니다. **다음** 을 선택합니다.
1. **요약** 패널에서 연결 정보를 검토합니다. 연결 정보가 올바른 경우 **연결** 을 선택합니다.

### <a name="connect-to-azure-cosmos-db"></a>Azure Cosmos DB에 연결

Storage Explorer는 Azure Cosmos DB 리소스에 대한 연결도 지원합니다.

#### <a name="connect-to-an-azure-cosmos-db-account-by-using-a-connection-string"></a>연결 문자열을 사용하여 Azure Cosmos DB 계정에 연결

Azure 구독을 통해 Azure Cosmos DB 계정을 관리하는 대신 연결 문자열을 사용하여 Azure Cosmos DB에 연결할 수 있습니다. 연결하려면 다음 단계를 따릅니다.

1. **EXPLORER** 에서 **로컬 및 연결됨** 을 확장하고 **Cosmos DB 계정** 을 마우스 오른쪽 단추로 클릭한 다음, **Azure Cosmos DB에 연결** 을 선택합니다.

    ![연결 문자열을 사용하여 Azure Cosmos DB에 연결][21]

1. Azure Cosmos DB API를 선택하고 **연결 문자열** 데이터를 입력한 다음, **확인** 을 선택하여 Azure Cosmos DB 계정을 연결합니다. 연결 문자열을 검색하는 방법에 대한 자세한 내용은 [Azure Cosmos 계정 관리](./cosmos-db/how-to-manage-database-account.md)를 참조하세요.

    ![연결 문자열][22]

#### <a name="connect-to-azure-data-lake-store-by-uri"></a>URI로 Azure Data Lake Store에 연결

구독에 없는 리소스에 액세스할 수 있습니다. 리소스 URI를 제공하려면 해당 리소스에 대한 액세스 권한이 있어야 합니다. 로그인한 후, URI를 사용하여 Data Lake Store에 연결합니다. 연결하려면 다음 단계를 따릅니다.

1. **EXPLORER** 에서 **로컬 및 연결됨** 을 확장합니다.

1. **Data Lake Storage Gen1** 을 마우스 오른쪽 단추로 클릭하고 **Data Lake Storage Gen1에 연결** 을 선택합니다.

    ![Data Lake Store 바로 가기 메뉴에 연결](./media/vs-azure-tools-storage-manage-with-storage-explorer/storage-explorer-connect-data-lake-storage.png)

1. URI를 입력한 다음, **확인** 을 선택합니다. 사용자의 Data Lake Store가 **Data Lake Storage** 아래에 나타납니다.

    ![Data Lake Store에 연결 결과](./media/vs-azure-tools-storage-manage-with-storage-explorer/storage-explorer-attach-data-lake-finished.png)

이 예제에서는 Data Lake Storage Gen1을 사용합니다. 이제 Azure Data Lake Storage Gen2를 사용할 수 있습니다. 자세한 내용은 [Azure Data Lake Storage Gen1이란?](./data-lake-store/data-lake-store-overview.md)을 참조하세요.

## <a name="generate-a-shared-access-signature-in-storage-explorer"></a>Storage Explorer에서 공유 액세스 서명 생성<a name="generate-a-sas-in-storage-explorer"></a>

### <a name="account-level-shared-access-signature"></a>계정 수준 공유 액세스 서명

1. 공유하려는 스토리지 계정을 마우스 오른쪽 단추로 클릭한 다음, **공유 액세스 서명 가져오기** 를 선택합니다.

    ![공유 액세스 서명 가져오기 바로 가기 메뉴 옵션][14]

1. **공유 액세스 서명** 에서 계정에 대해 원하는 시간 프레임 및 권한을 지정한 다음, **만들기** 를 선택합니다.

    ![공유 액세스 서명 가져오기][15]

1. **연결 문자열** 또는 원시 **쿼리 문자열** 을 클립보드에 복사합니다.

### <a name="service-level-shared-access-signature"></a>서비스 수준 공유 액세스 서명

서비스 수준에서 공유 액세스 서명을 가져올 수 있습니다. 자세한 내용은 [blob 컨테이너에 대한 SAS 가져오기](vs-azure-tools-storage-explorer-blobs.md#get-the-sas-for-a-blob-container)를 참조하세요.

## <a name="search-for-storage-accounts"></a>스토리지 계정 검색

스토리지 리소스를 찾으려면 **EXPLORER** 창에서 검색할 수 있습니다.

검색 상자에 텍스트를 입력하면 Storage Explorer에 해당 시점까지 사용자가 입력한 검색 값과 일치하는 모든 리소스가 표시됩니다. 이 예제에서는 **엔드포인트** 에 대한 검색이 표시됩니다.

![Storage 계정 검색][23]

> [!NOTE]
> 검색 속도를 높이려면 **계정 관리** 를 사용하여 검색 중인 항목이 포함되지 않은 구독을 선택 취소합니다. 노드를 마우스 오른쪽 단추로 클릭하고 **여기에서 검색** 을 선택하여 특정 노드에서 검색을 시작할 수도 있습니다.

## <a name="next-steps"></a>다음 단계

* [Storage Explorer를 사용하여 Azure Blob Storage 리소스 관리](vs-azure-tools-storage-explorer-blobs.md)
* [Azure Storage Explorer 를 사용하여 데이터 작업](./cosmos-db/storage-explorer.md)
* [Storage Explorer를 사용하여 Azure Data Lake Store 리소스 관리](./data-lake-store/data-lake-store-in-storage-explorer.md)

[14]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/get-shared-access-signature-for-storage-explorer.png
[15]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/create-shared-access-signature-for-storage-explorer.png
[21]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/connect-to-cosmos-db-by-connection-string.png
[22]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/connection-string-for-cosmos-db.png
[23]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/storage-explorer-search-for-resource.png
