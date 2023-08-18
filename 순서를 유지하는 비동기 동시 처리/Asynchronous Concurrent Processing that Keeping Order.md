# Asynchronous Concurrent Processing that Keeping Order

Separate asynchronous task execution and completion handling with a queue in between

- Upon receiving input, create a task object and add it to the queue.
- Execute asynchronous tasks.
- Register the logic that update the task object's state, dequeue and output the completed task object when an
  asynchronous task is completed.
- Separate the logic for executing asynchronous tasks and processing completed tasks, allowing the next asynchronous
  task to be executed without waiting for task completion.
- Since a queue is used, can keep the order.

## Show me the code:

A simple example using events:

```typescript
import {EventEmitter} from "node:events";

type Task<T> = { done: false } | { done: true; value: T };
type TaskWrapper<T> = { task: Task<T> };

export function run<TInput, TOutput>(params: {
    source: EventEmitter;
    f: (value: TInput) => Promise<TOutput>;
}): EventEmitter {
    const destination = new EventEmitter();

    let isClosed = false;
    const queue: Array<TaskWrapper<TOutput>> = [];

    // Execute upon receiving input.
    function handler(value: TInput): void {
        // Create a task object and add it to the queue.
        const taskWrapper: TaskWrapper<TOutput> = {task: {done: false}};
        queue.push(taskWrapper);

        // Execute the asynchronous task and register the function to run upon completion.
        params.f(value).then((value) => {
            // Update the task object's state.
            taskWrapper.task = {done: true, value: value};
            // Execute the output function.
            tick();
        });
    }

    // Output completed tasks.
    function tick() {
        // Retrieve and output completed tasks in order from the queue. Stop outputting if an incomplete task is encountered.
        let taskWrapper = queue.at(0);
        while (taskWrapper != null && taskWrapper.task.done) {
            destination.emit(`data`, taskWrapper.task.value);
            queue.shift();
            taskWrapper = queue.at(0);
        }

        // Close the destination event stream when all tasks are completed and the source event stream is closed.
        if (queue.length === 0 && isClosed) {
            params.source.off(`data`, handler);
            destination.emit(`close`);
        }
    }

    params.source.on(`data`, handler);
    params.source.once(`close`, () => {
        // Update upon closure of the source event stream and execute the output function.
        // !!Caution!! Immediate closure of the destination event stream could lead to loss of incomplete tasks.
        isClosed = true;
        tick();
    });

    return destination;
}
```

You can test whether the above example function outputs results in order and whether tasks are executed concurrently
with the following code:

```typescript
import {EventEmitter} from "node:events";
import {delay} from "./delay";
import {run} from "./example1";

const data = [1, 2, 3, 4, 5];
const expected = [`1`, `2`, `3`, `4`, `5`];
const delayMs = 1000;
const f = async (value: number): Promise<string> => {
    await delay(delayMs);
    return `${value}`;
};

describe(`Example 1`, () => {
    it(`Keep the original sequence.`, async () => {
        const source = new EventEmitter();
        const destination = run({source: source, f: f});
        const output: Array<string> = [];
        const handler = (value: string): void => {
            output.push(value);
        };
        destination.on(`data`, handler);
        const done = new Promise<void>((resolve) => {
            destination.once(`close`, () => {
                destination.off(`data`, handler);
                resolve();
            });
        });

        for (const value of data) {
            source.emit(`data`, value);
        }
        source.emit(`close`);
        await done;

        expect(output).toEqual(expected);
    });

    it(`Run concurrently.`, async () => {
        const source = new EventEmitter();
        const destination = run({source: source, f: f});
        const done = new Promise<void>((resolve) => {
            destination.once(`close`, () => {
                resolve();
            });
        });

        const start = Date.now();
        for (const value of data) {
            source.emit(`data`, value);
        }
        source.emit(`close`);
        await done;
        const end = Date.now();
        const duration = end - start;

        // Considering the tolerance of the setTimeout API.
        const toleration = 30;
        expect(duration).toBeGreaterThanOrEqual(delayMs - toleration);
        expect(duration).toBeLessThanOrEqual(delayMs + toleration);
    });
});
```

## ⚠️ Caution:

If a large amount of data is input in a short period, the queue could become excessively long, leading to potential
memory exhaustion.

- The example function does not control backpressure.
- You should handle further input only when there is capacity to execute additional asynchronous tasks by setting a
  maximum concurrency limit.
- For detailed implementation, refer to
  the [example](https://github.com/daengdaengLee/til/blob/main/%EC%88%9C%EC%84%9C%EB%A5%BC%20%EC%9C%A0%EC%A7%80%ED%95%98%EB%8A%94%20%EB%B9%84%EB%8F%99%EA%B8%B0%20%EB%8F%99%EC%8B%9C%20%EC%B2%98%EB%A6%AC/example2.ts).

## Reference:

You can find the complete example and test code in
the [example repository](https://github.com/daengdaengLee/til/tree/main/%EC%88%9C%EC%84%9C%EB%A5%BC%20%EC%9C%A0%EC%A7%80%ED%95%98%EB%8A%94%20%EB%B9%84%EB%8F%99%EA%B8%B0%20%EB%8F%99%EC%8B%9C%20%EC%B2%98%EB%A6%AC).
