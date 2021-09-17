---
title: 포함 파일
description: 포함 파일
ms.topic: include
ms.custom: include file
ms.date: 03/11/2020
ms.openlocfilehash: 615346c16fbbb31bcefc53fc68d04c1255ece88c
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/03/2021
ms.locfileid: "123453858"
---
협업을 위해 제공되는 역할은 다음과 같습니다.

|역할|기능|API 액세스|API 사용 권한|
|--|--|--|--|
|소유자|모두|인증 키|모두|
|기여자|역할에 새 멤버를 추가하는 기능을 제외한 모든 기능|인증 키|역할에 새 멤버를 추가하는 기능을 제외한 모든 기능|
|QnA Maker 읽기<br>(읽기)|내보내기/다운로드<br>테스트|전달자 토큰|1. KB API 다운로드<br>2. 사용자 API에 대한 KB 나열<br>3. 기술 자료 세부 정보 가져오기<br>4. 변경 사항 다운로드<br>답변 생성 |
|QnA Maker 편집기<br>(읽기/쓰기)|내보내기/다운로드<br>테스트<br>KB 업데이트<br>KB 내보내기<br>KB 가져오기<br>KB 바꾸기<br>기술 자료 만들기|전달자 토큰|1. KB API 만들기<br>2. KB API 업데이트<br>3. KB API 바꾸기<br>4. 변경 내용 바꾸기<br>5. "학습 API"[새 서비스 모델 v5]|
|Cognitive Service 사용자<br>(읽기/쓰기/게시)|모두|인증 키|다음을 수행할 수 있는 기능을 제외한 Cognitive Services 리소스에 대한 모든 액세스 권한: <br>1. 역할에 새 멤버를 추가합니다.<br>2. 새 리소스를 만듭니다.|
