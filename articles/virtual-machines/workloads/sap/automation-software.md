---
title: 자동화 프레임워크용 SAP 소프트웨어 다운로드
description: Ansible 플레이북을 사용하여 Azure 환경에 SAP 소프트웨어를 다운로드하여 Azure에서 SAP 배포 자동화 프레임워크를 사용합니다.
author: kimforss
ms.author: kimforss
ms.reviewer: kimforss
ms.date: 11/17/2021
ms.topic: how-to
ms.service: virtual-machines-sap
ms.openlocfilehash: d93f1d9daa433a6abcd6ad54426060795708c9ef
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/17/2021
ms.locfileid: "132730394"
---
# <a name="download-sap-software"></a>SAP 소프트웨어 다운로드

Azure에서 SAP [배포 자동화 프레임워크를](automation-deployment-framework.md)사용하려면 SAP 소프트웨어의 복사본이 필요합니다. 스토리지 계정에 SAP 미디어를 배치할 수 있도록 [Azure 환경을 준비합니다.](#configure-key-vault) 그런 다음 [Ansible 플레이북을 사용하여 SAP 소프트웨어를 다운로드합니다.](#download-sap-software)

## <a name="prerequisites"></a>필수 구성 요소

- Azure 구독 Azure 구독이 없는 경우 [체험 계정을 만들 수 있습니다](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- 소프트웨어 다운로드 권한이 있는 SAP 사용자 계정(SAP-User 또는 S-User 계정)

## <a name="configure-key-vault"></a>키 자격 증명 모음 구성

먼저 배포자 키 자격 증명 모음 비밀을 구성합니다. 이 예제 구성의 경우 리소스 그룹은 `DEMO-EUS2-DEP00-INFRASTRUCTURE` 또는 입니다. `DEMO-SCUS-DEP00-INFRASTRUCTURE`

1. 사용하려는 [계정으로 Azure CLI 로그인합니다.](/cli/azure/authenticate-azure-cli)

    ```azurecli-interactive
    az login
    ```

1. SAP 사용자 계정의 사용자 이름을 가진 비밀을 추가합니다. `<keyvault-name>`를 배포자 키 자격 증명 모음의 이름으로 대체합니다. 또한 `<sap-username>` 을 SAP 사용자 이름으로 대체합니다.

    ```azurecli-interactive
     az keyvault secret set --name "S-Username" --vault-name "<keyvault-name>" --value "<sap-username>";
    ```

2. SAP 사용자 계정의 암호로 비밀을 추가합니다. `<keyvault-name>`를 배포자 키 자격 증명 모음의 이름으로 대체합니다. 또한 `<sap-password>` 를 SAP 암호로 대체합니다.

```azurecli-interactive
az keyvault secret set --name "S-Password" --vault-name "<keyvault-name>" --value "<sap-password>";
```

3. 스토리지 계정에 대해 이 단계에서 필요한 두 가지 다른 `sapbits` 비밀이 자동화 프레임워크에 의해 자동으로 설정됩니다. 그러나 항상 배포자 키에 있는지 여부를 확인하는 것이 좋습니다.

    ```azurecli-interactive
    sapbits-access-key
    sapbits-location-base-path
    ```

## <a name="download-sap-software"></a>SAP 소프트웨어 다운로드

다음으로, 다운로드 프로세스에 대한 [SAP 매개 변수 파일을 구성합니다.](#configure-parameters-file) 그런 다음 [Ansible 플레이북을 사용하여 SAP 소프트웨어를 다운로드합니다.](#download-sap-software) 

### <a name="configure-parameters-file"></a>매개 변수 파일 구성

SAP 매개 변수 파일을 구성합니다.

1. SAP 배포 작업 영역의 디렉터리로 이동합니다.

    ```bash
    cd ~/Azure_SAP_Automated_Deployment/WORKSPACES/
    ```

1. 라는 새 디렉터리를 만듭니다. `BOMS`

    ```bash
    mkdir -p ~/Azure_SAP_Automated_Deployment/WORKSPACES/BOMS; cd $_
    ```

1. SAP 매개 변수 YAML 파일을 만듭니다.

    ```bash
    cat <<EOF > sap-parameters.yaml
    ---
    bom_base_name:               S41909SPS03_v0006ms
    sapbits_location_base_path:  https://<storage_account_FQDN>/sapbits
    kv_name: Name of your Management/Control Plane keyvault
    secret_prefix:
    ...
    EOF
    ```

1. `sap-parameters.yaml`편집기에서 을 엽니다.

    ```bash
    vi sap-parameters.yaml
    ``` 

1. 다음 매개 변수를 업데이트합니다.

    1. `bom_base_name`의 값을 `S41909SPS03_v0006ms`로 변경합니다.

    2. 값을 스토리지 `sapbits_location_base_path` 계정 의 경로로 `sapbits` 변경합니다.

    3. 값을 `kv_name` 배포자 키 자격 증명 모음의 이름으로 변경합니다.
   
   4. (필요한 경우) `secret_prefix` 사용자 환경의 접두사(예: DEV-WEEU-SAP)와 일치하도록 값을 변경합니다.
   
### <a name="execute-ansible-playbooks"></a>Ansible 플레이북 실행

그런 다음, Ansible 플레이북을 실행합니다. 플레이북을 실행할 수 있는 한 가지 방법은 유효성 검사기 테스트 메뉴를 사용하는 것입니다.

1. 유효성 검사기 테스트 메뉴 스크립트를 실행합니다.

    ```bash
    ~/Azure_SAP_Automated_Deployment/sap-hana/deploy/ansible/validator_test_menu.sh
    ```

1. 실행할 플레이북을 선택합니다. 예를 들어 다음과 같습니다.
    
    ```output
    1) BoM Downloader
    2) Quit
    Please select playbook: 
    ```


또 다른 옵션은 명령을 사용하여 Ansible 플레이북을 실행하는 `ansible-playbook` 것입니다. 

```bash
ansible-playbook                                                                                   \
  --user        azureadm                                                                           \
  --extra-vars="@sap-parameters.yaml"                                                              \
  ~/Azure_SAP_Automated_Deployment/sap-hana/deploy/ansible/playbook_bom_downloader.yaml
```

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [자동화 프레임워크 배포 시작](automation-get-started.md)
