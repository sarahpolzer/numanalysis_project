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









```
