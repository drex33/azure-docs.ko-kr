---
title: Azure Spring Cloud | 애플리케이션 시작, 중지 및 삭제 Microsoft Docs
description: Azure Spring Cloud 애플리케이션을 시작, 중지 및 삭제하는 방법
author: karlerickson
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 10/31/2019
ms.author: karler
ms.custom: devx-track-java
ms.openlocfilehash: 0e27f7ac841635f33bc40db739df4cf2240e2149
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/15/2021
ms.locfileid: "132484760"
---
# <a name="start-stop-and-delete-an-application-in-azure-spring-cloud"></a>Azure Spring Cloud 애플리케이션 시작, 중지 및 삭제

**이 문서는 다음에 적용됩니다.** ✔️ Java ✔️ C#

이 가이드에서는 Azure Portal 또는 Azure CLI를 사용하여 Azure Spring Cloud에서 애플리케이션의 상태를 변경하는 방법을 설명합니다.

## <a name="using-the-azure-portal"></a>Azure Portal 사용

애플리케이션을 배포한 후 Azure Portal을 사용하여 애플리케이션을 시작, 중지 및 삭제할 수 있습니다.

1. Azure Portal에서 Azure Spring Cloud 서비스 인스턴스로 이동합니다.
1. **애플리케이션 대시보드** 탭을 선택합니다.
1. 상태를 변경하려는 애플리케이션을 선택합니다.
1. 해당 애플리케이션에 대한 **개요** 페이지에서 **시작/중지**, **다시 시작**, 또는 **삭제** 를 선택합니다.

## <a name="using-the-azure-cli"></a>Azure CLI 사용

> [!NOTE]
> Azure CLI를 사용하여 선택적 매개 변수를 사용하고 기본값을 구성할 수 있습니다. Azure CLI에 대한 자세한 내용은 [참조 설명서](/cli/azure/spring-cloud)를 참조하세요.

먼저 다음과 같이 Azure CLI에 대한 Azure Spring Cloud 확장을 설치합니다.

```azurecli
az extension add --name spring-cloud
```

다음으로 아래 Azure CLI 작업 중 하나를 선택합니다.

* 애플리케이션을 시작하려면 다음을 수행합니다.

    ```azurecli
    az spring-cloud app start -n <application name> -g <resource group> -s <Azure Spring Cloud name>
    ```

* 애플리케이션을 중지하려면 다음을 수행합니다.

    ```azurecli
    az spring-cloud app stop -n <application name> -g <resource group> -s <Azure Spring Cloud name>
    ```

* 애플리케이션을 다시 시작하려면 다음을 수행합니다.

    ```azurecli
    az spring-cloud app restart -n <application name> -g <resource group> -s <Azure Spring Cloud name>
    ```

* 애플리케이션을 삭제하려면 다음을 수행합니다.

    ```azurecli
    az spring-cloud app delete -n <application name> -g <resource group> -s <Azure Spring Cloud name>
    ```
