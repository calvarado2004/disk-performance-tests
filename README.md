# disk-performance-tests

If you want to get the baseline performance of your Portworx cluster installed on Kubernetes or OpenShift you can perform the following: 

```
kubectl create ns perf 

kubectl create -f storage-class.yaml 

kubectl create -f pvc.yaml -n perf

kubectl create -f configmap.yaml -n perf 

kubectl create -f fio-pod.yaml -n perf

kubectl exec -it -n perf fio-repl1 -- /usr/local/bin/fio --blocksize=64k --filename=/data/fio.dat --ioengine=libaio --readwrite=read --size=10G --name=test --direct=1 --iodepth=128 --end_fsync=1 /fiocfg/fiojobs.fio

kubectl exec -it -n perf fio-repl1 -- rm -rf /data/fio.dat
```
Perform that test like 10 times to get a good average of your IOPS

For example, on a EKS cluster with a GP3 disks Portworx 2.8 cluster:
 
```
test: (g=0): rw=read, bs=64.0KiB-64.0KiB,64.0KiB-64.0KiB,64.0KiB-64.0KiB, ioengine=libaio, iodepth=128
fio-2.17-45-g06cb
Starting 1 process
test: Laying out IO file(s) (1 file(s) / 10240MiB)
Jobs: 1 (f=1): [R(1)][94.9%][r=1512MiB/s,w=0KiB/s][r=24.2k,w=0 IOPS][eta 00m:02s]
test: (groupid=0, jobs=1): err= 0: pid=70: Fri Aug 27 16:20:05 2021
   read: IOPS=4496, BW=281MiB/s (295MB/s)(10.0GiB/36441msec)
    slat (usec): min=4, max=11063, avg=10.11, stdev=57.01
    clat (usec): min=877, max=167939, avg=28456.20, stdev=29575.19
     lat (usec): min=885, max=167948, avg=28466.30, stdev=29575.52
    clat percentiles (usec):
     |  1.00th=[ 1768],  5.00th=[ 4192], 10.00th=[ 4512], 20.00th=[ 4704],
     | 30.00th=[ 4960], 40.00th=[ 5344], 50.00th=[ 6752], 60.00th=[13376],
     | 70.00th=[62720], 80.00th=[63744], 90.00th=[65280], 95.00th=[68096],
     | 99.00th=[91648], 99.50th=[102912], 99.90th=[152576], 99.95th=[162816],
     | 99.99th=[166912]
    lat (usec) : 1000=0.01%
    lat (msec) : 2=1.11%, 4=2.86%, 10=54.09%, 20=2.94%, 50=2.41%
    lat (msec) : 100=36.03%, 250=0.56%
  cpu          : usr=1.29%, sys=5.71%, ctx=77732, majf=0, minf=2058
  IO depths    : 1=0.1%, 2=0.1%, 4=0.1%, 8=0.1%, 16=0.1%, 32=0.1%, >=64=100.0%
     submit    : 0=0.0%, 4=100.0%, 8=0.0%, 16=0.0%, 32=0.0%, 64=0.0%, >=64=0.0%
     complete  : 0=0.0%, 4=100.0%, 8=0.0%, 16=0.0%, 32=0.0%, 64=0.0%, >=64=0.1%
     issued rwt: total=163840,0,0, short=0,0,0, dropped=0,0,0
     latency   : target=0, window=0, percentile=100.00%, depth=128

Run status group 0 (all jobs):
   READ: bw=281MiB/s (295MB/s), 281MiB/s-281MiB/s (295MB/s-295MB/s), io=10.0GiB (10.8GB), run=36441-36441msec

Disk stats (read/write):
  pxd!pxd485798653434466349: ios=158688/4, merge=0/1, ticks=4581442/484, in_queue=4308944, util=99.63%
```
