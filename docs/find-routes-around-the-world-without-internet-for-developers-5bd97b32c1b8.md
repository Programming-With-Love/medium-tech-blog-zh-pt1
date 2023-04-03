# 在没有互联网的情况下查找世界各地的路线(针对开发人员)

> 原文：<https://medium.com/globant/find-routes-around-the-world-without-internet-for-developers-5bd97b32c1b8?source=collection_archive---------0----------------------->

![](img/e0eb55d1442103f2943597cda6b39a2b.png)

find a peace full place

让我看一下现实世界中的应用程序，在我们向用户提供的所有应用程序中，有最好的场景，即
1)用户将拥有良好的互联网连接。
2)用户拥有大存储容量和 ram 的高端手机。
3)用户没有**计量连接**。

但现实中，用户并没有**高端手机**或**大存储容量**和**无限数据包**(Jio 服务除外)。

到目前为止，我们一直在做 API 调用，从来没有试图缓存它们，甚至也没有试图在一个同步服务中一起上传片段。如果用户没有互联网，他们就不能喜欢或订阅帖子。这就像我们给软件一个单一的状态

> 用户需要互联网来喜欢这篇文章或使用此功能，请检查您的互联网

API 可以用各种方式缓存，数据库和所有的，但是让我们看看像地图这样的难题。

> **如何离线显示地图？**

要求:
1)地图文件(包含您所在地区的地理结构，或者我所说的您在谷歌或任何其他地图上看到的标记下方的纹理)
2)该地区的 OSM 文件(如果您需要显示路线并在经度之间离线查找路线，则为 OSM 文件)
3) Graphhopper(我不知道 OsmDroid 是否会离线查找路线，但现在我已经检查了 Graphhopper，工作正常)。

