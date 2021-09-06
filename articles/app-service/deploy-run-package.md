---
title: ZIP 패키지에서 앱 실행
description: 원자성을 사용하여 앱의 ZIP 패키지를 배포합니다. ZIP 배포 프로세스 중에 앱 동작에 대한 예측 가능성과 안정성을 개선합니다.
ms.topic: article
ms.date: 01/14/2020
ms.openlocfilehash: 99de9745b75bd21a0fb3126c51b38b047e9b77f4
ms.sourcegitcommit: 8942cdce0108372d6fc5819c71f7f3cf2f02dc60
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/01/2021
ms.locfileid: "113135296"
---
# <a name="run-your-app-in-azure-app-service-directly-from-a-zip-package"></a>Azure App Service에서 ZIP 패키지의 앱 직접 실행하기

[Azure App Service](overview.md)에서는 배포 ZIP 패키지 파일에서 직접 앱을 실행할 수 있습니다. 이 문서에서는 앱에서 해당 기능을 사용하도록 설정하는 방법을 설명합니다.

App Service의 다른 모든 배포 방법에는 공통점이 있습니다. 파일이 앱의 *D:\home\site\wwwroot*(Linux 앱의 경우 */home/site/wwwroot*)에 배포됩니다. 런타임에 앱에서 동일한 디렉터리를 사용하므로 파일 잠금 충돌로 인해 배포가 실패할 수 있으며 일부 파일이 아직 업데이트되지 않았기 때문에 앱이 예상과 다르게 동작할 수 있습니다.

반면 패키지에서 직접 실행하면 패키지의 파일이 *wwwroot* 디렉터리에 복사되지 않습니다. 대신 ZIP 패키지 자체가 읽기 전용 *wwwroot* 디렉터리로 직접 탑재됩니다. 다음은 패키지에서 직접 실행할 경우 누릴 수 있는 여러 이점입니다.

- 배포와 런타임 간의 파일 잠금 충돌을 제거합니다.
- 언제든지 완전히 배포된 앱만 실행되도록 보장합니다.
- (다시 시작으로) 프로덕션 앱에 배포될 수 있습니다.
- Azure Resource Manager 배포의 성능을 향상시킵니다.
- 특히 대규모 npm 패키지 트리가 있는 JavaScript 함수의 경우 콜드 부팅 시간을 줄일 수 있습니다.

> [!NOTE]
> 현재는 ZIP 패키지 파일만 지원됩니다.

[!INCLUDE [Create a project ZIP file](../../includes/app-service-web-deploy-zip-prepare.md)]

## <a name="enable-running-from-package"></a>패키지에서 실행 사용 설정

`WEBSITE_RUN_FROM_PACKAGE` 앱 설정을 사용하면 앱을 패키지에서 실행할 수 있습니다. 이를 설정하려면 Azure CLI를 사용하여 다음 명령을 실행합니다.

```azurecli-interactive
az webapp config appsettings set --resource-group <group-name> --name <app-name> --settings WEBSITE_RUN_FROM_PACKAGE="1"
```

