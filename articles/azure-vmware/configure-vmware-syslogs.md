---
title: Azure VMware 솔루션에 대 한 VMware syslog 구성
description: Azure VMware 솔루션 사설 클라우드에 대해 VMware syslog를 수집 하도록 진단 설정을 구성 하는 방법에 대해 알아봅니다.
ms.topic: how-to
ms.date: 09/24/2021
ms.openlocfilehash: 983a62eb5b094c94048e7580fd53558df002d816
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128634547"
---
# <a name="configure-vmware-syslogs-for-azure-vmware-solution"></a>Azure VMware 솔루션에 대 한 VMware syslog 구성

진단 설정은 사용자가 선택한 대상으로 리소스에 대 한 플랫폼 로그 및 메트릭의 스트리밍 내보내기를 구성 하는 데 사용 됩니다. 서로 다른 로그 및 메트릭을 독립적인 대상으로 보내는 최대 5 개의 서로 다른 진단 설정을 만들 수 있습니다. 

이 항목에서는 Azure VMware 솔루션 사설 클라우드에 대해 VMware syslog를 수집 하도록 진단 설정을 구성 합니다. 저장소 계정에 syslog를 저장 하 여 vCenter 로그를 보고 진단 목적을 분석 합니다. 

## <a name="prerequisites"></a>필수 구성 요소

VCenter 및 NSX Manager 인터페이스에 대 한 액세스 권한이 있는 Azure VMware 솔루션 사설 클라우드가 있는지 확인 합니다. 

## <a name="configure-diagnostic-settings"></a>진단 설정 구성

1. Azure VMware 솔루션 사설 클라우드에서 **진단 설정** 을 선택 하 고 **진단 설정을 추가** 합니다.
 
   :::image type="content" source="media/diagnostic-settings/diagnostic-settings-log-analytics-workspace-1.png" alt-text="VMware syslog를 구성 하는 위치를 보여 주는 스크린샷" lightbox="media/diagnostic-settings/diagnostic-settings-log-analytics-workspace-1.png":::


1. **Vmwaresyslog**, **allmetrics** 및 **저장소 계정에 보관 옵션을** 선택 합니다.

   >[!IMPORTANT]
   >**Log analytics 작업 영역으로 보내기** 옵션이 현재 작동 하지 않습니다.
 
1. 로그를 저장 하려는 저장소 계정을 선택 하 고 **저장** 을 선택 합니다.

   :::image type="content" source="media/diagnostic-settings/diagnostic-settings-log-analytics-workspace-2.png" alt-text="Syslog을 저장 하기 위해 선택할 수 있는 옵션을 보여 주는 스크린샷" lightbox="media/diagnostic-settings/diagnostic-settings-log-analytics-workspace-2.png":::

1. 저장소 및 계정으로 이동 하 여 정보 **로그 vmwarelog** 이 생성 되었는지 확인 하 고이를 선택 합니다. 
 
   :::image type="content" source="media/diagnostic-settings/diagnostic-settings-log-analytics-workspace-3.png" alt-text="Vmwarelog 옵션을 만들고 사용할 수 있는 정보 로그를 보여 주는 스크린샷" lightbox="media/diagnostic-settings/diagnostic-settings-log-analytics-workspace-3.png":::


1. 해당 위치로 이동 하 고 json 파일을 다운로드 하 여 로그를 확인 합니다.

   :::image type="content" source="media/diagnostic-settings/diagnostic-settings-log-analytics-workspace-4.png" alt-text="Json 파일에 대 한 드릴 다운 경로를 보여 주는 스크린샷" lightbox="media/diagnostic-settings/diagnostic-settings-log-analytics-workspace-4.png"::: 

