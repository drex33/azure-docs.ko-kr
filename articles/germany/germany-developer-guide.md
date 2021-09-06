---
title: Azure 독일 개발자 가이드 | Microsoft Docs
description: 이 문서에서는 Azure 독일의 기능을 비교하고 애플리케이션 개발에 대한 지침을 제공합니다.
ms.topic: article
ms.date: 10/16/2020
author: gitralf
ms.author: ralfwi
ms.service: germany
ms.custom: bfdocs
ms.openlocfilehash: e631c37123c375ac3005aceb6c51289f07d9a22d
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/21/2020
ms.locfileid: "122566181"
---
# <a name="azure-germany-developer-guide"></a>Azure 독일 개발자 가이드

[!INCLUDE [closureinfo](../../includes/germany-closure-info.md)]

Azure 독일 환경은 Microsoft 네트워크의 나머지 부분과는 별개인 Microsoft Azure 인스턴스입니다. 이 가이드에서는 애플리케이션 개발자와 관리자가 Azure의 개별 지역과 상호 작용하고 작업하기 위해 이해해야 하는 차이점에 대해 설명합니다.

## <a name="overview"></a>개요
Microsoft에서는 개발자가 글로벌 Microsoft Azure 서비스("글로벌 Azure") 및 Microsoft Azure 독일 서비스에 클라우드 애플리케이션을 만들고 배포하는 데 도움이 되는 다양한 도구를 제공합니다. Azure 독일은 독일 데이터 개인 정보 보호 규정을 준수하기 위한 고객의 보안 및 규정 준수 요구 사항을 해결합니다. Azure 독일은 글로벌 Azure 배포에서 물리적 및 네트워크 격리를 제공하고 독일 법률에 따라 조치를 하는 데이터 보안 트러스티를 제공합니다.

개발자는 글로벌 Azure와 달리 Azure 독일에 애플리케이션을 만들고 배포할 때 두 서비스 집합 간의 차이점을 알아야 합니다. 특히 프로그래밍 환경 설정 및 구성, 엔드포인트 구성, 애플리케이션 작성 및 Azure 독일에 서비스로 애플리케이션 배포 등에 대해 이해해야 합니다.

이 가이드의 정보는 이러한 차이점을 요약합니다. [Azure 독일](https://azure.microsoft.com/overview/clouds/germany/ "Azure 독일") 사이트 및 [Azure 설명서 센터](https://azure.microsoft.com/documentation/)에서 사용할 수 있는 정보를 보완합니다. 

공식 정보는 다음과 같은 다른 위치에서도 사용할 수 있습니다.
* [Microsoft Azure 보안 센터](https://azure.microsoft.com/support/trust-center/ "Microsoft Azure 보안 센터") 
* [Azure 블로그](https://azure.microsoft.com/blog/ "Azure 블로그")
* [Azure 독일 블로그](/archive/blogs/azuregermany/ "Azure 독일 블로그")

## <a name="guidance-for-developers"></a>개발자를 위한 지침
현재 제공되는 대부분의 기술 내용은 Azure 독일이 아닌 글로벌 Azure를 위해 개발되고 있다고 가정하고 있습니다. 이러한 이유로 Azure 독일에서 호스팅하기 위해 개발하는 애플리케이션의 두 가지 주요 차이점을 알고 있어야 합니다.

* 글로벌 Azure의 특정 지역에 있는 특정 서비스와 기능이 Azure 독일에서 제공되지 않을 수 있습니다.
* Azure 독일의 기능 구성은 글로벌 Azure의 기능 구성과 다를 수 있습니다. 샘플 코드, 구성 및 단계를 검토하여 Azure 독일 Cloud Services 환경 내에서 빌드하고 실행하는지 확인해야 합니다.

현재 독일 중부 및 독일 북동부는 Azure 독일에서 사용할 수 있는 지역입니다. 지역 및 사용 가능한 서비스는 [지역별 사용 가능한 제품](https://azure.microsoft.com/regions/services)을 참조하세요.


## <a name="endpoint-mapping"></a>엔드포인트 매핑
글로벌 Azure 및 Azure SQL Database 엔드포인트를 Azure 독일 특정 엔드포인트에 매핑하는 방법에 대해 알아보려면 다음 표를 참조합니다.

| Name | Azure 독일 엔드포인트 |
| --- | --- |
| ActiveDirectoryServiceEndpointResourceId | `https://management.core.cloudapi.de/` |
| GalleryUrl                               | `https://gallery.cloudapi.de/` |
| ManagementPortalUrl                      | `https://portal.microsoftazure.de/` |
| ServiceManagementUrl                     | `https://management.core.cloudapi.de/` |
| PublishSettingsFileUrl                   | `https://manage.microsoftazure.de/publishsettings/index` |
| ResourceManagerUrl                       | `https://management.microsoftazure.de/` |
| SqlDatabaseDnsSuffix                     | `.database.cloudapi.de` |
| StorageEndpointSuffix                    | `core.cloudapi.de` |
| ActiveDirectoryAuthority                 | `https://login.microsoftonline.de/` |
| GraphUrl                                 | `https://graph.cloudapi.de/` |
| TrafficManagerDnsSuffix                  | `azuretrafficmanager.de` |
| AzureKeyVaultDnsSuffix                   | `vault.microsoftazure.de` |
| AzureKeyVaultServiceEndpointResourceId   | `https://vault.microsoftazure.de` |
| Service Bus 접미사                       | `servicebus.cloudapi.de` |


## <a name="next-steps"></a>다음 단계
Azure 독일에 대한 자세한 내용은 다음 리소스를 참조하세요.

* [평가판에 등록](https://azure.microsoft.com/free/germany/)
* [Azure 독일 획득](https://azure.microsoft.com/overview/clouds/germany/)
* Azure 독일 계정이 이미 있는 경우 [로그인 페이지](https://portal.microsoftazure.de/)
* [Azure 독일 개요](./germany-welcome.md)
* [Azure 독일 블로그](/archive/blogs/azuregermany/)
* [Azure 규정 준수](https://www.microsoft.com/en-us/trustcenter/compliance/complianceofferings)