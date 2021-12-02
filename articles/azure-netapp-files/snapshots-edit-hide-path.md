---
title: 의 스냅샷 경로 숨기기 옵션을 편집합니다Azure NetApp Files | Microsoft Docs
description: Azure NetApp Files 사용하여 스냅샷 볼륨의 표시를 제어하는 방법을 설명합니다.
services: azure-netapp-files
documentationcenter: ''
author: b-hchen
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 09/16/2021
ms.author: b-hchen
ms.openlocfilehash: efce92cd930f1166dd425460fd670fffa53dea74
ms.sourcegitcommit: 9ef0965834870700468c822ddcafc011881fc2d5
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/02/2021
ms.locfileid: "133485481"
---
# <a name="edit-the-hide-snapshot-path-option-of-azure-netapp-files"></a>의 스냅샷 경로 숨기기 옵션을 편집합니다Azure NetApp Files
스냅샷 경로 숨기기 옵션은 볼륨의 스냅샷 경로가 표시되는지 여부를 제어합니다. [NFS](azure-netapp-files-create-volumes.md#create-an-nfs-volume) 또는 [SMB](azure-netapp-files-create-volumes-smb.md#add-an-smb-volume) 볼륨을 만드는 동안 스냅샷 경로를 숨길지 여부를 지정하는 옵션이 제공됩니다. 이후에 필요에 따라 스냅샷 경로 숨기기 옵션을 편집할 수 있습니다.  

> [!NOTE]
> 지역 간 복제의 [대상 볼륨](cross-region-replication-create-peering.md#create-the-data-replication-volume-the-destination-volume)에서는 스냅샷 경로 숨기기 옵션이 기본적으로 사용하도록 설정되며 이 설정을 수정할 수 없습니다. 

## <a name="steps"></a>단계

1. 볼륨의 스냅샷 경로 숨기기 옵션 설정을 보려면 볼륨을 선택합니다. **스냅샷 경로 숨기기** 필드에는 옵션이 사용하도록 설정되어 있는지 여부가 표시됩니다.   
    ![스냅샷 경로 숨기기 필드를 설명하는 스크린샷](../media/azure-netapp-files/hide-snapshot-path-field.png) 
2. 스냅샷 경로 숨기기 옵션을 편집하려면 볼륨 페이지에서 **편집** 을 클릭하고 필요에 따라 **스냅샷 경로 숨기기** 옵션을 수정합니다.   
    ![볼륨 스냅샷 편집 옵션을 설명하는 스크린샷](../media/azure-netapp-files/volume-edit-snapshot-options.png) 

## <a name="next-steps"></a>다음 단계

* [스냅샷에 대한 자세한 정보](snapshots-introduction.md)