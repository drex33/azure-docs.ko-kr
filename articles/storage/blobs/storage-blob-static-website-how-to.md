---
title: Azure Storage에서 정적 웹 사이트 호스트
description: Azure Storage GPv2 계정의 컨테이너에서 직접 정적 콘텐츠(HTML, CSS, JavaScript 및 이미지 파일)를 제공하는 방법에 대해 알아봅니다.
author: normesta
ms.service: storage
ms.subservice: blobs
ms.topic: conceptual
ms.author: normesta
ms.date: 03/04/2020
ms.custom: devx-track-js, devx-track-azurepowershell
ms.openlocfilehash: 46c22fe3033a9fc9cda90bf9901f10f20355838e
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128645895"
---
# <a name="host-a-static-website-in-azure-storage"></a>Azure Storage에서 정적 웹 사이트 호스트

[범용 V2](../common/storage-account-create.md) 또는 [BlockBlobStorage](../common/storage-account-create.md) 계정의 컨테이너에서 직접 고정적인 콘텐츠(HTML, CSS, JavaScript, 이미지 파일)를 제공할 수 있습니다. 자세한 내용은 [Azure Storage에서 정적 웹 사이트 호스팅](storage-blob-static-website.md)을 참조하세요.

이 문서에서는 Azure Portal, Azure CLI 또는 PowerShell을 사용하여 정적 웹 사이트 호스팅을 사용하도록 설정하는 방법을 보여 줍니다.

## <a name="enable-static-website-hosting"></a>정적 웹 사이트 호스팅 사용

정적 웹 사이트 호스팅은 스토리지 계정에서 사용하도록 설정해야 하는 기능입니다.

### <a name="portal"></a>[포털](#tab/azure-portal)

