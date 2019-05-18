---
title: CompletionService优点及其应用
date: 2019-03-03 18:18:52
category: 多线程
tag: 多线程
---

### 1. 需求描述

某一图片网站首页有许多图片，渲染时间较长，给用户带来较差体验，为提高用户体验度，图片需缓存且无需等待所有图片全部准备完毕后，进行渲染

### 2. 问题分析与实现

显而易见，我们可以想到一边获取图片，一边进行渲染，并行操作。但是每张图片获取的时间无法预知，即任务的执行时长不一致，有的可能几毫秒，有的可能几秒, 我们如何才能做到先获取的图片先进行渲染呢？幸好CompletionService适合该场景: 将Executor与BlockingQueue的功能融合在一起，将Callable任务提交给它执行，然后类似队列中的take与poll获取已经完成的任务。

### 3. CompletionService源码分析

``` bash
public interface CompletionService<V> {
        Future<V> submit(Callable<V> task);

        Future<V> take() throws InterruptedException;

        Future<V> poll();

        ......
}
```

CompletionService唯一实现类： ExecutorCompletionService。 在构建函数中创建一个BlockingQueue保存结果。任务提交后，将任务包装成QueueingFuture(FutureTask的一个子类)，任务完成后，调用QueueingFuture的done方法，将结果放入BlockingQueue中。

``` bash
public class ExecutorCompletionService<V> implements CompletionService<V> {
    	private final BlockingQueue<Future<V>> completionQueue;

    	public ExecutorCompletionService(Executor executor) {
                if (executor == null)
                    throw new NullPointerException();
                this.executor = executor;
                this.aes = (executor instanceof AbstractExecutorService) ?
                    (AbstractExecutorService) executor : null;
                this.completionQueue = new LinkedBlockingQueue<Future<V>>();
        }

    	public Future<V> submit(Callable<V> task) {
                if (task == null) throw new NullPointerException();
                RunnableFuture<V> f = newTaskFor(task);
                executor.execute(new QueueingFuture(f)); // 任务包装成QueueingFuture
                return f;
        }

        private class QueueingFuture extends FutureTask<Void> {
                QueueingFuture(RunnableFuture<V> task) {
                    super(task, null);
                    this.task = task;
                }
                protected void done() { completionQueue.add(task); } // 任务完成后，调用done方法，放入队列
                private final Future<V> task;
        }

        public Future<V> take() throws InterruptedException {
                return completionQueue.take();
        }

        public Future<V> poll() {
                return completionQueue.poll();
        }
}
```

### 4. 代码实现

``` bash
/**
 * 使用CompletionService实现页面渲染器
 */
public class Renderer {

        private ExecutorService executor;

        public Renderer(ExecutorService executor) {
                this.executor = executor;
        }

        public void renderPage(CharSequence source) {
                List<ImageInfo> info = scanImageInfo(source);
                CompletionService<ImageData> completionService = new ExecutorCompletionService<>(executor);

                for (final ImageInfo imageInfo : info) {
                        completionService.submit(() -> {
                                return imageInfo.downloadImage();
                        });
                }

                renderText(source);

                try {
                        for (int i = 0; i < info.size(); i++) {
                                Future<ImageData> future = completionService.take();
                                ImageData imageData = future.get();
                                renderImage(imageData);
                        }
                } catch (InterruptedException e) {
                        Thread.currentThread().interrupt();
                } catch (ExecutionException e) {
                        e.printStackTrace();
                }
        }
}
```
