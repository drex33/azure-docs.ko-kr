---
title: Azure CLI를 사용한 데이터 관리(Azure Data Lake Storage Gen2)
description: Azure CLI를 사용하여 계층 구조 네임스페이스가 있는 스토리지 계정에서 디렉터리 및 파일을 만들고 관리합니다.
services: storage
author: normesta
ms.service: storage
ms.subservice: data-lake-storage-gen2
ms.topic: how-to
ms.date: 02/17/2021
ms.author: normesta
ms.reviewer: prishet
ms.custom: devx-track-azurecli
ms.openlocfilehash: 8dec2e939c6f39169d4c8fbbdfbcdf08d4d42599
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128651582"
---
# <a name="use-azure-cli-to-manage-directories-and-files-in-azure-data-lake-storage-gen2"></a>Azure Data Lake Storage Gen2에서 Azure CLI를 사용하여 디렉터리 및 파일 관리

이 문서에서는 [Azure CLI](/cli/azure/)(명령줄 인터페이스)를 사용하여 계층 구조 네임스페이스가 있는 스토리지 계정에서 디렉터리 및 파일을 만들고 관리하는 방법을 보여 줍니다.

디렉터리 및 파일의 ACL(액세스 제어 목록)을 가져오거나 설정하고 업데이트하는 방법에 대한 자세한 내용은 [Azure CLI를 사용한 Azure Data Lake Storage Gen2 ACL 관리](data-lake-storage-acl-cli.md)를 참조하세요.

