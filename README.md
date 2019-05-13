ISA525700 Computer Vision for Visual Effects
Assignment 5: multi-view 3D visual effects
Team 24
===


## Abstract 
- 本次作業主要為拍攝不同角度的camera的照片，進行alignment後，以產生3D 視覺效果，主要共分成三個部分：Motion parallax, Stop motion, Live photo。
- 其中Motion parallax的部分有比較加上後製後並比較有後製和沒後製的效果。

## Table of Contents
1. [Introduction](#Introduction)
2. [Take multi-view images by yourselves](#Take-multi-view-images-by-yourselves5)
3. [Show alignment results and Generate multi-view 3D visual effects](#Show-image-alignment-results-and-Generate-the-multi-view-3D-visual-effects35)
4. [Exploit creativity to add some image processing](#Exploit-creativity-to-add-some-image-processing-to-enhance-effect10)
5. [Conclusion](#Conclusion)

## Introduction
- 不同的角度的camera有不同的intrinsic matrix以及extrinsic matrix如下圖。
![](https://i.imgur.com/Zde30rn.png)
其中外部參數，表示將物體的世界座標轉換到camera的座標，內部參數則為將camera座標系(3D)投影到圖片的座標系(2D)，其中投影的方式又分為兩種perspective projection和Orthographic projection。perspective projection會使，較遠的物體較小，Orthographic projection則是平行投影，物體實際大小一致，則投影出來的影像大小便一致。因此相機拍出來的正常應多屬於perpective projection
![](https://i.imgur.com/4NawjGJ.png)

- 而本次作業理論上，需要將不同物體投影在不同的camera上(不同的extrinsic matrix)，而兩座標系的轉換，可用一transformation matrix簡化取代。因此本次作業計算上，為簡化實作流程，以找出affine matrix簡化取代camera之間的投影。
- 因為本次作業可視為，同一相機不同view的照片align，因此如欲增進效果，可以利用feature extraction抽出兩張不同視角的圖片，並找出matching points後，利用Fundamental matrix來過濾掉mis-match  points(如果是matching points必須符合xFx'=0)，算是提供除了RANSAC之外的另一個過濾掉matching錯誤點的方式(如下圖)。
![](https://i.imgur.com/ArgLWIO.png)




## Take multi-view images by yourselves(5%)
### Motion parallax
|圖1|圖2|
|--|--|
|![](https://i.imgur.com/521gmFT.png)|![](https://i.imgur.com/XqpZF64.png)|
### Stop motion
| 圖1 | 圖2 |圖3|圖4|圖5|
| ------ | ----------- |---|--|--|
|![](https://i.imgur.com/xyT9PZo.jpg)|![](https://i.imgur.com/wVgpbpR.jpg)|![](https://i.imgur.com/5w7tkc3.jpg)|![](https://i.imgur.com/dLjmbnX.jpg)|![](https://i.imgur.com/GB1SH8I.jpg)|
| 圖6 | 圖7 ||||
|![](https://i.imgur.com/kFOtSQ2.jpg)|![](https://i.imgur.com/WdMHSbO.jpg)


### Live photo
| 圖1 | 圖2 |圖3|圖4|圖5|
| ------ | ----------- |---|--|--|
|![](https://i.imgur.com/5VxxDpu.png)|![](https://i.imgur.com/0c7WhoT.png)|![](https://i.imgur.com/RVuLlJD.png)|![](https://i.imgur.com/Aa5ib2b.png)|![](https://i.imgur.com/QO2L7Em.png)|
| 圖6 | 圖7 |圖8|圖9|圖10|
|![](https://i.imgur.com/9RwRh3q.png)|![](https://i.imgur.com/G3sqvPT.png)|![](https://i.imgur.com/uEj1CXi.png)|![](https://i.imgur.com/nR0nI2E.png)|![](https://i.imgur.com/IXtoAo0.png)|

## Show image alignment results and Generate the multi-view 3D visual effects(35%)
### Motion parallax
#### 方法
目標是讓畫面中特定景深的物體不變，其他像素作轉換，以下目標align的照片。

處理步驟為:
   1. 對 img1 和 img2 做影像去背，這次使用的是 [Background Burner](https://api.bonanza.com/docs/background_burner/reference)提供的 API ，使用api不使用opencv主要是因為照片處理效果較佳。
   2. 產生 img1' 和 img2' ，再透過其求出Homography H
   3. 對img2 使用 H 進行 warping
   4. 製作GIF
#### align 結果   
|img2 align前| img2 align後 |
|---| ------ |
|![](https://i.imgur.com/XqpZF64.png)|![](https://i.imgur.com/e3dEF70.jpg)|

#### 完成結果的GIF

![](https://i.imgur.com/5q1KSzY.gif)
#### 比較分析
|原始圖片GIF|align後GIF|
|--|--|
|![](https://i.imgur.com/yBNo9im.gif)|![](https://i.imgur.com/5q1KSzY.gif)|

- 完成結果可明顯發現看板沒有移動，而其前景與後景都有旋轉的效果，讓看板有很不錯的3D立體感受。
- img2 warping後變得有稍微模糊，可能與img2內看版在照片中的比例比img1小，所以warping有放大造成。


### Stop motion
#### 方法
該項目需要圍繞著一個物體拍攝不同角度的照片最後再align起來。
本次所使用的方法為，將多張照片兩兩計算affine matrix並儲存，接著以最中心的照片為基準，分別計算每張照片需要對到最中心照片的affine matrix並將之乘起來。最後再crop掉多餘的部分。
#### align 結果
| aligned image | crop image |
| ------ | ----------- |
|![](https://i.imgur.com/VgOaoJy.png)|![](https://i.imgur.com/VMCyRci.png)
|![](https://i.imgur.com/LISN1A2.png)|![](https://i.imgur.com/qb2fwUW.png)
|![](https://i.imgur.com/6E4WfSv.png)|![](https://i.imgur.com/rvOtmkl.png)
|![](https://i.imgur.com/r6UE0nZ.png)|![](https://i.imgur.com/0ujOfTY.png)
|![](https://i.imgur.com/shayDzw.png)|![](https://i.imgur.com/bb0UFiV.png)
#### 完成後的GIF
![](https://i.imgur.com/NQUREXT.gif)

#### 比較分析

|原始圖片GIF|align後GIF|
|--|--|
|![](https://i.imgur.com/065BmF3.gif)|![](https://i.imgur.com/NQUREXT.gif)|

- 進行align後明顯改善，相機的移動感覺上更像是環繞物體的原型。
- 相機拍攝物體時，每張照片中心不同，可能是導致視角忽近忽遠的原因。



### Live photo
#### 方法
本項目需要拍攝數張照片，並且讓前景的照片動，背景的照片不動，該章可依照Motion parallax的方式先將前景過濾，並對齊背景。但其實本項目直接align即可，並固定抽樣疊加成GIF即可。


#### align 結果
取部分圖片編號結果 

|圖片編號|align前|align後 |
|---| ------ |--|
|1|![](https://i.imgur.com/5VxxDpu.png)|![](https://i.imgur.com/lcPoDGv.jpg)
|2|![](https://i.imgur.com/0c7WhoT.png)|![](https://i.imgur.com/HnSUGXq.jpg)|
|4|![](https://i.imgur.com/Aa5ib2b.png)|![](https://i.imgur.com/pNxg1a9.jpg)|
|7|![](https://i.imgur.com/G3sqvPT.png)|![](https://i.imgur.com/BpAVHBY.jpg)

#### 完成後的GIF
![](https://i.imgur.com/IKLir8x.gif)
#### 比較分析
|原始圖片GIF|align後GIF|
|--|--|
|![](https://i.imgur.com/rr5DIZq.gif)|![](https://i.imgur.com/IKLir8x.gif)|

- 圖片選用非常適合live photo ，導致有沒有 align 效果差不多。
- 結果顯示在製作 live photo 特效時，拍攝手法太差的時候會導致align也沒有用，拍攝結果太好會導致align不明顯。


## Exploit creativity to add some image processing to enhance effect(10%)

### Motion parallax
#### 改進
為了增強效果，
先從抓出去背後的照片

|img1'|img2'|
|--|--|
|![](https://i.imgur.com/U3nY7f9.png)|![](https://i.imgur.com/KIVLJ9k.jpg)|
用sift比對match結果

|SIFT match|
|--|
|![](https://i.imgur.com/hw0rUIx.png)|

發現有很多錯誤的比對線，因此用photoshop進行更仔細的去背，產生 img1" 、 img2"。

|img1"|img2"|
|--|--|
|![](https://i.imgur.com/Ubuelhu.png)|![](https://i.imgur.com/GXDM6Z2.png)|
確認比對結果

|SIFT match|
|--|
|![](https://i.imgur.com/N2Ctnoy.png)|

最後再進行求Homography H ，並warping img2。

|img2 align前| img2 align後 |
|---| ------ |
|![](https://i.imgur.com/XqpZF64.png)|![](https://i.imgur.com/7wS32hW.jpg)|
#### 後置後完成結果的GIF
![](https://i.imgur.com/WqTIKKp.gif)


#### 比較分析

|原始align結果|後置後align結果|
|--|--|
|![](https://i.imgur.com/5q1KSzY.gif)|![](https://i.imgur.com/WqTIKKp.gif)|


- 發現用photoshop 完整去除看板以外像素後，效果反而變差很多，推測是因為沒去除前面草地時，求出的 H 對 img2進行 warping後在比看板近的前景上變化較小，而因此造成的輕微看板扭曲十分不明顯，反而是為了讓看板更完美的match img1而去掉所有除看板以外的畫素會造成前景扭曲嚴重，產生不自然的感覺
- 原本以為matching時的比對線出現很多錯誤會造成求出的 H 不準確，結果並不然，推測是因為在程式碼中有使用 ransac 並且取用的 key point只選前三分之一


## Conclusion

- 照片的拍攝手法與照片選擇是這次作業3D效果是否成功製作的，拍得好的時候如製作 live photo 選用的照片，因為晃動少，且主題明顯，align 其實可有可無，因為本身照片製作的GIF就有 live photo的3D 效果。
- 製作 Motion parallax 的時候，雖然目標是固定看板，但其實如果只比對看板不一定會產生最好的3D效果，反而前景扭曲稍為嚴重。
- 進行align的時候，選用的照片主題必須勁量在照片中心，效果最佳