1.  从这里找到地图文件:
    [*openandromaps*](http://www.openandromaps.org/en/)这些都是很好的轮廓值得捐赠。[download.mapsforge.org](http://download.mapsforge.org/)官方地图伪造出处。
    [openmaps.eu](http://openmaps.eu/download?format=mapsforge) 我没有试过这些(没有英国..)

2.  如果你不想在两个纬度之间寻找路线，跳过这一步(目前我不知道如何在 graphhopper offline 中从名字中追溯点或位置，如果你知道，让我和其他人知道)。
    1。 **D** 从 [github](https://github.com/graphhopper/graphhopper) 下载 graphhopper 源码。
    2。抽出并前往打开端子
    3 的位置。命令:
    。/graphhopper.sh 导入 canary-islands-latest.osm(其中一个有着好听轨迹的岛屿)
    。/graphhopper.sh 导入 canary-islands-170716.osm.pbf(生成路线)
    4 .现在你有了一个加那利群岛的文件夹或者是 graphhopper 文件夹中的 osm 文件
    5。如果这些文件很小，则创建一个 API 或将它们添加到资产中，或者执行一些逻辑操作将文件下载到用户设备上
3.  好去打开 Android 工作室和开发阶段如下所述

> 渲染地图等东西的依赖关系
> 编译' org.mapsforge:maps forge-core:0 . 8 . 0 '
> 编译' org . maps forge:maps forge-map:0 . 8 . 0 '
> 编译' org . maps forge:maps forge-poi:0 . 8 . 0 '
> 编译' org . maps forge:maps forge-poi-Android:0 . 8 . 0 '
> 编译' org . maps forge
> 
> 路由搜索的依赖关系
> 编译(组:' com.graphhopper '，名称:' graphhopper-core '，版本:' 0.10-SNAPSHOT') {
> 排除组:' com.google.protobuf '，模块:' protobuf-java'
> 排除组:' org . openstreetmap . osm-binary '
> 排除组:' org.apache.xmlgraphics '，模块:' xmlgraphics-commons'
> }
> 编译' org.slf4j:slf

地图设置

```
**private void** setUpMap() {
   AndroidGraphicFactory.*createInstance*(**this**.getApplication());
   **this**.**mapView** = **new** MapView(**this**);
   setContentView(**this**.**mapView**);
   **this**.**mapView**.setClickable(**true**);
   **this**.**mapView**.getMapScaleBar().setVisible(**true**);
   **this**.**mapView**.setBuiltInZoomControls(**true**);
   **this**.**tileCache** = AndroidUtil.*createTileCache*(**this**, **"mapcache"**, **mapView**.getModel().**displayModel**.getTileSize(), 1f, **this**.**mapView**.getModel().**frameBufferModel**.getOverdrawFactor());

   MapDataStore mapDataStore = **new** MapFile(**new** File(Environment.*getExternalStorageDirectory*(), ***MAP_FILE***));
   **this**.**tileRendererLayer** = **new** TileRendererLayer(**tileCache**, mapDataStore, **this**.**mapView**.getModel().**mapViewPosition**, AndroidGraphicFactory.*INSTANCE*);
   **tileRendererLayer**.setXmlRenderTheme(InternalRenderTheme.***DEFAULT***);
   **this**.**mapView**.getLayerManager().getLayers().add(**tileRendererLayer**);
   **this**.**mapView**.setCenter(**new** LatLong(28.3778434,-16.5694826));
   createPositionMarker(28.3778434,-16.5694826);
   **this**.**mapView**.setZoomLevel((**byte**) 17);
}
```

其中 mapview 来自 mapforge 库，这将帮助我们渲染标记和地图设备。定义中心要在中心制作动画，您可以制作自己的中心或用户的位置

```
**private void** getDirections() {
      *// create one GraphHopper instance* **hopper** = **new** GraphHopper().forMobile();
      **hopper**.setDataReaderFile(Environment.*getExternalStorageDirectory*()+**"/Downloads/tenerife.osm"**);
*//    grassHopperOsmFile = new File(Environment.getExternalStorageDirectory()+"/Downloads/tenerife.osm").getAbsolutePath();
//    hopper.setDataReaderFile(grassHopperOsmFile);

      // where to store graphhopper files?* String grassHopperFolder  = Environment.*getExternalStorageDirectory*()+**"/grasshopper"**;
      **if**(!(**new** File(grassHopperFolder).exists())){
         **new** File(grassHopperFolder).mkdir();
      }
      **hopper**.setGraphHopperLocation(grassHopperFolder);

      *// now this can take minutes if it imports or a few seconds for loading
      // of course this is dependent on the area you import* **hopper**.load(grassHopperFolder);

      GHRequest req = **new** GHRequest(28.5743, -16.1657, 28.4288, -16.4060).setAlgorithm(Parameters.Algorithms.***DIJKSTRA_BI***);*//setWeighting("fastest");* GHResponse rsp = **hopper**.route(req);

      *// first check for errors* **if**(rsp.hasErrors()) {
         *// handle them!
         // rsp.getErrors()* **return**;
      }Polyline polyline = createPolyline(rsp);

      **this**.**mapView**.getLayerManager().getLayers().add(polyline);
   }**private** Polyline createPolyline(GHResponse response)
{
   GraphicFactory gf=AndroidGraphicFactory.*INSTANCE*;
   Paint paint=gf.createPaint();
   paint.setColor(AndroidGraphicFactory.*INSTANCE*.createColor(Color.***BLACK***));
   paint.setStyle(Style.***STROKE***);
   paint.setDashPathEffect(**new float**[] { 25, 15 });
   paint.setStrokeWidth(8);
   Polyline line = **new** Polyline(paint,AndroidGraphicFactory.*INSTANCE*);

   List<LatLong> geoPoints = line.getLatLongs();
   PointList tmp = response.getBest().getPoints();
   **for** (**int** i = 0; i < response.getBest().getPoints().getSize(); i++) {
      geoPoints.add(**new** LatLong(tmp.getLatitude(i), tmp.getLongitude(i)));
   }

   **return** line;
}
```

GraphHopper 用于对 routes
**hopper**=**new**graph hopper()进行请求。for mobile()；
当我们为 Android/iOS 手机编码时会使用 forMobile，如果我们在后端工作，它将用于 Server()

**料斗**。load(grassHopperFolder)；
用于定位我们使用命令创建的 grasshopper 文件的文件夹。最终，当您想要查找如下代码所示的路线时，将会使用该工具，在该代码中，它使用 Dijkstra 算法查找两点之间的路线。

```
GHRequest req = **new** GHRequest(28.5743, -16.1657, 28.4288, -16.4060).setAlgorithm(Parameters.Algorithms.***DIJKSTRA_BI***);
```

GHRequest 对象用于定义我们需要在其间找到最佳路线的点。

```
GHResponse rsp = hopper.route(req);

*// first check for errors* **if** (rsp.hasErrors()) {
   *// handle them!
   // rsp.getErrors()* Iterator<Throwable> throwableIterator = rsp.getErrors().iterator();
   **while** (throwableIterator.hasNext()) {
      Throwable throwable = throwableIterator.next();
      throwable.printStackTrace();
      System.***out***.println(**"Message:"** + throwable.getMessage());
   }
   **return**;
}
```

在请求检查 Graphhopper 响应和它们的错误格式之后，像 OpenGL 有从数组中读取的错误格式，它们也有一个 throwables 列表。

如果没有任何错误，并且您想要找到这些点的时间和距离，那么您可以检查以下代码:

```
Iterator<PathWrapper> pathWrapperIterator = rsp.getAll().iterator();

**while** (pathWrapperIterator.hasNext()) {
   PathWrapper pathWrapper = pathWrapperIterator.next();

   System.***out***.println(**"Time:"** + pathWrapper.getTime());
   System.***out***.println(**"Distance:"** + pathWrapper.getDistance());
}
```

pathwrapper 将有路径的时间和距离，这样您可以在导航地图时计算或在屏幕上显示一些内容。

要像谷歌地图一样绘制折线，你可以创建一个你想要的颜色的绘画对象。然后创建 mapforge polyline 对象，并通过迭代您从之前的搜索响应中收到的响应来添加 latlong 点，并将它们添加到 geopoints 中。并将地理点添加到多边形线，并通过此显示多边形线。 **mapView** 。getLayerManager()。getLayers()。添加(多段线)；
全法如下:

```
Polyline polyline = createPolyline(rsp);

**this**.**mapView**.getLayerManager().getLayers().add(polyline);
**hopper**.close();**private** Polyline createPolyline(GHResponse response) {
   GraphicFactory gf = AndroidGraphicFactory.*INSTANCE*;
   Paint paint = gf.createPaint();
   paint.setColor(AndroidGraphicFactory.*INSTANCE*.createColor(Color.***BLACK***));
   paint.setStyle(Style.***STROKE***);
   paint.setDashPathEffect(**new float**[]{25, 15});
   paint.setStrokeWidth(8);
   Polyline line = **new** Polyline(paint, AndroidGraphicFactory.*INSTANCE*);

   List<LatLong> geoPoints = line.getLatLongs();
   PointList tmp = response.getBest().getPoints();
   **for** (**int** i = 0; i < response.getBest().getPoints().getSize(); i++) {
      geoPoints.add(**new** LatLong(tmp.getLatitude(i), tmp.getLongitude(i)));
   }

   **return** line;
}
```

[](https://github.com/mapsforge/mapsforge/blob/master/docs/POI.md) [## 地图伪造/地图伪造

### mapsforge -用 Java 写的矢量地图库-运行在 Android 和桌面上。

github.com](https://github.com/mapsforge/mapsforge/blob/master/docs/POI.md) [](https://github.com/mapsforge/mapsforge/blob/master/mapsforge-poi-writer/src/main/config/poi-mapping.xml) [## 地图伪造/地图伪造

### mapsforge -用 Java 写的矢量地图库-运行在 Android 和桌面上。

github.com](https://github.com/mapsforge/mapsforge/blob/master/mapsforge-poi-writer/src/main/config/poi-mapping.xml) [](https://github.com/mapsforge/mapsforge/tree/master/mapsforge-poi-writer) [## 地图伪造/地图伪造

### mapsforge -矢量地图库和 writer -运行在 Android 和桌面上。

github.com](https://github.com/mapsforge/mapsforge/tree/master/mapsforge-poi-writer) 

现在你会得到黑线的基本路线。希望这篇教程能帮到你。让我知道是否有任何替代的方法来找到路线，以便其他人可以检查最好的。

感谢你阅读:D