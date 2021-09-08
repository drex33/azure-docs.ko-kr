---
title: vCenter 액세스 및 ID 설명
description: vCenter는 cloudadmin이라는 기본 제공 로컬 사용자를 포함하고 CloudAdmin 역할에 할당됩니다.
ms.topic: include
ms.date: 08/31/2021
ms.openlocfilehash: 1fdc81c11b833b1239d37f613732eec42b463e1a
ms.sourcegitcommit: 2eac9bd319fb8b3a1080518c73ee337123286fa2
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/31/2021
ms.locfileid: "123252529"
---
<!-- used in concepts-run-commands.md and tutorial-configure-identity-source.md -->

Azure VMware Solution에서 vCenter는 cloudadmin이라는 기본 제공 로컬 사용자를 포함하고 *cloudadmin* 역할에 할당됩니다. 프라이빗 클라우드에 대한 CloudAdmin 역할을 사용하여 AD(Active Directory)에서 사용자 및 그룹을 구성할 수 있습니다. 일반적으로 CloudAdmin 역할은 프라이빗 클라우드에서 워크로드를 만들고 관리합니다. 그러나 Azure VMware Solution에서 CloudAdmin 역할에는 다른 VMware 클라우드 솔루션 및 온-프레미스 배포와 다른 vCenter 권한이 있습니다.

>[!IMPORTANT]
>로컬 cloudadmin 사용자는 프라이빗 클라우드의 "비상" 시나리오에 대한 비상 액세스 계정으로 취급되어야 합니다. 일상적인 관리 활동이나 다른 서비스와의 통합을 위한 것이 아닙니다. 

- vCenter 및 ESXi 온-프레미스 배포에서 관리자는 vCenter 관리자 \@ vsphere.local 계정에 액세스할 수 있습니다. 또한 더 많은 AD 사용자 및 그룹이 할당됩니다. 

- Azure VMware Solution 배포에서 관리자는 관리자 사용자 계정에 액세스할 수 없습니다. 그러나 AD 사용자 및 그룹을 vCenter의 CloudAdmin 역할에 할당할 수 있습니다.  CloudAdmin 역할에는 온-프레미스 LDAP 또는 LDAPS 서버와 같은 ID 원본을 vCenter에 추가할 수 있는 권한이 없습니다. 그러나 실행 명령을 사용하여 ID 원본을 추가하고 사용자 및 그룹에 cloudadmin 역할을 할당할 수 있습니다.
 
프라이빗 클라우드 사용자는 Microsoft에서 지원하고 관리하는 특정 관리 구성 요소에 액세스할 수 없으며 구성 요소를 구성할 수도 없습니다. 예를 들어 클러스터, 호스트, 데이터 저장소 및 분산 가상 스위치가 있습니다.

>[!NOTE]
>Azure VMware Solution에서 *vsphere.local* SSO 도메인은 플랫폼 작업을 지원하기 위한 관리되는 리소스로 제공됩니다. 프라이빗 클라우드와 함께 기본적으로 제공되는 것 이외의 로컬 그룹 및 사용자의 만들기 및 관리는 지원하지 않습니다.
