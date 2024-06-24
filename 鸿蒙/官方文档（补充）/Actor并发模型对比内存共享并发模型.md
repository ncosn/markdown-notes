**内存共享并发模型**指多线程同时执行复数任务，**这些线程依赖同一内存**并且都有权限访问，线程访问内存前**需要抢占并锁定内存的使用权**，没有抢占到内存的线程需要等待其他线程释放使用权再执行。

**Actor并发模型**每一个线程都是一个独立Actor，每个Actor有自己独立的内存，Actor之间通过**消息传递机制**（线程间通信机制）触发对方Actor的行为，不同Actor之间不能直接访问对方的内存空间。

Actor并发模型对比内存共享并发模型，优势在于不同线程间内存隔离，不会产生不同线程竞争统一内存资源的问题。开发者不需要考虑对内存上锁导致的一系列功能、性能问题。



### 内存共享模型

内存锁





### Actor模型

Actor模型不同角色之间不共享内存，生产者线程和UI线程都有自己独占的内存。生产者生产处结果后通过序列化通信将结果发送给UI线程，UI线程消费结果后再发送新的生产任务给生产者线程。

```ts
import taskpool from '@ohos.taskpool';
// 跨线程并发任务
@Concurrent
async function produce(): Promise<number>{
  // 添加生产相关逻辑
  console.log("producing...");
  return Math.random();
}

class Consumer {
  public consume(value : number) {
    // 添加消费相关逻辑
    console.log("consuming value: " + value);
  }
}

@Entry
@Component
struct Index {
  @State message: string = 'Hello World'

  build() {
    Row() {
      Column() {
        Text(this.message)
          .fontSize(50)
          .fontWeight(FontWeight.Bold)
        Button() {
          Text("start")
        }.onClick(() => {
          let produceTask: taskpool.Task = new taskpool.Task(produce);
          let consumer: Consumer = new Consumer();
          for (let index: number = 0; index < 10; index++) {
            // 执行生产异步并发任务
            taskpool.execute(produceTask).then((res : number) => {
              consumer.consume(res);
            }).catch((e : Error) => {
              console.error(e.message);
            })
          }
        })
        .width('20%')
        .height('20%')
      }
      .width('100%')
    }
    .height('100%')
  }
}
```

