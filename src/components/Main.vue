<script setup lang="ts">
import * as d3 from 'd3'
import InlineSvg from 'vue-inline-svg'
import { ColorPicker } from 'vue3-colorpicker'
import 'vue3-colorpicker/style.css'

import { onMounted, ref } from 'vue'

const clickables = [
	'solid-club-body-1',
	'solid-club-body-2',
	'solid-club-body-3',
	'hollow-club-body-fill-1',
	'hollow-club-body-fill-2',
	'hollow-club-body-fill-3',
	'solid-club-handle-fill-1',
	'solid-club-handle-fill-2',
	'solid-club-handle-fill-3',
	'hollow-club-handle-fill-1',
	'hollow-club-handle-fill-2',
	'hollow-club-handle-fill-3',
	'knob-1',
	'knob-2',
	'knob-3',
	'solid-cap-1',
	'solid-cap-2',
	'solid-cap-3',
	'hollow-cap-1',
	'hollow-cap-2',
	'hollow-cap-3',
	'solid-club-mid-1',
	'solid-club-mid-2',
	'solid-club-mid-3',
	'hollow-club-mid-1',
	'hollow-club-mid-2',
	'hollow-club-mid-3',
	'text',
]

onMounted(() => {})

const bgColor = ref('white')
const palette = ref([
	'rgb(0,0,0)',
	'rgb(255,255,255)',
	'rgb(103, 179, 202)',
	'rgb(142, 109, 197)',
	'rgb(214, 147, 216)',
])
const currentColor = ref(palette.value[0])

let outlinesOn = true
let capsOn = true
let im: any = null
const addingColor = ref(false)
const newColor = ref<any>('rgb(0,0,0)')

function addColor() {
	addingColor.value = true
}

function addColorConfirm() {
	palette.value.push(newColor.value)
	addingColor.value = false
	currentColor.value = newColor.value
}

function toggleOutlines() {
	outlinesOn = !outlinesOn
	im.selectAll('.outline').style('stroke', outlinesOn ? 'rgb(0, 0, 0)' : 'none')
}
function toggleCaps() {
	capsOn = !capsOn
	im.selectAll('.cap').style('opacity', capsOn ? 1 : 0)
}

function colorElement(element: any, color: string) {
	element.style('fill', color)
}

function getImageUrl() {
	return new URL('../assets/img/bjc-logo-web.svg', import.meta.url).href
}

function svgLoaded(event: any) {
	im = d3.select('#logo')

	for (let e of clickables) {
		im.select(`#${e}`)
			.style('cursor', 'pointer')
			.on('click', () => {
				colorElement(im.select(`#${e}`), currentColor.value)
			})
	}
}

function svgError(error: any) {
	console.error(error)
}
</script>

<template>
	<div id="main">
		<div id="image-box" :style="`background-color: ${bgColor}`">
			<inline-svg
				id="logo"
				:src="getImageUrl()"
				width="100%"
				height="100%"
				@loaded="svgLoaded($event)"
				@error="svgError($event)"
			/>
		</div>
		<div id="controls">
			<div class="col color-controls">
				<label>Current color:</label>
				<div
					class="color current"
					:style="`background-color: ${currentColor}`"
				></div>
				<label>Available colors:</label>
				<div id="colors">
					<div
						class="color"
						v-for="color in palette"
						:style="`background-color: ${color}`"
						@click="currentColor = color"
					></div>
				</div>
				<button @click="addColor" v-if="!addingColor">Add color</button>
			</div>
			<div class="col">
				<label for="bg-color"
					>Background color
					<select id="bg-color" v-model="bgColor">
						<option value="white">White</option>
						<option value="black">Black</option>
					</select></label
				>
				<button @click="toggleOutlines">Toggle Outlines</button>
				<button @click="toggleCaps">Toggle End Caps</button>
			</div>
		</div>
	</div>
	<div id="popup" v-if="addingColor">
		<color-picker
			:isWidget="true"
			pickerType="chrome"
			useType="pure"
			:disableAlpha="true"
			:disableHistory="true"
			v-model:pureColor="newColor"
		/>
		<button @click="addColorConfirm" v-if="addingColor">Ok</button>
	</div>
</template>

<style lang="scss" scoped>
@import '@/assets/styles/scssVars.scss';

#main {
	display: flex;
	flex-direction: column;

	align-items: center;
	justify-content: space-between;

	#image-box {
		flex: 1 1 100%;
		// width: 100%;
		padding: 2rem;
		display: flex;
		border-radius: 5%;
		margin-bottom: 0.5rem;
	}

	#controls {
		flex: 1 1 0;
		display: flex;
		flex-direction: row;
		justify-content: space-between;
		width: 100%;

		.col {
			flex: 0 0 50%;
			display: flex;
			flex-direction: column;
			padding: 0 1rem 0 1rem;
			justify-content: flex-end;

			&.color-controls {
				align-items: flex-end;
			}

			#colors {
				display: flex;
				flex-direction: row;
				flex-wrap: wrap;
				justify-content: flex-end;
			}
			.color {
				border: 1px solid black;
				width: 32px;
				height: 32px;
				cursor: pointer;

				&.current {
					width: 64px;
					height: 64px;
				}
			}
		}
	}
}

#popup {
	position: absolute;
	left: 0;
	top: 0;
	width: 100%;
	height: 100%;
	background-color: rgba(0, 0, 0, 0.8);
	display: flex;
	flex-direction: column;
	justify-content: center;
	align-items: center;

	button {
		width: 240px;
	}
	.vc-colorpicker--container {
		border-radius: 1rem !important;
	}
}
</style>