[샘플](https://github.com/Azure/azure-cli/blob/dev/src/azure-cli/azure/cli/command_modules/storage/docs/ADLS%20Gen2.md) | [피드백 제공](https://github.com/Azure/azure-cli-extensions/issues)

## <a name="prerequisites"></a>사전 요구 사항

- Azure 구독 자세한 내용은 [Azure 평가판 받기를 참조하세요.](https://azure.microsoft.com/pricing/free-trial/)

- 계층 구조 네임스페이스를 사용하도록 설정된 스토리지 계정입니다. 만들려면 [다음 지침](create-data-lake-storage-account.md)을 수행합니다.

- Azure CLI 버전 `2.6.0` 이상.

## <a name="ensure-that-you-have-the-correct-version-of-azure-cli-installed"></a>올바른 버전의 Azure CLI를 설치했는지 확인

1. [Azure Cloud Shell](../../cloud-shell/overview.md)을 열거나 Azure CLI를 로컬로 [설치](/cli/azure/install-azure-cli)한 경우 Windows PowerShell과 같은 명령 콘솔 애플리케이션을 엽니다.

2. 다음 명령을 사용하여 설치된 Azure CLI 버전이 `2.6.0` 이상인지 확인합니다.

   ```azurecli
    az --version
   ```

   Azure CLI 버전이 `2.6.0`보다 낮은 경우 이후 버전을 설치합니다. 자세한 내용은 [Azure CLI 설치](/cli/azure/install-azure-cli)를 참조하세요.

## <a name="connect-to-the-account"></a>계정에 연결

1. Azure CLI를 로컬로 사용하는 경우 로그인 명령을 실행합니다.

   ```azurecli
   az login
   ```

   CLI는 기본 브라우저를 열 수 있으면 기본 브라우저를 열고 Azure 로그인 페이지를 로드합니다.

   그렇지 않으면 [https://aka.ms/devicelogin](https://aka.ms/devicelogin)에서 브라우저 페이지를 열고 터미널에 표시된 권한 부여 코드를 입력합니다. 그런 다음 브라우저에서 계정 자격 증명으로 로그인합니다.

   다른 인증 방법에 대한 자세한 내용은 [Azure CLI를 사용하여 Blob 또는 큐 데이터에 대한 액세스 권한 부여](./authorize-data-operations-cli.md)를 참조하세요.

2. ID가 둘 이상의 구독과 연결된 경우 정적 웹 사이트를 호스트하는 스토리지 계정의 구독으로 활성 구독을 설정합니다.

   ```azurecli
   az account set --subscription <subscription-id>
   ```

   `<subscription-id>` 자리 표시자 값을 구독의 ID로 바꿉니다.

> [!NOTE]
> 이 문서에 제공된 예제는 Azure AD(Active Directory) 권한 부여를 보여 줍니다. 권한 부여 방법에 대한 자세한 내용은 [Azure CLI를 사용하여 Blob 또는 큐 데이터에 대한 액세스 권한 부여I](./authorize-data-operations-cli.md)를 참조하세요.

## <a name="create-a-container"></a>컨테이너 만들기

컨테이너는 파일의 파일 시스템 역할을 합니다. `az storage fs create` 명령을 사용하여 만들 수 있습니다.

다음 예제에서는 `my-file-system`라는 컨테이너를 만듭니다.

```azurecli
az storage fs create -n my-file-system --account-name mystorageaccount --auth-mode login
```

## <a name="show-container-properties"></a>컨테이너 속성 표시

`az storage fs show` 명령을 사용하여 콘솔에 컨테이너 속성을 출력할 수 있습니다.

```azurecli
az storage fs show -n my-file-system --account-name mystorageaccount --auth-mode login
```

## <a name="list-container-contents"></a>컨테이너 내용 나열

`az storage fs file list` 명령을 사용하여 디렉터리의 콘텐츠를 나열합니다.

다음 예제에서는 `my-file-system`이라는 컨테이너의 콘텐츠를 나열합니다.

```azurecli
az storage fs file list -f my-file-system --account-name mystorageaccount --auth-mode login
```

## <a name="delete-a-container"></a>컨테이너 삭제

`az storage fs delete` 명령을 사용하면 컨테이너를 삭제할 수 있습니다.

다음 예제에서는 `my-file-system`라는 컨테이너를 삭제합니다.

```azurecli
az storage fs delete -n my-file-system --account-name mystorageaccount --auth-mode login
```

## <a name="create-a-directory"></a>디렉터리 만들기

`az storage fs directory create` 명령을 사용하여 디렉터리 참조를 만듭니다.

다음 예제에서는 `mystorageaccount`라는 계정에 있는 `my-file-system`이라는 컨테이너에 `my-directory`라는 디렉터리를 추가합니다.

```azurecli
az storage fs directory create -n my-directory -f my-file-system --account-name mystorageaccount --auth-mode login
```

## <a name="show-directory-properties"></a>디렉터리 속성 표시

`az storage fs directory show` 명령을 사용하여 콘솔에 디렉터리의 속성을 출력할 수 있습니다.

```azurecli
az storage fs directory show -n my-directory -f my-file-system --account-name mystorageaccount --auth-mode login
```

## <a name="rename-or-move-a-directory"></a>디렉터리 이름 바꾸기 또는 이동

`az storage fs directory move` 명령을 사용하여 디렉터리의 이름을 바꾸거나 디렉터리를 이동합니다.

다음 예제에서는 동일한 컨테이너에 있는 디렉터리의 이름을 `my-directory`에서 `my-new-directory`로 바꿉니다.

```azurecli
az storage fs directory move -n my-directory -f my-file-system --new-directory "my-file-system/my-new-directory" --account-name mystorageaccount --auth-mode login
```

다음 예제에서는 `my-second-file-system`이라는 컨테이너로 디렉터리를 이동합니다.

```azurecli
az storage fs directory move -n my-directory -f my-file-system --new-directory "my-second-file-system/my-new-directory" --account-name mystorageaccount --auth-mode login
```

## <a name="delete-a-directory"></a>디렉터리 삭제

`az storage fs directory delete` 명령을 사용하여 디렉터리를 삭제합니다.

다음 예제에서는 `my-directory`라는 디렉터리를 삭제합니다.

```azurecli
az storage fs directory delete -n my-directory -f my-file-system  --account-name mystorageaccount --auth-mode login
```

## <a name="check-if-a-directory-exists"></a>디렉터리가 존재하는지 확인

`az storage fs directory exists` 명령을 사용하여 컨테이너에 특정 디렉터리가 있는지 확인합니다.

다음 예제에서는 `my-file-system` 컨테이너에 `my-directory`라는 디렉터리가 있는지 확인합니다.

```azurecli
az storage fs directory exists -n my-directory -f my-file-system --account-name mystorageaccount --auth-mode login
```

## <a name="download-from-a-directory"></a>디렉터리에서 다운로드

`az storage fs file download` 명령을 사용하여 디렉터리에서 파일을 다운로드합니다.

다음 예제에서는 `my-directory`라는 디렉터리에서 `upload.txt`라는 파일을 다운로드합니다.

```azurecli
az storage fs file download -p my-directory/upload.txt -f my-file-system -d "C:\myFolder\download.txt" --account-name mystorageaccount --auth-mode login
```

## <a name="list-directory-contents"></a>디렉터리 콘텐츠 나열

`az storage fs file list` 명령을 사용하여 디렉터리의 콘텐츠를 나열합니다.

다음 예제에서는 `mystorageaccount`라는 스토리지 계정의 `my-file-system` 컨테이너에 있는 `my-directory`라는 디렉터리의 콘텐츠를 나열합니다.

```azurecli
az storage fs file list -f my-file-system --path my-directory --account-name mystorageaccount --auth-mode login
```

## <a name="upload-a-file-to-a-directory"></a>디렉터리에 파일 업로드

`az storage fs file upload` 명령을 사용하여 디렉터리에 파일을 업로드합니다.

다음 예제에서는 `upload.txt`라는 파일을 `my-directory`라는 디렉터리에 업로드합니다.

```azurecli
az storage fs file upload -s "C:\myFolder\upload.txt" -p my-directory/upload.txt  -f my-file-system --account-name mystorageaccount --auth-mode login
```

## <a name="show-file-properties"></a>파일 속성 표시

`az storage fs file show` 명령을 사용하여 콘솔에 파일의 속성을 출력할 수 있습니다.

```azurecli
az storage fs file show -p my-file.txt -f my-file-system --account-name mystorageaccount --auth-mode login
```

## <a name="rename-or-move-a-file"></a>파일 이름 바꾸기 또는 이동

`az storage fs file move` 명령을 사용하여 파일의 이름을 바꾸거나 파일을 이동합니다.

다음 예제에서는 파일의 이름을 `my-file.txt`에서 `my-file-renamed.txt`로 바꿉니다.

```azurecli
az storage fs file move -p my-file.txt -f my-file-system --new-path my-file-system/my-file-renamed.txt --account-name mystorageaccount --auth-mode login
```

## <a name="delete-a-file"></a>파일 삭제

`az storage fs file delete` 명령을 사용하여 파일을 삭제합니다.

다음 예제에서는 `my-file.txt`라는 파일을 삭제합니다.

```azurecli
az storage fs file delete -p my-directory/my-file.txt -f my-file-system  --account-name mystorageaccount --auth-mode login
```

## <a name="see-also"></a>참고 항목

- [샘플](https://github.com/Azure/azure-cli/blob/dev/src/azure-cli/azure/cli/command_modules/storage/docs/ADLS%20Gen2.md)
- [사용자 의견 제공](https://github.com/Azure/azure-cli-extensions/issues)
- [알려진 문제](data-lake-storage-known-issues.md#api-scope-data-lake-client-library)
- [Azure CLI를 사용한 Azure Data Lake Storage Gen2의 ACL 관리](data-lake-storage-acl-cli.md)
