2019-02-27T09:51:55.935+0800: [GC (Allocation Failure) 2019-02-27T09:51:55.982+0800: [ParNew: 7799K->896K(9216K), 0.0086485 secs] 7799K->6018K(39936K), 0.0486063 secs][Times: user=0.00 sys=0.00, real=0.05 secs] 
2019-02-27T09:51:55.982+0800: [GC (Allocation Failure) 2019-02-27T09:51:55.982+0800: [ParNew: 8221K->77K(9216K), 0.0048848 secs] 13343K->13154K(39936K), 0.0049206 secs] [Times: user=0.03 sys=0.01, real=0.02 secs] 
2019-02-27T09:51:55.998+0800: [GC (Allocation Failure) 2019-02-27T09:51:55.998+0800: [ParNew: 7396K->19K(9216K), 0.0048546 secs] 20473K->20264K(39936K), 0.0048893 secs] [Times: user=0.00 sys=0.00, real=0.00 secs] 
2019-02-27T09:51:55.998+0800: [GC (CMS Initial Mark) [1 CMS-initial-mark: 20245K(30720K)] 22467K(39936K), 0.0001586 secs] [Times: user=0.00 sys=0.00, real=0.00 secs] 
2019-02-27T09:51:55.998+0800: [CMS-concurrent-mark-start]
2019-02-27T09:51:55.998+0800: [CMS-concurrent-mark: 0.001/0.001 secs] [Times: user=0.00 sys=0.00, real=0.00 secs] 
2019-02-27T09:51:55.998+0800: [CMS-concurrent-preclean-start]
2019-02-27T09:51:55.998+0800: [CMS-concurrent-preclean: 0.000/0.000 secs] [Times: user=0.00 sys=0.00, real=0.00 secs] 
2019-02-27T09:51:55.998+0800: [CMS-concurrent-abortable-preclean-start]
Heap
 par new generation   total 9216K, used 2385K [0x00000000fd800000, 0x00000000fe200000, 0x00000000fe200000)
  eden space 8192K,  28% used [0x00000000fd800000, 0x00000000fda4fa98, 0x00000000fe000000)
  from space 1024K,   1% used [0x00000000fe100000, 0x00000000fe104d10, 0x00000000fe200000)
  to   space 1024K,   0% used [0x00000000fe000000, 0x00000000fe000000, 0x00000000fe100000)
 concurrent mark-sweep generation total 30720K, used 20245K [0x00000000fe200000, 0x0000000100000000, 0x0000000100000000)
 Metaspace       used 3505K, capacity 4496K, committed 4864K, reserved 1056768K
  class space    used 386K, capacity 388K, committed 512K, reserved 1048576K







