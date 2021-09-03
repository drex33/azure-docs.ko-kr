---
title: Microsoft Endpoint Configuration Manager와 Azure Automation 업데이트 관리 통합
description: 이 문서에서는 업데이트 관리를 사용하여 Microsoft Endpoint Configuration Manager를 구성하여 소프트웨어 업데이트를 관리자 클라이언트에 배포하는 방법을 설명합니다.
services: automation
ms.subservice: update-management
ms.date: 07/14/2021
ms.topic: conceptual
ms.openlocfilehash: 6889d5058a7ca93e410afa0454e108d79bb70569
ms.sourcegitcommit: abf31d2627316575e076e5f3445ce3259de32dac
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/15/2021
ms.locfileid: "114203906"
---
# <a name="integrate-update-management-with-microsoft-endpoint-configuration-manager"></a>업데이트 관리를 Microsoft Endpoint Configuration Manager와 통합

PC, 서버 및 모바일 디바이스를 관리하기 위해 Microsoft Endpoint Configuration Manager에 투자하는 고객들은 SUM(소프트웨어 업데이트 관리) 주기의 일환으로 이 강력하고 완성도 있는 소프트웨어 업데이트 관리를 신뢰합니다.

Microsoft Endpoint Configuration Manager에서 소프트웨어 업데이트 배포를 만들고 미리 준비하여 관리형 Windows 서버를 보고 및 업데이트하고, [업데이트 관리](overview.md)를 사용하여 완료된 업데이트 배포에 대한 자세한 상태를 확인할 수 있습니다. Microsoft Endpoint Configuration Manager를 사용하여 업데이트 준수 보고를 수행하지만 Windows 서버를 통해 업데이트 배포를 관리하지 않는 경우, 보안 업데이트가 Azure Automation 업데이트 관리로 관리되는 동안 Microsoft Endpoint Configuration Manager에 계속 보고할 수 있습니다.

>[!NOTE]
>업데이트 관리는 Windows Server 2008 R2의 업데이트 평가 및 패치를 지원하지만 이 운영 체제를 실행하는 Microsoft Endpoint Configuration Manager에서 관리하는 클라이언트는 지원하지 않습니다.

## <a name="prerequisites"></a>사전 요구 사항

* Automation 계정에 추가된 [Azure Automation 업데이트 관리](overview.md)가 있어야 합니다.
* 현재 Microsoft Endpoint Configuration Manager 환경에서 관리되는 Windows 서버는 업데이트 관리를 사용하도록 설정되어 있는 Log Analytics 작업 영역에도 보고해야 합니다.
* 이 기능은 Microsoft Endpoint Configuration Manager 현재 분기 버전 1606 이상에서 사용할 수 있습니다. Microsoft Endpoint Configuration Manager 중앙 관리 사이트 또는 독립 실행형 기본 사이트를 Azure Monitor 로그와 통합하고 컬렉션을 가져오려면 [Azure Monitor 로그에 Configuration Manager 연결](../../azure-monitor/logs/collect-sccm.md)을 검토하세요.  
* Windows 에이전트는 WSUS(Windows Server Update Services) 서버와 통신하도록 구성되거나 Microsoft Endpoint Configuration Manager에서 보안 업데이트를 받지 않는 경우 Microsoft 업데이트에 액세스할 수 있어야 합니다.

