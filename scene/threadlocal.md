
ThreadLocal 使用
One possible (and common) use is when you have some object that is not thread-safe, but you want to avoid synchronizing access to that object
```java
SimpleDateFormat dateFormat = new SimpleDateFormat("yyyy-MM-dd HH:mm:ss");
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

