# 순서를 유지하는 비동기 동시 처리

큐를 사이에 두고 비동기 작업 실행과 완료 작업 처리를 분리

- 입력을 받으면 작업 객체를 생성해 큐에 추가합니다.
- 비동기 작업을 실행합니다.
- 비동기 작업을 완료했을 때 작업 객체 상태를 업데이트하고 큐에서 완료한 작업 객체를 꺼내 출력하는 로직을 등록합니다.
- 비동기 작업을 실행하는 로직과 완료한 작업을 처리하는 로직을 분리해서 작업 완료를 기다리지 않고 다음 비동직 작업을 실행할 수 있습니다.
- 큐를 사용했기 때문에 순서를 유지할 수 있습니다.

## Show me the code:

이벤트를 사용한 간단한 예제입니다.

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

    // 입력을 받으면 실행합니다.
    function handler(value: TInput): void {
        // 작업 객체를 생성해 큐에 추가합니다.
        const taskWrapper: TaskWrapper<TOutput> = {task: {done: false}};
        queue.push(taskWrapper);

        // 비동기 작업을 실행하고 완료했을 때 실행할 함수를 등록합니다.
        params.f(value).then((value) => {
            // 작업 객체의 상태를 업데이트합니다.
            taskWrapper.task = {done: true, value: value};
            // 출력 함수를 실행합니다.
            tick();
        });
    }

    // 완료한 작업을 출력합니다.
    function tick() {
        // 큐에서 순서대로 완료한 작업을 꺼내 출력합니다. 아직 완료되지 않은 작업을 만나면 출력을 중단합니다.
        let taskWrapper = queue.at(0);
        while (taskWrapper != null && taskWrapper.task.done) {
            destination.emit(`data`, taskWrapper.task.value);
            queue.shift();
            taskWrapper = queue.at(0);
        }

        // 모든 작업을 완료하고 입력 이벤트 스트림이 닫혔을 때 출력 이벤트 스트림을 닫습니다.
        if (queue.length === 0 && isClosed) {
            params.source.off(`data`, handler);
            destination.emit(`close`);
        }
    }

    params.source.on(`data`, handler);
    params.source.once(`close`, () => {
        // 입력 이벤트 스트림이 닫혔다고 업데이트하고 출력 함수를 실행합니다.
        // !!주의!! 바로 출력 이벤트 스트림을 닫으면 아직 완료하지 않은 작업을 잃어버릴 수 있습니다.
        isClosed = true;
        tick();
    });

    return destination;
}
```

위 예제 함수가 결과를 순서대로 출력하는지, 작업을 동시에 실행하는지 테스트할 수 있습니다.

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

        // setTimeount API의 오차를 고려합니다.
        const toleration = 30;
        expect(duration).toBeGreaterThanOrEqual(delayMs - toleration);
        expect(duration).toBeLessThanOrEqual(delayMs + toleration);
    });
});
```

## ⚠️ Caution:

짧은 시간에 많은 데이터가 입력되면 큐가 무한히 길어져서 메모리가 부족해질 수 있습니다.

- 예제 함수에서는 배압을 조절하지 않고 있습니다.
- 최대 동시 실행 수를 설정하여 추가로 비동기 작업을 실행할 수 있을 때만 입력을 받을 수 있도록 처리해야 합니다.
- 자세한 구현
  코드는 [예제](https://github.com/daengdaengLee/til/blob/main/%EC%88%9C%EC%84%9C%EB%A5%BC%20%EC%9C%A0%EC%A7%80%ED%95%98%EB%8A%94%20%EB%B9%84%EB%8F%99%EA%B8%B0%20%EB%8F%99%EC%8B%9C%20%EC%B2%98%EB%A6%AC/example2.ts)
  를 참고해주세요.

## Reference:

전체 예제 및 테스트
코드는 [예제 저장소](https://github.com/daengdaengLee/til/tree/main/%EC%88%9C%EC%84%9C%EB%A5%BC%20%EC%9C%A0%EC%A7%80%ED%95%98%EB%8A%94%20%EB%B9%84%EB%8F%99%EA%B8%B0%20%EB%8F%99%EC%8B%9C%20%EC%B2%98%EB%A6%AC)
에서 확인할 수 있습니다.
