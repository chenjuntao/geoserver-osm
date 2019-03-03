本文参考了《[An almost idiot's guide to install PostgreSQL 9.5, PostGIS 2.2 and pgRouting 2.1.0 with Yum](http://www.postgresonline.com/journal/archives/362-An-almost-idiots-guide-to-install-PostgreSQL-9.5,-PostGIS-2.2-and-pgRouting-2.1.0-with-Yum.html#PostGIS) 》和PostGis[官方网站的安装说明](http://postgis.net/install/)

# 1. 先安装Postgresql

请参见《[CentOS7下安装并简单设置PostgreSQL笔记](install_postgresql.md)》

# 2. 先安装几个以后能用的上的工具包，免得用的时候又没有

```
sudo yum  install wget net-tools epel-release -y
```

# 3. 安装PostGis

```
sudo yum install postgis2_95 postgis2_95-client -y
```

postgis2_95-client中包含了PostGis的命名行工具，如：shp2pgsql,pgsql2shp,raster2pgsql等，看名字也大概知道是什么意思了。

# 4. 安装ogrfdw

ogrfdw的全称是OGR Foreign Data Wrapper

```
sudo yum install ogr_fdw95 -y
```

更多ogr_fdw信息，请参见[这里](https://github.com/pramsey/pgsql-ogr-fdw)

# 5. 安装pgRouting

```
sudo yum install pgrouting_95 -y
```

# 6. 建立一个Postgresql数据库，然后安装PostGis扩展

安装PostGis扩展

```
CREATE EXTENSION postgis;
CREATE EXTENSION postgis_topology;
CREATE EXTENSION ogr_fdw;
```

PostGis的扩展列表

```
-- Enable PostGIS (includes raster)
CREATE EXTENSION postgis;
-- Enable Topology
CREATE EXTENSION postgis_topology;
-- Enable PostGIS Advanced 3D 
-- and other geoprocessing algorithms
-- sfcgal not available with all distributions
CREATE EXTENSION postgis_sfcgal;
-- fuzzy matching needed for Tiger
CREATE EXTENSION fuzzystrmatch;
-- rule based standardizer
CREATE EXTENSION address_standardizer;
-- example rule data set
CREATE EXTENSION address_standardizer_data_us;
-- Enable US Tiger Geocoder
CREATE EXTENSION postgis_tiger_geocoder;
```

# 7. 验证安装结果

```
SELECT postgis_full_version();
```

如果能成功显示版本信息，说明PostGis成功安装好了。

# 8. 创建空间数据表 

存储城市信息（cities），并添加一个存储空间位置的列
```
CREATE TABLE cities(id varchar(20),name varchar(50));
SELECT AddGeometryColumn ('cities', 'the_geom', 4326, 'POINT', 2);
```
# 9. 之后插入数据

```
INSERT INTO cities(id, the_geom, name) VALUES (1,ST_GeomFromText('POINT(-0.1257 51.508)',4326),'London, England');
INSERT INTO cities (id, the_geom, name) VALUES (2,ST_GeomFromText('POINT(-81.233 42.983)',4326),'London, Ontario');
INSERT INTO cities (id, the_geom, name) VALUES (3,ST_GeomFromText('POINT(27.91162491 -33.01529)',4326),'East London,SA');
```

# 10. 查询记录

```
SELECT * FROM cities;
SELECT id, ST_AsText(the_geom), ST_AsEwkt(the_geom), ST_X(the_geom), ST_Y(the_geom) FROM cities;
```

# 11. 空间查询城市相互距离 

```
SELECT p1.name,p2.name,ST_Distance_Sphere(p1.the_geom,p2.the_geom) FROM cities AS p1, cities AS p2 WHERE p1.id>p2.id;
```
