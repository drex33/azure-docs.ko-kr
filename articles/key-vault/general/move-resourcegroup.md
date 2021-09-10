---
title: 자격 증명 모음을 다른 리소스 그룹으로 옮기는 Azure Key Vault | Microsoft Docs
description: 키 자격 증명 모음을 다른 리소스 그룹으로 옮기는 방법에 대한 지침입니다.
services: key-vault
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.date: 03/31/2021
ms.author: mbaldwin
ms.openlocfilehash: 508a6aebe581df1c4fcaa634bdcb1d870f319f84
ms.sourcegitcommit: 0ede6bcb140fe805daa75d4b5bdd2c0ee040ef4d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/20/2021
ms.locfileid: "122606209"
---
# <a name="moving-an-azure-key-vault-across-resource-groups"></a>리소스 그룹 간에 Azure Key Vault 이동

## <a name="overview"></a>개요

키 자격 증명 모음을 다른 리소스 그룹으로 옮기는 것은 지원되는 키 자격 증명 기능입니다. 리소스 그룹 간에 키 자격 증명 모음을 옮겨도 키 자격 증명 모음 방화벽이나 액세스 정책 구성은 영향받지 않습니다. 연결된 애플리케이션과 서비스 주체는 원래대로 계속 작동합니다.

> [!IMPORTANT]
> **디스크 암호화에 사용한 키 자격 증명 모음은 옮길 수 없습니다.**
> VM용 디스크 암호화와 함께 키 자격 증명 모음을 사용한다면, 디스크 암호화를 사용 설정한 상태에서는 키 자격 증명 모음을 다른 리소스 그룹이나 구독으로 옮길 수 없습니다. 키 자격 증명 모음을 새 리소스 그룹이나 구독으로 옮기기 전에 먼저 디스크 암호화를 사용 중지해야 합니다. 

## <a name="design-considerations"></a>디자인 고려 사항

조직은 리소스 그룹 수준에서 적용 또는 제외를 사용하여 Azure Policy를 구현할 수도 있습니다. 키 자격 증명 모음이 현재 존재하는 리소스 그룹과 키 자격 증명을 옮기려는 리소스 그룹의 정책 할당 세트가 서로 다를 수 있습니다. 정책 요구 사항의 충돌로 인해 애플리케이션이 중단될 수 있습니다.

### <a name="example"></a>예제

2년 동안 유효한 인증서를 생성하는 키 자격 증명 모음에 연결된 애플리케이션이 있습니다. 키 자격 증명 모음을 이동하려는 리소스 그룹에는 1년 이상 유효한 인증서 생성을 차단하는 정책 할당이 있습니다. 키 자격 증명 모음을 새 리소스 그룹으로 이동하면, 2년 동안 유효한 인증서를 만드는 작업은 Azure Policy 할당에 의해 차단됩니다.

### <a name="solution"></a>해결 방법

Azure Portal에서 Azure Policy 페이지로 이동하고 현재 리소스 그룹에 대한 정책 할당과 이동 중인 리소스 그룹을 확인하고 불일치 항목이 없는지 확인합니다.

## <a name="procedure"></a>프로시저

1. Azure Portal에 로그인
2. 키 자격 증명 모음으로 이동
3. “개요” 탭을 클릭
4. “이동” 단추를 선택
5. 드롭다운 옵션에서 ‘다른 리소스 그룹으로 이동’ 선택
6. 키 자격 증명 모음을 이동할 리소스 그룹을 선택
7. 리소스 이동과 관련된 경고 승인
8. "확인" 선택

이제 키 자격 증명 모음에서 리소스 이동의 유효성을 평가하고 오류를 경고합니다. 오류가 발견되지 않으면 리소스 이동이 완료됩니다. 
