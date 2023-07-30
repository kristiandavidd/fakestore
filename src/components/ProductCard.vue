<!-- eslint-disable vue/no-parsing-error -->
<template>
    <div>
        <div v-if="loading" class="loader"></div>
        <section v-else-if="isProductAvailable">
            <!-- <p>{{ currentProduct.id }}</p> -->
            <div class="img">
                <img :src=currentProduct.image alt="" class="image">
            </div>
            <div>
                <h2 :style="getTitleStyle">{{ currentProduct.title }}</h2>
                <div class="category">
                    <p>{{ currentProduct.category }}</p>
                    <div class="rating">
                        <p>{{ currentProductRating }}/5</p>
                        <svg class="rating-circles" :width="circleSize * 5" :height="circleSize">
                        <circle
                            v-for="i in 5"
                            :key="i"
                            :cx="(circleSize + circleSpacing) * (i - 1) + circleSize / 2"
                            :cy="circleSize / 2"
                            :r="circleSize / 2"
                            :fill="(i <= getRatingValue(currentProductRating)) ? '#aaaaaa' : '#dddddd'"
                            :stroke-width="circleSize / 8"
                        />
                        </svg>
                        
                    </div>
                </div>
                <hr>
                <p>{{currentProduct.description}}</p>
                <hr>
                <h2 :style="getTitleStyle">${{ currentProduct.price }}</h2>
                <div class="button">
                    <buyButton :style="getBuyButtonStyle" />
                    <nextButton :style="getNextButtonStyle" @click="fetchNextProduct" />
                </div>
            </div>
        </section>
        <section v-else-if="isProductUnavailable">
            <div class="unavailable">
                <!-- <p>{{ currentProduct.id }}</p> -->
                <p>This product is unavailable to show.</p>
                <nextButton :style="getNextButtonStyle" @click="fetchNextProduct" />
            </div>
        </section>
        
    </div>
    
</template>

<script>
import axios from "axios"
import nextButton from "@/components/nextButton.vue"
import buyButton from "./buyButton.vue"

export default {
    name: "productCard",
    components: {
        nextButton, buyButton
    },
    data() {
        return {
            products: [],
            currentProductIndex:0,
            currentProductRating: 0,
            category: "",
            loading: false
        }
    },

    async mounted() {
        await this.fetchStoreApi();
    },

    methods: {
        async fetchStoreApi() {
            
            try {
                if (this.loading) return;
                this.loading = true;
                await new Promise(resolve => setTimeout(resolve, 1000));

                const response = await axios.get("https://fakestoreapi.com/products");
                this.products = response.data
                // .filter((product) =>
                //     product.category === "men's clothing" || product.category === "women's clothing"
                // );
                this.updateCurrentProduct();
                this.loading = false;
            } catch (error) {
            this.error = error.message;
            }
        },

        fetchNextProduct() {
            this.currentProductIndex = (this.currentProductIndex+1) % this.products.length;
            this.updateCurrentProduct();
        },

        updateCurrentProduct() {
            const currentProduct = this.products[this.currentProductIndex];
            this.currentProduct = currentProduct;
            this.currentProductRating = currentProduct.rating.rate;
            this.category = currentProduct.category;
        },
        getRatingValue(rating) {
        
            return Math.round(rating);
        },
    },

    computed: {
        currentProduct() {
            return this.products[this.currentProductIndex] || {};
            
        },

        isProductAvailable() {
            return this.category === "men's clothing" || this.category === "women's clothing";
        },

        isProductUnavailable() {
            return this.category !== "men's clothing" || this.category !== "women's clothing";
        },

        getTitleStyle() {
            if (this.currentProduct.category === "men's clothing") {
                return { color: "#002772" };
            } else if (this.currentProduct.category === "women's clothing") {
                return { color: "#720060" };
            } else {
                return {}; // Empty object for default style (no changes)
            }
        },

        getNextButtonStyle() {
            if (this.currentProduct.category === "men's clothing") {
                return { border: "2px solid #002772", color: "#002772" };
            } else if (this.currentProduct.category === "women's clothing") {
                return { border: "2px solid #720060", color: "#720060" };
            } else {
                return {}; // Empty object for default style (no changes)
            }
        },

        getBuyButtonStyle() {
            if (this.currentProduct.category === "men's clothing") {
                return { background: "#002772", color: "white" };
            } else if (this.currentProduct.category === "women's clothing") {
                return { background: "#720060", color: "white" };
            } else {
                return {}; // Empty object for default style (no changes)
            }
        },

        circleSize() {
            return 18; // Adjust the size of the circles here
        },

        circleSpacing() {
            return 1; // Adjust the spacing between circles here
        },
        },
}

</script>

<style>
    section {
        width: 70%;
        margin: 50px auto;
        background-color: white;
        display: flex;
        overflow: hidden;
        box-shadow: 5px 10px 20px #88888860;
        padding: 40px;
        border-radius: 10px;
    }

    .img {
        width: 200px;
        height: inherit;
        padding: 20px;
        display: flex;
        justify-content: center;
        align-items: start;
    }

    img {
        width: inherit;

    }

    .container {
        width: 100%;
        padding: 0 50px;
    }

    .category {
        display: flex;
        justify-content: space-between;
        font-size: 14px;
        align-items: center;
    }

    .button {
        width: 100%;
        display: flex;
        gap: 10px;
    }

    h2 {
        color: #aaaaaa;
    }

    button {
        width: 100%;
        cursor: pointer;
        padding: 10px 20px;
        border-radius: 5px;
    }

    .unavailable {
        background-image: url('./../assets/sad-face.png');
        background-position: center;
        background-size: contain;
        background-repeat: no-repeat;
        margin:auto;
        height: 300px;
        width: 80%;
        text-align: center;
        display: flex;
        flex-direction: column;
        justify-content: center;
    }

    .loader {
        margin: auto;
        border: 16px solid #f3f3f3; /* Light grey */
        border-top: 16px solid #3498db; /* Blue */
        border-radius: 50%;
        width: 40px;
        height: 40px;
        animation: spin 2s linear infinite;
    }

        @keyframes spin {
        0% { transform: rotate(0deg); }
        100% { transform: rotate(360deg); }
    }

    .rating {
        display: flex;
        align-items: center;
        gap:4px;
    }

    .rating-circles {
        display: block;
        
    }
</style>

