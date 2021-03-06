# Recast

## Solo Mesh Sample
单模型构建导航网格

### 步骤
一、初始化构建导航网格参数

对个别参数进行解析，后续补充：

**`cs、ch: `**
有单元格的底部大小，和单元格高

**`walkableSlopeAngle:`**

可行走区域的三角网格的最大坡度，后面会有几步过滤，这个参数用在第一步，用于筛出，三角网格角度（法线与Up方向夹角，这里我不太好解释这个up，就是世界的Up的意思吧）

**`walkableHeight:`**
智能体的高度，就是用于之后导航网格中，筛选掉，智能体无法通过的高度较为矮小的区域

**`walkableClimb:`**

智能体的可攀爬高度

**`walkableRadius:`**

智能体的半径

二、标记可行走区域并光栅化（体素化）三角形

这里会用到之前的`walkableSlopeAngle`参数，并输入mesh的所有三角形，然后计算每个三角形的法线，并且法线与Up的夹角小于这个角度，就标记为``RC_WALKABLE_AREA``，如果大于这个角度就不标记。

`dividePoly`函数

![divePoly](imgs/dividePoly.png)

![divePolys](imgs/dividePolys.jpg)


`rasterizeTri`函数

![divePolys](imgs/recastTri1.png)

第一步，用平行于z轴（从最小平面往最大平面前进一个cellsize）的平面切三角形

然后得到小的三角形

然后小三角形x轴方向，占及格cell，然后以同样的方法，沿着x轴方向从minx到maxx按照同样的方法去切小三角形。


![divePolys](imgs/recastTri2.png)

每切出一个多边形，就根据这个多边形所在的x、y和最大最小高度，就创建一个span

`addSpan`函数

添加一个span，并且和当前高度域中，当前存在高度域的span比较，如果隔开了不接触，就把这两个分别从下面指向上面。

如果有相交，就把两个span合并了。