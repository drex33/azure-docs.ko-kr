---
title: 자동화를 위한 SAP 시스템 매개 변수 구성
description: 매개 변수 JSON 파일을 사용하여 Azure에서 SAP 배포 자동화 프레임워크에 대한 SAP 시스템 속성을 정의합니다.
author: kimforss
ms.author: kimforss
ms.reviewer: kimforss
ms.date: 11/17/2021
ms.topic: conceptual
ms.service: virtual-machines-sap
ms.openlocfilehash: 6b18d33ce3b05809a6dfeb61b3f8337c4d0701cb
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/17/2021
ms.locfileid: "132730422"
---
# <a name="configure-sap-system-parameters-for-automation"></a>자동화를 위한 SAP 시스템 매개 변수 구성

Azure ]에서 [SAP 배포 자동화 프레임워크에 대한](automation-deployment-framework.md)구성은 매개 변수 파일을 통해 발생합니다. 자동화 프레임워크가 배포에 사용하는 tfvars 파일에서 SAP 시스템 속성에 대한 정보를 제공합니다. 

SAP 워크로드 영역의 구성은 Terraform tfvars 변수 파일을 통해 수행됩니다.

## <a name="terraform-parameters"></a>Terraform 매개 변수

아래 표에는 Terraform 매개 변수가 포함되어 있습니다. 배포 스크립트를 사용하지 않는 경우 이러한 매개 변수를 수동으로 입력해야 합니다.


> [!div class="mx-tdCol2BreakAll "]
> | 변수                  | 설명                                                                                                      | 형식       |
> | ------------------------- | ---------------------------------------------------------------------------------------------------------------- | ---------- | 
> | `tfstate_resource_id`     | Terraform 상태 파일을 포함할 SAP 라이브러리의 Storage 계정에 대한 Azure 리소스 식별자 | 필수 * |
> | `deployer_tfstate_key`    | 배포자의 상태 파일 이름                                                                      | 필수 * |
> | `landscaper_tfstate_key`  | 작업 영역에 대한 상태 파일의 이름                                                                 | 필수 * |

\* = 수동 배포에 필요
## <a name="generic-parameters"></a>제네릭 매개 변수

아래 표에는 리소스 그룹 및 리소스 이름을 정의하는 매개 변수가 포함되어 있습니다.


> [!div class="mx-tdCol2BreakAll "]
> | 변수                | 설명                           | 형식       | 
> | ----------------------- | ------------------------------------- | ---------- | 
> | `environment`           | 작업 영역에 대한 5자 식별자입니다. 예를 들어 `PROD` 프로덕션 환경 및 `NP` 비프로덕션 환경의 경우 입니다. | 필수 |
> | `location`              | 배포할 Azure 지역입니다.     | 필수   | 
> | `resource_group_name`   | 만들 리소스 그룹의 이름 | 선택 사항   |
> | `resource_group_arm_id` | 기존 리소스 그룹에 대한 Azure 리소스 식별자 | 선택 사항   | 

## <a name="network-parameters"></a>네트워크 매개 변수

서브넷이 워크로드 영역 배포를 사용하여 배포되지 않은 경우 시스템의 tfvars 파일에 추가할 수 있습니다.

자동화 프레임워크는 새 환경 배포를 위한 가상 네트워크 및 서브넷 만들기(녹색 필드) 또는 기존 가상 네트워크 및 기존 서브넷을 기존 환경 배포(Brown 필드)에 사용하거나 새 환경 배포 및 기존 환경 배포의 경우 의 조합을 지원합니다.
 - 녹색 필드 시나리오의 경우 가상 네트워크 주소 공간 및 서브넷 주소 접두사를 지정해야 합니다. 
 - 브라운 필드 시나리오의 경우 가상 네트워크 및 서브넷에 대한 Azure 리소스 식별자를 지정해야 합니다.

가상 네트워크 주소 공간이 모든 리소스를 호스트할 수 있을 만큼 충분한지 확인합니다.

아래 표에는 네트워킹 매개 변수가 포함되어 있습니다.


