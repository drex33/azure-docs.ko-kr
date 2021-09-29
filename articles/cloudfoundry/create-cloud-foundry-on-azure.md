---
title: Azure에서 Pivotal Cloud Foundry 클러스터 만들기
description: Azure에서 PCF(Pivotal Cloud Foundry) 클러스터를 프로비전하는 데 필요한 매개 변수를 설정하는 방법을 알아봅니다.
services: Cloud Foundry
documentationcenter: CloudFoundry
author: ruyakubu
manager: brunoborges
editor: ruyakubu
ms.assetid: ''
ms.author: ruyakubu
ms.date: 09/13/2018
ms.devlang: ''
ms.service: azure
ms.tgt_pltfrm: multiple
ms.topic: tutorial
ms.workload: web
ms.openlocfilehash: 1ed9dbef648eace79807f06f034ba02e8bc67817
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128655869"
---
# <a name="create-a-pivotal-cloud-foundry-cluster-on-azure"></a>Azure에서 Pivotal Cloud Foundry 클러스터 만들기

이 자습서에는 Azure에서 PCF(Pivotal Cloud Foundry) 클러스터를 프로비전하는 데 필요한 매개 변수를 만들고 생성하기 위한 빠른 단계가 제공됩니다. Pivotal Cloud Foundry 솔루션을 찾으려면 Azure Marketplace에서 검색합니다.

![Azure에서 Pivotal Cloud Foundry 검색](media/deploy/pcf-marketplace.png)


## <a name="generate-an-ssh-public-key"></a>SSH 공개 키 생성

Windows, Mac 또는 Linux를 사용하여 공개 SSH(Secure Shell) 키를 생성하는 데에는 몇 가지 방법이 있습니다.

```Bash
ssh-keygen -t rsa -b 2048
```

자세한 내용은 [Azure에서 Windows를 통해 SSH 키 사용](../virtual-machines/linux/ssh-from-windows.md)을 참조하세요.

## <a name="create-a-service-principal"></a>서비스 주체 만들기

> [!NOTE]
>
> 서비스 주체를 만들려면 소유자 계정 권한이 필요합니다. 또한 서비스 주체를 자동으로 만드는 스크립트를 작성할 수도 있습니다. 예를 들어 [az ad sp create-for-rbac](/cli/azure/ad/sp) Azure CLI를 사용할 수 있습니다.

1. Azure 계정에 로그인합니다.

    ```azurecli
    az login
    ```

    ![Azure CLI 로그인](media/deploy/az-login-output.png )
 
    "id" 값을 **구독 ID** 로 복사하고, 나중에 사용할 "tenantId" 값을 복사합니다.

2. 이 구성에 대한 기본 구독을 설정합니다.

    ```azurecli
    az account set -s {id}
    ```

3. PCF용 Azure Active Directory 애플리케이션을 만듭니다. 고유한 영숫자 암호를 지정합니다. 암호를 나중에 사용할 **clientSecret** 로 저장합니다.

    ```azurecli
    az ad app create --display-name "Svc Principal for OpsManager" --password {enter-your-password} --homepage "{enter-your-homepage}" --identifier-uris {enter-your-homepage}
    ```

    출력의 "appId" 값을 나중에 사용할 **clientID** 로 복사합니다.

    > [!NOTE]
    >
    > 사용자 고유의 애플리케이션 홈 페이지와 식별자 URI를 선택하세요(예: http\://www\.contoso.com).

4. 새 앱 ID를 사용하여 서비스 주체를 만듭니다.

    ```azurecli
    az ad sp create --id {appId}
    ```

5. 서비스 주체의 권한 역할을 기여자로 설정합니다.

    ```azurecli
    az role assignment create --assignee "{enter-your-homepage}" --role "Contributor"
    ```

    또는 다음을 사용할 수도 있습니다.

    ```azurecli
    az role assignment create --assignee {service-principal-name} --role "Contributor"
    ```

    ![서비스 주체 역할 할당](media/deploy/svc-princ.png )

6. 앱 ID, 암호 및 테넌트 ID를 사용하여 서비스 주체에 성공적으로 로그인할 수 있는지 확인합니다.

    ```azurecli
    az login --service-principal -u {appId} -p {your-password}  --tenant {tenantId}
    ```

7. .json 파일을 다음 형식으로 만듭니다. 이전에 복사한 **subscription ID**, **tenantID**, **clientID** 및 **clientSecret** 값을 사용합니다. 파일을 저장합니다.

    ```json
    {
        "subscriptionID": "{enter-your-subscription-Id-here}",
        "tenantID": "{enter-your-tenant-Id-here}",
        "clientID": "{enter-your-app-Id-here}",
        "clientSecret": "{enter-your-key-here}"
    }
    ```

## <a name="get-the-pivotal-network-token"></a>Pivotal 네트워크 토큰 가져오기

1. [Pivotal 네트워크](https://network.pivotal.io) 계정에 등록하거나 로그인합니다.
2. 페이지의 오른쪽 위 모서리에서 프로필 이름을 선택합니다. **프로필 편집** 을 선택합니다.
3. 페이지 아래쪽으로 스크롤하여 **레거시 API 토큰** 값을 복사합니다. 이 값은 나중에 사용하는 **Pivotal 네트워크 토큰** 값입니다.

## <a name="provision-your-cloud-foundry-cluster-on-azure"></a>Azure에서 Cloud Foundry 클러스터 프로비전

이제 Azure에서 Pivotal Cloud Foundry 클러스터를 프로비전하는 데 필요한 모든 매개 변수가 있습니다.
매개 변수를 입력하고 PCF 클러스터를 만듭니다.

## <a name="verify-the-deployment-and-sign-in-to-the-pivotal-ops-manager"></a>배포를 확인하고 Pivotal Ops Manager에 로그인합니다.

1. PCF 클러스터에 배포 상태가 표시됩니다.

    ![Azure 배포 상태](media/deploy/deployment.png )

2. 왼쪽 탐색 영역에서 **배포** 링크를 선택하여 PCF Ops Manager에 대한 자격 증명을 가져옵니다. 다음 페이지에서 **배포 이름** 을 선택합니다.
3. 왼쪽 탐색 영역에서 **출력** 링크를 선택하여 PCF Ops Manager에 대한 URL, 사용자 이름 및 암호를 표시합니다. “OPSMAN-FQDN” 값은 URL입니다.
 
    ![Cloud Foundry 배포 출력](media/deploy/deploy-outputs.png )
 
4. 웹 브라우저에서 URL을 시작합니다. 이전 단계의 자격 증명을 입력하여 로그인합니다.

    ![Pivotal 로그인 페이지](media/deploy/pivotal-login.png )
         
    > [!NOTE]
    >
    > "사이트가 안전하지 않습니다"라는 경고 메시지로 인해 Internet Explorer 브라우저가 실패하면 **추가 정보** 를 선택하고 웹 페이지로 이동합니다. Firefox의 경우 **Advance**(다음)를 선택하고, 인증서를 추가하여 계속 진행합니다.

5. 배포된 Azure 인스턴스가 PCF Ops Manager에 표시됩니다. 이제 여기서 애플리케이션을 배포하고 관리할 수 있습니다.
               
    ![Pivotal에 배포된 Azure 인스턴스](media/deploy/ops-mgr.png )
