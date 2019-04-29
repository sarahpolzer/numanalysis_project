# Introduction

The Two Dimensional Cosine Transform can be used to compress small blocks of images.The compression results in the loss of information, but the transform is designed so that the information ignored is the information that the eye is least sensitive to.

The two-dimensional Discrete Cosine Transform is simply the one dimensional DCT applied in two dimensions, one after the other. It is used to interpolate data on a two-dimensional grid, in a straightforward analogy to the one dimensional case. In the case of images, the two-dimensional grid represents a block of pixel values with either grayscale or color intensities.

The 2D Discrete Cosine Transform (2D-DCT) of the 2D n by n matrix is the matrix Y = C*X*C_transpose. The inverse of the 2D-DCT is easy to express in terms of the DCT matrix C. Since Y=C*X*C_transpose and C is orthogonal, the X is recovered as X=C_transpose*Y*C

2D Cosine Transform 

![](/2DCT.PNG)

Finding the 2D-DCT of an image and inverting it allows you to interpolate pixels of the image. Inverting the orthogonal 2D-DCT is similar to interpolation. Interpolation recovers the original data points from functions that are constructed with interpolating coefficients that come from the transform. Because C is an orthogonal matrix, the inversion of the 2D-DCT can be used to interpolate original data points. 


Example:
If you gather an 8 by 8 block of pixels from an image and convert them to gray scale, you can create a matrix such as X. 
![](/x.PNG)

If you apply the 2D-DCT you can get the matrix Y. 
![](/Y.PNG)

If you apply low pass filtering, and then find the inverse DCT, you can invert the transformed matrix and find the image A. 

![](/a.PNG)

** Quantization **

Quantization is a selective method of low pass filtering. It is a way to retain low accuracy versions of pixel coefficients at a lower storage cost. The human eye is least sensitive to higher spatial frequencies - so quantization is a good method of low pass filtering. The idea is to assign fewer bits to store information about the lower right corner of the transform matrix instead of just throwing it away. 

Quatization is performed like so: you are rounding y/q to the nearest integer : 

![](/quantization.PNG)

If quantization of the transformed image is performed. Y will be replaced by the matrix below. 


![](/y_sub_Q.PNG)

The transformed matrix Y is divided by the quantization matrix. The quantization matrix is pictured below. A coefficient p is included. As p increases, the more lossy the compression will be with quantization. 


![](/quantization_matrix.PNG)


After the matrix Y is divided by the quantization matrix, it is dequantized and the inverse-DCT is found of the dequantized matrix. The pixel values are converted to uint8 integers and the finished compressed image is ready to be displayed!


**Note** 

The quantization matrix is different for JPEG images. The quantization matrix for JPEG images is pictured below.

![](/JPEG_quantization_matrix.PNG)





**Question 3 Part 1:**

Obtain a grayscale image ﬁle of your choice, and use the imread command to import into MATLAB. Crop the resulting matrix so that each dimension is a multiple of 8. If necessary, converting a color RGB image to gray scale can be accomplished by the standard formula (11.15).


```matlab

x = imread('baboon.png');
x = imresize(x,[256 256]);
r=x(:,:,1);g=x(:,:,2);b=x(:,:,3);
xgray=0.2126*r+0.7152*g+0.0722*b;
xgray=uint8(xgray);
imagesc(xgray);colormap(gray)


```

Output

![A baboon](/baboon_q3_p1.png)




**Question 3a:**
(a) Extract an 8×8 pixel block, for example, by using the MATLAB command xb=x(81:88,81:88). Display the block with the imagesc command


```matlab

%Question 3 Part a
x_eight = xgray(1:8,1:8);
imagesc(x_eight);colormap(gray);

```


![Another baboon](/baboon_q3_pa.png)


**Question 3b:**


(b) Apply the 2D-DCT



```matlab

C = zeros(8);
for i=1:8
for j=1:8
C(i,j)=cos((i-1)*(2*j-1)*pi/(2*8));
end
end
C=sqrt(2/8)*C;
C(1,:)=C (1,:)/sqrt(2);

```


```matlab

%Question 3 Part 3
Xd=double(x_eight);
Xc=Xd-128;
y = C*Xc*C';



```




**Question 3c:**

(c) Quantize by using linear quantization with p=1,2, and 4. Print out each YQ. 

