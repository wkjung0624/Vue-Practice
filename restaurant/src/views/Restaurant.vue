<template>
    <div class="container">
        <div class="MENU_BOARD">
        <!-- 상품 아이템 리스트 -->
            <button v-for="(menuItem, menuIdx) in menus" v-bind:key=menuIdx
                @click="sendOrder(menuItem.menuId)"
                class="btn">
                <span style="color: rgb(60, 255, 0);">{{menuItem.menuName}}</span><br>
                <span style="color: white;">({{menuItem.price.toLocaleString()}} 원)</span>
            </button>
        </div>
    
        <!-- 주문 목록 -->
        <div>
            <table class="MN_ORDER_BOOK">
                <thead>
                    <tr>
                        <th class="th th-head01"> 메뉴명 </th>
                        <th class="th th-head02"> 가격 </th>
                        <th class="th th-head03"> 수량 </th>
                        <th class="th th-head02"> 총합 </th>
                        <th class="th th-head03"> </th>
                    </tr>
                </thead>
                <tbody>
                    <!-- qty값이 음수일 때 방지 필요 -->
                    <tr :key=orderIdx v-for="(orderItem, orderIdx) in currentOrderList">
                        <td class="item-font"> {{orderItem.menuName}} </td>
                        <td class="item-font"> {{orderItem.price.toLocaleString()}} 원</td>
                        <td><input type="number" style="width:30px;"
                            v-model.number="orderItem.qty" 
                            @change="changeOrder(orderIdx)">
                        </td>
                        <td class="item-font"> {{orderItem.total.toLocaleString()}} 원 </td>
                        <td> <button @click="deleteOrder(orderIdx)"> X </button> </td>
                    </tr>
                </tbody>
            </table>
        </div>
        <!-- 할인 전 총 금액 -->
        <div class="result">
            <p class="big-white">할인 전 금액 : {{totalAmount.toLocaleString()}} 원</p>
        </div>

        <!-- 할인 쿠폰, 카드 선택 -->
        <div class="MENU_BOARD">
            <p class="big-white">쿠폰 선택 및 카드 선택 </p>
            <div>
                <select v-model="selectedCouponId"
                        style="width:200px;"
                        @change="payment()">
                    <option v-bind:value=0>
                        {{ paddingBothSide("할인쿠폰",21,"=") }}
                    </option>
                    <!-- v-bind:value 에 함수를 집어넣어 호출마다 1, 2, 3 ... n 을 반환할 때 왜 coupons의 크기만큼 호출하지 않는가? 
                        너무 많은 횟수를 호출함 -->
                    <option v-bind:key=cpIdx+1
                            v-bind:value=cpIdx+1
                            v-for="(cpItem, cpIdx) in coupons">
                            
                            {{cpItem.title}}

                    </option>
                </select>
            </div>
        



        <div v-bind:key=ctIdx v-for="(ctItem, ctIdx) in cardTypes">
            <select v-model="selectedCardIdList[ctIdx]" 
                    @change="payment()" 
                    style="width:200px;">
                <option v-bind:value=0> {{paddingBothSide(ctItem.title,21,"=")}} </option>
                <option v-bind:key=fctIdx+1
                        v-bind:value=fctIdx+1
                        v-for="(fctItem, fctIdx) in getFilteredCards(ctItem.cardType)">
                            {{fctItem.cardName}}
                </option>
            </select>

            <!-- creditCards -> discount, discountType -->
        </div>
        </div>
        <!-- 최종 금액 -->
        <div class="result">
            <p class="big-white"> 최종 결제액 : {{discounted.toLocaleString()}} 원</p>
        </div>
    </div>
</template>

