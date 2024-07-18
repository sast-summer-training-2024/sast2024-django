# 课前准备

## 前置知识

在课前, 我希望同学们已经学习过:

- Python, 知道其基本语法和数据结构

- Web 基础原理, 知道 HTTP 协议是怎么回事

- 前后端分离的基本概念, 知道前后端分离的优势

这些知识相当多, 我没法在课上讲 (之前也讲过了). 一些比较复杂的知识我会重新讲一遍.

## 环境准备

电脑上已经安装了:

- conda (如果有, 那么新建一个环境; 如果没有, 万一遇到了奇怪的包冲突别来找我)

- Python >= 3.8 (Linux 自带的 Python3 应该满足这个要求, conda 建议直接装 `conda create -n env_name python` 最新版本)

- Django (如果没有, 请使用 `pip install django` 安装)

- Apifox 或者 Postman (用于测试 API, 当然没有也不是不行)

## 课程简述

Django 是一个 Python 的 Web 框架. 用 c7wgg 的话说,

> 作为适合初学者入门前后端分离开发范式的开发框架，Django 可以让你在距离 DDL 还有几个小时的时候优雅地摆烂。
>
> From: https://summer23.net9.org/backend/django/

之前我是不觉得这句话说得对的 (毕竟我一般等不到距离 DDL 还有几个小时的时候), 但是昨天 (相对于写这段话的时候) 我发现我用 2h 不到的时间就搓出来了课程作业的全部框架, 看来这句话还是有些道理.

在本节课程中, 你将学到:

- Python 的部分高级用法
  - Decorator 装饰器
  - 如何动态传参

- 后端开发的基本模式与流程
  - MVC 模式
  - 后端的作用与对后端的期望

- Django 的用法
  - Django 的工程结构
  - Django 的 MVC 模式
    - Model
    - Template (View), 这在前后端分离的开发范式里用不着
    - View (Controller)
  - Django 的 ORM
  - Django 的单元测试
