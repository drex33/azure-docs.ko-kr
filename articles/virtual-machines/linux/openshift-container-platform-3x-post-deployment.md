---
title: Azure에 OpenShift Container Platform 3.11 배포 후 작업
description: OpenShift Container Platform 3.11 클러스터 배포 이후의 추가 작업입니다.
author: haroldwongms
manager: mdotson
ms.service: virtual-machines
ms.subservice: openshift
ms.collection: linux
ms.topic: how-to
ms.workload: infrastructure
ms.date: 10/14/2019
ms.author: haroldw
ms.custom: devx-track-ansible, devx-track-azurecli
ms.openlocfilehash: 9370fcbe4955c1163d8f533dac43039bff9f5989
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/23/2021
ms.locfileid: "122688265"
---
# <a name="post-deployment-tasks"></a>배포 후 작업

**적용 대상:** :heavy_check_mark: Linux VM :heavy_check_mark: 유연한 확장 집합 

OpenShift 클러스터를 배포한 후에는 추가 항목을 구성할 수 있습니다. 이 문서에서는 다음 내용을 설명합니다.

- Azure AD(Azure Active Directory)를 사용하여 Single Sign-On을 구성하는 방법
- OpenShift를 모니터하도록 Azure Monitor 로그를 구성하는 방법
- 메트릭 및 로깅을 구성하는 방법
- OSBA(Open Service Broker for Azure)를 설치하는 방법

## <a name="configure-single-sign-on-by-using-azure-active-directory"></a>Azure Active Directory를 사용하여 Single Sign-On 구성

Azure Active Directory를 인증에 사용하려면 먼저 Azure AD 앱 등록을 만들어야 합니다. 이 과정에는 앱 등록 만들기 및 권한 구성이라는 두 가지 단계가 포함됩니다.

### <a name="create-an-app-registration"></a>앱 등록 만들기

이 단계에서는 Azure CLI를 사용하여 앱 등록을 만들고 GUI(포털)를 사용하여 권한을 설정합니다. 앱 등록을 만들려면 다음 5가지 정보가 필요합니다.

- 표시 이름: 앱 등록 이름(예: OCPAzureAD)
- 홈페이지: OpenShift 콘솔 URL(예: `https://masterdns343khhde.westus.cloudapp.azure.com/console`)
- 식별자 URI: OpenShift 콘솔 URL(예: `https://masterdns343khhde.westus.cloudapp.azure.com/console`)
- 회신 URL: 마스터 퍼블릭 URL 및 앱 등록 이름(예: `https://masterdns343khhde.westus.cloudapp.azure.com/oauth2callback/OCPAzureAD`)
- 암호: 보안 암호(강력한 암호 사용)

다음 예에서는 위 정보를 사용하여 앱 등록을 만듭니다.

```azurecli
az ad app create --display-name OCPAzureAD --homepage https://masterdns343khhde.westus.cloudapp.azure.com/console --reply-urls https://masterdns343khhde.westus.cloudapp.azure.com/oauth2callback/hwocpadint --identifier-uris https://masterdns343khhde.westus.cloudapp.azure.com/console --password {Strong Password}
```

명령이 성공하면 다음과 유사한 JSON 출력이 표시됩니다.

```json
{
  "appId": "12345678-ca3c-427b-9a04-ab12345cd678",
  "appPermissions": null,
  "availableToOtherTenants": false,
  "displayName": "OCPAzureAD",
  "homepage": "https://masterdns343khhde.westus.cloudapp.azure.com/console",
  "identifierUris": [
    "https://masterdns343khhde.westus.cloudapp.azure.com/console"
  ],
  "objectId": "62cd74c9-42bb-4b9f-b2b5-b6ee88991c80",
  "objectType": "Application",
  "replyUrls": [
    "https://masterdns343khhde.westus.cloudapp.azure.com/oauth2callback/OCPAzureAD"
  ]
}
```

이후 단계를 위해 명령에서 반환되는 appId 속성을 기록해 둡니다.

Azure Portal에서 다음을 수행합니다.

1. **Azure Active Directory** > **앱 등록** 을 선택합니다.
2. 앱 등록(예: OCPAzureAD)을 검색합니다.
3. 결과에서 앱 등록을 클릭합니다.
4. **설정** 에서 **필요한 권한** 을 선택합니다.
5. **필요한 권한** 에서 **추가** 를 선택합니다.

   ![앱 등록](media/openshift-post-deployment/app-registration.png)

6. 1단계: API 선택을 클릭한 다음 **Windows Azure Active Directory(Microsoft.Azure.ActiveDirectory)** 를 클릭합니다. 아래쪽에서 **선택** 을 선택합니다.

   ![앱 등록 API 선택](media/openshift-post-deployment/app-registration-select-api.png)

7. 2단계: 권한 선택에서 **위임된 권한** 아래 **로그인 및 사용자 프로필 읽기** 를 선택한 다음 **선택** 을 클릭합니다.

   ![앱 등록 액세스](media/openshift-post-deployment/app-registration-access.png)

8. **완료** 를 선택합니다.

### <a name="configure-openshift-for-azure-ad-authentication"></a>Azure AD 인증을 위한 OpenShift 구성

Azure AD를 인증 공급자로 사용하도록 OpenShift를 구성하려면 모든 마스터 노드에서 /etc/origin/master/master-config.yaml 파일을 편집해야 합니다.

