---
title: '문제 해결: SDK에서 만든 작업 영역에서 Synapse Studio를 시작할 수 없음'
description: SDK에서 만든 작업 영역에서 Synapse Studio를 시작할 수 없는 문제를 해결하는 단계
author: WilliamDAssafMSFT
ms.author: wiassaf
ms.topic: troubleshooting
ms.service: synapse-analytics
ms.subservice: troubleshooting
ms.date: 11/24/2020
ms.openlocfilehash: 86ebcaa35af5de612a98901771b523e9c5883d81
ms.sourcegitcommit: 66b6e640e2a294a7fbbdb3309b4829df526d863d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/01/2021
ms.locfileid: "133367990"
---
# <a name="troubleshoot-azure-synapse-analytics-workspaces-created-using-sdk"></a>SDK를 사용하여 만든 Azure Synapse Analytics 작업 영역 문제 해결

이 문서는 SDK(소프트웨어 개발 키트)를 사용하여 만든 Synapse 작업 영역에서 Synapse Studio를 시작하기 위한 문제 해결 단계를 제공합니다.


## <a name="prerequisites"></a>사전 요구 사항

- SDK를 사용하여 만든 Synapse 작업 영역

## <a name="workaround"></a>해결 방법

SDK에서 만든 작업 영역에서 Synapse Studio를 시작하려면 다음 단계를 완료합니다. 
  1.    `az synapse workspace create`를 실행하여 작업 영역을 만듭니다.
  2.    `$identity=$(az synapse workspace show --name {workspace name}  --resource-group {resource group name} --query "identity.principalId")`를 실행하여 관리 ID를 추출합니다.
  3.    ` az role assignment create --role "Storage Blob Data Contributor" --assignee-object-id {identity } --scope {storage account resource id}.` 실행을 통해 관리 ID 스토리지 계정에 스토리지 Blob 데이터 참가자 역할을 부여합니다.
  4.    ` az synapse firewall-rule create --name allowAll --start-ip-address 0.0.0.0 --end-ip-address 255.255.255.255 `를 실행하여 방화벽 규칙을 추가합니다.

## <a name="next-steps"></a>다음 단계

* [Azure Synapse란?](../overview-what-is.md)
* [시작](../get-started.md)
