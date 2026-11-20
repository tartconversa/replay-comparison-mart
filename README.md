# 开放场景回放的可比运行事实仓

工业回放工作负载的阶段报告与可比性归档。 资料为本地合成样本。

## 源交付

`sources/` 存放 JSON 原件，文件名前两位是到达序号，同一序号内的各表属于同次交付。每个文件有 `intake`、`table`、`records`，记录 `id` 在所属表内唯一。`source-fields.json` 列出各表字段类型，空值用 `null`，空集合用 `[]`。业务时间按下方约定解释。

| 数据表 | 说明 |
| --- | --- |
| `scenarios` | 场景集合、负载组及输入摘要。 |
| `environments` | 环境登记，invocation 为调度方记录的设备别名。 |
| `aliases` | 调度回执用到的设备别名及有效范围。 |
| `equivalence` | 证据指向具体输入及操作结论。 |
| `schedules` | 调度动作的原始回执。 |
| `attempts` | 调度下的运行尝试。 |
| `phases` | 处理工件的回执，dependency 指实际产物摘要。 |
| `requests` | 采购的预先登记及固定范围。 |

## 来源约定

接收序号代表资料到达的先后；采购确认的观察截止点是已接收序号。每次 attempt 指向 schedule 下的一条明确分支；parent 表示前一次尝试。phase 需审计 stage、input 和已结束的依赖阶段。一个登记可复用的阶段，其 input 及所有必要依赖产物指纹必须与申请复用的分支相同，并在复用时仍具有有效环境和输入凭证。依赖顺序为 prepare、run、check，只有 check 完成且所有必要依赖成立才有完整结果；score 取 check 的成功结果。
每个 window 内，每个环境和场景以截止之前最早的有效完成尝试为正式配对对象；阶段复用保留原尝试的失败事实。same 允许绑定两个输入，different 排除，revoked 撤销指定凭证。环境以硬件、栈与版本范围决定，alias 只在所列时段有效。独立环境的完成情况按登记场景计数，各组的最低 pair 数由 request 给出。

## 资料盘点

运行环境为 Python 3.11，盘点与资料校验仅使用标准库。盘点结果按到达批次显示表名及行数。

```sh
python source_inventory.py
python -m unittest discover -s tests -v
```
