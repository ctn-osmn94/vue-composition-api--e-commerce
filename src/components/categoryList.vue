<script setup>
import {ref} from 'vue'

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
        if (categories.value.includes(">")) {
            
        }
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

        <div
        class="flex flex-col p-5 bg-lime-200 w-96 mr-6 rounded-md" 
        v-for="product in eachCard"
        :key="product.id"
        >
            <img class="rounded-md" :src="product.image" alt="">
            <p class="font-bold text-lg">{{product.name}}</p>
            <p class="font-extrabold text-3xl">{{product.priceText}}</p>
            <p v-show="product.params.shippingFee =='FREE'">Ücretsiz Kargo</p>
            <button class="p-3 bg-blue-300 border-none">Sepete Ekle</button> 
        </div>
        
    </div>
   
</template>