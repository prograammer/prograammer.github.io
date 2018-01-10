---
layout: post
title:  "Memory Analyzer 에서 OQL 예제"
date:   2018-01-10 11:00:00 +0900
description: "eclipse Memory Analyzer 에서 OQL 예제"
categories: java
comments: true
---

# className 체크
```sql
// java.util.Vector 클래스 객체만 조회
SELECT classof(v.elementData).getName() 
FROM java.util.Vector v 
WHERE (classof(v).getName() = "java.util.Vector")

SELECT v.@clazz.getName() 
FROM java.util.Vector v 
WHERE (classof(v).getName() = "java.util.Vector")
```

# Vector에 저장 된 객체 갯수 체크
```sql
// 객체를 10개 이상 가지고 있는 Vector 만 조회
SELECT * 
FROM java.util.Vector v 
WHERE v.elementData.@length > 10
```

# Vecor 에 저장 된 N 번째 객체 체크
```sql
// Vector 에 저장 된 첫 번째/ 두 번째 데이터 체크
SELECT toString(v.elementData[0]), toString(v.elementData[1]) 
FROM java.util.Vector v 
WHERE ((v.elementData[0] != null) and (v.elementData[1] != null) and (toString(v.elementData[0]) != "") and (toString(v.elementData[1]) != ""))
```


# 참고
- [eclipse > Memory Analyzer > Reference > OQL Syntax](http://help.eclipse.org/oxygen/index.jsp?topic=%2Forg.eclipse.mat.ui.help%2Freference%2Foqlsyntax.html&cp=66_4_2)
