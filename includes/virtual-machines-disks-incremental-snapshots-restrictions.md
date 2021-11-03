---
title: 포함 파일
description: 포함 파일
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 03/05/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: c6f83c1590057b8b46b19f3100947bd838666792
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131022320"
---
- 현재 증분 스냅샷은 구독 간에 이동할 수 없습니다.
- 현재는 지정된 시간에 특정 스냅샷 패밀리 중 최대 5개 스냅샷의 SAS URI만 생성할 수 있습니다.
- 해당 디스크의 구독 외부에 있는 특정 디스크에 대해 증분 스냅샷을 만들 수 없습니다.
- 5분마다 디스크당 최대 7개의 증분 스냅샷이 만들어질 수 있습니다.
- 단일 디스크에 대해 총 200개의 증분 스냅샷을 만들 수 있습니다.
- 4TB 경계에서 부모 디스크의 크기를 변경하기 전후에 만들어진 스냅샷 간의 변경 내용을 가져올 수 없습니다. 예를 들어 디스크 크기가 2tb 인 경우 증분 스냅숏을 `snapshot-a` 만들었습니다. 이제 디스크 크기를 6TB로 늘렸습니다. 그러면 다른 증분 스냅숏이 생성 `snapshot-b` 됩니다. 와 간의 변경 내용을 가져올 수 `snapshot-a` 없습니다 `snapshot-b` . 크기를 조정한 후에 생성 된의 전체 복사본을 다시 다운로드 해야 `snapshot-b` 합니다. 이후에 생성 된와 스냅숏 간의 변경 내용을 가져올 수 있습니다 `snapshot-b` `snapshot-b` .
