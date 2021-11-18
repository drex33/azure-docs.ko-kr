---
title: Ansible를 실행 하 여 SAP 시스템 구성
description: Azure에서 sap deployment automation 프레임 워크를 사용 하 여 환경을 구성 하 고 Ansible 플레이 북를 사용 하 여 sap를 설치 합니다.
author: kimforss
ms.author: kimforss
ms.reviewer: kimforss
ms.date: 11/17/2021
ms.topic: how-to
ms.service: virtual-machines-sap
ms.openlocfilehash: 0837b7fb2adf833ce04799bd6b334c52cc20fd4f
ms.sourcegitcommit: 1244a72dbec39ac8cf16bb1799d8c46bde749d47
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/18/2021
ms.locfileid: "132757828"
---
# <a name="get-started-ansible-configuration"></a>Ansible 구성 시작

[Azure에서 SAP deployment automation 프레임 워크](automation-deployment-framework.md)를 사용 하는 경우 [자동화 된 인프라 배포](automation-get-started.md)를 수행할 수 있지만, 필요한 운영 체제 구성을 수행 하 고 리포지토리에 제공 된 Ansible 플레이 북을 사용 하 여 SAP를 설치할 수도 있습니다. 이러한 플레이 북은 폴더의 automation framework 리포지토리에 있습니다 `/sap-automation/deploy/ansible` .

| 파일 이름                                   | Description                                       |
| ------------------------------------------ | ------------------------------------------------- |
| `playbook_01_os_base_config.yaml`          | 기본 OS (운영 체제) 구성          |
| `playbook_02_os_sap_specific_config.yaml`  | SAP 관련 OS 구성                     |
| `playbook_03_bom_processing.yaml`          | Sap BoM (sap 자재 청구) 처리        |
| `playbook_04_00_00_hana_db_install`        | SAP HANA 데이터베이스 설치                    |
| `playbook_05_00_00_sap_scs_install.yaml`   | SAP 중앙 서비스 (SCS) 설치           |
| `playbook_05_01_sap_dbload.yaml`           | 데이터베이스 로더                                   |
| `playbook_05_02_sap_pas_install.yaml`      | SAP PAS (기본 응용 프로그램 서버) 설치 |
| `playbook_05_03_sap_app_install.yaml`      | SAP 응용 프로그램 서버 설치               |
| `playbook_05_04_sap_web_install.yaml`      | SAP 웹 디스패처 설치                   |
| `playbook_04_00_01_hana_hsr.yaml`          | SAP HANA HA 구성                         |

## <a name="prerequisites"></a>사전 요구 사항

Ansible 플레이 북에는 다음 파일 `sap-parameters.yaml` 및 `SID_host.yaml` 현재 디렉터리가 필요 합니다.

### <a name="configuration-files"></a>구성 파일

Ansible에서 SAP 인프라 구성에 사용 하는 정보를 포함 하는 **sap 매개 변수. yaml**

```yaml
---

# bom_base_name is the name of the SAP Application Bill of Materials file
bom_base_name:                 S41909SPS03_v0006ms
# Set to true to instruct Ansible to update all the packages on the virtual machines
upgrade_packages:              false 

# TERRAFORM CREATED
sap_fqdn:                      sap.contoso.net                      
# kv_name is the name of the key vault containing the system credentials
kv_name:                       DEVWEEUSAP01user###
# secret_prefix is the prefix for the name of the secret stored in key vault
secret_prefix:                 DEV-WEEU-SAP01

# sap_sid is the application SID
sap_sid:                       X01
# scs_high_availability is a boolean flag indicating 
# if the SAP Central Services are deployed using high availability 
scs_high_availability:         false
# SCS Instance Number
scs_instance_number:           "00"
# scs_lb_ip is the SCS IP address of the load balancer in 
# from of the SAP Central Services virtual machines
scs_lb_ip:                     10.110.32.26
# ERS Instance Number
ers_instance_number:           "02"
# ecs_lb_ip is the ERS IP address of the load balancer in
# from of the SAP Central Services virtual machines
ers_lb_ip:                     

# sap_sid is the database SID
db_sid:                        XDB
# platform
platform:                      HANA

# db_high_availability is a boolean flag indicating if the 
# SAP database servers are deployed using high availability
db_high_availability:          false
# db_lb_ip is the IP address of the load balancer in from of the database virtual machines
db_lb_ip:                      10.110.96.13

disks:
  - { host: 'x01dxdb00l0538', LUN: 0, type: 'sap' }
  - { host: 'x01dxdb00l0538', LUN: 10, type: 'data' }
  - { host: 'x01dxdb00l0538', LUN: 11, type: 'data' }
  - { host: 'x01dxdb00l0538', LUN: 12, type: 'data' }
  - { host: 'x01dxdb00l0538', LUN: 13, type: 'data' }
  - { host: 'x01dxdb00l0538', LUN: 20, type: 'log' }
  - { host: 'x01dxdb00l0538', LUN: 21, type: 'log' }
  - { host: 'x01dxdb00l0538', LUN: 22, type: 'log' }
  - { host: 'x01dxdb00l0538', LUN: 2, type: 'backup' }
  - { host: 'x01app00l538', LUN: 0, type: 'sap' }
  - { host: 'x01app01l538', LUN: 0, type: 'sap' }
  - { host: 'x01scs00l538', LUN: 0, type: 'sap' }

...
```

