# tomnet\_plot\_generation.ipynb

## How to use the notebook

* **I have not checked every Python version. I personally run it with Python 3.9.6; if you encounter problems, just let me know.**
* First import the required libraries.
* Change `PROJECT_ROOT = Path('/Users/Jer_ry/Desktop/scripts')` so it points to the folder that contains your local `scripts` directory.
* Import the helper functions `generate_social_score`, `Center_Agent`, … `run_logic_simulation`.
* Then set the key parameters.

### Key parameters


| Parameter                | What it does                                 | Notes                                                                                                                                                                                        |
| ------------------------ | -------------------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **n\_chosen\_agents**    | Number of agents on the map                  | Leave at 3; the*Intermediate*and*Logic*rules only work when`agents = 3`.                                                                                                                     |
| **move\_type**           | `'RuleMap'`,`'Random'`, or`'Intermediate'`   | The*Logic*rule is generated separately with`run_logic_simulation`. To build datasets for all three rule types, run the notebook three times—once for each value—without changing**FNAME**. |
| **FNAME**                | Folder name for everything you save          | –                                                                                                                                                                                           |
| **STEP\_TOTAL**          | Steps per trial (within one rule)            | –                                                                                                                                                                                           |
| **SET\_UP\_MAZE\_TOTAL** | Number of independent trials under that rule | –                                                                                                                                                                                           |

### Running the simulations

1. **`generate_social_score`** – creates the social-score table used by the `'RuleMap'` rule.
   • Written to `scripts/data/*FNAME*/log/social_score/`.
2. **`simulation_data_dynamic`** – simulates trajectories for the chosen rule.
   • Output files: `data/FNAME/move_type/FNAME_1.txt` … `FNAME_{SET_UP_MAZE_TOTAL}.txt`.
   • Each text file starts with an ASCII sketch of the maze, followed by the coordinate history of each agent (first three pairs = initial positions; next three = positions at the next step, etc.).
3. **`for CASE_TO_RUN in range(1, 21): run_logic_simulation`** – builds the *Logic*-rule text files.
   • Saved as `data/sim1/logic/logic_case{CASE_TO_RUN}.txt`.
   **Case definition**
   * *Parity (odd/even label shown on the moving agent)*
     * `case // 12 == 0` → A odd | B odd | C even
     * `case // 12 == 1` → A odd | B even | C odd
     * `case // 12 == 2` → A even | B odd | C odd
   * *Direction pattern*
     ```
     case % 12 ==  1 : ['L', 'L', 'D']   case % 12 ==  7 : ['L', 'R', 'U']
     case % 12 ==  2 : ['D', 'L', 'L']   case % 12 ==  8 : ['U', 'L', 'R']
     case % 12 ==  3 : ['L', 'D', 'L']   case % 12 ==  9 : ['U', 'R', 'L']
     case % 12 ==  4 : ['R', 'R', 'D']   case % 12 == 10 : ['L', 'U', 'R']
     case % 12 ==  5 : ['D', 'R', 'R']   case % 12 == 11 : ['R', 'U', 'L']
     case % 12 ==  6 : ['R', 'D', 'R']   case % 12 ==  0 : ['R', 'L', 'U']
     ```
   * The scheme yields 36 combinations, but we only need 20, so we take the first twenty and label them *case 1* … *case 20*.

### Plotting and saving figures

1. Import `parse_coordinates`, …, `process_maze_file`.
2. **TXT preview settings**

   ```
   SIM_FOLDER = FNAME
   RUN_TYPE   = 'random'        # rule type to preview
   FILES_TO_SHOW, STEPS_TO_SHOW # quick sanity-check parameters
   ```

   *(TXT rendering for *Logic* files is still pending.)*
3. **Generate the actual plots** – once the parameters look right, run the next block.
   • Images go to `data/FNAME/{move_type}_plot/step_plot_{FNAME}_{trial_num}/`, one PNG per step.
   • For *Logic* rules: `data/FNAME/logic_plot/step_plot_logic_case{case_num}/`.

## 使用方式

- **我沒有特別確認版本的問題，我自己是使用 python 3.9.6 執行，如果有任何問題可以詢問我**
- 首先先載入必要的函式庫
- 更改 `PROJECT_ROOT = Path('/Users/Jer_ry/Desktop/scripts')` ，此路徑得是目前 `scripts` 資料夾所在的位置
- 載入函式 generate_social_score、Center_Agent, ..., run_logic_simulation
- 重要參數調整

### 參數調整

