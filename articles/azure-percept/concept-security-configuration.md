---
title: Azure Percept 보안 권장 사항
description: Azure Percept 방화벽 구성 및 보안 권장 사항에 대해 자세히 알아보기
author: mimcco
ms.author: amiyouss
ms.service: azure-percept
ms.topic: conceptual
ms.date: 03/25/2021
ms.openlocfilehash: 146b39db7aaae2ee043d14d61a7be4f00363c548
ms.sourcegitcommit: 40866facf800a09574f97cc486b5f64fced67eb2
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/30/2021
ms.locfileid: "123222331"
---
# <a name="azure-percept-security-recommendations"></a>Azure Percept 보안 권장 사항

Azure Percept를 사용하여 방화벽 및 일반적인 보안 모범 사례를 구성하는 방법에 대한 자세한 내용은 아래 지침을 검토하세요.

## <a name="configuring-firewalls-for-azure-percept-dk"></a>Azure Percept에 대한 방화벽 구성

네트워킹 설정에 따라 Azure Percept DK의 연결을 명시적으로 허용하는 경우 다음 구성 요소 목록을 검토합니다.

다음 검사 목록은 방화벽 규칙의 시작점입니다.

|URL(* = 와일드카드)|아웃바운드 TCP 포트|사용량|
|-------------------|------------------|---------|
|*.auth.azureperceptdk.azure.net|443|Azure DK SOM 인증 및 권한 부여|
|*.auth.projectsantacruz.azure.net|443|Azure DK SOM 인증 및 권한 부여|

또한 [Azure IoT Edge에서 사용하는 연결](../iot-edge/production-checklist.md#allow-connections-from-iot-edge-devices)의 목록을 검토합니다.

## <a name="additional-recommendations-for-deployment-to-production"></a>프로덕션 환경에 대한 추가 권장 사항

Azure Percept는 기본 제공되는 다양한 보안 기능을 제공합니다. Microsoft는 현재 릴리스에 포함된 강력한 보안 기능 외에도 프로덕션 배포를 고려할 때 다음과 같은 지침을 제안합니다.

- 디바이스 자체의 강력한 물리적 보호
- 미사용 데이터 암호화 사용 확인
- 지속적으로 디바이스 상태를 모니터링하고 경고에 신속하게 응답
- 디바이스에 액세스할 수 있는 관리자 수 제한

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [Azure Percept 보안에 대해 자세히 알아보기](./overview-percept-security.md)