# Week 2 課堂筆記 — System-Level Diagnosis

**日期**：2026/9/15<br>
**課程**：書報討論（電子系）<br>
**講者**：王大進 教授（Montclair State University）<br>
**講題**：System-Level Diagnosis – An Introduction and Recent Results（網路診斷 & 容錯）

---

## 1. 背景與動機 (Background & Motivation)

- 越來越多高速、大規模的 multiprocessor / multicomputer systems 被部署使用
- 這些系統透過 **interconnection networks**（不同拓樸）連結處理器（nodes）
  - 節點（node）：處理器
  - 邊（line）：連結
  - 連結方式各有規則，按照一定的規律去連結
- 系統規模越大 + 網路攻擊活動 → 計算節點的故障 / 失效不可避免（faults / faulty nodes）

### 可靠度示例（processor reliability = 99.99%）

| 處理器數量 | 系統可靠度 |
|---|---|
| 100 | 0.9999¹⁰⁰ ≈ 99.01% |
| 1,000 | 0.9999¹⁰⁰⁰ ≈ 90.48% |
| 10,000 | 0.9999¹⁰⁰⁰⁰ ≈ 36.79% |

→ 節點數量越多，即使單一節點可靠度很高，整體系統可靠度也會大幅下降。

---

## 2. 節點故障的處理方式

當 node（處理器）壞掉時，可以：
1. **替代**（找其他節點）
2. **用演算法繞開**（避開壞節點）

但這些方法有一個大前提：**必須先發現壞的節點**
→ 這正是 **System-Level Diagnosis** 要解決的問題：透過節點間 **mutually check（互相檢測）** 找出所有故障節點。

---

## 3. 系統級故障診斷 (System-Level Diagnosis)

### 基本概念
- 節點透過「互相檢測」達成「自檢」：系統中的 nodes 檢測其他 nodes
- 例：node *i* 對 node *j* 做測試，得出結論「*j* 是 faults / faulty（0/1）」
- 測試結果可以雙向進行：i → j 與 j → i 都各自獨立產生結果

### 關鍵限制
> **如果施行測試的節點本身是故障節點，則測試結果是任意的（不可靠、不確定的），因而無效！**

也就是說，只有「好的節點」做出的測試結果才可信。

### PMC 模型 (Preparata–Metze–Chien)
- 節點 i 測試節點 j，結果 aᵢⱼ ∈ {0, 1}
  - i good, j good → 結果 = 0
  - i good, j bad → 結果 = 1
  - i bad → 結果任意（x, 不可靠）
- 一個「不受控（uncontrolled）」的 arbiter 會根據所有測試結果（syndrome）判斷每個處理器是 faulty 或 fault-free
- 每一列（row）測試結果的集合稱為 **syndrome（症狀）**

---

## 4. t-可診斷度 (t-Diagnosability)

### 定義
> 對於一個給定的網路，存在一個最大允許的故障節點數 **t**，只要壞節點數不超過 t，arbiter 就能透過互測結果正確辨識出所有壞節點。這個數字 t 稱為該網路的 **Diagnosability（診斷度）**。

- 若壞節點數 > t，此互測策略即失效
- 條件：bad node 數量不能太多，策略才有效

### 三角形網路範例（3 個節點 a, b, c）
- 範例網路中，診斷度 t = 1（只能正確判斷 1 個壞點）
- 若壞點數達到 t = 2，系統即失效（無法正確判斷）

### 充分條件 (Sufficient Condition)
網路 G = (V, E) 為 t-diagnosable 的充分條件：

```
|V| ≥ 2t + 1
且
κ(G) ≥ t
```

其中：
- |V| = 節點總數
- κ(G) = G 的最小分支度（min degree）

---

## 5. 條件式診斷度 (Conditional Diagnosability)

### 動機
原本 t-diagnosability 對壞點的分布沒有做任何限制（壞點可無條件、任意分布）。

但研究者發現：**如果假設任一節點的鄰居不會同時全部故障**（這是高機率合理的假設），那麼可以排除很多不確定的檢測結果，互測就能檢測出更多壞點 → 大幅提高系統的診斷度。

> 若「一個節點所有連接節點同時壞掉」這種不合理情況被排除，arbiter 能容忍的故障節點數就能大幅增加。

### 範例：n 維超立方體 Qₙ（PMC Model 下）

| 診斷度類型 | 數值 |
|---|---|
| 原始（unconditional）diagnosability | *n* |
| Conditional diagnosability [Lai et al. 2005] | *4n − 7*，for *n ≥ 5* |

→ 加入合理假設後，可診斷的故障節點數大幅提升。

---

## 6. 重點總結

1. 大型系統節點越多，整體可靠度下降越明顯，因此故障診斷極為重要
2. System-Level Diagnosis 的核心：節點間互相測試（mutually check），但測試結果的可信度取決於測試者本身是否正常
3. **t-diagnosability**：系統能正確辨識的最大故障節點數，壞點數超過 t 則系統失效
4. 充分條件：|V| ≥ 2t+1 且最小分支度 κ(G) ≥ t
5. **Conditional diagnosability**：加入「鄰居不會全壞」的合理假設後，能大幅提升可容忍的故障節點數量（如 Qₙ 從 n 提升到 4n−7）

---

*筆記整理自課堂手寫筆記與投影片內容*
*翁慧霖 2026.09.15 22:30*
