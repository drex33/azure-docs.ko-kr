---
title: 빠른 시작 - Azure CLI를 사용하여 Spring Cloud에서 서비스 연결 만들기
description: Azure CLI를 사용하여 Spring Cloud에서 서비스 연결을 만드는 방법을 보여 주는 빠른 시작
author: shizn
ms.author: xshi
ms.service: serviceconnector
ms.topic: quickstart
ms.date: 10/29/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: 8d8905d58d6a67609fc28bb5cf680a66a51c2797
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131456996"
---
# <a name="quickstart-create-a-service-connection-in-spring-cloud-with-the-azure-cli"></a>빠른 시작: Azure CLI를 사용하여 Spring Cloud에서 서비스 연결 만들기

[Azure CLI(Azure 명령줄 인터페이스)](/cli/azure)는 Azure 리소스를 만들고 관리하는 데 사용되는 명령 세트입니다. Azure CLI는 모든 Azure 서비스에서 사용할 수 있으며, Azure를 빠르게 사용할 수 있도록 자동화에 초점을 두고 설계되었습니다. 이 빠른 시작에서는 Azure CLI를 사용하여 Azure Web PubSub 인스턴스를 만드는 옵션을 보여줍니다.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- 이 빠른 시작에는 Azure CLI 버전 2.22.0 이상이 필요합니다. Azure Cloud Shell을 사용하는 경우 최신 버전이 이미 설치되어 있습니다.

- 이 빠른 시작에서는 이미 Azure에서 실행 중인 Spring Cloud 애플리케이션이 하나 이상 있다고 가정하겠습니다. Spring Cloud 애플리케이션이 없는 경우 [생성](../spring-cloud/quickstart.md)합니다.


## <a name="view-supported-target-service-types"></a>지원되는 대상 서비스 유형 보기

Azure CLI [az spring-cloud connection]() 명령을 사용하여 Spring Cloud 애플리케이션에 대한 서비스 연결을 만들고 관리할 수 있습니다. 

```azurecli-interactive
az provider register -n Microsoft.ServiceLinker
az spring-cloud connection list-support-types
```

## <a name="create-a-service-connection"></a>서비스 연결 만들기

Azure CLI [az spring-cloud connection]() 명령을 사용해 BLOB 스토리지에 대한 서비스 연결을 만들고 다음 정보를 제공할 수 있습니다.

- **Spring Cloud 리소스 그룹 이름:** Spring Cloud의 리소스 그룹 이름입니다.
- **Spring Cloud 이름:** Spring Cloud의 이름입니다.
- **Spring Cloud 애플리케이션 이름:** 대상 서비스와 연결되는 Spring Cloud 애플리케이션의 이름입니다.
- **대상 서비스 리소스 그룹 이름:** BLOB 스토리지의 리소스 그룹 이름입니다.
- **스토리지 계정 이름:** BLOB 스토리지의 계정 이름입니다.

```azurecli-interactive
az spring-cloud connection create storage-blob -g <spring_cloud_resource_group> --service <spring_cloud_name> --app <app_name> --deployment default --tg <storage_resource_group> --account <storage_account_name> --system-identity
```

> [!NOTE]
> BLOB 스토리지가 없는 경우, `az spring-cloud connection create storage-blob -g <app_service_resource_group> -n <app_service_name> --tg <storage_resource_group> --account <storage_account_name> --system-identity --new`를 실행해 새로운 스토리지를 만들어 애플리케이션 서비스에 바로 연결할 수 있습니다.

## <a name="view-connections"></a>연결 보기

Azure CLI [az spring-cloud connection]() 명령을 사용해 Spring Cloud 애플리케이션에 대한 연결을 나열하고 다음 정보를 제공합니다.

```azurecli-interactive
az spring-cloud connection list -g <your-spring-cloud-resource-group> --spring-cloud <your-spring-cloud-name>
```

## <a name="next-steps"></a>다음 단계

아래 나열된 자습서를 따라 서비스 커넥터로 사용자만의 애플리케이션을 빌드할 수 있습니다.

> [!div class="nextstepaction"]
> [자습서: Spring Cloud + MySQL](./tutorial-java-spring-mysql.md)

> [!div class="nextstepaction"]
> [자습서: Spring Cloud + Confluent Cloud의 Apache Kafka](./tutorial-java-spring-confluent-kafka.md)
