---
title: Azure Spring Cloud Service의 보안 컨트롤
description: Azure Spring Cloud Service에 기본 제공되는 보안 컨트롤을 사용합니다.
author: karlerickson
ms.author: karler
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 04/23/2020
ms.custom: devx-track-java
ms.openlocfilehash: 362916e0c4c35907fee7a781c758bc11b8f7f003
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130234116"
---
# <a name="security-controls-for-azure-spring-cloud-service"></a>Azure Spring Cloud Service의 보안 컨트롤

**이 문서는 다음에 적용됩니다.** ✔️ Java ✔️ C#

보안 컨트롤은 Azure Spring Cloud Service에 기본 제공됩니다.

보안 제어는 보안 취약성을 방지, 탐지 및 대응할 수 있는 서비스의 기능에 기여하는 Azure 서비스의 품질 또는 기능입니다.  각 컨트롤에 대해 *예* 또는 *아니요* 를 사용하여 현재 서비스에 적용되는지 여부를 나타냅니다.  서비스에 적용되지 않는 컨트롤에는 *N/A* 를 사용합니다.

**데이터 보호 보안 컨트롤**

| 보안 컨트롤 | 예/아니요 | 메모 | 문서화 |
|:-------------|:-------|:-------------------------------|:----------------------|
| 미사용 서버 쪽 암호화: Microsoft 관리형 키 | Yes | 사용자가 업로드한 원본 및 아티팩트, 구성 서버 설정, 앱 설정 및 영구 스토리지의 데이터는 미사용 콘텐츠를 자동으로 암호화하는 Azure Storage에 저장됩니다.<br><br>Config Server 캐시, 업로드된 원본에서 빌드된 런타임 바이너리 및 애플리케이션 수명 동안 애플리케이션 로그는 미사용 콘텐츠를 자동으로 암호화하는 Azure Managed Disk에 저장됩니다.<br><br>사용자가 업로드한 원본에서 빌드된 컨테이너 이미지는 미사용 이미지 콘텐츠를 자동으로 암호화하는 Azure Container Registry에 저장됩니다. | [미사용 데이터에 대한 Azure Storage 암호화](../storage/common/storage-service-encryption.md)<br><br>[Azure Managed Disks의 서버 쪽 암호화](../virtual-machines/disk-encryption.md)<br><br>[Azure Container Registry의 컨테이너 이미지 스토리지](../container-registry/container-registry-storage.md) |
| 일시적인 암호화 | Yes | 사용자 앱 퍼블릭 엔드포인트는 기본적으로 인바운드 트래픽에 HTTPS를 사용합니다. |  |
| API 호출 암호화 | 예 | Azure Spring Cloud Service를 구성하기 위한 관리 호출은 HTTPS를 통한 Azure Resource Manager 호출을 통해 발생합니다. | [Azure Resource Manager](../azure-resource-manager/index.yml) |

**네트워크 액세스 보안 컨트롤**.

| 보안 컨트롤 | 예/아니요 | 메모 | 문서화 |
|:-------------|:-------|:-------------------------------|:----------------------|
| 서비스 태그 | Yes | **AzureSpringCloud** 서비스 태그를 사용해 [네트워크 보안 그룹](../virtual-network/network-security-groups-overview.md#security-rules) 또는 [Azure Firewall](../firewall/service-tags.md)에서 아웃바운드 네트워크 액세스 컨트롤을 정의하여 Azure Spring Cloud 애플리케이션에 대한 트래픽을 허용합니다. | [서비스 태그](../virtual-network/service-tags-overview.md) |

## <a name="next-steps"></a>다음 단계

* [빠른 시작: Azure 스프링 클라우드에서 첫 번째 스프링 부팅 앱 배포](./quickstart.md)
