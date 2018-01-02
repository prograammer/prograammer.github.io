---
layout: post
title:  "heapdump 에서 old 영역의 객체 찾기"
date:   2017-12-22 1:32:00 +0900
description: "heapdump 에서 old 영역의 객체 찾기"
categories: java
comments: true
---

minor GC 후 old 영역으로 이동 된 객체들을 찾기 위한 방법을 설명합니다.
이를 위해 다음 3가지 항목이 필요합니다.
  1. MAT(Eclipse Memory Analyzer tool)
  2. heap dump
  3. heap (영역별) 사용 현황 정보
     - thread dump (HotSpot VM 1.6 부터 맨 마지막 부분에 추가 됨)
     - -XX:+PrintHeapAtGC JVM 옵션을 이용


# heap (영역별) 사용 현황 로그
![heap 영역(세대)별 사용 현황 로그]({{"/assets/heap_memory_address.png" | absolute_url }})



# OQL(MAT)
```sql
SELECT * 
FROM INSTANCEOF java.lang.Object t 
WHERE (toHex(t.@objectAddress) >= "0x7b9c00000" 
    AND toHex(t.@objectAddress) <= "0x7bd02d6d0")
```


# heap 영역(세대)별 사용 현황 로그(Text)
{% highlight bash %}
{Heap before GC invocations=79 (full 4):
 PSYoungGen      total 25600K, used 23060K [0x00000007bdf00000, 0x00000007bfe80000, 0x00000007c0000000)
  eden space 18944K, 100% used [0x00000007bdf00000,0x00000007bf180000,0x00000007bf180000)
  from space 6656K, 61% used [0x00000007bf800000,0x00000007bfc05360,0x00000007bfe80000)
  to   space 6656K, 0% used [0x00000007bf180000,0x00000007bf180000,0x00000007bf800000)
 ParOldGen       total 68608K, used 53421K [0x00000007b9c00000, 0x00000007bdf00000, 0x00000007bdf00000)
  object space 68608K, 77% used [0x00000007b9c00000,0x00000007bd02b6d0,0x00000007bdf00000)
 Metaspace       used 49175K, capacity 49844K, committed 53120K, reserved 1095680K
  class space    used 5350K, capacity 5501K, committed 6528K, reserved 1048576K
[GC (Allocation Failure) [PSYoungGen: 23060K->3615K(23040K)] 76482K->57045K(91648K), 0.0231463 secs] [Times: user=0.03 sys=0.07, real=0.03 secs] 
Heap after GC invocations=79 (full 4):
 PSYoungGen      total 23040K, used 3615K [0x00000007bdf00000, 0x00000007bfd80000, 0x00000007c0000000)
  eden space 18944K, 0% used [0x00000007bdf00000,0x00000007bdf00000,0x00000007bf180000)
  from space 4096K, 88% used [0x00000007bf180000,0x00000007bf507e18,0x00000007bf580000)
  to   space 6144K, 0% used [0x00000007bf780000,0x00000007bf780000,0x00000007bfd80000)
 ParOldGen       total 68608K, used 53429K [0x00000007b9c00000, 0x00000007bdf00000, 0x00000007bdf00000)
  object space 68608K, 77% used [0x00000007b9c00000,0x00000007bd02d6d0,0x00000007bdf00000)
 Metaspace       used 49175K, capacity 49844K, committed 53120K, reserved 1095680K
  class space    used 5350K, capacity 5501K, committed 6528K, reserved 1048576K
}
{% endhighlight %}

# 참고
- [oracle > How do I find what's getting promoted to my old generation?](https://blogs.oracle.com/poonam/how-do-i-find-whats-getting-promoted-to-my-old-generation)
- [stackoverflow > Meaning of heap memory addresses in GC logs using -XX:+PrintHeapAtGC?](https://stackoverflow.com/a/28347947) 
- [eclipse > Memory Analyzer > Reference > OQL Syntax](http://help.eclipse.org/oxygen/index.jsp?topic=%2Forg.eclipse.mat.ui.help%2Freference%2Foqlsyntax.html&cp=66_4_2)
