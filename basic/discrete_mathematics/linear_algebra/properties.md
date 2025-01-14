# properties


<!-- @import "[TOC]" {cmd="toc" depthFrom=1 depthTo=6 orderedList=false} -->

<!-- code_chunk_output -->

- [properties](#properties)
    - [overview](#overview)
      - [1.product](#1product)
        - [(1) inner product 和 outer product](#1-inner-product-和-outer-product)
        - [(2) dot product (vector)](#2-dot-product-vector)
        - [(3) matrix multiply](#3-matrix-multiply)
        - [(4) cross product](#4-cross-product)
        - [(5) dot product vs cross product](#5-dot-product-vs-cross-product)
      - [2.eigenvectors and eigenvalues](#2eigenvectors-and-eigenvalues)
        - [(1) 特征值和特征向量](#1-特征值和特征向量)
        - [(2) eigenbasis](#2-eigenbasis)
        - [(3) 性质](#3-性质)
      - [3.norm](#3norm)
        - [(1) vector norm](#1-vector-norm)
        - [(2) matrix norm](#2-matrix-norm)
      - [4.$e^A$](#4ea)

<!-- /code_chunk_output -->


### overview

#### 1.product

##### (1) inner product 和 outer product

* outer product
    * 两个向量组成一个新的矩阵
    * $\vec u \otimes \vec v = \begin{bmatrix} u_1v_1 & u_1v_2 & \cdots & u_1v_n \\ u_2v_1 & u_2v_2 & \cdots &u_2v_n \\ \vdots & \vdots & \ddots & \vdots \\ u_mv_1 & u_mv_2 & \cdots & u_mv_n\end{bmatrix}$

* inner product
    * 向量的inner product就是dot product
    * 矩阵的inner product比较复杂

##### (2) dot product (vector)
* dot product可以表示
    * 向量的inner product
    * 矩阵的matrix multiply

* 线性理解
    * $\begin{bmatrix} 1 & 2 \end{bmatrix} \begin{bmatrix} 4 \\ 3 \end{bmatrix}=1*4+2*3=10$

* 映射理解：**$\vec a\cdot\vec b= a_xb_x+a_yb_y=|\vec a||\vec b|\cos\theta$**
    * 一个向量在另一个向量上的**映射**，并**乘以另一个向量的长度**
    * 单位向量$\vec u=(u_x,u_y)$
        * $|\vec u|=1$，$u_x,u_y$不等于1
        * duality: 
            * ($\vec i$在$\vec u$上的映射) = ($\vec u$在$\vec i$上的映射) = $u_x$
            * ($\vec j$在$\vec u$上的映射) = ($\vec u$在$\vec j$上的映射) = $u_y$
    * 已知向量$\vec v=(mu_x,nu_y)$
        * 则向量$\vec x =(x,y)=x\vec i+y\vec j$在$\vec v$上的映射$xmu_x+ymu_y$
        * 就等于$\vec v\cdot\vec x$
    * 所以
        * **$\vec a\cdot\vec b=|\vec a||\vec b|\cos\theta$**
            * $\theta$为a和b之间的夹角
            * $|\vec b|\cos\theta$为$\vec b$在$\vec a$上的映射
            * 所以a,b方向不一致时为负数，垂直时为0

    ![](./imgs/overview_02.png)

* 所以$\vec a$在$\vec b$上的映射向量为: $\frac{\vec a\cdot \vec b}{|\vec b|^2}\vec b$
    * $\vec a$在$\vec b$上的映射长度为$\frac{\vec a\cdot \vec b}{|\vec b|}$

##### (3) matrix multiply

* 理解: $A \cdot B$
    * 多个向量的**线性变换**
        * B的每一列是一个向量，对每一个向量进行线性变换A
    * A的每行和B的每列进行**dot product**

* dot product 和 matrix multiply区分
    * dot product对象：**向量**，**输出**是一个**值**
    * matrix multiply对象：**矩阵**，**输出**是另一个**矩阵**： $M = A \cdot B$ 
        * 前提： A矩阵的每行和B矩阵的每列做点积（A每行的元素数 = B每列的元素数）
        * M的第m行，第n列的元素值 = A矩阵的第m行 $\cdot$ B矩阵的第n列

##### (4) cross product
* **$\vec a\times\vec b=\begin{bmatrix}a_1\\a_2\\a_3\end{bmatrix}\times\begin{bmatrix}b_1\\b_2\\b_3\end{bmatrix}=\begin{bmatrix}a_2b_3-a_3b_2\\a_3b_1-a_1b_3\\a_1b_2-a_2b_1\end{bmatrix}=|\vec a||\vec b|\sin\theta\ \vec n$**
    * $|\vec a||\vec b|\sin\theta$是 $\vec a$和$\vec b$形成的平行四边形的面积（即行列式的值）
    * $\vec n$是单位向量，用于描述方向（使用右手法则）：与$\vec a$和$\vec b$形成的平面**垂直**
        * 右手法则:
            * 食指表示$\vec a$
            * 中指表示$\vec b$
            * 大拇指的方向就是新向量的方向
    * $\vec a,\vec b\in R^3$
    * 二维向量叉乘：标量
        * $\vec v \times \vec w$ 结果是两个向量构成的平行四边形的面积（如果$\vec v$ 在坐标轴种的位置在右边，则是正数）

##### (5) dot product vs cross product
* dot product
    * 描述两个向量，同向的程度: $\frac{\vec a\cdot\vec b}{||\vec a||\ ||\vec b||}$
* cross product
    * 描述两个向量，垂直的程度: $\frac{\vec a\times\vec b}{||\vec a||\ ||\vec b||}$

#### 2.eigenvectors and eigenvalues

##### (1) 特征值和特征向量
* **特征向量**：经过线性变换，向量的**方向没有改变**，只是进行了scale
    * scale的值就是 **特征值**
    * 比如: 进行$\begin{bmatrix} 1 & 1 \\ 0 & 1 \end{bmatrix}$线性变换，x轴的span没有改变，scale值为1,所以x轴上的所有向量都是特征向量，特征值为1

* 数学定义: $A\vec v= \lambda \vec v$
    * $\lambda\vec v$可以写成 $\lambda I\vec v$
    * 所以$(A-\lambda I)\vec v= 0$
    * 要使方程有解 所以$det(A-\lambda I)=0$
    * 从而求出特征值
    * 将某个特侦值带入 $(A-\lambda I)\vec v= 0$，从而求出与该特征值关联的特征向量空间$E_{\lambda}$
* 不是所有的线性变换都有特征值和特征向量的

##### (2) eigenbasis
针对某一个线性变换，使用一组特征向量作为新的基向量，如果需要计算多次进行该线性变换，使用eignebasis简化计算，因为除了主对角线上，其他地方都为0

##### (3) 性质

* $A^nx = \lambda^nx$
    * $A^{-1}x = \frac{1}{\lambda}x$
* $A=X\Lambda X^{-1}$ (A非对称 方正矩阵)
    * X每一列都是**A的单位特征向量**, $\Lambda$每一列都是对应的特征值
    * 因为$AX=X\Lambda$，所以$A=X\Lambda X^{-1}$
    * 注意：**只有方正矩阵可逆**
    * 能够推出：
        * $A^k=X\Lambda^k X^{-1}$
* 特征值的和 = A对角线的数值相加
    * 根据$A=X\Lambda X^{-1}$推出
* 特征值的乘积 = det(A)
    * 因为$det(A-\lambda I)=0$，所以$det(A) = det(\lambda I)$，右边等于特征值的乘积

#### 3.norm

##### (1) vector norm
* Lp-norm: $\Vert \vec x \Vert_p = (\sum\limits_{i=1}^{n}|x_i|)^{1/p}$
    * L1-norm (Manhatten distance)
      * $\Vert \vec x\Vert_1 = \sum\limits_{i=1}^{n}|x_1|$
    * L2-norm (Euclidean distance)
      * $\Vert \vec x\Vert_2 = (\sum\limits_{i=1}^{n}|x_1|^2)^{1/2}$
* $\infty$-norm: $\Vert \vec x \Vert_{\infty} = \underset{i}{max}|x_i|$

##### (2) matrix norm

* Frobenius norm
  * $\Vert A\Vert_F = (\sum_{i,j}abs(a_{i,j})^2)^{1/2}$
* L1-norm
  * $\Vert A\Vert_1$ = `max( sum(abs(x), axis=0) )`
* L2-norm
  * $\Vert A\Vert_2$ = largest sing. value

#### 4.$e^A$

根据泰勒展开
* $e^A = E + A + \frac{A^2}{2!} + ... = \sum\limits_{k=0}^{\infty}\frac{A^k}{k!}$