---
title: Azure CLI를 사용하여 Azure Blockchain Service 관리
description: Azure CLI를 사용하여 Azure Blockchain Service를 관리하는 방법
ms.date: 07/23/2020
ms.topic: how-to
ms.reviewer: ravastra
ms.openlocfilehash: 6920458795a84e44c19164d1342de1b9a33052d0
ms.sourcegitcommit: 32ee8da1440a2d81c49ff25c5922f786e85109b4
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/12/2021
ms.locfileid: "122642101"
---
# <a name="manage-azure-blockchain-service-using-azure-cli"></a>Azure CLI를 사용하여 Azure Blockchain Service 관리

Azure Portal 외에도 Azure CLI를 사용하여 Azure Blockchain 서비스의 블록체인 멤버 및 트랜잭션 노드를 관리할 수 있습니다.

[!INCLUDE [Retirement note](./includes/retirement.md)]
## <a name="launch-azure-cloud-shell"></a>Azure Cloud Shell 시작

Azure Cloud Shell은 이 항목의 단계를 실행하는 데 무료로 사용할 수 있는 대화형 셸입니다. 공용 Azure 도구가 사전 설치되어 계정에서 사용하도록 구성되어 있습니다.

Cloud Shell을 열려면 코드 블록의 오른쪽 위 모서리에 있는 **사용해 보세요** 를 선택하기만 하면 됩니다. 또한 [https://shell.azure.com/bash](https://shell.azure.com/bash)로 이동하여 별도의 브라우저 탭에서 Cloud Shell을 시작할 수도 있습니다. **복사** 를 선택하여 코드 블록을 복사하여 Cloud Shell에 붙여넣고, Enter 키를 눌러 실행합니다.

CLI를 로컬에서 설치하고 사용하는 것을 선호하는 경우 [Azure CLI 설치](/cli/azure/install-azure-cli)를 참조하세요.

## <a name="prepare-your-environment"></a>환경 준비

1. 로그인합니다.

    로컬에 설치된 CLI를 사용하는 경우 [az login](/cli/azure/reference-index#az_login) 명령을 사용하여 로그인합니다.

    ```azurecli
    az login
    ```

    터미널에 표시된 단계에 따라 인증 프로세스를 완료합니다.

1. Azure CLI 확장을 설치합니다.

    Azure CLI에 대한 확장 참조를 사용하는 경우 먼저 확장을 설치해야 합니다.  Azure CLI 확장은 아직 핵심 CLI의 일부로 제공되지 않는 실험적 명령과 시험판 명령에 대한 액세스를 제공합니다.  확장 업데이트 및 제거를 포함하여 확장에 대해 자세한 내용을 보려면 [Azure CLI에서 확장 사용](/cli/azure/azure-cli-extensions-overview)을 참조하세요.

    다음 명령을 실행하여 [Azure Blockchain Service](/cli/azure/blockchain)를 설치합니다.

    ```azurecli-interactive
    az extension add --name blockchain
    ```

## <a name="create-blockchain-member"></a>블록체인 멤버 만들기

예제는 새 컨소시엄에서 Quorum 원장 프로토콜을 실행하는  Azure Blockchain Service의 [블록체인 멤버를 만듭니다](/cli/azure/blockchain/member#az_blockchain_member_create).

```azurecli
az blockchain member create \
                            --resource-group <myResourceGroup> \
                            --name <myMemberName> \
                            --location <myBlockchainLocation> \
                            --password <strongMemberAccountPassword> \
                            --protocol "Quorum" \
                            --consortium <myConsortiumName> \
                            --consortium-management-account-password <strongConsortiumManagementPassword> \
                            --sku <skuName>
```

| 매개 변수 | Description |
|---------|-------------|
| **resource-group** | Azure Blockchain Service 리소스가 만들어지는 리소스 그룹 이름입니다. |
| **name** | Azure Blockchain Service 블록체인 멤버를 식별하는 고유한 이름입니다. 이 이름은 공용 엔드포인트 주소에 사용됩니다. `myblockchainmember.blockchain.azure.com`)을 입력합니다. |
| **location** | 블록체인 멤버가 만들어지는 Azure 지역입니다. `eastus`)을 입력합니다. 사용자 또는 다른 Azure 애플리케이션에 가장 가까운 위치를 선택합니다. 기능은 일부 지역에서 사용하지 못할 수도 있습니다. |
| **password** | 멤버의 기본 트랜잭션 노드에 대한 암호입니다. 블록체인 멤버의 기본 트랜잭션 노드 공용 엔드포인트에 연결할 때 기본 인증용 암호를 사용합니다. 암호는 다음 4가지 요구 사항 중 3가지를 충족해야 합니다. 길이는 12~72자 사이이며, 1개의 소문자, 1개의 대문자, 1개의 숫자, 1개의 특수 문자를 사용해야 합니다. 특수 문자는 숫자 기호(#), 퍼센트(%), 쉼표(,), 별표(*), 역따옴표(\`), 큰따옴표(”), 작은따옴표(’), 대시(-), 세미콜론(;)이 아니어야 합니다|
| **protocol** | Blockchain 프로토콜입니다. 현재 *Quorum* 프로토콜이 지원됩니다. |
| **컨소시엄** | 참가할 또는 만들 컨소시엄의 이름입니다. 컨소시엄에 대한 자세한 내용은 [Azure Blockchain Service 컨소시엄](consortium.md)을 참조하세요. |
| **컨소시엄-관리-계정-암호** | 컨소시엄 계정 암호는 멤버 계정 암호라고도 합니다. 멤버 계정 암호는 멤버용으로 생성된 Ethereum 계정의 프라이빗 키를 암호화하는 데 사용됩니다. 컨소시엄 관리용 멤버 계정과 멤버 계정 암호를 사용합니다. |
| **sku** | 계층 유형입니다. *표준* 또는 *기본* 입니다. 개발, 테스트 및 개념 증명에 *기본* 계층을 사용합니다. 프로덕션 등급 배포에 *표준* 계층을 사용합니다. Blockchain Data Manager를 사용하거나 대량의 프라이빗 트랜잭션을 전송하는 경우 *표준* 계층도 사용해야 합니다. 멤버를 만든 후 기본과 표준 간의 가격 책정 계층 변경은 지원되지 않습니다. |

## <a name="change-blockchain-member-passwords-or-firewall-rules"></a>블록체인 멤버 암호 또는 방화벽 규칙 변경

예제는 [블록체인 멤버의 암호, 컨소시엄 관리 암호 및 방화벽 규칙을 업데이트](/cli/azure/blockchain/member#az_blockchain_member_update) 합니다.

```azurecli
az blockchain member update \
                     --resource-group <myResourceGroup> \
                     --name <myMemberName> \
                     --password <strongMemberAccountPassword> \
                     --consortium-management-account-password <strongConsortiumManagementPassword> \
                     --firewall-rules <firewallRules>
```

| 매개 변수 | Description |
|---------|-------------|
| **resource-group** | Azure Blockchain Service 리소스가 만들어지는 리소스 그룹 이름입니다. |
| **name** | Azure Blockchain Service 멤버를 식별하는 이름. |
| **password** | 멤버의 기본 트랜잭션 노드에 대한 암호입니다. 블록체인 멤버의 기본 트랜잭션 노드 공용 엔드포인트에 연결할 때 기본 인증용 암호를 사용합니다. 암호는 다음 4가지 요구 사항 중 3가지를 충족해야 합니다. 길이는 12~72자 사이이며, 1개의 소문자, 1개의 대문자, 1개의 숫자, 1개의 특수 문자를 사용해야 합니다. 특수 문자는 숫자 기호(#), 퍼센트(%), 쉼표(,), 별표(*), 역따옴표(\`), 큰따옴표(”), 작은따옴표(’), 대시(-), 세미콜론(;)이 아니어야 합니다|
| **컨소시엄-관리-계정-암호** | 컨소시엄 계정 암호는 멤버 계정 암호라고도 합니다. 멤버 계정 암호는 멤버용으로 생성된 Ethereum 계정의 프라이빗 키를 암호화하는 데 사용됩니다. 컨소시엄 관리용 멤버 계정과 멤버 계정 암호를 사용합니다. |
| **방화벽 규칙** | IP 허용 목록의 시작 및 끝 IP 주소입니다. |

## <a name="create-transaction-node"></a>트랜잭션 노드 만들기

기존 블록체인 멤버 안에 [트랜잭션 노드를 만듭니다](/cli/azure/blockchain/transaction-node#az_blockchain_transaction_node_create). 트랜잭션 노드를 추가하여 보안 격리를 강화하고 부하를 분산할 수 있습니다. 예를 들어 다른 클라이언트 애플리케이션에 대한 트랜잭션 노드 엔드포인트가 있을 수 있습니다.

```azurecli
az blockchain transaction-node create \
                     --resource-group <myResourceGroup> \
                     --member-name <myMemberName> \
                     --password <strongTransactionNodePassword> \
                     --name <myTransactionNodeName>
```

| 매개 변수 | Description |
|---------|-------------|
| **resource-group** | Azure Blockchain Service 리소스가 만들어지는 리소스 그룹 이름입니다. |
| **location** | 블록체인 멤버의 Azure 지역 |
| **멤버 이름** | Azure Blockchain Service 멤버를 식별하는 이름. |
| **password** | 트랜잭션 노드에 대한 암호입니다. 트랜잭션 노드 공용 엔드포인트에 연결할 때 기본 인증용 암호를 사용합니다. 암호는 다음 4가지 요구 사항 중 3가지를 충족해야 합니다. 길이는 12~72자 사이이며, 1개의 소문자, 1개의 대문자, 1개의 숫자, 1개의 특수 문자를 사용해야 합니다. 특수 문자는 숫자 기호(#), 퍼센트(%), 쉼표(,), 별표(*), 역따옴표(\`), 큰따옴표(”), 작은따옴표(’), 대시(-), 세미콜론(;)이 아니어야 합니다|
| **name** | 트랜잭션 노드 이름입니다. |

## <a name="change-transaction-node-password"></a>트랜잭션 노드 암호 변경하기

예제는 [트랜잭션 노드 암호를 업데이트](/cli/azure/blockchain/transaction-node#az_blockchain_transaction_node_update)합니다.

```azurecli
az blockchain transaction-node update \
                     --resource-group <myResourceGroup> \
                     --member-name <myMemberName> \
                     --password <strongTransactionNodePassword> \
                     --name <myTransactionNodeName>
```

| 매개 변수 | Description |
|---------|-------------|
| **resource-group** | Azure Blockchain Service 리소스가 존재하는 리소스 그룹 이름입니다. |
| **멤버 이름** | Azure Blockchain Service 멤버를 식별하는 이름. |
| **password** | 트랜잭션 노드에 대한 암호입니다. 트랜잭션 노드 공용 엔드포인트에 연결할 때 기본 인증용 암호를 사용합니다. 암호는 다음 4가지 요구 사항 중 3가지를 충족해야 합니다. 길이는 12~72자 사이이며, 1개의 소문자, 1개의 대문자, 1개의 숫자, 1개의 특수 문자를 사용해야 합니다. 특수 문자는 숫자 기호(#), 퍼센트(%), 쉼표(,), 별표(*), 역따옴표(\`), 큰따옴표(”), 작은따옴표(’), 대시(-), 세미콜론(;)이 아니어야 합니다|
| **name** | 트랜잭션 노드 이름입니다. |

## <a name="list-api-keys"></a>API 키 나열하기

API 키는 사용자 이름 및 암호와 유사한 노드 액세스에 사용할 수 있습니다. 키 회전을 지원하는 두 개의 API 키가 있습니다. [API 키를 나열](/cli/azure/blockchain/member#az_blockchain_transaction_node_list-api-key)하려면 다음 명령을 사용합니다.

```azurecli
az blockchain member list-api-key \
                            --resource-group <myResourceGroup> \
                            --name <myMemberName>
```

| 매개 변수 | Description |
|---------|-------------|
| **resource-group** | Azure Blockchain Service 리소스가 존재하는 리소스 그룹 이름입니다. |
| **name** | Azure Blockchain Service 블록체인 멤버 이름 |

## <a name="regenerate-api-keys"></a>API 키 다시 생성하기

다음 명령을 사용하여 [API 키를 다시 생성](/cli/azure/blockchain/member#az_blockchain_transaction_node_regenerate-api-key)합니다.

```azurecli
az blockchain member regenerate-api-key \
                            --resource-group <myResourceGroup> \
                            --name <myMemberName> \
                            [--key-name {<keyValue1>, <keyValue2>}]
```

| 매개 변수 | Description |
|---------|-------------|
| **resource-group** | Azure Blockchain Service 리소스가 존재하는 리소스 그룹 이름입니다. |
| **name** | Azure Blockchain Service 블록체인 멤버 이름. |
| **keyName** | \<keyValue\>을 키1이나 키2 중 하나 또는 모두로 대체합니다. |

## <a name="delete-a-transaction-node"></a>트랜잭션 노드 제거하기

예제는 [블록체인 멤버 트랜잭션 노드를 삭제합니다](/cli/azure/blockchain/transaction-node#az_blockchain_transaction_node_delete).

```azurecli
az blockchain transaction-node delete \
                     --resource-group <myResourceGroup> \
                     --member-name <myMemberName> \
                     --name <myTransactionNode>
```

| 매개 변수 | Description |
|---------|-------------|
| **resource-group** | Azure Blockchain Service 리소스가 존재하는 리소스 그룹 이름입니다. |
| **멤버 이름** | 삭제할 트랜잭션 노드 이름도 포함하는 Azure Blockchain Service 블록체인 멤버 이름입니다. |
| **name** | 삭제할 트랜잭션 노드 이름입니다. |

## <a name="delete-a-blockchain-member"></a>블록체인 멤버 삭제

예제는 [블록체인 멤버를 삭제합니다](/cli/azure/blockchain/member#az_blockchain_member_delete).

```azurecli
az blockchain member delete \
                     --resource-group <myResourceGroup> \
                     --name <myMemberName>

```

| 매개 변수 | Description |
|---------|-------------|
| **resource-group** | Azure Blockchain Service 리소스가 존재하는 리소스 그룹 이름입니다. |
| **name** | 삭제할 Azure Blockchain Service 블록체인 멤버의 이름입니다. |

## <a name="azure-active-directory"></a>Azure Active Directory

### <a name="grant-access-for-azure-ad-user"></a>Azure AD 사용자에 대한 액세스 권한 부여

```azurecli
az role assignment create \
                            --role <role> \
                            --assignee <assignee> \
                            --scope /subscriptions/<subId>/resourceGroups/<groupName>/providers/Microsoft.Blockchain/blockchainMembers/<myMemberName>
```

| 매개 변수 | 설명 |
|---------|-------------|
| **role** | Azure Active Directory 역할의 이름. |
| **assignee** | Azure Active Directory 사용자 ID. 예를 들어 `user@contoso.com` |
| **범위** | 역할 할당의 범위. 블록체인 멤버 또는 트랜잭션 노드일 수 있습니다. |

**예:**

Azure AD 사용자에 대한 노드 액세스를 블록체인 **멤버** 에게 다음과 같이 부여

```azurecli
az role assignment create \
                            --role 'myRole' \
                            --assignee user@contoso.com \
                            --scope /subscriptions/mySubscriptionId/resourceGroups/contosoResourceGroup/providers/Microsoft.Blockchain/blockchainMembers/contosoMember1
```

**예:**

Azure Active Directory 사용자에 대한 노드 액세스를 블록체인 **트랜잭션 노드** 에게 다음과 같이 부여

```azurecli
az role assignment create \
                            --role 'MyRole' \
                            --assignee user@contoso.com \
                            --scope /subscriptions/mySubscriptionId/resourceGroups/contosoResourceGroup/providers/Microsoft.Blockchain/blockchainMembers/contosoMember1/transactionNodes/contosoTransactionNode1
```

### <a name="grant-node-access-for-azure-ad-group-or-application-role"></a>Azure Active Directory 그룹 또는 애플리케이션 역할에 대한 노드 액세스 권한 부여

```azurecli
az role assignment create \
                            --role <role> \
                            --assignee-object-id <assignee_object_id>
```

| 매개 변수 | 설명 |
|---------|-------------|
| **role** | Azure Active Directory 역할의 이름. |
| **담당자-개체-id** | Azure AD 그룹 ID 또는 애플리케이션 ID입니다. |
| **범위** | 역할 할당의 범위. 블록체인 멤버 또는 트랜잭션 노드일 수 있습니다. |

**예:**

**애플리케이션 역할** 에 대한 노드 액세스 권한 부여

```azurecli
az role assignment create \
                            --role 'myRole' \
                            --assignee-object-id 22222222-2222-2222-2222-222222222222 \
                            --scope /subscriptions/mySubscriptionId/resourceGroups/contosoResourceGroup/providers/Microsoft.Blockchain/blockchainMembers/contosoMember1
```

### <a name="remove-azure-ad-node-access"></a>Azure AD 노드 액세스 제거

```azurecli
az role assignment delete \
                            --role <myRole> \
                            --assignee <assignee> \
                            --scope /subscriptions/mySubscriptionId/resourceGroups/<myResourceGroup>/providers/Microsoft.Blockchain/blockchainMembers/<myMemberName>/transactionNodes/<myTransactionNode>
```

| 매개 변수 | 설명 |
|---------|-------------|
| **role** | Azure Active Directory 역할의 이름. |
| **assignee** | Azure Active Directory 사용자 ID. 예를 들어 `user@contoso.com` |
| **범위** | 역할 할당의 범위. 블록체인 멤버 또는 트랜잭션 노드일 수 있습니다. |

## <a name="next-steps"></a>다음 단계

Azure Portal를 사용하여 [Azure Blockchain Service 트랜잭션 노드를 구성](configure-transaction-nodes.md)하는 방법을 알아봅니다.
