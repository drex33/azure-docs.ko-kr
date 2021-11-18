---
title: 자동화 프레임 워크의 작업 영역 구성 정보
description: Azure의 SAP 배포 자동화 프레임 워크 내에서 SAP 작업 영역 구성 프로세스의 개요입니다.
author: kimforss
ms.author: kimforss
ms.reviewer: kimforss
ms.date: 11/17/2021
ms.topic: conceptual
ms.service: virtual-machines-sap
ms.openlocfilehash: 4fd67edf5f6668efa26fd3c6c6f4adf7a3707c24
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/17/2021
ms.locfileid: "132730454"
---
# <a name="workload-zone-configuration-in-sap-automation-framework"></a>SAP automation framework의 작업 영역 구성

[SAP 응용 프로그램](automation-deployment-framework.md#sap-concepts) 에는 일반적으로 여러 개발 계층이 있습니다. 예를 들어 개발, 품질 보증 및 프로덕션 계층이 있을 수 있습니다. [Azure의 SAP 배포 자동화 프레임 워크](automation-deployment-framework.md) 는 이러한 계층을 [작업 영역](automation-deployment-framework.md#deployment-components)으로 참조 합니다.

## <a name="workload-zone-deployment-configuration"></a>작업 영역 배포 구성

SAP 워크 로드 영역의 구성은 Terraform tfvars 변수 파일을 통해 수행 됩니다.

## <a name="terraform-parameters"></a>Terraform 매개 변수

아래 표에는 Terraform 매개 변수가 포함 되어 있습니다. 이러한 매개 변수는 배포 스크립트를 사용 하지 않는 경우 수동으로 입력 해야 합니다.


| 변수                | Type       | 설명                           | 
| ----------------------- | ---------- | ------------------------------------- | 
| `tfstate_resource_id`   | 하다 | terraform 상태 파일을 포함 하는 SAP 라이브러리의 Storage 계정에 대 한 Azure 리소스 식별자입니다.  |
| `deployer_tfstate_key`  | 하다 | 배포자에 대 한 상태 파일의 이름입니다.  |

## <a name="generic-parameters"></a>제네릭 매개 변수

아래 표에는 리소스 그룹 및 리소스 명명을 정의 하는 매개 변수가 포함 되어 있습니다.


> [!div class="mx-tdCol2BreakAll "]
> | 변수                | 설명                           | 형식  |
> | ----------------------- | ------------------------------------- | ----- |
> | `environment`           | 작업 영역에 대 한 5 자로 된 식별자입니다. 예를 들어 프로덕션 `PROD` 환경 및 비프로덕션 `NP` 환경의 경우입니다.| 필수 |
> | `location`              | 배포할 Azure 지역입니다.     | 필수 |
> | `resource_group_name`   | 만들 리소스 그룹의 이름입니다. | 선택 사항 |
> | `resource_group_arm_id` | 기존 리소스 그룹에 대 한 Azure 리소스 식별자 | 선택 사항 |


## <a name="network-parameters"></a>네트워크 매개 변수

자동화 프레임 워크는 새 환경 배포를 위한 가상 네트워크 및 서브넷 만들기 (녹색 필드) 또는 기존 환경 배포에 대 한 기존 가상 네트워크 및 기존 가상 네트워크 및 기존 환경 배포에 대 한 기존 서브넷 (갈색 필드) 및 새 환경 배포를 위한 조합, 기존 환경 배포를 모두 지원 합니다.
 - 녹색 필드 시나리오의 경우 가상 네트워크 주소 공간 및 서브넷 주소 접두사를 지정 해야 합니다. 
 - 갈색 필드 시나리오의 경우 가상 네트워크 및 서브넷에 대 한 Azure 리소스 식별자를 지정 해야 합니다.

가상 네트워크 주소 공간이 모든 리소스를 호스트 하기에 충분히 큰지 확인 합니다.

아래 표에는 네트워킹 매개 변수가 포함 되어 있습니다.

> [!div class="mx-tdCol2BreakAll "]
> | 변수                         | 설명                                                          | 형식      | 참고  |
> | -------------------------------- | -------------------------------------------------------------------- | --------- | ------ |
> | `network_name`                   | 네트워크의 논리적 이름                                      | 필수  |        |       
> | `network_arm_id`                 | 가상 네트워크에 대 한 Azure 리소스 식별자입니다.                | 선택 사항  | 기존 환경 배포의 경우 |
> | `network_address_space`          | 가상 네트워크의 주소 범위입니다.                            | 필수 | 새 환경 배포의 경우   |
> | `admin_subnet_name`              | 서브넷의 이름입니다. `admin`                                       | 선택 사항  |         |
> | `admin_subnet_address_prefix`    | 서브넷의 주소 범위입니다. `admin`                             | 필수 | 새 환경 배포의 경우  |
> | `admin_subnet_arm_id`            | 서브넷에 대 한 Azure 리소스 식별자입니다. `admin`                 | 필수 | 기존 환경 배포의 경우 |
> | `admin_subnet_nsg_name`          | `admin`네트워크 보안 그룹 이름 이름                  | 선택 사항   |         |
> | `admin_subnet_nsg_arm_id`        | 네트워크 보안 그룹에 대 한 Azure 리소스 식별자입니다. `admin` | 필수 | 기존 환경 배포의 경우 |
> | `db_subnet_name`                 | 서브넷의 이름입니다. `db`                                          | 선택 사항  |         |
> | `db_subnet_address_prefix`       | 서브넷의 주소 범위입니다. `db`                                | 필수 | 새 환경 배포의 경우  |
> | `db_subnet_arm_id`               | 서브넷에 대 한 Azure 리소스 식별자입니다. `db`                    | 필수 | 기존 환경 배포의 경우 |
> | `db_subnet_nsg_name`             | `db`네트워크 보안 그룹 이름 이름                     | 선택 사항  |          |
> | `db_subnet_nsg_arm_id`           | 네트워크 보안 그룹에 대 한 Azure 리소스 식별자입니다. `db`    | 필수 | 기존 환경 배포의 경우 |
> | `app_subnet_name`                | 서브넷의 이름입니다. `app`                                         | 선택 사항  |          |
> | `app_subnet_address_prefix`      | 서브넷의 주소 범위입니다. `app`                               | 필수 | 새 환경 배포의 경우  |
> | `app_subnet_arm_id`              | 서브넷에 대 한 Azure 리소스 식별자입니다. `app`                   | 필수 | 기존 환경 배포의 경우 |
> | `app_subnet_nsg_name`            | `app`네트워크 보안 그룹 이름 이름                    | 선택 사항  |          |
> | `app_subnet_nsg_arm_id`          | 네트워크 보안 그룹에 대 한 Azure 리소스 식별자입니다. `app`   | 필수 | 기존 환경 배포의 경우 |
> | `web_subnet_name`                | 서브넷의 이름입니다. `web`                                         | 선택 사항  |          |
> | `web_subnet_address_prefix`      | 서브넷의 주소 범위입니다. `web`                               | 필수 | 새 환경 배포의 경우  |
> | `web_subnet_arm_id`              | 서브넷에 대 한 Azure 리소스 식별자입니다. `web`                   | 필수 | 기존 환경 배포의 경우 |
> | `web_subnet_nsg_name`            | `web`네트워크 보안 그룹 이름 이름                    | 선택 사항  |          |
> | `web_subnet_nsg_arm_id`          | 네트워크 보안 그룹에 대 한 Azure 리소스 식별자입니다. `web`   | 필수 | 기존 환경 배포의 경우 |

## <a name="iscsi-parameters"></a>ISCSI 매개 변수


> [!div class="mx-tdCol2BreakAll "]
> | 변수                         | 설명                                                               | 형식      | 참고                                  |
> | -------------------------------- | ------------------------------------------------------------------------- | --------- | -------------------------------------- |
> | `iscsi_subnet_name`              | 서브넷의 이름입니다. `iscsi`                                            | 선택 사항  |                                        |
> | `iscsi_subnet_address_prefix`    | 서브넷의 주소 범위입니다. `iscsi`                                  | 필수 | 새 환경 배포의 경우        |
> | `iscsi_subnet_arm_id`            | 서브넷에 대 한 Azure 리소스 식별자입니다. `iscsi`                      | 필수 | 기존 환경 배포의 경우   |
> | `iscsi_subnet_nsg_name`          |  `iscsi`네트워크 보안 그룹 이름 이름                      | 선택 사항  |                                        |
> | `iscsi_subnet_nsg_arm_id`        | 네트워크 보안 그룹에 대 한 Azure 리소스 식별자입니다. `iscsi`      | 필수 | 기존 환경 배포의 경우   |
> | `iscsi_count`                    | ISCSI Virtual Machines 수                                      | 선택 사항  |                                        |   
> | `iscsi_use_DHCP`                 | Azure 서브넷에서 제공 하는 동적 IP 주소를 사용할지 여부를 제어 합니다. | 선택 사항  |                                        |
> | `iscsi_image`                    | 사용할 가상 컴퓨터 이미지를 정의 합니다. 아래를 참조 하십시오.                       | 선택 사항  |                                        |
> | `iscsi_authentication_type`      | ISCSI Virtual Machines에 대 한 기본 인증을 정의 합니다.         | 선택 사항  |                                        |
> | `iscsi__authentication_username` | 관리자 계정 이름                                                | 선택 사항  |                                        |
> | `iscsi_nic_ips`                  | ISCSI Virtual Machines에 대 한 IP 주소                               | 선택 사항  | 가 정의 된 경우 무시 됩니다. `iscsi_use_DHCP` |
 


```python 
{ 
os_type=""
source_image_id=""
publisher="SUSE"
offer="sles-sap-12-sp5"
sku="gen1"
version="latest"
}
```

### <a name="authentication-parameters"></a>인증 매개 변수


다음 표에서는 가상 컴퓨터 인증을 정의 하는 데 사용 되는 자격 증명을 정의 합니다.

> [!div class="mx-tdCol2BreakAll "]
> | 변수                           | 설명                          | 형식        | 
> | ---------------------------------- | -------------------------------------| ----------- | 
> | `automation_username`              | 관리자 계정 이름           | 선택 사항    |
> | `automation_password`              | 관리자 암호입니다.               | 선택 사항    |
> | `automation_path_to_public_key`    | 기존 공개 키 경로          | 선택 사항    |
> | `automation_path_to_private_key`   | 기존 개인 키의 경로         | 선택 사항    |


## <a name="key-vault-parameters"></a>Key Vault 매개 변수

다음 표에서는 Key Vault 정보를 정의 하는 데 사용 되는 매개 변수를 정의 합니다.


> [!div class="mx-tdCol2BreakAll "]
> | 변수                           | 설명                                                               | 형식          | 
> | ---------------------------------- | ------------------------------------------------------------------------- | ------------- | 
> | `user_keyvault_id`                 | 시스템 자격 증명 키 자격 증명 모음에 대 한 Azure 리소스 식별자            | 선택 사항      | 
> | `spn_keyvault_id`                  | Spn (배포 자격 증명) 키 자격 증명 모음에 대 한 Azure 리소스 식별자 | 선택 사항      | 


## <a name="dns"></a>DNS


> [!div class="mx-tdCol2BreakAll "]
> | 변수                           | 설명                                                    | 형식        | 
> | ---------------------------------- | -------------------------------------------------------------- | ----------- | 
> | `dns_label`                        | 지정 된 경우은 개인 DNS 영역의 DNS 이름입니다.          | 선택 사항    | 
> | `dns_resource_group_name`          | 사설 DNS 영역을 포함 하는 리소스 그룹의 이름입니다. | 선택 사항    | 


## <a name="azure-netapp-support"></a>Azure NetApp 지원


> [!div class="mx-tdCol2BreakAll "]
> | 변수                           | 설명                                                            | 형식         | 참고  |
> | ---------------------------------- | -----------------------------------------------------------------------| -----------  | ------ |
> | `use_ANF`                          | 지정 하는 경우 Azure NetApp Files 계정 및 용량 풀을 배포 합니다. | 선택 사항     | |
> | `ANF_account_arm_id`               | Azure NetApp Files 계정에 대 한 Azure 리소스 식별자입니다.           | 선택 사항     | 기존 환경 배포의 경우 |
> | `ANF_account_name`                 | Azure NetApp Files 계정의 이름입니다.                                | 선택 사항     | |
> | `ANF_service_level`                | Azure NetApp Files 용량 풀의 서비스 수준                 | 선택 사항     | |
> | `ANF_pool_size`                    | Azure NetApp Files 용량 풀의 크기 (GB)               | 선택 사항     | |
> | `anf_subnet_name`                  | ANF 서브넷의 이름입니다.                                             | 선택 사항     | |
> | `anf_subnet_arm_id`                | 서브넷에 대 한 Azure 리소스 식별자입니다. `ANF`                     | 필수     | 기존 환경 배포의 경우 |
> | `anf_subnet_address_prefix`        | 서브넷의 주소 범위입니다. `ANF`                                 | 필수     | 새 환경 배포의 경우  |


## <a name="other-parameters"></a>기타 매개 변수


> [!div class="mx-tdCol2BreakAll "]
> | 변수                             | 설명                                                            | 형식     | 참고                                 |
> | ------------------------------------ | ---------------------------------------------------------------------- | -------- | ------------------------------------- |
> | `enable_purge_control_for_keyvaults` | Key Vault에서 제거 컨트롤을 사용할 수 있는지 여부를 제어 하는 부울 플래그입니다. | 선택 사항 | 테스트 배포에만 사용         |
> | `use_private_endpoint`               | 개인 끝점이 사용 되는 경우를 제어 하는 부울 플래그입니다.                 | 선택 사항 |                                       |
> | `diagnostics_storage_account_arm_id` | 진단 저장소 계정에 대 한 Azure 리소스 식별자입니다.      | 필수 | 기존 환경 배포의 경우  |
> | `witness_storage_account_arm_id`     | 감시 저장소 계정에 대 한 Azure 리소스 식별자입니다.          | 필수 | 기존 환경 배포의 경우  |


## <a name="next-step"></a>다음 단계

> [!div class="nextstepaction"]
> [자동화 프레임워크의 SAP 시스템 배포 정보](automation-deploy-workload-zone.md)
