1. 什么是⾃旋锁？

   ⾃旋锁是SMP架构中的⼀种low-level的同步机制。
   1、<u>当线程A想要获取⼀把⾃旋锁⽽该锁⼜被其它线程锁持有时，线程A会在⼀个循环中⾃旋以检测锁是不是已经可⽤了。</u>
   2、⾃选锁需要注意：
   由于⾃旋时不释放CPU，因⽽持有⾃旋锁的线程应该尽快释放⾃旋锁，否则等待该⾃旋锁的线程会⼀直在那⾥⾃旋，这就会浪费CPU时间。
   持有⾃旋锁的线程在sleep之前应该释放⾃旋锁以便其它线程可以获得⾃旋锁。
   3、⽬前的JVM实现⾃旋会消耗CPU，如果⻓时间不调⽤doNotify⽅法，doWait⽅法会⼀直⾃旋，CPU会消耗太⼤
   4、<u>⾃旋锁⽐较适⽤于锁使⽤者保持锁时间⽐较短的情况，这种情况⾃旋锁的效率⽐较⾼</u>。
   5、⾃旋锁是⼀种对多处理器相当有效的机制，⽽<u>在单处理器⾮抢占式的系统中基本上没有作⽤</u>。