```matlab 

p = 1.0;
Q=p*8./hilb(8);
Yq=round(y./Q);
imagesc(Yq);colormap(gray);
Yq

```

p=1.0


Yq = 

```matlab


Yq =

   -41    -4    -1     2    -2    -1     0     0
    -2     4     1     0     0     1     0     0
    -2     1     1     0     1     0     0     0
     2    -2     0     0     0     1     0     0
     0     0     1     0     0     0     0     0
     0     0     0     0     0     0     0     0
     0     0     0     0     0     0     0     0
     0     0     0     0     0     0     0     0


```

p=2.0

```matlab

Yq =

   -21    -2     0     1    -1    -1     0     0
    -1     2     0     0     0     0     0     0
    -1     1     0     0     0     0     0     0
     1    -1     0     0     0     0     0     0
     0     0     0     0     0     0     0     0
     0     0     0     0     0     0     0     0
     0     0     0     0     0     0     0     0
     0     0     0     0     0     0     0     0





```
p=4.0

```matlab 
Yq =

   -10    -1     0     0     0     0     0     0
    -1     1     0     0     0     0     0     0
     0     0     0     0     0     0     0     0
     0    -1     0     0     0     0     0     0
     0     0     0     0     0     0     0     0
     0     0     0     0     0     0     0     0
     0     0     0     0     0     0     0     0
     0     0     0     0     0     0     0     0


```

**Question 3d:**


(d) Reconstruct the block by using the inverse 2D-DCT, and compare with the original. Use MATLAB commands colormap(gray) and imagesc(X,[0 255]). 


```matlab
Ydq=Yq.*Q;
Xdq=C'*Ydq*C;
Xe=Xdq+128;
Xf=uint8(Xe);
imagesc(Xf,[0 255]);colormap(gray);

```
Original

![](/original_p_5.png)

New

![](/new_p_5.png)



(e) Carry out (a)–(d) for all 8×8 blocks, and reconstitute the image in each case.

```matlab

new = zeros(256,256);
p = 1.0;

C = zeros(8);
for i=1:8
for j=1:8
C(i,j)=cos((i-1)*(2*j-1)*pi/(2*8));
end
end
C=sqrt(2/8)*C;
C(1,:)=C (1,:)/sqrt(2);

x = imread('baboon.png');
x = imresize(x,[256 256]);
r=x(:,:,1);g=x(:,:,2);b=x(:,:,3);
xgray=0.2126*r+0.7152*g+0.0722*b;
xgray=uint8(xgray);

for i=1:8:256
    for j=1:8:256
       x_eight = xgray(i:i+7,j:j+7);
       Xd=double(x_eight);
       Xc=Xd-128;
       y = C*Xc*C';
       Q=p*8./hilb(8);
        Yq=round(y./Q);
        Ydq=Yq.*Q;
        Xdq=C'*Ydq*C;
        Xe=Xdq+128;
        Xf=uint8(Xe);
        new(i:i+7,j:j+7) = Xf; 
    end
end

imagesc(new);colormap(gray);

```
Baboon image for p=1

![](/baboon_q3_p1.png)


Baboon image for p=2


![](/baboon_q3_p2.png)

Baboon image for p=4

![](/baboon_q3_p4.png)





**4. Carry out the steps of Computer Problem 3, but quantize by the JPEG-suggested matrix (11.25) with p=1.**


```matlab
new = zeros(256,256);
p = 1.0;
n = 8.0;
Q = [16 11 10 16 24 40 51 61;
    12 12 14 19 26 58 60 55;
    14 13 16 24 40 57 69 56;
    14 17 22 29 51 87 80 62;
    18 22 37 56 68 109 103 77;
    24 35 55 64 81 104 113 92;
    49 64 78 87 103 121 120 101;
    72 92 95 98 112 100 103 99];
Q = p*Q;

C = zeros(8);
for i=1:8
    for j=1:8
        C(i,j)=cos((i-1)*(2*j-1)*pi/(2*8));
    end
end
C=sqrt(2/n)*C;
C(1,:)=C (1,:)/sqrt(2);

x = imread('squirrel.png');
x = imresize(x,[256 256]);
r=x(:,:,1);g=x(:,:,2);b=x(:,:,3);
xgray=0.2126*r+0.7152*g+0.0722*b;
xgray=uint8(xgray);

for i=1:8:256
    for j=1:8:256
       x_eight = xgray(i:i+7,j:j+7);
       Xd=double(x_eight);
       Xc=Xd-128;
       y = C*Xc*C';
        Yq=round(y./Q);
        Ydq=Yq.*Q;
        Xdq=C'*Ydq*C;
        Xe=Xdq+128;
        Xf=uint8(Xe);
        new(i:i+7,j:j+7) = Xf; 
    end
end

imagesc(new);colormap(gray);


```


