
## ThreadLocal 使用
ThreadLocal 提供了线程本地的实例。它与普通变量的区别在于，每个使用该变量的线程都会初始化一个完全独立的实例副本。ThreadLocal 变量通常被private static修饰。当一个线程结束时，它所使用的所有 ThreadLocal 相对的实例副本都可被回收。

### 使用场景是？
One possible (and common) use is when you have some object that is not thread-safe, but you want to avoid synchronizing access to that object.

比较经典的例子是 `SimpleDateFormat` 的使用。

例如：
```java
String dateTime = "2016-12-30 15:35:34";
for (int i = 0; i < 5; i++) {
	new Thread(new Runnable() {
	@Override
	public void run() {
		for (int i = 0; i < 5; i++) {
			try {
				System.out.println(Thread.currentThread().getName() + "\t" + dateFormat.parse(dateTime));
			} catch (ParseException e) {
				e.printStackTrace();
			}
		}
	}
	}).start();
}
```
根据打印结果发现，SimpleDateFormat 是线程不安全的。

使用 ThreadLocal 可解决此问题。
```java
	// SimpleDateFormat is not thread-safe, so give one to each thread
	private static final ThreadLocal<SimpleDateFormat> formatter = new ThreadLocal<SimpleDateFormat>() {
		@Override
		protected SimpleDateFormat initialValue() {
			return new SimpleDateFormat("yyyy-MM-dd HH:mm:ss");
		}
	};

	public static String formatIt(Date date) {
		return formatter.get().format(date);
	}
	
	public static Date parseIt(String date) throws ParseException {
		return formatter.get().parse(date);
	}
  
  String dateTime = "2016-12-30 15:35:34";
		for (int i = 0; i < 5; i++) {
			new Thread(new Runnable() {
				@Override
				public void run() {
					for (int i = 0; i < 5; i++) {
						try {
							System.out.println(Thread.currentThread().getName() + "\t" + parseIt(dateTime));
						} catch (ParseException e) {
							e.printStackTrace();
						}
					}
				}
			}).start();
		}
```

