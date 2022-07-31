<script setup>
import {ref} from 'vue'
import { Swiper, SwiperSlide } from 'swiper/vue';
import 'swiper/css';
import { Pagination, Navigation } from "swiper";
import "swiper/css/bundle";

const categories = ref([])
const products = ref(null)
const eachProduct = ref([])
const eachCard = ref([])
const show = ref(false)
const totalAmount = ref(0)
const pushBasket = ref([])
const isActive = ref(false)

async function fetchData() {
await fetch("/product-list.json")
    .then(res => res.json())
    .then((data)=> {
        
        products.value = data.responses[0][0].params.recommendedProducts
        categories.value = data.responses[0][0].params.userCategories
    })
}
fetchData()

function setProducts(index) {
    for (let i = 0; i < products.value.length; i++) {
        if (index === i) {
            eachProduct.value = Object.values(products.value[i])
        }
        
    }
    eachProduct.value.map(item => {
        eachCard.value = item
    })
} 

function addToBasket(product) {
    show.value = true
    setTimeout(() => {
        show.value = false
    }, 1000);

    totalAmount.value++
    pushBasket.value.push(product)
   
}

function totalPrice() {
    return pushBasket.value.reduce((current,next) => 
    current+next.price,0)
}

function deleteItems(index) {
    pushBasket.value.splice(index,1)
    totalAmount.value--
    return pushBasket.value.reduce((current,next)=>
    next-current.price,0)
}

function Basket() {
    isActive.value = !isActive.value
}

</script>

<template>
    <div class="md:p-10 pt-10">
        <div class="shadow-xl" :class="{'popup':isActive}">
            <h1 class="font-bold text-2xl md:text-3xl md:mb-10 sm:text-left m-6">Sizin İçin Seçtiklerimiz</h1>
            <div class="flex md:flex-row flex-col pb-10">

                <div class="flex sm:flex-col flex-wrap gap-4 justify-center text-center items-center">
                    <div
                    class="bg-blue-200 p-3 w-40 md:w-full rounded-md cursor-pointer hover:bg-blue-300 transition duration-300"
                    v-for="(title,index) in categories" 
                    :key="title.index"
                    @click="setProducts(index)">
                        <p class="line-clamp-1" v-if="title.includes('>')">{{title.split('>')[1]}}</p>
                        <p v-else>{{title}}</p>
                    </div>
                
                </div>
                    
                <swiper
                    :slidesPerView="5"
                    :spaceBetween="30"
                    :loop="true"
                    :navigation="true"
                    :loopFillGroupWithBlank="true"
                    :modules= [Navigation,Pagination]
                    class="flex sm:max-w-screen-2xl max-w-sm md:mx-6 py-6"
                    :breakpoints="{
                    0:{
                            slidesPerView: 1,
                            spaceBetween: 10, 
                        },
                    640: {
                        slidesPerView: 1,
                        spaceBetween: 10,
                    },
                    767: {
                        slidesPerView: 2,
                        spaceBetween: 20,
                    },
                    1023: {
                        slidesPerView: 3,
                        spaceBetween: 40,
                    },
                    1280: {
                        slidesPerGroup: 5,
                        slidesPerView: 5,
                        spaceBetween: 25,
                    },
                    }"
                    >
                        <swiper-slide
                        class="shadow rounded-md cursor-pointer"
                        v-for="(product) in eachCard"
                        :key="product.id"
                        >
                            <div class="p-5 bg-slate-200 w-full md:h-[480px]  space-y-3 rounded-md">
                                <img class="rounded-md" :src="product.image" alt="">
                                <p class="font-bold text-base line-clamp-1">{{product.name}}</p>
                                <p class="font-extrabold text-2xl">{{product.priceText}}</p>
                                <p v-show="product.params.shippingFee =='FREE'">Ücretsiz Kargo</p>
                                <p @click="addToBasket(product)" class="p-3 bg-blue-300 border-none rounded-md cursor-pointer text-center">Sepete Ekle</p>
                            </div>
                        </swiper-slide>
                </swiper>
                
            </div>
        </div>
    </div>

    <div v-show="show" class="absolute md:top-2 md:right-5 z-50 bottom-80 right-8">
        <div class="flex justify-center items-center md:w-[400px] w-80 bg-slate-800 rounded-md py-6 gap-4">
            <i class="fa-solid fa-check text-white bg-green-300 text-2xl rounded-[50%] px-3 py-2"></i>
            <div class="text-xl font-semibold">
                <h2 class="text-white">Ürün sepete eklendi.</h2>
                <h2 class="text-blue-200">Sepete Git</h2>
            </div>
        </div>
    </div>
    <div>
        <p v-show="totalAmount>0" class="absolute md:top-8 md:right-8 top-3 right-3 text-sm bg-red-400 rounded-[50%] px-[5px] text-white">{{totalAmount}}</p>
        <i @click="Basket" class="fa-solid fa-basket-shopping absolute md:top-10 md:right-10 top-5 right-5 text-3xl text-blue-500"></i>
    </div>

    <div class="z-50 md:h-[500px] md:w-[700px] h-[400px] md:p-10 p-5 shade 0px 5px 15px] absolute md:top-20 md:right-16 bg-white  scrollbar rounded-md top-14 w-full" v-show="isActive">
        <h1 class="font-bold text-2xl pb-6">Sepetim</h1>
        <div v-if="totalAmount>0" class="flex flex-col gap-5" >
            <div class="flex justify-between items-center bg-slate-300 p-3 w-full rounded-lg" v-for="(item,index) in pushBasket"
            :key="item.id">
                <div class="flex items-center gap-3">
                    <img class="w-16 h-16 rounded-md" :src="item.image" alt="">
                    <div>
                        <p class="line-clamp-1"> {{item.name}} </p>
                        <p> {{item.price}} TL</p>
                        <p v-show="item.params.shippingFee =='FREE'">Ücretsiz Kargo</p>
                    </div>
                </div>
                <i @click="deleteItems(index)" class="fa-solid fa-trash-can cursor-pointer m-0 md:ml-5 text-2xl"></i>
            </div>
            <p class="font-bold text-lg"> Toplam Fiyat: {{totalPrice().toFixed(2)}} TL </p>
        </div>
        <div v-else class="flex flex-col gap-8">
            <i class="fa-solid fa-cart-plus text-4xl text-blue-400"></i>
            <h2 class="font-bold text-xl">Sepetiniz şu an boş</h2>
            <p class="text-lg">Sepete ürün ekleyiniz.</p>
        </div>
    </div>
</template>

<style>

    .popup {
       filter: blur(20px); 
    }

    .shadow:hover {
        box-shadow: rgba(0, 0, 0, 0.35) 0px 5px 15px;
        transition: .5s;
    }

    .shade {
        box-shadow: rgba(0, 0, 0, 0.35) 0px 5px 15px;
    }

    .swiper-button-prev {
        left: 0;
    }

    .swiper-button-next {
        right: 0;
    }

    .scrollbar{
        scrollbar-color: #d4aa70 #e4e4e4;
        scrollbar-width: thin;
        direction: ltr;
        overflow-y: auto;
    }

    .scrollbar::-webkit-scrollbar {
        width: 13px;
    }

    .scrollbar::-webkit-scrollbar-track {
        background-color: #e4e4e4;
        border-radius: 100px;
    }

    .scrollbar::-webkit-scrollbar-thumb {
        border-radius: 100px;
        border: 3px solid transparent;
        background-clip: content-box;
        background-color: #60a5fa;
    }

</style>

