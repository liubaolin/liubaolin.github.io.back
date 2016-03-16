---
layout: post
title: 03并发相关API(重要)
category: 技术
tags: java并发编程
keywords: java并发编程 线程安全
description: 
---

###一、同步容器类

　　同步容器类包括Vector和HashTable，二者是早期JDK的一部分（后来JDK又出了并发容器类），此外如果其它的容器类想成为同步容器类，可以通过调用Collections.synchronizedXxx接口成为同步容器类。

　　同步容器类，实际上就是通过synchronized来实现的容器，如果有多个线程访问同步容器类，他们会串行执行（同时只有一个线程持有锁）。这些类实现线程安全的方式就是将它们的状态封装起来，并且对每个公有方法都用synchronized进行加锁同步，使得每次只有一个线程能访问容器的状态。

>同步容器类都是线程安全的，但是当有复合操作时（比如同步容器的两个线程安全的方法，先后执行，这两个操作本应是原子操作才能保证线程安全）仍需要通过加锁来保证线程的安全。


###二、并发容器类

　　同步容器的缺点是明显的，它将所有对容器状态的访问都串行化，以实现线程的安全，这严重的降低了并发性，当有多个线程竞争容器的锁时，程序的吞吐量会严重的降低。

　　并发容器类是针对多个线程并发访问设计的，Jdk5.0的concurrent包提供了多种并发容器来来改进同步容器类的性能，如ConcurrentHashMap、CopyOnWriteArrayList等。

>通过并发容器来代替同步容器，可以极大的提高伸缩性并降低风险。

####1、 ConcurrentHashMap

　　在ConcurrentHashMap中使用了一种粒度更细的加锁机制，可称之为分段锁，这种加锁机制，允许任意数量的读线程并发的访问map，并且读操作的线程和写操作的线程也可以并发的访问map，同时允许一定数量的写线程并发的修改map，所以他可以在并发的环境下实现更高的吞吐量。ConcurrentMap接口中增加了对一些常见的符合操作的支持，如“若没有则添加”、替换以及有条件删除等，但是由于并发容器不能通过加锁来独占访问，所以我们无法通过加锁来实现复合操作了。

　　ConcurrentHashMap与其它并发容器一起增强了同步容器：他们提供的迭代器不会抛出ConcurrentModificationException（同步容器类在迭代的时候，如果容器被修改就会报此错，），因此 不需要在迭代的过程中加锁。ConcurrentHashMap返回的迭代器具有弱一致性，弱一致性的迭代器在迭代的过程中允许容器发生修改，当创建迭代器的时候会遍历所有已有的元素，并可以（但是不保证）在迭代器在创建之后将修改的操作反应给容器。

　　ConcurrentHashMap的size和isEmpty等这种对整个map进行计算的方法返回的可能只是一个估值，这种语义被减弱了以反映容器的并发特性。

>ConcurrentHashMap没有实现对map加锁以提供独占访问，与HashTable和synchronizedMap相比，它有更多的优势，只有当程序需要加锁map以实现独占访问时才应该放弃使用ConcurrentHashMap。


####2、CopyOnWriteArrayList

　　CopyOnWriteArrayList用于替代同步List。

　　Copy-On-Write简称COW，CopyOnWrite容器即写时复制的容器。通俗的理解是当我们往一个容器添加元素的时候，不直接往当前容器添加，而是先将当前容器进行Copy，复制出一个新的容器，然后新的容器里添加元素，添加完元素之后，再将原容器的引用指向新的容器。这样做的好处是我们可以对CopyOnWrite容器进行并发的读，而不需要加锁，因为当前容器不会添加任何元素。所以CopyOnWrite容器也是一种读写分离的思想，读和写不同的容器。

　　实际上CopyOnWriteArrayList在写入的时候进行了加锁处理，当有多个写操作的时候，只有一个写操作可以获得锁然后拷贝一份容器的副本进行修改，修改完之后会将原容器的引用指向新的副本容器，然后释放锁，下一个写操作才能获得锁继续操作。

　　可以看出CopyOnWriteArrayList适用于仅当迭代操作远远多于修改操作时，因为写操作需要拷贝底层数组，这需要一定的开销，而且写操作也是加锁同步的。

####3、Queue和BlockingQueue

