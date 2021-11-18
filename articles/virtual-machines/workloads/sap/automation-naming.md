---
title: 자동화 프레임워크에 대한 명명 표준
description: Azure의 SAP 배포 자동화 프레임워크에 대한 명명 규칙에 대한 설명입니다.
author: kimforss
ms.author: kimforss
ms.reviewer: kimforss
ms.date: 11/17/2021
ms.topic: reference
ms.service: virtual-machines-sap
ms.openlocfilehash: 628eb0dc4cef93b8782e38660709ee93230cc616
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/17/2021
ms.locfileid: "132730416"
---
# <a name="naming-conventions-for-sap-automation-framework"></a>SAP 자동화 프레임워크에 대한 명명 규칙

[Azure의 SAP 배포 자동화 프레임워크는](automation-deployment-framework.md) 표준 명명 규칙을 사용합니다. 일관된 이름 지정은 Terraform을 통해 자동화 프레임워크를 올바르게 실행하는 데 도움이 됩니다. 표준 명명은 자동화 프레임워크를 원활하게 배포하는 데 도움이 됩니다. 예를 들어 일관된 이름을 지정하면 다음을 수행할 수 있습니다.

- SAP 가상 네트워크 인프라를 지원되는 모든 Azure 지역에 배포합니다.

- 분할된 가상 네트워크를 사용하여 여러 배포를 수행합니다. 

- SAP 시스템을 모든 SAP 워크로드 영역에 배포합니다. 

- 일반 및 HA(고가용성) 인스턴스 실행

- 재해 복구 및 전달 동작을 수행합니다.

배포를 시작하기 전에 표준 용어, 영역 경로, 변수 이름을 검토합니다. 필요한 경우 사용자 [지정 이름 지정](automation-naming-module.md)을 구성할 수도 있습니다.

## <a name="placeholder-values"></a>자리 표시자 값

명명 규칙의 예제 형식은 다음 자리 표시자 값을 사용합니다.

