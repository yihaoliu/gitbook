# 其它 / 几何形状

##立方体
###BoxGeometry(width, height, depth, widthSegments, heightSegments, depthSegments)
```
width — X轴上的面的宽度.

height — Y轴上的面的高度.

depth — Z轴上的面的深度.

widthSegments — 可选参数. 沿宽度面的分割面数量. 默认值为1.

heightSegments — 可选参数. 沿高度面的分割面数量. 默认值为1.

depthSegments — 可选参数. 沿深度面的分割面数量. 默认值为1.

```


##缓存立方体
###BoxBufferGeometry(width, height, depth, widthSegments, heightSegments, depthSegments)
```
width — X轴上的面的宽度.

height — Y轴上的面的高度.

depth — Z轴上的面的深度.

widthSegments — 可选参数. 沿宽度面的分割面数量. 默认值为1.

heightSegments — 可选参数. 沿高度面的分割面数量. 默认值为1.

depthSegments — 可选参数. 沿深度面的分割面数量. 默认值为1.

```

##缓存圆形
###CircleBufferGeometry(radius, segments, thetaStart, thetaLength)
```
radius — 圆的半径, 默认值 = 50.
segments — 分割面数量 (三角形), 最低值 = 3, 默认值 = 8.
thetaStart — 第一个分割面的开始角度, 默认值 = 0 (3点钟方向).
thetaLength — 圆形扇形的圆心角通常称为θ。默认为2 * Pi，这形成了一个完整的圆

```

##圆形
###CircleGeometry(radius, segments, thetaStart, thetaLength)
```
radius — 圆的半径, 默认值 = 50.
segments — 分割面数量 (三角形), 最低值 = 3, 默认值 = 8.
thetaStart — 第一个分割面的开始角度, 默认值 = 0 (3点钟方向).
thetaLength — 圆形扇形的圆心角通常称为θ。默认为2 * Pi，这形成了一个完整的圆

```

##缓存锥形
###ConeBufferGeometry(radius, height, radiusSegments, heightSegments, openEnded, thetaStart, thetaLength)
```
radius — 锥底半径. 默认值为20.
height — 锥体高度. 默认值为100.
radiusSegments — 围绕圆锥周长的分割面数量. 默认值为8.
heightSegments — 沿圆锥高度的分割面数量. 默认值为1.
openEnded — 指示锥底是打开还是覆盖的布尔值. 默认值为false, 意思是覆盖.
thetaStart — 第一个分割面的开始角度, 默认值 = 0 (3点钟方向).
thetaLength — 圆形扇形的圆心角通常称为θ。默认为2 * Pi，这形成了一个完整的锥体.

```

##锥形
###ConeGeometry(radius, height, radiusSegments, heightSegments, openEnded, thetaStart, thetaLength)
```
radius — 锥底半径. 默认值为20.
height — 锥体高度. 默认值为100.
radiusSegments — 围绕圆锥周长的分割面数量. 默认值为8.
heightSegments — 沿圆锥高度的分割面数量. 默认值为1.
openEnded — 指示锥底是打开还是覆盖的布尔值. 默认值为false, 意思是覆盖.
thetaStart — 第一个分割面的开始角度, 默认值 = 0 (3点钟方向).
thetaLength — 圆形扇形的圆心角通常称为θ。默认为2 * Pi，这形成了一个完整的锥体.

```


##缓存柱体
###CylinderBufferGeometry(radiusTop, radiusBottom, height, radiusSegments, heightSegments, openEnded, thetaStart, thetaLength)
```
radiusTop — 圆柱体顶端半径. 默认值为20.
radiusBottom — 圆柱体底端半径. 默认值为20.
height — 圆柱体高度. 默认值为100.
radiusSegments — 围绕圆柱体周长的分割面数量. 默认值为8.
heightSegments — 沿圆柱体高度的分割面数量. 默认值为1.
openEnded — 指示圆柱体两端是打开还是覆盖的布尔值. 默认值为false, 意思是覆盖.
thetaStart — 第一个分割面的开始角度, 默认值 = 0 (3点钟方向).
thetaLength — 圆形扇形的圆心角通常称为θ。默认为2 * Pi，这形成了一个完整的圆柱体.
```

##柱体
###CylinderGeometry(radiusTop, radiusBottom, height, radiusSegments, heightSegments, openEnded, thetaStart, thetaLength)
```
radiusTop — 圆柱体顶端半径. 默认值为20.
radiusBottom — 圆柱体底端半径. 默认值为20.
height — 圆柱体高度. 默认值为100.
radiusSegments — 围绕圆柱体周长的分割面数量. 默认值为8.
heightSegments — 沿圆柱体高度的分割面数量. 默认值为1.
openEnded — 指示圆柱体两端是打开还是覆盖的布尔值. 默认值为false, 意思是覆盖.
thetaStart — 第一个分割面的开始角度, 默认值 = 0 (3点钟方向).
thetaLength — 圆形扇形的圆心角通常称为θ。默认为2 * Pi，这形成了一个完整的圆柱体.
```

##十二面体
###DodecahedronGeometry(radius, detail)
```
radius — 十二面体的半径. 默认值为1.
detail — 默认值为0. 设置为大于0的值将添加顶点使之不再是一个十二面体.
```
