---
title: 스냅샷에서 앱 복원
description: 스냅샷에서 앱을 복원하는 방법에 대해 알아봅니다. 자동 섀도 복사본을 사용하여 프리미엄 계층의 예기치 않은 데이터 손실로부터 복구합니다.
ms.assetid: 4164f9b5-f735-41c6-a2bb-71f15cdda417
ms.topic: article
ms.date: 09/02/2021
ms.reviewer: nicking
ms.custom: seodec18
ms.openlocfilehash: 48b3d859a73a2d63644e1d5881c3505cee93f9d7
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128679506"
---
# <a name="restore-an-app-in-azure-from-a-snapshot"></a>스냅샷에서 Azure의 앱 복원
이 문서에서는 스냅샷에서 [Azure App Service](../app-service/overview.md)의 앱을 복원하는 방법을 설명합니다. 앱의 스냅샷 중 하나를 기반으로 이전 상태로 앱을 복원할 수 있습니다. 스냅숏을 사용 하도록 설정할 필요는 없습니다. 플랫폼은 데이터 복구를 위해 모든 앱의 스냅숏을 자동으로 저장 합니다.

스냅숏은 App Service 앱의 증분 섀도 복사본입니다. 앱이 Premium 계층 이상에 있으면 App Service는 앱의 콘텐츠와 해당 구성의 스냅숏을 주기적으로 사용 합니다. [표준 백업](manage-backup.md)에 비해 여러 가지 이점을 제공 합니다.

- 파일 잠금으로 인한 파일 복사 오류가 없습니다.
- 최대 스냅숏 크기 (30GB)
- 지원 되는 가격 책정 계층에는 구성이 필요 하지 않습니다.
- 모든 Azure 지역의 새 App Service 앱으로 스냅숏을 복원할 수 있습니다.

스냅샷 복원은 **프리미엄** 계층 이상에서 실행되는 앱에서 사용할 수 있습니다. 앱 확장에 대한 자세한 내용은 [Azure에서 앱 확장](manage-scale-up.md)을 참조하세요.

## <a name="limitations"></a>제한 사항

- 스냅숏 복원에 지원 되는 최대 크기는 30GB입니다. 저장소 크기가 30GB 보다 큰 경우 스냅숏 복원이 실패 합니다. 저장소 크기를 줄이려면 로그, 이미지, 오디오 및 비디오와 같은 파일을 [Azure Storage](../storage/index.yml)로 이동 하는 것이 좋습니다 (예:).
- [Standard backup](manage-backup.md#what-gets-backed-up) 에서 지원 하거나 [탑재](configure-connect-to-azure-storage.md?pivots=container-windows) 한 모든 연결 된 데이터베이스는 스냅숏에 포함 *되지* 않습니다. 연결 된 Azure 서비스의 기본 백업 기능을 사용 하는 것이 좋습니다 (예: [SQL Database](../azure-sql/database/automated-backups-overview.md) 및 [Azure Files](../storage/files/storage-snapshots-files.md)).
- App Service는 스냅숏을 복원 하는 동안 대상 앱 또는 대상 슬롯을 중지 합니다. 프로덕션 앱에 대 한 가동 중지 시간을 최소화 하려면 먼저 [스테이징 슬롯](deploy-staging-slots.md) 에 스냅숏을 복원한 다음 프로덕션으로 교환 합니다.
- 지난 30 일간의 스냅숏을 사용할 수 있습니다. 보존 기간 및 스냅숏 빈도는 구성할 수 없습니다.
- App Service 환경에서 실행 중인 App Services는 스냅숏을 지원 하지 않습니다.

## <a name="restore-an-app-from-a-snapshot"></a>스냅샷에서 앱 복원

1. [Azure Portal](https://portal.azure.com)에서 앱의 **설정** 페이지에서 **백업** 을 클릭하여 **백업** 페이지를 표시합니다. 그런 다음, **스냅샷(미리 보기)** 섹션에서 **복원** 을 클릭합니다.
   
    ![스냅숏에서 앱을 복원 하는 방법을 보여 주는 스크린샷](./media/app-service-web-restore-snapshots/1.png)

2. **복원** 페이지에서 복원할 스냅샷을 선택합니다.
   
    ![복원할 스냅샷을 선택하는 방법을 보여 주는 스크린샷 ](./media/app-service-web-restore-snapshots/2.png)
   
3. **복원 대상** 에서 앱 복원 대상을 지정합니다.
   
    ![복원 대상을 지정하는 방법을 보여 주는 스크린샷](./media/app-service-web-restore-snapshots/3.png)
   
   > [!WARNING]
   > 모범 사례로, 새 슬롯으로 복원 하 고 교환을 수행 하는 것이 좋습니다. **덮어쓰기** 를 선택하면 앱의 현재 파일 시스템에 있는 기존 데이터를 모두 지우고 덮어씁니다. **확인** 을 클릭하기 전에 수행하려는 작업이 맞는지 확인하세요.
   > 
   > 
      
   > [!Note]
   > 현재의 기술적 제한으로 인해 동일한 배율 단위로만 앱을 복원할 수 있습니다. 이 제한 사항은 향후 릴리스에서 제거될 예정입니다.
   > 
   > 
   
    **기존 앱** 을 선택하여 슬롯에 복원할 수 있습니다. 이 옵션을 사용하려면 먼저 앱에 슬롯을 만들어야 합니다.

4. 사이트 구성을 복원하도록 선택할 수 있습니다.
   
    ![사이트 구성을 복원하는 방법을 보여 주는 스크린샷](./media/app-service-web-restore-snapshots/4.png)

5. **확인** 을 클릭합니다.
