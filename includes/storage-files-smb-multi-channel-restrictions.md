---
title: 포함 파일
description: 포함 파일
services: storage
author: roygara
ms.service: storage
ms.topic: include
ms.date: 07/14/2021
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: b2ed6b2b47ead64eac664f789aa8eba7887e68e7
ms.sourcegitcommit: 92dd25772f209d7d3f34582ccb8985e1a099fe62
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/15/2021
ms.locfileid: "114228804"
---
Azure 파일 공유에 대한 SMB 다중 채널에는 현재 다음과 같은 제한 사항이 있습니다.
- SMB 3.1.1을 사용하는 [Windows](../articles/storage/files/storage-how-to-use-files-windows.md) 및 [Linux](../articles/storage/files/storage-how-to-use-files-linux.md) 클라이언트에서만 지원됩니다.
- 최대 채널 수는 4개입니다. 자세한 내용은 [여기](../articles/storage/files/storage-troubleshooting-files-performance.md#cause-4-number-of-smb-channels-exceeds-four)를 참조하세요.
- SMB Direct는 지원되지 않습니다.
- 스토리지 계정에 대한 프라이빗 엔드포인트는 지원되지 않습니다.