주로 기존 Microsoft Endpoint Configuration Manager 환경으로 Azure IaaS에 호스트되는 클라이언트를 관리하는 방법은 Azure 데이터 센터와 사용자 인프라 사이의 연결에 따라 달라집니다. 이 연결은 사용자가 Microsoft Endpoint Configuration Manager 인프라에 가하는 디자인 변경과 해당 필수 변경 내용을 지원하기 위해 관련된 비용에 영향을 줍니다. 계속하기 전에 평가해야 하는 계획 고려 사항에 대해 알아보려면 [Azure에서 Configuration Manager - 질문과 대답](/configmgr/core/understand/configuration-manager-on-azure#networking)을 검토하세요.

## <a name="manage-software-updates-from-microsoft-endpoint-configuration-manager"></a>Microsoft Endpoint Configuration Manager에서 소프트웨어 업데이트 관리

Microsoft Endpoint Configuration Manager에서 업데이트 배포 관리를 계속하려는 경우 다음 단계를 수행합니다. Azure Automation에서 Microsoft Endpoint Configuration Manager에 연결하여 Log Analytics 작업 영역에 연결된 클라이언트 컴퓨터에 업데이트를 적용합니다. 업데이트 콘텐츠는 배포를 Microsoft Endpoint Configuration Manager에서 관리하는 경우 클라이언트 컴퓨터 캐시에서 사용할 수 있습니다.

1. [소프트웨어 업데이트 배포](/configmgr/sum/deploy-use/deploy-software-updates)에 설명된 프로세스를 사용하여 Microsoft Endpoint Configuration Manager 계층 구조의 최상위 사이트에서 소프트웨어 업데이트 배포를 만듭니다. 표준 배포와 다르게 구성해야 하는 유일한 설정은 Endpoint Configuration Manager의 **설치 최종 기한** 옵션입니다. Automation 업데이트 관리만 업데이트 배포를 시작하도록 하려면 미래 날짜로 설정해야 합니다. 이 설정은 [4단계, 소프트웨어 업데이트 그룹 배포](/configmgr/sum/deploy-use/manually-deploy-software-updates#BKMK_4DeployUpdateGroup)에서 설명합니다.

2. Endpoint Configuration Manager에서 대상 머신에 알림이 표시되지 않도록 **사용자 알림** 옵션을 구성합니다. 로그온한 사용자가 예약된 업데이트 배포에 관한 알림을 받고 해당 업데이트를 수동으로 배포하지 않도록 하려면 **소프트웨어 센터 및 모든 알림에서 숨기기** 옵션을 설정하는 것이 좋습니다. 이 설정은 [4단계, 소프트웨어 업데이트 그룹 배포](/configmgr/sum/deploy-use/manually-deploy-software-updates#BKMK_4DeployUpdateGroup)에서 설명합니다.

3. Azure Automation에서 **업데이트 관리** 를 선택합니다. [업데이트 배포 만들기](deploy-updates.md#schedule-an-update-deployment)에서 설명한 단계에 따라 새 배포를 만들고, **유형** 드롭다운에서 **가져온 그룹** 을 선택하여 적절한 Microsoft Endpoint Configuration Manager 컬렉션을 선택합니다. 다음 중요 사항을 주의하세요.

    a. 선택한 Microsoft Endpoint Configuration Manager 디바이스 컬렉션에 유지 관리 기간이 정의되어 있는 경우 컬렉션의 멤버는 예약된 배포에 정의된 **기간** 설정 대신 이 기간을 사용합니다.

    b. 대상 컬렉션의 구성원은 인터넷에 연결되어 있어야 합니다(직접, 프록시 서버 또는 Log Analytics 게이트웨이를 통해).

Azure Automation을 통해 업데이트 배포가 완료되면, 선택한 컴퓨터 그룹의 멤버인 대상 컴퓨터에서 로컬 클라이언트 캐시의 예약된 시간에 업데이트를 설치합니다. [업데이트 배포 상태를 확인하여](deploy-updates.md#check-deployment-status) 배포 결과를 모니터링할 수 있습니다.

## <a name="manage-software-updates-from-azure-automation"></a>Azure Automation에서 소프트웨어 업데이트 관리

Microsoft Endpoint Configuration Manager 클라이언트인 Windows Server VM에 대한 업데이트를 관리하려면 업데이트 관리에서 관리하는 모든 클라이언트에 대해 소프트웨어 업데이트 관리 기능을 사용하지 않도록 클라이언트 정책을 구성해야 합니다. 기본적으로 클라이언트 설정은 계층의 모든 디바이스를 대상으로 합니다. 이 정책 설정 및 구성하는 방법에 대한 자세한 내용은 [Configuration Manager에서 클라이언트 설정을 구성하는 방법](/configmgr/core/clients/deploy/configure-client-settings)을 검토합니다.

이렇게 구성을 변경한 후에 [업데이트 배포 만들기](deploy-updates.md#schedule-an-update-deployment)에서 설명한 단계에 따라 새 배포를 만들고, **유형** 드롭다운에서 **가져온 그룹** 을 선택하여 적절한 Microsoft Endpoint Configuration Manager 컬렉션을 선택합니다.

## <a name="next-steps"></a>다음 단계

통합 일정을 설정하려면 [업데이트 배포 예약](deploy-updates.md#schedule-an-update-deployment)을 참조하세요.
