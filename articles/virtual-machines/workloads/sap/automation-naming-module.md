---
title: 자동화 프레임워크에 대한 사용자 지정 명명 모듈 구성
description: Azure에서 SAP 배포 자동화 프레임워크에 대한 사용자 지정 명명 규칙을 구현하는 방법에 대한 설명입니다.
author: kimforss
ms.author: kimforss
ms.reviewer: kimforss
ms.date: 11/17/2021
ms.topic: conceptual
ms.service: virtual-machines-sap
ms.openlocfilehash: bbb285bfef7e6fb380801c75144af4eebb256253
ms.sourcegitcommit: 1244a72dbec39ac8cf16bb1799d8c46bde749d47
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/18/2021
ms.locfileid: "132758835"
---
# <a name="configure-custom-naming-module"></a>사용자 지정 명명 모듈 구성

[Azure의 SAP 배포 자동화 프레임워크는 Azure](automation-deployment-framework.md) [리소스](automation-naming.md)명명 에 표준 명명 규칙을 사용합니다. 

Terraform 모듈은 `sap_namegenerator` 자동화 프레임워크가 배포하는 모든 리소스의 이름을 정의합니다. 모듈은 `/deploy/terraform/terraform-units/modules/sap_namegenerator/` 리포지토리의 에 있습니다. 

또한 프레임워크는 [매개 변수 파일](automation-configure-system.md)를 사용하여 일부 리소스에 대한 고유한 이름 제공을 지원합니다. 이러한 기능으로 충분하지 않은 경우 자동화에서 사용하는 명명 모듈을 수정하여 사용자 지정 명명 논리를 사용할 수도 있습니다.

모듈 내에 리소스 이름을 지정하기 위한 여러 파일이 있습니다.

- VM(가상 머신) 및 컴퓨터 이름은 ( )에 정의되어 있습니다. `vm.tf`
- 리소스 그룹 명명은 ( )에 정의되어 있습니다. `resourcegroup.tf`
- 의 키 자격 증명 모음( `keyvault.tf` )
- 리소스 접미사( `variables_local.tf` )

다른 리소스 이름은 Terraform 코드의 접두사로 식별됩니다.
- SAP 배포자 배포는 접두사로 리소스 이름을 사용합니다. `deployer_`
- SAP 라이브러리 배포는 접두사로 리소스 이름을 사용합니다. `library`
- SAP 가로 배포는 접두사로 리소스 이름을 사용합니다. `vnet_`
- SAP 시스템 배포는 접두사로 리소스 이름을 사용합니다. `sdu_`

계산된 이름은 모든 terraform 모듈에서 사용되는 데이터 사전에 반환됩니다. 

## <a name="using-custom-names"></a>사용자 지정 이름 사용

일부 리소스 이름은 tfvars 매개 변수 파일에 매개 변수를 제공하여 변경할 수 있습니다.

| 리소스               | 매개 변수               | 메모                                                              |
| ---------------------- | ----------------------- | ------------------------------------------------------------------ |
| `Prefix`               | `custom_prefix`         | 리소스 그룹의 모든 리소스에 대한 접두사로 사용됩니다. |
| `Resource group`       | `resourcegroup_name`    |                                                                    |
| `admin subnet name`    | `admin_subnet_name`     |                                                                    |
| `admin nsg name`       | `admin_subnet_nsg_name` |                                                                    |
| `db subnet name`       | `db_subnet_name`        |                                                                    |
| `db nsg name`          | `db_subnet_nsg_name`    |                                                                    |
| `app subnet name`      | `app_subnet_name`       |                                                                    |
| `app nsg name`         | `app_subnet_nsg_name`   |                                                                    |
| `web subnet name`      | `web_subnet_name`       |                                                                    |
| `web nsg name`         | `web_subnet_nsg_name`   |                                                                    |
| `admin nsg name`       | `admin_subnet_nsg_name` |                                                                    |

## <a name="changing-the-naming-module"></a>명명 모듈 변경

사용자 지정 명명을 위해 Terraform 환경을 준비하려면 먼저 사용자 지정 명명 모듈을 만들어야 합니다. 가장 쉬운 방법은 기존 모듈을 복사하고 복사한 모듈에서 필요한 변경을 하는 것입니다.

