---
title: Map Coordinate Transformation | 地图坐标转换
layout: post
tags:
  - R
  - Map
  - WGS
  - GCJ

---

在天朝搞与地理信息有关的研究不可避免的会遇到坐标转换问题，与世界通用的WGS坐标不同，天朝的地理坐标一般是“有偏”的，据说是为了安全的考虑，但也为我们进行学术研究带来了不小的麻烦。

通常来说，国内常见的坐标有两种，1)最为通行的是在WGS（真实坐标）上进行一次加偏后出来的GCJ坐标，也就是通常叫的“火星坐标”，像高德，国内的google，搜狗地图等都是用的GCJ坐标。2)还有进行二次加偏的，最有代表性的是百度，名之为“百度坐标”。

所以在研究的过程中我们为了将坐标信息匹配到实际地理空间上，需要进行坐标转换，一般都要转成WGS坐标。好在这些坐标转换规则已成为“公开的秘密”。下面一段代码是我从github上综合不同人的方法，用R写成的，欢迎进行学术研究使用。
		
	#Coordinate
	#Lei Dong
	#2015
	#Ref:https://github.com/googollee/eviltransform


	transformLat <- function (x, y){
	ret <- -100.0 + 2.0*x + 3.0*y + 0.2*y*y + 0.1*x*y + 0.2*sqrt(abs(x))
	ret <- ret + (20.0*sin(6.0*x*pi) + 20.0*sin(2.0*x*pi)) * 2.0 / 3.0
	ret <- ret + (20.0*sin(y*pi) + 40.0*sin(y/3.0*pi)) * 2.0 / 3.0
	ret <- ret + (160.0*sin(y/12.0*pi) + 320*sin(y*pi/30.0)) * 2.0 / 3.0
	return (ret)
	}


	transformLon <- function (x, y){
	ret <- 300.0 + x + 2.0*y + 0.1*x*x + 0.1*x*y + 0.1*sqrt(abs(x))
	ret <- ret + (20.0*sin(6.0*x*pi) + 20.0*sin(2.0*x*pi)) * 2.0 / 3.0
	ret <- ret + (20.0*sin(x*pi) + 40.0*sin(x/3.0*pi)) * 2.0 / 3.0
	ret <- ret + (150.0*sin(x/12.0*pi) + 300.0*sin(x/30.0*pi)) * 2.0 / 3.0
	return (ret)
	}


	delta <- function(lat, lng){
  	a <- 6378245.0
  	ee <- 0.00669342162296594323
  	dLat <- transformLat(lng-105.0, lat-35.0)
  	dLng <- transformLon(lng-105.0, lat-35.0)
  	radLat <- lat / 180.0 * pi
  	magic <- sin(radLat)
  	magic <- 1 - ee*magic*magic
  	sqrtMagic <- sqrt(magic)
  	dLat <- (dLat * 180.0) / ((a * (1 - ee)) / (magic * sqrtMagic) * pi)
  	dLng <- (dLng * 180.0) / (a / sqrtMagic * cos(radLat) * pi)
  	return (c(dLat, dLng))
	}


	##wgs and gcj
	wgs2gcj <- function(wgsLat, wgsLng){
  	if (outOfChina(wgsLat, wgsLng))
    	return (c(wgsLat, wgsLng))
  	else
    	d <- delta(wgsLat, wgsLng)
    	return (c(wgsLat + d[1], wgsLng + d[2]))
	}
  

	gcj2wgs <- function(gcjLat, gcjLng){
  	if (outOfChina(gcjLat, gcjLng))
    	return (c(gcjLat, gcjLng))
  	else
    	d <- delta(gcjLat, gcjLng)
    	return (c(gcjLat - d[1], gcjLng - d[2]))
	}



	##distance
	distance <- function(latA, lngA, latB, lngB) {
  	earthR <- 6371000;
  	x <- cos(latA*pi/180) * cos(latB*pi/180) * cos((lngA-lngB)*pi/180)
  	y <- sin(latA*pi/180) * sin(latB*pi/180)
  	s <- x + y
  	if (s > 1)
    	s <- 1
  	if (s < -1)
    	s <- -1
  	alpha <- acos(s)
  	distance <- alpha * earthR
  	return (distance)
	}


***