| 자리표시자 | 개념 | 문자 제한 | 예제 | 
| ----------- | ------- | --------------- | ------- |
| `{ENVIRONMENT}` | Environment | 5 | `DEV`, `PROTO`, `NP`, `PROD` |
| `{REGION_MAP}` | [지역](#azure-region-names) 맵 | 4 | `westus`의 경우 `weus` |
| `{SAP_VNET}` | SAP VNet(가상 네트워크) | 7 |  `SAP0` |
| `{SID}` | SAP 시스템 식별자 | 3 | `X01` |
| `{PREFIX}` | SAP 리소스 접두사 | | `DEV-WEEU-SAP01-X01` |
| `{DEPLOY_VNET}` | 배포자 VNet | 7 |  |
| `{REMOTE_VNET}` | 원격 VNet | 7 |  |
| `{LOCAL_VNET}` |로컬 VNet | 7 |  |
| `{CODENAME}` | 버전의 논리적 이름 |  | `version1`, `beta` |
| `{VM_NAME}` | VM 이름 |  |  |
| `{SUBNET}` | 서브넷 |  |  |
| `{DBSID}` | 데이터베이스 시스템 식별자 |  |  |
| `{DIAG}` | | 5 |  |
| `{RND}` | | 3 |  |
| `{USER}` | | 12 |  |
| `{COMPUTER_NAME}` | | 14 |  |

### <a name="deployer-names"></a>배포자 이름

**서식** 열에 대한 설명은 [자리 표시자 값 에 대한 정의를 참조하세요.](#placeholder-values)

| 개념 | 문자 제한 | 형식 | 예제 |
| ------- | --------------- | ------ | ------- |
| 리소스 그룹 | 80 | `{ENVIRONMENT}-{REGION_MAP}-{DEPLOY_VNET}-INFRASTRUCTURE` |  `MGMT-WEEU-DEP00-INFRASTRUCTURE` |
| 가상 네트워크 | 38 (64)  | `{ENVIRONMENT}-{REGION_MAP}-{DEPLOY_VNET}-vnet` | `MGMT-WEEU-DEP00-vnet` |
| 서브넷 | 80 | `{ENVIRONMENT}-{REGION_MAP}-{DEPLOY_VNET}_deployment-subnet` | `MGMT-WEEU-DEP00_deployment-subnet` |
| 스토리지 계정 | 24 | `{ENVIRONMENT}{REGION_MAP}{SAP_VNET}{DIAG}{RND}` | `mgmtweeudep00diagxxx` |
| NSG(네트워크 보안 그룹) | 80 | `{ENVIRONMENT}-{REGION_MAP}-{DEPLOY_VNET}_deployment-nsg` | `MGMT-WEEU-DEP00_deployment-nsg` |
| 경로 테이블 | | `{ENVIRONMENT}-{REGION_MAP}-{DEPLOY_VNET}_routeTable` | `MGMT-WEEU-DEP00_route-table` |
| 네트워크 인터페이스 구성 요소 | 80 | `{ENVIRONMENT}-{REGION_MAP}-{DEPLOY_VNET}_{COMPUTER_NAME}-nic` | `-ipconfig1` (블록에 필요한 것은 `ip_configuration` 없습니다.) |
| 디스크 | |`{vm.name}-deploy00` | `PROTO-WUS2-DEPLOY_deploy00-disk00` |
| VM | | `{ENVIRONMENT}-{REGION_MAP}-{SAP_VNET}_deploy##` | `MGMT-WEEU-DEP00_permweeudep00deploy00` |
| OS(운영 체제) 디스크 | | `{ENVIRONMENT}-{REGION_MAP}-{DEPLOY_VNET}_deploy##-OsDisk` | `PERM-WEEU-DEP00_permweeudep00deploy00-OsDisk` |
| 컴퓨터 이름 | | `{environment[_map]}{DEPLOY_VNET}{region_map}deploy##` | `MGMT-WEEU-DEP00_permweeudep00deploy00` |
| 주요 자격 증명 모음 | 24 | `{ENVIRONMENT}{REGION_MAP}{DEPLOY_VNET}{USER}{RND}` (배포 자격 증명) | `MGMTWEEUDEP00userxxx` |
| 공용 IP 주소 | | `{ENVIRONMENT}-{REGION_MAP}-{DEPLOY_VNET}_{COMPUTER_NAME}-pip` | `MGMT-WEEU-DEP00_permweeudep00deploy00-pip` |

### <a name="sap-library-names"></a>SAP 라이브러리 이름

**서식** 열에 대한 설명은 [자리 표시자 값 에 대한 정의를 참조하세요.](#placeholder-values)

| 개념 | 문자 제한 | 형식 | 예제 |
| ------- | --------------- | ------ | ------- |
| Resource group | 80 | `{ENVIRONMENT}-{REGION_MAP}-SAP_LIBRARY` | `MGMT-WEEU-SAP_LIBRARY` |
| 스토리지 계정 | 24 | `{ENVIRONMENT}{REGION_MAP}saplib(12CHAR){RND}` | `mgmtweeusaplibxxx` |
| 스토리지 계정 | 24 | `{ENVIRONMENT}{REGION_MAP}tfstate(12CHAR){RND}` | `mgmtweeutfstatexxx` |

### <a name="sap-workload-zone-names"></a>SAP 워크로드 영역 이름

**서식** 열에 대한 설명은 [자리 표시자 값 에 대한 정의를 참조하세요.](#placeholder-values)

| 개념         | 문자 제한 | 형식 | 예제 |
| --------------- | --------------- | ------ | ------- |
| Resource group  | 80              | `{ENVIRONMENT}-{REGION_MAP}-{SAP_VNET}-INFRASTRUCTURE` | `DEV-WEEU-SAP01-INFRASTRUCTURE` |
| 가상 네트워크 | 38 (64)         | `{ENVIRONMENT}-{REGION_MAP}-{SAP_VNET}-vnet` | `DEV-WEEU-SAP01-vnet` |
| 피어링         | 80              | `{LOCAL_VNET}_to_{REMOTE_VNET}` | `DEV-WEEU-SAP01-vnet_to_MGMT-WEEU-DEP00-vnet` |
| 서브넷          | 80              | `{ENVIRONMENT}-{REGION_MAP}-{SAP_VNET}_utility-subnet` | `DEV-WEEU-SAP01_db-subnet` |
| 네트워크 보안 그룹 | 80 | `{ENVIRONMENT}-{REGION_MAP}-{SAP_VNET}_utility-nsg` | `DEV-WEEU-SAP01_dbSubnet-nsg` |
| 경로 테이블 | | `{ENVIRONMENT}-{REGION_MAP}-{SAP_VNET}_routeTable` | `DEV-WEEU-SAP01_route-table` |
| 스토리지 계정 | 80              | `{ENVIRONMENT}{REGION_MAP}{SAP_VNET}diag(5CHAR){RND}` | `devweeusap01diagxxx` |
| 사용자 정의 경로 | | `{remote_vnet}_Hub-udr` | |
| 사용자 정의 경로 (방화벽)| | `{ENVIRONMENT}-{REGION_MAP}-{SAP_VNET}_firewall-route` |`DEV-WEEU-SAP01_firewall-route` |
| 가용성 집합 (AV 집합) | | `{ENVIRONMENT}-{REGION_MAP}-{SAP_VNET}_iscsi-avset` | |
| 네트워크 인터페이스 구성 요소 | 80 | `{ENVIRONMENT}-{REGION_MAP}-{SAP_VNET}_iscsi##-nic` | |
| 디스크 | | `{vm.name}-iscsi00` 또는 `${azurerm_virtual_machine.iscsi.*.name}-iscsi00` (코드) | `DEV-WEEU-SAP01_iscsi00-iscsi00` |
| VM | | `{ENVIRONMENT}-{REGION_MAP}-{SAP_VNET}_iscsi##` | |
| OS 디스크 | `{ENVIRONMENT}-{REGION_MAP}-{SAP_VNET}_iscsi##-OsDisk` | |
| 컴퓨터 이름 | | `{ENVIRONMENT}_{REGION_MAP}{SAP_VNET}{region_map}iscsi##` | |
| 주요 자격 증명 모음 | 24 | `{ENVIRONMENT}{REGION_MAP}{SAP_VNET}{USER}{RND}` | `DEVWEEUSAP01userxxx` |
| NetApp 계정 |  | `{ENVIRONMENT}{REGION_MAP}{SAP_VNET}_netapp_account` | `DEV-WEEU-SAP01_netapp_account` |
| NetApp 용량 풀 | 24 | `{ENVIRONMENT}{REGION_MAP}{SAP_VNET}_netapp_pool` | `DEV-WEEU-SAP01_netapp_pool` |

### <a name="sap-system-names"></a>SAP 시스템 이름

**형식** 열에 대 한 설명은 [자리 표시자 값에 대 한 정의](#placeholder-values)를 참조 하세요.

| 개념         | 문자 제한 | 형식 | 예제 |
| --------------- | --------------- | ------ | ------- |
| 리소스 접두사 | 80              | `{ENVIRONMENT}-{REGION_MAP}-{SAP-VNET}-{SID}` 또는 `{ENVIRONMENT}-{REGION_MAP}-{SAP-VNET}_{CODENAME}-{SID}` | `DEV-WEEU-SAP01-X01` |
| Resource group  | 80              | `{PREFIX}` | `DEV-WEEU-SAP01-X01` |
| Azure 근접 배치 그룹 (PPG) | | `{PREFIX}_ppg` | |
| 가용성 집합 | | `{PREFIX}_app-avset` | `DEV-WEEU-SAP01-X01_app-avset` |
| 서브넷          | 80              | `{PREFIX}_utility-subnet` | `DEV-WEEU-SAP01_X01_db-subnet` |
| 네트워크 보안 그룹 | 80 | `{PREFIX}_utility-nsg` | `DEV-WEEU-SAP01_X01_dbSubnet-nsg` |
| 네트워크 인터페이스 구성 요소 | | `{PREFIX}_{VM_NAME}-{SUBNET}-nic` | `-app-nic`, `-web-nic`, `-admin-nic`, `-db-nic` |
| 컴퓨터 이름 (데이터베이스) | 14 | `{SID}d{DBSID}##{OS flag l/w}{primary/secondary 0/1}{RND}` | `DEV-WEEU-SAP01-X01_x01dxdb00l0xxx` |
| 컴퓨터 이름 (데이터베이스 외) | 14 | `{SID}{ROLE}##{OS flag l/w}{RND}` | `DEV-WEEU-SAP01-X01_x01app01l538`, `DEV-WEEU-SAP01-X01_x01scs01l538` |
| VM | | `{PREFIX}_{COMPUTER-NAME}` | |
| 디스크 | | `{PREFIX}_{VM_NAME}-{disk_type}{counter}` | `{VM-NAME}-sap00`, `{VM-NAME}-data00`, `{VM-NAME}-log00`, `{VM-NAME}-backup00` |
| OS 디스크 | | `{PREFIX}_{VM_NAME}-osDisk` | `DEV-WEEU-SAP01-X01_x01scs00lxxx-OsDisk` |
| Azure 부하 분산 장치 (유틸리티)| 80 | `{PREFIX}_db-alb` | `DEV-WEEU-SAP01-X01_db-alb` |
| 부하 분산 장치 프런트 엔드 IP 주소 (유틸리티)| | `{PREFIX}_dbAlb-feip` | `DEV-WEEU-SAP01-X01_dbAlb-feip` |
| 부하 분산 장치 백 엔드 풀 (유틸리티)| | `{PREFIX}_dbAlb-bePool` | `DEV-WEEU-SAP01-X01_dbAlb-bePool` |
| 부하 분산 장치 상태 프로브 (유틸리티)| | `{PREFIX}_dbAlb-hp` | `DEV-WEEU-SAP01-X01_dbAlb-hp`|
| 키 자격 증명 모음 (사용자) | 24 | `{SHORTPREFIX}u{RND}` | `DEVWEEUSAP01uX01xxx` |
| NetApp volume (유틸리티) | 24 | `{PREFIX}-utility` | `DEV-WEEU-SAP01-X01_sapmnt` |



> [!NOTE]
> 디스크 번호는 0부터 시작 합니다. 명명 규칙은 두 문자 형식을 사용 합니다. 예를 들면 `00` 입니다.

## <a name="azure-region-names"></a>Azure 지역 이름

자동화 프레임 워크는 간단한 형식의 Azure 지역 이름을 사용 합니다. 짧은 Azure 지역 이름은 일반적인 지역 이름에 매핑됩니다.

`_region_mapping`이름 생성기의 구성 파일인에서 변수 아래에 매핑을 설정할 수 있습니다 `../../../deploy/terraform/terraform-units/modules/sap_namegenerator/variables_local.tf` .

그런 다음 `_region_mapping` 영역 경로와 같은 다른 곳에서 변수를 사용할 수 있습니다. 영역 경로의 형식은 `{ENVIRONMENT}-{REGION_MAP}-{SAP_VNET}-{ARTIFACT}` 다음과 같습니다.

- `{ENVIRONMENT}` 환경 또는 작업 영역 이름입니다.
- `{REGION_MAP}` 는 Azure 지역 이름의 약식 형식입니다.
- `{SAP_VNET}` 는 환경 내의 SAP 가상 네트워크입니다.
- `{ARTIFACT}` 는 가상 네트워크 내의 배포 아티팩트 (예:)입니다 `INFRASTRUCTURE` .

다음과 같이 변수를 사용할 수 있습니다 `_region_mapping` .

```
"${upper(var.__environment)}-${upper(element(split(",", lookup(var.__region_mapping, var.__region, "-,unknown")),1))}-${upper(var.__SAP_VNET)}-INFRASTRUCTURE"
```

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [사용자 지정 명명 모듈을 구성 하는 방법을 알아봅니다.](automation-naming-module.md)
