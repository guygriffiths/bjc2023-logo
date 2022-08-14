<script setup lang="ts">
import Main from './components/Main.vue'
</script>

<template>
	<Main id="main"></Main>
</template>

<style lang="scss">
@import '@/assets/styles/main.scss';
@import '@/assets/styles/scssVars.scss';

#app {
	width: 100vw;
	height: 100vh;
	max-width: 100vw;
	max-height: 100vh;
	padding: 1rem;

	#main {
		width: 100%;
		height: 100%;
	}

}
</style>
