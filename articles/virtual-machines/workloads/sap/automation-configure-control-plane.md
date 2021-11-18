---
title: 자동화 프레임워크에 대한 컨트롤 플레인 구성
description: Azure에서 SAP 배포 자동화 프레임워크에 대한 배포 제어 평면을 구성합니다.
author: kimforss
ms.author: kimforss
ms.reviewer: kimforss
ms.date: 11/17/2021
ms.topic: conceptual
ms.service: virtual-machines-sap
ms.openlocfilehash: f44a3c7ec4d6f46dcfaf238200a9284e1f7d4efa
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/17/2021
ms.locfileid: "132730423"
---
# <a name="configure-the-control-plane"></a>컨트롤 플레인 구성

[Azure의 SAP 배포 자동화 프레임워크에](automation-deployment-framework.md) 대한 제어 평면은 다음 구성 요소로 구성됩니다.
 - 배포자
 - SAP 라이브러리

:::image type="content" source="./media/automation-deployment-framework/control-plane.png" alt-text="다이어그램 컨트롤 플레인.":::

## <a name="deployer"></a>배포자

[배포자는](automation-deployment-framework.md#deployment-components) [SAP 자동화 프레임워크](automation-deployment-framework.md)의 실행 엔진입니다. Terraform 및 Ansible 명령을 실행하는 데 사용되는 미리 구성된 VM(가상 머신)입니다. 

배포자의 구성은 Terraform tfvars 변수 파일에서 수행됩니다.

## <a name="terraform-parameters"></a>Terraform 매개 변수

아래 표에는 Terraform 매개 변수가 포함되어 있습니다. 배포 스크립트를 사용하지 않는 경우 이러한 매개 변수를 수동으로 입력해야 합니다.

> [!div class="mx-tdCol2BreakAll "]
> | 변수                | 설명                           | 형식       |
> | ----------------------- | ------------------------------------- | ---------- | 
> | `tfstate_resource_id`   | Terraform 상태 파일이 포함된 SAP 라이브러리의 스토리지 계정에 대한 Azure 리소스 식별자 | 필수   | 


### <a name="generic-parameters"></a>제네릭 매개 변수

아래 표에는 리소스 그룹 및 리소스 이름을 정의하는 매개 변수가 포함되어 있습니다.

> [!div class="mx-tdCol2BreakAll "]
> | 변수                | 설명                           | 형식       | 
> | ----------------------- | ------------------------------------- | ---------- | 
> | `environment`           | 작업 영역에 대한 5자 식별자입니다. 예를 들어 `PROD` 프로덕션 환경 및 `NP` 비프로덕션 환경의 경우 입니다. | 필수 |
> | `location`              | 배포할 Azure 지역입니다.     | 필수   | 
> | `resource_group_name`   | 만들 리소스 그룹의 이름 | 선택 사항   |
> | `resource_group_arm_id` | 기존 리소스 그룹에 대한 Azure 리소스 식별자 | 선택 사항   | 

### <a name="network-parameters"></a>네트워크 매개 변수

자동화 프레임워크는 가상 네트워크와 서브넷(녹색 필드)을 만들거나 기존 가상 네트워크와 기존 서브넷(브라운 필드) 또는 녹색 필드와 브라운 필드의 조합을 사용할 수 있습니다.
 - 녹색 필드 시나리오의 경우 가상 네트워크 주소 공간 및 서브넷 주소 접두사를 지정해야 합니다. 
 - 브라운 필드 시나리오의 경우 가상 네트워크 및 서브넷에 대한 Azure 리소스 식별자를 지정해야 합니다.

가상 네트워크 주소 공간의 권장 CIDR은 /27이며, 32개의 IP 주소를 위한 공간을 허용합니다. CIDR 값 /28은 16개의 IP 주소만 허용합니다. Azure Firewall 포함하려면 /25의 CIDR 값을 사용합니다. Azure Firewall /26 범위가 필요하기 때문입니다. 

관리 서브넷에 권장되는 CIDR 값은 16개의 IP 주소를 허용하는 /28입니다.
방화벽 서브넷에 권장되는 CIDR 값은 64개의 IP 주소를 허용하는 /26입니다.

아래 표에는 네트워킹 매개 변수가 포함되어 있습니다.

> [!div class="mx-tdCol2BreakAll "]
> | 변수                                    | 설명                                                      | 형식       | 참고  |
> | ------------------------------------------  | ---------------------------------------------------------------- | ---------- | ------ |
> | `management_network_name`                   | 네트워크의 논리적 이름(DEV-WEEU-MGMT01-INFRASTRUCTURE) | 필수   | |
> | `management_network_arm_id`                 | 가상 네트워크에 대한 Azure 리소스 식별자            | 선택 사항   | 기존 환경 배포의 경우 |
> | `management_network_address_space`          | 가상 네트워크의 주소 범위                        | 필수  | 새 환경 배포의 경우 |
> | `management_subnet_name`                    | 서브넷의 이름                                           | 선택 사항   | |
> | `management_subnet_address_prefix`          | 서브넷의 주소 범위                                 | 필수  | 새 환경 배포의 경우 |
> | `management_subnet_arm_id`                  | 서브넷에 대한 Azure 리소스 식별자                     | 필수  | 기존 환경 배포의 경우 |
> | `management_subnet_nsg_name`                | 네트워크 보안 그룹 이름의 이름                      | 선택 사항   | |
> | `management_subnet_nsg_arm_id`              | 네트워크 보안 그룹에 대한 Azure 리소스 식별자     | 필수  | 기존 환경 배포에 필수 |
> | `management_subnet_nsg_allowed_ips`         | Azure Firewall 추가할 수 있는 IP 주소 범위           | 선택 사항   | |  
> | `management_firewall_subnet_arm_id`         | 네트워크 보안 그룹에 대한 Azure 리소스 식별자     | 필수  | 기존 환경 배포의 경우 |
> | `management_firewall_subnet_address_prefix` | 서브넷의 주소 범위                                 | 필수  | 새 환경 배포의 경우 | 

### <a name="deployer-virtual-machine-parameters"></a>배포자 가상 머신 매개 변수

아래 표에는 배포자 가상 머신과 관련된 매개 변수가 포함되어 있습니다. 

> [!div class="mx-tdCol2BreakAll "]
> | 변수                        | 설명                                                                  | 형식       |
> | ------------------------------- | ---------------------------------------------------------------------------- | ---------- |
> | `deployer_size`                 | 사용할 가상 머신 SKU를 정의합니다(예: Standard_D4s_v3        | 선택 사항   |
> | `deployer_image`                | 사용할 가상 머신 이미지를 정의합니다. 아래를 참조하세요.                          | 선택 사항   |
> | `deployer_disk_type`            | 디스크 유형을 정의합니다(예: Premium_LRS                               | 선택 사항   |
> | `deployer_use_DHCP`             | Azure 서브넷에서 제공한 IP 주소를 사용해야 하는지(동적) true를 제어합니다. | 선택 사항   |
> | `deployer_private_ip_address`   | 사용할 개인 IP 주소를 정의합니다.                                        | 선택 사항   |
> | `deployer_enable_public_ip`     | 배포자가 공용 IP를 갖는 경우 정의됨                                      | 선택 사항   |

Virtual Machine 이미지는 다음 구조를 사용하여 정의됩니다. 
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

아래 표는 Virtual Machine 인증을 정의하는 데 사용되는 매개 변수를 정의합니다.


> [!div class="mx-tdCol2BreakAll "]
> | 변수                                         | 설명                                         | 형식      | 
> | ------------------------------------------------ | --------------------------------------------------- | --------- | 
> | `deployer_vm_authentication_type`                | 배포자의 기본 인증을 정의합니다. | 선택 사항  |
> | `deployer_authentication_username`               | 관리자 계정 이름                          | 선택 사항  |
> | `deployer_authentication_password`               | 관리자 암호입니다.                              | 선택 사항  |
> | `deployer_authentication_path_to_public_key`     | 인증에 사용되는 공개 키의 경로      | 선택 사항  |
> | `deployer_authentication_path_to_private_key`    | 인증에 사용되는 프라이빗 키의 경로     | 선택 사항  |

### <a name="key-vault-parameters"></a>Key Vault 매개 변수

아래 표는 Key Vault 정보를 정의하는 데 사용되는 매개 변수를 정의합니다.

> [!div class="mx-tdCol2BreakAll "]
> | 변수                             | 설명                                                                 | 형식       | 
> | ------------------------------------ | --------------------------------------------------------------------------- | ---------- | 
> | `user_keyvault_id`                   | 사용자 키 자격 증명 모음에 대한 Azure 리소스 식별자                            | 선택 사항   |
> | `spn_keyvault_id`                    | SPN 세부 정보를 포함하는 사용자 키 자격 증명 모음에 대한 Azure 리소스 식별자 | 선택 사항   |
> | `deployer_private_key_secret_name`   | 배포자 프라이빗 키의 Azure Key Vault 비밀 이름                | 선택 사항   |
> | `deployer_public_key_secret_name`    | 배포자 공개 키의 Azure Key Vault 비밀 이름                 | 선택 사항   |
> | `deployer_username_secret_name`      | 배포자 사용자 이름의 Azure Key Vault 비밀 이름                   | 선택 사항   |
> | `deployer_password_secret_name`      | 배포자 암호의 Azure Key Vault 비밀 이름                   | 선택 사항   |

### <a name="other-parameters"></a>다른 매개 변수

> [!div class="mx-tdCol2BreakAll "]
> | 변수                             | 설명                           | 형식        | 
> | ------------------------------------ | ------------------------------------- | ----------- | 
> | `firewall_deployment`                | Azure Firewall을 배포할 것인지를 제어하는 부울 플래그 | 필수   | 
> | `enable_purge_control_for_keyvaults` | Key Vault 제거 컨트롤이 사용되는지 제어하는 부울 플래그입니다. 테스트 배포에만 사용 | 선택 사항    | 
> | `use_private_endpoint`               | 프라이빗 엔드포인트가 사용되는지 제어하는 부울 플래그입니다. | 선택 사항    | 

### <a name="example-parameters-file-for-deployer-required-parameters-only"></a>배포자용 예제 매개 변수 파일(필수 매개 변수만 해당)

```bash
# The environment value is a mandatory field, it is used for partitioning the environments, for example (PROD and NP)
environment="MGMT"
# The location/region value is a mandatory field, it is used to control where the resources are deployed
location="westeurope"

# management_network_address_space is the address space for management virtual network
management_network_address_space="10.10.20.0/25"
# management_subnet_address_prefix is the address prefix for the management subnet
management_subnet_address_prefix="10.10.20.64/28"
# management_firewall_subnet_address_prefix is the address prefix for the firewall subnet
management_firewall_subnet_address_prefix="10.10.20.0/26"

deployer_enable_public_ip=true
firewall_deployment=true
```


## <a name="sap-library"></a>SAP 라이브러리

[SAP 라이브러리는](automation-deployment-framework.md#deployment-components) Terraform 상태 파일의 영구 스토리지와 컨트롤 플레인에 대해 다운로드한 SAP 설치 미디어를 제공합니다. 

SAP 라이브러리의 구성은 Terraform tfvars 변수 파일에서 수행됩니다.

### <a name="terraform-parameters"></a>Terraform 매개 변수

아래 표에는 Terraform 매개 변수가 포함되어 있습니다. 배포 스크립트를 사용하지 않을 때 이러한 매개 변수를 수동으로 입력해야 합니다.

> [!div class="mx-tdCol2BreakAll "]
> | 변수                | 설명                           | 형식       | 
> | ----------------------- | ------------------------------------- | ---------- | 
> | `deployer_tfstate_key`  | 배포자의 상태 파일 이름  | 필수   | 

### <a name="generic-parameters"></a>제네릭 매개 변수

아래 표에는 리소스 그룹 및 리소스 이름을 정의하는 매개 변수가 포함되어 있습니다.


> [!div class="mx-tdCol2BreakAll "]
> | 변수                | 설명                           | 형식       |
> | ----------------------- | ------------------------------------- | ---------- |
> | `environment`           | 작업 영역에 대한 5자 식별자입니다. 예를 들어 `PROD` 프로덕션 환경 및 `NP` 비프로덕션 환경의 경우 입니다. | 필수 |
> | `location`              | 배포할 Azure 지역입니다.     | 필수   | 
> | `resource_group_name`   | 만들 리소스 그룹의 이름 | 선택 사항   |
> | `resource_group_arm_id` | 기존 리소스 그룹에 대한 Azure 리소스 식별자 | 선택 사항   | 

### <a name="deployer-parameters"></a>배포자 매개 변수

아래 표에는 리소스 그룹 및 리소스 이름을 정의하는 매개 변수가 포함되어 있습니다.

> [!div class="mx-tdCol2BreakAll "]
> | 변수               | 설명                           | 형식 |
> | ---------------------  | ------------------------------------- | ----- | 
> | `deployer_environment` | 작업 영역에 대한 5자 식별자입니다. 예를 들어 `PROD` 프로덕션 환경 및 `NP` 비프로덕션 환경의 경우 입니다. | 필수 |
> | `deployer_location`    | 배포할 Azure 지역입니다.   | 필수 |
> | `deployer_vnet`        | 배포자 VNet의 논리적 이름 | 필수 |


### <a name="sap-installation-media-storage-account"></a>SAP 설치 미디어 스토리지 계정

> [!div class="mx-tdCol2BreakAll "]
> | 변수                  | 설명                 | 형식       | 
> | ------------------------- | --------------------------- | ---------- | 
> | `library_sapmedia_arm_id` | Azure 리소스 식별자   | 선택 사항   |

### <a name="terraform-remote-state-storage-account"></a>Terraform 원격 상태 스토리지 계정

> [!div class="mx-tdCol2BreakAll "]
> | 변수                         | 설명                | 형식       | 
> | -------------------------------- | -------------------------- | ---------- | 
> | `library_terraform_state_arm_id` | Azure 리소스 식별자  | 선택 사항   |

### <a name="extra-parameters"></a>extras 매개 변수


> [!div class="mx-tdCol2BreakAll "]
> | 변수                           | 설명                      | 형식     | 
> | ---------------------------------- | -------------------------------- | -------- | 
> | `dns_label`                        | 프라이빗 DNS 영역의 DNS 이름 | 선택 사항 | 
> | `use_private_endpoint`             | 프라이빗 엔드포인트 사용            | 선택 사항 | 

### <a name="example-parameters-file-for-sap-library-required-parameters-only"></a>sap 라이브러리에 대한 예제 매개 변수 파일(필수 매개 변수만 해당)

```bash
# The environment value is a mandatory field, it is used for partitioning the environments, for example (PROD and NP)
environment="MGMT"
# The location/region value is a mandatory field, it is used to control where the resources are deployed
location="westeurope"
# The deployer_environment value is a mandatory field, it is used for identifying the deployer
deployer_environment="MGMT"
# The deployer_location value is a mandatory field, it is used for identifying the deployer
deployer_location="westeurope"
# The deployer_vnet value is a mandatory field, it is used for identifying the deployer
deployer_vnet="DEP00"

```


## <a name="next-step"></a>다음 단계

> [!div class="nextstepaction"]
> [SAP 시스템 구성](automation-configure-system.md)
