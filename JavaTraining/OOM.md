# Java OOM 介紹與分析

## EP1: What is OOM \(2016/01/08\)

1. What is OOM
2. GC
3. MAT introduction
4. MAT heapdump analyze
5. Practice

## Outline

* What is OOM
* Why OOM
* Java Memory Model
* To be continued...

## What is OOM

* Out Of My way?
* Out Of Mana?
* Out Of Memory!

## Why OOM

* Try to allocate a very large memory.
* Memory leak. No sufficient memory available.
* Too many classes loaded.
* ??? God knows.

1.

```java
byte[] huge = new byte[10_000_000_000L];
```

2.

```java
/* Reproducible in Java 6 */
public class TestGC {
    private String largeString = new String(new byte[100000]);

    public String getString() {
        return largeString.substring(0, 2);
    }

    public static void main(String[] args) {
        List list = new ArrayList();
        for (int i = 0; i < 1000000; i++) {
            TestGC gc = new TestGC();
            list.add(gc.getString());
        }
    }
}
```

## Java Memory Model

* Heap
  * Young generation
  * Old generation
* Non-heap



