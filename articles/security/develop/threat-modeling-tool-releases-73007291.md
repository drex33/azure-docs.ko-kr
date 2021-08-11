---
title: Microsoft Threat Modeling Tool 릴리스 07/29/2020 - Azure
description: Threat Modeling Tool 릴리스 7.3.00729.1에 대한 릴리스 정보를 문서화합니다.
author: jegeib
ms.author: jegeib
ms.service: security
ms.topic: article
ms.date: 07/29/2020
ms.openlocfilehash: 3e6fcd52ad9cb6c127c14bac2f33223fb921519e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "94516376"
---
# <a name="threat-modeling-tool-update-release-73007291---07292020"></a>Threat Modeling Tool 업데이트 릴리스 7.3.00729.1 - 07/29/2020

Microsoft TMT(Threat Modeling Tool) 버전 7.3.00729.1은 2020년 7월 29일에 릴리스되었으며 이 버전에 포함된 변경 내용은 다음과 같습니다.

- 버그 수정
 
## <a name="known-issues"></a>알려진 문제

### <a name="errors-related-to-tmt7application-file-deserialization"></a>TMT7.application 파일 역직렬화와 관련된 오류

#### <a name="issue"></a>문제

일부 고객이 Threat Modeling Tool을 다운로드할 때 다음 오류 메시지 수신을 보고했습니다.

```
The threat model file '$PATH\TMT7.application' could not be deserialized. File is not an actual threat model or the threat model may be corrupted.
```

이 오류는 일부 브라우저에서 ClickOnce 설치를 기본적으로 지원하지 않기 때문에 발생합니다. 이러한 경우 ClickOnce 애플리케이션 파일이 사용자의 하드 드라이브에 다운로드됩니다.

#### <a name="workaround"></a>해결 방법

TMT7.application 파일을 두 번 클릭하여 Threat Modeling Tool을 시작하면 이 오류가 계속 표시됩니다. 그러나 이 오류를 무시하면 도구는 정상적으로 작동합니다. TMT7.application 파일을 두 번 클릭하여 Threat Modeling Tool을 시작하는 대신, 설치 중에 Windows 메뉴에서 만든 바로 가기를 사용해 Threat Modeling Tool을 시작해야 합니다.

## <a name="system-requirements"></a>시스템 요구 사항

- 지원되는 운영 체제
  - [Microsoft Windows 10 1주년 업데이트](https://blogs.windows.com/windowsexperience/2016/08/02/how-to-get-the-windows-10-anniversary-update/#HTkoK5Zdv0g2F2Zq.97) 이상
- 필요한 .NET 버전
  - [.NET 4.7.1](https://go.microsoft.com/fwlink/?LinkId=863262) 이상
- 추가 요구 사항
  - 도구와 템플릿에 대한 업데이트를 받으려면 인터넷 연결이 필요합니다.

## <a name="documentation-and-feedback"></a>문서 및 피드백

- Threat Modeling Tool에 대한 문서는 [docs.microsoft.com](./threat-modeling-tool.md)에 있으며 [도구 사용](./threat-modeling-tool-getting-started.md)에 대한 정보를 포함합니다.

## <a name="next-steps"></a>다음 단계

[Microsoft Threat Modeling Tool](https://aka.ms/threatmodelingtool)의 최신 버전을 다운로드합니다.