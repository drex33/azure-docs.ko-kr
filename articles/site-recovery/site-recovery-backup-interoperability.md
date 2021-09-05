---
title: Azure Backup에서 Azure Site Recovery 사용에 대한 지원
description: Azure Site Recovery 및 Azure Backup을 함께 사용할 수 있는 방법에 대한 개요를 제공합니다.
author: sideeksh
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 10/15/2019
ms.author: sideeksh
ms.openlocfilehash: 4f96ba97537763bb4aec0ce558a12d675369ad57
ms.sourcegitcommit: e0ef8440877c65e7f92adf7729d25c459f1b7549
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/09/2021
ms.locfileid: "113567761"
---
# <a name="support-for-using-site-recovery-with-azure-backup"></a>Azure Backup에서 Site Recovery 사용에 대한 지원

이 문서에서는 [Azure Backup 서비스](../backup/backup-overview.md)와 함께 [Site Recovery 서비스](site-recovery-overview.md)를 사용하기 위한 지원을 요약합니다.

**동작** | **Site Recovery 지원** | **세부 정보**
--- | --- | ---
**여러 서비스 배포** | 지원됨 | 서비스는 상호 운용할 수 있으며 함께 구성할 수 있습니다.
**파일 백업/복원** | 지원됨 | VM에 대해 백업 및 복제를 사용하도록 설정하고 백업을 수행하는 경우 원본 측 VM 또는 VM 그룹에서 파일을 복원하는 데 문제가 없습니다. 복제 상태를 변경하지 않고 정상적으로 복제를 계속합니다.
**디스크 복원** | 현재 지원 없음 | 백업된 디스크를 복원하는 경우 VM에 대한 복제를 사용하지 않도록 설정했다가 다시 사용하도록 설정해야 합니다.
**VM 복원** | 현재 지원 없음 | VM 또는 VM 그룹을 복원하는 경우 VM에 대한 복제를 사용하지 않도록 설정했다가 다시 사용하도록 설정해야 합니다.  

위의 표는 지원되는 모든 Azure Site Recovery 시나리오에 적용됩니다.
