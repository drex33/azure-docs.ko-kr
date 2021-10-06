---
title: '자습서: 동일한 가상 네트워크에 Azure Database for MySQL 유연한 서버(미리 보기) 및 Azure App Service 웹앱 만들기'
description: 웹앱을 사용하여 가상 네트워크에 Azure Database for MySQL 유연한 서버(미리 보기)를 만드는 빠른 시작 가이드
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.devlang: azurecli
ms.topic: tutorial
ms.date: 03/18/2021
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: 0d95def7048b3077232bb728a97c28107ec80313
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/13/2021
ms.locfileid: "128654468"
---
# <a name="tutorial-create-an-azure-database-for-mysql---flexible-server-preview-with-app-services-web-app-in-virtual-network"></a>자습서: App Services 웹앱을 사용하여 가상 네트워크에 Azure Database for MySQL - 유연한 서버(미리 보기) 만들기

[[!INCLUDE[applies-to-mysql-flexible-server](../includes/applies-to-mysql-flexible-server.md)]


> [!IMPORTANT]
> Azure Database for MySQL - 유연한 서버는 현재 공개 미리 보기로 제공됩니다.


이 자습서에서는 MySQL 유연한 서버(미리 보기)를 사용하여 [가상 네트워크](../../virtual-network/virtual-networks-overview.md) 내에 Azure App Service 웹앱을 만드는 방법을 보여줍니다.

이 자습서에서는 다음 방법에 대해 알아봅니다.
>[!div class="checklist"]
> * 가상 네트워크에서 MySQL 유연한 서버 만들기
> * App Service에 위임할 서브넷 만들기
> * 웹앱 만들기
> * 가상 네트워크에 웹앱 추가
> * 웹앱에서 Postgres에 연결 

## <a name="prerequisites"></a>사전 요구 사항

[!INCLUDE [flexible-server-free-trial-note](../includes/flexible-server-free-trial-note.md)]

이 문서에서는 Azure CLI 버전 2.0 이상을 로컬로 실행해야 합니다. 설치된 버전을 확인하려면 `az --version` 명령을 실행합니다. 설치 또는 업그레이드해야 하는 경우 [Azure CLI 설치](/cli/azure/install-azure-cli)를 참조하세요.

[az login](/cli/azure/reference-index#az_login) 명령을 사용하여 계정에 로그인해야 합니다. 해당 구독 이름에 대한 명령 출력에서 **id** 속성을 참고합니다.

```azurecli
az login
```

구독이 여러 개인 경우 리소스가 과금되어야 할 적절한 구독을 선택합니다. [az account set](/cli/azure/account) 명령을 사용하여 계정에 속한 특정 구독 ID를 선택합니다. 구독에 대한 **az login** 출력의 **구독 ID** 속성을 구독 ID 자리 표시자로 바꿉니다.

```azurecli
az account set --subscription <subscription ID>
```

## <a name="create-an-azure-database-for-mysql-flexible-server"></a>Azure Database for MySQL 유연한 서버 만들기

다음 명령을 사용하여 VNET(가상 네트워크) 내부에 프라이빗 유연한 서버를 만듭니다.
```azurecli
az mysql flexible-server create --resource-group myresourcegroup --location westus2
```
연결 문자열과 새로 만든 가상 네트워크의 이름을 복사합니다. 이 명령은 다음 작업을 수행하며 몇 분 정도 걸릴 수 있습니다.

- 리소스 그룹이 없는 경우 생성합니다.
- 서버 이름을 입력하지 않은 경우 서버 이름을 생성합니다.
- 새 MySQL 서버에 사용할 새 가상 네트워크를 만듭니다. 동일한 가상 네트워크에 웹앱을 추가해야 하므로, 서버에 사용하기 위해 만든 가상 네트워크 이름과 서브넷 이름을 적어 둡니다.
- 서버의 관리자 사용자 이름 및 암호를 입력하지 않은 경우 지금 만듭니다.
- **flexibleserverdb** 라는 빈 데이터베이스를 만듭니다.

> [!NOTE]
> 암호를 입력하지 않으면 암호가 자동으로 생성됩니다. 이 암호를 기록해 둡니다. 암호를 잊은 경우 ``` az mysql flexible-server update``` 명령을 사용하여 암호를 다시 설정해야 합니다.

## <a name="create-subnet-for-app-service-endpoint"></a>App Service 엔드포인트에 대한 서브넷 만들기
이제 App Service 웹앱 엔드포인트에 위임된 서브넷이 있어야 합니다. 다음 명령을 실행하여 데이터베이스 서버가 생성된 것과 동일한 가상 네트워크에 새 서브넷을 만듭니다. 

```azurecli
az network vnet subnet create -g myresourcegroup --vnet-name VNETName --name webappsubnetName  --address-prefixes 10.0.1.0/24  --delegations Microsoft.Web/serverFarms --service-endpoints Microsoft.Web
```
웹앱을 만든 후 웹앱에 대한 VNET 통합 규칙을 추가하는 데 필요하므로 이 명령 뒤에 가상 네트워크 이름과 서브넷 이름을 기록해 둡니다. 

## <a name="create-a-web-app"></a>웹앱 만들기

이 섹션에서는 App Service 앱에서 앱 호스트를 만들고, 이 앱을 MySQL 데이터베이스에 연결합니다. 터미널에서 애플리케이션 코드의 리포지토리 루트에 있는지 확인합니다.

az webapp up 명령을 사용하여 App Service 앱(호스트 프로세스) 만들기

```azurecli
az webapp up --resource-group myresourcegroup --location westus2 --plan testappserviceplan --sku P2V2 --name mywebapp
```

> [!NOTE]
> - --location 인수의 경우 이전 섹션의 데이터베이스에 사용한 것과 동일한 위치를 사용합니다.
> - _\<app-name\>_ 을 모든 Azure에서 고유한 이름으로 바꿉니다(서버 엔드포인트는 `https://\<app-name>.azurewebsites.net`임). \<app-name\>에 허용되는 문자는 A-Z, 0-9 및 -입니다. 회사 이름과 앱 식별자를 조합하여 사용하는 것이 좋습니다.
> - App Service 기본 계층에서는 VNET 통합이 지원되지 않습니다. 표준 또는 프리미엄을 사용하세요. 

이 명령은 다음 작업을 수행하며 몇 분 정도 걸릴 수 있습니다.

- 리소스 그룹이 없는 경우 생성합니다. (이 명령에서는 이전에 데이터베이스를 만든 것과 동일한 리소스 그룹을 사용합니다.)
- ```testappserviceplan```이라는 App Service 요금제가 기본 가격 책정 계층(B1)에 없는 경우 이 요금제를 만듭니다. --plan 및 --sku는 선택 사항입니다.
- 없는 경우 App Service 앱을 만듭니다.
- 아직 사용하도록 설정되지 않은 경우 기본 로깅을 앱에 사용하도록 설정합니다.
- 빌드 자동화를 사용하도록 설정된 ZIP 배포를 사용하여 리포지토리를 업로드합니다.

## <a name="add-the-web-app-to-the-virtual-network"></a>가상 네트워크에 웹앱 추가

**az webapp vnet-integration** 명령을 사용하여 지역 가상 네트워크 통합을 웹앱에 추가합니다. _&lt;vnet-name>_ 및 _&lt;subnet-name_ 을 유연한 서버에서 사용하는 가상 네트워크 및 서브넷 이름으로 바꿉니다.

```azurecli
az webapp vnet-integration add -g myresourcegroup -n  mywebapp --vnet VNETName --subnet webappsubnetName
```

## <a name="configure-environment-variables-to-connect-the-database"></a>데이터베이스를 연결하도록 환경 변수 구성

코드가 App Service에 배포되었으므로, 다음 단계는 Azure에서 앱을 유연한 서버에 연결하는 것입니다. 앱 코드는 다양한 환경 변수에서 데이터베이스 정보를 찾을 것으로 예상합니다. App Service에서 환경 변수를 설정하려면 ```az webapp config appsettings``` set 명령을 사용하여 "앱 설정"을 만듭니다.

```azurecli
az webapp config appsettings set --settings DBHOST="<mysql-server-name>.mysql.database.azure.com" DBNAME="flexibleserverdb" DBUSER="<username>" DBPASS="<password>"
```

- 새로 만든 유연한 서버 명령의 _&lt;mysql-server-name>_ , _&lt;username>_ 및 _&lt;password>_ 를 바꿉니다.
- _&lt;username>_ 및 _&lt;password>_ 를 명령에서 자동으로 생성한 자격 증명으로 바꿉니다.
- 리소스 그룹 및 앱 이름은 .azure/config 파일의 캐시된 값에서 가져옵니다.
- 이 명령은 DBHOST, DBNAME, DBUSER 및 DBPASS라는 설정을 만듭니다. 애플리케이션 코드에서 데이터베이스 정보에 다른 이름을 사용하는 경우 코드에 설명된 대로 해당 이름을 앱 설정에 사용하세요.

## <a name="clean-up-resources"></a>리소스 정리

다음 명령을 사용하여 자습서에서 만든 모든 리소스를 정리합니다. 이 명령은 리소스 그룹에 있는 모든 리소스를 삭제합니다.

```azurecli
az group delete -n myresourcegroup
```

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [Azure App Service에 기존 사용자 지정 DNS 이름 매핑](../../app-service/app-service-web-tutorial-custom-domain.md)