　　JDK5.0新增了Queue和BlockingQueue容器，她们和List和Set是同一级别的接口。

　　队列是一种非常常用的数据结构，存取数据本着先进先出原则，Java提供queue接口来描述队列，常见的实现类为LinkedList、CurrentLinkedQueue、及PriorityQueue等。CurrentLinkedQueue，是一个传统的先进先出的队列，PriorityQueue是一个（非并发的）优先队列。Queu上的操作不会阻塞，如果队列为空那么获取元素的操作就将返回空值。

	 	boolean offer<E e>  向队列末尾追加元素，追加成功放回true
	
	    E poll(): 从队首获取元素。
	                注意：获取后，队首元素就从队列中删除了。
	
	    E peek():获取队首元素，但该元素不会从队列中删除。
	           Queue<String> queue = new LinkedList<String>();
	     /**
	     * 向队列中添加元素
	     */
	    queue.offer( "A");
	    queue.offer( "B");
	    queue.offer( "C");
	    
	    System. out.println(queue);
	     //输出队首元素
	     //peek()方法获取队首元素，但不会将其从队列中删除
	    System. out.println(queue.peek());
	    System. out.println(queue);
	     //poll()方法获取队首元素，但是会将其从队列中删除。
	      遍历队列
	    
	     /**
	     * 遍历队列是一次性的      取出来队列就为空了
	     */
	    String element;
	     while ((element = queue.poll()) != null){
	        System. out.println(element);
	    }
	    System. out.println(queue);//对列为空了


　　BlockingQueue扩展了Queue（BlockingQueu是concurrent包的），增加了可阻塞的插入和获取操作，如果队列为空，那么获取元素的操作将一直阻塞，直到队列中出现一个可用的元素。如果队列已满（对于有界队列来说），那么插入操作将一直阻塞，直到队列中出现可用的空间。（在生产者-消费者模式中队列非常有用）

　　阻塞队列提供了可阻塞的put和take方法，如果队列已满那么put方法将阻塞，直到有空间可用，如果队列为空那么take方法将阻塞，直到有元素可用。

>java.util.concurrent包中实现的各种阻塞队列都包含了足够的内部同步机制，从而安全的将对象从生产者线程发布到消费者线程。

####4、Deque和BlockingDeque

　　jdk6增加了两种容器类型Deque和BlockingDeque，它们分别对Queue和BlockingQueue进行了扩展。

　　Deque是一个双端队列，实现了在队列头和队列尾的高效插入和移除，具体的实现包括ArrayDeque和LinkedBlockingDeque。

　　正如阻塞队列对应了“生产者-消费者”模式，双端队列对应“工作密取”模式。在生产者消费者模式中，所有的消费者共享一个队列，而在工作密取模式中，每个消费者都有自己的双端队列。如果一个消费者完成了自己双端队列中的全部内容，那么它可以从其它双端队列的末尾秘密的获取工作。密取模式比生产者消费者模式具有更高的可伸缩性，这是因为消费者线程不会在单个的队列上发生竞争，他们大多数情况下都是在访问自己的双端队列，而且如果一个消费者线程要访问其它的队列时，它会从队列的尾部而不是头部去获取工作，这进一步降低了竞争性。

　　工作密取模式，非常适合既是生产者有是消费者的问题，当一个工作线程找到新的任务时，它可以将任务放在自己队列的末尾，当双端队列为空时，它也可以从别的队列尾部获取任务，从而保证每个线程都处于忙碌的状态。

###三、线程的阻塞和中断

　　线程可能会被阻塞或者暂停执行，原因有很多种，比如等待I/O操作完成，等待获得锁，等待从Thread.sleep()中醒来等等。线程阻塞与执行时间很长的普通操作的区别在于，被阻塞的线程必须等待某个不被它控制的时间发生后（状态被置会RUNNABLE状态）才可以继续执行。

　　每个阻塞方法都会抛出一个受检查异常（Check Eception）InterruptedException（比如Thread.sleep()），这是为了及时中断阻塞线程，每个线程都会有一个boolean类型的属性，用于表示线程的中断状态，jvm会及时的去检查这个状态，当发现中断状态为true的时候，非阻塞的线程来说就会被被及时的中断，而对于阻塞的线程则会在阻塞方法的调用处抛出InterruptException（所以每个阻塞方法都会抛出该受检查异常），并且在抛出异常后立即将中断状态改为false。抛出异常是为了线程从阻塞状态醒过来（状态变成Runable），并在结束线程前让程序员有足够的时间来处理中断请求。

