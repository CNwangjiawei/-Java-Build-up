## HashMap<br>
存储在数组中，put(key,value)时会执行index=Hash(key)确定要插入的位置；（Entry=键值对）；当重叠时产生链表，新put的同index的Entry插到老entry的前面：“头插法”。get（）执行index=Hash（key），由于刚说的Hash冲突，同一位置可能匹配多个Entry，将顺着链表头节点往下查。之所以使用“头插法”，是因为HashMap的发明者觉得后插入的Entry被查找的可能性大。HashMap的默认初始长度是16，并且每次自动扩展或是手动初始化时，长度必须是2的幂。之所以选择16，是为了服务于从key映射到index的Hash算法，让它尽量均匀分布。<br>
## 高并发HashMap<br>
Rehash是HashMap扩容（Resize）时的一个步骤，Resize先扩容，创建一个新的Entry数组，长度是原数组的2倍；然后Rehash，遍历原数组，把所有的Entry重新Hash到新数组，为什么要重新Hash呢？因为长度扩大以后，Hash的规则也随之改变index=hashcode（key）&（length-1）。
假设一个HashMap已到了resize的临界值，此时线程A、B同时put可能会产生链表环。<br>
## ConcurrentHashMap:<br>
segment是子HashMap，乐观锁如果失败多次转悲观锁。<br>
## volatile:<br>
synchronizd同步锁虽然可以保证线程安全，但是对程序性能影响太大。volatile保证了被修饰的变量对所有线程的可见性，当一个线程修改了变量的值，新的值会立刻同步到主内存当中，而其他线程读取这个变量的时候，也会从主内存中拉取最新值。volatile的特性源于java语言的先行发生原则（happens-before）；volatile只能保证变量的可见性，并不能保证变量的原子性。在一个变量被volatile修饰后，JVM会在每个该变量的读写操作前后插入内存屏障，字节码无法交换顺序，从而阻止指令重排。<br>
## CAS：<br>
Synchronized属于悲观锁，认为程序中并发情况严重，所以严防死守，CAS属于乐观锁，让线程不断尝试更新。Atomic系列类，Lock系列类，Synchronized转为重量级锁之前，都是采用CAS机制。CAS缺点：如果并发量大，反复尝试更新但不成功，cpu开销大；只是保证一个变量的原子性，代码块、多个变量就只能Synchronized了。自旋。ABA问题：本该失败的进程成功了，小灰从他100元账户取50，银行错误的开了两个进程，本该有个线程因为当前值和预期值不等而失败，但如果两个线程之间小灰他妈给他转了50，那普通CAS会误判，两次取钱进程就都得以完成。所以要增加版本号。<br>
## 重载vs重写:<br>
重载发生在同一个类中，方法名必须相同，参数类型不同、个数不同、顺序不同，方法返回值和访问修饰符可以不同，发生在编译时。重写发生在父子类中，方法名、参数列表必须相同，返回值范围小于等于父类，抛出的异常范围小于等于父类，访问修饰符大于等于父类；如果父类方法访问修饰符为private则子类不能重写该方法。<br>
## String/StringBuffer/StringBuilder<br>
String类中使用final关键字字符数组保存字符串，private final char value[]，所以String对象不可变，而StringBuffer/StringBuilder都继承自AbstractStringBuilder类，没有用final修饰所以对象可变。String对象不可变，可以理解为常量所以线程安全；StringBuffer对方法加了同步锁或对调用方法加了同步锁，所以线程安全；StringBuilder没加锁所以非线程安全。每次对String类型进行改变时，都会生成一个新的String对象，然后将指针指向新String对象。StringBuffer每次都会对对象本身操作，相同情况使用Builder比Buffer只提升10%-15%性能但缺要冒多线程不安全的险。<br>
## ==/equals（）<br>
基本数据类型==比较值；引用数据类型==比较内存地址。equals未覆盖就比地址，一般覆写为比值。<br>
举例：当创建String类型对象时，JVM会在常量池中查找有没有已经存在的值和要创建的值相同的对象，如果有就把它赋给当前引用，如果没有就在常量池中重新创建一个String。String的equals就是被重写为值对比。<br>
## final<br>
final用在三个地方：变量/方法/类。final变量，如果是基本数据类型的变量，则其数值一旦初始化以后不能更改；如果是引用类型变量，则初始化后不能再让其指向另一个对象。final类，表示该类不能被继承，final类中所有成员方法都会隐式地指定为final方法。使用final方法有两种情况：一是把方法锁定以防任何继承类修改；二是效率，早期java 内嵌。类中private方法都隐式final。<br>
## ArrayList/LinkedList/Vector<br>
ArrayList、LinkedList都不是同步的，也就是不保证线程安全。ArrayList底层是Object数组；LinkedList底层是双向链表。数组的插入和删除时间复杂度受元素位置影响。比如插入到末尾O（1）；插入指定位置i就是O（n-i）因为i后的元素都要执行向后移一位的操作；LinkedList插入删除都是O（1）。LinkedList不支持高效的随机元素访问，ArrayList支持：get（int index）。Arraylist会在列表结尾预留一定空间，Linkedlist的空间浪费是每个元素都更多。Vector类所有方法都是同步的，可以由两个线程安全地访问一个Vector对象，但是一个线程访问Vector的话代码要在同步操作上耗费大量时间。Arraylist不是同步，所以在不需要保证线程安全时建议使用。<br>
## HashMap补充<br>
链表散列，jdk1.8后当链表长度大于阀值（默认8）将链表转化为红黑树，以减少搜索时间。hash方法就是扰动函数，是为了防止一些实现较差的hashcode用以减少碰撞。红黑树是为了解决二叉查找树的缺陷，因为二叉查找树在某些情况下会退化成一个线性结构。jdk1.8解决了resize链表死循环问题，jdk1。8后concurrenthashmap取消了segment分段锁采用CAS和synchronized来保证并发安全，数据结构也是数组加链表加红黑二叉树，syschronized只锁定当前链表或者红黑树的首节点，这样只要hash不冲突，就不会产生并发，效率提升n倍。<br>
## Collection<br>
1、List；（1）ArrayList：Object数组；（2）Vector：Object数组；LinkedList：双向链表<br>
2、Set：（1）HashSet：无序、唯一，基于HashMap实现，底层采用HashMap来保存元素；（2）LinkedHashSet：继承自HashSet；（3）TreeSet：有序、唯一，红黑树。<br>
## Map<br>
1.HashMap\LinkedHashMao\HashTable\TreeMap<br>
# Java多线程<br>
## synchronized<br>
解决多个线程之间访问资源的同步性，可以保证被它修饰的方法或者代码块任意时刻只有一个线程执行。早期java版本中synchronized属于重量级锁，1.6以后官方从jvm层面对synchronized较大优化，引入如自旋锁、适应性自旋锁、锁消除、锁粗化、偏向锁、轻量级锁等技术来减少锁的开销。<br>
synchronized修饰实例方法，作用于当前对象实例加锁，进入同步代码前要获得当前对象实例的锁。syn修饰静态方法，作用于当前类对象加锁，进入同步代码前要获得当前类对象的锁；允许线程A调一个实例对象的静态syn方法的同时线程B调用它的非静态syn方法：一个占用类锁一个占用对象锁。syn修饰代码块，指定加锁对象，对给定对象加锁，进入同步代码库前要获得给定对象的锁synchronized（this）。<br>
## 单例<br>
public class Singleton {
    private volatile static Singleton a;
    private Singleton(){}
    public static Singleton getInstance(){
        if (a==null){
            synchronized (Singleton.class){
                if (a==null){
                    a=new Singleton();
                }
            }
        }
        return a;
    }
}
a采用volatile修饰有必要，a=new Singleton（）；实际分三步执行：1、为a分配内存空间。2、初始化a。3、将a指向分配的内存地址。但是由于jvm具有指令重排的特性，执行顺序可能变成1-3-2，单线程没问题，多线程下会导致一个线程获得还没有初始化的实例。例如线程A刚执行完1-3，线程B发现a不为空因此返回a，但a还未被初始化。<br>
 0: getstatic     #2                  // Field a:Lsyn/Singleton;
         3: ifnonnull     37
         6: ldc           #3                  // class syn/Singleton
         8: dup
         9: astore_0
        10: monitorenter
        11: getstatic     #2                  // Field a:Lsyn/Singleton;
        14: ifnonnull     27
        17: new           #3                  // class syn/Singleton
        20: dup
        21: invokespecial #4                  // Method "<init>":()V
        24: putstatic     #2                  // Field a:Lsyn/Singleton;
        27: aload_0
        28: monitorexit
        29: goto          37
        32: astore_1
        33: aload_0
        34: monitorexit
        35: aload_1
        36: athrow
        37: getstatic     #2                  // Field a:Lsyn/Singleton;
        40: areturn
