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