{Heap before GC invocations=0 (full 0):
 par new generation   total 9216K, used 7799K [0x00000000fd800000, 0x00000000fe200000, 0x00000000fe200000)
  eden space 8192K,  95% used [0x00000000fd800000, 0x00000000fdf9df78, 0x00000000fe000000)
  from space 1024K,   0% used [0x00000000fe000000, 0x00000000fe000000, 0x00000000fe100000)
  to   space 1024K,   0% used [0x00000000fe100000, 0x00000000fe100000, 0x00000000fe200000)
 concurrent mark-sweep generation total 30720K, used 0K [0x00000000fe200000, 0x0000000100000000, 0x0000000100000000)
 Metaspace       used 3498K, capacity 4494K, committed 4864K, reserved 1056768K
  class space    used 385K, capacity 386K, committed 512K, reserved 1048576K
2019-02-27T09:58:51.478+0800: [GC (Allocation Failure) 2019-02-27T09:58:51.478+0800: [ParNew: 7799K->899K(9216K), 0.0028387 secs] 7799K->6021K(39936K), 0.0028896 secs][Times: user=0.00 sys=0.00, real=0.00 secs] 
Heap after GC invocations=1 (full 0):
 par new generation   total 9216K, used 899K [0x00000000fd800000, 0x00000000fe200000, 0x00000000fe200000)
  eden space 8192K,   0% used [0x00000000fd800000, 0x00000000fd800000, 0x00000000fe000000)
  from space 1024K,  87% used [0x00000000fe100000, 0x00000000fe1e0c90, 0x00000000fe200000)
  to   space 1024K,   0% used [0x00000000fe000000, 0x00000000fe000000, 0x00000000fe100000)
 concurrent mark-sweep generation total 30720K, used 5122K [0x00000000fe200000, 0x0000000100000000, 0x0000000100000000)
 Metaspace       used 3498K, capacity 4494K, committed 4864K, reserved 1056768K
  class space    used 385K, capacity 386K, committed 512K, reserved 1048576K
}
{Heap before GC invocations=1 (full 0):
 par new generation   total 9216K, used 8224K [0x00000000fd800000, 0x00000000fe200000, 0x00000000fe200000)
  eden space 8192K,  89% used [0x00000000fd800000, 0x00000000fdf273c0, 0x00000000fe000000)
  from space 1024K,  87% used [0x00000000fe100000, 0x00000000fe1e0c90, 0x00000000fe200000)
  to   space 1024K,   0% used [0x00000000fe000000, 0x00000000fe000000, 0x00000000fe100000)
 concurrent mark-sweep generation total 30720K, used 5122K [0x00000000fe200000, 0x0000000100000000, 0x0000000100000000)
 Metaspace       used 3498K, capacity 4494K, committed 4864K, reserved 1056768K
  class space    used 385K, capacity 386K, committed 512K, reserved 1048576K
2019-02-27T09:58:51.478+0800: [GC (Allocation Failure) 2019-02-27T09:58:51.478+0800: [ParNew: 8224K->77K(9216K), 0.0045993 secs] 13346K->13155K(39936K), 0.0046371 secs] [Times: user=0.00 sys=0.00, real=0.00 secs] 
Heap after GC invocations=2 (full 0):
 par new generation   total 9216K, used 77K [0x00000000fd800000, 0x00000000fe200000, 0x00000000fe200000)
  eden space 8192K,   0% used [0x00000000fd800000, 0x00000000fd800000, 0x00000000fe000000)
  from space 1024K,   7% used [0x00000000fe000000, 0x00000000fe013710, 0x00000000fe100000)
  to   space 1024K,   0% used [0x00000000fe100000, 0x00000000fe100000, 0x00000000fe200000)
 concurrent mark-sweep generation total 30720K, used 13077K [0x00000000fe200000, 0x0000000100000000, 0x0000000100000000)
 Metaspace       used 3498K, capacity 4494K, committed 4864K, reserved 1056768K
  class space    used 385K, capacity 386K, committed 512K, reserved 1048576K
}
{Heap before GC invocations=2 (full 0):
 par new generation   total 9216K, used 7397K [0x00000000fd800000, 0x00000000fe200000, 0x00000000fe200000)
  eden space 8192K,  89% used [0x00000000fd800000, 0x00000000fdf25d10, 0x00000000fe000000)
  from space 1024K,   7% used [0x00000000fe000000, 0x00000000fe013710, 0x00000000fe100000)
  to   space 1024K,   0% used [0x00000000fe100000, 0x00000000fe100000, 0x00000000fe200000)
 concurrent mark-sweep generation total 30720K, used 13077K [0x00000000fe200000, 0x0000000100000000, 0x0000000100000000)
 Metaspace       used 3498K, capacity 4494K, committed 4864K, reserved 1056768K
  class space    used 385K, capacity 386K, committed 512K, reserved 1048576K
2019-02-27T09:58:51.478+0800: [GC (Allocation Failure) 2019-02-27T09:58:51.478+0800: [ParNew: 7397K->19K(9216K), 0.0038838 secs] 20474K->20265K(39936K), 0.0039114 secs] [Times: user=0.09 sys=0.00, real=0.02 secs] 
Heap after GC invocations=3 (full 0):
 par new generation   total 9216K, used 19K [0x00000000fd800000, 0x00000000fe200000, 0x00000000fe200000)
  eden space 8192K,   0% used [0x00000000fd800000, 0x00000000fd800000, 0x00000000fe000000)
  from space 1024K,   1% used [0x00000000fe100000, 0x00000000fe104dc0, 0x00000000fe200000)
  to   space 1024K,   0% used [0x00000000fe000000, 0x00000000fe000000, 0x00000000fe100000)
 concurrent mark-sweep generation total 30720K, used 20245K [0x00000000fe200000, 0x0000000100000000, 0x0000000100000000)
 Metaspace       used 3498K, capacity 4494K, committed 4864K, reserved 1056768K
  class space    used 385K, capacity 386K, committed 512K, reserved 1048576K
}
2019-02-27T09:58:51.494+0800: [GC (CMS Initial Mark) [1 CMS-initial-mark: 20245K(30720K)] 22467K(39936K), 0.0003066 secs] [Times: user=0.00 sys=0.00, real=0.00 secs] 
2019-02-27T09:58:51.494+0800: [CMS-concurrent-mark-start]
2019-02-27T09:58:51.494+0800: [CMS-concurrent-mark: 0.001/0.001 secs] [Times: user=0.00 sys=0.00, real=0.00 secs] 
2019-02-27T09:58:51.494+0800: [CMS-concurrent-preclean-start]
2019-02-27T09:58:51.494+0800: [CMS-concurrent-preclean: 0.000/0.000 secs] [Times: user=0.00 sys=0.00, real=0.00 secs] 
2019-02-27T09:58:51.494+0800: [CMS-concurrent-abortable-preclean-start]
Heap
 par new generation   total 9216K, used 2386K [0x00000000fd800000, 0x00000000fe200000, 0x00000000fe200000)
  eden space 8192K,  28% used [0x00000000fd800000, 0x00000000fda4fa98, 0x00000000fe000000)
  from space 1024K,   1% used [0x00000000fe100000, 0x00000000fe104dc0, 0x00000000fe200000)
  to   space 1024K,   0% used [0x00000000fe000000, 0x00000000fe000000, 0x00000000fe100000)
 concurrent mark-sweep generation total 30720K, used 20245K [0x00000000fe200000, 0x0000000100000000, 0x0000000100000000)
 Metaspace       used 3505K, capacity 4496K, committed 4864K, reserved 1056768K
  class space    used 386K, capacity 388K, committed 512K, reserved 1048576K





