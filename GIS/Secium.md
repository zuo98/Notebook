## Cesium笔记

### Cesium坐标讲解

1. 屏幕坐标 **（像素）**

   即二维笛卡尔平面坐标，我们通过鼠标点击直接获取的坐标就是屏幕坐标了，单位是像素值，也可以通过`new Cesium.Cartesian2(x, y)`创建。

2. 笛卡尔空间直角坐标（世界坐标系）**（米）**

   笛卡尔空间直角坐标又称为世界坐标，Cesium中用Cartesian3变量表示，可通过`new Cesium.Cartesian3(x, y, z)`创建，主要是用来做空间位置的变化如平移、旋转和缩放等等，它的坐标原点在椭球的中心.

3. 地理坐标**（弧度）**

   Cesium中的地理坐标单位默认是弧度制，用Cartographic变量表示，可通过`new Cesium.Cartographic(longitude, latitude, height)`创建，其中这里的参数是用弧度表示的经纬度，即经度和纬度。

4. 经纬度坐标**（度）**

   即测绘中的地理经纬度坐标，默认是WGS-84坐标系，坐标原点在椭球的质心。
   经度：参考椭球面上某点的大地子午面与本初子午面间的两面角，东正西负。
   纬度 ：参考椭球面上某点的法线与赤道平面的夹角，北正南负。
   Cesuim中没有具体的经纬度对象，要得到经纬度首先需要计算为弧度，再进行转换。

 ### 经纬度与世界坐标系互转：

+ 经纬度坐标系转世界坐标系（Cartesian3）
  + 方法1：直接转换  //height非必填，不填时默认为0;
     `var cartesian3 = Cesium.Cartesian3.fromDegrees(lng, lat, height);//单位：度，度，米`
  + 方法2：借助ellipsoid对象，先转换成弧度再转换
    ``var cartographic = Cesium.Cartographic.fromDegrees(lng, lat, height); //单位：度，度，米`
    var cartesian3 = Cesium.Ellipsoid.WGS84.cartographicToCartesian(cartographic);``

+ 世界坐标系（Cartesian3）转经纬度坐标系：

  1. 步骤1：笛卡尔空间直角坐标系转为地理坐标（弧度制）
      `var cartographic = Cesium.Cartographic.fromCartesian(cartesian3); // 方法1`
      `var cartographic = Cesium.Ellipsoid.WGS84.cartesianToCartographic(cartesian3); // 方法2`

  2. 步骤2：地理坐标（弧度制）转为经纬度坐标
      `var lat = Cesium.Math.toDegrees(cartographic.latitude);`
      `var lng = Cesium.Math.toDegrees(cartographic.longitude);`
      `var height = cartographic.height;`


 ### 弧度与经纬度互转：
+ 角度转弧度 π/180×角度
  ```
   // 经纬度转弧度
   Cesium.Math.toRadians(degrees)
  ```

+ 弧度变角度 180/π×弧度

  ```
   // 弧度转经纬度
   Cesium.Math.toDegrees(radians)
  ```

### 屏幕坐标和世界坐标互转

+ 屏幕转世界坐标：

  二维屏幕坐标转为三维笛卡尔空间直角坐标（世界坐标）
  ```
  var cartesian3 = scene.globe.pick(
     viewer.camera.getPickRay(windowPostion),
     scene
  );
  ```
  注意这里屏幕坐标一定要在球上，否则生成出的cartesian对象是undefined。

+ 世界坐标转屏幕坐标：

  三维笛卡尔空间直角坐标（世界坐标）转为二维屏幕坐标
  结果是Cartesian2对象，取出X,Y即为屏幕坐标。
  ```
  windowPostion = Cesium.SceneTransforms.wgs84ToWindowCoordinates(
    scene,
    cartesian3
  );
  ```
### 坐标变换工具
只有转换到笛卡尔坐标系后才能运用计算机图形学中的仿射变换知识进行空间位置变换如平移旋转缩放。Cesium为我们提供了如下几种很有用的变换工具类：

Cesium.Cartesian3（相当于Point3D）
Cesium.Matrix3（3x3矩阵，用于描述旋转变换）
Cesium.Matrix4（4x4矩阵，用于描述旋转加平移变换）
Cesium.Quaternion（四元数，用于描述围绕某个向量旋转一定角度的变换）
Cesium.Transforms(包含将位置转换为各种参考系的功能)