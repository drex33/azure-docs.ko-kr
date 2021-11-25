---
title: 빠른 시작 - Azure CLI를 사용하여 Azure 알림 허브 만들기 | Microsoft Docs
description: 이 자습서에서는 Azure CLI를 사용하여 Azure 알림 허브를 만드는 방법을 알아봅니다.
services: notification-hubs
author: dbradish-microsoft
manager: barbkess
editor: sethmanheim
ms.service: notification-hubs
ms.devlang: azurecli
ms.workload: mobile
ms.topic: quickstart
ms.date: 05/27/2020
ms.author: dbradish
ms.reviewer: thsomasu
ms.lastreviewed: 03/18/2020
ms.custom: devx-track-azurecli, mode-api
ms.openlocfilehash: b68136633fd4e5110ef6e00fc9c02393525b2661
ms.sourcegitcommit: 56235f8694cc5f88db3afcc8c27ce769ecf455b0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/24/2021
ms.locfileid: "133045751"
---
# <a name="quickstart-create-an-azure-notification-hub-using-the-azure-cli"></a>Azure CLI를 사용하여 Azure 알림 허브 만들기

Azure Notification Hubs는 모든 백 엔드(클라우드 또는 온-프레미스)에서 모든 플랫폼(iOS, Android, Windows, Kindle, Baidu 등)에 알림을 보낼 수 있도록 하는 사용하기 쉬운 스케일 아웃 푸시 엔진을 제공합니다. 서비스에 대한 자세한 내용은 [Azure Notification Hubs란?](notification-hubs-push-notification-overview.md)을 참조하세요.

이 빠른 시작에서는 Azure CLI를 사용하여 알림 허브를 만듭니다. 첫 번째 섹션에서는 알림 허브 네임스페이스를 만드는 단계를 안내합니다. 두 번째 섹션에서는 기존 네임스페이스에서 알림 허브를 만드는 단계를 안내합니다. 사용자 지정 액세스 정책을 만드는 방법도 알아봅니다.

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

