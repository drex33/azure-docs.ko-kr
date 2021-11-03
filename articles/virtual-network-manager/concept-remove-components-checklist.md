---
title: Azure Virtual Network Manager 미리 보기 구성 요소 검사 목록 제거
description: 이 문서는 Azure Virtual Network Manager 내에서 구성 요소를 삭제하기 위한 검사 목록입니다.
author: duongau
ms.author: duau
ms.service: virtual-network-manager
ms.topic: conceptual
ms.date: 11/02/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: 65987c576ff16d766cb8da88ec824f0bb5bdd40a
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131052876"
---
# <a name="remove-and-update-azure-virtual-network-manager-preview-components-checklist"></a>Azure Virtual Network Manager 미리 보기 구성 요소 검사 목록 제거 및 업데이트

이 문서에서는 Azure Virtual Network Manager 미리 보기의 구성 구성 요소를 제거하거나 업데이트하기 위해 완료해야 하는 단계의 검사 목록이 표시됩니다.

## <a name="remove-components-checklist"></a><a name="remove"></a>구성 요소 제거 검사 목록

| 작업 | 단계 | 
| ------ | ----- |
| 연결 구성 배포 배포를 배포하지 않습니다. | 대상 지역에 **없음** 연결 구성을 배포합니다. |
| 보안 관리자 구성 배포 배포를 배포하지 않습니다. | 대상 지역에 **없음** 연결 구성을 배포합니다. |
| 보안 관리자 규칙 제거 | 1. 보안 관리자 구성 배포를 배포하지 않습니다. </br> 2. 보안 구성과 연결된 보안 관리자 규칙을 삭제합니다. |
| 보안 규칙 컬렉션 제거 | 1. 보안 관리자 구성 배포를 배포하지 않습니다. </br> 2. 보안 구성과 연결된 보안 관리자 규칙을 삭제합니다. </br> 3. 규칙 컬렉션을 삭제합니다. |
| 보안 관리자 구성 제거 | 1. 보안 관리자 구성 배포를 배포하지 않습니다. </br> 2. 보안 구성과 연결된 보안 관리자 규칙을 삭제합니다. </br> 3. 규칙 컬렉션을 삭제합니다. </br> 4. 보안 관리자 구성을 삭제합니다. |
| 연결 구성 제거 | 1. 연결 구성 배포를 배포하지 않습니다. </br> 2. 연결 구성을 삭제합니다. |
| 네트워크 그룹 제거 | 1. 이 네트워크 그룹과 연결된 연결 구성 삭제 </br> 2. 네트워크 그룹과 연결된 모든 보안 규칙을 삭제합니다. </br> 3. 네트워크 그룹을 삭제합니다. |
| Azure Virtual Network Manager 인스턴스 삭제 | 1. 모든 연결 및 보안 관리자 구성을 삭제합니다. </br> 2. 모든 네트워크 그룹을 삭제합니다. </br> 3. Azure Virtual Network Manager 인스턴스를 삭제합니다. |

## <a name="update-components-checklist"></a>구성 요소 업데이트 검사 목록

이 표에 제공된 정보는 대상 지역에 연결 또는 보안 관리자 구성이 배포되어 있다고 가정합니다.

| 작업 | 단계 |
| ------ | ----- |
| 정적 멤버 자격을 사용하여 가상 네트워크 추가 또는 제거 | 1. 네트워크 그룹에 가상 네트워크를 추가합니다. </br> 2. 네트워크 그룹을 포함하는 연결 또는 보안 구성을 커밋합니다. |
| 동적 멤버 자격을 사용하여 가상 네트워크 추가 또는 제거 | Azure Virtual Network Manager는 조건문과 일치하도록 자동으로 변경합니다. |
| 보안 규칙 추가, 제거 또는 업데이트 | * 보안 구성이 정적 멤버를 포함하는 네트워크 그룹에 적용되는 경우 적용하려면 구성을 다시 배포해야 합니다. </br> * 동적 멤버를 포함하는 네트워크 그룹은 자동으로 업데이트됩니다. |
| 규칙 컬렉션 추가, 제거 또는 업데이트 | * 보안 구성이 정적 멤버를 포함하는 네트워크 그룹에 적용되는 경우 적용하려면 구성을 다시 배포해야 합니다. </br> * 동적 멤버를 포함하는 네트워크 그룹은 자동으로 업데이트됩니다. |

## <a name="next-steps"></a>다음 단계

Azure Portal을 사용하여 [Azure Virtual Network Manager](create-virtual-network-manager-portal.md) 인스턴스를 만듭니다.
