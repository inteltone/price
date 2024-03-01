<template>

	<!-- ----------------------------------------------------------------- -->

  <div class="choice block">
	<h3 class="choice__title">Комплексный маркетинг</h3>
	<div class="choice__items">
		<div class="choice__item" v-for="(item,index) in marketingChoice" :key="index">
			<input type="radio" :id="item.id" name="choice" :value="item.name" v-model="picked">
			<label :for="item.id">{{ item.name }}</label>
		</div>		
	</div>
  </div>

  <!-- ----------------------------------------------------------------- -->

  <div class="precise" v-if="picked === 'Продвижение по выбору'">
	<div class="precise__block" v-for="item in marketing">		
		<p class="precise__title">
			{{ item.section }}
			<button class="precise__close" @click="item.picked = false">x</button>		
		</p>
		<div class="precise__list">
			<div class="precise__item" v-for="el in item.items">
				<input type="radio" :id="el.name" :value="el.name" v-model="item.picked">
				<label :for="el.name">
					<span>{{ el.name }}</span>
					<span class="precise__modal" @click.prevent="handleShowModal(el.name)">?</span> 
					<span>{{ el.price }}</span></label>
			</div>
		</div>
	</div>
  </div>

  <!-- ----------------------------------------------------------------- -->

  <div class="sum-full" v-if="picked === false">
	<h3 class="sum-full__title">Смета заказа</h3>
	<p class="sum-full__item">Пока ничего не выбрано</p>	
	<p class="sum-full__price">Итого: 0 &#8381;</p>
  </div>

  <!-- ----------------------------------------------------------------- -->

  <div class="sum-full" v-if="picked === 'Продвижение «Под ключ»'">
	<h3 class="sum-full__title">Смета заказа</h3>
	<p class="sum-full__item" v-for="item in marketing">
		{{ item.items[item.items.length-1].name }}
		<span class="precise__modal" @click="handleShowModal(item.items[item.items.length-1].name)">?</span> 
		<span>{{ item.items[item.items.length-1].price }}</span>
		
	</p>	
	<p class="sum-full__price">Итого: {{ calcSumFull }} &#8381;</p>
  </div>

  <!-- ----------------------------------------------------------------- -->

	<div class="sum-precise" v-if="picked === 'Продвижение по выбору'">
		<h3 class="sum-precise__title">Смета заказа</h3>
		<p v-if="calcSumPrecise === 0" class="sum-precise__item">Пока ничего не выбрано</p>	
		<div v-else class="sum-precise__item" v-for="elem in marketing">
			<div v-for="item in elem.items" :key="item.name">
				<p v-if="item.name === elem.picked">
					<span class="sum-precise__checkbox" @click="handleRemoveItem(item.name)"></span>
					{{ item.name }} 
					<span class="sum-precise__price">{{ item.price }}</span>
				</p>
			</div>
		</div>
		<p class="sum-precise__total">Итого: {{ calcSumPrecise }} &#8381;</p>
	</div>

	<!-- ----------------------------------------------------------------- -->

	<div class="popup-backdrop" :class="{show: showModal}" @click="showModal = false"></div>
	<dialog class="popup" :open="showModal">		
			<p class="popup__title">
				<span class="popup__title-text">{{ popupTitle }}</span>
				<button class="popup__close" @click="showModal = false">X</button>
			</p>
			<p class="popup__desc">{{ popupDesc }}</p>
	</dialog>
</template>

<script>
import { marketing, marketingChoice } from './assets/data.js'

export default {
	data() {
		return {
			marketingChoice,
			picked: false,
			marketing,
			showModal: false,
			popupTitle: '',
			popupDesc: '',
      obj:[],
		}
	},
	computed: {
		calcSumFull() {
			let sum = 0
			for (let item of this.marketing) {
				for (let el of item.items) {
					if (el.name.includes('Под ключ')) {
						sum += el.price
					}
				}
			}
			return sum
		},
		calcSumPrecise() {
			let sum = 0
			for (let elem of this.marketing){
				for (let item of elem.items) {
					if (elem.picked === item.name) {
						sum += item.price
					}
				}
			}
			return sum
		}
	},
	methods: {
		handleShowModal(name) {
			for (let elem of this.marketing) {
				for (let item of elem.items) {
					if (item.name === name) {
						this.popupTitle = name
						this.popupDesc = item.desc
						this.showModal = true
					}
				}
			}
		},
		handleRemoveItem(name) {
			console.log(name)
			for (let elem of this.marketing) {
				for (let item of elem.items) {
					if (item.name === name) {
						elem.picked = false
					}
				}
			}
		}
	},
  mounted(){
    fetch('https://headless.bearlogics.host/wp-json/wp/v2/service_seo/')
    .then(response => response.json())
    .then(data => {
      this.obj=data
      console.log(data)
    })
  }
}

</script>