<script>
export default {
    data(){
        return {
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
            //할인 쿠폰
            coupons : [{
                couponId: 1,
                title: "5% 할인쿠폰(중복할인 가능)",
                discount: 5,
                doubleDiscount: true,
                discountType: "%"
                },
                {
                couponId: 2,
                title: "10% 할인쿠폰(중복할인 가능)",
                discount: 10,
                doubleDiscount: true,
                discountType: "%"
                },
                {
                couponId: 3,
                title: "15% 할인쿠폰(중복할인 가능)",
                discount: 15,
                doubleDiscount: true,
                discountType: "%"
                },
                {
                couponId: 4,
                title: "5000 할인쿠폰(중복할인 가능)",
                discount: 5000,
                doubleDiscount: true,
                discountType: ""
                },
                {
                couponId: 5,
                title: "10,000 할인쿠폰(중복할인 가능)",
                discount: 10000,
                doubleDiscount: true,
                discountType: ""
                },
                {
                couponId: 6,
                title: "20,000 할인쿠폰(중복할인 가능)",
                discount: 20000,
                doubleDiscount: true,
                discountType: ""
                },
                {
                couponId: 7,
                title: "5% 할인쿠폰(중복할인 불가능)",
                discount: 5,
                doubleDiscount: false,
                discountType: "%"
                },
                {
                couponId: 8,
                title: "10% 할인쿠폰(중복할인 불가능)",
                discount: 10,
                doubleDiscount: false,
                discountType: "%"
                },
                {
                couponId: 9,
                title: "15% 할인쿠폰(중복할인 불가능)",
                discount: 15,
                doubleDiscount: false,
                discountType: "%"
                },
                {
                couponId: 10,
                title: "5000 할인쿠폰(중복할인 불가능)",
                discount: 5000,
                doubleDiscount: false,
                discountType: ""
                },
                {
                couponId: 11,
                title: "10,000 할인쿠폰(중복할인 불가능)",
                discount: 10000,
                doubleDiscount: false,
                discountType: ""
                },
                {
                couponId: 12,
                title: "20,000 할인쿠폰(중복할인 불가능)",
                discount: 20000,
                doubleDiscount: false,
                discountType: ""
                }
            ],


            // 현재 주문한 상품 정보 
            // menuid 를 인덱스로 값들을 가지는걸로
            currentOrderList : {},

            selectedCardIdList : Array.from({length: 4}, () => 0),
            selectedCouponId : 0,

            totalAmount : 0,
            discounted : 0,
        };
    },
    watch: {
        currentOrderList : {
            deep:true,
            
            handler(){
                let result = 0;
                const tempList = this.currentOrderList

                if (Object.keys(tempList).length > 0){
                    
                    for (var idx in tempList){
                        result += tempList[idx].total
                    }
                    this.totalAmount = result
                }
                else {
                    this.totalAmount = 0
                }
            }
        },

        totalAmount : {
            handler(){
                this.payment()
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
                this.currentOrderList[`${menuId}`].total = price * this.currentOrderList[`${menuId}`].qty;
            }

            else{
                this.currentOrderList[menuId] = {
                    menuName : name,
                    price : price,
                    qty : 1,
                    total : price,
                };
            }
        },
        
        deleteOrder(orderId){
            delete this.currentOrderList[orderId];
        },

        changeOrder(orderId){
            // 딥카피 안되있는 변수
            var oldItem = this.currentOrderList[orderId]
            this.payment()

            if (oldItem.qty <= 0){
                this.deleteOrder(orderId)
            }else{
                this.currentOrderList[orderId].total = oldItem.price * oldItem.qty
            }
            
        },
        
        // 할인율, 할인 타입, 할인 전 금액을 받아 최종 할인된 금액을 반환함
        // getDiscountValue
        getDiscountPrice(discount, discountType, oldPrice){
            
            let result = 0;

            switch(discountType){
                case '%':
                    result = oldPrice * (1 - (discount * 0.01))
                    return (result) > 0 ? result : 0;
                case '':
                    result = oldPrice - discount;
                    return (result) > 0 ? result : 0;
                default:
                    console.log("discountType is not '%' or ''")
                    return -1
            }
        },

        payment(){
            
            var oldAmount = this.totalAmount;
            var Discount_Results = [];
                                    
            // 선 쿠폰 후 카드 계산
            // 쿠폰 정보 부터 가져온다.

            if(this.selectedCouponId > 0){
                var couponItem = this.coupons.filter(item => item.couponId == this.selectedCouponId)[0]
                var total_coupon_discount = this.getDiscountPrice(couponItem.discount, couponItem.discountType, oldAmount)
            
                // 쿠폰이 중복할인이 가능한지 확인한다.
                // 가능하면 전체금액 감소 이후 카드 계산
                if(couponItem.doubleDiscount){
                    oldAmount = total_coupon_discount;
                    console.log("double discounted", oldAmount, total_coupon_discount)
                }else{
                    // 중복할인 불가하면 카드별 최대 할인 비교해야하므로 배열에 푸쉬
                    // 쿠폰명, 할인금액(할인전금액 - 할인액) 푸쉬
                    Discount_Results.push(oldAmount - total_coupon_discount)
                }
            }


            // 카드 정보를 가져온다.
            // 이전 카드 할인율이 현재 카드 할인율보다 높으면 가장 좋은 카드 Value 갱신

            
            var g_idx = 0;
            var idx = 0;

            for(var credit of this.cardTypes){
                var cards = this.creditCards.filter(i => i.cardType == credit.cardType)
                var cardId = this.selectedCardIdList[idx]

                idx += 1;

                if(cardId != 0){
                    var items = cards.filter(i => i.cardId == (g_idx + cardId))[0]
                    var total_credit_discount = this.getDiscountPrice(items.discount, items.discountType, oldAmount)

                    console.table(items)
                    Discount_Results.push(oldAmount - total_credit_discount)     
                }

                g_idx += cards.length;
            }

            
            
            // 쿠폰 중복 할인 불가라면 가장 할인율이 높은 결과로 리턴, 어떻게 줄이지
            var max = 0;

            for(var item of Discount_Results){
                if(item > max){
                    max = item
                }
            }
            
            this.discounted = Math.floor((oldAmount - max) / 10) * 10
        },

        getFilteredCards(cardType){
            return this.creditCards.filter(item => item.cardType == cardType)
        },

        paddingBothSide(originText, maxLength=21, fillString=""){
            const padCount = (maxLength - originText.length) / 2
            const padString = `${fillString.repeat(padCount)}`

            return `${padString}${originText}${padString}`
        }
    },

}
</script>

