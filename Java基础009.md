### 流程控制

- switch-case

  与if不同的是，switch-case只能完成等值判断，而无法完成判断大小。

  如果是判断两个值是否相等，可以使用switch-case，如果比较两个值的大小关系，则不能使用swtich-case。

  switch支持int、short、byte、char、枚举、String类型，不支持boolean类型。

  基本语法：

  ```java
  switch(/*变量*/){
  	case /*值1*/：
  		//业务代码
    break;
  	case /*值2*/：
  		//业务代码
    break;
    default:
    	//业务代码
    break;
  }
  ```

  case判断变量是否等于某个值，default表示所有的case都不成立的情况下所执行的代码。

  - 1奖励2000

  - 2奖励1000

  - 3奖励500

  - 否则没有奖励

    ```java
    int placing = 2;
    if (placing == 1) {
    	System.out.println("奖励2000元");
    } else if (placing == 2) {
    	System.out.println("奖励1000元");
    } else if (placing == 3) {
    	System.out.println("奖励500元");
    } else {
    	System.out.println("没有奖励");
    }
    
    switch (placing) {
    	case 1:
    		System.out.println("奖励2000元");
    		break;
    	case 2:
    		System.out.println("奖励1000元");
    		break;
      case 3:
    		System.out.println("奖励500元");
    		break;
    	default:
    		System.out.println("没有奖励");
    		break;
    }
    ```

### 循环

for、while、do-while、foreach

循环四要素：

- 初始化循环变量
- 循环条件
- 循环体
- 更新循环变量

while :

```java
//初始化循环变量
while (/*循环条件*/) {
	//循环体
	//更新循环变量
}
```

```java
//初始化循环变量
int num = 0;
//循环条件
while (num < 10) {
	System.out.println(num + "Hello World!");
	//更新循环变量
  num++;
}
```

```java
Scanner scanner = new Scanner(System.in);
String flag = "y";
while (flag.equals("y")) {
	System.out.print("请输入学生学号：");
	int id = scanner.nextInt();
	System.out.println("用户输入的学号是："+id);
	switch (id) {
		case 1:
			System.out.println("张三的成绩是98");
			break;
		case 2:
			System.out.println("李四的成绩是91");
			break;
		case 3:
			System.out.println("王五的成绩是89");
			break;
		default:
			System.out.println("请输入正确学号");
			break;
	}
	System.out.print("是否继续 y/n：");
	flag = scanner.next();
}
System.out.println("感谢使用学生成绩查询系统");
```

do-while :

```java
//初始化循环变量
int num = 0;
//循环条件
do {
  //循环体
  System.out.println("Hello World!");
  //更新循环变量
  num++;
} while (num < 10);
//循环条件
```

```java
String flag;
Scanner scanner = new Scanner(System.in);
do {
  System.out.print("请输入学生学号：");
  int id = scanner.nextInt();
  System.out.println("用户输入的学号是："+id);
  switch (id) {
    case 1:
      System.out.println("张三的成绩是98");
      break;
    case 2:
      System.out.println("李四的成绩是91");
      break;
    case 3:
      System.out.println("王五的成绩是89");
      break;
    default:
      System.out.println("请输入正确学号");
      break;
  }
  System.out.print("是否继续 y/n：");
  flag = scanner.next();
} while (flag.equals("y"));
System.out.println("感谢使用本系统");
```

先执行再判断，用do-while比较好

```java
Scanner scanner = new Scanner(System.in);
String result;
do {
  System.out.println("张三参加体能测试，跑1000米");
  System.out.print("是否合格 y/n");
  result = scanner.next();
} while (result.equals("n"));
System.out.println("合格，通过测试");
```

while do-while适合循环次数不确定的情况下

for :

```java
for (/*初始化循环变量；循环条件；更新循环变量*/) {
	//循环体
}
```

```java
int i = 0;
for (i = 5;i < 10;) {
  System.out.println("Hello World!");
  i++;
}
```

```java
for (int i = 0; i < 10; i++) {
  System.out.println("Hello World!");
}
```

while、do-while、for循环，三种循环的区别：

- 相同点：都遵循循环四要素，初始化循环变量、循环条件、循环体、更新循环变量
- 不同点：
  - while和do-while适用于循环次数不确定的业务场景：for适用于循环次数确定的场景。
  - while和for都是先判断循环条件，再执行循环体；do-while先执行循环体，再判断循环条件。

分别使用while、do-while、for循环输出10以内的所有奇数。

```java
//while循环
int num = 0;
while (num <= 10) {
  if (num % 2 != 0) {
    System.out.println(num);
  }
  num++;
}
//do-while循环
int num1 = 0;
do {
  if (num1 % 2 != 0) {
    System.out.println(num1);
  }
  num1++;
} while (num1 <= 10);
//for循环
for (int i = 0; i <= 10; i++) {
  if (i % 2 != 0) {
    System.out.println(i);
  }
}
```

for循环只能适用于循环次数确定的场景下（for也可以适用于循环次数不确定的场景，只不过一般不会用这种方式进行开发），while和do-while循环次数确定或不确定都可以使用。

```java
Scanner scanner = new Scanner(System.in);
boolean flag = true;
for (;flag;){
  String input = scanner.next();
  if (input.equals("y")){
    flag = true;
  } else {
    flag = false;
  }
}

```

非常规的用法

```java
Scanner scanner = new Scanner(System.in);
//for (String result = "n";
//     result.equals("n");
//     result = scanner.next()){
String result = "n";
for (;result.equals("n");)
  System.out.println("张三参加体能测试，跑1000米");
  System.out.print("是否合格？y/n");
	result = scanner.next();
}
System.out.println("合格，通过测试");
```

