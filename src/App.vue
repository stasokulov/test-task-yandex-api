<template>
  <div id="app">
    <!-- <img alt="Vue logo" src="./assets/logo.png"> -->
	<h1 class="title">Vue.js + Яндекс карты</h1>
    <div class="dashboard">
		<div class="dashboard__section">
			<h2 class="dashboard__h2">Добавление геозон</h2>
			<div class="dashboard__inputs">
				<Input type="text" title="Название" placeholder="Название геозон" v-model="formData.name" />
				<Input type="select" title="Фигура" :options="figures" v-model="formData.figure" />
				<Input type="text" title="Подсказка" placeholder="Это маркер" v-model="formData.hint" />
				<Input type="number" title="Широта" v-model="formData.coords[0]" />
				<Input type="number" title="Долгота" v-model="formData.coords[1]" />
				<Input type="custom_color" title="Цвет" :colors="colors" v-model="formData.color" :activeColor="formData.color" />
			</div>
			<button @click="addZone" class="btn btn_align-center">Добавить</button>
		</div>
		<div class="dashboard__section zones">
			<h2 class="dashboard__h2">Список геозон</h2>
			<ul class="zones__list">
				<li class="zones__item zone">
					<div class="zone__item">
						<input type="checkbox" @change="toggleCheckedZones">
					</div>
					<div class="zone__item">
						Цвет
					</div>
					<div class="zone__item">
						Название
					</div>
					<div class="zone__item">
						Фигура
					</div>
					<div class="zone__item">
						Скрыть на карте
					</div>
				</li>
				<li v-for="zone in zones" :key="zone.marker.id" class="zones__item zone">
					<div class="zone__item">
						<input type="checkbox" v-model="zone.checked">
					</div>
					<div class="zone__item">
						<div class="zones__color" :style="{'background-color': zone.color}"></div>
					</div>
					<div class="zone__item">
						{{zone.name}}
					</div>
					<div class="zone__item">
						{{zone.figure}}
					</div>
					<div class="zone__item">
						<input type="checkbox" v-model="zone.marker.hidden">
					</div>
				</li>
			</ul>
			<button class="btn btn_align-end" @click="deleteCheckedZone">Удалить {{checkedZones}}</button>
		</div>
	</div>
	<yandex-map
		:settings="settings" 
		:coords="coords" 
		:zoom="zoom"
		@click="mapClick"
	>
		<ymapMarker
			v-for="marker in markersList"
			:key="marker.marker.id"
			:markerId="marker.marker.id"
			:coords="marker.marker.coords"
			:hint-content="marker.marker.hint"
			@click="clickOnMarker(marker.marker.id)"
		/>
	</yandex-map>
  </div>
</template>

<script>
import { yandexMap, ymapMarker } from 'vue-yandex-maps'
import Input from './components/Input'
 
export default {
	name: 'App',
	components: { yandexMap, ymapMarker, Input}, 
	data () {
		return {
			settings: {
				apiKey: 'f468125f-834a-4d3c-92cf-50aae2822cc4',
				lang: 'ru_RU',
				coordorder: 'latlong',
				version: '2.1'
			},
			coords: [62, 30],
			zoom: 7,
			markersCounter: 0,
			formData: {
				name: null,
				figure: null,
				hint: null,
				color: null,
				coords: [62, 30]
			},
			zoneTemplate: {
				marker: {
					id: 1,
					coords: [62, 30],
					hint: 'хинт',
					hidden: false
				},
				name: 'Имя зоны',
				figure: 'Имя фигуры',
				color: 'red',
				checked: false
			},
			zones: [],
			figures: ['Полигон', 'Круг', 'Линия', 'Точка'],
			colors: ['red', 'green', 'blue'],
			allZonesChecked: false
		}
	},
	methods: {
		mapClick(e) {
			this.formData.coords = e.get('coords');
		},
		addZone() {
			this.markersCounter = this.markersCounter + 1;
			const newZone = JSON.parse(JSON.stringify(this.zoneTemplate));
			newZone.marker.id = this.markersCounter;
			newZone.marker.coords = this.formData.coords;
			newZone.marker.hint = this.formData.hint;
			newZone.name = this.formData.name;
			newZone.figure = this.formData.figure;
			newZone.color = this.formData.color;
			this.zones.push(newZone);
		},
		deleteCheckedZone() {
			const newZonesArray = this.zones.filter(zone => zone.checked === false);
			this.zones = newZonesArray;
		},
		toggleCheckedZones() {
			this.allZonesChecked = !this.allZonesChecked;
			this.allZonesChecked
			?
			this.zones.forEach(zone => zone.checked = true)
			:
			this.zones.forEach(zone => zone.checked = false)
		},
		clickOnMarker(id) {
			const zone = this.zones.find(zone => zone.marker.id === id);
			this.formData.name = zone.name;
			this.formData.figure = zone.figure;
			this.formData.hint = zone.marker.hint;
			this.formData.color = zone.color;
			this.formData.coords = zone.marker.coords;
		}
	},
	computed: {
		markersList: function() {
			return this.zones.filter(zone => zone.marker.hidden === false);
		},
		checkedZones: function() {
			return this.zones.filter(zone => zone.checked === true).length;
		}
	}
}
</script>

<style lang="scss">
body {
	margin: 0;
}
#app {
	display: flex;
	flex-wrap: wrap;
	font-family: Avenir, Helvetica, Arial, sans-serif;
	height: 100vh;
}
.title {
	min-width: 100%;
	text-align: center;
}
.btn {
	width: fit-content;
	padding: 10px;
	border: none;
	border-radius: 20px;
	color: #fff;
	background-color: #f00;
	cursor: pointer;
	&_align-center {
		align-self: center;
	}
	&_align-end {
		align-self: flex-end;
	}
}
.dashboard {
	display: flex;
	flex-direction: column;
	width: 50%;
	box-sizing: border-box;
	padding: 0 10px;
	&__section {
		display: flex;
		flex-direction: column;
		width: 100%;
	}
	&__h2 {
		width: 100%;
	}
	&__inputs {
		display: flex;
		flex-wrap: wrap;
		margin-bottom: 10px;
	}
}
.zones {
	&__list {
		margin: 0;
		padding: 0;
	}
	&__color {
		width: 20px;
		height: 20px;
		border-radius: 50%;
	}
.zone {
	display: flex;
	&__item {
		width: 25%;
		padding: 20px 20px 0 0;
	}
}
}
.ymap-container {
	width: 50%;
	height: 100%;
}
</style>
