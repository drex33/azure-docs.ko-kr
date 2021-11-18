---
title: RabbitMQ와 Service Bus 통합하는 방법
description: RabbitMQ와 Service Bus 통합하는 방법에 대한 단계별 가이드
author: videlalvaro
ms.topic: how-to
ms.date: 11/17/2021
ms.author: alvidela
ROBOTS: NOINDEX
ms.openlocfilehash: 62b73e5636378b8700dbf451013e5fd6f21c82c8
ms.sourcegitcommit: 1244a72dbec39ac8cf16bb1799d8c46bde749d47
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/18/2021
ms.locfileid: "132763566"
---
# <a name="how-to-integrate-service-bus-with-rabbitmq"></a>RabbitMQ와 Service Bus 통합하는 방법

이 가이드에서는 RabbitMQ에서 Service Bus 메시지를 보내는 방법을 알아봅니다.

다음은 해당 기능을 사용할 수 있는 몇 가지 시나리오입니다.

- **에지 설정**: RabbitMQ에 메시지를 보내는 에지 설정이 있지만, 추가 처리를 위해 해당 메시지를 [Azure Service Bus](./service-bus-messaging-overview.md)로 전달하려고 하므로, 많은 [Azure Big Data 기능](/azure/architecture/guide/architecture-styles/big-data)을 사용할 수 있습니다.
- **하이브리드 클라우드**: 회사에서 메시징 요구 사항을 처리하기 위해 RabbitMQ를 사용하는 타사를 인수했습니다. 각각 서로 다른 클라우드에 있습니다. Azure로 전환하는 동안 RabbitMQ를 Azure Service Bus와 브리징하여 이미 데이터 공유를 시작할 수 있습니다.
- **타사 통합**: 타사에서 RabbitMQ를 broker로 사용하고 당사에 데이터를 보내려고 하지만, 해당 회사는 당사 외부에 있습니다. 메시지를 전달할 수 있는 제한된 Azure Service Bus 큐 세트에 대한 액세스 권한을 부여하는 SAS 키를 제공할 수 있습니다.

목록은 계속되지만 RabbitMQ를 Azure에 브리징하여 대부분의 사용 사례를 해결할 수 있습니다.

