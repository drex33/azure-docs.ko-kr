---
title: Azure 스프링 클라우드 서비스 인스턴스를 시작 또는 중지 하는 방법
description: Azure 스프링 클라우드 서비스 인스턴스를 시작 또는 중지 하는 방법을 설명 합니다.
author: karlerickson
ms.author: wepa
ms.service: spring-cloud
ms.topic: how-to
ms.date: 11/04/2021
ms.custom: devx-track-java
ms.openlocfilehash: 7cd9ca0eba60500d38a24460afa9bc1b766259a0
ms.sourcegitcommit: 1a0fe16ad7befc51c6a8dc5ea1fe9987f33611a1
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/05/2021
ms.locfileid: "131867384"
---
# <a name="start-or-stop-your-azure-spring-cloud-service-instance"></a>Azure 스프링 클라우드 서비스 인스턴스 시작 또는 중지

이 문서에서는 Azure 스프링 클라우드 서비스 인스턴스를 시작 하거나 중지 하는 방법을 보여 줍니다.

> [!NOTE]
> 중지 및 시작은 현재 미리 보기 상태입니다.

업무 시간 중에만 사용 되는 서비스 인스턴스를 사용 하는 경우 처럼 Azure 스프링 클라우드에서 실행 중인 응용 프로그램을 지속적으로 실행할 필요가 없을 수도 있습니다. 이러한 시간에 Azure 스프링 클라우드는 유휴 상태이 고 시스템 구성 요소만 실행할 수 있습니다.

실행 중인 인스턴스를 줄이고 계산 리소스에 대 한 비용이 절감 되도록 하 여 Azure 스프링 클라우드의 활성 공간을 줄일 수 있습니다.

비용을 줄이기 위해 Azure 스프링 클라우드 서비스 인스턴스를 완전히 중지할 수 있습니다. 모든 사용자 앱 및 시스템 구성 요소가 중지 됩니다. 그러나 서비스 인스턴스를 다시 시작 하 고 중단 한 위치에서 바로 선택할 수 있도록 모든 개체 및 네트워크 설정이 저장 됩니다.

> [!NOTE]
> 중지 된 Azure 스프링 클라우드 서비스 인스턴스의 상태는 미리 보기 중 최대 90 일 동안 보존 됩니다. 클러스터가 90 일 넘게 중지 된 경우 클러스터 상태를 복구할 수 없습니다.
> 최대 중지 시간은 미리 보기 후에 변경 될 수 있습니다.

중지 된 Azure 스프링 클라우드 서비스 인스턴스만 시작, 확인 또는 삭제할 수 있습니다. 앱을 만들거나 크기를 조정 하는 등의 업데이트 작업을 수행 하기 전에 서비스 인스턴스를 시작 해야 합니다.

## <a name="prerequisites"></a>필수 구성 요소

- Azure 스프링 클라우드의 기존 서비스 인스턴스. 새 서비스 인스턴스를 만들려면 [빠른 시작: Azure 스프링 클라우드의 첫 번째 응용 프로그램 배포](./quickstart.md)를 참조 하세요.
- 필드 2.11.2 이상 버전을 [Azure CLI](/cli/azure/install-azure-cli) 합니다.

# <a name="portal"></a>[포털](#tab/azure-portal)

## <a name="stop-a-running-instance"></a>실행 중인 인스턴스 중지

Azure Portal에서 다음 단계를 사용 하 여 실행 중인 Azure 스프링 클라우드 인스턴스를 중지 합니다.

1. Azure 스프링 클라우드 서비스 개요 페이지로 이동 합니다.
2. **중지** 를 선택 하 여 실행 중인 인스턴스를 중지 합니다.

:::image type="content" source="media/stop-start-service/spring-cloud-stop-service.png" alt-text="중지 단추와 상태 값이 강조 표시 된 Azure 스프링 클라우드 개요 페이지를 보여 주는 Azure Portal 스크린샷":::

3. 인스턴스가 중지 된 후 상태가 **성공 (중지 됨)** 으로 표시 됩니다.

## <a name="start-a-stopped-instance"></a>중지 된 인스턴스 시작

Azure Portal에서 다음 단계를 사용 하 여 중지 된 Azure 스프링 클라우드 인스턴스를 시작 합니다.

1. Azure 스프링 클라우드 서비스 개요 페이지로 이동 합니다.
2. **시작** 을 선택 하 여 중지 된 인스턴스를 시작 합니다.

:::image type="content" source="media/stop-start-service/spring-cloud-start-service.png" alt-text="시작 단추와 상태 값이 강조 표시 된 Azure 스프링 클라우드 개요 페이지를 보여 주는 Azure Portal 스크린샷":::

3. 인스턴스가 시작 된 후 상태가 **성공 (실행 중)** 으로 표시 됩니다.

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

## <a name="stop-a-running-instance"></a>실행 중인 인스턴스 중지

Azure CLI를 사용 하 여 다음 명령을 사용 하 여 실행 중인 Azure 스프링 클라우드 인스턴스를 중지 합니다.

```azurecli
az spring-cloud stop \
    --name <service-instance-name> \
    --resource-group <resource-group-name>
```

## <a name="start-a-stopped-instance"></a>중지 된 인스턴스 시작

Azure CLI에서 다음 명령을 사용 하 여 중지 된 Azure 스프링 클라우드 인스턴스를 시작 합니다.

```azurecli
az spring-cloud start \
    --name <service-instance-name> \
    --resource-group <resource-group-name>
```

## <a name="check-the-power-state"></a>전원 상태를 확인 합니다.

인스턴스를 중지 하거나 시작한 후에는 다음 명령을 사용 하 여 전원 상태를 확인 합니다.

```azurecli
az spring-cloud show \
    --name <service-instance-name> \
    --resource-group <resource-group-name>
```

---

## <a name="next-steps"></a>다음 단계

* [Azure Activity 로그 및 Azure Service Health를 사용하여 앱 수명 주기 이벤트 모니터링](./monitor-app-lifecycle-events.md)
* [Azure Monitor의 사용량 및 예상 비용 모니터링](../azure-monitor/usage-estimated-costs.md)
