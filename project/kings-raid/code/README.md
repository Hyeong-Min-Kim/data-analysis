프로젝트 - 킹스레이드
-	성과 : 발렌타인 프로모션 비즈니스 분석 후 차월 매출 18% 개선
역할 : BM 및 컨텐츠 분석

분석기간)
- 블랙프라이데이 : 11/19~ 11/25(D+7일차)
- 발렌타인 : 2/11 ~2/17(D+7일차)

분석과정)

-	카테고리별 동기간 전 프로모션인 (블랙프라이데이)와 상품 매출 비교결과, 스팟상품군 40% 매출 감소

(상시상품인 다른 상품군은 매출 등에 큰 차이는 없음.)

![image](https://user-images.githubusercontent.com/97493166/148948253-1d9c7b5f-355d-4a2c-b155-5429ada41a0b.png)


-	팀레벨별로 확인결과, 팀레벨 100 이하 유저군층에서 동기간 대비 50% 매출 감소. 

100레벨 이상 유저군은 동기간 약 30% 매출 감소된 점으로 판단됨.

![image](https://user-images.githubusercontent.com/97493166/148948269-a9e5e12b-863c-4e65-a2a4-097dbaf34906.png)


-	과금구간대별 매출볼륨 확인결과, 

110,000~ 550,000원 구간의 매출 볼륨이 블랙프라이 동기간 대비 약 40% 감소.
 
![image](https://user-images.githubusercontent.com/97493166/148948295-fb6a3ef3-3a9c-4e96-a6a0-8eae901cc277.png)


-	주요 스팟상품별 구매율 비교 결과, 
-	블랙프라이데이 프로모션 패키지는 약 0.7%~ 1.8% 구매율(%)을 보이나, 발렌타인데이는 약 0.2~0.9% 구매율(%)을 보임.

![image](https://user-images.githubusercontent.com/97493166/148948363-8fe68729-e193-423e-a25b-5f0c8694eb65.png)
![image](https://user-images.githubusercontent.com/97493166/148948371-fdcb2168-a195-4077-a86b-e3c201cac610.png)



-	분석결론) 
1.	스팟상품 내 주요 프로모션 패키지의 낮은 구매율(%) 로, 고과금 구간대의 매출 볼륨 감소로 이어진 것으로 판단됨. 
2.	이는 전 프로모션 대비 패키지 내 상품의 효율 가치 재고해야할 것으로 보임.

-	Query)

```select		aa.server, 
				count(distinct aa.accountid) 'pu', 
				count(distinct case when aa.sales >= 1 and aa.sales < 33000 then aa.accountid else null end) '1~33,000 원',
				count(distinct case when aa.sales >= 33000 and aa.sales < 110000 then aa.accountid else null end) '33,000~110,000 원',
				count(distinct case when aa.sales >= 110000 and aa.sales < 330000 then aa.accountid else null end) '110,000 ~ 330,000 원',
				count(distinct case when aa.sales >= 330000 and aa.sales < 550000 then aa.accountid else null end) '330,000 ~ 550,000 원',
				count(distinct case when aa.sales >= 550000 then aa.accountid else null end) '550,000 원 이상~'
from		
(
select		server, accountid, sum(price) 'sales' 
from		dbo.inappbilling a with (nolock)
join			dbo.cm_billing b with (nolock) on a.clientproductid=b.clientproductid 
and 
				cast(a.time  as date) >= cast(b.created_Date as date) and cast(a.time as date) < cast(b.deleted_Date as date) 
where
				cast(time as date) between '2020-02-11' and '2020-02-17'      ---  기간별
group by 
				server, accountid
) as aa 
group by
				aa.server


select		aa.server, 
				sum(aa.sales) 'pu', 
				sum( case when aa.sales >= 1 and aa.sales < 33000 then aa.sales else null end) '1~33,000 원',
				sum( case when aa.sales >= 33000 and aa.sales < 110000 then  aa.sales  else null end) '33,000~110,000 원',
				sum( case when aa.sales >= 110000 and aa.sales < 330000 then  aa.sales  else null end) '110,000 ~ 330,000 원',
				sum( case when aa.sales >= 330000 and aa.sales < 550000 then  aa.sales  else null end) '330,000 ~ 550,000 원',
				sum( case when aa.sales >= 550000 then  aa.sales  else null end) '550,000 원 이상~'
from		
(
select		server, accountid, sum(price) 'sales' 
from		dbo.inappbilling a with (nolock)
join			dbo.cm_billing b with (nolock) on a.clientproductid=b.clientproductid 
and 
				cast(a.time  as date) >= cast(b.created_Date as date) and cast(a.time as date) < cast(b.deleted_Date as date) 
where
				cast(time as date) between '2019-11-19' and '2019-11-25'      ---  기간별
group by 
				server, accountid
) as aa 
group by
				aa.server```