1. [Azure Portal](https://portal.azure.com/)에 로그인하여 시작합니다.

2. 스토리지 계정을 찾아 계정 개요를 표시합니다.

3. **정적 웹 사이트** 를 선택하여 정적 웹 사이트에 대한 구성 페이지를 표시합니다.

4. **사용** 을 선택하여 스토리지 계정에서 정적 웹 사이트를 호스팅할 수 있습니다.

5. **인덱스 문서 이름** 필드에서 기본 인덱스 페이지(예: *index.html*)를 지정합니다.

   기본 인덱스 페이지는 사용자가 정적 웹 사이트의 루트로 이동할 때 표시됩니다.

6. **오류 문서 경로** 필드에서 기본 오류 페이지(예: *404.html*)를 지정합니다.

   기본 오류 페이지는 사용자가 정적 웹 사이트에서 존재하지 않는 페이지로 이동하려고 할 때 표시됩니다.

7. **저장** 을 클릭합니다. 이제 Azure Portal에는 정적 웹 사이트 엔드포인트가 표시됩니다.

    ![스토리지 계정에서 정적 웹 사이트 호스팅 설정](media/storage-blob-static-website-host/enable-static-website-hosting.png)

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

<a id="cli"></a>

CLI([Azure 명령줄 인터페이스)](/cli/azure/)를 사용하여 정적 웹 사이트 호스팅을 사용하도록 설정할 수 있습니다.

1. 먼저 [Azure Cloud Shell](../../cloud-shell/overview.md)을 열거나 Azure CLI를 로컬로 [설치](/cli/azure/install-azure-cli)한 경우 Windows PowerShell과 같은 명령 콘솔 애플리케이션을 엽니다.

2. ID가 둘 이상의 구독과 연결된 경우 정적 웹 사이트를 호스트하는 스토리지 계정의 구독으로 활성 구독을 설정합니다.

   ```azurecli-interactive
   az account set --subscription <subscription-id>
   ```

   `<subscription-id>` 자리 표시자 값을 구독의 ID로 바꿉니다.

3. 정적 웹 사이트 호스팅을 사용하도록 설정합니다.

   ```azurecli-interactive
   az storage blob service-properties update --account-name <storage-account-name> --static-website --404-document <error-document-name> --index-document <index-document-name>
   ```

   - `<storage-account-name>` 자리 표시자 값을 스토리지 계정 이름으로 바꿉니다.

   - 브라우저에서 존재하지 않는 페이지를 요청하는 경우 사용자에게 표시되는 오류 문서 이름으로 `<error-document-name>` 자리 표시자를 바꿉니다.

   - `<index-document-name>` 자리 표시자를 인덱스 문서의 이름으로 바꿉니다. 이 문서는 일반적으로 "index.html"입니다.

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

<a id="powershell"></a>

Azure PowerShell 모듈을 사용하여 정적 웹 사이트 호스팅을 사용하도록 설정할 수 있습니다.

1. Windows PowerShell 명령 창을 엽니다.

2. Azure PowerShell 모듈 Az 버전 0.7 이상이 있는지 확인합니다.

   ```powershell
   Get-InstalledModule -Name Az -AllVersions | select Name,Version
   ```

   설치 또는 업그레이드해야 하는 경우 [Azure PowerShell 모듈 설치](/powershell/azure/install-Az-ps)를 참조하세요.

3. `Connect-AzAccount` 명령을 사용하여 Azure 구독에 로그인하고 화면의 지시를 따릅니다.

   ```powershell
   Connect-AzAccount
   ```

4. ID가 둘 이상의 구독과 연결된 경우 정적 웹 사이트를 호스트하는 스토리지 계정의 구독으로 활성 구독을 설정합니다.

   ```powershell
   $context = Get-AzSubscription -SubscriptionId <subscription-id>
   Set-AzContext $context
   ```

   `<subscription-id>` 자리 표시자 값을 구독의 ID로 바꿉니다.

5. 사용하려는 스토리지 계정을 정의하는 스토리지 계정 컨텍스트를 가져옵니다.

   ```powershell
   $storageAccount = Get-AzStorageAccount -ResourceGroupName "<resource-group-name>" -AccountName "<storage-account-name>"
   $ctx = $storageAccount.Context
   ```

   - `<resource-group-name>` 자리 표시자 값을 리소스 그룹의 이름으로 바꿉니다.

   - `<storage-account-name>` 자리 표시자 값을 스토리지 계정 이름으로 바꿉니다.

6. 정적 웹 사이트 호스팅을 사용하도록 설정합니다.

   ```powershell
   Enable-AzStorageStaticWebsite -Context $ctx -IndexDocument <index-document-name> -ErrorDocument404Path <error-document-name>
   ```

   - 브라우저에서 존재하지 않는 페이지를 요청하는 경우 사용자에게 표시되는 오류 문서 이름으로 `<error-document-name>` 자리 표시자를 바꿉니다.

   - `<index-document-name>` 자리 표시자를 인덱스 문서의 이름으로 바꿉니다. 이 문서는 일반적으로 "index.html"입니다.

---

## <a name="upload-files"></a>파일 업로드

### <a name="portal"></a>[포털](#tab/azure-portal)

이 지침에서는 Azure Portal에 표시되는 Storage Explorer 버전을 사용하여 파일을 업로드하는 방법을 보여 줍니다. 그러나 Azure Portal 외부에서 실행되는 [Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) 버전을 사용할 수도 있습니다. [AzCopy](../common/storage-use-azcopy-v10.md), PowerShell, CLI 또는 계정의 **$web** 컨테이너에 파일을 업로드할 수 있는 사용자 지정 애플리케이션을 사용할 수 있습니다. Visual Studio 코드를 사용하여 파일을 업로드하는 단계별 자습서는 [자습서: Blob Storage에서 정적 웹 사이트 호스트](./storage-blob-static-website-host.md)를 완료하세요.

1. **Storage Explorer(미리 보기)** 를 선택합니다.

2. **BLOB 컨테이너** 노드를 확장한 다음, **$web** 컨테이너를 선택합니다.

3. **업로드** 단추를 선택하여 파일을 업로드합니다.

   ![파일 업로드](media/storage-blob-static-website/storage-blob-static-website-upload.png)

4. 브라우저에서 파일의 콘텐츠를 표시하려면 해당 파일의 콘텐츠 형식이 `text/html`로 설정되어 있는지 확인합니다.

   ![콘텐츠 형식 확인](media/storage-blob-static-website/storage-blob-static-website-content-type.png)

   > [!NOTE]
   > Storage Explorer는 `.html`과 같은 일반적으로 인식되는 확장에 대해 이 속성을 자동으로 `text/html`로 설정합니다. 그러나 이를 직접 설정해야 하는 경우도 있습니다. 이 속성을 `text/html`로 설정하지 않으면 브라우저에서 사용자에게 콘텐츠를 렌더링하지 않고 파일을 다운로드하라는 메시지를 표시합니다. 이 속성을 설정하려면 파일을 마우스 오른쪽 단추로 클릭한 다음, **속성** 을 클릭합니다.

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

소스 디렉터리의 *$web* 컨테이너에 개체를 업로드합니다.

이 예제에서는 Azure Cloud Shell 세션에서 명령을 실행 중인 것으로 가정합니다.

```azurecli-interactive
az storage blob upload-batch -s <source-path> -d '$web' --account-name <storage-account-name>
```

> [!NOTE]
> 브라우저에서 사용자에게 콘텐츠를 렌더링하지 않고 파일을 다운로드하라는 메시지를 표시하는 경우 명령에 `--content-type 'text/html; charset=utf-8'`을 추가할 수 있습니다.

- `<storage-account-name>` 자리 표시자 값을 스토리지 계정 이름으로 바꿉니다.

- `<source-path>` 자리 표시자를 업로드할 파일의 위치에 대한 경로로 바꿉니다.

> [!NOTE]
> Azure CLI의 위치 설치를 사용하는 경우 로컬 컴퓨터의 모든 위치에 대한 경로를 사용할 수 있습니다(예: `C:\myFolder`).
>
> Azure Cloud Shell을 사용하는 경우 Cloud Shell에 표시되는 파일 공유를 참조해야 합니다. 이 위치는 클라우드 공유 자체의 파일 공유 또는 Cloud Shell에서 탑재한 기존 파일 공유일 수 있습니다. 이 작업을 수행하는 방법에 대한 자세한 내용은 [Azure Cloud Shell에서 파일 유지](../../cloud-shell/persisting-shell-storage.md)를 참조하세요.

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

소스 디렉터리의 *$web* 컨테이너에 개체를 업로드합니다.

```powershell
# upload a file
set-AzStorageblobcontent -File "<path-to-file>" `
-Container `$web `
-Blob "<blob-name>" `
-Context $ctx
```

> [!NOTE]
> 브라우저에서 사용자에게 콘텐츠를 렌더링하지 않고 파일을 다운로드하라는 메시지를 표시하는 경우 명령에 `-Properties @{ ContentType = "text/html; charset=utf-8";}`을 추가할 수 있습니다.

- `<path-to-file>` 자리 표시자 값을 업로드하려는 파일의 정규화된 경로로 바꿉니다(예: `C:\temp\index.html`).

- `<blob-name>` 자리 표시자 값을 결과 Blob에 지정할 이름으로 바꿉니다(예: `index.html`).

---

<a id="portal-find-url"></a>

## <a name="find-the-website-url"></a>웹 사이트 URL 찾기

웹 사이트의 공용 URL을 사용하여 브라우저에서 사이트의 페이지를 볼 수 있습니다.

### <a name="portal"></a>[포털](#tab/azure-portal)

스토리지 계정의 계정 개요 페이지 옆에 표시되는 창에서 **정적 웹 사이트** 를 선택합니다. **기본 엔드포인트** 필드에 사이트의 URL이 표시됩니다.

![Azure Storage 정적 웹 사이트 메트릭](./media/storage-blob-static-website/storage-blob-static-website-url.png)

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

다음 명령을 사용하여 정적 웹 사이트의 공용 URL을 찾습니다.

```azurecli-interactive
az storage account show -n <storage-account-name> -g <resource-group-name> --query "primaryEndpoints.web" --output tsv
```

- `<storage-account-name>` 자리 표시자 값을 스토리지 계정 이름으로 바꿉니다.

- `<resource-group-name>` 자리 표시자 값을 리소스 그룹의 이름으로 바꿉니다.

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

다음 명령을 사용하여 정적 웹 사이트의 공용 URL을 찾습니다.

```powershell
 $storageAccount = Get-AzStorageAccount -ResourceGroupName "<resource-group-name>" -Name "<storage-account-name>"
Write-Output $storageAccount.PrimaryEndpoints.Web
```

- `<resource-group-name>` 자리 표시자 값을 리소스 그룹의 이름으로 바꿉니다.

- `<storage-account-name>` 자리 표시자 값을 스토리지 계정 이름으로 바꿉니다.

---

<a id="metrics"></a>

## <a name="enable-metrics-on-static-website-pages"></a>정적 웹 사이트 페이지에서 메트릭 사용

메트릭을 사용하도록 설정하면 **$web** 컨테이너의 파일에 대한 트래픽 통계가 메트릭 대시보드에 보고됩니다.

1. 스토리지 계정 메뉴의 **모니터** 섹션에서 **메트릭** 을 클릭합니다.

   > [!div class="mx-imgBorder"]
   > ![메트릭 링크](./media/storage-blob-static-website/metrics-link.png)

   > [!NOTE]
   > 여러 메트릭 API에 연결하여 메트릭 데이터가 생성됩니다. 데이터를 반환하는 멤버에만 집중하기 위해 특정 시간 프레임에 사용된 API 멤버만 포털에 표시됩니다. 필요한 API 멤버를 선택할 수 있도록 가장 먼저 할 일은 시간 프레임을 확장하는 것입니다.

2. 시간 프레임 단추를 클릭하고, 시간 프레임을 선택한 다음, **적용** 을 클릭합니다.

   ![Azure Storage 정적 웹 사이트 메트릭 시간 범위](./media/storage-blob-static-website/storage-blob-static-website-metrics-time-range.png)

3. *네임스페이스* 드롭다운에서 **Blob** 을 선택합니다.

   ![Azure Storage 정적 웹 사이트 메트릭 네임스페이스](./media/storage-blob-static-website/storage-blob-static-website-metrics-namespace.png)

4. 그런 다음, **송신** 메트릭을 선택합니다.

   ![Azure Storage 고정적인 웹사이트 송신 메트릭을 보여 주는 스크린샷.](./media/storage-blob-static-website/storage-blob-static-website-metrics-metric.png)

5. *집계* 선택기에서 **합계** 를 선택합니다.

   ![Azure Storage 정적 웹 사이트 메트릭 집계](./media/storage-blob-static-website/storage-blob-static-website-metrics-aggregation.png)

6. **필터 추가** 단추를 클릭하고 *속성* 선택기에서 **API 이름** 을 선택합니다.

   ![Azure Storage 정적 웹 사이트 메트릭 API 이름](./media/storage-blob-static-website/storage-blob-static-website-metrics-api-name.png)

7. *값* 선택기에서 **GetWebContent** 옆에 있는 상자를 선택하여 메트릭 보고서를 채웁니다.

   ![Azure Storage 정적 웹 사이트 GetWebContent](./media/storage-blob-static-website/storage-blob-static-website-metrics-getwebcontent.png)

   > [!NOTE]
   > **GetWebContent** 확인란은 해당 API 멤버가 지정된 시간 프레임 내에 사용된 경우에만 표시됩니다. 데이터를 반환하는 멤버에만 집중하기 위해 특정 시간 프레임에 사용된 API 멤버만 포털에 표시됩니다. 이 목록에서 특정 API 멤버를 찾을 수 없는 경우 시간 프레임을 확장합니다.

## <a name="next-steps"></a>다음 단계

- 정적 웹 사이트에서 사용자 지정 도메인을 구성하는 방법을 알아봅니다. [Azure Blob Storage 엔드포인트에 사용자 지정 도메인 매핑](storage-custom-domain-name.md)을 참조하세요.
