---
title: Azure 스프링 클라우드에서 아웃바운드 공용 IP 주소를 식별하는 방법
description: 데이터베이스, 스토리지, Key Vault 등의 외부 리소스와 통신하기 위해 고정 아웃바운드 공용 IP 주소를 확인하는 방법입니다.
author: karlerickson
ms.author: karler
ms.service: spring-cloud
ms.topic: how-to
ms.date: 09/17/2020
ms.custom: devx-track-java
ms.openlocfilehash: cc3050cb7c92e0e46c02a4a12f218364b94b2d1f
ms.sourcegitcommit: 7f3ed8b29e63dbe7065afa8597347887a3b866b4
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122529590"
---
# <a name="how-to-identify-outbound-public-ip-addresses-in-azure-spring-cloud"></a>Azure 스프링 클라우드에서 아웃바운드 공용 IP 주소를 확인하는 방법

이 페이지에서는 Azure 스프링 클라우드 애플리케이션의 고정 아웃바운드 공용 IP 주소를 보는 방법을 설명합니다.  공용 IP는 데이터베이스, 스토리지 및 키 자격 증명 모음과 같은 외부 리소스와 통신하는 데 사용됩니다.

## <a name="how-ip-addresses-work-in-azure-spring-cloud"></a>Azure 스프링 클라우드에서 IP 주소가 작동하는 방식

Azure 스프링 클라우드 서비스에는 하나 이상의 아웃바운드 공용 IP 주소가 있습니다. 아웃바운드 공용 IP 주소의 수는 계층 및 기타 요인에 따라 달라질 수 있습니다.

아웃바운드 공용 IP 주소는 일반적으로 일정하며 동일하게 유지되지만 예외가 있습니다.

## <a name="when-outbound-ips-change"></a>아웃바운드 IP가 변경되는 경우

각 Azure 스프링 클라우드 인스턴스에는 지정된 시간의 아웃바운드 공용 IP 주소 수가 설정되어 있습니다. 애플리케이션에서 가령 백 엔드 데이터베이스로의 모든 아웃바운드 연결은 원래 IP 주소로 아웃바운드 공용 IP 주소 중 하나를 사용합니다. IP 주소는 런타임에 임의로 선택됩니다. 따라서 백 엔드 서비스는 아웃바운드 IP 주소의 방화벽을 열어야 합니다.

다음 작업 중 하나를 수행하는 경우 아웃바운드 공용 IP 개수가 변경됩니다.

- 계층 간에 Azure 스프링 클라우드 인스턴스를 업그레이드합니다.
- 비즈니스 요구에 따라 더 많은 아웃바운드 공용 IP에 대한 지원 티켓을 제출합니다.

## <a name="find-outbound-ips"></a>아웃바운드 IP 찾기

현재 Azure Portal의 서비스 인스턴스에서 사용되는 아웃바운드 공용 IP 주소를 찾으려면 인스턴스 왼쪽 창에서 **네트워킹** 을 선택합니다. 이러한 필드는 **아웃바운드 IP 주소** 필드에 나열됩니다.

Cloud Shell에서 다음 명령을 실행하면 동일한 정보를 찾을 수 있습니다.

```azurecli
az spring-cloud show --resource-group <group_name> --name <service_name> --query properties.networkProfile.outboundIps.publicIps --output tsv
```

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
* [Azure 리소스의 관리 ID에 대해 자세히 알아보기](https://github.com/MicrosoftDocs/azure-docs/blob/master/articles/active-directory/managed-identities-azure-resources/overview.md)
* [Azure Spring Cloud의 키 자격 증명 모음에 대해 자세히 알아보기](./tutorial-managed-identities-key-vault.md)
