---
title: Azure CLI를 통해 Apache Kafka for Confluent Cloud 만들기 - Azure 파트너 솔루션
description: 이 문서에서는 Azure CLI를 사용하여 Apache Kafka for Confluent Cloud 인스턴스를 만드는 방법을 설명합니다.
ms.service: partner-services
ms.topic: quickstart
ms.date: 06/07/2021
author: tfitzmac
ms.author: tomfitz
ms.custom: mode-api
ms.openlocfilehash: cd42839f3efd4764448dfbce7f04e017f6313f07
ms.sourcegitcommit: 56235f8694cc5f88db3afcc8c27ce769ecf455b0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/24/2021
ms.locfileid: "133071259"
---
# <a name="quickstart-get-started-with-apache-kafka-for-confluent-cloud---azure-cli"></a>빠른 시작: Apache Kafka for Confluent Cloud 시작 - Azure CLI

이 빠른 시작에서는 Azure Marketplace 및 Azure CLI를 사용하여 Apache Kafka for Confluent Cloud 인스턴스를 만듭니다.

## <a name="prerequisites"></a>사전 요구 사항

- Azure 계정. 활성 Azure 구독이 없는 경우 [체험 계정](https://azure.microsoft.com/free/)을 만듭니다.
- Azure 구독에 대한 _소유자_ 또는 _기여자_ 역할이 있어야 합니다. Azure와 Confluent 간의 통합은 _소유자_ 또는 _기여자_ 액세스 권한이 있는 사용자만 설정할 수 있습니다. 시작하기 전에 [적절한 액세스 권한이 있는지 확인합니다](../../role-based-access-control/check-access.md).

## <a name="find-offer"></a>제안 찾기

Azure Portal을 사용하여 Apache Kafka for Confluent Cloud 애플리케이션을 찾습니다.

1. 웹 브라우저에서 [Azure Portal](https://portal.azure.com/)로 이동한 후 로그인합니다.

1. 최근 세션에서 **Marketplace** 를 방문한 경우 사용 가능한 옵션에서 아이콘을 선택합니다. 그렇지 않으면 _Marketplace_ 를 검색합니다.

    :::image type="content" source="media/marketplace.png" alt-text="Marketplace 아이콘":::

1. **Marketplace** 페이지에서 원하는 계획 유형에 따라 두 가지 옵션이 제공됩니다. 종량제 요금제 또는 약정 요금제에 가입할 수 있습니다. 종량제는 공개적으로 사용할 수 있습니다. 약정 요금제는 프라이빗 제안에 대해 승인된 고객이 사용할 수 있습니다.

   - **종량제** 고객의 경우 _Apache Kafka on Confluent Cloud_ 를 검색합니다. Apache Kafka on Confluent Cloud에 대한 제안을 선택합니다.

     :::image type="content" source="media/search-pay-as-you-go.png" alt-text="Azure Marketplace 제안 검색":::

   - **약정** 고객의 경우 **프라이빗 제안 보기** 에 대한 링크를 선택합니다. 약정에 따라 최소 지출 금액에 가입해야 합니다. 이 옵션은 서비스가 오랫동안 필요한 경우에만 사용합니다.

     :::image type="content" source="media/view-private-offers.png" alt-text="프라이빗 제안 보기":::

     _Apache Kafka on Confluent Cloud_ 를 찾습니다.

     :::image type="content" source="media/select-from-private-offers.png" alt-text="프라이빗 제안 선택":::

## <a name="create-resource"></a>리소스 만들기

Apache Kafka on Confluent Cloud에 대한 제안을 선택하면 애플리케이션을 설정할 준비가 된 것입니다.

Azure CLI에 대한 환경 준비하는 것으로 시작합니다.

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../../includes/azure-cli-prepare-your-environment-no-header.md)]

로그인한 후 [az confluent organization create](/cli/azure/confluent/organization#az_confluent_organization_create) 명령을 사용하여 새 조직 리소스를 만듭니다.

```azurecli
az confluent organization create --name "myOrganization" --resource-group "myResourceGroup" \
 --location "my location" \ 
 --offer-detail id="string" plan-id="string" plan-name="string" publisher-id="string" term-unit="string" \ 
 --user-detail email-address="contoso@microsoft.com" first-name="string" last-name="string" \ 
 --tags Environment="Dev" 
```

> [!NOTE]
> 만들기 작업이 완료되기 전에 명령을 반환하려면 선택적 매개 변수 `--no-wait`를 추가합니다. Confluent 조직을 만들 때까지 작업은 계속 실행됩니다.
 
조직의 특정 이벤트 또는 조건이 발생할 때까지 CLI 실행을 일시 중지하려면 [az confluent organization wait](/cli/azure/confluent/organization#az_confluent_organization_wait) 명령을 사용합니다. 예를 들어 조직이 생성될 때까지 대기하려면 다음을 수행합니다.

```azurecli
az confluent organization wait --name "myOrganization" --resource-group "myResourceGroup" --created
```

기존 조직 목록을 보려면 [az confluent organization list](/cli/azure/confluent/organization#az_confluent_organization_list) 명령을 사용합니다.

구독에 있는 모든 조직을 볼 수 있습니다.

```azurecli
az confluent organization list
```

또는 리소스 그룹의 조직을 봅니다.

```azurecli
az confluent organization list --resource-group "myResourceGroup"
```

특정 조직의 속성을 보려면 [az confluent organization show](/cli/azure/confluent/organization#az_confluent_organization_show) 명령을 사용합니다.

다음과 같은 이름을 기준으로 조직을 볼 수 있습니다.

```azurecli
az confluent organization show --name "myOrganization" --resource-group "myResourceGroup"
```

또는 리소스 ID별로 조직을 봅니다.

```azurecli
az confluent organization show --ids "/subscriptions/{SubID}/resourceGroups/{myResourceGroup}/providers/Microsoft.Confluent/organizations/{myOrganization}"
```

오류가 발생하면 [Apache Kafka for Confluent Cloud 솔루션 문제 해결](troubleshoot.md)을 참조하세요.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [Confluent Cloud 리소스 관리](manage.md)
