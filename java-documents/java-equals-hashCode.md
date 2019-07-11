# Java中的equals()和hashCode()

`equals()`和`hashCode()`是`java.lang.Object`类中定义的两个方法，这两个方法均用在对象比较的场景，即判断两个对象是否相等。在某些场景下，我们需要将我们自定义类的比较逻辑按照们的应用场景来工作，就可以通过`Override`这两个方法来达到目的。

`equals()`和`hashCode()`两个方法的定义：

```java
package java.lang;

// equals()方法定义
public boolean equals(Object obj) {
    return (this == obj);
}

// hashCode()方法定义
public native int hashCode();
```

## 1 equals()方法

`equals()`方法的作用是判断所传入的对象是否跟当前对象相同，JDK对这个方法的默认实现就是比较两个对象的内存地址，只有这两个对象的内存地址一样才认为它们相同。

## 2 hashCode()方法

`hashCode()`方法的作用是为对象生成一个int类型的哈希码（hashcode），hashCode()方法主要用于配合Java中基于散列的集合一起工作，比如HashMap、HashTable以及HashSet。

## 3 equals() & hashCode()

如果想自定义对象的对比逻辑，我们需要覆写对象的`equals()`方法。**如果覆写了`equals()`方法，那么必须同时覆写`hashCode()`方法。因为如果仅仅覆写equals()，对象的对比机制可能在某些业务场景能正常工作，但是在结合散列集合（如HashMap）工作的时候，将不能正确按照我们的预期工作！**

>1. 两个对象相等，那它们的`hashCode()`方法的返回值一定相同
>2. 两个对象的`hashCode()`方法的返回值相同，这两个对象却不一定相等

**下面我们来实践一下：**

场景预设：我们定义一个`Book`类，我们自定义的对比机制为：当且仅当两本书的`id`和`name`都一样的时候，我们认为它们一样（相等，是同一本书）；否则不一样（不是同一本书）。

### 3.1 不覆写equals()和hashCode()的情况

`Book.java`类的定义如下：

```java
public class Book {

    private int id;
    private String name;

    public Book(int id, String name) {
        this.id = id;
        this.name = name;
    }

    public int getId() {
        return id;
    }

    public void setId(int id) {
        this.id = id;
    }

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

}
```

> 测试代码1如下

```java
private static void test1() {
    Book book1 = new Book(1, "Effective Java");
    Book book2 = new Book(1, "Effective Java");
    System.out.println("book1.equals(book2): " + book1.equals(book2));
    System.out.println("book1.hashCode(): " + book1.hashCode());
    System.out.println("book2.hashCode(): " + book2.hashCode());
}

// 测试结果
book1.equals(book2): false
book1.hashCode(): 1590550415
book2.hashCode(): 1058025095
```

根据场景预设，只要id和name相同我们就认为是同一本书，所以`equals()`对比的结果为`true`才符合我们的预期。但是结果并相同，这是因为我们并没有覆写`equals()`方法，所以默认是对比两个对象的地址。上述测试代码分别`new`了两个`Book`对象，地址肯定不一样，所以对比结果为`false`。

### 3.2 仅覆写equals()的情况

现在我们在`Book.java`类中覆写`equals()`方法，自定义对比机制：

```java
@Override
public boolean equals(Object obj) {
    if (obj == null) {
        return false;
    }
    if (this == obj) {
        return true;
    }
    if (!(obj instanceof Book)) {
        return false;
    }
    return this.getId() == ((Book) obj).getId() && this.getName().equals(((Book) obj).getName());
}
```

此时再运行3.1中的test1()测试代码，结果如下：
```java
book1.equals(book2): true
book1.hashCode(): 1590550415
book2.hashCode(): 1058025095
```

从上面测试结果看来，只要书本的`id`和`name`相同，我们自定义的对比机制已经能正确判断它们是同一本书；

但是，由于我们仅覆写了`equals()`，并没有覆写`hashCode()`，那么我们这个比较机制在配合`HashMap`、`HashTable`以及H`ashSet`这些散列集合进行使用的时候，将不能正确得到预期的结果。

