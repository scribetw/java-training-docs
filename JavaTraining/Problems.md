# Problems

## Auto unboxing

Q. Which one is corrent?

```java
Integer i = null;
if (i == 0) {
    System.out.println("Zero");
} else {
    System.out.println("Not Zero");
}
```

1. Zero
2. Not Zero
3. Throws Exception
4. None of above

Use Objects.equals\(Object, Object\) to compare objects.

Ref: JLS 15.21.1 Numerical Equality Operators == and !=

