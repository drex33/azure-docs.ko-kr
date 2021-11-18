---
title: Automation framework 수동 배포 시작
description: 예제 구성 및 샘플 매개 변수 파일을 사용 하 여 Azure에서 SAP deployment automation 프레임 워크를 수동으로 배포 합니다.
author: kimforss
ms.author: kimforss
ms.reviewer: kimforss
ms.date: 11/17/2021
ms.topic: how-to
ms.service: virtual-machines-sap
ms.openlocfilehash: cd8250d690a07fe8999274779ce00d3ac8fa04dc
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/17/2021
ms.locfileid: "132730417"
---
# <a name="get-started-with-manual-deployment"></a>수동 배포 시작

자동화 된 [배포](automation-get-started.md)와 함께 [Azure에서 SAP 배포 자동화 프레임 워크](automation-deployment-framework.md)를 수동으로 배포할 수도 있습니다. 이 예제 구성 및 샘플 매개 변수 파일을 사용 하 여 시작 하세요.

> [!TIP]
> 이 가이드에서는 **수동** 배포를 수행 하는 방법에 대해서만 설명 합니다. 신속 하 게 시작 하려면 [ **자동화** 된 배포 가이드](automation-get-started.md) 를 참조 하세요.

이러한 단계는 자동화 프레임 워크에 대 한 [기본 명명 규칙](automation-naming.md) 을 참조 하 고 사용 합니다. 예제 값은 코드 전체에서 이름을 지정 하는 데도 사용 됩니다. 예를 들어 배포자 이름은 `DEMO-EUS2-DEP00-INFRASTRUCTURE` 입니다. 이 예제에서 환경은 데모 ( `DEMO` )이 고 지역은 **미국 동부 2** ( `EUS2` ) 이며 배포자 가상 네트워크는 `DEP00` 입니다. 

## <a name="prerequisites"></a>필수 구성 요소

