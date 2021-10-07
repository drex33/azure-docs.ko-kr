---
title: 계정 관리 - Azure VMware Solution by CloudSimple
description: Azure VMware Solution by CloudSimple 포털에서 계정을 관리하는 방법을 설명합니다.
author: suzizuber
ms.author: v-szuber
ms.date: 08/14/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: e93b445d54fd181aa173f8179911c69195659f4c
ms.sourcegitcommit: 1d56a3ff255f1f72c6315a0588422842dbcbe502
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/06/2021
ms.locfileid: "129618312"
---
# <a name="manage-accounts-on-the-azure-vmware-solution-by-cloudsimple-portal"></a>Azure VMware Solution by CloudSimple 포털에서 계정 관리

CloudSimple 서비스를 만들면 CloudSimple에 계정이 만들어집니다. 계정은 서비스가 있는 Azure 구독과 연결됩니다. 구독에서 소유자 및 기여자 역할을 가진 모든 사용자는 CloudSimple 포털에 액세스할 수 있습니다. CloudSimple 서비스와 연결된 Azure 구독 ID 및 테넌트 ID는 계정 페이지에서 찾을 수 있습니다.

CloudSimple 포털에서 계정을 관리하려면 [포털에 액세스](access-cloudsimple-portal.md)하고 사이드 메뉴에서 **계정** 을 선택합니다.

회사 CloudSimple 구성에 대한 정보를 보려면 **요약** 을 선택합니다. 프라이빗 클라우드 수, 총 스토리지, vSphere 클러스터 구성, 노드 수, 컴퓨팅 코어 수를 포함하여 클라우드 구성의 현재 용량이 표시됩니다. 현재 구성이 모든 요구 사항을 충족하지 못하는 경우 추가 노드를 구매할 수 있는 링크가 포함되어 있습니다.

## <a name="email-alerts"></a>메일 경고

프라이빗 클라우드 구성의 변경 사항에 대해 알리고 싶은 사람의 이메일 주소를 추가할 수 있습니다.

1. **추가 이메일 경고** 영역에서 **새로 추가** 를 클릭합니다.
2. 이메일 주소를 입력합니다.
3. RETURN 키를 누릅니다.  

항목을 제거하려면 **X** 를 클릭합니다.

## <a name="cloudsimple-operator-access"></a>CloudSimple 운영자 액세스

운영자 액세스 설정을 사용하면 지원 엔지니어가 CloudSimple 포털에 로그인할 수 있도록 허용하여 CloudSimple이 문제 해결을 도울 수 있습니다.  이 설정은 기본적으로 사용하도록 설정됩니다. 고객 계정에 로그인할 때 지원 엔지니어가 수행한 모든 작업은 기록되고 **작업** > **감사** 페이지에서 검토할 수 있습니다.

**CloudSimple 운영자 액세스 사용** 토글을 클릭하여 액세스를 켜거나 끕니다.
