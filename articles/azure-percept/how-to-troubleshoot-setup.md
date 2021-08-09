---
title: Azure Percept DK 설치 환경에서 발생하는 문제 해결
description: 설치 환경에서 발견된 몇 가지 일반적인 문제에 대한 문제 해결 팁 확인
author: mimcco
ms.author: mimcco
ms.service: azure-percept
ms.topic: troubleshooting
ms.date: 03/25/2021
ms.custom: template-how-to
ms.openlocfilehash: 1be326c9486b4017ef9b4547f9b648a15865e2a6
ms.sourcegitcommit: 070122ad3aba7c602bf004fbcf1c70419b48f29e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/04/2021
ms.locfileid: "111440487"
---
# <a name="azure-percept-dk-setup-experience-troubleshooting-guide"></a>Azure Percept DK 설치 환경 문제 해결 가이드

[Azure PERCEPT DK 설치 환경](./quickstart-percept-dk-set-up.md)에서 발견된 일반적인 문제에 대한 해결 방법은 아래 표를 참조하세요. 문제가 계속되면 Azure 고객 지원 서비스에 문의하세요.

|문제|이유|해결 방법|
|:-----|:------|:----------|
|Azure 계정 등록 페이지 또는 Azure Portal에 연결하는 경우 캐시된 계정으로 자동으로 로그인할 수 있습니다. 올바른 계정으로 로그인하지 않으면 설명서와 일치하지 않는 환경이 발생할 수 있습니다.|이는 이전에 사용한 계정을 "기억"하도록 브라우저를 설정하는 경우 발생합니다.|Azure 페이지의 오른쪽 상단에서 계정 이름을 선택하고 **로그아웃** 을 선택합니다. 그러면 올바른 계정으로 로그인할 수 있습니다.|
|Azure Percept DK Wi-Fi 액세스 지점(apd-xxxx)은 사용 가능한 Wi-Fi 네트워크 목록에 표시되지 않습니다.|이 문제는 일반적으로 15분 이내에 해결되는 일시적인 문제입니다.|네트워크가 나타날 때까지 기다리세요. 15분 이상 표시되지 않으면 디바이스를 다시 부팅합니다.|
|Azure Percept DK Wi-Fi 액세스 지점에 대한 연결이 자주 끊어집니다.|이는 디바이스와 호스트 컴퓨터가 제대로 연결되어 있지 않기 때문일 수 있습니다. 호스트 컴퓨터에서 다른 Wi-Fi 연결의 간섭으로 인해 발생할 수도 있습니다.|안테나가 개발자 키트에 제대로 연결되어 있는지 확인합니다. 개발자 키트가 호스트 컴퓨터에서 멀리 떨어져 있는 경우 좀 더 가깝게 이동해 보세요. 호스트 컴퓨터에서 실행 중인 경우에는 LTE/5G 등의 다른 인터넷 연결을 해제합니다.|
|호스트 컴퓨터에는 Azure Percept DK 액세스 지점에 대한 연결과 관련된 보안 경고가 표시됩니다.|이 문제는 알려진 문제이며 이후 업데이트에서 수정될 예정입니다.|설치 환경을 계속 진행하는 것이 안전합니다.|
|Azure Percept DK Wi-Fi 액세스 지점(scz-xxxx 또는 apd-xxxx)이 네트워크 목록에 표시되지만 연결은 실패합니다.|개발자 키트의 Wi-Fi 액세스 지점이 일시적으로 손상되었기 때문일 수 있습니다.|개발자 키트를 다시 부팅한 후 다시 시도하세요.|
|설치 환경에서 Wi-Fi 네트워크에 연결할 수 없습니다.|Wi-Fi 네트워크는 현재 Azure와 통신하기 위해 인터넷에 연결되어 있어야 합니다. EAP[PEAP/MSCHAP], 종속 포털 및 엔터프라이즈 EAP-TLS 연결은 현재 지원되지 않습니다.|Wi-Fi 네트워크 유형이 지원되고 인터넷에 연결되어 있는지 확인하세요.|
|디바이스 코드를 사용하여 Azure에 로그인하면 후 정책 권한 또는 규정 준수 문제에 대한 오류가 표시되고 계속 진행할 수 없습니다. 표시될 수 있는 오류는 다음과 같습니다.<br>**BlockedByConditionalAccessOnSecurityPolicy** 테넌트 관리자가 이 요청을 차단하는 보안 정책을 구성했습니다. 테넌트 수준에 정의된 보안 정책을 확인하여 요청이 정책을 충족하는지 확인합니다. <br>**DevicePolicyError** 사용자가 현재 조건부 액세스 정책을 통해 지원되지 않는 플랫폼에서 디바이스에 로그인하려고 했습니다.<br>**DeviceNotCompliant** - 조건부 액세스 정책에는 이를 준수하는 디바이스가 필요하며, 해당 디바이스는 정책을 준수하지 않았습니다. 사용자는 Intune과 같이 승인된 MDM 공급자에 자신의 디바이스를 등록해야 합니다.<br>**BlockedByConditionalAccess** 조건부 액세스 정책에 의해 액세스가 차단되었습니다. 액세스 정책에 따라 토큰 발급을 허용하지 않습니다.    |일부 Azure 테넌트는 보안 예방 조치로 Azure 리소스를 조작하기 위한 "디바이스 코드"의 사용을 차단할 수 있습니다. 이는 일반적으로 조직의 IT 정책에 따른 결과입니다. 따라서 Azure Percept 설치 환경에서는 Azure 리소스를 만들 수 없습니다.    |해결 방법    |