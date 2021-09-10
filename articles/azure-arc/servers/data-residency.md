---
title: 데이터 상주
description: Azure Arc 지원 서버에 대한 데이터 상주 및 정보.
ms.topic: reference
ms.date: 08/05/2021
ms.custom: references_regions
ms.openlocfilehash: 05dbea8ce3d91e46bbb566f9d9989dec648c9853
ms.sourcegitcommit: 2da83b54b4adce2f9aeeed9f485bb3dbec6b8023
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/24/2021
ms.locfileid: "122770971"
---
# <a name="azure-arc-enabled-servers-data-residency"></a>Azure Arc 지원 서버: 데이터 상주

이 문서에서는 데이터 상주 개념과 Azure Arc 지원 서버에 적용되는 방식을 설명합니다.

Azure Arc 지원 서버는 **미국, 유럽, 영국, 오스트레일리아 및 아시아 태평양** 에서 **[사용할 수](https://azure.microsoft.com/global-infrastructure/services/?products=azure-arc)** 있습니다.

## <a name="data-residency"></a>데이터 상주

Azure Arc 지원 서버는 연결된 서버에서 사용하도록 설정하기 전에 확장을 지정해야 하는 [Azure VM 확장](manage-vm-extensions.md) 구성 설정(즉, 속성 값)을 저장합니다. 예를 들어 Log Analytics VM 확장을 사용하도록 설정하면 Log Analytics **작업 영역 ID** 및 **기본 키** 를 요청합니다.

연결된 머신에 대한 메타데이터 정보도 수집됩니다. 특히:

* 운영 체제 이름, 형식 및 버전
* 컴퓨터 이름
* 컴퓨터 FQDN(정규화된 도메인 이름)
* Connected Machine 에이전트 버전
* Active Directory 및 DNS FQDN(정규화된 도메인 이름)
* UUID(BIOS ID)
* Connected Machine 에이전트 하트비트
* Connected Machine 에이전트 버전
* 관리 ID에 대한 공개 키
* 정책 준수 상태 및 세부 정보(게스트 구성 정책을 사용하는 경우)

Arc 지원 서버를 사용하면 데이터가 저장되는 지역을 지정할 수 있습니다. Microsoft는 데이터 복원력을 위해 다른 지역에 복제할 수 있지만, Microsoft는 지리 외부로 데이터를 복제하거나 이동하지 않습니다. 이 데이터는 Azure Arc 머신 리소스가 구성된 지역에 저장됩니다. 예를 들어 머신이 미국 동부 지역에 있는 Arc에 등록된 경우 이 데이터는 미국 지역에 저장됩니다.

> [!NOTE] 
> 동남 아시아의 경우 데이터가 이 지역 외부로 복제되지 않습니다. 

지역 복원력 및 규정 준수 지원에 대한 자세한 내용은 [Azure 지리](https://azure.microsoft.com/global-infrastructure/geographies/)를 참조하세요.

## <a name="next-steps"></a>다음 단계

[Azure 복원력](/azure/architecture/reliability/architect) 설계에 대해 자세히 알아보세요.