2019-02-27T11:01:32.375+0800: [GC (Allocation Failure) 2019-02-27T11:01:32.375+0800: [ParNew: 7799K->899K(9216K), 0.0009092 secs] 7799K->899K(19456K), 0.0009609 secs][Times: user=0.00 sys=0.00, real=0.00 secs] 
2019-02-27T11:01:32.375+0800: [GC (Allocation Failure) 2019-02-27T11:01:32.375+0800: [ParNew: 8224K->0K(9216K), 0.0024248 secs] 8224K->788K(19456K), 0.0024452 secs] [Times: user=0.00 sys=0.00, real=0.00 secs] 
2019-02-27T11:01:34.391+0800: [GC (CMS Initial Mark) [1 CMS-initial-mark: 6932K(10240K)] 11180K(19456K), 0.0005739 secs] [Times: user=0.00 sys=0.00, real=0.00 secs] 
2019-02-27T11:01:34.391+0800: [CMS-concurrent-mark-start]
2019-02-27T11:01:34.391+0800: [CMS-concurrent-mark: 0.002/0.002 secs] [Times: user=0.00 sys=0.00, real=0.00 secs] 
2019-02-27T11:01:34.391+0800: [CMS-concurrent-preclean-start]
2019-02-27T11:01:34.391+0800: [CMS-concurrent-preclean: 0.000/0.000 secs] [Times: user=0.00 sys=0.00, real=0.00 secs] 
2019-02-27T11:01:34.391+0800: [CMS-concurrent-abortable-preclean-start]
 CMS: abort preclean due to time 2019-02-27T11:01:39.482+0800: [CMS-concurrent-abortable-preclean: 0.008/5.088 secs] [Times: user=0.00 sys=0.00, real=5.09 secs] 