　　处理InterruptException的方式有两种：

* 继续往外抛出该异常，我们应该将该异常抛给方法的调用者，只有他最知道该怎么处理。
* 也有些时候不应该往外抛，比如当异常所在的代码就是Runnable的一部分的时候，就应该在这里明确的处理该异常。

>特别注意的是，当抛出InterruptException后，线程的中断状态会立即被重置为false，但是线程已不再是阻塞状态，方便我们对其进行进一步的处理，比如继续将其中断状态改为true（将一个非阻塞线程的中断状态改为true，jvm会及时发现并中断线程）。

	线程的thread.interrupt()方法用于将中断状态改为true。
	thread.interrupted()方法会将中断状态改为false；
	Thread.currentThread().isInterrupted()方法，用于判断某个线程是否已被中断

>注:synchronized在获锁的过程中是不能被中断的，意思是说如果产生了死锁，则不可能被中断（请参考后面的测试例子）。与synchronized功能相似的reentrantLock.lock()方法也是一样，它也不可中断的，即如果发生死锁，那么reentrantLock.lock()方法无法终止，如果调用时被阻塞，则它一直阻塞到它获取到锁为止。但是如果调用带超时的tryLock方法reentrantLock.tryLock(long timeout, TimeUnit unit)，那么如果线程在等待时被中断，将抛出一个InterruptedException异常，这是一个非常有用的特性，因为它允许程序打破死锁。你也可以调用reentrantLock.lockInterruptibly()方法，它就相当于一个超时设为无限的tryLock方法。

**下面是一些DEMO**

	中断应用
	使用中断信号量中断非阻塞状态的线程
	中断线程最好的，最受推荐的方式是，使用共享变量（shared variable）发出信号，告诉线程必须停止正在运行的任务。线程必须周期性的核查这一变量，然后有秩序地中止任务
	 
	package com.ljq.test;
	
	public class ThreadTest extends Thread{
	    //线程中断信号量
	    volatile boolean stop=false; 
	    
	    public static void main(String[] args) throws Exception {
	        ThreadTest thread=new ThreadTest();
	        System.out.println("Starting thread...");   
	        thread.start();   
	        Thread.sleep(3000);   
	        System.out.println("Asking thread to stop...");   
	        // 设置中断信号量   
	        thread.stop = true;   
	        Thread.sleep(3000);   
	        System.out.println("Stopping application...");  
	    }
	    
	    
	    @Override
	    public void run() {
	        //每隔一秒检测一下中断信号量
	        while(!stop){
	            System.out.println("Thread is running!");
	            long begin=System.currentTimeMillis();
	            /**
	             * 使用while循环模拟sleep方法，这里不要使用sleep，否则在阻塞时会抛InterruptedException异常而退出循环，
	             * 这样while检测stop条件就不会执行，失去了意义。 
	             */
	            while ((System.currentTimeMillis() - begin < 1000)) {
	                
	            }   
	        }
	        System.out.println("Thread exiting under request!");   
	    }
	}
	 
	运行结果如下：
	    
