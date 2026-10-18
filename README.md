# lidar-camera-autonomous-driving
RGB and LiDAR sensor fusion for autonomous driving control prediction using PyTorch.
RGB-LiDAR 多模態融合之自動駕駛控制預測

使用 PyTorch 實作 RGB 影像與 LiDAR 資訊融合，探索多模態深度學習於自動駕駛控制預測的應用。

一、專案簡介

本專案旨在探索如何整合 RGB 相機影像與 LiDAR 衍生表示，透過深度學習模型進行自動駕駛控制訊號預測。

本專案實作兩種不同的多模態特徵融合架構：

特徵串接模型（Feature Concatenation）：分別擷取 RGB 與 LiDAR 特徵，再透過特徵串接進行融合。
跨模態注意力模型（Cross-Modal Attention）：利用雙向多頭注意力機制，讓 RGB 與 LiDAR 特徵彼此交換資訊。

本專案希望透過不同的特徵融合方式，探索視覺資訊與空間感測資訊整合於自動駕駛控制預測的可行性。

二、模型架構
模型一：CNN 特徵串接融合

此模型使用兩個獨立的 CNN Encoder，分別處理 RGB 影像與 LiDAR 輸入，取得各自的特徵表示，再透過串接與全連接層進行融合，最後輸出三個控制相關預測值。

模型流程：

RGB 影像
   ↓
CNN Encoder
   ↓
RGB 特徵 ─────────┐
                  ├→ 特徵串接 → 全連接層 → 控制預測
LiDAR 輸入 ───────┤
   ↓              │
CNN Encoder       │
   ↓              │
LiDAR 特徵 ────────┘


此架構作為基準模型，用於建立多模態特徵融合的初步實作。

模型二：跨模態注意力融合（Cross-Modal Attention）

第二種架構進一步導入 Multi-Head Attention，讓 RGB 與 LiDAR 特徵透過雙向注意力機制進行資訊交互。

主要處理流程：

使用獨立的 CNN Encoder 擷取 RGB 與 LiDAR 特徵。
將特徵圖轉換為 Token 序列。
執行 RGB-to-LiDAR Cross-Attention。
執行 LiDAR-to-RGB Cross-Attention。
聚合融合後的特徵，並透過預測頭輸出控制相關數值。

模型流程：

flowchart TB
    A[RGB 影像] --> B[CNN Encoder]
    C[LiDAR 輸入] --> D[CNN Encoder]

    B --> E[RGB Tokens]
    D --> F[LiDAR Tokens]

    E <--> G[雙向 Cross-Attention]
    F <--> G

    G --> H[特徵聚合]
    H --> I[Control Head]
    I --> J[控制預測]

--
RGB 影像                         LiDAR 輸入
   |                                 |
   v                                 v
CNN Encoder                     CNN Encoder
   |                                 |
   v                                 v
RGB Tokens                      LiDAR Tokens
   |                                 |
   +----------+     +----------------+
              |     |
              v     v
       雙向 Cross-Attention
       - RGB-to-LiDAR
       - LiDAR-to-RGB
              |
              v
           特徵聚合
              |
              v
         Control Head
              |
              v
           控制預測
--
RGB 影像 → CNN Encoder → RGB Tokens
                              ↕
                    雙向 Cross-Attention
                              ↕
LiDAR 輸入 → CNN Encoder → LiDAR Tokens
                              ↓
                         特徵聚合
                              ↓
                       Control Head
                              ↓
                         控制預測


本架構為跨模態融合的實驗性實作，目的在於探索注意力機制於不同感測器特徵整合上的應用。

三、使用技術
程式語言： Python
深度學習框架： PyTorch
特徵擷取： Convolutional Neural Network（CNN）
特徵融合： Feature Concatenation
注意力機制： Multi-Head Attention、Cross-Modal Attention
應用領域： 自動駕駛、多模態深度學習、感測器融合
四、初步訓練結果

跨模態注意力模型目前的訓練 Loss 如下：

Epoch	Training Loss
第 1 輪	0.066959
第 2 輪	0.032174
第 3 輪	0.019264

在目前三輪訓練中，Training Loss 呈現下降趨勢，顯示模型對訓練資料的擬合誤差逐步降低。

但訓練 Loss 並不能單獨證明模型具有良好的泛化能力，也不能據此認定跨模態注意力模型優於特徵串接模型。後續仍需在一致的資料切分、損失函數與評估條件下進行比較。

五、後續研究方向
建立訓練集、驗證集與測試集的完整評估流程。
在一致的實驗條件下，比較特徵串接與跨模態注意力兩種架構。
使用 MAE、RMSE 等指標評估控制預測誤差。
探討位置編碼與 Token 數量對模型效能及計算成本的影響。
視覺化預測結果，分析不同駕駛情境下的模型誤差。
進一步探討多模態感測器融合於自動駕駛感知與控制的應用。
六、專案檔案
rgb_lidar_autonomous_driving.ipynb：包含模型實作、訓練流程與相關實驗程式碼的 Jupyter Notebook。
七、目前限制

本專案目前屬於深度學習模型的實驗性實作。現有訓練結果尚不足以證明模型在未見資料上的泛化能力，也未證實模型可安全應用於真實道路環境。

後續將透過更完整的驗證、模型比較與誤差分析，逐步評估不同多模態融合方法的實際表現。

八、專案目標

透過本專案，實作並探索深度學習、多模態特徵融合與注意力機制，建立對 AI 模型設計、訓練流程及實驗評估的理解，並作為後續人工智慧相關研究與應用的基礎。