먼저 [여기](https://azure.microsoft.com/free/)에서 가입하여 무료 Azure 계정을 만들어야 합니다.

계정에 로그인하고 나면 [Azure Portal](https://portal.azure.com/)로 이동하여 새 Azure Service Bus [네임스페이스](./service-bus-create-namespace-portal.md)를 만듭니다. 네임스페이스는 큐와 토픽과 같이 메시징 구성 요소가 위치할 범위 지정 컨테이너입니다.

## <a name="adding-a-new-azure-service-bus-namespace"></a>새 Azure Service Bus 네임스페이스 추가

Azure Portal에서 큰 더하기 단추를 클릭하여 새 리소스를 추가합니다.

:::image type="content" source="./media/service-bus-integrate-with-rabbitmq/create-resource.png" alt-text="리소스 만들기":::

그런 다음 통합을 선택하고 Azure Service Bus를 클릭하여 메시징 네임스페이스를 만듭니다.

:::image type="content" source="./media/service-bus-integrate-with-rabbitmq/integration.png" alt-text="Azure Service Bus 선택":::

네임스페이스 정보를 입력하라는 메시지가 표시됩니다. 사용할 Azure 구독을 선택합니다. [리소스 그룹](../azure-resource-manager/management/manage-resource-groups-portal.md)이 없으면 새 리소스 그룹을 만들 수 있습니다.

:::image type="content" source="./media/service-bus-integrate-with-rabbitmq/create-namespace.png" alt-text="네임스페이스 만들기":::

`Namespace name`에 `rabbitmq`를 사용하지만 원하는 대로 사용할 수 있습니다. 그런 다음 위치로 `East US`를 설정합니다. `Basic`을 가격 책정 계층으로 선택합니다.

모두 제대로 수행되면 다음 확인 화면이 표시됩니다.

:::image type="content" source="./media/service-bus-integrate-with-rabbitmq/create-namespace-confirm.png" alt-text="네임스페이스 만들기 확인":::

그런 다음 Azure Portal 돌아가면 새 `rabbitmq` 네임스페이스 목록이 표시됩니다. 큐를 추가할 수 있도록 리소스를 클릭하여 액세스합니다.

:::image type="content" source="./media/service-bus-integrate-with-rabbitmq/resource-view-with-namespace.png" alt-text="새 네임스페이스가 포함된 리소스 목록":::

## <a name="creating-our-azure-service-bus-queue"></a>Azure Service Bus 큐 만들기

이제 Azure Service Bus 네임스페이스가 있으므로 왼쪽의 `Entities` 아래에 있는 `Queues` 단추를 클릭하여 새 큐를 추가할 수 있습니다.

:::image type="content" source="./media/service-bus-integrate-with-rabbitmq/create-queue.png" alt-text="큐 만들기":::

큐의 이름은 메시지의 출처를 알려주는 `from-rabbitmq`입니다. 다른 모든 옵션은 기본값으로 둘 수 있지만, 앱의 요구 사항에 맞게 변경할 수 있습니다.

## <a name="enabling-the-rabbitmq-shovel-plugin"></a>RabbitMQ Shovel 플러그 인 사용

RabbitMQ에서 Azure Service Bus로 메시지를 전달하기 위해 RabbitMQ와 함께 제공되는 [Shovel 플러그 인](https://www.rabbitmq.com/shovel.html)을 사용합니다. 다음 명령을 사용하여 플러그 인과 시각적 인터페이스를 사용으로 설정할 수 있습니다.

```bash
rabbitmq-plugins enable rabbitmq_shovel_management
```

>루트로 해당 명령을 실행해야 할 수도 있습니다.

이제 RabbitMQ를 Azure에 연결하는 데 필요한 자격 증명을 가져와야 합니다.

## <a name="connecting-rabbitmq-to-azure-service-bus"></a>Azure Service Bus에 RabbitMQ 연결

RabbitMQ가 메시지를 게시할 수 있도록 큐의 [공유 액세스 정책](../storage/common/storage-sas-overview.md)(SAS)을 만들어야 합니다. SAS Policy를 사용하면 리소스로 수행할 수 있는 외부 당사자를 지정할 수 있습니다. RabbitMQ는 메시지를 보낼 수 있지만, 큐를 수신 대기하거나 관리하지는 않습니다.

:::image type="content" source="./media/service-bus-integrate-with-rabbitmq/create-sas-policy.png" alt-text="SAS Policy 추가":::

`Send` 상자를 선택한 다음 `Create`를 클릭하여 SAS Policy를 적용합니다.

정책을 만든 다음 클릭하여 **기본 연결 문자열** 을 확인합니다. RabbitMQ가 Azure Service Bus와 통신하는 데 사용합니다.

:::image type="content" source="./media/service-bus-integrate-with-rabbitmq/sas-policy-key.png" alt-text="SAS Policy 가져오기":::

이 연결 문자열을 사용하려면 먼저 RabbitMQ의 AMQP 연결 형식으로 변환해야 합니다. [연결 문자열 변환기 도구](https://red-mushroom-0f7446a0f.azurestaticapps.net/)로 이동하여 양식에 연결 문자열을 붙여넣은 다음, 변환을 클릭합니다. RabbitMQ가 준비된 연결 문자열을 얻을 수 있습니다. (해당 웹 사이트는 모두 브라우저에서 로컬로 실행하므로 데이터가 유선으로 전송되지 않습니다.) [GitHub](https://github.com/videlalvaro/connstring_to_amqp)의 소스 코드에 액세스할 수 있습니다.

:::image type="content" source="./media/service-bus-integrate-with-rabbitmq/converter.png" alt-text="연결 문자열 변환":::

이제 브라우저 `http://localhost:15672/#/dynamic-shovels`에서 RabbitMQ 관리 플러그 인을 열고 `Admin -> Shovel Management`로 이동하여 RabbitMQ 큐에서 Azure Service Bus 큐로 메시지를 보내는 작업을 처리할 새 shovel을 추가할 수 있습니다.

:::image type="content" source="./media/service-bus-integrate-with-rabbitmq/add-shovel.png" alt-text="RabbitMQ Shovel 추가":::

여기에서 Shovel `azure`를 호출하고 `AMQP 0.9.1`을 원본 프로토콜로 선택합니다. 스크린샷에는 로컬 RabbitMQ 서버에 연결하는 기본 URI인 `amqp://`가 있습니다. 현재 배포에 맞게 조정해야 합니다.

큐 쪽에서 `azure`를 큐의 이름으로 사용할 수 있습니다. 해당 큐가 없으면 RabbitMQ가 큐를 만듭니다. 이미 존재하는 큐의 이름을 선택할 수도 있습니다. 다른 옵션은 기본값으로 둡니다.

그런 다음 `destination` 쪽에서 프로토콜로 `AMQP 1.0`을 선택합니다. `URI` 필드에 Azure 연결 문자열을 RabbitMQ 형식으로 변환한 이전 단계에서 얻은 연결 문자열을 입력합니다. 다음과 같이 표시됩니다.

```
amqps://rabbitmq-shovel:StringOfRandomChars@rabbitmq.servicebus.windows.net:5671/?sasl=plain
```

`Address` 필드에 **Azure Service Bus Queue** 의 이름을 입력합니다. 이 경우 이름은 `from-rabbitmq`입니다. `Add Shovel`을 클릭하면 설정이 메시지 수신을 시작할 준비가 됩니다.

## <a name="publishing-messages-from-rabbitmq-to-azure-service-bus"></a>RabbitMQ에서 Azure Service Bus로 메시지 게시

RabbitMQ 관리 인터페이스에서 `Queues`로 이동하여 `azure` 큐를 선택하고 `Publish message` 패널을 검색합니다. 메시지를 큐에 직접 게시할 수 있는 양식이 표시됩니다. 이 예에서는 `fist message`를 `Payload`로 추가하고 `Publish Message`를 누릅니다.

:::image type="content" source="./media/service-bus-integrate-with-rabbitmq/first-message.png" alt-text="첫 번째 메시지 게시":::

Azure로 돌아가서 큐를 검사합니다. 왼쪽 패널에서 `Service Bus Explorer`를 클릭한 다음 _피킹_ 단추를 클릭합니다. 모든 작업이 제대로 수행되면 큐에 하나의 메시지가 표시됩니다. 축하합니다!

:::image type="content" source="./media/service-bus-integrate-with-rabbitmq/service-bus-queue.png" alt-text="Azure Service Bus 큐":::

하지만 메시지가 RabbitMQ에서 보낸 메시지인지 확인해 보겠습니다. `Peek` 탭을 선택하고 `Peek` 단추를 클릭하여 큐의 마지막 메시지를 검색합니다. 메시지를 클릭하여 콘텐츠를 검사합니다. `first message`가 나열된 아래 이미지와 같은 내용이 표시됩니다.

:::image type="content" source="./media/service-bus-integrate-with-rabbitmq/peek.png" alt-text="큐 피킹":::

## <a name="lets-recap"></a>요약해 보겠습니다.

축하합니다! 많은 작업을 이행했습니다. RabbitMQ에서 Azure Service Bus로 메시지를 가져왔습니다. 단계를 요약해 보겠습니다.

1. Azure Service Bus 네임스페이스 만들기
2. 네임스페이스에 큐 추가
3. 큐에 SAS Policy 추가
4. 큐 연결 문자열 가져오기
5. RabbitMQ shovel 플러그 인과 관리 인터페이스 사용
6. Azure Service Bus 연결 문자열을 RabbitMQ의 AMQP 형식으로 변환
7. RabbitMQ에 새 Shovel을 추가하여 Azure Service Bus에 연결
8. 메시지 게시

이전 단계를 따라 Azure 외부의 조직 영역을 통합했습니다. Shovel 플러그 인을 사용하여 RabbitMQ에서 Azure Service Bus로 메시지를 전송할 수 있습니다. 이제 신뢰할 수 있는 타사에서 앱을 Azure 배포와 연결하도록 허용할 수 있으므로 엄청난 이점이 있습니다.

결국 메시징은 연결을 사용으로 설정하는 데 관한 것이며 이 기술을 사용하여 방금 새 연결을 시작했습니다.

## <a name="next-steps"></a>다음 단계

- [Azure Service Bus](./service-bus-messaging-overview.md)에 대해 자세히 알아보세요.
- [Service Bus의 AMQP 1.0 지원](./service-bus-amqp-overview.md)에 관한 자세한 정보
