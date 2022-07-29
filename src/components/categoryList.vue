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



</script>

<template>
    <div class="flex">
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
                :pagination="{
                    clickable: true
                }"
                :navigation="true"
                :modules= [Navigation,Pagination]
                class="mySwiper flex max-w-screen-2xl"
            >
                
                <swiper-slide
                v-for="product in eachCard"
                :key="product.id"
                >
                    <div class="p-5 bg-lime-200 w-full h-full mr-6 rounded-md">
                        <img class="rounded-md" :src="product.image" alt="">
                        <p class="font-bold text-lg">{{product.name}}</p>
                        <p class="font-extrabold text-3xl">{{product.priceText}}</p>
                        <p v-show="product.params.shippingFee =='FREE'">Ücretsiz Kargo</p>
                        <button class="p-3 bg-blue-300 border-none">Sepete Ekle</button>
                    </div>
                    
                </swiper-slide>
            </swiper>
        
    </div>
   
</template>

