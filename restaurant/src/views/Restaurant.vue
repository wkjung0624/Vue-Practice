<template>
    <div>
        <div>
        <!-- 상품 아이템 리스트 -->
            <button v-for="menuItem in menus"
            @click="sendOrder(menuItem.menuId);">
                {{menuItem.menuName}}<br>
                ({{menuItem.price}})
            </button>
        </div>
    </div>

    <div>
        <!-- 주문 목록 -->
        <table>
            <thead>
                <tr>
                    <th> 메뉴2명 </th>
                    <th> 가격 </th>
                    <th> 수량 </th>
                </tr>
            </thead>
            <tbody>
                <!-- qty값이 음수일 때 방지 필요 -->
                <tr :key=orderIdx v-for="(orderItem, orderIdx) in currentOrderList">
                    <td style="width:150px; font-size:12px"> {{orderItem.menuName}} </td>
                    <td style="width:100px; font-size:12px"> {{orderItem.price}} </td>
                    <td> 
                        <input type="number" v-model="orderItem.qty" style="width:30px;">
                    </td>
                    <td> <button @click="deleteOrder(orderIdx)"> X </button> </td>
                </tr>
            </tbody>
        </table>

        <!-- 할인 전 총 금액 -->
        <div>
            <h1>{{totalAmount}}</h1>
        </div>

        <!-- 할인 쿠폰, 카드 선택 -->
        <div>
            쿠폰 선택 및 카드 선택
            <select v-for="(cardType, cardIdx) in cardTypes">
                <option value="1" v-for="creditItem in creditCards">
                    {{creditItem.cardName}}</option>
            </select>
        </div>

        <!-- 최종 금액 -->
        <div>
            <h3> 최종 결제액 </h3>
        </div>
    </div>

</template>

