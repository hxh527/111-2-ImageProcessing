##Q1：Please prepare four images: (a) dark; (b) bright; (c) low contrast; (d) high contrast, like the following images. The examples are shown as follows.<br>
*(a) Describe how you generate your four images. Show the images together with the corresponding histograms.<br>
        - 像素值的線性變換公式：$g(x)=\alpha f(x)+\beta$
            - 其中 $f(x)$ 是原始圖片的像素值， $g(x)$ 是變換後的圖片像素值， $\alpha$和 $\beta$是線性變換的參數。 $\alpha$是代表對像素值進行縮放的比例因子， $\beta$ 是代表對像素值進行平移的量。
            - 當 $\alpha<1$時，圖片的對比度會降低，當 $\alpha>1$時，圖片的對比度會增加
            - 當$\beta<0$時，圖片會變暗，當$\beta>0$時，圖片會變亮。
        - 我設計了一個為convert_scale_abs(img, alpha, beta)的函式，這個函式接受三個參數：img是待處理的圖片，alpha是線性變換的斜率，beta是線性變換的截距。
        - 以下是實作結果
            
            ![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/f9bee30f-7e96-414c-8be3-3ca6e6745c20/Untitled.png)
            
        - 結果討論
            - Original Image
                - 原始的彩色照片，而後續為了方便操作，在讀入數據時就將照片轉為黑白進行儲存。
                    
                    ![image.jpeg](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/61acd1e0-6e0a-4cf1-8c3d-5f33fec70e2e/image.jpeg)
                    
            - Dark Image (alpha=0.8, beta=-50)
                - 在處理暗調的圖片時，將beta設為-50，代表對像素值進行負偏移；alpha設為0.8。
            - Bright Image (alpha=1.1, beta=50)
                - 在處理亮調的圖片時，先將beta設為50，代表對像素值進行正偏移，但此時圖片亮度太高，導致細節不清晰，故後續將alpha值設為1.1。
            - Low contrast Image (alpha=0.5, beta=50)
                - 在處理低對比的圖片時，先將alpha設為0.5，對像素值進行縮小，此時雖然完成了低對比，但圖片會太暗無法清楚辨識，故又將beta提高到50。
            - High contrast Image (alpha=1.7, beta=-15)
                - 在處理高對比的圖片時，先將alpha值設為1.7，對像素值進行放大，此時雖然完成了高對比，但圖片會太亮而有點過曝，以及直方圖數值無法清楚辨識，故降低了beta到-15。
*(b) Find the transfer curves to enhance these images, show the results and histograms as well. Please consider using contrast stretching methods such as power curves, as well as equalization techniques. Additionally, please provide a comparison and discussion of the different methods used.
        - Power curves
            - 函式設計
                - power_low(image,gamma)，此函式接受兩個參數：image是待處理的圖片，gamma是用來調整圖片的參數。
                - 首先，會先將黑白的圖片進行歸一化處理，使所有像素值都介於0和1之間。
                - 而後續會利用公式： $O=I^\gamma$
                    - 而公式中的 $I$是原始圖片的像素值， $O$是增強後的圖片像素值， $\gamma$ 是一個參數，用於控制曲線的形狀。
            - 以下是實作結果
                
                ![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/a6dee498-09bd-4b57-bb3b-eabc0c8c1922/Untitled.png)
                
                ![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/d1f8bb9b-fc57-44e7-b5af-df44c113c1a8/Untitled.png)
                
                ![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/5c464d0c-110c-4511-a93d-26e31aa3e0da/Untitled.png)
                
                ![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/99f42f7d-a2a7-4cab-b034-ec346b1c96cc/Untitled.png)
                
                ![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/f8eb358e-588d-46a5-81ac-efb6d2d6fca8/Untitled.png)
                
            - 結果討論
                - 當gamma大於1並且逐漸增加時，圖片的細節會越來越明顯，並且亮度會降低，可能需要搭配可以調整亮度的函式使用，便能形成高對比度並細節清楚的照片。
                - 當gamma小於1時，圖片的細節明顯降低，並且亮度有些微的提升。在使用像bright image亮度本身就較高的照片時，可能會需要調整亮度，才不會導致過曝的情況發生。
        - Equalization
            - 函式設計
                - hist_equalization(img)，此函式接受一個名為img的參數，此為待處理的圖片。
                    - 首先，會先計算出圖片的直方圖，再計算直方圖的累積和(CDF)，再將累積和歸一化，得到概率密度函數(PDF)，並且創建新的強度映射函數。
                - apply_intensity_mapping(img, map_func)，此函數接受一個名為img，為待處理的圖片，以及map_func，為先前計算出的強度映射函數。
                    - 此函式會遍歷每個像素，並將該像素的強度值映射到新的值，最後返回應用了強度映射的新圖片
            - 以下是實作結果
                
                ![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/38cd7199-8368-4c67-9687-7bb62823a749/Untitled.png)
                
                ![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/ee8564f1-c8ea-4e12-a5ac-5f88bd717d61/Untitled.png)
                
                ![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/c1d93480-4dc0-4128-bd2d-078ebb333a90/Untitled.png)
                
                ![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/41a0ae23-e92a-41f8-ad36-33d82d977fcc/Untitled.png)
                
                ![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/7c68466e-a4bf-482c-961c-1ec324a43b92/Untitled.png)
                
            - 結果討論
                - 左側為每張照片的原始圖像以及直方圖，右邊則是經過處理後的圖像，可以感受到每張圖片都被增強了細節，並且直方圖也比先前更加的平均，但仍然有改善的空間。
        - power curves與Equalization的比較
            
            兩種方法皆可以很好的進行細節的增強，但在觀察圖片以及直方圖後，發現Equalization的直方圖更加的平均，並且整張圖片的表現也更和諧。
            
- Q2：Most area of the noon is nearly dark because there is no light there. Enhance the selected image by using equalization and specification, compare the results and give a discussion.
    - 函式設計
        - 在實作equalization 的部分，我使用了與Q1(b)一樣的函式：hist_equalization(img)以及apply_intensity_mapping(img, map_func)。
        - 在實作specification的部分
            - 首先，先使用cv2.createCLAHE()創建CLAHE。並且設置ClipLimit的值限制局部區域中像素值的峰值，以避免過度增強對比度，tileGridSize是指圖片被分成多少個局部區域進行均衡化處理。
            - 接下來使用apply(img, ref)將CLAHE應用到輸入圖片中，img為輸入圖像，而ref是參考圖像均衡化後的直方圖。
    - 以下是實作結果
        
        ![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/a0efbe8f-1d86-4805-b00a-b46b520372ff/Untitled.png)
        
    - 結果討論
        - Original Image
            - 在尋找圖片時花費了一點時間，網路上有些圖片會將偏黑色的部分去除，只留下白色的弦月。而這樣的圖片不管如何調整，都沒辦法顯現出完整的月亮，故最後只能找了一張較為明顯的照片進行調整。
        - Equalization
            - 在進行均衡化後，觀察直方圖發現原本靠近左側的數值有好好的平均向右側移動，可是數值偏低，觀察有點困難。
        - Specification
            - 此項目的參考圖片是使用第一題的圖片，在進行操作後，直方圖也有平均分散了一些，但與Equalization 比較，仍然是前者顯現出的月亮更為清晰，並且直方圖也更為平均。
