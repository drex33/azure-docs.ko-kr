---
title: '자습서: 서비스 커넥터를 사용하여 Postgres on Azure App Service로 Django 앱 빌드'
description: PostgreSQL 데이터베이스를 사용하는 Python 웹앱을 만들어 Azure에 배포합니다. 이 자습서에서는 Django 프레임워크를 사용하고, 앱이 Azure App Service on Linux에 호스팅되며, App Service 및 데이터베이스가 서비스 커넥터와 연결됩니다.
ms.devlang: python
author: shizn
ms.author: xshi
ms.service: serviceconnector
ms.topic: tutorial
ms.date: 11/30/2021
zone_pivot_groups: postgres-server-options
ms.custom: ignite-fall-2021
ms.openlocfilehash: ba4acaa0e6917641163c4af4cb37162a0a7de1b2
ms.sourcegitcommit: dcf3424d7149fceaea0340eb0657baa2c27882a5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/30/2021
ms.locfileid: "133269981"
---
# <a name="tutorial-using-service-connector-preview-to-build-a-django-app-with-postgres-on-azure-app-service"></a>자습서: 서비스 커넥터(미리 보기)를 사용하여 Postgres on Azure App Service로 Django 앱 빌드

> [!NOTE]
> 이 자습서에서는 서비스 커넥터(미리 보기)를 사용하여 웹앱을 데이터베이스 서비스에 쉽게 연결할 수 있습니다. 여기에서는 이 미리 보기 기능을 사용하여 유사점을 확인할 수 있도록 [App Service 자습서](../app-service/tutorial-python-postgresql-app.md)를 수정했습니다. 이 자습서의 [4.2 환경 변수를 구성하여 데이터베이스 연결](#42-configure-environment-variables-to-connect-the-database) 섹션을 살펴보고 서비스 커넥터가 작동되는 위치를 확인하며 App Service 자습서에서 제공된 연결 프로세스를 간소화합니다.

::: zone pivot="postgres-single-server"

이 자습서에서는 데이터 기반 Python [Django](https://www.djangoproject.com/) 웹앱을 [Azure App Service](overview.md)에 배포하고 Azure Database for Postgres 데이터베이스에 연결하는 방법을 보여 줍니다. 위의 옵션을 선택하여 PostgresSQL 유연한 서버를 사용해 볼 수도 있습니다. 유연한 서버는 더 간단한 배포 메커니즘을 제공하고 지속적인 비용을 낮춥니다.

이 자습서에서는 Azure CLI를 사용하여 다음 작업을 완료합니다.

> [!div class="checklist"]
> * Python 및 Azure CLI를 사용하여 초기 환경 설정
> * Azure Database for PostgreSQL 데이터베이스 만들기
> * Azure App Service에 코드 배포 및 PostgreSQL에 연결
> * 코드 업데이트 및 다시 배포
> * 진단 로그 보기
> * Azure Portal에서 웹앱 관리

:::zone-end

::: zone pivot="postgres-flexible-server"

이 자습서에서는 데이터 기반 Python [Django](https://www.djangoproject.com/) 웹앱을 배포하여 [Azure App Service Azure Database for PostgreSQL](overview.md) 유연한 [서버(미리 보기)](../postgresql/flexible-server/index.yml) 데이터베이스에 연결하는 방법을 보여줍니다. PostgreSQL 유연한 서버를 사용할 수 없는 경우 위의 단일 서버 옵션을 선택합니다. 

이 자습서에서는 Azure CLI를 사용하여 다음 작업을 완료합니다.

> [!div class="checklist"]
> * Python 및 Azure CLI를 사용하여 초기 환경 설정
> * Azure Database for PostgreSQL 유연한 서버 데이터베이스 만들기
> * Azure App Service 코드를 배포하고 PostgreSQL 유연한 서버에 연결
> * 코드 업데이트 및 다시 배포
> * 진단 로그 보기
> * Azure Portal에서 웹앱 관리


:::zone-end

## <a name="1-set-up-your-initial-environment"></a>1. 초기 환경 설정

1. 활성 구독이 포함된 Azure 계정이 있어야 합니다. [체험 계정을 만듭니다](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
1. <a href="https://www.python.org/downloads/" target="_blank">Python 3.6 이상</a>을 설치합니다.
1. <a href="/cli/azure/install-azure-cli" target="_blank">Azure CLI</a> 2.30.0 이상을 설치합니다. 이를 통해 셸에서 명령을 실행하여 Azure 리소스를 프로비저닝하고 구성할 수 있습니다.

터미널 창을 열고 Python 버전이 3.6 이상인지 확인합니다.

# <a name="bash"></a>[Bash](#tab/bash)

```bash
python3 --version
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```cmd
py -3 --version
```

# <a name="cmd"></a>[Cmd](#tab/cmd)

```cmd
py -3 --version
```

---

Azure CLI 버전이 2.30.0 이상인지 확인합니다.

```azurecli
az --version
```

업그레이드해야 하는 경우 `az upgrade` 명령(버전 2.30.0 이상 필요)을 시도하거나 <a href="/cli/azure/install-azure-cli" target="_blank">Azure CLI 설치</a>를 참조하세요.

그런 다음, CLI를 통해 Azure에 로그인합니다.

```azurecli
az login
```

이 명령은 로그인 정보를 수집하는 브라우저를 엽니다. 명령이 완료되면 구독에 대한 정보가 포함된 JSON 출력이 표시됩니다.

로그인하면 Azure CLI에서 Azure 명령을 실행하여 구독의 리소스를 사용할 수 있습니다.

문제가 있나요? [알려주세요](https://aka.ms/DjangoCLITutorialHelp).

## <a name="2-clone-or-download-the-sample-app"></a>2. 샘플 앱 복제 또는 다운로드

# <a name="git-clone"></a>[git clone](#tab/clone)

샘플 리포지토리를 복제합니다.

```terminal
git clone https://github.com/Azure-Samples/serviceconnector-webapp-postgresql-django.git
```

그런 다음, 해당 폴더로 이동합니다.

```terminal
cd serviceconnector-webapp-postgresql-django
```

::: zone pivot="postgres-flexible-server"

유연한 서버(미리 보기)의 경우 샘플의 유연한 서버 분기를 사용합니다. 이 분기에는 데이터베이스 서버 URL을 설정하는 방법과 `'OPTIONS': {'sslmode': 'require'}` Azure PostgreSQL 유연한 서버의 필요에 따라 Django 데이터베이스 구성에 추가하는 방법과 같은 몇 가지 필요한 변경 내용이 포함되어 있습니다.

```terminal
git checkout flexible-server
```

::: zone-end

# <a name="download"></a>[다운로드](#tab/download)

[https://github.com/Azure-Samples/djangoapp](https://github.com/Azure-Samples/djangoapp)로 이동합니다.

::: zone pivot="postgres-flexible-server"
유연한 서버의 경우 "마스터"라는 분기 컨트롤을 선택하고 대신 유연한 서버 분기를 선택합니다.
::: zone-end

**복제** 를 선택하고 **ZIP 다운로드** 를 선택합니다. 

ZIP 파일의 압축을 *djangoapp* 이라는 폴더에 풉니다. 

그런 다음, 해당 *djangoapp* 폴더에서 터미널 창을 엽니다.

---

djangoapp 샘플에는 Django 설명서의 [첫 번째 Django 앱 작성](https://docs.djangoproject.com/en/3.1/intro/tutorial01/)에 따라 가져오는 데이터 기반 Django 설문 조사 앱이 포함되어 있습니다. 편의를 위해 완성된 앱이 여기에 제공됩니다.

또한 샘플은 App Service와 같은 프로덕션 환경에서 실행되도록 수정됩니다.

- 프로덕션 설정은 *azuresite/production.py* 파일에 있습니다. 개발 설정은 *azuresite/settings.py* 에 있습니다.
- `WEBSITE_HOSTNAME` 환경 변수가 설정되면 앱에서 프로덕션 설정을 사용합니다. Azure App Service는 자동으로 이 변수를 웹앱의 URL로 설정합니다(예: `msdocs-django.azurewebsites.net`).

프로덕션 설정은 모든 프로덕션 환경에서 실행되도록 Django를 구성하는 것과 관련이 있으며 App Service에만 국한되지 않습니다. 자세한 내용은 [Django 배포 검사 목록](https://docs.djangoproject.com/en/3.1/howto/deployment/checklist/)을 참조하세요. 또한 일부 변경 내용에 대한 자세한 내용은 [Azure의 Django에 대한 프로덕션 설정](../app-service/configure-language-python.md#production-settings-for-django-apps)을 참조하세요.

문제가 있나요? [알려주세요](https://aka.ms/DjangoCLITutorialHelp).

## <a name="3-create-postgres-database-in-azure"></a>3. Azure에서 Postgres 데이터베이스 만들기

::: zone pivot="postgres-single-server"
<!-- > [!NOTE]
> Before you create an Azure Database for PostgreSQL server, check which [compute generation](../postgresql/concepts-pricing-tiers.md#compute-generations-and-vcores) is available in your region. -->

모든 명령에 해당 매개 변수를 제공할 필요가 없도록 Azure CLI에서 매개 변수 캐싱을 사용하도록 설정합니다. (캐시된 값은 *.azure* 폴더에 저장됩니다.)

```azurecli
az config param-persist on 
```

Azure CLI용 `db-up` 확장을 설치합니다.

```azurecli
az extension add --name db-up
```

`az` 명령이 인식되지 않는 경우 [초기 환경 설정](#1-set-up-your-initial-environment)에서 설명한 대로 Azure CLI가 설치되어 있는지 확인합니다.

그런 다음, [`az postgres up`](/cli/azure/postgres#az_postgres_up) 명령을 사용하여 Azure에서 Postgres 데이터베이스를 만듭니다.

```azurecli
az postgres up --resource-group ServiceConnector-tutorial-rg --location eastus --sku-name B_Gen5_1 --server-name <postgres-server-name> --database-name pollsdb --admin-user <admin-username> --admin-password <admin-password> --ssl-enforcement Enabled
```

- *\<postgres-server-name>* 를 **모든 Azure에서 고유** 한 이름으로 **바꿉니다**(서버 엔드포인트는 `https://<postgres-server-name>.postgres.database.azure.com`이 됨). 회사 이름과 다른 고유한 값을 조합하여 사용하는 것이 좋습니다.
- *\<admin-username>* 및 *\<admin-password>* 의 경우 이 Postgres 서버에 대한 관리자 사용자를 만들기 위한 자격 증명을 지정합니다. 관리 사용자 이름은 *azure_superuser*, *azure_pg_admin*, *admin*, *administrator*, *root*, *guest* 또는 *public* 이 될 수 없습니다. *pg_* 로 시작할 수 없습니다. 암호는 다음 세 가지 범주에서 **8~128자** 를 포함해야 합니다. 영문 대문자, 영문 소문자, 숫자(0-9) 및 영숫자가 아닌 문자(예: !, #, %). 암호는 사용자 이름을 포함할 수 없습니다.
- 사용자 이름 또는 암호에 `$` 문자를 사용하지 마세요. 나중에 Python 앱을 실행하는 데 사용되는 Linux 컨테이너 내에서 `$` 문자가 특수한 의미를 갖는 이러한 값을 사용하여 환경 변수를 만듭니다.
- 여기서 사용되는 B_Gen5_1(기본, 5세대, 1개 코어) [가격 책정 계층](../postgresql/concepts-pricing-tiers.md)이 가장 저렴합니다. 프로덕션 데이터베이스의 경우 GP_Gen5_2(범용, 5세대, 2개 코어) 계층을 대신 사용하려면 `--sku-name` 인수를 생략합니다.

이 명령은 다음 작업을 수행하며 몇 분 정도 걸릴 수 있습니다.

- 아직 없는 경우 `ServiceConnector-tutorial-rg`라는 [리소스 그룹](../azure-resource-manager/management/overview.md#terminology)을 만듭니다.
- `--server-name` 인수로 명명된 Postgres 서버를 만듭니다.
- `--admin-user` 및 `--admin-password` 인수를 사용하여 관리자 계정을 만듭니다. 명령에서 고유한 자격 증명을 생성할 수 있도록 이러한 인수를 생략할 수 있습니다.
- `--database-name` 인수로 명명된 `pollsdb` 데이터베이스를 만듭니다.
- 로컬 IP 주소에서 액세스하도록 설정합니다.
- Azure 서비스에서 액세스하도록 설정합니다.
- `pollsdb` 데이터베이스에 액세스할 수 있는 데이터베이스 사용자를 만듭니다.

다른 `az postgres` 및 `psql` 명령을 사용하여 모든 단계를 개별적으로 수행할 수 있지만, `az postgres up`은 모든 단계를 함께 수행합니다.

명령이 완료되면 서버 URL, 생성된 사용자 이름(예: "joyfulKoala@msdocs-djangodb-12345") 및 GUID 암호와 함께 데이터베이스에 대한 다른 연결 문자열이 포함된 JSON 개체가 출력됩니다. 이 자습서의 뒷부분에서 필요하므로 **사용자 이름과 암호를 임시 텍스트 파일에 복사** 합니다.

<!-- not all locations support az postgres up -->
> [!TIP]
> `-l <location-name>`은 [Azure 지역](https://azure.microsoft.com/global-infrastructure/regions/) 중 하나로 설정할 수 있습니다. 구독에 사용할 수 있는 지역은 [`az account list-locations`](/cli/azure/account#az_account_list_locations) 명령을 사용하여 가져올 수 있습니다. 프로덕션 앱의 경우 데이터베이스와 앱을 동일한 위치에 배치합니다.

::: zone-end

::: zone pivot="postgres-flexible-server"

1. 모든 명령에 해당 매개 변수를 제공할 필요가 없도록 Azure CLI에서 매개 변수 캐싱을 사용하도록 설정합니다. (캐시된 값은 *.azure* 폴더에 저장됩니다.)

    ```azurecli
    az config param-persist on 
    ```

1. [리소스 그룹](../azure-resource-manager/management/overview.md#terminology)을 만듭니다(원하는 경우 이름을 변경할 수 있음). 리소스 그룹 이름은 캐시되고 후속 명령에 자동으로 적용됩니다.

    ```azurecli
    az group create --name ServiceConnector-tutorial-rg --location eastus
    ```

1. 데이터베이스 서버를 만듭니다(이 프로세스는 몇 분 정도 소요됨).

    ```azurecli
    az postgres flexible-server create --sku-name Standard_B1ms --public-access all
    ```
    
    `az` 명령이 인식되지 않는 경우 [초기 환경 설정](#1-set-up-your-initial-environment)에서 설명한 대로 Azure CLI가 설치되어 있는지 확인합니다.
    
    [az postgres flexible-server create](/cli/azure/postgres/flexible-server#az_postgres_flexible_server_create) 명령은 다음 작업을 수행합니다. 이 작업은 몇 분 정도 걸립니다.
    
    - 캐시된 이름이 아직 없는 경우 기본 리소스 그룹을 만듭니다.
    - PostgreSQL 유연한 서버를 만듭니다.
        - 기본적으로 이 명령은 `server383813186`과 같은 생성된 이름을 사용합니다. `--name` 매개 변수를 통해 고유한 이름을 지정할 수 있습니다. Azure의 모든 이름은 고유해야 합니다.
        - 이 명령은 가장 저렴한 `Standard_B1ms` 가격 책정 계층을 사용합니다. 기본 `Standard_D2s_v3` 계층을 사용하려면 `--sku-name` 인수를 생략합니다.
        - 이 명령은 이전 `az group create` 명령에서 캐시된 리소스 그룹 및 위치를 사용합니다. 이 예제에서는 `eastus` 지역의 리소스 그룹 `ServiceConnector-tutorial-rg`입니다.
    - 사용자 이름 및 암호를 사용하여 관리자 계정을 만듭니다. `--admin-user` 및 `--admin-password` 매개 변수를 통해 이러한 값을 직접 지정할 수 있습니다.
    - 기본적으로 `flexibleserverdb`라는 데이터베이스를 만듭니다. `--database-name` 매개 변수를 사용하여 데이터베이스 이름을 지정할 수 있습니다.
    - `--public-access` 매개 변수를 사용하여 제어할 수 있는 완전한 퍼블릭 액세스 권한을 사용하도록 설정합니다.
    
1. 이 명령이 완료되면 이 자습서의 뒷부분에서 나오는 출력 값, 특히 호스트, 사용자 이름 및 암호와 데이터베이스 이름이 필요하므로 **명령의 JSON 출력을 파일에 복사합니다**.

::: zone-end

문제가 있나요? [알려주세요](https://aka.ms/DjangoCLITutorialHelp).

## <a name="4-deploy-the-code-to-azure-app-service"></a>4. Azure App Service에 코드 배포

이 섹션에서는 App Service 앱에서 앱 호스트를 만들고, 이 앱을 Postgres 데이터베이스에 연결한 다음, 코드를 해당 호스트에 배포합니다.

### <a name="41-create-the-app-service-app"></a>4.1 App Service 앱 만들기

::: zone pivot="postgres-single-server"

터미널에서 앱 코드가 포함된 *djangoapp* 리포지토리 폴더에 있는지 확인합니다.

[`az webapp up`](/cli/azure/webapp#az_webapp_up) 명령을 사용하여 App Service 앱(호스트 프로세스)을 만듭니다.

```azurecli
az webapp up --resource-group ServiceConnector-tutorial-rg --location eastus --plan ServiceConnector-tutorial-plan --sku B1 --name <app-name>
```
<!-- without --sku creates PremiumV2 plan -->

- `--location` 인수의 경우 [서비스 커넥터가 지원](concept-region-support.md)하는 위치를 사용해야 합니다.
- *\<app-name>* 를 모든 Azure에서 고유한 이름으로 **바꿉니다**(서버 엔드포인트는 `https://<app-name>.azurewebsites.net`임). *\<app-name>* 에 허용되는 문자는 `A`-`Z`, `0`-`9` 및 `-`입니다. 회사 이름과 앱 식별자를 조합하여 사용하는 것이 좋습니다.

이 명령은 다음 작업을 수행하며 몇 분 정도 걸릴 수 있습니다.

<!-- - Create the resource group if it doesn't exist. `--resource-group` is optional. -->
<!-- No it doesn't. az webapp up doesn't respect --resource-group -->
- 아직 없는 경우 [리소스 그룹](../azure-resource-manager/management/overview.md#terminology)을 만듭니다. (이 명령에서는 이전에 데이터베이스를 만든 것과 동일한 리소스 그룹을 사용합니다.)
- 없는 경우 기본 가격 책정 계층(B1)에서 *DjangoPostgres-tutorial-plan* 이라는 [App Service 계획](../app-service/overview-hosting-plans.md)을 만듭니다. `--plan` 및 `--sku`는 선택 사항입니다.
- 없는 경우 App Service 앱을 만듭니다.
- 아직 사용하도록 설정되지 않은 경우 기본 로깅을 앱에 사용하도록 설정합니다.
- 빌드 자동화를 사용하도록 설정된 ZIP 배포를 사용하여 리포지토리를 업로드합니다.
- 일반적인 매개 변수(예: 리소스 그룹 이름 및 App Service 계획)를 *.azure/config* 파일에 캐시합니다. 따라서 나중에 명령을 사용하여 동일한 매개 변수를 모두 지정할 필요가 없습니다. 예를 들어 변경한 후에 앱을 다시 배포하려면 매개 변수 없이 `az webapp up`을 다시 실행하기만 하면 됩니다. 그러나 CLI 확장에서 제공하는 명령(예: `az postgres up`)은 현재 캐시를 사용하지 않으므로 `az webapp up`의 초기 사용을 사용하여 리소스 그룹과 위치를 여기에 지정해야 합니다.

::: zone-end

::: zone pivot="postgres-flexible-server"

1. 터미널에서 앱 코드가 포함된 *djangoapp* 리포지토리 폴더에 있는지 확인합니다.

1. 샘플 앱의 `flexible-server` 분기로 전환합니다. 이 분기에는 PostgreSQL 유연한 서버에 필요한 특정 구성이 포함 됩니다.

    ```cmd
    git checkout flexible-server
    ```

1. 다음 [`az webapp up`](/cli/azure/webapp#az_webapp_up) 명령을 실행하여 앱에 대한 App Service 호스트를 만듭니다.

    ```azurecli
    az webapp up --name <app-name> --sku B1 
    ```
    <!-- without --sku creates PremiumV2 plan -->
        
    이 명령은 이전 `az group create` 명령에서 캐시된 리소스 그룹 및 위치(이 예제에서는 `eastus` 지역에 있는 그룹 `Python-Django-PGFlex-rg`)를 사용하여 다음 작업을 수행합니다. 이 작업에는 몇 분 정도 걸릴 수 있습니다.
    
    <!-- - Create the resource group if it doesn't exist. `--resource-group` is optional. -->
    <!-- No it doesn't. az webapp up doesn't respect --resource-group -->
    - 기본 가격 책정 계층(B1)에서 [App Service 플랜](../app-service/overview-hosting-plans.md)을 만듭니다. `--sku`를 생략하여 기본값을 사용할 수 있습니다.
    - App Service 앱을 만듭니다.
    - 앱에 대해 기본 로깅을 사용하도록 설정합니다.
    - 빌드 자동화를 사용하도록 설정된 ZIP 배포를 사용하여 리포지토리를 업로드합니다.

::: zone-end

성공적으로 배포되면 명령에서 다음 예제와 같은 JSON 출력을 생성합니다.

![az webapp up 명령 출력 예제](../app-service/media/tutorial-python-postgresql-app/az-webapp-up-output.png)

문제가 있나요? 먼저 [문제 해결 가이드](../app-service/configure-language-python.md#troubleshooting)를 참조하세요. 그렇지 않으면 [알려주세요](https://aka.ms/DjangoCLITutorialHelp).

### <a name="42-configure-environment-variables-to-connect-the-database"></a>4.2 데이터베이스를 연결하도록 환경 변수 구성

이제 코드가 App Service에 배포되면 다음 단계는 Azure에서 앱을 Postgres 데이터베이스에 연결하는 것입니다.

앱 코드는 `AZURE_POSTGRESQL_HOST`, `AZURE_POSTGRESQL_NAME`, `AZURE_POSTGRESQL_USER` 및 `AZURE_POSTGRESQL_PASS`라는 네 가지 환경 변수에서 데이터베이스 정보를 찾을 수 있어야 합니다.

App Service에서 환경 변수를 설정하려면 다음 [az connection create]() 명령을 사용하여 "앱 설정"을 만듭니다.

::: zone pivot="postgres-single-server"

```azurecli
az webapp connection create postgres --client-type django
```

리소스 그룹, 앱 이름, db 이름은 캐시된 값에서 그려집니다. 이 명령을 실행하는 동안 postgres 데이터베이스의 관리자 암호를 제공해야 합니다.

- 이 명령은 앱 코드에서 예상한 대로 "AZURE_POSTGRESQL_HOST", "AZURE_POSTGRESQL_NAME", "AZURE_POSTGRESQL_USER", "AZURE_POSTGRESQL_PASS"라는 설정을 만듭니다.
- 관리자 자격 증명을 잊어버린 경우 이 명령은 다시 설정하도록 안내합니다.


::: zone-end

::: zone pivot="postgres-flexible-server"
```azurecli
az webapp connection create postgres --client-type django
```

리소스 그룹, 앱 이름, db 이름은 캐시된 값에서 그려집니다. 이 명령을 실행하는 동안 postgres 데이터베이스의 관리자 암호를 제공해야 합니다.

- 이 명령은 앱 코드에서 예상한 대로 "AZURE_POSTGRESQL_HOST", "AZURE_POSTGRESQL_NAME", "AZURE_POSTGRESQL_USER", "AZURE_POSTGRESQL_PASS"라는 설정을 만듭니다.
- 관리자 자격 증명을 잊어버린 경우 이 명령은 다시 설정하도록 안내합니다.

::: zone-end

> [!NOTE]
> "구독이 Microsoft.ServiceLinker를 사용하도록 등록되어 있지 않습니다."라는 오류 메시지가 표시되면 `az provider register -n Microsoft.ServiceLinker`를 실행하여 Service Connector 리소스 공급자를 등록하고 연결 명령을 다시 실행합니다. 

Python 코드에서 `os.environ.get('AZURE_POSTGRESQL_HOST')`과 같은 명령문을 사용하여 이러한 설정을 환경 변수로 액세스합니다. 자세한 내용은 [환경 변수 액세스](../app-service/configure-language-python.md#access-environment-variables)를 참조하세요.

문제가 있나요? 먼저 [문제 해결 가이드](../app-service/configure-language-python.md#troubleshooting)를 참조하세요. 그렇지 않으면 [알려주세요](https://aka.ms/DjangoCLITutorialHelp).

### <a name="43-run-django-database-migrations"></a>4.3 Django 데이터베이스 마이그레이션 실행

Django 데이터베이스 마이그레이션은 Azure 데이터베이스에서 PostgreSQL의 스키마가 코드에서 설명한 것과 일치하는지 확인합니다.

1. `az webpp ssh`를 실행하여 브라우저에서 웹앱에 대한 SSH 세션을 엽니다.

    ```azurecli
    az webapp ssh
    ```


1. SSH 세션에서 다음 명령을 실행합니다(**Ctrl**+**Shift**+**V** 를 사용하여 명령을 붙여넣을 수 있음).

    ```bash
    # Run database migrations
    python manage.py migrate

    # Create the super user (follow prompts)
    python manage.py createsuperuser
    ```

    데이터베이스 연결과 관련된 오류가 발생하면 이전 섹션에서 만든 애플리케이션 설정 값을 확인합니다.

1. `createsuperuser` 명령은 슈퍼 사용자 자격 증명을 요구하는 메시지를 표시합니다. 이 자습서에서는 기본 사용자 이름으로 `root`를 사용하고, **Enter** 키를 눌러 이메일 주소를 비워 두고, 암호로 `Pollsdb1`을 입력합니다.

1. 데이터베이스가 잠겨 있다는 오류가 표시되면 이전 섹션의 `az webapp settings` 명령을 실행했는지 확인합니다. 이러한 설정이 없으면 마이그레이션 명령이 데이터베이스와 통신할 수 없으므로 오류가 발생합니다.

문제가 있나요? 먼저 [문제 해결 가이드](../app-service/configure-language-python.md#troubleshooting)를 참조하세요. 그렇지 않으면 [알려주세요](https://aka.ms/DjangoCLITutorialHelp).
    
### <a name="44-create-a-poll-question-in-the-app"></a>4.4 앱에서 설문 조사 질문 만들기

1. 앱 웹 사이트를 엽니다. 데이터베이스에 아직 특정 설문 조사가 없으므로 앱에서 "설문 조사 앱" 및 "설문 조사를 사용할 수 없습니다."라는 메시지가 표시됩니다.

    ```azurecli
    az webapp browse
    ```

    "애플리케이션 오류"가 표시되면 이전 단계인 [데이터베이스를 연결하도록 환경 변수 구성](#42-configure-environment-variables-to-connect-the-database)에서 필요한 설정을 만들지 않았거나 해당 값에 오류가 포함되어 있을 수 있습니다. `az webapp config appsettings list` 명령을 실행하여 설정을 확인합니다.

    오류를 수정하도록 설정이 업데이트되면 앱이 다시 시작할 때까지 1분 정도 기다린 다음, 브라우저를 새로 고칩니다.

1. URL에 `/admin`을 추가하여(예: `http://<app-name>.azurewebsites.net/admin`) 웹앱의 관리자 페이지로 이동하세요. 이전 섹션의 Django 슈퍼 사용자 자격 증명(`root` 및 `Pollsdb1`)을 사용하여 로그인합니다. **설문 조사** 아래에서 **질문** 옆에 있는 **추가** 를 선택하고, 몇 가지 선택 항목이 있는 설문 조사 질문을 만듭니다.

1. 기본 웹 사이트(`http://<app-name>.azurewebsites.net`)로 돌아가 이제 질문이 사용자에게 표시되는지 확인합니다. 데이터베이스에 일부 데이터를 생성하려는 질문에 대답합니다.

**축하합니다.** Linux용 Azure App Service에서 활성 Postgres 데이터베이스를 사용하는 Python Django 웹앱이 실행되고 있습니다.

> [!NOTE]
> App Service는 각 하위 폴더에서 기본적으로 `manage.py startproject`를 통해 만드는 *wsgi.py* 파일을 찾아서 Django 프로젝트를 검색합니다. App Service에서 해당 파일을 찾으면 Django 웹앱이 로드됩니다. 자세한 내용은 [기본 제공 Python 이미지 구성](../app-service/configure-language-python.md)을 참조하세요.


## <a name="5-clean-up-resources"></a>5. 리소스 정리

앱을 유지하거나 추가 자습서로 계속 진행하려면 [다음 단계](#next-steps)로 건너뜁니다. 그렇지 않으면 요금이 지속적으로 청구되지 않도록 이 자습서에서 만든 리소스 그룹을 삭제할 수 있습니다.

```azurecli
az group delete --name ServiceConnector-tutorial-rg --no-wait
```

리소스 그룹을 삭제하면 해당 그룹에 포함된 모든 리소스도 할당 취소되고 삭제됩니다. 이 명령을 사용하기 전에 해당 그룹의 리소스가 더 이상 필요하지 않은지 확인하세요.

모든 리소스를 삭제하는 데 시간이 걸릴 수 있습니다. `--no-wait` 인수를 사용하면 명령이 즉시 반환됩니다.

문제가 있나요? [알려주세요](https://aka.ms/DjangoCLITutorialHelp).

## <a name="next-steps"></a>다음 단계

서비스 커넥터에 대해 자세히 알아보려면 아래 나열된 자습서를 따르세요.

> [!div class="nextstepaction"]
> [서비스 커넥터 개념에 대한 자세한 정보](./concept-service-connector-internals.md)