<script>
export default {
    data(){
        return {
            // totalAmmount : 0,
            //상품 정보
            menus : [{
                menuId: 1,
                menuName: "무제한 샐러드바",
                price: 25000,
            },
            {
                menuId: 2,
                menuName: "안심 스테이크(150g)",
                price: 35500,
            },
            {
                menuId: 3,
                menuName: "립아이 스테이크(220g)",
                price: 22500,
            },
            {
                menuId: 4,
                menuName: "채끝 등심 스테이크(210g)",
                price: 30500,
            },
            {
                menuId: 5,
                menuName: "자몽에이드",
                price: 6500,
            },
            {
                menuId: 6,
                menuName: "애플망고에이드",
                price: 6500,
            },
            {
                menuId: 7,
                menuName: "생맥주",
                price: 400,
            },
            ],
            //할인 종류
            cardTypes : [{
                cardType: "CREDIT",
                title: "신용카드"
            },
            {
                cardType: "TELECOM",
                title: "통신사"
            },
            {
                cardType: "OKCASHBAG",
                title: "OK캐시백"
            },
            {
                cardType: "POINT",
                title: "포인트결제"
            }
            ],
            //할인 카드/통신사/포인트/OK캐시백
            creditCards : [{
                cardId: 1,
                cardType: "CREDIT",
                cardName: "CJ ONE 삼성카드",
                discount: 30,
                discountType: "%"
            },
            {
                cardId: 2,
                cardType: "CREDIT",
                cardName: "CJ ONE 신한카드",
                discount: 30,
                discountType: "%"
            },
            {
                cardId: 3,
                cardType: "CREDIT",
                cardName: "The CJ 카드",
                discount: 22,
                discountType: "%"
            },
            {
                cardId: 4,
                cardType: "CREDIT",
                cardName: "삼성 6 V4카드",
                discount: 20,
                discountType: "%"
            },
            {
                cardId: 5,
                cardType: "CREDIT",
                cardName: "신한 Lady카드",
                discount: 20,
                discountType: "%"
            },
            {
                cardId: 6,
                cardType: "CREDIT",
                cardName: "삼성 SFC",
                discount: 20,
                discountType: "%"
            },
            {
                cardId: 7,
                cardType: "CREDIT",
                cardName: "삼성 S클라스",
                discount: 20,
                discountType: "%"
            },
            {
                cardId: 8,
                cardType: "CREDIT",
                cardName: "하나 Yes OK Saver",
                discount: 20,
                discountType: "%"
            },
            {
                cardId: 9,
                cardType: "CREDIT",
                cardName: "홈플러스 하나줄리엣카드",
                discount: 20,
                discountType: "%"
            },
            {
                cardId: 10,
                cardType: "CREDIT",
                cardName: "하나 줄리엣카드 & Yes 4u shopping",
                discount: 20,
                discountType: "%"
            },
            {
                cardId: 11,
                cardType: "CREDIT",
                cardName: "KB Star",
                discount: 20,
                discountType: "%"
            },
            {
                cardId: 12,
                cardType: "CREDIT",
                cardName: "이마트 KB카드",
                discount: 15,
                discountType: "%"
            },
            {
                cardId: 13,
                cardType: "TELECOM",
                cardName: "KT 멤버십 일반 할인",
                discount: 5,
                discountType: "%"
            },
            {
                cardId: 14,
                cardType: "TELECOM",
                cardName: "KT 멤버십 VIP 할인",
                discount: 15,
                discountType: "%"
            },
            {
                cardId: 15,
                cardType: "TELECOM",
                cardName: "T 멤버십 실버 할인",
                discount: 5,
                discountType: "%"
            },
            {
                cardId: 16,
                cardType: "TELECOM",
                cardName: "T 멤버십 VIP/골드 할인",
                discount: 15,
                discountType: "%"
            },
            {
                cardId: 17,
                cardType: "OKCASHBAG",
                cardName: "OK캐시백",
                discount: 30,
                discountType: "%"
            },
            {
                cardId: 18,
                cardType: "POINT",
                cardName: "BC Top 포인트",
                discount: 100,
                discountType: "%"
            },
            {
                cardId: 19,
                cardType: "POINT",
                cardName: "기아멤버스 카드",
                discount: 20,
                discountType: "%"
            },
            {
                cardId: 20,
                cardType: "POINT",
                cardName: "삼성카드 포인트",
                discount: 100,
                discountType: "%"
            },
            {
                cardId: 21,
                cardType: "POINT",
                cardName: "현대카드 M",
                discount: 20,
                discountType: "%"
            },
            {
                cardId: 22,
                cardType: "POINT",
                cardName: "신한 Hi-Point 카드",
                discount: 20,
                discountType: "%"
            },
            {
                cardId: 23,
                cardType: "POINT",
                cardName: "블루멤버스 카드",
                discount: 20,
                discountType: "%"
            }
            ],


            //현재 주문한 상품 정보 
            // menuid 를 인덱스로 값들을 가지는걸로
            currentOrderList : {},
        };
    },
    mounted(){

    },
    computed: {
        // getTotalAmount() {
        //     console.log("amnt");
        //     const reducer = (accumulator, curValue) => accumulator + curValue.price * curValue.qty
        //     this.totalAmount = this.currentOrderList.reduce(reducer)
        // }
        totalAmount(){
            if (Object.keys(this.currentOrderList).length > 0){
                //console.table("###",this.currentOrderList, "####")
                const reducer = (accumulator, curValue) => accumulator + curValue.price * curValue.qty
                for (var i in this.currentOrderList){
                    console.log(this.currentOrderList[i])
                }
                return 1 //this.currentOrderList.reduce(reducer)
            }
            else {
                return 0
            }
        }
    },
    methods : {
        sendOrder(menuId){
            const selectedItem = this.menus.filter(item => item.menuId == menuId)[0];
            const name = selectedItem.menuName;
            const price = selectedItem.price;

            const isKeyExist = Object.keys(this.currentOrderList).includes(`${menuId}`)
            
            if(isKeyExist){
                this.currentOrderList[`${menuId}`].qty += 1;
            }
            else{
                this.currentOrderList[menuId] = {
                    menuName : name,
                    price : price,
                    qty : 1,
                };
            }
            console.table(this.currentOrderList);
        },
        
        deleteOrder(delItem){
            delete this.currentOrderList[delItem];
        },


    },

}
</script>

<style>

</style>