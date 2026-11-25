# 坑：React effect 依赖扩大导致页签/状态回写抖动

## 何时使用

改共享 Tabs / 子页签、日志 pageKey、埋点、翻译对象，或任何「扩大 `useEffect` 依赖 + 回调父组件」的改动时必读。

## 典型错因

为刷新旁路数据（日志 key、文案）把 `tabData` / `t` / style 等加入 effect 依赖后，effect 里仍调用「选中项变更」回调，参数却是**更新前的旧索引**。父层把旧索引写回 store，与外部 `changeToIndex` 互相覆盖 → 页签来回跳、重复请求。

## 必须

1. **effect 内回调只报告本组件状态变化**，不因 props/列表/翻译刷新去报「选中项变了」。
2. **同一状态单写者**：本地 / store / 父 props 三选一真相源；外部值变化时禁止用旧本地值回写。
3. **`setState` updater 必须是纯函数**：禁止在 updater 内 dispatch / 写 storage / 调其它 setState；需同步写外部时用 `ref` 持最新值，在 updater 外写。
4. **扩大依赖 = 行为变更**：修 A 加依赖前，拆 effect，或用 ref 记录「已通知值」，值未变则跳过。
5. **异步写回先取最新列表**：用 `ref.current` + 稳定 id 合并，禁止闭包旧数组整表 set。
6. **改共享组件先审计全部消费方**（mount / 外部驱动 / 数据刷新），并补回归测试。

## 禁止

- 依赖数组「多挂几个保险」却不重审回调语义
- 用更新前的旧索引/旧 id 通知父组件
- 在 updater 里夹带副作用
- 改完共享组件不跑消费方路径与相关测试

## 反面 → 正面

| 旧 | 新 |
|----|----|
| `[tabIndex, tabData, t]` 里直接 `onTabSwitch(tabIndex)` | 数据/文案刷新与「选中通知」拆开；仅 `tabIndex` 真变时通知 |
| 父写 redux、子又用旧 index 回调父 | 外部 `changeToIndex` 优先；变化时不回写旧本地值 |
| `setTabs(prev => { dispatch(...); return next })` | updater 纯计算；外部写入放 updater 外，必要处用 ref |

## 自检

- [ ] 这些依赖各自对应哪段逻辑？能否拆成两个 effect？
- [ ] 回调参数是否可能是变化前的旧值？
- [ ] 回调是否会写回驱动本组件的状态，形成环路？
- [ ] 消费方在 mount / 外部切换 / 数据刷新下是否仍正确？
- [ ] 是否补了回归测试？
