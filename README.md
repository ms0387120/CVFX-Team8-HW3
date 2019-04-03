# CVFX-Team8-HW3-GAN-Dissection

## Generate images with GANPaint
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
#### bed room：
![](https://imgur.com/EQCiuFc.png)
#### 觀察：
### 2. layer4
#### living room：
#### bed room：
![](https://imgur.com/6Wj5QfG.png)
#### 觀察：
### 3. layer7
#### living room：
#### bed room：
#### 觀察：

---
## Compare with other method
### 1. Inpainting
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
