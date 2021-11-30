---
title: 가상 네트워크에서 App Service 및 Azure Database for PostgreSQL - 유연한 서버를 사용하여 Django 앱을 배포하는 방법에 대한 자습서
description: App Serice를 사용하여 Django 앱 배포 및 Azure Database for PostgreSQL - 가상 네트워크에서 유연한 서버
author: mksuni
ms.author: sumuth
ms.service: postgresql
ms.devlang: azurecli
ms.topic: tutorial
ms.date: 11/30/2021
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: a55412ad70f5ea303a95e2ec3a641ace08b07f7d
ms.sourcegitcommit: dcf3424d7149fceaea0340eb0657baa2c27882a5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/30/2021
ms.locfileid: "133267511"
---
# <a name="tutorial-deploy-django-app-with-app-service-and-azure-database-for-postgresql---flexible-server"></a>자습서: App Service 및 Azure Database for PostgreSQL 사용하여 Django 앱 배포 - 유연한 서버 

이 자습서에서는 Azure에서 App Services 및 Azure Database for PostgreSQL - 유연한 서버를 사용하여 가상 네트워크에 Django 애플리케이션을 배포하는 방법을 알아봅니다.

## <a name="prerequisites"></a>사전 요구 사항

Azure 구독이 아직 없는 경우 시작하기 전에 [체험](https://azure.microsoft.com/free/) 계정을 만듭니다.

이 문서에서는 Azure CLI 버전 2.0 이상을 로컬로 실행해야 합니다. 설치된 버전을 확인하려면 `az --version` 명령을 실행합니다. 설치 또는 업그레이드해야 하는 경우 [Azure CLI 설치](/cli/azure/install-azure-cli)를 참조하세요.

[az login](/cli/azure/authenticate-azure-cli) 명령을 사용하여 계정에 로그인해야 합니다. 해당 구독 이름에 대한 명령 출력에서 **id** 속성을 참고합니다.

```azurecli
az login
```

구독이 여러 개인 경우 리소스가 과금되어야 할 적절한 구독을 선택합니다. [az account set](/cli/azure/account) 명령을 사용하여 계정에 속한 특정 구독 ID를 선택합니다. 구독에 대한 **az login** 출력의 **구독 ID** 속성을 구독 ID 자리 표시자로 바꿉니다.

```azurecli
az account set --subscription <subscription id>
```
## <a name="clone-or-download-the-sample-app"></a>샘플 앱 복제 또는 다운로드

# <a name="git-clone"></a>[git clone](#tab/clone)

샘플 리포지토리를 복제합니다.

```terminal
git clone https://github.com/Azure-Samples/djangoapp
```

그런 다음, 해당 폴더로 이동합니다.

```terminal
cd djangoapp
```

# <a name="download"></a>[다운로드](#tab/download)

[https://github.com/Azure-Samples/djangoapp](https://github.com/Azure-Samples/djangoapp)을 방문하고, **복제** 를 선택한 다음, **ZIP 다운로드** 를 선택합니다.

ZIP 파일의 압축을 *djangoapp* 이라는 폴더에 풉니다.

그런 다음, 해당 *djangoapp* 폴더에서 터미널 창을 엽니다.

---

djangoapp 샘플에는 Django 설명서의 [첫 번째 Django 앱 작성](https://docs.djangoproject.com/en/2.1/intro/tutorial01/)에 따라 가져오는 데이터 기반 Django 설문 조사 앱이 포함되어 있습니다. 편의를 위해 완성된 앱이 여기에 제공됩니다.

또한 샘플은 App Service와 같은 프로덕션 환경에서 실행되도록 수정됩니다.

- 프로덕션 설정은 *azuresite/production.py* 파일에 있습니다. 개발 세부 정보는 *azuresite/settings.py* 에 있습니다.
- `DJANGO_ENV` 환경 변수가 "production"으로 설정되면 앱에서 프로덕션 설정을 사용합니다. 이 환경 변수는 자습서의 뒷부분에서 PostgreSQL 데이터베이스 구성에 사용된 다른 환경 변수와 함께 만듭니다.

이러한 변경은 모든 프로덕션 환경에서 실행되도록 Django를 구성하는 것과 관련이 있으며 App Service에만 국한되지 않습니다. 자세한 내용은 [Django 배포 검사 목록](https://docs.djangoproject.com/en/2.1/howto/deployment/checklist/)을 참조하세요.

## <a name="create-a-postgresql-flexible-server-in-a-new-virtual-network"></a>새 가상 네트워크에 PostgreSQL 유연한 서버 만들기

다음 명령을 사용하여 VNET(가상 네트워크) 내부에 프라이빗 유연한 서버 및 데이터베이스를 만듭니다.
```azurecli
# Create Flexible server in a VNET

az postgres flexible-server create --resource-group myresourcegroup --location westus2

```

이 명령은 다음 작업을 수행하며 몇 분 정도 걸릴 수 있습니다.

- 리소스 그룹이 없는 경우 생성합니다.
- 서버 이름을 입력하지 않은 경우 서버 이름을 생성합니다.
- 새 postgreSQL 서버에 사용할 새 가상 네트워크를 만듭니다. 동일한 가상 네트워크에 웹앱을 추가해야 하므로, 서버에 사용하기 위해 만든 **가상 네트워크 이름과 서브넷 이름을 적어 둡니다**.
- 서버의 관리자 사용자 이름 및 암호를 입력하지 않은 경우 지금 만듭니다. 다음 단계에서 사용할 수 있도록 **사용자 이름과 암호를 적어둡니다**.
- 개발에 사용할 수 있는 ```postgres``` 데이터베이스를 만듭니다. [**psql** 을 실행하여 데이터베이스에 연결](quickstart-create-server-portal.md#connect-to-the-postgresql-database-using-psql)하면 다른 데이터베이스를 만들 수 있습니다.

> [!NOTE]
> 암호를 입력하지 않으면 암호가 자동으로 생성됩니다. 이 암호를 기록해 둡니다. 암호를 잊은 경우 ``` az postgres flexible-server update``` 명령을 사용하여 암호를 다시 설정해야 합니다.


## <a name="deploy-the-code-to-azure-app-service"></a>Azure App Service에 코드 배포

이 섹션에서는 App Service 앱에서 앱 호스트를 만들고, 이 앱을 Postgres 데이터베이스에 연결한 다음, 코드를 해당 호스트에 배포합니다.


### <a name="create-the-app-service-web-app-in-a-virtual-network"></a>가상 네트워크에 App Service 웹앱 만들기

터미널에서 앱 코드가 포함된 리포지토리 루트(`djangoapp`)에 있는지 확인합니다.

[`az webapp up`](/cli/azure/webapp#az_webapp_up) 명령을 사용하여 App Service 앱(호스트 프로세스)을 만듭니다.

```azurecli

# Create a web app
az webapp up --resource-group myresourcegroup --location westus2 --plan DjangoPostgres-tutorial-plan --sku B1 --name <app-name>

# Enable VNET integration for web app.
# Replace <vnet-name> and <subnet-name> with the virtual network and subnet name that the flexible server is using.

az webapp vnet-integration add -g myresourcegroup -n  mywebapp --vnet <vnet-name> --subnet <subnet-name>

# Configure database information as environment variables
# Use the postgres server name , database name , username , password for the database created in the previous steps

az webapp config appsettings set --settings DJANGO_ENV="production" DBHOST="<postgres-server-name>.postgres.database.azure.com" DBNAME="postgres" DBUSER="<username>" DBPASS="<password>"
```
- `--location` 인수에 대해 이전 섹션의 데이터베이스에 사용한 것과 동일한 위치를 사용합니다.
- *\<app-name>* 을 모든 Azure에서 고유한 이름으로 바꿉니다(서버 엔드포인트는 `https://\<app-name>.azurewebsites.net`임). *\<app-name>* 에 허용되는 문자는 `A`-`Z`, `0`-`9` 및 `-`입니다. 회사 이름과 앱 식별자를 조합하여 사용하는 것이 좋습니다.
- 없는 경우 기본 가격 책정 계층(B1)에서 *DjangoPostgres-tutorial-plan* 이라는 [App Service 계획](../../app-service/overview-hosting-plans.md)을 만듭니다. `--plan` 및 `--sku`는 선택 사항입니다.
- 없는 경우 App Service 앱을 만듭니다.
- 아직 사용하도록 설정되지 않은 경우 기본 로깅을 앱에 사용하도록 설정합니다.
- 빌드 자동화를 사용하도록 설정된 ZIP 배포를 사용하여 리포지토리를 업로드합니다.
- **az webapp vnet-integration** 명령은 postgres 서버와 동일한 가상 네트워크에 웹앱을 추가합니다.
- 앱 코드는 다양한 환경 변수에서 데이터베이스 정보를 찾을 것으로 예상합니다. App Service에서 환경 변수를 설정하려면 [az webapp config appsettings set](/cli/azure/webapp/config/appsettings#az_webapp_config_appsettings_set) 명령을 사용하여 "앱 설정"을 만듭니다.

> [!TIP]
> 대부분의 Azure CLI 명령에서는 일반적인 매개 변수(예: 리소스 그룹 이름 및 App Service 계획)를 *.azure/config* 파일에 캐시합니다. 따라서 나중에 명령을 사용하여 동일한 매개 변수를 모두 지정할 필요가 없습니다. 예를 들어 변경한 후에 앱을 다시 배포하려면 매개 변수 없이 `az webapp up`을 다시 실행하기만 하면 됩니다.

### <a name="run-django-database-migrations"></a>Django 데이터베이스 마이그레이션 실행

Django 데이터베이스 마이그레이션은 Azure 데이터베이스에서 PostgreSQL의 스키마가 코드에서 설명한 것과 일치하는지 확인합니다.

1. *https://\<app-name>.scm.azurewebsites.net/webssh/host* 로 이동하여 브라우저에서 SSH 세션을 열고, 데이터베이스 서버 자격 증명이 아니라 Azure 계정 자격 증명을 사용하여 로그인합니다.

1. SSH 세션에서 다음 명령을 실행합니다(**Ctrl**+**Shift**+**V** 를 사용하여 명령을 붙여넣을 수 있음).

    ```bash
    cd site/wwwroot

    # Activate default virtual environment in App Service container
    source /antenv/bin/activate
    # Install packages
    pip install -r requirements.txt
    # Run database migrations
    python manage.py migrate
    # Create the super user (follow prompts)
    python manage.py createsuperuser
    ```

1. `createsuperuser` 명령은 슈퍼 사용자 자격 증명을 요구하는 메시지를 표시합니다. 이 자습서에서는 기본 사용자 이름으로 `root`를 사용하고, **Enter** 키를 눌러 이메일 주소를 비워 두고, 암호로 `postgres1`을 입력합니다.

### <a name="create-a-poll-question-in-the-app"></a>앱에서 설문 조사 질문 만들기

1. 브라우저에서 *http:\//\<app-name>.azurewebsites.net* URL을 엽니다. 데이터베이스에 아직 특정 설문 조사가 없으므로 앱에서 "설문 조사를 사용할 수 없습니다."라는 메시지가 표시됩니다.

1. *http:\//\<app-name>.azurewebsites.net/admin* 으로 이동합니다. 이전 섹션의 슈퍼 사용자 자격 증명(`root` 및 `postgres1`)을 사용하여 로그인합니다. **설문 조사** 아래에서 **질문** 옆에 있는 **추가** 를 선택하고, 몇 가지 선택 항목이 있는 설문 조사 질문을 만듭니다.

1. *http:\//\<app-name>.azurewebsites.net/* 으로 다시 이동하여 질문이 사용자에게 표시되는지 확인합니다. 하지만 일부 데이터를 데이터베이스에 생성하려는 질문에 대답합니다.

**축하합니다.** Linux용 Azure App Service에서 활성 Postgres 데이터베이스를 사용하는 Python Django 웹앱이 실행되고 있습니다.

> [!NOTE]
> App Service는 각 하위 폴더에서 기본적으로 `manage.py startproject`를 통해 만드는 *wsgi.py* 파일을 찾아서 Django 프로젝트를 검색합니다. App Service에서 해당 파일을 찾으면 Django 웹앱이 로드됩니다. 자세한 내용은 [기본 제공 Python 이미지 구성](../../app-service/configure-language-python.md)을 참조하세요.

## <a name="make-code-changes-and-redeploy"></a>코드 변경 및 다시 배포

이 섹션에서는 앱을 로컬로 변경하고 코드를 App Service에 다시 배포합니다. 이 프로세스에서 지속적인 작업을 지원하는 Python 가상 환경을 설정합니다.

### <a name="run-the-app-locally"></a>로컬에서 앱 실행하기

터미널 창에서 다음 명령을 실행합니다. 슈퍼 사용자를 만드는 경우 화면의 지시를 따라야 합니다.

```bash
# Configure the Python virtual environment
python3 -m venv venv
source venv/bin/activate

# Install packages
pip install -r requirements.txt
# Run Django migrations
python manage.py migrate
# Create Django superuser (follow prompts)
python manage.py createsuperuser
# Run the dev server
python manage.py runserver
```
웹앱이 완전히 로드되면 Django 개발 서버에서 http://127.0.0.1:8000/ 에서 개발 서버를 시작하고 있습니다. CTRL-BREAK를 사용하여 서버를 종료하세요."라는 메시지에서 로컬 앱 URL을 제공합니다.

:::image type="content" source="./media/tutorial-django-app-service-postgres/django-dev-server-output.png" alt-text="Django 개발 서버 출력 예제":::

다음 단계를 사용하여 앱을 로컬로 테스트합니다.

1. 브라우저에서 *http:\//localhost:8000* 으로 이동합니다. 그러면 "사용할 수 있는 설문 조사가 없습니다."라는 메시지가 표시됩니다.

1. *http:\//localhost:8000/admin* 으로 이동하고, 이전에 만든 관리 사용자를 사용하여 로그인합니다. 다시 한 번 **설문 조사** 아래에서 **질문** 옆에 있는 **추가** 를 선택하고, 몇 가지 선택 항목이 있는 설문 조사 질문을 만듭니다.

1. 다시 한 번 *http:\//localhost:8000* 으로 이동하고, 질문에 대답하여 앱을 테스트합니다.

1. **Ctrl**+**C** 를 눌러 Django 서버를 중지합니다.

로컬로 실행하는 경우 앱에서 로컬 Sqlite3 데이터베이스를 사용하며 프로덕션 데이터베이스를 방해하지 않습니다. 필요한 경우 로컬 PostgreSQL 데이터베이스를 사용하여 프로덕션 환경을 더 효율적으로 시뮬레이션할 수도 있습니다.



### <a name="update-the-app"></a>앱 업데이트

`polls/models.py`에서 `choice_text`로 시작하는 줄을 찾고, `max_length` 매개 변수를 100으로 변경합니다.

```python
# Find this lie of code and set max_length to 100 instead of 200
choice_text = models.CharField(max_length=100)
```

데이터 모델이 변경되었으므로 새 Django 마이그레이션을 만들고 데이터베이스를 마이그레이션합니다.

```
python manage.py makemigrations
python manage.py migrate
```

`python manage.py runserver`를 사용하여 개발 서버를 다시 실행하고, *http:\//localhost:8000/admin* 에서 앱을 테스트합니다.

다시 한 번 **Ctrl**+**C** 를 사용하여 Django 웹 서버를 중지합니다.


### <a name="redeploy-the-code-to-azure"></a>Azure에 코드 다시 배포

리포지토리 루트에서 다음 명령을 실행합니다.

```azurecli
az webapp up
```

이 명령은 *.azure/config* 파일에 캐시된 매개 변수를 사용합니다. App Service에서 앱이 이미 있음을 검색하므로 코드를 다시 배포하기만 하면 됩니다.



### <a name="rerun-migrations-in-azure"></a>Azure에서 마이그레이션 다시 실행

데이터 모델이 변경되었으므로 App Service에서 데이터베이스 마이그레이션을 다시 실행해야 합니다.

*https://\<app-name>.scm.azurewebsites.net/webssh/host* 로 이동하여 브라우저에서 SSH 세션을 다시 엽니다. 그런 다음, 다음 명령을 실행합니다.

```
cd site/wwwroot

# Activate default virtual environment in App Service container
source /antenv/bin/activate
# Run database migrations
python manage.py migrate
```

### <a name="review-app-in-production"></a>프로덕션에서 앱 검토

*http:\//\<app-name>.azurewebsites.net* 으로 이동하여 프로덕션에서 앱을 다시 테스트합니다. (데이터베이스 필드의 길이만 변경되었으므로 질문을 만들 때 더 긴 응답을 입력하려고 하는 경우에만 변경 내용이 인식됩니다.)

> [!TIP]
> [django-storages](https://django-storages.readthedocs.io/en/latest/backends/azure.html) 명령을 사용하여 Azure 스토리지에 고정 자산과 미디어 자산을 저장할 수 있습니다. Azure CDN을 사용하여 정적 파일을 압축할 수 있습니다.


## <a name="manage-your-app-in-the-azure-portal"></a>Azure Portal에서 앱 관리

[Azure Portal](https://portal.azure.com)에서 앱 이름을 검색하고, 결과에서 앱을 선택합니다.

:::image type="content" source="./media/tutorial-django-app-service-postgres/navigate-to-django-app-in-app-services-in-the-azure-portal.png" alt-text="Azure Portal에서 Python Django 앱으로 이동합니다.":::

기본적으로 포털에는 일반적인 성능 보기를 제공하는 앱의 **개요** 페이지가 표시됩니다. 여기서는 찾아보기, 중지, 다시 시작 및 삭제와 같은 기본 관리 작업을 수행할 수도 있습니다. 페이지의 왼쪽에 있는 탭에서는 열 수 있는 여러 구성 페이지를 보여 줍니다.

:::image type="content" source="./media/tutorial-django-app-service-postgres/manage-django-app-in-app-services-in-the-azure-portal.png" alt-text="Azure Portal의 개요 페이지에서 Python Django 앱 관리":::


## <a name="clean-up-resources"></a>리소스 정리

앱을 유지하거나 다음 자습서로 계속 진행하려면 [다음 단계](#next-steps)로 건너뜁니다. 그렇지 않으면 요금이 지속적으로 청구되지 않도록 이 자습서에서 만든 리소스 그룹을 삭제할 수 있습니다.

```azurecli
az group delete -g myresourcegroup
```

이 명령은 *.azure/config* 파일에 캐시된 리소스 그룹 이름을 사용합니다. 리소스 그룹을 삭제하면 해당 그룹에 포함된 모든 리소스도 할당 취소되고 삭제됩니다.

## <a name="next-steps"></a>다음 단계

사용자 지정 DNS 이름을 앱에 매핑하는 방법을 알아봅니다.

> [!div class="nextstepaction"]
> [자습서: 앱에 사용자 지정 DNS 이름 매핑](../../app-service/app-service-web-tutorial-custom-domain.md)

App Service에서 Python 앱을 실행하는 방법을 알아봅니다.

> [!div class="nextstepaction"]
> [Python 앱 구성](../../app-service/configure-language-python.md)
