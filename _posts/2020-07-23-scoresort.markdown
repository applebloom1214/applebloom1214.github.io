---
layout: post
title:  "기타 기능 - 평점 & 게시판 정렬"
---
<br>
## 게시글 평점 개관 및 주요 메서드<br>
 ![Gatok Jekyll Theme]({{site.baseurl}}/images/6-1.png)<br>
<br>
 AJAX 댓글 추가시 평점도 추가하여 평점 기능을 구현하였다
<br>
 <br>
 AJAX 평점 처리<br>
 ~~~c
 //get.jsp
 				<c:if test="${pinfo.username != board.writer}"> // 글쓴이는 평점을 달지 못함
					<div class="form-group">
						<label>평점</label> 
            <select class="form-control" id='score'> // select 태그로 사용자가 택한 점수(1~10)을 저장
							<option value="1">☆ 1</option>
							<option value="2">★ 2</option>
							<option value="3">★☆ 3</option>
							<option value="4">★★ 4</option>
							<option value="5">★★☆ 5</option>
							<option value="6">★★★ 6</option>
							<option value="7">★★★☆ 7</option>
							<option value="8">★★★★ 8</option>
							<option value="9">★★★★☆ 9</option>
							<option value="10">★★★★★ 10</option>
						</select>
					</div>
				</c:if>
        
        .....(생략)
        
        replyService.getList({bno:bnoValue,page: page|| 1 }, function(replyCnt, list) {
           // getList 메소드를 통해 댓글 리스트를 불러오면 댓글에 저장된 평점(score)도 같이 가져온다
	         // 이후 가져온 score를 통해 별로 평점을 만들어 태그에 추가해준다
  if(page == -1){
    pageNum = Math.ceil(replyCnt/10.0);
    showList(pageNum);
    return;
  } 
    
   var str="";
   
   if(list == null || list.length == 0){
	   replyUL.html("");
	   return;
   }
   
   
   for (var i = 0, len = list.length || 0; i < len; i++) {
	   
	   var score = list[i].score;
	   var star = '';
	   
	   switch(score){
	   
	   case 1: 
		   star = '☆';
		   break;
	   case 2: 
		   star = '★';
		   break;
	   case 3: 
		   star = '★☆';
		   break;
	   case 4: 
		   star = '★★';
		   break;		   
	   case 5: 
		   star = '★★☆';
		   break;
	   case 6: 
		   star = '★★★';
		   break;
	   case 7: 
		   star = '★★★☆';
		   break;
	   case 8: 
		   star = '★★★★';
		   break;
	   case 9: 
		   star = '★★★★☆';
		   break;
	   case 10: 
		   star = '★★★★★';
		   break;		   
	   }
        
 ~~~
<br>
## 게시판 정렬 개관 및 주요 메서드<br>
 ![Gatok Jekyll Theme]({{site.baseurl}}/images/6-2.png)<br>
<br>
 최신수, 댓글 TOP 10, 평점 TOP10 세 종류로 정렬을 구현하였다
<br>
 <br>
 정렬을 위한 게시판, 댓글 테이블
 <br>
  ![Gatok Jekyll Theme]({{site.baseurl}}/images/6-3.png)<br>
<br>
 댓글이 달릴 때마다 증가되는 게시판 테이블(HAPPY_BOARD)의 <br>
 리플수(REPLYCNT)와 리플레이 테이블(HAPPY_REPLY)의 평점(SCORE)을 이용하여<br>
 SQL 쿼리문을 사용하여 정렬을 구현하였다<br>
 <br>
 BUTTON을 이용한 정렬 처리
 <br>
 ~~~c
     // list.jsp
 	//최신순 버튼 클릭 이벤트
	$("#latest").on("click", function(e) {

		e.preventDefault();
		self.location = "/happy/list";
		//self = 현재창 자신 window와 같음
	});
						
	//평점순 버튼 클릭 이벤트
	$("#score").on("click", function(e) {

		var sortForm = $("#sortForm");
		e.preventDefault();
		sortForm.find("input[name='sort']")
		.val($(this).attr("name"));
		sortForm.submit();
							
	});
						
	//댓글순 버튼 클릭 이벤트
	$("#replycnt").on("click", function(e) {

		var sortForm = $("#sortForm");
		e.preventDefault();
		sortForm.find("input[name='sort']")
		.val($(this).attr("name"));
		sortForm.submit();
	});
 ~~~
  <br><br>
 SORT 종류에 따른 컨트롤러 처리
 <br>
 ~~~c
  // BoardController.java
 	@GetMapping("/list")
	public void list(Criteria cri, Model model) {

		log.info("list: " + cri);
		
		if(cri.getSort() == null) { // sort 값이 없으면 기본 정렬(최신순)
			model.addAttribute("list", service.getList(cri));
		}else if (cri.getSort().equals("score")) { // 평균 평점으로 정렬
			model.addAttribute("list", service.getListScore(cri));
		}else if(cri.getSort().equals("replycnt")) { // 리플수로 정렬
			model.addAttribute("list", service.getListReplyCnt(cri));
		}
		
		int total = service.getTotal(cri);

		model.addAttribute("pageMaker", new PageDTO(cri, total));

	}
 ~~~
   <br>
 <br>
  
## 실기 영상<br>
 댓글 평점 남기기<br>
  ![Gatok Jekyll Theme]({{site.baseurl}}/images/6-1.gif)
 <br><br>
 게시판 정렬<br>
  ![Gatok Jekyll Theme]({{site.baseurl}}/images/6-2.gif)
