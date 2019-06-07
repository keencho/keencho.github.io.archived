---
layout: post
title: 쿠키를 이용한 php 최근본상품 구현
categories: [php]
tags: [php]
description: php에서 쿠키를 사용해 최근본 상품을 구현하기 위한 방법을 알아보자
comments: true
---
# **최근 본 상품**  
앞서 포스팅했던 사이드 플로팅 배너에 최근본 상품을 php 쿠키를 이용해 구현해보았다. 게시글 중복조회 방지코드로도 사용가능하다.  

~~~php
$cookiePno = $no; // 여기서 $no는 상품번호이다.
$i=0;
if(isset($_COOKIE['today_view'])){ // today_view라는 쿠키가 존재하면
	$todayview=$_COOKIE['today_view']; // $todayview 변수에 today_view 쿠키를 저장한다.
	$tod2=explode(",", $_COOKIE['today_view']); // today_view 쿠키를 ','로 나누어 구분한다.
	$tod=array_reverse($tod2); // 최근 목록 5개를 뽑기 위해 배열을 최신 것부터로 반대로 정렬해준다.

	// 중복을 막기위한 save 변수 지정
	while($i<sizeof($tod)){ // $tod배열의 사이즈만큼 반복한다.
		if($cookiePno==$tod[$i]){
			$save='no';
		}
		$i++;
	}
}
// 여기까지의 소스를 모든 컨트롤러의 메소드의 붙여넣기

// 저장된 쿠키값이 없을 경우 새로 쿠키를 저장하는 소스
if(!isset($_COOKIE['today_view'])){
	setcookie('today_view', $cookiePno, time() + 21600, "/");
}

// 저장된 쿠키값이 존재하고, 중복된 값이 아닌 경우 기존의 today_view 쿠키에 현재 쿠키를 추가하는 소스
if(isset($_COOKIE['today_view'])){
	if($save != 'no'){
		setcookie('today_view' , $todayview. "," . $cookiePno , time() + 21600, "/");
	}
}

// view쪽에 cookie값을 뿌리기 위한 소스, 2차원 배열을 사용해 쿠키의 사이즈를 가져와 뿌리도록 설정하였다.
if(isset($_COOKIE['today_view'])){
	$cookiecount;
		for($cookiecount = 0; $cookiecount < sizeof($tod); $cookiecount++) {
			$data["recentview"][$cookiecount] = $this->Product_m->getrecentview($tod[$cookiecount]);
		}
}
~~~  
모델쪽에서의 sql문은 최근본 상품에 필요한 요소들을 얻어오는 sql문을 작성하면 된다.  
~~~sql
function getrecentview($no){
	$sql="select productsale.*, productinfo.pic1 as cookiepic from productsale
		  left join productinfo on productsale.productno=productinfo.no
		  where productsale.no='$no'";
		 return $this->db->query($sql)->result();
}
~~~  
view에서 뿌려주는 부분을 담당하는 소스이다.  
~~~html
<!-- 사이드 플로팅 배너 -->
<div class="col-md-11 col-md-push-3 col-sm-12">
	<div id="sidebar" style="position: absolute;">	
		<ul>
			<li align="center" >
				<font color="black" style="font-weight: bold;">
					최근본상품
				</font>
				<font color="red">
				<?
					if(isset($_COOKIE['today_view'])){
						$cookieArray=explode(",", $_COOKIE['today_view']);
						$cookieCount=sizeof($cookieArray);
				?>
					<?=$cookieCount;?>
				<? 
					}
				?>
				</font>
				<br>￣￣￣￣￣
			</li>
			<div style="text-align: center;">
				<div class="scrollbarauto">
				<?
					if(isset($_COOKIE['today_view'])){
						for($i=0; $i < $cookieCount; $i++){
							foreach($recentview[$i] as $key){
				?>
					<li>
						<a href="<?php echo base_url(); ?>CI/Product/view/no/<?=$key->no?>" target="_blank">
							<img src="<?php echo base_url(); ?>CI/userimg/<?=$key->cookiepic?>" width="60" height="60" alt="">
						</a>
					</li>
					<br>
				<?			}
						}
					}
				?>
					
				</div>
			</div>
		</ul>
	</div>
</div>
<!-- 플로팅 배너 끝 -->\
~~~  
저번 포스팅에 있던 플로팅 배너 html 소스에 최근본 상품 소스를 적용한 결과물이다. 일단 결과는 이렇게 아래처럼 잘 출력된다.  
![1](https://user-images.githubusercontent.com/36055500/58605338-5e660c80-82d2-11e9-93e9-04a3f497331d.PNG)  
일단 isset 함수를 사용하여 쿠키가 있는지 확인한다. 쿠키가 있다면 $cookieArray에 쿠키값을 ,로 나눈값을 저장하고, 이 $cookieArray의 요소를 카운트해서 나온 결과를 $cookieCount에 저장한다. 따라서 for문을 이용해 이 $cookieCount값까지 반복문을 돌린후, foreach문을 이용해 컨트롤러에서 보내온 $recentview 2차원배열의 각각의 요소를 $key로 변환한다. 현재 $key에는 상품번호, 상품명, 상품가격, 상품이미지 등등 상품에 관한 모든 내용을 데이터베이스에서 불러와서 사용가능하므로, 원하는 값을 $key->'원하는 요소'를 사용해 출력하면 된다.


