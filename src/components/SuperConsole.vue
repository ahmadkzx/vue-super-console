<template>
	<div class="super-console">
		<div class="super-console-header">
			<span class="super-console__title">SUPER CONSOLE</span>
			<button class="super-console__close"></button>
		</div>

		<div class="super-console-logs">
			<SuperLog log="salam" logType="error" />
			<SuperLog log="salam" logType="info" />
			<SuperLog log="salam" logType="success" />
			<SuperLog log="salam" logType="warning" />
		</div>
	</div>
</template>

<script>
import SuperLog from './SuperLog'

export default {
	name: 'SuperConsole',

	components: {
		SuperLog
	}
}
</script>

<style src="@/assets/sass/superConsole.scss" lang="scss" scoped />