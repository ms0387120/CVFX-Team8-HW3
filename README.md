# CVFX-Team8-HW3-GAN-Dissection

## Generate images with GANPaint
* GANPaint draws with object-level control using a deep network. Each brush activates a set of neurons in a GAN that has learned to draw scenes
* 測試網址：[GANPaint](http://gandissect.res.ibm.com/ganpaint.html?project=churchoutdoor&layer=layer4)

![](https://i.imgur.com/RORvol2.jpg)
![](https://i.imgur.com/pDR5LTE.jpg)
![](https://i.imgur.com/KQVCj3b.jpg)
![](https://i.imgur.com/VN7MNnS.jpg)
> It is impossible to generate grass in the sky and **the GAN know that**.

![](https://i.imgur.com/EBBkvIE.jpg)

* 目前來看，除了 Remove bricks 主觀看起來效果不怎麼好，其餘的都補圖的還算正常跟合理


---
## Dissect any GAN model and analyze what you find

### 1. layer1
#### living room：
![](https://imgur.com/3OTnaOq.png)
#### bed room：
![](https://imgur.com/EQCiuFc.png)
#### 觀察：
* 客廳

    - 圈選出來的物件都很局部細微，看不太出來特別想學什麼類型的物件
    - 

* 臥室

    - 圈選的物件以局部的床、枕頭居多
    - 效果比 layer1 的客廳略好

### 2. layer4
#### living room：
![](https://imgur.com/yaoQO35.png)

#### bed room：
![](https://imgur.com/6Wj5QfG.png)
#### 觀察：
* 客廳

    - ？？？

* 臥室

    - 圈選的物件以床、枕頭、櫃子、窗戶居多
    - 物件辨識的框種類繁多
    - 效果比 layer1 好

### 3. layer7
#### living room：
#### bed room：
![](https://imgur.com/G9R0RB6.png)
#### 觀察：
* 客廳

    - ？？？

* 臥室

    - 圈選的物件仍以床、枕頭居多
    - 但框出來的區域沒有很完整，反而都是大物件局部的區域
    - 效果感覺比 layer4 的略差，但比 layer1 略好 


---
## Compare with other method
### 1. Globally and Locally Consistent Image Completion
* 論文參考自這篇：[Globally and Locally Consistent Image Completion](http://iizuka.cs.tsukuba.ac.jp/projects/completion/data/completion_sig2017.pdf)
* 使用CNN來進行圖像修復，與patch-based方法(ex:PatchMatch)相比，nn-based 方法能生成出沒有在圖片其他地方生成過的圖像，能使填補的成果完整度更高，適用於結構強度高、精細的圖片修復：例如人臉。此篇使用CNN網絡作為基礎，設計兩部分網絡，一部分用於生成圖像，一部分用於鑑別生成圖像是否與原圖片一致。

#### 詳細原理：
Network的架構可以分為兩部分
* 1.生成圖片:GL採用12層卷積網絡對有缺快的原始圖片進行encoding，得到一張原圖16分之一大小的網格。然後再對該網格採用4層卷積網絡進行decoding，從而得到復原圖像。
* 2.做判斷：由兩個Discriminator構成，一個是 Global Discriminator，另一個是Local Discriminator。Global Discriminator將完整圖像作為輸入，辨識場景大局的一致性，而Local Discriminator僅在以填充區域為中心的原圖像4分之一大小區域上觀測，辨識局部一致性。

![](https://imgur.com/gPeHysA.png)
#### 測試：

![](https://imgur.com/2KywRji.png)


---
### 2. Partial Convolution Layer for Padding and Image Inpainting
* 以往使用深度學習進行圖像修復的任務，通常會採用CNN修補圖片後，看起來都有點不自然，比方說顏色差異或模糊，因為這種方式會將正常的 pixels 與空洞的 pixels 都視為同樣的方式去處理
* 論文參考自

    - [Partial Convolution based Padding](https://arxiv.org/pdf/1811.11718.pdf)
            
        - 利用 **partial convolution based padding**，可將 padding region 視為 holes，將 original input 視為 non-holes。具體來說，在卷積操作期間，基於 padding region 和 convolution sliding window area 之間的比例，在圖像邊界附近對卷積結果進行重新加權，使得此方法的效果比一般 zero padding 更佳，附圖提供實驗結果
        - ![](https://i.imgur.com/sz2weYw.png)

    - [Image Inpainting for Irregular Holes Using Partial Convolutions](https://arxiv.org/pdf/1804.07723.pdf)
        
        - 作用就是讓 CNN 在傳遞時有個 **Mask** （區分空洞與否）作輔助，就可知道此 pixel 是正常或不正常的話，就能進行更為精準的修改
        - 提出一個自動傳導 mask 的機制，透過此機制讓**上述提出的PConv**可以讓下一層知道哪邊還需要進行修復
        - 使用 U-Net 進行圖像修復，將 Conv 改為 PConv 可達到更好的準確度
        - 以往的圖像修復往往都是處理長方形的缺口，而此篇是第一個展示**修復不規則缺口的圖像修復任務**
        - 提出一個不規則 mask 的資料集，並且公開出來讓大家可以評估圖像修復任務

* NVIDIA 有提供網站供線上 Demo [Image Inpainting](https://www.nvidia.com/research/inpainting/) 
* 測試如下：
![](https://i.imgur.com/dDalisY.jpg)


---
## Conclusion

* **GANPaint**

    - 優點

        - 修圖空間可以任意
        - 天空、草地、圓頂等補圖/移除效果，主觀上都是合理正常
        - 補圖時，如果元素選在不應該出現的地方，GAN會知道，且不會亂生成

    - 缺點

        - 需要選好元素、移除/增加的動作才可執行，不夠便利使用
        - Remove Bricks 效果不好
        - 只能從網站上給的圖片進行測試，不能用自己的照片

* **Inpainting**

    - 優點

        -     

    - 缺點

        - 



* **Partial Convolution Layer for Padding and Image Inpainting**

    - 優點

        - 修圖空間可以任意
        - 生成出來的圖，直觀上正常合理
        - 效果完勝 GANPaint、Inpainting
        - 便利使用，不用刻意選取素材或手動填選操作動作
        - 任意照片皆可操作

    - 缺點

        - ？？？
        
 

