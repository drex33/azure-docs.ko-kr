---
title: Azure Spring Cloud 서비스 인스턴스를 시작하거나 중지하는 방법
description: Azure Spring Cloud 서비스 인스턴스를 시작하거나 중지하는 방법을 설명합니다.
author: karlerickson
ms.author: wepa
ms.service: spring-cloud
ms.topic: how-to
ms.date: 11/04/2021
ms.custom: devx-track-java
ms.openlocfilehash: 010c117218edccd31fb397785358a35c1774754a
ms.sourcegitcommit: 4cd97e7c960f34cb3f248a0f384956174cdaf19f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/08/2021
ms.locfileid: "132027492"
---
# <a name="start-or-stop-your-azure-spring-cloud-service-instance"></a>Azure Spring Cloud 서비스 인스턴스 시작 또는 중지

이 문서에서는 Azure Spring Cloud 서비스 인스턴스를 시작하거나 중지하는 방법을 보여 줍니다.

> [!NOTE]
> 중지 및 시작은 현재 미리 보기로 제공됩니다.

Azure Spring Cloud 실행 중인 애플리케이션은 지속적으로 실행하지 않아도 될 수 있습니다(예: 업무 시간 중에만 사용되는 서비스 인스턴스가 있는 경우). 이 경우 Azure Spring Cloud 유휴 상태이며 시스템 구성 요소만 실행될 수 있습니다.

실행 중인 인스턴스를 줄이고 컴퓨팅 리소스에 대한 비용을 줄여 Azure Spring Cloud 활성 공간을 줄일 수 있습니다.

비용을 더 줄이기 위해 Azure Spring Cloud 서비스 인스턴스를 완전히 중지할 수 있습니다. 모든 사용자 앱 및 시스템 구성 요소가 중지됩니다. 그러나 서비스 인스턴스를 다시 시작하고 중단한 위치를 바로 선택할 수 있도록 모든 개체 및 네트워크 설정이 저장됩니다.

> [!NOTE]
> 중지된 Azure Spring Cloud 서비스 인스턴스의 상태는 미리 보기 동안 최대 90일 동안 유지됩니다. 클러스터가 90일 넘게 중지된 경우 클러스터 상태를 복구할 수 없습니다.
> 미리 보기 후 최대 중지 시간이 변경 될 수 있습니다.

중지된 Azure Spring Cloud 서비스 인스턴스만 시작, 보기 또는 삭제할 수 있습니다. 앱 만들기 또는 크기 조정과 같은 업데이트 작업을 수행하기 전에 서비스 인스턴스를 시작해야 합니다.

## <a name="prerequisites"></a>사전 요구 사항

- Azure Spring Cloud 기존 서비스 인스턴스입니다. 새 서비스 인스턴스를 만들려면 [빠른 시작: Azure Spring Cloud 첫 번째 애플리케이션 배포를 참조하세요.](./quickstart.md)
- (선택 사항) [Azure CLI](/cli/azure/install-azure-cli) 버전 2.11.2 이상

# <a name="portal"></a>[포털](#tab/azure-portal)

## <a name="stop-a-running-instance"></a>실행 중인 인스턴스 중지

Azure Portal 다음 단계를 사용하여 실행 중인 Azure Spring Cloud 인스턴스를 중지합니다.

1. Azure Spring Cloud 서비스 개요 페이지로 이동합니다.
2. **중지를** 선택하여 실행 중인 인스턴스를 중지합니다.

   :::image type="content" source="media/stop-start-service/spring-cloud-stop-service.png" alt-text="중지 단추와 상태 값이 강조 표시된 Azure Spring Cloud 개요 페이지를 보여주는 Azure Portal 스크린샷":::

3. 인스턴스가 중지되면 상태가 **성공(중지)으로** 표시됩니다.

## <a name="start-a-stopped-instance"></a>중지된 인스턴스 시작

Azure Portal 다음 단계를 사용하여 중지된 Azure Spring Cloud 인스턴스를 시작합니다.

1. Azure Spring Cloud 서비스 개요 페이지로 이동합니다.
2. **시작을** 선택하여 중지된 인스턴스를 시작합니다.

   :::image type="content" source="media/stop-start-service/spring-cloud-start-service.png" alt-text="시작 단추와 상태 값이 강조 표시된 Azure Spring Cloud 개요 페이지를 보여주는 Azure Portal 스크린샷":::

3. 인스턴스가 시작되면 상태가 **성공(실행 중)으로** 표시됩니다.

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

## <a name="stop-a-running-instance"></a>실행 중인 인스턴스 중지

Azure CLI 다음 명령을 사용하여 실행 중인 Azure Spring Cloud 인스턴스를 중지합니다.

```azurecli
az spring-cloud stop \
    --name <service-instance-name> \
    --resource-group <resource-group-name>
```

## <a name="start-a-stopped-instance"></a>중지된 인스턴스 시작

Azure CLI 다음 명령을 사용하여 중지된 Azure Spring Cloud 인스턴스를 시작합니다.

```azurecli
az spring-cloud start \
    --name <service-instance-name> \
    --resource-group <resource-group-name>
```

## <a name="check-the-power-state"></a>전원 상태 확인

인스턴스가 중지되거나 시작된 후 다음 명령을 사용하여 전원 상태를 확인합니다.

```azurecli
az spring-cloud show \
    --name <service-instance-name> \
    --resource-group <resource-group-name>
```

---

## <a name="next-steps"></a>다음 단계

* [Azure Activity 로그 및 Azure Service Health를 사용하여 앱 수명 주기 이벤트 모니터링](./monitor-app-lifecycle-events.md)
* [Azure Monitor 사용량 및 예상 비용 모니터링](../azure-monitor/usage-estimated-costs.md)
