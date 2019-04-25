Question 3 Part 1:

Obtain a grayscale image ﬁle of your choice, and use the imread command to import into MATLAB. Crop the resulting matrix so that each dimension is a multiple of 8. If necessary, converting a color RGB image to gray scale can be accomplished by the standard formula (11.15).


```matlab

%Question 3 Part 1
x = imread('baboon.png');
x = imresize(x,[256 256]);
r=x(:,:,1);g=x(:,:,2);b=x(:,:,3);
xgray=0.2126*r+0.7152*g+0.0722*b;
xgray=uint8(xgray);
imagesc(xgray);colormap(gray)


```

Output

![A baboon](/baboon_q3_p1.png)




Question 3a:
(a) Extract an 8×8 pixel block, for example, by using the MATLAB command xb=x(81:88,81:88). Display the block with the imagesc command


```matlab

%Question 3 Part a
x_eight = xgray(1:8,1:8);
imagesc(x_eight);colormap(gray);

```


![Another baboon](/baboon_q3_pa.png)


Question 3b:


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




Question 3c: 

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

Question 3d:


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





4. Carry out the steps of Computer Problem 3, but quantize by the JPEG-suggested matrix (11.25) with p=1.


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


5. Obtain a color image ﬁle of your choice. Carry out the steps of Computer Problem 3 for colors R, G, and B separately, using linear quantization, and recombine as a color image. 


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

