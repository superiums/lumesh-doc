---
title: "{{ replace .Name "-" " " | title }}"
authors:
  - beima
date: {{ .Date }}
goal:
  - 正念观照
  - 光明中脉
  - 高维链接
  - 身心疗愈
  - 本心回归
  - 潜能开发
method:
  - 正念冥想
  - 禅修
  - 呼吸冥想
  - 身体扫描
  - 慈心冥想
  - 可视化冥想
  - 止念
  - 内观
duration:
  - 快速练习
  - 深度冥想
  - 睡前冥想
  - 工作间隙冥想
  - 户外冥想
  - 居家冥想
level:
  - 入门
  - 中级
  - 高级
  - 精进
series: ""
draft: false
description: ""
---

{{< video
    src="mp3/{{ .Name }}.mp3"
    caption="{{ .Name }} **beima**"
    poster="img/moon3.webp"
    loop=false
    muted=false
>}}

