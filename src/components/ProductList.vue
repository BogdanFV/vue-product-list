<template>
    <div class="form-cover">
        <h2>Список продуктов</h2>
        <div class="form-list">
            <ul>
                <li v-for="(product, index) in products" :key="index">
                    <span :class="{ 'crossed-out': product.isCrossedOut }">
                        {{ product.name }}
                    </span>
                    <button @click="toggleProduct(product)" class="hide__button">
                        {{ product.isCrossedOut ? "Вернуть" : "Убрать" }}
                    </button>
                </li>
            </ul>
        </div>
        <div class="buttons__block">
            <form @submit.prevent="addProduct">
                <input type="text" v-model="newProductName" @keyup.enter="addProduct" required
                    placeholder="Введите новый продукт">
                <button type="submit" @click="addProduct">Добавить</button>
            </form>
        </div>
    </div>
</template>
  
<script>

import "./ProductList.scss";

export default {
    data() {
        return {
            products: [],
            newProductName: '',
        };
    },
    methods: {
        addProduct() {
            if (this.newProductName.trim() !== '') {
                const newProduct = { name: this.newProductName, isCrossedOut: false };
                const crossedOutProducts = this.products.filter((p) => p.isCrossedOut);
                const nonCrossedOutProducts = this.products.filter((p) => !p.isCrossedOut);

                nonCrossedOutProducts.unshift(newProduct);

                this.products = [...nonCrossedOutProducts, ...crossedOutProducts];
                this.newProductName = '';
                this.saveToLocalStorage();
            }
        },

        toggleProduct(product) {
            product.isCrossedOut = !product.isCrossedOut;

            const crossedOutProducts = this.products.filter((p) => p.isCrossedOut);
            const nonCrossedOutProducts = this.products.filter((p) => !p.isCrossedOut);

            if (product.isCrossedOut) {
                const index = nonCrossedOutProducts.indexOf(product);
                if (index !== -1) {
                    nonCrossedOutProducts.splice(index, 1);
                    crossedOutProducts.push(product);
                }
            } else {
                const index = crossedOutProducts.indexOf(product);
                if (index !== -1) {
                    crossedOutProducts.splice(index, 1);
                    nonCrossedOutProducts.unshift(product);
                }
            }

            this.products = [...nonCrossedOutProducts, ...crossedOutProducts.reverse()];
            this.saveToLocalStorage();
        },
        saveToLocalStorage() {
            localStorage.setItem('products', JSON.stringify(this.products));
        },
        loadFromLocalStorage() {
            const savedProducts = localStorage.getItem('products');
            if (savedProducts) {
                this.products = JSON.parse(savedProducts);
            }
        },
    },
    mounted() {
        this.loadFromLocalStorage();
    },
    updated() {
        this.saveToLocalStorage();
    },

};
</script>
  