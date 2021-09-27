---
title: Azure Blob Storage 엔드포인트에 사용자 지정 도메인 매핑
titleSuffix: Azure Storage
description: Azure 스토리지 계정의 Blob Storage 또는 웹 엔드포인트에 사용자 지정 도메인을 매핑합니다.
author: normesta
ms.service: storage
ms.topic: how-to
ms.date: 02/12/2021
ms.author: normesta
ms.reviewer: dineshm
ms.subservice: blobs
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: e56a36947930894548a4490320c48efab0509ef1
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128662568"
---
# <a name="map-a-custom-domain-to-an-azure-blob-storage-endpoint"></a>Azure Blob Storage 엔드포인트에 사용자 지정 도메인 매핑

사용자 지정 도메인을 Blob 서비스 엔드포인트 또는 [정적 웹 사이트](storage-blob-static-website.md) 엔드포인트에 매핑할 수 있습니다.

> [!NOTE]
> 이 매핑은 하위 도메인(예:`www.contoso.com`)에만 적용됩니다. 웹 엔드포인트를 루트 도메인(예:`contoso.com`)에서 사용할 수 있도록 하려면 Azure CDN을 사용해야 합니다. 지침을 얻기 위해 이 문서의 [HTTPS를 사용하여 사용자 지정 도메인 매핑](#enable-https) 섹션을 참조하세요. 이 문서의 해당 섹션에서 사용자 지정 도메인의 루트 도메인을 사용하도록 설정하기 때문에 HTTPS를 사용하도록 설정하는 단계는 선택 사항입니다.

<a id="enable-http"></a>

## <a name="map-a-custom-domain-with-only-http-enabled"></a>HTTP만 사용하는 사용자 지정 도메인 매핑

이 방법은 더 쉽지만 HTTP 액세스만 가능합니다. 스토리지 계정이 HTTPS를 통한 [보안 전송을 요구](../common/storage-require-secure-transfer.md)하도록 구성된 경우 사용자 지정 도메인에 대한 HTTPS 액세스를 사용하도록 설정해야 합니다.

HTTPS 액세스를 사용하도록 설정하려면 이 문서의 [HTTPS를 사용하도록 설정된 사용자 지정 도메인 매핑](#enable-https) 섹션을 참조하세요.

<a id="map-a-domain"></a>

### <a name="map-a-custom-domain"></a>사용자 지정 도메인 매핑

> [!IMPORTANT]
> 구성을 완료하는 동안 사용자는 사용자 지정 도메인을 일시적으로 사용할 수 없습니다. 현재 도메인에서 가동 중지 시간이 0이어야 하는 SLA(서비스 수준 규약)를 사용하는 애플리케이션을 지원하는 경우 이 문서의 [가동 중지 시간이 없는 사용자 지정 도메인 매핑](#zero-down-time) 섹션의 단계에 따라 DNS 매핑이 수행되는 동안 사용자가 도메인에 액세스할 수 있는지 확인합니다.

사용자가 도메인을 일시적으로 사용할 수 없는 경우가 문제가 되지 않는다면 다음 단계를 수행합니다.

:heavy_check_mark: 1단계: 스토리지 엔드포인트의 호스트 이름을 가져옵니다.

:heavy_check_mark: 2단계: 도메인 공급자를 사용하여 CNAME(정식 이름) 레코드를 만듭니다.

:heavy_check_mark: 3단계: Azure에 사용자 지정 도메인을 등록합니다.

:heavy_check_mark: 4단계: 사용자 지정 도메인을 테스트합니다.

<a id="endpoint"></a>

#### <a name="step-1-get-the-host-name-of-your-storage-endpoint"></a>1단계: 스토리지 엔드포인트의 호스트 이름 가져오기

호스트 이름은 프로토콜 식별자 및 후행 슬래시가 없는 스토리지 엔드포인트 URL입니다.

1. [Azure Portal](https://portal.azure.com)에서 스토리지 계정으로 이동합니다.

2. 메뉴 창의 **설정** 에서 **엔드포인트** 를 선택합니다.

3. **Blob 서비스** 엔드포인트 또는 **정적 웹 사이트** 엔드포인트의 값을 텍스트 파일에 복사합니다.

   > [!NOTE]
   > 데이터 레이크 스토리지 엔드포인트는 지원되지 않습니다(예: `https://mystorageaccount.dfs.core.windows.net/`).

4. 해당 문자열에서 프로토콜 식별자(예: `HTTPS`)와 후행 슬래시를 제거합니다. 예시는 다음 표를 참조하세요.

   | 엔드포인트 형식 |  엔드포인트(endpoint) | 호스트 이름 |
   |------------|-----------------|-------------------|
   |Blob 서비스  | `https://mystorageaccount.blob.core.windows.net/` | `mystorageaccount.blob.core.windows.net` |
   |정적 웹 사이트  | `https://mystorageaccount.z5.web.core.windows.net/` | `mystorageaccount.z5.web.core.windows.net` |

   나중에 이 값을 따로 설정합니다.

<a id="create-cname-record"></a>

#### <a name="step-2-create-a-canonical-name-cname-record-with-your-domain-provider"></a>2단계: 도메인 공급자를 사용하여 CNAME(정식 이름) 레코드 만들기

호스트 이름을 가리키는 CNAME 레코드를 만듭니다. CNAME 레코드는 원본 도메인 이름을 대상 도메인 이름에 매핑하는 DNS(Domain Name System) 레코드의 형식입니다.

1. 도메인 등록자의 웹 사이트에 로그인한 다음, DNS 설정 관리 페이지로 이동합니다.

   **도메인 이름**, **DNS** 또는 **이름 서버 관리** 라는 섹션에서 해당 페이지를 찾을 수 있습니다.

2. CNAME 레코드를 관리하는 섹션을 찾습니다.

   고급 설정 페이지로 이동하여 **CNAME**, **별칭** 또는 **하위 도메인** 을 찾아야 할 수 있습니다.

3. CNAME 레코드를 만듭니다. 해당 레코드의 일부로 다음 항목을 제공합니다.

   - `www` 또는 `photos`와 같은 하위 도메인 별칭입니다. 하위 도메인은 필수이며 루트 도메인은 지원되지 않습니다.

   - 이 문서 앞부분의 [스토리지 엔드포인트의 호스트 이름 가져오기](#endpoint) 섹션에서 가져온 호스트 이름입니다.

<a id="register"></a>

#### <a name="step-3-register-your-custom-domain-with-azure"></a>3단계: Azure에 사용자 지정 도메인 등록

##### <a name="portal"></a>[포털](#tab/azure-portal)

1. [Azure Portal](https://portal.azure.com)에서 스토리지 계정으로 이동합니다.

2. 메뉴 창의 **보안 + 네트워킹** 에서 **네트워킹** 을 선택합니다.

3. **네트워킹** 페이지에서 **사용자 지정 도메인** 탭을 선택합니다.

   > [!NOTE]
   > 이 옵션은 계층 구조 네임스페이스 기능을 사용하도록 설정된 계정에는 표시되지 않습니다. 이러한 계정에 대해 PowerShell 또는 Azure CLI를 사용하여 이 단계를 완료합니다.

3. **도메인 이름** 텍스트 상자에 하위 도메인을 포함하여 사용자 지정 도메인 이름을 입력합니다.

   예를 들어 도메인이 *contoso.com* 이고 하위 도메인 별칭이 *www* 이면 `www.contoso.com`을 입력합니다. 하위 도메인이 *사진* 이면 `photos.contoso.com`을 입력합니다.

4. 사용자 지정 도메인을 등록하려면 **저장** 단추를 선택합니다.

   새 CNAME 레코드가 DNS(Domain Name Server)를 통해 전파된 후에는 적절한 사용 권한이 있는 사용자가 사용자 지정 도메인을 사용하여 Blob 데이터를 확인할 수 있습니다.

##### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

다음 PowerShell 명령을 실행 합니다.

```powershell
Set-AzStorageAccount -ResourceGroupName <resource-group-name> -Name <storage-account-name> -CustomDomainName <custom-domain-name> -UseSubDomain $false
```

- `<resource-group-name>` 자리 표시자를 리소스 그룹의 이름으로 바꿉니다.

- `<storage-account-name>` 자리 표시자를 스토리지 계정 이름으로 바꿉니다.

- `<custom-domain-name>` 자리 표시자를 하위 도메인을 포함하는 사용자 지정 도메인의 이름으로 바꿉니다.

  예를 들어 도메인이 *contoso.com* 이고 하위 도메인 별칭이 *www* 이면 `www.contoso.com`을 입력합니다. 하위 도메인이 *사진* 이면 `photos.contoso.com`을 입력합니다.

새 CNAME 레코드가 DNS(Domain Name Server)를 통해 전파된 후에는 적절한 사용 권한이 있는 사용자가 사용자 지정 도메인을 사용하여 Blob 데이터를 확인할 수 있습니다.

##### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

다음 PowerShell 명령을 실행 합니다.

```azurecli
az storage account update \
   --resource-group <resource-group-name> \ 
   --name <storage-account-name> \
   --custom-domain <custom-domain-name> \
   --use-subdomain false
  ```

- `<resource-group-name>` 자리 표시자를 리소스 그룹의 이름으로 바꿉니다.

- `<storage-account-name>` 자리 표시자를 스토리지 계정 이름으로 바꿉니다.

- `<custom-domain-name>` 자리 표시자를 하위 도메인을 포함하는 사용자 지정 도메인의 이름으로 바꿉니다.

  예를 들어 도메인이 *contoso.com* 이고 하위 도메인 별칭이 *www* 이면 `www.contoso.com`을 입력합니다. 하위 도메인이 *사진* 이면 `photos.contoso.com`을 입력합니다.

새 CNAME 레코드가 DNS(Domain Name Server)를 통해 전파된 후에는 적절한 사용 권한이 있는 사용자가 사용자 지정 도메인을 사용하여 Blob 데이터를 확인할 수 있습니다.

---

#### <a name="step-4-test-your-custom-domain"></a>4단계: 사용자 지정 도메인 테스트

사용자 지정 도메인이 Blob 서비스 엔드포인트에 매핑되었는지 확인하려면 스토리지 계정 내의 공용 컨테이너에서 Blob을 만듭니다. 그런 다음, 웹 브라우저에서 `http://<subdomain.customdomain>/<mycontainer>/<myblob>` 형식의 URI를 사용하여 Blob에 액세스합니다.

예를 들어 *photos.contoso.com* 사용자 지정 하위 도메인의 `myforms` 컨테이너에서 웹 형식에 액세스하려면 다음 URI를 사용할 수 있습니다. `http://photos.contoso.com/myforms/applicationform.htm`

<a id="zero-down-time"></a>

### <a name="map-a-custom-domain-with-zero-downtime"></a>가동 중지 시간이 0인 사용자 지정 도메인 매핑

> [!NOTE]
> 사용자가 도메인을 일시적으로 사용할 수 없는 경우가 문제가 되지 않는다면, 이 문서의 [사용자 지정 도메인 매핑](#map-a-domain) 섹션에 나오는 단계를 사용하는 것이 좋습니다. 이는 더 간단한 단계로 이루어진 쉬운 방법입니다.

현재 도메인에서 가동 중지 시간이 0이어야 하는 SLA(서비스 수준 규약)를 사용하는 애플리케이션을 지원하는 경우, DNS 매핑이 수행되는 동안 사용자가 도메인에 액세스할 수 있는지 확인하려면 다음 단계를 수행합니다.

:heavy_check_mark: 1단계: 스토리지 엔드포인트의 호스트 이름을 가져옵니다.

:heavy_check_mark: 2단계: 도메인 공급자를 사용하여 중간 CNAME(정식 이름) 레코드를 만듭니다.

:heavy_check_mark: 3단계: Azure를 사용하여 사용자 지정 도메인을 미리 등록합니다.

:heavy_check_mark: 4단계: 도메인 공급자를 사용하여 CNAME 레코드를 만듭니다.

:heavy_check_mark: 5단계: 사용자 지정 도메인을 테스트합니다.

<a id="endpoint-2"></a>

#### <a name="step-1-get-the-host-name-of-your-storage-endpoint"></a>1단계: 스토리지 엔드포인트의 호스트 이름 가져오기

호스트 이름은 프로토콜 식별자 및 후행 슬래시가 없는 스토리지 엔드포인트 URL입니다.

1. [Azure Portal](https://portal.azure.com)에서 스토리지 계정으로 이동합니다.

2. 메뉴 창의 **설정** 에서 **엔드포인트** 를 선택합니다.

3. **Blob 서비스** 엔드포인트 또는 **정적 웹 사이트** 엔드포인트의 값을 텍스트 파일에 복사합니다.

   > [!NOTE]
   > 데이터 레이크 스토리지 엔드포인트는 지원되지 않습니다(예: `https://mystorageaccount.dfs.core.windows.net/`).

4. 해당 문자열에서 프로토콜 식별자(예: `HTTPS`)와 후행 슬래시를 제거합니다. 예시는 다음 표를 참조하세요.

   | 엔드포인트 형식 |  엔드포인트(endpoint) | 호스트 이름 |
   |------------|-----------------|-------------------|
   |Blob 서비스  | `https://mystorageaccount.blob.core.windows.net/` | `mystorageaccount.blob.core.windows.net` |
   |정적 웹 사이트  | `https://mystorageaccount.z5.web.core.windows.net/` | `mystorageaccount.z5.web.core.windows.net` |

   나중에 이 값을 따로 설정합니다.

#### <a name="step-2-create-an-intermediary-canonical-name-cname-record-with-your-domain-provider"></a>2단계: 도메인 공급자를 사용하여 중간 CNAME(정식 이름) 레코드 만들기

호스트 이름을 가리키는 임시 CNAME 레코드를 만듭니다. CNAME 레코드는 원본 도메인을 대상 도메인 이름에 매핑하는 DNS 레코드의 형식입니다.

1. 도메인 등록자의 웹 사이트에 로그인한 다음, DNS 설정 관리 페이지로 이동합니다.

   **도메인 이름**, **DNS** 또는 **이름 서버 관리** 라는 섹션에서 해당 페이지를 찾을 수 있습니다.

2. CNAME 레코드를 관리하는 섹션을 찾습니다.

   고급 설정 페이지로 이동하여 **CNAME**, **별칭** 또는 **하위 도메인** 을 찾아야 할 수 있습니다.

3. CNAME 레코드를 만듭니다. 해당 레코드의 일부로 다음 항목을 제공합니다.

   - `www` 또는 `photos`와 같은 하위 도메인 별칭입니다. 하위 도메인은 필수이며 루트 도메인은 지원되지 않습니다.

     `asverify` 하위 도메인을 별칭에 추가합니다. 예를 들어 `asverify.www` 또는 `asverify.photos`입니다.

   - 이 문서 앞부분의 [스토리지 엔드포인트의 호스트 이름 가져오기](#endpoint) 섹션에서 가져온 호스트 이름입니다.

     `asverify` 하위 도메인을 호스트 이름에 추가합니다. 예: `asverify.mystorageaccount.blob.core.windows.net`

#### <a name="step-3-pre-register-your-custom-domain-with-azure"></a>3단계: Azure를 사용하여 사용자 지정 도메인 미리 등록

Azure에 사용자 지정 도메인을 미리 등록하는 경우 도메인에 대한 DNS 레코드를 수정하지 않고도 Azure에서 사용자 지정 도메인을 인식할 수 있도록 허용합니다. 도메인의 DNS 레코드를 수정하면 가동 중지 시간 없이 Blob 엔드포인트에 매핑됩니다.

##### <a name="portal"></a>[포털](#tab/azure-portal)

1. [Azure Portal](https://portal.azure.com)에서 스토리지 계정으로 이동합니다.

2. 메뉴 창의 **보안 + 네트워킹** 에서 **네트워킹** 을 선택합니다.

3. **네트워킹** 페이지에서 **사용자 지정 도메인** 탭을 선택합니다.

   > [!NOTE]
   > 이 옵션은 계층 구조 네임스페이스 기능을 사용하도록 설정된 계정에는 표시되지 않습니다. 이러한 계정에 대해 PowerShell 또는 Azure CLI를 사용하여 이 단계를 완료합니다.

3. **도메인 이름** 텍스트 상자에 하위 도메인을 포함하여 사용자 지정 도메인 이름을 입력합니다.

   예를 들어 도메인이 *contoso.com* 이고 하위 도메인 별칭이 *www* 이면 `www.contoso.com`을 입력합니다. 하위 도메인이 *사진* 이면 `photos.contoso.com`을 입력합니다.

4. **간접 CNAME 유효성 검사 사용** 확인란을 선택합니다.

5. 사용자 지정 도메인을 등록하려면 **저장** 단추를 선택합니다.

   등록에 성공한 경우 포털에서 스토리지 계정이 성공적으로 업데이트되었음을 알립니다. 사용자 지정 도메인이 Azure에서 확인되었지만 도메인 공급자를 사용하여 CNAME 레코드를 만들 때까지 도메인에 대한 트래픽은 아직 스토리지 계정으로 라우팅되지 않습니다. 다음 섹션에서 구성을 완료합니다.

##### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

다음 PowerShell 명령을 실행 합니다.

```powershell
Set-AzStorageAccount -ResourceGroupName <resource-group-name> -Name <storage-account-name> -CustomDomainName <custom-domain-name> -UseSubDomain $true
```

- `<resource-group-name>` 자리 표시자를 리소스 그룹의 이름으로 바꿉니다.

- `<storage-account-name>` 자리 표시자를 스토리지 계정 이름으로 바꿉니다.

- `<custom-domain-name>` 자리 표시자를 하위 도메인을 포함하는 사용자 지정 도메인의 이름으로 바꿉니다.

  예를 들어 도메인이 *contoso.com* 이고 하위 도메인 별칭이 *www* 이면 `www.contoso.com`을 입력합니다. 하위 도메인이 *사진* 이면 `photos.contoso.com`을 입력합니다.

도메인에 대한 트래픽은 도메인 공급자를 사용하여 CNAME 레코드를 만들 때까지 아직 스토리지 계정으로 라우팅되지 않습니다. 다음 섹션에서 구성을 완료합니다.

##### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

다음 PowerShell 명령을 실행 합니다.

```azurecli
az storage account update \
   --resource-group <resource-group-name> \ 
   --name <storage-account-name> \
   --custom-domain <custom-domain-name> \
   --use-subdomain true
  ```

- `<resource-group-name>` 자리 표시자를 리소스 그룹의 이름으로 바꿉니다.

- `<storage-account-name>` 자리 표시자를 스토리지 계정 이름으로 바꿉니다.

- `<custom-domain-name>` 자리 표시자를 하위 도메인을 포함하는 사용자 지정 도메인의 이름으로 바꿉니다.

  예를 들어 도메인이 *contoso.com* 이고 하위 도메인 별칭이 *www* 이면 `www.contoso.com`을 입력합니다. 하위 도메인이 *사진* 이면 `photos.contoso.com`을 입력합니다.

도메인에 대한 트래픽은 도메인 공급자를 사용하여 CNAME 레코드를 만들 때까지 아직 스토리지 계정으로 라우팅되지 않습니다. 다음 섹션에서 구성을 완료합니다.

---

#### <a name="step-4-create-a-cname-record-with-your-domain-provider"></a>4단계: 도메인 공급자를 사용하여 CNAME 레코드 만들기

호스트 이름을 가리키는 임시 CNAME 레코드를 만듭니다.

1. 도메인 등록자의 웹 사이트에 로그인한 다음, DNS 설정 관리 페이지로 이동합니다.

   **도메인 이름**, **DNS** 또는 **이름 서버 관리** 라는 섹션에서 해당 페이지를 찾을 수 있습니다.

2. CNAME 레코드를 관리하는 섹션을 찾습니다.

   고급 설정 페이지로 이동하여 **CNAME**, **별칭** 또는 **하위 도메인** 을 찾아야 할 수 있습니다.

3. CNAME 레코드를 만듭니다. 해당 레코드의 일부로 다음 항목을 제공합니다.

   - `www` 또는 `photos`와 같은 하위 도메인 별칭입니다. 하위 도메인은 필수이며 루트 도메인은 지원되지 않습니다.

   - 이 문서 앞부분의 [스토리지 엔드포인트의 호스트 이름 가져오기](#endpoint-2) 섹션에서 가져온 호스트 이름입니다.

#### <a name="step-5-test-your-custom-domain"></a>5단계: 사용자 지정 도메인 테스트

사용자 지정 도메인이 Blob 서비스 엔드포인트에 매핑되었는지 확인하려면 스토리지 계정 내의 공용 컨테이너에서 Blob을 만듭니다. 그런 다음, 웹 브라우저에서 `http://<subdomain.customdomain>/<mycontainer>/<myblob>` 형식의 URI를 사용하여 Blob에 액세스합니다.

예를 들어 *photos.contoso.com* 사용자 지정 하위 도메인의 `myforms` 컨테이너에서 웹 형식에 액세스하려면 다음 URI를 사용할 수 있습니다. `http://photos.contoso.com/myforms/applicationform.htm`

### <a name="remove-a-custom-domain-mapping"></a>사용자 지정 도메인 매핑 제거

사용자 지정 도메인 매핑을 제거하려면 사용자 지정 도메인의 등록을 취소합니다. 다음 절차 중 하나를 수행하십시오.

#### <a name="portal"></a>[포털](#tab/azure-portal)

1. [Azure Portal](https://portal.azure.com)에서 스토리지 계정으로 이동합니다.

2. 메뉴 창의 **보안 + 네트워킹** 에서 **네트워킹** 을 선택합니다.

3. **네트워킹** 페이지에서 **사용자 지정 도메인** 탭을 선택합니다.

4. 사용자 지정 도메인 이름이 포함된 텍스트 상자의 콘텐츠를 지웁니다.

5. **저장** 단추를 선택합니다.

사용자 지정 도메인이 성공적으로 제거된 경우 스토리지 계정이 성공적으로 업데이트되었다는 포털 알림이 표시됩니다.

#### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

사용자 지정 도메인 등록을 제거하려면 [Set-AzStorageAccount](/powershell/module/az.storage/set-azstorageaccount) PowerShell cmdlet을 사용한 다음, `-CustomDomainName` 인수 값에 빈 문자열(`""`)을 지정합니다.

- 명령 형식:

  ```powershell
  Set-AzStorageAccount `
      -ResourceGroupName "<resource-group-name>" `
      -AccountName "<storage-account-name>" `
      -CustomDomainName ""
  ```

- 명령 예:

  ```powershell
  Set-AzStorageAccount `
      -ResourceGroupName "myresourcegroup" `
      -AccountName "mystorageaccount" `
      -CustomDomainName ""
  ```

#### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

사용자 지정 도메인 등록을 제거하려면 [az storage account update](/cli/azure/storage/account) CLI 명령을 사용한 다음, `--custom-domain` 인수 값에 빈 문자열(`""`)을 지정합니다.

- 명령 형식:

  ```azurecli
  az storage account update \
      --name <storage-account-name> \
      --resource-group <resource-group-name> \
      --custom-domain ""
  ```

- 명령 예:

  ```azurecli
  az storage account update \
      --name mystorageaccount \
      --resource-group myresourcegroup \
      --custom-domain ""
  ```

---

<a id="enable-https"></a>

## <a name="map-a-custom-domain-with-https-enabled"></a>사용하는 것으로 설정된 HTTPS로 사용자 지정 도메인 매핑

이 방법은 더 많은 단계가 필요하지만 HTTPS 액세스를 사용할 수 있게 합니다.

사용자가 HTTPS를 사용하여 Blob 또는 웹 콘텐츠에 액세스하지 않아도 되는 경우, 이 문서의 [HTTP만 사용하도록 설정된 사용자 지정 도메인 매핑](#enable-http) 섹션을 참조하세요.

1. Blob 또는 웹 엔드포인트에서 [Azure CDN](../../cdn/cdn-overview.md)을 사용하도록 설정합니다.

   Blob Storage 엔드포인트의 경우 [Azure CDN과 Azure 스토리지 계정 통합](../../cdn/cdn-create-a-storage-account-with-cdn.md)을 참조하세요.

   정적 웹 사이트 엔드포인트의 경우 [Azure CDN과 정적 웹 사이트 통합](static-website-content-delivery-network.md)을 참조하세요.

2. [Azure CDN 콘텐츠를 사용자 지정 도메인에 매핑합니다](../../cdn/cdn-map-content-to-custom-domain.md).

3. [Azure CDN 사용자 지정 도메인에서 HTTPS를 사용하도록 설정합니다](../../cdn/cdn-custom-ssl.md).

   > [!NOTE]
   > 정적 웹 사이트를 업데이트하는 경우 CDN 엔드포인트를 제거하여 CDN 에지 서버에서 캐시된 콘텐츠를 지워야 합니다. 자세한 내용은 [Azure CDN 엔드포인트 제거](../../cdn/cdn-purge-endpoint.md)를 참조하세요.

4. (선택 사항) 다음 지침을 검토합니다.

   - [Azure CDN을 사용하는 SAS(공유 액세스 서명) 토큰](../../cdn/cdn-storage-custom-domain-https.md#shared-access-signatures).

   - [Azure CDN을 사용하는 HTTP에서 HTTPS로의 리디렉션](../../cdn/cdn-storage-custom-domain-https.md#http-to-https-redirection).

   - [Azure CDN과 Blob Storage를 사용하는 경우 가격 책정 및 청구](../../cdn/cdn-storage-custom-domain-https.md#pricing-and-billing).

## <a name="feature-support"></a>기능 지원

이 표에서는 사용자 계정에서 이 기능이 지원되는 방법과 특정 기능을 활성화할 때 지원에 미치는 영향을 보여 줍니다.

| Storage 계정 유형                | Blob Storage(기본 지원)   | Data Lake Storage Gen2 <sup>1</sup>                        | NFS 3.0 <sup>1</sup>
|-----------------------------|---------------------------------|------------------------------------|--------------------------------------------------|
| 표준 범용 v2 | ![예](../media/icons/yes-icon.png) | ![예](../media/icons/yes-icon.png)  <sup>2</sup> | ![예](../media/icons/yes-icon.png)  <sup>2</sup> |
| Premium 블록 Blob          | ![예](../media/icons/yes-icon.png) | ![예](../media/icons/yes-icon.png)  <sup>2</sup> | ![예](../media/icons/yes-icon.png)  <sup>2</sup> |

<sup>1</sup> Data Lake Storage Gen2와 NFS(네트워크 파일 시스템) 3.0 프로토콜 모두에는 계층 구조 네임스페이스를 사용하는 스토리지 계정이 필요합니다.

<sup>2</sup>    기능은 미리 보기 수준에서 지원 됩니다.

## <a name="next-steps"></a>다음 단계

- [Azure Blob Storage의 정적 웹 사이트 호스팅에 대해 알아봅니다.](storage-blob-static-website.md)
