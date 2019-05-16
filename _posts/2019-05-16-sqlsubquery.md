---
layout: post
title: mysql 서브쿼리
categories: [sql]
tags: [sql]
description: mysql에서 간단한 서브쿼리 사용법
comments: true
---
# **서브쿼리**  
### 1. 서브쿼리의 의미  
서브쿼리란 하나의 쿼리문 안에 포함되어 있는 또 하나의 쿼리문을 뜻한다. 쉽게 말해 쿼리문안에 쿼리문을 쓴다고 생각하면 될 것이다. 여기에는 주의사항이 있다.  
  * 서브쿼리는 괄호로 묶어서 사용해야한다.  
  * 서브쿼리 안에서 order by절은 사용불가능하다.  
  * 연산자 오른쪽에 사용해야 한다.
  
### 2. 예제  
아래 볼 예제는 현재 보고있는 상품의 카테고리 번호를 불러와 그 카테고리 번호를 가진 상품들을 랜덤하게 6개 가져오는 쿼리문이다.

예를 들면 상품a, b, c, d의 카테고리 번호는 1번, 상품  1, 2, 3, 4의 카테고리 번호는 2번이라고 가정하고, 현재 사용자는 상품 c의 상품정보 상세보기 페이지를 보고있다고 가정하자. 그렇다면 상품 c의 관련상품은 a, b, d가 되고 상품 1, 2, 3, 4는 관련상품이 아니게 된다. 이때 관련상품 a, b, d를 얻어오기 위해 필요한 쿼리문이 바로 서브쿼리문이다.  
~~~sql
$sql="select category.name as categoryname, division.name as divisionname, section.name as sectionname,
	productinfo.modelname as productmodelname, productinfo.price as productprice, productinfo.volt as productvolt, productinfo.watt as productwatt, productinfo.date as productdate,
productinfo.size as productsize, productinfo.killogram as productkillogram, productinfo.color as productcolor, productinfo.feature as 		productfeature, productinfo.brand as productbrand, productinfo.madeby as productmadeby,
	productinfo.income as productincome, productinfo.specification as productspecification, productinfo.kc1 as productkc1, productinfo.kc2 as productkc2,
	productinfo.pic1 as productpic1, productinfo.pic2 as productpic2, productinfo.pic3 as productpic3, member.name as adminname, productsale.* from productsale
	left join category on productsale.categoryno=category.no
	left join division on productsale.divisionno=division.no
	left join section on productsale.sectionno=section.no
	left join productinfo on productsale.productno=productinfo.no
	left join member on productsale.registerno=member.no
	where productsale.stock>=1
	and productsale.categoryno = (select categoryno from productsale where no='$no')
	order by rand() limit 6";
~~~  
괄호안의 쿼리문은 현재 페이지의 카테고리 번호를 가져와 productsale테이블의 categoryno과 같은 결과값을 찾는다는 의미이다.
