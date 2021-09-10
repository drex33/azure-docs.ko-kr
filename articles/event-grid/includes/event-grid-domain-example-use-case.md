---
title: 포함 파일
description: 포함 파일
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: include
ms.date: 03/04/2021
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: 2459cf6c5055dcde83dccf37addc160aeeaa64ad
ms.sourcegitcommit: 5163ebd8257281e7e724c072f169d4165441c326
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/21/2021
ms.locfileid: "112413560"
---
이벤트 도메인을 가장 쉽게 설명하는 방법은 예제입니다. 트랙터, 굴착 장비 및 기타 중장비를 제작하는 Contoso Construction Machinery라는 회사를 운영 중이라고 가정해 보겠습니다. 비즈니스의 일부로, 여러분은 장비 유지 관리, 시스템 상태 및 계약 업데이트에 대한 실시간 정보를 고객에게 푸시합니다. 이 모든 정보는 회사 앱, 고객 엔드포인트, 고객이 설치한 기타 인프라를 비롯한 다양한 엔드포인트로 이동합니다.

이벤트 도메인을 사용하면 Contoso Construction Machinery를 단일 이벤트 엔터티로 모델링할 수 있습니다. 각 고객은 도메인 내의 토픽으로 표시됩니다. 인증 및 권한 부여는 Azure Active Directory를 사용하여 처리됩니다. 각 고객은 토픽을 구독하고 자신에게 전달된 이벤트를 가져올 수 있습니다. 이벤트 도메인을 통해 관리되는 액세스는 고객이 자신의 토픽에만 액세스할 수 있도록 보장합니다.

또한 모든 고객 이벤트를 게시할 수 있는 단일 엔드포인트를 제공합니다. Event Grid는 각 토픽이 해당 테넌트로 범위가 지정된 이벤트만 인식하도록 처리합니다.

:::image type="content" source="./media/event-grid-domain-example-use-case/contoso-construction-example.png" alt-text="Contoso Construction 예제" lightbox="./media/event-grid-domain-example-use-case/contoso-construction-example.png":::