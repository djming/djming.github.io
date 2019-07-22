---
title: MySQL学习笔记
reward: false
date: 2019-01-02 09:25:11
tags:
	- MySQL
---

# MySQL学习笔记

MySQL索引：提高读取速度，降低写入速度。索引也会占用磁盘空间。

MySQL指定引擎：`create table xxx(...) engine=$ENGINE_NAME`

复合组件：多个字段共同组成主键，允许其中部分字段重复，不可所有字段同时重复。
