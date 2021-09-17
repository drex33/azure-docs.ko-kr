---
title: Azure Storage Explorer 직접 링크 | Microsoft Docs
description: Azure Storage Explorer 직접 링크의 설명서
services: storage
author: JasonYeMSFT
ms.service: storage
ms.topic: article
ms.date: 02/24/2021
ms.author: chuye
ms.openlocfilehash: 283ee2dd27d22e16177efe34d4f456eca40d997d
ms.sourcegitcommit: 43dbb8a39d0febdd4aea3e8bfb41fa4700df3409
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/03/2021
ms.locfileid: "123451496"
---
# <a name="azure-storage-explorer-direct-link"></a>Azure Storage Explorer 직접 링크

Windows 및 macOS에서 Storage Explorer는 `storageexplorer://` 프로토콜을 통해 URI를 지원합니다. 이 URI를 직접 링크라고 합니다. 직접 링크는 Storage Explorer에서 Azure Storage 리소스를 가리킵니다. 직접 링크를 따라가면 Storage Explorer가 열리고 가리키는 리소스로 이동합니다. 이 문서에서는 직접 링크의 작동 방식 및 사용 방법을 설명합니다.

## <a name="how-direct-links-work"></a>직접 링크의 작동 방식

Storage Explorer는 트리 뷰를 사용하여 Azure에서 리소스를 시각화합니다. 직접 링크는 트리에 있는 연결된 리소스 노드의 계층 구조 정보를 포함합니다. 직접 링크를 따라가면 Storage Explorer가 열리고 직접 링크의 매개 변수를 수신합니다. 그런 다음, Storage Explorer는 이 매개 변수를 사용하여 트리 뷰의 연결된 리소스로 이동합니다.

> [!IMPORTANT]
> 직접 링크가 작동하려면 로그인하고 연결된 리소스에 액세스하는 데 필요한 권한이 있어야 합니다.

## <a name="parameters"></a>매개 변수

Storage Explorer 직접 링크는 항상 `storageexplorer://` 프로토콜을 통해 시작됩니다. 다음 표에서는 직접 링크의 가능한 각 매개 변수를 설명합니다.

매개 변수 | Description
:---------| :---------
`v`         | 직접 링크 프로토콜의 버전입니다.
`accountid` | 연결된 리소스를 위한 스토리지 계정의 Azure Resource Manager 리소스 ID입니다. 연결된 리소스가 스토리지 계정인 경우 이 ID는 해당 스토리지 계정의 Azure Resource Manager 리소스 ID가 됩니다. 이외의 경우 이 ID는 연결된 리소스가 속한 스토리지 계정의 Azure Resource Manager 리소스 ID가 됩니다.
`resourcetype` | 선택 사항입니다. 연결된 리소스가 Blob 컨테이너, 파일 공유, 큐 또는 테이블인 경우에만 사용됩니다. “Azure.BlobContainer”, “Azure.FileShare”, “Azure.Queue”, “Azure.FileShare” 중 하나여야 합니다.
`resourcename` | 선택 사항입니다. 연결된 리소스가 Blob 컨테이너, 파일 공유, 큐 또는 테이블인 경우에만 사용됩니다. 연결된 리소스의 이름입니다.

Blob 컨테이너의 예제 직접 링크는 다음과 같습니다. 
`storageexplorer://v=1&accountid=/subscriptions/the_subscription_id/resourceGroups/the_resource_group_name/providers/Microsoft.Storage/storageAccounts/the_storage_account_name&subscriptionid=the_subscription_id&resourcetype=Azure.BlobContainer&resourcename=the_blob_container_name`

## <a name="get-a-direct-link-from-storage-explorer"></a>Storage Explorer에서 직접 링크 가져오기

Storage Explorer를 사용하여 리소스의 직접 링크를 가져올 수 있습니다. 트리 뷰에서 리소스 노드의 상황에 맞는 메뉴를 엽니다. 그런 다음, “직접 링크 복사” 작업을 사용하여 해당 직접 링크를 클립보드에 복사합니다.

## <a name="direct-link-limitations"></a>직접 링크 제한 사항

직접 링크는 구독 노드에 있는 리소스에만 지원됩니다. 또한 다음 리소스 종류만 지원됩니다.

- 스토리지 계정
- Blob 컨테이너
- 큐
- 파일 공유
- 테이블