- Azure 구독 Azure 구독이 없는 경우 [체험 계정을 만들 수 있습니다](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- 서비스 주체를 만들 수 있는 권한이 있는 Azure 계정 
- Azure 환경에서 [SAP 소프트웨어를 다운로드](automation-software.md) 합니다.

## <a name="deployer-setup"></a>배포자 설정

시작 하기 전에 [올바른 Azure 구독에 있는지 확인](#check-azure-subscription)합니다. 그런 다음 배포자을 설정 합니다.

1. [Terraform을 다운로드 하 여 설치](#download-terraform)합니다. 
1. 배포자에서 [자동화 프레임 워크 리포지토리를 복제 하 고 구성](#set-up-repository) 합니다. 
1. [Terraform 초기화](#initialize-terraform)
1. 배포의 나머지 부분에서 사용할 [SSH 키를 가져옵니다](#get-ssh-keys) .
### <a name="check-azure-subscription"></a>Azure 구독 확인

적절 한 Azure 구독을 사용 하 고 있는지 확인 합니다.

1. [Azure Portal에 로그인](https://portal.azure.com/)합니다.

1. [Azure Cloud Shell를 엽니다](https://shell.azure.com/).

1. 사용 하려는 구독에 있는지 확인 합니다.

    ```azurecli-interactive
    az account list --output=table | grep -i true
    ```

1. 필요한 경우 [활성 구독](/cli/azure/manage-azure-subscriptions-azure-cli#change-the-active-subscription) 을 사용 하려는 구독으로 변경 합니다. 

### <a name="download-terraform"></a>Terraform 다운로드

사용자 환경에 Terraform을 다운로드 합니다.

1. 를 만들고 새 디렉터리인로 이동 `bin` 합니다.

    ```azurecli-interactive
    mkdir -p ~/bin; cd $_
    ```

1. 적절 한 Terraform 이진 파일을 검색 합니다. 예를 들어 다음과 같습니다.

    ```azurecli-interactive
    wget  https://releases.hashicorp.com/terraform/0.14.7/terraform_0.14.7_linux_amd64.zip
    ```

1. Terraform 이진 파일의 압축을 풉니다. 예를 들어 다음과 같습니다.

    ```azurecli-interactive
    unzip terraform_0.14.7_linux_amd64.zip
    ```

1. Terraform 다운로드를 확인 합니다.

    ```azurecli-interactive
    hash terraform
    ```

1. SAP 자동 배포를 위한 디렉터리를 만듭니다.

    ```azurecli-interactive
    mkdir -p ~/Azure_SAP_Automated_Deployment; cd $_
    ```

## <a name="set-up-repository"></a>리포지토리 설정

[자동화 프레임 워크 리포지토리](https://github.com/Azure/sap-hana)를 복제 하 고 구성 합니다.

1. GitHub에서 리포지토리를 복제 합니다.

    ```azurecli-interactive
    git clone https://github.com/Azure/sap-hana.git
    ```

1. `sap-hana` 폴더로 이동합니다.

    ```azurecli-interactive
    cd  ~/Azure_SAP_Automated_Deployment/sap-hana
    ```

1. 필요에 따라 주 분기와 다른 분기를 체크 아웃 합니다. 리포지토리의 주 분기가 기본값입니다.
    
    1. `<branch>`사용 하려는 분기 이름 또는 커밋 해시로 대체 합니다.

        ```azurecli
        git checkout <branch>
        ```

    1. 분기가 예상 수정 버전 인지 확인 합니다.

        ```azurecli-interactive
        git rev-parse HEAD
        ```

### <a name="initialize-terraform"></a>Terraform 초기화

1. 작업 디렉터리를 만듭니다. 디렉터리 이름은 [기본 명명 규칙](automation-naming.md)을 따라야 합니다. 예를 들어 다음과 같습니다.

    ```azurecli-interactive
    mkdir -p ~/Azure_SAP_Automated_Deployment/WORKSPACES/DEPLOYER/DEMO-EUS2-DEP00-INFRASTRUCTURE; cd $_
    ```

1. JSON 매개 변수 파일을 만듭니다.

    ```azurecli-interactive
    cat <<EOF > DEMO-EUS2-DEP00-INFRASTRUCTURE.json
    {
      "infrastructure": {
        "environment"                         : "DEMO",
        "region"                              : "eastus2",
        "vnets": {
          "management": {
            "name"                            : "DEP00",
            "address_space"                   : "10.0.0.0/25",
            "subnet_mgmt": {
              "prefix"                        : "10.0.0.64/28"
            },
            "subnet_fw": {
              "prefix"                        : "10.0.0.0/26"
            }
          }
        }
      },
      "options": {
        "enable_deployer_public_ip"           : true
      },
      "firewall_deployment"                   : true
    }
    EOF
    ```

1. Terraform을 초기화합니다.

    ```azurecli-interactive
    terraform init  ../../../sap-hana/deploy/terraform/bootstrap/sap_deployer/
    ```

1. 기본 명명 규칙을 따르는 Terraform 실행 계획을 만듭니다.

    ```azurecli-interactive
    terraform plan                                                                    \
                    --var-file=DEMO-EUS2-DEP00-INFRASTRUCTURE.json                    \
                    ../../../sap-hana/deploy/terraform/bootstrap/sap_deployer/
    ```

1. Terraform 실행 계획을 적용 하 여 리소스를 배포 합니다.

    ```azurecli-interactive
    terraform apply --auto-approve                                                    \
                    --var-file=DEMO-EUS2-DEP00-INFRASTRUCTURE.json                    \
                    ../../../sap-hana/deploy/terraform/bootstrap/sap_deployer/
    ```

1. 출력을 확인 합니다.

### <a name="get-ssh-keys"></a>SSH 키 가져오기

1. [Terraform 배포](#initialize-terraform)의 출력을 사용 하 여 다음 필드에 대 한 값을 확인 합니다.

    1. 공용 IP 주소: `deployer_public_ip_address` .

    1. Key vault의 사용자 이름 `deployer_kv_user_name` 입니다.

    1. 개인 키 자격 증명 모음 이름: `deployer_kv_prvt_name` .

    1. 공개 키의 `deployer_public_key_secret_name` 이름:

    1. 개인 키의 `deployer_private_key_secret_name` 이름:

1. 후 처리 스크립트를 실행 합니다.

    ```azurecli-interactive
    ./post_deployment.sh
    ```

1. 개인 SSH 키를 추출 합니다.
    
    ```azurecli-interactive
    az keyvault secret show               \
      --vault-name DEMOEUS2DEP00userE27   \
      --name DEMO-EUS2-DEP00-sshkey     | \
      jq -r .value > sshkey
    
    ```

1. 공용 SSH 키를 추출 합니다.
    
    ```azurecli-interactive
    az keyvault secret show               \
      --vault-name DEMOEUS2DEP00userF6A   \
      --name DEMO-EUS2-DEP00-sshkey-pub | \
      jq -r .value > sshkey.pub
    
    ```

1. 개인 및 공개 키 쌍을 다운로드 합니다. Cloud Shell 메뉴에서 **업로드/다운로드 파일** &gt; **다운로드** 를 선택 합니다.

## <a name="service-principal-configuration"></a>서비스 사용자 구성

배포자는 서비스 주체를 사용 하 여 구독에 리소스를 배포 합니다. 

1. Azure CLI에 로그인합니다.

    ```azurecli-interactive
    az login
    ```

1. 서비스 주체를 생성합니다. 을 (를) `<subscription-id>` Azure 구독 식별자로 바꾸어야 합니다. 또한를 `<sp-name>` 서비스 사용자의 이름으로 바꿉니다.

    ```azurecli-interactive
    az ad sp create-for-rbac --role="Contributor" --scopes="/subscriptions/<subscription-id>" --name="<sp-name>"
    ```

1. 서비스 주체에 대 한 정보를 포함 하는 출력을 기록 합니다. 다음 필드의 값을 복사 합니다.

    1. 응용 프로그램 식별자 `appId` :

    1. 암호: `password` .

    1. 테 넌 트 `tenant` 식별자:

1. 서비스 사용자에 대 한 역할 할당을 만듭니다. 을 `<appId>` 이전 단계에서 적어둔 응용 프로그램 식별자로 바꾸어야 합니다.

    ```azurecli-interactive
    az role assignment create --assignee <appId> --role "User Access Administrator"
    ```

1. 다음과 같이 키 자격 증명 모음에 서비스 사용자에 대 한 키를 추가 합니다. 자리 표시자 값을 이전 단계에서 적어둔 정보로 바꾸어야 합니다. 을 `<environment>` 사용자 환경의 이름 (예:)으로 바꿉니다 `DEMO` . 

    ```azurecli-interactive
    az keyvault secret set --name "<environment>-subscription-id" --vault-name "<deployer_kv_user_name>" --value "<subscription-id>";
    az keyvault secret set --name "<environment>-tenant-id"       --vault-name "<deployer_kv_user_name>" --value "<tenant>";
    az keyvault secret set --name "<environment>-client-id"       --vault-name "<deployer_kv_user_name>" --value "<appId>";
    az keyvault secret set --name "<environment>-client-secret"   --vault-name "<deployer_kv_user_name>" --value "<password>";
    ```

## <a name="library-configuration"></a>라이브러리 구성

1. SSH 클라이언트 및 [배포자 설치](#get-ssh-keys)중에 검색 한 ssh 키를 사용 하 여 배포자에 로그인 합니다. PuTTY를 SSH 클라이언트로 사용 하는 경우를 사용 하기 전에 SSH 키를 `.ppk` 형식으로 변환 합니다.

1. 자동화 프레임 워크 리포지토리를 복제 한 위치로 이동 합니다.

    ```bash
    cd  ~/Azure_SAP_Automated_Deployment/sap-hana
    ```

1. 필요에 따라 주 분기와 다른 분기를 체크 아웃 합니다. 리포지토리의 주 분기가 기본값입니다.
    
    1. `<branch>`사용 하려는 분기 이름 또는 커밋 해시로 대체 합니다.

        ```azurecli
        git checkout <branch>
        ```

    1. 분기가 예상 수정 버전 인지 확인 합니다.

        ```azurecli-interactive
        git rev-parse HEAD
        ```

1. 작업 디렉터리를 만듭니다.

    ```bash
    mkdir -p ~/Azure_SAP_Automated_Deployment/WORKSPACES/LIBRARY/DEMO-EUS2-SAP_LIBRARY; cd $_
    ```

1. JSON 구성 파일을 만듭니다.

    ```bash
    cat <<EOF > DEMO-EUS2-SAP_LIBRARY.json
    {
      "infrastructure": {
        "environment"                         : "DEMO",
        "region"                              : "eastus2"
      },
      "deployer": {
        "environment"                         : "DEMO",
        "region"                              : "eastus2",
        "vnet"                                : "DEP00"
      }
    }
    EOF
    ```

1. Terraform을 초기화합니다.

    ```bash
    terraform init  ../../../sap-hana/deploy/terraform/bootstrap/sap_library/
    ```

1. 기본 명명 규칙을 따르는 Terraform 실행 계획을 만듭니다.

    ```bash
    terraform plan                                                                  \
                --var-file=DEMO-EUS2-SAP_LIBRARY.json                           \
                ../../../sap-hana/deploy/terraform/bootstrap/sap_library

    ```

1. Terraform 실행 계획을 적용 하 여 리소스를 배포 합니다.

    ```bash
    terraform apply --auto-approve                                                  \
                --var-file=DEMO-EUS2-SAP_LIBRARY.json                           \
                ../../../sap-hana/deploy/terraform/bootstrap/sap_library/

    ```

## <a name="reinitialize-deployment"></a>배포 다시 초기화

[배포자](#reinitialize-deployer) 및 [SAP 라이브러리](#reinitialize-sap-library)를 모두 다시 초기화 합니다.

### <a name="reinitialize-deployer"></a>배포자 다시 초기화

1. SSH 클라이언트에서 배포자에 로그인 된 상태를 유지 합니다. 또는 다시 로그인 합니다. 

1. 만든 작업 디렉터리로 이동 합니다.

    ```bash
    cd ~/Azure_SAP_Automated_Deployment/WORKSPACES/LOCAL/DEMO-EUS2-DEP00-INFRASTRUCTURE
    ```

1. 이라는 다른 매개 변수 파일 `backend` 을 만듭니다. 기본 명명 규칙을 따릅니다. 의 경우 `resource_group_name` Terraform 상태 파일 ()이 있는 저장소 계정이 있는 리소스 그룹의 이름을 사용 `.tfstate` 합니다. 의 `storage_account_name` 경우 `<tfstate_storage_account_name>` 파일에 대 한 SAP 라이브러리 배포의 저장소 계정 이름으로 대체 `.tfstate` 합니다. 의 `key` 경우 배포자의 리소스 그룹 이름을 확장과 결합 `.terraform.tfstate` 합니다. 예를 들어 다음과 같습니다.

    ```bash
    cat <<EOF > backend
    resource_group_name   = "DEMO-EUS2-SAP_LIBRARY"
    storage_account_name  = "<tfstate_storage_account_name>"
    container_name        = "tfstate"
    key                   = "DEMO-EUS2-DEP00-INFRASTRUCTURE.terraform.tfstate"
    EOF
    ```

1. Terraform을 다시 초기화 하십시오.

    ```bash
    terraform init  --backend-config backend                                        \
                    ../../../sap-hana/deploy/terraform/run/sap_deployer/
    ```

1. **새 백 엔드에 기존 상태를 복사 하 시겠습니까?** 라는 메시지가 표시 되 면을 입력 `yes` 합니다.

1. 로컬 상태 파일을 제거 합니다.

    ```bash
    rm terraform.tfstate*
    ```

1. Terraform 실행 계획을 만듭니다. 기본 명명 규칙을 따릅니다. 예를 들어 다음과 같습니다.

    ```bash
    terraform plan                                                                  \
                    --var-file=DEMO-EUS2-DEP00-INFRASTRUCTURE.json                  \
                    ../../../sap-hana/deploy/terraform/run/sap_deployer/
    ```
    
1. Terraform 실행 계획을 적용 합니다. 예를 들어 다음과 같습니다.

    ```bash
    terraform apply --auto-approve                                                  \
                    --var-file=DEMO-EUS2-DEP00-INFRASTRUCTURE.json                  \
                    ../../../sap-hana/deploy/terraform/run/sap_deployer/
    ```

### <a name="reinitialize-sap-library"></a>SAP 라이브러리 다시 초기화

1. SSH 클라이언트에서 배포자에 로그인 된 상태를 유지 합니다. 또는 다시 로그인 합니다. 

1. 만든 작업 디렉터리로 이동 합니다.

    ```bash
    cd ~/Azure_SAP_Automated_Deployment/WORKSPACES/LIBRARY/DEMO-EUS2-SAP_LIBRARY
    ```

1. 이라는 다른 매개 변수 파일 `backend` 을 만듭니다. 기본 명명 규칙을 따릅니다. 의 경우 `resource_group_name` Terraform 상태 파일 ()이 있는 저장소 계정이 있는 리소스 그룹의 이름을 사용 `.tfstate` 합니다. 의 `storage_account_name` 경우 `<tfstate_storage_account_name>` 파일에 대 한 SAP 라이브러리 배포의 저장소 계정 이름으로 대체 `.tfstate` 합니다. 의 `key` 경우 배포자의 리소스 그룹 이름을 확장과 결합 `.terraform.tfstate` 합니다. 예를 들어 다음과 같습니다.

    ```bash
    cat <<EOF > backend
    resource_group_name   = "DEMO-EUS2-SAP_LIBRARY"
    storage_account_name  = "<tfstate_storage_account_name>"
    container_name        = "tfstate"
    key                   = "DEMO-EUS2-SAP_LIBRARY.terraform.tfstate"
    EOF
    ```

1. 매개 변수 파일의 여는 대괄호 () 바로 다음에 새 키-값 쌍을 추가 `{` `backend` 합니다. 의 `tfstate_resource_id` 경우 Terraform 상태 파일 저장소 계정에 대 한 리소스 식별자를 사용 합니다. 의 `deployer_tfstate_key` 경우 배포자 상태 파일의 키 이름을 사용 합니다. 예를 들어 다음과 같습니다.
    
    ```bash
    {
        "tfstate_resource_id"                   : "<identifier>",
        "deployer_tfstate_key"                  : "<key>",
        "infrastructure": {
            ...
    }
    ```

1. Terraform을 다시 초기화 하십시오.

    ```bash
    terraform init  --backend-config backend                                          \
                    ../../../sap-hana/deploy/terraform/run/sap_library/
    ```

1. **새 백 엔드에 기존 상태를 복사 하 시겠습니까?** 라는 메시지가 표시 되 면을 입력 `yes` 합니다.

1. 로컬 상태 파일을 제거 합니다.

    ```bash
    rm terraform.tfstate*
    ```

1. Terraform 실행 계획을 만듭니다. 기본 명명 규칙을 따릅니다. 예를 들어 다음과 같습니다.

    ```bash
    terraform plan                                                                    \
                    --var-file=DEMO-EUS2-SAP_LIBRARY.json                             \
                    ../../../sap-hana/deploy/terraform/run/sap_library/
    ```

1. Terraform 실행 계획을 적용 합니다. 예를 들어 다음과 같습니다.

    ```bash
    terraform apply --auto-approve                                                    \
                    --var-file=DEMO-EUS2-SAP_LIBRARY.json                             \
                    ../../../sap-hana/deploy/terraform/run/sap_library/
    ```

## <a name="deploy-workload-virtual-network"></a>워크 로드 가상 네트워크 배포

그런 다음 SAP 워크 로드 가상 네트워크를 배포 합니다.

1. SSH 클라이언트에서 배포자에 로그인 된 상태를 유지 합니다. 또는 다시 로그인 합니다. 

1. 작업 디렉터리를 만듭니다. 기본 명명 규칙을 따릅니다.

    ```bash
    mkdir -p ~/Azure_SAP_Automated_Deployment/WORKSPACES/LANDSCAPE/DEMO-EUS2-SAP00-INFRASTRUCTURE; cd $_
    ```

1. 이라는 매개 변수 파일을 만듭니다 `backend` . 의 경우 `resource_group_name` Terraform 상태 파일 ()이 있는 저장소 계정이 있는 리소스 그룹의 이름을 사용 `.tfstate` 합니다. 의 `storage_account_name` 경우 `<tfstate_storage_account_name>` 파일에 대 한 SAP 라이브러리 배포의 저장소 계정 이름으로 대체 `.tfstate` 합니다. 의 `key` 경우 배포자의 리소스 그룹 이름을 확장과 결합 `.terraform.tfstate` 합니다. 예를 들어 다음과 같습니다.

    ```bash
    cat <<EOF > backend
    resource_group_name   = "DEMO-EUS2-SAP_LIBRARY"
    storage_account_name  = "<tfstate_storage_account_name>"
    container_name        = "tfstate"
    key                   = "DEMO-EUS2-SAP00-INFRASTRUCTURE.terraform.tfstate"
    EOF
    ```

1. Terraform을 다시 초기화 하십시오.

    ```bash
    terraform init  --backend-config backend                                        \
                    ../../../sap-hana/deploy/terraform/run/sap_landscape/
    ```

1. Terraform 실행 계획을 만듭니다. 기본 명명 규칙을 따릅니다. 예를 들어 다음과 같습니다.

    ```bash
    terraform plan                                                                  \
                --var-file=DEMO-EUS2-SAP00-INFRASTRUCTURE.json                  \
                ../../../sap-hana/deploy/terraform/run/sap_landscape/
    ```

1. Terraform 실행 계획을 적용 합니다. 예를 들어 다음과 같습니다.

    ```bash
    terraform apply --auto-approve                                                  \
                    --var-file=DEMO-EUS2-SAP00-INFRASTRUCTURE.json                  \
                    ../../../sap-hana/deploy/terraform/run/sap_landscape/
    ```

## <a name="sap-deployment-unit"></a>SAP 배포 단위

그런 다음 SAP 배포 단위를 설정 합니다.

1. SSH 클라이언트에서 배포자에 로그인 된 상태를 유지 합니다. 또는 다시 로그인 합니다.

1. 작업 디렉터리를 만듭니다. 기본 명명 규칙을 따릅니다.

    ```bash
    mkdir -p ~/Azure_SAP_Automated_Deployment/WORKSPACES/SYSTEM/DEMO-EUS2-SAP00-X00; cd $_
    ```

1. 이라는 다른 매개 변수 파일 `backend` 을 만듭니다. 의 경우 `resource_group_name` Terraform 상태 파일 ()이 있는 저장소 계정이 있는 리소스 그룹의 이름을 사용 `.tfstate` 합니다. 의 `storage_account_name` 경우 `<tfstate_storage_account_name>` 파일에 대 한 SAP 라이브러리 배포의 저장소 계정 이름으로 대체 `.tfstate` 합니다. 의 `key` 경우 배포자의 리소스 그룹 이름을 확장과 결합 `.terraform.tfstate` 합니다. 예를 들어 다음과 같습니다.

    ```bash
    cat <<EOF > backend
    resource_group_name   = "DEMO-EUS2-SAP_LIBRARY"
    storage_account_name  = "<tfstate_storage_account_name>"
    container_name        = "tfstate"
    key                   = "DEMO-EUS2-SAP00-X00.terraform.tfstate"
    EOF
    ```

1. 다음과 같이 입력 매개 변수를 사용 하 여 JSON 매개 변수 파일을 만듭니다. 샘플 값을 고유한 값으로 바꾸어야 합니다.

    ```bash
    cat <<EOF > DEMO-EUS2-SAP00-X00.json
    {
      "tfstate_resource_id"                   : "<resource-id>",
      "deployer_tfstate_key"                  : "DEMO-EUS2-DEP00-INFRASTRUCTURE.terraform.tfstate",
      "landscape_tfstate_key"                 : "DEMO-EUS2-SAP00-INFRASTRUCTURE.terraform.tfstate",
      "infrastructure": {
        "environment"                         : "DEMO",
        "region"                              : "eastus2",
        "vnets": {
          "sap": {
            "name"                            : "SAP00",
            "subnet_db": {
              "prefix"                        : "0.0.0.0/28"
            },
            "subnet_web": {
              "prefix"                        : "0.0.0.0/28"
            },
            "subnet_app": {
              "prefix"                        : "0.0.0.0/27"
            },
            "subnet_admin": {
              "prefix"                        : "0.0.0.0/27"
            }
          }
        }
      },
      "databases": [
        {
          "platform"                          : "HANA",
          "high_availability"                 : false,
          "size"                              : "S4Demo",
          "os": {
            "publisher"                       : "SUSE",
            "offer"                           : "sles-sap-12-sp5",
            "sku"                             : "gen2",
            "version"                         : "latest"
          }
        }
      ],
      "application": {
        "enable_deployment"                   : true,
        "sid"                                 : "X00",
        "scs_instance_number"                 : "00",
        "ers_instance_number"                 : "10",
        "scs_high_availability"               : false,
        "application_server_count"            : 3,
        "webdispatcher_count"                 : 1,
        "authentication": {
          "type"                              : "key",
          "username"                          : "azureadm"
        }
      }
    }
    EOF
    ```

1. Terraform을 다시 초기화 하십시오.

    ```bash
    terraform init  --backend-config backend                                        \
                    ../../../sap-hana/deploy/terraform/run/sap_system/
  
    ```

1. Terraform 실행 계획을 만듭니다. 기본 명명 규칙을 따릅니다. 예를 들어 다음과 같습니다.

    ```bash
    terraform plan                                                                  \
                    --var-file=DEMO-EUS2-SAP00-X00.json                             \
                    ../../../sap-hana/deploy/terraform/run/sap_system/
    ```

1. Terraform 실행 계획을 적용 합니다. 예를 들어 다음과 같습니다.

    ```bash
    terraform apply --auto-approve                                                  \
                    --var-file=DEMO-EUS2-SAP00-X00.json                             \
                    ../../../sap-hana/deploy/terraform/run/sap_system/
    ```

## <a name="ansible-configuration"></a>Ansible 구성

Ansible playbooks를 실행 하 여 설치를 구성 합니다. 이러한 플레이 북은의 automation framework 리포지토리에 있습니다 `/sap-hana/deploy/ansible` .

| 파일 이름          | 설명                        |
| ----------------- | ---------------------------------- |
| `playbook_01_os_base_config.yaml` | 기본 OS (운영 체제) 구성 |
| `playbook_02_os_sap_specific_config.yaml ` | SAP 관련 OS 구성 |
| `playbook_03_bom_processing.yaml` | SAP BOM (자재 자료) 처리 소프트웨어 다운로드 |
| `playbook_04a_sap_scs_install.yaml   ` | SAP 중앙 서비스 (SCS) 설치 |
| `playbook_05a_hana_db_install.yaml ` | SAP HANA 데이터베이스 설치 |
| `playbook_06a_sap_dbload.yaml` | 데이터베이스 로더 |
| `playbook_06b_sap_pas_install.yaml` | SAP PAS (기본 응용 프로그램 서버) 설치 |
| `playbook_06c_sap_app_install.yaml` | SAP 응용 프로그램 서버 설치 |
| `playbook_06d_sap_web_install.yaml` | SAP 웹 디스패처 설치 |
| `playbook_06_00_00_pacemaker.yaml` | Pacemaker 클러스터 구성 |
| `playbook_06_00_01_pacemaker_scs.yaml` | SCS 용 Pacemaker 구성 | 
| `playbook_06_00_03_pacemaker_hana.yaml` | SAP HANA 데이터베이스에 대 한 Pacemaker 구성 |

플레이 북 또는 여러 플레이 북을 실행 하려면 다음과 같이 명령을 사용 `ansible-playbook` 합니다. 모든 자리 표시자 값을 사용자의 정보로 변경 해야 합니다.

- `<your-sapbits-path>` `sapbits` SAP 라이브러리의 저장소 계정에 대 한 경로로 변경 합니다.
- `<azure-admin>`Azure 관리자 사용자 이름으로 변경 합니다.
- `<ssh-key`>를 사용 하려는 개인 SSH 키로 변경 합니다.
- 설정의 필요에 따라 아래의 다른 값을 변경 `--extra-vars` 합니다.

문제가 발생 하는 경우 Azure 환경에 [SAP 소프트웨어를 다운로드](automation-software.md) 했는지 확인 합니다.

```azurecli-interactive
export           ANSIBLE_HOST_KEY_CHECKING=False
# export OBJC_DISABLE_INITIALIZE_FORK_SAFETY=Yes
# export           ANSIBLE_KEEP_REMOTE_FILES=1


ansible-playbook                                                                                                      \
  --inventory   new-hosts.yaml                                                                                        \
  --user        <azure-admin>                                                                                              \
  --private-key <ssh-key>                                                                                                \
  --extra-vars="{                                                                                                     \
                  \"bom_base_name\":                \"HANA_2_00_053_v001\",                                           \
                  \"download_templates\":           \"false\",                                                        \
                  \"sapbits_location_base_path\":   \"<your-sapbits-path>",        \
                  \"target_media_location\":        \"/usr/sap/install\",                                             \
                  \"sap_sid\":                      \"X00\",                                                          \
                  \"hdb_sid\":                      \"HDB\"                                                           \
                }"                                                                                                    \
~/Azure_SAP_Automated_Deployment/centiq-sap-hana/deploy/ansible/playbook_00_transition_start_for_sap_install_refactor.yaml     \
~/Azure_SAP_Automated_Deployment/centiq-sap-hana/deploy/ansible/playbook_01_os_base_config.yaml                       \
~/Azure_SAP_Automated_Deployment/centiq-sap-hana/deploy/ansible/playbook_02_os_sap_specific_config.yaml               \
~/Azure_SAP_Automated_Deployment/centiq-sap-hana/deploy/ansible/playbook_03_bom_processing.yaml                       \
~/Azure_SAP_Automated_Deployment/centiq-sap-hana/deploy/ansible/playbook_04a_sap_scs_install.yaml                     \
~/Azure_SAP_Automated_Deployment/centiq-sap-hana/deploy/ansible/playbook_05a_hana_db_install.yaml                     \
~/Azure_SAP_Automated_Deployment/centiq-sap-hana/deploy/ansible/playbook_06a_sap_dbload.yaml                          \
~/Azure_SAP_Automated_Deployment/centiq-sap-hana/deploy/ansible/playbook_06b_sap_pas_install.yaml                     \
~/Azure_SAP_Automated_Deployment/centiq-sap-hana/deploy/ansible/playbook_06c_sap_app_install.yaml                     \
~/Azure_SAP_Automated_Deployment/centiq-sap-hana/deploy/ansible/playbook_06d_sap_web_install.yaml

```

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [자동화 된 배포를 빠르게 시작 하기](automation-get-started.md)
