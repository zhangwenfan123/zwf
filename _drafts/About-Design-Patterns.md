---
layout: post
title:  "浅谈设计模式"
categories: Design Patterns
tags:  Software
---

* content
{:toc}

Library|	Operation|	Op Count|	Time (ms)|	Time/op (μs)
Dapper|	insert|	30000|	10,016.88|	333.90
Fast Crud|	insert|	30000|	10,431.64|	347.72
Dapper| Extensions|	insert|	30000|	13,272.40|	442.41
Simple Crud|	insert|	30000|	19,954.31|	665.14
Entity Framework|	insert|	30000|	43,636.47|	1,454.55


Dapper	update	30000	6,439.43	214.65
Fast Crud	update	30000	6,668.78	222.29
Dapper Extensions	update	30000	8,803.26	293.44
Simple Crud	update	30000	10,204.28	340.14
Entity Framework	update	30000	39,954.88	1,331.83
Dapper	delete	30000	8,312.94	277.10
Fast Crud	delete	30000	8,693.02	289.77
Dapper Extensions	delete	30000	10,804.55	360.15
Simple Crud	delete	30000	13,642.98	454.77
Entity Framework	delete	30000	35,973.40	1,199.11
Dapper	select by id	30000	6,010.58	200.35
Fast Crud	select by id	30000	6,172.08	205.74
Dapper Extensions	select by id	30000	6,355.57	211.85
Simple Crud	select by id	30000	12,912.19	430.41
Entity Framework	select by id	30000	21,126.72	704.22
Dapper	select all	3	217.06	72,353.87
Fast Crud	select all	3	262.41	87,468.83
Dapper Extensions	select all	3	291.22	97,073.00
Simple Crud	select all	3	814.65	271,549.57
Entity Framework	select all	3	5,431.27	1,810,423.27