1. Terraform 환경에서 루트 수준 폴더를 만듭니다. 예: `Azure_SAP_Automated_Deployment`.
1. 새 루트 수준 폴더로 이동합니다.
1. [자동화 프레임워크 리포지토리를](https://github.com/Azure/sap-automation)복제합니다. 이 단계에서는 새 폴더 를 `sap-automation` 만듭니다.
1. 라는 루트 수준 폴더 내에 폴더를 `Contoso_naming` 만듭니다.
1. `sap-automation` 폴더로 이동합니다.
1. git에서 적절한 분기를 확인합니다.
1. 폴더 `\deploy\terraform\terraform-units\modules` 내에서 로 `sap-automation` 이동합니다.
1. 폴더를 `sap_namegenerator` `Contoso_naming` 폴더에 복사합니다.

명명 모듈은 루트 terraform 폴더에서 호출됩니다.

```terraform
module "sap_namegenerator" {
  source           = "../../terraform-units/modules/sap_namegenerator"
  environment      = local.infrastructure.environment
  location         = local.infrastructure.region
  codename         = lower(try(local.infrastructure.codename, ""))
  random_id        = module.common_infrastructure.random_id
  sap_vnet_name    = local.vnet_logical_name
  sap_sid          = local.sap_sid
  db_sid           = local.db_sid
  app_ostype       = try(local.application.os.os_type, "LINUX")
  anchor_ostype    = upper(try(local.anchor_vms.os.os_type, "LINUX"))
  db_ostype        = try(local.databases[0].os.os_type, "LINUX")
  db_server_count  = var.database_server_count
  app_server_count = try(local.application.application_server_count, 0)
  web_server_count = try(local.application.webdispatcher_count, 0)
  scs_server_count = local.application.scs_high_availability ? 2 * local.application.scs_server_count : local.application.scs_server_count  
  app_zones        = local.app_zones
  scs_zones        = local.scs_zones
  web_zones        = local.web_zones
  db_zones         = local.db_zones
  resource_offset  = try(var.options.resource_offset, 0)
  custom_prefix    = var.custom_prefix
}
```

다음으로, 다른 Terraform 모듈 파일을 사용자 지정 명명 모듈로 가리킬 필요가 있습니다. 이러한 모듈 파일은 다음과 같습니다.

- `deploy\terraform\run\sap_system\module.tf`
- `deploy\terraform\bootstrap\sap_deployer\module.tf`
- `deploy\terraform\bootstrap\sap_library\module.tf`
- `deploy\terraform\run\sap_library\module.tf`
- `deploy\terraform\run\sap_deployer\module.tf`

각 파일에 대해 새 명명 `sap_namegenerator` 모듈의 위치를 가리키도록 모듈의 원본을 변경합니다. 다음은 그 예입니다.

`module "sap_namegenerator" { source = "../../terraform-units/modules/sap_namegenerator"`은 `module "sap_namegenerator" { source = "../../../../Contoso_naming"`가 됩니다.

## <a name="change-resource-group-naming-logic"></a>리소스 그룹 명명 논리 변경

리소스 그룹의 명명 논리를 변경하려면 사용자 지정 명명 모듈 폴더(예: `Workspaces\Contoso_naming` )로 이동합니다. 그런 다음 파일을 `resourcegroup.tf` 편집합니다. 사용자 고유의 명명 논리를 통해 다음 코드를 수정합니다.

```terraform
locals {

  // Resource group naming
  sdu_name = length(var.codename) > 0 ? (
    upper(format("%s-%s-%s_%s-%s", local.env_verified, local.location_short, local.sap_vnet_verified, var.codename, var.sap_sid))) : (
    upper(format("%s-%s-%s-%s", local.env_verified, local.location_short, local.sap_vnet_verified, var.sap_sid))
  )

  deployer_name  = upper(format("%s-%s-%s", local.deployer_env_verified, local.deployer_location_short, local.dep_vnet_verified))
  landscape_name = upper(format("%s-%s-%s", local.landscape_env_verified, local.location_short, local.sap_vnet_verified))
  library_name   = upper(format("%s-%s", local.library_env_verified, local.location_short))

  // Storage account names must be between 3 and 24 characters in length and use numbers and lower-case letters only. The name must be unique.
  deployer_storageaccount_name       = substr(replace(lower(format("%s%s%sdiag%s", local.deployer_env_verified, local.deployer_location_short, local.dep_vnet_verified, local.random_id_verified)), "/[^a-z0-9]/", ""), 0, var.azlimits.stgaccnt)
  landscape_storageaccount_name      = substr(replace(lower(format("%s%s%sdiag%s", local.landscape_env_verified, local.location_short, local.sap_vnet_verified, local.random_id_verified)), "/[^a-z0-9]/", ""), 0, var.azlimits.stgaccnt)
  library_storageaccount_name        = substr(replace(lower(format("%s%ssaplib%s", local.library_env_verified, local.location_short, local.random_id_verified)), "/[^a-z0-9]/", ""), 0, var.azlimits.stgaccnt)
  sdu_storageaccount_name            = substr(replace(lower(format("%s%s%sdiag%s", local.env_verified, local.location_short, local.sap_vnet_verified, local.random_id_verified)), "/[^a-z0-9]/", ""), 0, var.azlimits.stgaccnt)
  terraformstate_storageaccount_name = substr(replace(lower(format("%s%stfstate%s", local.library_env_verified, local.location_short, local.random_id_verified)), "/[^a-z0-9]/", ""), 0, var.azlimits.stgaccnt)

}
```

## <a name="change-resource-suffixes"></a>리소스 접미사 변경

리소스 접미사를 변경하려면 사용자 지정 명명 모듈 폴더(예: `Workspaces\Contoso_naming` )로 이동합니다. 그런 다음 파일을 `variables_local.tf` 편집합니다. 사용자 고유의 리소스 접미사를 사용해서 다음 맵을 수정합니다.

> [!NOTE]
> 맵 값 만 **변경합니다.** Terraform 코드에서 사용하는 맵 **키를** 변경하지 마세요.
> 예를 들어 관리자 네트워크 인터페이스 구성 요소의 이름을 바꾸려면 를 `"admin-nic"           = "-admin-nic"` 로 `"admin-nic"           = "yourNICname"` 변경합니다.

```terraform
variable resource_suffixes {
  type        = map(string)
  description = "Extension of resource name"

  default = {
    "admin_nic"           = "-admin-nic"
    "admin_subnet"        = "admin-subnet"
    "admin_subnet_nsg"    = "adminSubnet-nsg"
    "app_alb"             = "app-alb"
    "app_avset"           = "app-avset"
    "app_subnet"          = "app-subnet"
    "app_subnet_nsg"      = "appSubnet-nsg"
    "db_alb"              = "db-alb"
    "db_alb_bepool"       = "dbAlb-bePool"
    "db_alb_feip"         = "dbAlb-feip"
    "db_alb_hp"           = "dbAlb-hp"
    "db_alb_rule"         = "dbAlb-rule_"
    "db_avset"            = "db-avset"
    "db_nic"              = "-db-nic"
    "db_subnet"           = "db-subnet"
    "db_subnet_nsg"       = "dbSubnet-nsg"
    "deployer_rg"         = "-INFRASTRUCTURE"
    "deployer_state"      = "_DEPLOYER.terraform.tfstate"
    "deployer_subnet"     = "_deployment-subnet"
    "deployer_subnet_nsg" = "_deployment-nsg"
    "iscsi_subnet"        = "iscsi-subnet"
    "iscsi_subnet_nsg"    = "iscsiSubnet-nsg"
    "library_rg"          = "-SAP_LIBRARY"
    "library_state"       = "_SAP-LIBRARY.terraform.tfstate"
    "kv"                  = ""
    "msi"                 = "-msi"
    "nic"                 = "-nic"
    "osdisk"              = "-OsDisk"
    "pip"                 = "-pip"
    "ppg"                 = "-ppg"
    "sapbits"             = "sapbits"
    "storage_nic"         = "-storage-nic"
    "storage_subnet"      = "_storage-subnet"
    "storage_subnet_nsg"  = "_storageSubnet-nsg"
    "scs_alb"             = "scs-alb"
    "scs_alb_bepool"      = "scsAlb-bePool"
    "scs_alb_feip"        = "scsAlb-feip"
    "scs_alb_hp"          = "scsAlb-hp"
    "scs_alb_rule"        = "scsAlb-rule_"
    "scs_avset"           = "scs-avset"
    "scs_ers_feip"        = "scsErs-feip"
    "scs_ers_hp"          = "scsErs-hp"
    "scs_ers_rule"        = "scsErs-rule_"
    "scs_scs_rule"        = "scsScs-rule_"
    "sdu_rg"              = ""
    "tfstate"             = "tfstate"
    "vm"                  = ""
    "vnet"                = "-vnet"
    "vnet_rg"             = "-INFRASTRUCTURE"
    "web_alb"             = "web-alb"
    "web_alb_bepool"      = "webAlb-bePool"
    "web_alb_feip"        = "webAlb-feip"
    "web_alb_hp"          = "webAlb-hp"
    "web_alb_inrule"      = "webAlb-inRule"
    "web_avset"           = "web-avset"
    "web_subnet"          = "web-subnet"
    "web_subnet_nsg"      = "webSubnet-nsg"

  }
}
```

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [명명 규칙에 대해 알아보기](automation-naming.md)