> [!div class="mx-tdCol2BreakAll "]
> | 변수                         | 설명                                                          | 형식      | 참고  |
> | -------------------------------- | -------------------------------------------------------------------- | --------- | ------ |
> | `network_logical_name`           | 네트워크의 논리적 이름                                      | 필수  |        |       
> | `network_address_space`          | 가상 네트워크의 주소 범위                            | 필수 | 새 환경 배포의 경우   |
> | `admin_subnet_name`              | 'admin' 서브넷의 이름                                       | 선택 사항  |         |
> | `admin_subnet_address_prefix`    | 'admin' 서브넷의 주소 범위                             | 필수 | 새 환경 배포의 경우  |
> | `admin_subnet_arm_id`            | 'admin' 서브넷에 대한 Azure 리소스 식별자                 | 필수 | 기존 환경 배포의 경우 |
> | `admin_subnet_nsg_name`          | 'admin' 네트워크 보안 그룹 이름의 이름                  | 선택 사항  |         |
> | `admin_subnet_nsg_arm_id`        | 'admin' 네트워크 보안 그룹에 대한 Azure 리소스 식별자 | 필수 | 기존 환경 배포의 경우 |
> | `db_subnet_name`                 | 'db' 서브넷의 이름                                          | 선택 사항  |         |
> | `db_subnet_address_prefix`       | 'db' 서브넷의 주소 범위                                | 필수 | 새 환경 배포의 경우  |
> | `db_subnet_arm_id`               | 'db' 서브넷에 대한 Azure 리소스 식별자                    | 필수 | 기존 환경 배포의 경우 |
> | `db_subnet_nsg_name`             | 'db' 네트워크 보안 그룹 이름의 이름                     | 선택 사항  |          |
> | `db_subnet_nsg_arm_id`           | 'db' 네트워크 보안 그룹에 대한 Azure 리소스 식별자    | 필수 | 기존 환경 배포의 경우 |
> | `app_subnet_name`                | 'app' 서브넷의 이름                                         | 선택 사항  |          |
> | `app_subnet_address_prefix`      | '앱' 서브넷의 주소 범위                               | 필수 | 새 환경 배포의 경우  |
> | `app_subnet_arm_id`              | '앱' 서브넷에 대한 Azure 리소스 식별자                   | 필수 | 기존 환경 배포의 경우 |
> | `app_subnet_nsg_name`            | '앱' 네트워크 보안 그룹 이름의 이름                    | 선택 사항  |          |
> | `app_subnet_nsg_arm_id`          | '앱' 네트워크 보안 그룹에 대한 Azure 리소스 식별자   | 필수 | 기존 환경 배포의 경우 |
> | `web_subnet_name`                | '웹' 서브넷의 이름                                         | 선택 사항  |          |
> | `web_subnet_address_prefix`      | '웹' 서브넷의 주소 범위                               | 필수 | 새 환경 배포의 경우  |
> | `web_subnet_arm_id`              | '웹' 서브넷에 대한 Azure 리소스 식별자                   | 필수 | 기존 환경 배포의 경우 |
> | `web_subnet_nsg_name`            | '웹' 네트워크 보안 그룹 이름의 이름                    | 선택 사항  |          |
> | `web_subnet_nsg_arm_id`          | '웹' 네트워크 보안 그룹에 대한 Azure 리소스 식별자   | 필수 | 기존 환경 배포의 경우 |

\* = 기존 환경 배포 배포의 경우 필수

### <a name="database-tier-parameters"></a>데이터베이스 계층 매개 변수

데이터베이스 계층은 데이터베이스 계층에 대 한 인프라를 정의 하며, 지원 되는 데이터베이스 백 엔드는 다음과 같습니다.

- `HANA`
- `DB2`
- `ORACLE`
- `ORACLE-ASM`
- `ASE`
- `SQLSERVER`
- `NONE` (이 경우에는 데이터베이스 계층이 배포 되지 않습니다.)


