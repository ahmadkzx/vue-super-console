<template>
	<div class="super-console-container">
		<button :class="['super-fab', { 'opened': isConsoleOpened }]" @click="toggleIsConsoleOpened">
			<i class="si si-arrow"></i>
		</button>
		<SuperConsole :class="{ 'opened': isConsoleOpened }" />
	</div>
</template>

<script>
import SuperConsole from './SuperConsole'

export default {
	name: 'SuperFab',

	components: {
		SuperConsole
	},

	data: () => ({
		isConsoleOpened: false
	}),

	methods: {
		toggleIsConsoleOpened() {
			this.isConsoleOpened = !this.isConsoleOpened
		}
	}
}
</script>

<style src="@/assets/sass/superFab.scss" lang="scss" scoped />