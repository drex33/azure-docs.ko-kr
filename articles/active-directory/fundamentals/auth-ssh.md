---
title: Azure Active Directory를 사용한 SSH 인증
description: Azure Active Directory를 사용한 SSH 통합 구현에 대한 아키텍처 지침
services: active-directory
author: BarbaraSelden
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 10/19/2020
ms.author: baselden
ms.reviewer: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4b1860fd614ab0074896f51a16a268d21f82c11a
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131049314"
---
# <a name="ssh"></a>SSH  

SSH(Secure Shell)는 보안되지 않은 네트워크를 통해 안전하게 운영 네트워크 서비스에 대한 암호화를 제공하는 네트워크 프로토콜입니다. 또한 SSH는 명령줄 로그인을 제공하고, 원격 명령을 실행하고, 파일을 안전하게 전송합니다. Linux®와 같은 UNIX 기반 시스템에서 일반적으로 사용됩니다. SSH는 보안되지 않은 네트워크에서 암호화를 제공하지 않는 Telnet 프로토콜을 대체합니다. 

Azure AD(Azure Active Directory)는 Azure에서 실행되는 Linux® 기반 시스템용 VM(가상 머신) 확장을 제공합니다. 

## <a name="use-when"></a>적용 가능한 상황 

* 원격 로그인이 필요한 Linux® 기반 VM 작업

* Linux® 기반 시스템에서 원격 명령 실행

* 안전하지 않은 네트워크에서 파일을 안전하게 전송

![SSH 프로토콜을 사용하는 Azure AD 다이어그램](./media/authentication-patterns/ssh-auth.png)

Azure AD를 사용하여 SSH

## <a name="components-of-system"></a>시스템의 구성 요소 

* **사용자**: Linux® VM과의 연결을 설정하고 인증을 위한 자격 증명을 제공하기 위해 SSH 클라이언트를 시작합니다.

* **웹 브라우저**: 사용자가 상호 작용하는 구성 요소입니다. 사용자를 안전하게 인증하고 권한을 부여하기 위해 ID 공급자(Azure AD)와 통신합니다.

* **SSH 클라이언트**: 연결 설정 프로세스를 구동합니다.

* **Azure AD**: 디바이스 흐름을 사용하여 사용자의 ID를 인증하고 Linux VM에 토큰을 발급합니다.

* **Linux VM**: 토큰을 수락하고 성공적인 연결을 제공합니다.

## <a name="implement-ssh-with-azure-ad"></a>Azure AD를 사용하여 SSH 구현 

* [Azure Active Directory 자격 증명을 사용하여 Linux® VM에 로그인 - Azure Virtual Machines](../../virtual-machines/linux/login-using-aad.md) 

* [OAuth 2.0 디바이스 코드 흐름 - Microsoft ID 플랫폼](../develop/v2-oauth2-device-code.md)

* [Azure Active Directory와 통합 (akamai.com)](https://learn.akamai.com/en-us/webhelp/enterprise-application-access/enterprise-application-access/GUID-6B16172C-86CC-48E8-B30D-8E678BF3325F.html)
