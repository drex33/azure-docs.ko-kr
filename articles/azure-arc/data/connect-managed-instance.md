---
title: Azure Arc 지원 SQL Managed Instance에 연결하기
description: Azure Arc 지원 SQL Managed Instance에 연결하기
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: dnethi
ms.author: dinethi
ms.reviewer: mikeray
ms.date: 07/30/2021
ms.topic: how-to
ms.openlocfilehash: c0a64d5756895f18cbb1285586570ac72dd1c12e
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122536652"
---
# <a name="connect-to-azure-arc-enabled-sql-managed-instance"></a>Azure Arc 지원 SQL Managed Instance에 연결하기

이 문서에서는 Azure Arc 지원 SQL Managed Instance에 연결할 수 있는 방법을 설명합니다. 


## <a name="view-azure-arc-enabled-sql-managed-instances"></a>Azure Arc 지원 SQL Managed Instance 보기

Azure Arc 지원 SQL Managed Instance 및 외부 엔드포인트를 보려면 다음 명령을 사용합니다.

```azurecli
az sql mi-arc list --k8s-namespace <namespace> --use-k8s -o table
```

출력은 다음과 같습니다.

```console
Name       PrimaryEndpoint      Replicas    State
---------  -------------------  ----------  -------
sqldemo    10.240.0.107,1433    1/1         Ready
```

AKS, kubeadm 또는 OpenShift 등을 사용하는 경우 여기에서 외부 IP 및 포트 번호를 복사하고 즐겨 사용하는 도구를 사용하여 Azure Data Studio 또는 SQL Server Management Studio와 같은 SQL Sever/Azure SQL 인스턴스에 연결할 수 있습니다.  그러나 빠른 시작 VM을 사용하는 경우 Azure 외부에서 해당 VM에 연결하는 방법에 대한 특수 정보는 아래를 참조하세요. 

> [!NOTE]
> 특히, 퍼블릭 클라우드에서 생성된 경우 회사 정책에서 IP 및 포트에 대한 액세스를 차단할 수 있습니다.

## <a name="connect"></a>연결 

Azure Data Studio, SQL Server Management Studio 또는 SQLCMD와 연결

Azure Data Studio를 열고 위의 외부 엔드포인트 IP 주소와 포트 번호를 사용하여 인스턴스에 연결합니다. Azure VM을 사용하는 경우 [Azure 가상 머신 배포에 대한 특별 메모](#special-note-about-azure-virtual-machine-deployments)를 사용하여 식별할 수 있는 _공용_ IP 주소가 필요합니다.

예를 들면 다음과 같습니다.

- 서버: 52.229.9.30,30913
- 사용자 이름: sa
- 암호: 프로비저닝 시 지정한 SQL 암호

> [!NOTE]
> Azure Data Studio 사용하여 [SQL Managed Instance 대시보드를 볼 수 있습니다](azure-data-studio-dashboards.md#view-the-sql-managed-instance-dashboards).

SQLCMD, Linux 또는 Windows를 사용하여 연결하려면 다음과 같은 명령을 사용할 수 있습니다. 프롬프트가 표시되면 SQL 암호를 입력합니다.

```bash
sqlcmd -S 52.229.9.30,30913 -U sa
```

## <a name="special-note-about-azure-virtual-machine-deployments"></a>Azure 가상 머신 배포에 대한 특별 메모

Azure 가상 머신을 사용하는 경우 엔드포인트 IP 주소는 공용 IP 주소를 표시하지 않습니다. 외부 IP 주소를 찾으려면 다음 명령을 사용합니다.

```azurecli
az network public-ip list -g azurearcvm-rg --query "[].{PublicIP:ipAddress}" -o table
```

그러면 공용 IP 주소와 포트를 결합하여 연결을 만들 수 있습니다.

NSG(네트워크 보안 게이트웨이)를 통해 sql 인스턴스의 포트를 노출해야 할 수도 있습니다. NSG를 통한 트래픽을 허용하려면 다음 명령을 사용하여 수행할 수 있는 규칙을 추가해야 합니다.

규칙을 설정하려면 NSG의 이름을 알아야 합니다. NSG의 이름은 다음 명령을 사용하여 찾을 수 있습니다.

```azurecli
az network nsg list -g azurearcvm-rg --query "[].{NSGName:name}" -o table
```

NSG의 이름을 보유한 경우 다음 명령을 사용하여 방화벽 규칙을 추가할 수 있습니다. 이 예제 값에서는 포트 30913에 대한 NSG 규칙을 만들고 **모든** 원본 IP 주소에서 연결할 수 있도록 허용합니다.  이 방법은 보안 모범 사례가 아닙니다.  팀 또는 조직의 IP 주소를 포함하는 IP 주소 범위 또는 클라이언트 IP 주소와 관련된 -source-address-prefixes 값을 지정하면 더 잘 잠글 수 있습니다.

아래의 `--destination-port-ranges` 매개변수 값을 위의 `az sql mi-arc list` 명령에서 얻은 포트 번호로 바꿉니다.

```azurecli
az network nsg rule create -n db_port --destination-port-ranges 30913 --source-address-prefixes '*' --nsg-name azurearcvmNSG --priority 500 -g azurearcvm-rg --access Allow --description 'Allow port through for db access' --destination-address-prefixes '*' --direction Inbound --protocol Tcp --source-port-ranges '*'
```

## <a name="next-steps"></a>다음 단계

- [SQL 관리형 인스턴스 대시보드 보기](azure-data-studio-dashboards.md#view-the-sql-managed-instance-dashboards)
- [Azure Portal에서 SQL Managed Instance 보기](view-arc-data-services-inventory-in-azure-portal.md)