다음 CLI 명령을 사용하여 테넌트 ID를 찾습니다.

```azurecli
az account show
```

yaml 파일에서 다음 줄을 찾습니다.

```yaml
oauthConfig:
  assetPublicURL: https://masterdns343khhde.westus.cloudapp.azure.com/console/
  grantConfig:
    method: auto
  identityProviders:
  - challenge: true
    login: true
    mappingMethod: claim
    name: htpasswd_auth
    provider:
      apiVersion: v1
      file: /etc/origin/master/htpasswd
      kind: HTPasswdPasswordIdentityProvider
```

이전 줄 바로 뒤에 다음 줄을 삽입합니다.

```yaml
  - name: <App Registration Name>
    challenge: false
    login: true
    mappingMethod: claim
    provider:
      apiVersion: v1
      kind: OpenIDIdentityProvider
      clientID: <appId>
      clientSecret: <Strong Password>
      claims:
        id:
        - sub
        preferredUsername:
        - unique_name
        name:
        - name
        email:
        - email
      urls:
        authorize: https://login.microsoftonline.com/<tenant Id>/oauth2/authorize
        token: https://login.microsoftonline.com/<tenant Id>/oauth2/token
```

identityProviders 아래의 텍스트가 제대로 정렬되어 있는지 확인합니다. 다음 CLI 명령을 사용하여 테넌트 ID를 찾습니다. ```az account show```

모든 마스터 노드에서 OpenShift 마스터 서비스를 다시 시작합니다.

```bash
sudo /usr/local/bin/master-restart api
sudo /usr/local/bin/master-restart controllers
```

OpenShift 콘솔에 htpasswd_auth 및 [앱 등록]이라는 두 가지 인증 옵션이 표시됩니다.

## <a name="monitor-openshift-with-azure-monitor-logs"></a>Azure Monitor 로그를 사용하여 OpenShift 모니터링

OpenShift에 Log Analytics 에이전트를 추가하는 방법에는 세 가지가 있습니다.
- 각 OpenShift 노드에 직접 Linux용 Log Analytics 에이전트 설치
- 각각의 OpenShift 노드에 Azure Monitor VM 확장을 사용하도록 설정
- Log Analytics 에이전트를 OpenShift 디먼 집합으로 설치

자세한 내용은 [지침](../../azure-monitor/containers/containers.md#configure-a-log-analytics-agent-for-red-hat-openshift) 전문을 읽어보세요.

## <a name="configure-metrics-and-logging"></a>메트릭 및 로깅 구성

분기에 따라, OpenShift Container Platform 및 OKD용 Azure Resource Manager 템플릿은 설치의 일부로 메트릭 및 로깅을 사용하도록 설정하기 위한 입력 매개 변수를 제공할 수 있습니다.

또한 OpenShift Container Platform Marketplace 제품은 클러스터 설치 동안 메트릭 및 로깅을 사용하도록 설정하기 위한 옵션도 제공합니다.

클러스터를 설치하는 동안 메트릭/로깅을 사용하도록 설정하지 않으면 사후에 쉽게 사용하도록 설정할 수 있습니다.

### <a name="azure-cloud-provider-in-use"></a>사용 중인 Azure Cloud Provider

배포 중에 제공된 자격 증명을 사용하여 bastion 노드 또는 첫 번째 마스터 노드(사용 중인 템플릿 및 분기에 따라)에 대해 SSH를 수행합니다. 다음 명령을 실행합니다.

```bash
ansible-playbook /usr/share/ansible/openshift-ansible/playbooks/openshift-metrics/config.yml \
-e openshift_metrics_install_metrics=True \
-e openshift_metrics_cassandra_storage_type=dynamic

ansible-playbook /usr/share/ansible/openshift-ansible/playbooks/openshift-logging/config.yml \
-e openshift_logging_install_logging=True \
-e openshift_logging_es_pvc_dynamic=true
```

### <a name="azure-cloud-provider-not-in-use"></a>사용되지 않는 Azure Cloud Provider

```bash
ansible-playbook /usr/share/ansible/openshift-ansible/playbooks/openshift-metrics/config.yml \
-e openshift_metrics_install_metrics=True

ansible-playbook /usr/share/ansible/openshift-ansible/playbooks/openshift-logging/config.yml \
-e openshift_logging_install_logging=True
```

## <a name="install-open-service-broker-for-azure-osba"></a>OSBA(Open Service Broker for Azure) 설치

Open Service Broker for Azure 또는 OSBA를 사용하여 OpenShift에서 직접 Azure Cloud Services를 프로비전할 수 있습니다. OSBA는 Azure에서 구현된 Open Service Broker API입니다. Open Service Broker API는 클라우드 기본 애플리케이션이 잠금 없이 클라우드 서비스를 관리하는 데 사용할 수 있는 클라우드 공급자에 대한 공용 언어를 정의하는 사양입니다.

OpenShift에 OSBA를 설치하려면 https://github.com/Azure/open-service-broker-azure#openshift-project-template의 지침을 따르세요. 
> [!NOTE]
> OpenShift 프로젝트 템플릿 섹션의 단계만 완료하고 전체 설치 섹션은 완료하지 않습니다.

## <a name="next-steps"></a>다음 단계

- [OpenShift Container Platform 시작](https://docs.openshift.com)
