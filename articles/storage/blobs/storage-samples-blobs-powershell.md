---
title: Azure Blob Storage에 대한 Azure PowerShell 샘플 | Microsoft Docs
description: 스토리지 계정 만들기, 계정간 Blob 마이그레이션 등과 같은 Azure Blob 스토리지를 사용하기 위한 Azure PowerShell 스크립트 샘플에 대한 링크를 참조하세요.
author: normesta
ms.author: normesta
ms.date: 11/07/2017
ms.service: storage
ms.subservice: blobs
ms.topic: sample
ms.openlocfilehash: 38412bedf8fca61cb55a994618e8029127dc5a3a
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128593119"
---
# <a name="azure-powershell-samples-for-azure-blob-storage"></a>Azure Blob Storage에 대한 Azure PowerShell 샘플

다음 표에는 Azure Storage를 만들고 관리하는 PowerShell 스크립트 샘플에 대한 링크가 제공됩니다.

| 스크립트 | Description |
|---|---|
|**스토리지 계정**||
| [스토리지 계정 만들기 및 액세스 키 검색/회전](../scripts/storage-common-rotate-account-keys-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Azure Storage 계정을 만들고 해당 액세스 키 중 하나를 검색 및 회전합니다. |
| [Windows에서 AzCopy를 사용해 여러 스토리지 계정으로 Blob 마이그레이션](/previous-versions/azure/storage/storage-common-transfer-between-storage-accounts?toc=%2fpowershell%2fmodule%2ftoc.json)| Windows에서 AzCopy를 사용해 여러 Azure Storage 계정으로 Blob을 마이그레이션하는 방법을 설명합니다. |
|**Blob Storage**||
| [Blob Storage 컨테이너의 전체 크기 계산](../scripts/storage-blobs-container-calculate-size-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json) | 컨테이너에 있는 모든 Blob의 전체 크기를 계산합니다. |
| [청구 목적으로 Blob Storage 컨테이너 크기 계산](../scripts/storage-blobs-container-calculate-billing-size-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json) | 청구 비용을 예측하기 위해 Blob Storage에 있는 컨테이너의 크기를 계산합니다. |
| [특정 접두사가 있는 컨테이너 삭제](../scripts/storage-blobs-container-delete-by-prefix-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json) | 지정된 문자열로 시작되는 컨테이너를 삭제합니다. |