는 **`X01_hosts.yaml`** SAP 인프라의 구성에 Ansible에서 사용 하는 인벤토리 파일입니다. ' X01 '은 배포에 대해 다를 수 있습니다.

```yaml
X01_DB:
  hosts:
    x01dxdb00l0538:
      ansible_host        : 10.110.96.12
      ansible_user        : azureadm
      ansible_connection  : ssh 
      connection_type     : key
  vars:
    node_tier             : hana

X01_SCS:
  hosts:
    x01scs00l538:
      ansible_host        : 10.110.32.25
      ansible_user        : azureadm
      ansible_connection  : ssh 
      connection_type     : key
  vars:
    node_tier             : scs

X01_ERS:
  hosts:
  vars:
    node_tier             : ers

X01_PAS:
  hosts:
    x01app00l538:
      ansible_host        : 10.110.32.24
      ansible_user        : azureadm
      ansible_connection  : ssh 
      connection_type     : key 

  vars:
    node_tier             : pas

X01_APP:
  hosts:
    x01app01l538:
      ansible_host        : 10.110.32.15
      ansible_user        : azureadm
      ansible_connection  : ssh 
      connection_type     : key 

  vars:
    node_tier             : app

X01_WEB:
  hosts:
  vars:
    node_tier             : web

```

## <a name="run-a-playbook"></a>플레이북 실행

이 단계를 실행 하기 전에 Azure 환경에 [SAP 소프트웨어를 다운로드](automation-software.md) 했는지 확인 합니다.

플레이 북 또는 여러 플레이 북을 실행 하려면 다음과 같이 명령을 사용 `ansible-playbook` 합니다. 아래 예제는 운영 체제 구성 플레이 북를 실행 합니다.


```bash

sap_params_file=sap-parameters.yaml

if [[ ! -e "${sap_params_file}" ]]; then
        echo "Error: '${sap_params_file}' file not found!"
        exit 1
fi

# Extract the sap_sid from the sap_params_file, so that we can determine
# the inventory file name to use.
sap_sid="$(awk '$1 == "sap_sid:" {print $2}' ${sap_params_file})"

kv_name="$(awk '$1 == "kv_name:" {print $2}' ${sap_params_file})"

prefix="$(awk '$1 == "secret_prefix:" {print $2}' ${sap_params_file})"
password_secret_name=$prefix-sid-password

password_secret=$(az keyvault secret show --vault-name ${kv_name} --name ${password_secret_name} | jq -r .value)

export           ANSIBLE_PASSWORD=$password_secret
export           ANSIBLE_INVENTORY="${sap_sid}_hosts.yaml"
export           ANSIBLE_PRIVATE_KEY_FILE=sshkey
export           ANSIBLE_COLLECTIONS_PATHS=/opt/ansible/collections${ANSIBLE_COLLECTIONS_PATHS:+${ANSIBLE_COLLECTIONS_PATHS}}
export           ANSIBLE_REMOTE_USER=azureadm

# Ref: https://docs.ansible.com/ansible/2.9/reference_appendices/interpreter_discovery.html
# Silence warnings about Python interpreter discovery
export           ANSIBLE_PYTHON_INTERPRETER=auto_silent

# Set of options that will be passed to the ansible-playbook command
playbook_options=(
        --inventory-file="${sap_sid}_hosts.yaml"
        --private-key=${ANSIBLE_PRIVATE_KEY_FILE}
        --extra-vars="_workspace_directory=`pwd`"
        --extra-vars="@${sap_params_file}"
        -e ansible_ssh_pass='{{ lookup("env", "ANSIBLE_PASSWORD") }}'
        "${@}"
)

ansible-playbook "${playbook_options[@]}" ~/Azure_SAP_Automated_Deployment/sap-automation/deploy/ansible/playbook_01_os_base_config.yaml

```

