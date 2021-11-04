---
title: 프라이빗 액세스를 사용하여 서버 그룹 만들기(미리 보기) - 하이퍼스케일(Citus) - Azure Database for PostgreSQL
description: VM을 서버 그룹 프라이빗 엔드포인트에 연결
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: tutorial
ms.date: 10/15/2021
ms.openlocfilehash: 1ae844b4778f8a117bd8ae8807fa28849d9d310d
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131056351"
---
# <a name="create-server-group-with-private-access-preview-in-azure-database-for-postgresql---hyperscale-citus"></a>Azure Database for PostgreSQL에서 프라이빗 액세스(미리 보기)를 사용하여 서버 그룹 만들기 - 하이퍼스케일(Citus)

이 자습서에서는 가상 머신 및 하이퍼스케일(Citus) 서버 그룹을 만들고 이들 간에 [프라이빗 액세스](concepts-hyperscale-private-access.md)를 설정합니다.

## <a name="create-a-virtual-network"></a>가상 네트워크 만들기

먼저 리소스 그룹 및 가상 네트워크를 설정합니다. 서버 그룹 및 가상 머신을 포함합니다.

```sh
az group create \
    --name link-demo \
    --location eastus

az network vnet create \
    --resource-group link-demo \
    --name link-demo-net \
    --address-prefix 10.0.0.0/16

az network nsg create \
    --resource-group link-demo \
    --name link-demo-nsg

az network vnet subnet create \
    --resource-group link-demo \
    --vnet-name link-demo-net \
    --name link-demo-subnet \
    --address-prefixes 10.0.1.0/24 \
    --network-security-group link-demo-nsg
```

## <a name="create-a-virtual-machine"></a>가상 머신 만들기

데모를 위해 Debian Linux와 `psql` PostgreSQL 클라이언트를 실행하는 가상 머신을 사용합니다.

```sh
# provision the VM
az vm create \
    --resource-group link-demo \
    --name link-demo-vm \
    --vnet-name link-demo-net \
    --subnet link-demo-subnet \
    --nsg link-demo-nsg \
    --public-ip-address link-demo-net-ip \
    --image debian \
    --admin-username azureuser \
    --generate-ssh-keys

# install psql database client
az vm run-command invoke \
    --resource-group link-demo \
    --name link-demo-vm \
    --command-id RunShellScript \
    --scripts \
        "sudo touch /home/azureuser/.hushlogin" \
        "sudo DEBIAN_FRONTEND=noninteractive apt-get update" \
        "sudo DEBIAN_FRONTEND=noninteractive apt-get install -q -y postgresql-client"
```

## <a name="create-a-server-group-with-a-private-link"></a>프라이빗 링크를 사용하여 서버 그룹 만들기

1. Azure Portal의 왼쪽 위 모서리에서 **리소스 만들기** 를 선택합니다.

2. **새로 만들기** 페이지에서 **데이터베이스** 를 선택하고, **데이터베이스** 페이지에서 **PostgreSQL용 Azure Database** 를 선택합니다.

3. 배포 옵션의 경우 **하이퍼스케일(Citus) 서버 그룹** 아래에서 **만들기** 단추를 선택합니다.

4. 새 서버 세부 정보 양식을 다음 정보로 작성합니다.

    - **리소스 그룹**: `link-demo`
    - **서버 그룹 이름**: `link-demo-sg`
    - **위치**: `East US`
    - **암호**:(선택 사항)

    > [!NOTE]
    >
    > 서버 그룹 이름은 DNS 항목을 만들기 때문에 Azure에서 전역적으로 고유해야 합니다. `link-demo-sg`를 사용할 수 없는 경우 다른 이름을 선택하고 아래 단계를 적절하게 조정하세요.

5. **서버 그룹 구성**, **기본** 계획, **저장** 을 차례로 선택합니다.

6. 완료되면 **다음: 네트워킹** 을 선택합니다.

7. **프라이빗 액세스(미리 보기)** 를 선택합니다.

    > [!NOTE]
    >
    > 프라이빗 액세스는 [특정 지역](concepts-hyperscale-limits.md#regions)에서만 미리 볼 수 있습니다.
    >
    > 서버 그룹이 허용된 지역 내에 있더라도 서버 그룹에 대해 프라이빗 액세스 옵션을 선택할 수 없는 경우 Azure [지원 요청](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)을 열고 Azure 구독 ID를 포함하여 액세스 권한을 가져오세요.

8. **프라이빗 엔드포인트 만들기** 라는 화면이 나타납니다. 이러한 값을 입력하고 **확인** 을 선택합니다.

    - **리소스 그룹**: `link-demo`
    - **위치**: `(US) East US`
    - **이름**: `link-demo-sg-c-pe1`
    - **대상 하위 리소스**: `coordinator`
    - **가상 네트워크**: `link-demo-net`
    - **서브넷**: `link-demo-subnet`
    - **프라이빗 DNS 영역과 통합**: 예

9. 프라이빗 엔드포인트를 만든 후 **검토 + 만들기** 를 선택하여 하이퍼스케일(Citus) 서버 그룹을 만듭니다.

## <a name="access-the-server-group-privately-from-the-virtual-machine"></a>가상 머신에서 비공개로 서버 그룹에 액세스

프라이빗 링크를 사용하면 가상 머신이 서버 그룹에 연결하여 외부 호스트에서 이 작업을 수행할 수 없습니다. 이 단계에서는 가상 머신의 `psql` 데이터베이스 클라이언트가 서버 그룹의 코디네이터 노드와 통신할 수 있는지 확인합니다.

```sh
# save db URI
#
# obtained from Settings -> Connection Strings in the Azure portal
#
# replace {your_password} in the string with your actual password
PG_URI='host=c.link-demo-sg.postgres.database.azure.com port=5432 dbname=citus user=citus password={your_password} sslmode=require'

# attempt to connect to server group with psql in the virtual machine
az vm run-command invoke \
    --resource-group link-demo \
    --name link-demo-vm \
    --command-id RunShellScript \
    --scripts "psql '$PG_URI' -c 'SHOW citus.version;'" \
    --query 'value[0].message' \
    | xargs printf
```

출력에 Citus의 버전 번호가 표시되어야 합니다. 이렇게 하면 psql에서 명령을 실행할 수 있으며 프라이빗 링크가 작동합니다.

## <a name="clean-up-resources"></a>리소스 정리

가상 머신과 하이퍼스케일(Citus) 서버 그룹 간에 프라이빗 링크를 만드는 방법을 살펴보았습니다. 이제 리소스의 프로비전을 해제할 수 있습니다.

리소스 그룹을 삭제하면 내부의 리소스가 프로비전 해제됩니다.

```sh
az group delete --resource-group link-demo

# press y to confirm
```

## <a name="next-steps"></a>다음 단계

* [프라이빗 액세스](concepts-hyperscale-private-access.md)(미리 보기)에 대해 자세히 알아보기
* [프라이빗 엔드포인트](../private-link/private-endpoint-overview.md)에 대해 알아보기
* [가상 네트워크](../virtual-network/concepts-and-best-practices.md)에 대해 알아보기
* [프라이빗 DNS 영역](../dns/private-dns-overview.md)에 대해 알아보기