`WEBSITE_RUN_FROM_PACKAGE="1"`을 사용하면 앱의 로컬 패키지에서 앱을 실행할 수 있습니다. [원격 패키지에서 실행](#run-from-external-url-instead)할 수도 있습니다.

## <a name="run-the-package"></a>패키지 실행

App Service에서 패키지를 실행하는 가장 쉬운 방법은 Azure CLI [az webapp deployment source config-zip](/cli/azure/webapp/deployment/source#az_webapp_deployment_source_config_zip) 명령을 사용하는 것입니다. 예를 들면 다음과 같습니다.

```azurecli-interactive
az webapp deployment source config-zip --resource-group <group-name> --name <app-name> --src <filename>.zip
```

`WEBSITE_RUN_FROM_PACKAGE` 앱 설정이 설정되어 있으므로 해당 명령은 패키지 콘텐츠를 앱의 *D:\home\site\wwwroot* 디렉터리에 추출하지 않습니다. 대신 ZIP 파일을 있는 그대로 *D:\home\data\SitePackages* 에 업로드하고, 런타임에 로드할 ZIP 패키지의 이름을 포함하는 *packagename.txt* 를 동일한 디렉터리에 만듭니다. [FTP](deploy-ftp.md)와 같은 다른 방식으로 ZIP 패키지를 업로드하는 경우 *D:\home\data\SitePackages* 디렉터리와 *packagename.txt* 파일을 수동으로 만들어야 합니다.

또한 해당 명령은 앱을 다시 시작합니다. `WEBSITE_RUN_FROM_PACKAGE`가 설정되었기 때문에 App Service는 업로드된 패키지를 읽기 전용 *wwwroot* 디렉터리로 탑재하고 탑재된 해당 디렉터리에서 직접 앱을 실행합니다.

## <a name="run-from-external-url-instead"></a>외부 URL에서 실행

Azure Blob Storage와 같은 외부 URL에서 패키지를 실행할 수도 있습니다. [Azure Storage Explorer](../vs-azure-tools-storage-manage-with-storage-explorer.md)를 사용하여 Blob 스토리지 계정에 패키지 파일을 업로드할 수 있습니다. [SAS(공유 액세스 서명)](../vs-azure-tools-storage-manage-with-storage-explorer.md#generate-a-sas-in-storage-explorer)가 포함된 프라이빗 스토리지 컨테이너를 사용하거나 [관리 ID를 사용](#fetch-a-package-from-azure-blob-storage-using-a-managed-identity)하여 App Service 런타임이 패키지에 안전하게 액세스할 수 있게 해야 합니다. 

Blob 스토리지에 파일을 업로드하고 파일에 대한 SAS URL이 있는 경우 `WEBSITE_RUN_FROM_PACKAGE` 앱 설정을 URL로 설정합니다. 다음 예에서는 Azure CLI를 사용하여 해당 작업을 수행합니다.

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings WEBSITE_RUN_FROM_PACKAGE="https://myblobstorage.blob.core.windows.net/content/SampleCoreMVCApp.zip?st=2018-02-13T09%3A48%3A00Z&se=2044-06-14T09%3A48%3A00Z&sp=rl&sv=2017-04-17&sr=b&sig=bNrVrEFzRHQB17GFJ7boEanetyJ9DGwBSV8OM3Mdh%2FM%3D"
```

Blob 스토리지에 동일한 이름의 업데이트된 패키지를 게시하는 경우 업데이트된 패키지가 App Service에 로드되도록 앱을 다시 시작해야 합니다.

### <a name="fetch-a-package-from-azure-blob-storage-using-a-managed-identity"></a>관리 ID를 사용하여 Azure Blob Storage에서 패키지 페치

[!INCLUDE [Run from package via Identity](../../includes/app-service-run-from-package-via-identity.md)]

## <a name="troubleshooting"></a>문제 해결

- 앱을 패키지에서 직접 실행하면 `wwwroot`가 읽기 전용으로 설정됩니다. 앱에서 해당 디렉터리에 파일을 쓰려고 하면 오류가 발생합니다.
- TAR 및 GZIP 형식은 지원되지 않습니다.
- ZIP 파일은 최대 1GB까지 지원됩니다.
- 해당 기능은 [로컬 캐시](overview-local-cache.md)와 호환되지 않습니다.
- 콜드 부팅 성능을 개선하려면 로컬 Zip 옵션(`WEBSITE_RUN_FROM_PACKAGE`=1)을 사용합니다.

## <a name="more-resources"></a>추가 리소스

- [Azure App Service에 대한 지속적인 배포](deploy-continuous-deployment.md)
- [ZIP 또는 WAR 파일로 코드 배포](deploy-zip.md)
