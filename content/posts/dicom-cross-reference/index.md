---
title: DICOM Cross Reference
tags: ["dicom", "3D", "linear algebra"]
categories: dicom
date: 2021-06-08
---

다른 이미지에 사영시킬 이미지를 reference 평면, 이 reference 이미지가 사영될 이미지를 target 평면이라고 하자.

Cross reference는 이 두 평면이 공간상에서 교차할 때, 그 교선을 target 평면상에 표시하는 기능이다.
두 평면 모두 무한평면이 아닌, 일반적으로는 직사각형 면이기 때문에 두 면이 서로 평행하지 않더라도 교차하지 않을 수도 있다.

여러가지 방법을 생각해볼 수 있는데, 이 글에서는 다음과 같은 방법으로 계산한다.
1. reference 평면의 경계선들을 구한다. 이미지는 직사각형이므로 4개의 선분이 얻어진다.
2. 1에서 구한 각 선분들과 target 평면의 교점을 계산한다. 선분들은 하나의 닫힌 볼록다각형을 구성하므로, 최대 2개의 교점이 얻어진다.
3. 2에서 얻은 교점을 이은 선분을 target 평면 상의 픽셀 좌표계로 변환해서 그린다.

위 계산을 위해 필요한 정보로는

{{< katex >}}
\begin{cases}
  P_t &= \text{Image position} \\
  \vec{v_t}, \vec{u_t} &= \text{Image orientation} \\
  d_v, d_u &= \text{Pixel spacing}
\end{cases}
{{< /katex >}}

{{< figure src="crossref.png" title="Cross Reference" width="300px" >}}

target 평면의 방정식은

{{< katex >}}
\vec{n_t} \cdot ( P - P_t ) = 0 \quad (\vec{n_t} = \vec{v_t} \times \vec{u_t})
{{< /katex >}}
이고 [^1]

두 점 {{< katex >}}a, b{{< /katex >}}를 잇는 직선의 방정식은

{{< katex >}}
P = \lambda (b - a) + a
{{< /katex >}}
이고, {{< katex >}}0 \leq \lambda \leq 1{{< /katex >}} 이어야 {{< katex >}}P{{< /katex >}}가 선분 {{< katex >}}\overline{ab}{{< /katex >}} 위에 있게 된다.[^2]

교점을 구하면
{{< katex >}}
\vec{n_t} \cdot (\lambda (b - a) + a - P_t) = 0 \\
\implies \lambda \vec{n_t} \cdot (b - a) = \vec{n_t} \cdot (P_t - a) \\
\implies \lambda = \dfrac{\vec{n_t} \cdot (P_t - a)}{\vec{n_t} \cdot (b - a)}
{{< /katex >}}

이 교점을 이미지 픽셀 좌표로 변환하려면
{{< katex >}}
P = P_t + x (d_u \vec{u_t}) + y (d_v \vec{v_t})
{{< /katex >}}
인 {{< katex >}}(x, y){{< /katex >}}를 찾아야 한다.

우선 {{< katex >}}x{{< /katex >}}부터 구하기 위해 {{< katex >}}\vec{v_t}{{< /katex >}}를 외적하여 {{< katex >}}y{{< /katex >}}를 제거한다. [^3]

{{< katex >}}
x (d_u \vec{u_t}) = P - P_t - y (d_v \vec{v_t}) \\
\implies x (d_u \vec{u_t} \times \vec{v_t}) = (P - P_t) \times \vec{v_t} \\
\implies x d_u (\vec{u_t} \times \vec{v_t}) \cdot (\vec{u_t} \times \vec{v_t}) = ((P - P_t) \times \vec{v_t}) \cdot (\vec{u_t} \times \vec{v_t})  \\
\implies x = \dfrac{((P - P_t) \times \vec{v_t}) \cdot (\vec{u_t} \times \vec{v_t})}{d_u (\vec{u_t} \times \vec{v_t}) \cdot (\vec{u_t} \times \vec{v_t})}
{{< /katex >}}

{{< katex >}}y{{< /katex >}}도 마찬가지 방법으로 구하면

{{< katex >}}
y = \dfrac{((P - P_t) \times \vec{u_t}) \cdot (\vec{v_t} \times \vec{u_t})}{d_v (\vec{v_t} \times \vec{u_t}) \cdot (\vec{v_t} \times \vec{u_t})}
{{< /katex >}}

[^1]: [평면의 방정식](https://ko.wikipedia.org/wiki/%ED%8F%89%EB%A9%B4#%EC%A0%90%EA%B3%BC_%EB%B2%95%EC%84%A0_%EB%B2%A1%ED%84%B0)
[^2]: [선분의 방정식](https://ko.wikipedia.org/wiki/%EC%84%A0%EB%B6%84)
[^3]: 일반적으론 {{< katex >}}\vec{v_t}{{< /katex >}}와 {{< katex >}}\vec{u_t}{{< /katex >}}는 수직이므로 바로 내적을 해서 간결하게 구할 수도 있지만,
  아닌 경우도 있으므로 이렇게 계산한다
