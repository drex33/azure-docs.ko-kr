---
title: Azure CLI를 사용하여 Azure CDN(Content Delivery Network) 프로필 및 엔드포인트 만들기
description: Azure CDN 프로필, 엔드포인트, 원본 그룹, 원본 및 사용자 지정 도메인을 만드는 Azure CLI 샘플 스크립트.
author: duongau
ms.author: duau
manager: danielgi
ms.date: 03/09/2021
ms.topic: sample
ms.service: azure-cdn
ms.devlang: azurecli
ms.custom: devx-track-azurecli
ms.openlocfilehash: 46966ed6e4e0db1be50919d368bf1ff2fd0fa85a
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131476855"
---
# <a name="create-an-azure-cdn-profile-and-endpoint-using-the-azure-cli"></a>Azure CLI를 사용하여 Azure CDN 프로필 및 엔드포인트 만들기

Azure Portal 대신 이러한 샘플 Azure CLI 스크립트를 사용하여 다음 CDN 작업을 관리할 수 있습니다.

- CDN 프로필을 만듭니다.
- CDN 엔드포인트 만들기
- CDN 원본 그룹을 만들고 기본 그룹으로 지정합니다.
- CDN 원본을 만듭니다.
- 사용자 지정 도메인을 만들고 HTTPS를 사용하도록 설정합니다.

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../../includes/azure-cli-prepare-your-environment.md)]

## <a name="sample-scripts"></a>샘플 스크립트

CDN 프로필에 대한 리소스 그룹이 아직 없는 경우 `az group create` 명령을 사용하여 만듭니다.

```azurecli
# Create a resource group to use for the CDN.
az group create --name MyResourceGroup --location eastus

```

다음 Azure CLI 스크립트는 CDN 프로필 및 CDN 엔드포인트를 만듭니다.

```azurecli
# Create a CDN profile.
az cdn profile create --resource-group MyResourceGroup --name MyCDNProfile --sku Standard_Microsoft

# Create a CDN endpoint.
az cdn endpoint create --resource-group MyResourceGroup --name MyCDNEndpoint --profile-name MyCDNProfile --origin www.contoso.com

```

다음 Azure CLI 스크립트는 CDN 원본 그룹을 만들고, 엔드포인트에 대한 기본 원본 그룹을 설정하고, 새 원본을 만듭니다.

```azurecli
# Create an origin group.
az cdn origin-group create --resource-group MyResourceGroup --endpoint-name MyCDNEndpoint --profile-name MyCDNProfile --name MyOriginGroup --origins origin-0

# Make the origin group the default group of an endpoint.
az cdn endpoint update --resource-group MyResourceGroup --name MyCDNEndpoint --profile-name MyCDNProfile --default-origin-group MyOriginGroup
                           
# Create another origin for an endpoint.
az cdn origin create --resource-group MyResourceGroup --endpoint-name MyCDNEndpoint --profile-name MyCDNProfile --name origin-1 --host-name example.contoso.com

```

다음 Azure CLI 스크립트는 CDN 사용자 지정 도메인을 만들고 HTTPS를 사용하도록 설정합니다. 사용자 지정 도메인을 Azure CDN 엔드포인트와 연결하려면 먼저 Azure DNS 또는 DNS 공급자를 통해 CNAME(Canonical Name) 레코드를 만들어서 CDN 엔드포인트를 가리켜야 합니다. 자세한 내용은 [CNAME DNS 레코드 만들기](../../../cdn/cdn-map-content-to-custom-domain.md#create-a-cname-dns-record)를 참조하세요.

```azurecli
# Associate a custom domain with an endpoint.
az cdn custom-domain create --resource-group MyResourceGroup --endpoint-name MyCDNEndpoint --profile-name MyCDNProfile --name MyCustomDomain --hostname www.example.com

# Enable HTTPS on the custom domain.
az cdn custom-domain enable-https --resource-group MyResourceGroup --endpoint-name MyCDNEndpoint --profile-name MyCDNProfile --name MyCustomDomain

```

## <a name="clean-up-resources"></a>리소스 정리

샘플 스크립트 실행을 완료한 후에는 다음 명령을 사용하여 리소스 그룹 및 해당 그룹에 연결된 모든 리소스를 제거합니다.

```azurecli
# Delete the resource group.
az group delete --name MyResourceGroup

```

## <a name="azure-cli-commands-used-in-this-article"></a>이 문서에서 사용되는 Azure CLI 명령

- [az cdn endpoint create](/cli/azure/cdn/endpoint#az_cdn_endpoint_create)
- [az cdn endpoint update](/cli/azure/cdn/endpoint#az_cdn_endpoint_update)
- [az cdn origin create](/cli/azure/cdn/origin#az_cdn_origin_create)
- [az cdn origin-group create](/cli/azure/cdn/origin-group#az_cdn_origin_group_create)
- [az cdn profile create](/cli/azure/cdn/profile#az_cdn_profile_create)
- [az group create](/cli/azure/group#az_group_create)
- [az group delete](/cli/azure/group#az_group_delete)
- [az cdn custom-domain create](/cli/azure/cdn/custom-domain#az_cdn_custom_domain_create)
- [az cdn custom-domain enable-https](/cli/azure/cdn/custom-domain#az_cdn_custom_domain_enable_https)
