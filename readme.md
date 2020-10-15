1\. 개요

\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_

이 문서는 본인인증 서비스 이해를 돕고 시스템 연동에 도움이 되고자 작성되었으며,

각 프로세스 및 Parameter 등 본인 인증 시스템 연동에 필요한 내용에 대해 설명한다

A. 사전 준비사항

  (구현 후 작성)

B. 서비스 연동 순서

  a. 각 서버 환경에 맞는 웹 통신 환경 설정

  b. CGI Script 수정(CP정보 입력 및 경로정보 수정 등)

  c. 본인인증 완료 후 CP작업 시행 (서비스 제공, TID 저장, DB 관련작업 등)

C. System Requirement

a. Linux / Sun OS / Windows 계열/ FreeBSD / AIX 등 모든 OS 지원

b. PHP / JSP / ASP / .NET 등

2\. 본인인증 서비스

\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_

다날의 본인인증 서비스의 구성 및 흐름 등에 관하여 설명한다.

A. 서비스 다이어그램

![](https://blog.kakaocdn.net/dn/yKS0b/btqKZohDLzh/dSZnJ5s0sd3ZzkR08KZzhk/img.png)

B. 데이터베이스

<table style="border-collapse: collapse; width: 44.1813%; height: 317px;" border="1" data-ke-style="style13"><tbody><tr style="height: 20px;"><td style="width: 33.3333%; height: 20px;" colspan="3">Log</td></tr><tr style="height: 20px;"><td style="width: 33.3333%; height: 20px;">PK</td><td style="width: 33.3333%; height: 20px;">idx</td><td style="width: 33.3333%; height: 20px;">int(10), Auto_Increment</td></tr><tr style="height: 20px;"><td style="width: 33.3333%; height: 20px;">&nbsp;</td><td style="width: 33.3333%; height: 20px;">ID</td><td style="width: 33.3333%; height: 20px;">varchar(20)</td></tr><tr style="height: 20px;"><td style="width: 33.3333%; height: 20px;">&nbsp;</td><td style="width: 33.3333%; height: 20px;"><span>a₁<br></span></td><td style="width: 33.3333%; height: 20px;">int(5)</td></tr><tr style="height: 20px;"><td style="width: 33.3333%; height: 20px;">&nbsp;</td><td style="width: 33.3333%; height: 20px;"><span style="color: #333333;">a₂<span></span></span></td><td style="width: 33.3333%; height: 20px;"><span style="color: #333333;">int(5)</span></td></tr><tr style="height: 20px;"><td style="width: 33.3333%; height: 20px;">&nbsp;</td><td style="width: 33.3333%; height: 20px;"><span style="color: #333333;">b₁<span></span></span></td><td style="width: 33.3333%; height: 20px;"><span style="color: #333333;">int(5)</span></td></tr><tr style="height: 20px;"><td style="width: 33.3333%; height: 20px;">&nbsp;</td><td style="width: 33.3333%; height: 20px;"><span style="color: #333333;">b₂</span></td><td style="width: 33.3333%; height: 20px;"><span style="color: #333333;">int(5)</span></td></tr><tr><td style="width: 33.3333%;">&nbsp;</td><td style="width: 33.3333%;"><span style="color: #333333;">timestamp</span></td><td style="width: 33.3333%;">datetime</td></tr></tbody></table>

<table style="border-collapse: collapse; width: 44.384%; height: 156px;" border="1" data-ke-style="style13"><tbody><tr style="height: 20px;"><td style="width: 99.9999%; height: 20px;" colspan="3">User</td></tr><tr style="height: 20px;"><td style="width: 33.3333%; height: 20px;">PK</td><td style="width: 33.3333%; height: 20px;">idx</td><td style="width: 33.3333%; height: 20px;">int(10), Auto_Increment</td></tr><tr style="height: 20px;"><td style="width: 33.3333%; height: 20px;">&nbsp;</td><td style="width: 33.3333%; height: 20px;">ID</td><td style="width: 33.3333%; height: 20px;">varchar(20)</td></tr><tr style="height: 20px;"><td style="width: 33.3333%; height: 20px;">&nbsp;</td><td style="width: 33.3333%; height: 20px;"><span>API Key</span></td><td style="width: 33.3333%; height: 20px;">varchar(20)</td></tr></tbody></table>

<table style="border-collapse: collapse; width: 44.6512%; height: 156px;" border="1" data-ke-style="style13"><tbody><tr style="height: 20px;"><td style="width: 99.9999%; height: 20px;" colspan="3">Admin</td></tr><tr style="height: 20px;"><td style="width: 33.3333%; height: 20px;">PK</td><td style="width: 33.3333%; height: 20px;">idx</td><td style="width: 33.3333%; height: 20px;">int(10), Auto_Increment</td></tr><tr style="height: 20px;"><td style="width: 33.3333%; height: 20px;">&nbsp;</td><td style="width: 33.3333%; height: 20px;">admin_ID</td><td style="width: 33.3333%; height: 20px;">varchar(20)</td></tr><tr style="height: 20px;"><td style="width: 33.3333%; height: 20px;">&nbsp;</td><td style="width: 33.3333%; height: 20px;">PSWD</td><td style="width: 33.3333%; height: 20px;">varchar(20)</td></tr></tbody></table>

<table style="border-collapse: collapse; width: 45.3488%; height: 283px;" border="1" data-ke-style="style13"><tbody><tr style="height: 20px;"><td style="width: 33.3333%; height: 20px;" colspan="3">Calculate Info</td></tr><tr style="height: 20px;"><td style="width: 33.3333%; height: 20px;">PK</td><td style="width: 33.3333%; height: 20px;">idx</td><td style="width: 33.3333%; height: 20px;">int(10), Auto_Increment</td></tr><tr style="height: 20px;"><td style="width: 33.3333%; height: 20px;">&nbsp;</td><td style="width: 33.3333%; height: 20px;">admin_ID</td><td style="width: 33.3333%; height: 20px;">varchar(20)</td></tr><tr style="height: 20px;"><td style="width: 33.3333%; height: 20px;">&nbsp;</td><td style="width: 33.3333%; height: 20px;"><span>a₁<br></span></td><td style="width: 33.3333%; height: 20px;">int(5)</td></tr><tr style="height: 20px;"><td style="width: 33.3333%; height: 20px;">&nbsp;</td><td style="width: 33.3333%; height: 20px;"><span style="color: #333333;">a₂</span></td><td style="width: 33.3333%; height: 20px;"><span style="color: #333333;">int(5)</span></td></tr><tr style="height: 20px;"><td style="width: 33.3333%; height: 20px;">&nbsp;</td><td style="width: 33.3333%; height: 20px;"><span style="color: #333333;">b₁</span></td><td style="width: 33.3333%; height: 20px;"><span style="color: #333333;">int(5)</span></td></tr><tr style="height: 20px;"><td style="width: 33.3333%; height: 20px;">&nbsp;</td><td style="width: 33.3333%; height: 20px;"><span style="color: #333333;">b₂</span></td><td style="width: 33.3333%; height: 20px;"><span style="color: #333333;">int(5)</span></td></tr><tr style="height: 20px;"><td style="width: 33.3333%; height: 20px;">&nbsp;</td><td style="width: 33.3333%; height: 20px;"><span style="color: #333333;">timestamp</span></td><td style="width: 33.3333%; height: 20px;">datetime</td></tr></tbody></table>

C. Parameter

  a. INPUT

<table style="border-collapse: collapse; width: 100%; height: 60px;" border="1" data-ke-style="style12"><tbody><tr style="height: 20px;"><td style="width: 25%; height: 20px;">Field</td><td style="width: 25%; height: 20px;">Required</td><td style="width: 25%; height: 20px;">Max(Byte)</td><td style="width: 25%; height: 20px;">비고</td></tr><tr><td style="width: 25%;"><span style="color: #333333;">CPID</span></td><td style="width: 25%;">Y</td><td style="width: 25%;">10</td><td style="width: 25%;">서비스 사용자 ID</td></tr><tr style="height: 20px;"><td style="width: 25%; height: 20px;">ORIGIN_MONEY</td><td style="width: 25%; height: 20px;">Y</td><td style="width: 25%; height: 20px;">2</td><td style="width: 25%; height: 20px;">원화</td></tr><tr style="height: 20px;"><td style="width: 25%; height: 20px;">EXCHANGE_MONEY</td><td style="width: 25%; height: 20px;">Y</td><td style="width: 25%; height: 20px;">2</td><td style="width: 25%; height: 20px;">페이코인</td></tr><tr><td style="width: 25%;">CHANGE_TYPE</td><td style="width: 25%;">Y</td><td style="width: 25%;">2</td><td style="width: 25%;">살 때 / 팔 때</td></tr></tbody></table>

  b. OUTPUT

<table style="border-collapse: collapse; width: 100%; height: 80px;" border="1" data-ke-style="style12"><tbody><tr style="height: 20px;"><td style="width: 25%; height: 20px;">Field</td><td style="width: 25%; height: 20px;">Required</td><td style="width: 25%; height: 20px;">Max(Byte)</td><td style="width: 25%; height: 20px;">비고</td></tr><tr style="height: 20px;"><td style="width: 25%; height: 20px;">RETURNCODE</td><td style="width: 25%; height: 20px;">Y</td><td style="width: 25%; height: 20px;">4</td><td style="width: 25%; height: 20px;">결과 코드</td></tr><tr style="height: 20px;"><td style="width: 25%; height: 20px;">RETURNMSG</td><td style="width: 25%; height: 20px;">Y</td><td style="width: 25%; height: 20px;">255</td><td style="width: 25%; height: 20px;">결과 메시지</td></tr><tr style="height: 20px;"><td style="width: 25%; height: 20px;">EXCHANGE_RATE</td><td style="width: 25%; height: 20px;">Y</td><td style="width: 25%; height: 20px;">10</td><td style="width: 25%; height: 20px;">결과 환율</td></tr></tbody></table>