<br>
  synchronized同步语句块的实现使用的是monitorenter和monitorexit指令，其中monitorenter指向同步代码块开始位置，monitorexit指向结束位置，当执行monitorenter时，线程试图获取monitor的持有权（monitor对象存在于每个java对象的对象头中）当计数器为0则可以成功获取，获取后将计数器设为1也就是加1，相应的在执行monitorexit时将计数器设为0，表示锁被释放，如果获取对象锁失败，那当前线程就要阻塞等待，直到锁被另外一个线程释放。synchronized修饰的方法并没有monitor指令，取而代之的是ACC_SYNCHRONIZED标识，该标识指明了该方法是一个同步方法，jvm通过该标识来辨别一个方法是否被生命为同步方法，从而执行相应的同步调用。<br>
  ## synchronized和ReenTrantLock区别<br>
  都是可重入锁：自己可以再次获取自己的内部锁，比如一个线程获得了某个对象的锁，此时这个对象锁还没有释放，当其再次想获取这个对象的锁的时候还是可以获取的，如果不可锁重入的话，就会造成死锁，同一个线程每次获取锁，锁的计数器都自增1，所以要等到锁的计数器下降为0才能释放锁。synchronized基于jvm，很多优化都在jvm层面实现并没有直接暴露。ReenTrantLock是jdk层面实现的也就是api层面，需要lock（）unlock（）配合try/finally语句来完成，可以查看源代码来看它如何实现。<br>
  TODO
  <br>
  ## java内存模型jmm<br>
  线程可以把变量保存本地工作内存比如机器的寄存器中，而不是直接在主存中进行读写，这就可能造成一个线程在主存中修个了一个变量的值而另一个线程还继续使用它的寄存器中的变量值的拷贝，造成数据不一致。要解决这个问题就要把变量声明为volatile，这就指示jvm这个变量不稳定，每次使用要从主存中进行读取。保证变量的内存可见性然后还有一个作用是防止指令重排。
  ## volatile和synchronized区别<br>
  volatile轻量级性能好，只能修饰变量。syn只能方法和代码块。多线程访问volatile不会阻塞。volatile保证数据可见性，但不保证数据的原子性，syn两者都保证。<br>
  ## 线程池<br>
  线程池提供一种限制和资源管理（包括执行一个任务），每个线程池还维护一些基本统计信息，例如已完成任务的数量。降低资源消耗：通过复制利用已创建的线程降低创建和销毁线程的消耗；提高响应速度：当任务到达时，任务可以不需要等到线程创建就能立刻执行；提高线程的可管理性：统一管理分配，调优监控。<br>
 
  
 