![16031601](/public/img/tec/concurrent-api01.png)

         
	使用thread.interrupt()中断非阻塞状态线程
	虽然上面案例要求一些编码，但并不难实现。同时，它给予线程机会进行必要的清理工作。这里需注意一点的是需将共享变量定义成volatile 类型或将对它的一切访问封入同步的块/方法（synchronized blocks/methods）中。上面是中断一个非阻塞状态的线程的常见做法，但对非检测isInterrupted()条件会更简洁:
	 
	package com.ljq.test;
	
	public class ThreadTest extends Thread{
	    
	    public static void main(String[] args) throws Exception {
	        ThreadTest thread=new ThreadTest();
	        System.out.println("Starting thread...");   
	        thread.start();   
	        Thread.sleep(3000);   
	        System.out.println("Asking thread to stop...");   
	        // 发出中断请求 
	        thread.interrupt(); 
	        Thread.sleep(3000);   
	        System.out.println("Stopping application...");  
	    }
	    
	    
	    @Override
	    public void run() {
	        //每隔一秒检测一下中断信号量
	        while(!Thread.currentThread().isInterrupted()){
	            System.out.println("Thread is running!");
	            long begin=System.currentTimeMillis();
	            /**
	             * 使用while循环模拟sleep方法，这里不要使用sleep，否则在阻塞时会抛InterruptedException异常而退出循环，
	             * 这样while检测stop条件就不会执行，失去了意义。 
	             */
	            while ((System.currentTimeMillis() - begin < 1000)) {
	                
	            }   
	        }
	        System.out.println("Thread exiting under request!");   
	    }
	}
	 
	    
	到目前为止一切顺利！但是，当线程等待某些事件发生而被阻塞，又会发生什么？当然，如果线程被阻塞，它便不能核查共享变量，也就不能停止。这在许多情况下会发生，例如调用Object.wait()、ServerSocket.accept()和DatagramSocket.receive()时，这里仅举出一些。
	他们都可能永久的阻塞线程。即使发生超时，在超时期满之前持续等待也是不可行和不适当的，所以，要使用某种机制使得线程更早地退出被阻塞的状态。下面就来看一下中断阻塞线程技术。
	使用thread.interrupt()中断阻塞状态线程
	Thread.interrupt()方法不会中断一个正在运行的线程。这一方法实际上完成的是，设置线程的中断标示位，在线程受到阻塞的地方（如调用sleep、wait、join等地方）抛出一个异常InterruptedException，并且中断状态也将被清除，这样线程就得以退出阻塞的状态。下面是具体实现：
	  
	 
	package com.ljq.test;
	
	public class ThreadTest extends Thread{
	    
	    public static void main(String[] args) throws Exception {
	        ThreadTest thread=new ThreadTest();
	        System.out.println("Starting thread...");   
	        thread.start();   
	        Thread.sleep(3000);   
	        thread.interrupt();// 等中断信号量设置后再调用 
	        System.out.println("Asking thread to stop...");   
	        Thread.sleep(3000);   
	        System.out.println("Stopping application...");  
	    }
	    
	    
	    @Override
	    public void run() {
	        while(!Thread.currentThread().isInterrupted()){
	            System.out.println("Thread running...");   
	            try {
	                /**
	                 * 如果线程阻塞，将不会去检查中断信号量stop变量，所以thread.interrupt()  
	                 * 会使阻塞线程从阻塞的地方抛出异常，让阻塞线程从阻塞状态逃离出来，并进行异常块进行相应的处理
	                 */
	                Thread.sleep(1000); //线程阻塞，如果线程收到中断操作信号将抛出异常
	            } catch (InterruptedException e) {
	                System.out.println("Thread interrupted...");   
	                /**  
	                 * 如果线程在调用Object.wait()方法，或者该类的join()、sleep()方法  
	                 * 过程中受阻，则其中断状态将被清除  
	                 */  
	                System.out.println(this.isInterrupted());//false   
	                //中不中断由自己决定，如果需要中断线程，则需要重新设置中断位，如果不需要，则不用调用   
	                Thread.currentThread().interrupt();  
	                e.printStackTrace();
	            }
	        }
	        System.out.println("Thread exiting under request...");   
	    }
	}
	 
	上面案例中一旦Thread.interrupt()被调用，线程便收到一个异常，于是逃离了阻塞状态并确定应该停止。上面我们还可以使用共享信号量来替换!Thread.currentThread().isInterrupted()条件，但不如它简洁.
	               
	死锁状态线程无法被中断
	下面案例中试着去中断处于死锁状态的两个线程，但这两个线都没有收到任何中断信号（抛出异常），所以interrupt()方法是不能中断死锁线程的，因为锁定的位置根本无法抛出异常
	 
	package com.ljq.test;
	
	public class ThreadTest extends Thread {
	    public static void main(String args[]) throws Exception {
	        final Object lock1 = new Object();
	        final Object lock2 = new Object();
	        Thread thread1 = new Thread() {
	            public void run() {
	                deathLock(lock1, lock2);
	            }
	        };
	        Thread thread2 = new Thread() {
	            public void run() {
	                // 注意，这里在交换了一下位置
	                deathLock(lock2, lock1);
	            }
	        };
	        System.out.println("Starting thread...");
	        thread1.start();
	        thread2.start();
	        Thread.sleep(3000);
	        System.out.println("Interrupting thread...");
	        thread1.interrupt();
	        thread2.interrupt();
	        Thread.sleep(3000);
	        System.out.println("Stopping application...");
	    }
	
	    private static void deathLock(Object lock1, Object lock2) {
	        try {
	            synchronized (lock1) {
	                Thread.sleep(10);// 不会在这里死掉
	                synchronized (lock2) {// 会锁在这里，虽然阻塞了，但不会抛异常
	                    System.out.println(Thread.currentThread());
	                }
	            }
	        } catch (InterruptedException e) {
	            e.printStackTrace();
	            System.exit(1);
	        }
	    }
	}
	 
	     
	中断I/O操作
	然而，如果线程在I/O操作进行时被阻塞，又会如何？I/O操作可以阻塞线程一段相当长的时间，特别是牵扯到网络应用时。例如，服务器可能需要等待一个请求（request），又或者，一个网络应用程序可能要等待远端主机的响应。
	                 
	实现此InterruptibleChannel接口的通道是可中断的：如果某个线程在可中断通道上因调用某个阻塞的 I/O 操作（常见的操作一般有这些：serverSocketChannel. accept()、socketChannel.connect、socketChannel.open、socketChannel.read、socketChannel.write、fileChannel.read、fileChannel.write）而进入阻塞状态，而另一个线程又调用了该阻塞线程的 interrupt 方法，这将导致该通道被关闭，并且已阻塞线程接将会收到ClosedByInterruptException，并且设置已阻塞线程的中断状态。另外，如果已设置某个线程的中断状态并且它在通道上调用某个阻塞的 I/O 操作，则该通道将关闭并且该线程立即接收到 ClosedByInterruptException；并仍然设置其中断状态。如果情况是这样，其代码的逻辑和第三个例子中的是一样的，只是异常不同而已。
	                  
	如果你正使用通道（channels）（这是在Java 1.4中引入的新的I/O API），那么被阻塞的线程将收到一个ClosedByInterruptException异常。但是，你可能正使用Java1.0之前就存在的传统的I/O，而且要求更多的工作。既然这样，Thread.interrupt()将不起作用，因为线程将不会退出被阻塞状态。尽管interrupt()被调用，线程也不会退出被阻塞状态，比如ServerSocket的accept方法根本不抛出异常。
	                  
	很幸运，Java平台为这种情形提供了一项解决方案，即调用阻塞该线程的套接字的close()方法。在这种情形下，如果线程被I/O操作阻塞，当调用该套接字的close方法时，该线程在调用accept地方法将接收到一个SocketException（SocketException为IOException的子异常）异常，这与使用interrupt()方法引起一个InterruptedException异常被抛出非常相似，（注，如果是流因读写阻塞后，调用流的close方法也会被阻塞，根本不能调用，更不会抛IOExcepiton，此种情况下怎样中断？我想可以转换为通道来操作流可以解决，比如文件通道）。下面是具体实现：
	 
	package com.ljq.test;
	
	import java.io.IOException;
	import java.net.ServerSocket;
	
	public class ThreadTest extends Thread {
	    volatile ServerSocket socket;
	
	    public static void main(String args[]) throws Exception {
	        ThreadTest thread = new ThreadTest();
	        System.out.println("Starting thread...");
	        thread.start();
	        Thread.sleep(3000);
	        System.out.println("Asking thread to stop...");
	        Thread.currentThread().interrupt();// 再调用interrupt方法
	        thread.socket.close();// 再调用close方法，此句去掉将发生阻塞状态
	        try {
	            Thread.sleep(3000);
	        } catch (InterruptedException e) {
	        }
	        System.out.println("Stopping application...");
	    }
	
	    public void run() {
	        try {
	            socket = new ServerSocket(3036);
	        } catch (IOException e) {
	            System.out.println("Could not create the socket...");
	            return;
	        }
	        while (!Thread.currentThread().isInterrupted()) {
	            System.out.println("Waiting for connection...");
	            try {
	                socket.accept();
	            } catch (IOException e) {
	                System.out.println("accept() failed or interrupted...");
	                Thread.currentThread().interrupt();// 重新设置中断标示位
	            }
	        }
	        //判断线程是否被阻塞，如果被阻塞则无法打印此句
	        System.out.println("Thread exiting under request...");
	    }
	}
	
