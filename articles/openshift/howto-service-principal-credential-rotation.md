---
title: ARO(Azure Red Hat OpenShift) 클러스터에 대한 서비스 주체 자격 증명 회전
description: ARO(Azure Red Hat OpenShift)에서 서비스 주체 자격 증명을 회전하는 방법을 알아봅니다.
author: swiencki
ms.author: b-swiencki
ms.service: azure-redhat-openshift
ms.topic: article
ms.date: 05/31/2021
ms.openlocfilehash: 286126bdd3cfcc5139549d1137431c8016c99472
ms.sourcegitcommit: 8b38eff08c8743a095635a1765c9c44358340aa8
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/30/2021
ms.locfileid: "113090874"
---
# <a name="rotate-service-principal-credentials-for-your-azure-red-hat-openshift-aro-cluster"></a>ARO(Azure Red Hat OpenShift) 클러스터에 대한 서비스 주체 자격 증명 회전
이 문서에서는 [ARO](https://github.com/Azure/ARO-RP)(Azure Red Hat OpenShift) 클러스터에서 서비스 주체 자격 증명을 회전하는 데 필요한 세부 정보를 제공합니다.

## <a name="before-you-begin"></a>시작하기 전에
이 문서에서는 최신 업데이트가 적용된 기존 ARO 클러스터가 있다고 가정합니다.

ARO 클러스터 내에서 서비스 주체 자격 증명을 회전하기 위한 최소 Azure CLI 요구 사항은 2.24.0입니다.

Azure CLI 실행 버전을 확인하려면 다음을 수행합니다.
```azurecli-interactive
# Azure CLI version
az --version
```
Azure CLI를 설치 또는 업그레이드하려면 [Azure CLI 설치](/cli/azure/install-azure-cli)를 수행하세요.

다음 지침에서는 bash 구문을 사용합니다.

## <a name="service-principal-credential-rotation"></a>서비스 주체 자격 증명 회전
>[!IMPORTANT]
>  서비스 주체 자격 증명 회전은 클러스터 상태에 따라 2시간 이상이 걸릴 수 있습니다.

서비스 주체 자격 증명 회전에는 두 가지 방법이 있습니다.
 - [자동화된 서비스 주체 자격 증명 회전](#Automated-Service-Principal-Credential-Rotation)
 - [사용자가 제공한 클라이언트 ID 및 클라이언트-비밀 서비스 주체 자격 증명 회전](#User-Provided-client-id-and-client-secret-Service-Principal-Credential-Rotation)

### <a name="automated-service-principal-credential-rotation"></a>자동화된 서비스 주체 자격 증명 회전<a id="Automated-Service-Principal-Credential-Rotation"></a>
>[!IMPORTANT]
>  자동화된 서비스 주체 자격 증명을 회전하려면 Azure CLI 버전 2.24.0 이상으로 ARO 클러스터를 만들어야 합니다.

자동화된 서비스 주체 자격 증명 회전은 서비스 주체가 존재하는지 확인하고 새 서비스 주체를 회전하거나 만듭니다.

다음 명령을 사용하여 자동으로 서비스 주체 자격 증명을 회전합니다.

```azurecli-interactive
# Automatically rotate service principal credentials
az aro update --refresh-credentials --name MyManagedCluster --resource-group MyResourceGroup
```

### <a name="user-provided-client-id-and-client-secret-service-principal-credential-rotation"></a>사용자가 제공한 클라이언트 ID 및 클라이언트-비밀 서비스 주체 자격 증명 회전<a id="User-Provided-client-id-and-client-secret-Service-Principal-Credential-Rotation"></a>


다음 지침에 따라 사용자가 제공한 클라이언트 ID 및 클라이언트 비밀을 사용하여 서비스 주체 자격 증명을 수동으로 회전합니다.

서비스 사용자 clientId(`--client-id`)를 검색하고 `SP_ID` 환경 변수로 설정합니다.
```azurecli-interactive
# Retrieve the service principal clientId
SP_ID=$(az aro show --name MyManagedCluster --resource-group MyResourceGroup \
    --query servicePrincipalProfile.clientId -o tsv)
```
위의 `SP_ID` 변수를 사용하여 서비스 주체에 대한 새 보안 비밀(`--client-secret`)을 생성합니다. 새 보안 비밀을 `SP_SECRET` 환경 변수로 저장합니다.
```azurecli-interactive
# Generate a new secure secret for the service principal
SP_SECRET=$(az ad sp credential reset --name $SP_ID --query password -o tsv)
```
위의 환경 변수를 사용하여 서비스 주체 자격 증명을 회전합니다.
```azurecli-interactive
# Rotate service principal credentials
az aro update --client-id $SP_ID --client-secret $SP_SECRET \
    --name MyManagedCluster --resource-group MyResourceGroup
```

## <a name="troubleshoot"></a>문제 해결
### <a name="service-principal-expiration-date"></a>서비스 주체 만료 날짜
서비스 주체 자격 증명은 1년의 만료 날짜를 설정하고 지정된 기간 내에 회전해야 합니다.

만료 날짜가 경과하면 다음과 같은 오류가 발생할 수 있습니다.
```bash
Failed to refresh the Token for request to MyResourceGroup StatusCode=401
Original Error: Request failed. Status Code = '401'.
[with]
Response body: {"error":"invalid_client","error_description": The provided client secret keys are expired.
[or]
Response body: {"error":"invalid_client","error_description": Invalid client secret is provided.
```
서비스 주체 자격 증명의 만료 날짜를 확인하려면 다음을 실행합니다.
```azurecli-interactive
# Service principal expiry in ISO 8601 UTC format
SP_ID=$(az aro show --name MyManagedCluster --resource-group MyResourceGroup \
    --query servicePrincipalProfile.clientId -o tsv)
az ad sp credential list --id $SP_ID --query "[].endDate" -o tsv
```
서비스 주체 자격 증명이 만료된 경우 두 가지 자격 증명 회전 방법 중 하나를 사용하여 업데이트하세요.

### <a name="cluster-aad-application-contains-a-client-secret-with-an-empty-description"></a>클러스터 AAD 애플리케이션에 빈 설명의 클라이언트 암호가 포함되어 있습니다.
[자동화된 서비스 주체 자격 증명 회전](#Automated-Service-Principal-Credential-Rotation)을 사용하는 경우 다음 오류가 발생합니다.
```bash
$ az aro update --refresh-credentials --name MyManagedCluster --resource-group MyResourceGroup

Cluster AAD application contains a client secret with an empty description.
Please either manually remove the existing client secret and run `az aro update --refresh-credentials`,
or manually create a new client secret and run `az aro update --client-secret <ClientSecret>`.
```
클러스터가 Azure CLI 2.24.0 이상을 사용하여 생성되지 않았습니다. [사용자가 제공한 클라이언트 ID 및 클라이언트-비밀 서비스 주체 자격 증명 회전](#User-Provided-client-id-and-client-secret-Service-Principal-Credential-Rotation) 방법을 대신 사용합니다.

### <a name="azure-cli-aro-update-help"></a>Azure CLI ARO 업데이트 도움말
자세한 내용은 Azure CLI ARO 업데이트 도움말 명령을 참조하세요.
```azurecli-interactive
# Azure CLI ARO update help
az aro update -h
```
