---
title: kubeadm部署kubernetes集群
date: 2019-07-22 17:37:20
tags: 
 - kubernetes
 - devops
categories: 
 - kubernetes
---

javascript是一门充满活力、简单易用的语言，又是一门具有许多复杂微妙技术的语言。即使是经验丰富的javascript开发者，如果没有认真学习的话，也无法真正理解它们，这就是javascript的矛盾之处。由于javascript不必理解就可以使用，因此通常来说很难真正理解语言本身，这就是我们面临的挑战。不满足于只是让代码正常工作，而是想要弄清楚为什么，勇于挑战这条崎岖颠簸的少有人走的路，拥抱整个javascript



### 概 述

Kubernetes集群的搭建方法其实有多种，比如我在之前的文章《利用K8S技术栈打造个人私有云（连载之：K8S集群搭建）》中使用的就是二进制的安装方法。虽然这种方法有利于我们理解 k8s集群，但却过于繁琐。而 kubeadm是 Kubernetes官方提供的用于快速部署Kubernetes集群的工具，其历经发展如今已经比较成熟了，利用其来部署 Kubernetes集群可以说是非常好上手，操作起来也简便了许多，因此本文详细叙述之。

注： 本文首发于 My Personal Blog：CodeSheep·程序羊，欢迎光临 小站

### 节点规划

本文准备部署一个 一主两从 的 三节点 Kubernetes集群，整体节点规划如下表所示：

主机名	IP	角色
k8s-master	192.168.39.79	k8s主节点	
k8s-node-1	192.168.39.77	k8s从节点	
k8s-node-2	192.168.39.78	k8s从节点

架构图如图
<!-- ![header](images/header.jpg) -->