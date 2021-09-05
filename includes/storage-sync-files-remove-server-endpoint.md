---
title: 포함 파일
description: 포함 파일
services: storage
author: roygara
ms.service: storage
ms.topic: include
ms.date: 05/31/2018
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: d527af6607dfdaf2cf2b5de7b6adbfed40f74895
ms.sourcegitcommit: 9339c4d47a4c7eb3621b5a31384bb0f504951712
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/14/2021
ms.locfileid: "113760130"
---
없음: 서버 엔드포인트를 제거하는 것은 서버를 다시 부팅하는 것과 다릅니다! 서버 엔드포인트를 제거했다가 다시 만드는 것은 동기화, 클라우드 계층화 또는 Azure 파일 동기화의 다른 측면과 관련된 문제를 해결하기 위한 적절한 솔루션이 아닙니다. 서버 엔드포인트를 제거하는 것은 파괴적인 작업입니다. 계층화된 파일이 서버 엔드포인트 네임스페이스 외부에 존재하는 경우 데이터가 손실될 수 있습니다. 자세한 내용은 [계층화된 파일이 서버 엔드포인트 네임스페이스 외부에 존재하는 이유](../articles/storage/files/storage-files-faq.md#afs-tiered-files-out-of-endpoint)를 참조하세요. 또는 서버 엔드포인트 네임스페이스 내에 있는 계층화된 파일에 액세스할 수 없는 파일이 생성될 수 있습니다. 이러한 문제는 서버 엔드포인트를 다시 만들어도 해결되지 않습니다. 클라우드 계층화를 사용하도록 설정하지 않아도 계층화된 파일이 서버 엔드포인트 네임스페이스에 있을 수 있습니다. 따라서 이 특정 폴더에 Azure 파일 동기화를 더 이상 사용하지 않거나 Microsoft 엔지니어가 명시적으로 이렇게 하도록 지시한 경우가 아니라면, 서버 엔드포인트를 제거하지 않는 것이 좋습니다. 서버 엔드포인트 제거에 대한 자세한 내용은 [서버 엔드포인트 제거](../articles/storage/file-sync/file-sync-server-endpoint-delete.md)를 참조하세요.    