* n_chosen_agents: 調整地圖上的 agents 數量（建議不要更動，因為 Intermediate 和 Logic 規則都只能在 agents = 3 時運作）
* move_type: 可選擇是 'Random', 'RuleMap', 'Random', 'Intermediate' （注意：因為 'Logic' 的規則比較不一樣，和三種的規則會使用不一樣的函式（run_logic_simulation））
  * 若要把三種不同規則的 data 都製作出來的話，記得在不更改 FNAME 的情況下，把 'RuleMap', 'Random', 'Intermediate' 都執行一輪。
* FNAME: 會是接下來資料儲存的位址
* STEP_TOTAL：表示的是在同一個規則的同一次 trial 中，agents 會走幾次
* SET_UP_MAZE_TOTAL: 表示在同一個規則下，要做幾次不同的 trials

### 實際運行

* generate_social_score: 做出在 'RuleMap' 規則時會使用到的 social scores
  * 檔案會存在 scripts/data/*FNAME*/log/social_score 中
* simulation_data_dynamic：會把此次規則下的路徑變化模擬出來
  * 結果會儲存在：data/FNAME/move_type/FNAME_1.txt ~ data/FNAME/move_type/FNAME_{SET_UP_MAZE_TOTAL}.txt中
  * 在 txt 檔案中，會有 maze 初始的示意圖，以下會有每個 agents 移動的座標變化。若是有三個 agents ，則前三個座標值表示三個 agents 分別的初始座標，而接下來三個則依序是三個 agents 在下一時間點的座標
* for CASE_TO_RUN in range(1, 21): run_logic_simulation
  * 這段程式碼會製作 Logic rule 的 txt 檔（儲存為 data/sim1/logic/logic_case{CASE_TO_RUN}.txt）
  * 這裡的 case 所對應的規則是依照一下規則來制定的：
  * Agent 的數字變化（Agents 在移動時，顯示的數字會變化，但會固定其基偶性）
    * 若 case // 12 == 0: Agent A 是 Odd, Agent B 是 Odd, Agent C 是 Even
    * 若 case // 12 == 1: Agent A 是 Odd, Agent B 是 Even, Agent C 是 Odd
    * 若 case // 12 == 2: Agent A 是 Even, Agent B 是 Odd, Agent C 是 Odd
  * 而 Agent 移動的規則遵循著：若指定兩個 Agents 是同樣向左或向右移動的，則第三個 agents 會向下移動；反之若兩 Agents 是一左一右的移動的，則第三個 Agents 會向上。不過為了簡化，我們統一在同一次規則中使用同一種移動模式：
    * 若 case % 12 == 1:   則 A, B, C 分別移動方向為 ['L', 'L', 'D']
    * 若 case % 12 == 2:   則 A, B, C 分別移動方向為 ['D', 'L', 'L']
    * 若 case % 12 == 3:   則 A, B, C 分別移動方向為 ['L', 'D', 'L']
    * 若 case % 12 == 4:   則 A, B, C 分別移動方向為 ['R', 'R', 'D']
    * 若 case % 12 == 5:   則 A, B, C 分別移動方向為['D', 'R', 'R']
    * 若 case % 12 == 6:   則 A, B, C 分別移動方向為['R', 'D', 'R']
    * 若 case % 12 == 7:   則 A, B, C 分別移動方向為['L', 'R', 'U']
    * 若 case % 12 == 8:   則 A, B, C 分別移動方向為['U', 'L', 'R']
    * 若 case % 12 == 9:   則 A, B, C 分別移動方向為['U', 'R', 'L']
    * 若 case % 12 == 10: 則 A, B, C 分別移動方向為['L', 'U', 'R']
    * 若 case % 12 == 11: 則 A, B, C 分別移動方向為['R', 'U', 'L']
    * 若 case % 12 == 0:   則 A, B, C 分別移動方向為['R', 'L', 'U']
  * 其中，雖然以上規則可產生 3* 12 種 rule，因為我們只需要 20 種不同的 rule，我們取前二十種，分別表示為 case 1 ~ 20

### 畫圖和儲存圖片

* 先載入 parse_coordinates, ..., process_maze_file
* 調整參數（在 TXT 區的參數）：
  * SIM_FOLDER     = FNAME
  * RUN_TYPE       = 'random'（表示為要繪製的規則類型）
  * 簡單調整此區 FILES_TO_SHOW, STEPS_TO_SHOW 參數可以快速以 TXT 的方式檢驗規則和移動方式是否符合預期
  * 不過目前有關 logic 規則的 txt 繪圖仍未修正，無法使用
* 下一區的程式碼則是正式生成圖檔的區塊，確認好參數後便可直接執行：
  * 圖檔會儲存為：data/FNAME/{move_type}_plot/step_plot_{FNAME}_{trial_num} 資料夾中，而其中會有每一步的圖片
  * （Logic Rule 的圖檔會儲存為 data/FNAME/logic_plot/step_plot_logic_case{case_num}）
