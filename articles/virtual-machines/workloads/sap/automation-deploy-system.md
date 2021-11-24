---
title: 자동화 프레임워크에 대한 SAP 시스템 배포 정보
description: Azure의 SAP 배포 자동화 프레임워크 내에서 SAP 시스템 배포 프로세스에 대한 개요입니다.
author: kimforss
ms.author: kimforss
ms.reviewer: kimforss
ms.date: 11/17/2021
ms.topic: conceptual
ms.service: virtual-machines-sap
ms.openlocfilehash: 1167014bcbfd46fb92e95e6db4abab78fc9f1c1f
ms.sourcegitcommit: 1aeff9f012cfd868104ef0159c5204e402d75696
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/24/2021
ms.locfileid: "133031387"
---
# <a name="sap-system-deployment-for-the-automation-framework"></a>자동화 프레임워크에 대한 SAP 시스템 배포

[SAP 시스템](automation-deployment-framework.md#sap-concepts) 만들기는 Azure 프로세스에서 SAP 배포 [자동화 프레임워크의](automation-deployment-framework.md) 일부입니다. SAP 시스템은 VM(가상 머신)과 [SAP 애플리케이션에](automation-deployment-framework.md#sap-concepts)대한 지원 구성 요소를 만듭니다. 

SAP 시스템은 다음을 배포합니다.

- [데이터베이스 계층](#database-tier)- 데이터베이스 VM, 해당 디스크 및 표준 Azure Load Balancer 배포합니다. 이 계층에서 [HANA 데이터베이스](automation-configure-extra-disks.md#hana-databases) 또는 [AnyDB 데이터베이스를](automation-configure-extra-disks.md#anydb-databases) 실행할 수 있습니다.
- 고객이 정의한 수의 VM 및 Azure 표준 Load Balancer 배포하는 [SAP 중앙 서비스 계층입니다.](#central-services-tier)
- [애플리케이션 계층](#application-tier)- VM 및 해당 디스크를 배포합니다.
- [웹 디스패처 계층](#web-dispatcher-tier)

## <a name="application-tier"></a>애플리케이션 계층

애플리케이션 계층은 고객이 정의한 수의 VM을 배포합니다. 이러한 VM은  30GB OS(운영 체제) 디스크와 512GB 데이터 디스크를 Standard_D4s_v3 크기입니다.

애플리케이션 서버 수를 설정하려면 매개 변수 `application_server_count` 파일에서 이 계층에 대한 매개 변수를 정의합니다. 예들 들어 `application_server_count= 3`입니다.


## <a name="central-services-tier"></a>중앙 서비스 계층

SAP SCS(중앙 서비스) 계층은 고객이 정의한 수의 VM을 배포합니다. 이러한 VM은 **30GB** OS 디스크 및 512GB 데이터 디스크를 Standard_D4s_v3 크기입니다. 또한 이 계층은 [Azure 표준 Load Balancer](../../../load-balancer/load-balancer-overview.md)배포합니다.

SCS 서버 수를 설정하려면 매개 변수 `scs_server_count` 파일에서 이 계층에 대한 매개 변수를 정의합니다. 예들 들어 `scs_server_count=1`입니다.


## <a name="web-dispatcher-tier"></a>웹 디스패처 계층

웹 디스패처 계층은 고객이 정의한 수의 VM을 배포합니다.  또한 이 계층은 [Azure 표준 Load Balancer](../../../load-balancer/load-balancer-overview.md)배포합니다.

웹 서버 수를 설정하려면 매개 변수 `web_server_count` 파일에서 이 계층에 대한 매개 변수를 정의합니다. 예들 들어 `web_server_count = 2`입니다.

## <a name="database-tier"></a>데이터베이스 계층

데이터베이스 계층은 VM 및 해당 디스크와 Azure [표준 Load Balancer](../../../load-balancer/load-balancer-overview.md)배포합니다. [HANA 데이터베이스](automation-configure-extra-disks.md#hana-databases) 또는 [AnyDB 데이터베이스를 데이터베이스](automation-configure-extra-disks.md#anydb-databases) VM으로 사용할 수 있습니다.

이 계층에 대한 매개 변수를 통해 데이터베이스 VM의 크기를 설정할 수 `size` 있습니다. 예를 들어 `"size": "S4Demo"` HANA 데이터베이스의 경우 또는 `"size": "1 TB"` AnyDB 데이터베이스의 경우 입니다. 가능한 값은 [HANA 데이터베이스 VM 옵션](automation-configure-extra-disks.md#hana-databases) 테이블의 **Size** 매개 변수 및 [AnyDB 데이터베이스 VM 옵션을](automation-configure-extra-disks.md#anydb-databases) 참조하세요.

기본적으로 자동화 프레임워크는 HANA 데이터베이스 배포를 위한 올바른 디스크 구성을 배포합니다. HANA 데이터베이스 배포의 경우 프레임워크는 VM 크기에 따라 기본 디스크 구성을 계산합니다. 그러나 AnyDB 데이터베이스 배포의 경우 프레임워크는 데이터베이스 크기에 따라 기본 디스크 구성을 계산합니다. 배포에서 사용자 지정 JSON 파일을 만들어 필요에 따라 디스크 크기를 설정할 수 있습니다. 예를 들어 [다음 JSON 코드 샘플을 참조하고 구성에 필요한 값을 대체합니다.](automation-configure-extra-disks.md#custom-sizing-file) 그런 다음 `db_disk_sizes_filename` 데이터베이스 계층에 대한 매개 변수 파일에서 매개 변수를 정의합니다. 예들 들어 `db_disk_sizes_filename = "path/to/JSON/file"`입니다.

[새 시스템에 추가 디스크를 추가하거나](automation-configure-extra-disks.md#custom-sizing-file)기존 시스템에 추가 디스크를 추가할 수도 [있습니다.](automation-configure-extra-disks.md#add-extra-disks-to-existing-system)

## <a name="core-configuration"></a>핵심 구성

다음 예제 매개 변수 파일은 필수 매개 변수만 보여줍니다.

```bash
# The environment value is a mandatory field, it is used for partitioning the environments, for example (PROD and NP)
environment="DEV"

# The location value is a mandatory field, it is used to control where the resources are deployed
location="westeurope"

# The network logical name is mandatory - it is used in the naming convention and should map to the workload virtual network logical name 
network_name="SAP01"

# sid is a mandatory field that defines the SAP Application SID
sid="RH7"

app_tier_vm_sizing="Production"
app_tier_use_DHCP=true

database_platform="HANA"

database_size="S4Demo"
database_sid="XDB"

database_vm_use_DHCP=true

database_vm_image={
  os_type="linux"
  source_image_id=""
  publisher="SUSE"
  offer="sles-sap-15-sp2"
  sku="gen2"
  version="latest"
}

# application_server_count defines how many application servers to deploy
application_server_count=2

application_server_image= {
  os_type=""
  source_image_id=""
  publisher="SUSE"
  offer="sles-sap-15-sp2"
  sku="gen2"
  version="latest"
}

scs_server_count=1

# scs_instance_number
scs_instance_number="00"

# ers_instance_number
ers_instance_number="02"

# webdispatcher_server_count defines how many web dispatchers to deploy
webdispatcher_server_count=0


```

## <a name="deploying-the-sap-system"></a>SAP 시스템 배포
   
샘플 SAP 시스템 구성 `DEV-WEEU-SAP01-X01.tfvars` 파일은 `~/Azure_SAP_Automated_Deployment/WORKSPACES/SYSTEM/DEV-WEEU-SAP01-X01` 폴더에 있습니다.

아래 명령을 실행하면 SAP 시스템이 배포됩니다.

# <a name="linux"></a>[Linux](#tab/linux)

> [!TIP]
> 배포자에서 이 작업을 수행합니다.

샘플 구성 파일을 복사하여 배포 자동화 프레임워크 테스트를 시작할 수 있습니다.

```bash
cd ~/Azure_SAP_Automated_Deployment

cp -R sap-automation/deploy/samples/WORKSPACES WORKSPACES

```


```bash
cd ~/Azure_SAP_Automated_Deployment/WORKSPACES/SYSTEM/DEV-WEEU-SAP01-X01

${DEPLOYMENT_REPO_PATH}/deploy/scripts/installer.sh          \
        --parameterfile DEV-WEEU-SAP01-X01.tfvars           \
        --type sap_system
```
# <a name="windows"></a>[Windows](#tab/windows)

샘플 구성 파일을 복사하여 배포 자동화 프레임워크 테스트를 시작할 수 있습니다.

```powershell

cd C:\Azure_SAP_Automated_Deployment

xcopy sap-automation\deploy\samples\WORKSPACES WORKSPACES

```


```powershell

cd C:\Azure_SAP_Automated_Deployment\WORKSPACES\SYSTEM\DEV-WEEU-SAP01-X01

New-SAPSystem -Parameterfile DEV-WEEU-SAP01-X01.tfvars 
-Type sap_system
```

---


## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [자동화 프레임워크를 통해 워크로드 영역 배포 정보](automation-software.md)
