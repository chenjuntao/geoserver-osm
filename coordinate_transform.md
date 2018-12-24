GDAL库下载地址
-----------------------------------------------------------------
网站：http://www.gisinternals.com/query.html?content=filelist&file=release-1900-x64-gdal-2-3-2-mapserver-7-2-1.zip  
下载第一个：release-1900-x64-gdal-2-3-2-mapserver-7-2-1.zip  
描述为：Compiled binaries in a single .zip package  

坐标系查询
-----------------------------------------------------------------
地理坐标系WKID：https://developers.arcgis.com/javascript/3/jshelp/gcs.htm  
投影坐标系WKID：https://developers.arcgis.com/javascript/3/jshelp/pcs.htm

我国常用坐标系代码
--------------------------------------------------------- 
4214 GCS_Beijing_1954 
4326 GCS_WGS_1984 
4490 GCS_China_Geodetic_Coordinate_System_2000 
4610 GCS_Xian_1980
3395 WGS84/World Mercator

坐标在线转换
---------------------------------------------------------------
在线坐标转换测试地址：
https://mygeodata.cloud/cs2cs/  
http://epsg.io/


GDAL安装与JAVA开发配置步骤
-------------------------------------------------
1、解压缩  
2、设置环境变量bin到PATH  
3、设置环境变量GDAL_DATA为\bin\gdal-data  
4、bin\gdal\java中的库拷贝到项目根目录下  
5、设置gdal.jar到项目build path中  

代码
-----------------------------------------------------------------------
```
// creating EPSG:4326
SpatialReference sourceSRS = new SpatialReference();
sourceSRS.ImportFromEPSG(4326);
// creating EPSG:3068
SpatialReference targetSRS = new SpatialReference();
targetSRS.ImportFromEPSG(4212);
// printing both SRS's
System.out.println(sourceSRS); // does get printed
System.out.println(targetSRS); // doesn't get printed!?
// creating a point in EPSG:3068 coordinates 850,-150
Geometry point = Geometry.CreateFromWkt("POINT(120 30)");
// assigning EPSG:3068 to the point so the system knows that it is in EPSG:3068
point.AssignSpatialReference(sourceSRS);
// trying to transform it to EPSG:4326 but Exception occurs.
point.TransformTo(targetSRS);

CoordinateTransformation coodTrans = CoordinateTransformation.CreateCoordinateTransformation(sourceSRS, targetSRS);
double[] result1 = coodTrans.TransformPoint(120, 30, 0);
System.out.println(result1);
```
