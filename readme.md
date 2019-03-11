# 통합근무 관련 이슈

## 전제사항

- 통합근무 관련된 api 작업은 별개의 브런치에서 진행합니다.
  - 브런치 이름은 topic/intergrated-orderwork
- 관련 api의 주소는 전부 /v3/ 으로 시작합니다.
  - 기존의 이름에서 /v3/을 붙이거나, /v2/의 숫자를 바꾸거나 하는 식으로 진행합니다.
  - 이전 api와의 호환을 위해 (업데이트 고려) 새 api에 작업합니다.

## 예약 ~ 근무 상세

1. 예약현황

   1. 요구사항

      - 날짜별로 예약을 한 아파트를 목록으로 보여주고,

      - 그 아파트의 하위객체로 발주 된 order_works 목록을 담아줍니다.

        ~~~json
        [
            {"아파트":
         		{"이름":"갈매 5단지 와이시티", 
        			"업무목록":[
                        {
                            "택배사":"CJ",
                            "업무종류":"SORT"
                        },      
        			    {
        			          "택배사":"우체국",
                  			"업무종류":"LIFT"
              			},
          			]
         		}
        	}
        ]
        ~~~

      - 기존에는 예약 목록을 먼저 보여줬는데, 아파트 별로 가져오는 방식으로 해야할듯 합니다.

        - 가능하면 모델 에서 처리하는 식으로..!

      - 예약목록을 따로 보여주진 않고, 예약한 아파트 목록에 바로 발주된 업무 목록을 보여주자.

   2. Endpoint

      - 현재 : /my_reservation
      - 변경 : /v3/my_reservation

2. 근무 상세

   1. 요구사항

      - 지금은 하나의 order_work에 대해서 상세정보를 보여주지만,

      - 아파트의 정보에 수락한 업무목록이 달려있는 형식을 자세히 표현하는 방향이어야 합니다.

      - ~~~json
        {
            "아파트":{
                "이름":"갈매 5단지 와이시티",
                "업무목록":[
                   	{
                      	"택배사":"CJ",
                      	"업무종류":"SORT"
                  	},
                    {
                      	"택배사":"CJ",
                      	"업무종류":"SORT"
                  	}
                ]
            }
        }
        ~~~

   2. Endpoint

      - 현재 : /v2/order/order_work
      - 변경 : /v3/aprtment/today_orders - GET

   3. 파라미터

      - 유저토큰 - Header
      - 어느 아파트 - apartment_id
      - 어느날짜 - date
        - null일 경우 오늘날짜로

   4. 동작 내용

      - 유져 / 아파트 / 날짜 => contract 조회.
        - 내가 수락한 업무 목록을 Array로
      - 결과 : 내가 수락한 order_works가 array로 들어가게됨.
        - 오더워크의 부가정보는 예약현황과 같은 방식으로.

## Sort 근무 관련

1. 동 목록 화면 - 근무현황

   1. 요구사항

      - 아파트의 의 동 목록을 보여주자
      - 배송 수량을 보여주자
        - 모든 order_work에서 수행된 박스 카운트의 합
      - 완료 보고 여부도.
        - 모든 order_work가 전부 완료되었을때만 된걸로.
      - 여러개의 order_work를 일괄처리.
        - 씨리얼 발급 절차처럼 파라미터를 order_work_ids 로 (배열로)
   2. 엔드포인트

      - 현재 : /v2/order/order_work/process
      - 변경 : /v3/apartment/order_process - GET
   3. 파라미터
      - order_work_ids 로 (배열로)
   4. 응답 형테

   ~~~json
   {
       "동목록":[
           {
               "동이름":"1동",
          		"수량":100,
               "보고여부":true/false
           }
       ]
   }
   ~~~

   