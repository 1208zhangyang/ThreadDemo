# ThreadDemo

使用java的线程模拟进程进行同步和通信,三个线程同时对一个数据进行读写操作，其中A进程按照顺序写入一个整数1-20；并显示发送的数据。其中奇数发送给B进程，偶数发送给C进程。B/C进程分别读取数据，若不是发送给自己的数据则不读取，否则接收并显示数据，同时通知A进程发送另外一个数据

实现代码如下:

```
public class ThreadDemo {
	public static void main(String[] args) {
		Thread t1 = new Thread(new ThreadTest(0), "进程A");
		Thread t2 = new Thread(new ThreadTest(1), "进程B");
		Thread t3 = new Thread(new ThreadTest(2), "进程C");
		t1.start();
		t2.start();
		t3.start();
	}
}

class ThreadTest implements Runnable {
	private static int count = 0;
	private int type = 0;
	private int value = 0;
	private static boolean Thread_A = true;
	private static boolean Thread_B = false;
	private static boolean Thread_C = false;

	public ThreadTest(int type) {
		this.type = type;
	}

	public void run() {
		while (count < 20) {
			try {
				// 休眠50秒
				Thread.sleep(50);
			} catch (InterruptedException e) {
				e.printStackTrace();
			}

			synchronized (this) {
				if (value == 0) {
					value++;
//					System.out.println("type" + type);
//					System.out.println("value is" + value);
//					System.out.println(Thread_C + "   " + type);
					if (Thread_A && type == 0) {
						Write();
					} else if (Thread_B && type == 1) {
						Bread();
					} else if (Thread_C && type == 2) {

						Cread();
					}
					value--;
				}

			}
		}
	}

	public void Write() {
		System.out.println(Thread.currentThread().getName() + ",执行写入" + (count + 1));
		count++;

		if (count % 2 == 0) {
			Thread_B = true;
			Thread_A = false;

		} else {
			Thread_C = true;
			Thread_A = false;

		}
	}

	public void Bread() {
		System.out.println(Thread.currentThread().getName() + ",执行读取" + count);
		Thread_A = true;
		Thread_B = false;
	}

	public void Cread() {
		System.out.println(Thread.currentThread().getName() + ",执行读取" + count);
		Thread_A = true;
		Thread_C = false;
	}
}

```
