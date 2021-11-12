---
title: 권한이 상승된 액세스 중에 금지된 작업
description: VMware Engine은 다음 금지된 작업을 검색할 때 변경 내용을 되돌려서 서비스가 중단되지 않았는지 확인합니다.
titleSuffix: Azure VMware Solution by CloudSimple
ms.date: 10/28/2020
ms.topic: article
ms.service: azure-vmware-cloudsimple
author: divka78
ms.author: dikamath
ms.openlocfilehash: 282484e4935e0a5fcc9e79351ffe2dd75b0b5f44
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132344811"
---
# <a name="forbidden-actions-during-elevated-access"></a>권한이 상승된 액세스 중에 금지된 작업

권한 상승 시간 간격 동안 일부 작업이 금지됩니다. VMware Engine은 다음 금지된 작업을 검색할 때 변경 내용을 되돌려서 서비스가 중단되지 않았는지 확인합니다.

## <a name="cluster-actions"></a>클러스터 작업

- vCenter에서 클러스터 제거
- 클러스터에서 vSphere HA(고가용성) 변경
- vCenter에서 클러스터에 호스트 추가
- vCenter의 클러스터에서 호스트 제거

## <a name="host-actions"></a>호스트 작업

- ESXi 호스트에서 데이터 저장소 제거
- 호스트에서 vCenter 에이전트 제거
- 호스트 구성 수정
- 호스트 프로필 변경
- 호스트를 유지 관리 모드로 전환

## <a name="network-actions"></a>네트워크 작업

- 프라이빗 클라우드에서 기본 DVS(분산 가상 스위치) 삭제
- 기본 DVS에서 호스트 제거
- DVS 설정 가져오기
- DVS 설정 다시 구성
- DVS 업그레이드
- 관리 포트 그룹 삭제
- 관리 포트 그룹 편집

## <a name="roles-and-permissions-actions"></a>역할 및 권한 작업

- 전역 역할 만들기
- 모든 관리 개체에 대한 권한 수정 또는 삭제
- 기본 역할 수정 또는 제거
- 역할의 권한을 클라우드 소유자 역할보다 높이기

## <a name="other-actions"></a>다른 작업

- 기본 라이선스 제거:
  - vCenter Server
  - ESXi 노드
  - NSX-T
  - HCX
- 관리 리소스 풀 수정 또는 삭제
- 관리 VM 복제


## <a name="next-steps"></a>다음 단계
[CloudSimple 유지 관리 및 업데이트](cloudsimple-maintenance-updates.md) 