2019-02-27T11:01:39.482+0800: [GC (CMS Final Remark) [YG occupancy: 4247 K (9216 K)]2019-02-27T11:01:39.482+0800: [Rescan (parallel) , 0.0004501 secs]2019-02-27T11:01:39.482+0800: [weak refs processing, 0.0000695 secs]2019-02-27T11:01:39.482+0800: [class unloading, 0.0005075 secs]2019-02-27T11:01:39.482+0800: [scrub symbol table, 0.0009436 secs]2019-02-27T11:01:39.482+0800: [scrub string table, 0.0003089 secs][1 CMS-remark: 6932K(10240K)] 11180K(19456K), 0.0024679 secs] [Times: user=0.00 sys=0.00, real=0.00 secs] 
2019-02-27T11:01:39.482+0800: [CMS-concurrent-sweep-start]
2019-02-27T11:01:39.482+0800: [CMS-concurrent-sweep: 0.001/0.001 secs] [Times: user=0.00 sys=0.00, real=0.00 secs] 
2019-02-27T11:01:39.482+0800: [CMS-concurrent-reset-start]
2019-02-27T11:01:39.482+0800: [CMS-concurrent-reset: 0.000/0.000 secs] [Times: user=0.00 sys=0.00, real=0.00 secs] 
2019-02-27T11:01:43.498+0800: [GC (CMS Initial Mark) [1 CMS-initial-mark: 6931K(10240K)] 11342K(19456K), 0.0008394 secs] [Times: user=0.00 sys=0.00, real=0.00 secs] 
2019-02-27T11:01:43.498+0800: [CMS-concurrent-mark-start]
2019-02-27T11:01:43.498+0800: [CMS-concurrent-mark: 0.001/0.001 secs] [Times: user=0.00 sys=0.00, real=0.00 secs] 
2019-02-27T11:01:43.498+0800: [CMS-concurrent-preclean-start]
2019-02-27T11:01:43.498+0800: [CMS-concurrent-preclean: 0.000/0.000 secs] [Times: user=0.00 sys=0.00, real=0.00 secs] 
2019-02-27T11:01:43.498+0800: [CMS-concurrent-abortable-preclean-start]
 CMS: abort preclean due to time 2019-02-27T11:01:48.531+0800: [CMS-concurrent-abortable-preclean: 0.007/5.031 secs] [Times: user=0.00 sys=0.00, real=5.03 secs] 
2019-02-27T11:01:48.531+0800: [GC (CMS Final Remark) [YG occupancy: 4411 K (9216 K)]2019-02-27T11:01:48.531+0800: [Rescan (parallel) , 0.0006668 secs]2019-02-27T11:01:48.531+0800: [weak refs processing, 0.0000340 secs]2019-02-27T11:01:48.531+0800: [class unloading, 0.0009689 secs]2019-02-27T11:01:48.531+0800: [scrub symbol table, 0.0010100 secs]2019-02-27T11:01:48.531+0800: [scrub string table, 0.0003296 secs][1 CMS-remark: 6931K(10240K)] 11342K(19456K), 0.0031573 secs] [Times: user=0.00 sys=0.00, real=0.00 secs] 
2019-02-27T11:01:48.531+0800: [CMS-concurrent-sweep-start]
2019-02-27T11:01:48.531+0800: [CMS-concurrent-sweep: 0.001/0.001 secs] [Times: user=0.00 sys=0.00, real=0.00 secs] 
2019-02-27T11:01:48.531+0800: [CMS-concurrent-reset-start]
2019-02-27T11:01:48.531+0800: [CMS-concurrent-reset: 0.000/0.000 secs] [Times: user=0.00 sys=0.00, real=0.00 secs] 
Heap
 par new generation   total 9216K, used 4493K [0x00000000fec00000, 0x00000000ff600000, 0x00000000ff600000)
  eden space 8192K,  54% used [0x00000000fec00000, 0x00000000ff063570, 0x00000000ff400000)
  from space 1024K,   0% used [0x00000000ff400000, 0x00000000ff400000, 0x00000000ff500000)
  to   space 1024K,   0% used [0x00000000ff500000, 0x00000000ff500000, 0x00000000ff600000)
 concurrent mark-sweep generation total 10240K, used 787K [0x00000000ff600000, 0x0000000100000000, 0x0000000100000000)
 Metaspace       used 3505K, capacity 4496K, committed 4864K, reserved 1056768K
  class space    used 386K, capacity 388K, committed 512K, reserved 1048576K

