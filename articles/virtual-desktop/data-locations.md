---
title: Windows Virtual Desktop의 데이터 위치 - Azure
description: Azure Virtual Desktop의 데이터와 메타데이터가 저장되는 위치에 대한 간략한 개요입니다.
author: Heidilohr
ms.topic: conceptual
ms.custom: references_regions
ms.date: 06/30/2021
ms.author: helohr
manager: femila
ms.openlocfilehash: bc356e828adbe9b2d93a202f6b58673dd4ec5efc
ms.sourcegitcommit: 695a33a2123429289ac316028265711a79542b1c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/01/2021
ms.locfileid: "113125909"
---
# <a name="data-locations-for-azure-virtual-desktop"></a>Azure Virtual Desktop의 데이터 위치

Azure Virtual Desktop은 현재 모든 지리적 위치에서 사용할 수 있습니다. 관리자는 호스트 풀 가상 머신 및 파일 서버와 같은 관련 서비스를 만들 때 사용자 데이터를 저장할 위치를 선택할 수 있습니다. [Azure 데이터 센터 맵](https://azuredatacentermap.azurewebsites.net/)에서 Azure 지리에 대해 자세히 알아보세요.

>[!NOTE]
>Microsoft는 사용자가 사용자 및 앱 특정 데이터에 액세스할 수 있는 지역을 제어하거나 제한하지 않습니다.

>[!IMPORTANT]
>Azure Virtual Desktop은 호스트 풀 이름, 앱 그룹 이름, 작업 영역 이름, 사용자 계정 이름과 같은 다양한 형식의 정보를 데이터 센터에 저장합니다. 서비스 개체를 만들 때, 고객은 개체를 만들어야 하는 위치를 입력해야 합니다. 개체의 위치에 따라 개체의 정보가 저장되는 위치가 결정됩니다. 고객이 Azure 지역을 선택하면 관련 정보가 연결된 지리에 저장됩니다. 모든 Azure 지역 및 관련 지역 목록은 [https://azure.microsoft.com/global-infrastructure/geographies/](https://azure.microsoft.com/global-infrastructure/geographies/)를 참조하세요.

이 문서에서는 Azure Virtual Desktop 서비스가 저장하는 정보를 설명합니다. 고객 데이터 정의에 대한 자세한 내용은 [Microsoft의 온라인 서비스 데이터 분류 방식](https://www.microsoft.com/trust-center/privacy/customer-data-definitions)을 참조하세요.

## <a name="customer-input"></a>고객 입력

Azure Virtual Desktop 서비스를 설정하려면 고객이 호스트 풀 및 기타 서비스 개체를 만들어야 합니다. 구성 시, 고객은 호스트 풀 이름, 애플리케이션 그룹 이름 등의 정보를 제공해야 합니다. 이 정보를 고객 입력으로 간주합니다. 고객 입력은 개체가 만들어진 지역과 연결된 지리에 저장됩니다. 개체에 대한 Azure Resource Manager 경로는 조직 정보로 간주되므로 데이터 보존이 적용되지 않습니다. Azure Resource Manager 경로에 대한 데이터는 선택한 지리 외부에 저장됩니다.

## <a name="customer-data"></a>고객 데이터

서비스는 사용자 또는 앱 관련 정보를 직접 저장하지 않지만 애플리케이션 이름 및 사용자 계정 이름과 같은 고객 데이터는 개체 설정 프로세스에 속하기 때문에 이러한 데이터는 저장합니다. 이 정보는 고객이 개체를 만든 지역과 연결된 지리에 저장됩니다.

## <a name="diagnostic-data"></a>진단 데이터

Azure Virtual Desktop은 고객 또는 사용자가 서비스와 상호 작용할 때마다 서비스에서 생성된 진단 데이터를 수집합니다. 이 데이터는 집계 형식으로 서비스 상태에 대한 문제 해결, 지원, 확인 작업에만 사용됩니다. 예를 들어 세션 호스트 측에서 VM이 서비스에 등록되면 VM(가상 머신) 이름, VM이 속한 호스트 풀 등이 포함된 정보가 생성됩니다. 이 정보는 호스트 풀이 만들어진 지역과 연결된 지리에 저장됩니다. 또한 사용자가 서비스에 연결하고 원격 데스크톱을 시작하면 사용자 계정 이름, 클라이언트 위치, 클라이언트 IP 주소, 사용자가 연결하는 호스트 풀 등이 포함된 진단 정보가 생성됩니다. 이 정보는 다음의 두 위치로 전송됩니다.

- 서비스 인프라(클라이언트 추적, 사용자 추적, 진단 데이터)가 있는 사용자와 가장 가까운 위치입니다.
- 호스트 풀이 있는 위치입니다.

## <a name="service-generated-data"></a>서비스 생성 데이터

Azure Virtual Desktop을 안정적이고 스케일링 가능한 상태로 유지하기 위해 트래픽 패턴 및 사용량이 집계되어 인프라 컨트롤 플레인의 상태와 성능이 확인됩니다. 예를 들어 서비스 사용량이 증가함에 따라 지역 인프라 용량을 늘리는 방법을 파악하기 위해 서비스 사용량 로그 데이터가 처리됩니다. 그런 다음, 사용량이 가장 많은 시간에 대한 로그가 검토되고 이 용량을 충족하기 위해 추가할 데이터 센터가 결정됩니다. 서비스 인프라가 있는 모든 위치에서 이러한 정보가 집계된 다음, 미국 지역으로 전송됩니다. 미국 지역으로 전송되는 데이터에는 스크럽된 데이터가 포함되지만 고객 데이터는 포함되지 않습니다.

현재 이러한 데이터 저장이 지원되는 위치는 다음과 같습니다.

- 미국(US)(일반적으로 사용 가능)
- 유럽(EU)(일반적으로 사용 가능)
- 영국(UK)(일반적으로 사용 가능)
- 캐나다(CA)(일반적으로 사용 가능)

서비스가 증가함에 따라 더 많은 지리가 추가될 예정입니다. 저장된 메타데이터는 미사용 상태에서 암호화되고 지역 중복 미러는 해당 지리 내에서 유지 관리됩니다. 앱 설정 및 사용자 데이터와 같은 모든 고객 데이터는 고객이 선택한 위치에 상주하며 서비스에서 관리되지 않습니다.

서비스 메타데이터는 재해 복구를 위해 Azure 지리 내에서 복제됩니다.
