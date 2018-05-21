

### Thread.yield()
``` 
该静态方法表明当前线程愿意让出当前占用的处理器，从而变成就绪状态，
然后同就绪队列中的其他线程一同竞争处理器。
```
* 当一个线程调用了`yield`方法后，并不会释放当前线程中对象所持有的对象锁，在同步
的过程中，容易导致死锁

**测试代码：**
```
// Buffer.java
public interface Buffer {
    void insert(Object item) throws InterruptedException;

    Object remove() throws InterruptedException;
}

//Producer.java
import java.util.Date;

public class Producer implements Runnable {

    Buffer buffer;

    public Producer(Buffer br){
        buffer = br;
    }

    public void run() {
        Date msg;
        while(true) {
            //SleepUtilities.nap();
            msg = new Date();
            try {
                System.out.println("produce: " + msg);
                buffer.insert(msg);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }

        }
    }
}

//Comsumer.java
import java.util.Date;

public class Comsumer implements Runnable {

    private Buffer buffer;

    public Comsumer(Buffer br) {
        buffer = br;
    }

    public void run() {
        Date msg;

        while(true) {
            SleepUtilities.nap();
            try {
                msg = (Date) buffer.remove();
                System.out.println("comsumer: " + msg);

            } catch (InterruptedException e) {
                e.printStackTrace();
            }

        }
    }
}


//Factory.java
public class Factory {
  
      public static void main(String[] args) {
          Buffer buffer = new BoundedBuffer();
          Thread producer = new Thread(new Producer(buffer));
          Thread comsumer = new Thread(new Comsumer(buffer));
          producer.start();
  
          comsumer.start();
      }
  }
在消费者进程中，消费之前先让生产者填满缓冲器，并在生产中进程中调用yield方法，这样由于
生产者一直阻塞在等待消费者释放缓冲区，并且生产者不释放BoundedBuffer对象的对象锁，导致
死锁产生。

```