<style scoped>

div{
    padding: 1px;
    margin:10px;
    border-radius: 15px;
}

.th{
    font-size: 17px;
    font-weight: bold;
    color: rgb(109, 71, 0);
    border-bottom: 2px dashed;
}

.th-head01{
    width: 150px;
}
.th-head02{
    width: 100px;
}
.th-head03{
    width: 40px;
}


.item-font{
    color:rgb(66, 27, 0);
    font-size: 12px;
    font-weight: bold;
}
.btn{
    margin:10px;
    padding: 5px;
    width: 160px;
    height:100px;
    background: rgb(29, 28, 28);
    font-size: 12px;
    font-weight: bold;
    border-radius: 10px;
    border-color: rgb(78, 105, 14);
}
.MN_ORDER_BOOK{
    background: rgb(255, 203, 107);
    width: 580px;
    border:2px solid;
    border-color: #dcff12;
    border-radius: 10px;
}

.big-white{
    font-size: 25px;
    font-weight: bold;
    color:rgb(255, 255, 255);
}
.result{
    border: 3px;
    border-color: azure;
    width: 580px;
    height: 80px;
    background-color: rgb(59, 59, 59);
    
}
.MENU_BOARD{
   background: rgb(194, 194, 194); 
}
.container{
    border: 2px solid;
    width: 600px;
    height:1040px;
    background: rgb(37, 25, 25);
}

</style>