> [!IMPORTANT]
> Notification Hubs를 사용하려면 Azure CLI 2.0.67 이상 버전이 필요합니다. [az version](/cli/azure/reference-index#az_version)을 실행하여 설치된 버전과 종속 라이브러리를 찾습니다. 최신 버전으로 업그레이드하려면 [az upgrade](/cli/azure/reference-index#az_upgrade)를 실행합니다.

## <a name="create-a-resource-group"></a>리소스 그룹 만들기

모든 Azure 리소스와 마찬가지로 Azure Notification Hubs는 리소스 그룹에 배포해야 합니다.  리소스 그룹을 사용하면 관련 Azure 리소스를 구성하고 관리할 수 있습니다.  리소스 그룹에 대한 자세한 내용은 [Azure Resource Manager란?](../azure-resource-manager/management/overview.md)을 참조하세요.

이 빠른 시작에서는 다음과 같이 [az group create](/cli/azure/group#az_group_create) 명령을 사용하여 **eastus** 위치에 **spnhubrg** 라는 리소스 그룹을 만듭니다.

```azurecli
az group create --name spnhubrg --location eastus
```

## <a name="create-a-notification-hubs-namespace"></a>Notification Hubs 네임스페이스 만들기

1. 알림 허브에 대한 네임스페이스를 만듭니다.

   네임스페이스는 하나 이상의 허브를 포함하며, 이름은 모든 Azure 구독에서 고유해야 하고, 길이가 6자 이상이어야 합니다. 이름의 가용성을 확인하려면 [az notification-hub namespace check-availability](/cli/azure/notification-hub/namespace#az_notification_hub_namespace_check-availability) 명령을 사용합니다.

   ```azurecli
   az notification-hub namespace check-availability --name spnhubns
   ```

   Azure CLI는 다음 콘솔 출력을 표시하여 가용성에 대한 요청에 응답합니다.

   ```shell
   {
   "id": "/subscriptions/yourSubscriptionID/providers/Microsoft.NotificationHubs/checkNamespaceAvailability",
   "isAvailable": true,
   "location": null,
   "name": "spnhubns",
   "properties": false,
   "sku": null,
   "tags": null,
   "type": "Microsoft.NotificationHubs/namespaces/checkNamespaceAvailability"
   }
   ```

   Azure CLI 응답의 두 번째 줄(`"isAvailable": true`)을 확인하세요. 사용자가 네임스페이스에 지정하기를 원하는 이름이 없는 경우 이 줄은 `false`가 됩니다. 이름의 사용 가능 여부를 확인한 후에는 [az notification-hub namespace create](/cli/azure/notification-hub/namespace#az_notification_hub_namespace_create) 명령을 실행하여 네임스페이스를 만듭니다.  

   ```azurecli
   az notification-hub namespace create --resource-group spnhubrg --name spnhubns  --location eastus --sku Free
   ```

   `az notification-hub namespace create` 명령에 지정한 `--name`이 사용 불가하거나 [Azure 리소스에 대한 명명 규칙 및 제한](../azure-resource-manager/management/resource-name-rules.md)을 충족하지 않는 경우 Azure CLI는 다음 콘솔 출력으로 응답합니다.

   ```shell
   #the name is not available
   The specified name is not available. For more information visit https://aka.ms/eventhubsarmexceptions.

   #the name is invalid
   The specified service namespace is invalid.
   ```

   처음에 시도한 이름이 실패할 경우 새 네임스페이스에 다른 이름을 선택하고 `az notification-hub namespace create` 명령을 다시 실행합니다.

   > [!NOTE]
   > 이 단계부터 이 빠른 시작에서 복사하는 각 Azure CLI 명령의 `--namespace` 매개 변수 값을 바꾸어야 합니다.

2. 네임스페이스의 목록을 가져옵니다.

   새 네임스페이스에 대한 세부 정보를 보려면 [az notification-hub namespace list](/cli/azure/notification-hub/namespace#az_notification_hub_namespace_list) 명령을 사용합니다. 구독에 대한 모든 네임스페이스를 확인하려는 경우 `--resource-group` 매개 변수는 선택 사항입니다.

   ```azurecli
   az notification-hub namespace list --resource-group spnhubrg
   ```

## <a name="create-notification-hubs"></a>알림 허브 만들기

1. 첫 번째 알림 허브를 만듭니다.

   이제 새 네임스페이스에서 하나 이상의 알림 허브를 만들 수 있습니다. [az notification-hub create](/cli/azure/notification-hub#az_notification_hub_create) 명령을 실행하여 알림 허브를 만듭니다.

   ```azurecli
   az notification-hub create --resource-group spnhubrg --namespace-name spnhubns --name spfcmtutorial1nhub --location eastus --sku Free
   ```

2. 두 번째 알림 허브를 만듭니다.

   단일 네임스페이스에 여러 알림 허브를 만들 수 있습니다. 같은 네임스페이스에 두 번째 알림 허브를 만들려면 다른 허브 이름을 사용하여 `az notification-hub create` 명령을 다시 실행합니다.

   ```azurecli
   az notification-hub create --resource-group spnhubrg --namespace-name spnhubns --name mysecondnhub --location eastus --sku Free
   ```

3. 알림 허브 목록을 가져옵니다.

   Azure CLI는 각 명령이 실행될 때마다 성공 또는 오류 메시지를 반환합니다. 그러나 알림 허브 목록을 쿼리할 수 있으면 안심할 수 있습니다. [az notification-hub list](/cli/azure/notification-hub#az_notification_hub_list) 명령은 이 목적을 위해 설계되었습니다.

   ```azurecli
   az notification-hub list --resource-group spnhubrg --namespace-name spnhubns --output table
   ```

## <a name="work-with-access-policies"></a>액세스 정책 작업

1. Azure Notification Hubs는 액세스 정책을 통해 [공유 액세스 서명 보안](./notification-hubs-push-notification-security.md)을 사용합니다. 알림 허브를 만들면 두 가지 정책이 자동으로 생성됩니다. 이러한 정책의 연결 문자열은 푸시 알림을 구성하는 데 필요합니다. [az notification-hub authorization-rule list](/cli/azure/notification-hub/authorization-rule#az_notification_hub_authorization-rule-list) 명령은 정책 이름 및 해당 리소스 그룹의 목록을 제공합니다.

   ```azurecli
   az notification-hub authorization-rule list --resource-group spnhubrg --namespace-name spnhubns --notification-hub-name spfcmtutorial1nhub --output table
   ```

   > [!IMPORTANT]
   > 애플리케이션에서 _DefaultFullSharedAccessSignature_ 정책을 사용하지 마세요. 이 정책은 백 엔드에서만 사용할 수 있습니다. 클라이언트 애플리케이션에서는 `Listen` 액세스 정책만 사용하세요.

2. 의미 있는 이름을 사용하여 추가 권한 부여 규칙을 만들려는 경우 [az notification-hub authorization-rule create](/cli/azure/notification-hub/authorization-rule#az_notification_hub_authorization_rule_create) 명령을 사용하여 사용자 고유의 액세스 정책을 만들고 사용자 지정할 수 있습니다. `--rights` 매개 변수는 할당하려는 권한의 공백으로 구분된 목록입니다.

   ```azurecli
   az notification-hub authorization-rule create --resource-group spnhubrg --namespace-name spnhubns --notification-hub-name spfcmtutorial1nhub --name spnhub1key --rights Listen Manage Send
   ```

3. 액세스 정책마다 두 가지 키 및 연결 문자열 세트가 있습니다. 나중에 [알림 허브를 구성](./configure-notification-hub-portal-pns-settings.md)하는 데 필요합니다. 알림 허브 액세스 정책에 대한 키 및 연결 문자열을 나열하려면 [az notification-hub authorization-rule list-keys](/cli/azure/notification-hub/authorization-rule#az_notification_hub_authorization_rule_list_keys) 명령을 사용합니다.

   ```azurecli
   # query the keys and connection strings for DefaultListenSharedAccessSignature
   az notification-hub authorization-rule list-keys --resource-group spnhubrg --namespace-name spnhubns --notification-hub-name spfcmtutorial1nhub --name DefaultListenSharedAccessSignature --output table
   ```

   ```azurecli
   # query the keys and connection strings for a custom policy
   az notification-hub authorization-rule list-keys --resource-group spnhubrg --namespace-name spnhubns --notification-hub-name spfcmtutorial1nhub --name spnhub1key --output table
   ```

   > [!NOTE]
   > [알림 허브 네임스페이스](/cli/azure/notification-hub/namespace/authorization-rule#az_notification_hub_namespace_authorization_rule_list_keys) 및 [알림 허브](/cli/azure/notification-hub/authorization-rule#az_notification_hub_authorization_rule_list_keys)는 별도의 액세스 정책을 사용합니다. 키 및 연결 문자열을 쿼리할 때 올바른 Azure CLI 참조를 사용하고 있는지 확인해야 합니다.

## <a name="clean-up-resources"></a>리소스 정리

더 이상 필요하지 않은 경우 [az group delete](/cli/azure/group) 명령을 사용하여 리소스 그룹 및 모든 관련 리소스를 제거할 수 있습니다.

```azurecli
az group delete --name spnhubrg
```

## <a name="next-steps"></a>다음 단계

* 이 빠른 시작에서는 알림 허브를 만들었습니다. PNS(플랫폼 알림 시스템) 설정을 사용하여 허브를 구성하는 방법을 알아보려면 [알림 허브에 푸시 알림 설정](configure-notification-hub-portal-pns-settings.md)을 참조하세요.

* Azure CLI를 사용하여 알림 허브를 관리하기 위한 광범위한 기능을 검색합니다.

  [Notification Hubs 전체 참조 목록](/cli/azure/notification-hub)

  [Notification Hubs 네임스페이스 참조 목록](/cli/azure/notification-hub/namespace)

  [Notification Hubs 권한 부여 규칙 참조 목록](/cli/azure/notification-hub/authorization-rule)

  [Notification Hubs 자격 증명 참조 목록](/cli/azure/notification-hub/credential)
