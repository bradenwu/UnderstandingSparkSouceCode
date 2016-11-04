# UnderstandingSparkSouceCode
## 第一章内存管理
### 1.1 TaskMemoryManager
该类的主要功能是



```graph```
graph LR
A-->B
```



```
sequenceDiagram

  
    
    CoarseGrainedSchedulerBackend ->> CoarseGrainedSchedulerBackend : 1.call killExecutors() or
    CoarseGrainedSchedulerBackend ->> CoarseGrainedSchedulerBackend : 1.call requestTotalExecutors() or
    CoarseGrainedSchedulerBackend ->> CoarseGrainedSchedulerBackend : 1.call requestExecutors
    CoarseGrainedSchedulerBackend ->> CoarseGrainedSchedulerBackend : 2. synchronized {} 上锁!
    CoarseGrainedSchedulerBackend ->> yarnSchedulerEndpointRef : 3. t1时刻调用doRequestTotalExecutors
    yarnSchedulerEndpointRef ->> yarnSchedulerEndpoint : 4. 发送消息等待应答 askWithRetry(RequestExecutors)
    yarnSchedulerEndpoint ->> yarnSchedulerEndpoint : 5. t2时刻接收到有人发送的RemoveExecutor 的消息
    yarnSchedulerEndpoint ->> CoarseGrainedSchedulerBackend : 6. removeExecutor()
    CoarseGrainedSchedulerBackend ->> driverEndpointRef : 7: driverEndpoint.askWithRetry(RemoveExecutor)
    driverEndpointRef ->> driverEndpoint : 8. RemoveExecutor
    driverEndpoint ->> CoarseGrainedSchedulerBackend : 9. removeExecutor{CoarseGrainedSchedulerBackend.this.synchronized }
    yarnSchedulerEndpoint ->> yarnSchedulerEndpoint : 10. t3时刻接收到4发送的RequestExecutors消息!!!
    
    
```

