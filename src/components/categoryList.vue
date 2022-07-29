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


async function fetchData() {
await fetch("/product-list.json")
    .then(res => res.json())
    .then((data)=> {
        const categoryTitles = data.responses[0][0].params.userCategories
        products.value = data.responses[0][0].params.recommendedProducts
        categories.value = categoryTitles
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
        console.log(item);
    })
} 

function addToBasket() {
    show.value = true
    setTimeout(() => {
        show.value = false
    }, 1000);
}




</script>

<template>
    <div class="flex flex-col justify-center w-full px-5 py-10 shadow-lg">
        <h1 class="font-bold text-3xl mb-10">Sizin İçin Seçtiklerimiz</h1>
        <div class="flex gap-10">
            <div class="flex flex-col gap-4">
                <div class="bg-blue-200 p-5 rounded-md cursor-pointer hover:bg-blue-300 transition duration-300" 
                    v-for="(title,index) in categories" 
                    :key="title.index"
                    @click="setProducts(index)">
                    <p v-if="title.includes('>')">{{title.split('>')[1]}}</p>
                    <p v-else>{{title}}</p>
                    
                </div>
            </div>
                
            <swiper
                :slidesPerView="5"
                :spaceBetween="30"
                :slidesPerGroup="5"
                :loop="true"
                :loopFillGroupWithBlank="true"
                :navigation="true"
                :modules= [Navigation,Pagination]
                class="mySwiper flex max-w-screen-2xl"
            >
                
                <swiper-slide
                v-for="product in eachCard"
                :key="product.id"
                >
                    <div class="p-5 bg-lime-200 w-full h-[480px] rounded-md space-y-3">
                        <img class="rounded-md" :src="product.image" alt="">
                        <p class="font-bold text-base line-clamp-1">{{product.name}}</p>
                        <p class="font-extrabold text-2xl">{{product.priceText}}</p>
                        <p v-show="product.params.shippingFee =='FREE'">Ücretsiz Kargo</p>
                        <p @click="addToBasket" class="p-3 bg-blue-300 border-none rounded-md cursor-pointer">Sepete Ekle</p>
                    </div>
                    
                </swiper-slide>
            </swiper>
            
        </div>
    </div>
    <div v-show="show" class="flex justify-center w-full">
        <div class="flex justify-center items-center w-1/3 bg-slate-800 rounded-md py-10 gap-4">
            <i class="fa-solid fa-check text-white bg-green-300 text-2xl rounded-full p-2"></i>
            <div class="text-xl font-semibold">
                <h2 class="text-white">Ürün sepete eklendi.</h2>
                <h2 class="text-blue-200">Sepete Git</h2>
            </div>
        </div>
    </div>
   
</template>

