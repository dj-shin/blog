---
title: DICOM SOP
tags: ["dicom"]
categories: dicom
date: 2021-04-28
---

# DICOM SOP

DICOM은 여러 의료 이미지 뿐만 아니라 Structured Report (SR), PDF 등의 문서, 심지어는 통신을 위해 주고받는 메시지까지 포함하는
매우 다양한 정보를 나타내는 표준입니다.

그래서 `이 DICOM 파일이 어떤 종류의 데이터인가`를 알고 있어야 하는데, 이걸 SOP Class라고 부릅니다.
이해를 위해 엑스레이 사진을 예시로 들어보겠습니다.

    (0008,0016) UI 1.2.840.10008.5.1.4.1.1.1.1        # 1, 28 SOP Class UID

SOP Class UID라는 태그에 이 DICOM 이미지가 어떤 SOP에 해당하는지가 담겨 있습니다.
저 UID를 [표준 SOP Class 목록](http://dicom.nema.org/medical/dicom/current/output/chtml/part04/sect_B.5.html)에서 찾으면

{{< table >}}
|SOP Class Name                                |SOP Class UID              |IOD Specification      |
|----------------------------------------------|---------------------------|-----------------------|
|Digital X-Ray Image Storage - For Presentation|1.2.840.10008.5.1.4.1.1.1.1|Digital X-Ray Image IOD|
{{< /table >}}

이렇게 엑스레이 이미지라는 사실을 알 수 있습니다.

그런데 `어떤 종류의 데이터인가`라는 정보는 사실 Information Object Definition (IOD) 만으로 충분합니다.
그럼 SOP Class가 왜 필요할까요?

SOP란 Service-Object Pair의 약자로 Information Object Definition (IOD)와 DICOM Service Element (DIMSE)의 결합을 말합니다.
Object에 해당하는 IOD 말고도, Service에 해당하는 DIMSE 정보도 포함하고 있다는 뜻이죠.
다만 이미지 IOD의 경우는 할 수 있는 Service들이 보통 같습니다.
때문에 이미지 IOD가 조합된 SOP Class들은 보통 [Storage Service Class](http://dicom.nema.org/medical/dicom/current/output/chtml/part04/chapter_B.html)에 속하며
C-STORE Service를 공통적으로 지원합니다.
