

# for-each不能修改数组值 - - 邱涛

```java
输入：

public static void main(String[] args) {

  int[] a = new int[5];

  for(int i:a) {

   i=1;

  }
  

  System.out.println(Arrays.toString(a));

  for(int i=0;i<a.length;i++) {

   a[i]=1;

  }

  System.out.println(Arrays.toString(a));

 }



输出：

[0, 0, 0, 0, 0]

[1, 1, 1, 1, 1]



实际上：

  for(int i:a) {

   i=1;

  }

等于：

 for(int i=0;i<a.length;i++) {

   int b=a[i];

   b=1;

  }


```