> [!div class="mx-tdCol2BreakAll "]
> | 변수                          | 설명                                                                              | 형식         | 참고              |
> | --------------------------------  | -----------------------------------------------------------------------------------------| -----------  | ------------------ |
> | `database_platform`               | 데이터베이스 백 엔드를 정의 합니다.                                                             | 필수     |                    |
> | `database_high_availability`      | 데이터베이스 계층이 항상 사용 가능한 배포 되는지 정의 합니다.                                | 선택 사항     | 고가용성 [구성](automation-configure-system.md#high-availability-configuration) 을 참조 하세요. |
> | `database_server_count`           | 데이터베이스 서버 수를 정의 합니다.                                                   | 선택 사항     | 기본값은 1입니다. |
> | `database_vm_names`               | 기본 이름 지정이 허용 되지 않는 경우 데이터베이스 서버 가상 컴퓨터 이름을 정의 합니다. | 선택 사항    |                    |
> | `database_size`                   | 데이터베이스 크기 조정 정보를 정의 합니다.                                                  | 필수     | [사용자 지정 크기 조정](automation-configure-extra-disks.md) 참조 |
> | `database_sid`                    | 데이터베이스 SID를 정의 합니다.                                                                 | 필수     |                    |
> | `db_disk_sizes_filename`          | 사용자 지정 데이터베이스 크기 조정을 정의 합니다.                                                       | 선택 사항     | [사용자 지정 크기 조정](automation-configure-extra-disks.md) 참조 |
> | `database_sid`                    | 데이터베이스 SID를 정의 합니다.                                                                 | 필수     |                    |
> | `database_vm_image`               | 사용할 가상 컴퓨터 이미지를 정의 합니다. 아래를 참조 하십시오.                                      | 선택 사항     |                    |
> | `database_vm_use_DHCP`            | Azure 서브넷 제공 IP 주소를 사용 해야 하는지 여부를 제어 합니다 (동적).             | 선택 사항     |                    |
> | `database_vm_db_nic_ips`          | 데이터베이스 서버 (데이터베이스 서브넷)의 고정 IP 주소를 정의 합니다.               | 선택 사항     |                    |
> | `database_vm_admin_nic_ips`       | 데이터베이스 서버 (관리 서브넷)의 고정 IP 주소를 정의 합니다.                  | 선택 사항     |                    |
> | `database_vm_authentication_type` | 데이터베이스 가상 컴퓨터 (키/암호)의 인증 유형을 정의 합니다.         | 선택 사항     |                    | 
> | `database_vm_zones`               | 가용성 영역를 정의 합니다.                                                           | 선택 사항     |                    |
> | `database_vm_avset_arm_ids`       | 기존 가용성 집합 Azure 리소스 Id를 정의 합니다.                                | 선택 사항     | 주로 ANF 고정과 함께 사용 됩니다.| 
> | `database_no_avset`               | 가용성 집합 없이 데이터베이스 가상 머신을 배포할지 여부를 제어 합니다.         | 선택 사항     | 기본값은 false입니다.   |
> | `database_no_ppg`                 | 데이터베이스 서버가 근접 배치 그룹에 배치 되지 않을 지 여부를 제어 합니다.       | 선택 사항     | 기본값은 false입니다.   |

가상 컴퓨터 및 운영 체제 이미지는 다음 구조를 사용 하 여 정의 됩니다. 

```python 
{
  os_type="linux"
  source_image_id=""
  publisher="SUSE"
  offer="sles-sap-15-sp2"
  sku="gen2"
  version="8.2.2021040902"
}
```

### <a name="common-application-tier-parameters"></a>일반 응용 프로그램 계층 매개 변수

응용 프로그램 계층은 응용 프로그램 계층에 대 한 인프라를 정의 하며, 응용 프로그램 서버, 중앙 서비스 서버 및 웹 발송 서버로 구성 될 수 있습니다.


> [!div class="mx-tdCol2BreakAll "]
> | 변수                           | 설명                                                                 | 형식       | 참고  |
> | ---------------------------------- | --------------------------------------------------------------------------- | -----------| ------ |
> | `enable_app_tier_deployment`         | 응용 프로그램 계층을 배포할지 여부를 정의 합니다.                                 | 선택 사항     |        |
> | `sid`                                |  SAP 응용 프로그램 SID를 정의 합니다.                                            | 필수     |        | 
> | `app_disk_sizes_filename`            | 응용 프로그램 계층 서버에 대 한 사용자 지정 디스크 크기 파일을 정의 합니다.          | 선택 사항   | [사용자 지정 크기 조정](automation-configure-extra-disks.md) 참조 |
> | `app_tier_authentication_type`     | 응용 프로그램 계층 가상 컴퓨터에 대 한 인증 유형을 정의 합니다. | 선택 사항   |       |
> | `app_tier_use_DHCP`                | Azure 서브넷 제공 IP 주소를 사용 해야 하는지 여부를 제어 합니다 (동적).     | 선택 사항   |       |
> | `app_tier_dual_nics`                 | 응용 프로그램 계층 서버에 두 개의 네트워크 인터페이스가 있는지 여부를 정의 합니다.     | 선택 사항     |       |
> | `app_tier_vm_sizing`                 | Tha 응용 프로그램 계층 서버에 대 한 VM SKU 및 디스크 레이아웃을 정의 하는 조회 값 | 선택 사항 |

### <a name="application-server-parameters"></a>응용 프로그램 서버 매개 변수


> [!div class="mx-tdCol2BreakAll "]
> | 변수                               | 설명                                                                  | 형식       | 참고  |
> | -------------------------------------- | ---------------------------------------------------------------------------- | -----------| ------ |
> | `application_server_count`             | 응용 프로그램 서버 수를 정의 합니다.                                    | 필수     | |
> | `application_server_sku`                 | 사용할 가상 컴퓨터 SKU를 정의 합니다.                                       | 선택 사항   | | 
> | `application_server_image`             | 사용할 가상 컴퓨터 이미지를 정의 합니다.                                     | 필수   | | 
> | `application_server_zones`             | 응용 프로그램 서버를 배포할 가용성 영역을 정의 합니다. | 선택 사항   | | 
> | `application_server_app_nic_ips[]`     | 응용 프로그램 서버에 대 한 IP 주소 목록 (앱 서브넷)                 | 선택 사항   | 을 사용 하는 경우 무시 됩니다. `app_tier_use_DHCP` |
> | `application_server_app_admin_nic_ips` | 응용 프로그램 서버 (관리 서브넷)의 IP 주소 목록               | 선택 사항   | 을 사용 하는 경우 무시 됩니다. `app_tier_use_DHCP` |
> | `application_server_no_ppg`            | 응용 프로그램 서버 근접 배치 그룹 제어                        | 선택 사항   | |
> | `application_server_no_avset`            | 응용 프로그램 서버 가용성 집합 배치 제어                       | 선택 사항   | |
> | `application_server_tags`                | 응용 프로그램 서버에 적용할 태그 목록을 정의 합니다.              | 선택 사항   | |

### <a name="sap-central-services-parameters"></a>SAP Central Services 매개 변수


> [!div class="mx-tdCol2BreakAll "]
> | 변수                               | 설명                                                          | 형식      | 참고  |
> | -------------------------------------- | -------------------------------------------------------------------- | ----------| ------ |
> | `scs_high_availability`                | 중앙 서비스의 가용성을 높일 수 있는지 여부를 정의 합니다.                  | 선택 사항    | 고가용성 [구성](automation-configure-system.md#high-availability-configuration) 을 참조 하세요. |
> | `scs_instance_number`                    | SCS 인스턴스 번호                                           | 선택 사항  |        |
> | `ers_instance_number`                    | 인스턴스 수                                           | 선택 사항  |        |
> | `scs_server_count`                     | Scs 서버 수를 정의 합니다.                                    | 필수    |        |
> | `scs_server_sku`                         | 사용할 가상 컴퓨터 SKU를 정의 합니다.                               | 선택 사항  |        | 
> | `scs_server_image`                     | 사용할 가상 컴퓨터 이미지를 정의 합니다.                             | 필수  |        | 
> | `scs_server_zones`                     | Scs 서버를 배포할 가용성 영역을 정의 합니다. | 선택 사항  |        | 
> | `scs_server_app_nic_ips[]`             | Scs 서버 (앱 서브넷)의 IP 주소 목록                 | 선택 사항  | 을 사용 하는 경우 무시 됩니다. `app_tier_use_DHCP` |
> | `scs_server_app_admin_nic_ips`         | Scs 서버 (관리 서브넷)의 IP 주소 목록               | 선택 사항  | 을 사용 하는 경우 무시 됩니다. `app_tier_use_DHCP` |
> | `scs_server_no_ppg`                    | Scs 서버 근접 배치 그룹 제어                        | 선택 사항  |         |
> | `scs_server_no_avset`                    | Scs 서버 가용성 집합 배치 제어                       | 선택 사항  |         |
> | `scs_server_tags`                        | Scs 서버에 적용할 태그 목록을 정의 합니다.              | 선택 사항  |         |


### <a name="web-dispatcher-parameters"></a>웹 디스패처 매개 변수


> [!div class="mx-tdCol2BreakAll "]
> | 변수                                | 설명                                                              | 형식      | 참고  |
> | --------------------------------------- | ------------------------------------------------------------------------ | --------- | ------ |
> | `webdispatcher_server_count`              | 웹 디스패처 서버 수를 정의 합니다.                             | 필수  |        |
> | `webdispatcher_server_sku`              | 사용할 가상 컴퓨터 SKU를 정의 합니다.                                   | 선택 사항  |        | 
> | `webdispatcher_server_image`              | 사용할 가상 컴퓨터 이미지를 정의 합니다.                                 | 선택 사항  |        | 
> | `webdispatcher_server_zones`              | 웹 디스패처를 배포할 가용성 영역을 정의 합니다. | 선택 사항  |        |
> | `webdispatcher_server_app_nic_ips[]`    | 웹 디스패처 서버 (앱 서브넷)의 IP 주소 목록          | 선택 사항  | 을 사용 하는 경우 무시 됩니다. `app_tier_use_DHCP` |
> | `webdispatcher_server_app_admin_nic_ips`| 웹 디스패처 서버 (관리 서브넷)의 IP 주소 목록        | 선택 사항  | 을 사용 하는 경우 무시 됩니다. `app_tier_use_DHCP` |
> | `webdispatcher_server_no_ppg`           | 웹 근접 배치 그룹 배치를 제어 합니다.                         | 선택 사항  | |
> | `webdispatcher_server_no_avset`         | 웹 디스패처 가용성 집합 배치를 정의 합니다.                        | 선택 사항  | |
> | `webdispatcher_server_tags`             | 웹 디스패처 서버에 적용할 태그 목록을 정의 합니다.       | 선택 사항  | |

### <a name="anchor-virtual-machine-parameters"></a>가상 컴퓨터 매개 변수 앵커

SAP deployment automation 프레임 워크는 앵커 가상 머신을 지원 합니다. 앵커 가상 머신은 배포할 첫 번째 가상 머신이 되며, 근접 배치 그룹을 고정 하는 데 사용 됩니다.

아래 표에는 앵커 가상 머신과 관련 된 매개 변수가 포함 되어 있습니다. 


> [!div class="mx-tdCol2BreakAll "]
> | 변수                           | 설명                                                                       | 형식        | 
> | ---------------------------------- | --------------------------------------------------------------------------------- | ----------- | 
> | `deploy_anchor_vm`                 | 앵커 가상 머신을 사용 하는지 여부를 정의 합니다.                                     | 선택 사항      | 
> | `anchor_vm_sku`                    | 사용할 VM SKU를 정의 합니다. 예를 들어 Standard_D4s_v3 합니다.              | 선택 사항    | 
> | `anchor_vm_image`                    | 사용할 VM 이미지를 정의 합니다. 다음 코드 샘플을 참조 하세요.                              | 선택 사항     | 
> | `anchor_vm_use_DHCP`               | Azure 서브넷에서 제공 하는 동적 IP 주소를 사용할지 여부를 제어 합니다.           | 선택 사항    | 
> | `anchor_vm_accelerated_networking` | 앵커 VM이 가속화 된 네트워킹을 사용 하도록 구성 되어 있는지 여부를 정의 합니다. | 선택 사항    | 
> | `anchor_vm_authentication_type`    | 앵커 VM 키 및 암호의 인증 유형을 정의 합니다.       | 선택 사항       | 

가상 컴퓨터 및 운영 체제 이미지는 다음 구조를 사용 하 여 정의 됩니다. 
```python 
{ 
os_type=""
source_image_id=""
publisher="Canonical"
offer="0001-com-ubuntu-server-focal"
sku="20_04-lts"
version="latest"
}
```

### <a name="authentication-parameters"></a>인증 매개 변수

기본적으로 SAP 시스템 배포는 SAP 워크 로드 영역의 자격 증명을 사용 합니다. SAP 시스템에서 고유한 자격 증명을 요구 하는 경우 이러한 매개 변수를 사용 하 여 제공할 수 있습니다.


> [!div class="mx-tdCol2BreakAll "]
> | 변수                           | 설명                          | 형식        | 
> | ---------------------------------- | -------------------------------------| ----------- | 
> | `automation_username`              | 관리자 계정 이름           | 선택 사항    |
> | `automation_password`              | 관리자 암호입니다.               | 선택 사항    |
> | `automation_path_to_public_key`    | 기존 공개 키 경로          | 선택 사항    |
> | `automation_path_to_private_key`   | 기존 개인 키의 경로         | 선택 사항    |


## <a name="other-parameters"></a>기타 매개 변수


> [!div class="mx-tdCol2BreakAll "]
> | 변수                                       | 설명                           | 형식        | 
> | ---------------------------------------------- | ------------------------------------- | ----------- | 
> | `resource_offset`                              | 리소스 명명에 대 한 및 오프셋을 제공 합니다. 여러 리소스를 만들 때 리소스 이름에 대 한 오프셋 번호입니다. 기본값은 disk0, disk1 등의 명명 패턴을 만드는 0입니다. 오프셋 1은 disk1, disk2 등의 명명 패턴을 만듭니다. | 선택 사항    |
> | `disk_encryption_set_id`                       | 고객이 제공한 키를 사용 하 여 관리 디스크를 암호화 하는 데 사용할 디스크 암호화 키 | 선택 사항   |
> | `use_loadbalancers_for_standalone_deployments` | 독립 실행형 설치를 위해 부하 분산 장치를 배포할지 여부를 제어 합니다. | 선택 사항 |
> | `bom_name`                                     | 사용할 SAP BOM의 이름                                           | 선택 사항 |


## <a name="azure-netapp-support"></a>Azure NetApp 지원


> [!div class="mx-tdCol2BreakAll "]
> | 변수                           | 설명                                                             | 형식        |
> | ---------------------------------- | ----------------------------------------------------------------------- | ----------- |
> | `use_ANF`                          | 지정 하는 경우 Azure NetApp Files 계정 및 용량 풀을 배포 합니다.  | 선택 사항    |
> | `anf_sapmnt_volume_size`           | ' Sapmnt ' 볼륨의 크기 (GB)를 정의 합니다.                        | 선택 사항    |
> | `anf_transport_volume_size`        | ' Saptransport ' 볼륨의 크기 (GB)를 정의 합니다.                  | 선택 사항    |


## <a name="high-availability-configuration"></a>고가용성 구성

데이터베이스 계층 및 SCS 계층에 대 한 고가용성 구성은 및 플래그를 사용 하 여 구성 됩니다 `database_high_availability` `scs_high_availability` . 

고가용성 구성은 Azure 펜스 에이전트와 함께 Pacemaker를 사용 합니다. 펜스 에이전트는 가상 컴퓨터를 중지 하 고 시작할 수 있는 권한이 있는 고유한 서비스 주체를 사용 하도록 구성 해야 합니다. 자세한 내용은 [Fence 에이전트 만들기](high-availability-guide-suse-pacemaker.md#create-azure-fence-agent-stonith-device) 를 참조 하세요.

```azurecli-interactive
az ad sp create-for-rbac --role="Linux Fence Agent Role" --scopes="/subscriptions/<subscriptionID>" --name="<prefix>-Fencing-Agent"
```

를 `<prefix>` `DEV-WEEU-SAP01` `<subscriptionID>` 작업 영역 구독 ID와 같은 사용자 환경의 이름 접두사로 바꿉니다.
  
> [!IMPORTANT]
> 펜스 에이전트 서비스 사용자의 이름은 테 넌 트에서 고유 해야 합니다. 이 스크립트는 ' Linux Fence 에이전트 역할 ' 역할이 이미 만들어져 있다고 가정 합니다.
>
> 펜스 에이전트 SPN의 값을 기록 합니다.
   > - appId
   > - password
   > - tenant

펜스 에이전트 세부 정보는 미리 정의 된 명명 규칙을 사용 하 여 작업 영역 키 자격 증명 모음에 저장 해야 합니다. 를 `<prefix>` 사용자 환경의 이름 접두사 (예: `DEV-WEEU-SAP01` `<workload_kv_name>` 작업 영역 리소스 그룹의 키 자격 증명 모음 이름 및 기타 값)로 바꾸고는 이전 단계에서 기록 된 값을 사용 하 고 스크립트를 실행 합니다.


```azurecli

    ```azurecli-interactive
    az keyvault secret set --name "<prefix>-fencing-spn-id" --vault-name "<workload_kv_name>" --value "<appId>";
    az keyvault secret set --name "<prefix>-fencing-spn-pwd" --vault-name "<workload_kv_name>" --value "<password>";
    az keyvault secret set --name "<prefix>-fencing-spn-tenant" --vault-name "<workload_kv_name>" --value "<tenant>";
    ```
```

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [SAP 시스템 배포](automation-deploy-system.md)

