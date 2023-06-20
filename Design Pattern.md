# 設計模式 Design Pattern

> 參考資料
>
> [設計模式系列文](https://kimlin20011.medium.com/%E4%BB%80%E9%BA%BC%E6%98%AF%E8%A8%AD%E8%A8%88%E6%A8%A1%E5%BC%8F-design-pattern-%E8%A8%AD%E8%A8%88%E6%A8%A1%E5%BC%8F%E7%B3%BB%E5%88%97%E6%96%87-%E4%B8%8A-5988dacfda4a)

## 什麼是設計模式

> 每一個模式(Pattern)都是在某個特定情境(context)下，針對某問題(Problem)提出的解決方案(solution)。

在軟體工程中將 Pattern 分為：

- Architecture Pattern: 解決軟體系統架構層面的問題。如：MVC 架構、Layer...
- Design Pattern: 提供改善軟體系統中子系統與元件(components)的方案。常見如：Observer, Facade, Adapter...
- Idioms: 是一種 lowest-level pattern，為 programming(程式撰寫)層級提供程式改善方案，主要透過程式語言的解決方案來實現。

### 範例

情境(context)：每個人都喜歡坐在窗邊，有低矮的大窗台與舒適的椅子，若一個房間中沒有如此環境，很難讓人感到舒服。

此時的問題(problem)正是：如何讓人感到舒服自在？

此時有兩個需求(Force)：

- 你想要舒服地坐下
- 你想要面對窗戶

但往往兩個需求會互相衝突：

- 舒服的椅子背對窗戶
- 面對窗戶只有一張壞掉的椅子

最好的方法就是當你每次在安排室內布局時，雖然房間可能沒有太多擺設的選擇，但至少依循著一個"面對窗戶有一個舒服的位置"這樣的模式。

## 如何描述設計模式

設計模式的發表雖然也是學術的一環，但其講求的重點與一般學術論文不同。

比起一般學術論文要求深入嚴謹，設計模式更像是一個產品說明，須具備高度結構性的方法與淺顯易懂的行文來表達。

這裡主要使用的是 POSA 中，設計模式的描述結構。

### Name

名字是一個讀者了解模式最快的方法，也是總結一個模式的重要方法。而且要對 pattern 命名有很多種方法，常見的有：

- 取自 solution 的抽象特徵，如：
  - Adapter 模式(像是一個轉接器，轉介不同規格的物品)
  - Observer 模式(像一個觀察者，若訂閱的標的有狀態的變化，則馬上通知訂閱者)
- 取自 solution 中元件的名稱，像是 MVC (取自系統中的 model、view、controller 三個元件的頭一個字母)

### Context

舉出模式可能適用的情況。主要是整理 problem 可能發生的情境來界定 problem 可能發生的範圍。雖然 context 不能列出所有情境，但至少可以提供重要的指引。

### Problem

problem 是描述會在 context 中重複發生的問題。另一方面 problem 也是 pattern 的核心元素，說明主要模式的設計議題。
problem 中有一個很重要的元素：force。在前面的範例中有提到，force 是導致設計問題中存在的具體的力，同時，force 幫助 context 中具體形塑出 solution 的邊界。主要列出幾個需要解決的層面：

- solution 需要滿足的需求
- solution 需要考慮的限制
- solution 需要包含的特性

但不幸的是，force 常常是互相矛盾且衝突的，在設計 solution 時常常需要權衡不同的 force 來得到最適合的解決方案。

### Solution(解決方案)

以高階的方式描述 pattern 解決方案原理。solution 提供解決重複發生的 problem 的方法，並盡可能平衡相關的 force (前文提到 force 常常是互相矛盾的)。另一方面，solution 又以 structure(建構) (像是以 UML 類別圖描述各個類別元件之間的關係)與 Dynamics(動力學) (像是以 UML 的循環圖描述元件之間的動態行為與協作)層面來描述 pattern 中不同元件之間的靜態關連與動態協作。

### Implementation(執行)

引導讀者實作 pattern。此部分可以依照 pattern 描述的需求採用，並適當提供實作範例(如：程式碼等等)，通常提供與 Example 部分相關的實作方案。

### Example

透過舉實際案例來補足在 solution 與 Implementation 部分沒有被涵蓋但又為解決方案中重要的層面。

### Variants(變體)

此部分可以依照 pattern 描述的需求採用。簡述 pattern 相關變形的其他 pattern。

### Known uses

列舉與 pattern 相關的現存系統。用來證明提出的 pattern 是真實世界中相關情境存在的設計問題，pattern 的解決方案已被應用且能有效的解決問題。

### Consequences(結果)

列舉 pattern 的優劣勢。一個 pattern 不可能是完美的，因此也需要列出在特定情境下可能造成的限制或缺陷。

### Related pattern

列舉用來解決相似情境下的設計問題或能與該 pattern 整合協作的其他 pattern。

## 撰寫設計模式時需要的思考脈絡

再次列舉上面提到的例子：

1. 首先，先確定目標的情境。
   > 我們要設計一個工作室，而這個工作室只有兩扇窗，幾張舒適的椅子，幾張難坐的椅子
2. 確定設計問題
   > 如何讓工作室工作的人一整天都能舒服自在。
3. 接著思考影響上述問題發生的具體作用力，以形塑解決方案的邊界
   > 需要舒適的坐著
   > 想要靠窗，才能偶爾看看窗外來紓解壓力。
4. 列出這些 force 後，接著可以開始設計解決方案
   > 需要舒服地坐著：在工作位置上挑選舒適的椅子
   > 想要靠窗：將辦工作桌與辦公椅，盡可能的緊靠窗戶
5. 可以用繪圖的方式輔助說明解決方案，列出工作室擺設的動態(這個模式可能不需要)與靜態(擺設圖)關係
6. 透過舉例，引導參考此模式的施工人員，實作本模式提出的方法
   > 例如：列出椅子的規格、窗戶和椅子的距離、擺放角度等等
7. 為了證明此方案是有效的，也可以利用幾個經典的室內設計案例來佐證
8. 完成大部分的設計模式內容後，可以透過列舉優點與限制來為設計模式做個總結
   > 優點：可以使人長時間舒適的待在工作室工作
   > 缺點：選擇舒適的椅子，需花費較高的成本。窗戶須有不同高度(人坐著可以直接看到窗外的高度)
9. 透過提出的解決方案的特徵，來為此設計模式命名
   > 椅子與窗戶的對向模式

## 設計模式撰寫時的思考脈絡

- 界定情境(context)
- 定義設計問題(problem)
- 從設計問題中列出會影響該問題發生具體的力(force)
- 依循所列出的 force 來設計解決方法(solution)
- 統整解決方案，以靜態(structure)與動態(dynamics)的描述方式表述之
- 最後引導讀者實作 pattern 並提出相關的範例(example)
- 為了證明 pattern 的實用性，可列舉出已知應用 pattern 中相似解法的例子(known uses)
- 最後列出應用該 pattern 後產生的優點與限制(consequences)
- 通常在 pattern 完成之後可以依據 pattern 的特性為 pattern 命名(name)，也可以邊撰寫時邊思考。

## 如何完善已完成的設計模式？

### Big Picture

- 如何掌握設計模式的主旨：

  - 設計模式的初稿通常難以理解。而描述得太簡潔往往缺乏實質的內容；相反的，內容太過龐大（描述的多而深入）通常因為太專注細節的描述而模糊了模式的核心概念。

- ThereFore
  - shepherd 在剛剛接觸設計模式的時候必須先閱讀 problem 與 solution 的部分來掌握該模式的大綱，這主要是因為 sheep 通常最注重再解決方案的設計部分，並將所有對模式的想法，灌輸在 solution 中。

### Matching Problem and Solution

- 如何確定模式真的是模式的樣子(pattern-ish)

  - 在撰寫模式時，常會先寫好 solution （因為可能在不斷實作中領悟到通用解決方法），而後在去設計模式的問題，所以在審稿的時候，常常讀起來不清楚這個模式的真正目的是什麼。

- ThereFore
  - 審稿的時候要確認設計模式的 solution 有完整的針對 problem 解決，解決方案也不能超過 problem 的範圍（剛好就好，不能多餘），之後再加強其中不足的部分（problem 或是 solution ）。

### Convincing Solution

- 如何提高設計模式的可信度(believable)
  - 有時候 solution 提供的解法根本無濟於事，看起來沒有實現的能力。
- therefore
  - 一般的 pattern 在看完 solution 後通常要有「原來可以者麼解決」的感覺。如果沒有，則比需要提供真正實作應用的細節，才能使人信服。

### Forces Define Problem

- 如何更深層的瞭解問題
  - 很多設計模式的初稿，對於 problem 的描述都很薄弱。
- Therefore
  - 須要透過 Force 來具體化對 problem 的敘述。而作者須要以迭代的方式不對斷修正與增強對 Force 與 Problem 的敘述，透過列出具體化不同的 Force，尋找交集後，最後為其總結出核心問題。

### Balance Context

- 如何為 pattern 劃定出合適的範圍
  - 在撰寫設計模式的 context 時，常常不小心把情境的範圍界定的太大或太小，也往往忽略了界定該情境應用 pattern 之後可能的結果。
- Therefore
  - 在撰寫設計模式時，必須要具體思考並說明情境使用與不適用這個模式前後的差異，這樣不僅能夠賦予模式的使用者對模式應有的期望，也能幫助我們界定剛好符合所提出的模式範圍。

### War Store

- 如何推進模式前進？
  - 有時不管如何 sheep 如何修改或是更正 shepherd 的建議，shepherd 還是覺得設計模式描述的不清楚、缺乏對該模式的實際應用想像。
- Therefore
  - 這時 shepherd 應要求 sheep 提供設計模式應用的真實案例，幫助對模式的想像更為生動實際。

### Form Follows Function

- 如何將設計模式套用到一個新的格式(form)
  - 有時 sheep 所選擇得 patten 格式，沒辦法完表達(或是不適合)所提出的設計模式，這可能是因為 sheep 對 patten 的格式不熟悉(可能只知道一兩個)，或是因為要把格式整個換掉，須要花費相當大的功夫，因此選擇不換。
- Therefore
  - shepherd 須要一步一步的引導 sheep 修改原本模式所應用的格式(一次改一些就好)，而終極目的是「making the form serve the patten」

### Small Patterns

- 如何使設計模式變的更可消化
  - 設計模式常常經由不斷修正(可能是因為 sheep 不願意對已完成的內容做刪減)後，內容變的很龐大。
- Therefore
  - 先放任內容增加，最後再做刪減。刪減可以透過移除不必要的區塊，或是將過於龐大模式，拆分成多個小模式(要做到一個模式只能有一個 context, problem 與 solution)