>测试代码2如下：

```java
private static void test2() {
    Book book1 = new Book(1, "Effective Java");
    Book book2 = new Book(1, "Effective Java");
    System.out.println("book1.equals(book2): " + book1.equals(book2));
    System.out.println("book1.hashCode(): " + book1.hashCode());
    System.out.println("book2.hashCode(): " + book2.hashCode());
    // map——维护书本与库存量的关系
    Map<Book, Integer> bookStock = new HashMap<>();
    // 设置id为1，书名为"Effective Java"的这本书的库存为10
    bookStock.put(book1, 10);
    // 查询id为1，书名为"Effective Java"的这本书的库存
    System.out.println("Book[id: 1, name: Effective Java]: " + bookStock.get(book2));
}

// 测试结果
book1.equals(book2): true
book1.hashCode(): 1590550415
book2.hashCode(): 1058025095
Book[id: 1, name: Effective Java]： null
```

查询结果为null，说明这本书（id=1&&name=Effective Java）在库存中不存在。可是我们明明已经将这本书（id=1&&name=Effective Java）的库存设置成10并更新到库存里了！哪里出了问题？
前面提到，任何时候覆写equals()，必须同时覆写hashCode()方法，否则在结合散列集合将无法正确工作！

>这是因为，散列集合在添加、查找元素的时候都用到了hashCode()方法。比如我们测试代码中的HashMap，在put或者get的时候，都会先将Key对象的hashCode()返回值进行计算，得到一个hash值，根据这个值去定位Value的位置。从上面的测试结果可知，虽然是同一本书，但是它们的hashCode()返回值却不同。这就导致HashMap认为book1和book2是两个不同的Key，所以我们在put(book1, 10)却get(book2)的时候肯定找不到这本书。

### 3.3 同时覆写equals() & hashCode()的情况

为了能让我们对象的自定义对比机制能正常工作，我们在覆写`equals()`之后，一定也要同时覆写`hashCode()`：

```java
@Override
public boolean equals(Object obj) {
    if (obj == null) {
        return false;
    }
    if (this == obj) {
        return true;
    }
    if (!(obj instanceof Book)) {
        return false;
    }
    return this.getId() == ((Book) obj).getId() && this.getName().equals(((Book) obj).getName());
}

@Override
public int hashCode() {
    // 为了简单演示，我们这里将每本书的hashCode返回值设置成书本的id（保证唯一性）
    return this.getId();
}
```

然后我们再运行3.2中的`test2()`测试代码，结果如下：

```java
book1.equals(book2): true
book1.hashCode(): 1
book2.hashCode(): 1
Book[id: 1, name: Effective Java]: 10
```

运行结果显示，同时覆写`equals()`和`hashCode()`之后，程序已经如我们的预期正确运行。

**虽然`book1`和`book2`是两个不同的对象（对象地址不一样），但是我们通过覆写`equals()`和`hashCode()`来定制我们的对比机制，达到我们自定义的对象对比逻辑，满足我们一些特殊的业务场景要求。**

## 4 最后总结

如果我们在使用自定义类的时候，想自定义对象的对比机制来达到某些需求场景的要求，例如上面的例子：如果书本的编号（id）和书本的名字（name）都相同，则认为它们是同一本书。我们可以通过同时覆写equals()和hashCode()来达到全面的效果，而不是局部起作用（如3.2章节中仅覆写equals()的情况）。

**任何时候，如果覆写了对象`equals()`方法，就一定也要同时覆写对象的`hashCode()`方法！**

<br /> <br /> <br />

>**——————–【参考文章】——————–**
>1. [Working With hashcode() and equals()](https://dzone.com/articles/working-with-hashcode-and-equals-in-java)
>2. [浅谈Java中的hashcode方法](https://www.cnblogs.com/dolphin0520/p/3681042.html)
>3. [Java 中正确使用 hashCode 和 equals 方法](https://www.oschina.net/question/82993_75533)