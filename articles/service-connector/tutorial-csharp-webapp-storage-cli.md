---
title: '자습서: 서비스 커넥터를 사용하여 Azure Storage Blob에 연결된 웹 애플리케이션 배포'
description: 서비스 커넥터를 사용하여 Azure Storage Blob에 연결된 웹 애플리케이션 생성합니다.
author: shizn
ms.author: xshi
ms.service: serviceconnector
ms.topic: tutorial
ms.date: 10/28/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: 8768d188ae56896fe1f7272ee18533e95343e69c
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/05/2021
ms.locfileid: "131846561"
---
# <a name="tutorial-deploy-web-application-connected-to-azure-storage-blob-with-service-connector"></a>자습서: 서비스 커넥터를 사용하여 Azure Storage Blob에 연결된 웹 애플리케이션 배포

관리 ID를 사용하여 Azure App Service에서 실행되는 웹앱(로그인한 사용자가 아님)용 Azure Storage에 액세스하는 방법을 알아봅니다. 이 자습서에서는 Azure CLI를 사용하여 다음 작업을 완료합니다.

> [!div class="checklist"]
> * Azure CLI를 사용하여 초기 환경 설정
> * 스토리지 계정 및 Azure Blob Storage 컨테이너를 만듭니다.
> * 서비스 커넥터를 사용하여 Azure App Service에 코드 배포 및 관리 ID로 스토리지에 연결

## <a name="1-set-up-your-initial-environment"></a>1. 초기 환경 설정

1. 활성 구독이 포함된 Azure 계정이 있어야 합니다. [체험 계정을 만듭니다](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
2. <a href="/cli/azure/install-azure-cli" target="_blank">Azure CLI</a> 2.30.0 이상을 설치합니다. 이를 통해 셸에서 명령을 실행하여 Azure 리소스를 프로비저닝하고 구성할 수 있습니다.


Azure CLI 버전이 2.30.0 이상인지 확인합니다.

```Azure CLI
az --version
```

업그레이드해야 하는 경우 `az upgrade` 명령(버전 2.11 이상 필요)을 시도하거나 <a href="/cli/azure/install-azure-cli" target="_blank">Azure CLI 설치</a>를 참조하세요.

그런 다음, CLI를 통해 Azure에 로그인합니다.

```Azure CLI
az login
```

이 명령은 로그인 정보를 수집하는 브라우저를 엽니다. 명령이 완료되면 구독에 대한 정보가 포함된 JSON 출력이 표시됩니다.

로그인하면 Azure CLI에서 Azure 명령을 실행하여 구독의 리소스를 사용할 수 있습니다.

## <a name="2-clone-or-download-the-sample-app"></a>2. 샘플 앱 복제 또는 다운로드

샘플 리포지토리를 복제합니다.
```Bash
git clone https://github.com/Azure-Samples/serviceconnector-webapp-storageblob-dotnet.git
```

그리고 리포지토리의 루트 폴더로 이동 합니다.
```Bash
cd WebAppStorageMISample
```

## <a name="3-create-the-app-service-app"></a>3. App Service 앱 만들기

터미널에서 앱 코드가 포함된 *WebAppStorageMISample* 리포지토리 폴더에 있는지 확인합니다.

[`az webapp up`](/cli/azure/webapp#az_webapp_up) 명령을 사용하여 App Service 앱(호스트 프로세스)을 만듭니다.

```Azure CLI
az webapp up --name <app-name> --sku B1 --location eastus --resource-group ServiceConnector-tutorial-rg
```

- `--location` 인수의 경우 [서비스 커넥터가 지원](concept-region-support.md)하는 위치를 사용해야 합니다.
- *\<app-name>* 를 모든 Azure에서 고유한 이름으로 **바꿉니다**(서버 엔드포인트는 `https://<app-name>.azurewebsites.net`임). *\<app-name>* 에 허용되는 문자는 `A`-`Z`, `0`-`9` 및 `-`입니다. 회사 이름과 앱 식별자를 조합하여 사용하는 것이 좋습니다.

## <a name="4-create-a-storage-account-and-blob-storage-container"></a>4. 스토리지 계정 및 Blob Storage 컨테이너 만들기

범용 v2 스토리지 계정 및 Blob Storage 컨테이너를 만들려면 다음 명령을 실행합니다. *\<storage-name>* 를 고유 이름으로 **교체** 합니다. 컨테이너 이름은 소문자여야 하고, 문자 또는 숫자로 시작해야 하며, 문자, 숫자 및 대시(-) 문자만 포함할 수 있습니다.

```Azure CLI
az storage account create --name <storage-name> --resource-group ServiceConnector-tutorial-rg --sku Standard_RAGRS --https-only
```


## <a name="5-connect-app-service-app-to-blob-storage-container-with-managed-identity"></a>5. 관리 ID를 사용하여 Blob Storage 컨테이너에 App Service 앱 연결

터미널에서 관리 ID를 사용하여 웹앱을 Blob Storage에 연결하기 위해 다음 명령을 실행합니다.

```Azure CLI
az webapp connection create storage-blob -g ServiceConnector-tutorial-rg -n <app-name> --tg ServiceConnector-tutorial-rg --account <storage-name> --system-identity
```

- *\<app-name>* 를 단계 3에서 사용한 웹앱 이름으로 **교체** 합니다.
- *\<storage-name>* 를 단계 4에서 사용한 스토리지 앱 이름으로 **교체** 합니다.

> [!NOTE]
> "구독이 Microsoft.ServiceLinker를 사용하도록 등록되어 있지 않습니다."라는 오류 메시지가 표시되면 `az provider register -n Microsoft.ServiceLinker`를 실행하여 Service Connector 리소스 공급자를 등록하고 연결 명령을 다시 실행합니다. 

## <a name="6-run-sample-code"></a>6. 샘플 코드 실행

터미널에서 다음 명령을 실행하여 브라우저에서 샘플 애플리케이션을 엽니다. *\<app-name>* 을 단계 3에서 사용한 웹앱 이름으로 교체합니다.

```Azure CLI
az webapp browse --name <app-name> 
```

샘플 코드는 웹 애플리케이션입니다. 인덱스 페이지를 새로 고칠 때마다 스토리지 컨테이너에 대해 `Hello Service Connector! Current is {UTC Time Now}` 텍스트를 포함하는 BLOB을 만들거나 업데이트하고 인덱스 페이지에 표시하기 위해 다시 읽습니다.

## <a name="next-steps"></a>다음 단계

서비스 커넥터에 대해 자세히 알아보려면 아래 나열된 자습서를 따르세요.

> [!div class="nextstepaction"]
> [서비스 커넥터 개념에 대해 알아보기](./concept-service-connector-internals.md)