Result

![squirrel](/squirrel_q4.png)


**5. Obtain a color image ﬁle of your choice. Carry out the steps of Computer Problem 3 for colors R, G, and B separately, using linear quantization, and recombine as a color image.** 


```matlab
new = zeros(256,256,3);
p = 1.0;

C = zeros(8);
for i=1:8
    for j=1:8
        C(i,j)=cos((i-1)*(2*j-1)*pi/(2*8));
    end
end


C=sqrt(2/8)*C;
C(1,:)=C (1,:)/sqrt(2);

x = imread('squirrel.png');
x = x(200:556,200:556,:);

for i=1:8:256
    for j=1:8:256
        for k=1:3
            x_eight = x(i:i+7,j:j+7,k);
            Xd=double(x_eight);
            Xc=Xd-128;
            y = C*Xc*C';
            Q=p*8./hilb(8);
            Yq=round(y./Q);
            Ydq=Yq.*Q;
            Xdq=C'*Ydq*C;
            Xe=Xdq+128;
            Xf=uint8(Xe);
            new(i:i+7,j:j+7,k) = Xf; 
        end
    end
end
new = uint8(new);
imagesc(new);colormap(gray);

```

Result

![](/squirrel_eye.png)





6. Obtain a color image, and transform the RGB values to luminance/color difference coordinates. Carry out the steps of Computer Problem 3 for Y, U, andV separately by using JPEG quantization, and recombine as a color image.

```matlab

new = zeros(256,256,3);
p = 1.0;

C = zeros(8);
for i=1:8
    for j=1:8
        C(i,j)=cos((i-1)*(2*j-1)*pi/(2*8));
    end
end

Q1 = [17 18 24 47 99 99 99 99;
    18 21 26 66 99 99 99 99;
    24 26 56 99 99 99 99 99;
    47 66 99 99 99 99 99 99;
    99 99 99 99 99 99 99 99;
    99 99 99 99 99 99 99 99;
    99 99 99 99 99 99 99 99;
    99 99 99 99 99 99 99 99];
Q1 = p*Q;

Q2 = [16 11 10 16 24 40 51 61;
    12 12 14 19 26 58 60 55;
    14 13 16 24 40 57 69 56;
    14 17 22 29 51 87 80 62;
    18 22 37 56 68 109 103 77;
    24 35 55 64 81 104 113 92;
    49 64 78 87 103 121 120 101;
    72 92 95 98 112 100 103 99];
Q2 = p*Q;



C=sqrt(2/8)*C;
C(1,:)=C (1,:)/sqrt(2);

x = imread('squirrel.png');
x = double(x);
x = x(200:556,200:556,:);
y = 0.299*x(:,:,1)+0.587*x(:,:,2)+0.114*x(:,:,3);
u = x(:,:,3)-y;
v = x(:,:,1)-y ;
x(:,:,1) = y;
x(:,:,2) = u;
x(:,:,3) = v;
for i=1:8:256
    for j=1:8:256
        for k=1:3
            x_eight = x(i:i+7,j:j+7,k);
            Xd=double(x_eight);
            Xc=Xd-128;
            y = C*Xc*C';
            if k==1
               Yq=round(y./Q2);
               Ydq=Yq.*Q2;
            else
               Yq=round(y./Q1);
               Ydq=Yq.*Q1;
            end
            Xdq=C'*Ydq*C;
            Xe=Xdq+128;
            Xf = Xe;
            new(i:i+7,j:j+7,k) = Xf; 
        end
    end
end

b = new(:,:,2)+new(:,:,1);
r = new(:,:,1) + new(:,:,3);
g = (new(:,:,1)-0.299*r-0.114*b)/0.587;
new(:,:,1) = r;
new(:,:,2) = g;
new(:,:,3) = b;


new = uint8(new);
imagesc(new);
```

Result

![](/Squirrel_eye_yuv.png)

